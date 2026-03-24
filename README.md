# ĐẶC TẢ YÊU CẦU PHẦN MỀM (SRS) — HỆ THỐNG HIS-PACS TÍCH HỢP

**Tuân thủ chuẩn IEEE 830-1998 (IEEE Recommended Practice for Software Requirements Specifications)**

**Phiên bản**: 1.0 — Draft  
**Ngày tạo**: 24/03/2026  
**Tác giả**: [Tên đơn vị / Tên cá nhân]  
**Trạng thái**: Dàn ý chi tiết (Detailed Outline)

---

## LỊCH SỬ THAY ĐỔI TÀI LIỆU

| Phiên bản | Ngày | Người thực hiện | Mô tả thay đổi |
|-----------|------|-----------------|-----------------|
| 1.0 | 24/03/2026 | [Tên] | Tạo dàn ý chi tiết ban đầu |

---

## MỤC LỤC

- [1. GIỚI THIỆU (Introduction)](#1-giới-thiệu-introduction)
  - [1.1. Mục đích (Purpose)](#11-mục-đích-purpose)
  - [1.2. Phạm vi sản phẩm (Scope)](#12-phạm-vi-sản-phẩm-scope)
  - [1.3. Định nghĩa, từ viết tắt và thuật ngữ](#13-định-nghĩa-từ-viết-tắt-và-thuật-ngữ)
  - [1.4. Tài liệu tham chiếu (References)](#14-tài-liệu-tham-chiếu-references)
  - [1.5. Tổng quan tài liệu (Overview)](#15-tổng-quan-tài-liệu-overview)
- [2. MÔ TẢ TỔNG QUAN (Overall Description)](#2-mô-tả-tổng-quan-overall-description)
  - [2.1. Bối cảnh sản phẩm (Product Perspective)](#21-bối-cảnh-sản-phẩm-product-perspective)
  - [2.2. Chức năng sản phẩm (Product Functions)](#22-chức-năng-sản-phẩm-product-functions)
  - [2.3. Đặc điểm người dùng (User Characteristics)](#23-đặc-điểm-người-dùng-user-characteristics)
  - [2.4. Ràng buộc (Constraints)](#24-ràng-buộc-constraints)
  - [2.5. Giả định và phụ thuộc (Assumptions and Dependencies)](#25-giả-định-và-phụ-thuộc-assumptions-and-dependencies)
- [3. YÊU CẦU CỤ THỂ (Specific Requirements)](#3-yêu-cầu-cụ-thể-specific-requirements)
  - [3.1. Yêu cầu chức năng (Functional Requirements)](#31-yêu-cầu-chức-năng-functional-requirements)
  - [3.2. Yêu cầu phi chức năng (Non-Functional Requirements)](#32-yêu-cầu-phi-chức-năng-non-functional-requirements)
  - [3.3. Yêu cầu giao diện bên ngoài (External Interface Requirements)](#33-yêu-cầu-giao-diện-bên-ngoài-external-interface-requirements)
- [4. PHỤ LỤC (Appendices)](#4-phụ-lục-appendices)
- [5. MỤC LỤC THAM CHIẾU (Index)](#5-mục-lục-tham-chiếu-index)

---

## 1. GIỚI THIỆU (Introduction)

### 1.1. Mục đích (Purpose)

- Mô tả mục đích của tài liệu SRS
- Đối tượng đọc: ban giám đốc bệnh viện, đội ngũ phát triển, QA, đội triển khai, đơn vị bảo hiểm y tế liên kết
- Phạm vi áp dụng: hệ thống HIS-PACS tích hợp cho cơ sở y tế

### 1.2. Phạm vi sản phẩm (Scope)

- **Tên hệ thống**: HIS-PACS Integrated Platform
- **Mô tả tổng quan**:
  - HIS: Quản lý toàn bộ quy trình khám chữa bệnh — từ tiếp đón, khám ngoại trú/nội trú, kê đơn dược, viện phí đến thanh toán BHYT
  - PACS: Tiếp nhận chỉ định CĐHA từ HIS, lưu trữ ảnh DICOM, xem/đọc ảnh (viewer), trả kết quả về HIS
  - Tích hợp: Liên thông HIS↔PACS qua chuẩn HL7 và DICOM
- **Lợi ích**: Số hóa quy trình, giảm thời gian chờ, tăng chính xác chẩn đoán, tuân thủ quy định BHYT Việt Nam
- **Ngoài phạm vi (Out of scope)**: LIS (xét nghiệm), telemedicine, mobile app (nếu có, ghi rõ)

### 1.3. Định nghĩa, từ viết tắt và thuật ngữ (Definitions, Acronyms, Abbreviations)

| Thuật ngữ | Định nghĩa |
|-----------|------------|
| HIS | Hospital Information System — Hệ thống thông tin bệnh viện |
| PACS | Picture Archiving and Communication System — Hệ thống lưu trữ và truyền tải hình ảnh |
| DICOM | Digital Imaging and Communications in Medicine — Chuẩn truyền thông hình ảnh y khoa |
| HL7 | Health Level Seven — Chuẩn trao đổi dữ liệu y tế |
| HL7 FHIR | Fast Healthcare Interoperability Resources — Phiên bản hiện đại của HL7 |
| BHYT | Bảo hiểm Y tế |
| BHXH | Bảo hiểm Xã hội |
| CĐHA | Chẩn đoán hình ảnh |
| ICD-10 | International Classification of Diseases, 10th Revision |
| SOP | Standard Operating Procedure |
| RIS | Radiology Information System |
| Worklist | Danh sách công việc DICOM Modality Worklist |
| MPPS | Modality Performed Procedure Step |
| ORM | Order Message (HL7) |
| ORU | Observation Result (HL7) |
| ADT | Admit-Discharge-Transfer (HL7 message type) |
| MWL | Modality Worklist |
| C-STORE | DICOM service lưu ảnh |
| C-FIND | DICOM service truy vấn |
| C-MOVE | DICOM service di chuyển ảnh |
| WADO | Web Access to DICOM Objects |
| WADO-RS | WADO RESTful Services |
| DICOMweb | Bộ RESTful services cho DICOM (STOW-RS, WADO-RS, QIDO-RS) |
| AE Title | Application Entity Title — Định danh DICOM node |
| MLLP | Minimal Lower Layer Protocol — Giao thức truyền HL7 |
| SCP | Service Class Provider — Bên cung cấp dịch vụ DICOM |
| SCU | Service Class User — Bên sử dụng dịch vụ DICOM |
| GSPS | Grayscale Softcopy Presentation State |
| SR | Structured Report (DICOM) |
| TAT | Turnaround Time — Thời gian xử lý |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| RBAC | Role-Based Access Control |
| MFA | Multi-Factor Authentication |
| IHE | Integrating the Healthcare Enterprise |
| SWF | Scheduled Workflow (IHE profile) |
| PIR | Patient Information Reconciliation (IHE profile) |
| FEFO | First Expired, First Out |
| AMS | Antimicrobial Stewardship |

### 1.4. Tài liệu tham chiếu (References)

- IEEE 830-1998: Recommended Practice for Software Requirements Specifications
- DICOM Standard (PS3.x) — NEMA
- HL7 v2.x Messaging Standard
- HL7 FHIR R4 (nếu áp dụng)
- IHE (Integrating the Healthcare Enterprise) Technical Framework — Radiology
- IHE Integration Profiles: SWF (Scheduled Workflow), PIR (Patient Information Reconciliation)
- Thông tư 54/2017/TT-BYT — Quy định bộ tiêu chí ứng dụng CNTT tại cơ sở khám chữa bệnh
- Thông tư 56/2017/TT-BYT — Quy định chi tiết thi hành Luật BHYT
- Quyết định 4210/QĐ-BYT — Phê duyệt tiêu chuẩn kết nối liên thông y tế
- Chuẩn dữ liệu đầu ra theo quy định của BHXH Việt Nam (XML 4210)

### 1.5. Tổng quan tài liệu (Overview)

- Cấu trúc các phần còn lại của tài liệu
- Hướng dẫn đọc tài liệu

---

## 2. MÔ TẢ TỔNG QUAN (Overall Description)

### 2.1. Bối cảnh sản phẩm (Product Perspective)

#### 2.1.1. Sơ đồ ngữ cảnh hệ thống (System Context Diagram)

- Vị trí HIS-PACS trong hệ sinh thái CNTT bệnh viện
- Các hệ thống liên quan: cổng BHXH, hệ thống thiết bị y tế (Modalities), hệ thống báo cáo Bộ Y tế, LIS (nếu có)
- Sơ đồ C4 Level 1 — System Context

#### 2.1.2. Giao diện hệ thống (System Interfaces)

- **HIS ↔ PACS**: HL7 ORM/ORU messages + DICOM MWL/MPPS
- **HIS ↔ Cổng BHXH**: XML theo chuẩn 4210/QĐ-BYT
- **PACS ↔ Modalities** (CT, MRI, X-Ray, Ultrasound): DICOM C-STORE, MWL, MPPS
- **HIS ↔ Thiết bị ngoại vi**: máy in, máy đọc thẻ BHYT, barcode scanner

#### 2.1.3. Giao diện người dùng (User Interfaces)

- HIS: Giao diện web responsive (hỗ trợ trình duyệt Chrome, Edge, Firefox)
- PACS Viewer: Zero-footprint web viewer (HTML5) + thick client cho chẩn đoán chuyên sâu
- Responsive cho tablet (bác sĩ đi buồng nội trú)

#### 2.1.4. Giao diện phần cứng (Hardware Interfaces)

- Máy đọc thẻ BHYT
- Barcode/QR scanner
- Máy in (phiếu khám, toa thuốc, kết quả CĐHA)
- DICOM Modalities (CT, MRI, DR, CR, US, Mammography)
- DICOM Printer (film)

#### 2.1.5. Giao diện phần mềm (Software Interfaces)

- Hệ điều hành: Windows Server / Linux Server
- Database: PostgreSQL / Oracle / SQL Server
- Application Server: .NET Core / Java Spring Boot
- DICOM Server: Orthanc / dcm4chee / tự phát triển
- Message Broker: RabbitMQ / Kafka (cho HL7 messaging)
- Storage: SAN/NAS cho DICOM, Object Storage (MinIO/S3) cho long-term archive

#### 2.1.6. Giao diện truyền thông (Communication Interfaces)

- Giao thức mạng: TCP/IP, HTTP/HTTPS
- HL7 v2.x over MLLP (Minimal Lower Layer Protocol)
- DICOM over TCP (port 104, 11112)
- WADO-RS / DICOMweb cho web viewer
- REST API cho tích hợp nội bộ

#### 2.1.7. Ràng buộc bộ nhớ và lưu trữ (Memory & Storage Constraints)

- PACS storage: Ước lượng dung lượng theo số ca chụp/năm
- Archive tier: Hot (SSD) → Warm (HDD) → Cold (tape/cloud)
- Database sizing cho HIS

#### 2.1.8. Vận hành (Operations)

- Chế độ hoạt động: 24/7 cho PACS, giờ hành chính + trực cho HIS
- Backup/restore procedures
- Disaster recovery

### 2.2. Chức năng sản phẩm (Product Functions) — Tổng quan

#### 2.2.1. Phân hệ HIS

- **F-HIS-01**: Quản lý tiếp đón (Registration/ADT)
- **F-HIS-02**: Khám bệnh ngoại trú (Outpatient)
- **F-HIS-03**: Quản lý nội trú (Inpatient)
- **F-HIS-04**: Quản lý dược (Pharmacy)
- **F-HIS-05**: Quản lý viện phí (Billing)
- **F-HIS-06**: Quản lý BHYT (Insurance)

#### 2.2.2. Phân hệ PACS

- **F-PACS-01**: Tiếp nhận chỉ định từ HIS (Order Management)
- **F-PACS-02**: Quản lý Worklist & chụp ảnh (Acquisition Workflow)
- **F-PACS-03**: Lưu trữ ảnh DICOM (Archive)
- **F-PACS-04**: Xem/đọc ảnh — Viewer (Diagnostic Viewing)
- **F-PACS-05**: Trả kết quả về HIS (Result Reporting)

#### 2.2.3. Tích hợp HIS-PACS

- **F-INT-01**: Truyền chỉ định từ HIS sang PACS (HL7 ORM → DICOM MWL)
- **F-INT-02**: Nhận kết quả CĐHA từ PACS về HIS (HL7 ORU)
- **F-INT-03**: Đồng bộ thông tin bệnh nhân (HL7 ADT)
- **F-INT-04**: Xem ảnh DICOM từ trong HIS (WADO/DICOMweb)

### 2.3. Đặc điểm người dùng (User Characteristics)

| Vai trò | Mô tả | Trình độ CNTT | Module sử dụng |
|---------|-------|---------------|-----------------|
| Nhân viên tiếp đón | Tiếp nhận, đăng ký BN | Cơ bản | Tiếp đón |
| Bác sĩ khám ngoại trú | Khám, kê đơn, chỉ định CLS | Trung bình | Ngoại trú, xem kết quả CĐHA |
| Bác sĩ điều trị nội trú | Chỉ định, theo dõi điều trị | Trung bình | Nội trú, xem kết quả CĐHA |
| Điều dưỡng | Thực hiện y lệnh, chăm sóc | Cơ bản - Trung bình | Nội trú |
| Bác sĩ CĐHA (Radiologist) | Đọc ảnh, viết kết quả | Cao | PACS Viewer, báo cáo |
| Kỹ thuật viên CĐHA | Chụp ảnh, quản lý thiết bị | Trung bình | PACS Worklist |
| Dược sĩ | Phát thuốc, kiểm tra tương tác | Trung bình | Dược |
| Nhân viên viện phí | Tính phí, thu tiền | Cơ bản | Viện phí, BHYT |
| Quản trị hệ thống | Cấu hình, phân quyền | Cao | Admin toàn hệ thống |
| Ban giám đốc | Xem báo cáo, thống kê | Cơ bản | Dashboard, báo cáo |

### 2.4. Ràng buộc (Constraints)

- Tuân thủ quy định pháp luật Việt Nam về y tế và BHYT
- Tuân thủ Thông tư 54/2017/TT-BYT về CNTT y tế
- Tuân thủ chuẩn kết nối BHXH (XML 4210)
- Tuân thủ DICOM 3.0 và HL7 v2.x
- Bảo mật thông tin bệnh nhân theo quy định
- Thời gian phản hồi: < 3 giây cho thao tác thông thường
- Uptime: ≥ 99.5% cho HIS, ≥ 99.9% cho PACS

### 2.5. Giả định và phụ thuộc (Assumptions and Dependencies)

- Hạ tầng mạng LAN/WLAN đã sẵn sàng (≥ 1Gbps backbone)
- Các thiết bị CĐHA hỗ trợ DICOM 3.0
- Bệnh viện có kết nối Internet ổn định để liên thông BHXH
- Người dùng được đào tạo trước khi go-live
- Dữ liệu danh mục (ICD-10, danh mục thuốc, danh mục dịch vụ BHYT) được cung cấp bởi bệnh viện
- Bảng giá dịch vụ và quy tắc BHYT theo quy định hiện hành

---

## 3. YÊU CẦU CỤ THỂ (Specific Requirements)

### 3.1. YÊU CẦU CHỨC NĂNG (Functional Requirements)

---

#### 3.1.1. PHÂN HỆ QUẢN LÝ TIẾP ĐÓN (Registration / ADT)

##### 3.1.1.1. Đăng ký bệnh nhân mới (FR-REG-001)

- Nhập thông tin hành chính: họ tên, ngày sinh, giới tính, CMND/CCCD, địa chỉ, SĐT
- Quét/đọc thẻ BHYT (barcode, QR, chip)
- Kiểm tra trùng bệnh nhân (matching algorithm: tên + ngày sinh + CMND)
- Tạo mã bệnh nhân (PID) duy nhất toàn viện
- Chụp ảnh chân dung bệnh nhân (webcam)
- Phát sinh HL7 ADT^A04 (Register a patient) → truyền sang PACS

##### 3.1.1.2. Quản lý thẻ BHYT (FR-REG-002)

- Đọc mã thẻ BHYT (mã mới 15 ký tự theo Luật BHYT)
- Kiểm tra giá trị thẻ online qua cổng BHXH (API giám định)
- Xác định đúng tuyến/trái tuyến/cấp cứu
- Xác định tỷ lệ đồng chi trả (100%, 95%, 80%)
- Kiểm tra thông tuyến nội trú/ngoại trú
- Lưu lịch sử kiểm tra thẻ

##### 3.1.1.3. Đăng ký khám bệnh (FR-REG-003)

- Chọn phòng khám/chuyên khoa
- Phát số thứ tự (hàng đợi thông minh — ưu tiên: cấp cứu > BHYT > dịch vụ)
- In phiếu khám bệnh (mã vạch/QR)
- Hỗ trợ đăng ký nhiều phòng khám trong 1 lần đến
- Đăng ký khám theo hẹn (appointment)

##### 3.1.1.4. Nhập viện nội trú (FR-REG-004)

- Chuyển đổi từ ngoại trú sang nội trú
- Phân giường/phòng/khoa
- Phát sinh HL7 ADT^A01 (Admit) → truyền sang PACS
- In giấy nhập viện

##### 3.1.1.5. Chuyển khoa / Chuyển giường (FR-REG-005)

- Chuyển bệnh nhân giữa các khoa
- Cập nhật trạng thái giường
- Phát sinh HL7 ADT^A02 (Transfer)

##### 3.1.1.6. Ra viện (FR-REG-006)

- Quy trình xuất viện (có thanh toán viện phí)
- Phát sinh HL7 ADT^A03 (Discharge)
- In tóm tắt bệnh án, giấy ra viện

---

#### 3.1.2. PHÂN HỆ KHÁM BỆNH NGOẠI TRÚ (Outpatient)

##### 3.1.2.1. Màn hình danh sách chờ khám (FR-OPD-001)

- Hiển thị danh sách BN chờ khám theo phòng
- Gọi số (tích hợp màn hình LCD/TV phòng chờ)
- Sắp xếp ưu tiên: cấp cứu, ưu tiên, thường
- Lọc theo trạng thái: chờ khám, đang khám, đã khám

##### 3.1.2.2. Khám bệnh (FR-OPD-002)

- Ghi nhận lý do khám, tiền sử bệnh
- Ghi nhận triệu chứng, khám lâm sàng
- Nhập sinh hiệu (mạch, huyết áp, nhiệt độ, cân nặng, SpO2)
- Chẩn đoán sơ bộ và chẩn đoán xác định (ICD-10)
- Chẩn đoán phụ (comorbidities)
- Ghi nhận dị ứng thuốc

##### 3.1.2.3. Kê đơn thuốc (FR-OPD-003)

- Kê đơn từ danh mục thuốc (thuốc BHYT / thuốc dịch vụ)
- Kiểm tra tương tác thuốc (drug interaction check)
- Kiểm tra dị ứng thuốc đã khai báo
- Hỗ trợ đơn mẫu (order set / template)
- Hiển thị giá thuốc ước tính
- In đơn thuốc (tuân thủ mẫu Bộ Y tế)

##### 3.1.2.4. Chỉ định cận lâm sàng (FR-OPD-004)

- Chỉ định xét nghiệm (→ LIS nếu có)
- **Chỉ định CĐHA** (→ PACS): X-quang, CT, MRI, siêu âm, nội soi, v.v.
  - Chọn loại chỉ định từ danh mục dịch vụ CĐHA
  - Nhập thông tin lâm sàng (clinical indication)
  - Chọn mức độ ưu tiên (routine / urgent / stat)
  - **Phát sinh HL7 ORM^O01** (Order Message) → truyền sang PACS
- Chỉ định thủ thuật
- Hỗ trợ bộ chỉ định mẫu (order set)

##### 3.1.2.5. Xem kết quả cận lâm sàng (FR-OPD-005)

- Xem kết quả xét nghiệm (từ LIS)
- **Xem kết quả CĐHA** (từ PACS):
  - Nhận HL7 ORU^R01 (Result Message) từ PACS
  - Hiển thị báo cáo kết quả (text report)
  - **Xem ảnh DICOM trực tiếp trong HIS** qua WADO-RS / DICOMweb embedded viewer
  - Hiển thị trạng thái chỉ định: đã gửi → đang chụp → đã có kết quả
- Xem lịch sử kết quả CLS các lần khám trước

##### 3.1.2.6. Kết thúc khám (FR-OPD-006)

- Hoàn tất phiếu khám
- Hẹn tái khám
- Chuyển viện phí
- In phiếu chỉ định, biên lai

---

#### 3.1.3. PHÂN HỆ NỘI TRÚ (Inpatient)

##### 3.1.3.1. Quản lý giường bệnh (FR-IPD-001)

- Sơ đồ giường theo khoa/phòng (visual bed map)
- Trạng thái giường: trống, có BN, đang dọn, bảo trì
- Phân giường tự động/thủ công
- Chuyển giường/chuyển phòng

##### 3.1.3.2. Bệnh án điện tử nội trú (FR-IPD-002)

- Tạo bệnh án theo mẫu Bộ Y tế
- Ghi nhận diễn biến bệnh hàng ngày
- Y lệnh điều trị (thuốc, thủ thuật, chế độ ăn, chăm sóc)
- Phiếu theo dõi chức năng sống
- Phiếu chăm sóc điều dưỡng
- Phiếu phẫu thuật/thủ thuật

##### 3.1.3.3. Chỉ định cận lâm sàng nội trú (FR-IPD-003)

- Tương tự FR-OPD-004 nhưng áp dụng cho bệnh nhân nội trú
- **Chỉ định CĐHA nội trú → PACS** (HL7 ORM^O01 với visit type = Inpatient)
- Chỉ định theo y lệnh hàng ngày
- Theo dõi trạng thái thực hiện chỉ định

##### 3.1.3.4. Tổng kết bệnh án (FR-IPD-004)

- Tóm tắt ra viện
- Chẩn đoán ra viện (ICD-10)
- Hướng xử trí: ra viện, chuyển viện, trốn viện, tử vong
- Đơn thuốc ra viện
- Giấy hẹn tái khám
- In bệnh án (bản cứng lưu trữ)

##### 3.1.3.5. Hội chẩn (FR-IPD-005)

- Tạo phiếu hội chẩn
- Ghi nhận kết quả hội chẩn
- Kết nối kết quả CĐHA phục vụ hội chẩn (xem ảnh DICOM)

---

#### 3.1.4. PHÂN HỆ DƯỢC (Pharmacy)

##### 3.1.4.1. Quản lý danh mục thuốc (FR-PHA-001)

- Danh mục thuốc theo DM thuốc BHYT (Thông tư hiện hành)
- Thông tin thuốc: tên hoạt chất, tên thương mại, dạng bào chế, nồng độ/hàm lượng, đơn vị, giá
- Phân nhóm: thuốc BHYT, thuốc dịch vụ, thuốc gây nghiện/hướng thần
- Mapping mã thuốc BHXH

##### 3.1.4.2. Quản lý kho thuốc (FR-PHA-002)

- Nhập kho (từ nhà cung cấp, từ kho khác)
- Xuất kho (theo đơn, xuất khoa/phòng, xuất hủy)
- Chuyển kho
- Kiểm kê
- Quản lý lô, hạn dùng (FEFO — First Expired, First Out)
- Cảnh báo thuốc sắp hết hạn, tồn kho tối thiểu

##### 3.1.4.3. Phát thuốc ngoại trú (FR-PHA-003)

- Nhận đơn thuốc từ module khám bệnh
- Kiểm tra tồn kho
- Phát thuốc, xác nhận phát
- In nhãn thuốc, hướng dẫn sử dụng
- Xử lý thay thế thuốc (khi hết thuốc kê)

##### 3.1.4.4. Phát thuốc nội trú (FR-PHA-004)

- Nhận y lệnh thuốc từ module nội trú
- Phát thuốc theo ngày/theo đợt
- Cắt thuốc nội trú
- Hoàn trả thuốc

##### 3.1.4.5. Quản lý thuốc đặc biệt (FR-PHA-005)

- Thuốc gây nghiện, hướng thần: sổ theo dõi riêng
- Thuốc kiểm soát đặc biệt
- Kháng sinh: theo dõi sử dụng kháng sinh (AMS — Antimicrobial Stewardship)

##### 3.1.4.6. Báo cáo dược (FR-PHA-006)

- Báo cáo xuất nhập tồn
- Báo cáo sử dụng thuốc theo khoa/bác sĩ
- Báo cáo thuốc BHYT/dịch vụ
- Báo cáo thuốc gây nghiện/hướng thần (mẫu Bộ Y tế)

---

#### 3.1.5. PHÂN HỆ VIỆN PHÍ (Billing)

##### 3.1.5.1. Quản lý bảng giá (FR-BIL-001)

- Bảng giá dịch vụ BHYT (theo Thông tư liên tịch)
- Bảng giá dịch vụ yêu cầu (dịch vụ)
- Bảng giá theo đối tượng (BHYT, viện phí, miễn phí, nước ngoài)
- Quản lý thay đổi giá theo thời gian (effective date)

##### 3.1.5.2. Tạm ứng viện phí (FR-BIL-002)

- Thu tạm ứng nội trú
- Quản lý sổ tạm ứng
- Cảnh báo khi tạm ứng sắp hết

##### 3.1.5.3. Tính phí tự động (FR-BIL-003)

- Tổng hợp chi phí từ tất cả module: khám, thuốc, CĐHA, xét nghiệm, giường, thủ thuật
- Phân loại chi phí: BHYT chi trả / BN tự trả / BN cùng chi trả
- Áp dụng quy tắc BHYT: trần thanh toán, gói dịch vụ, chi phí ngoài danh mục
- Tính phí chênh lệch giường bệnh theo yêu cầu

##### 3.1.5.4. Thu phí / Thanh toán (FR-BIL-004)

- Thu tiền mặt, chuyển khoản, thẻ, ví điện tử
- In hóa đơn/biên lai (kết nối hóa đơn điện tử)
- Miễn giảm viện phí (theo quyết định)
- Hoàn phí

##### 3.1.5.5. Quyết toán nội trú (FR-BIL-005)

- Tổng hợp toàn bộ chi phí đợt điều trị nội trú
- Bảng kê chi phí KCB (mẫu 01/BV hoặc mẫu C79a-HD theo BHXH)
- Quyết toán BHYT
- In bảng kê chi phí cho bệnh nhân

---

#### 3.1.6. PHÂN HỆ BẢO HIỂM Y TẾ (Insurance)

##### 3.1.6.1. Giám định BHYT (FR-INS-001)

- Kiểm tra online thẻ BHYT (API cổng giám định BHXH)
- Kiểm tra 5 năm liên tục
- Kiểm tra đúng tuyến/trái tuyến
- Kiểm tra thông tuyến huyện/tỉnh
- Lưu log kiểm tra

##### 3.1.6.2. Áp dụng chính sách BHYT (FR-INS-002)

- Tỷ lệ đồng chi trả theo đối tượng (100%, 95%, 80%)
- Trần thanh toán thuốc/VTYT
- Danh mục thuốc BHYT cho phép
- Danh mục dịch vụ kỹ thuật BHYT
- Xử lý trường hợp đặc biệt: cấp cứu, chuyển tuyến, tai nạn giao thông

##### 3.1.6.3. Tổng hợp và gửi dữ liệu BHXH (FR-INS-003)

- Xuất XML 4210 theo format BHXH
- Gửi hồ sơ giám định lên cổng BHXH
- Nhận kết quả giám định
- Xử lý từ chối/yêu cầu bổ sung

##### 3.1.6.4. Đối soát BHYT (FR-INS-004)

- Đối soát dữ liệu gửi và dữ liệu duyệt
- Phân tích lý do từ chối
- Báo cáo chênh lệch
- Hỗ trợ khiếu nại/điều chỉnh

##### 3.1.6.5. Báo cáo BHYT (FR-INS-005)

- Báo cáo 20 — Quyết toán chi phí KCB BHYT
- Báo cáo 21 — Tổng hợp chi phí KCB ngoại trú
- Báo cáo 79a — Bảng kê chi phí KCB
- Báo cáo theo yêu cầu BHXH cấp huyện/tỉnh

---

#### 3.1.7. PHÂN HỆ PACS — TIẾP NHẬN CHỈ ĐỊNH TỪ HIS (PACS Order Management)

##### 3.1.7.1. Nhận chỉ định từ HIS (FR-PACS-ORD-001)

- Nhận HL7 ORM^O01 (New Order) từ HIS
- Parse thông tin: PID (patient ID), bệnh nhân, chỉ định, phòng chỉ định, bác sĩ chỉ định, mức ưu tiên, thông tin lâm sàng
- Mapping dịch vụ HIS → Procedure Code PACS
- Tạo Accession Number duy nhất
- Lưu order vào PACS database
- Xử lý trạng thái order: New → Scheduled → In Progress → Completed → Reported

##### 3.1.7.2. Quản lý sửa/hủy chỉ định (FR-PACS-ORD-002)

- Nhận HL7 ORM^O01 (Order Update/Cancel) từ HIS
- Xử lý hủy chỉ định (nếu chưa thực hiện)
- Xử lý sửa chỉ định (thay đổi loại chụp, thông tin BN)
- Cảnh báo nếu chỉ định đã thực hiện (không cho hủy)

##### 3.1.7.3. DICOM Modality Worklist (FR-PACS-ORD-003)

- Cung cấp DICOM MWL (C-FIND SCP) cho các Modalities
- Truy vấn theo: ngày chụp, phòng chụp, modality, trạng thái
- Thông tin trả về: Patient Name, Patient ID, Accession Number, Procedure, Scheduled Station AET, Date/Time
- Auto-populate thông tin BN trên Modality (tránh nhập sai)

---

#### 3.1.8. PHÂN HỆ PACS — LƯU TRỮ ẢNH DICOM (PACS Archive)

##### 3.1.8.1. Tiếp nhận ảnh DICOM (FR-PACS-ARC-001)

- DICOM C-STORE SCP: nhận ảnh từ Modalities
- Hỗ trợ tất cả SOP Classes phổ biến: CT, MR, CR, DX, US, MG, NM, PT, SC, SR
- Kiểm tra DICOM conformance khi nhận
- Auto-routing: phân phối ảnh đến workstation phù hợp
- Ghi nhận MPPS (Modality Performed Procedure Step) nếu có

##### 3.1.8.2. Lưu trữ và quản lý ảnh (FR-PACS-ARC-002)

- Lưu trữ theo cấu trúc: Patient → Study → Series → Instance
- Hỗ trợ lossless compression (JPEG 2000 lossless, JPEG-LS)
- Tiered storage: Online (SSD/HDD) → Nearline → Offline (archive)
- Chính sách lưu trữ theo thời gian (retention policy): ≥ 10 năm cho CĐHA
- De-identification cho nghiên cứu (nếu cần)

##### 3.1.8.3. Truy vấn và truy xuất ảnh (FR-PACS-ARC-003)

- DICOM C-FIND SCP: tìm kiếm theo Patient, Study, Series level
- DICOM C-MOVE SCP: gửi ảnh đến workstation chỉ định
- DICOM C-GET SCP
- WADO-RS / DICOMweb: truy cập ảnh qua HTTP cho web viewer
- Tìm kiếm nâng cao: theo ngày, modality, referring physician, body part, accession number

##### 3.1.8.4. Quản lý lưu trữ (FR-PACS-ARC-004)

- Monitoring dung lượng lưu trữ
- Auto-migration giữa các tier lưu trữ
- Purge/delete theo chính sách (có audit trail)
- Backup/Disaster Recovery cho DICOM data
- Integrity check (checksum verification)

---

#### 3.1.9. PHÂN HỆ PACS — VIEWER CHẨN ĐOÁN HÌNH ẢNH (Diagnostic Viewer)

##### 3.1.9.1. Web Viewer — Zero-footprint (FR-PACS-VWR-001)

- Viewer chạy trên trình duyệt (HTML5/WebGL), không cần cài đặt
- Hỗ trợ hiển thị tất cả DICOM modalities phổ biến
- Công cụ cơ bản: zoom, pan, window/level (W/L), rotate, flip
- Measurement tools: ruler, angle, ellipse ROI, pixel probe
- Annotation tools: text, arrow, marker
- Hỗ trợ Hanging Protocol (bố trí tự động series theo loại khám)
- Multi-monitor support (dual screen cho CĐHA)
- Cine mode (cho CT/MR đa lát cắt)

##### 3.1.9.2. Công cụ nâng cao (FR-PACS-VWR-002)

- MPR (Multi-Planar Reconstruction): Axial, Sagittal, Coronal
- 3D rendering cơ bản (MIP, VR) — nếu nằm trong scope
- Comparison study: so sánh ảnh trước/sau (prior studies)
- Key Image Note: đánh dấu ảnh quan trọng
- Presentation State: lưu trạng thái hiển thị (DICOM GSPS)
- Prefetch prior studies tự động

##### 3.1.9.3. Viewer tích hợp trong HIS (FR-PACS-VWR-003)

- Embedded viewer trong giao diện HIS (iframe/component)
- Context launch: click từ chỉ định trong HIS → mở đúng study trong viewer
- URL-based launch: `https://pacs-viewer/study?accession={accession_number}`
- WADO-RS integration cho thumbnail preview trong HIS

---

#### 3.1.10. PHÂN HỆ PACS — TRẢ KẾT QUẢ VỀ HIS (Result Reporting)

##### 3.1.10.1. Viết báo cáo kết quả CĐHA (FR-PACS-RPT-001)

- Giao diện viết kết quả tích hợp với viewer
- Template báo cáo theo loại chụp (CT ngực, X-quang tim phổi, MRI sọ não, v.v.)
- Structured reporting (DICOM SR) — khuyến khích
- Free-text reporting (bắt buộc hỗ trợ)
- Speech-to-text integration (tùy chọn)
- Chèn key images vào báo cáo
- Trạng thái báo cáo: Draft → Preliminary → Final → Amended → Addendum

##### 3.1.10.2. Duyệt và ký kết quả (FR-PACS-RPT-002)

- Quy trình duyệt: BS đọc → BS duyệt (senior) → Final
- Chữ ký điện tử (digital signature) cho báo cáo cuối cùng
- Lưu audit trail: ai đọc, ai duyệt, thời gian

##### 3.1.10.3. Gửi kết quả về HIS (FR-PACS-RPT-003)

- **Phát sinh HL7 ORU^R01** (Observation Result) khi báo cáo ở trạng thái Final
- Nội dung: kết quả text, kết luận, ICD chẩn đoán CĐHA, link xem ảnh (WADO URL)
- HIS nhận ORU → cập nhật trạng thái chỉ định → hiển thị kết quả cho bác sĩ lâm sàng
- Gửi HL7 ORU^R01 (Amended) nếu sửa kết quả
- Notification cho bác sĩ chỉ định khi có kết quả (real-time alert)

##### 3.1.10.4. In kết quả CĐHA (FR-PACS-RPT-004)

- In báo cáo kèm key images
- In film DICOM (DICOM Print SCU)
- Xuất PDF kết quả
- Gửi kết quả qua email/SMS cho bệnh nhân (tùy chọn)

---

#### 3.1.11. TÍCH HỢP HIS-PACS (Integration)

##### 3.1.11.1. Integration Engine / Interface Engine (FR-INT-ENG-001)

- Mô tả kiến trúc Integration Engine (Mirth Connect / tự phát triển)
- Message routing, transformation, filtering
- Queue management cho reliability
- Error handling và dead-letter queue
- Monitoring dashboard cho message flow

##### 3.1.11.2. Luồng dữ liệu HL7 (FR-INT-HL7-001)

**Bảng tổng hợp HL7 Messages:**

| Message | Trigger | Hướng | Mô tả |
|---------|---------|-------|-------|
| ADT^A04 | Đăng ký BN | HIS → PACS | Đăng ký bệnh nhân mới |
| ADT^A08 | Cập nhật BN | HIS → PACS | Cập nhật thông tin BN |
| ADT^A01 | Nhập viện | HIS → PACS | Nhập viện nội trú |
| ADT^A02 | Chuyển khoa | HIS → PACS | Chuyển khoa/phòng |
| ADT^A03 | Ra viện | HIS → PACS | Xuất viện |
| ORM^O01 | Chỉ định CĐHA | HIS → PACS | Tạo mới/sửa/hủy chỉ định |
| ORU^R01 | Kết quả CĐHA | PACS → HIS | Trả kết quả CĐHA |
| ACK | Xác nhận | Hai chiều | Acknowledge message |

##### 3.1.11.3. Luồng dữ liệu DICOM (FR-INT-DCM-001)

**Bảng tổng hợp DICOM Services:**

| Service | Hướng | Mô tả |
|---------|-------|-------|
| MWL (C-FIND) | PACS → Modality | Worklist cho thiết bị chụp |
| C-STORE | Modality → PACS | Gửi ảnh vào PACS |
| MPPS | Modality → PACS | Thông báo trạng thái chụp |
| C-FIND | Viewer → PACS | Truy vấn study |
| C-MOVE | PACS → Viewer | Gửi ảnh đến viewer |
| WADO-RS | HIS → PACS | Xem ảnh qua web |

##### 3.1.11.4. Quy trình tích hợp end-to-end (FR-INT-E2E-001)

**Workflow hoàn chỉnh — Ví dụ: Chỉ định chụp X-quang ngực:**

```
Bước 1: [HIS] Bác sĩ tạo chỉ định CĐHA "X-quang ngực thẳng"

Bước 2: [HIS → PACS] HIS gửi HL7 ORM^O01 (New Order)
         → Integration Engine nhận, validate, transform
         → PACS nhận, tạo order, cấp Accession Number
         → PACS gửi HL7 ACK về HIS

Bước 3: [PACS → Modality] Kỹ thuật viên gọi MWL từ máy X-quang
         → Máy X-quang gửi C-FIND request
         → PACS trả worklist có thông tin BN + chỉ định
         → KTV chọn BN, chụp

Bước 4: [Modality → PACS] Máy X-quang gửi ảnh qua C-STORE
         → PACS nhận, lưu trữ, index
         → Modality gửi MPPS (Completed)

Bước 5: [PACS] Bác sĩ CĐHA mở viewer, đọc ảnh
         → Viết kết quả (báo cáo)
         → BS duyệt → Final report

Bước 6: [PACS → HIS] PACS gửi HL7 ORU^R01 (Result)
         → Integration Engine nhận, transform
         → HIS nhận, cập nhật trạng thái chỉ định = "Đã có kết quả"

Bước 7: [HIS] Bác sĩ lâm sàng xem kết quả + ảnh trong HIS
         → Click "Xem ảnh" → WADO-RS lấy ảnh từ PACS hiển thị inline
```

##### 3.1.11.5. Xử lý lỗi tích hợp (FR-INT-ERR-001)

- Retry mechanism: tự gửi lại khi thất bại (exponential backoff)
- Dead-letter queue cho message lỗi
- Alert khi tích hợp gián đoạn (email, SMS)
- Reconciliation: đối soát chỉ định HIS vs PACS định kỳ
- Manual fallback: nhập thủ công trên PACS khi tích hợp gián đoạn

##### 3.1.11.6. Đồng bộ Master Data (FR-INT-SYNC-001)

- Đồng bộ danh mục bệnh nhân (Patient Master Index)
- Đồng bộ danh mục dịch vụ CĐHA
- Đồng bộ danh mục bác sĩ
- Đồng bộ danh mục phòng/khoa

---

#### 3.1.12. QUẢN TRỊ HỆ THỐNG (System Administration)

##### 3.1.12.1. Quản lý người dùng và phân quyền (FR-ADM-001)

- RBAC (Role-Based Access Control)
- Quản lý user, role, permission
- Phân quyền theo module, chức năng, khoa/phòng
- LDAP/Active Directory integration (tùy chọn)
- Single Sign-On giữa HIS và PACS

##### 3.1.12.2. Quản lý danh mục dùng chung (FR-ADM-002)

- Danh mục ICD-10
- Danh mục dịch vụ kỹ thuật
- Danh mục thuốc
- Danh mục khoa/phòng
- Danh mục nhân viên/bác sĩ

##### 3.1.12.3. Audit Trail (FR-ADM-003)

- Ghi log tất cả thao tác: ai, lúc nào, làm gì, trên dữ liệu gì
- Log truy cập hồ sơ bệnh nhân (HIPAA-style)
- Log DICOM access (ai xem ảnh nào, lúc nào)
- Báo cáo audit theo yêu cầu

##### 3.1.12.4. Báo cáo và thống kê (FR-ADM-004)

- Dashboard tổng quan (số BN khám/ngày, doanh thu, công suất giường)
- Báo cáo hoạt động theo khoa
- Báo cáo CĐHA: số ca chụp, TAT (turnaround time), workload bác sĩ
- Báo cáo theo mẫu Bộ Y tế (báo cáo 4210, báo cáo hoạt động BV)
- Export Excel/PDF

---

### 3.2. YÊU CẦU PHI CHỨC NĂNG (Non-Functional Requirements)

#### 3.2.1. Hiệu năng (Performance — NFR-PERF)

| ID | Yêu cầu | Metric |
|----|---------|--------|
| NFR-PERF-001 | Thời gian phản hồi thao tác thông thường HIS | ≤ 3 giây |
| NFR-PERF-002 | Thời gian mở ảnh DICOM (CR/DX) trên viewer | ≤ 5 giây |
| NFR-PERF-003 | Thời gian mở study CT/MR (200+ ảnh) | ≤ 15 giây |
| NFR-PERF-004 | Thời gian xử lý HL7 message | ≤ 1 giây |
| NFR-PERF-005 | Throughput DICOM C-STORE | ≥ 50 ảnh/giây |
| NFR-PERF-006 | Số user đồng thời HIS | ≥ 200 |
| NFR-PERF-007 | Số ca CĐHA/ngày | ≥ 500 |

#### 3.2.2. Khả năng mở rộng (Scalability — NFR-SCAL)

- NFR-SCAL-001: Horizontal scaling cho web server HIS
- NFR-SCAL-002: Storage scaling cho PACS (thêm node/disk không downtime)
- NFR-SCAL-003: Hỗ trợ multi-site (nhiều cơ sở, PACS tập trung)

#### 3.2.3. Độ sẵn sàng (Availability — NFR-AVAIL)

- NFR-AVAIL-001: HIS uptime ≥ 99.5% (downtime ≤ 44 giờ/năm)
- NFR-AVAIL-002: PACS uptime ≥ 99.9% (downtime ≤ 8.7 giờ/năm)
- NFR-AVAIL-003: RPO (Recovery Point Objective) ≤ 1 giờ
- NFR-AVAIL-004: RTO (Recovery Time Objective) ≤ 4 giờ
- NFR-AVAIL-005: Zero data loss cho DICOM images

#### 3.2.4. Bảo mật (Security — NFR-SEC)

- NFR-SEC-001: Mã hóa truyền tải (TLS 1.2+)
- NFR-SEC-002: Mã hóa dữ liệu tại rest (AES-256) cho dữ liệu nhạy cảm
- NFR-SEC-003: Authentication: username/password + MFA (tùy chọn)
- NFR-SEC-004: Session timeout: 30 phút không hoạt động
- NFR-SEC-005: Password policy: ≥ 8 ký tự, phức tạp, đổi 90 ngày
- NFR-SEC-006: Bảo mật thông tin bệnh nhân theo quy định pháp luật VN
- NFR-SEC-007: DICOM TLS cho truyền ảnh ngoài mạng nội bộ
- NFR-SEC-008: Audit log không thể xóa/sửa (append-only)

#### 3.2.5. Khả năng bảo trì (Maintainability — NFR-MAIN)

- NFR-MAIN-001: Mã nguồn có tài liệu (code documentation)
- NFR-MAIN-002: Modular architecture (microservices hoặc modular monolith)
- NFR-MAIN-003: Cấu hình hệ thống qua file/database (không hardcode)
- NFR-MAIN-004: Hỗ trợ cập nhật không downtime (rolling update)

#### 3.2.6. Tính khả chuyển (Portability — NFR-PORT)

- NFR-PORT-001: HIS chạy trên trình duyệt chuẩn (Chrome, Edge, Firefox)
- NFR-PORT-002: PACS viewer hỗ trợ tối thiểu Chrome và Edge
- NFR-PORT-003: Server hỗ trợ Windows Server và Linux
- NFR-PORT-004: Database abstraction (hỗ trợ ≥ 2 DBMS)

#### 3.2.7. Tính tương thích (Compatibility — NFR-COMP)

- NFR-COMP-001: DICOM 3.0 conformance (DICOM Conformance Statement bắt buộc)
- NFR-COMP-002: HL7 v2.x conformance
- NFR-COMP-003: IHE SWF (Scheduled Workflow) profile
- NFR-COMP-004: Tích hợp cổng BHXH (XML 4210)
- NFR-COMP-005: ICD-10 WHO và ICD-10 VN

#### 3.2.8. Khả năng sử dụng (Usability — NFR-USE)

- NFR-USE-001: Thời gian đào tạo người dùng cơ bản ≤ 8 giờ
- NFR-USE-002: Hỗ trợ phím tắt cho thao tác thường xuyên
- NFR-USE-003: Giao diện tiếng Việt, hỗ trợ Unicode
- NFR-USE-004: Responsive design cho tablet (bác sĩ đi buồng)
- NFR-USE-005: Online help / user manual tích hợp

---

### 3.3. YÊU CẦU GIAO DIỆN BÊN NGOÀI (External Interface Requirements)

#### 3.3.1. Giao diện người dùng (User Interfaces)

- Wireframe/mockup mẫu cho từng module chính (đính kèm phụ lục)
- Nguyên tắc thiết kế UI/UX: consistent, accessible, minimal clicks
- Hỗ trợ multi-tab/multi-window cho bác sĩ

#### 3.3.2. Giao diện phần cứng (Hardware Interfaces)

- Đặc tả kết nối máy đọc thẻ BHYT
- Đặc tả kết nối barcode/QR scanner
- Đặc tả kết nối DICOM Modalities (AE Title, port, transfer syntax)

#### 3.3.3. Giao diện phần mềm (Software Interfaces)

- API specification cho tích hợp bên thứ ba
- DICOM Conformance Statement
- HL7 Message specification (segment-level detail)

#### 3.3.4. Giao diện truyền thông (Communication Interfaces)

- Network topology requirement
- Bandwidth requirement cho DICOM traffic
- VPN requirement cho remote access

---

## 4. PHỤ LỤC (Appendices)

### Phụ lục A — Mô hình dữ liệu (Data Model)

- ERD tổng quan HIS
- ERD tổng quan PACS
- DICOM Information Model (Patient → Study → Series → Instance)

### Phụ lục B — Sơ đồ quy trình nghiệp vụ (Business Process Diagrams)

- BPMN: Quy trình khám ngoại trú end-to-end
- BPMN: Quy trình nội trú end-to-end
- BPMN: Quy trình CĐHA end-to-end (HIS → PACS → HIS)
- BPMN: Quy trình thanh toán BHYT

### Phụ lục C — HL7 Message Specification

- Chi tiết cấu trúc ADT^A04, ADT^A01, ADT^A02, ADT^A03, ADT^A08
- Chi tiết cấu trúc ORM^O01 (New, Update, Cancel)
- Chi tiết cấu trúc ORU^R01
- Chi tiết cấu trúc ACK
- Mapping table: HIS field → HL7 segment.field

**Ví dụ cấu trúc HL7 ORM^O01 (Order Message):**

```
MSH|^~\&|HIS|HOSPITAL|PACS|HOSPITAL|20260324120000||ORM^O01|MSG00001|P|2.4
PID|1||PID123456^^^HIS||NGUYEN^VAN A||19800115|M|||123 DUONG ABC, QUAN 1, HCM||0901234567
PV1|1|O|OPD^ROOM01^^HOSPITAL||||DR001^NGUYEN^VAN B^BS||||||||||||V001^^^HIS
ORC|NW|ORD001^HIS||||||^^^20260324120000^^R||DR001^NGUYEN^VAN B^BS
OBR|1|ORD001^HIS||XRAY-CHEST^X-QUANG NGUC THANG^LOCAL|||20260324120000||||||||DR001^NGUYEN^VAN B^BS||||||||||1^^^20260324120000^^R
```

**Ví dụ cấu trúc HL7 ORU^R01 (Result Message):**

```
MSH|^~\&|PACS|HOSPITAL|HIS|HOSPITAL|20260324140000||ORU^R01|MSG00002|P|2.4
PID|1||PID123456^^^HIS||NGUYEN^VAN A||19800115|M
OBR|1|ORD001^HIS|ACC001^PACS|XRAY-CHEST^X-QUANG NGUC THANG^LOCAL|||20260324120000|||||||20260324133000||DR002^TRAN^VAN C^BS_CDHA||||||||F
OBX|1|TX|FINDING||Tim khong to. Hai phoi sang. Khong tran dich mang phoi.||||||F
OBX|2|TX|CONCLUSION||Hinh anh X-quang nguc binh thuong.||||||F
OBX|3|RP|WADO_URL||https://pacs.hospital.vn/wado-rs/studies/1.2.3.4.5||||||F
```

### Phụ lục D — DICOM Conformance Statement

- Supported SOP Classes
- Supported Transfer Syntaxes
- Association Policies
- Network configuration

### Phụ lục E — Danh sách IHE Integration Profiles áp dụng

- SWF (Scheduled Workflow)
- PIR (Patient Information Reconciliation)
- CDA (Consistent Data Access)

### Phụ lục F — Bảng ma trận truy vết yêu cầu (Requirements Traceability Matrix)

- Mapping: Business Requirement → Functional Requirement → Test Case

| Business Req | Functional Req | Module | Priority | Test Case |
|-------------|---------------|--------|----------|-----------|
| BR-001: Tiếp đón BN | FR-REG-001 ~ FR-REG-006 | Tiếp đón | High | TC-REG-xxx |
| BR-002: Khám ngoại trú | FR-OPD-001 ~ FR-OPD-006 | Ngoại trú | High | TC-OPD-xxx |
| BR-003: Điều trị nội trú | FR-IPD-001 ~ FR-IPD-005 | Nội trú | High | TC-IPD-xxx |
| BR-004: Quản lý dược | FR-PHA-001 ~ FR-PHA-006 | Dược | High | TC-PHA-xxx |
| BR-005: Viện phí | FR-BIL-001 ~ FR-BIL-005 | Viện phí | High | TC-BIL-xxx |
| BR-006: BHYT | FR-INS-001 ~ FR-INS-005 | BHYT | High | TC-INS-xxx |
| BR-007: PACS Order | FR-PACS-ORD-001 ~ 003 | PACS | High | TC-PORD-xxx |
| BR-008: PACS Archive | FR-PACS-ARC-001 ~ 004 | PACS | High | TC-PARC-xxx |
| BR-009: PACS Viewer | FR-PACS-VWR-001 ~ 003 | PACS | High | TC-PVWR-xxx |
| BR-010: PACS Report | FR-PACS-RPT-001 ~ 004 | PACS | High | TC-PRPT-xxx |
| BR-011: Tích hợp HIS-PACS | FR-INT-xxx | Integration | Critical | TC-INT-xxx |
| BR-012: Quản trị | FR-ADM-001 ~ 004 | Admin | Medium | TC-ADM-xxx |

### Phụ lục G — Thuật ngữ y khoa (Medical Glossary)

---

## 5. MỤC LỤC THAM CHIẾU (Index)

### Chỉ mục theo Module

| Module | Mã yêu cầu | Phần |
|--------|-----------|------|
| Tiếp đón | FR-REG-001 ~ 006 | 3.1.1 |
| Ngoại trú | FR-OPD-001 ~ 006 | 3.1.2 |
| Nội trú | FR-IPD-001 ~ 005 | 3.1.3 |
| Dược | FR-PHA-001 ~ 006 | 3.1.4 |
| Viện phí | FR-BIL-001 ~ 005 | 3.1.5 |
| BHYT | FR-INS-001 ~ 005 | 3.1.6 |
| PACS Order | FR-PACS-ORD-001 ~ 003 | 3.1.7 |
| PACS Archive | FR-PACS-ARC-001 ~ 004 | 3.1.8 |
| PACS Viewer | FR-PACS-VWR-001 ~ 003 | 3.1.9 |
| PACS Report | FR-PACS-RPT-001 ~ 004 | 3.1.10 |
| Tích hợp | FR-INT-xxx | 3.1.11 |
| Quản trị | FR-ADM-001 ~ 004 | 3.1.12 |
| Phi chức năng | NFR-xxx | 3.2 |

---

## SƠ ĐỒ KIẾN TRÚC TÍCH HỢP HIS-PACS

```
┌──────────────────────────────────────────────────────────────┐
│                           HIS                                 │
│  ┌────────┐ ┌──────────┐ ┌────────┐ ┌──────┐ ┌──────┐ ┌───┐│
│  │Tiếp đón│ │Ngoại trú │ │Nội trú │ │ Dược │ │V.Phí │ │BHY││
│  └───┬────┘ └─────┬────┘ └───┬────┘ └──────┘ └──────┘ └───┘│
│      │            │           │                               │
│      │   ADT      │  ORM^O01  │   ORM^O01                    │
│      └─────┬──────┴──────┬────┘                               │
│            │             │           ORU^R01                   │
│            ▼             ▼             ▲                       │
│  ┌─────────────────────────────────────────┐                  │
│  │      Integration Engine (HL7 / REST)    │                  │
│  │      (Mirth Connect / Custom)           │                  │
│  └────────────┬──────────────▲─────────────┘                  │
└───────────────┼──────────────┼────────────────────────────────┘
                │HL7 (MLLP)   │HL7 (MLLP)
                ▼              │
┌───────────────┼──────────────┼────────────────────────────────┐
│               ▼              │           PACS                  │
│  ┌─────────────────────────────────────┐                      │
│  │      Integration Engine (HL7)       │                      │
│  └────────┬─────────────▲──────────────┘                      │
│           │             │                                      │
│    ┌──────▼────┐  ┌─────┴──────┐  ┌───────────┐  ┌─────────┐│
│    │  Order    │  │  Report    │  │  DICOM    │  │ Viewer  ││
│    │  Manager  │  │  Manager   │  │  Archive  │  │(Web/Fat)││
│    └──────┬────┘  └────────────┘  └─────▲─────┘  └────┬────┘│
│           │                             │              │      │
│           │ MWL (C-FIND)         C-STORE│        WADO-RS     │
│           ▼                             │              │      │
│    ┌───────────────┐                    │              │      │
│    │  Modalities   │────────────────────┘              │      │
│    │ CT/MR/XR/US   │                                   │      │
│    └───────────────┘                                   │      │
└────────────────────────────────────────────────────────┼──────┘
                                                         │
                                           WADO-RS / DICOMweb
                                                         │
                                          ┌──────────────▼─────┐
                                          │  Embedded Viewer   │
                                          │  trong HIS (Web)   │
                                          └────────────────────┘
```

---

## TỔNG KẾT

### Thống kê yêu cầu

| Loại | Số lượng | Phạm vi |
|------|---------|---------|
| Yêu cầu chức năng (FR) | ~120 | HIS + PACS + Integration |
| Yêu cầu phi chức năng (NFR) | ~25 | Performance, Security, Availability, ... |
| HL7 Message Types | 8 | ADT, ORM, ORU, ACK |
| DICOM Services | 6 | MWL, C-STORE, MPPS, C-FIND, C-MOVE, WADO-RS |
| User Roles | 10 | Từ tiếp đón đến quản trị |
| Modules HIS | 6 | Tiếp đón, Ngoại trú, Nội trú, Dược, Viện phí, BHYT |
| Modules PACS | 4 | Order, Archive, Viewer, Reporting |

### Ghi chú cho người viết SRS chi tiết

Mỗi mục FR/NFR khi triển khai viết chi tiết cần bao gồm:

1. **Mô tả** (Description): Mô tả chức năng rõ ràng
2. **Input/Output**: Dữ liệu đầu vào và đầu ra
3. **Precondition**: Điều kiện tiên quyết
4. **Postcondition**: Trạng thái sau khi thực hiện
5. **Business Rules**: Quy tắc nghiệp vụ áp dụng
6. **Exception Handling**: Xử lý ngoại lệ
7. **Priority**: MoSCoW (Must/Should/Could/Won't) hoặc High/Medium/Low
8. **Dependencies**: Phụ thuộc vào yêu cầu khác

---

*Tài liệu này là dàn ý chi tiết (detailed outline) theo chuẩn IEEE 830-1998. Cần được mở rộng thành tài liệu SRS đầy đủ với mô tả chi tiết cho từng yêu cầu.*
