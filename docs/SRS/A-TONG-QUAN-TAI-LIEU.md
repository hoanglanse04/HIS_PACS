# PHẦN A — TỔNG QUAN TÀI LIỆU

> **Mã phần**: SRS-PART-A  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## A.1. Mục đích tài liệu

### A.1.1. Mục đích chính

Tài liệu Đặc tả Yêu cầu Phần mềm (Software Requirements Specification — SRS) này mô tả **đầy đủ, chi tiết và có thể kiểm thử được** toàn bộ yêu cầu chức năng và phi chức năng của **Hệ thống Tích hợp HIS-RIS-PACS** dành cho hệ thống bệnh viện đa cơ sở quy mô 1.000 giường, 5 cơ sở tại Việt Nam.

Tài liệu phục vụ các mục đích:

| STT | Mục đích | Đối tượng |
|-----|----------|-----------|
| 1 | Cơ sở thống nhất yêu cầu giữa các bên | Tất cả stakeholders |
| 2 | Đầu vào cho thiết kế kiến trúc hệ thống | Kiến trúc sư, Tech Lead |
| 3 | Đầu vào cho phát triển phần mềm | Đội phát triển (Dev) |
| 4 | Cơ sở xây dựng test plan và test case | Đội kiểm thử (QA) |
| 5 | Cơ sở đánh giá nghiệm thu sản phẩm | PM, Ban giám đốc BV |
| 6 | Tài liệu pháp lý kèm hợp đồng triển khai | Pháp chế, Mua sắm |

### A.1.2. Phạm vi tài liệu

Tài liệu bao gồm:

- **Hệ thống HIS (Hospital Information System)**: 16+ phân hệ bao phủ toàn bộ quy trình khám chữa bệnh, quản trị bệnh viện
- **Hệ thống RIS/PACS (Radiology Information System / Picture Archiving and Communication System)**: 8+ phân hệ quản lý chẩn đoán hình ảnh
- **Tầng tích hợp**: Giao thức liên thông HIS↔RIS/PACS, hệ thống bên ngoài (BHXH, LIS, ký số)
- **Yêu cầu phi chức năng**: Hiệu năng, bảo mật, tuân thủ, sẵn sàng cao, giám sát
- **Kế hoạch triển khai**: Release theo giai đoạn, migration, đào tạo
- **Phân tích rủi ro**: Kỹ thuật, vận hành, pháp lý

### A.1.3. Đối tượng đọc

| Đối tượng | Vai trò | Phần liên quan |
|-----------|---------|----------------|
| Ban giám đốc bệnh viện | Phê duyệt phạm vi, ngân sách | A, B, H, I |
| Trưởng khoa lâm sàng | Xác nhận yêu cầu nghiệp vụ | C1, C3, C4 |
| Trưởng khoa CĐHA | Xác nhận yêu cầu RIS/PACS | C2, C3, C4 |
| Project Manager | Quản lý phạm vi, tiến độ | Tất cả |
| Business Analyst | Phân tích & xác minh yêu cầu | C1-C5, D, E |
| Solution Architect | Thiết kế kiến trúc | B, D, E, F |
| Development Team | Phát triển phần mềm | C1-C5, D, E, F, J |
| QA Team | Kiểm thử | C3, C4, C5, F, J |
| DevOps / Infra Team | Hạ tầng, triển khai | F, G |
| Đội BHYT bệnh viện | Nghiệp vụ bảo hiểm | C1 (module BHYT), E |
| Đơn vị ký số | Tích hợp chữ ký điện tử | C2 (ký kết quả), E |

---

## A.2. Định nghĩa thuật ngữ và viết tắt

### A.2.1. Thuật ngữ hệ thống

| Thuật ngữ | Viết tắt | Định nghĩa |
|-----------|----------|------------|
| Hospital Information System | HIS | Hệ thống thông tin bệnh viện — quản lý toàn bộ quy trình khám chữa bệnh, hành chính, tài chính |
| Radiology Information System | RIS | Hệ thống thông tin phóng xạ — quản lý quy trình chẩn đoán hình ảnh (chỉ định, worklist, kết quả) |
| Picture Archiving and Communication System | PACS | Hệ thống lưu trữ và truyền tải hình ảnh y khoa — lưu trữ, truy xuất, hiển thị ảnh DICOM |
| Electronic Medical Record | EMR | Bệnh án điện tử — phiên bản số hóa của hồ sơ bệnh án giấy |
| Laboratory Information System | LIS | Hệ thống thông tin xét nghiệm |
| Clinical Decision Support System | CDSS | Hệ thống hỗ trợ ra quyết định lâm sàng |

