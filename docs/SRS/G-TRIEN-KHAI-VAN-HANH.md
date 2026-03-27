# PHẦN G — TRIỂN KHAI VÀ VẬN HÀNH

> **Mã phần**: SRS-PART-G  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## G.1. Môi trường Triển khai

### G.1.1. Tổng quan các môi trường

| Môi trường | Mục đích | Hạ tầng | Dữ liệu | Quản lý bởi |
|------------|---------|---------|---------|-------------|
| **DEV** | Phát triển, unit test | VM/Container (cloud hoặc on-prem dev) | Dữ liệu giả (seed data) | Đội phát triển |
| **SIT** (System Integration Test) | Test tích hợp HIS ↔ PACS ↔ BHXH | On-prem hoặc VM (spec = 50% PROD) | Dữ liệu giả + sample DICOM | Đội phát triển + QA |
| **UAT** (User Acceptance Test) | Nghiệm thu bởi end-user BV | On-prem (spec = 70% PROD) | Dữ liệu giả mô phỏng thực tế (anonymized) | QA + Đại diện BV |
| **STAGING** | Pre-production, final verification | On-prem (spec = 100% PROD) | Bản sao dữ liệu PROD (anonymized) | DevOps |
| **PROD** | Production (vận hành thực) | On-prem (full spec) | Dữ liệu thực | Đội vận hành + BV |

### G.1.2. Đặc tả Hạ tầng PROD

#### HIS Servers

| Component | Số lượng | CPU | RAM | Disk | OS | Ghi chú |
|-----------|---------|-----|-----|------|----|---------|
| HIS Web App (Active-Active) | 2 | 8 vCPU | 32 GB | 100 GB SSD | Ubuntu 22.04 LTS / Windows Server 2022 | Behind LB |
| HIS Database (Primary) | 1 | 16 vCPU | 64 GB | 1 TB SSD (RAID 10) | Ubuntu 22.04 LTS | PostgreSQL 15+ |
| HIS Database (Standby) | 1 | 16 vCPU | 64 GB | 1 TB SSD (RAID 10) | Ubuntu 22.04 LTS | Streaming replication |
| Redis Cache (Cluster) | 3 | 4 vCPU | 16 GB | 50 GB SSD | Ubuntu 22.04 LTS | Session + cache |
| Integration Engine | 2 | 8 vCPU | 16 GB | 200 GB SSD | Ubuntu 22.04 LTS | Mirth Connect / Custom |
| Message Queue (Cluster) | 3 | 4 vCPU | 8 GB | 100 GB SSD | Ubuntu 22.04 LTS | RabbitMQ clustered |
| Load Balancer | 2 | 4 vCPU | 8 GB | 50 GB SSD | Ubuntu 22.04 LTS | HAProxy Active-Passive |

#### PACS Servers

| Component | Số lượng | CPU | RAM | Disk | OS | Ghi chú |
|-----------|---------|-----|-----|------|----|---------|
| PACS Application | 2 | 16 vCPU | 64 GB | 200 GB SSD | Ubuntu 22.04 LTS | Active-Passive (GĐ1); Active-Active (GĐ2) |
| PACS Database | 2 | 8 vCPU | 32 GB | 500 GB SSD (RAID 10) | Ubuntu 22.04 LTS | Primary + Standby |
| DICOM Storage — HOT | 1 | 8 vCPU | 16 GB | 24 TB SSD/NVMe (RAID 6) | Ubuntu 22.04 LTS | ~2 năm data (12 TB/năm) |
| DICOM Storage — WARM | 1 | 4 vCPU | 8 GB | 60 TB HDD (RAID 6) | Ubuntu 22.04 LTS | ~5 năm data |
| DICOM Storage — COLD | 1 | 4 vCPU | 8 GB | 120 TB (NAS/Object) | — | MinIO hoặc NAS |
| PACS Web Viewer | 2 | 8 vCPU | 32 GB | 100 GB SSD | Ubuntu 22.04 LTS | Behind LB, WebGL rendering |

#### PACS Satellite (per cơ sở vệ tinh × 4)

