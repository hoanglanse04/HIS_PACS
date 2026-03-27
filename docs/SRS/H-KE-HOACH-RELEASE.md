# PHẦN H — KẾ HOẠCH RELEASE

> **Mã phần**: SRS-PART-H  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026

---

## H.1. Tổng quan Chiến lược Release

### H.1.1. Phân chia 3 Giai đoạn

| Giai đoạn | Tên | Thời lượng ước tính | Mục tiêu chính |
|-----------|-----|---------------------|---------------|
| **GĐ1** | MVP — Core Operations | 9-12 tháng | Thay thế quy trình giấy, tích hợp HIS↔PACS cơ bản, liên thông BHXH |
| **GĐ2** | Expansion — Extended Features | 6-9 tháng (sau GĐ1) | Tích hợp LIS, nâng cao PACS, hóa đơn điện tử, SMS/Email BN |
| **GĐ3** | Optimization — Advanced | 6-9 tháng (sau GĐ2) | FHIR API, AI/ML hỗ trợ, offline mode, advanced analytics |

### H.1.2. Timeline tổng quan

```
       GĐ1 (MVP)              GĐ2 (Expansion)         GĐ3 (Optimization)
├─────────────────────────┤──────────────────────┤──────────────────────┤
  M1        M6        M12   M13       M18    M21   M22       M27    M30
  │          │          │     │         │      │     │         │      │
  Kickoff  Alpha    Go-Live  Start   Beta  Go-Live  Start   Beta  Go-Live
            SIT       GĐ1    GĐ2    GĐ2    GĐ2    GĐ3    GĐ3    GĐ3
```

---

## H.2. GĐ1 — MVP (Minimum Viable Product)

### H.2.1. Phạm vi GĐ1

#### Module HIS — In Scope GĐ1

| Module | Mã | Phạm vi GĐ1 | Ưu tiên |
|--------|-----|-------------|---------|
| Quản trị hệ thống | HIS-SYS | Đầy đủ: RBAC, danh mục, SSO, audit log | Must |
| Tiếp đón / Cấp cứu | HIS-ER, HIS-REG | Đầy đủ: đăng ký BN, BHYT, hàng đợi, nhập viện, ra viện | Must |
| Khám ngoại trú | HIS-OPD | Đầy đủ: danh sách chờ, khám, kê đơn, chỉ định CLS, xem KQ | Must |
| Chỉ định CLS | HIS-CLS | Chỉ định CĐHA → PACS (HL7 ORM), nhận KQ (HL7 ORU) | Must |
| Nội trú | HIS-IPD | Core: quản lý giường, bệnh án, y lệnh, chỉ định nội trú | Must |
| Phẫu thuật / Thủ thuật | HIS-SUR | Core: lịch phẫu thuật, phiếu phẫu thuật, ghi nhận kết quả | Must |
| Bệnh án điện tử | HIS-EMR | Core: tạo bệnh án, ghi diễn biến, tóm tắt ra viện | Must |
| Dược | HIS-PHA | Đầy đủ: danh mục, kho, phát thuốc ngoại trú + nội trú | Must |
| Viện phí | HIS-BIL | Đầy đủ: bảng giá, tính phí tự động, thu phí, quyết toán | Must |
| Nhà thuốc bệnh viện | HIS-RPH | Core: nhập kho, xuất kho, kiểm kê, FEFO, cảnh báo hạn | Must |
| Bảo hiểm y tế | HIS-INS | Đầy đủ: kiểm tra thẻ, áp dụng chính sách, XML 4210, đối soát | Must |
| Báo cáo thống kê | HIS-RPT | Core: báo cáo hoạt động BV, báo cáo BHXH bắt buộc, dashboard | Must |
| Lập kế hoạch / Hẹn khám | HIS-PLN | Core: đặt lịch hẹn, nhắc lịch | Should |

#### Module RIS/PACS — In Scope GĐ1

