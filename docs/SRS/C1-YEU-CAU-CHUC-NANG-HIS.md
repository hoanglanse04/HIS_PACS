# PHẦN C1 — YÊU CẦU CHỨC NĂNG HIS

> **Mã phần**: SRS-PART-C1  
> **Phiên bản**: 2.0 | **Ngày**: 25/03/2026  
> **Quy ước mã**: FR-HIS-{MODULE}-{STT}

---

## C1.1. PHÂN HỆ HỆ THỐNG (HIS-SYS)

### FR-HIS-SYS-001: Quản lý người dùng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo, sửa, khóa, xóa tài khoản người dùng hệ thống HIS |
| **Actor** | ACT-S01 (SysAdmin) |
| **Ưu tiên** | Must |
| **Đầu vào** | Họ tên, username, mật khẩu, email, SĐT, khoa/phòng, chức danh, trạng thái |
| **Đầu ra** | Tài khoản người dùng được tạo/cập nhật trong hệ thống |
| **Quy tắc nghiệp vụ** | - Username duy nhất toàn hệ thống<br/>- Mật khẩu ≥ 8 ký tự, chứa chữ hoa/thường/số/đặc biệt<br/>- Tài khoản bị khóa sau 5 lần đăng nhập sai liên tiếp<br/>- Mỗi user phải gắn ≥ 1 role<br/>- Hỗ trợ LDAP/AD sync `[Should]` |
| **Tiêu chí chấp nhận** | 1. Tạo user mới thành công trong ≤ 30 giây<br/>2. User bị khóa sau 5 lần sai mật khẩu<br/>3. Không tạo được 2 user cùng username |

### FR-HIS-SYS-002: Phân quyền RBAC

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Phân quyền dựa trên vai trò (Role-Based Access Control), hỗ trợ phân quyền theo module, chức năng, khoa/phòng, cơ sở |
| **Actor** | ACT-S01 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Mỗi role gồm tập hợp permissions (xem/thêm/sửa/xóa/in/duyệt)<br/>- Permission granularity: module → menu → chức năng → thao tác<br/>- Hỗ trợ phân quyền theo khoa: BS khoa Nội chỉ xem BN khoa Nội<br/>- Hỗ trợ phân quyền theo cơ sở (multi-site)<br/>- Role kế thừa (role hierarchy) `[Should]` |
| **Tiêu chí chấp nhận** | 1. User không có quyền → không thấy menu/chức năng<br/>2. BS khoa A không xem được BN khoa B (khi bật phân quyền khoa)<br/>3. Thay đổi permission có hiệu lực ngay (không cần logout) |

### FR-HIS-SYS-003: Quản lý danh mục dùng chung

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý tất cả danh mục dùng chung toàn viện |
| **Actor** | ACT-S01 |
| **Ưu tiên** | Must |
| **Danh sách danh mục** | - ICD-10 (≥ 14.000 mã)<br/>- Danh mục dịch vụ kỹ thuật (≥ 5.000 dịch vụ)<br/>- Danh mục thuốc BHYT + dịch vụ (≥ 10.000 thuốc)<br/>- Danh mục khoa/phòng<br/>- Danh mục nhân viên/bác sĩ<br/>- Danh mục giường bệnh<br/>- Danh mục vật tư y tế<br/>- Danh mục nhà cung cấp<br/>- Danh mục địa chính (tỉnh/huyện/xã)<br/>- Danh mục dân tộc, nghề nghiệp, quốc tịch |
| **Quy tắc nghiệp vụ** | - Mỗi danh mục có: mã, tên, mô tả, trạng thái, ngày hiệu lực<br/>- Hỗ trợ import Excel/CSV<br/>- Hỗ trợ versioning (lưu lịch sử thay đổi)<br/>- Danh mục ICD-10 và thuốc BHYT phải đồng bộ với dữ liệu BHXH |
| **Tiêu chí chấp nhận** | 1. Import 10.000 dòng danh mục thuốc trong ≤ 60 giây<br/>2. Tìm kiếm danh mục trả kết quả trong ≤ 1 giây<br/>3. Thay đổi danh mục được ghi audit log |

### FR-HIS-SYS-004: Cấu hình hệ thống

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Cấu hình tham số hệ thống không cần thay đổi mã nguồn |
| **Actor** | ACT-S01 |
| **Ưu tiên** | Must |
| **Tham số cấu hình** | - Thông tin bệnh viện (tên, địa chỉ, mã, logo)<br/>- Cấu hình cơ sở (multi-site)<br/>- Template in ấn (header/footer)<br/>- Quy tắc đánh mã BN, mã lần khám<br/>- Timeout session<br/>- Cấu hình tích hợp (endpoint BHXH, HL7, DICOM) |

### FR-HIS-SYS-005: Audit Trail

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhật ký tất cả thao tác quan trọng trên hệ thống |
| **Actor** | Tự động (System) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Ghi log: user, timestamp, IP, action, module, đối tượng dữ liệu, giá trị cũ/mới<br/>- Log bắt buộc cho: truy cập hồ sơ BN, thay đổi dữ liệu y khoa, thay đổi viện phí, xóa dữ liệu<br/>- Audit log không thể sửa/xóa (append-only)<br/>- Lưu trữ ≥ 5 năm |
| **Tiêu chí chấp nhận** | 1. Mọi thao tác CRUD trên dữ liệu BN đều có audit log<br/>2. Audit log không bị xóa bởi bất kỳ user nào kể cả admin<br/>3. Tìm kiếm audit log theo user/ngày/module trong ≤ 3 giây |

### FR-HIS-SYS-006: Single Sign-On (SSO)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đăng nhập một lần sử dụng cho cả HIS và RIS/PACS |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Token-based authentication (JWT/OAuth2)<br/>- Thời gian token: 8 giờ (ca làm việc), refresh tự động<br/>- Đăng xuất từ HIS → đăng xuất khỏi PACS |

