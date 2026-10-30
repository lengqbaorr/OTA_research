# 08. Rà soát toàn bộ & đề xuất tính năng mở rộng

> Rà lại [02 – Đề bài](02-de-bai-vinpearl-cho-nguoi-tre.md) và các tài liệu 03–07 để tìm: (1) **khoảng trống**: yêu cầu hoặc persona trong đề bài chưa được phục vụ tốt; (2) **tính năng mở rộng** có giá trị.
> Mọi đề xuất đều tuân thủ mục 8 (ngoài phạm vi): không iOS, không tính năng nhóm, không thanh toán thật, không dịch vụ ngoài Vinpearl, không CS người thật.

---

## 1. Khoảng trống phát hiện khi rà lại

| # | Khoảng trống | Ở đâu trong đề bài | Hiện trạng ý tưởng |
|---|---|---|---|
| K1 | **Bạn 17 tuổi** chỉ bị chặn, chưa có lối đi tiếp | Persona, A8, mục 5 | Mới có thông báo "cần đủ 18 tuổi" |
| K2 | **Chứng minh các con số ở mục 9** mới nằm trên giấy; người hướng dẫn tự cầm máy thử nên cần **bằng chứng ngay trong sản phẩm** | Mục 9, mục 12 | Có kế hoạch đo (tài liệu 03), chưa có công cụ hiển thị |
| K3 | **Hà** muốn đọc kỹ nhưng chưa có công cụ lọc bài theo độ tin cậy | Persona Hà | Có dấu "Đã đi thật", nhãn Hợp tác, B1 |
| K4 | **Tab Khám phá** chưa có nội dung | A3, A6 | Bản mô phỏng còn trống |
| K5 | **Người đăng bị ẩn bài** chỉ được báo, chưa có cách phản hồi | A14 | Có thông báo kèm lý do |
| K6 | **Linh** phải tự viết caption, tự chia chương; tốn công trên điện thoại | Persona Linh, A2 | Có gợi ý chương bằng AI |
| K7 | **Sau khi đặt** chỉ có gợi ý phần còn thiếu; **trong lúc đi** app chưa giúp gì | A5, A9 | Chưa có |
| K8 | **Mạng yếu** mới được xử lý ở mức kỹ thuật, người dùng chưa chủ động chọn được | Mục 7 | Có tối ưu tự động |
| K9 | **Kiểm duyệt viên** xử lý "hàng trăm mục mỗi ngày" nhưng chưa có công cụ tăng tốc | Persona kiểm duyệt, A14 | Có hàng chờ ưu tiên và gợi ý AI |

---

## 2. Danh sách tính năng mở rộng

**Thang chấm**: Giá trị (★ đến ★★★) · Công sức (Thấp / TB / Cao) · Khớp đề bài (✅ trong phạm vi · ⚠️ cần hỏi người hướng dẫn).

### 2.1. Cho người xem

| # | Tính năng | Mô tả | Persona | Giá trị | Công sức | Khớp |
|---|---|---|---|---|---|---|
| X1 | **"Nhờ người lớn đặt"** | Người dưới 18 tuổi bấm "Đi y chang" → tạo **link lộ trình đã điền sẵn** (ngày, số người, các mục) gửi cho bố mẹ hoặc anh chị. Người lớn mở link, đăng nhập tài khoản của mình và tự thanh toán. Người dưới 18 không bao giờ tự thanh toán | Bạn 17 tuổi | ★★★ | Thấp (dùng lại link chia sẻ A10 + màn xác nhận A11) | ⚠️ Cần xác nhận đây không phải "đặt chung" (mục 8) mà là chia sẻ link |
| X2 | **Bộ lọc tin cậy** | Lọc bảng tin và tìm kiếm: "Chỉ bài không tài trợ", "Có lộ trình", "Đi trong 3 tháng gần đây", "Cùng mùa với ngày tôi định đi" | Hà | ★★★ | Thấp | ✅ |
| X3 | **Hỏi người đã đi** | Câu hỏi dưới bài được gửi tới người **đã đi chỗ đó** (không chỉ tác giả); câu trả lời có huy hiệu "Đã đi thật" | Hà, người mới | ★★ | TB | ✅ (mở rộng A10) |
| X4 | **"Hỏi về bài này"** | Nút mở trợ lý **chỉ trong ngữ cảnh một bài**: *"phòng này có bồn tắm không?"*, *"đi 4 người thì sao?"*. Trợ lý chỉ trả lời dựa trên bài, các bài cùng chỗ và dữ liệu sản phẩm | Hà, Khang | ★★ | Thấp (dùng lại A12) | ✅ |
| X5 | **Khám phá trên bản đồ** | Tab Khám phá dạng bản đồ: ghim theo điểm đến, kèm video và độ "nóng" xu hướng; lọc theo ngân sách | Người mới, Vy | ★★ | TB | ✅ |
| X6 | **So sánh hai lộ trình** | Chọn 2 bài → so sánh song song: số ngày, chỗ ở, tổng giá hiện tại cho cùng ngày và số người | Vy, Khang | ★★ | TB | ✅ |
| X7 | **"Đi ngay cuối tuần"** | Lối tắt hiện những lộ trình **còn chỗ** cho cuối tuần này hoặc tuần sau trong ngân sách đã nói | Khang | ★★ | Thấp | ✅ |
| X8 | **Lịch giá cả lộ trình** | Lịch 30 ngày, mỗi ngày tô màu theo **tổng giá thật** của cả lộ trình | Vy | ★★ | TB | ✅ (B3 dùng lại) |
| X9 | **Cảnh báo lệch mùa** | Video quay "Hè 2026" mà người xem chọn tháng 11 → *"Bài quay mùa hè, cảnh có thể khác thời điểm bạn chọn"* kèm bài cùng mùa nếu có | Hà | ★★ | Thấp | ✅ (không cần dữ liệu thời tiết, chỉ so mùa quay với ngày chọn) |
| X10 | **Chế độ tiết kiệm dữ liệu** | Công tắc: không tự phát video, chỉ tải ảnh bìa; hiện dung lượng đã dùng | Mọi người dùng mạng yếu | ★★ | Thấp | ✅ (mục 7) |
| X11 | **Chế độ "Đang đi"** | Trong ngày đi, màn hình chính đổi thành lịch trình hôm nay: giờ nhận phòng, vé QR, điểm tiếp theo, mẹo của người đăng. Dùng được khi mất mạng | Khang | ★★★ | TB (dùng lại B4) | ✅ (mở rộng A5) |
| X12 | **Lưu lộ trình thành bản nháp** | Lưu cấu hình đã chọn (ngày, người, mục) để đặt sau; đồng bộ app và web | Khang, Vy | ★★ | Thấp | ✅ |

