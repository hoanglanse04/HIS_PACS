# PHẦN B — MÔ TẢ HỆ THỐNG TỔNG THỂ

> **Mã phần**: SRS-PART-B  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## B.1. Bối cảnh nghiệp vụ bệnh viện

### B.1.1. Mô hình tổ chức

Hệ thống phục vụ mô hình **bệnh viện đa cơ sở** với các đặc điểm:

| Đặc điểm | Giá trị |
|-----------|---------|
| Tổng quy mô giường | 1.000 giường |
| Số cơ sở | 5 (1 trung tâm + 4 vệ tinh) |
| Lượt khám ngoại trú/ngày (tổng) | 300 lượt |
| Năng lực CĐHA/ngày (tổng) | 200 ca |
| Số khoa lâm sàng dự kiến | 20-30 khoa |
| Số phòng khám ngoại trú | 15-25 phòng |
| Số thiết bị CĐHA | 15-25 máy (CT, MRI, DR, CR, US, Mammo, Endo) |
| Đối tượng bệnh nhân | BHYT (~70%), Dịch vụ (~25%), Miễn phí/Khác (~5%) |

### B.1.2. Mô hình cơ sở

```
┌─────────────────────────────────────────────────────────────────┐
│                     CƠ SỞ TRUNG TÂM (Flagship)                 │
│  ┌─────────┐ ┌──────────┐ ┌────────┐ ┌──────┐ ┌─────────────┐ │
│  │ Ngoại   │ │ Nội trú  │ │ Cấp    │ │ CĐHA │ │ Datacenter  │ │
│  │ trú     │ │ (600 G)  │ │ cứu    │ │ (đủ) │ │ (HIS+PACS)  │ │
│  └─────────┘ └──────────┘ └────────┘ └──────┘ └─────────────┘ │
└─────────────────────────────────────────────────────────────────┘
         │ WAN/VPN              │ WAN/VPN
┌────────┴────────┐    ┌───────┴─────────┐
│  CƠ SỞ VỆ TINH 1│    │  CƠ SỞ VỆ TINH 2│
│  (100 G, NTr+NT) │    │  (100 G, NTr+NT) │
└─────────────────┘    └─────────────────┘
         │ WAN/VPN              │ WAN/VPN
┌────────┴────────┐    ┌───────┴─────────┐
│  CƠ SỞ VỆ TINH 3│    │  CƠ SỞ VỆ TINH 4│
│  (100 G, NTr)    │    │  (100 G, NTr)    │
└─────────────────┘    └─────────────────┘
```

**`[GIẢ ĐỊNH]`** Cơ sở trung tâm đặt datacenter chính. Các cơ sở vệ tinh kết nối qua WAN/VPN, sử dụng chung hệ thống HIS-RIS-PACS tập trung.

### B.1.3. Quy trình nghiệp vụ tổng quan

```
BỆNH NHÂN ĐẾN VIỆN
     │
     ├──→ [CẤP CỨU] ──→ Phân loại ──→ Xử trí cấp cứu ──→ Nhập viện/Về
     │
     └──→ [ĐĂNG KÝ] ──→ Quét thẻ BHYT ──→ Lấy số ──→ Chờ khám
              │
              ▼
         [KHÁM BỆNH] ──→ Khám LS ──→ Chẩn đoán sơ bộ
              │
              ├──→ [KÊ ĐƠN THUỐC] ──→ Dược phát thuốc
              ├──→ [CHỈ ĐỊNH CLS] ──→ Xét nghiệm (LIS) / CĐHA (RIS-PACS)
              ├──→ [CHỈ ĐỊNH THỦ THUẬT]
              └──→ [NHẬP VIỆN NỘI TRÚ]
                       │
                       ▼
                  [ĐIỀU TRỊ NỘI TRÚ]
                       │
                       ├──→ Y lệnh hàng ngày (thuốc, CLS, thủ thuật)
                       ├──→ Phẫu thuật
                       ├──→ Hội chẩn
                       └──→ [RA VIỆN] ──→ Thanh toán ──→ Quyết toán BHYT
                                │
              ┌─────────────────┘
              ▼
         [VIỆN PHÍ/BHYT]
              │
              ├──→ Tính phí tự động
              ├──→ Thu phí / Thanh toán
              ├──→ Xuất XML 4210 → Cổng BHXH
              └──→ Đối soát BHYT
```

