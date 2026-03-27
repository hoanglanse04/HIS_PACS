# PHẦN I — PHÂN TÍCH RỦI RO

> **Mã phần**: SRS-PART-I  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: RSK-{CATEGORY}-{STT}

---

## I.1. Phương pháp Đánh giá Rủi ro

### I.1.1. Thang đo Xác suất

| Mức | Nhãn | Mô tả | Tần suất ước lượng |
|-----|-------|-------|--------------------|
| 3 | **Cao** | Rất có khả năng xảy ra | > 60% trong vòng đời dự án |
| 2 | **Trung bình (TB)** | Có thể xảy ra | 20-60% |
| 1 | **Thấp** | Ít khả năng xảy ra | < 20% |

### I.1.2. Thang đo Tác động

| Mức | Nhãn | Mô tả |
|-----|-------|-------|
| 3 | **Cao** | Ảnh hưởng nghiêm trọng: trì hoãn go-live > 1 tháng, mất dữ liệu, vi phạm pháp luật, nguy hiểm bệnh nhân |
| 2 | **Trung bình (TB)** | Ảnh hưởng đáng kể: trì hoãn 1-4 tuần, giảm hiệu năng, tăng chi phí 10-25% |
| 1 | **Thấp** | Ảnh hưởng hạn chế: trì hoãn < 1 tuần, bất tiện nhưng có workaround |

### I.1.3. Ma trận Xác suất × Tác động

```
            │    Tác động     │
            │ Thấp │  TB  │ Cao │
  ──────────┼──────┼──────┼─────┤
  Cao (XS)  │  TB  │ CAO  │ CỰC │
  ──────────┼──────┼──────┼─────┤
  TB  (XS)  │ THẤP │  TB  │ CAO │
  ──────────┼──────┼──────┼─────┤
  Thấp (XS) │ THẤP │ THẤP │  TB │
  ──────────┴──────┴──────┴─────┘

  Ký hiệu mức rủi ro:
    CỰC (Critical) = XS:Cao × TĐ:Cao → Hành động ngay, escalate lên Steering Committee
    CAO (High)     = Điểm 5-6         → Lập kế hoạch giảm thiểu, review hàng tuần
    TB  (Medium)   = Điểm 3-4         → Theo dõi, review hàng tháng
    THẤP (Low)     = Điểm 1-2         → Ghi nhận, review hàng quý
```

### I.1.4. Công thức Điểm rủi ro

> **Risk Score** = Xác suất × Tác động (thang 1-9)

| Điểm | Mức rủi ro | Hành động |
|------|-----------|-----------|
| 7-9 | 🔴 CỰC (Critical) | Escalate ngay, yêu cầu quyết định Steering Committee |
| 5-6 | 🟠 CAO (High) | Plan mitigation trước Sprint tiếp theo |
| 3-4 | 🟡 TB (Medium) | Monitor hàng tháng, mitigation nếu có nguồn lực |
| 1-2 | 🟢 THẤP (Low) | Accept, review hàng quý |

---

## I.2. Rủi ro Kỹ thuật (Technical)

### RSK-TECH-001: Hiệu năng DICOM Viewer không đạt SLA

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Web viewer (zero-footprint) không đạt TTFI ≤ 5s cho CR/DX, ≤ 15s cho CT/MR 200+ ảnh (NFR-PERF-002, NFR-PERF-003) do WebGL rendering overhead, kích thước ảnh lớn, hoặc tải mạng cao |
| **Xác suất** | Cao (3) |
| **Tác động** | Cao (3) — Bác sĩ CĐHA phải chờ đợi, giảm throughput đọc ảnh, ảnh hưởng TAT |
| **Điểm rủi ro** | 🔴 9 — CỰC |
| **NFR liên quan** | NFR-PERF-002, NFR-PERF-003, NFR-PERF-007 |
| **FR liên quan** | FR-RIS-VWR-001, FR-RIS-VWR-002 |
| **Biện pháp giảm thiểu** | (1) Progressive loading: hiển thị thumbnail → load full resolution on demand; (2) JPEG2000 streaming (JPIP) hoặc HTJ2K cho lossy preview → lossless on zoom; (3) Server-side rendering (WSI) cho dataset > 500 ảnh; (4) CDN nội bộ/caching layer trước DICOM server; (5) Prefetch prior studies ngoài giờ cao điểm |
| **Kế hoạch dự phòng** | Deploy thick client viewer (Horos/RadiAnt) song song tại phòng đọc CĐHA trong khi tối ưu web viewer |
| **Chủ sở hữu** | Tech Lead PACS |
| **Giai đoạn** | GĐ1 — phát hiện sớm tại SIT (M6-M8) |

