# BỘ TÀI LIỆU ĐẶC TẢ YÊU CẦU PHẦN MỀM (SRS)
# HỆ THỐNG TÍCH HỢP HIS-RIS-PACS

**Phiên bản**: 2.0  
**Ngày cập nhật**: 25/03/2026  
**Trạng thái**: SRS Chi tiết (Detailed SRS)  
**Chuẩn áp dụng**: IEEE 830-1998, ISO/IEC/IEEE 29148:2018

---

## THÔNG TIN DỰ ÁN

| Hạng mục | Giá trị |
|----------|---------|
| Quy mô bệnh viện | 1.000 giường bệnh |
| Số cơ sở | 5 cơ sở |
| Lượt khám ngoại trú/ngày | 300 lượt |
| Năng lực CĐHA/ngày | 200 ca |
| Quốc gia | Việt Nam |
| Kiến trúc triển khai | On-premises |
| Tích hợp HIS cũ | Mức ưu tiên thấp |

---

## LỊCH SỬ THAY ĐỔI TÀI LIỆU

| Phiên bản | Ngày | Người thực hiện | Mô tả thay đổi |
|-----------|------|-----------------|-----------------|
| 1.0 | 24/03/2026 | [Tên] | Tạo dàn ý chi tiết ban đầu |
| 2.0 | 25/03/2026 | [Tên] | Mở rộng SRS đầy đủ, tách file theo phần A-J |

---

## CẤU TRÚC TÀI LIỆU

| Phần | File | Mô tả |
|------|------|-------|
| **A** | [A-TONG-QUAN-TAI-LIEU.md](./A-TONG-QUAN-TAI-LIEU.md) | Mục đích, phạm vi, đối tượng đọc, thuật ngữ, tài liệu tham chiếu |
| **B** | [B-MO-TA-HE-THONG-TONG-THE.md](./B-MO-TA-HE-THONG-TONG-THE.md) | Bối cảnh nghiệp vụ, kiến trúc mức cao, actors, giả định & ràng buộc |
| **C1** | [C1-YEU-CAU-CHUC-NANG-HIS.md](./C1-YEU-CAU-CHUC-NANG-HIS.md) | Yêu cầu chức năng chi tiết — Phân hệ HIS (tất cả module) |
| **C2** | [C2-YEU-CAU-CHUC-NANG-RIS-PACS.md](./C2-YEU-CAU-CHUC-NANG-RIS-PACS.md) | Yêu cầu chức năng chi tiết — Phân hệ RIS/PACS |
| **C3** | [C3-USE-CASES.md](./C3-USE-CASES.md) | 10+ Use cases quan trọng nhất (chi tiết đầy đủ) |
| **C4** | [C4-LUONG-END-TO-END.md](./C4-LUONG-END-TO-END.md) | 3 luồng nghiệp vụ end-to-end bắt buộc |
| **C5** | [C5-MA-TRAN-TRUY-VET.md](./C5-MA-TRAN-TRUY-VET.md) | Ma trận truy vết: yêu cầu nghiệp vụ → chức năng → API → kiểm thử |
| **D** | [D-YEU-CAU-DU-LIEU.md](./D-YEU-CAU-DU-LIEU.md) | Đối tượng dữ liệu, mô hình khái niệm, chất lượng dữ liệu, đồng bộ |
| **E** | [E-YEU-CAU-TICH-HOP.md](./E-YEU-CAU-TICH-HOP.md) | Tích hợp LIS, BHXH, ký số, HL7/FHIR mapping, xử lý lỗi |
| **F** | [F-YEU-CAU-PHI-CHUC-NANG.md](./F-YEU-CAU-PHI-CHUC-NANG.md) | Hiệu năng, bảo mật, tuân thủ, HA/DR, giám sát |
| **G** | [G-TRIEN-KHAI-VAN-HANH.md](./G-TRIEN-KHAI-VAN-HANH.md) | Môi trường, migration, cutover, đào tạo, hỗ trợ |
| **H** | [H-KE-HOACH-RELEASE.md](./H-KE-HOACH-RELEASE.md) | 3 giai đoạn release, out of scope từng giai đoạn |
| **I** | [I-RUI-RO.md](./I-RUI-RO.md) | Rủi ro kỹ thuật, vận hành, pháp lý, tích hợp + biện pháp |
| **J** | [J-PHU-LUC.md](./J-PHU-LUC.md) | API dự kiến, báo cáo chuẩn, danh sách UAT |

---

## CÁCH ĐỌC TÀI LIỆU

1. **Ban giám đốc / PM**: Đọc Phần A, B, H, I
2. **Business Analyst**: Đọc toàn bộ, đặc biệt C1-C5, D, E
3. **Developer / Architect**: Đọc B, C1-C5, D, E, F, J
4. **QA / Tester**: Đọc C3, C4, C5, F, J (phần UAT)
5. **DevOps / Infra**: Đọc F, G
6. **Pháp chế / Compliance**: Đọc A (tham chiếu), F (tuân thủ), I (rủi ro pháp lý)

---

## QUY ƯỚC MÃ YÊU CẦU

| Tiền tố | Ý nghĩa | Ví dụ |
|---------|---------|-------|
| `FR-HIS-xxx` | Yêu cầu chức năng HIS | FR-HIS-REG-001 |
| `FR-RIS-xxx` | Yêu cầu chức năng RIS/PACS | FR-RIS-WL-001 |
| `FR-INT-xxx` | Yêu cầu tích hợp | FR-INT-HL7-001 |
| `NFR-xxx` | Yêu cầu phi chức năng | NFR-PERF-001 |
| `UC-xxx` | Use Case | UC-001 |
| `DR-xxx` | Yêu cầu dữ liệu | DR-001 |
| `BR-xxx` | Yêu cầu nghiệp vụ | BR-001 |
| `TC-xxx` | Test Case | TC-REG-001 |
