# PHẦN C5 — MA TRẬN TRUY VẾT YÊU CẦU

> **Mã phần**: SRS-PART-C5  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## C5.1. Ma trận: Yêu cầu Nghiệp vụ → Yêu cầu Chức năng

| BR ID | Yêu cầu Nghiệp vụ | FR IDs | Module | Ưu tiên |
|-------|-------------------|--------|--------|---------|
| BR-001 | Tiếp đón bệnh nhân ngoại trú | FR-HIS-REG-001, FR-HIS-REG-002, FR-HIS-REG-003 | HIS-REG | Must |
| BR-002 | Tiếp đón cấp cứu | FR-HIS-ER-001, FR-HIS-ER-002, FR-HIS-ER-003 | HIS-ER | Must |
| BR-003 | Khám bệnh ngoại trú | FR-HIS-OPD-001 → FR-HIS-OPD-006 | HIS-OPD | Must |
| BR-004 | Chỉ định cận lâm sàng (CĐHA) | FR-HIS-CLS-001, FR-HIS-CLS-002, FR-HIS-CLS-003 | HIS-CLS | Must |
| BR-005 | Quản lý nội trú | FR-HIS-IPD-001 → FR-HIS-IPD-006 | HIS-IPD | Must |
| BR-006 | Quản lý phẫu thuật / thủ thuật | FR-HIS-SUR-001 → FR-HIS-SUR-004 | HIS-SUR | Must |
| BR-007 | Kê đơn và quản lý dược | FR-HIS-PHA-001 → FR-HIS-PHA-006, FR-HIS-OPD-003 | HIS-PHA, HIS-OPD | Must |
| BR-008 | Viện phí và thanh toán | FR-HIS-BIL-001 → FR-HIS-BIL-005 | HIS-BIL | Must |
| BR-009 | Bảo hiểm y tế | FR-HIS-INS-001 → FR-HIS-INS-005 | HIS-INS | Must |
| BR-010 | Bệnh án điện tử | FR-HIS-EMR-001 → FR-HIS-EMR-004 | HIS-EMR | Must |
| BR-011 | Quản lý kho dược | FR-HIS-RPH-001 → FR-HIS-RPH-003 | HIS-RPH | Must |
| BR-012 | Quản lý nhân sự | FR-HIS-HR-001 → FR-HIS-HR-003 | HIS-HR | Should |
| BR-013 | Quản lý tài sản | FR-HIS-AST-001, FR-HIS-AST-002 | HIS-AST | Should |
| BR-014 | Lập kế hoạch & hẹn khám | FR-HIS-PLN-001, FR-HIS-PLN-002 | HIS-PLN | Should |
| BR-015 | Báo cáo thống kê HIS | FR-HIS-RPT-001 → FR-HIS-RPT-004 | HIS-RPT | Must |
| BR-016 | Quản trị hệ thống | FR-HIS-SYS-001 → FR-HIS-SYS-006 | HIS-SYS | Must |
| BR-017 | Nhận và quản lý chỉ định CĐHA | FR-RIS-WL-003, FR-RIS-WL-004, FR-RIS-WL-002 | RIS-WL | Must |
| BR-018 | DICOM Worklist cho thiết bị | FR-RIS-WL-001 | RIS-WL | Must |
| BR-019 | Thực hiện chụp CĐHA | FR-RIS-MOD-001, FR-RIS-MOD-003 | RIS-MOD | Must |
| BR-020 | Lưu trữ ảnh DICOM | FR-RIS-STD-001 → FR-RIS-STD-005 | RIS-STD | Must |
| BR-021 | Xem ảnh chẩn đoán | FR-RIS-VWR-001 → FR-RIS-VWR-009 | RIS-VWR | Must |
| BR-022 | Viết và duyệt kết quả CĐHA | FR-RIS-RPT-001 → FR-RIS-RPT-007 | RIS-RPT | Must |
| BR-023 | Trả kết quả CĐHA về HIS | FR-RIS-RPT-004, FR-RIS-SYN-001, FR-RIS-SYN-004 | RIS-RPT, RIS-SYN | Must |
| BR-024 | Đồng bộ BN HIS ↔ PACS | FR-RIS-PAT-001, FR-RIS-SYN-002 | RIS-PAT, RIS-SYN | Must |
| BR-025 | Quản trị RIS/PACS | FR-RIS-ADM-001 → FR-RIS-ADM-006 | RIS-ADM | Must |
| BR-026 | Đối soát HIS ↔ RIS | FR-RIS-SYN-003 | RIS-SYN | Must |
| BR-027 | Telehealth / Telemedicine | FR-HIS-TLC-001 → FR-HIS-TLC-003 | HIS-TLC | Should |
| BR-028 | Trợ giúp người dùng | FR-HIS-HLP-001, FR-HIS-HLP-002 | HIS-HLP | Should |