| Component | Số lượng mỗi site | CPU | RAM | Disk |
|-----------|-------------------|-----|-----|------|
| PACS Node (Cache + MWL) | 1 | 8 vCPU | 32 GB | 4 TB SSD |
| DICOM Router | 1 | 4 vCPU | 8 GB | 100 GB SSD |

#### Mạng

| Hạng mục | Yêu cầu |
|----------|---------|
| LAN backbone (Data center) | ≥ 10 Gbps |
| LAN access (user) | ≥ 1 Gbps |
| WAN (Hub ↔ Spoke) | ≥ 100 Mbps dedicated, ≤ 20ms latency |
| Internet | ≥ 50 Mbps (cho BHXH API, update, remote access) |
| VLAN | Tách VLAN: Servers, DICOM Modality, User, Management |
| Firewall | Chỉ mở port cần thiết: 443 (HTTPS), 2575 (HL7), 11112 (DICOM), 5432 (DB — nội bộ) |

### G.1.3. Phần mềm hệ thống

| Layer | Lựa chọn | Phiên bản tối thiểu | Ghi chú |
|-------|---------|---------------------|---------|
| OS | Ubuntu 22.04 LTS hoặc Windows Server 2022 | — | LTS support |
| Database | PostgreSQL | 15+ | Hoặc SQL Server 2019+ (tùy BV) |
| Application Runtime | .NET 8 LTS hoặc Java 17 LTS | — | Quyết định ở design phase |
| Web Server | Nginx / Kestrel | — | Reverse proxy + LB |
| Cache | Redis | 7+ | Clustered |
| Message Queue | RabbitMQ | 3.12+ | Hoặc Kafka 3.x |
| DICOM Server | Orthanc / dcm4chee / Custom | — | Phải có DICOM Conformance Statement |
| Integration Engine | Mirth Connect (NextGen) | 4.x | Hoặc custom HL7 adapter |
| Monitoring | Prometheus + Grafana | — | Hoặc Zabbix |
| Logging | ELK Stack / Loki + Grafana | — | Centralized |
| Backup | pgBackRest (PostgreSQL) / custom | — | + rsync cho DICOM files |

---

## G.2. Chiến lược Data Migration (Di chuyển Dữ liệu)

### G.2.1. Phạm vi migration

| Nguồn | Dữ liệu | Phương pháp | Ưu tiên | Ghi chú |
|-------|---------|------------|---------|---------|
| HIS cũ (nếu có) | Danh mục bệnh nhân (demographics) | ETL batch | Must | Cleanse + deduplicate trước khi import |
| HIS cũ | Lịch sử khám (visit history) | ETL batch | Should | Chỉ import 3 năm gần nhất |
| HIS cũ | Bệnh án nội trú đang nằm | ETL + manual verify | Must | Phải chính xác 100% |
| HIS cũ | Danh mục thuốc, dịch vụ, bảng giá | ETL mapping | Must | Mapping mã cũ → mã mới |
| BHXH | Danh mục cơ sở KCB, ICD-10, thuốc BHYT | Import từ nguồn chính thức | Must | Download từ cổng BHXH |
| PACS cũ (nếu có) | DICOM studies | DICOM C-MOVE/C-GET | Should | Chỉ import 3 năm gần nhất; sử dụng background migration |
| File | Ảnh chân dung BN, scan giấy tờ | File copy + link DB | Could | Nếu BV có |

### G.2.2. Quy trình Migration

```
Phase 1: CHUẨN BỊ (T-8 tuần → T-4 tuần)
├── Phân tích dữ liệu nguồn (schema, quality, volume)
├── Xây dựng mapping rules (mã cũ → mã mới)
├── Phát triển ETL scripts
├── Chuẩn bị môi trường staging
└── Test migration trên môi trường SIT

Phase 2: MIGRATION THỬ (T-4 tuần → T-2 tuần)
├── Migration #1 (Dry Run): chạy full migration trên STAGING
├── Verify: kiểm tra dữ liệu (sampling 10% + spot check)
├── Fix issues, adjust scripts
├── Migration #2 (Dry Run): chạy lại, verify
└── Sign-off dry run bởi BV

Phase 3: MIGRATION THỰC (T-1 tuần → Go-Live)
├── Freeze dữ liệu HIS cũ (hoặc delta sync)
├── Final migration run
├── Full verification (automated + manual)
├── BV sign-off dữ liệu migrated
└── Switch traffic sang hệ thống mới

Phase 4: DELTA SYNC (Go-Live → T+2 tuần)
├── Sync dữ liệu phát sinh trên HIS cũ (nếu parallel run)
├── Reconciliation report
└── Đóng HIS cũ (archive)
```

