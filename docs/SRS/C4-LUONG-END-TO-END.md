# PHẦN C4 — LUỒNG NGHIỆP VỤ END-TO-END

> **Mã phần**: SRS-PART-C4  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## TỔNG QUAN

Ba luồng end-to-end bắt buộc mô tả quy trình xuyên suốt từ HIS → RIS/PACS → HIS, bao gồm tất cả message, protocol, actor, và timing tại từng điểm.

| # | Luồng | Phạm vi | Use Cases liên quan |
|---|-------|---------|-------------------|
| E2E-001 | Đăng ký → Khám → Chỉ định CĐHA | HIS (REG → OPD → CLS) | UC-001, UC-002 |
| E2E-002 | Nhận chỉ định → Worklist → Chụp ảnh → Lưu PACS | RIS/PACS (WL → MOD → STD) | UC-003 |
| E2E-003 | Đọc ảnh → Viết kết quả → Duyệt → Ký → Trả kết quả về HIS | RIS/PACS → HIS (VWR → RPT → SYN → OPD) | UC-004, UC-005, UC-006 |

---

## E2E-001: Đăng ký → Khám → Chỉ định CĐHA

### Sơ đồ tổng quan

```
BN đến viện
    │
    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                              HIS                                        │
│                                                                         │
│  ┌──────────┐    ┌──────────────┐    ┌───────────────────┐              │
│  │ 1. Tiếp  │───▶│ 2. Hàng đợi  │───▶│ 3. Khám bệnh     │              │
│  │    đón   │    │    phòng khám │    │    ngoại trú      │              │
│  │ (REG)    │    │              │    │    (OPD)           │              │
│  └────┬─────┘    └──────────────┘    └─────────┬─────────┘              │
│       │                                        │                        │
│       │ ADT^A04                                 │ ORM^O01               │
│       ▼                                        ▼                        │
│  ┌──────────────────────────────────────────────────────┐               │
│  │            Integration Engine (HL7/MLLP)              │               │
│  └──────────┬─────────────────────────────┬──────────────┘               │
└─────────────┼─────────────────────────────┼──────────────────────────────┘
              │ ADT^A04                     │ ORM^O01
              ▼                             ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            RIS/PACS                                      │
│  ┌───────────────┐              ┌────────────────────┐                   │
│  │ Patient Master │              │ Worklist / Order    │                   │
│  │ (đồng bộ BN)  │              │ Manager              │                   │
│  └───────────────┘              └────────────────────┘                   │
└─────────────────────────────────────────────────────────────────────────┘
```

### Chi tiết từng bước

#### Bước 1: Tiếp đón bệnh nhân

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T0 — BN đến quầy tiếp đón |
| **Actor** | NV tiếp đón (ACT-01) |
| **Hành động** | Quét thẻ BHYT → tìm BN → kiểm tra thẻ BHXH → chọn phòng khám → cấp số thứ tự |
| **Hệ thống HIS** | 1. Tạo/cập nhật Patient record (PID)<br/>2. Tạo Visit record (VID)<br/>3. Phát số thứ tự (Queue Number) |
| **Message ra** | HL7 ADT^A04 → RIS/PACS |
| **HL7 Detail** | `MSH\|^~\&\|HIS\|{SITE}\|RIS\|{SITE}\|{TIMESTAMP}\|\|ADT^A04\|{MSG_ID}\|P\|2.4`<br/>`PID\|1\|\|{PID}^^^HIS\|\|{PATIENT_NAME}\|\|{DOB}\|{SEX}\|\|\|{ADDRESS}\|\|{PHONE}`<br/>`PV1\|1\|O\|{DEPT}^{ROOM}^^{SITE}\|\|\|\|{DOCTOR_ID}^{DOCTOR_NAME}\|\|\|\|\|\|\|\|\|\|\|{VID}^^^HIS` |
| **PACS phản hồi** | Nhận ADT^A04 → tạo/cập nhật Patient Master → trả ACK^A04 (AA) |
| **Timing** | BN → PACS patient record: ≤ 3 giây |
| **Output** | Phiếu khám (mã vạch: VID), BN xuất hiện trong hàng đợi |