---

## B.2. Kiến trúc nghiệp vụ HIS-RIS-PACS mức cao

### B.2.1. Sơ đồ kiến trúc tổng quan

```
┌─══════════════════════════════════════════════════════════════════════════┐
║                        TẦNG TRÌNH DIỆN (Presentation Layer)              ║
║  ┌──────────────┐  ┌─────────────┐  ┌──────────┐  ┌──────────────────┐  ║
║  │ Web App HIS  │  │ PACS Viewer │  │ Dashboard│  │ Mobile/Tablet    │  ║
║  │ (Browser)    │  │(Zero-footer)│  │ Quản trị │  │ (Bác sĩ buồng)  │  ║
║  └──────┬───────┘  └──────┬──────┘  └────┬─────┘  └───────┬──────────┘  ║
╚═════════╪═════════════════╪══════════════╪═════════════════╪═════════════╝
          │ HTTPS           │ HTTPS/WSS    │ HTTPS           │ HTTPS
┌─══════════════════════════════════════════════════════════════════════════┐
║                        TẦNG API GATEWAY                                  ║
║  ┌────────────────────────────────────────────────────────────────────┐  ║
║  │  API Gateway — Authentication, Rate Limiting, Routing, Logging     │  ║
║  └────────────────────────────────────────────────────────────────────┘  ║
╚═══════════════════════╤══════════════════════════════════════════════════╝
                        │ REST / gRPC
┌─══════════════════════════════════════════════════════════════════════════┐
║                        TẦNG NGHIỆP VỤ (Business Logic Layer)            ║
║                                                                          ║
║  ┌─── HIS Services ───────────────────────────────────────────────────┐  ║
║  │ Đăng ký │ Khám bệnh │ Nội trú │ Cấp cứu │ Dược │ Viện phí │ BHYT│  ║
║  │ Phẫu thuật │ EMR │ Nhân sự │ Tài sản │ Kế hoạch │ Báo cáo      │  ║
║  └────────────────────────────────────────────────────────────────────┘  ║
║                                                                          ║
║  ┌─── RIS/PACS Services ─────────────────────────────────────────────┐  ║
║  │ Order Mgr │ Worklist │ Archive │ Viewer │ Reporting │ Sync       │  ║
║  └────────────────────────────────────────────────────────────────────┘  ║
║                                                                          ║
║  ┌─── Integration Services ──────────────────────────────────────────┐  ║
║  │ HL7 Engine │ DICOM Router │ FHIR Gateway │ BHXH Connector        │  ║
║  └────────────────────────────────────────────────────────────────────┘  ║
╚═══════════════════════╤══════════════════════════════════════════════════╝
                        │
┌─══════════════════════════════════════════════════════════════════════════┐
║                        TẦNG DỮ LIỆU (Data Layer)                        ║
║  ┌──────────┐  ┌──────────────┐  ┌──────────┐  ┌──────────────────┐    ║
║  │ RDBMS    │  │ DICOM Store  │  │ Message  │  │ Cache / Search   │    ║
║  │(Postgres)│  │ (File/Object)│  │ Queue    │  │ (Redis/Elastic)  │    ║
║  └──────────┘  └──────────────┘  └──────────┘  └──────────────────┘    ║
╚══════════════════════════════════════════════════════════════════════════╝
```

### B.2.2. Nguyên tắc kiến trúc

| STT | Nguyên tắc | Mô tả |
|-----|-----------|-------|
| 1 | **Modular Monolith / Service-Oriented** | Các module tách biệt logic nhưng triển khai linh hoạt (monolith hoặc microservice) |
| 2 | **Single Source of Truth** | HIS là master cho dữ liệu bệnh nhân, chỉ định; PACS là master cho ảnh DICOM |
| 3 | **Loose Coupling** | HIS và RIS/PACS giao tiếp qua HL7/FHIR message, không truy cập trực tiếp DB lẫn nhau |
| 4 | **Standards-Based Integration** | Chỉ dùng giao thức chuẩn (HL7, DICOM, FHIR) cho tích hợp liên hệ thống |
| 5 | **Security by Design** | Mã hóa, phân quyền, audit trail tích hợp sẵn từ thiết kế |
| 6 | **Multi-tenant Ready** | Hỗ trợ nhiều cơ sở trên cùng hệ thống, phân tách dữ liệu theo site |
| 7 | **Offline Tolerance** | Cơ sở vệ tinh có thể hoạt động cơ bản khi mất kết nối WAN `[GIẢ ĐỊNH]` |