### A.2.2. Thuật ngữ tiêu chuẩn y tế

| Thuật ngữ | Viết tắt | Định nghĩa |
|-----------|----------|------------|
| Digital Imaging and Communications in Medicine | DICOM | Chuẩn quốc tế về truyền thông và lưu trữ hình ảnh y khoa (NEMA PS3.x) |
| Health Level Seven | HL7 | Bộ tiêu chuẩn quốc tế về trao đổi dữ liệu y tế điện tử |
| Fast Healthcare Interoperability Resources | FHIR | Chuẩn trao đổi dữ liệu y tế thế hệ mới của HL7 (RESTful) |
| International Classification of Diseases, 10th Revision | ICD-10 | Phân loại quốc tế bệnh tật phiên bản 10 (WHO) |
| Integrating the Healthcare Enterprise | IHE | Sáng kiến cải thiện tích hợp hệ thống y tế thông qua các Integration Profile |
| Scheduled Workflow | SWF | IHE profile cho quy trình chẩn đoán hình ảnh có lịch |
| Patient Information Reconciliation | PIR | IHE profile cho đối chiếu thông tin bệnh nhân |
| Cross-Enterprise Document Sharing for Imaging | XDS-I | IHE profile chia sẻ tài liệu hình ảnh liên cơ sở |

### A.2.3. Thuật ngữ kỹ thuật DICOM

| Thuật ngữ | Viết tắt | Định nghĩa |
|-----------|----------|------------|
| Application Entity Title | AE Title | Định danh duy nhất của một node trong mạng DICOM |
| Service Class Provider | SCP | Bên cung cấp dịch vụ DICOM (ví dụ: PACS server nhận ảnh) |
| Service Class User | SCU | Bên sử dụng dịch vụ DICOM (ví dụ: modality gửi ảnh) |
| Modality Worklist | MWL | Danh sách công việc DICOM cung cấp cho thiết bị chụp |
| Modality Performed Procedure Step | MPPS | Thông báo trạng thái thực hiện quy trình chụp từ modality |
| C-STORE | — | Dịch vụ DICOM lưu trữ (gửi ảnh từ modality vào PACS) |
| C-FIND | — | Dịch vụ DICOM truy vấn (tìm kiếm study, series) |
| C-MOVE | — | Dịch vụ DICOM di chuyển ảnh giữa các node |
| Web Access to DICOM Objects | WADO / WADO-RS | Truy cập ảnh DICOM qua giao thức HTTP/REST |
| DICOMweb | — | Bộ RESTful services: STOW-RS, WADO-RS, QIDO-RS |
| Grayscale Softcopy Presentation State | GSPS | Trạng thái hiển thị (W/L, annotations) lưu dạng DICOM |
| Structured Report | SR | Báo cáo có cấu trúc theo chuẩn DICOM |
| SOP Class | — | Định nghĩa loại đối tượng DICOM (ví dụ: CT Image Storage) |
| Transfer Syntax | — | Cách mã hóa dữ liệu DICOM khi truyền (Implicit/Explicit VR, compression) |

### A.2.4. Thuật ngữ HL7

| Thuật ngữ | Viết tắt | Định nghĩa |
|-----------|----------|------------|
| Admit-Discharge-Transfer | ADT | Loại message HL7 về tiếp nhận, xuất viện, chuyển khoa |
| Order Message | ORM | Message HL7 truyền chỉ định (order) |
| Observation Result | ORU | Message HL7 truyền kết quả xét nghiệm/CĐHA |
| Minimal Lower Layer Protocol | MLLP | Giao thức truyền tải HL7 v2.x qua TCP |
| Acknowledgment | ACK | Message xác nhận đã nhận thành công |

### A.2.5. Thuật ngữ nghiệp vụ bệnh viện Việt Nam