---

## C1.2. PHÂN HỆ CẤP CỨU (HIS-ER)

### FR-HIS-ER-001: Tiếp nhận cấp cứu

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tiếp nhận bệnh nhân cấp cứu nhanh, không yêu cầu đầy đủ thông tin hành chính ban đầu |
| **Actor** | ACT-04 (BS cấp cứu), ACT-07 (Điều dưỡng) |
| **Ưu tiên** | Must |
| **Đầu vào** | Tên BN (có thể tạm), giới tính, tuổi ước lượng, lý do cấp cứu, phương tiện đến |
| **Quy tắc nghiệp vụ** | - Cho phép tạo hồ sơ tạm khi chưa có giấy tờ (mã tạm: ER-YYYYMMDD-XXX)<br/>- Bổ sung thông tin hành chính sau khi ổn định<br/>- Phân loại cấp cứu theo thang ESI (5 mức) hoặc START triage<br/>- Phát sinh HL7 ADT^A04 với visit type = Emergency |
| **Tiêu chí chấp nhận** | 1. Tạo hồ sơ cấp cứu tạm trong ≤ 15 giây (không cần đầy đủ thông tin)<br/>2. Cập nhật thông tin hành chính sau không mất dữ liệu y khoa |

### FR-HIS-ER-002: Phân loại cấp cứu (Triage)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Phân loại mức độ cấp cứu để ưu tiên xử trí |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - 5 mức: Hồi sinh (Đỏ) → Cấp cứu (Cam) → Khẩn (Vàng) → Ít khẩn (Xanh lá) → Không cấp cứu (Xanh dương)<br/>- Ghi nhận sinh hiệu, Glasgow, SpO2<br/>- Timestamp phân loại và bắt đầu xử trí (tính TAT cấp cứu) |

### FR-HIS-ER-003: Xử trí cấp cứu

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhận các biện pháp xử trí cấp cứu, y lệnh, thuốc cấp cứu |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Chỉ định CLS cấp cứu (ưu tiên stat → HL7 ORM với priority = STAT)<br/>- Kê đơn thuốc cấp cứu (thuốc cấp cứu có danh mục riêng)<br/>- Hướng xử trí: nhập viện, chuyển viện, về, tử vong tại cấp cứu<br/>- Theo dõi thời gian lưu tại cấp cứu |

### FR-HIS-ER-004: Chuyển đổi từ cấp cứu

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Chuyển BN từ cấp cứu sang nội trú hoặc ngoại trú |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Gộp hồ sơ tạm vào hồ sơ BN chính (nếu đã có)<br/>- Chuyển toàn bộ dữ liệu y khoa sang module đích<br/>- Phát sinh ADT^A01 (nhập viện) hoặc ADT^A04 (đăng ký ngoại trú) |

---

## C1.3. PHÂN HỆ ĐĂNG KÝ (HIS-REG)

### FR-HIS-REG-001: Đăng ký bệnh nhân mới

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nhập thông tin hành chính bệnh nhân lần đầu đến viện |
| **Actor** | ACT-01 (Nhân viên tiếp đón) |
| **Ưu tiên** | Must |
| **Đầu vào** | Họ tên, ngày sinh, giới tính, CMND/CCCD, địa chỉ (tỉnh/huyện/xã), SĐT, email, nghề nghiệp, dân tộc, quốc tịch, người thân liên hệ |
| **Đầu ra** | Mã bệnh nhân (PID) duy nhất toàn viện, hồ sơ BN |
| **Quy tắc nghiệp vụ** | - Mã PID: auto-generated, format configurable (VD: BN-{SITE}-{YYYYMMDD}-{SEQ})<br/>- Kiểm tra trùng BN: matching algorithm (tên + ngày sinh + CMND) → cảnh báo nếu trùng > 90%<br/>- Chụp ảnh chân dung (webcam) `[Should]`<br/>- Quét/đọc thẻ BHYT (barcode, QR, chip NFC)<br/>- Phát sinh HL7 ADT^A04 → truyền sang PACS |
| **Tiêu chí chấp nhận** | 1. Đăng ký BN mới hoàn tất trong ≤ 60 giây<br/>2. Trùng BN được cảnh báo trước khi tạo mới<br/>3. Mã PID duy nhất toàn viện (5 cơ sở) |

### FR-HIS-REG-002: Quản lý thẻ BHYT

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đọc và kiểm tra giá trị thẻ BHYT |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Đọc mã thẻ BHYT (15 ký tự, format mới)<br/>- Kiểm tra online qua API cổng BHXH giám định<br/>- Xác định: đúng tuyến/trái tuyến/cấp cứu/thông tuyến<br/>- Xác định tỷ lệ đồng chi trả: 100%, 95%, 80%<br/>- Kiểm tra 5 năm liên tục<br/>- Lưu log mỗi lần kiểm tra (timestamp, kết quả, mã giao dịch BHXH) |
| **Tiêu chí chấp nhận** | 1. Kiểm tra thẻ BHYT online ≤ 5 giây<br/>2. Kết quả kiểm tra hiển thị rõ: hợp lệ/hết hạn/trái tuyến<br/>3. Log kiểm tra không thể xóa |

### FR-HIS-REG-003: Đăng ký khám bệnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đăng ký lượt khám, chọn phòng khám, phát số thứ tự |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Chọn phòng khám/chuyên khoa (gợi ý theo lịch BS, lượng BN chờ)<br/>- Hàng đợi thông minh: ưu tiên cấp cứu > người cao tuổi > BHYT > dịch vụ<br/>- In phiếu khám (mã vạch/QR)<br/>- Hỗ trợ đăng ký nhiều phòng khám trong 1 lần đến<br/>- Đăng ký khám theo hẹn (appointment) `[Should]` |
| **Tiêu chí chấp nhận** | 1. Phát số thứ tự ≤ 5 giây sau đăng ký<br/>2. Số thứ tự hiển thị trên màn hình LCD phòng chờ |