### RSK-TECH-002: Dung lượng lưu trữ DICOM tăng nhanh hơn dự kiến

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Dung lượng DICOM vượt ước tính ~11.35 TB/năm (xem D.7.3) do tăng ca chụp, modalities mới (3D Mammo, PET/CT), hoặc không compress hiệu quả |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Tăng chi phí hạ tầng, có thể ảnh hưởng hiệu năng archive nếu hết dung lượng HOT tier |
| **Điểm rủi ro** | 🟡 4 — TB |
| **NFR liên quan** | NFR-SCAL-002, NFR-AVAIL-005 |
| **FR liên quan** | FR-RIS-STD-002 (tiered storage), DR-007 (DICOM storage policy) |
| **Biện pháp giảm thiểu** | (1) Tiered storage policy: HOT → WARM → COLD tự động theo tuổi study; (2) JPEG2000 lossless compression (~2:1 ratio); (3) Giám sát dung lượng với alert ở 70%, 85%, 95%; (4) Budget reserve 30% cho storage expansion năm 2+ |
| **Kế hoạch dự phòng** | Bổ sung NAS node hoặc kết nối Object Storage (MinIO) trong 2-4 tuần procurement |
| **Chủ sở hữu** | Infra Lead |
| **Giai đoạn** | GĐ1+ (liên tục) |

### RSK-TECH-003: Deadlock / Performance degradation Database HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PostgreSQL deadlock hoặc slow query khi 200+ user đồng thời thao tác trên bảng Visit, Order, Prescription trong giờ cao điểm |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Cao (3) — HIS response > 3s (vi phạm NFR-PERF-001), nhân viên không thể tiếp đón BN |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-PERF-001, NFR-PERF-006, NFR-PERF-008 |
| **FR liên quan** | FR-HIS-REG-001 → FR-HIS-REG-006, FR-HIS-OPD-001 → FR-HIS-OPD-004 |
| **Biện pháp giảm thiểu** | (1) Query optimization + indexing strategy review trước SIT; (2) Connection pooling (PgBouncer); (3) Read replicas cho report queries; (4) Partitioning bảng Visit theo tháng; (5) Redis caching cho danh mục tĩnh (thuốc, ICD-10, dịch vụ) |
| **Kế hoạch dự phòng** | Vertical scaling DB (tăng RAM/CPU) + emergency index tuning, TAT < 4 giờ |
| **Chủ sở hữu** | DBA / Backend Lead |
| **Giai đoạn** | GĐ1 — load test tại SIT (M7-M8) |

### RSK-TECH-004: Single Point of Failure hạ tầng On-premises

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Sự cố phần cứng (server, switch core, SAN controller) gây downtime toàn hệ thống, vi phạm SLA uptime HIS 99.5% / PACS 99.9% |
| **Xác suất** | Thấp (1) |
| **Tác động** | Cao (3) — Toàn bệnh viện ngưng hoạt động CNTT, ảnh hưởng chăm sóc bệnh nhân |
| **Điểm rủi ro** | 🟡 3 — TB |
| **NFR liên quan** | NFR-AVAIL-001, NFR-AVAIL-002, NFR-AVAIL-003, NFR-AVAIL-004 |
| **Biện pháp giảm thiểu** | (1) HA architecture: dual app server (active-active), DB primary+standby, PACS dual-node (xem F.3); (2) Dual power supply + UPS + generator; (3) Dual network path (core switch stack); (4) RAID 10 cho tất cả storage; (5) Annual hardware health check |
| **Kế hoạch dự phòng** | DR site (warm standby) với RTO ≤ 4h HIS, ≤ 2h PACS; spare hardware on-site cho component swap < 2h |
| **Chủ sở hữu** | Infra Lead / IT BV |
| **Giai đoạn** | GĐ1 — thiết kế HA tại M1-M3 |

### RSK-TECH-005: Chọn sai technology stack

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Framework/library core (DICOM server, HL7 engine, web viewer) không đáp ứng yêu cầu hoặc bị discontinue, phải thay thế giữa dự án |
| **Xác suất** | Thấp (1) |
| **Tác động** | Cao (3) — Rework lớn, trì hoãn go-live 2-3 tháng |
| **Điểm rủi ro** | 🟡 3 — TB |
| **NFR liên quan** | NFR-MAIN-002, NFR-COMP-001, NFR-COMP-002 |
| **Biện pháp giảm thiểu** | (1) PoC (Proof of Concept) 4-6 tuần cho DICOM server + HL7 engine + viewer trước khi commit; (2) Ưu tiên thư viện OSS mature (Orthanc, dcm4chee, OHIF, Cornerstone.js); (3) Abstraction layer cho DICOM/HL7 — cho phép swap implementation; (4) Đánh giá community activity + release cadence |
| **Kế hoạch dự phòng** | Backup vendor list cho mỗi component (primary + alternative) |
| **Chủ sở hữu** | Solution Architect |
| **Giai đoạn** | GĐ1 — quyết định tại M1-M2 |

