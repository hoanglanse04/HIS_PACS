# PHẦN D — YÊU CẦU DỮ LIỆU

> **Mã phần**: SRS-PART-D  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: DR-{STT}

---

## D.1. Chiến lược Định danh (ID Strategy)

### D.1.1. Quy tắc sinh mã định danh

| Đối tượng | Format | Ví dụ | Ghi chú |
|-----------|--------|-------|---------|
| Mã bệnh nhân (PID) | `{MÃ_CƠ_SỞ}{SEQ8}` | `CS0100000001` | Duy nhất toàn hệ thống multi-site |
| Mã lượt khám (Visit ID) | `{MÃ_CƠ_SỞ}{YYMMDD}{SEQ5}` | `CS01260325-00142` | Reset SEQ hàng ngày |
| Mã nội trú (Admission ID) | `{MÃ_CƠ_SỞ}NT{YYMMDD}{SEQ4}` | `CS01NT260325-0012` | Duy nhất theo đợt nhập viện |
| Mã chỉ định CLS (Order ID) | `{MODULE}{YYMMDD}{SEQ6}` | `CLS260325000142` | MODULE = CLS/XN/CDHA |
| Accession Number (PACS) | `{SITE}{YYMMDD}{SEQ6}` | `CS01260325000142` | Duy nhất PACS, gắn 1:1 với order CĐHA |
| Study Instance UID | `1.2.840.{ORG_ROOT}.{TIMESTAMP}.{SEQ}` | `1.2.840.113619.2.260325120000.1` | Tuân thủ DICOM PS3.5 — tối đa 64 ký tự |
| Series Instance UID | `{Study UID}.{SERIES_SEQ}` | `1.2.840.113619.2.…1.1` | Tự sinh bởi modality hoặc PACS |
| SOP Instance UID | `{Series UID}.{INSTANCE_SEQ}` | `1.2.840.113619.2.…1.1.1` | Tự sinh bởi modality |
| Mã đơn thuốc (Rx ID) | `RX{YYMMDD}{SEQ5}` | `RX260325-00321` | Bao gồm ngoại trú + nội trú |
| Mã hóa đơn (Invoice ID) | `HD{YYMMDD}{SEQ5}` | `HD260325-00089` | Hóa đơn viện phí |
| Mã nhân viên (Staff ID) | `NV{MÃ_CƠ_SỞ}{SEQ4}` | `NVCS01-0045` | Duy nhất toàn hệ thống |

### D.1.2. Yêu cầu sinh mã

| Hạng mục | Chi tiết |
|----------|---------|
| **DR-001** | Tất cả ID được sinh bởi service trung tâm (ID Generator Service) — đảm bảo không trùng khi multi-instance |
| **DR-002** | Sequence sử dụng database sequence hoặc distributed ID (Snowflake/ULID) — không dùng MAX+1 |
| **DR-003** | Study Instance UID, Series UID, SOP Instance UID tuân thủ DICOM PS3.5 §9 — tối đa 64 ký tự, chỉ chứa `0-9` và `.` |
| **DR-004** | Accession Number duy nhất trong phạm vi PACS, ≤ 16 ký tự (DICOM tag 0008,0050) |
| **DR-005** | Mã bệnh nhân (PID) được dùng làm Patient ID trong DICOM (0010,0020) và HL7 PID-3 |

---

## D.2. Đối tượng Dữ liệu Chính (Core Data Objects)

### D.2.1. Bệnh nhân (Patient)

**DR-010**: Đối tượng Bệnh nhân

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| patient_id (PID) | VARCHAR(16) | ✅ | PK, Unique toàn hệ thống | Format: `{MÃ_CƠ_SỞ}{SEQ8}` |
| full_name | NVARCHAR(200) | ✅ | Không để trống, Unicode | Họ và tên đầy đủ |
| date_of_birth | DATE | ✅ | ≤ ngày hiện tại | Bắt buộc để matching |
| gender | CHAR(1) | ✅ | M/F/O | DICOM (0010,0040) |
| national_id | VARCHAR(12) | ❌ | Unique nếu có | CMND/CCCD |
| phone | VARCHAR(15) | ❌ | Format VN | Số điện thoại liên hệ |
| address_full | NVARCHAR(500) | ❌ | | Địa chỉ đầy đủ |
| address_province_code | VARCHAR(3) | ✅ | Theo DMHC | Mã tỉnh/thành |
| address_district_code | VARCHAR(5) | ✅ | Theo DMHC | Mã quận/huyện |
| address_ward_code | VARCHAR(7) | ❌ | Theo DMHC | Mã xã/phường |
| ethnic_code | VARCHAR(3) | ❌ | Theo DM dân tộc | 54 dân tộc VN |
| occupation_code | VARCHAR(5) | ❌ | Theo DM nghề nghiệp | |
| insurance_number | VARCHAR(15) | ❌ | Format thẻ BHYT mới | Mã thẻ BHYT |
| insurance_expiry | DATE | ❌ | | Ngày hết hạn thẻ |
| photo_url | VARCHAR(500) | ❌ | | Ảnh chân dung |
| allergies | NVARCHAR(2000) | ❌ | | Danh sách dị ứng (JSON hoặc text) |
| blood_type | VARCHAR(5) | ❌ | A/B/AB/O ± Rh | Nhóm máu |
| created_at | TIMESTAMP | ✅ | Auto | Ngày tạo |
| created_by | VARCHAR(20) | ✅ | FK → Staff | Người tạo |
| updated_at | TIMESTAMP | ✅ | Auto | Cập nhật cuối |
| site_code | VARCHAR(6) | ✅ | FK → Site | Cơ sở tạo ban đầu |
| is_active | BOOLEAN | ✅ | Default TRUE | Trạng thái hoạt động |