### FR-HIS-REG-004: Nhập viện nội trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Chuyển BN từ ngoại trú/cấp cứu sang nội trú |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Phân giường/phòng/khoa (gợi ý theo chuyên khoa, giường trống)<br/>- In giấy nhập viện, cam kết điều trị<br/>- Phát sinh HL7 ADT^A01 → PACS<br/>- Kế thừa dữ liệu từ lần khám ngoại trú (nếu có) |

### FR-HIS-REG-005: Chuyển khoa / Chuyển giường

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Chuyển BN giữa các khoa/phòng/giường |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Cập nhật trạng thái giường (cũ: trống, mới: có BN)<br/>- Ghi nhận lý do chuyển, BS chỉ định chuyển<br/>- Phát sinh HL7 ADT^A02 → PACS |

### FR-HIS-REG-006: Ra viện

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quy trình xuất viện bệnh nhân nội trú |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Kiểm tra: bệnh án đã tổng kết, viện phí đã thanh toán, thuốc đã hoàn trả<br/>- Phát sinh HL7 ADT^A03 → PACS<br/>- In giấy ra viện, tóm tắt bệnh án, đơn thuốc ra viện<br/>- Giải phóng giường |

---

## C1.4. PHÂN HỆ KHÁM BỆNH (HIS-OPD)

### FR-HIS-OPD-001: Danh sách chờ khám

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hiển thị danh sách BN chờ khám theo phòng, hỗ trợ gọi số |
| **Actor** | ACT-02 (BS ngoại trú) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Sắp xếp: cấp cứu > ưu tiên > thường (FIFO trong cùng nhóm)<br/>- Lọc: chờ khám, đang khám, chờ CLS, đã khám<br/>- Gọi số: tích hợp màn hình LCD/TV (hiển thị số + tên BN + phòng)<br/>- Real-time update khi có BN mới đăng ký |
| **Tiêu chí chấp nhận** | 1. Danh sách tự cập nhật ≤ 3 giây khi có BN mới<br/>2. Gọi số hiển thị trên TV phòng chờ ≤ 2 giây |

### FR-HIS-OPD-002: Khám bệnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhận thông tin khám lâm sàng |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Đầu vào** | Lý do khám, tiền sử (bệnh, gia đình, dị ứng), triệu chứng, khám lâm sàng, sinh hiệu (mạch, HA, nhiệt độ, cân nặng, chiều cao, SpO2, nhịp thở) |
| **Đầu ra** | Chẩn đoán sơ bộ/xác định (ICD-10), chẩn đoán phụ |
| **Quy tắc nghiệp vụ** | - Chẩn đoán ICD-10 bắt buộc (autocomplete từ danh mục)<br/>- Hỗ trợ template khám theo chuyên khoa<br/>- Cảnh báo dị ứng thuốc đã khai báo trước đó<br/>- Hiển thị lịch sử khám trước (last 10 visits) |
| **Tiêu chí chấp nhận** | 1. Tìm ICD-10 trả ≤ 10 kết quả trong ≤ 0.5 giây<br/>2. Load lịch sử khám ≤ 2 giây |

### FR-HIS-OPD-003: Kê đơn thuốc

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Kê đơn thuốc từ danh mục, kiểm tra tương tác và dị ứng |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Chọn thuốc từ danh mục (tên hoạt chất/tên thương mại, autocomplete)<br/>- Nhập liều, tần suất, đường dùng, số ngày<br/>- Kiểm tra tương tác thuốc tự động (drug-drug interaction) → cảnh báo mức Serious/Moderate<br/>- Kiểm tra dị ứng thuốc đã khai báo<br/>- Phân loại: thuốc BHYT / dịch vụ<br/>- Hỗ trợ đơn mẫu (order set/template)<br/>- In đơn thuốc theo mẫu BYT (Thông tư 52/2017) |
| **Tiêu chí chấp nhận** | 1. Cảnh báo tương tác thuốc hiển thị trong ≤ 1 giây sau kê<br/>2. Đơn mẫu áp dụng trong ≤ 2 giây<br/>3. In đơn đúng format BYT |

### FR-HIS-OPD-004: Chỉ định cận lâm sàng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo chỉ định xét nghiệm, CĐHA, thủ thuật |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - **Chỉ định CĐHA → PACS**: chọn dịch vụ CĐHA, nhập thông tin lâm sàng (clinical indication), chọn ưu tiên (routine/urgent/stat) → Phát sinh HL7 ORM^O01 → Integration Engine → PACS<br/>- **Chỉ định xét nghiệm → LIS** `[GĐ2]`<br/>- Chỉ định thủ thuật<br/>- Hỗ trợ bộ chỉ định mẫu (order set)<br/>- Hiển thị giá ước tính (BHYT/dịch vụ) |
| **Tiêu chí chấp nhận** | 1. Chỉ định CĐHA → PACS nhận được trong ≤ 5 giây<br/>2. Trạng thái chỉ định cập nhật real-time trên HIS |

### FR-HIS-OPD-005: Xem kết quả cận lâm sàng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hiển thị kết quả CLS (xét nghiệm, CĐHA) cho BS lâm sàng |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Nhận HL7 ORU^R01 từ PACS → hiển thị kết quả text<br/>- Xem ảnh DICOM trực tiếp trong HIS (embedded viewer qua WADO-RS)<br/>- Trạng thái: đã gửi → đang chụp → đã có kết quả (real-time update)<br/>- Xem lịch sử CLS các lần khám trước<br/>- Notification khi có kết quả mới |
| **Tiêu chí chấp nhận** | 1. Kết quả hiển thị ≤ 3 giây sau PACS trả ORU<br/>2. Ảnh DICOM load trong viewer nhúng ≤ 5 giây |