### RSK-TECH-006: Bảo mật dữ liệu y tế bị xâm phạm

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Lỗ hổng bảo mật dẫn đến truy cập trái phép hồ sơ bệnh nhân, ảnh DICOM, hoặc dữ liệu BHYT — vi phạm pháp luật VN về bảo mật thông tin y tế |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Cao (3) — Vi phạm pháp luật, mất uy tín, có thể bị xử phạt hành chính hoặc hình sự |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-SEC-001 → NFR-SEC-008 |
| **FR liên quan** | FR-HIS-SYS-003 (audit trail), FR-RIS-ADM-003 (DICOM access log) |
| **Biện pháp giảm thiểu** | (1) TLS 1.2+ cho toàn bộ traffic; (2) AES-256 encryption at rest cho PII + PHI; (3) RBAC + MFA cho admin/bác sĩ; (4) Penetration test tại SIT và trước mỗi go-live; (5) WAF (Web Application Firewall); (6) Audit log append-only, tamper-proof; (7) Tuân thủ OWASP Top 10 |
| **Kế hoạch dự phòng** | Incident Response Plan: detect → contain → eradicate → recover → post-mortem, TAT phát hiện ≤ 1h |
| **Chủ sở hữu** | Security Lead |
| **Giai đoạn** | GĐ1-GĐ3 (liên tục) |

---

## I.3. Rủi ro Tích hợp (Integration)

### RSK-INT-001: Tích hợp HL7 HIS↔PACS không ổn định

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Message loss, duplicate, hoặc parsing error trong luồng HL7 ORM/ORU giữa HIS và PACS — chỉ định CĐHA không đến PACS hoặc kết quả không về HIS |
| **Xác suất** | Cao (3) |
| **Tác động** | Cao (3) — Bệnh nhân không được chụp đúng, bác sĩ không nhận kết quả, ảnh hưởng an toàn BN |
| **Điểm rủi ro** | 🔴 9 — CỰC |
| **FR liên quan** | FR-INT-HL7-001 → FR-INT-HL7-004, FR-INT-ERR-001 |
| **Kênh tích hợp** | INT-CH-001 (ORM), INT-CH-002 (ORU), INT-CH-003 (ADT) — xem E.11 |
| **Biện pháp giảm thiểu** | (1) Message persistence: queue (RabbitMQ) trước khi xử lý — guaranteed delivery; (2) ACK/NAK mechanism — retry on NAK; (3) Idempotency key (Message Control ID) — deduplicate; (4) Reconciliation job: đối soát HIS orders vs PACS orders mỗi 15 phút; (5) Alert nếu message unprocessed > 5 phút; (6) Integration test suite: 50+ HL7 message scenarios |
| **Kế hoạch dự phòng** | Manual fallback: KTV nhập thủ công trên PACS worklist khi tích hợp gián đoạn; HIS hiển thị banner "Tích hợp PACS đang gián đoạn" |
| **Chủ sở hữu** | Integration Lead |
| **Giai đoạn** | GĐ1 — SIT (M6-M9) là giai đoạn critical |

### RSK-INT-002: DICOM Modality không tương thích MWL/C-STORE

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thiết bị CĐHA cũ (CR, Ultrasound analog) không hỗ trợ MWL hoặc C-STORE đúng chuẩn, transfer syntax không tương thích |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Phải nhập worklist thủ công, scan film qua CR digitizer, tăng workload KTV |
| **Điểm rủi ro** | 🟡 4 — TB |
| **FR liên quan** | FR-RIS-WL-001, FR-RIS-STD-001, FR-INT-DCM-001 |
| **Biện pháp giảm thiểu** | (1) Khảo sát DICOM conformance toàn bộ modalities trước M3; (2) DICOM gateway/broker cho thiết bị legacy; (3) Danh sách transfer syntax hỗ trợ rộng (Implicit VR LE, Explicit VR LE/BE, JPEG, JPEG2000); (4) Workaround: manual worklist entry cho thiết bị không MWL |
| **Kế hoạch dự phòng** | Budget dự phòng cho DICOM gateway device (~$5K-10K/unit) hoặc upgrade firmware modality |
| **Chủ sở hữu** | PACS Engineer + Phòng CĐHA BV |
| **Giai đoạn** | GĐ1 — khảo sát M2-M3, test M6-M8 |

### RSK-INT-003: Cổng BHXH thay đổi format/API đột ngột

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | BHXH VN thay đổi schema XML 4210, API endpoint, hoặc business rule mà không thông báo trước đủ thời gian, dẫn đến reject hồ sơ hàng loạt |
| **Xác suất** | Cao (3) |
| **Tác động** | Trung bình (2) — Không gửi được hồ sơ BHXH, trì hoãn quyết toán, ảnh hưởng tài chính BV |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **FR liên quan** | FR-INT-BHXH-001 → FR-INT-BHXH-004, FR-HIS-INS-003 |
| **Kênh tích hợp** | INT-CH-009, INT-CH-010 — xem E.11 |
| **Biện pháp giảm thiểu** | (1) Abstraction layer cho BHXH integration — tách riêng XSD mapping từ business logic; (2) Feature flag để bật/tắt field mapping nhanh; (3) Theo dõi thông báo BHXH (portal + group Zalo/Telegram cộng đồng CNTT y tế); (4) Giữ backward compatibility 2 phiên bản XML; (5) Automated regression test cho XML export |
| **Kế hoạch dự phòng** | Hotfix patch ≤ 48h khi phát hiện thay đổi; nhập thủ công qua portal BHXH tạm thời |
| **Chủ sở hữu** | Backend Lead (BHXH module) |
| **Giai đoạn** | GĐ1-GĐ3 (rủi ro liên tục) |

