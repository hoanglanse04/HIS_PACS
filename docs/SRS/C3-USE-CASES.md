# PHẦN C3 — USE CASES CHI TIẾT

> **Mã phần**: SRS-PART-C3  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: UC-{STT}

---

## TỔNG QUAN USE CASES

| UC ID | Tên Use Case | Actor chính | Phân hệ | Ưu tiên |
|-------|-------------|-------------|---------|---------|
| UC-001 | Đăng ký khám bệnh ngoại trú | Nhân viên tiếp đón | HIS-REG | Must |
| UC-002 | Khám bệnh và kê chỉ định CĐHA | Bác sĩ ngoại trú | HIS-OPD | Must |
| UC-003 | Thực hiện chụp CĐHA | Kỹ thuật viên CĐHA | RIS-WL, RIS-STD | Must |
| UC-004 | Đọc ảnh và viết kết quả CĐHA | Bác sĩ CĐHA | RIS-VWR, RIS-RPT | Must |
| UC-005 | Duyệt và ký kết quả CĐHA | Bác sĩ CĐHA Senior | RIS-RPT | Must |
| UC-006 | Xem kết quả CĐHA từ HIS | Bác sĩ lâm sàng | HIS-OPD, RIS-VWR | Must |
| UC-007 | Kê đơn và phát thuốc ngoại trú | BS ngoại trú, Dược sĩ | HIS-OPD, HIS-PHA | Must |
| UC-008 | Thanh toán viện phí ngoại trú | Nhân viên viện phí | HIS-BIL, HIS-INS | Must |
| UC-009 | Nhập viện nội trú | Nhân viên tiếp đón | HIS-REG, HIS-IPD | Must |
| UC-010 | Chỉ định CĐHA cấp cứu | Bác sĩ cấp cứu | HIS-ER, RIS-WL | Must |
| UC-011 | Đối soát và gửi BHXH | Nhân viên BHYT | HIS-INS | Must |
| UC-012 | Tạo chỉ định thủ công trên RIS | KTV CĐHA | RIS-WL | Must |

---

## UC-001: Đăng ký khám bệnh ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-001 |
| **Tên** | Đăng ký khám bệnh ngoại trú |
| **Mục tiêu** | Tiếp nhận bệnh nhân, tạo hồ sơ, cấp số thứ tự khám |
| **Actor chính** | ACT-01 (Nhân viên tiếp đón) |
| **Actor phụ** | Cổng BHXH (hệ thống ngoài) |
| **Trigger** | Bệnh nhân đến quầy tiếp đón |
| **Tiền điều kiện** | 1. Nhân viên đã đăng nhập HIS<br/>2. Hệ thống hàng đợi đang hoạt động |
| **Hậu điều kiện (thành công)** | 1. Hồ sơ BN được tạo/cập nhật<br/>2. Lần khám được đăng ký<br/>3. Phiếu khám (mã vạch) được in<br/>4. BN xuất hiện trong hàng đợi phòng khám<br/>5. HL7 ADT^A04 gửi đến PACS |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | NV tiếp đón | Quét thẻ BHYT hoặc nhập CCCD/SĐT | Tìm kiếm BN trong master patient index |
| 2 | Hệ thống | — | **[Nếu BN đã có]** Hiển thị thông tin BN, pre-fill form<br/>**[Nếu BN mới]** Mở form đăng ký mới |
| 3 | NV tiếp đón | Kiểm tra/bổ sung thông tin hành chính | Validate dữ liệu bắt buộc |
| 4 | NV tiếp đón | Chọn "Kiểm tra thẻ BHYT" | Gọi API cổng BHXH kiểm tra giá trị thẻ → hiển thị kết quả: hợp lệ/hết hạn/trái tuyến |
| 5 | NV tiếp đón | Chọn phòng khám/chuyên khoa | Hiển thị danh sách phòng khám đang hoạt động + số BN đang chờ |
| 6 | NV tiếp đón | Xác nhận đăng ký | 1. Tạo/cập nhật hồ sơ BN<br/>2. Tạo mã lần khám (Visit ID)<br/>3. Phát số thứ tự<br/>4. Gửi HL7 ADT^A04 → PACS<br/>5. In phiếu khám (mã vạch) |
| 7 | NV tiếp đón | Giao phiếu khám cho BN | — |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 2a | BN trùng (cùng tên + ngày sinh, khác CCCD) | Hiển thị danh sách BN nghi trùng → NV chọn đúng BN hoặc tạo mới |
| 4a | API BHXH không phản hồi (timeout 10s) | Cho phép đăng ký không kiểm tra thẻ, đánh dấu "Chưa kiểm tra BHYT", kiểm tra sau |
| 4b | Thẻ BHYT hết hạn / không hợp lệ | Thông báo cho NV, chuyển đối tượng sang "Dịch vụ" (BN tự trả) hoặc "Miễn phí" |
| 5a | Tất cả phòng khám chuyên khoa đã đầy | Hiển thị cảnh báo "Phòng khám đông", gợi ý khung giờ/ngày khác |
| 6a | Mất kết nối PACS (HL7 gửi fail) | Đăng ký thành công trên HIS, HL7 vào retry queue, alert admin |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-001 | Mã BN (PID) format: `{MÃ_CƠ_SỞ}{SEQ8}`, VD: CS0100000001 |
| BR-002 | Mã lần khám (Visit ID): `{PID}-{YYMMDD}-{SEQ2}`, VD: CS0100000001-260325-01 |
| BR-003 | Hàng đợi ưu tiên: Cấp cứu > Ưu tiên (thương binh, người > 80 tuổi, trẻ < 6 tuổi, BHXH > 5 năm liên tục) > Thường |
| BR-004 | 1 BN có thể đăng ký nhiều phòng khám trong 1 lần đến (VD: Nội tổng quát + Mắt) |
| BR-005 | Thẻ BHYT đúng tuyến → 100% hoặc 95% (tùy đối tượng). Trái tuyến → 40%–70% |