**Quy tắc nghiệp vụ**:
- BR-030: Matching trùng bệnh nhân: `full_name + date_of_birth + gender` — nếu trùng 3/3 → cảnh báo nhập trùng
- BR-031: Nếu có `national_id` → kiểm tra unique; trùng → gợi ý merge
- BR-032: `insurance_number` validate format: `{2 ký tự}{1 số}{2 số}{10 số}` (format mới BHYT)

### D.2.2. Lượt khám / Đợt điều trị (Visit / Encounter)

**DR-011**: Đối tượng Lượt khám

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| visit_id | VARCHAR(20) | ✅ | PK | Format: `{MÃ_CƠ_SỞ}{YYMMDD}{SEQ5}` |
| patient_id | VARCHAR(16) | ✅ | FK → Patient | |
| visit_type | VARCHAR(3) | ✅ | OPD/IPD/ER | Ngoại trú/Nội trú/Cấp cứu |
| visit_date | TIMESTAMP | ✅ | | Ngày giờ đến khám |
| department_code | VARCHAR(10) | ✅ | FK → Department | Khoa/phòng khám |
| doctor_id | VARCHAR(20) | ❌ | FK → Staff | Bác sĩ khám (gán khi khám) |
| status | VARCHAR(20) | ✅ | ENUM | WAITING → IN_PROGRESS → COMPLETED → CANCELLED |
| priority | VARCHAR(10) | ✅ | Default NORMAL | EMERGENCY/URGENT/NORMAL |
| queue_number | INT | ✅ | Auto per dept/day | Số thứ tự |
| insurance_type | VARCHAR(5) | ❌ | BHYT/FEE/FREE/FOREIGN | Đối tượng thanh toán |
| insurance_rate | DECIMAL(5,2) | ❌ | 0.00–100.00 | Tỷ lệ BHYT chi trả (%) |
| referral_from | VARCHAR(20) | ❌ | | Mã cơ sở chuyển đến |
| icd_primary | VARCHAR(10) | ❌ | ICD-10 | Chẩn đoán chính (khi khám xong) |
| icd_secondary | NVARCHAR(200) | ❌ | JSON array | Chẩn đoán phụ |
| discharge_type | VARCHAR(10) | ❌ | ENUM | NORMAL/TRANSFER/ESCAPE/DEATH |
| total_amount | DECIMAL(18,2) | ❌ | ≥ 0 | Tổng chi phí |
| insurance_amount | DECIMAL(18,2) | ❌ | ≥ 0 | BHYT chi trả |
| patient_amount | DECIMAL(18,2) | ❌ | ≥ 0 | BN tự trả |
| site_code | VARCHAR(6) | ✅ | FK → Site | Cơ sở khám |
| created_at | TIMESTAMP | ✅ | Auto | |
| admission_id | VARCHAR(20) | ❌ | FK tự tham chiếu | Liên kết với đợt nội trú nếu chuyển |

### D.2.3. Chỉ định CLS / Order