---

## B.3. Sơ đồ ngữ cảnh tích hợp (System Context)

### B.3.1. Mô tả ngữ cảnh

```
                    ┌─────────────────┐
                    │  Bộ Y tế        │
                    │  (Báo cáo 4210) │
                    └────────┬────────┘
                             │ XML/API
                             ▼
┌─────────────┐    ┌─────────────────────────────┐    ┌──────────────┐
│ Cổng BHXH   │◄──►│                             │◄──►│ Nhà cung cấp │
│ (Giám định) │    │                             │    │ thuốc/VTYT   │
└─────────────┘    │    HỆ THỐNG HIS-RIS-PACS    │    └──────────────┘
                   │        (Phạm vi SRS)        │
┌─────────────┐    │                             │    ┌──────────────┐
│ Hệ thống    │◄──►│                             │◄──►│ SMS/Email    │
│ ký số (CA)  │    │                             │    │ Gateway      │
└─────────────┘    └──────────┬──────────────────┘    └──────────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
     ┌──────────────┐ ┌─────────────┐ ┌─────────────┐
     │ DICOM        │ │ LIS (Xét    │ │ Hóa đơn     │
     │ Modalities   │ │ nghiệm)    │ │ điện tử     │
     │ CT/MR/XR/US  │ │ [Tương lai] │ │ [Tương lai] │
     └──────────────┘ └─────────────┘ └─────────────┘
```

### B.3.2. Danh sách hệ thống bên ngoài

| STT | Hệ thống | Giao thức | Hướng | Mức ưu tiên |
|-----|----------|-----------|-------|-------------|
| 1 | Cổng giám định BHXH | HTTPS/XML 4210 | Hai chiều | **Must** |
| 2 | DICOM Modalities (CT, MRI, DR, US...) | DICOM 3.0 | Hai chiều | **Must** |
| 3 | Hệ thống ký số (CA) | REST API / PKCS#11 | HIS/PACS → CA | **Must** |
| 4 | LIS (Hệ thống xét nghiệm) | HL7 v2.x / REST | Hai chiều | **Should** (GĐ2) |
| 5 | SMS Gateway | REST API | Một chiều (outbound) | **Should** |
| 6 | Email Server | SMTP | Một chiều (outbound) | **Should** |
| 7 | Hóa đơn điện tử | REST API | Một chiều (outbound) | **Should** (GĐ2) |
| 8 | Cổng báo cáo Bộ Y tế | HTTPS/XML | Một chiều (outbound) | **Must** |
| 9 | HIS cũ (nếu migration) | DB-to-DB / ETL | Một chiều (import) | **Could** |

---

## B.4. Danh sách Actor và Vai trò chi tiết

### B.4.1. Actor chính (Primary Actors)