| Module | Mã | Phạm vi GĐ1 | Ưu tiên |
|--------|-----|-------------|---------|
| Quản trị RIS | RIS-ADM | Đầy đủ: cấu hình modality, danh mục dịch vụ, phòng chụp | Must |
| Quản lý bệnh nhân PACS | RIS-PAT | Đầy đủ: nhận ADT, matching, merge | Must |
| Kết nối Modality | RIS-MOD | Đầy đủ: C-STORE SCP, MPPS, auto-routing | Must |
| Worklist | RIS-WL | Đầy đủ: MWL SCP, order management, status tracking | Must |
| Lưu trữ | RIS-STD | Core: tiered storage (HOT + WARM), query, retrieve, WADO-RS | Must |
| Viewer | RIS-VWR | Core: web viewer zero-footprint, tools cơ bản, hanging protocol, cine | Must |
| Báo cáo kết quả | RIS-RPT | Đầy đủ: template, soạn KQ, duyệt, ký số, gửi ORU | Must |
| Đồng bộ | RIS-SYN | Core: sync HIS↔PACS, reconciliation cơ bản | Must |

#### Tích hợp — In Scope GĐ1

| Kênh | Mã | GĐ1 |
|------|-----|------|
| HIS ↔ PACS (HL7 + DICOM) | FR-INT-HL7-001/002/003, FR-INT-DCM-001/002/003 | ✅ Đầy đủ |
| HIS ↔ BHXH | FR-INT-BHXH-001/002/003 | ✅ Đầy đủ |
| Chữ ký số | FR-INT-SIGN-001/002 | ✅ Đầy đủ |
| Notification real-time | FR-INT-NOTI-001 | ✅ Đầy đủ |
| Integration monitoring | FR-INT-MON-001 | ✅ Đầy đủ |

### H.2.2. Out of Scope GĐ1

| Hạng mục | Lý do | Chuyển sang |
|----------|-------|-------------|
| Tích hợp LIS | Cần thêm phân tích LIS vendor | GĐ2 |
| SMS/Email cho bệnh nhân | Nice-to-have, không blocking | GĐ2 |
| Hóa đơn điện tử | Cần chọn vendor, ký hợp đồng | GĐ2 |
| PACS Viewer nâng cao (MPR, 3D) | Cần WebGL engine phức tạp | GĐ2 |
| COLD/ARCHIVE tier storage | Chưa cần trong năm đầu | GĐ2 |
| PACS Active-Active | Active-Passive đủ cho GĐ1 | GĐ2 |
| Telemedicine / Remote Reading | Ngoài scope ban đầu | GĐ3 |
| FHIR R4 API | Chưa có use case cấp bách | GĐ3 |
| Offline mode multi-site | Phức tạp, cần thêm thiết kế | GĐ3 |
| AI/ML hỗ trợ chẩn đoán | Cần research + dataset | GĐ3 |
| Module HR (nhân sự) | Có thể dùng hệ thống HR riêng | GĐ2 |
| Module tài sản (AST) | Có thể dùng hệ thống riêng | GĐ2 |
| Telehealth (TLC) | Scope mở rộng | GĐ3 |
| Helpdesk tích hợp (HLP) | Nice-to-have | GĐ3 |
| Module khác (OTH) | Đánh giá sau GĐ1 | GĐ2/GĐ3 |

### H.2.3. Milestones GĐ1

| Milestone | Thời điểm | Deliverable | Exit Criteria |
|-----------|----------|-------------|---------------|
| M1.1 — Kickoff | M1 | Project charter, team, environments | Sign-off |
| M1.2 — Design Complete | M3 | Architecture doc, DB design, UI wireframe, API spec | Review + approve |
| M1.3 — Sprint Demo 1 (HIS Core) | M4 | Tiếp đón, OPD, IPD — demo trên SIT | BV feedback incorporated |
| M1.4 — Sprint Demo 2 (PACS + Integration) | M6 | PACS workflow E2E, HL7 integration | Integration test pass |
| M1.5 — Sprint Demo 3 (Billing + BHXH) | M7 | Viện phí, BHXH XML, chữ ký số | BHXH test submission success |
| M1.6 — Alpha (SIT Complete) | M8 | Full system integration tested | SIT pass ≥ 95% test cases |
| M1.7 — Beta (UAT Start) | M9 | System deployed on UAT, user training starts | UAT environment ready |
| M1.8 — UAT Complete | M11 | All UAT scenarios passed, user sign-off | UAT pass ≥ 90%, 0 P1 open |
| M1.9 — Go-Live GĐ1 | M12 | Production deployment, data migration, hypercare | Go-Live checklist complete |

---

## H.3. GĐ2 — Expansion

### H.3.1. Phạm vi GĐ2