**DR-012**: Đối tượng Chỉ định

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| order_id | VARCHAR(20) | ✅ | PK | Mã chỉ định HIS |
| visit_id | VARCHAR(20) | ✅ | FK → Visit | |
| patient_id | VARCHAR(16) | ✅ | FK → Patient | Denormalized cho query |
| order_type | VARCHAR(10) | ✅ | IMAGING/LAB/PROCEDURE | Loại chỉ định |
| service_code | VARCHAR(20) | ✅ | FK → Service Catalog | Mã dịch vụ |
| service_name | NVARCHAR(200) | ✅ | | Tên dịch vụ (snapshot) |
| quantity | INT | ✅ | ≥ 1, default 1 | Số lượng |
| priority | VARCHAR(10) | ✅ | ROUTINE/URGENT/STAT | |
| clinical_indication | NVARCHAR(1000) | ❌ | Khuyến cáo có | Lý do chỉ định (gửi PACS) |
| ordering_doctor_id | VARCHAR(20) | ✅ | FK → Staff | Bác sĩ chỉ định |
| ordering_department | VARCHAR(10) | ✅ | FK → Department | Khoa chỉ định |
| ordered_at | TIMESTAMP | ✅ | Auto | Thời gian chỉ định |
| status | VARCHAR(20) | ✅ | ENUM | NEW → SENT → SCHEDULED → IN_PROGRESS → COMPLETED → REPORTED → CANCELLED |
| accession_number | VARCHAR(16) | ❌ | Unique PACS | Sinh bởi PACS khi nhận order |
| hl7_message_id | VARCHAR(50) | ❌ | | MSH-10 của ORM^O01 gửi đi |
| result_status | VARCHAR(20) | ❌ | | PRELIMINARY/FINAL/AMENDED |
| result_text | NTEXT | ❌ | | Nội dung kết quả từ PACS (ORU) |
| result_received_at | TIMESTAMP | ❌ | | Thời gian nhận kết quả |
| wado_url | VARCHAR(500) | ❌ | | URL xem ảnh DICOM |
| unit_price | DECIMAL(18,2) | ✅ | ≥ 0 | Đơn giá dịch vụ |
| insurance_covered | BOOLEAN | ✅ | | BHYT có chi trả |
| site_code | VARCHAR(6) | ✅ | FK → Site | |

### D.2.4. Study CĐHA (DICOM Study)

**DR-013**: Đối tượng Study DICOM

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| study_instance_uid | VARCHAR(64) | ✅ | PK, DICOM (0020,000D) | Unique toàn cầu |
| accession_number | VARCHAR(16) | ✅ | Unique PACS, DICOM (0008,0050) | Liên kết với HIS order |
| patient_id | VARCHAR(16) | ✅ | DICOM (0010,0020) | = PID trong HIS |
| patient_name | NVARCHAR(200) | ✅ | DICOM (0010,0010) | Format: LAST^FIRST^MIDDLE |
| patient_dob | DATE | ❌ | DICOM (0010,0030) | YYYYMMDD |
| patient_sex | CHAR(1) | ❌ | DICOM (0010,0040) | M/F/O |
| study_date | DATE | ✅ | DICOM (0008,0020) | Ngày thực hiện |
| study_time | TIME | ✅ | DICOM (0008,0030) | Giờ thực hiện |
| study_description | NVARCHAR(500) | ❌ | DICOM (0008,1030) | Mô tả study |
| modality_type | VARCHAR(10) | ✅ | DICOM (0008,0060) | CT/MR/CR/DX/US/MG/NM/PT/ES |
| referring_physician | NVARCHAR(200) | ❌ | DICOM (0008,0090) | BS chỉ định |
| performing_physician | NVARCHAR(200) | ❌ | DICOM (0008,1050) | BS thực hiện |
| institution_name | NVARCHAR(200) | ✅ | DICOM (0008,0080) | Tên bệnh viện |
| station_name | VARCHAR(16) | ❌ | DICOM (0008,1010) | Tên máy chụp |
| body_part | VARCHAR(20) | ❌ | DICOM (0018,0015) | CHEST/HEAD/ABDOMEN/… |
| number_of_series | INT | ❌ | DICOM (0020,1206) | Số series |
| number_of_instances | INT | ❌ | DICOM (0020,1208) | Tổng số ảnh |
| study_status | VARCHAR(20) | ✅ | ENUM | SCHEDULED → ARRIVED → IN_PROGRESS → COMPLETED → READ → REPORTED |
| report_status | VARCHAR(20) | ❌ | ENUM | DRAFT → PRELIMINARY → FINAL → AMENDED → ADDENDUM |
| storage_size_mb | DECIMAL(12,2) | ❌ | | Dung lượng lưu trữ |
| storage_tier | VARCHAR(10) | ✅ | Default HOT | HOT/WARM/COLD/ARCHIVE |
| site_code | VARCHAR(6) | ✅ | FK → Site | Cơ sở thực hiện |

### D.2.5. Báo cáo CĐHA (Radiology Report)