| Mã | Actor | Mô tả | Trình độ CNTT | Module chính | Số lượng ước tính |
|----|-------|-------|---------------|-------------|-------------------|
| ACT-01 | Nhân viên tiếp đón | Đăng ký BN, quét thẻ BHYT, phát số, thu phí tạm | Cơ bản | Đăng ký, Viện phí | 15-20 người |
| ACT-02 | Bác sĩ khám ngoại trú | Khám bệnh, chẩn đoán, kê đơn, chỉ định CLS | Trung bình | Khám bệnh, EMR | 30-50 người |
| ACT-03 | Bác sĩ điều trị nội trú | Y lệnh, theo dõi, tổng kết bệnh án | Trung bình | Nội trú, EMR | 40-60 người |
| ACT-04 | Bác sĩ cấp cứu | Tiếp nhận cấp cứu, xử trí ban đầu, chuyển nội trú | Trung bình | Cấp cứu, EMR | 10-15 người |
| ACT-05 | Bác sĩ CĐHA (Radiologist) | Đọc ảnh DICOM, viết kết quả, duyệt báo cáo | Cao | PACS Viewer, RIS | 5-10 người |
| ACT-06 | Kỹ thuật viên CĐHA | Nhận BN, chụp ảnh, gửi ảnh lên PACS | Trung bình | Worklist, Modality | 10-15 người |
| ACT-07 | Điều dưỡng | Thực hiện y lệnh, chăm sóc, ghi nhận sinh hiệu | Cơ bản-TB | Nội trú | 100-150 người |
| ACT-08 | Dược sĩ | Phát thuốc, kiểm tra tương tác, quản lý kho | Trung bình | Dược | 15-20 người |
| ACT-09 | Nhân viên viện phí | Tính phí, thu tiền, quyết toán, BHYT | Cơ bản | Viện phí, BHYT | 10-15 người |
| ACT-10 | Bác sĩ phẫu thuật | Lên lịch mổ, ghi phiếu phẫu thuật | Trung bình | Phẫu thuật | 15-25 người |
| ACT-11 | Nhân viên nhà thuốc | Bán thuốc lẻ, quản lý kho nhà thuốc | Cơ bản | Nhà thuốc | 5-8 người |
| ACT-12 | Nhân viên kế hoạch | Tổng hợp số liệu, lập kế hoạch | Trung bình | Kế hoạch, Báo cáo | 3-5 người |
| ACT-13 | Nhân viên nhân sự | Quản lý hồ sơ nhân viên, lịch trực | Trung bình | Nhân sự | 3-5 người |
| ACT-14 | Ban giám đốc | Xem dashboard, phê duyệt báo cáo | Cơ bản | Dashboard, Báo cáo | 3-5 người |

### B.4.2. Actor hỗ trợ (Supporting Actors)

| Mã | Actor | Mô tả | Module chính |
|----|-------|-------|-------------|
| ACT-S01 | Quản trị hệ thống (SysAdmin) | Cấu hình, phân quyền, quản lý hệ thống | Admin toàn hệ thống |
| ACT-S02 | Quản trị PACS (PACS Admin) | Cấu hình DICOM, quản lý AE Title, storage | RIS/PACS Admin |
| ACT-S03 | Trưởng khoa | Duyệt, phê duyệt, thống kê khoa | Duyệt + Báo cáo khoa |
| ACT-S04 | Nhân viên CNTT | Hỗ trợ kỹ thuật, xử lý sự cố | Monitor, Troubleshoot |

### B.4.3. Actor hệ thống (System Actors)

| Mã | Actor | Mô tả | Giao thức |
|----|-------|-------|-----------|
| ACT-SYS-01 | Cổng BHXH | Hệ thống giám định BHXH | HTTPS/XML |
| ACT-SYS-02 | DICOM Modality | Thiết bị chụp CĐHA | DICOM 3.0 |
| ACT-SYS-03 | LIS | Hệ thống xét nghiệm | HL7 v2.x |
| ACT-SYS-04 | CA Server | Dịch vụ ký số | REST API |
| ACT-SYS-05 | SMS/Email Gateway | Dịch vụ thông báo | REST API / SMTP |

---

## B.5. Giả định và Ràng buộc hệ thống

### B.5.1. Giả định (Assumptions)

| Mã | Giả định | Tác động nếu sai |
|----|---------|-------------------|
| AS-001 | Hạ tầng mạng LAN tại mỗi cơ sở có tốc độ ≥ 1Gbps, WAN giữa các cơ sở ≥ 100Mbps | Cần đầu tư nâng cấp mạng, ảnh hưởng timeline GĐ1 |
| AS-002 | Tất cả thiết bị CĐHA hiện có hỗ trợ DICOM 3.0 (MWL, C-STORE) | Máy cũ không DICOM cần giải pháp bridge hoặc thay thế |
| AS-003 | Bệnh viện có kết nối Internet ổn định (≥ 20Mbps) cho liên thông BHXH | Cần giải pháp offline/queue cho gửi BHXH |
| AS-004 | Người dùng được đào tạo tối thiểu 8 giờ trước go-live | Rủi ro sai sót cao nếu không đào tạo đủ |
| AS-005 | Dữ liệu danh mục (ICD-10, thuốc, dịch vụ, giá) được bệnh viện cung cấp đầy đủ | Chậm triển khai nếu thiếu danh mục |
| AS-006 | Bảng giá dịch vụ và quy tắc BHYT tuân theo quy định hiện hành tại thời điểm triển khai | Cần cập nhật khi quy định thay đổi |
| AS-007 | Mỗi bệnh nhân có CMND/CCCD hoặc mã BHYT làm mã định danh duy nhất | Cần giải pháp matching cho BN không có giấy tờ |
| AS-008 | Cơ sở trung tâm có phòng server đạt chuẩn (UPS, điều hòa, PCCC) | Cần đầu tư nếu chưa có |
| AS-009 | Datacenter đặt tại cơ sở trung tâm, các cơ sở vệ tinh kết nối tập trung | Thay đổi kiến trúc nếu cần distributed |
| AS-010 | Ưu tiên tích hợp HIS cũ ở mức thấp — migration dữ liệu lịch sử là tùy chọn | Nếu cần full migration, tăng scope GĐ1 đáng kể |