### 2.2. Cho người đăng

| # | Tính năng | Mô tả | Persona | Giá trị | Công sức | Khớp |
|---|---|---|---|---|---|---|
| D1 | **AI soạn nháp caption từ lộ trình** | Dựa trên các mục đã đặt và vibe đã chọn, AI đề xuất caption nháp; người đăng **phải sửa và xác nhận**. Không tự thêm nội dung không có trong chuyến | Linh | ★★ | Thấp | ✅ (cần ghi rõ "nháp do AI gợi ý"; không bịa) |
| D2 | **Phụ đề tự động** | Chuyển giọng nói trong video thành phụ đề tiếng Việt; người đăng sửa được. Giúp xem khi tắt tiếng và giúp tìm kiếm | Linh, người xem | ★★ | TB (đã có bước chép lời cho tìm kiếm) | ✅ |
| D3 | **Tự chia chương** | AI đề xuất toàn bộ chương video theo các mục trong chuyến; người đăng chỉ kéo chỉnh | Linh | ★★ | TB | ✅ |
| D4 | **Thống kê cho người đăng** | Lượt xem, lưu, câu hỏi, **số người đã đặt theo lộ trình**; không hiện thông tin cá nhân của người đặt | Linh | ★★ | Thấp | ✅ |
| D5 | **Kháng nghị khi bị ẩn bài** | Người đăng gửi giải thích; vào hàng chờ riêng, người kiểm duyệt **khác** xem lại; có audit log | Linh | ★★ | Thấp | ✅ (mở rộng A14) |
| D6 | **Remix lộ trình** | Người đã đi theo đăng bài "phiên bản của tôi", liên kết bài gốc; bài gốc hiện "3 phiên bản đi theo" | Khang → Linh | ★★ | TB | ✅ |
| D7 | **Mẫu bài theo loại chuyến** | Gợi ý cấu trúc: "3N2Đ nghỉ dưỡng", "1 ngày VinWonders", "Cuối tuần sống ảo" | Linh | ★ | Thấp | ✅ |

### 2.3. Cho trang quản trị

| # | Tính năng | Mô tả | Giá trị | Công sức | Khớp |
|---|---|---|---|---|---|
| Q1 | **Bảng số liệu chất lượng (mục 9) ngay trong admin** | Hiển thị số đo thật từ thiết bị: thời gian thấy bài đầu tiên (p50/p95), độ trễ đồng bộ app ↔ web, **số lần trừ tiền hai lần (phải = 0)**, **số lần bán vượt chỗ (phải = 0)**, tỷ lệ gợi ý trợ lý bị bộ kiểm chứng loại, chi phí trợ lý theo ngày | ★★★ | TB | ✅ Biến mục 9 thành bằng chứng người hướng dẫn tự xem được |
| Q2 | **Bàn làm việc kiểm duyệt nhanh** | Phím tắt; gom báo cáo trùng về cùng một bài; xử lý hàng loạt bình luận spam giống nhau; đồng hồ SLA cho mục nghiêm trọng; AI tóm tắt lý do | ★★★ | TB | ✅ (persona kiểm duyệt) |
| Q3 | **Sandbox đẩy ảo** | Nút tạo kịch bản giả lập (50 tài khoản ảo tương tác một bài) để **demo trực tiếp** rằng hệ thống phát hiện được | ★★ | Thấp | ✅ (A15; dữ liệu gắn nhãn mô phỏng) |
| Q4 | **Đo độ chính xác kiểm duyệt AI** | Tỷ lệ gợi ý AI được kiểm duyệt viên chấp nhận/bác bỏ theo từng nhóm vi phạm | ★★ | Thấp | ✅ |
| Q5 | **Bảng đối soát đơn** | Đối chiếu đơn ↔ giao dịch giả lập ↔ tồn kho; nút "hoàn tiền giả lập" cho đơn bất thường | ★★ | TB | ✅ (A16) |
| Q6 | **Bản đồ nhiệt xu hướng** | Điểm đến nào đang lên, lên vì tín hiệu gì, so với tuần trước | ★★ | TB | ✅ (A15) |
| Q7 | **Trình tạo dữ liệu mô phỏng** | Tạo người dùng, đơn đã hoàn thành, bài đăng mẫu **có nhãn "Mô phỏng"** phục vụ demo và đo | ★★★ | TB | ✅ (bắt buộc vì dữ liệu không có bài đăng) |

