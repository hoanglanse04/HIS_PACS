# PHẦN E — YÊU CẦU TÍCH HỢP

> **Mã phần**: SRS-PART-E  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: FR-INT-{MODULE}-{STT}

---

## E.1. Tổng quan Kiến trúc Tích hợp

### E.1.1. Sơ đồ tích hợp toàn hệ thống

```
                    ┌──────────────────────────────────────────┐
                    │            CỔNG BHXH VN                  │
                    │         (XML 4210 / Web API)             │
                    └──────────────┬───────────────────────────┘
                                   │ HTTPS (XML)
                    ┌──────────────▼───────────────────────────┐
                    │       INTEGRATION ENGINE (HUB)            │
                    │    (Mirth Connect / Custom ESB)           │
                    │                                           │
                    │  ┌─────┐ ┌──────┐ ┌─────┐ ┌──────────┐  │
                    │  │HL7  │ │DICOM │ │REST │ │XML/SOAP  │  │
                    │  │Adapter│ │Bridge│ │GW  │ │Connector │  │
                    │  └──┬──┘ └──┬───┘ └──┬──┘ └────┬─────┘  │
                    │     │       │        │          │         │
                    │  Message Queue (RabbitMQ / Kafka)         │
                    │  Dead-Letter Queue + Retry Engine         │
                    └──┬──────┬───────┬──────────┬─────────────┘
                       │      │       │          │
              HL7/MLLP │      │DICOM  │REST API  │XML
                       │      │       │          │
               ┌───────▼──┐ ┌─▼────┐ ┌▼───────┐ ┌▼──────────┐
               │   HIS    │ │ PACS │ │  LIS   │ │ Hệ thống  │
               │          │ │      │ │ [GĐ2]  │ │  khác     │
               └──────────┘ └──────┘ └────────┘ └───────────┘
```

### E.1.2. Nguyên tắc tích hợp

| Mã | Nguyên tắc | Mô tả |
|----|-----------|-------|
| INT-P01 | Loosely Coupled | Các hệ thống giao tiếp qua message queue, không gọi trực tiếp DB |
| INT-P02 | Idempotent | Mọi message có thể replay an toàn — xử lý trùng không gây side effect |
| INT-P03 | Guaranteed Delivery | Mỗi message được persist trước khi ack, retry tự động khi fail |
| INT-P04 | Audit Everything | Mọi message IN/OUT đều ghi log: timestamp, content, source, destination, status |
| INT-P05 | Standard First | Ưu tiên chuẩn HL7/DICOM/FHIR; chỉ dùng custom protocol khi chuẩn không đáp ứng |
| INT-P06 | Graceful Degradation | Khi tích hợp fail → hệ thống con vẫn hoạt động ở chế độ standalone |

---

## E.2. Tích hợp HIS ↔ PACS (Core Integration)