### Yêu cầu liên quan

FR-HIS-REG-001, FR-HIS-REG-002, FR-HIS-REG-003, FR-HIS-SYS-005, FR-RIS-PAT-001

---

## UC-002: Khám bệnh và kê chỉ định CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-002 |
| **Tên** | Khám bệnh và kê chỉ định chẩn đoán hình ảnh |
| **Mục tiêu** | Bác sĩ khám bệnh nhân và tạo chỉ định CĐHA gửi sang RIS/PACS |
| **Actor chính** | ACT-02 (Bác sĩ ngoại trú) |
| **Actor phụ** | RIS/PACS (hệ thống nhận chỉ định) |
| **Trigger** | BN xuất hiện trong danh sách chờ khám của BS |
| **Tiền điều kiện** | 1. BN đã đăng ký khám (UC-001 hoàn tất)<br/>2. BS đã đăng nhập HIS, đang ở phòng khám được phân công |
| **Hậu điều kiện (thành công)** | 1. Thông tin khám được lưu<br/>2. Chỉ định CĐHA được tạo trên HIS<br/>3. HL7 ORM^O01 gửi đến RIS/PACS<br/>4. Order xuất hiện trên Worklist PACS |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS | Chọn BN từ danh sách chờ (hoặc gọi số tự động) | Hiển thị hồ sơ BN: thông tin hành chính, tiền sử, dị ứng, lịch sử khám |
| 2 | BS | Ghi nhận sinh hiệu (hoặc điều dưỡng đã nhập trước) | Validate phạm vi sinh hiệu hợp lý |
| 3 | BS | Ghi nhận lý do khám, triệu chứng, khám lâm sàng | Lưu vào EMR |
| 4 | BS | Chẩn đoán sơ bộ: nhập/chọn ICD-10 | Auto-suggest ICD-10 theo text nhập |
| 5 | BS | Chọn "Chỉ định CĐHA" | Mở form chỉ định CĐHA |
| 6 | BS | Chọn dịch vụ CĐHA từ danh mục (VD: "X-quang ngực thẳng") | Hiển thị: mã dịch vụ, giá BHYT, giá dịch vụ, phòng chụp available |
| 7 | BS | Nhập thông tin lâm sàng (Clinical Indication) | — |
| 8 | BS | Chọn mức ưu tiên (Routine / Urgent / Stat) | — |
| 9 | BS | Xác nhận chỉ định | 1. Tạo order CĐHA trên HIS<br/>2. Construct & gửi HL7 ORM^O01 (New Order) → RIS<br/>3. Đợi ACK → cập nhật trạng thái "Đã gửi PACS"<br/>4. Hiển thị thông báo thành công + Accession Number |
| 10 | BS | (Tùy chọn) Kê thêm chỉ định khác, đơn thuốc, hoặc kết thúc khám | — |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 5a | BS dùng order set (bộ chỉ định mẫu) | Chọn order set → tự động fill nhiều chỉ định (VD: "Check-up cơ bản" = XQ ngực + siêu âm bụng + ECG) |
| 9a | RIS trả ACK AE (lỗi) | Hiển thị thông báo lỗi cho BS, order trên HIS status = "Gửi lỗi", retry tự động hoặc thủ công |
| 9b | Mất kết nối RIS (timeout 30s) | Order lưu trên HIS, status = "Chờ gửi", vào retry queue, alert admin |
| 6a | Dịch vụ CĐHA ngoài danh mục BHYT | Hiển thị cảnh báo "Dịch vụ ngoài BHYT — BN tự trả", BS xác nhận |
| 6b | BS chỉ định trùng (cùng dịch vụ, cùng ngày) | Cảnh báo "Chỉ định trùng", yêu cầu BS xác nhận hoặc hủy |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-006 | Mỗi chỉ định CĐHA phải có Clinical Indication (bắt buộc nhập ≥ 10 ký tự) |
| BR-007 | Chỉ định Stat: chỉ cho phép khi BS thuộc khoa Cấp cứu hoặc có quyền "stat_order" |
| BR-008 | Chỉ định CT/MRI có tiêm thuốc cản quang → cảnh báo kiểm tra creatinine, dị ứng |
| BR-009 | Chỉ định cho BN nữ trong độ tuổi sinh sản → cảnh báo "Kiểm tra thai kỳ trước khi chụp X-quang/CT" |
| BR-010 | HL7 ORM^O01 phải chứa: PID (patient), PV1 (visit), ORC (order control = NW), OBR (service code, clinical indication, priority) |