### FR-HIS-OPD-006: Kết thúc khám

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hoàn tất phiếu khám, hẹn tái khám, chuyển viện phí |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Kiểm tra đầy đủ: chẩn đoán, đơn thuốc (nếu có), CLS (nếu có)<br/>- Hẹn tái khám (ghi ngày hẹn, lý do)<br/>- Tự động chuyển viện phí → quầy thu<br/>- In phiếu: toa thuốc, phiếu CLS, biên lai |

---

## C1.5. PHÂN HỆ CẬN LÂM SÀNG (HIS-CLS)

### FR-HIS-CLS-001: Quản lý chỉ định CLS tập trung

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý toàn bộ chỉ định CLS từ cả ngoại trú và nội trú, theo dõi trạng thái |
| **Actor** | ACT-02, ACT-03, ACT-04 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Dashboard chỉ định: theo khoa, theo loại (XN/CĐHA/TDCN), theo trạng thái<br/>- Trạng thái pipeline: Đã chỉ định → Đã thanh toán → Đang thực hiện → Đã có kết quả<br/>- Sửa/hủy chỉ định: chỉ khi chưa thực hiện; hủy → gửi HL7 ORM^O01 (Cancel)<br/>- Cảnh báo chỉ định trùng (cùng dịch vụ, cùng BN, trong 24h) |
| **Tiêu chí chấp nhận** | 1. Dashboard CLS load ≤ 3 giây<br/>2. Hủy chỉ định chưa thực hiện thành công 100%<br/>3. Không hủy được chỉ định đã thực hiện |

### FR-HIS-CLS-002: Nhận kết quả từ RIS/PACS

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tiếp nhận kết quả CĐHA qua HL7 ORU^R01 từ PACS |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Parse ORU: mapping accession number → order ID trong HIS<br/>- Lưu kết quả text + link xem ảnh (WADO URL)<br/>- Cập nhật trạng thái chỉ định = "Đã có kết quả"<br/>- Gửi notification cho BS chỉ định |

---

## C1.6. PHÂN HỆ ĐIỀU TRỊ NỘI TRÚ (HIS-IPD)

### FR-HIS-IPD-001: Quản lý giường bệnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý sơ đồ giường theo khoa/phòng, trạng thái giường |
| **Actor** | ACT-03, ACT-07 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Sơ đồ giường trực quan (visual bed map) theo khoa/phòng<br/>- Trạng thái: trống, có BN, đang dọn, bảo trì, cách ly<br/>- Phân giường tự động (gợi ý theo chuyên khoa, giới tính, loại giường)<br/>- Thông tin giường: loại (thường/dịch vụ/ICU), giá, thiết bị đi kèm |
| **Tiêu chí chấp nhận** | 1. Bed map load ≤ 3 giây, hiển thị đúng trạng thái real-time<br/>2. Phân giường gợi ý ≤ 3 giường phù hợp |

### FR-HIS-IPD-002: Y lệnh điều trị

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo y lệnh hàng ngày: thuốc, CLS, thủ thuật, chế độ ăn, chăm sóc |
| **Actor** | ACT-03 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Y lệnh thuốc: kê theo ngày, liều/lần, số lần/ngày, đường dùng<br/>- Y lệnh CLS nội trú → PACS/LIS (HL7 ORM^O01 với visit type = Inpatient)<br/>- Y lệnh chăm sóc: chế độ ăn, theo dõi sinh hiệu, chăm sóc vết thương<br/>- Copy y lệnh từ ngày trước (nếu không thay đổi)<br/>- Duyệt y lệnh bởi BS trưởng khoa `[Should]` |

### FR-HIS-IPD-003: Diễn biến bệnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhận diễn biến bệnh hàng ngày theo mẫu BYT |
| **Actor** | ACT-03 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Ghi theo template: ngày/giờ, diễn biến, xử trí, BS ký<br/>- Hỗ trợ text + structured data<br/>- Liên kết kết quả CLS cùng ngày |

### FR-HIS-IPD-004: Phiếu chăm sóc điều dưỡng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Điều dưỡng ghi nhận thực hiện y lệnh, theo dõi sinh hiệu, chăm sóc |
| **Actor** | ACT-07 (Điều dưỡng) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Nhập sinh hiệu: mạch, HA, nhiệt độ, nhịp thở, SpO2 (biểu đồ tự động)<br/>- Xác nhận thực hiện y lệnh (check-off)<br/>- Ghi nhận chăm sóc: tiêm, truyền, thay băng, vệ sinh<br/>- Cảnh báo sinh hiệu bất thường |

### FR-HIS-IPD-005: Tổng kết bệnh án

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tổng kết bệnh án khi xuất viện |
| **Actor** | ACT-03 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Chẩn đoán ra viện (ICD-10 chính + phụ)<br/>- Tóm tắt bệnh án: lý do, quá trình, kết quả<br/>- Hướng xử trí: ra viện, chuyển viện, trốn viện, tử vong<br/>- Đơn thuốc ra viện<br/>- Hẹn tái khám<br/>- In bệnh án (bản cứng + PDF lưu trữ) |

### FR-HIS-IPD-006: Hội chẩn

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo phiếu hội chẩn, ghi nhận kết quả |
| **Actor** | ACT-03 |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Yêu cầu hội chẩn: lý do, BS yêu cầu, BS tham gia<br/>- Kết quả hội chẩn: chẩn đoán, hướng điều trị<br/>- Xem ảnh CĐHA phục vụ hội chẩn (link PACS viewer) |

---

## C1.7. PHÂN HỆ ĐIỀU TRỊ NGOẠI TRÚ (HIS-OPT)