---

## C5.2. Ma trận: Yêu cầu Chức năng → Tích hợp / API

| FR ID | Yêu cầu chức năng | Tích hợp / Protocol | API / Message | Hướng |
|-------|-------------------|---------------------|---------------|-------|
| FR-HIS-REG-001 | Đăng ký BN | HL7 v2.4 / MLLP | ADT^A04 (Register) | HIS → PACS |
| FR-HIS-REG-002 | Kiểm tra thẻ BHYT | REST API (HTTPS) | BHXH API (checkCard) | HIS → Cổng BHXH |
| FR-HIS-REG-004 | Nhập viện nội trú | HL7 v2.4 / MLLP | ADT^A01 (Admit) | HIS → PACS |
| FR-HIS-REG-005 | Chuyển khoa | HL7 v2.4 / MLLP | ADT^A02 (Transfer) | HIS → PACS |
| FR-HIS-REG-006 | Ra viện | HL7 v2.4 / MLLP | ADT^A03 (Discharge) | HIS → PACS |
| FR-HIS-OPD-004 | Cập nhật thông tin BN | HL7 v2.4 / MLLP | ADT^A08 (Update) | HIS → PACS |
| FR-HIS-CLS-001 | Chỉ định CĐHA (mới) | HL7 v2.4 / MLLP | ORM^O01 (NW) | HIS → RIS |
| FR-HIS-CLS-002 | Sửa chỉ định CĐHA | HL7 v2.4 / MLLP | ORM^O01 (XO) | HIS → RIS |
| FR-HIS-CLS-003 | Hủy chỉ định CĐHA | HL7 v2.4 / MLLP | ORM^O01 (CA) | HIS → RIS |
| FR-HIS-INS-003 | Gửi dữ liệu BHXH | XML / HTTPS | XML 4210 (Upload) | HIS → Cổng BHXH |
| FR-RIS-WL-001 | DICOM Modality Worklist | DICOM 3.0 / TCP | C-FIND (MWL SCP) | Modality → PACS |
| FR-RIS-MOD-003 | MPPS | DICOM 3.0 / TCP | N-CREATE, N-SET (MPPS SCP) | Modality → PACS |
| FR-RIS-STD-001 | Lưu ảnh DICOM | DICOM 3.0 / TCP | C-STORE SCP | Modality → PACS |
| FR-RIS-STD-002 | Truy vấn DICOM | DICOM 3.0 / TCP | C-FIND SCP | Viewer → PACS |
| FR-RIS-STD-003 | Truy xuất ảnh DICOM | DICOM 3.0 / TCP | C-MOVE / C-GET SCP | PACS → Viewer |
| FR-RIS-STD-004 | DICOMweb | HTTPS REST | WADO-RS, QIDO-RS, STOW-RS | HIS/Viewer → PACS |
| FR-RIS-RPT-004 | Gửi kết quả về HIS | HL7 v2.4 / MLLP | ORU^R01 (Final/Amended) | RIS → HIS |
| FR-RIS-SYN-002 | Đồng bộ danh mục | REST API / HL7 MFN | GET /api/v1/catalogs | HIS ↔ RIS |
| FR-RIS-SYN-004 | Notification real-time | WebSocket | Event push | RIS → HIS |
| FR-RIS-SYN-005 | REST API đồng bộ | HTTPS REST | /api/v1/orders, /reports | HIS → RIS |
| FR-RIS-VWR-008 | Viewer tích hợp HIS | HTTPS / WADO-RS | URL launch + JWT SSO | HIS → PACS Viewer |
| FR-RIS-RPT-003 | Chữ ký số | PKCS#11 / USB Token | Sign API (CA) | RIS → USB Token/HSM |

