# PHẦN C2 — YÊU CẦU CHỨC NĂNG RIS/PACS

> **Mã phần**: SRS-PART-C2  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: FR-RIS-{MODULE}-{STT}

---

## C2.1. PHÂN HỆ QUẢN TRỊ RIS (RIS-ADM)

### FR-RIS-ADM-001: Cấu hình thiết bị CĐHA (Modality)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đăng ký và cấu hình các thiết bị chẩn đoán hình ảnh trong hệ thống |
| **Actor** | ACT-S01 (SysAdmin), ACT-S02 (PACS Admin) |
| **Ưu tiên** | Must |
| **Đầu vào** | AE Title, IP, port DICOM (default 104/11112), loại modality (CT/MR/DR/CR/US/MG/NM/PT/Endo), phòng đặt, cơ sở, hãng sản xuất, model, serial number, transfer syntax hỗ trợ |
| **Đầu ra** | Bản ghi thiết bị trong PACS database, AE Title được kích hoạt trên DICOM server |
| **Quy tắc nghiệp vụ** | - AE Title duy nhất toàn hệ thống (≤ 16 ký tự, ASCII)<br/>- Mỗi thiết bị gắn với 1 phòng và 1 cơ sở<br/>- Hỗ trợ cấu hình auto-routing rule (ảnh từ thiết bị X → workstation Y)<br/>- Hỗ trợ bật/tắt thiết bị không xóa cấu hình<br/>- Kiểm tra kết nối DICOM Echo (C-ECHO) từ giao diện quản trị |
| **Tiêu chí chấp nhận** | 1. Tạo thiết bị mới, gửi C-ECHO thành công trong ≤ 5 giây<br/>2. Thiết bị bị tắt → worklist không trả cho thiết bị đó<br/>3. AE Title trùng → báo lỗi, không cho tạo |

### FR-RIS-ADM-002: Quản lý danh mục dịch vụ CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý danh mục dịch vụ chẩn đoán hình ảnh dùng cho RIS/PACS |
| **Actor** | ACT-S02 (PACS Admin) |
| **Ưu tiên** | Must |
| **Đầu vào** | Mã dịch vụ HIS, mã procedure PACS, tên dịch vụ (VN/EN), loại modality, body part (theo DICOM Body Part Examined), laterality, thời gian chuẩn (phút), SOP Class UID, protocol mặc định |
| **Đầu ra** | Bản ghi dịch vụ CĐHA, mapping HIS ↔ PACS |
| **Quy tắc nghiệp vụ** | - Mỗi dịch vụ HIS mapping đến ≥ 1 procedure PACS<br/>- Body Part theo chuẩn DICOM (0018,0015): CHEST, HEAD, ABDOMEN, …<br/>- Hỗ trợ nhóm dịch vụ: X-quang, CT, MRI, Siêu âm, Nội soi, Mammography, Y học hạt nhân<br/>- Import/export danh mục Excel<br/>- Đồng bộ tự động với danh mục HIS khi có thay đổi |
| **Tiêu chí chấp nhận** | 1. Tìm kiếm dịch vụ theo tên/mã trong ≤ 1 giây<br/>2. Mapping HIS→PACS chính xác cho 100% dịch vụ đã cấu hình<br/>3. Thay đổi danh mục có audit log |

### FR-RIS-ADM-003: Quản lý phòng chụp

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cấu hình phòng chụp CĐHA, liên kết phòng với thiết bị và kỹ thuật viên |
| **Actor** | ACT-S02 |
| **Ưu tiên** | Must |
| **Đầu vào** | Mã phòng, tên phòng, cơ sở, tầng/khu, danh sách thiết bị trong phòng, danh sách KTV phân công, lịch hoạt động |
| **Đầu ra** | Cấu hình phòng, hiển thị trên worklist filter |
| **Quy tắc nghiệp vụ** | - 1 phòng có thể chứa nhiều thiết bị<br/>- Lịch hoạt động phòng: giờ mở/đóng, thứ trong tuần<br/>- Hỗ trợ trạng thái phòng: hoạt động, tạm đóng, bảo trì |
| **Tiêu chí chấp nhận** | 1. Worklist chỉ hiển thị chỉ định cho phòng đang hoạt động<br/>2. KTV chỉ thấy phòng được phân công |