### G.2.3. Tiêu chí chấp nhận Migration

| # | Tiêu chí | Target | Phương pháp kiểm tra |
|---|---------|--------|---------------------|
| 1 | Bệnh nhân: số lượng migrated = nguồn (sau deduplicate) | 100% | Count + sampling 5% manual verify |
| 2 | Bệnh nhân: không mất trường bắt buộc (tên, DOB, gender) | 100% | Null check query |
| 3 | DICOM studies: accession number match với order HIS | ≥ 98% | Reconciliation query |
| 4 | Danh mục thuốc: mapping BHXH code chính xác | 100% | Cross-check với danh mục BHXH official |
| 5 | Lịch sử khám: ICD-10 code hợp lệ | ≥ 95% | Validate against ICD-10 catalog |
| 6 | Migration time: tổng thời gian cutover migration | ≤ 8 giờ (1 ca đêm) | Timing |

---

## G.3. Kế hoạch Cutover (Go-Live)

### G.3.1. Timeline Cutover

```
T-7 ngày: Go/No-Go meeting
├── Review UAT results, open issues, migration readiness
├── Quyết định Go hoặc Rollback date
└── Thông báo toàn BV

T-2 ngày: Pre-cutover preparation
├── Final backup HIS cũ
├── Deploy PROD environment (nếu chưa)
├── Smoke test PROD environment
└── Verify tất cả integration connections

T-1 ngày (Thứ 7 tối): Cutover Window [20:00 – 06:00]
├── 20:00 — Freeze HIS cũ (read-only mode)
├── 20:30 — Start final data migration
├── 02:00 — Migration complete → verification
├── 03:00 — Verification complete → switch DNS/LB
├── 04:00 — Smoke test hệ thống mới (core workflows)
├── 05:00 — Mở hệ thống cho pilot users
└── 06:00 — Go-Live (Chủ nhật sáng — lượng KCB thấp)

T+0 (Chủ nhật): Go-Live Day
├── War room: đội dev + infra + BV trực tại chỗ
├── Support desk mở 7:00 – 22:00
├── Monitor tất cả metrics real-time
└── Escalation: issue critical → rollback trong 2 giờ (nếu cần)

T+1 → T+5 (Tuần đầu): Hypercare
├── Đội support on-site 12 giờ/ngày
├── Daily standup: review issues, fixes
├── Hotfix deploy nếu cần (rolling update)
└── Daily report cho BV leadership
```

### G.3.2. Rollback Plan

| Điều kiện rollback | Hành động |
|-------------------|----------|
| Data migration failed > 5% error | Dừng migration, sử dụng HIS cũ |
| Core workflow broken (tiếp đón/khám/kê đơn) | Switch LB về HIS cũ trong ≤ 30 phút |
| PACS không nhận ảnh (C-STORE fail) | Chuyển modality về PACS cũ (nếu có) hoặc local storage |
| Integration HIS↔PACS broken | Chạy standalone mode: HIS + PACS riêng, manual worklist |
| Performance degradation > 5× baseline | Scale up resources hoặc rollback nếu không fix trong 2 giờ |

**Rollback SLA**: Quyết định rollback trong ≤ 2 giờ kể từ khi phát hiện issue critical.

---

## G.4. Kế hoạch Đào tạo

### G.4.1. Tổng quan chương trình đào tạo