### FR-HIS-OPT-001: Quản lý điều trị ngoại trú dài ngày

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý BN điều trị ngoại trú dài ngày: hóa trị, thận nhân tạo, phục hồi chức năng |
| **Actor** | ACT-02 |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Lập phác đồ điều trị (protocol/cycle)<br/>- Lịch hẹn điều trị định kỳ<br/>- Theo dõi tiến trình (session tracking)<br/>- Tính phí theo đợt/theo phiên |

---

## C1.8. PHÂN HỆ BỆNH ÁN ĐIỆN TỬ (HIS-EMR)

### FR-HIS-EMR-001: Bệnh án ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bệnh án ngoại trú theo mẫu quy định Bộ Y tế |
| **Actor** | ACT-02 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Theo mẫu Thông tư 46/2018/TT-BYT<br/>- Bao gồm: hành chính, lý do khám, bệnh sử, khám LS, CLS, chẩn đoán, điều trị<br/>- Tích hợp kết quả CLS tự động<br/>- Hỗ trợ template theo chuyên khoa<br/>- Ký điện tử bởi BS khám `[Must]` |

### FR-HIS-EMR-002: Bệnh án nội trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bệnh án nội trú đầy đủ theo mẫu Bộ Y tế |
| **Actor** | ACT-03 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Mẫu bệnh án theo Thông tư 46/2018<br/>- Gồm: bệnh án nhập viện, diễn biến, y lệnh, phiếu chăm sóc, kết quả CLS, tổng kết<br/>- Phiếu theo dõi chức năng sống (biểu đồ tự vẽ)<br/>- Phiếu công khai thuốc, VTYT<br/>- In bệnh án hoàn chỉnh (bản cứng) |

### FR-HIS-EMR-003: Phiếu chuyên khoa

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Các phiếu chuyên khoa theo mẫu BYT |
| **Actor** | ACT-02, ACT-03 |
| **Ưu tiên** | Must |
| **Danh sách phiếu** | - Phiếu khám thai<br/>- Phiếu theo dõi sản<br/>- Phiếu ghi chép phẫu thuật<br/>- Phiếu gây mê hồi sức<br/>- Phiếu truyền máu<br/>- Phiếu chăm sóc người bệnh nặng/nguy kịch<br/>- Phiếu cam kết/đồng thuận |

---

## C1.9. PHÂN HỆ PHẪU THUẬT - THỦ THUẬT (HIS-SUR)

### FR-HIS-SUR-001: Lên lịch phẫu thuật

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đăng ký và lên lịch phẫu thuật/thủ thuật |
| **Actor** | ACT-10 (BS phẫu thuật) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Đăng ký mổ: BN, loại PT/TT, ưu tiên (cấp cứu/phiên), BS mổ chính, BS gây mê<br/>- Lịch phòng mổ: calendar view, tránh trùng phòng/ekip<br/>- Kiểm tra tiền mê (checklist pre-op)<br/>- Cảnh báo xung đột lịch |
| **Tiêu chí chấp nhận** | 1. Lịch mổ hiển thị calendar ≤ 2 giây<br/>2. Cảnh báo trùng phòng/BS |

### FR-HIS-SUR-002: Phiếu phẫu thuật / Thủ thuật

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhận chi tiết ca phẫu thuật/thủ thuật |
| **Actor** | ACT-10 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Phiếu mô tả PT: chẩn đoán trước/sau mổ, phương pháp, diễn biến, biến chứng<br/>- Phiếu gây mê: loại gây mê, thuốc, theo dõi<br/>- Biên bản kiểm đếm dụng cụ, gạc<br/>- Ghi nhận ekip: BS mổ, BS gây mê, ĐD dụng cụ, ĐD gây mê<br/>- Liên kết ảnh CĐHA trước mổ (từ PACS) |

### FR-HIS-SUR-003: Hồi tỉnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Theo dõi BN sau mổ tại phòng hồi tỉnh |
| **Actor** | ACT-07 |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Theo dõi sinh hiệu 15 phút/lần<br/>- Đánh giá Aldrete Score<br/>- Chuyển khoa khi đủ tiêu chuẩn |

---

## C1.10. PHÂN HỆ DƯỢC (HIS-PHA)

### FR-HIS-PHA-001: Quản lý danh mục thuốc

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý danh mục thuốc toàn viện |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Theo DM thuốc BHYT (Thông tư 30/2018 và cập nhật)<br/>- Thông tin: tên hoạt chất, tên thương mại, dạng bào chế, hàm lượng, đơn vị, giá nhập, giá bán, nhóm thuốc<br/>- Phân nhóm: BHYT, dịch vụ, gây nghiện, hướng thần, kháng sinh<br/>- Mapping mã thuốc BHXH<br/>- Quản lý tương tác thuốc (drug interaction database) |

### FR-HIS-PHA-002: Quản lý kho thuốc

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nhập/xuất/tồn kho thuốc |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Nhập kho: từ nhà cung cấp (phiếu nhập, hóa đơn, kiểm tra chất lượng)<br/>- Xuất kho: theo đơn BN, xuất khoa lâm sàng, xuất hủy<br/>- Chuyển kho giữa các kho (kho chính → kho lẻ → tủ trực)<br/>- Kiểm kê (đối soát số liệu vs thực tế)<br/>- FEFO: xuất thuốc hết hạn trước<br/>- Cảnh báo: tồn kho tối thiểu, thuốc sắp hết hạn (30/60/90 ngày) |
| **Tiêu chí chấp nhận** | 1. Xuất kho theo đơn ≤ 10 giây<br/>2. Cảnh báo hết hạn hiển thị chính xác |

