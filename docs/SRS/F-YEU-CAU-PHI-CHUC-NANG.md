# PHẦN F — YÊU CẦU PHI CHỨC NĂNG

> **Mã phần**: SRS-PART-F  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: NFR-{CATEGORY}-{STT}

---

## F.1. Hiệu năng (Performance)

### NFR-PERF-001: Thời gian phản hồi HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian phản hồi cho thao tác thông thường trên HIS web interface |
| **Metric** | Response time (từ click → dữ liệu hiển thị hoàn chỉnh) |
| **Target** | P95 ≤ 3 giây; P99 ≤ 5 giây |
| **Điều kiện đo** | 200 user đồng thời, giờ cao điểm (8:00–10:00), mạng LAN 1 Gbps |
| **Phạm vi** | Tất cả màn hình HIS: tiếp đón, khám bệnh, kê đơn, viện phí |
| **Ngoại lệ** | Báo cáo phức tạp (> 10.000 dòng) cho phép ≤ 30 giây |
| **Tiêu chí chấp nhận** | Load test 200 concurrent users × 10 phút → P95 ≤ 3s; zero HTTP 5xx |

### NFR-PERF-002: Thời gian hiển thị ảnh DICOM — Single Image

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian từ click "Xem ảnh" đến hiển thị ảnh đầu tiên trên web viewer |
| **Metric** | Time to First Image (TTFI) |
| **Target** | CR/DX (đơn ảnh, ~10 MB): ≤ 5 giây |
| **Điều kiện đo** | Ảnh ở HOT tier, mạng LAN 1 Gbps, 10 viewer sessions đồng thời |
| **Tiêu chí chấp nhận** | Mở 10 study CR đồng thời → TTFI P95 ≤ 5s |

### NFR-PERF-003: Thời gian hiển thị ảnh DICOM — Multi-slice Study

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian load study CT/MR đa lát cắt trên web viewer |
| **Metric** | Time to Full Study Load |
| **Target** | CT 200 ảnh (~100 MB): ≤ 15 giây; MR 500 ảnh (~250 MB): ≤ 30 giây |
| **Điều kiện đo** | Ảnh ở HOT tier, mạng LAN 1 Gbps, progressive loading enabled |
| **Quy tắc** | Viewer phải hỗ trợ progressive loading: hiển thị ảnh đầu tiên nhanh, load phần còn lại trong background |
| **Tiêu chí chấp nhận** | CT 200 ảnh: TTFI ≤ 5s, full load ≤ 15s |

### NFR-PERF-004: Throughput HL7 Message

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Khả năng xử lý HL7 message của Integration Engine |
| **Metric** | Messages per second (MPS) |
| **Target** | ≥ 50 messages/giây (sustained) |
| **Điều kiện đo** | Mix message: 60% ADT, 30% ORM, 10% ORU |
| **Tiêu chí chấp nhận** | Stress test 50 MPS × 5 phút → 0 message lost, latency P99 ≤ 1s |

### NFR-PERF-005: Throughput DICOM C-STORE

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tốc độ nhận ảnh DICOM qua C-STORE |
| **Metric** | Images per second |
| **Target** | ≥ 50 ảnh/giây (512 KB/ảnh, CT-size) |
| **Điều kiện đo** | 3 modality gửi đồng thời, mạng LAN 1 Gbps |
| **Tiêu chí chấp nhận** | 3 modality × burst 100 ảnh mỗi → tổng 300 ảnh nhận thành công trong ≤ 10s |

### NFR-PERF-006: Concurrent Users

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Số user đồng thời HIS mà hệ thống đáp ứng ổn định |
| **Target** | ≥ 200 concurrent sessions (active) |
| **Peak** | ≥ 300 concurrent sessions (10% degradation chấp nhận) |
| **Tiêu chí chấp nhận** | Load test 200 users: response time vẫn đạt NFR-PERF-001; 300 users: P95 ≤ 5s |