**DR-014**: Đối tượng Báo cáo CĐHA

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| report_id | VARCHAR(20) | ✅ | PK | Mã báo cáo nội bộ |
| study_instance_uid | VARCHAR(64) | ✅ | FK → Study | |
| accession_number | VARCHAR(16) | ✅ | FK → Study | Denormalized |
| report_status | VARCHAR(20) | ✅ | ENUM | DRAFT → PRELIMINARY → FINAL → AMENDED → ADDENDUM |
| findings | NTEXT | ❌ | | Mô tả hình ảnh |
| conclusion | NTEXT | ❌ | | Kết luận |
| recommendation | NTEXT | ❌ | | Đề nghị |
| icd_codes | VARCHAR(100) | ❌ | JSON array | Mã ICD-10 chẩn đoán CĐHA |
| template_code | VARCHAR(20) | ❌ | FK → Report Template | Mẫu báo cáo sử dụng |
| reading_physician_id | VARCHAR(20) | ✅ | FK → Staff | BS đọc kết quả |
| reading_started_at | TIMESTAMP | ❌ | | Bắt đầu đọc |
| approving_physician_id | VARCHAR(20) | ❌ | FK → Staff | BS duyệt |
| approved_at | TIMESTAMP | ❌ | | Thời gian duyệt |
| digital_signature | TEXT | ❌ | | Chữ ký số (hash + certificate) |
| key_image_uids | TEXT | ❌ | JSON array | SOP Instance UIDs của ảnh đại diện |
| hl7_oru_message_id | VARCHAR(50) | ❌ | | MSH-10 của ORU^R01 gửi HIS |
| hl7_sent_at | TIMESTAMP | ❌ | | Thời gian gửi ORU |
| tat_minutes | INT | ❌ | Computed | Turnaround time (study completed → report final) |
| created_at | TIMESTAMP | ✅ | Auto | |
| updated_at | TIMESTAMP | ✅ | Auto | |

### D.2.6. Đơn thuốc (Prescription)

**DR-015**: Đối tượng Đơn thuốc

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| prescription_id | VARCHAR(20) | ✅ | PK | Format: RX{YYMMDD}{SEQ5} |
| visit_id | VARCHAR(20) | ✅ | FK → Visit | |
| patient_id | VARCHAR(16) | ✅ | FK → Patient | |
| prescribing_doctor_id | VARCHAR(20) | ✅ | FK → Staff | |
| prescription_type | VARCHAR(5) | ✅ | OPD/IPD | Ngoại trú / Nội trú |
| prescribed_at | TIMESTAMP | ✅ | Auto | |
| status | VARCHAR(20) | ✅ | ENUM | DRAFT → CONFIRMED → DISPENSING → DISPENSED → CANCELLED |
| total_amount | DECIMAL(18,2) | ❌ | ≥ 0 | |
| notes | NVARCHAR(1000) | ❌ | | Ghi chú bác sĩ |

**DR-016**: Đối tượng Chi tiết đơn thuốc (Prescription Item)

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| item_id | BIGINT | ✅ | PK, Identity | |
| prescription_id | VARCHAR(20) | ✅ | FK → Prescription | |
| drug_code | VARCHAR(20) | ✅ | FK → Drug Catalog | Mã thuốc |
| drug_name | NVARCHAR(200) | ✅ | | Tên thuốc (snapshot) |
| dosage | NVARCHAR(100) | ✅ | | Liều dùng |
| frequency | NVARCHAR(100) | ✅ | | Cách dùng (sáng/trưa/chiều/tối) |
| duration_days | INT | ✅ | ≥ 1 | Số ngày dùng |
| quantity | DECIMAL(10,2) | ✅ | > 0 | Số lượng kê |
| unit | NVARCHAR(20) | ✅ | | Đơn vị (viên/ống/chai/…) |
| route | VARCHAR(10) | ✅ | ORAL/IV/IM/SC/TOPICAL/… | Đường dùng |
| is_insurance | BOOLEAN | ✅ | | Thuốc BHYT |
| unit_price | DECIMAL(18,2) | ✅ | ≥ 0 | Đơn giá |
| dispensed_quantity | DECIMAL(10,2) | ❌ | | Số lượng thực phát |
| substituted_drug_code | VARCHAR(20) | ❌ | | Thuốc thay thế nếu hết |
| interaction_warning | NVARCHAR(500) | ❌ | | Cảnh báo tương tác (nếu có) |

### D.2.7. Hóa đơn / Viện phí (Invoice)

**DR-017**: Đối tượng Hóa đơn

| Thuộc tính | Kiểu | Bắt buộc | Ràng buộc | Ghi chú |
|------------|------|----------|-----------|---------|
| invoice_id | VARCHAR(20) | ✅ | PK | Format: HD{YYMMDD}{SEQ5} |
| visit_id | VARCHAR(20) | ✅ | FK → Visit | |
| patient_id | VARCHAR(16) | ✅ | FK → Patient | |
| invoice_type | VARCHAR(10) | ✅ | OPD/IPD/ER | |
| total_amount | DECIMAL(18,2) | ✅ | ≥ 0 | Tổng chi phí |
| insurance_amount | DECIMAL(18,2) | ✅ | ≥ 0 | BHYT chi trả |
| patient_amount | DECIMAL(18,2) | ✅ | ≥ 0 | BN tự trả |
| discount_amount | DECIMAL(18,2) | ❌ | ≥ 0 | Miễn giảm |
| deposit_amount | DECIMAL(18,2) | ❌ | ≥ 0 | Tạm ứng (nội trú) |
| payment_status | VARCHAR(20) | ✅ | ENUM | PENDING → PARTIAL → PAID → REFUNDED |
| payment_method | VARCHAR(20) | ❌ | CASH/CARD/TRANSFER/EWALLET | |
| paid_at | TIMESTAMP | ❌ | | Thời gian thanh toán |
| cashier_id | VARCHAR(20) | ❌ | FK → Staff | Thu ngân |
| e_invoice_number | VARCHAR(50) | ❌ | | Số hóa đơn điện tử |
| bhxh_xml_sent | BOOLEAN | ✅ | Default FALSE | Đã gửi XML 4210 |
| site_code | VARCHAR(6) | ✅ | FK → Site | |
| created_at | TIMESTAMP | ✅ | Auto | |