### 2.4. Tầm nhìn sau dự án (ngoài phạm vi hiện tại)

| # | Tính năng | Vì sao để sau |
|---|---|---|
| V1 | Lên kế hoạch và đặt chung theo nhóm, chia tiền | Mục 8: ngoài phạm vi |
| V2 | Mở nhà cung cấp ngoài Vinpearl (homestay, quán, tour địa phương) | Mục 8 |
| V3 | Thưởng tiền mặt cho creator, chương trình đại sứ | Cần pháp lý, thuế |
| V4 | iOS, ngôn ngữ khác | Mục 8 |
| V5 | Giữ giá có phí (price lock), trả sau | Cần thanh toán thật |
| V6 | Thông tin thời tiết thật theo ngày đi | Cần nguồn dữ liệu có quyền sử dụng |
| V7 | Magic Camera (nhận diện, dịch tại điểm đến) | Không phục vụ trực tiếp yêu cầu đề bài |
| V8 | Làm đối tác nguồn hàng cho TikTok GO, API cho bên thứ ba | Chiến lược kinh doanh, không phải dự án 4 tuần |

---

## 3. Đề xuất ưu tiên cho phần thi

Tiêu chí:
- lấp được khoảng trống trực tiếp với persona hoặc nghiệm thu;
- dùng lại hạ tầng phần A;
- giúp **người hướng dẫn tự thử và thấy được giá trị**.

| Thứ tự | Tính năng | Lý do |
|---|---|---|
| 1 | **Q1 – Bảng số liệu chất lượng** | Mục 9 yêu cầu "chứng minh" và mục 12 đánh giá cách đo. Một màn hình có "0 lần trừ tiền hai lần" và "đồng bộ p95 = 1,8 giây" thuyết phục hơn mọi slide |
| 2 | **Q7 – Trình tạo dữ liệu mô phỏng** | Không có nó thì bảng tin, xu hướng, tìm kiếm trống. Đây là điều kiện để mọi thứ khác demo được |
| 3 | **X1 – Nhờ người lớn đặt** | Biến persona "17 tuổi" từ một lệnh chặn thành trải nghiệm tốt; công sức thấp; cần hỏi người hướng dẫn trước |
| 4 | **X2 + X9 – Bộ lọc tin cậy & cảnh báo lệch mùa** | Phục vụ trực tiếp Hà; công sức thấp; thể hiện triết lý "không thao túng" |
| 5 | **X11 – Chế độ "Đang đi"** | Hoàn thiện vòng đời (trước, trong, sau chuyến); dùng lại B4 offline |
| 6 | **Q2 – Bàn làm việc kiểm duyệt nhanh** | Phục vụ persona kiểm duyệt "hàng trăm mục mỗi ngày" |
| 7 | **D1, D3 – AI soạn nháp caption & tự chia chương** | Giảm công đăng bài cho Linh, tăng lượng nội dung |

Đề xuất kết hợp với các phần B đã chọn (B7, B1, B4):
- **Q1 + B7** gộp thành một khu vực "Chất lượng & Trợ lý" trong admin.
- **X11 + B4** dùng chung cơ chế lưu offline.
- **X4 + B1** dùng chung trợ lý có kiểm chứng trích dẫn.

---

## 4. Câu hỏi bổ sung cho người hướng dẫn

1. *"Với bạn 17 tuổi, em đề xuất nút 'Nhờ người lớn đặt' tạo link lộ trình đã điền sẵn để người lớn mở và **tự đặt bằng tài khoản của họ**. Như vậy có bị coi là tính năng 'đặt chung' nằm ngoài phạm vi không ạ?"*
2. *"Em muốn có một màn hình số liệu chất lượng trong trang quản trị (thời gian tải, độ trễ đồng bộ, số lần trừ tiền hai lần…) để anh/chị tự kiểm tra trong buổi demo. Phần này có được tính là đóng góp cho mục 9 không ạ?"*
3. *"AI soạn nháp caption cho người đăng có vi phạm 'không bài đăng giả' không, nếu người đăng bắt buộc phải sửa và xác nhận, và bài ghi rõ là đã dùng gợi ý AI?"*