### NFR-PERF-007: Năng lực CĐHA hàng ngày

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Số ca CĐHA hệ thống xử lý ổn định trong 1 ngày (8 giờ) |
| **Target** | ≥ 500 ca/ngày (peak: 200 ca design × 2.5 headroom) |
| **Bao gồm** | Order nhận, MWL query, C-STORE nhận ảnh, báo cáo, ORU gửi |
| **Tiêu chí chấp nhận** | Simulate 500 ca workflow E2E trong 8 giờ → 100% thành công |

### NFR-PERF-008: Database Query Performance

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian truy vấn database cho các thao tác phổ biến |
| **Targets** | - Tra cứu BN theo PID: ≤ 100ms<br/>- Tìm BN theo tên + DOB: ≤ 500ms<br/>- Danh sách chờ khám (1 phòng): ≤ 200ms<br/>- Lịch sử khám 1 BN (100 lượt): ≤ 1s<br/>- DICOM study search (QIDO-RS) by accession: ≤ 200ms |
| **Tiêu chí chấp nhận** | 2 triệu patient records, 10 triệu visit records → query targets đạt |

---

## F.2. Khả năng Mở rộng (Scalability)

### NFR-SCAL-001: Horizontal Scaling — HIS Application

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS web application hỗ trợ horizontal scaling (thêm instance) |
| **Yêu cầu** | - Stateless application design (session stored in Redis/DB)<br/>- Load balancer (HAProxy/Nginx) phân phối request<br/>- Thêm instance không cần downtime<br/>- [GIẢ ĐỊNH] Minimum 2 instances cho HA |
| **Tiêu chí chấp nhận** | Thêm 1 instance → throughput tăng ≥ 80% (near-linear) |

### NFR-SCAL-002: Storage Scaling — PACS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PACS storage mở rộng dung lượng không gây downtime |
| **Yêu cầu** | - Thêm disk/node vào storage pool online<br/>- Rebalance data tự động (background)<br/>- Hỗ trợ ≥ 100 TB (10 năm dữ liệu) |
| **Tiêu chí chấp nhận** | Thêm 1 storage node → capacity tăng, không service interruption |

### NFR-SCAL-003: Multi-site Scaling

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hệ thống hỗ trợ 5 cơ sở (hiện tại) và mở rộng đến 10 cơ sở |
| **Yêu cầu** | - Thêm cơ sở = cấu hình site_code + deploy PACS node cục bộ<br/>- Không thay đổi code application<br/>- Dữ liệu phân lập theo site nhưng query cross-site được (với quyền) |
| **Tiêu chí chấp nhận** | Thêm cơ sở thứ 6 hoàn thành trong ≤ 1 ngày (cấu hình + deploy) |

---

## F.3. Độ Sẵn sàng và Khả năng Phục hồi (Availability & Disaster Recovery)

### NFR-AVAIL-001: HIS Uptime

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Độ sẵn sàng tổng thể của HIS |
| **Target** | ≥ 99.5% (downtime ≤ 44 giờ/năm) |
| **Scheduled Maintenance** | Không tính vào downtime — thực hiện ngoài giờ (Chủ nhật 01:00–05:00) |
| **Tiêu chí chấp nhận** | Đo monthly uptime ≥ 99.5% liên tục 3 tháng sau go-live |

### NFR-AVAIL-002: PACS Uptime

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Độ sẵn sàng tổng thể của PACS (Archive + Viewer + MWL) |
| **Target** | ≥ 99.9% (downtime ≤ 8.7 giờ/năm) |
| **Lý do cao hơn HIS** | PACS cần liên tục nhận ảnh từ modality (24/7, bao gồm cấp cứu đêm) |
| **Tiêu chí chấp nhận** | PACS C-STORE + MWL available 24/7, đo monthly ≥ 99.9% |

