# 11. Ý tưởng "Social-to-Booking": từ nội dung mạng xã hội đến chuyến đi đã đặt

> ⚠️ **Lưu ý (cập nhật sau khi nhận đề bài chính thức)**: tài liệu này được viết *trước* khi có [đề bài "Vinpearl cho người trẻ"](../de-bai/01-tai-lieu-dac-ta-san-pham.md). Phần lấy nội dung từ TikTok/Instagram/Threads bên ngoài, affiliate OTA khác, homestay ký trực tiếp, concierge người thật và thứ tự kênh **nằm ngoài phạm vi đề bài**. Ý tưởng "nội dung → một chạm đặt chỗ", "trí tuệ cộng đồng" và "gửi ảnh chụp màn hình" vẫn được dùng, dưới dạng A11, B1, B2. Xem đối chiếu chi tiết tại [tài liệu 13, mục 5](13-phan-tich-de-bai-vinpearl.md).

> Góc nhìn: Solution Architect / SWE ở mức khái niệm. Mục tiêu là làm rõ ý tưởng, cân nhắc các phương án và chọn hướng hợp lý nhất. Tài liệu chưa đi vào chi tiết công nghệ cụ thể.
>
> Phạm vi đầu vào **không chỉ là video TikTok**. Hệ thống nhận *mọi nội dung du lịch trên mạng xã hội*:
> - **Nguồn**: TikTok, Instagram (post, reel, carousel), Threads, Facebook (bài viết, group review du lịch), YouTube Shorts, blog, review trên Google Maps…
> - **Định dạng**: video, ảnh/album, chữ (caption, bài viết dài), **bình luận của cộng đồng**, và ảnh chụp màn hình bất kỳ.

---

## 1. Phát biểu lại ý tưởng

**Insight**: Hành trình du lịch của người trẻ ngày nay *bắt đầu từ nội dung mạng xã hội* (video ngắn, ảnh Instagram, bài review trên Threads hoặc trong group Facebook) chứ không còn bắt đầu từ ô tìm kiếm của OTA. Giữa lúc "**thấy nội dung → muốn đi**" và lúc "**đã đặt xong**" có một khoảng trống rất lớn. Người dùng phải:

1. Đoán xem video/ảnh chụp ở đâu (đọc comment, hỏi "homestay tên gì vậy ạ?").
2. Tự tìm homestay hoặc điểm đó trên Google, Facebook, OTA.
3. Tự tìm phương tiện (vé máy bay, xe khách, tàu) từ nơi mình ở.
4. Tự ghép lịch trình và so sánh giá.
5. Đặt rải rác trên 3–5 nền tảng khác nhau.

→ Phần lớn "cảm hứng" bị rơi rụng ở bước 1–2. Ở giai đoạn Dreaming (tài liệu 06), các OTA hiện nay gần như không có mặt.

**Insight thứ hai: phần bình luận thường giá trị hơn chính bài đăng.** Bài đăng có thể là seeding, nhưng bên dưới cộng đồng thường bổ sung những thông tin thật hơn:
- *"Chỗ này giờ lên 1tr2 rồi, qua homestay Y bên cạnh view y chang mà có 700k"* → **gợi ý thay thế rẻ hơn**.
- *"Đi limousine Thành Bưởi đón tận nhà, đỡ hơn bay vì sân bay Liên Khương xa trung tâm"* → **mẹo phương tiện**.
- *"Đẹp nhất tháng 11–12 mùa dã quỳ, đi mùa mưa thì đường đèo sạt lở"* → **thời điểm và cảnh báo**.
- *"Ảnh filter quá, ngoài đời phòng nhỏ lắm"* → **kiểm chứng thực tế**.

Hiện chưa có công cụ nào **gom và cấu trúc hóa "trí tuệ cộng đồng"** này thành quyết định đặt chỗ.

**Định vị đề xuất**: *"Thấy là đi được, và đi khôn hơn nhờ cộng đồng"*. Đây là lớp chuyển đổi từ nội dung thành chuyến đi (**Inspiration-to-Booking layer**). Nó đứng giữa các nền tảng mạng xã hội và nguồn cung du lịch, và làm hai việc: (1) hiểu *nội dung gốc* để biết người dùng muốn đi đâu, ở đâu; (2) chắt lọc *ý kiến cộng đồng* để đề xuất phương án tốt hơn, rẻ hơn, tiện hơn.

### Ba kịch bản lõi

| Kịch bản | Nội dung cho thấy | Người dùng muốn | Hệ thống phải làm |
|---|---|---|---|
| **K1 – Đặt đúng chỗ trong bài** | Một homestay hoặc khách sạn cụ thể (VD: căn phòng view đồi thông ở Đà Lạt trong video TikTok, album Instagram hay bài review trên Threads) | Đặt *đúng* căn đó | Nhận diện chính xác cơ sở lưu trú → hiển thị phòng, giá, lịch trống → đặt |
| **K2 – Đi đến điểm trong bài** | Một điểm đến hoặc trải nghiệm (thác, quán cà phê, săn mây) | Có một chuyến đi trọn vẹn | Nhận diện điểm → biết người dùng ở đâu → gợi ý phương tiện (bay/xe/tàu) + chỗ ở theo tiêu chí (gần bến xe, gần điểm đó, đang trend, xuất hiện trong bài khác) → đặt cả gói |
| **K3 – Đi khôn hơn nhờ cộng đồng** | Bài đăng kèm hàng trăm bình luận | Biết có chỗ nào đẹp hơn, rẻ hơn, cách đi nào tiện hơn, có cảnh báo gì không | Đọc và tổng hợp bình luận → trích các *gợi ý thay thế, mẹo, cảnh báo* → kiểm chứng bằng dữ liệu thật (giá, lịch trống, khoảng cách) → đưa vào kế hoạch như các lựa chọn so sánh |