---

## D.3. Mô hình Khái niệm (Conceptual Data Model)

### D.3.1. Sơ đồ quan hệ chính

```
┌──────────┐    1:N     ┌───────────┐    1:N     ┌───────────┐
│ PATIENT  │───────────▶│   VISIT   │───────────▶│   ORDER   │
│ (BN)     │            │ (Lượt khám)│           │ (Chỉ định) │
└──────────┘            └─────┬─────┘            └─────┬─────┘
                              │ 1:N                    │ 1:1
                              ▼                        ▼ (CĐHA only)
                        ┌───────────┐           ┌───────────┐
                        │PRESCRIPTION│          │   STUDY   │
                        │ (Đơn thuốc)│          │ (DICOM)   │
                        └─────┬─────┘           └─────┬─────┘
                              │ 1:N                    │ 1:1
                              ▼                        ▼
                        ┌───────────┐           ┌───────────┐
                        │  Rx ITEM  │           │  REPORT   │
                        │(Chi tiết) │           │(Kết quả)  │
                        └───────────┘           └─────┬─────┘
                                                      │ 1:N
                        ┌───────────┐                 ▼
                        │  INVOICE  │◀──────── ┌───────────┐
                        │ (Hóa đơn) │         │ KEY IMAGE │
                        └───────────┘         └───────────┘
                              ▲
                              │ N:1
                        ┌───────────┐
                        │INV. DETAIL│
                        │(Chi tiết) │
                        └───────────┘
```

### D.3.2. Quan hệ giữa HIS và PACS

```
HIS Domain                          PACS Domain
─────────────                       ────────────
Patient (PID) ◄─────────────────► DICOM Patient (0010,0020)
  │                                     │
  └─ Visit ◄─ Order ──────────────► Study (Accession Number)
                │                       │
                │ HL7 ORM^O01 ──────►   │ MWL Query
                │                       │
                │ HL7 ORU^R01 ◄──────   │
                │                       ├─ Series
                └───── WADO URL ◄────── └─ Instance (Image)
```

**Điểm liên kết chính (Join Keys)**:

| HIS Field | PACS/DICOM Field | Mục đích |
|-----------|-----------------|----------|
| `patient_id` | DICOM (0010,0020) Patient ID | Định danh bệnh nhân |
| `order.accession_number` | DICOM (0008,0050) Accession Number | Liên kết chỉ định ↔ study |
| `visit_id` | HL7 PV1-19 Visit Number | Liên kết lượt khám |
| `order_id` | HL7 ORC-2 Placer Order Number | Liên kết chỉ định gốc |

---

## D.4. Danh mục Dữ liệu Tham chiếu (Master Data / Reference Data)

### D.4.1. Danh sách danh mục

**DR-020**: Yêu cầu danh mục dùng chung

| STT | Danh mục | Nguồn | Cập nhật | Phạm vi | Mã yêu cầu |
|-----|----------|-------|----------|---------|-------------|
| 1 | ICD-10 (bệnh) | WHO + BYT VN | Hàng năm (BYT công bố) | Toàn hệ thống | DR-020a |
| 2 | Danh mục thuốc BHYT | BHXH | Khi có thông tư mới | HIS Dược, Viện phí | DR-020b |
| 3 | Danh mục dịch vụ kỹ thuật | BYT/BHXH | Khi có thông tư mới | HIS + RIS | DR-020c |
| 4 | Danh mục dịch vụ CĐHA | BYT + nội bộ BV | Khi thay đổi | HIS + PACS | DR-020d |
| 5 | Bảng giá dịch vụ | BV ban hành | Khi thay đổi giá | HIS Viện phí | DR-020e |
| 6 | Danh mục hành chính VN | Bộ Nội vụ | Khi thay đổi ĐGHC | Toàn hệ thống | DR-020f |
| 7 | Danh mục khoa/phòng | Nội bộ BV | Khi thay đổi tổ chức | Toàn hệ thống | DR-020g |
| 8 | Danh mục nhân viên | Nội bộ BV/HR | Khi có nhân sự mới | Toàn hệ thống | DR-020h |
| 9 | Danh mục dân tộc | BYT | Hiếm thay đổi | HIS Tiếp đón | DR-020i |
| 10 | Danh mục nghề nghiệp | BHXH | Hiếm thay đổi | HIS Tiếp đón | DR-020j |
| 11 | Danh mục cơ sở KCB | BHXH | Hàng tháng | HIS BHYT | DR-020k |
| 12 | DICOM Body Part Examined | DICOM PS3.16 | Chuẩn quốc tế | PACS | DR-020l |
| 13 | DICOM Modality codes | DICOM PS3.3 C.7.3.1.1.1 | Chuẩn quốc tế | PACS | DR-020m |