#### Bước 2: Hàng đợi phòng khám

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T0 + vài phút (BN di chuyển đến phòng khám) |
| **Actor** | Hệ thống hàng đợi (tự động) |
| **Hành động** | BN xuất hiện trong danh sách chờ của phòng khám được chọn |
| **Hiển thị** | Màn hình LCD phòng chờ: số thứ tự + tên BN. Màn hình BS: danh sách BN chờ khám |
| **Sorting** | Ưu tiên: Cấp cứu > Ưu tiên > Thường. Trong cùng mức: theo thứ tự đăng ký |

#### Bước 3: Khám bệnh ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T1 — BS gọi BN vào khám |
| **Actor** | BS ngoại trú (ACT-02) |
| **Hành động** | Gọi BN → ghi nhận sinh hiệu → khám → chẩn đoán ICD-10 → kê chỉ định CĐHA |
| **Data captured** | Sinh hiệu, triệu chứng, khám lâm sàng, chẩn đoán sơ bộ (ICD-10), dị ứng |

#### Bước 4: Tạo chỉ định CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T1 + vài phút |
| **Actor** | BS ngoại trú (ACT-02) |
| **Hành động** | Chọn dịch vụ CĐHA → nhập Clinical Indication → chọn Priority → xác nhận |
| **Hệ thống HIS** | 1. Tạo Order record trên HIS<br/>2. Construct HL7 ORM^O01<br/>3. Gửi qua Integration Engine → RIS |
| **Message ra** | HL7 ORM^O01 (Order Control = NW) → RIS |
| **HL7 Detail** | `MSH\|^~\&\|HIS\|{SITE}\|RIS\|{SITE}\|{TIMESTAMP}\|\|ORM^O01\|{MSG_ID}\|P\|2.4`<br/>`PID\|1\|\|{PID}^^^HIS\|\|{PATIENT_NAME}\|\|{DOB}\|{SEX}`<br/>`PV1\|1\|O\|{DEPT}^{ROOM}^^{SITE}\|\|\|\|{DOCTOR_ID}^{DOCTOR_NAME}\|\|\|\|\|\|\|\|\|\|\|{VID}^^^HIS`<br/>`ORC\|NW\|{ORDER_ID}^HIS\|\|\|\|\|\|\|^^^{TIMESTAMP}^^{PRIORITY}\|\|{DOCTOR_ID}^{DOCTOR_NAME}`<br/>`OBR\|1\|{ORDER_ID}^HIS\|\|{PROCEDURE_CODE}^{PROCEDURE_NAME}^LOCAL\|\|\|{TIMESTAMP}\|\|\|\|\|\|\|\|{DOCTOR_ID}^{DOCTOR_NAME}\|\|\|\|\|\|\|{PRIORITY}^^^{SCHEDULED_DT}^^{PRIORITY_CODE}`<br/>`OBR-31: {CLINICAL_INDICATION}` |
| **RIS phản hồi** | 1. Parse ORM → validate → tạo Order + Accession Number + Study Instance UID<br/>2. Trả ACK (AA) chứa Accession Number<br/>3. Order xuất hiện trên Worklist |
| **Timing** | ORM gửi → Order trên worklist: ≤ 5 giây |
| **Output** | HIS: trạng thái chỉ định = "Đã gửi PACS", Accession Number nhận được. RIS: order status = SCHEDULED |

### Xử lý lỗi E2E-001

| Lỗi | Phát hiện | Xử lý | Recovery |
|-----|-----------|-------|----------|
| BHXH API timeout | Bước 1 | Cho phép đăng ký, đánh dấu "Chưa kiểm tra BHYT" | Kiểm tra lại sau (batch cuối ngày) |
| ADT^A04 gửi fail | Bước 1 → PACS | Message vào retry queue, đăng ký HIS vẫn thành công | Retry 3 lần, sau đó dead-letter + alert |
| ORM^O01 gửi fail | Bước 4 → RIS | Order trên HIS status = "Chờ gửi", vào retry queue | Retry 3 lần (30s, 60s, 120s). Dead-letter + alert admin |
| RIS trả ACK AE | Bước 4 | Hiển thị lỗi cho BS, HIS lưu error message | BS sửa lỗi (thiếu clinical indication, procedure code sai) → gửi lại |
| Trùng Order | Bước 4 | RIS detect trùng Placer Order Number | Reject với ACK AE "Duplicate order" → BS kiểm tra |

---

## E2E-002: Nhận chỉ định → Worklist → Chụp ảnh → Lưu PACS