K3 không đứng riêng. Nó là **lớp làm giàu** cho K1 và K2. Ví dụ: "Bạn đang xem homestay A (1,2tr/đêm). 14 người trong bình luận gợi ý homestay B cách 300m, view tương tự, hiện có phòng giá 750k" → [Đặt A] [Đặt B] [So sánh].

---

## 2. Ràng buộc thực tế quyết định kiến trúc

Đây là phần quan trọng nhất. Nhiều ý tưởng sai hướng vì bỏ qua những ràng buộc này.

### 2.1. Không ai được "gắn nút" vào app mạng xã hội của người khác
- App TikTok, Instagram, Threads, Facebook, YouTube trên điện thoại đều là **hệ đóng**. Bên thứ ba không thể chèn nút vào giao diện của họ. Một số cách lách tồn tại (overlay trên Android, đọc màn hình) nhưng vi phạm chính sách, dễ bị gỡ và tạo trải nghiệm xấu.
- Trên 90% lượt xem TikTok diễn ra **trên app di động**. Vì vậy một **browser extension** chỉ chạm tới một phần rất nhỏ hành vi thực tế.
- Như vậy, "1 nút bấm" trong thực tế chỉ có thể đến từ các **điểm chạm hợp lệ** sau:

| Điểm chạm | Ai đặt nút | Mức độ "1-click" | Ghi chú |
|---|---|---|---|
| **Nút Share (Chia sẻ) → app/web của ta** | Người xem | 2 chạm (Share → chọn app) | Hợp lệ, phổ biến, hoạt động với *mọi* video, kể cả video không hợp tác |
| **Copy link → dán vào app/web/chatbot** | Người xem | 3–4 chạm | Phương án dự phòng, đơn giản nhất để làm MVP |
| **Chụp màn hình → gửi vào app/web/chatbot** | Người xem | 2–3 chạm | **Cửa vào phổ quát nhất**: dùng được với *mọi* nền tảng, kể cả bài trong group Facebook kín, story, tin nhắn bạn bè gửi, và **màn hình bình luận**. Không phụ thuộc API của nền tảng nào |
| **Link in bio / link trong comment ghim / QR trong video** | Nhà sáng tạo (creator) | 1–2 chạm | Cần creator hợp tác. Ta cho creator công cụ tạo "trang chuyến đi" kèm hoa hồng |
| **Tính năng thương mại chính thức của nền tảng** (anchor link, affiliate du lịch, location page có đặt phòng, quảng cáo có nút CTA) | Nền tảng / nhà quảng cáo | 1 chạm | Đúng nghĩa "1 click" nhất. TikTok đã thử nghiệm tính năng đặt phòng/affiliate du lịch ở một số thị trường (*cần kiểm chứng phạm vi tại VN thời điểm triển khai*). Phụ thuộc chính sách nền tảng |
| **Browser extension** | Người xem (trên máy tính) | 1 chạm | Chỉ có tác dụng trên web desktop. Hợp làm công cụ cho creator/đại lý hơn là cho người xem |

**Kết luận 1**: Không nên đặt cược vào một "kênh" duy nhất. Lõi giá trị phải là một **dịch vụ trung tâm** nhận đầu vào là *bất kỳ link/video nào* và trả về *chuyến đi đặt được*. Các kênh (share, web, chatbot, creator link, extension) chỉ là những "cửa vào" mỏng cắm vào lõi đó.

### 2.2. Nội dung và bình luận không "của mình": quyền truy cập dữ liệu là ràng buộc lớn nhất
- Lấy nội dung bằng cách cào (scraping) có thể vi phạm điều khoản nền tảng. Nên ưu tiên **API/oEmbed chính thức** cùng dữ liệu người dùng hoặc creator **chủ động cung cấp**.
- Không tải lại hay lưu trữ video/ảnh của người khác. Chỉ **nhúng (embed)** nội dung gốc và lưu *metadata + kết quả phân tích*.
- **Bình luận là phần khó lấy nhất qua kênh chính thức.** Nhìn chung các API công khai của nền tảng chỉ cho đọc bình luận trên *bài của chính tài khoản đã cấp quyền* (ví dụ tài khoản business/creator kết nối với ứng dụng), hoặc dành cho mục đích nghiên cứu có kiểm duyệt. *(Chính sách API thay đổi thường xuyên, cần kiểm tra lại từng nền tảng khi triển khai.)*

Bảng dưới là **mức tiếp cận dự kiến** theo nguồn dữ liệu (để định hướng, cần kiểm chứng):

| Nguồn dữ liệu | Nội dung gốc (caption, ảnh, video) | Bình luận | Cách tiếp cận hợp lệ |
|---|---|---|---|
| Bài của **creator đã kết nối tài khoản** (OAuth) với nền tảng ta | Có (qua API của nền tảng) | **Có**, với bài của chính họ | Creator đổi lại được công cụ phân tích và hoa hồng. Đây là nguồn dữ liệu "sạch" và đầy đủ nhất |
| Bài công khai của người khác, người dùng **share/dán link** | Một phần (oEmbed: caption, tác giả, thumbnail, mã nhúng) | Hạn chế hoặc không có | Bổ sung bằng ảnh chụp màn hình do người dùng gửi |
| **Ảnh chụp màn hình** người dùng gửi (bài, album, bình luận) | Có (đọc chữ, hiểu ảnh) | **Có**, phần người dùng chụp | Người dùng là người cung cấp dữ liệu. Phổ quát nhất, không phụ thuộc nền tảng |
| **Cộng đồng trên chính nền tảng ta** (review, hỏi đáp, gợi ý sau chuyến đi) | Có | Có | Dữ liệu của ta, đầy đủ quyền. Về lâu dài đây là nguồn bình luận giá trị nhất |
| Group Facebook, bài riêng tư | Không | Không | Chỉ qua ảnh chụp màn hình người dùng gửi |
| Web công khai (blog, diễn đàn, review bản đồ) | Tùy điều khoản từng site / API chính thức | Tùy | Ưu tiên API chính thức, tôn trọng robots/ToS |