| Đợt | Đối tượng | Nội dung | Thời lượng | Thời điểm | Phương pháp |
|-----|----------|---------|------------|-----------|-------------|
| 1 | IT BV + Admin | Quản trị hệ thống, cấu hình, monitoring, backup/restore | 3 ngày (24h) | T-6 tuần | Workshop + Hands-on |
| 2 | Super Users (đại diện mỗi khoa) | Tất cả module HIS + PACS liên quan | 3 ngày (24h) | T-4 tuần | Workshop + Hands-on |
| 3 | Tiếp đón + Viện phí | Tiếp đón, BHYT, viện phí | 1 ngày (8h) | T-3 tuần | Class + UAT environment |
| 4 | Bác sĩ (OPD/IPD) | Khám bệnh, kê đơn, chỉ định CLS, xem KQ | 1 ngày (8h) | T-3 tuần | Class + UAT environment |
| 5 | Điều dưỡng | Module nội trú, y lệnh, chăm sóc | 1 ngày (8h) | T-3 tuần | Class + UAT environment |
| 6 | Dược sĩ | Phát thuốc, quản lý kho | 1 ngày (8h) | T-3 tuần | Class + UAT environment |
| 7 | KTV CĐHA | PACS worklist, vận hành modality | 0.5 ngày (4h) | T-2 tuần | Hands-on + thực tế |
| 8 | BS CĐHA (Radiologist) | PACS Viewer, tools đọc ảnh, viết kết quả | 2 ngày (16h) | T-2 tuần | Hands-on + ca lâm sàng |
| 9 | Ban giám đốc | Dashboard, báo cáo, KPIs | 0.5 ngày (4h) | T-1 tuần | Demo + Q&A |

### G.4.2. Tài liệu đào tạo

| Loại | Nội dung | Format | Ngôn ngữ |
|------|---------|--------|----------|
| User Manual | Hướng dẫn sử dụng từng module (có screenshot) | PDF + Online (built-in help) | Tiếng Việt |
| Quick Reference Card | Cheat sheet phím tắt + quy trình nhanh (1 trang A4) | PDF (laminated) | Tiếng Việt |
| Video Tutorial | Video hướng dẫn từng workflow chính (3-5 phút/video) | MP4 (nội bộ) | Tiếng Việt |
| FAQ | Câu hỏi thường gặp + troubleshooting | Online Wiki | Tiếng Việt |
| Admin Guide | Hướng dẫn quản trị: cài đặt, cấu hình, backup, monitoring | PDF + Online | Tiếng Việt + English |

### G.4.3. Tiêu chí đào tạo thành công

| # | Tiêu chí | Target |
|---|---------|--------|
| 1 | Tỷ lệ tham dự đào tạo | ≥ 90% user liên quan |
| 2 | Bài kiểm tra cuối khóa (quiz) | ≥ 80% đạt (score ≥ 70/100) |
| 3 | Thực hành trên UAT: hoàn thành scenario chuẩn | 100% super user hoàn thành |
| 4 | Super user có khả năng hỗ trợ đồng nghiệp | Đánh giá qua mock support |

---

## G.5. Hỗ trợ Sau Go-Live

### G.5.1. Giai đoạn hỗ trợ

| Giai đoạn | Thời gian | Mức hỗ trợ | Nhân sự |
|-----------|----------|-----------|---------|
| **Hypercare** | T+0 → T+2 tuần | On-site 12h/ngày (7:00–19:00) + remote ngoài giờ | 4-6 kỹ sư (2 HIS, 2 PACS, 1 infra, 1 HL7/integration) |
| **Stabilization** | T+2 → T+8 tuần | On-site 8h/ngày (giờ hành chính) + remote ngoài giờ | 2-3 kỹ sư |
| **Warranty** | T+8 → T+24 tuần (6 tháng) | Remote support, on-site khi cần | 1-2 kỹ sư (remote) |
| **Maintenance** | Sau warranty | Theo hợp đồng bảo trì | Theo SLA |

### G.5.2. SLA Hỗ trợ