### FR-RIS-ADM-004: Quản lý template báo cáo

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo và quản lý mẫu báo cáo kết quả CĐHA |
| **Actor** | ACT-S02, ACT-05 (BS CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Tên template, loại dịch vụ áp dụng (X-quang ngực, CT sọ não, …), nội dung mẫu (sections: Mô tả, Kết luận, Khuyến nghị), key fields, variables |
| **Đầu ra** | Template sẵn dùng khi viết báo cáo |
| **Quy tắc nghiệp vụ** | - Template phân loại theo loại modality + body part<br/>- Hỗ trợ rich-text formatting<br/>- Hỗ trợ placeholder/variable: {patient_name}, {patient_age}, {exam_date}<br/>- Versioning template<br/>- Template cá nhân (BS tự tạo) và template chung (toàn khoa) |
| **Tiêu chí chấp nhận** | 1. BS chọn template → nội dung được tự động điền trong ≤ 2 giây<br/>2. Thay đổi template không ảnh hưởng báo cáo đã lưu<br/>3. Hỗ trợ ≥ 50 templates đồng thời |

### FR-RIS-ADM-005: Cấu hình quy tắc phân phối ảnh (Auto-Routing)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cấu hình quy tắc tự động gửi ảnh DICOM từ thiết bị đến workstation/viewer phù hợp |
| **Actor** | ACT-S02 |
| **Ưu tiên** | Must |
| **Đầu vào** | Nguồn (AE Title modality), điều kiện (modality type, body part, referring department), đích (AE Title workstation), hành động (C-STORE forward) |
| **Đầu ra** | Rule engine tự động forward ảnh |
| **Quy tắc nghiệp vụ** | - Rule theo thứ tự ưu tiên (priority order)<br/>- Hỗ trợ nhiều đích cho 1 rule (ảnh gửi song song)<br/>- Fallback rule: nếu không match → gửi đến default workstation<br/>- Rule có thể bật/tắt<br/>- Log mỗi lần forward: thời gian, study, nguồn, đích, kết quả |
| **Tiêu chí chấp nhận** | 1. Ảnh từ modality → workstation đích trong ≤ 3 giây sau C-STORE hoàn tất<br/>2. Rule bị tắt → ảnh không forward theo rule đó<br/>3. Audit log đầy đủ cho mỗi forward |

### FR-RIS-ADM-006: Cấu hình chính sách lưu trữ (Storage Policy)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cấu hình chính sách lưu trữ phân tầng cho ảnh DICOM |
| **Actor** | ACT-S02 |
| **Ưu tiên** | Must |
| **Đầu vào** | Tier definition (Hot/Warm/Cold), ngưỡng thời gian chuyển tier, loại storage (SSD/HDD/NAS/Tape/Object Store), retention period, compression policy |
| **Đầu ra** | Policy engine tự động migrate ảnh giữa các tier |
| **Quy tắc nghiệp vụ** | - Hot tier (SSD): study ≤ 30 ngày<br/>- Warm tier (HDD/NAS): study 31 ngày – 2 năm<br/>- Cold tier (Tape/Object Storage): study > 2 năm<br/>- Retention tối thiểu 10 năm theo quy định Bộ Y tế<br/>- Không xóa study có linked report chưa final<br/>- Migration chạy off-peak (22:00–06:00) `[GIẢ ĐỊNH]` |
| **Tiêu chí chấp nhận** | 1. Study > 30 ngày tự động migrate sang warm tier<br/>2. Study trên cold tier vẫn truy xuất được (thời gian mở ≤ 60 giây)<br/>3. Integrity check (SHA-256) pass 100% sau migration |

---

## C2.2. PHÂN HỆ QUẢN LÝ BỆNH NHÂN CĐHA (RIS-PAT)

### FR-RIS-PAT-001: Đồng bộ thông tin bệnh nhân từ HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nhận và đồng bộ thông tin bệnh nhân từ HIS vào RIS/PACS qua HL7 ADT messages |
| **Actor** | Hệ thống (tự động) |
| **Ưu tiên** | Must |
| **Đầu vào** | HL7 ADT^A04 (Register), ADT^A08 (Update), ADT^A01 (Admit), ADT^A02 (Transfer), ADT^A03 (Discharge) |
| **Đầu ra** | Bản ghi bệnh nhân trong PACS database (Patient Master) |
| **Quy tắc nghiệp vụ** | - PID (Patient ID) từ HIS là khóa chính, duy nhất<br/>- Mapping HL7 PID segment → PACS Patient record:<br/>&nbsp;&nbsp;PID-3 → Patient ID<br/>&nbsp;&nbsp;PID-5 → Patient Name (Vietnamese Unicode)<br/>&nbsp;&nbsp;PID-7 → Date of Birth<br/>&nbsp;&nbsp;PID-8 → Sex<br/>&nbsp;&nbsp;PID-11 → Address<br/>&nbsp;&nbsp;PID-13 → Phone Number<br/>- ADT^A08 → cập nhật thông tin, ghi log thay đổi<br/>- Xử lý patient merge (ADT^A40) khi phát hiện trùng `[GĐ2]`<br/>- Nếu HL7 parse error → đẩy vào dead-letter queue + alert |
| **Tiêu chí chấp nhận** | 1. BN đăng ký trên HIS → xuất hiện trên PACS trong ≤ 3 giây<br/>2. Sửa tên BN trên HIS → PACS cập nhật đúng tên<br/>3. HL7 lỗi cú pháp → message vào dead-letter, không mất dữ liệu |

### FR-RIS-PAT-002: Tra cứu bệnh nhân trên RIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tìm kiếm bệnh nhân trong hệ thống RIS/PACS |
| **Actor** | ACT-05 (BS CĐHA), ACT-06 (KTV CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Patient ID, tên (hỗ trợ wildcard), ngày sinh, accession number, mã thẻ BHYT |
| **Đầu ra** | Danh sách bệnh nhân khớp điều kiện, thông tin chi tiết |
| **Quy tắc nghiệp vụ** | - Tìm kiếm không phân biệt hoa/thường, có/không dấu tiếng Việt<br/>- Hỗ trợ tìm gần đúng (fuzzy search) `[Should]`<br/>- Kết quả hiển thị: PID, tên, ngày sinh, giới, số study, study gần nhất<br/>- Phân quyền: KTV chỉ thấy BN có chỉ định tại phòng mình |
| **Tiêu chí chấp nhận** | 1. Tìm theo PID trả kết quả trong ≤ 1 giây<br/>2. Tìm theo tên trả kết quả trong ≤ 2 giây (database 500.000+ BN)<br/>3. Tìm "Nguyễn Văn" và "nguyen van" đều ra cùng kết quả |

### FR-RIS-PAT-003: Xem lịch sử CĐHA bệnh nhân

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hiển thị toàn bộ lịch sử chụp CĐHA của một bệnh nhân |
| **Actor** | ACT-05, ACT-06, ACT-02 (BS lâm sàng — chỉ xem) |
| **Ưu tiên** | Must |
| **Đầu vào** | Patient ID |
| **Đầu ra** | Danh sách studies theo thời gian (mới nhất trước), grouped theo encounter |
| **Quy tắc nghiệp vụ** | - Hiển thị: ngày chụp, loại chụp, accession, số series/images, trạng thái report, BS đọc<br/>- Click vào study → mở viewer<br/>- Click vào report → xem kết quả<br/>- Hỗ trợ lọc theo: loại modality, khoảng thời gian, body part<br/>- Highlight study chưa đọc/chưa final |
| **Tiêu chí chấp nhận** | 1. Load lịch sử 100+ studies trong ≤ 3 giây<br/>2. Lọc theo CT chỉ hiển thị studies CT<br/>3. BS lâm sàng xem được lịch sử nhưng không sửa được report |

---

## C2.3. PHÂN HỆ QUẢN LÝ THIẾT BỊ (RIS-MOD)

### FR-RIS-MOD-001: Giám sát trạng thái thiết bị CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Dashboard giám sát real-time trạng thái tất cả thiết bị CĐHA |
| **Actor** | ACT-S02, ACT-06 (KTV CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | DICOM C-ECHO heartbeat, MPPS events, DICOM Association log |
| **Đầu ra** | Dashboard: trạng thái online/offline, số ca đã chụp hôm nay, ca đang chờ, cảnh báo |
| **Quy tắc nghiệp vụ** | - Heartbeat C-ECHO mỗi 60 giây cho mỗi thiết bị<br/>- Trạng thái: Online (✅), Offline (❌), Warning (⚠️ — kết nối chậm)<br/>- Alert khi thiết bị offline > 5 phút (notification cho PACS Admin)<br/>- Thống kê theo thiết bị: số ca/ngày, thời gian chụp trung bình<br/>- Hỗ trợ xem từ xa (remote monitoring) |
| **Tiêu chí chấp nhận** | 1. Thiết bị rút mạng → trạng thái chuyển sang Offline trong ≤ 2 phút<br/>2. Dashboard refresh tự động mỗi 30 giây<br/>3. Alert gửi thành công khi thiết bị offline > 5 phút |

### FR-RIS-MOD-002: Quản lý DICOM Association

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giám sát và quản lý kết nối DICOM giữa các node |
| **Actor** | ACT-S02 |
| **Ưu tiên** | Should |
| **Đầu vào** | Log DICOM Association (calling/called AE, timestamp, SOP Class, status) |
| **Đầu ra** | Log viewer với filter, thống kê kết nối |
| **Quy tắc nghiệp vụ** | - Log tất cả DICOM Association: Negotiate → Accept/Reject → Release/Abort<br/>- Hiển thị: thời gian, calling AE, called AE, SOP Class, transfer syntax, status, data size<br/>- Filter theo AE, thời gian, status, SOP Class<br/>- Cảnh báo khi reject rate > 5% |
| **Tiêu chí chấp nhận** | 1. Log viewer load 10.000 records trong ≤ 3 giây<br/>2. Filter áp dụng trong ≤ 1 giây<br/>3. Reject rate > 5% → alert hiển thị trên dashboard |

### FR-RIS-MOD-003: Ghi nhận MPPS (Modality Performed Procedure Step)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tiếp nhận thông tin trạng thái thực hiện ca chụp từ thiết bị |
| **Actor** | Hệ thống (tự động), thiết bị CĐHA |
| **Ưu tiên** | Must |
| **Đầu vào** | DICOM N-CREATE / N-SET (MPPS SOP Class) từ modality |
| **Đầu ra** | Cập nhật trạng thái order: In Progress → Completed / Discontinued |
| **Quy tắc nghiệp vụ** | - N-CREATE (In Progress): cập nhật order status = "Đang chụp"<br/>- N-SET (Completed): cập nhật order = "Đã chụp", ghi dose information (nếu có)<br/>- N-SET (Discontinued): cập nhật order = "Hủy tại máy", lý do<br/>- Matching MPPS ↔ Order qua: Accession Number + SPS ID<br/>- Nếu modality không hỗ trợ MPPS → fallback: cập nhật trạng thái khi nhận C-STORE |
| **Tiêu chí chấp nhận** | 1. MPPS Completed → order status thay đổi trong ≤ 2 giây<br/>2. Dose info (CT) được ghi nhận và hiển thị trong report<br/>3. Modality không có MPPS → trạng thái vẫn cập nhật khi nhận ảnh |

---

## C2.4. PHÂN HỆ WORKLIST (RIS-WL)

### FR-RIS-WL-001: DICOM Modality Worklist (MWL) Provider

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cung cấp danh sách công việc DICOM cho thiết bị CĐHA (MWL SCP) |
| **Actor** | Hệ thống (DICOM SCP), thiết bị CĐHA (DICOM SCU) |
| **Ưu tiên** | Must |
| **Đầu vào** | C-FIND request từ modality (query keys: Scheduled Station AE Title, Modality, Date) |
| **Đầu ra** | Danh sách Scheduled Procedure Step (SPS) matching điều kiện query |
| **Quy tắc nghiệp vụ** | - Trả về SPS với đầy đủ DICOM attributes:<br/>&nbsp;&nbsp;(0010,0010) Patient Name — hỗ trợ Vietnamese Unicode<br/>&nbsp;&nbsp;(0010,0020) Patient ID<br/>&nbsp;&nbsp;(0010,0030) Patient Birth Date<br/>&nbsp;&nbsp;(0010,0040) Patient Sex<br/>&nbsp;&nbsp;(0008,0050) Accession Number<br/>&nbsp;&nbsp;(0040,0100) Scheduled Procedure Step Sequence<br/>&nbsp;&nbsp;(0040,0001) Scheduled Station AE Title<br/>&nbsp;&nbsp;(0040,0002) Scheduled Date<br/>&nbsp;&nbsp;(0040,0003) Scheduled Time<br/>&nbsp;&nbsp;(0008,0060) Modality<br/>&nbsp;&nbsp;(0032,1060) Requested Procedure Description<br/>&nbsp;&nbsp;(0040,0007) Scheduled Procedure Step Description<br/>&nbsp;&nbsp;(0040,0009) Scheduled Procedure Step ID<br/>&nbsp;&nbsp;(0020,000D) Study Instance UID<br/>- Chỉ trả SPS có status = SCHEDULED (không trả completed/cancelled)<br/>- Filter theo AE Title của thiết bị gọi (thiết bị chỉ thấy chỉ định cho mình)<br/>- Hỗ trợ broad query (date range) và narrow query (specific patient) |
| **Tiêu chí chấp nhận** | 1. Modality query MWL → response trong ≤ 2 giây (100 orders/ngày)<br/>2. Patient Name hiển thị đúng Vietnamese diacritics trên modality<br/>3. Chỉ trả order SCHEDULED, không trả completed |

### FR-RIS-WL-002: Worklist Dashboard (Giao diện web)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giao diện web hiển thị danh sách chỉ định CĐHA cho KTV và BS |
| **Actor** | ACT-06 (KTV CĐHA), ACT-05 (BS CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Orders từ database RIS |
| **Đầu ra** | Bảng danh sách chỉ định với lọc, sắp xếp, trạng thái real-time |
| **Quy tắc nghiệp vụ** | - Cột hiển thị: STT, Patient ID, Tên BN, Tuổi/Giới, Accession, Dịch vụ, Ưu tiên (Stat/Urgent/Routine), Phòng chụp, Trạng thái, Thời gian chỉ định, BS chỉ định<br/>- Color coding ưu tiên: Stat = 🔴 Đỏ, Urgent = 🟡 Vàng, Routine = trắng<br/>- Filter: theo phòng chụp, loại modality, trạng thái, khoảng thời gian, ưu tiên<br/>- Real-time update: order mới xuất hiện không cần refresh (WebSocket/SSE)<br/>- Sort: theo ưu tiên (Stat first), sau đó theo thời gian<br/>- Hỗ trợ in worklist |
| **Tiêu chí chấp nhận** | 1. Order mới từ HIS xuất hiện trên worklist trong ≤ 5 giây<br/>2. Filter 200 orders trong ≤ 1 giây<br/>3. Order Stat luôn hiển thị trên cùng |

### FR-RIS-WL-003: Nhận chỉ định từ HIS (HL7 Order Receiver)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tiếp nhận, validate và lưu chỉ định CĐHA từ HIS qua HL7 ORM |
| **Actor** | Hệ thống (tự động) |
| **Ưu tiên** | Must |
| **Đầu vào** | HL7 ORM^O01 message: New Order (NW), Order Update (XO), Order Cancel (CA) |
| **Đầu ra** | Order record trong RIS database, HL7 ACK response |
| **Quy tắc nghiệp vụ** | - Parse ORM^O01 segments: MSH, PID, PV1, ORC, OBR<br/>- Mapping:<br/>&nbsp;&nbsp;ORC-2 (Placer Order Number) → HIS Order ID<br/>&nbsp;&nbsp;ORC-1 (Order Control): NW = New, XO = Update, CA = Cancel<br/>&nbsp;&nbsp;OBR-4 (Universal Service ID) → Procedure Code<br/>&nbsp;&nbsp;OBR-31 (Reason for Study) → Clinical Indication<br/>- Validate: PID tồn tại, Procedure Code hợp lệ, mandatory fields có giá trị<br/>- Generate Accession Number: format `{SITE}{YYMMDD}{SEQ6}` (VD: CS01260325000142)<br/>- Generate Study Instance UID (1.2.840.xxxxx...)<br/>- NW: tạo order mới, trả ACK AA (Accept)<br/>- XO: cập nhật order (chỉ khi status = SCHEDULED)<br/>- CA: hủy order (chỉ khi chưa chụp, status ≠ IN_PROGRESS/COMPLETED)<br/>- Validation fail → trả ACK AE (Error) + error message |
| **Tiêu chí chấp nhận** | 1. ORM NW → order tạo trong ≤ 2 giây, ACK AA trả lại<br/>2. ORM CA cho order đã chụp → ACK AE, order không bị hủy<br/>3. ORM thiếu PID → ACK AE, ghi dead-letter log |

### FR-RIS-WL-004: Quản lý chỉ định thủ công (Walk-in / Emergency)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo chỉ định CĐHA trực tiếp trên RIS khi tích hợp HIS gián đoạn hoặc bệnh nhân cấp cứu |
| **Actor** | ACT-06 (KTV CĐHA), ACT-12 (Tiếp đón CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Thông tin BN (tối thiểu: họ tên, ngày sinh, giới tính), dịch vụ CĐHA, ưu tiên, BS chỉ định, thông tin lâm sàng |
| **Đầu ra** | Order trong RIS, hiển thị trên worklist |
| **Quy tắc nghiệp vụ** | - Cho phép tạo BN tạm (không có PID từ HIS) → merge sau<br/>- Bắt buộc ghi lý do tạo thủ công (dropdown: mất kết nối, cấp cứu, bệnh nhân ngoài)<br/>- Đánh dấu order là "Manual Entry" để đối soát<br/>- Sau khi tích hợp phục hồi → reconcile order thủ công với HIS |
| **Tiêu chí chấp nhận** | 1. Tạo order thủ công trong ≤ 30 giây (fast entry mode)<br/>2. Order thủ công hiển thị icon "Manual" trên worklist<br/>3. Báo cáo đối soát liệt kê tất cả order thủ công chưa reconcile |

---

## C2.5. PHÂN HỆ LƯU TRỮ ẢNH DICOM (RIS-STD)

### FR-RIS-STD-001: DICOM C-STORE SCP (Tiếp nhận ảnh)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tiếp nhận và lưu trữ ảnh DICOM từ thiết bị chụp |
| **Actor** | Hệ thống (DICOM SCP) |
| **Ưu tiên** | Must |
| **Đầu vào** | DICOM C-STORE request (dataset) từ modality |
| **Đầu ra** | File DICOM lưu trên storage, metadata index trong database |
| **Quy tắc nghiệp vụ** | - Hỗ trợ SOP Classes: CT Image, MR Image, CR Image, DX Image, US Image, US Multi-frame, MG Image, NM Image, PT Image, Secondary Capture, GSPS, SR, Encapsulated PDF, Key Object Selection<br/>- Transfer Syntax: Implicit VR LE, Explicit VR LE, Explicit VR BE, JPEG Baseline, JPEG Lossless, JPEG 2000 Lossless, JPEG 2000<br/>- Lưu file theo cấu trúc: `/{StudyInstanceUID}/{SeriesInstanceUID}/{SOPInstanceUID}.dcm`<br/>- Index vào DB: Patient, Study, Series, Instance level metadata<br/>- Verify DICOM conformance khi nhận (mandatory tags check)<br/>- Coerce Patient ID / Accession Number theo order nếu mismatch `[Should]`<br/>- Throughput: ≥ 50 images/giây sustained<br/>- Trigger auto-routing sau khi store thành công |
| **Tiêu chí chấp nhận** | 1. CT scan 500 slices → store hoàn tất trong ≤ 15 giây<br/>2. DICOM file integrity: checksum match 100%<br/>3. Metadata indexed: truy vấn study mới trong ≤ 1 giây sau store |

### FR-RIS-STD-002: DICOM C-FIND SCP (Truy vấn)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cung cấp dịch vụ truy vấn DICOM cho viewer, workstation, và hệ thống khác |
| **Actor** | Hệ thống (DICOM SCP), Viewer (DICOM SCU) |
| **Ưu tiên** | Must |
| **Đầu vào** | C-FIND request với query keys |
| **Đầu ra** | Danh sách matching records (Patient/Study/Series/Instance level) |
| **Quy tắc nghiệp vụ** | - Hỗ trợ Query/Retrieve levels:<br/>&nbsp;&nbsp;Patient Root: Patient → Study → Series → Instance<br/>&nbsp;&nbsp;Study Root: Study → Series → Instance<br/>- Return keys bắt buộc: Patient Name, Patient ID, Study Date, Study Time, Accession Number, Study Instance UID, Modality, Series Instance UID, Number of Instances<br/>- Hỗ trợ wildcard matching: `*`, `?`<br/>- Hỗ trợ date range: `20260101-20260331`<br/>- Result limit: configurable (default 500 per query) |
| **Tiêu chí chấp nhận** | 1. Query by Accession Number → response ≤ 1 giây<br/>2. Query by date range (1 tháng, ~6.000 studies) → response ≤ 5 giây<br/>3. Wildcard search `NGUYEN*` trả đúng kết quả |

### FR-RIS-STD-003: DICOM C-MOVE / C-GET SCP (Truy xuất ảnh)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Gửi ảnh DICOM đến workstation/viewer theo yêu cầu |
| **Actor** | Hệ thống (DICOM SCP), Viewer (DICOM SCU) |
| **Ưu tiên** | Must |
| **Đầu vào** | C-MOVE/C-GET request (Study/Series/Instance level) |
| **Đầu ra** | DICOM images gửi đến destination AE |
| **Quy tắc nghiệp vụ** | - C-MOVE: gửi ảnh từ PACS đến AE Title chỉ định (push model)<br/>- C-GET: trả ảnh trực tiếp trên cùng association (pull model, cho web viewer)<br/>- Hỗ trợ retrieve ở Study, Series, Instance level<br/>- Compression negotiation: gửi theo transfer syntax được negotiate<br/>- Concurrent retrieve: ≥ 10 simultaneous sessions<br/>- Prior study prefetch: tự động retrieve study cũ của cùng BN `[Should]` |
| **Tiêu chí chấp nhận** | 1. C-MOVE study 200 images → hoàn tất trong ≤ 30 giây (1Gbps LAN)<br/>2. 10 concurrent retrieves không gây timeout<br/>3. Retrieve study đã migrate sang warm tier → hoàn tất trong ≤ 60 giây |

### FR-RIS-STD-004: DICOMweb Services (WADO-RS, STOW-RS, QIDO-RS)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cung cấp RESTful API truy cập DICOM cho web viewer và tích hợp HIS |
| **Actor** | Web Viewer, HIS (embedded viewer), ứng dụng di động `[GĐ3]` |
| **Ưu tiên** | Must |
| **Đầu vào** | HTTP requests theo chuẩn DICOMweb |
| **Đầu ra** | DICOM/JSON metadata, DICOM files, rendered images |
| **Quy tắc nghiệp vụ** | - **WADO-RS**: Retrieve DICOM objects qua HTTP GET<br/>&nbsp;&nbsp;`GET /studies/{study}/series/{series}/instances/{instance}`<br/>&nbsp;&nbsp;Accept: `application/dicom`, `image/jpeg`, `image/png`<br/>- **QIDO-RS**: Query DICOM objects qua HTTP GET<br/>&nbsp;&nbsp;`GET /studies?PatientID={pid}&StudyDate={date}`<br/>&nbsp;&nbsp;Response: `application/dicom+json`<br/>- **STOW-RS**: Store DICOM qua HTTP POST `[Should]`<br/>&nbsp;&nbsp;`POST /studies` với `multipart/related; type="application/dicom"`<br/>- Authentication: Bearer token (OAuth2/JWT) — tích hợp SSO HIS<br/>- CORS: cho phép origin của HIS web app<br/>- Rate limit: 100 requests/giây per user |
| **Tiêu chí chấp nhận** | 1. WADO-RS retrieve single instance → ≤ 2 giây<br/>2. QIDO-RS query studies → ≤ 3 giây<br/>3. HIS embedded viewer load thumbnail qua WADO-RS thành công |

### FR-RIS-STD-005: Quản lý lưu trữ & Integrity Check

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giám sát dung lượng lưu trữ và kiểm tra tính toàn vẹn dữ liệu DICOM |
| **Actor** | ACT-S02 (PACS Admin), Hệ thống (tự động) |
| **Ưu tiên** | Must |
| **Đầu vào** | Storage metrics, DICOM file checksums |
| **Đầu ra** | Dashboard lưu trữ, báo cáo integrity |
| **Quy tắc nghiệp vụ** | - Dashboard: tổng dung lượng, đã dùng, còn trống, tỷ lệ sử dụng (%), trend<br/>- Alert khi storage usage > 80%<br/>- Integrity check chạy hàng đêm: verify SHA-256 checksum cho random 1% studies<br/>- Full integrity scan: hàng tháng (toàn bộ hot + warm tier)<br/>- Nếu corrupt detected → alert, attempt restore từ backup/redundant copy |
| **Tiêu chí chấp nhận** | 1. Storage alert gửi khi dung lượng > 80%<br/>2. Integrity check phát hiện file corrupt (giả lập) → alert trong ≤ 1 phút<br/>3. Báo cáo integrity monthly hoàn tất trong ≤ 4 giờ (1M files) |

---

## C2.6. PHÂN HỆ VIEWER CĐHA (RIS-VWR)

### FR-RIS-VWR-001: Web Viewer Zero-footprint

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Viewer chạy hoàn toàn trên trình duyệt (HTML5/WebGL), không cần cài đặt plugin |
| **Actor** | ACT-05 (BS CĐHA), ACT-02 (BS lâm sàng), ACT-06 (KTV CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Study Instance UID / Accession Number → load study qua DICOMweb |
| **Đầu ra** | Hiển thị ảnh DICOM với đầy đủ công cụ chẩn đoán |
| **Quy tắc nghiệp vụ** | - Trình duyệt hỗ trợ: Chrome ≥ 90, Edge ≥ 90, Firefox ≥ 88<br/>- Rendering engine: WebGL 2.0 cho hiệu năng cao<br/>- Hỗ trợ hiển thị: CT, MR, CR, DX, US, MG, NM, PT, SC, SR<br/>- Hỗ trợ tất cả common transfer syntaxes (JPEG, JPEG 2000, JPEG-LS, RLE)<br/>- Progressive loading: hiển thị ảnh đầu tiên trước khi toàn bộ study tải xong<br/>- Cornerstone.js / OHIF Viewer based `[GIẢ ĐỊNH]` |
| **Tiêu chí chấp nhận** | 1. Mở CR/DX (1 ảnh, 10MB) → hiển thị trong ≤ 3 giây<br/>2. Mở CT study (300 slices, 150MB) → ảnh đầu tiên hiển thị trong ≤ 5 giây, toàn bộ ≤ 15 giây<br/>3. Không cần cài đặt bất kỳ plugin/ActiveX |

### FR-RIS-VWR-002: Công cụ hiển thị cơ bản

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bộ công cụ thao tác ảnh cơ bản cho chẩn đoán |
| **Actor** | ACT-05, ACT-02 |
| **Ưu tiên** | Must |
| **Danh sách công cụ** | - **Window/Level (W/L)**: kéo chuột hoặc preset (Lung, Bone, Brain, Abdomen, Mediastinum)<br/>- **Zoom**: scroll wheel hoặc công cụ<br/>- **Pan**: kéo chuột<br/>- **Scroll**: cuộn qua stack images (CT/MR)<br/>- **Rotate**: 90°, 180°, free rotation<br/>- **Flip**: horizontal, vertical<br/>- **Invert**: đảo âm bản<br/>- **Magnifying glass**: kính lúp vùng quan tâm<br/>- **Reset**: về trạng thái gốc |
| **Quy tắc nghiệp vụ** | - Phím tắt cho mỗi công cụ (configurable)<br/>- Thao tác W/L phản hồi real-time (< 100ms delay)<br/>- Scroll qua 500 slices CT mượt (≥ 30 fps) |
| **Tiêu chí chấp nhận** | 1. Kéo W/L phản hồi real-time (< 100ms)<br/>2. Scroll CT 500 slices không lag (≥ 30 fps)<br/>3. Tất cả phím tắt hoạt động đúng |

### FR-RIS-VWR-003: Công cụ đo lường

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Công cụ đo kích thước, góc, density trên ảnh DICOM |
| **Actor** | ACT-05 |
| **Ưu tiên** | Must |
| **Danh sách công cụ** | - **Ruler (Length)**: đo khoảng cách (mm/cm), tính theo pixel spacing<br/>- **Angle**: đo góc 2 đoạn thẳng<br/>- **Cobb Angle**: đo góc Cobb (cột sống)<br/>- **Ellipse ROI**: vẽ ellipse, tính Mean/StdDev/Min/Max HU (Hounsfield Unit)<br/>- **Rectangle ROI**: tương tự ellipse<br/>- **Pixel Probe**: giá trị pixel tại con trỏ<br/>- **Bidirectional**: đo 2 trục (dài x rộng) |
| **Quy tắc nghiệp vụ** | - Đo lường dựa trên Pixel Spacing (0028,0030) và Imager Pixel Spacing (0018,1164)<br/>- Hiển thị đơn vị: mm, cm, HU<br/>- Kết quả đo có thể lưu dưới dạng DICOM Presentation State (GSPS)<br/>- Calibration thủ công khi thiếu Pixel Spacing `[Should]` |
| **Tiêu chí chấp nhận** | 1. Đo ruler trên ảnh có Pixel Spacing → sai số ≤ 1%<br/>2. ROI ellipse hiển thị Mean HU chính xác (so với viewer tham chiếu)<br/>3. Kết quả đo lưu GSPS → mở lại hiển thị đúng |

### FR-RIS-VWR-004: Công cụ chú thích (Annotation)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thêm chú thích văn bản, mũi tên, đánh dấu trên ảnh |
| **Actor** | ACT-05 |
| **Ưu tiên** | Must |
| **Danh sách công cụ** | - **Text annotation**: gõ text trên ảnh<br/>- **Arrow**: mũi tên chỉ vùng quan tâm<br/>- **Freehand draw**: vẽ tự do<br/>- **Circle/Ellipse marker**: khoanh vùng |
| **Quy tắc nghiệp vụ** | - Annotation lưu dưới dạng DICOM GSPS hoặc Key Object Selection (KOS)<br/>- Annotation có thể ẩn/hiện<br/>- Mỗi annotation gắn user tạo + timestamp<br/>- Hỗ trợ xóa annotation của mình (không xóa của người khác trừ admin) |
| **Tiêu chí chấp nhận** | 1. Thêm annotation → lưu GSPS thành công<br/>2. Mở study trên workstation khác → thấy annotation đã lưu<br/>3. User A không xóa được annotation của User B |

### FR-RIS-VWR-005: Hanging Protocol

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tự động bố trí layout hiển thị series theo loại khám |
| **Actor** | Hệ thống (tự động), ACT-05 (tùy chỉnh) |
| **Ưu tiên** | Must |
| **Đầu vào** | Study metadata (modality, body part, description, number of series) |
| **Đầu ra** | Layout tự động: vị trí series trên viewport grid |
| **Quy tắc nghiệp vụ** | - Matching criteria: Modality + Body Part + Study Description<br/>- Ví dụ hanging protocol:<br/>&nbsp;&nbsp;CT Chest: 1x1 Axial Mediastinum W/L<br/>&nbsp;&nbsp;CT Abdomen: 2x2 (Axial + Coronal + Sagittal + 3D/MIP)<br/>&nbsp;&nbsp;MR Brain: 2x3 (T1, T2, FLAIR, DWI, ADC, T1+C)<br/>&nbsp;&nbsp;X-ray Chest PA: 1x1 with lung preset<br/>&nbsp;&nbsp;Mammography: 2x2 (RCC, LCC, RMLO, LMLO)<br/>- BS có thể tùy chỉnh và lưu hanging protocol cá nhân<br/>- Hỗ trợ prior study comparison layout (current vs prior side-by-side) |
| **Tiêu chí chấp nhận** | 1. Mở CT Brain → tự động layout đúng protocol trong ≤ 2 giây<br/>2. BS chỉnh layout → lưu cá nhân → lần sau tự động áp dụng<br/>3. Không có protocol match → fallback: hiển thị series đầu tiên, 1x1 |

### FR-RIS-VWR-006: Multi-Planar Reconstruction (MPR)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tái tạo ảnh trên các mặt phẳng Axial, Sagittal, Coronal từ dataset CT/MR |
| **Actor** | ACT-05 |
| **Ưu tiên** | Must |
| **Đầu vào** | Volumetric dataset (CT/MR series) |
| **Đầu ra** | Ảnh MPR trên 3 mặt phẳng, crosshair đồng bộ |
| **Quy tắc nghiệp vụ** | - MPR yêu cầu axial dataset liên tục (slice thickness ≤ 5mm)<br/>- Crosshair sync: click trên 1 plane → các plane khác cập nhật<br/>- W/L đồng bộ giữa các plane<br/>- Oblique MPR (tùy chỉnh góc) `[Should]`<br/>- Thick slab (MIP, MinIP, Average) `[Should]` |
| **Tiêu chí chấp nhận** | 1. MPR từ CT 300 slices render trong ≤ 5 giây<br/>2. Crosshair sync chính xác giữa 3 planes<br/>3. Scroll trên MPR mượt (≥ 15 fps) |

### FR-RIS-VWR-007: So sánh Prior Studies

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | So sánh study hiện tại với các studies trước của cùng bệnh nhân |
| **Actor** | ACT-05 |
| **Ưu tiên** | Must |
| **Đầu vào** | Study hiện tại + prior study(s) từ lịch sử BN |
| **Đầu ra** | Layout so sánh side-by-side, đồng bộ scroll/W-L |
| **Quy tắc nghiệp vụ** | - Tự động tìm prior studies: cùng BN + cùng modality + cùng body part<br/>- Layout: current (trái) | prior (phải), hoặc 2x1, 2x2<br/>- Đồng bộ: scroll sync (khi cùng orientation), W/L sync (toggle)<br/>- Hiển thị thông tin delta: ngày chụp hiện tại vs ngày chụp prior<br/>- Prefetch prior study tự động khi mở study hiện tại `[Should]` |
| **Tiêu chí chấp nhận** | 1. Mở comparison → prior study load trong ≤ 10 giây (từ warm tier)<br/>2. Scroll sync chính xác (cùng slice position)<br/>3. Không có prior → hiển thị thông báo "Không có study cũ" |

### FR-RIS-VWR-008: Viewer tích hợp trong HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Viewer nhúng trong giao diện HIS cho bác sĩ lâm sàng xem kết quả CĐHA |
| **Actor** | ACT-02 (BS lâm sàng), ACT-03 (BS nội trú) |
| **Ưu tiên** | Must |
| **Đầu vào** | Context launch từ HIS: Accession Number hoặc Study Instance UID |
| **Đầu ra** | Viewer mở đúng study, hiển thị inline trong HIS |
| **Quy tắc nghiệp vụ** | - Launch URL format: `https://{pacs-host}/viewer?AccessionNumber={acc}` hoặc `StudyInstanceUID={uid}`<br/>- SSO: token HIS → authenticate PACS viewer (không đăng nhập lại)<br/>- Embedded mode: hiển thị trong iframe/component HIS (ẩn toolbar quản trị PACS)<br/>- Standalone mode: mở tab mới full viewer (link "Mở viewer đầy đủ")<br/>- Thumbnail preview trong HIS: WADO-RS rendered image `GET /studies/{uid}/rendered?viewport=256,256`<br/>- Phân quyền: BS lâm sàng chỉ xem (view-only), không chỉnh report |
| **Tiêu chí chấp nhận** | 1. Click "Xem ảnh" trên HIS → viewer mở đúng study trong ≤ 5 giây<br/>2. Không hiện prompt đăng nhập (SSO hoạt động)<br/>3. Thumbnail hiển thị trong HIS ≤ 2 giây |

### FR-RIS-VWR-009: 3D Rendering cơ bản

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hiển thị 3D Volume Rendering và MIP từ dataset CT/MR |
| **Actor** | ACT-05 |
| **Ưu tiên** | Should `[GĐ2]` |
| **Đầu vào** | Volumetric dataset (CT/MR series) |
| **Đầu ra** | 3D rendered image có thể xoay, zoom |
| **Quy tắc nghiệp vụ** | - Rendering modes: VR (Volume Rendering), MIP (Maximum Intensity Projection), MinIP, Average<br/>- Preset: CT Angiography, CT Bone, CT Lung<br/>- Interact: rotate, zoom, pan, adjust opacity/threshold<br/>- Clip planes: cắt bỏ vùng không cần thiết<br/>- Tính năng chạy trên WebGL, không cần GPU chuyên dụng |
| **Tiêu chí chấp nhận** | 1. 3D VR render từ CT 300 slices trong ≤ 10 giây<br/>2. Xoay 3D model mượt (≥ 15 fps)<br/>3. MIP projection chính xác so với viewer tham chiếu |

---

## C2.7. PHÂN HỆ BÁO CÁO KẾT QUẢ CĐHA (RIS-RPT)

### FR-RIS-RPT-001: Viết báo cáo kết quả

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giao diện viết kết quả CĐHA tích hợp bên cạnh viewer |
| **Actor** | ACT-05 (BS CĐHA) |
| **Ưu tiên** | Must |
| **Đầu vào** | Study context (accession, patient info, procedure), template |
| **Đầu ra** | Báo cáo kết quả CĐHA (draft → final) |
| **Quy tắc nghiệp vụ** | - Giao diện split-screen: viewer (trái 60-70%) + report editor (phải 30-40%)<br/>- Auto-populate từ template theo procedure type<br/>- Sections bắt buộc: Mô tả (Findings), Kết luận (Impression), Khuyến nghị (Recommendation) `[Should]`<br/>- Rich-text editor: bold, italic, bullet, numbered list<br/>- Hỗ trợ snippet/macro: gõ tắt → expand text (VD: "bt" → "bình thường")<br/>- Chèn key image vào báo cáo (link/thumbnail)<br/>- Auto-save mỗi 30 giây<br/>- Hỗ trợ ICD-10 code cho chẩn đoán CĐHA<br/>- Ghi dose information (DLP, CTDIvol) cho CT `[Should]` |
| **Tiêu chí chấp nhận** | 1. Mở report editor ≤ 2 giây, template auto-fill<br/>2. Auto-save hoạt động (mất kết nối → khôi phục bản draft)<br/>3. Snippet "bt" → expand "bình thường" trong ≤ 0.5 giây |

### FR-RIS-RPT-002: Workflow trạng thái báo cáo

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý trạng thái báo cáo CĐHA theo quy trình duyệt |
| **Actor** | ACT-05 (BS CĐHA đọc), ACT-13 (BS CĐHA duyệt — senior) |
| **Ưu tiên** | Must |
| **Đầu vào** | Báo cáo draft từ BS đọc |
| **Đầu ra** | Báo cáo final, gửi kết quả về HIS |
| **Quy tắc nghiệp vụ** | - Trạng thái workflow:<br/>&nbsp;&nbsp;**Draft** → BS đang viết, có thể chỉnh sửa tự do<br/>&nbsp;&nbsp;**Preliminary** → BS đọc hoàn tất, chờ duyệt (tùy chọn, cấu hình theo site)<br/>&nbsp;&nbsp;**Final** → BS duyệt (hoặc BS đọc tự duyệt nếu cấu hình cho phép)<br/>&nbsp;&nbsp;**Amended** → Sửa báo cáo final (giữ lại bản gốc + ghi lý do sửa)<br/>&nbsp;&nbsp;**Addendum** → Bổ sung thêm vào báo cáo final (không sửa nội dung gốc)<br/>- Quy tắc duyệt:<br/>&nbsp;&nbsp;Mode 1 (1 cấp): BS đọc → Final (tự duyệt)<br/>&nbsp;&nbsp;Mode 2 (2 cấp): BS đọc → Preliminary → BS senior duyệt → Final<br/>&nbsp;&nbsp;Cấu hình mode theo site/modality<br/>- Khi báo cáo → Final: trigger gửi HL7 ORU^R01 về HIS<br/>- Khi báo cáo → Amended: trigger gửi HL7 ORU^R01 (status = Amended) về HIS<br/>- Audit trail đầy đủ: ai viết, ai duyệt, thời gian, mỗi thay đổi |
| **Tiêu chí chấp nhận** | 1. BS đọc chuyển Draft → Final → HL7 ORU gửi về HIS trong ≤ 5 giây<br/>2. Amended report giữ nguyên bản gốc + ghi version history<br/>3. Mode 2 cấp: BS đọc không thể tự chuyển trạng thái Final |

### FR-RIS-RPT-003: Chữ ký điện tử báo cáo

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ký số (digital signature) trên báo cáo CĐHA khi duyệt Final |
| **Actor** | ACT-05 (BS CĐHA), ACT-13 (BS duyệt) |
| **Ưu tiên** | Must |
| **Đầu vào** | Báo cáo ở trạng thái Preliminary/Draft, chứng thư số (USB Token / HSM) |
| **Đầu ra** | Báo cáo đã ký số, chuyển sang Final |
| **Quy tắc nghiệp vụ** | - Hỗ trợ chữ ký số theo quy định Việt Nam (Nghị định 130/2018/NĐ-CP)<br/>- Tích hợp USB Token: VNPT-CA, Viettel-CA, FPT-CA, BKAV-CA<br/>- Tích hợp HSM (Hardware Security Module) cho ký hàng loạt `[GĐ2]`<br/>- Mỗi báo cáo Final phải có ≥ 1 chữ ký (BS đọc)<br/>- Mode 2 cấp: cần 2 chữ ký (BS đọc + BS duyệt)<br/>- Hiển thị trạng thái ký: ✅ Đã ký (tên BS, thời gian, issuer CA)<br/>- Verify chữ ký: kiểm tra tính toàn vẹn và hiệu lực chứng thư |
| **Tiêu chí chấp nhận** | 1. Ký bằng USB Token thành công trong ≤ 10 giây<br/>2. Báo cáo đã ký → bất kỳ chỉnh sửa nào → invalidate chữ ký<br/>3. Verify chữ ký → hiển thị thông tin CA đúng |

### FR-RIS-RPT-004: Gửi kết quả về HIS (HL7 ORU)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tự động gửi kết quả CĐHA về HIS khi báo cáo đạt trạng thái Final/Amended |
| **Actor** | Hệ thống (tự động) |
| **Ưu tiên** | Must |
| **Đầu vào** | Báo cáo Final/Amended |
| **Đầu ra** | HL7 ORU^R01 message gửi đến HIS |
| **Quy tắc nghiệp vụ** | - Construct HL7 ORU^R01 segments:<br/>&nbsp;&nbsp;MSH: sending/receiving app, message type, control ID<br/>&nbsp;&nbsp;PID: patient demographics<br/>&nbsp;&nbsp;PV1: visit info<br/>&nbsp;&nbsp;OBR: order info, result status (F=Final, C=Corrected/Amended)<br/>&nbsp;&nbsp;OBX-1 (TX): Findings text<br/>&nbsp;&nbsp;OBX-2 (TX): Impression/Conclusion text<br/>&nbsp;&nbsp;OBX-3 (TX): Recommendation `[Should]`<br/>&nbsp;&nbsp;OBX-4 (RP): WADO-RS URL để xem ảnh<br/>&nbsp;&nbsp;OBX-5 (CWE): ICD-10 diagnosis code `[Should]`<br/>- Retry: nếu HIS không trả ACK trong 30 giây → retry (tối đa 3 lần, exponential backoff)<br/>- Dead-letter: sau 3 retry fail → đẩy vào dead-letter queue + alert<br/>- Gửi notification real-time cho BS chỉ định (WebSocket event qua HIS) |
| **Tiêu chí chấp nhận** | 1. Report Final → HL7 ORU gửi đến HIS trong ≤ 5 giây<br/>2. HIS nhận ORU → cập nhật trạng thái chỉ định thành "Đã có kết quả"<br/>3. HL7 gửi fail 3 lần → message trong dead-letter queue + admin nhận alert |

### FR-RIS-RPT-005: In và xuất kết quả

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | In báo cáo CĐHA kèm ảnh, xuất PDF, gửi cho bệnh nhân |
| **Actor** | ACT-05, ACT-06, ACT-12 |
| **Ưu tiên** | Must |
| **Đầu vào** | Báo cáo Final + key images |
| **Đầu ra** | Bản in, file PDF, email/SMS `[Should]` |
| **Quy tắc nghiệp vụ** | - Template in: header bệnh viện, thông tin BN, kết quả, key images, chữ ký BS, QR code (link xem online)<br/>- In key images chất lượng cao (DICOM Print SCU → DICOM Printer)<br/>- Xuất PDF: báo cáo + key images embedded<br/>- Burn CD/DVD (kèm viewer portable) `[Should]`<br/>- Gửi email/SMS cho BN khi có kết quả `[GĐ3]`<br/>- Watermark "BẢN SAO" cho bản in lại (reprint) |
| **Tiêu chí chấp nhận** | 1. In báo cáo kèm 4 key images trong ≤ 10 giây<br/>2. PDF export đúng layout, ảnh rõ nét<br/>3. QR code trên bản in → scan → mở đúng kết quả online |

### FR-RIS-RPT-006: Thống kê & TAT Report

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Báo cáo thống kê hoạt động CĐHA và thời gian xử lý (Turnaround Time) |
| **Actor** | ACT-S02, ACT-14 (Trưởng khoa CĐHA), ACT-10 (Ban Giám đốc) |
| **Ưu tiên** | Must |
| **Đầu vào** | Dữ liệu order, study, report từ RIS database |
| **Đầu ra** | Bảng biểu thống kê, biểu đồ |
| **Quy tắc nghiệp vụ** | - **TAT Metrics** (tính tự động):<br/>&nbsp;&nbsp;TAT-1: Thời gian từ khi nhận order → chụp xong (Order-to-Acquisition)<br/>&nbsp;&nbsp;TAT-2: Thời gian từ chụp xong → báo cáo Final (Acquisition-to-Report)<br/>&nbsp;&nbsp;TAT-3: Tổng TAT = Order → Report Final<br/>- **KPI mục tiêu**:<br/>&nbsp;&nbsp;X-quang Routine: TAT-3 ≤ 60 phút<br/>&nbsp;&nbsp;CT/MRI Routine: TAT-3 ≤ 120 phút<br/>&nbsp;&nbsp;X-quang Stat: TAT-3 ≤ 30 phút<br/>&nbsp;&nbsp;CT Stat: TAT-3 ≤ 60 phút<br/>- **Thống kê sản lượng**: số ca/ngày/tuần/tháng, theo modality, theo BS, theo phòng chụp<br/>- **Workload**: số study/BS, phân bố đều vs lệch<br/>- **Tỷ lệ**: critical findings, amended reports, manual orders<br/>- Export Excel, PDF, biểu đồ |
| **Tiêu chí chấp nhận** | 1. TAT tính đúng (so sánh thủ công 10 cases)<br/>2. Báo cáo tháng generate trong ≤ 30 giây<br/>3. Highlight đỏ các ca vượt KPI mục tiêu |

### FR-RIS-RPT-007: DICOM Structured Report (SR)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Lưu trữ kết quả CĐHA dưới dạng DICOM Structured Report |
| **Actor** | Hệ thống (tự động khi báo cáo Final) |
| **Ưu tiên** | Should `[GĐ2]` |
| **Đầu vào** | Báo cáo Final text + metadata |
| **Đầu ra** | DICOM SR object lưu cùng study |
| **Quy tắc nghiệp vụ** | - Generate DICOM SR (Basic Text SR hoặc Enhanced SR) khi report → Final<br/>- SR linked to study (cùng Study Instance UID)<br/>- Content: coded findings (RadLex/SNOMED CT) `[Could]`, free text findings, impression<br/>- SR hiển thị được trên bất kỳ DICOM viewer hỗ trợ SR |
| **Tiêu chí chấp nhận** | 1. Report Final → DICOM SR generated và stored trong ≤ 5 giây<br/>2. DICOM SR mở được trên viewer tiêu chuẩn (dcm4chee, Horos)<br/>3. Nội dung SR khớp 100% với report text |

---

## C2.8. PHÂN HỆ ĐỒNG BỘ & TÍCH HỢP RIS-HIS (RIS-SYN)

### FR-RIS-SYN-001: Integration Engine RIS-side

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Engine xử lý message HL7 phía RIS/PACS |
| **Actor** | Hệ thống (tự động) |
| **Ưu tiên** | Must |
| **Đầu vào** | HL7 messages từ HIS (ADT, ORM), internal events từ RIS (report final, study received) |
| **Đầu ra** | Processed orders, HL7 ORU gửi về HIS, ACK responses |
| **Quy tắc nghiệp vụ** | - Message listener: TCP/MLLP trên port cấu hình (default 2575)<br/>- Message parser: HL7 v2.4 (hỗ trợ v2.3, v2.5 backward/forward)<br/>- Message routing: ADT → Patient module, ORM → Worklist module<br/>- Message transformation: HL7 field mapping theo cấu hình (không hardcode)<br/>- Queue: persistent queue (survive restart)<br/>- Error handling: parse error → dead-letter + alert, business error → ACK AE<br/>- Throughput: ≥ 100 messages/giây |
| **Tiêu chí chấp nhận** | 1. HL7 message processed trong ≤ 500ms average<br/>2. Service restart → pending messages không mất<br/>3. Parse error → message trong dead-letter + alert gửi admin |

### FR-RIS-SYN-002: Đồng bộ danh mục HIS ↔ RIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đồng bộ danh mục dùng chung giữa HIS và RIS |
| **Actor** | Hệ thống (tự động, batch), ACT-S02 (trigger thủ công) |
| **Ưu tiên** | Must |
| **Đầu vào** | Danh mục từ HIS: dịch vụ CĐHA, bác sĩ, khoa/phòng |
| **Đầu ra** | Danh mục RIS cập nhật, log đồng bộ |
| **Quy tắc nghiệp vụ** | - Đồng bộ tự động: batch mỗi 30 phút hoặc event-driven (khi HIS thay đổi)<br/>- Danh mục đồng bộ:<br/>&nbsp;&nbsp;Dịch vụ CĐHA: mã HIS ↔ procedure code PACS<br/>&nbsp;&nbsp;Bác sĩ: mã BS, tên, chuyên khoa<br/>&nbsp;&nbsp;Khoa/Phòng: mã, tên<br/>&nbsp;&nbsp;Cơ sở: mã, tên<br/>- Conflict resolution: HIS là master → ghi đè RIS<br/>- Audit log cho mỗi thay đổi<br/>- API: REST endpoint hoặc HL7 MFN (Master File Notification) |
| **Tiêu chí chấp nhận** | 1. Thêm dịch vụ CĐHA mới trên HIS → RIS có trong ≤ 5 phút (batch) hoặc ≤ 30 giây (event-driven)<br/>2. 100% danh mục HIS ↔ RIS khớp (kiểm tra đối soát)<br/>3. Log đồng bộ ghi chi tiết: record nào thay đổi, giá trị cũ/mới |

### FR-RIS-SYN-003: Đối soát chỉ định HIS ↔ RIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Kiểm tra và đối soát tính nhất quán giữa chỉ định CĐHA trên HIS và order trên RIS |
| **Actor** | ACT-S02, Hệ thống (tự động, hàng ngày) |
| **Ưu tiên** | Must |
| **Đầu vào** | Order list từ HIS (qua API/view), Order list từ RIS |
| **Đầu ra** | Báo cáo đối soát: khớp, thiếu, thừa, sai trạng thái |
| **Quy tắc nghiệp vụ** | - Chạy tự động mỗi đêm (02:00 AM `[GIẢ ĐỊNH]`)<br/>- So sánh: HIS Order ID ↔ RIS Placer Order Number, trạng thái, thông tin BN<br/>- Phân loại mismatch:<br/>&nbsp;&nbsp;Type A: Có trên HIS nhưng không có trên RIS (HL7 mất)<br/>&nbsp;&nbsp;Type B: Có trên RIS nhưng không có trên HIS (manual order chưa reconcile)<br/>&nbsp;&nbsp;Type C: Trạng thái không khớp (HIS: đã hủy, RIS: đã chụp)<br/>&nbsp;&nbsp;Type D: Thông tin BN không khớp (tên, ngày sinh)<br/>- Alert cho admin nếu mismatch > 0<br/>- Hỗ trợ manual reconcile: admin xác nhận từng case |
| **Tiêu chí chấp nhận** | 1. Đối soát 1.000 orders/ngày hoàn tất trong ≤ 5 phút<br/>2. Phát hiện 100% orders thiếu (inject test case)<br/>3. Báo cáo đối soát hiển thị chi tiết từng mismatch |

### FR-RIS-SYN-004: Notification real-time cho HIS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Gửi thông báo real-time đến HIS/BS lâm sàng khi có sự kiện quan trọng trên RIS |
| **Actor** | Hệ thống (tự động) |
| **Ưu tiên** | Should |
| **Đầu vào** | Events: report finalized, critical finding, study received |
| **Đầu ra** | Notification đến BS chỉ định (popup, badge, sound) |
| **Quy tắc nghiệp vụ** | - Events trigger notification:<br/>&nbsp;&nbsp;Report Final → "Kết quả CĐHA đã sẵn sàng" cho BS chỉ định<br/>&nbsp;&nbsp;Critical Finding → "CẢNH BÁO: Phát hiện bất thường nghiêm trọng" cho BS chỉ định + BS trực `[Must]`<br/>&nbsp;&nbsp;Study Received → "Ảnh CĐHA đã sẵn sàng để xem" `[Should]`<br/>- Transport: WebSocket (primary), long-polling (fallback)<br/>- Critical finding: yêu cầu BS confirm đã đọc (acknowledge)<br/>- Notification history: lưu trữ, tra cứu |
| **Tiêu chí chấp nhận** | 1. Report Final → BS chỉ định nhận notification trong ≤ 10 giây<br/>2. Critical finding notification → BS phải acknowledge trong 30 phút, escalate nếu không<br/>3. Notification hoạt động khi BS đang trên tab HIS khác |

### FR-RIS-SYN-005: API đồng bộ dữ liệu (REST)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | REST API cho HIS gọi trực tiếp đến RIS/PACS (bổ sung cho HL7) |
| **Actor** | HIS backend (caller), RIS backend (provider) |
| **Ưu tiên** | Should |
| **Đầu vào** | HTTP request |
| **Đầu ra** | JSON response |
| **Quy tắc nghiệp vụ** | - Endpoints chính:<br/>&nbsp;&nbsp;`GET /api/v1/orders?patientId={pid}` → danh sách orders BN<br/>&nbsp;&nbsp;`GET /api/v1/orders/{accession}` → chi tiết order<br/>&nbsp;&nbsp;`GET /api/v1/reports/{accession}` → kết quả CĐHA<br/>&nbsp;&nbsp;`GET /api/v1/studies/{studyUid}/thumbnail` → thumbnail ảnh<br/>&nbsp;&nbsp;`POST /api/v1/orders` → tạo order (alternative cho HL7)<br/>- Authentication: OAuth2 / API Key<br/>- Rate limit: 1.000 requests/phút per client<br/>- Versioning: `/api/v1/`, `/api/v2/`<br/>- Response format: JSON, pagination, error codes |
| **Tiêu chí chấp nhận** | 1. API response time ≤ 500ms (p95)<br/>2. API documentation (OpenAPI/Swagger) đầy đủ<br/>3. Rate limit hoạt động: request thứ 1.001 → HTTP 429 |

---

*Tiếp theo: [Phần C3 — Use Cases](./C3-USE-CASES.md)*