### FR-HIS-PHA-003: Phát thuốc ngoại trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Phát thuốc cho BN ngoại trú theo đơn |
| **Actor** | ACT-08 (Dược sĩ) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Nhận đơn từ module khám bệnh (tự động xuất hiện trên danh sách chờ phát)<br/>- Kiểm tra tồn kho<br/>- Phát thuốc, xác nhận phát (scan barcode thuốc `[Should]`)<br/>- In nhãn thuốc (tên BN, liều dùng, cách dùng)<br/>- Xử lý thay thế thuốc (khi hết thuốc kê, dược sĩ đề xuất → BS phê duyệt) |

### FR-HIS-PHA-004: Phát thuốc nội trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Phát thuốc cho BN nội trú theo y lệnh |
| **Actor** | ACT-08 |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Nhận y lệnh thuốc từ module nội trú<br/>- Phát thuốc theo ngày/theo đợt/theo xuất (đơn thuốc hàng ngày)<br/>- Cắt thuốc nội trú (khi BN ra viện, chuyển khoa, đổi thuốc)<br/>- Hoàn trả thuốc: thuốc chưa sử dụng trả về kho |

### FR-HIS-PHA-005: Quản lý thuốc đặc biệt

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý thuốc gây nghiện, hướng thần, kháng sinh |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Thuốc gây nghiện/hướng thần: sổ theo dõi riêng, giới hạn kê đơn (7/10/30 ngày), người bệnh ký nhận<br/>- Kháng sinh: AMS - theo dõi sử dụng, kháng sinh dự trữ cần phê duyệt trưởng khoa<br/>- Cảnh báo vượt liều, vượt thời gian sử dụng |

### FR-HIS-PHA-006: Báo cáo dược

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Các báo cáo quản lý dược |
| **Ưu tiên** | Must |
| **Danh sách báo cáo** | - BC xuất nhập tồn (theo kho, theo thuốc, theo thời gian)<br/>- BC sử dụng thuốc (theo khoa, theo BS, theo nhóm thuốc)<br/>- BC thuốc BHYT vs dịch vụ<br/>- BC thuốc gây nghiện/hướng thần (mẫu BYT)<br/>- BC thuốc sắp hết hạn<br/>- BC kháng sinh (AMS report) |

---

## C1.11. PHÂN HỆ THANH TOÁN VIỆN PHÍ (HIS-BIL)

### FR-HIS-BIL-001: Quản lý bảng giá

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý bảng giá dịch vụ y tế |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Bảng giá BHYT (theo Thông tư liên tịch): giá trần thanh toán<br/>- Bảng giá dịch vụ (yêu cầu): bệnh viện tự quyết<br/>- Phân giá theo đối tượng: BHYT, viện phí, miễn phí, nước ngoài<br/>- Quản lý hiệu lực giá (effective date): giá cũ → giá mới, không ảnh hưởng hóa đơn đã xuất<br/>- Import bảng giá Excel |

### FR-HIS-BIL-002: Tạm ứng viện phí

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thu tạm ứng cho BN nội trú |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Thu tạm ứng (tiền mặt, chuyển khoản)<br/>- Sổ tạm ứng: theo dõi tổng tạm ứng vs tổng phí phát sinh<br/>- Cảnh báo khi tạm ứng sắp hết (configurable threshold) |

### FR-HIS-BIL-003: Tính phí tự động

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tự động tổng hợp chi phí từ các module |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Tổng hợp: khám, thuốc, CĐHA, XN, giường, PT/TT, VTYT, dinh dưỡng<br/>- Phân loại: BHYT chi trả / BN đồng chi trả / BN tự trả / Ngoài DM BHYT<br/>- Áp dụng quy tắc BHYT: trần thanh toán, quy tắc tổng quát, quy tắc đặc biệt<br/>- Tính phí chênh lệch giường bệnh yêu cầu |
| **Tiêu chí chấp nhận** | 1. Tính phí tự động chính xác 100% so với tính tay<br/>2. Tổng hợp chi phí ≤ 5 giây |

### FR-HIS-BIL-004: Thu phí / Thanh toán

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thu tiền từ bệnh nhân |
| **Actor** | ACT-09 (NV viện phí) |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Phương thức: tiền mặt, chuyển khoản, thẻ (POS), ví điện tử, QR code<br/>- In hóa đơn/biên lai (kết nối hóa đơn điện tử `[Should - GĐ2]`)<br/>- Miễn giảm viện phí (theo quyết định giám đốc BV)<br/>- Hoàn phí (khi hủy dịch vụ chưa thực hiện) |

### FR-HIS-BIL-005: Quyết toán nội trú

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tổng hợp và quyết toán toàn bộ chi phí đợt điều trị nội trú |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Bảng kê chi phí KCB (mẫu C79a-HD theo BHXH)<br/>- Quyết toán BHYT: tính phần BHYT, phần BN<br/>- So sánh tạm ứng vs thực tế → trả lại hoặc thu thêm<br/>- In bảng kê chi tiết cho BN |

---

## C1.12. PHÂN HỆ NHÀ THUỐC (HIS-RPH)

### FR-HIS-RPH-001: Bán lẻ thuốc

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Bán thuốc lẻ cho bệnh nhân (không qua đơn BS nội viện) |
| **Actor** | ACT-11 (NV nhà thuốc) |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Bán theo đơn hoặc không đơn (OTC)<br/>- Quản lý kho nhà thuốc riêng<br/>- In hóa đơn bán lẻ<br/>- Kiểm tra thuốc kê đơn phải có đơn BS |

### FR-HIS-RPH-002: Quản lý kho nhà thuốc

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Nhập/xuất/tồn kho nhà thuốc (tách biệt kho bệnh viện) |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Kho nhà thuốc independent vs kho BV<br/>- Chuyển kho: BV → nhà thuốc<br/>- Báo cáo doanh thu nhà thuốc |

---

## C1.13. PHÂN HỆ KẾ HOẠCH - TỔNG HỢP (HIS-PLN)