### D.4.2. Yêu cầu quản lý danh mục

| Mã | Yêu cầu | Tiêu chí chấp nhận |
|----|---------|---------------------|
| DR-021 | Mỗi danh mục có giao diện CRUD + tìm kiếm + import Excel | Import 10.000 dòng trong ≤ 30 giây |
| DR-022 | Hỗ trợ versioning: khi thay đổi giá/danh mục → lưu phiên bản cũ, có effective_date | Tra cứu giá tại thời điểm T bất kỳ trả đúng giá |
| DR-023 | Danh mục dùng chung giữa HIS-PACS được quản lý tại 1 nơi (master), đồng bộ sang hệ thống còn lại | Thay đổi danh mục dịch vụ CĐHA trên HIS → PACS nhận trong ≤ 5 phút |
| DR-024 | Audit log cho mọi thay đổi danh mục: ai, lúc nào, giá trị cũ, giá trị mới | Log đầy đủ cho 100% thay đổi |
| DR-025 | Danh mục có trạng thái active/inactive — inactive không xóa khỏi DB, chỉ ẩn khỏi dropdown | Bản ghi inactive không hiển thị khi chọn, nhưng hiển thị trong lịch sử |

---

## D.5. Chính sách Đồng bộ Dữ liệu Multi-Site

### D.5.1. Mô hình đồng bộ

**DR-030**: Kiến trúc dữ liệu multi-site

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô hình** | Hub-and-Spoke: 1 Database trung tâm (Hub) + 5 cơ sở (Spoke) |
| **Master Data** | Quản lý tập trung tại Hub — đồng bộ xuống Spoke |
| **Transaction Data** | Mỗi cơ sở ghi trực tiếp vào DB trung tâm (online mode) |
| **Offline mode** | [GĐ3] Hỗ trợ ghi cục bộ + sync khi có kết nối — chỉ áp dụng cho cơ sở vệ tinh |
| **PACS Storage** | Mỗi cơ sở có PACS node cục bộ, sync lên PACS trung tâm (async replication) |
| **Conflict resolution** | Last-write-wins cho master data; Transaction data không conflict (mỗi site có ID prefix riêng) |

### D.5.2. Yêu cầu đồng bộ cụ thể

| Mã | Đối tượng | Hướng đồng bộ | Tần suất | SLA | Ghi chú |
|----|-----------|---------------|----------|-----|---------|
| DR-031 | Patient Master | Hub → Spoke (bidirectional) | Real-time | ≤ 3 giây | Tạo BN tại Spoke → sync ngay lên Hub |
| DR-032 | Danh mục thuốc | Hub → Spoke (1 chiều) | Khi thay đổi | ≤ 5 phút | Hub là master |
| DR-033 | Danh mục dịch vụ | Hub → Spoke (1 chiều) | Khi thay đổi | ≤ 5 phút | |
| DR-034 | Bảng giá | Hub → Spoke (1 chiều) | Khi thay đổi | ≤ 5 phút | Có effective_date |
| DR-035 | DICOM Studies | Spoke PACS → Hub PACS | Async | ≤ 30 phút | Chỉ metadata + compressed images |
| DR-036 | Báo cáo CĐHA | Với Study (DR-035) | Async | ≤ 30 phút | |
| DR-037 | Kết quả xét nghiệm | LIS → HIS Hub | Real-time | ≤ 10 giây | Nếu tích hợp LIS |
| DR-038 | Dữ liệu BHXH XML | Hub → Cổng BHXH | Batch (cuối ngày hoặc real-time) | Theo quy định | |

---

## D.6. Quy tắc Chất lượng Dữ liệu (Data Quality Rules)

### D.6.1. Quy tắc bắt buộc