### FR-INT-HL7-001: Gửi chỉ định CĐHA (ORM^O01)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS gửi HL7 ORM^O01 khi bác sĩ tạo/sửa/hủy chỉ định CĐHA |
| **Actor** | HIS (sender), PACS (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | HL7 v2.4 over MLLP, port 2575 (configurable) |
| **Trigger** | Bác sĩ xác nhận chỉ định CĐHA trên HIS |
| **Message Structure** | MSH, PID, PV1, ORC, OBR (xem mapping E.2.5) |
| **Order Control** | NW (New), XO (Change), CA (Cancel) |
| **Quy tắc nghiệp vụ** | - ORC-1 = NW → PACS tạo order mới, cấp Accession Number<br/>- ORC-1 = XO → PACS cập nhật order (chỉ khi status < IN_PROGRESS)<br/>- ORC-1 = CA → PACS hủy order (chỉ khi status < IN_PROGRESS)<br/>- PACS gửi ACK trong ≤ 3 giây<br/>- Nếu ACK = AE (error) → HIS retry 3 lần, interval 10s → dead-letter queue |
| **Tiêu chí chấp nhận** | 1. ORM^O01 (NW) → PACS nhận, tạo order, trả ACK (AA) trong ≤ 3s<br/>2. ORM^O01 (CA) cho order đang chụp → PACS trả ACK (AE) + lý do<br/>3. Mất kết nối → HIS queue message, gửi lại khi phục hồi, thứ tự đúng |

### FR-INT-HL7-002: Nhận kết quả CĐHA (ORU^R01)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PACS gửi HL7 ORU^R01 khi báo cáo CĐHA đạt trạng thái Final/Amended |
| **Actor** | PACS (sender), HIS (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | HL7 v2.4 over MLLP |
| **Trigger** | BS CĐHA duyệt báo cáo (status → FINAL hoặc AMENDED) |
| **Message Structure** | MSH, PID, OBR, OBX (findings, conclusion, WADO URL) |
| **Quy tắc nghiệp vụ** | - OBR-25 = F (Final) hoặc C (Amended)<br/>- OBX-2 = TX (text result), RP (reference pointer = WADO URL)<br/>- HIS cập nhật order status → REPORTED<br/>- HIS hiển thị notification real-time cho BS chỉ định<br/>- OBR-2 (Placer Order Number) = HIS Order ID → dùng để match |
| **Tiêu chí chấp nhận** | 1. ORU^R01 nhận → HIS cập nhật trạng thái trong ≤ 2 giây<br/>2. BS lâm sàng thấy notification "Có kết quả CĐHA" trong ≤ 5 giây<br/>3. Click kết quả → hiển thị text + link xem ảnh DICOM |

### FR-INT-HL7-003: Đồng bộ thông tin bệnh nhân (ADT)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS gửi HL7 ADT messages khi có sự kiện đăng ký/cập nhật/nhập viện/ra viện |
| **Actor** | HIS (sender), PACS (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | HL7 v2.4 over MLLP |
| **Message Types** | ADT^A04 (Register), ADT^A08 (Update), ADT^A01 (Admit), ADT^A02 (Transfer), ADT^A03 (Discharge), ADT^A40 (Merge) |
| **Quy tắc nghiệp vụ** | - ADT^A04 → PACS tạo/cập nhật Patient record<br/>- ADT^A08 → PACS cập nhật demographics (tên, ngày sinh, …)<br/>- ADT^A40 → PACS merge 2 patient records (update Patient ID trên tất cả study)<br/>- Patient merge phải propagate xuống DICOM level (update 0010,0020 trong study index) |
| **Tiêu chí chấp nhận** | 1. ADT^A04 → PACS tạo patient trong ≤ 2 giây<br/>2. ADT^A40 (merge) → tất cả study cũ hiển thị dưới Patient ID mới<br/>3. ADT^A08 đổi tên BN → PACS worklist hiển thị tên mới |

### FR-INT-DCM-001: DICOM Modality Worklist (MWL)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PACS cung cấp DICOM MWL (C-FIND SCP) cho các modality truy vấn |
| **Actor** | Modality (SCU), PACS (SCP) |
| **Ưu tiên** | Must |
| **Giao thức** | DICOM C-FIND, SOP Class: 1.2.840.10008.5.1.4.31 |
| **Query Keys** | Scheduled Date, Modality, Scheduled Station AE Title, Patient ID, Accession Number |
| **Return Keys** | Patient Name, Patient ID, DOB, Sex, Accession Number, Scheduled Procedure Step ID, Description, Modality, Station AET, Date/Time, Referring Physician |
| **Quy tắc nghiệp vụ** | - Chỉ trả worklist cho order có status = SCHEDULED<br/>- Lọc theo AE Title: thiết bị chỉ thấy order phù hợp modality type<br/>- Worklist tự refresh: modality query lại sẽ nhận order mới<br/>- [GIẢ ĐỊNH] Modality hỗ trợ MWL C-FIND SCU |
| **Tiêu chí chấp nhận** | 1. KTV chọn worklist trên modality → hiển thị danh sách BN trong ≤ 3 giây<br/>2. Order mới tạo → xuất hiện trên worklist trong ≤ 10 giây<br/>3. Order bị hủy → biến mất khỏi worklist |

### FR-INT-DCM-002: DICOM Image Storage (C-STORE)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PACS nhận ảnh DICOM từ modality qua C-STORE SCP |
| **Actor** | Modality (SCU), PACS Archive (SCP) |
| **Ưu tiên** | Must |
| **Giao thức** | DICOM C-STORE, port 11112 (configurable per AE) |
| **SOP Classes** | CT Image, MR Image, CR Image, DX Image, US Image, MG Image, NM Image, PT Image, SC Image, Enhanced CT/MR, Encapsulated PDF, DICOM SR |
| **Transfer Syntax** | Implicit VR Little Endian (default), Explicit VR Little Endian, JPEG 2000 Lossless, JPEG Lossless |
| **Quy tắc nghiệp vụ** | - Nhận ảnh → validate DICOM conformance (mandatory tags present)<br/>- Match Accession Number (0008,0050) với order trong DB<br/>- Nếu không match → lưu vào "Unmatched Queue" để reconciliation thủ công<br/>- Auto-routing: forward ảnh đến reading workstation theo rule<br/>- Ghi MPPS nếu modality gửi (N-SET) |
| **Tiêu chí chấp nhận** | 1. C-STORE 1 ảnh CR (10 MB) thành công trong ≤ 1 giây (LAN 1 Gbps)<br/>2. C-STORE batch 300 ảnh CT (mỗi ảnh 512 KB) thành công trong ≤ 30 giây<br/>3. Ảnh không match → vào Unmatched Queue, admin thấy trên dashboard |

### FR-INT-DCM-003: DICOM Viewer Integration (WADO-RS / DICOMweb)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS truy cập ảnh DICOM qua WADO-RS/DICOMweb để hiển thị trong embedded viewer |
| **Actor** | HIS Web Client (consumer), PACS DICOMweb Server (provider) |
| **Ưu tiên** | Must |
| **Giao thức** | HTTPS REST (DICOMweb) |
| **API Endpoints** | - WADO-RS: `GET /studies/{studyUID}` (retrieve study)<br/>- QIDO-RS: `GET /studies?AccessionNumber={AN}` (search)<br/>- STOW-RS: `POST /studies` (store — cho upload từ web) [GĐ2]<br/>- Thumbnail: `GET /studies/{studyUID}/thumbnail` |
| **Quy tắc nghiệp vụ** | - Authentication: JWT token từ HIS SSO<br/>- Authorization: BS chỉ xem ảnh của BN mình khám (hoặc có quyền FULL_VIEW)<br/>- Viewer URL format: `https://{PACS_HOST}/viewer?StudyInstanceUID={UID}&token={JWT}`<br/>- Cross-origin: CORS cho phép domain HIS |
| **Tiêu chí chấp nhận** | 1. Click "Xem ảnh" trên HIS → viewer mở đúng study trong ≤ 5 giây<br/>2. User không có quyền → trả HTTP 403<br/>3. Study không tồn tại → trả HTTP 404 với message rõ ràng |

### E.2.5. Bảng Mapping HL7 Segments

#### ORM^O01 — Chỉ định CĐHA (HIS → PACS)

| HL7 Segment.Field | Tên | HIS Source Field | Ví dụ |
|-------------------|-----|------------------|-------|
| MSH-3 | Sending Application | "HIS" (cố định) | HIS |
| MSH-4 | Sending Facility | site_code | CS01 |
| MSH-5 | Receiving Application | "PACS" (cố định) | PACS |
| MSH-9 | Message Type | "ORM^O01" | ORM^O01 |
| MSH-10 | Message Control ID | UUID v4 | `a1b2c3d4-...` |
| PID-3 | Patient ID | patient.patient_id | CS0100000001 |
| PID-5 | Patient Name | patient.full_name (LAST^FIRST^MIDDLE) | NGUYEN^VAN A |
| PID-7 | DOB | patient.date_of_birth (YYYYMMDD) | 19800115 |
| PID-8 | Sex | patient.gender | M |
| PV1-2 | Patient Class | visit.visit_type (O/I/E) | O |
| PV1-3 | Assigned Location | visit.department_code | OPD^ROOM01 |
| PV1-7 | Attending Doctor | visit.doctor_id^name | DR001^NGUYEN^VAN B |
| PV1-19 | Visit Number | visit.visit_id | CS01260325-00142 |
| ORC-1 | Order Control | NW/XO/CA | NW |
| ORC-2 | Placer Order Number | order.order_id | CLS260325000142 |
| ORC-9 | Date/Time of Transaction | order.ordered_at | 20260325120000 |
| ORC-12 | Ordering Provider | order.ordering_doctor_id^name | DR001^NGUYEN^VAN B |
| OBR-2 | Placer Order Number | order.order_id | CLS260325000142 |
| OBR-4 | Universal Service ID | order.service_code^service_name^LOCAL | XRAY-CHEST^X-QUANG NGUC THANG^LOCAL |
| OBR-7 | Observation Date/Time | order.ordered_at | 20260325120000 |
| OBR-16 | Ordering Provider | order.ordering_doctor_id^name | DR001^NGUYEN^VAN B |
| OBR-27 | Quantity/Timing | priority mapping: ROUTINE→R, URGENT→U, STAT→S | ^^^20260325120000^^R |
| OBR-31 | Reason for Study | order.clinical_indication | Ho keo dai 2 tuan |

#### ORU^R01 — Kết quả CĐHA (PACS → HIS)

| HL7 Segment.Field | Tên | PACS Source Field | HIS Target Field |
|-------------------|-----|-------------------|------------------|
| MSH-3 | Sending Application | "PACS" | — |
| PID-3 | Patient ID | study.patient_id | → match patient |
| OBR-2 | Placer Order Number | — (from ORM) | → match order_id |
| OBR-3 | Filler Order Number | study.accession_number | order.accession_number |
| OBR-4 | Universal Service ID | study.procedure_code | — |
| OBR-22 | Results Report Date | report.approved_at | order.result_received_at |
| OBR-25 | Result Status | F/C/P | order.result_status |
| OBR-32 | Interpreting Physician | report.reading_physician | — |
| OBX-1 (seq 1) | Set ID | 1 | — |
| OBX-2 | Value Type | TX | — |
| OBX-3 | Observation ID | FINDING | — |
| OBX-5 | Observation Value | report.findings (text) | order.result_text (part 1) |
| OBX-1 (seq 2) | Set ID | 2 | — |
| OBX-3 | Observation ID | CONCLUSION | — |
| OBX-5 | Observation Value | report.conclusion (text) | order.result_text (part 2) |
| OBX-1 (seq 3) | Set ID | 3 | — |
| OBX-2 | Value Type | RP | — |
| OBX-3 | Observation ID | WADO_URL | — |
| OBX-5 | Observation Value | WADO-RS URL cho study | order.wado_url |

---

## E.3. Tích hợp HIS ↔ Cổng BHXH

### FR-INT-BHXH-001: Kiểm tra thẻ BHYT online

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS gọi API cổng giám định BHXH để kiểm tra giá trị thẻ BHYT real-time |
| **Actor** | HIS (caller), Cổng BHXH (provider) |
| **Ưu tiên** | Must |
| **Giao thức** | HTTPS REST / SOAP (theo API BHXH cung cấp) |
| **Input** | Mã thẻ BHYT (15 ký tự), mã cơ sở KCB, ngày khám |
| **Output** | Trạng thái thẻ (active/expired/suspended), đúng tuyến/trái tuyến, tỷ lệ đồng chi trả, 5 năm liên tục, thông tuyến |
| **Quy tắc nghiệp vụ** | - Gọi API tại thời điểm tiếp đón<br/>- Timeout API: 10 giây → nếu timeout → cho phép tiếp đón, đánh dấu "chưa kiểm tra"<br/>- Cache kết quả trong 24h cho cùng BN + cùng ngày<br/>- Ghi log mọi lần gọi API (request + response) |
| **Tiêu chí chấp nhận** | 1. Kiểm tra thẻ thành công → hiển thị kết quả trong ≤ 5 giây<br/>2. API BHXH down → tiếp đón không bị block, cảnh báo "Chưa kiểm tra BHYT"<br/>3. Thẻ hết hạn → hiển thị rõ + không cho áp dụng BHYT |

### FR-INT-BHXH-002: Gửi hồ sơ giám định BHXH (XML 4210)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS xuất và gửi hồ sơ KCB BHYT lên cổng BHXH theo format XML 4210/QĐ-BYT |
| **Actor** | HIS (sender), Cổng BHXH (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | HTTPS (upload XML) |
| **XML Tables** | XML1 (thông tin BN), XML2 (dịch vụ CLS), XML3 (thuốc), XML4 (VTYT), XML5 (tổng hợp chi phí) |
| **Quy tắc nghiệp vụ** | - Gửi theo lô: cuối ngày hoặc real-time (cấu hình)<br/>- Validate XML trước khi gửi (XSD schema validation)<br/>- Ký số XML bằng chữ ký số bệnh viện<br/>- Retry 3 lần nếu gửi thất bại<br/>- Lưu bản sao XML đã gửi vào file storage |
| **Tiêu chí chấp nhận** | 1. Xuất XML cho 300 BN/ngày hoàn thành trong ≤ 5 phút<br/>2. XML validate pass schema 100%<br/>3. Gửi thất bại → retry tự động, ghi log, alert admin |

### FR-INT-BHXH-003: Nhận kết quả giám định từ BHXH

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS nhận kết quả giám định (duyệt/từ chối/yêu cầu bổ sung) từ cổng BHXH |
| **Actor** | Cổng BHXH (sender), HIS (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | HTTPS (polling hoặc webhook — tùy API BHXH) |
| **Output** | Trạng thái từng hồ sơ: Approved / Rejected / Pending + lý do |
| **Quy tắc nghiệp vụ** | - Polling interval: mỗi 30 phút (nếu không có webhook)<br/>- Hồ sơ rejected → đánh dấu, hiển thị lý do, cho phép sửa + gửi lại<br/>- Cập nhật báo cáo đối soát BHYT |
| **Tiêu chí chấp nhận** | 1. Nhận kết quả → cập nhật trạng thái hồ sơ trong ≤ 2 phút<br/>2. Hồ sơ bị từ chối → hiển thị lý do cụ thể, cho sửa |

---

## E.4. Tích hợp HIS ↔ LIS (Xét nghiệm) [GĐ2]

### FR-INT-LIS-001: Gửi chỉ định xét nghiệm (ORM^O01)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS gửi HL7 ORM^O01 cho LIS khi bác sĩ chỉ định xét nghiệm |
| **Actor** | HIS (sender), LIS (receiver) |
| **Ưu tiên** | Should [GĐ2] |
| **Giao thức** | HL7 v2.4 over MLLP hoặc REST (tùy LIS vendor) |
| **Quy tắc nghiệp vụ** | - Mapping service code HIS → test code LIS<br/>- Hỗ trợ order group (profile xét nghiệm = nhiều test)<br/>- Barcode specimen label in tại HIS |
| **Tiêu chí chấp nhận** | 1. Chỉ định XN → LIS nhận order trong ≤ 5 giây<br/>2. Hủy chỉ định → LIS hủy order (nếu chưa thực hiện) |

### FR-INT-LIS-002: Nhận kết quả xét nghiệm (ORU^R01)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS nhận HL7 ORU^R01 từ LIS khi có kết quả xét nghiệm |
| **Actor** | LIS (sender), HIS (receiver) |
| **Ưu tiên** | Should [GĐ2] |
| **Giao thức** | HL7 v2.4 over MLLP |
| **Output** | Test name, result value, unit, reference range, abnormal flag, result status |
| **Quy tắc nghiệp vụ** | - OBX-8 (Abnormal Flag): H (High), L (Low), A (Abnormal), N (Normal)<br/>- Kết quả bất thường → highlight trên giao diện bác sĩ<br/>- Hỗ trợ partial result (result trickle in) |
| **Tiêu chí chấp nhận** | 1. Kết quả XN hiển thị trên HIS trong ≤ 10 giây sau khi LIS gửi<br/>2. Giá trị bất thường highlighted đỏ<br/>3. BS nhận notification real-time |

---

## E.5. Tích hợp Chữ ký Số

### FR-INT-SIGN-001: Ký số báo cáo CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bác sĩ CĐHA ký số (digital signature) vào báo cáo kết quả khi duyệt Final |
| **Actor** | ACT-C02 (Bác sĩ CĐHA), Hệ thống chữ ký số (CA) |
| **Ưu tiên** | Should |
| **Giao thức** | PKCS#11 (USB Token) hoặc Remote Signing API (VNPT-CA, Viettel-CA, FPT-CA) |
| **Quy tắc nghiệp vụ** | - Ký số tại thời điểm duyệt Final report<br/>- Hash nội dung báo cáo (SHA-256) → ký bằng private key<br/>- Lưu signature + certificate vào report record<br/>- Hỗ trợ cả USB Token (trình duyệt plugin) và Remote Signing (API)<br/>- Verify signature khi xem báo cáo: hiển thị "Đã ký số — {Tên BS} — {Thời gian}" |
| **Tiêu chí chấp nhận** | 1. Ký số hoàn thành trong ≤ 5 giây (USB Token) hoặc ≤ 3 giây (Remote)<br/>2. Sửa nội dung báo cáo sau ký → signature invalid, phải ký lại<br/>3. Verify signature → hiển thị thông tin chứng thư hợp lệ |

### FR-INT-SIGN-002: Ký số hồ sơ BHXH

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ký số XML 4210 trước khi gửi cổng BHXH |
| **Actor** | Hệ thống (auto) hoặc nhân viên BHYT (manual) |
| **Ưu tiên** | Must |
| **Giao thức** | XMLDSig (XML Digital Signature) |
| **Quy tắc nghiệp vụ** | - Sử dụng chứng thư số bệnh viện (organization certificate)<br/>- Ký toàn bộ XML document<br/>- BHXH yêu cầu ký số hợp lệ để nhận hồ sơ |
| **Tiêu chí chấp nhận** | 1. XML đã ký → gửi BHXH accepted<br/>2. XML không ký hoặc ký sai → BHXH reject + thông báo lỗi |

---

## E.6. Tích hợp Thông báo (Notification)

### FR-INT-NOTI-001: Thông báo Real-time trong hệ thống

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Gửi notification real-time cho user qua WebSocket khi có sự kiện quan trọng |
| **Actor** | Hệ thống (sender), User trên web (receiver) |
| **Ưu tiên** | Must |
| **Giao thức** | WebSocket (WSS) hoặc Server-Sent Events (SSE) |
| **Events** | - Kết quả CĐHA có (ORU nhận) → thông báo BS chỉ định<br/>- Kết quả XN bất thường → thông báo BS điều trị<br/>- Order CĐHA mới → thông báo KTV CĐHA<br/>- Chỉ định cấp cứu (STAT) → thông báo BS CĐHA + KTV |
| **Tiêu chí chấp nhận** | 1. Event phát sinh → user nhận notification trong ≤ 3 giây<br/>2. User offline → notification queued, hiển thị khi login<br/>3. Notification có âm thanh + badge count |

### FR-INT-NOTI-002: Gửi SMS/Email cho bệnh nhân [GĐ2]

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Gửi SMS/Email thông báo kết quả, lịch hẹn, nhắc tái khám |
| **Actor** | Hệ thống (sender), SMS Gateway / Email Server |
| **Ưu tiên** | Could [GĐ2] |
| **Giao thức** | SMS: API gateway (VNPT, Viettel, FPT); Email: SMTP/API (SendGrid, SES) |
| **Events** | - Kết quả CĐHA final → gửi link xem kết quả (nếu BN đồng ý)<br/>- Lịch hẹn tái khám: nhắc trước 1 ngày<br/>- Đơn thuốc sẵn sàng phát |
| **Quy tắc nghiệp vụ** | - Chỉ gửi khi BN đồng ý nhận thông báo (consent)<br/>- Nội dung SMS/Email không chứa chi tiết bệnh — chỉ thông báo chung + link<br/>- Rate limit: tối đa 3 SMS/BN/ngày |
| **Tiêu chí chấp nhận** | 1. SMS gửi thành công ≥ 95% (đo qua delivery report)<br/>2. Email delivery rate ≥ 98%<br/>3. BN không consent → không gửi |

---

## E.7. Tích hợp Hóa đơn Điện tử [GĐ2]

### FR-INT-EINV-001: Xuất hóa đơn điện tử

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS gọi API nhà cung cấp hóa đơn điện tử để xuất hóa đơn khi thanh toán |
| **Actor** | HIS (caller), E-Invoice Provider (VNPT Invoice, Viettel S-Invoice, FPT eInvoice, …) |
| **Ưu tiên** | Should [GĐ2] |
| **Giao thức** | REST API (HTTPS) |
| **Quy tắc nghiệp vụ** | - Tự động xuất hóa đơn khi thanh toán hoàn tất<br/>- Hỗ trợ hóa đơn điều chỉnh, hóa đơn thay thế<br/>- Lưu số hóa đơn điện tử vào invoice record |
| **Tiêu chí chấp nhận** | 1. Thanh toán → hóa đơn điện tử xuất trong ≤ 10 giây<br/>2. Hóa đơn lỗi → retry + alert, không block thanh toán |

---

## E.8. Tích hợp FHIR R4 (Lộ trình) [GĐ3]

### FR-INT-FHIR-001: FHIR API cho liên thông y tế

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cung cấp FHIR R4 API cho liên thông với các hệ thống bên ngoài (sở y tế, BV tuyến trên, telemedicine) |
| **Actor** | Hệ thống bên ngoài (consumer), HIS (FHIR server) |
| **Ưu tiên** | Could [GĐ3] |
| **FHIR Resources** | Patient, Encounter, Observation, DiagnosticReport, ImagingStudy, MedicationRequest, Condition, Procedure |
| **Giao thức** | REST API (HTTPS), JSON format |
| **Quy tắc nghiệp vụ** | - Read-only API (GĐ3 ban đầu)<br/>- OAuth 2.0 authentication<br/>- Rate limiting: 100 req/phút/client<br/>- Mapping nội bộ → FHIR resources |
| **Tiêu chí chấp nhận** | 1. GET /Patient/{id} trả đúng FHIR Patient resource<br/>2. GET /ImagingStudy?patient={id} trả danh sách study DICOM<br/>3. Validate response bằng FHIR Validator → 0 error |

---

## E.9. Xử lý Lỗi Tích hợp và Reconciliation

### E.9.1. Chiến lược xử lý lỗi

| Mã | Loại lỗi | Hành động | Escalation |
|----|----------|----------|------------|
| ERR-001 | Network timeout (HL7/DICOM) | Retry 3 lần, exponential backoff (10s, 30s, 90s) | → Dead-letter queue |
| ERR-002 | Message parse error | Reject + ghi log chi tiết + alert | → Manual review |
| ERR-003 | Business logic error (e.g., duplicate Accession) | Reject + trả ACK^AE + mô tả lỗi | → Auto-log |
| ERR-004 | Destination system down | Queue message + retry mỗi 5 phút | → Alert sau 15 phút liên tục fail |
| ERR-005 | DICOM association rejected | Retry 3 lần → log + alert | → PACS admin kiểm tra AE config |
| ERR-006 | BHXH API timeout/error | Cho phép tiến hành KCB, gửi lại sau | → Batch retry cuối ngày |

### E.9.2. Dead-Letter Queue (DLQ)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Message không gửi thành công sau tất cả retry → chuyển vào DLQ |
| **Yêu cầu** | - Giao diện quản lý DLQ: xem, replay, delete<br/>- Mỗi message DLQ có: original content, error reason, retry count, timestamps<br/>- Alert khi DLQ có > 10 messages/giờ<br/>- Replay = gửi lại message (có thể sửa trước khi gửi) |
| **Tiêu chí chấp nhận** | 1. Admin thấy DLQ dashboard với đầy đủ thông tin<br/>2. Replay message thành công → message xóa khỏi DLQ<br/>3. DLQ > 10 msg → email alert trong ≤ 5 phút |

### E.9.3. Reconciliation (Đối soát)

| Mã | Đối soát | Tần suất | Phương pháp | Output |
|----|---------|----------|-------------|--------|
| REC-001 | HIS Orders vs PACS Studies | Hàng ngày 23:30 | So sánh order (status=SENT) vs study (accession match) | Danh sách order không có study + study không có order |
| REC-002 | HIS Patient vs PACS Patient | Hàng tuần | So sánh PID list | Danh sách patient mismatch (tên, DOB khác nhau) |
| REC-003 | HIS Invoice vs BHXH XML | Trước khi gửi BHXH | So sánh tổng chi phí, số lượng dịch vụ | Báo cáo chênh lệch |
| REC-004 | HL7 Message sent vs ACK received | Real-time | Mỗi ORM gửi phải có ACK trong 30s | Alert nếu thiếu ACK |

---

## E.10. Dashboard Giám sát Tích hợp

### FR-INT-MON-001: Integration Monitoring Dashboard

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Dashboard real-time hiển thị trạng thái tất cả kênh tích hợp |
| **Actor** | ACT-S01 (SysAdmin) |
| **Ưu tiên** | Must |
| **Metrics hiển thị** | - Số message HL7 gửi/nhận hôm nay (per channel)<br/>- Số DICOM study nhận hôm nay<br/>- Message queue depth (backlog)<br/>- DLQ count<br/>- Latency trung bình per channel<br/>- Uptime per connection (HIS↔PACS, HIS↔BHXH, HIS↔LIS)<br/>- Error rate (%) per channel |
| **Alert Rules** | - Connection down > 5 phút → email + SMS<br/>- Error rate > 5% trong 15 phút → email<br/>- DLQ > 10 messages → email<br/>- Latency > 10 giây (trung bình 5 phút) → warning |
| **Tiêu chí chấp nhận** | 1. Dashboard refresh ≤ 30 giây<br/>2. Alert gửi trong ≤ 5 phút kể từ khi trigger<br/>3. Hiển thị lịch sử 7 ngày, có thể lọc theo channel |

---

## E.11. Bảng Tổng hợp Tích hợp

| # | Kênh tích hợp | Giao thức | Hướng | GĐ | Mã FR |
|---|--------------|-----------|-------|-----|-------|
| 1 | HIS → PACS (Order) | HL7 v2.4 / MLLP | Một chiều | 1 | FR-INT-HL7-001 |
| 2 | PACS → HIS (Result) | HL7 v2.4 / MLLP | Một chiều | 1 | FR-INT-HL7-002 |
| 3 | HIS → PACS (ADT) | HL7 v2.4 / MLLP | Một chiều | 1 | FR-INT-HL7-003 |
| 4 | PACS ↔ Modality (MWL) | DICOM C-FIND | Hai chiều | 1 | FR-INT-DCM-001 |
| 5 | Modality → PACS (Store) | DICOM C-STORE | Một chiều | 1 | FR-INT-DCM-002 |
| 6 | HIS → PACS (Viewer) | DICOMweb HTTPS | Một chiều | 1 | FR-INT-DCM-003 |
| 7 | HIS → BHXH (Check card) | HTTPS REST | Hai chiều | 1 | FR-INT-BHXH-001 |
| 8 | HIS → BHXH (XML 4210) | HTTPS XML | Một chiều | 1 | FR-INT-BHXH-002 |
| 9 | BHXH → HIS (Result) | HTTPS | Một chiều | 1 | FR-INT-BHXH-003 |
| 10 | HIS → LIS (Order) | HL7 v2.4 / REST | Một chiều | 2 | FR-INT-LIS-001 |
| 11 | LIS → HIS (Result) | HL7 v2.4 | Một chiều | 2 | FR-INT-LIS-002 |
| 12 | Ký số CĐHA | PKCS#11 / API | Nội bộ | 1 | FR-INT-SIGN-001 |
| 13 | Ký số BHXH | XMLDSig | Nội bộ | 1 | FR-INT-SIGN-002 |
| 14 | Notification (internal) | WebSocket | Nội bộ | 1 | FR-INT-NOTI-001 |
| 15 | SMS/Email BN | API Gateway | Một chiều | 2 | FR-INT-NOTI-002 |
| 16 | Hóa đơn điện tử | REST API | Hai chiều | 2 | FR-INT-EINV-001 |
| 17 | FHIR R4 API | REST HTTPS | Hai chiều | 3 | FR-INT-FHIR-001 |

---

*Tiếp theo: [Phần F — Yêu cầu Phi chức năng](./F-YEU-CAU-PHI-CHUC-NANG.md)*