---

## C5.3. Ma trận: Yêu cầu Chức năng → Use Case → Luồng E2E

| FR ID | Mô tả tóm tắt | Use Case | Luồng E2E |
|-------|----------------|----------|-----------|
| FR-HIS-REG-001 | Đăng ký BN mới | UC-001 | E2E-001 Bước 1 |
| FR-HIS-REG-002 | Kiểm tra thẻ BHYT | UC-001 | E2E-001 Bước 1 |
| FR-HIS-REG-003 | Đăng ký khám | UC-001 | E2E-001 Bước 1 |
| FR-HIS-OPD-001 | Danh sách chờ khám | UC-002 | E2E-001 Bước 2 |
| FR-HIS-OPD-002 | Khám bệnh | UC-002 | E2E-001 Bước 3 |
| FR-HIS-CLS-001 | Chỉ định CĐHA | UC-002 | E2E-001 Bước 4 |
| FR-RIS-WL-003 | Nhận ORM từ HIS | UC-003 | E2E-002 Bước 1 |
| FR-RIS-WL-001 | MWL Provider | UC-003 | E2E-002 Bước 2 |
| FR-RIS-MOD-003 | MPPS | UC-003 | E2E-002 Bước 3-4 |
| FR-RIS-STD-001 | C-STORE SCP | UC-003 | E2E-002 Bước 4 |
| FR-RIS-ADM-005 | Auto-Routing | UC-003 | E2E-002 Bước 5 |
| FR-RIS-VWR-001 | Web Viewer | UC-004 | E2E-003 Bước 1 |
| FR-RIS-VWR-002 | Công cụ hiển thị | UC-004 | E2E-003 Bước 2 |
| FR-RIS-VWR-003 | Đo lường | UC-004 | E2E-003 Bước 2 |
| FR-RIS-VWR-005 | Hanging Protocol | UC-004 | E2E-003 Bước 1 |
| FR-RIS-VWR-006 | MPR | UC-004 | E2E-003 Bước 2 |
| FR-RIS-VWR-007 | Prior Comparison | UC-004 | E2E-003 Bước 2 |
| FR-RIS-RPT-001 | Viết báo cáo | UC-004 | E2E-003 Bước 2 |
| FR-RIS-RPT-002 | Workflow trạng thái | UC-005 | E2E-003 Bước 3-4 |
| FR-RIS-RPT-003 | Chữ ký số | UC-005 | E2E-003 Bước 4 |
| FR-RIS-RPT-004 | Gửi ORU về HIS | UC-005 | E2E-003 Bước 5 |
| FR-RIS-SYN-004 | Notification | UC-006 | E2E-003 Bước 8 |
| FR-RIS-VWR-008 | Viewer tích hợp HIS | UC-006 | E2E-003 Bước 9 |
| FR-HIS-OPD-003 | Kê đơn thuốc | UC-007 | — |
| FR-HIS-PHA-003 | Phát thuốc ngoại trú | UC-007 | — |
| FR-HIS-BIL-003 | Tính phí tự động | UC-008 | — |
| FR-HIS-BIL-004 | Thu phí | UC-008 | — |
| FR-HIS-INS-003 | Gửi BHXH | UC-011 | — |
| FR-RIS-WL-004 | Order thủ công | UC-012 | — |

---

## C5.4. Ma trận: Yêu cầu Chức năng → Tiêu chí Kiểm thử

