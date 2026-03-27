# PHẦN J — PHỤ LỤC

> **Mã phần**: SRS-PART-J  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## J.1. Danh mục API dự kiến

### J.1.1. HIS REST API

| # | Method | Endpoint | Mô tả | Auth | GĐ |
|---|--------|----------|-------|------|-----|
| 1 | POST | `/api/v1/patients` | Đăng ký bệnh nhân mới | JWT + RBAC | GĐ1 |
| 2 | GET | `/api/v1/patients/{pid}` | Lấy thông tin BN theo PID | JWT + RBAC | GĐ1 |
| 3 | PUT | `/api/v1/patients/{pid}` | Cập nhật thông tin BN | JWT + RBAC | GĐ1 |
| 4 | GET | `/api/v1/patients/search?q={keyword}` | Tìm BN (tên, CCCD, mã BHYT, PID) | JWT + RBAC | GĐ1 |
| 5 | POST | `/api/v1/visits` | Tạo lượt khám (ngoại trú / nội trú) | JWT + RBAC | GĐ1 |
| 6 | GET | `/api/v1/visits/{visitId}` | Lấy thông tin lượt khám | JWT + RBAC | GĐ1 |
| 7 | PUT | `/api/v1/visits/{visitId}/status` | Cập nhật trạng thái lượt khám | JWT + RBAC | GĐ1 |
| 8 | GET | `/api/v1/queues/{roomId}` | Danh sách hàng đợi theo phòng khám | JWT + RBAC | GĐ1 |
| 9 | POST | `/api/v1/queues/{roomId}/call` | Gọi số tiếp theo | JWT + RBAC | GĐ1 |
| 10 | POST | `/api/v1/encounters` | Tạo phiếu khám (bắt đầu khám) | JWT + RBAC | GĐ1 |
| 11 | PUT | `/api/v1/encounters/{encId}` | Cập nhật phiếu khám (chẩn đoán, ghi chú) | JWT + RBAC | GĐ1 |
| 12 | POST | `/api/v1/orders` | Tạo chỉ định CLS (CĐHA, xét nghiệm) | JWT + RBAC | GĐ1 |
| 13 | PUT | `/api/v1/orders/{orderId}` | Sửa chỉ định | JWT + RBAC | GĐ1 |
| 14 | DELETE | `/api/v1/orders/{orderId}` | Hủy chỉ định (soft delete) | JWT + RBAC | GĐ1 |
| 15 | GET | `/api/v1/orders?visitId={id}&status={s}` | Danh sách chỉ định theo lượt khám | JWT + RBAC | GĐ1 |
| 16 | POST | `/api/v1/prescriptions` | Kê đơn thuốc | JWT + RBAC | GĐ1 |
| 17 | GET | `/api/v1/prescriptions/{rxId}` | Lấy thông tin đơn thuốc | JWT + RBAC | GĐ1 |
| 18 | POST | `/api/v1/prescriptions/{rxId}/dispense` | Phát thuốc (dược sĩ xác nhận) | JWT + RBAC | GĐ1 |
| 19 | GET | `/api/v1/drug-interactions/check` | Kiểm tra tương tác thuốc | JWT + RBAC | GĐ1 |
| 20 | POST | `/api/v1/invoices` | Tạo phiếu thu / hóa đơn | JWT + RBAC | GĐ1 |
| 21 | POST | `/api/v1/invoices/{invId}/pay` | Xác nhận thanh toán | JWT + RBAC | GĐ1 |
| 22 | GET | `/api/v1/invoices/{invId}` | Lấy thông tin hóa đơn | JWT + RBAC | GĐ1 |
| 23 | POST | `/api/v1/insurance/check-card` | Kiểm tra thẻ BHYT (proxy → cổng BHXH) | JWT + RBAC | GĐ1 |
| 24 | POST | `/api/v1/insurance/submit-xml` | Gửi XML 4210 lên cổng BHXH | JWT + RBAC | GĐ1 |
| 25 | GET | `/api/v1/insurance/reconciliation` | Đối soát dữ liệu BHXH | JWT + RBAC | GĐ1 |
| 26 | GET | `/api/v1/beds?ward={wardId}` | Sơ đồ giường bệnh theo khoa | JWT + RBAC | GĐ1 |
| 27 | PUT | `/api/v1/beds/{bedId}/assign` | Phân giường BN nội trú | JWT + RBAC | GĐ1 |
| 28 | POST | `/api/v1/medical-records` | Tạo bệnh án điện tử | JWT + RBAC | GĐ1 |
| 29 | PUT | `/api/v1/medical-records/{mrId}` | Cập nhật bệnh án | JWT + RBAC | GĐ1 |
| 30 | GET | `/api/v1/pharmacy/inventory?drug={code}` | Kiểm tra tồn kho thuốc | JWT + RBAC | GĐ1 |
| 31 | POST | `/api/v1/pharmacy/import` | Nhập kho thuốc | JWT + RBAC | GĐ1 |
| 32 | POST | `/api/v1/pharmacy/export` | Xuất kho thuốc | JWT + RBAC | GĐ1 |
| 33 | GET | `/api/v1/catalogs/icd10?q={keyword}` | Tra cứu ICD-10 | JWT | GĐ1 |
| 34 | GET | `/api/v1/catalogs/drugs?q={keyword}` | Tra cứu danh mục thuốc | JWT | GĐ1 |
| 35 | GET | `/api/v1/catalogs/services?q={keyword}` | Tra cứu danh mục dịch vụ | JWT | GĐ1 |
| 36 | GET | `/api/v1/catalogs/departments` | Danh sách khoa/phòng | JWT | GĐ1 |
| 37 | GET | `/api/v1/catalogs/doctors` | Danh sách bác sĩ | JWT | GĐ1 |
| 38 | GET | `/api/v1/reports/{reportCode}` | Lấy báo cáo theo mã | JWT + RBAC | GĐ1 |
| 39 | POST | `/api/v1/reports/generate` | Sinh báo cáo (async) | JWT + RBAC | GĐ1 |
| 40 | GET | `/api/v1/dashboard/summary` | Dashboard tổng quan (BN, doanh thu, giường) | JWT + RBAC | GĐ1 |

### J.1.2. HIS REST API — Giai đoạn 2 & 3

| # | Method | Endpoint | Mô tả | Auth | GĐ |
|---|--------|----------|-------|------|-----|
| 41 | POST | `/api/v1/lab-orders` | Chỉ định xét nghiệm → LIS | JWT + RBAC | GĐ2 |
| 42 | GET | `/api/v1/lab-results/{orderId}` | Nhận kết quả xét nghiệm từ LIS | JWT + RBAC | GĐ2 |
| 43 | POST | `/api/v1/e-invoices` | Phát hành hóa đơn điện tử | JWT + RBAC | GĐ2 |
| 44 | POST | `/api/v1/notifications/sms` | Gửi SMS thông báo BN | JWT + RBAC | GĐ2 |
| 45 | POST | `/api/v1/notifications/email` | Gửi email thông báo BN | JWT + RBAC | GĐ2 |
| 46 | POST | `/api/v1/telehealth/sessions` | Tạo phiên khám từ xa | JWT + RBAC | GĐ3 |
| 47 | GET | `/api/v1/analytics/trends` | Phân tích xu hướng nâng cao | JWT + RBAC | GĐ3 |

### J.1.3. RIS/PACS REST API