### B.5.2. Ràng buộc (Constraints)

| Mã | Ràng buộc | Loại | Mô tả |
|----|----------|------|-------|
| CON-001 | Tuân thủ Thông tư 46/2018/TT-BYT | Pháp lý | Bệnh án điện tử phải đáp ứng mẫu Bộ Y tế |
| CON-002 | Tuân thủ Thông tư 54/2017/TT-BYT | Pháp lý | Bộ tiêu chí CNTT tại cơ sở KCB |
| CON-003 | Xuất dữ liệu XML 4210 cho BHXH | Pháp lý | Format và API theo quy định BHXH hiện hành |
| CON-004 | DICOM 3.0 compliance | Kỹ thuật | Bắt buộc cho mọi giao tiếp CĐHA |
| CON-005 | HL7 v2.4+ cho tích hợp HIS↔PACS | Kỹ thuật | Chuẩn message ADT, ORM, ORU |
| CON-006 | On-premises deployment | Hạ tầng | Toàn bộ hệ thống triển khai tại datacenter bệnh viện |
| CON-007 | Tiếng Việt, Unicode UTF-8 | Kỹ thuật | Giao diện, dữ liệu, báo cáo hoàn toàn tiếng Việt |
| CON-008 | Bảo mật thông tin BN theo NĐ 13/2023 | Pháp lý | Dữ liệu cá nhân phải được bảo vệ theo quy định |
| CON-009 | ICD-10 VN cho mã chẩn đoán | Nghiệp vụ | Sử dụng ICD-10 phiên bản Việt Nam hóa |
| CON-010 | Uptime ≥ 99.5% HIS, ≥ 99.9% PACS | Vận hành | SLA cam kết với bệnh viện |
| CON-011 | Thời gian phản hồi ≤ 3 giây cho HIS | Hiệu năng | Đáp ứng thao tác thường xuyên |
| CON-012 | Lưu trữ DICOM ≥ 10 năm | Pháp lý | Theo quy định lưu trữ hồ sơ y khoa |

### B.5.3. Phụ thuộc (Dependencies)

| Mã | Phụ thuộc | Bên chịu trách nhiệm | Rủi ro |
|----|----------|----------------------|--------|
| DEP-001 | Hạ tầng mạng LAN/WAN sẵn sàng | Bệnh viện / Đơn vị hạ tầng | Cao |
| DEP-002 | API cổng BHXH ổn định, có tài liệu | BHXH Việt Nam | Trung bình |
| DEP-003 | Thiết bị CĐHA có DICOM conformance | Nhà cung cấp thiết bị | Trung bình |
| DEP-004 | Dịch vụ ký số (CA) có API tích hợp | Nhà cung cấp CA | Thấp |
| DEP-005 | Danh mục dữ liệu ban đầu từ BV | Bệnh viện | Cao |
| DEP-006 | Nhân sự bệnh viện tham gia UAT | Bệnh viện | Cao |

---

## B.6. Tổng quan phân hệ chức năng

### B.6.1. Phân hệ HIS