| Mã | Quy tắc | Đối tượng | Hành động khi vi phạm | Mức độ |
|----|---------|-----------|----------------------|--------|
| DQ-001 | Họ tên bệnh nhân không chứa ký tự đặc biệt (chỉ Unicode letter + space + dấu) | Patient.full_name | Reject + thông báo lỗi | Error |
| DQ-002 | Ngày sinh ≤ ngày hiện tại và ≥ 01/01/1900 | Patient.date_of_birth | Reject | Error |
| DQ-003 | Mã ICD-10 phải tồn tại trong danh mục ICD-10 active | Visit.icd_primary | Reject | Error |
| DQ-004 | Accession Number unique trong PACS | Study.accession_number | Reject | Error |
| DQ-005 | Study Instance UID unique toàn cầu, format hợp lệ (chỉ chứa `0-9` và `.`) | Study.study_instance_uid | Reject | Error |
| DQ-006 | Tổng chi phí = Σ(chi tiết) — sai lệch ≤ 1 VNĐ (do làm tròn) | Invoice.total_amount | Cảnh báo + không cho thanh toán | Error |
| DQ-007 | Thuốc trong đơn phải active trong danh mục thuốc tại thời điểm kê | Rx Item.drug_code | Reject | Error |
| DQ-008 | Số thẻ BHYT 15 ký tự, format hợp lệ | Patient.insurance_number | Warning, cho phép lưu | Warning |
| DQ-009 | Mã tỉnh/huyện phải tồn tại trong DM hành chính | Patient.address_province_code | Warning | Warning |
| DQ-010 | DICOM Patient ID trong ảnh nhận (C-STORE) phải match với Patient ID trong MWL đã gửi | DICOM (0010,0020) | Warning + ghi log reconciliation | Warning |

### D.6.2. Quy tắc kiểm tra định kỳ (Batch Validation)

| Mã | Quy tắc | Tần suất | Output |
|----|---------|----------|--------|
| DQ-020 | Đối soát số lượng chỉ định HIS vs Study PACS — phát hiện chênh lệch | Hàng ngày 23:00 | Báo cáo chênh lệch gửi email admin |
| DQ-021 | Phát hiện Study PACS không có order HIS tương ứng (unmatched study) | Hàng ngày 23:00 | Danh sách study orphan |
| DQ-022 | Phát hiện order HIS có status SENT > 24h mà không có Study PACS | Hàng ngày 23:00 | Danh sách order treo |
| DQ-023 | Kiểm tra tính toàn vẹn DICOM: checksum ảnh lưu trữ vs checksum gốc | Hàng tuần (Chủ nhật 02:00) | Báo cáo integrity |
| DQ-024 | Đối soát BHYT: so sánh dữ liệu gửi BHXH vs dữ liệu duyệt | Hàng tháng | Báo cáo chênh lệch BHXH |

---

## D.7. Quy tắc Lưu trữ và Retention

### D.7.1. Chính sách lưu trữ

| Mã | Loại dữ liệu | Retention tối thiểu | Tier lưu trữ | Cơ sở pháp lý |
|----|--------------|---------------------|---------------|---------------|
| DR-040 | Bệnh án điện tử (EMR) | 10 năm kể từ lần khám cuối | Database (active) → Archive DB | Luật Khám chữa bệnh 2023 |
| DR-041 | Ảnh DICOM | 10 năm | HOT (1 năm) → WARM (3 năm) → COLD (6 năm) | Thông tư BYT + DICOM practice |
| DR-042 | Báo cáo CĐHA | 10 năm | Database | Cùng với EMR |
| DR-043 | Audit log | 5 năm | Append-only storage | Best practice |
| DR-044 | Hóa đơn / chứng từ | 10 năm | Database → Archive | Luật kế toán |
| DR-045 | Dữ liệu BHXH XML | 5 năm | File storage | Quy định BHXH |
| DR-046 | HL7 message log | 1 năm (active) + 4 năm (archive) | File/DB → Archive | Troubleshooting + audit |
| DR-047 | Backup database | 30 ngày (daily) + 12 tháng (monthly) + 3 năm (yearly) | Backup storage | DR policy |

### D.7.2. Tiered Storage cho DICOM

```
Tiered Storage Policy — PACS DICOM Images

Tier 1: HOT (SSD/NVMe)
├── Thời gian: 0 – 12 tháng kể từ study_date
├── Access: Online, ≤ 5 giây load study
├── Dung lượng ước tính: ~12 TB/năm (200 ca/ngày × 250 MB/ca × 250 ngày)
└── Compression: None hoặc Lossless

Tier 2: WARM (HDD RAID)
├── Thời gian: 13 – 48 tháng
├── Access: Online, ≤ 15 giây load study
├── Compression: JPEG 2000 Lossless
└── Migration: Tự động hàng đêm từ HOT → WARM

Tier 3: COLD (NAS/Object Storage)
├── Thời gian: 49 – 120 tháng
├── Access: Nearline, ≤ 60 giây (on-demand retrieve)
├── Compression: JPEG 2000 Lossless
└── Migration: Tự động hàng tuần từ WARM → COLD

Tier 4: ARCHIVE (Tape/Cloud)
├── Thời gian: > 120 tháng (nếu cần giữ)
├── Access: Offline, yêu cầu restore (≤ 4 giờ)
└── Compression: JPEG 2000 Lossless
```

### D.7.3. Ước lượng dung lượng