| Module / Feature | Mã | Mô tả | Ưu tiên |
|-----------------|-----|-------|---------|
| Tích hợp LIS | FR-INT-LIS-001/002 | Chỉ định XN → LIS, nhận KQ XN | Must |
| Hóa đơn điện tử | FR-INT-EINV-001 | Tích hợp e-Invoice vendor | Should |
| SMS/Email BN | FR-INT-NOTI-002 | Thông báo KQ, nhắc lịch tái khám | Should |
| PACS Viewer nâng cao | FR-RIS-VWR-002 (mở rộng) | MPR, MIP, comparison study, key image | Must |
| PACS Active-Active | NFR-AVAIL-006 (nâng cấp) | HA cho PACS application | Should |
| COLD/ARCHIVE storage | DR-040 (mở rộng) | Tier 3 + Tier 4 storage, auto-migration | Must |
| Module HR | HIS-HR | Quản lý nhân sự, lịch trực, chấm công cơ bản | Should |
| Module Tài sản | HIS-AST | Quản lý trang thiết bị y tế, bảo trì | Could |
| DICOM ATNA audit | NFR-SEC-007 (mở rộng) | IHE ATNA profile cho DICOM audit | Should |
| Báo cáo nâng cao | HIS-RPT (mở rộng) | Thêm báo cáo phân tích, custom report builder | Should |
| Kho thuốc nâng cao | HIS-RPH (mở rộng) | Multi-kho, chuyển kho liên cơ sở, thầu thuốc | Should |

### H.3.2. Milestones GĐ2

| Milestone | Thời điểm | Deliverable |
|-----------|----------|-------------|
| M2.1 — GĐ2 Kickoff + Planning | M13 | Scope confirm, backlog prioritized |
| M2.2 — LIS Integration Complete | M15 | LIS↔HIS tested with LIS vendor |
| M2.3 — PACS Advanced Viewer | M17 | MPR, MIP, comparison tested |
| M2.4 — Beta GĐ2 | M18 | UAT for new features |
| M2.5 — Go-Live GĐ2 | M21 | Rolling deployment, no downtime |

---

## H.4. GĐ3 — Optimization

### H.4.1. Phạm vi GĐ3

| Module / Feature | Mã | Mô tả | Ưu tiên |
|-----------------|-----|-------|---------|
| FHIR R4 API | FR-INT-FHIR-001 | RESTful API cho liên thông y tế, Patient/ImagingStudy/DiagnosticReport | Should |
| Offline mode (multi-site) | DR-030 (mở rộng) | Ghi cục bộ + sync khi có kết nối cho cơ sở vệ tinh | Could |
| AI/ML hỗ trợ CĐHA | — (mới) | Tích hợp AI model: phát hiện bất thường X-quang, CT | Could |
| Telehealth / Remote Reading | HIS-TLC | Tư vấn từ xa, đọc ảnh từ xa (VPN + viewer) | Could |
| Patient Portal | — (mới) | BN tra cứu KQ, lịch hẹn, thanh toán online | Could |
| IHE XDS-I.b | NFR-COMP-003 (mở rộng) | Chia sẻ ảnh CĐHA giữa các BV (cross-enterprise) | Could |
| Advanced Analytics | HIS-RPT (mở rộng) | BI dashboard, predictive analytics, operational intelligence | Should |
| Helpdesk tích hợp | HIS-HLP | Ticket system cho sự cố CNTT nội bộ | Could |
| English Interface | NFR-USE-003 (mở rộng) | Giao diện song ngữ VN/EN | Could |

### H.4.2. Milestones GĐ3

| Milestone | Thời điểm | Deliverable |
|-----------|----------|-------------|
| M3.1 — GĐ3 Kickoff + Roadmap | M22 | Feature prioritization based on GĐ1+2 feedback |
| M3.2 — FHIR API Beta | M24 | Read-only FHIR endpoints, validator pass |
| M3.3 — AI Pilot | M26 | AI model integrated, pilot with 1 modality (X-quang) |
| M3.4 — Beta GĐ3 | M27 | UAT for new features |
| M3.5 — Go-Live GĐ3 | M30 | Full deployment |

---

## H.5. Ma trận Module × Giai đoạn