### NFR-AVAIL-003: Recovery Point Objective (RPO)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Lượng dữ liệu tối đa có thể mất khi xảy ra thảm họa |
| **Targets** | - HIS Database: RPO ≤ 1 giờ (incremental backup hourly)<br/>- PACS Database: RPO ≤ 15 phút (WAL shipping/streaming replication)<br/>- DICOM Files (HOT): RPO ≤ 5 phút (sync replication)<br/>- DICOM Files (WARM/COLD): RPO ≤ 24 giờ |
| **Tiêu chí chấp nhận** | DR drill: restore từ backup, verify data loss ≤ RPO target |

### NFR-AVAIL-004: Recovery Time Objective (RTO)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian tối đa để khôi phục hệ thống sau thảm họa |
| **Targets** | - HIS: RTO ≤ 4 giờ<br/>- PACS (core services): RTO ≤ 2 giờ<br/>- PACS (full archive): RTO ≤ 8 giờ (COLD tier có thể chậm hơn) |
| **Tiêu chí chấp nhận** | DR drill hàng quý: khôi phục từ backup → hệ thống hoạt động trong ≤ RTO |

### NFR-AVAIL-005: Zero Data Loss cho DICOM

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Không mất ảnh DICOM đã nhận thành công (C-STORE ACK = success) |
| **Phương pháp** | - Sync replication cho HOT tier<br/>- Checksum verification (CRC32/SHA-256) mỗi ảnh<br/>- PACS chỉ trả C-STORE success SAU khi write + fsync thành công |
| **Tiêu chí chấp nhận** | DR drill: restore → verify 100% ảnh intact (checksum match) |

### NFR-AVAIL-006: High Availability Architecture

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Kiến trúc HA cho các component chính |
| **HIS Web** | Active-Active (2+ instances behind LB) |
| **HIS Database** | Primary + Standby (streaming replication, auto-failover) |
| **PACS Application** | Active-Passive (auto-failover, shared storage) hoặc Active-Active [GĐ2] |
| **PACS Storage** | RAID 6 + replication to DR site |
| **Integration Engine** | Active-Active (clustered), persistent queue |
| **Message Queue** | Clustered (RabbitMQ mirrored queues / Kafka replication) |
| **Tiêu chí chấp nhận** | Kill primary DB → failover trong ≤ 30 giây, 0 data loss |

```
HA Architecture Diagram

┌──────────────────────────────────────────────────────┐
│                  Load Balancer (LB)                   │
│              HAProxy / Nginx (Active-Active)          │
└───────────┬──────────────────────┬───────────────────┘
            │                      │
     ┌──────▼──────┐       ┌──────▼──────┐
     │  HIS App 1  │       │  HIS App 2  │   ← Stateless
     └──────┬──────┘       └──────┬──────┘
            │                      │
     ┌──────▼──────────────────────▼──────┐
     │         Redis (Session Store)       │ ← Clustered
     └──────────────┬─────────────────────┘
                    │
     ┌──────────────▼─────────────────────┐
     │     HIS Database (PostgreSQL)       │
     │   Primary ◄── Streaming ──► Standby│ ← Auto-failover
     └────────────────────────────────────┘

     ┌────────────────────────────────────┐
     │      PACS Application Server       │
     │      Active ◄── Failover ──► Passive│
     └──────────────┬─────────────────────┘
                    │
     ┌──────────────▼─────────────────────┐
     │    PACS Database (PostgreSQL)       │
     │   Primary ◄── Streaming ──► Standby│
     └────────────────────────────────────┘
                    │
     ┌──────────────▼─────────────────────┐
     │    DICOM Storage (SAN/NAS)         │
     │    RAID 6 + Replicate to DR        │
     └────────────────────────────────────┘
```

---

## F.4. Bảo mật (Security)