### Sơ đồ tổng quan

```
     Từ E2E-001 (ORM^O01 đã đến RIS)
              │
              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            RIS/PACS                                      │
│                                                                         │
│  ┌──────────────┐     ┌───────────────┐     ┌──────────────┐           │
│  │ 1. Order     │────▶│ 2. DICOM MWL  │────▶│ 3. Modality  │           │
│  │    Manager   │     │    SCP        │     │    (Máy chụp)│           │
│  └──────────────┘     └───────────────┘     └──────┬───────┘           │
│                                                     │                   │
│                                              MPPS   │ C-STORE           │
│                                              N-CREATE│                  │
│                                              N-SET  │                   │
│                                                     ▼                   │
│  ┌──────────────┐     ┌───────────────┐     ┌──────────────┐           │
│  │ 5. Auto-     │◀────│ 4. DICOM      │◀────│   Archive    │           │
│  │    Routing   │     │    Store SCP  │     │   (Storage)  │           │
│  └──────┬───────┘     └───────────────┘     └──────────────┘           │
│         │                                                               │
│         ▼                                                               │
│  ┌──────────────┐                                                       │
│  │ 6. Reading   │                                                       │
│  │    Worklist  │  (Study sẵn sàng cho BS CĐHA)                        │
│  └──────────────┘                                                       │
└─────────────────────────────────────────────────────────────────────────┘
```

### Chi tiết từng bước

#### Bước 1: Order Manager nhận và xử lý chỉ định

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T2 — Ngay sau E2E-001 Bước 4 |
| **Actor** | Hệ thống (Order Manager) |
| **Input** | HL7 ORM^O01 (NW) từ HIS |
| **Processing** | 1. Parse HL7 message (MSH, PID, PV1, ORC, OBR)<br/>2. Validate: PID exists in Patient Master, Procedure Code hợp lệ<br/>3. Map: HIS Procedure Code → PACS Procedure / SOP Class<br/>4. Generate: Accession Number = `{SITE}{YYMMDD}{SEQ6}`<br/>5. Generate: Study Instance UID = `1.2.840.{ORG_ROOT}.{TIMESTAMP}.{SEQ}`<br/>6. Determine: Scheduled Station AE Title (dựa trên modality type + phòng available)<br/>7. Create: Scheduled Procedure Step (SPS) record |
| **Output** | Order record (status=SCHEDULED), ACK^O01 (AA) gửi về HIS |
| **Timing** | Nhận ORM → Order created: ≤ 2 giây |

#### Bước 2: DICOM Modality Worklist (MWL)

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T3 — KTV tại máy chụp query worklist |
| **Actor** | Modality (DICOM SCU), PACS MWL SCP |
| **Protocol** | DICOM C-FIND (Modality Worklist Information Model) |
| **Query Keys** | `(0040,0100)` Scheduled Procedure Step Sequence:<br/>&nbsp;&nbsp;`(0040,0001)` Scheduled Station AE Title = {AE_TITLE máy chụp}<br/>&nbsp;&nbsp;`(0040,0002)` Scheduled Procedure Step Start Date = {TODAY}<br/>&nbsp;&nbsp;`(0008,0060)` Modality = {CT/MR/DX/...} |
| **Return Keys** | `(0010,0010)` Patient Name (Vietnamese UTF-8)<br/>`(0010,0020)` Patient ID<br/>`(0010,0030)` Patient Birth Date<br/>`(0010,0040)` Patient Sex<br/>`(0008,0050)` Accession Number<br/>`(0020,000D)` Study Instance UID<br/>`(0032,1060)` Requested Procedure Description<br/>`(0040,0007)` Scheduled Procedure Step Description<br/>`(0040,0009)` Scheduled Procedure Step ID<br/>`(0008,0080)` Institution Name<br/>`(0008,0090)` Referring Physician Name |
| **Filter** | Chỉ trả SPS có status = SCHEDULED, AE Title match |
| **Timing** | C-FIND request → response: ≤ 2 giây |