### FR-HIS-PLN-001: Kế hoạch giường bệnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Thống kê và kế hoạch công suất giường |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Tỷ lệ sử dụng giường theo khoa/thời gian<br/>- Dự báo nhu cầu giường<br/>- Ngày điều trị trung bình theo khoa/bệnh |

### FR-HIS-PLN-002: Tổng hợp số liệu hoạt động

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tổng hợp số liệu hoạt động toàn viện |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Số lượt khám ngoại trú/nội trú theo ngày/tháng/quý/năm<br/>- Số ca phẫu thuật theo loại<br/>- Số ca CĐHA theo loại<br/>- Doanh thu theo nguồn (BHYT/dịch vụ/khác) |

---

## C1.14. PHÂN HỆ BÁO CÁO QUẢN TRỊ (HIS-RPT)

### FR-HIS-RPT-001: Dashboard quản trị

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Dashboard tổng quan cho ban giám đốc |
| **Actor** | ACT-14 (Ban giám đốc) |
| **Ưu tiên** | Must |
| **Nội dung** | - Số BN khám/ngày (ngoại trú, nội trú, cấp cứu)<br/>- Công suất giường (% sử dụng)<br/>- Doanh thu ngày/tháng<br/>- Số ca CĐHA/ngày<br/>- Cảnh báo: thuốc sắp hết, giường đầy, SLA tích hợp |
| **Tiêu chí chấp nhận** | 1. Dashboard load ≤ 5 giây<br/>2. Dữ liệu refresh ≤ 5 phút |

### FR-HIS-RPT-002: Báo cáo Bộ Y tế

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Báo cáo theo mẫu quy định Bộ Y tế |
| **Ưu tiên** | Must |
| **Danh sách** | - Báo cáo hoạt động bệnh viện (tổng hợp)<br/>- Báo cáo bệnh truyền nhiễm<br/>- Báo cáo 4210 (tổng hợp KCB, dữ liệu cho BHXH)<br/>- Báo cáo tử vong<br/>- Báo cáo sử dụng kháng sinh<br/>- Export: Excel, PDF |

### FR-HIS-RPT-003: Báo cáo CĐHA

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Báo cáo hoạt động chẩn đoán hình ảnh |
| **Ưu tiên** | Must |
| **Nội dung** | - Số ca chụp theo loại modality (CT, MRI, XR, US)<br/>- TAT (turnaround time): trung bình, P95<br/>- Workload BS CĐHA (số ca đọc/ngày)<br/>- Tỷ lệ kết quả bất thường<br/>- Thống kê theo khoa chỉ định |

### FR-HIS-RPT-004: Báo cáo tùy chỉnh

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Công cụ tạo báo cáo tùy chỉnh |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Report builder: drag-drop chọn field, filter, group<br/>- Lưu template báo cáo<br/>- Lên lịch gửi báo cáo tự động (email) `[Could]` |

---

## C1.15. PHÂN HỆ NHÂN SỰ (HIS-HR)

### FR-HIS-HR-001: Quản lý hồ sơ nhân viên

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý thông tin nhân viên, bác sĩ, điều dưỡng |
| **Actor** | ACT-13 (NV nhân sự) |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Thông tin: họ tên, mã NV, chuyên khoa, chứng chỉ hành nghề, bằng cấp<br/>- Quản lý chứng chỉ: ngày cấp, ngày hết hạn, cảnh báo sắp hết hạn<br/>- Gắn NV với khoa/phòng/cơ sở |

### FR-HIS-HR-002: Lịch trực

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Lập và quản lý lịch trực BS, ĐD |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Lịch trực theo khoa, theo ca (sáng/chiều/đêm)<br/>- Cảnh báo trùng lịch, thiếu người<br/>- Liên kết với phân quyền (BS trực mới có quyền y lệnh ngoài giờ) |

### FR-HIS-HR-003: Chấm công

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Ghi nhận chấm công nhân viên |
| **Ưu tiên** | Could |
| **Quy tắc nghiệp vụ** | - Tích hợp máy chấm công (vân tay/thẻ) `[GIẢ ĐỊNH: có API]`<br/>- Tổng hợp giờ làm, tăng ca, trực |

---

## C1.16. PHÂN HỆ TÀI SẢN - THIẾT BỊ (HIS-AST)

### FR-HIS-AST-001: Quản lý tài sản

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý tài sản cố định bệnh viện |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Mã tài sản, tên, loại, vị trí (khoa/phòng), ngày mua, giá trị, khấu hao<br/>- Kiểm kê tài sản (barcode/QR scan)<br/>- Thanh lý, điều chuyển |

### FR-HIS-AST-002: Quản lý thiết bị y tế

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý thiết bị y tế, đặc biệt thiết bị CĐHA |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Thông tin thiết bị: mã, tên, hãng, model, serial, ngày mua, bảo hành<br/>- Lịch bảo trì định kỳ (cảnh báo sắp đến hạn)<br/>- Ghi nhận sự cố, downtime<br/>- Liên kết thiết bị CĐHA với AE Title DICOM |

---

## C1.17. PHÂN HỆ BHYT / BẢO HIỂM (HIS-INS)

### FR-HIS-INS-001: Giám định BHYT

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Kiểm tra giá trị thẻ BHYT qua cổng BHXH |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - API cổng giám định BHXH<br/>- Kiểm tra: hợp lệ, hết hạn, 5 năm liên tục, đúng/trái tuyến, thông tuyến<br/>- Lưu log mọi giao dịch kiểm tra |
| **Tiêu chí chấp nhận** | 1. Giám định online ≤ 5 giây<br/>2. Xử lý graceful khi cổng BHXH không phản hồi (timeout 10s → cho phép nhập tay) |