### NFR-SEC-001: Mã hóa truyền tải (Encryption in Transit)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tất cả giao tiếp qua mạng phải được mã hóa |
| **Yêu cầu** | - HTTPS (TLS 1.2+) cho tất cả web traffic<br/>- DICOM TLS cho truyền ảnh ngoài mạng LAN nội bộ<br/>- HL7 over TLS (hoặc VPN) khi qua WAN<br/>- Database connection: SSL/TLS required |
| **Certificate** | Sử dụng CA nội bộ hoặc public CA (Let's Encrypt / DigiCert) |
| **Tiêu chí chấp nhận** | SSL Labs scan: Grade A; 0 traffic HTTP (redirect → HTTPS); DICOM association ngoài LAN reject nếu không TLS |

### NFR-SEC-002: Mã hóa dữ liệu nghỉ (Encryption at Rest)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Mã hóa dữ liệu nhạy cảm khi lưu trữ |
| **Phạm vi** | - Database: TDE (Transparent Data Encryption) hoặc column-level encryption cho PII<br/>- DICOM files: filesystem-level encryption (LUKS/BitLocker) hoặc application-level<br/>- Backup: encrypted backup |
| **Algorithm** | AES-256 |
| **Key Management** | Key stored riêng biệt, rotation hàng năm |
| **Tiêu chí chấp nhận** | 1. Raw disk/file access → dữ liệu unreadable<br/>2. Key rotation không gây downtime |

### NFR-SEC-003: Authentication

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Xác thực người dùng truy cập hệ thống |
| **Phương pháp** | - Username/Password (bắt buộc)<br/>- MFA — TOTP (Google Authenticator) hoặc SMS OTP (tùy chọn, khuyến cáo cho admin/BS CĐHA)<br/>- LDAP/Active Directory integration (tùy chọn)<br/>- SSO giữa HIS và PACS (JWT-based) |
| **Password Policy** | - Tối thiểu 8 ký tự<br/>- Chứa chữ hoa + chữ thường + số + ký tự đặc biệt<br/>- Đổi mật khẩu: bắt buộc mỗi 90 ngày<br/>- Không trùng 5 mật khẩu gần nhất<br/>- Khóa tài khoản sau 5 lần sai liên tiếp (unlock bởi admin hoặc tự động sau 30 phút) |
| **Tiêu chí chấp nhận** | 1. Đăng nhập thành công với username/password hợp lệ<br/>2. 5 lần sai → tài khoản khóa 30 phút<br/>3. SSO: login HIS → mở PACS viewer không cần login lại |

### NFR-SEC-004: Authorization (RBAC)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Phân quyền truy cập dựa trên vai trò (Role-Based Access Control) |
| **Mô hình** | RBAC + optional ABAC (Attribute-Based) cho quy tắc phức tạp |
| **Phân quyền** | - Theo module/chức năng: view, create, edit, delete, approve, export<br/>- Theo khoa/phòng: BS khoa A chỉ thấy BN khoa A (configurable)<br/>- Theo cơ sở: nhân viên cơ sở X chỉ thấy dữ liệu cơ sở X (default, override cho manager)<br/>- Đặc biệt: xem ảnh DICOM = cần quyền VIEW_DICOM; export ảnh = EXPORT_DICOM |
| **Vai trò mặc định** | SysAdmin, PACS Admin, Doctor (OPD/IPD), Radiologist, Technologist, Nurse, Pharmacist, Cashier, Insurance Staff, Manager, Read-only |
| **Tiêu chí chấp nhận** | 1. User thiếu quyền → HTTP 403 + thông báo rõ<br/>2. Pharmacist không thấy menu "Khám bệnh"<br/>3. BS khoa A không thấy BN khoa B (nếu cấu hình restrict) |

### NFR-SEC-005: Session Management

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý phiên đăng nhập |
| **Yêu cầu** | - Session timeout: 30 phút không hoạt động → auto logout<br/>- Concurrent session: tối đa 3 session/user (configurable)<br/>- Force logout: admin có thể kick session<br/>- Session token: JWT, signed, expiry 8 giờ (refresh token 24 giờ) |
| **Tiêu chí chấp nhận** | 1. Idle 30 phút → redirect login page<br/>2. Login từ máy thứ 4 → session cũ nhất bị kick |

### NFR-SEC-006: Audit Trail

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi log tất cả thao tác trên hệ thống |
| **Nội dung log** | - User ID, timestamp, IP address, action, module, entity_type, entity_id<br/>- Old value, new value (cho update/delete)<br/>- Login/logout events<br/>- Failed login attempts<br/>- DICOM access: user, study_uid, action (view/download/print) |
| **Lưu trữ** | Append-only storage (không cho phép sửa/xóa audit log) |
| **Retention** | 5 năm |
| **Query** | Giao diện tìm kiếm audit: theo user, thời gian, action, entity |
| **Tiêu chí chấp nhận** | 1. Mọi thao tác CRUD đều có audit record<br/>2. Xóa audit log → báo lỗi "Operation not permitted"<br/>3. Tìm kiếm audit 1 triệu records → kết quả trong ≤ 5 giây |

### NFR-SEC-007: DICOM Security

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bảo mật cho truyền thông DICOM |
| **Yêu cầu** | - DICOM TLS (Secure Transport Connection Profile) cho kết nối ngoài LAN<br/>- AE Title whitelist: chỉ accept association từ AE Title đã đăng ký<br/>- DICOM audit trail (IHE ATNA profile) [GĐ2]<br/>- De-identification support cho export research data |
| **Tiêu chí chấp nhận** | 1. C-STORE từ AE Title chưa đăng ký → association rejected<br/>2. DICOM connection qua WAN không TLS → rejected |

---

## F.5. Tuân thủ Pháp luật và Tiêu chuẩn (Compliance)

### NFR-COMP-001: DICOM 3.0 Conformance

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | PACS tuân thủ chuẩn DICOM 3.0 |
| **Yêu cầu** | - Có DICOM Conformance Statement đầy đủ<br/>- Hỗ trợ SOP Classes: CT, MR, CR, DX, US, MG, NM, PT, SC, Enhanced CT/MR, SR, GSPS, KOS<br/>- Hỗ trợ DICOM services: C-STORE, C-FIND, C-MOVE, C-GET, MWL, MPPS, WADO-RS, QIDO-RS, STOW-RS |
| **Tiêu chí chấp nhận** | 1. DICOM Conformance Statement được review bởi chuyên gia<br/>2. Interoperability test với ≥ 3 loại modality (CT, MR, DR) thành công |

### NFR-COMP-002: HL7 v2.x Conformance

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tích hợp tuân thủ chuẩn HL7 v2.4 |
| **Yêu cầu** | - Message structure tuân thủ HL7 v2.4 specification<br/>- Hỗ trợ message types: ADT (A01/A02/A03/A04/A08/A40), ORM (O01), ORU (R01), ACK<br/>- Character encoding: UTF-8<br/>- MLLP framing đúng specification |
| **Tiêu chí chấp nhận** | 1. HL7 message validate qua HL7 validator → 0 error<br/>2. Interoperability test HIS ↔ PACS → tất cả message type pass |

### NFR-COMP-003: IHE Profiles

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tuân thủ IHE Integration Profiles |
| **Profiles** | - SWF (Scheduled Workflow): bắt buộc GĐ1<br/>- PIR (Patient Information Reconciliation): bắt buộc GĐ1<br/>- CPI (Consistent Presentation of Images): khuyến cáo GĐ2<br/>- XDS-I.b (Cross-enterprise Document Sharing for Imaging): khuyến cáo GĐ3 |
| **Tiêu chí chấp nhận** | 1. IHE SWF workflow test thành công: Order → MWL → Acquire → Store → Report<br/>2. PIR: Patient merge propagate đến PACS studies |

### NFR-COMP-004: Tuân thủ quy định BHXH

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS tuân thủ format và quy trình gửi dữ liệu BHXH |
| **Yêu cầu** | - XML 4210/QĐ-BYT đúng schema version hiện hành<br/>- Mapping ICD-10, mã thuốc, mã dịch vụ theo danh mục BHXH<br/>- Hỗ trợ cập nhật khi BHXH thay đổi schema (< 2 tuần deploy) |
| **Tiêu chí chấp nhận** | 1. Gửi XML → BHXH accept 100% (trừ lỗi nghiệp vụ)<br/>2. BHXH đổi schema → hệ thống cập nhật trong ≤ 2 tuần |

### NFR-COMP-005: Tuân thủ pháp luật Việt Nam

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hệ thống tuân thủ các quy định pháp luật VN về y tế và CNTT |
| **Quy định** | - Luật Khám chữa bệnh 2023: bệnh án điện tử, lưu trữ 10 năm<br/>- Thông tư 54/2017/TT-BYT: tiêu chí ứng dụng CNTT y tế<br/>- Thông tư 46/2018/TT-BYT: hồ sơ bệnh án điện tử<br/>- Nghị định 13/2023/NĐ-CP: bảo vệ dữ liệu cá nhân<br/>- Quy định BHXH về liên thông dữ liệu KCB |
| **Tiêu chí chấp nhận** | 1. Compliance checklist theo TT54 → đạt ≥ 90% tiêu chí<br/>2. Hồ sơ bệnh án điện tử đúng mẫu TT46 |

---

## F.6. Khả năng Sử dụng (Usability)

### NFR-USE-001: Thời gian Đào tạo

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thời gian đào tạo cho user mới sử dụng thành thạo module chính |
| **Target** | - Tiếp đón, viện phí: ≤ 8 giờ<br/>- Bác sĩ (khám bệnh, kê đơn): ≤ 8 giờ<br/>- KTV CĐHA (PACS worklist): ≤ 4 giờ<br/>- BS CĐHA (Viewer + báo cáo): ≤ 16 giờ<br/>- SysAdmin: ≤ 24 giờ |
| **Tiêu chí chấp nhận** | User hoàn thành task chuẩn không cần hỗ trợ sau đào tạo |

### NFR-USE-002: Keyboard Shortcuts

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hỗ trợ phím tắt cho thao tác thường xuyên |
| **Phạm vi** | - HIS: F2 (lưu), F5 (tìm kiếm), Enter (xác nhận), Esc (hủy), Ctrl+P (in)<br/>- PACS Viewer: W/L adjustment (mouse drag), Zoom (scroll), Pan (middle mouse), Measure (Ctrl+M), Reset (R), Next/Prev series (←→) |
| **Tiêu chí chấp nhận** | 1. Phím tắt hoạt động nhất quán trên tất cả module<br/>2. Có bảng phím tắt (help overlay) khi nhấn "?" |

### NFR-USE-003: Đa ngôn ngữ & Unicode

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giao diện tiếng Việt, hỗ trợ Unicode đầy đủ |
| **Yêu cầu** | - Giao diện mặc định: Tiếng Việt<br/>- Database: UTF-8 (hỗ trợ tên BN có dấu, ký tự đặc biệt)<br/>- DICOM Patient Name: hỗ trợ Unicode (ISO IR 192)<br/>- HL7: UTF-8 encoding trong MSH-18<br/>- [GĐ3] Hỗ trợ English interface (chuyển đổi) |
| **Tiêu chí chấp nhận** | 1. Tên BN có dấu (VD: Nguyễn Văn Á) hiển thị đúng trên HIS, PACS, và báo cáo in<br/>2. DICOM worklist: tên BN Unicode hiển thị đúng trên modality |

### NFR-USE-004: Responsive Design

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | HIS web hỗ trợ responsive cho tablet (bác sĩ đi buồng nội trú) |
| **Breakpoints** | Desktop (≥ 1280px): full layout; Tablet (768–1279px): simplified layout; Mobile: không hỗ trợ |
| **Tiêu chí chấp nhận** | 1. Tablet (iPad 10.2"): đọc bệnh án, xem kết quả, ghi y lệnh → usable<br/>2. PACS Viewer: chỉ desktop (≥ 1280px, khuyến cáo 1920px) |

---

## F.7. Khả năng Bảo trì (Maintainability)

### NFR-MAIN-001: Code Documentation

| Hạng mục | Chi tiết |
|----------|---------|
| **Yêu cầu** | - API documentation: OpenAPI 3.0 / Swagger<br/>- Code: JSDoc / XML comments cho public methods<br/>- Architecture decision records (ADR)<br/>- Database schema documentation |
| **Tiêu chí chấp nhận** | 1. API docs tự sinh từ code (Swagger UI)<br/>2. Developer mới hiểu module trong ≤ 2 ngày đọc docs |

### NFR-MAIN-002: Modular Architecture

| Hạng mục | Chi tiết |
|----------|---------|
| **Yêu cầu** | - Modular monolith hoặc microservices (quyết định ở design phase)<br/>- Mỗi module có boundary rõ ràng (API contract)<br/>- Module có thể deploy/update độc lập (nếu microservices) |
| **Tiêu chí chấp nhận** | 1. Update module Dược → không ảnh hưởng module PACS<br/>2. Build time 1 module ≤ 5 phút |

### NFR-MAIN-003: Configuration Management

| Hạng mục | Chi tiết |
|----------|---------|
| **Yêu cầu** | - Tất cả config qua file/environment variables/database (0 hardcode)<br/>- Config thay đổi không cần rebuild<br/>- Secret config encrypted (DB password, API key, certificate) |
| **Tiêu chí chấp nhận** | 1. Đổi port HL7 → thay config, restart service, không rebuild<br/>2. Grep source code "password" → 0 plaintext credential |

### NFR-MAIN-004: Rolling Update

| Hạng mục | Chi tiết |
|----------|---------|
| **Yêu cầu** | - HIS web app: rolling update (deploy instance mới, drain cũ) — 0 downtime<br/>- Database migration: forward-compatible (support N and N-1 app version)<br/>- PACS: maintenance window cho major update (thông báo trước 48h) |
| **Tiêu chí chấp nhận** | 1. Deploy HIS update → 0 user bị disconnect<br/>2. Database migration rollback plan tested |

---

## F.8. Giám sát và Vận hành (Monitoring & Operations)

### NFR-MON-001: Application Monitoring

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giám sát sức khỏe application và infrastructure |
| **Metrics** | - CPU, Memory, Disk I/O, Network per server<br/>- Application response time (P50, P95, P99)<br/>- Active sessions count<br/>- Error rate (HTTP 5xx / total requests)<br/>- Database connection pool usage<br/>- Message queue depth + consumer lag |
| **Tools** | Prometheus + Grafana (khuyến cáo) hoặc Zabbix + custom dashboard |
| **Alert Rules** | - CPU > 80% sustained 5 min → warning<br/>- Memory > 90% → critical<br/>- Disk > 85% → warning; > 95% → critical<br/>- Error rate > 1% → warning; > 5% → critical<br/>- DB connection pool > 80% → warning |
| **Tiêu chí chấp nhận** | 1. Dashboard real-time cập nhật ≤ 30 giây<br/>2. Alert gửi email + SMS trong ≤ 5 phút<br/>3. Lưu metrics history ≥ 90 ngày |

### NFR-MON-002: Log Management

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thu thập và quản lý log tập trung |
| **Yêu cầu** | - Centralized logging: ELK Stack (Elasticsearch + Logstash + Kibana) hoặc Loki + Grafana<br/>- Log format: JSON structured (timestamp, level, service, trace_id, message, context)<br/>- Log levels: ERROR, WARN, INFO, DEBUG (configurable per module)<br/>- Correlation ID (trace_id) xuyên suốt request chain (HIS → Integration → PACS) |
| **Retention** | - ERROR/WARN: 90 ngày<br/>- INFO: 30 ngày<br/>- DEBUG: 7 ngày (chỉ bật khi troubleshoot) |
| **Tiêu chí chấp nhận** | 1. Tìm log theo trace_id → hiển thị toàn bộ request chain trong ≤ 5 giây<br/>2. Search log 30 ngày → kết quả trong ≤ 10 giây |

### NFR-MON-003: Health Check Endpoints

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Mỗi service cung cấp health check endpoint cho monitoring |
| **Endpoints** | - `/health/live`: service running (HTTP 200)<br/>- `/health/ready`: service ready to serve (DB connected, dependencies OK)<br/>- `/health/detail`: chi tiết status từng dependency (DB, Redis, PACS, queue) |
| **Tiêu chí chấp nhận** | 1. DB down → `/health/ready` trả HTTP 503 + chi tiết<br/>2. Load balancer sử dụng `/health/ready` cho routing |

---

## F.9. Bảng Tổng hợp NFR và Metrics

| Mã | Danh mục | Metric chính | Target | Phương pháp đo |
|----|---------|-------------|--------|----------------|
| NFR-PERF-001 | Performance | HIS response time | P95 ≤ 3s | Load test (JMeter/k6) |
| NFR-PERF-002 | Performance | DICOM TTFI (single) | ≤ 5s | Stopwatch test |
| NFR-PERF-003 | Performance | DICOM full load (CT 200) | ≤ 15s | Stopwatch test |
| NFR-PERF-004 | Performance | HL7 throughput | ≥ 50 msg/s | Stress test |
| NFR-PERF-005 | Performance | C-STORE throughput | ≥ 50 img/s | DICOM stress test |
| NFR-PERF-006 | Performance | Concurrent users | ≥ 200 | Load test |
| NFR-PERF-007 | Performance | CĐHA cases/day | ≥ 500 | E2E simulation |
| NFR-PERF-008 | Performance | DB query time | Varies per query | Query profiling |
| NFR-SCAL-001 | Scalability | HIS horizontal scale | Near-linear | Scale test |
| NFR-SCAL-002 | Scalability | PACS storage scale | Online expand | Operations test |
| NFR-SCAL-003 | Scalability | Multi-site | 5 → 10 sites | Config test |
| NFR-AVAIL-001 | Availability | HIS uptime | ≥ 99.5% | Monthly monitoring |
| NFR-AVAIL-002 | Availability | PACS uptime | ≥ 99.9% | Monthly monitoring |
| NFR-AVAIL-003 | Availability | RPO | HIS ≤1h, PACS ≤15m | DR drill |
| NFR-AVAIL-004 | Availability | RTO | HIS ≤4h, PACS ≤2h | DR drill |
| NFR-SEC-001 | Security | Encryption transit | TLS 1.2+ | SSL scan |
| NFR-SEC-003 | Security | Auth + MFA | 5 fail → lock | Pen test |
| NFR-SEC-006 | Security | Audit coverage | 100% CRUD | Audit test |
| NFR-COMP-001 | Compliance | DICOM conformance | Full conformance | Interop test |
| NFR-COMP-004 | Compliance | BHXH XML | 100% accepted | Submission test |
| NFR-USE-001 | Usability | Training time | ≤ 8h basic | User survey |
| NFR-MAIN-004 | Maintainability | Zero-downtime deploy | 0 disconnect | Deploy test |
| NFR-MON-001 | Monitoring | Alert latency | ≤ 5 min | Monitoring test |

---

*Tiếp theo: [Phần G — Triển khai và Vận hành](./G-TRIEN-KHAI-VAN-HANH.md)*