### Yêu cầu liên quan

FR-HIS-OPD-001, FR-HIS-OPD-002, FR-HIS-OPD-003, FR-HIS-CLS-001, FR-RIS-WL-003, FR-RIS-SYN-001

---

## UC-003: Thực hiện chụp CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-003 |
| **Tên** | Thực hiện chụp chẩn đoán hình ảnh |
| **Mục tiêu** | KTV thực hiện ca chụp theo chỉ định, ảnh được lưu vào PACS |
| **Actor chính** | ACT-06 (Kỹ thuật viên CĐHA) |
| **Actor phụ** | Modality (thiết bị chụp), PACS Archive |
| **Trigger** | BN đến phòng chụp CĐHA với phiếu chỉ định |
| **Tiền điều kiện** | 1. Order đã có trên RIS worklist (từ UC-002)<br/>2. Thiết bị CĐHA online và sẵn sàng<br/>3. KTV đã đăng nhập |
| **Hậu điều kiện (thành công)** | 1. Ảnh DICOM lưu vào PACS Archive<br/>2. Order status = "Completed"<br/>3. Study sẵn sàng cho BS CĐHA đọc |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | KTV | Xác nhận BN: quét mã vạch phiếu khám hoặc tìm BN trên worklist web | Hiển thị thông tin BN + chỉ định trên worklist |
| 2 | KTV | Chọn order trên worklist web, xác nhận BN đúng | Worklist highlight order đã chọn |
| 3 | KTV | Tại máy chụp: query Modality Worklist (MWL) | Máy chụp gửi C-FIND → PACS MWL SCP → trả danh sách SPS |
| 4 | KTV | Chọn đúng BN/procedure trên màn hình máy chụp | Máy chụp auto-fill Patient Name, Patient ID, Accession Number → tránh nhập tay sai |
| 5 | KTV | Chuẩn bị BN, thực hiện chụp | Máy chụp tạo ảnh DICOM |
| 6 | Modality | (Tự động) Gửi MPPS N-CREATE (In Progress) | PACS nhận → cập nhật order status = "Đang chụp" |
| 7 | KTV | Kiểm tra chất lượng ảnh trên console máy chụp | — |
| 8 | KTV | Xác nhận chụp hoàn tất | Modality: gửi MPPS N-SET (Completed) |
| 9 | Modality | (Tự động) Gửi ảnh qua C-STORE → PACS Archive | PACS: nhận, verify, lưu trữ, index, auto-route |
| 10 | Hệ thống | — | Order status = "Đã chụp", study xuất hiện trên worklist BS CĐHA |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 3a | MWL query không trả kết quả (order chưa đến PACS) | KTV kiểm tra trên worklist web → nếu có order: báo admin kiểm tra DICOM MWL. Nếu không có: yêu cầu BS gửi lại chỉ định |
| 4a | BN sai thông tin trên MWL (sai tên, sai procedure) | KTV không chụp, báo lại bên HIS/tiếp đón sửa → chờ ADT^A08/ORM update |
| 7a | Chất lượng ảnh kém → cần chụp lại | KTV chụp lại, modality gửi thêm ảnh C-STORE, PACS lưu cả 2 (BS CĐHA sẽ loại ảnh xấu) |
| 8a | KTV hủy ca chụp (BN từ chối, BN không phù hợp) | MPPS N-SET (Discontinued) + lý do → order status = "Hủy tại máy" |
| 9a | C-STORE fail (mất kết nối PACS) | Modality retry. Nếu fail liên tục → ảnh lưu local trên modality, KTV push lại sau khi kết nối phục hồi |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-011 | KTV phải xác nhận đúng BN trước khi chụp (matching: tên + ngày sinh + Patient ID) |
| BR-012 | Mỗi study phải có Accession Number từ worklist — không cho phép chụp không có order (trừ UC-012) |
| BR-013 | Ảnh DICOM phải chứa đúng Patient ID, Accession Number từ MWL (coerce nếu cần) |
| BR-014 | Auto-routing: ảnh CT → workstation CT; ảnh MRI → workstation MRI; ảnh khác → general worklist |

### Yêu cầu liên quan

FR-RIS-WL-001, FR-RIS-WL-002, FR-RIS-MOD-003, FR-RIS-STD-001, FR-RIS-ADM-005

---