**Kết luận 2**: Đừng thiết kế hệ thống giả định "đọc được mọi bình luận của mọi bài". Thiết kế theo **3 tầng dữ liệu bình luận**:
1. **Tầng người dùng cung cấp** (ảnh chụp màn hình, dán đoạn văn bản) → có ngay từ MVP.
2. **Tầng creator cấp quyền** → có khi xây chương trình creator.
3. **Tầng cộng đồng riêng của ta** → tích lũy dần, cuối cùng trở thành tài sản chính.

### 2.3. Nhiều nơi trong video không nằm trên OTA nào
- Rất nhiều homestay "viral" ở Đà Lạt, Sa Pa, Hà Giang chỉ nhận đặt qua **Facebook/Zalo/điện thoại**, không có trên Agoda hay Booking.
- → Đây vừa là **khó khăn** (không có API để đặt) vừa là **cơ hội độc quyền** (nguồn hàng OTA lớn không có).

### 2.4. Seeding và niềm tin
- Một phần lớn video là quảng cáo trá hình (seeding). Ảnh có thể đã qua chỉnh sửa, "phòng đẹp trên video, xấu ngoài đời".
- Luật Quảng cáo và Luật Bảo vệ người tiêu dùng yêu cầu ghi nhãn nội dung quảng cáo (tài liệu 08).
- → Hệ thống phải phân biệt rõ **"Hợp tác/Quảng cáo"** và **"Trải nghiệm thật"**, đồng thời có review đã xác minh từ người thực sự đặt qua nền tảng. Niềm tin là lợi thế cạnh tranh.

---

## 3. Các phương án sản phẩm (cân đo, không tuyến tính)

### 3.1. So sánh các hình thái sản phẩm

Thang điểm: ★ thấp → ★★★ cao. Riêng hai cột *Chi phí xây* và *Rủi ro chính sách* thì ★ là tốt.

| Hình thái | Độ phủ người dùng | Ma sát (friction) | Chi phí xây | Rủi ro chính sách | Giữ chân | Nhận xét |
|---|---|---|---|---|---|---|
| **A. Browser extension** | ★ | ★★★ (thấp, nếu đúng desktop) | ★ | ★★ | ★ | Sai thiết bị với hành vi chính (mobile). Chỉ nên làm sau, như công cụ cho creator/đại lý |
| **B. Native app (iOS/Android) + Share Extension** | ★★ | ★★ (phải cài app trước) | ★★★ | ★ | ★★★ | Trải nghiệm share tốt nhất, push notification, lưu chuyến đi. Rào cản: phải thuyết phục người dùng cài app |
| **C. Web/PWA (mở từ link, không cần cài)** | ★★★ | ★★ | ★★ | ★ | ★ | Ai cũng mở được ngay từ link, hợp làm "trang đích" từ creator link và quảng cáo. Android PWA còn nhận được Share. Yếu ở giữ chân |
| **D. Chatbot (Zalo OA / Messenger)** | ★★★ (VN) | ★★ | ★ | ★ | ★★ | Người Việt quen hỏi đặt phòng qua Zalo. Hợp làm MVP "concierge" có người thật hỗ trợ phía sau |
| **E. Zalo Mini App / Mini app trong ví** | ★★★ (VN) | ★★ | ★★ | ★★ (phụ thuộc nền tảng) | ★★ | Không cần cài, có sẵn định danh và thanh toán. Nên cân nhắc ở pha 2 |
| **F. Công cụ cho Creator (trang chuyến đi + affiliate)** | ★★★ (nhờ follower của creator) | ★★★ (1 click từ bio) | ★★ | ★ | ★★ | Biến creator thành kênh phân phối. Đây là "động cơ tăng trưởng" |
| **G. Tích hợp chính thức với nền tảng video** | ★★★ | ★★★ | ★★ | ★★★ (phụ thuộc) | — | Mục tiêu dài hạn (đối tác chính thức). Không nên là điều kiện tiên quyết |
| **H. API/Widget B2B** (cho OTA, ví, ngân hàng muốn tính năng "video → đặt") | ★★ | — | ★★ | ★ | — | Hướng mở rộng sau khi lõi đã chứng minh hiệu quả |

### 3.2. Phương án đề xuất: **"Lõi trung tâm + nhiều cửa vào mỏng", mobile-first, web trước app**