#### Bước 3: Thực hiện chụp tại Modality

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T4 — KTV chọn BN trên modality, bắt đầu chụp |
| **Actor** | KTV CĐHA (ACT-06), Modality (thiết bị) |
| **Hành động KTV** | 1. Chọn đúng BN từ worklist trên modality console<br/>2. Xác nhận thông tin BN khớp (tên, ngày sinh, PID)<br/>3. Chuẩn bị BN, positioning<br/>4. Chụp (acquire images) |
| **Modality → PACS** | **MPPS N-CREATE** (In Progress):<br/>&nbsp;&nbsp;SPS ID, Study Instance UID, Start DateTime<br/>&nbsp;&nbsp;→ PACS cập nhật order status = IN_PROGRESS |
| **Auto-populate** | Modality tự điền từ MWL: Patient Name, PID, Accession Number, Study Instance UID → **đảm bảo consistency, tránh nhập tay sai** |

#### Bước 4: Gửi ảnh về PACS (C-STORE)

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T5 — Chụp xong, modality gửi ảnh |
| **Actor** | Modality (DICOM SCU), PACS Archive (DICOM SCP) |
| **Protocol** | DICOM C-STORE |
| **Association** | Calling AE: {MODALITY_AE}, Called AE: {PACS_AE}<br/>Negotiate Transfer Syntax: JPEG Lossless preferred, Explicit VR LE fallback |
| **Data** | DICOM instances (images):<br/>&nbsp;&nbsp;CT: 100-1000 slices, 512x512, 10-500 MB/study<br/>&nbsp;&nbsp;MR: 50-500 slices, multi-series, 50-300 MB<br/>&nbsp;&nbsp;CR/DX: 1-4 images, 2000x2500, 10-50 MB<br/>&nbsp;&nbsp;US: 10-100 frames, 20-100 MB |
| **PACS Processing** | 1. Receive → verify DICOM conformance (mandatory tags)<br/>2. Coerce Patient ID / Accession Number nếu mismatch với order `[configurable]`<br/>3. Store file: `/{StudyInstanceUID}/{SeriesInstanceUID}/{SOPInstanceUID}.dcm`<br/>4. Index metadata vào database (Patient/Study/Series/Instance level)<br/>5. Calculate SHA-256 checksum, store |
| **Modality → PACS** | **MPPS N-SET** (Completed):<br/>&nbsp;&nbsp;End DateTime, Dose Information (DLP, CTDIvol cho CT), Number of Instances<br/>&nbsp;&nbsp;→ PACS cập nhật order status = COMPLETED |
| **Throughput** | ≥ 50 images/giây (sustained) |
| **Timing** | CT 300 slices: ≤ 15 giây từ start C-STORE đến store hoàn tất |

#### Bước 5: Auto-Routing

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T5 + 1-3 giây (trigger ngay sau C-STORE hoàn tất) |
| **Actor** | PACS Routing Engine (tự động) |
| **Processing** | 1. Evaluate routing rules (source AE, modality type, body part, department)<br/>2. Match rule → determine destination AE(s)<br/>3. C-STORE forward → destination workstation(s) |
| **Ví dụ** | Rule: Source=CT_SCANNER_01, Modality=CT → Dest=WS_CT_01, WS_CT_02<br/>Rule: Source=ANY, Priority=STAT → Dest=WS_EMERGENCY |
| **Log** | Mỗi forward: timestamp, study UID, source AE, dest AE, status, duration |

#### Bước 6: Study sẵn sàng trên Reading Worklist

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T5 + 3-5 giây |
| **Output** | Study xuất hiện trên reading worklist của BS CĐHA<br/>Sort: STAT → Urgent → Routine → thời gian |
| **Notification** | STAT study → popup alert cho BS CĐHA on-duty |

### Xử lý lỗi E2E-002

| Lỗi | Phát hiện | Xử lý | Recovery |
|-----|-----------|-------|----------|
| MWL trả empty | KTV query modality | Kiểm tra worklist web. Nếu có order → kiểm tra AE Title match, DICOM config | Admin fix AE config → KTV retry |
| Patient mismatch MWL | KTV verify trên console | KHÔNG CHỤP. Báo HIS sửa → chờ ADT^A08 update | HIS gửi ADT^A08 → PACS update → MWL refresh |
| C-STORE fail | Modality retry | Modality lưu local, retry khi kết nối phục hồi | Admin kiểm tra mạng, PACS disk space |
| MPPS not supported | Modality cũ | Fallback: PACS detect C-STORE → auto-update status | Cấu hình: "auto-complete on first image received" |
| Coerce fail | Accession mismatch | Log warning, lưu ảnh nhưng flag "UNMATCHED" | Admin manual match qua reconciliation tool |