## UC-004: Đọc ảnh và viết kết quả CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-004 |
| **Tên** | Đọc ảnh và viết kết quả chẩn đoán hình ảnh |
| **Mục tiêu** | BS CĐHA đọc ảnh DICOM, viết báo cáo kết quả |
| **Actor chính** | ACT-05 (Bác sĩ CĐHA — Radiologist) |
| **Trigger** | Study mới xuất hiện trên worklist đọc (sau khi chụp xong) |
| **Tiền điều kiện** | 1. Study đã lưu trên PACS (UC-003 hoàn tất)<br/>2. BS CĐHA đăng nhập PACS viewer |
| **Hậu điều kiện (thành công)** | 1. Báo cáo ở trạng thái Draft hoặc Preliminary<br/>2. Sẵn sàng cho quy trình duyệt (UC-005) |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS CĐHA | Mở worklist đọc (reading worklist) | Hiển thị danh sách studies chờ đọc, sort theo: Stat first → Urgent → Routine, sau đó theo thời gian |
| 2 | BS CĐHA | Chọn study để đọc | 1. Mở viewer với study<br/>2. Áp dụng hanging protocol tự động<br/>3. Prefetch prior studies (nếu có)<br/>4. Mở report editor bên cạnh (split-screen) |
| 3 | BS CĐHA | Đọc ảnh: zoom, W/L, scroll, MPR, đo lường | Viewer render real-time |
| 4 | BS CĐHA | (Tùy chọn) So sánh với prior study | Load prior study → hiển thị side-by-side |
| 5 | BS CĐHA | (Tùy chọn) Đánh dấu key images | Lưu Key Object Selection (KOS) |
| 6 | BS CĐHA | Viết kết quả trong report editor: Mô tả + Kết luận | Auto-save draft mỗi 30 giây |
| 7 | BS CĐHA | (Tùy chọn) Thêm ICD-10 chẩn đoán CĐHA | Auto-suggest ICD-10 |
| 8 | BS CĐHA | Chọn "Lưu Draft" hoặc "Gửi duyệt" (Preliminary) | Report status = Draft / Preliminary |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 1a | Worklist trống | Hiển thị "Không có study chờ đọc" |
| 2a | Study chưa tải đầy đủ (ảnh đang transfer) | Hiển thị progress bar, cho phép đọc ảnh đã nhận (progressive loading) |
| 3a | Ảnh không hiển thị được (unsupported transfer syntax) | Hiển thị lỗi, gợi ý decompress/convert hoặc báo admin |
| 6a | BS CĐHA phát hiện Critical Finding | Chọn "Critical Finding" flag → trigger alert ngay cho BS chỉ định (không đợi Final) |
| 8a | Chế độ 1 cấp: BS tự duyệt | Cho phép chuyển thẳng Draft → Final (skip Preliminary) → trigger UC-005 step cuối |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-015 | BS chỉ đọc studies thuộc modality/chuyên khoa được phân công (CT, MRI, X-ray, US) |
| BR-016 | Critical Finding: phải ghi nhận BS đã thông báo cho ai, lúc nào (communication log) |
| BR-017 | Report Findings section bắt buộc ≥ 20 ký tự (tránh để trống) |
| BR-018 | Report Impression/Conclusion bắt buộc (không cho phép Final khi trống) |

### Yêu cầu liên quan

FR-RIS-VWR-001 → 009, FR-RIS-RPT-001, FR-RIS-RPT-006

---