| # | Method | Endpoint | Mô tả | Auth | GĐ |
|---|--------|----------|-------|------|-----|
| 1 | GET | `/api/v1/worklist?date={d}&modality={m}` | Danh sách worklist RIS | JWT + RBAC | GĐ1 |
| 2 | POST | `/api/v1/worklist/manual` | Tạo order thủ công trên RIS | JWT + RBAC | GĐ1 |
| 3 | GET | `/api/v1/studies?accession={acc}` | Tra cứu study theo accession number | JWT + RBAC | GĐ1 |
| 4 | GET | `/api/v1/studies/{studyUid}` | Thông tin chi tiết study | JWT + RBAC | GĐ1 |
| 5 | GET | `/api/v1/studies/{studyUid}/series` | Danh sách series trong study | JWT + RBAC | GĐ1 |
| 6 | POST | `/api/v1/reports` | Tạo báo cáo CĐHA | JWT + RBAC | GĐ1 |
| 7 | PUT | `/api/v1/reports/{reportId}` | Cập nhật báo cáo (draft → preliminary) | JWT + RBAC | GĐ1 |
| 8 | PUT | `/api/v1/reports/{reportId}/finalize` | Duyệt và ký kết quả (Final) | JWT + RBAC | GĐ1 |
| 9 | PUT | `/api/v1/reports/{reportId}/amend` | Sửa kết quả (Amended) | JWT + RBAC | GĐ1 |
| 10 | GET | `/api/v1/reports/{reportId}` | Lấy báo cáo CĐHA | JWT + RBAC | GĐ1 |
| 11 | GET | `/api/v1/reports/{reportId}/pdf` | Xuất PDF báo cáo | JWT + RBAC | GĐ1 |
| 12 | POST | `/api/v1/sign/{reportId}` | Ký số báo cáo (PKCS#11/Remote) | JWT + RBAC + MFA | GĐ1 |
| 13 | GET | `/api/v1/modalities` | Danh sách thiết bị CĐHA | JWT + RBAC | GĐ1 |
| 14 | PUT | `/api/v1/modalities/{aeTitle}/status` | Cập nhật trạng thái thiết bị | JWT + RBAC | GĐ1 |
| 15 | GET | `/api/v1/statistics/tat` | Thống kê TAT theo loại/BS | JWT + RBAC | GĐ1 |
| 16 | GET | `/api/v1/statistics/workload` | Thống kê workload BS CĐHA | JWT + RBAC | GĐ1 |
| 17 | POST | `/api/v1/routing-rules` | Cấu hình auto-routing rule | JWT + Admin | GĐ1 |
| 18 | GET | `/api/v1/storage/status` | Thông tin dung lượng lưu trữ | JWT + Admin | GĐ1 |

### J.1.4. DICOMweb Endpoints (chuẩn IHE)

| # | Method | Endpoint | Mô tả | Auth | Chuẩn |
|---|--------|----------|-------|------|-------|
| 1 | GET | `/dicomweb/studies` (QIDO-RS) | Truy vấn studies | JWT / Basic | DICOM PS3.18 |
| 2 | GET | `/dicomweb/studies/{uid}/series` (QIDO-RS) | Truy vấn series | JWT / Basic | DICOM PS3.18 |
| 3 | GET | `/dicomweb/studies/{uid}/series/{uid}/instances` (QIDO-RS) | Truy vấn instances | JWT / Basic | DICOM PS3.18 |
| 4 | GET | `/dicomweb/studies/{uid}` (WADO-RS) | Lấy toàn bộ study | JWT / Basic | DICOM PS3.18 |
| 5 | GET | `/dicomweb/studies/{uid}/series/{uid}/instances/{uid}` (WADO-RS) | Lấy 1 instance | JWT / Basic | DICOM PS3.18 |
| 6 | GET | `/dicomweb/studies/{uid}/series/{uid}/instances/{uid}/rendered` | Lấy ảnh rendered (JPEG/PNG) | JWT / Basic | DICOM PS3.18 |
| 7 | GET | `/dicomweb/studies/{uid}/series/{uid}/instances/{uid}/thumbnail` | Lấy thumbnail | JWT / Basic | DICOM PS3.18 |
| 8 | POST | `/dicomweb/studies` (STOW-RS) | Upload DICOM instances | JWT + Admin | DICOM PS3.18 |

### J.1.5. HL7 v2.4 Messages (MLLP)

| # | Message Type | Trigger | Hướng | Port | Mô tả |
|---|-------------|---------|-------|------|-------|
| 1 | ADT^A04 | Đăng ký BN | HIS → RIS | 2575 | Register a Patient |
| 2 | ADT^A08 | Cập nhật BN | HIS → RIS | 2575 | Update Patient Information |
| 3 | ADT^A01 | Nhập viện | HIS → RIS | 2575 | Admit/Visit Notification |
| 4 | ADT^A02 | Chuyển khoa | HIS → RIS | 2575 | Transfer a Patient |
| 5 | ADT^A03 | Ra viện | HIS → RIS | 2575 | Discharge/End Visit |
| 6 | ORM^O01 (NW) | Chỉ định mới | HIS → RIS | 2575 | New Order |
| 7 | ORM^O01 (XO) | Sửa chỉ định | HIS → RIS | 2575 | Change Order |
| 8 | ORM^O01 (CA) | Hủy chỉ định | HIS → RIS | 2575 | Cancel Order |
| 9 | ORU^R01 (F) | Kết quả Final | RIS → HIS | 2576 | Observation Result — Final |
| 10 | ORU^R01 (C) | Kết quả Amended | RIS → HIS | 2576 | Observation Result — Corrected |
| 11 | ACK | Xác nhận | Hai chiều | — | General Acknowledgment |

### J.1.6. DICOM Services (TCP)

| # | Service | Role | Port | AE Title (mẫu) | Mô tả |
|---|---------|------|------|----------------|-------|
| 1 | C-FIND (MWL) | SCP | 104 | PACS_MWL | Modality Worklist Provider |
| 2 | C-STORE | SCP | 104 | PACS_STORE | Nhận ảnh từ Modalities |
| 3 | N-CREATE / N-SET (MPPS) | SCP | 104 | PACS_MPPS | Modality Performed Procedure Step |
| 4 | C-FIND (Study/Series) | SCP | 104 | PACS_QR | Query/Retrieve SCP |
| 5 | C-MOVE | SCP | 104 | PACS_QR | Retrieve — push mode |
| 6 | C-GET | SCP | 104 | PACS_QR | Retrieve — pull mode |
| 7 | C-STORE | SCU | — | PACS_ROUTE | Auto-routing đến workstation |
| 8 | DICOM Print | SCU | 2001 | PACS_PRINT | In film DICOM |

---

## J.2. Danh mục Báo cáo Chuẩn

### J.2.1. Báo cáo Bắt buộc — Bộ Y tế / BHXH

| Mã | Tên báo cáo | Cơ sở pháp lý | Tần suất | Module | GĐ |
|----|------------|---------------|----------|--------|-----|
| RPT-BYT-001 | Báo cáo hoạt động bệnh viện (mẫu BYT) | TT 54/2017/TT-BYT | Tháng, Quý, Năm | HIS-RPT | GĐ1 |
| RPT-BYT-002 | Báo cáo bệnh truyền nhiễm | Luật Phòng chống bệnh truyền nhiễm | Tuần, Tháng | HIS-RPT | GĐ1 |
| RPT-BYT-003 | Báo cáo sử dụng kháng sinh (AMS) | Quyết định 772/QĐ-BYT | Quý | HIS-PHA | GĐ1 |
| RPT-BYT-004 | Báo cáo thuốc gây nghiện / hướng thần | TT 20/2017/TT-BYT | Tháng | HIS-PHA | GĐ1 |
| RPT-BHXH-001 | Báo cáo 20 — Quyết toán chi phí KCB BHYT | QĐ 4210/QĐ-BYT | Tháng, Quý | HIS-INS | GĐ1 |
| RPT-BHXH-002 | Báo cáo 21 — Tổng hợp chi phí KCB ngoại trú | QĐ 4210/QĐ-BYT | Tháng | HIS-INS | GĐ1 |
| RPT-BHXH-003 | Mẫu C79a-HD — Bảng kê chi phí KCB | QĐ 4210/QĐ-BYT | Theo BN | HIS-BIL | GĐ1 |
| RPT-BHXH-004 | XML 4210 — File dữ liệu gửi cổng BHXH | QĐ 4210/QĐ-BYT | Hàng ngày / batch | HIS-INS | GĐ1 |
| RPT-BHXH-005 | Báo cáo đối soát BHXH (gửi vs duyệt) | Quy trình BHXH | Tháng | HIS-INS | GĐ1 |

### J.2.2. Báo cáo Quản trị & Vận hành

| Mã | Tên báo cáo | Mô tả | Tần suất | Module | GĐ |
|----|------------|-------|----------|--------|-----|
| RPT-OPR-001 | Thống kê lượt khám theo ngày/khoa/BS | Số lượt khám, phân theo loại (BHYT/dịch vụ) | Ngày, Tuần, Tháng | HIS-RPT | GĐ1 |
| RPT-OPR-002 | Thống kê BN nội trú | Nhập viện, ra viện, chuyển khoa, công suất giường | Ngày, Tháng | HIS-RPT | GĐ1 |
| RPT-OPR-003 | Báo cáo doanh thu tổng hợp | Thu tiền theo nguồn (BHYT, dịch vụ, viện phí) | Ngày, Tháng | HIS-BIL | GĐ1 |
| RPT-OPR-004 | Báo cáo doanh thu theo khoa/dịch vụ | Chi tiết doanh thu theo từng khoa, loại dịch vụ | Tháng | HIS-BIL | GĐ1 |
| RPT-OPR-005 | Báo cáo xuất nhập tồn thuốc | Tồn đầu kỳ + nhập − xuất = tồn cuối kỳ | Tháng | HIS-RPH | GĐ1 |
| RPT-OPR-006 | Cảnh báo thuốc sắp hết hạn | Danh sách thuốc hết hạn trong 30/60/90 ngày | Tuần | HIS-RPH | GĐ1 |
| RPT-OPR-007 | Thống kê sử dụng thuốc theo BS | Top thuốc, chi phí thuốc theo BS chỉ định | Tháng | HIS-PHA | GĐ1 |
| RPT-OPR-008 | Báo cáo ICD-10 — Mô hình bệnh tật | Phân bổ bệnh theo ICD-10, top 10 bệnh | Quý, Năm | HIS-RPT | GĐ1 |
| RPT-OPR-009 | Thống kê thời gian chờ khám | Trung bình, P90 thời gian chờ theo phòng khám | Ngày, Tuần | HIS-RPT | GĐ1 |
| RPT-OPR-010 | Dashboard ban giám đốc | KPI tổng hợp: BN, doanh thu, giường, CĐHA | Real-time | HIS-RPT | GĐ1 |

### J.2.3. Báo cáo CĐHA / PACS

| Mã | Tên báo cáo | Mô tả | Tần suất | Module | GĐ |
|----|------------|-------|----------|--------|-----|
| RPT-RIS-001 | Thống kê ca chụp CĐHA | Số ca theo modality, phòng chụp, loại dịch vụ | Ngày, Tháng | RIS-RPT | GĐ1 |
| RPT-RIS-002 | Báo cáo TAT (Turnaround Time) | Thời gian từ chỉ định → kết quả (P50, P90, P95) | Tuần, Tháng | RIS-RPT | GĐ1 |
| RPT-RIS-003 | Workload bác sĩ CĐHA | Số ca đọc/ký theo BS, TAT trung bình | Tháng | RIS-RPT | GĐ1 |
| RPT-RIS-004 | Thống kê dung lượng PACS | Dung lượng theo tier (HOT/WARM/COLD), trend tăng trưởng | Tháng | RIS-ADM | GĐ1 |
| RPT-RIS-005 | Báo cáo Critical Findings | Danh sách kết quả critical (urgent notification) | Real-time, Tháng | RIS-RPT | GĐ1 |
| RPT-RIS-006 | Đối soát chỉ định HIS ↔ PACS | Chỉ định gửi vs nhận, study vs report | Ngày | RIS-SYN | GĐ1 |
| RPT-RIS-007 | Báo cáo lỗi DICOM | Danh sách lỗi C-STORE, MWL, routing | Tuần | RIS-ADM | GĐ1 |

### J.2.4. Báo cáo In ấn (Output cho BN / Lưu hồ sơ)

| Mã | Tên phiếu / biểu mẫu | Mô tả | Module | GĐ |
|----|----------------------|-------|--------|-----|
| FRM-001 | Phiếu khám bệnh (mã vạch/QR) | Phiếu tiếp đón BN, QR code link hồ sơ | HIS-REG | GĐ1 |
| FRM-002 | Đơn thuốc (mẫu BYT) | Đơn thuốc ngoại trú theo mẫu quy định | HIS-OPD | GĐ1 |
| FRM-003 | Phiếu chỉ định CLS | Phiếu chỉ định CĐHA / xét nghiệm | HIS-CLS | GĐ1 |
| FRM-004 | Biên lai thu phí | Biên lai viện phí / BHYT | HIS-BIL | GĐ1 |
| FRM-005 | Bảng kê chi phí KCB (C79a-HD) | Bảng kê chi tiết cho BN nội trú | HIS-BIL | GĐ1 |
| FRM-006 | Giấy ra viện | Tóm tắt bệnh án + hướng dẫn | HIS-IPD | GĐ1 |
| FRM-007 | Giấy chuyển viện | Giấy giới thiệu chuyển tuyến | HIS-IPD | GĐ1 |
| FRM-008 | Giấy hẹn tái khám | Phiếu hẹn ngày tái khám | HIS-OPD | GĐ1 |
| FRM-009 | Kết quả CĐHA (PDF) | Báo cáo kết quả + key images | RIS-RPT | GĐ1 |
| FRM-010 | Nhãn mẫu xét nghiệm (barcode) | Nhãn dán cho ống/mẫu xét nghiệm | HIS-CLS | GĐ2 |

---

## J.3. Danh sách Kiểm thử UAT (UAT Acceptance Test Checklist)

### J.3.1. Quy ước

- **Mã TC**: `TC-{MODULE}-{SEQ}` — tương ứng C5.4, C5.5
- **Ưu tiên**: P1 (Must pass) / P2 (Should pass) / P3 (Nice to have)
- **GĐ**: Giai đoạn áp dụng (GĐ1 / GĐ2 / GĐ3)
- **Kết quả**: ✅ PASS / ❌ FAIL / ⏭️ SKIP (ghi lý do) / 🔄 RETEST

### J.3.2. Module Tiếp đón (HIS-REG)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-REG-001 | Đăng ký BN mới thành công | Hệ thống hoạt động, user có quyền Tiếp đón | 1. Nhập họ tên, ngày sinh, giới tính, CCCD, SĐT; 2. Nhấn "Đăng ký" | BN được tạo với PID duy nhất, HL7 ADT^A04 gửi sang PACS, BN xuất hiện trên PACS | P1 | GĐ1 | FR-HIS-REG-001 |
| TC-REG-002 | Phát hiện BN trùng | Đã có BN "NGUYEN VAN A", sinh 15/01/1980 | 1. Nhập "NGUYEN VAN A", 15/01/1980, CCCD khác; 2. Nhấn "Đăng ký" | Hiển thị cảnh báo BN trùng, cho phép chọn: merge / tạo mới / hủy | P1 | GĐ1 | FR-HIS-REG-001 |
| TC-REG-003 | Đăng ký BN với dữ liệu thiếu bắt buộc | User có quyền Tiếp đón | 1. Bỏ trống họ tên; 2. Nhấn "Đăng ký" | Hiển thị validation error, không cho phép lưu | P1 | GĐ1 | FR-HIS-REG-001 |
| TC-REG-004 | Kiểm tra thẻ BHYT — thẻ hợp lệ | BN có thẻ BHYT còn hiệu lực, đúng tuyến | 1. Nhập mã thẻ BHYT; 2. Nhấn "Kiểm tra" | Hiển thị: hợp lệ, đúng tuyến, tỷ lệ chi trả 80%, 5 năm liên tục (nếu đủ) | P1 | GĐ1 | FR-HIS-REG-002 |
| TC-REG-005 | Kiểm tra thẻ BHYT — thẻ hết hạn | BN có thẻ BHYT hết hạn | 1. Nhập mã thẻ hết hạn; 2. Nhấn "Kiểm tra" | Hiển thị: thẻ hết hạn, ngày hết hạn, gợi ý chuyển viện phí | P1 | GĐ1 | FR-HIS-REG-002 |
| TC-REG-006 | Kiểm tra thẻ BHYT — cổng BHXH offline | Cổng BHXH không phản hồi (timeout) | 1. Nhập mã thẻ; 2. Nhấn "Kiểm tra" | Hiển thị cảnh báo "Không kết nối được cổng BHXH", cho phép đăng ký tạm, kiểm tra lại sau | P1 | GĐ1 | FR-HIS-REG-002 |
| TC-REG-007 | Đăng ký khám ngoại trú + phát số | BN đã đăng ký, chọn phòng Nội tổng hợp | 1. Chọn phòng khám; 2. Nhấn "Đăng ký khám" | Phát STT, BN xuất hiện trong hàng đợi phòng khám, in phiếu khám (QR code) | P1 | GĐ1 | FR-HIS-REG-003 |
| TC-REG-008 | Ưu tiên hàng đợi: cấp cứu trước thường | 2 BN đã đăng ký: 1 thường, 1 cấp cứu | Quan sát hàng đợi phòng khám | BN cấp cứu hiển thị trước BN thường, bất kể STT | P1 | GĐ1 | FR-HIS-REG-003 |
| TC-REG-009 | Đăng ký nhiều phòng khám 1 lượt | BN cần khám Nội + Mắt | 1. Đăng ký phòng Nội; 2. Thêm phòng Mắt | BN xuất hiện trong hàng đợi cả 2 phòng, 2 phiếu khám riêng | P2 | GĐ1 | FR-HIS-REG-003 |

### J.3.3. Module Khám bệnh Ngoại trú (HIS-OPD)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-OPD-001 | Khám bệnh + chẩn đoán ICD-10 | BN đã đăng ký khám, BS đã đăng nhập | 1. Gọi BN; 2. Nhập triệu chứng, sinh hiệu; 3. Chọn ICD-10 (J18.9); 4. Lưu | Phiếu khám lưu thành công, chẩn đoán hiển thị đúng | P1 | GĐ1 | FR-HIS-OPD-002 |
| TC-OPD-002 | Ghi nhận dị ứng thuốc | BN có dị ứng Penicillin đã khai báo | 1. Mở hồ sơ BN; 2. Kiểm tra tab dị ứng | Dị ứng Penicillin hiển thị rõ, có icon cảnh báo | P1 | GĐ1 | FR-HIS-OPD-002 |
| TC-OPD-003 | Kê đơn thuốc ngoại trú | BS đã chẩn đoán, BN không dị ứng | 1. Chọn thuốc từ danh mục; 2. Nhập liều, cách dùng; 3. Lưu đơn | Đơn thuốc lưu thành công, hiển thị giá ước tính | P1 | GĐ1 | FR-HIS-OPD-003 |
| TC-OPD-004 | Cảnh báo tương tác thuốc | BS kê Warfarin + Aspirin cùng lúc | 1. Kê Warfarin; 2. Kê thêm Aspirin; 3. Lưu | Hiển thị cảnh báo tương tác nghiêm trọng, yêu cầu xác nhận hoặc thay đổi | P1 | GĐ1 | FR-HIS-OPD-003 |
| TC-OPD-005 | Cảnh báo dị ứng thuốc khi kê đơn | BN dị ứng Penicillin, BS kê Amoxicillin (nhóm penicillin) | 1. Kê Amoxicillin | Hiển thị cảnh báo dị ứng chéo nhóm Penicillin, yêu cầu xác nhận | P1 | GĐ1 | FR-HIS-OPD-003 |

### J.3.4. Module Chỉ định CLS / Tích hợp HL7 (HIS-CLS + Integration)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-CLS-001 | Chỉ định CĐHA X-quang ngực | BS đang khám BN, tích hợp HL7 hoạt động | 1. Chọn dịch vụ "X-quang ngực thẳng"; 2. Nhập chỉ định lâm sàng; 3. Gửi | Chỉ định lưu, trạng thái "Đã gửi", HL7 ORM^O01 phát sinh | P1 | GĐ1 | FR-HIS-CLS-001 |
| TC-CLS-002 | Chỉ định CĐHA CT Sọ não Urgent | BS đang khám BN, tích hợp HL7 hoạt động | 1. Chọn "CT Sọ não"; 2. Priority = Urgent; 3. Gửi | ORM^O01 gửi với priority "S" (stat), PACS nhận đúng priority | P1 | GĐ1 | FR-HIS-CLS-001 |
| TC-CLS-003 | Sửa chỉ định (chưa thực hiện) | Chỉ định X-quang đã gửi, status = Scheduled | 1. Mở chỉ định; 2. Đổi sang "X-quang ngực nghiêng"; 3. Lưu | ORM^O01 (XO) gửi, PACS cập nhật worklist, trạng thái = Scheduled | P1 | GĐ1 | FR-HIS-CLS-002 |
| TC-CLS-004 | Hủy chỉ định (chưa thực hiện) | Chỉ định đã gửi, status = Scheduled | 1. Mở chỉ định; 2. Nhấn "Hủy"; 3. Nhập lý do | ORM^O01 (CA) gửi, PACS xóa khỏi worklist, HIS status = Cancelled | P1 | GĐ1 | FR-HIS-CLS-003 |
| TC-CLS-005 | Hủy chỉ định đã thực hiện (rejected) | Chỉ định đã chụp, status = In Progress | 1. Thử hủy chỉ định | Hiển thị cảnh báo "Chỉ định đã thực hiện, không thể hủy" | P1 | GĐ1 | FR-HIS-CLS-003 |
| TC-INT-001 | HL7 ORM end-to-end | HIS + PACS tích hợp, Integration Engine hoạt động | 1. Tạo chỉ định trên HIS; 2. Kiểm tra PACS worklist | Order xuất hiện trên PACS, Accession Number khớp, thông tin BN đúng | P1 | GĐ1 | FR-INT-HL7-001 |
| TC-INT-002 | HL7 ORU end-to-end | BS CĐHA đã finalize kết quả trên PACS | 1. Finalize report trên PACS; 2. Kiểm tra HIS | Kết quả CĐHA hiển thị trên HIS, status = "Đã có kết quả", nội dung đúng | P1 | GĐ1 | FR-RIS-RPT-004 |

### J.3.5. Module Dược (HIS-PHA)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-PHA-001 | Nhập kho thuốc | Dược sĩ có quyền nhập kho | 1. Tạo phiếu nhập; 2. Chọn thuốc, số lượng, lô, hạn dùng; 3. Xác nhận | Tồn kho tăng đúng, lô + hạn dùng lưu đúng | P1 | GĐ1 | FR-HIS-RPH-001 |
| TC-PHA-002 | Cảnh báo thuốc sắp hết hạn (FEFO) | Tồn kho có lô thuốc hết hạn trong 30 ngày | 1. Mở dashboard dược | Hiển thị cảnh báo thuốc sắp hết hạn, gợi ý xuất trước | P1 | GĐ1 | FR-HIS-PHA-002 |
| TC-PHA-003 | Cảnh báo tồn kho tối thiểu | Thuốc Paracetamol tồn = 10, mức tối thiểu = 50 | 1. Mở dashboard dược | Hiển thị cảnh báo tồn kho thấp cho Paracetamol | P1 | GĐ1 | FR-HIS-PHA-002 |
| TC-PHA-004 | Phát thuốc ngoại trú | Đơn thuốc đã được kê, BN đã thanh toán | 1. Quét mã đơn; 2. Xác nhận phát thuốc; 3. In nhãn | Tồn kho giảm, đơn thuốc status = "Đã phát", nhãn thuốc in đúng | P1 | GĐ1 | FR-HIS-PHA-003 |
| TC-PHA-005 | Thay thế thuốc (hết thuốc kê) | Thuốc A hết kho, có thuốc B cùng hoạt chất | 1. Scan đơn; 2. Hệ thống báo hết thuốc A; 3. Gợi ý thuốc B; 4. Dược sĩ xác nhận | Đơn cập nhật thuốc B, ghi chú thay thế, thông báo BS kê đơn | P1 | GĐ1 | FR-HIS-PHA-003 |

### J.3.6. Module Viện phí (HIS-BIL)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-BIL-001 | Tính phí tự động ngoại trú (BHYT) | BN BHYT đã khám, kê đơn, chỉ định CĐHA | 1. Mở phiếu thu; 2. Kiểm tra chi tiết chi phí | Tổng phí = khám + thuốc + CĐHA; phần BHYT trả + phần BN trả tính đúng tỷ lệ | P1 | GĐ1 | FR-HIS-BIL-003 |
| TC-BIL-002 | Tính phí tự động ngoại trú (Dịch vụ) | BN dịch vụ đã khám + CĐHA | 1. Mở phiếu thu | Tổng phí = giá dịch vụ × số lượng, không áp dụng BHYT | P1 | GĐ1 | FR-HIS-BIL-003 |
| TC-BIL-003 | Thu tiền mặt + in biên lai | BN ngoại trú đã tính phí | 1. Chọn "Tiền mặt"; 2. Nhập số tiền; 3. In biên lai | Biên lai in đúng, phiếu thu status = "Đã thanh toán" | P1 | GĐ1 | FR-HIS-BIL-004 |
| TC-BIL-004 | Thu tiền chuyển khoản | BN ngoại trú đã tính phí | 1. Chọn "Chuyển khoản"; 2. Nhập mã giao dịch; 3. Xác nhận | Phiếu thu ghi nhận phương thức "Chuyển khoản" + mã GD | P1 | GĐ1 | FR-HIS-BIL-004 |

### J.3.7. Module BHYT (HIS-INS)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-INS-001 | Kiểm tra thẻ BHYT online — thành công | Cổng BHXH hoạt động | 1. Nhập mã thẻ BHYT; 2. Gọi API giám định | Trả về: hợp lệ, tuyến, tỷ lệ, 5 năm liên tục, lưu log | P1 | GĐ1 | FR-HIS-INS-001 |
| TC-INS-002 | Kiểm tra thẻ BHYT online — trái tuyến | BN thẻ tuyến huyện đến BV tuyến tỉnh (ngoài thông tuyến) | 1. Nhập mã thẻ; 2. Gọi API | Trả về: trái tuyến, tỷ lệ chi trả giảm (40-60%), hiển thị cảnh báo | P1 | GĐ1 | FR-HIS-INS-001 |
| TC-INS-003 | Xuất XML 4210 — validate thành công | BN ngoại trú BHYT đã thanh toán xong | 1. Chọn BN; 2. Xuất XML 4210; 3. Validate | XML đúng schema, đủ trường bắt buộc, pass validation tool BHXH | P1 | GĐ1 | FR-HIS-INS-003 |
| TC-INS-004 | Gửi batch XML lên cổng BHXH | 10 hồ sơ BN BHYT trong ngày | 1. Chọn batch; 2. Gửi lên cổng | Response: 10/10 nhận thành công, lưu mã giao dịch | P1 | GĐ1 | FR-HIS-INS-003 |

### J.3.8. Module DICOM Worklist (RIS-WL)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-MWL-001 | MWL query từ modality | Chỉ định CĐHA đã nhận từ HIS, modality đã cấu hình | 1. Trên máy CT, gọi MWL query theo ngày hôm nay | Danh sách BN chờ chụp hiển thị, thông tin BN + chỉ định đúng | P1 | GĐ1 | FR-RIS-WL-001 |
| TC-MWL-002 | MWL filter theo modality | Worklist có cả X-quang và CT | 1. Máy CT query MWL filter modality = CT | Chỉ hiển thị chỉ định CT, không hiện X-quang | P1 | GĐ1 | FR-RIS-WL-001 |
| TC-MWL-003 | MWL auto-populate patient info | MWL query thành công | 1. KTV chọn BN từ worklist; 2. Bắt đầu chụp | Thông tin BN (Name, ID, DOB, Sex) tự động điền trên modality, không cần nhập tay | P1 | GĐ1 | FR-RIS-WL-001 |
| TC-WL-001 | Nhận ORM^O01 New Order | HIS gửi ORM^O01 (NW) | 1. Kiểm tra RIS worklist | Order mới xuất hiện, status = Scheduled, Accession Number tạo đúng format | P1 | GĐ1 | FR-RIS-WL-003 |
| TC-WL-002 | Nhận ORM^O01 — sửa order | HIS gửi ORM^O01 (XO) | 1. Kiểm tra RIS worklist | Order cập nhật đúng (procedure, priority), version history lưu | P1 | GĐ1 | FR-RIS-WL-003 |
| TC-WL-003 | Nhận ORM^O01 — hủy order | HIS gửi ORM^O01 (CA), order chưa thực hiện | 1. Kiểm tra RIS worklist | Order bị hủy, xóa khỏi worklist, status = Cancelled | P1 | GĐ1 | FR-RIS-WL-003 |

### J.3.9. Module DICOM Store / Archive (RIS-STD)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-STD-001 | C-STORE nhận ảnh CR/DX | Modality đã kết nối, PACS SCP hoạt động | 1. Chụp ảnh trên modality; 2. Gửi C-STORE | PACS nhận ảnh, lưu đúng Patient/Study/Series, trả C-STORE-RSP success | P1 | GĐ1 | FR-RIS-STD-001 |
| TC-STD-002 | C-STORE nhận CT multi-slice (200+ ảnh) | CT scanner đã kết nối | 1. Chụp CT; 2. Gửi toàn bộ series | Tất cả instances nhận đủ, Study metadata đúng, không mất ảnh | P1 | GĐ1 | FR-RIS-STD-001 |
| TC-STD-003 | C-STORE reject — SOP Class không hỗ trợ | Modality gửi SOP Class lạ | 1. Gửi C-STORE | PACS trả rejection, ghi log error, không crash | P2 | GĐ1 | FR-RIS-STD-001 |
| TC-STD-004 | C-FIND query study level | Viewer cần tìm study | 1. Query theo Patient ID + Date range | Trả đúng danh sách studies, metadata đầy đủ | P1 | GĐ1 | FR-RIS-STD-002 |
| TC-STD-005 | C-FIND query series level | Viewer cần tìm series trong study | 1. Query theo Study UID | Trả đúng danh sách series + modality + number of instances | P1 | GĐ1 | FR-RIS-STD-002 |
| TC-STD-006 | C-MOVE gửi study đến viewer workstation | Viewer workstation đã cấu hình AE Title | 1. Send C-MOVE request | Toàn bộ study gửi đến workstation, nhận đủ instances | P1 | GĐ1 | FR-RIS-STD-003 |
| TC-STD-007 | C-GET pull study | Viewer pull trực tiếp | 1. Send C-GET request | Study download thành công về viewer | P2 | GĐ1 | FR-RIS-STD-003 |
| TC-STD-008 | WADO-RS retrieve study | Web viewer cần load ảnh | 1. GET `/dicomweb/studies/{uid}` | Trả multipart response chứa đúng DICOM instances | P1 | GĐ1 | FR-RIS-STD-004 |
| TC-STD-009 | QIDO-RS search studies | Web viewer tìm kiếm | 1. GET `/dicomweb/studies?PatientID={pid}` | Trả JSON array đúng metadata | P1 | GĐ1 | FR-RIS-STD-004 |
| TC-STD-010 | STOW-RS upload DICOM | Upload ảnh qua REST | 1. POST `/dicomweb/studies` with DICOM part | Ảnh lưu thành công, trả instance UIDs | P2 | GĐ1 | FR-RIS-STD-004 |

### J.3.10. Module PACS Viewer (RIS-VWR)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-VWR-001 | Mở web viewer — Chrome | Study có ảnh CR trong PACS | 1. Đăng nhập viewer; 2. Mở study | Ảnh hiển thị trong ≤ 5s (NFR-PERF-002), không cần plugin | P1 | GĐ1 | FR-RIS-VWR-001 |
| TC-VWR-002 | Mở web viewer — Edge | Study có ảnh CR trong PACS | 1. Đăng nhập viewer (Edge); 2. Mở study | Ảnh hiển thị đúng, tools hoạt động, layout tương đương Chrome | P1 | GĐ1 | FR-RIS-VWR-001 |
| TC-VWR-003 | Mở study CT 200+ ảnh | Study CT có 250 instances | 1. Mở study trên viewer | Study load hoàn chỉnh ≤ 15s (NFR-PERF-003), scroll cine mượt | P1 | GĐ1 | FR-RIS-VWR-001 |
| TC-VWR-004 | Window/Level adjustment | Ảnh CT đang hiển thị | 1. Kéo chuột phải (W/L); hoặc chọn preset (Lung, Bone, Brain) | W/L thay đổi real-time, preset áp dụng đúng giá trị | P1 | GĐ1 | FR-RIS-VWR-002 |
| TC-VWR-005 | Zoom, Pan, Rotate, Flip | Ảnh đang hiển thị | 1. Zoom in/out; 2. Pan; 3. Rotate 90°; 4. Flip horizontal | Mỗi thao tác phản hồi < 0.5s, chất lượng ảnh không giảm | P1 | GĐ1 | FR-RIS-VWR-002 |
| TC-VWR-006 | Đo khoảng cách (Ruler) | Ảnh đang hiển thị | 1. Chọn tool Ruler; 2. Đo 1 đoạn trên ảnh | Kết quả đo hiển thị mm/cm, chính xác ± 1% so với DICOM pixel spacing | P1 | GĐ1 | FR-RIS-VWR-003 |
| TC-VWR-007 | Đo góc (Angle) | Ảnh đang hiển thị | 1. Chọn tool Angle; 2. Đo 1 góc | Kết quả đo hiển thị độ (°), chính xác ± 0.5° | P1 | GĐ1 | FR-RIS-VWR-003 |
| TC-VWR-008 | Hanging Protocol — CT Ngực | Study CT Ngực đang mở | 1. Mở study CT Ngực | Tự động bố trí: Axial (main), Sagittal + Coronal (secondary) theo Hanging Protocol | P2 | GĐ1 | FR-RIS-VWR-005 |
| TC-VWR-009 | MPR — Axial/Sagittal/Coronal | Study CT có đủ 3D data | 1. Bật MPR mode | Hiển thị 3 mặt cắt đồng thời, crosshair liên kết, scroll đồng bộ | P1 | GĐ1 | FR-RIS-VWR-006 |
| TC-VWR-010 | MPR — MIP rendering | Study CT angio | 1. Bật MIP mode; 2. Điều chỉnh slab thickness | MIP hiển thị đúng, rotate mượt | P2 | GĐ1 | FR-RIS-VWR-006 |
| TC-VWR-011 | Viewer tích hợp HIS — Context Launch | BS đang xem chỉ định CĐHA trên HIS, kết quả đã có | 1. Click "Xem ảnh" trên HIS | Viewer mở đúng study (đúng accession number), SSO không cần đăng nhập lại | P1 | GĐ1 | FR-RIS-VWR-008 |
| TC-VWR-012 | Viewer tích hợp HIS — Thumbnail preview | BS đang xem danh sách kết quả CĐHA | 1. Hover/click icon ảnh | Thumbnail ảnh CĐHA hiển thị inline (WADO-RS rendered) | P2 | GĐ1 | FR-RIS-VWR-008 |

### J.3.11. Module Báo cáo CĐHA (RIS-RPT)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-RPT-001 | Viết báo cáo từ template | Study đã chụp, BS CĐHA đã mở viewer | 1. Click "Viết kết quả"; 2. Chọn template "CT Sọ não"; 3. Điền kết quả | Template load đúng, các field pre-fill, editor rich-text hoạt động | P1 | GĐ1 | FR-RIS-RPT-001 |
| TC-RPT-002 | Viết báo cáo free-text | Study đã chụp | 1. Click "Viết kết quả"; 2. Chọn "Blank"; 3. Gõ tự do | Báo cáo lưu dạng free-text, không mất format | P1 | GĐ1 | FR-RIS-RPT-001 |
| TC-RPT-003 | Workflow: Draft → Preliminary → Final | Báo cáo ở trạng thái Draft | 1. BS đọc submit → Preliminary; 2. BS duyệt → Final | Trạng thái chuyển đúng, timestamp + user ID ghi nhận mỗi bước | P1 | GĐ1 | FR-RIS-RPT-002 |
| TC-RPT-004 | Không cho chỉnh sửa sau Final (phải Amend) | Báo cáo đã Final | 1. Thử chỉnh sửa nội dung | Không cho phép edit trực tiếp, chỉ cho phép "Tạo Addendum" hoặc "Amend" | P1 | GĐ1 | FR-RIS-RPT-002 |
| TC-RPT-005 | Amend — sửa kết quả đã Final | Báo cáo Final, phát hiện sai sót | 1. Click "Amend"; 2. Sửa nội dung; 3. Ghi lý do; 4. Submit | Báo cáo mới status = Amended, bản cũ lưu lại (audit trail), ORU^R01 (C) gửi HIS | P1 | GĐ1 | FR-RIS-RPT-002 |
| TC-RPT-006 | Ký số báo cáo (USB Token) | BS có USB Token CA đã cắm | 1. Finalize báo cáo; 2. Nhấn "Ký số"; 3. Nhập PIN token | Chữ ký số gắn vào PDF, verify = valid, certificate info hiển thị | P1 | GĐ1 | FR-RIS-RPT-003 |
| TC-RPT-007 | Ký số — không có Token | BS không cắm USB Token | 1. Nhấn "Ký số" | Hiển thị thông báo lỗi rõ ràng: "Không phát hiện USB Token" | P1 | GĐ1 | FR-RIS-RPT-003 |
| TC-RPT-008 | Gửi ORU^R01 khi Final | Báo cáo vừa Finalize | 1. Kiểm tra Integration Engine log | ORU^R01 message phát sinh, gửi HIS, HIS ACK thành công | P1 | GĐ1 | FR-RIS-RPT-004 |
| TC-RPT-009 | Gửi ORU^R01 khi Amend | Báo cáo vừa Amend | 1. Kiểm tra Integration Engine log | ORU^R01 (C) message phát sinh, HIS cập nhật kết quả mới | P1 | GĐ1 | FR-RIS-RPT-004 |
| TC-RPT-010 | Thống kê TAT | Có ≥ 10 study completed trong ngày | 1. Mở báo cáo TAT | TAT P50, P90, P95 hiển thị đúng, phân theo modality/BS | P2 | GĐ1 | FR-RIS-RPT-006 |

### J.3.12. Module Tích hợp & Đồng bộ (RIS-SYN)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-SYN-001 | Integration Engine — message routing | HIS gửi ORM, Integration Engine đang chạy | 1. Gửi 10 ORM messages; 2. Kiểm tra PACS | 10/10 message đến PACS, latency ≤ 1s mỗi message | P1 | GĐ1 | FR-RIS-SYN-001 |
| TC-SYN-002 | Integration Engine — retry on failure | PACS tạm thời ngưng 30s | 1. Gửi ORM trong khi PACS down; 2. PACS khôi phục | Message queue giữ message, tự retry khi PACS up, không mất message | P1 | GĐ1 | FR-RIS-SYN-001 |
| TC-SYN-003 | Đối soát HIS ↔ RIS — match 100% | 50 chỉ định trong ngày, tất cả đã nhận | 1. Chạy đối soát; 2. Xem báo cáo | Report: 50 gửi / 50 nhận / 0 chênh lệch | P1 | GĐ1 | FR-RIS-SYN-003 |
| TC-SYN-004 | Đối soát HIS ↔ RIS — phát hiện mismatch | 50 chỉ định gửi, 48 nhận (2 mất) | 1. Chạy đối soát | Report: 50 gửi / 48 nhận / 2 missing, liệt kê accession number missing | P1 | GĐ1 | FR-RIS-SYN-003 |
| TC-SYN-005 | Notification real-time — kết quả CĐHA | BS lâm sàng đang online trên HIS | 1. BS CĐHA finalize report; 2. Kiểm tra HIS BS lâm sàng | BS lâm sàng nhận notification trong ≤ 30s: "Kết quả X-quang ngực đã sẵn sàng" | P1 | GĐ1 | FR-RIS-SYN-004 |
| TC-SYN-006 | Notification — BS offline khi gửi | BS lâm sàng đã logout | 1. BS CĐHA finalize report; 2. BS lâm sàng login sau 10 phút | BS lâm sàng nhận notification khi login, không mất thông báo | P2 | GĐ1 | FR-RIS-SYN-004 |

### J.3.13. Module Quản trị RIS/PACS (RIS-ADM)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-ADM-001 | Cấu hình modality mới | Admin đăng nhập | 1. Thêm modality: AE Title, IP, Port; 2. Lưu; 3. Test connection | Modality pingable, C-ECHO thành công | P1 | GĐ1 | FR-RIS-ADM-001 |
| TC-ADM-002 | Vô hiệu hóa modality | Modality cần bảo trì | 1. Disable modality; 2. Kiểm tra MWL | Modality không nhận worklist, status = Inactive | P2 | GĐ1 | FR-RIS-ADM-001 |
| TC-ADM-003 | Auto-routing rule | Study CT từ phòng CT1 | 1. Tạo rule: CT1 → Workstation_Dr_A; 2. Gửi study CT | Study tự động gửi đến Workstation_Dr_A | P1 | GĐ1 | FR-RIS-ADM-005 |
| TC-ADM-004 | Auto-routing fallback | Workstation_Dr_A offline | 1. Gửi study CT | Study gửi đến fallback workstation hoặc queue cho retry | P2 | GĐ1 | FR-RIS-ADM-005 |
| TC-ADM-005 | Storage policy — tier migration | Study > 6 tháng trên HOT tier | 1. Chạy storage policy job | Study chuyển sang WARM tier, vẫn query/retrieve được | P2 | GĐ1 | FR-RIS-ADM-006 |
| TC-ADM-006 | Storage monitoring alert | Dung lượng HOT tier đạt 85% | 1. Kiểm tra alert | Alert email/SMS gửi đến IT Admin | P1 | GĐ1 | FR-RIS-ADM-006 |

### J.3.14. Module Thiết bị CĐHA (RIS-MOD)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | FR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|--------|
| TC-MOD-001 | Giám sát thiết bị — online | Modality CT đang hoạt động | 1. Mở dashboard modalities | CT hiển thị status = Online (C-ECHO OK), uptime, last study time | P1 | GĐ1 | FR-RIS-MOD-001 |
| TC-MOD-002 | Giám sát thiết bị — offline | Modality MRI ngắt kết nối | 1. Mở dashboard modalities | MRI hiển thị status = Offline (C-ECHO fail), alert icon | P1 | GĐ1 | FR-RIS-MOD-001 |
| TC-MOD-003 | MPPS — In Progress → Completed | Modality gửi MPPS N-CREATE (In Progress) | 1. KTV bắt đầu chụp; 2. KTV hoàn tất | PACS nhận MPPS, order status cập nhật: In Progress → Completed | P1 | GĐ1 | FR-RIS-MOD-003 |
| TC-MOD-004 | MPPS — Discontinued | KTV hủy chụp giữa chừng | 1. KTV gửi MPPS (Discontinued) | PACS cập nhật status = Discontinued, ghi nhận lý do | P2 | GĐ1 | FR-RIS-MOD-003 |

### J.3.15. Kiểm thử Phi chức năng (NFR)

| TC ID | Tên kịch bản | Điều kiện tiên quyết | Các bước thực hiện | Kết quả mong đợi | Ưu tiên | GĐ | NFR Ref |
|-------|-------------|---------------------|-------------------|------------------|---------|-----|---------|
| TC-PERF-001 | Load test HIS 200 users | Môi trường SIT/UAT, JMeter/k6 setup | 1. Simulate 200 concurrent users; 2. Mỗi user thực hiện flow: login → search BN → mở hồ sơ → kê đơn; 3. Run 10 phút | P95 response ≤ 3s, P99 ≤ 5s, zero HTTP 5xx, CPU < 80% | P1 | GĐ1 | NFR-PERF-001 |
| TC-PERF-002 | DICOM TTFI — CR/DX | Ảnh CR 10 MB trong PACS HOT tier | 1. Mở study trên web viewer; 2. Đo thời gian First Image | TTFI ≤ 5s | P1 | GĐ1 | NFR-PERF-002 |
| TC-PERF-003 | DICOM full load — CT 200 ảnh | Study CT 250 instances | 1. Mở study; 2. Đo thời gian load complete | Full load ≤ 15s, cine scroll mượt | P1 | GĐ1 | NFR-PERF-003 |
| TC-PERF-004 | HL7 message processing latency | Integration Engine + PACS hoạt động | 1. Gửi 100 ORM messages liên tiếp; 2. Đo latency mỗi message | P95 latency ≤ 1s, zero message loss | P1 | GĐ1 | NFR-PERF-004 |
| TC-PERF-005 | C-STORE throughput | Modality simulator | 1. Gửi 500 ảnh liên tiếp (burst); 2. Đo throughput | Throughput ≥ 50 ảnh/giây | P1 | GĐ1 | NFR-PERF-005 |
| TC-PERF-006 | Concurrent users stress | JMeter/k6 setup | 1. Ramp up từ 50 → 200 → 300 users; 2. Monitor metrics | 200 users: OK; 300 users: graceful degradation (không crash) | P1 | GĐ1 | NFR-PERF-006 |
| TC-PERF-007 | E2E simulation 500 ca CĐHA/ngày | Full stack hoạt động | 1. Simulate 500 ca: ORM → MWL → C-STORE → Report → ORU; 2. Run 8h | Tất cả 500 ca hoàn thành, không mất dữ liệu, TAT metrics OK | P1 | GĐ1 | NFR-PERF-007 |
| TC-SEC-001 | TLS enforcement | SSL scanner (sslyze/testssl) | 1. Scan tất cả HTTPS endpoints | TLS 1.2+ only, no SSLv3/TLS1.0/1.1, no weak ciphers | P1 | GĐ1 | NFR-SEC-001 |
| TC-SEC-002 | Data encryption at rest | DB server access | 1. Kiểm tra PII columns (patient name, CCCD); 2. Query raw data | PII encrypted (AES-256), không đọc được dạng plaintext từ raw storage | P1 | GĐ1 | NFR-SEC-002 |
| TC-SEC-003 | Authentication + account lockout | User account test | 1. Đăng nhập sai mật khẩu 5 lần | Tài khoản bị khóa 30 phút, thông báo rõ ràng | P1 | GĐ1 | NFR-SEC-003 |
| TC-SEC-004 | Session timeout | User đã login | 1. Không thao tác 30 phút; 2. Thử thao tác | Redirect về login, session expired, dữ liệu chưa lưu hiển thị cảnh báo | P1 | GĐ1 | NFR-SEC-004 |
| TC-SEC-005 | Password policy enforcement | Tạo user mới | 1. Đặt mật khẩu "123456"; 2. Đặt mật khẩu "Ab@12345" | Mật khẩu yếu bị reject, mật khẩu mạnh accepted | P1 | GĐ1 | NFR-SEC-005 |
| TC-SEC-006 | Audit log — append-only | Admin access | 1. Thực hiện 10 thao tác; 2. Kiểm tra audit log; 3. Thử xóa/sửa log | 10 entries ghi nhận đúng; không thể xóa/sửa log (even admin) | P1 | GĐ1 | NFR-SEC-008 |
| TC-AVAIL-001 | HIS failover — app server | 2 app servers (active-active) | 1. Kill 1 app server; 2. Users tiếp tục sử dụng | Users không bị gián đoạn (LB redirect), < 5s downtime | P1 | GĐ1 | NFR-AVAIL-001 |
| TC-AVAIL-002 | PACS failover — DICOM server | 2 PACS nodes | 1. Kill 1 PACS node; 2. C-STORE + C-FIND test | DICOM services tiếp tục hoạt động, < 10s failover | P1 | GĐ1 | NFR-AVAIL-002 |
| TC-AVAIL-003 | Backup & RPO verification | Backup system configured | 1. Insert data; 2. Wait 1h; 3. Restore from backup | Dữ liệu restore đúng, data loss ≤ 1h (HIS), ≤ 15m (PACS) | P1 | GĐ1 | NFR-AVAIL-003 |
| TC-AVAIL-004 | DR drill — RTO verification | DR site (warm standby) | 1. Simulate primary site failure; 2. Activate DR; 3. Đo thời gian | HIS available trong ≤ 4h, PACS trong ≤ 2h | P1 | GĐ1 | NFR-AVAIL-004 |
| TC-COMP-001 | DICOM Conformance | DICOM Validation tool (DVTk) | 1. Validate C-STORE, C-FIND, MWL, WADO-RS | DICOM conformance 100%, Conformance Statement khớp | P1 | GĐ1 | NFR-COMP-001 |
| TC-COMP-002 | HL7 Conformance | HL7 validator | 1. Validate tất cả message types (ADT, ORM, ORU, ACK) | Messages conform to HL7 v2.4 spec, encoding correct | P1 | GĐ1 | NFR-COMP-002 |
| TC-COMP-003 | IHE SWF Profile | IHE test tools (Gazelle) | 1. Run SWF integration test (Order → MWL → MPPS → Image → Report) | Pass IHE SWF profile, transactions verified | P2 | GĐ1 | NFR-COMP-003 |

---

## J.4. Thống kê UAT Test Cases

### J.4.1. Tổng hợp theo Module

| Module | Prefix | Số TC | P1 | P2 | P3 |
|--------|--------|-------|-----|-----|-----|
| Tiếp đón (HIS-REG) | TC-REG | 9 | 8 | 1 | 0 |
| Ngoại trú (HIS-OPD) | TC-OPD | 5 | 5 | 0 | 0 |
| Chỉ định CLS + HL7 | TC-CLS, TC-INT | 7 | 7 | 0 | 0 |
| Dược (HIS-PHA) | TC-PHA | 5 | 5 | 0 | 0 |
| Viện phí (HIS-BIL) | TC-BIL | 4 | 4 | 0 | 0 |
| BHYT (HIS-INS) | TC-INS | 4 | 4 | 0 | 0 |
| DICOM Worklist (RIS-WL) | TC-MWL, TC-WL | 6 | 6 | 0 | 0 |
| DICOM Store (RIS-STD) | TC-STD | 10 | 7 | 3 | 0 |
| PACS Viewer (RIS-VWR) | TC-VWR | 12 | 9 | 3 | 0 |
| Báo cáo CĐHA (RIS-RPT) | TC-RPT | 10 | 9 | 1 | 0 |
| Tích hợp (RIS-SYN) | TC-SYN | 6 | 5 | 1 | 0 |
| Quản trị RIS (RIS-ADM) | TC-ADM | 6 | 3 | 3 | 0 |
| Thiết bị (RIS-MOD) | TC-MOD | 4 | 3 | 1 | 0 |
| Phi chức năng (NFR) | TC-PERF/SEC/AVAIL/COMP | 22 | 21 | 1 | 0 |
| **Tổng** | | **110** | **96** | **14** | **0** |

### J.4.2. Tiêu chí Pass UAT

| Tiêu chí | Ngưỡng | Bắt buộc |
|---------|--------|----------|
| P1 test cases pass | **100%** (96/96) | ✅ Bắt buộc |
| P2 test cases pass | ≥ 80% (≥ 12/14) | ✅ Bắt buộc |
| Tổng pass rate | ≥ 95% (≥ 105/110) | ✅ Bắt buộc |
| Critical/Blocker defects open | **0** | ✅ Bắt buộc |
| Major defects open | ≤ 3 (có workaround) | ✅ Bắt buộc |
| Performance tests pass | 100% (7/7) | ✅ Bắt buộc |
| Security tests pass | 100% (6/6) | ✅ Bắt buộc |
| Sign-off bởi đại diện BV | Có chữ ký | ✅ Bắt buộc |

### J.4.3. Quy trình UAT

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│  Chuẩn bị    │───▶│  Thực hiện   │───▶│  Đánh giá    │───▶│  Sign-off    │
│  UAT (1 tuần)│    │  UAT (2 tuần)│    │  (3 ngày)    │    │  (1 ngày)    │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
 • Setup data        • Chạy TC          • Tổng hợp kết quả  • Họp nghiệm thu
 • Training testers  • Log defects      • Phân loại defects  • Ký biên bản
 • Review TC         • Retest fixes     • Pass/Fail quyết    • Go/No-Go
                     • Daily standup      định                 decision
```

---

## J.5. Tham chiếu chéo

| Phần | Liên kết |
|------|---------|
| Yêu cầu chức năng HIS | [C1-YEU-CAU-CHUC-NANG-HIS.md](./C1-YEU-CAU-CHUC-NANG-HIS.md) |
| Yêu cầu chức năng RIS/PACS | [C2-YEU-CAU-CHUC-NANG-RIS-PACS.md](./C2-YEU-CAU-CHUC-NANG-RIS-PACS.md) |
| Use Cases | [C3-USE-CASES.md](./C3-USE-CASES.md) |
| Luồng End-to-End | [C4-LUONG-END-TO-END.md](./C4-LUONG-END-TO-END.md) |
| Ma trận truy vết | [C5-MA-TRAN-TRUY-VET.md](./C5-MA-TRAN-TRUY-VET.md) |
| Yêu cầu tích hợp | [E-YEU-CAU-TICH-HOP.md](./E-YEU-CAU-TICH-HOP.md) |
| Yêu cầu phi chức năng | [F-YEU-CAU-PHI-CHUC-NANG.md](./F-YEU-CAU-PHI-CHUC-NANG.md) |
| Kế hoạch release | [H-KE-HOACH-RELEASE.md](./H-KE-HOACH-RELEASE.md) |
| Rủi ro | [I-RUI-RO.md](./I-RUI-RO.md) |

---

*Đây là phần cuối cùng của bộ tài liệu SRS v2.0. Quay lại [README — Mục lục tổng](./README.md).*