| Thuật ngữ | Viết tắt | Định nghĩa |
|-----------|----------|------------|
| Bảo hiểm Y tế | BHYT | Bảo hiểm y tế theo Luật BHYT Việt Nam |
| Bảo hiểm Xã hội | BHXH | Cơ quan quản lý bảo hiểm xã hội |
| Chẩn đoán hình ảnh | CĐHA | Các phương pháp chẩn đoán dùng hình ảnh (X-quang, CT, MRI, siêu âm) |
| Cận lâm sàng | CLS | Xét nghiệm, CĐHA, thăm dò chức năng |
| Khám chữa bệnh | KCB | Hoạt động khám và điều trị bệnh |
| Viện phí | — | Chi phí khám chữa bệnh tại bệnh viện |
| Đúng tuyến | — | Bệnh nhân BHYT khám tại cơ sở đăng ký ban đầu |
| Trái tuyến | — | Bệnh nhân BHYT khám tại cơ sở khác đăng ký ban đầu |
| Thông tuyến | — | Chính sách cho phép KCB BHYT trái tuyến (huyện/tỉnh) |
| First Expired First Out | FEFO | Nguyên tắc xuất thuốc: hết hạn trước xuất trước |
| Antimicrobial Stewardship | AMS | Chương trình quản lý sử dụng kháng sinh hợp lý |
| Turnaround Time | TAT | Thời gian từ nhận chỉ định đến trả kết quả |
| Recovery Point Objective | RPO | Mốc thời gian chấp nhận mất dữ liệu tối đa |
| Recovery Time Objective | RTO | Thời gian phục hồi hệ thống tối đa chấp nhận |
| Role-Based Access Control | RBAC | Phân quyền dựa trên vai trò |
| Attribute-Based Access Control | ABAC | Phân quyền dựa trên thuộc tính |
| Multi-Factor Authentication | MFA | Xác thực đa yếu tố |

---

## A.3. Tài liệu tham chiếu và tiêu chuẩn áp dụng

### A.3.1. Tiêu chuẩn quốc tế

| Mã | Tiêu chuẩn | Phạm vi áp dụng |
|----|-----------|-----------------|
| STD-001 | IEEE 830-1998 — Recommended Practice for Software Requirements Specifications | Cấu trúc tài liệu SRS |
| STD-002 | ISO/IEC/IEEE 29148:2018 — Systems and software engineering — Life cycle processes — Requirements engineering | Quy trình yêu cầu |
| STD-003 | DICOM 3.0 (NEMA PS3.1-PS3.22) | Truyền thông, lưu trữ hình ảnh y khoa |
| STD-004 | HL7 v2.4 / v2.5.1 | Trao đổi dữ liệu y tế (ADT, ORM, ORU) |
| STD-005 | HL7 FHIR R4 | API RESTful trao đổi dữ liệu y tế |
| STD-006 | ICD-10-CM / ICD-10 VN | Mã hóa chẩn đoán bệnh |
| STD-007 | IHE Technical Framework — Radiology | Profile tích hợp CĐHA (SWF, PIR, XDS-I) |
| STD-008 | IHE IT Infrastructure — PIX/PDQ | Quản lý Patient Identifier |
| STD-009 | ISO 27001:2022 | Quản lý an toàn thông tin |
| STD-010 | ISO 27799:2016 | An toàn thông tin trong y tế |

### A.3.2. Quy định pháp luật Việt Nam

| Mã | Văn bản | Nội dung liên quan |
|----|---------|-------------------|
| VN-001 | Luật Khám bệnh, chữa bệnh số 15/2023/QH15 | Quy định về hồ sơ bệnh án điện tử, trách nhiệm cơ sở KCB |
| VN-002 | Luật Bảo hiểm Y tế (sửa đổi 2024) | Quyền lợi BHYT, thông tuyến, đồng chi trả |
| VN-003 | Thông tư 46/2018/TT-BYT | Quy định hồ sơ bệnh án điện tử |
| VN-004 | Thông tư 54/2017/TT-BYT | Bộ tiêu chí ứng dụng CNTT tại cơ sở KCB |
| VN-005 | Thông tư 56/2017/TT-BYT | Quy định chi tiết Luật BHYT về KCB |
| VN-006 | Quyết định 4210/QĐ-BYT | Tiêu chuẩn kết nối liên thông dữ liệu y tế |
| VN-007 | Nghị định 13/2023/NĐ-CP | Bảo vệ dữ liệu cá nhân |
| VN-008 | Luật An toàn thông tin mạng 2015 | Bảo mật hệ thống thông tin |
| VN-009 | Luật Giao dịch điện tử 2023 | Chữ ký số, chứng thư điện tử |
| VN-010 | Thông tư 30/2018/TT-BYT | Danh mục thuốc BHYT |
| VN-011 | Quy chế kê đơn thuốc (Thông tư 52/2017/TT-BYT) | Quy định kê đơn điện tử |
| VN-012 | Chuẩn XML 4210 — BHXH Việt Nam | Format dữ liệu giám định BHYT |