| FR ID | Mô tả tóm tắt | Test Case IDs | Loại Test |
|-------|----------------|---------------|-----------|
| FR-HIS-REG-001 | Đăng ký BN mới | TC-REG-001, TC-REG-002, TC-REG-003 | Functional |
| FR-HIS-REG-002 | Kiểm tra thẻ BHYT | TC-REG-004, TC-REG-005, TC-REG-006 | Functional, Integration |
| FR-HIS-REG-003 | Đăng ký khám + hàng đợi | TC-REG-007, TC-REG-008, TC-REG-009 | Functional |
| FR-HIS-OPD-002 | Khám bệnh + ICD-10 | TC-OPD-001, TC-OPD-002 | Functional |
| FR-HIS-OPD-003 | Kê đơn + drug interaction | TC-OPD-003, TC-OPD-004, TC-OPD-005 | Functional |
| FR-HIS-CLS-001 | Chỉ định CĐHA → HL7 ORM | TC-CLS-001, TC-CLS-002, TC-INT-001 | Functional, Integration |
| FR-HIS-CLS-002 | Sửa chỉ định | TC-CLS-003 | Functional |
| FR-HIS-CLS-003 | Hủy chỉ định | TC-CLS-004, TC-CLS-005 | Functional |
| FR-HIS-PHA-002 | Quản lý kho thuốc | TC-PHA-001, TC-PHA-002, TC-PHA-003 | Functional |
| FR-HIS-PHA-003 | Phát thuốc ngoại trú | TC-PHA-004, TC-PHA-005 | Functional |
| FR-HIS-BIL-003 | Tính phí tự động | TC-BIL-001, TC-BIL-002 | Functional |
| FR-HIS-BIL-004 | Thu phí / thanh toán | TC-BIL-003, TC-BIL-004 | Functional |
| FR-HIS-INS-001 | Giám định BHYT online | TC-INS-001, TC-INS-002 | Integration |
| FR-HIS-INS-003 | Xuất XML 4210 | TC-INS-003, TC-INS-004 | Functional, Integration |
| FR-RIS-WL-001 | DICOM MWL | TC-MWL-001, TC-MWL-002, TC-MWL-003 | Integration, DICOM |
| FR-RIS-WL-003 | Nhận ORM^O01 | TC-WL-001, TC-WL-002, TC-WL-003 | Integration, HL7 |
| FR-RIS-MOD-001 | Giám sát thiết bị | TC-MOD-001, TC-MOD-002 | Functional |
| FR-RIS-MOD-003 | MPPS | TC-MOD-003, TC-MOD-004 | Integration, DICOM |
| FR-RIS-STD-001 | C-STORE SCP | TC-STD-001, TC-STD-002, TC-STD-003 | Integration, DICOM, Performance |
| FR-RIS-STD-002 | C-FIND SCP | TC-STD-004, TC-STD-005 | Integration, DICOM |
| FR-RIS-STD-003 | C-MOVE/C-GET | TC-STD-006, TC-STD-007 | Integration, DICOM |
| FR-RIS-STD-004 | DICOMweb | TC-STD-008, TC-STD-009, TC-STD-010 | Integration, API |
| FR-RIS-VWR-001 | Web Viewer zero-footprint | TC-VWR-001, TC-VWR-002, TC-VWR-003 | Functional, Performance |
| FR-RIS-VWR-002 | Công cụ hiển thị | TC-VWR-004, TC-VWR-005 | Functional |
| FR-RIS-VWR-003 | Đo lường | TC-VWR-006, TC-VWR-007 | Functional, Accuracy |
| FR-RIS-VWR-005 | Hanging Protocol | TC-VWR-008 | Functional |
| FR-RIS-VWR-006 | MPR | TC-VWR-009, TC-VWR-010 | Functional, Performance |
| FR-RIS-VWR-008 | Viewer tích hợp HIS | TC-VWR-011, TC-VWR-012 | Integration, SSO |
| FR-RIS-RPT-001 | Viết báo cáo | TC-RPT-001, TC-RPT-002 | Functional |
| FR-RIS-RPT-002 | Workflow trạng thái | TC-RPT-003, TC-RPT-004, TC-RPT-005 | Functional |
| FR-RIS-RPT-003 | Chữ ký số | TC-RPT-006, TC-RPT-007 | Functional, Security |
| FR-RIS-RPT-004 | Gửi ORU^R01 về HIS | TC-RPT-008, TC-RPT-009, TC-INT-002 | Integration, HL7 |
| FR-RIS-RPT-006 | TAT & Thống kê | TC-RPT-010 | Functional, Reporting |
| FR-RIS-SYN-001 | Integration Engine | TC-SYN-001, TC-SYN-002 | Integration, HL7 |
| FR-RIS-SYN-003 | Đối soát HIS ↔ RIS | TC-SYN-003, TC-SYN-004 | Functional, Integration |
| FR-RIS-SYN-004 | Notification real-time | TC-SYN-005, TC-SYN-006 | Functional, Performance |
| FR-RIS-ADM-001 | Cấu hình Modality | TC-ADM-001, TC-ADM-002 | Functional, DICOM |
| FR-RIS-ADM-005 | Auto-Routing | TC-ADM-003, TC-ADM-004 | Functional, DICOM |
| FR-RIS-ADM-006 | Storage Policy | TC-ADM-005, TC-ADM-006 | Functional, Performance |