## UC-005: Duyệt và ký kết quả CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-005 |
| **Tên** | Duyệt và ký kết quả chẩn đoán hình ảnh |
| **Mục tiêu** | BS senior duyệt, ký số báo cáo → Final, hệ thống gửi kết quả về HIS |
| **Actor chính** | ACT-13 (BS CĐHA Senior / Trưởng khoa) |
| **Actor phụ** | Hệ thống ký số, HIS (nhận kết quả) |
| **Trigger** | Report ở trạng thái Preliminary (mode 2 cấp) hoặc BS tự duyệt (mode 1 cấp) |
| **Tiền điều kiện** | 1. Report đã được viết (UC-004 hoàn tất)<br/>2. BS duyệt có quyền "approve_report"<br/>3. Chứng thư số (USB Token) sẵn sàng |
| **Hậu điều kiện (thành công)** | 1. Report status = Final<br/>2. Chữ ký số đã gắn<br/>3. HL7 ORU^R01 gửi thành công đến HIS<br/>4. BS chỉ định nhận notification |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS Senior | Mở danh sách report chờ duyệt | Hiển thị danh sách Preliminary reports, sort theo Stat first |
| 2 | BS Senior | Chọn report để duyệt | Mở viewer + report view (read-only hoặc editable) |
| 3 | BS Senior | Review ảnh và nội dung report | — |
| 4a | BS Senior | **Đồng ý**: chọn "Duyệt & Ký" | Prompt cắm USB Token |
| 4b | BS Senior | **Không đồng ý**: chọn "Trả lại" + ghi comment | Report → Draft, notification cho BS đọc kèm comment |
| 5 | BS Senior | Cắm USB Token, nhập PIN | Ký số báo cáo |
| 6 | Hệ thống | — | 1. Verify chữ ký hợp lệ<br/>2. Report status → Final<br/>3. Generate DICOM SR `[GĐ2]`<br/>4. Construct HL7 ORU^R01 → gửi HIS<br/>5. Gửi notification cho BS chỉ định |
| 7 | Hệ thống | — | HL7 ACK nhận từ HIS → xác nhận gửi thành công |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 4a-alt | BS Senior muốn sửa trước khi duyệt | Chỉnh sửa nội dung → ghi audit (modified by approver) → ký & duyệt |
| 5a | USB Token không được nhận diện | Hiển thị lỗi driver, hướng dẫn cài đặt. Cho phép duyệt không ký (nếu cấu hình cho phép — phải có lý do) |
| 5b | Chứng thư số hết hạn | Hiển thị lỗi "Chứng thư hết hạn ngày XX/XX/XXXX", không cho ký |
| 6a | HL7 ORU gửi fail | Report vẫn Final (đã ký), HL7 vào retry queue, alert admin |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-019 | Mode 2 cấp: BS đọc ≠ BS duyệt (không tự duyệt report của mình) |
| BR-020 | Sau khi Final: mọi chỉnh sửa tạo phiên bản Amended (giữ nguyên bản gốc) |
| BR-021 | Amended report → gửi lại HL7 ORU^R01 với OBR-25 = "C" (Corrected) |
| BR-022 | Thời gian mục tiêu: Report Preliminary → Final ≤ 30 phút (KPI) |

### Yêu cầu liên quan

FR-RIS-RPT-002, FR-RIS-RPT-003, FR-RIS-RPT-004, FR-RIS-SYN-004

---

## UC-006: Xem kết quả CĐHA từ HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-006 |
| **Tên** | Xem kết quả chẩn đoán hình ảnh từ giao diện HIS |
| **Mục tiêu** | BS lâm sàng xem kết quả CĐHA và ảnh DICOM ngay trong HIS |
| **Actor chính** | ACT-02 (BS ngoại trú) hoặc ACT-03 (BS nội trú) |
| **Trigger** | Notification "Kết quả CĐHA đã sẵn sàng" hoặc BS chủ động kiểm tra |
| **Tiền điều kiện** | 1. Report đã Final trên PACS (UC-005 hoàn tất)<br/>2. HL7 ORU^R01 đã được HIS nhận<br/>3. BS đang trong phiên khám/điều trị |
| **Hậu điều kiện (thành công)** | BS xem được kết quả text + ảnh DICOM, đưa ra quyết định lâm sàng |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS | Nhận notification trên HIS (popup/badge) | "Kết quả X-quang ngực đã sẵn sàng — BN Nguyễn Văn A" |
| 2 | BS | Click notification hoặc vào tab "Kết quả CLS" trong hồ sơ BN | Hiển thị danh sách chỉ định CĐHA + trạng thái |
| 3 | BS | Chọn chỉ định đã có kết quả | Hiển thị: báo cáo text (Mô tả, Kết luận), BS đọc, BS duyệt, thời gian |
| 4 | BS | Chọn "Xem ảnh" | Mở embedded PACS viewer (iframe): load study qua WADO-RS, SSO tự động |
| 5 | BS | Xem ảnh trên viewer (zoom, W/L, scroll) | Viewer nhúng trong HIS, view-only mode |
| 6 | BS | (Tùy chọn) Chọn "Mở viewer đầy đủ" | Mở tab mới → full PACS viewer với study context |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 2a | Kết quả chưa có (report chưa Final) | Hiển thị trạng thái: "Đang chờ kết quả" / "Đang chụp" / "Đã chụp — chờ đọc" |
| 4a | PACS viewer không load được (lỗi mạng, PACS down) | Hiển thị thông báo lỗi, kết quả text vẫn xem được (từ HL7 ORU đã lưu trên HIS) |
| 3a | Report bị Amended sau khi BS đã xem | Hiển thị icon "Đã sửa đổi" + highlight thay đổi + notification |

### Yêu cầu liên quan

FR-HIS-OPD-004, FR-HIS-CLS-002, FR-RIS-VWR-008, FR-RIS-SYN-004

---