### A.3.3. Tài liệu dự án nội bộ

| Mã | Tài liệu | Trạng thái |
|----|----------|-----------|
| DOC-001 | Dàn ý SRS v1.0 (README.md gốc) | Đã hoàn thành |
| DOC-002 | Biên bản khảo sát nghiệp vụ bệnh viện | Cần thu thập |
| DOC-003 | Sơ đồ hạ tầng mạng hiện tại | Cần thu thập |
| DOC-004 | Danh sách thiết bị CĐHA hiện có | Cần thu thập |
| DOC-005 | Bảng giá dịch vụ y tế hiện hành | Cần thu thập |
| DOC-006 | Danh mục thuốc bệnh viện | Cần thu thập |

---

## A.4. Tổng quan cấu trúc tài liệu

Bộ SRS được tổ chức thành **10 phần (A → J)**, mỗi phần là một file markdown riêng biệt:

```
Phần A ─── Tổng quan tài liệu (file này)
  │
Phần B ─── Mô tả hệ thống tổng thể
  │         ├── Bối cảnh nghiệp vụ
  │         ├── Kiến trúc mức cao
  │         ├── Actors & vai trò
  │         └── Giả định & ràng buộc
  │
Phần C ─── Yêu cầu chức năng chi tiết
  │         ├── C1: HIS (16+ module)
  │         ├── C2: RIS/PACS (8+ module)
  │         ├── C3: 10+ Use Cases
  │         ├── C4: 3 luồng End-to-End
  │         └── C5: Ma trận truy vết
  │
Phần D ─── Yêu cầu dữ liệu
  │         ├── Đối tượng dữ liệu chính
  │         ├── Mô hình khái niệm
  │         ├── Chất lượng & đồng bộ
  │         └── Chiến lược mã định danh
  │
Phần E ─── Yêu cầu tích hợp
  │         ├── Danh mục hệ thống tích hợp
  │         ├── Giao thức & cơ chế
  │         ├── HL7/FHIR mapping
  │         └── Xử lý lỗi & đối soát
  │
Phần F ─── Yêu cầu phi chức năng
  │         ├── Hiệu năng & SLA
  │         ├── Bảo mật
  │         ├── Tuân thủ pháp lý
  │         ├── HA & DR
  │         └── Giám sát vận hành
  │
Phần G ─── Triển khai & Vận hành
  │         ├── Môi trường DEV/UAT/PROD
  │         ├── Migration dữ liệu
  │         ├── Cutover & Đào tạo
  │         └── Hỗ trợ sau go-live
  │
Phần H ─── Kế hoạch release 3 giai đoạn
  │
Phần I ─── Rủi ro & Biện pháp giảm thiểu
  │
Phần J ─── Phụ lục (API, báo cáo, UAT)
```

### Quy ước đọc

- Mỗi yêu cầu có **mã ID duy nhất** (ví dụ: `FR-HIS-REG-001`)
- Mức ưu tiên theo **MoSCoW**: Must / Should / Could / Won't
- **Tiêu chí chấp nhận** được ghi rõ cho yêu cầu quan trọng
- Bảng được sử dụng cho tất cả danh sách có cấu trúc
- Giả định được đánh dấu rõ ràng bằng `[GIẢ ĐỊNH]`

---

*Tiếp theo: [Phần B — Mô tả Hệ thống Tổng thể](./B-MO-TA-HE-THONG-TONG-THE.md)*