### FR-HIS-INS-002: Áp dụng chính sách BHYT

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Engine áp dụng quy tắc BHYT phức tạp |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Tỷ lệ đồng chi trả theo đối tượng<br/>- Trần thanh toán thuốc/VTYT<br/>- DM thuốc BHYT: trong/ngoài danh mục<br/>- DM dịch vụ kỹ thuật BHYT<br/>- Xử lý đặc biệt: cấp cứu (100% bất kể tuyến), chuyển tuyến, TNGT, bệnh nghề nghiệp<br/>- Configurable: cập nhật quy tắc khi thay đổi chính sách (không cần sửa code) |
| **Tiêu chí chấp nhận** | 1. Áp dụng chính sách chính xác 100%<br/>2. Cập nhật quy tắc mới ≤ 1 ngày (cấu hình, không code) |

### FR-HIS-INS-003: Xuất XML 4210 và gửi BHXH

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Tạo và gửi hồ sơ giám định BHYT lên cổng BHXH |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - Xuất XML theo format BHXH (XML 4210, cập nhật version mới nhất)<br/>- Gửi tự động/thủ công lên cổng BHXH<br/>- Nhận kết quả giám định (chấp nhận/từ chối/yêu cầu bổ sung)<br/>- Xử lý lỗi: retry, log lỗi, cảnh báo |
| **Tiêu chí chấp nhận** | 1. XML hợp lệ theo schema BHXH 100%<br/>2. Gửi batch 100 hồ sơ ≤ 60 giây |

### FR-HIS-INS-004: Đối soát BHYT

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Đối soát dữ liệu gửi vs dữ liệu duyệt từ BHXH |
| **Ưu tiên** | Must |
| **Quy tắc nghiệp vụ** | - So sánh: tổng chi phí gửi vs tổng chi phí duyệt<br/>- Phân tích lý do từ chối (theo mã lỗi BHXH)<br/>- Báo cáo chênh lệch theo khoa, theo loại dịch vụ<br/>- Hỗ trợ khiếu nại/điều chỉnh hồ sơ |

### FR-HIS-INS-005: Báo cáo BHYT

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Báo cáo BHYT theo mẫu BHXH |
| **Ưu tiên** | Must |
| **Danh sách** | - Mẫu 20: Quyết toán chi phí KCB BHYT<br/>- Mẫu 21: Tổng hợp chi phí KCB ngoại trú<br/>- Mẫu 79a (C79a-HD): Bảng kê chi phí KCB<br/>- Báo cáo theo yêu cầu BHXH huyện/tỉnh |

---

## C1.18. PHÂN HỆ MODULE KHÁC (HIS-OTH)

### FR-HIS-OTH-001: Dinh dưỡng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Quản lý chế độ ăn bệnh nhân nội trú |
| **Ưu tiên** | Could |
| **Quy tắc nghiệp vụ** | - Chế độ ăn theo bệnh lý (tiểu đường, tim mạch, thận)<br/>- Đặt suất ăn từ y lệnh<br/>- Tổng hợp suất ăn cho bộ phận dinh dưỡng |

### FR-HIS-OTH-002: Kiểm soát nhiễm khuẩn (KSNK)

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Giám sát và quản lý nhiễm khuẩn bệnh viện |
| **Ưu tiên** | Could |
| **Quy tắc nghiệp vụ** | - Đăng ký ca nhiễm khuẩn<br/>- Theo dõi vi khuẩn, kháng sinh đồ<br/>- Báo cáo nhiễm khuẩn theo khoa |

### FR-HIS-OTH-003: Quản lý chất lượng

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Hỗ trợ quản lý chất lượng bệnh viện |
| **Ưu tiên** | Could |
| **Quy tắc nghiệp vụ** | - KPI theo khoa (tỷ lệ tái nhập viện, TAT, v.v.)<br/>- Quản lý sự cố y khoa (incident reporting)<br/>- Khảo sát hài lòng bệnh nhân |

---

## C1.19. PHÂN HỆ TOOLCODE (HIS-TLC)

### FR-HIS-TLC-001: Form Builder

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Công cụ tạo form động không cần lập trình |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Drag-drop: text, number, date, dropdown, checkbox, radio, table<br/>- Gắn form vào module (EMR, phiếu chuyên khoa)<br/>- Validation rules configurable<br/>- Versioning form |

### FR-HIS-TLC-002: Report Builder

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Công cụ tạo báo cáo tùy chỉnh |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Chọn nguồn dữ liệu, field, filter, group, aggregate<br/>- Xuất Excel, PDF, biểu đồ<br/>- Lưu template, lên lịch |

### FR-HIS-TLC-003: Workflow Engine

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Engine xử lý workflow duyệt, phê duyệt |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Cấu hình luồng duyệt: đơn thuốc, y lệnh, báo cáo CĐHA<br/>- Multi-step approval (1 cấp, 2 cấp)<br/>- Notification khi pending approval |

---

## C1.20. PHÂN HỆ TRỢ GIÚP (HIS-HLP)

### FR-HIS-HLP-001: Hướng dẫn sử dụng trực tuyến

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | Help system tích hợp trong ứng dụng |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Context-sensitive help (F1 hoặc ? icon → mở help đúng trang hiện tại)<br/>- Searchable knowledge base<br/>- Video tutorial cho chức năng phức tạp `[Could]` |

### FR-HIS-HLP-002: FAQ và Hotline

| Hạng mục | Chi tiết |
|----------|---------|
| **Mô tả** | FAQ thường gặp và kênh hỗ trợ |
| **Ưu tiên** | Should |
| **Quy tắc nghiệp vụ** | - Danh sách câu hỏi thường gặp<br/>- Chat hỗ trợ trực tuyến `[Could]`<br/>- Hotline / email hỗ trợ kỹ thuật |

---

*Tiếp theo: [Phần C2 — Yêu cầu Chức năng RIS/PACS](./C2-YEU-CAU-CHUC-NANG-RIS-PACS.md)*