### RSK-INT-004: Tích hợp LIS giai đoạn 2 phức tạp hơn dự kiến

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hệ thống LIS bên thứ ba sử dụng protocol non-standard hoặc HL7 version khác (2.3 vs 2.4), mapping dữ liệu xét nghiệm phức tạp |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Trì hoãn GĐ2 tính năng LIS 1-2 tháng |
| **Điểm rủi ro** | 🟡 4 — TB |
| **FR liên quan** | FR-INT-LIS-001 → FR-INT-LIS-003 [GĐ2] |
| **Kênh tích hợp** | INT-CH-007, INT-CH-008 — xem E.11 |
| **Biện pháp giảm thiểu** | (1) Khảo sát LIS vendor API/protocol sớm (GĐ1 M9-M10); (2) Dự phòng HL7 2.3 ↔ 2.4 adapter; (3) Đặc tả interface contract trước khi bắt đầu GĐ2 |
| **Kế hoạch dự phòng** | Nếu LIS vendor không hợp tác → file-based integration (CSV/XML) làm phương án tạm |
| **Chủ sở hữu** | Integration Lead |
| **Giai đoạn** | GĐ2 (M13-M18) |

### RSK-INT-005: Tích hợp chữ ký số gặp vấn đề tương thích

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | USB Token (PKCS#11) từ nhiều CA khác nhau (Viettel-CA, VNPT-CA, FPT-CA) có driver/API khác nhau, không hoạt động trên một số trình duyệt hoặc xung đột driver |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Bác sĩ không thể ký số kết quả CĐHA / đơn thuốc, phải ký tay |
| **Điểm rủi ro** | 🟡 4 — TB |
| **FR liên quan** | FR-INT-SIGN-001, FR-INT-SIGN-002 |
| **Kênh tích hợp** | INT-CH-011, INT-CH-012 — xem E.11 |
| **Biện pháp giảm thiểu** | (1) Chọn chuẩn remote signing (server-side) thay USB token — giảm phụ thuộc client; (2) Test tương thích top 3 CA tại VN; (3) WebExtension/Native messaging wrapper chuẩn hóa API CA |
| **Kế hoạch dự phòng** | Workflow ký số ngoại tuyến: export PDF → ký qua tool CA → upload signed PDF |
| **Chủ sở hữu** | Backend Lead |
| **Giai đoạn** | GĐ1 (M8-M10) |

---

## I.4. Rủi ro Vận hành (Operational)

### RSK-OPS-001: Người dùng phản đối thay đổi quy trình

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nhân viên y tế (bác sĩ, điều dưỡng, KTV) từ chối hoặc sử dụng hệ thống mới không đúng cách do quen quy trình giấy / hệ thống cũ |
| **Xác suất** | Cao (3) |
| **Tác động** | Trung bình (2) — Giảm hiệu quả triển khai, song song giấy kéo dài, dữ liệu không đầy đủ |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-USE-001, NFR-USE-002, NFR-USE-005 |
| **Biện pháp giảm thiểu** | (1) Change champion program: chọn 2-3 nhân viên uy tín mỗi khoa làm "đại sứ số hóa"; (2) Đào tạo phân tầng (xem G.6): hands-on > lý thuyết; (3) UI/UX thiết kế theo workflow thực tế BV (không bắt user thay đổi quy trình); (4) Phase rollout: chạy pilot 1 khoa → đánh giá → mở rộng; (5) Hỗ trợ tại chỗ (on-site support) 4 tuần đầu |
| **Kế hoạch dự phòng** | Kéo dài giai đoạn chạy song song (giấy + máy) thêm 2-4 tuần; điều chỉnh UI theo feedback user |
| **Chủ sở hữu** | PM + Trưởng phòng CNTT BV |
| **Giai đoạn** | GĐ1 — UAT (M9-M10) và Go-live (M11-M12) |

### RSK-OPS-002: Thiếu nhân sự vận hành CNTT tại bệnh viện

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bệnh viện không đủ nhân sự CNTT (< 3 người) để vận hành hệ thống 24/7, xử lý sự cố, backup, monitoring |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Cao (3) — Sự cố kéo dài, không có người on-call, vi phạm SLA RTO |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-AVAIL-001, NFR-AVAIL-004 |
| **Biện pháp giảm thiểu** | (1) Automated monitoring + alerting (Zabbix/Grafana) — giảm workload manual; (2) Runbook chi tiết cho 20 tình huống phổ biến nhất; (3) Remote support từ vendor (VPN); (4) Đào tạo chuyên sâu cho IT BV (xem G.6 — IT Admin track); (5) SLA hợp đồng bảo trì với vendor: P1 response ≤ 30 phút (xem G.7.3) |
| **Kế hoạch dự phòng** | Managed service: vendor cung cấp on-call 24/7 trong 12 tháng đầu, chuyển giao dần |
| **Chủ sở hữu** | PM + Ban giám đốc BV |
| **Giai đoạn** | GĐ1 — ký hợp đồng trước go-live (M10) |

### RSK-OPS-003: Migration dữ liệu từ hệ thống cũ thất bại

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Dữ liệu BN, hồ sơ bệnh án, danh mục từ HIS cũ (nếu có) chất lượng kém (duplicate, missing, encoding sai), không import được vào hệ thống mới |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Trì hoãn go-live 2-4 tuần, thiếu lịch sử BN |
| **Điểm rủi ro** | 🟡 4 — TB |
| **FR liên quan** | DR-001 (Patient), DR-002 (Visit) |
| **Biện pháp giảm thiểu** | (1) Data profiling + quality assessment sớm (M3-M4); (2) ETL pipeline với validation rules (xem G.3 migration strategy); (3) Dry-run migration ≥ 3 lần trước cutover; (4) Chấp nhận import subset (BN active 2 năm gần nhất) thay vì toàn bộ |
| **Kế hoạch dự phòng** | Go-live với master data sạch (danh mục, BN active), import lịch sử cũ sau go-live (phase 2 migration) |
| **Chủ sở hữu** | DBA + BA Lead |
| **Giai đoạn** | GĐ1 — migration test M8-M10, cutover M11 |

### RSK-OPS-004: Sự cố mạng nội bộ ảnh hưởng DICOM traffic

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bandwidth LAN không đủ hoặc network congestion khi nhiều modalities gửi ảnh đồng thời (CT 500+ ảnh/study), gây timeout C-STORE hoặc viewer chậm |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Ảnh hưởng workflow chụp + đọc ảnh trong giờ cao điểm |
| **Điểm rủi ro** | 🟡 4 — TB |
| **NFR liên quan** | NFR-PERF-005 (C-STORE throughput ≥ 50 img/s) |
| **Biện pháp giảm thiểu** | (1) Dedicated VLAN cho DICOM traffic (xem G.1.3); (2) 10 Gbps backbone, 1 Gbps access; (3) QoS policy ưu tiên DICOM protocol; (4) Network capacity planning: peak = 5 modalities × 50 img/s × 10 MB = 2.5 GB/s |
| **Kế hoạch dự phòng** | Queue-based DICOM forwarding; tạm dừng auto-routing khi congestion |
| **Chủ sở hữu** | Network Admin BV |
| **Giai đoạn** | GĐ1 — network audit M2-M3 |

---

## I.5. Rủi ro Pháp lý & Tuân thủ (Legal & Compliance)

### RSK-LEG-001: Thay đổi quy định BHYT / Bộ Y tế

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thay đổi lớn về luật BHYT (tỷ lệ chi trả, danh mục dịch vụ), quy định BYT (mẫu báo cáo, tiêu chí CNTT y tế), hoặc format dữ liệu BHXH trong quá trình phát triển |
| **Xác suất** | Cao (3) |
| **Tác động** | Trung bình (2) — Rework module viện phí/BHYT, thay đổi business rule, trì hoãn 2-4 tuần |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-COMP-004, NFR-COMP-005 |
| **FR liên quan** | FR-HIS-INS-001 → FR-HIS-INS-005, FR-HIS-BIL-001 → FR-HIS-BIL-005 |
| **Biện pháp giảm thiểu** | (1) Rule engine cho business rules BHYT — cấu hình thay vì code cứng (BR-033); (2) Danh mục thuốc/dịch vụ BHYT = dynamic config; (3) Tham gia hội thảo/workshop CNTT y tế BYT để nắm trước thay đổi; (4) Sprint buffer 10% cho regulatory changes |
| **Kế hoạch dự phòng** | Hotfix release cycle ≤ 1 tuần cho thay đổi quy định bắt buộc |
| **Chủ sở hữu** | BA Lead + Legal/Compliance BV |
| **Giai đoạn** | GĐ1-GĐ3 (rủi ro liên tục) |

> **BR-033**: Tất cả business rules liên quan tỷ lệ chi trả BHYT, trần thanh toán, danh mục dịch vụ BHYT phải được quản lý qua rule engine/configuration, không hardcode trong source code.

### RSK-LEG-002: Vi phạm quy định bảo vệ dữ liệu cá nhân

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân yêu cầu consent management, data access control, right to erasure — hệ thống chưa đáp ứng đầy đủ |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Cao (3) — Xử phạt hành chính, yêu cầu sửa hệ thống khẩn cấp |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **NFR liên quan** | NFR-SEC-006, NFR-SEC-008 |
| **Biện pháp giảm thiểu** | (1) Consent form điện tử khi đăng ký BN (opt-in processing); (2) Data access log 100% (FR-HIS-SYS-003); (3) Data anonymization cho nghiên cứu/export; (4) Retention policy rõ ràng (10 năm y tế, 5 năm tài chính); (5) Legal review trước go-live |
| **Kế hoạch dự phòng** | Emergency patch + quy trình manual consent nếu bị kiểm tra |
| **Chủ sở hữu** | Legal BV + Security Lead |
| **Giai đoạn** | GĐ1 — thiết kế M1-M3, implement M4-M8 |

### RSK-LEG-003: Yêu cầu ký số bắt buộc cho hồ sơ bệnh án điện tử

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quy định tương lai có thể yêu cầu chữ ký số (qualified electronic signature) bắt buộc trên mọi hồ sơ bệnh án điện tử, không chỉ kết quả CĐHA |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Mở rộng scope ký số, tăng chi phí CA certificate, thay đổi workflow |
| **Điểm rủi ro** | 🟡 4 — TB |
| **FR liên quan** | FR-INT-SIGN-001, FR-INT-SIGN-002, FR-HIS-EMR-* |
| **Biện pháp giảm thiểu** | (1) Thiết kế signing framework từ GĐ1 — extensible cho mọi document type; (2) Batch signing capability; (3) Theo dõi dự thảo luật EMR |
| **Kế hoạch dự phòng** | Enable signing cho tất cả document types qua configuration, không cần code change lớn |
| **Chủ sở hữu** | Solution Architect |
| **Giai đoạn** | GĐ1 (framework), GĐ2-GĐ3 (mở rộng) |

---

## I.6. Rủi ro Dự án (Project Management)

### RSK-PM-001: Phạm vi dự án bị mở rộng không kiểm soát (Scope Creep)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bệnh viện liên tục yêu cầu thêm tính năng ngoài SRS trong quá trình phát triển — "chỉ thêm 1 cái nữa thôi" |
| **Xác suất** | Cao (3) |
| **Tác động** | Cao (3) — Trì hoãn go-live, vượt budget, kiệt sức team |
| **Điểm rủi ro** | 🔴 9 — CỰC |
| **Biện pháp giảm thiểu** | (1) SRS v2.0 (bộ tài liệu này) = baseline, sign-off bởi cả 2 bên; (2) Change Request (CR) process bắt buộc: đánh giá impact → approve/reject; (3) Product backlog: yêu cầu mới → GĐ2/GĐ3 trừ khi critical; (4) Sprint review: demo chỉ tính năng đã plan, không nhận CR tại chỗ; (5) Steering Committee review CR > 40h effort |
| **Kế hoạch dự phòng** | Re-baseline scope nếu CR tích lũy > 15% effort ban đầu |
| **Chủ sở hữu** | PM |
| **Giai đoạn** | GĐ1-GĐ3 (rủi ro liên tục) |

### RSK-PM-002: Thiếu nguồn lực phát triển (nhân sự chủ chốt nghỉ)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Developer/architect chủ chốt nghỉ việc hoặc chuyển dự án, mất kiến thức domain healthcare phức tạp |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Cao (3) — Trì hoãn 1-2 tháng tuyển dụng + onboard, mất chất lượng code |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **Biện pháp giảm thiểu** | (1) Knowledge sharing: pair programming, code review bắt buộc; (2) Bus factor ≥ 2 cho mỗi module; (3) Technical documentation (ADR, API docs) cập nhật liên tục; (4) Competitive compensation cho core team; (5) Cross-training giữa HIS team và PACS team |
| **Kế hoạch dự phòng** | Vendor backup: danh sách consultant/contractor healthcare IT có thể hire trong 2-4 tuần |
| **Chủ sở hữu** | PM + HR |
| **Giai đoạn** | GĐ1-GĐ3 |

### RSK-PM-003: Trì hoãn mua sắm hạ tầng (procurement)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quy trình đấu thầu/mua sắm server, storage, license tại BV công kéo dài hơn dự kiến (3-6 tháng thay vì 1-2 tháng) |
| **Xác suất** | Cao (3) |
| **Tác động** | Trung bình (2) — Không có môi trường SIT/UAT đúng spec, test không chính xác |
| **Điểm rủi ro** | 🟠 6 — CAO |
| **Biện pháp giảm thiểu** | (1) Bắt đầu procurement song song với M1 (kick-off); (2) Sử dụng cloud/VM tạm cho DEV/SIT trong khi chờ on-prem; (3) Tách procurement hardware thành gói riêng, khởi động trước; (4) Chuẩn bị spec chi tiết (xem G.1.2) để giảm vòng hỏi đáp |
| **Kế hoạch dự phòng** | Thuê server/cloud tạm (IaaS VN: Viettel Cloud, CMC Cloud) cho SIT/UAT, migrate sang on-prem khi ready |
| **Chủ sở hữu** | PM + Phòng Vật tư/Mua sắm BV |
| **Giai đoạn** | GĐ1 — M1-M4 (critical) |

### RSK-PM-004: Stakeholder engagement kém — BV không cử người tham gia

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đại diện BV (bác sĩ, điều dưỡng, viện phí) bận công việc chuyên môn, không tham gia sprint review, UAT, hoặc requirement validation |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Yêu cầu sai/thiếu, phát hiện muộn tại go-live, rework |
| **Điểm rủi ro** | 🟡 4 — TB |
| **Biện pháp giảm thiểu** | (1) MOU ký kết cam kết tham gia: ≥ 4h/tuần cho key users; (2) Schedule họp phù hợp (tránh giờ khám 8-11h); (3) Remote demo/review via Teams/Zoom; (4) Đào tạo BA team domain healthcare để giảm phụ thuộc |
| **Kế hoạch dự phòng** | Ghi nhận giả định trong meeting minutes → BV sign-off → rủi ro chuyển cho BV nếu yêu cầu sai |
| **Chủ sở hữu** | PM |
| **Giai đoạn** | GĐ1 — toàn bộ, đặc biệt M4-M10 (development + UAT) |

### RSK-PM-005: Budget overrun do thay đổi yêu cầu hoặc hạ tầng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Chi phí thực vượt budget ban đầu do: scope creep (RSK-PM-001), hạ tầng đắt hơn dự kiến, licensing cost, hoặc kéo dài timeline |
| **Xác suất** | Trung bình (2) |
| **Tác động** | Trung bình (2) — Giảm scope GĐ2/GĐ3, team bị cắt giảm, chất lượng giảm |
| **Điểm rủi ro** | 🟡 4 — TB |
| **Biện pháp giảm thiểu** | (1) Contingency budget 15-20%; (2) Monthly burn rate tracking; (3) Ưu tiên OSS (Orthanc, OHIF) thay commercial license; (4) CR process gắn cost estimate |
| **Kế hoạch dự phòng** | Scope negotiation: giảm tính năng GĐ2/GĐ3 để giữ ngân sách GĐ1 |
| **Chủ sở hữu** | PM + CFO BV |
| **Giai đoạn** | GĐ1-GĐ3 |

---

## I.7. Tổng hợp Risk Register

### I.7.1. Bảng tổng hợp theo điểm rủi ro

| Mã | Tên rủi ro | Loại | XS | TĐ | Điểm | Mức | Chủ sở hữu |
|----|-----------|------|-----|-----|------|-----|-------------|
| RSK-INT-001 | HL7 HIS↔PACS không ổn định | Integration | 3 | 3 | 9 | 🔴 CỰC | Integration Lead |
| RSK-PM-001 | Scope Creep | Project | 3 | 3 | 9 | 🔴 CỰC | PM |
| RSK-TECH-001 | DICOM Viewer hiệu năng kém | Technical | 3 | 3 | 9 | 🔴 CỰC | Tech Lead PACS |
| RSK-TECH-003 | DB HIS deadlock/slow query | Technical | 2 | 3 | 6 | 🟠 CAO | DBA |
| RSK-TECH-006 | Bảo mật dữ liệu y tế | Technical | 2 | 3 | 6 | 🟠 CAO | Security Lead |
| RSK-INT-003 | BHXH thay đổi format | Integration | 3 | 2 | 6 | 🟠 CAO | Backend Lead |
| RSK-OPS-001 | User phản đối thay đổi | Operational | 3 | 2 | 6 | 🟠 CAO | PM |
| RSK-OPS-002 | Thiếu nhân sự IT BV | Operational | 2 | 3 | 6 | 🟠 CAO | PM |
| RSK-LEG-001 | Thay đổi quy định BHYT/BYT | Legal | 3 | 2 | 6 | 🟠 CAO | BA Lead |
| RSK-LEG-002 | Vi phạm bảo vệ DLCN | Legal | 2 | 3 | 6 | 🟠 CAO | Legal BV |
| RSK-PM-002 | Mất nhân sự chủ chốt | Project | 2 | 3 | 6 | 🟠 CAO | PM |
| RSK-PM-003 | Trì hoãn procurement | Project | 3 | 2 | 6 | 🟠 CAO | PM |
| RSK-TECH-002 | DICOM storage tăng nhanh | Technical | 2 | 2 | 4 | 🟡 TB | Infra Lead |
| RSK-INT-002 | Modality không tương thích | Integration | 2 | 2 | 4 | 🟡 TB | PACS Engineer |
| RSK-INT-004 | LIS tích hợp phức tạp | Integration | 2 | 2 | 4 | 🟡 TB | Integration Lead |
| RSK-INT-005 | Chữ ký số tương thích | Integration | 2 | 2 | 4 | 🟡 TB | Backend Lead |
| RSK-OPS-003 | Migration dữ liệu thất bại | Operational | 2 | 2 | 4 | 🟡 TB | DBA |
| RSK-OPS-004 | Mạng nội bộ congestion | Operational | 2 | 2 | 4 | 🟡 TB | Network Admin |
| RSK-LEG-003 | Ký số bắt buộc EMR | Legal | 2 | 2 | 4 | 🟡 TB | Architect |
| RSK-PM-004 | Stakeholder engagement kém | Project | 2 | 2 | 4 | 🟡 TB | PM |
| RSK-PM-005 | Budget overrun | Project | 2 | 2 | 4 | 🟡 TB | PM |
| RSK-TECH-004 | SPOF hạ tầng On-prem | Technical | 1 | 3 | 3 | 🟡 TB | Infra Lead |
| RSK-TECH-005 | Chọn sai tech stack | Technical | 1 | 3 | 3 | 🟡 TB | Architect |

### I.7.2. Ma trận Xác suất × Tác động — Phân bố

```
                        TÁC ĐỘNG
                 Thấp (1)   TB (2)     Cao (3)
              ┌──────────┬──────────┬──────────────────┐
  Cao    (3)  │          │ RSK-INT  │ RSK-INT-001      │
  (XS)        │          │ -003     │ RSK-PM-001       │
              │          │ RSK-OPS  │ RSK-TECH-001     │
              │          │ -001     │                  │
              │          │ RSK-LEG  │                  │
              │          │ -001     │                  │
              │          │ RSK-PM   │                  │
              │          │ -003     │                  │
              ├──────────┼──────────┼──────────────────┤
  TB     (2)  │          │ RSK-TECH │ RSK-TECH-003     │
  (XS)        │          │ -002     │ RSK-TECH-006     │
              │          │ RSK-INT  │ RSK-OPS-002      │
              │          │ -002,-004│ RSK-LEG-002      │
              │          │ -005     │ RSK-PM-002       │
              │          │ RSK-OPS  │                  │
              │          │ -003,-004│                  │
              │          │ RSK-LEG  │                  │
              │          │ -003     │                  │
              │          │ RSK-PM   │                  │
              │          │ -004,-005│                  │
              ├──────────┼──────────┼──────────────────┤
  Thấp   (1)  │          │          │ RSK-TECH-004     │
  (XS)        │          │          │ RSK-TECH-005     │
              └──────────┴──────────┴──────────────────┘
```

### I.7.3. Thống kê theo mức rủi ro

| Mức rủi ro | Số lượng | Tỷ lệ |
|-----------|---------|--------|
| 🔴 CỰC (Critical) | 3 | 13% |
| 🟠 CAO (High) | 9 | 39% |
| 🟡 TB (Medium) | 11 | 48% |
| 🟢 THẤP (Low) | 0 | 0% |
| **Tổng** | **23** | **100%** |

### I.7.4. Thống kê theo loại rủi ro

| Loại | Số lượng | Critical | High | Medium |
|------|---------|----------|------|--------|
| Technical | 6 | 1 | 2 | 3 |
| Integration | 5 | 1 | 1 | 3 |
| Operational | 4 | 0 | 2 | 2 |
| Legal & Compliance | 3 | 0 | 2 | 1 |
| Project Management | 5 | 1 | 2 | 2 |
| **Tổng** | **23** | **3** | **9** | **11** |

---

## I.8. Kế hoạch Giám sát Rủi ro

### I.8.1. Quy trình Review

| Mức rủi ro | Tần suất review | Người review | Báo cáo |
|-----------|-----------------|-------------|---------|
| 🔴 CỰC | Hàng tuần | Steering Committee (PM + Ban GĐ BV) | Status report + action items |
| 🟠 CAO | 2 tuần / lần | PM + Tech Leads | Risk dashboard update |
| 🟡 TB | Hàng tháng | PM | Monthly risk report |
| 🟢 THẤP | Hàng quý | PM | Quarterly review |

### I.8.2. Risk Dashboard Metrics

| Metric | Mô tả | Target |
|--------|-------|--------|
| Open Critical Risks | Số rủi ro CỰC chưa có mitigation hiệu quả | 0 |
| Risk Trend | Xu hướng tổng điểm rủi ro qua các tháng | Giảm dần |
| Mitigation Completion | % mitigation actions hoàn thành đúng hạn | ≥ 80% |
| New Risks Identified | Số rủi ro mới phát sinh trong tháng | Tracking |
| Risk Escalations | Số lần escalate lên Steering Committee | Tracking |

### I.8.3. Trigger Points — Escalation tự động

| Trigger | Action |
|---------|--------|
| Rủi ro CỰC mới phát sinh | Họp Steering Committee trong 48h |
| 2+ rủi ro CAO chuyển thành CỰC cùng tháng | Đánh giá lại timeline dự án |
| Mitigation action quá hạn > 2 tuần | Escalate lên PM → Tech Director |
| Go-live còn 4 tuần mà có ≥ 1 CỰC open | Xem xét hoãn go-live |

---

## I.9. Business Rules bổ sung

| Mã | Quy tắc |
|----|---------|
| BR-033 | Tất cả business rules liên quan tỷ lệ chi trả BHYT, trần thanh toán, danh mục dịch vụ BHYT phải được quản lý qua rule engine/configuration, không hardcode trong source code |
| BR-034 | Mỗi rủi ro mức CỰC và CAO phải có ít nhất 1 mitigation action được assign và có deadline cụ thể trước khi bắt đầu sprint liên quan |
| BR-035 | Risk register phải được cập nhật trong mỗi Sprint Retrospective; rủi ro mới phải được đánh giá trong 48h kể từ khi phát sinh |
| BR-036 | Không được go-live khi còn bất kỳ rủi ro mức CỰC nào mà mitigation chưa đạt hiệu quả đo lường được |

---

*Tiếp theo: [Phần J — Phụ lục](./J-PHU-LUC.md)*