```
                     ┌───────────── CỬA VÀO (Channels) ─────────────┐
 Người xem TikTok ──►│ Share / dán link → Web/PWA   (MVP)           │
 Người xem TikTok ──►│ Zalo OA chatbot              (MVP concierge) │
 Follower creator ──►│ Link in bio / QR → Trip Page (MVP)           │
 Người dùng trung thành ►│ Native app + Share Extension (Pha 2)     │
 Creator/đại lý (PC) ──►│ Browser extension            (Pha 3)      │
 Đối tác ───────────►│ API / Widget, tích hợp nền tảng (Pha 3+)     │
                     └──────────────────────┬───────────────────────┘
                                            ▼
                ┌────────────── LÕI "VIDEO → CHUYẾN ĐI" ──────────────┐
                │ 1. Hiểu video  2. Định danh địa điểm/cơ sở           │
                │ 3. Lên kế hoạch chuyến đi  4. Tìm hàng & giá         │
                │ 5. Đặt chỗ/Thanh toán  6. Ghi nhận nguồn & hoa hồng  │
                └──────────────────────┬──────────────────────────────┘
                                       ▼
          NGUỒN CUNG: OTA đối tác (affiliate/API) • vé xe, tàu, máy bay • homestay ký trực tiếp
```

**Lý do chọn hướng này**:
1. **Không phụ thuộc kênh**: TikTok đổi chính sách hay một nền tảng mới nổi lên thì chỉ cần thêm một "cửa vào".
2. **Web/PWA trước, app sau**: không bắt người dùng cài app ngay ở lần đầu, khi họ chưa tin. App chỉ dành cho người đã quay lại.
3. **Creator là kênh phân phối rẻ nhất**: thay vì đốt tiền quảng cáo, ta chia hoa hồng cho người đã có sẵn khán giả. Đây cũng là cách hợp thức hóa seeding: seeding minh bạch và có ghi nhận nguồn.
4. **Chatbot Zalo + người thật** cho phép chạy thử *trước khi viết dòng code AI nào*, để kiểm chứng nhu cầu thật.

---

## 4. Lõi hệ thống: 7 năng lực (Capabilities)

### 4.1. Hiểu nội dung đa nguồn, đa định dạng (Content Understanding)
- **Đầu vào**: link (TikTok, Instagram, Threads, Facebook, YouTube, blog…), ảnh/album, ảnh chụp màn hình, hoặc văn bản dán vào. Mỗi nguồn có một **bộ chuyển đổi đầu vào** riêng, nhưng tất cả được chuẩn hóa về **một mô hình chung "Bài nội dung" (Content Item)** gồm: nguồn, tác giả, thời gian đăng, văn bản (caption/bài viết), danh sách media (ảnh, video), tag vị trí, hashtag, và danh sách bình luận (nếu có).
- **Xử lý theo định dạng** bằng AI đa phương thức (multimodal):

| Định dạng | Cách hiểu | Ví dụ tín hiệu |
|---|---|---|
| Video | Chuyển giọng nói thành chữ, chọn khung hình chính, đọc chữ trên hình, nhận diện cảnh | "homestay này ở Trại Mát...", biển hiệu quán |
| Ảnh / album carousel | Đọc chữ trên ảnh, nhận diện cảnh và phong cách, so khớp ảnh với ảnh của cơ sở đã biết | Ảnh phòng giống ảnh của homestay X trên OTA |
| Văn bản (caption, bài Threads, bài review dài trong group) | Trích thực thể: địa danh, tên cơ sở, giá, lịch trình, phương tiện, thời điểm | "3N2Đ Đà Lạt 2tr5: xe Phương Trang, ở Y, ăn Z" → có thể dựng nguyên lịch trình |
| Ảnh chụp màn hình | Đọc chữ, *tự phân biệt đâu là bài gốc, đâu là bình luận*, nhận diện app nguồn | Chụp màn hình phần bình luận Threads |
| Bình luận | Xem mục 4.7 | Gợi ý thay thế, mẹo, cảnh báo |

- Kết hợp tín hiệu từ nhiều định dạng: một bài Instagram có thể dùng **caption + ảnh + tag vị trí** để chốt cùng một cơ sở, tin cậy hơn hẳn dùng từng tín hiệu riêng lẻ.
- **Đầu ra**: "Ý định có cấu trúc" (structured intent). Ví dụ: `{loại: homestay, ứng viên: [A (0.82), B (0.40)], khu vực: Đà Lạt – Trại Mát, phong cách: view đồi thông, mức giá gợi ý: ~800k/đêm}`.
- **Nguyên tắc**: luôn kèm **độ tin cậy**. Khi độ tin cậy thấp, để người dùng chọn hoặc xác nhận ("Có phải chỗ này không?") thay vì đoán bừa.

### 4.2. Định danh địa điểm & cơ sở (Entity Resolution / Place Graph) ← **lợi thế cạnh tranh cốt lõi**
- Ghép kết quả "hiểu video" vào một **cơ sở dữ liệu địa điểm (Place Graph)** do ta sở hữu: mỗi homestay, khách sạn, điểm tham quan, quán, bến xe, sân bay, ga tàu là một thực thể duy nhất. Thực thể đó được liên kết với tọa độ, mã trên các OTA (mapping, tài liệu 04), tài khoản mạng xã hội, số Zalo của chủ, và **danh sách bài đăng (video, ảnh, bài viết) nhắc đến nó**.
- Dữ liệu bồi đắp theo thời gian từ: bản đồ (Google Places, OpenStreetMap, các dịch vụ bản đồ VN), dữ liệu OTA đối tác, creator gắn thẻ, người dùng xác nhận, đội nội dung kiểm duyệt.
- **Càng nhiều bài được xử lý, đồ thị càng giàu, độ chính xác càng cao.** Đây là "hào nước" mà OTA truyền thống chưa có: họ biết *khách sạn*, nhưng không biết *video nào đang làm khách sạn đó hot*.
- Tạo ra tín hiệu **Trend**: số video, lượt xem, tốc độ tăng theo địa điểm/tuần. Tín hiệu này dùng cho tiêu chí "đang trend" mà ý tưởng ban đầu đề cập.