---

## E2E-003: Đọc ảnh → Viết kết quả → Duyệt → Ký → Trả kết quả về HIS

### Sơ đồ tổng quan

```
     Từ E2E-002 (Study đã trên Reading Worklist)
              │
              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                            RIS/PACS                                      │
│                                                                         │
│  ┌──────────┐    ┌──────────────┐    ┌───────────────┐                  │
│  │ 1. Open  │───▶│ 2. Read &    │───▶│ 3. Submit     │                  │
│  │    Study │    │    Write     │    │    for Review  │                  │
│  │ (Viewer) │    │    Report    │    │ (Preliminary)  │                  │
│  └──────────┘    └──────────────┘    └───────┬───────┘                  │
│                                              │                          │
│                                              ▼                          │
│  ┌──────────────────┐    ┌───────────────────────────────┐              │
│  │ 5. Generate      │◀───│ 4. Approve & Sign             │              │
│  │    HL7 ORU^R01   │    │    (Digital Signature)        │              │
│  └────────┬─────────┘    └───────────────────────────────┘              │
│           │                                                             │
│           │ HL7 ORU^R01                                                 │
│           ▼                                                             │
│  ┌──────────────────┐                                                   │
│  │ Integration      │                                                   │
│  │ Engine (HL7)     │                                                   │
│  └────────┬─────────┘                                                   │
└───────────┼──────────────────────────────────────────────────────────────┘
            │ HL7 ORU^R01 (MLLP)
            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                              HIS                                        │
│                                                                         │
│  ┌──────────────────┐    ┌───────────────────┐    ┌──────────────────┐ │
│  │ 6. Receive ORU   │───▶│ 7. Update Order   │───▶│ 8. Notify BS     │ │
│  │    Parse result  │    │    Status =        │    │    & Display     │ │
│  │                  │    │    "Có kết quả"    │    │    Result        │ │
│  └──────────────────┘    └───────────────────┘    └──────────────────┘ │
│                                                           │             │
│                                                           ▼             │
│                                                   ┌──────────────┐      │
│                                                   │ 9. View in   │      │
│                                                   │    Embedded  │      │
│                                                   │    Viewer    │      │
│                                                   └──────────────┘      │
└─────────────────────────────────────────────────────────────────────────┘
```

### Chi tiết từng bước

#### Bước 1: Mở Study trên Viewer

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T6 — BS CĐHA chọn study từ reading worklist |
| **Actor** | BS CĐHA (ACT-05) |
| **Hành động** | Double-click study trên worklist |
| **Viewer Processing** | 1. QIDO-RS query: `GET /studies/{studyUID}` → metadata<br/>2. Apply Hanging Protocol (match modality + body part)<br/>3. WADO-RS retrieve: progressive load images<br/>4. Prefetch prior studies (QIDO-RS query: same patient + same modality)<br/>5. Open report editor panel (split-screen) |
| **Timing** | Click → first image displayed: ≤ 5 giây (CT/MR), ≤ 3 giây (CR/DX) |

#### Bước 2: Đọc ảnh và viết kết quả

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T6 + vài phút (tùy complexity) |
| **Actor** | BS CĐHA (ACT-05) |
| **Hành động đọc ảnh** | Scroll, W/L, zoom, MPR, đo lường, so sánh prior studies |
| **Hành động viết kết quả** | 1. Chọn template (hoặc auto-match theo procedure)<br/>2. Viết Findings (Mô tả)<br/>3. Viết Impression/Conclusion (Kết luận)<br/>4. (Tùy chọn) Thêm Recommendation (Khuyến nghị)<br/>5. (Tùy chọn) Chọn ICD-10 chẩn đoán CĐHA<br/>6. (Tùy chọn) Mark key images<br/>7. (Tùy chọn) Flag Critical Finding |
| **Auto-save** | Draft lưu tự động mỗi 30 giây |
| **Report status** | Draft |

#### Bước 3: Gửi duyệt (Preliminary)

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T7 — BS hoàn tất viết kết quả |
| **Actor** | BS CĐHA (ACT-05) |
| **Hành động** | Click "Gửi duyệt" (mode 2 cấp) hoặc "Duyệt & Ký" (mode 1 cấp) |
| **Mode 2 cấp** | Report status: Draft → Preliminary. Notification cho BS Senior |
| **Mode 1 cấp** | Skip → đến thẳng Bước 4 (BS tự duyệt + ký) |