| Mức độ | Mô tả | Response Time | Resolution Time | Ví dụ |
|--------|-------|---------------|-----------------|-------|
| **P1 — Critical** | Hệ thống không hoạt động, ảnh hưởng toàn BV | ≤ 15 phút | ≤ 4 giờ | HIS down, PACS không nhận ảnh |
| **P2 — High** | Module chính bị lỗi, ảnh hưởng 1 khoa/phòng | ≤ 30 phút | ≤ 8 giờ | Viện phí không tính đúng BHYT |
| **P3 — Medium** | Lỗi chức năng không chính, workaround có | ≤ 2 giờ | ≤ 24 giờ | Báo cáo sai format |
| **P4 — Low** | Enhancement request, cosmetic | ≤ 8 giờ | Theo sprint/release | Thêm cột báo cáo |

### G.5.3. Quy trình tiếp nhận và xử lý sự cố

```
┌───────────────────────────────────────────────────┐
│                  User Report Issue                  │
│     (Hotline / Email / Ticket System / Chat)        │
└──────────────────────┬────────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│           L1: Service Desk / Super User           │
│    ├── Known issue? → Hướng dẫn / workaround     │
│    ├── Config issue? → Fix config                 │
│    └── Unknown? → Escalate L2                     │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│           L2: Application Support Team            │
│    ├── Bug confirmed? → Hotfix + deploy           │
│    ├── Integration issue? → Debug HL7/DICOM log   │
│    └── Complex? → Escalate L3                     │
└──────────────────────┬───────────────────────────┘
                       ▼
┌──────────────────────────────────────────────────┐
│           L3: Development Team / Vendor            │
│    ├── Root cause analysis                        │
│    ├── Fix + test + deploy                        │
│    └── Post-mortem report                         │
└──────────────────────────────────────────────────┘
```

---

## G.6. Vận hành Định kỳ

### G.6.1. Checklist Vận hành Hàng ngày

| # | Task | Thời gian | Người thực hiện | Ghi chú |
|---|------|----------|----------------|---------|
| 1 | Kiểm tra dashboard monitoring (Grafana) | 8:00 | SysAdmin | CPU, RAM, disk, error rate |
| 2 | Kiểm tra backup đêm qua thành công | 8:00 | SysAdmin | Verify backup log |
| 3 | Kiểm tra DLQ (Dead-Letter Queue) | 8:30 | Integration Admin | Replay hoặc investigate |
| 4 | Kiểm tra dung lượng PACS storage | 8:30 | PACS Admin | Alert nếu < 20% free |
| 5 | Kiểm tra kết nối BHXH | 9:00 | BHYT Staff | Verify API connection |
| 6 | Review audit log bất thường | 16:00 | Security Admin | Failed logins, unusual access |

### G.6.2. Checklist Vận hành Hàng tuần

| # | Task | Ngày | Người thực hiện |
|---|------|------|----------------|
| 1 | DICOM integrity check (checksum) | Chủ nhật 02:00 | Auto (cron) |
| 2 | Review integration reconciliation report | Thứ 2 | Integration Admin |
| 3 | Review performance metrics trend | Thứ 2 | SysAdmin |
| 4 | Cleanup temp files, old logs | Chủ nhật 03:00 | Auto (cron) |
| 5 | Test restore 1 sample backup | Thứ 7 | SysAdmin |

### G.6.3. Checklist Vận hành Hàng tháng/quý

| # | Task | Tần suất | Người thực hiện |
|---|------|----------|----------------|
| 1 | Cập nhật OS patches (security) | Hàng tháng | SysAdmin |
| 2 | Cập nhật danh mục BHXH (nếu có thay đổi) | Khi có thông tư mới | BHYT Staff + Admin |
| 3 | DR drill (disaster recovery test) | Hàng quý | SysAdmin + DevOps |
| 4 | Performance baseline test (regression) | Hàng quý | QA + DevOps |
| 5 | Security audit / vulnerability scan | Hàng quý | Security Admin |
| 6 | DICOM storage tier migration review | Hàng tháng | PACS Admin |
| 7 | Certificate renewal check (SSL/TLS) | Hàng tháng | SysAdmin |
| 8 | User access review (deactivate departed staff) | Hàng tháng | HR + SysAdmin |

---

*Tiếp theo: [Phần H — Kế hoạch Release](./H-KE-HOACH-RELEASE.md)*