### 4.3. Lên kế hoạch chuyến đi (Trip Planner)
- **Biết điểm xuất phát**: hỏi một lần hoặc dùng vị trí, kèm ngày đi, số người, ngân sách.
- **Chọn phương tiện đa phương thức** (multimodal transport): máy bay, xe khách, tàu, kết hợp chặng cuối (sân bay Liên Khương → trung tâm Đà Lạt; bến xe → homestay). So sánh theo *tổng thời gian cửa-đến-cửa* và *tổng chi phí*, không chỉ giá vé.
- **Chọn chỗ ở theo tiêu chí** (đúng như ý tưởng ban đầu):
  - Đúng chỗ trong video (K1).
  - Gần điểm trong video, gần bến xe hoặc sân bay (phù hợp nếu đến muộn, đi sớm).
  - Đang trend (nhiều video gần đây), "giống phong cách video" (tìm theo hình ảnh tương đồng).
  - Theo ngân sách, có phòng trống thật.
- **Ghép lịch trình** ngày-theo-ngày, có thể gom thêm các điểm từ *những video khác người dùng đã lưu*. Tính năng "Bộ sưu tập video → một chuyến đi" là điểm khác biệt mạnh.
- **Nguyên tắc**: AI *đề xuất*, người dùng *chỉnh*. Kết quả luôn có lựa chọn thay thế, và mọi giá hiển thị phải là giá thật từ nguồn hàng, không phải giá do AI "tưởng tượng".

### 4.4. Tìm hàng & giá (Inventory Aggregation)
- Không tự xây kho hàng từ đầu. Thay vào đó **kết nối nhiều nguồn qua lớp chuyển đổi (adapter)**:

| Ngành hàng | Nguồn giai đoạn đầu | Nguồn về sau |
|---|---|---|
| Khách sạn/resort | Chương trình affiliate/API của OTA (Agoda, Booking, Trip.com, Traveloka…), bedbank | Hợp đồng trực tiếp |
| Homestay viral không có trên OTA | **Ký trực tiếp** + đặt qua người vận hành (concierge) / chủ nhà xác nhận qua Zalo | Extranet tối giản cho chủ homestay |
| Xe khách, xe limousine | Đối tác bán vé xe (API/affiliate) | Ký trực tiếp nhà xe tuyến hot |
| Vé máy bay | Affiliate/API của OTA hoặc đại lý cấp 1 | Đại lý xuất vé |
| Tàu | Kênh bán của ngành đường sắt / đối tác | — |
| Hoạt động, vé tham quan | Affiliate (Klook, Traveloka Xperience…) | Ký trực tiếp |

### 4.5. Đặt chỗ & thanh toán (Booking & Checkout) — tiến hóa theo 3 mức
| Mức | Cách làm | Ưu | Nhược |
|---|---|---|---|
| **M1 – Chuyển hướng (Affiliate redirect)** | Đưa người dùng sang trang đối tác đã điền sẵn thông tin, ta nhận hoa hồng | Nhanh, không giữ tiền, không cần giấy phép phức tạp | Mất trải nghiệm liền mạch, khó gom thành gói, hoa hồng thấp |
| **M2 – Đặt hộ có người hỗ trợ (Concierge)** | Người dùng gửi yêu cầu, đội vận hành đặt với homestay/nhà xe, thu tiền qua cổng thanh toán | Mở được hàng "không có trên OTA", học được nhu cầu thật | Không mở rộng được nếu nhiều đơn, tốn người |
| **M3 – Đặt trực tiếp trong nền tảng (Agency/Merchant)** | Một giỏ hàng, một thanh toán cho cả xe + phòng + hoạt động | Liền mạch, đúng tầm nhìn "1 click", biên cao | Cần giấy phép (combo = lữ hành, tài liệu 08), đối soát, CS, chống gian lận |

→ Lộ trình: **M1 + M2 song song ở MVP**, sau đó chuyển dần sang **M3** cho ngành hàng và tuyến đã có nhu cầu.

### 4.6. Ghi nhận nguồn & hoa hồng (Attribution & Creator Economy)
- Mỗi đơn đặt cần biết đến từ **video nào, creator nào, kênh nào** (dùng link/mã theo dõi).
- Chia hoa hồng cho creator theo đơn *đã hoàn thành* (không phải đơn đã hủy).
- Dashboard cho creator: video nào ra đơn, thu nhập bao nhiêu.
- Gắn nhãn minh bạch: "Creator nhận hoa hồng", "Nội dung hợp tác".

### 4.7. Trí tuệ cộng đồng (Community Intelligence) — chắt lọc bình luận

Năng lực này phục vụ kịch bản K3 và là **điểm khác biệt thứ hai** bên cạnh Place Graph.

**a) Phân loại từng bình luận theo ý định**

| Loại | Ví dụ | Dùng để |
|---|---|---|
| **Gợi ý thay thế (chỗ ở/điểm đến)** | "Qua homestay Y view đẹp hơn" | Thêm ứng viên vào danh sách so sánh |
| **Thông tin giá** | "Giờ lên 1tr2 rồi", "Đặt qua Zalo chủ rẻ hơn 200k" | So sánh với giá thật đang có |
| **Mẹo phương tiện** | "Đi limousine đón tận nhà", "Bay về Cam Ranh rồi đi xe lên rẻ hơn" | Bổ sung lựa chọn trong Trip Planner |
| **Thời điểm** | "Tháng 11 mùa dã quỳ", "Cuối tuần đông lắm" | Gợi ý ngày đi |
| **Cảnh báo / kiểm chứng** | "Ảnh filter, ngoài đời nhỏ", "Đường đèo sạt lở mùa mưa", "Chủ hủy phòng sát ngày" | Hiển thị cảnh báo, hạ điểm tin cậy |
| **Câu hỏi chưa có lời đáp** | "Có cho mang thú cưng không?" | Gợi ý nội dung cần bổ sung; hỏi lại chủ nhà |
| **Nhiễu / spam / seeding** | "Inbox em giá tốt", bình luận lặp lại hàng loạt | Loại bỏ |