#### Bước 4: Duyệt và Ký số

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T8 — BS Senior nhận notification và duyệt |
| **Actor** | ACT-13 (BS CĐHA Senior) — mode 2 cấp; hoặc ACT-05 — mode 1 cấp |
| **Hành động** | 1. Review report content + ảnh<br/>2. Quyết định: Approve / Reject (trả lại)<br/>3. Nếu Approve: cắm USB Token → nhập PIN → ký số |
| **Digital Signature** | 1. Hash report content (SHA-256)<br/>2. Sign hash bằng private key (RSA 2048 / ECDSA) trên USB Token<br/>3. Embed signature + certificate info vào report metadata<br/>4. Verify: certificate chain, not expired, not revoked |
| **Report status** | Preliminary → **Final** (sau khi ký thành công) |
| **Timing** | Ký số: ≤ 10 giây (bao gồm PIN entry) |

#### Bước 5: Generate HL7 ORU^R01

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T8 + 1-2 giây (trigger tự động khi report → Final) |
| **Actor** | Hệ thống (tự động) |
| **Processing** | Construct HL7 ORU^R01 message |
| **HL7 Detail** | `MSH\|^~\&\|RIS\|{SITE}\|HIS\|{SITE}\|{TIMESTAMP}\|\|ORU^R01\|{MSG_ID}\|P\|2.4`<br/>`PID\|1\|\|{PID}^^^HIS\|\|{PATIENT_NAME}\|\|{DOB}\|{SEX}`<br/>`PV1\|1\|O\|{DEPT}^^^\|{SITE}`<br/>`ORC\|RE\|{ORDER_ID}^HIS\|{ACCESSION}^RIS`<br/>`OBR\|1\|{ORDER_ID}^HIS\|{ACCESSION}^RIS\|{PROC_CODE}^{PROC_NAME}^LOCAL\|\|\|{ORDER_DT}\|\|\|\|\|\|\|{REPORT_DT}\|\|{READING_DR}\|\|\|\|\|\|\|F`<br/>`OBX\|1\|TX\|FINDINGS^Mô tả\|\|{FINDINGS_TEXT}\|\|\|\|\|\|F`<br/>`OBX\|2\|TX\|IMPRESSION^Kết luận\|\|{IMPRESSION_TEXT}\|\|\|\|\|\|F`<br/>`OBX\|3\|TX\|RECOMMENDATION^Khuyến nghị\|\|{RECOMMENDATION_TEXT}\|\|\|\|\|\|F`<br/>`OBX\|4\|RP\|WADO_URL^Link xem ảnh\|\|https://{PACS_HOST}/viewer?StudyInstanceUID={STUDY_UID}\|\|\|\|\|\|F`<br/>`OBX\|5\|CWE\|ICD10^Chẩn đoán\|\|{ICD_CODE}^{ICD_DESC}^I10\|\|\|\|\|\|F` |
| **OBR-25 (Result Status)** | `F` = Final, `C` = Corrected (Amended) |

#### Bước 6: HIS nhận ORU^R01

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T8 + 3-5 giây |
| **Actor** | HIS Integration Engine (tự động) |
| **Processing** | 1. Receive HL7 ORU^R01 qua MLLP<br/>2. Parse: extract Accession Number, Order ID, result text, WADO URL, ICD-10<br/>3. Match: Order ID trên HIS<br/>4. Update HIS Order status = "Đã có kết quả"<br/>5. Store result text (Findings, Impression, Recommendation)<br/>6. Store WADO URL<br/>7. Send ACK (AA) → RIS |
| **Timing** | ORU received → Order status updated: ≤ 2 giây |

#### Bước 7: Cập nhật trạng thái chỉ định trên HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Trạng thái trước** | "Đã gửi PACS" hoặc "Đang chụp" |
| **Trạng thái sau** | "Đã có kết quả" (icon: ✅) |
| **Hiển thị** | Trong hồ sơ BN: chỉ định CĐHA chuyển sang xanh, có link "Xem kết quả" |