| Module | GĐ1 | GĐ2 | GĐ3 |
|--------|------|------|------|
| HIS-SYS (Quản trị) | ✅ Full | Enhancement | — |
| HIS-ER (Cấp cứu) | ✅ Full | — | — |
| HIS-REG (Tiếp đón) | ✅ Full | — | — |
| HIS-OPD (Ngoại trú) | ✅ Full | — | — |
| HIS-CLS (Chỉ định CLS) | ✅ CĐHA | + XN (LIS) | — |
| HIS-IPD (Nội trú) | ✅ Core | Enhancement | — |
| HIS-OPT (Ngoại trú theo dõi) | ✅ Core | Enhancement | — |
| HIS-EMR (Bệnh án ĐT) | ✅ Core | Enhancement | AI suggest |
| HIS-SUR (Phẫu thuật) | ✅ Core | Enhancement | — |
| HIS-PHA (Dược) | ✅ Full | Enhancement | — |
| HIS-BIL (Viện phí) | ✅ Full | + e-Invoice | — |
| HIS-RPH (Kho dược) | ✅ Core | + Multi-kho | — |
| HIS-PLN (Hẹn khám) | ✅ Core | + SMS/Email | Patient Portal |
| HIS-RPT (Báo cáo) | ✅ Core | + Custom report | + BI/Analytics |
| HIS-HR (Nhân sự) | ❌ | ✅ Core | Enhancement |
| HIS-AST (Tài sản) | ❌ | ✅ Core | — |
| HIS-INS (BHYT) | ✅ Full | Enhancement | — |
| HIS-OTH (Khác) | ❌ | Evaluate | Evaluate |
| HIS-TLC (Telehealth) | ❌ | ❌ | ✅ Core |
| HIS-HLP (Helpdesk) | ❌ | ❌ | ✅ Core |
| RIS-ADM (Quản trị RIS) | ✅ Full | — | — |
| RIS-PAT (BN PACS) | ✅ Full | — | — |
| RIS-MOD (Modality) | ✅ Full | — | + AI integration |
| RIS-WL (Worklist) | ✅ Full | — | — |
| RIS-STD (Lưu trữ) | ✅ HOT+WARM | + COLD/ARCHIVE | + XDS-I.b |
| RIS-VWR (Viewer) | ✅ Core | + MPR/3D/Compare | + AI overlay |
| RIS-RPT (Báo cáo CĐHA) | ✅ Full | Enhancement | + AI assist |
| RIS-SYN (Đồng bộ) | ✅ Core | + Full reconcile | + Offline mode |
| Integration (HL7) | ✅ Full | + LIS | — |
| Integration (BHXH) | ✅ Full | Enhancement | — |
| Integration (FHIR) | ❌ | ❌ | ✅ Read API |
| Notification | ✅ Internal | + SMS/Email | + Patient Portal |

---

## H.6. Tiêu chí Go/No-Go cho mỗi Giai đoạn

### H.6.1. Checklist Go-Live

| # | Tiêu chí | GĐ1 | GĐ2 | GĐ3 |
|---|---------|------|------|------|
| 1 | UAT pass rate ≥ 90% | ✅ Bắt buộc | ✅ | ✅ |
| 2 | 0 bug P1 (Critical) open | ✅ Bắt buộc | ✅ | ✅ |
| 3 | ≤ 3 bug P2 (High) open (có workaround) | ✅ Bắt buộc | ✅ | ✅ |
| 4 | Performance test đạt NFR targets | ✅ Bắt buộc | ✅ | ✅ |
| 5 | Data migration verified | ✅ Bắt buộc | N/A | N/A |
| 6 | Integration test (HL7, DICOM, BHXH) pass 100% | ✅ Bắt buộc | ✅ (+LIS) | ✅ (+FHIR) |
| 7 | Security pen test — no Critical/High findings | ✅ Bắt buộc | ✅ | ✅ |
| 8 | DR drill successful | ✅ Bắt buộc | ✅ | ✅ |
| 9 | Training completed ≥ 90% users | ✅ Bắt buộc | ✅ (new users) | ✅ (new users) |
| 10 | User manual + admin guide available | ✅ Bắt buộc | ✅ (updated) | ✅ (updated) |
| 11 | Rollback plan documented + tested | ✅ Bắt buộc | ✅ | ✅ |
| 12 | Support team resourced + briefed | ✅ Bắt buộc | ✅ | ✅ |
| 13 | BV leadership sign-off | ✅ Bắt buộc | ✅ | ✅ |

---

*Tiếp theo: [Phần I — Rủi ro](./I-RUI-RO.md)*