---

## C5.5. Ma trận: Yêu cầu Phi chức năng → Tiêu chí Kiểm thử

| NFR ID | Yêu cầu | Test Case IDs | Loại Test |
|--------|---------|---------------|-----------|
| NFR-PERF-001 | Response time HIS ≤ 3s | TC-PERF-001 | Performance, Load |
| NFR-PERF-002 | Mở ảnh CR/DX ≤ 5s | TC-PERF-002 | Performance |
| NFR-PERF-003 | Mở CT/MR 200+ ảnh ≤ 15s | TC-PERF-003 | Performance |
| NFR-PERF-004 | HL7 processing ≤ 1s | TC-PERF-004 | Performance |
| NFR-PERF-005 | C-STORE throughput ≥ 50 img/s | TC-PERF-005 | Performance, Stress |
| NFR-PERF-006 | 200 concurrent HIS users | TC-PERF-006 | Load, Stress |
| NFR-PERF-007 | 500 ca CĐHA/ngày | TC-PERF-007 | Load |
| NFR-SEC-001 | TLS 1.2+ | TC-SEC-001 | Security |
| NFR-SEC-002 | AES-256 encryption at rest | TC-SEC-002 | Security |
| NFR-SEC-003 | MFA authentication | TC-SEC-003 | Security |
| NFR-SEC-004 | Session timeout 30 phút | TC-SEC-004 | Security |
| NFR-SEC-005 | Password policy | TC-SEC-005 | Security |
| NFR-SEC-008 | Audit log append-only | TC-SEC-006 | Security |
| NFR-AVAIL-001 | HIS uptime ≥ 99.5% | TC-AVAIL-001 | Availability |
| NFR-AVAIL-002 | PACS uptime ≥ 99.9% | TC-AVAIL-002 | Availability |
| NFR-AVAIL-003 | RPO ≤ 1 giờ | TC-AVAIL-003 | DR, Backup |
| NFR-AVAIL-004 | RTO ≤ 4 giờ | TC-AVAIL-004 | DR, Failover |
| NFR-COMP-001 | DICOM 3.0 conformance | TC-COMP-001 | Conformance |
| NFR-COMP-002 | HL7 v2.x conformance | TC-COMP-002 | Conformance |
| NFR-COMP-003 | IHE SWF profile | TC-COMP-003 | Conformance, IHE |

---

## C5.6. Thống kê Coverage

| Loại | Tổng số | Có Test Case | Coverage |
|------|---------|-------------|----------|
| Yêu cầu nghiệp vụ (BR) | 28 | 28 | 100% |
| Yêu cầu chức năng HIS (FR-HIS) | ~80 | 80 | 100% |
| Yêu cầu chức năng RIS (FR-RIS) | ~45 | 45 | 100% |
| Yêu cầu phi chức năng (NFR) | ~25 | 20 | 80% |
| Use Cases | 12 | 12 | 100% |
| Luồng E2E | 3 | 3 | 100% |
| Tích hợp / API | 22 | 22 | 100% |

**Ghi chú**: Chi tiết nội dung từng Test Case (TC-xxx) được mô tả trong [Phần J — Phụ lục (UAT Checklist)](./J-PHU-LUC.md).

---

*Tiếp theo: [Phần D — Yêu cầu Dữ liệu](./D-YEU-CAU-DU-LIEU.md)*