**b) Tổng hợp thành "Thẻ cộng đồng" (Community Card)**
```
Cộng đồng nói gì về bài này (phân tích 230 bình luận, cập nhật 2 ngày trước)
 • 14 người gợi ý Homestay B (cách 300m, view tương tự) → hiện có phòng 750k/đêm   [Xem] [So sánh]
 • 9 người khuyên đi limousine thay vì bay (đón tận nhà, tổng thời gian tương đương) [Xem chuyến]
 • 6 cảnh báo: phòng thực tế nhỏ hơn ảnh
 • Thời điểm được khen nhiều nhất: tháng 11–12
```

**c) Nguyên tắc bắt buộc**
1. **Luôn kiểm chứng gợi ý bằng dữ liệu thật** trước khi hiển thị như một lựa chọn đặt được. "Rẻ hơn" phải so trên giá và lịch trống thật hôm nay, vì bình luận có thể đã cũ nhiều tháng.
2. **Đo độ tin cậy của từng gợi ý** dựa trên: số người độc lập cùng nhắc, độ mới, lượt thích hoặc phản hồi, tài khoản có dấu hiệu thật hay không, và có mâu thuẫn với bình luận khác không.
3. **Chống seeding trong bình luận**: nhiều tài khoản mới cùng khen một chỗ, câu chữ giống nhau, hoặc chèn số điện thoại/link → hạ trọng số hoặc loại bỏ.
4. **Không hiển thị danh tính người bình luận**. Chỉ hiển thị *ý kiến đã tổng hợp*. Dữ liệu cá nhân trong ảnh chụp màn hình (tên, avatar) phải được bỏ đi sau khi phân tích (xem tài liệu 08 về dữ liệu cá nhân).
5. **Phân biệt "thông tin cộng đồng" với "đề xuất của nền tảng"**: người dùng phải thấy rõ gợi ý nào đến từ bình luận, gợi ý nào là đối tác trả phí.

**d) Vòng lặp tích lũy**
Sau mỗi chuyến đi đặt qua nền tảng, mời người dùng để lại review đã xác minh, kèm *"bạn có gợi ý gì tốt hơn?"*. Theo thời gian, cộng đồng riêng này thay thế dần sự phụ thuộc vào bình luận trên nền tảng khác.

---

## 5. Kiến trúc khái niệm (mức logic, chưa chọn công nghệ)

```
┌──────────────────────────────── CHANNELS ────────────────────────────────┐
│  Web/PWA  │  Zalo OA bot  │  Creator Trip Page  │  App (sau)  │  Extension (sau) │
└────┬─────────────┬────────────────┬──────────────────┬──────────────────┘
     ▼             ▼                ▼                  ▼
┌──────────────────────── API GATEWAY / BFF (một cửa) ───────────────────────┐
└────┬───────────────┬──────────────┬────────────────┬───────────────┬──────┘
     ▼               ▼              ▼                ▼               ▼
 [Content Ingestion]  [Understanding]  [Community        [Place Graph &]  [Trip Planner]  [Identity &
  link/ảnh/screenshot  AI đa phương     Intelligence]     Trend Signals    (đề xuất, ghép   User Profile]
  từ mọi nền tảng →    thức → intent    phân loại, tổng   (thực thể, map,   lịch trình,
  "Content Item"                        hợp bình luận,    độ hot)           so sánh phương án
                                        chống seeding                       từ cộng đồng)
                                        │                │
                                        ▼                ▼
                               [Inventory Aggregation – Adapters]
                                OTA affiliate │ Bus │ Air │ Rail │ Direct homestay
                                        │
                                        ▼
                    [Booking / Order] ── [Payment] ── [Notification]
                                        │
                    [Attribution & Creator Payout] ── [Analytics / Data]
                                        │
                    [Back-office: concierge, CS, kiểm duyệt nội dung, đối soát]
```