#### Bước 8: Thông báo cho BS chỉ định

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T8 + 5-10 giây |
| **Actor** | Hệ thống (tự động) → BS chỉ định (ACT-02 / ACT-03) |
| **Notification** | WebSocket event → popup/badge trên HIS:<br/>"Kết quả {PROCEDURE_NAME} cho BN {PATIENT_NAME} đã sẵn sàng" |
| **Critical Finding** | Notification đặc biệt: "⚠️ CẢNH BÁO: Phát hiện bất thường nghiêm trọng — {PROCEDURE} — BN {PATIENT_NAME}". Yêu cầu BS acknowledge |

#### Bước 9: BS lâm sàng xem kết quả + ảnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Thời điểm** | T9 — BS click notification hoặc mở hồ sơ BN |
| **Actor** | BS lâm sàng (ACT-02 / ACT-03) |
| **Xem kết quả text** | Hiển thị inline trên HIS: Mô tả, Kết luận, Khuyến nghị, BS đọc, BS duyệt |
| **Xem ảnh DICOM** | Click "Xem ảnh" → embedded viewer:<br/>1. HIS construct URL: `https://{PACS_HOST}/viewer?StudyInstanceUID={UID}&token={JWT_TOKEN}`<br/>2. SSO: JWT token từ HIS session → PACS verify → grant access<br/>3. Viewer load study qua WADO-RS<br/>4. View-only mode (BS lâm sàng không chỉnh report) |
| **Timing** | Click "Xem ảnh" → viewer hiển thị: ≤ 5 giây |

### Xử lý lỗi E2E-003

| Lỗi | Phát hiện | Xử lý | Recovery |
|-----|-----------|-------|----------|
| USB Token không nhận | Bước 4 | Hiển thị hướng dẫn driver. Cho phép duyệt không ký (cần lý do) | IT hỗ trợ driver. BS ký bổ sung sau |
| Chứng thư hết hạn | Bước 4 | Block ký. Hiển thị thông tin hết hạn | Gia hạn chứng thư với CA |
| HL7 ORU gửi fail | Bước 5→6 | Report vẫn Final trên PACS. ORU vào retry queue | Retry 3 lần (30s, 60s, 120s). Dead-letter + alert |
| HIS reject ORU (ACK AE) | Bước 6 | Log lỗi, gửi alert | Admin kiểm tra: Order ID mismatch? Encoding issue? Fix → re-send |
| PACS viewer fail load | Bước 9 | Kết quả text vẫn xem được. Hiển thị "Viewer không khả dụng" | Kiểm tra PACS server, CORS, SSL certificate |
| Report Amended sau khi HIS đã nhận | Bước 5 lặp lại | Gửi ORU^R01 mới với OBR-25 = "C" (Corrected). HIS update kết quả + thông báo BS | BS nhận notification "Kết quả đã được sửa đổi" |

### Timeline tổng hợp (KPI mục tiêu)

```
T0: BN đến viện
    │  Đăng ký: ≤ 5 phút
T1: Bắt đầu khám
    │  Khám + chỉ định: 10-15 phút (phụ thuộc BS)
    │  HL7 ORM → RIS: ≤ 5 giây
T2: Order trên RIS worklist
    │  BN di chuyển đến phòng chụp: 5-15 phút
T3: KTV query MWL: ≤ 2 giây
T4: Chụp ảnh: 5-30 phút (phụ thuộc loại)
T5: Ảnh vào PACS: ≤ 15 giây (CT 300 slices)
    │  Auto-route + index: ≤ 3 giây
T6: BS CĐHA mở study: ≤ 5 giây
    │  Đọc + viết kết quả: 5-30 phút (phụ thuộc complexity)
T7: Gửi duyệt
    │  BS Senior duyệt: 5-30 phút
T8: Ký số + Final: ≤ 10 giây
    │  HL7 ORU → HIS: ≤ 5 giây
    │  Notification BS: ≤ 10 giây
T9: BS lâm sàng xem kết quả + ảnh

═══════════════════════════════════════════
TAT mục tiêu (Order → Report Final):
  X-quang Routine:  ≤ 60 phút
  X-quang STAT:     ≤ 30 phút
  CT/MRI Routine:   ≤ 120 phút
  CT STAT:          ≤ 60 phút
  Siêu âm Routine:  ≤ 45 phút
═══════════════════════════════════════════
```

---

*Tiếp theo: [Phần C5 — Ma trận Truy vết](./C5-MA-TRAN-TRUY-VET.md)*