## UC-007: Kê đơn và phát thuốc ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-007 |
| **Tên** | Kê đơn thuốc ngoại trú và phát thuốc |
| **Mục tiêu** | BS kê đơn thuốc cho BN ngoại trú, dược sĩ phát thuốc đúng đơn |
| **Actor chính** | ACT-02 (BS ngoại trú), ACT-08 (Dược sĩ) |
| **Trigger** | BS hoàn tất khám bệnh, cần kê đơn thuốc |
| **Tiền điều kiện** | 1. BN đã khám (chẩn đoán ICD-10 đã có)<br/>2. Danh mục thuốc đã cấu hình |
| **Hậu điều kiện (thành công)** | 1. Đơn thuốc lưu trên HIS<br/>2. Thuốc được phát đúng cho BN<br/>3. Tồn kho cập nhật |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS | Mở form kê đơn thuốc | Hiển thị form: danh sách thuốc, search, order sets |
| 2 | BS | Tìm và chọn thuốc từ danh mục | Auto-suggest, hiển thị: tên thuốc, dạng, hàm lượng, giá, BHYT/dịch vụ |
| 3 | BS | Nhập liều dùng, cách dùng, số ngày, số lượng | Tính tự động số lượng từ liều x số ngày |
| 4 | Hệ thống | — | Kiểm tra: tương tác thuốc, dị ứng, chống chỉ định |
| 5a | Hệ thống | Có cảnh báo tương tác | Hiển th�� popup: mức độ (nghiêm trọng/trung bình/thấp), mô tả, BS chọn "Tiếp tục" hoặc "Đổi thuốc" |
| 5b | Hệ thống | Không có cảnh báo | Tiếp tục |
| 6 | BS | Xác nhận đơn thuốc → gửi nhà thuốc | Đơn thuốc status = "Chờ phát", chuyển sang quầy dược |
| 7 | Dược sĩ | Nhận đơn thuốc trên màn hình | Hiển thị: tên BN, danh sách thuốc, liều, số lượng |
| 8 | Dược sĩ | Kiểm tra tồn kho, quét mã vạch thuốc, phát thuốc | Kiểm tra khớp thuốc, trừ tồn kho |
| 9 | Dược sĩ | Xác nhận đã phát | Đơn status = "Đã phát", in nhãn thuốc/hướng dẫn sử dụng |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 2a | Sử dụng order set (đơn mẫu) | Chọn order set → tự động điền danh sách thuốc, BS chỉnh sửa nếu cần |
| 8a | Thuốc hết tồn kho | Dược sĩ đề xuất thuốc thay thế (cùng hoạt chất, khác hãng) → BS xác nhận |
| 8b | Thuốc BHYT hết → thay bằng dịch vụ | Cảnh báo BN phải tự trả, yêu cầu BS/BN xác nhận |
| 4a | Thuốc gây nghiện / hướng thần | Kiểm tra giới hạn kê đơn (số ngày, số lượng theo quy định), ghi sổ riêng |

### Yêu cầu liên quan

FR-HIS-OPD-003, FR-HIS-PHA-001 → FR-HIS-PHA-005

---

## UC-008: Thanh toán viện phí ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-008 |
| **Tên** | Thanh toán viện phí ngoại trú |
| **Mục tiêu** | Thu viện phí cho BN ngoại trú, phân tách BHYT và tự trả |
| **Actor chính** | ACT-09 (Nhân viên viện phí) |
| **Trigger** | BN hoàn tất khám bệnh + nhận thuốc, đến quầy thanh toán |
| **Tiền điều kiện** | 1. Tất cả dịch vụ trong lần khám đã hoàn tất (khám, CLS, thuốc)<br/>2. Bảng giá dịch vụ đã cấu hình |
| **Hậu điều kiện (thành công)** | 1. Viện phí đã thu<br/>2. Hóa đơn/biên lai đã in<br/>3. Dữ liệu sẵn sàng cho quyết toán BHYT |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | NV viện phí | Tìm BN (quét mã vạch phiếu khám hoặc nhập PID) | Hiển thị thông tin BN + bảng kê chi phí tự động tổng hợp |
| 2 | Hệ thống | — | Tổng hợp chi phí từ: tiền khám, thuốc, CĐHA, xét nghiệm, thủ thuật |
| 3 | Hệ thống | — | Phân tách: BHYT chi trả / BN đồng chi trả / BN tự trả |
| 4 | NV viện phí | Kiểm tra bảng kê, áp dụng miễn giảm nếu có | Tính lại tổng |
| 5 | NV viện phí | Thu tiền (tiền mặt / thẻ / chuyển khoản / ví điện tử) | Ghi nhận thanh toán, in hóa đơn/biên lai |
| 6 | Hệ thống | — | Tạo bảng kê 01/BV cho BHXH, lưu dữ liệu quyết toán |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 2a | BN có chỉ định CĐHA chưa thực hiện | Cảnh báo "Chỉ định [X-quang ngực] chưa thực hiện — xác nhận thanh toán?" |
| 3a | Dịch vụ ngoài danh mục BHYT | Tách riêng "BN tự trả", highlight rõ |
| 5a | BN yêu cầu hoàn phí (hủy dịch vụ chưa thực hiện) | Tạo phiếu hoàn, trừ khỏi bảng kê, hoàn tiền |
| 5b | BN không đủ tiền | Cho phép thanh toán một phần (tạm ứng) + in biên lai tạm |

### Yêu cầu liên quan