| Modality | Ca/ngày (ước lượng) | TB/ca (trung bình) | TB/năm (250 ngày) |
|----------|---------------------|-------------------|-------------------|
| X-quang (CR/DX) | 80 | 30 MB | 0.6 TB |
| CT | 40 | 500 MB | 5.0 TB |
| MRI | 20 | 800 MB | 4.0 TB |
| Siêu âm (US) | 40 | 50 MB | 0.5 TB |
| Nội soi (ES) | 15 | 200 MB | 0.75 TB |
| Mammography (MG) | 5 | 400 MB | 0.5 TB |
| **TỔNG** | **200** | | **~11.35 TB/năm** |

[GIẢ ĐỊNH] Dung lượng có thể biến động ±30% tùy protocol chụp và số series/study.

**DR-048**: Hệ thống phải cảnh báo khi dung lượng HOT tier còn < 20% — gửi email admin + hiển thị trên dashboard.

**DR-049**: Auto-migration giữa các tier không ảnh hưởng đến tính khả dụng của study — nếu study ở WARM/COLD, viewer tự động trigger retrieve lên HOT trước khi hiển thị.

---

## D.8. Sao lưu và Phục hồi Dữ liệu (Backup & Recovery)

### D.8.1. Chiến lược sao lưu

| Mã | Đối tượng | Phương pháp | Tần suất | Retention | RTO | RPO |
|----|-----------|------------|----------|-----------|-----|-----|
| DR-050 | HIS Database | Full + Incremental | Full: hàng tuần (CN 01:00); Incremental: hàng ngày 23:00 | 30 ngày daily, 12 tháng monthly | ≤ 4h | ≤ 1h |
| DR-051 | PACS Database | Full + WAL/Redo Log | Full: hàng tuần; WAL: continuous | 30 ngày, 6 tháng monthly | ≤ 2h | ≤ 15 phút |
| DR-052 | DICOM Files (HOT) | Replication (sync) | Real-time → DR site | Bản copy liên tục | ≤ 1h | ≤ 5 phút |
| DR-053 | DICOM Files (WARM/COLD) | Async replication | Hàng đêm | Bản copy liên tục | ≤ 4h | ≤ 24h |
| DR-054 | Application config | Git repository | Mỗi thay đổi | Vĩnh viễn | ≤ 30 phút | 0 (versioned) |
| DR-055 | Audit log | Append-only + replicate | Real-time | 5 năm | ≤ 4h | ≤ 1h |

### D.8.2. Tiêu chí chấp nhận Backup & Recovery

| Mã | Tiêu chí | Phương pháp kiểm tra |
|----|---------|---------------------|
| DR-056 | Restore HIS DB từ backup thành công trong ≤ 4 giờ | DR drill hàng quý |
| DR-057 | Restore PACS DB + DICOM files thành công, verify integrity ≥ 99.99% | DR drill hàng quý |
| DR-058 | Zero data loss cho DICOM images (RPO = 0 cho DICOM ở HOT tier, sử dụng sync replication) | Verify checksum sau restore |
| DR-059 | Backup job thất bại → alert email + SMS trong ≤ 5 phút | Monitoring test |

---

## D.9. Bảo mật Dữ liệu

### D.9.1. Phân loại dữ liệu

| Mã | Phân loại | Ví dụ | Mã hóa at-rest | Mã hóa in-transit | Access Control |
|----|-----------|-------|----------------|-------------------|----------------|
| DR-060 | **Bí mật (Confidential)** | Thông tin BN, bệnh án, ảnh DICOM | AES-256 ✅ | TLS 1.2+ ✅ | RBAC + audit log |
| DR-061 | **Nội bộ (Internal)** | Danh mục thuốc, bảng giá, nhân sự | Optional | TLS 1.2+ ✅ | RBAC |
| DR-062 | **Công khai (Public)** | Danh mục ICD-10, mã hành chính | Không cần | Không cần | Read-only |

### D.9.2. Yêu cầu bảo mật dữ liệu

| Mã | Yêu cầu | Tiêu chí chấp nhận |
|----|---------|---------------------|
| DR-063 | Mọi truy cập bệnh án / thông tin BN ghi audit log: user, timestamp, action, patient_id | 100% truy cập có log |
| DR-064 | Xuất dữ liệu BN (export/print) yêu cầu quyền đặc biệt + ghi log | Không export được nếu thiếu quyền |
| DR-065 | DICOM de-identification cho nghiên cứu: xóa/thay thế 18+ HIPAA identifiers | Verify bằng DICOM viewer sau de-id |
| DR-066 | Database connection sử dụng connection pool + mã hóa credential (không plaintext trong config) | Config audit: 0 credential dạng plaintext |
| DR-067 | Soft-delete cho Patient: đánh dấu is_active=false, không xóa vật lý — chỉ DBA mới xóa vật lý | DELETE Patient → trả lỗi 403 |

---

*Tiếp theo: [Phần E — Yêu cầu Tích hợp](./E-YEU-CAU-TICH-HOP.md)*