| STT | Phân hệ | Mã module | Mô tả tóm tắt | Ưu tiên |
|-----|---------|-----------|----------------|---------|
| 1 | Hệ thống (System) | HIS-SYS | Quản trị người dùng, phân quyền, cấu hình hệ thống, danh mục dùng chung | Must |
| 2 | Cấp cứu (Emergency) | HIS-ER | Tiếp nhận cấp cứu, phân loại, xử trí ban đầu, chuyển nội trú/về | Must |
| 3 | Đăng ký (Registration) | HIS-REG | Đăng ký BN mới, quét BHYT, phát số, đăng ký khám | Must |
| 4 | Khám bệnh (Outpatient) | HIS-OPD | Khám ngoại trú, chẩn đoán, kê đơn, chỉ định CLS | Must |
| 5 | Cận lâm sàng (Paraclinical) | HIS-CLS | Quản lý chỉ định CLS, theo dõi trạng thái, nhận kết quả | Must |
| 6 | Điều trị nội trú (Inpatient) | HIS-IPD | Quản lý giường, bệnh án, y lệnh, diễn biến | Must |
| 7 | Điều trị ngoại trú (Outpatient Treatment) | HIS-OPT | Điều trị ngoại trú dài ngày (hóa trị, thận nhân tạo) | Should |
| 8 | Bệnh án điện tử (EMR) | HIS-EMR | Bệnh án theo mẫu BYT, phiếu theo dõi, phiếu chăm sóc | Must |
| 9 | Phẫu thuật - Thủ thuật (Surgery) | HIS-SUR | Lên lịch mổ, phiếu PT/TT, gây mê, hồi sức | Must |
| 10 | Dược (Pharmacy) | HIS-PHA | Danh mục thuốc, kho, phát thuốc NTr/NT, thuốc đặc biệt | Must |
| 11 | Thanh toán viện phí (Billing) | HIS-BIL | Bảng giá, tính phí, thu phí, quyết toán | Must |
| 12 | Nhà thuốc (Retail Pharmacy) | HIS-RPH | Bán lẻ thuốc, quản lý kho nhà thuốc | Should |
| 13 | Kế hoạch - Tổng hợp (Planning) | HIS-PLN | Kế hoạch giường, nhân lực, vật tư, tổng hợp số liệu | Should |
| 14 | Báo cáo quản trị (Reports) | HIS-RPT | Dashboard, báo cáo BYT, báo cáo quản trị, KPI | Must |
| 15 | Nhân sự (HR) | HIS-HR | Hồ sơ nhân viên, lịch trực, chấm công, chứng chỉ | Should |
| 16 | Tài sản - Thiết bị (Assets) | HIS-AST | Quản lý tài sản, thiết bị y tế, bảo trì, kiểm kê | Should |
| 17 | BHYT/Bảo hiểm (Insurance) | HIS-INS | Giám định BHYT, chính sách, XML 4210, đối soát | Must |
| 18 | Module khác (Others) | HIS-OTH | Dinh dưỡng, KSNK, quản lý chất lượng | Could |
| 19 | ToolCode | HIS-TLC | Công cụ phát triển, cấu hình form/report, workflow engine | Should |
| 20 | Trợ giúp (Help) | HIS-HLP | Hướng dẫn sử dụng, FAQ, chat hỗ trợ, training material | Should |

### B.6.2. Phân hệ RIS/PACS

| STT | Phân hệ | Mã module | Mô tả tóm tắt | Ưu tiên |
|-----|---------|-----------|----------------|---------|
| 1 | Quản trị RIS/PACS | RIS-ADM | Người dùng, phân quyền, khu vực, đơn vị, danh mục BV | Must |
| 2 | Quản lý bệnh nhân CĐHA | RIS-PAT | Thông tin BN CĐHA, đồng bộ từ HIS, gộp/tách BN | Must |
| 3 | Quản lý máy chụp (Modality) | RIS-MOD | Cấu hình AE Title, transfer syntax, auto-routing | Must |
| 4 | Worklist | RIS-WL | DICOM MWL provider, danh sách chờ chụp, phân phòng | Must |
| 5 | Quản lý Study/Series/Image | RIS-STD | Lưu trữ, truy vấn, quản lý lifecycle ảnh DICOM | Must |
| 6 | DICOM Viewer | RIS-VWR | Viewer chẩn đoán (web zero-footprint + advanced tools) | Must |
| 7 | Đọc/Duyệt/Ký/Trả kết quả | RIS-RPT | Viết kết quả, duyệt, ký số, gửi ORU về HIS | Must |
| 8 | Đồng bộ HIS hai chiều | RIS-SYN | HL7 ADT/ORM/ORU, reconciliation, master data sync | Must |

---

*Tiếp theo: [Phần C1 — Yêu cầu Chức năng HIS](./C1-YEU-CAU-CHUC-NANG-HIS.md)*