FR-HIS-BIL-001 → FR-HIS-BIL-005, FR-HIS-INS-001, FR-HIS-INS-002

---

## UC-009: Nhập viện nội trú

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-009 |
| **Tên** | Nhập viện nội trú |
| **Mục tiêu** | Chuyển BN từ ngoại trú/cấp cứu vào nội trú, phân khoa/giường |
| **Actor chính** | ACT-01 (NV tiếp đón), ACT-02 (BS chỉ định nhập viện) |
| **Trigger** | BS ra quyết định nhập viện |
| **Tiền điều kiện** | 1. BN đã khám (có chẩn đoán nhập viện)<br/>2. Giường trống tại khoa tiếp nhận |
| **Hậu điều kiện (thành công)** | 1. Hồ sơ nội trú được tạo<br/>2. Giường được phân<br/>3. HL7 ADT^A01 gửi đến PACS<br/>4. Bệnh án nội trú sẵn sàng |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS | Tạo "Giấy nhập viện" trên HIS: chẩn đoán, khoa chỉ định | Lưu quyết định nhập viện |
| 2 | NV tiếp đón | Nhận giấy nhập viện, mở form nhập viện | Hiển thị: thông tin BN, chẩn đoán, khoa chỉ định |
| 3 | NV tiếp đón | Chọn khoa nội trú, xem sơ đồ giường | Hiển thị bed map: giường trống (xanh), đang có BN (đỏ), bảo trì (xám) |
| 4 | NV tiếp đón | Phân giường cho BN | Cập nhật trạng thái giường = "Có BN" |
| 5 | NV tiếp đón | Thu tạm ứng viện phí (nếu BN dịch vụ) | Ghi nhận tạm ứng |
| 6 | NV tiếp đón | Xác nhận nhập viện | 1. Tạo hồ sơ nội trú (Encounter Inpatient)<br/>2. Gửi HL7 ADT^A01 (Admit) → PACS<br/>3. In giấy nhập viện, phiếu tạm ứng |

### Luồng thay thế (Alternative Flows)

| ID | Điều kiện | Hành động |
|----|-----------|-----------|
| 3a | Khoa không còn giường trống | Hiển thị cảnh báo, gợi ý khoa khác hoặc giường phụ/nằm ghép |
| 1a | Nhập viện cấp cứu (từ HIS-ER) | Skip bước 2, tự động phân khoa cấp cứu, giường cấp cứu |

### Yêu cầu liên quan

FR-HIS-REG-004, FR-HIS-IPD-001, FR-HIS-IPD-002, FR-RIS-PAT-001

---

## UC-010: Chỉ định CĐHA cấp cứu

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-010 |
| **Tên** | Chỉ định chẩn đoán hình ảnh cấp cứu (STAT) |
| **Mục tiêu** | Tạo chỉ định CĐHA khẩn cấp, đảm bảo thời gian xử lý nhanh nhất |
| **Actor chính** | ACT-04 (BS cấp cứu) |
| **Trigger** | BN cấp cứu cần chụp CĐHA ngay |
| **Tiền điều kiện** | 1. BN đã được tiếp nhận cấp cứu (HIS-ER)<br/>2. BS có quyền "stat_order" |
| **Hậu điều kiện (thành công)** | 1. Order STAT xuất hiện đầu tiên trên worklist PACS<br/>2. KTV nhận alert STAT<br/>3. TAT mục tiêu: X-quang STAT ≤ 30 phút, CT STAT ≤ 60 phút |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | BS cấp cứu | Mở fast entry mode: "Chỉ định CĐHA Cấp cứu" | Form rút gọn: chọn dịch vụ + clinical indication |
| 2 | BS cấp cứu | Chọn dịch vụ, nhập indication, ưu tiên = STAT (tự động) | — |
| 3 | BS cấp cứu | Xác nhận | 1. Tạo order, priority = STAT<br/>2. HL7 ORM^O01 (priority = S) → RIS<br/>3. RIS nhận → order lên ĐẦU worklist<br/>4. Alert popup/sound cho KTV phòng chụp |
| 4 | KTV | Nhận alert STAT, ưu tiên BN cấp cứu | Chụp ngay (UC-003) |
| 5 | BS CĐHA | Nhận alert STAT study, đọc ưu tiên | Kết quả trả về nhanh nhất (UC-004, UC-005) |
| 6 | Hệ thống | — | Nếu Critical Finding → thông báo BS cấp cứu ngay (phone call + system alert) |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-023 | STAT order luôn ở đầu worklist, highlight đỏ, kèm sound alert |
| BR-024 | Cho phép BN cấp cứu chưa có đầy đủ thông tin hành chính (tối thiểu: giới tính + tuổi ước lượng) |
| BR-025 | TAT STAT được track riêng, báo cáo hàng ngày nếu vượt ngưỡng |

### Yêu cầu liên quan

FR-HIS-ER-001, FR-HIS-ER-002, FR-RIS-WL-003, FR-RIS-SYN-004