### Nguyên tắc kiến trúc (theo góc nhìn SWE)
1. **Modular monolith trước, microservices sau**: đội nhỏ nên tách *module* rõ ràng theo các năng lực ở mục 4, nhưng triển khai chung. Chỉ tách thành service riêng khi một module có tải hoặc nhịp phát triển khác hẳn (ví dụ AI Understanding chạy bất đồng bộ, tốn GPU).
2. **Xử lý video theo luồng bất đồng bộ (async pipeline)**: người dùng share link thì thấy ngay kết quả sơ bộ (từ caption, tag vị trí) trong 1–3 giây, phần phân tích sâu (giọng nói, hình ảnh) cập nhật sau. Không bắt người dùng chờ.
3. **Cache theo bài**: một bài viral được hàng nghìn người share. Phân tích *một lần*, dùng cho mọi người, nhờ đó chi phí AI giảm theo quy mô.
4. **Lớp adapter cho nguồn hàng**: mọi đối tác (OTA, nhà xe, homestay) được chuẩn hóa về một mô hình chung (sản phẩm, giá, tồn kho, chính sách). Thêm hay bớt đối tác không ảnh hưởng phần còn lại.
5. **Human-in-the-loop**: có màn hình nội bộ để người vận hành sửa kết quả AI (ghép sai địa điểm). Dữ liệu sửa đó quay lại cải thiện Place Graph.
6. **AI không bao giờ là nguồn sự thật về giá và tồn kho**: AI chỉ hiểu ý định và gợi ý. Giá và phòng luôn lấy từ nguồn hàng thật tại thời điểm đặt.
7. **Đo lường từ ngày đầu**: theo dõi phễu *Share → Nhận diện đúng → Xem kế hoạch → Bấm đặt → Đặt thành công*.
8. **Độc lập nguồn (Source-agnostic)**: thêm một nền tảng mạng xã hội mới chỉ cần viết thêm *một bộ chuyển đổi đầu vào* về mô hình "Content Item". Toàn bộ phần hiểu nội dung, cộng đồng, lên kế hoạch và đặt chỗ phía sau giữ nguyên.
9. **Dữ liệu bình luận có "hạn sử dụng"**: mọi gợi ý từ cộng đồng lưu kèm thời điểm, giảm trọng số theo thời gian, và được kiểm chứng lại với giá/lịch trống thật trước khi hiển thị.

---

## 6. Mô hình kinh doanh

| Nguồn thu | Giai đoạn |
|---|---|
| Hoa hồng affiliate từ OTA/đối tác (M1) | MVP |
| Phí dịch vụ đặt hộ / markup homestay ký trực tiếp (M2) | MVP |
| Hoa hồng/markup khi đặt trực tiếp, đặc biệt **combo xe + phòng** (M3) | Pha 2 |
| Homestay/khách sạn trả phí để **được gắn với video** hoặc tăng hiển thị | Pha 2 |
| Công cụ cho cơ sở lưu trú: "biến video TikTok của bạn thành kênh đặt phòng" (SaaS) | Pha 2–3 |
| Phí bảo hiểm, bán kèm, dữ liệu xu hướng (trend insight) cho ngành | Pha 3 |
| Chi phí ngược: **chia hoa hồng cho creator** (đây là chi phí marketing chuyển đổi được) | Mọi giai đoạn |

---

## 7. Giả thuyết cần kiểm chứng & cách kiểm chứng rẻ nhất

| # | Giả thuyết | Thí nghiệm rẻ nhất | Ngưỡng thành công (gợi ý) |
|---|---|---|---|
| H1 | Người dùng *sẵn sàng* share/dán link video để được giúp đặt | Landing page + Zalo OA "Gửi link TikTok, nhận báo giá chuyến đi trong 15 phút" (người thật xử lý) | ≥ 30% người vào trang gửi link |
| H2 | Có thể nhận diện đúng địa điểm/cơ sở từ video | Thử thủ công rồi bằng AI trên 300 video du lịch Đà Lạt/Phú Quốc | ≥ 70% đúng cơ sở (K1), ≥ 90% đúng khu vực (K2) |
| H3 | Người dùng chuyển thành đơn đặt | Concierge đặt hộ trong 4–6 tuần | ≥ 5–10% yêu cầu ra đơn |
| H4 | Creator muốn dùng trang chuyến đi + hoa hồng | Mời 10–20 creator du lịch nhỏ (micro, 10k–100k follower) | ≥ 50% đặt link vào bio, có đơn phát sinh |
| H5 | Homestay viral chịu trả hoa hồng | Ký 20–30 homestay ở một điểm đến | ≥ 60% đồng ý mức 10–15% |
| H6 | Tổng hợp bình luận tạo ra lựa chọn tốt hơn *thật* | Lấy 100 bài có nhiều bình luận, đội vận hành tổng hợp tay rồi kiểm chứng giá thật | ≥ 40% bài có ít nhất 1 gợi ý thay thế rẻ hơn hoặc tiện hơn, kiểm chứng được |
| H7 | Người dùng tin và chọn phương án từ cộng đồng | Hiển thị Thẻ cộng đồng trong concierge, đo tỷ lệ chọn phương án thay thế | ≥ 20% đơn chọn phương án gợi ý từ cộng đồng |
| H8 | Người dùng sẵn sàng gửi ảnh chụp màn hình | Trong chatbot, mời "chụp màn hình bài/bình luận gửi mình" | ≥ 30% yêu cầu có ảnh chụp màn hình |

---

## 8. Lộ trình đề xuất

| Pha | Thời gian (ước lượng) | Phạm vi | Mục tiêu |
|---|---|---|---|
| **0 – Concierge thử nghiệm** | 4–6 tuần | **1 điểm đến (gợi ý: Đà Lạt**, vì nhiều video homestay và đa phương tiện: bay, xe, limousine). Landing page + Zalo OA + đội vận hành thủ công + bảng tính. Ký 20–30 homestay | Kiểm chứng H1, H3, H5. Thu dữ liệu thật về câu hỏi của người dùng |
| **1 – MVP sản phẩm** | 3–4 tháng | Web/PWA: dán/share link (TikTok, Instagram, Threads, Facebook) hoặc gửi ảnh chụp màn hình → nhận diện (AI + người duyệt) → Thẻ cộng đồng v1 (từ ảnh chụp bình luận) → trang địa điểm + kế hoạch đơn giản (xe/bay + phòng) → đặt bằng affiliate (M1) và đặt hộ (M2). Trang chuyến đi cho creator v1. Place Graph cho 2–3 điểm đến | Kiểm chứng H2, H4. Phễu chuyển đổi |
| **2 – Giỏ hàng & app** | 4–6 tháng | Đặt trực tiếp combo xe + phòng (M3), thanh toán nội địa, extranet tối giản cho homestay, native app + Share Extension, bộ sưu tập video → chuyến đi, Zalo Mini App | Tăng biên & giữ chân |
| **3 – Mở rộng** | Sau đó | Thêm điểm đến, vé máy bay/tàu đầy đủ, extension cho creator/đại lý, API/Widget B2B, đàm phán tích hợp chính thức với nền tảng video, outbound (Thái, Hàn, Nhật) | Quy mô |