---

## UC-011: Đối soát và gửi BHXH

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-011 |
| **Tên** | Đối soát dữ liệu và gửi hồ sơ giám định BHXH |
| **Mục tiêu** | Tổng hợp, kiểm tra, gửi dữ liệu khám chữa bệnh BHYT lên cổng BHXH |
| **Actor chính** | ACT-11 (NV BHYT) |
| **Trigger** | Cuối ngày / cuối tháng, theo quy trình đối soát |
| **Tiền điều kiện** | 1. Dữ liệu KCB BHYT đã nhập đầy đủ<br/>2. Kết nối cổng BHXH hoạt động |
| **Hậu điều kiện (thành công)** | 1. Dữ liệu XML 4210 gửi thành công<br/>2. Phản hồi giám định nhận được |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | NV BHYT | Chọn khoảng thời gian, loại BN (ngoại trú/nội trú) | Tổng hợp danh sách hồ sơ KCB BHYT |
| 2 | NV BHYT | Chạy "Kiểm tra dữ liệu" | Validate: thiếu ICD-10, thiếu mã thẻ, giá vượt trần, mã thuốc sai mapping → hiển thị danh sách lỗi |
| 3 | NV BHYT | Sửa lỗi (gọi BS/dược sĩ bổ sung nếu cần) | Cập nhật hồ sơ |
| 4 | NV BHYT | Chọn "Xuất XML 4210" | Generate file XML theo format BHXH |
| 5 | NV BHYT | Chọn "Gửi lên cổng BHXH" | Upload XML → cổng BHXH, nhận mã tiếp nhận |
| 6 | NV BHYT | Kiểm tra phản hồi giám định (sau vài giờ/ngày) | Download kết quả: Duyệt / Từ chối / Yêu cầu bổ sung |
| 7 | NV BHYT | Xử lý hồ sơ từ chối: sửa → gửi lại | — |

### Yêu cầu liên quan

FR-HIS-INS-003, FR-HIS-INS-004, FR-HIS-INS-005

---

## UC-012: Tạo chỉ định thủ công trên RIS

| Hạng mục | Chi tiết |
|----------|---------|
| **UC ID** | UC-012 |
| **Tên** | Tạo chỉ định CĐHA thủ công trên RIS (Walk-in / Offline) |
| **Mục tiêu** | Tạo order trực tiếp trên RIS khi tích hợp HIS gián đoạn hoặc BN walk-in |
| **Actor chính** | ACT-06 (KTV CĐHA) hoặc ACT-12 (Tiếp đón CĐHA) |
| **Trigger** | Tích hợp HIS-RIS bị gián đoạn, hoặc BN bên ngoài (không có trên HIS) |
| **Tiền điều kiện** | 1. KTV/NV có quyền "manual_order"<br/>2. Có giấy chỉ định (bản cứng) hoặc thông tin từ BS |
| **Hậu điều kiện (thành công)** | 1. Order manual tạo trên RIS<br/>2. Study chụp xong, lưu PACS<br/>3. Sau khi HIS phục hồi → reconcile order |

### Luồng chính (Main Flow)

| Bước | Actor | Hành động | Hệ thống phản hồi |
|------|-------|-----------|-------------------|
| 1 | KTV | Chọn "Tạo chỉ định thủ công" | Mở form fast entry |
| 2 | KTV | Nhập thông tin BN (tên, ngày sinh, giới — tối thiểu) | Tìm kiếm BN trong PACS database |
| 3a | Hệ thống | BN đã có | Pre-fill thông tin |
| 3b | Hệ thống | BN mới | Tạo temporary patient record |
| 4 | KTV | Chọn dịch vụ CĐHA, nhập clinical indication | — |
| 5 | KTV | Chọn lý do manual: "Mất kết nối HIS" / "BN bên ngoài" / "Cấp cứu" | — |
| 6 | KTV | Xác nhận | Tạo order manual (flag = MANUAL), generate Accession Number |
| 7 | — | Tiếp tục UC-003 (chụp bình thường) | — |
| 8 | Admin | (Sau khi HIS phục hồi) Chạy đối soát → reconcile manual order với HIS order | Matching: tên BN + ngày + procedure → link manual order ↔ HIS order |

### Quy tắc nghiệp vụ

| Mã | Quy tắc |
|----|---------|
| BR-026 | Manual order bắt buộc ghi lý do |
| BR-027 | Manual order hiển thị icon "⚠️ Manual" trên mọi giao diện |
| BR-028 | Báo cáo đối soát hàng ngày liệt kê tất cả manual orders chưa reconcile |
| BR-029 | Manual order > 48 giờ chưa reconcile → escalate cho PACS Admin |

### Yêu cầu liên quan

FR-RIS-WL-004, FR-RIS-SYN-003

---

*Tiếp theo: [Phần C4 — Luồng End-to-End](./C4-LUONG-END-TO-END.md)*