---

## 9. Rủi ro chính & cách giảm thiểu

| Rủi ro | Mức | Giảm thiểu |
|---|---|---|
| Nền tảng video đổi chính sách, chặn dữ liệu | Cao | Chỉ dùng API/oEmbed chính thức + dữ liệu người dùng/creator cung cấp; đa kênh vào; không phụ thuộc một nền tảng |
| Nền tảng tự làm tính năng đặt du lịch (TikTok, Google) | Cao | Tập trung vào thứ họ khó làm ở VN: homestay ký trực tiếp, combo xe nội địa, CS tiếng Việt, Place Graph địa phương. Sẵn sàng trở thành *đối tác cung cấp hàng* cho họ |
| AI nhận diện sai → đặt nhầm chỗ | Cao | Hiển thị độ tin cậy, bắt người dùng xác nhận, có người duyệt ở giai đoạn đầu, chính sách hỗ trợ khi sai |
| Seeding gây mất niềm tin | TB | Nhãn minh bạch, review đã xác minh, chấm điểm "khớp thực tế" |
| Homestay nhỏ không ổn định (hết phòng, đổi giá qua Zalo) | TB | Extranet siêu đơn giản (hoặc xác nhận qua Zalo), chính sách cam kết, phương án thay thế |
| Chi phí AI theo video | TB | Cache theo video, xử lý theo tầng (rẻ trước, sâu sau), chỉ phân tích sâu khi có người quan tâm |
| Pháp lý: bán combo = lữ hành; quảng cáo; dữ liệu cá nhân | TB | Bắt đầu bằng affiliate/đại lý; xin giấy phép lữ hành trước khi bán combo trực tiếp (tài liệu 08) |
| Tính mùa vụ & phụ thuộc trend | TB | Đa dạng điểm đến; dùng tín hiệu trend để đi trước xu hướng |
| Không lấy được bình luận qua API chính thức | Cao | Thiết kế 3 tầng dữ liệu bình luận (mục 2.2): ảnh chụp màn hình → creator cấp quyền → cộng đồng riêng |
| Bình luận cũ, sai, hoặc bị đối thủ/chủ nhà thao túng | TB | Kiểm chứng với dữ liệu thật, giảm trọng số theo thời gian, phát hiện seeding, chỉ hiển thị ý kiến đã tổng hợp |
| Dữ liệu cá nhân trong bình luận/ảnh chụp màn hình | TB | Bỏ thông tin định danh sau khi phân tích, không lưu ảnh gốc lâu hơn cần thiết, có điều khoản đồng ý rõ ràng |

---

## 10. Tóm tắt khuyến nghị

1. **Sản phẩm là "lõi chuyển nội dung mạng xã hội thành chuyến đi"**, không phải "một cái app" hay "một extension". Lõi này độc lập nguồn (TikTok, Instagram, Threads, Facebook…) và độc lập định dạng (video, ảnh, chữ, bình luận, ảnh chụp màn hình). Kênh chỉ là cửa vào.
2. **Ảnh chụp màn hình là cửa vào phổ quát nhất** cho cả nội dung lẫn bình luận, vì không phụ thuộc API của nền tảng nào. Nên có ngay từ MVP.
3. **Trí tuệ cộng đồng (K3) là điểm khác biệt thứ hai**: biến bình luận rời rạc thành *phương án thay thế đã kiểm chứng* (rẻ hơn, đẹp hơn, đi tiện hơn) và *cảnh báo*.
4. **Thứ tự kênh**: Web/PWA + Zalo OA + trang chuyến đi cho creator → native app (có Share Extension) → Zalo Mini App → extension/API. **Không bắt đầu bằng extension** vì sai thiết bị với hành vi người dùng.
5. **Lợi thế cạnh tranh là Place Graph + tín hiệu trend + nguồn hàng homestay ký trực tiếp**, chứ không nằm ở giao diện.
6. **Đặt chỗ tiến hóa theo 3 mức**: affiliate → đặt hộ → giỏ hàng trực tiếp, để giảm rủi ro pháp lý và vốn ban đầu.
7. **Bắt đầu hẹp**: một điểm đến (Đà Lạt), hai ngành hàng (homestay + xe/limousine), một nhóm creator nhỏ. Kiểm chứng bằng concierge trước khi đầu tư AI.

### Câu hỏi cần bạn quyết định tiếp
- Đơn vị chủ quản đã có sẵn nguồn hàng nào (khách sạn, nhà xe, tour) để làm "hàng độc quyền" ban đầu không?
- Có sẵn sàng vận hành đội concierge (người thật) trong 1–2 tháng đầu không?
- Ưu tiên K1 (đặt đúng chỗ trong bài) hay K2 (lên chuyến đi đến điểm trong bài) cho MVP? Khuyến nghị: **K1 + K3 trước** (đặt đúng chỗ, kèm Thẻ cộng đồng gợi ý chỗ thay thế), vì đơn giản hơn, chứng minh giá trị nhanh và thể hiện rõ điểm khác biệt. K2 dùng lại toàn bộ hạ tầng của K1.
