# 04. Định nghĩa lại ý tưởng: "TikTok cho du lịch, xem là đặt được"

> Tài liệu này định nghĩa lại ý tưởng của đội, khảo sát những sản phẩm đã có, chọn lọc tính năng hay, phân tích sâu vấn đề **seeding bẩn** và thiết kế **cơ chế thưởng cho video viral**.
> Quan hệ với các tài liệu khác:
> - [02 – Đề bài](02-de-bai-vinpearl-cho-nguoi-tre.md) vẫn là nguồn sự thật cao nhất cho phần thi.
> - [03 – Ý tưởng triển khai](03-y-tuong-trien-khai.md) là bản bám sát đề bài; tài liệu này bổ sung **hướng sản phẩm** và **chiều sâu về chống seeding và thưởng creator**.
> - Ý tưởng có vài điểm **vượt khỏi phạm vi đề bài**; những điểm đó được đánh dấu ⚠️ ở mục 2.

---

## 1. Định nghĩa ý tưởng

### 1.1. Phát biểu một câu
> **Một ứng dụng video ngắn chuyên về du lịch. Mỗi video được "neo" vào chỗ có thật và đặt được. Người xem bấm một lần là có cả chuyến đi y chang, tính từ thành phố của chính họ. Người đăng được thưởng khi người khác thực sự đi theo họ, chứ không phải khi video chỉ nhiều view.**

### 1.2. Ba khác biệt so với TikTok thường

| | TikTok / Reels thường | Ứng dụng của mình |
|---|---|---|
| **Nội dung** | Bất kỳ ai, bất kỳ video nào, không biết thật giả | Video **gắn với chuyến đi đã xác minh**: người đăng đã đặt và đã đi; quảng cáo phải gắn nhãn |
| **Hành động sau khi xem** | Lưu, bình luận "xin info", tự đi tìm | Nút **"Đi y chang"**: phương tiện từ nơi mình ở + chỗ ở + vé vui chơi, giá thật, một lần thanh toán |
| **Động lực của người đăng** | View, follower, hợp đồng quảng cáo ngầm | Thưởng dựa trên **số người thực sự đi theo** và **mức độ hữu ích**, không dựa trên view hay việc khen |

### 1.3. Kịch bản gốc của đội, viết lại thành user story

> *Tôi ở Sài Gòn, lướt thấy video một homestay săn mây đẹp. Tôi muốn đi. Tôi bấm "Đi y chang", chọn ngày, chọn 2 người. App tự đề xuất vé máy bay SGN → điểm gần nhất, xe đưa đón, đêm ở đúng căn trong video, kèm gợi ý mùa đẹp nhất. Tôi trả tiền một lần và nhận lịch trình.*

Từ đây có **ba bài toán lõi**:
1. **Nhận diện**: video này nói về chỗ nào, có đặt được không? → giải bằng cách **neo** video vào chỗ ngay khi đăng, không phải đoán sau.
2. **Ghép chuyến từ nơi người xem đang ở**: lộ trình của người đăng tính từ Hà Nội, người xem ở Sài Gòn → phải **tính lại chặng đi**.
3. **Tin được không**: video thật hay seeding bẩn? → mục 5 và mục 6.

---

## 2. Đối chiếu với đề bài: chỗ nào khớp, chỗ nào vượt phạm vi

| Ý tưởng của đội | Đề bài | Cách xử lý |
|---|---|---|
| App video ngắn, bảng tin trending | ✅ Khớp A1 (bảng tin xu hướng), A2 (bài có chữ, ảnh và **video ngắn**) | Làm **video-first** cho app Android: bảng tin dạng cuộn dọc toàn màn hình |
| Bấm vào là tới thẳng trang đặt | ✅ Khớp A4 (một chạm), A11 (Đặt ngay theo lộ trình) | Nút "Đi y chang" chính là A11 |
| Người dùng thật, trải nghiệm thật | ✅ Khớp A2 ("chỉ người đã đặt và đã đi mới đăng"), quy tắc "không bài đăng giả" | Đây là **lá chắn chống seeding mạnh nhất**, đề bài đã bắt buộc |
| ⚠️ **Địa điểm ngoài Vinpearl** (homestay Tà Xùa, quán cà phê) | ❌ Mục 8: "Đặt dịch vụ không thuộc hệ thống Vinpearl" nằm ngoài phạm vi | **Bản dự thi**: chỉ neo vào dịch vụ Vinpearl (Phú Quốc, Nha Trang, Hội An…). Quán cà phê, điểm ngắm cảnh *không đặt được* có thể xuất hiện như **"điểm gợi ý ghé thăm"** trong lộ trình, nhưng không có nút đặt (cần hỏi người hướng dẫn). **Bản tầm nhìn**: mở cho nhà cung cấp ngoài (mục 8) |
| ⚠️ **Trả tiền / voucher cho video viral** | Đề bài **không đề cập**. Có ràng buộc liên quan: "không bịa khuyến mãi", "không thao túng" | Đề xuất làm như một **phần mở rộng tự đề xuất**: thưởng bằng *điểm/voucher Vinpearl thật*, thưởng theo **lượt đặt hoàn thành** chứ không theo view (mục 6). **Phải hỏi người hướng dẫn trước khi làm** |
| ⚠️ Seeding bẩn | Đề bài có các ý gần: A14 (kiểm duyệt), A15 ("dấu hiệu xu hướng bị đẩy ảo"), mục 6 (quảng cáo, lừa đảo) | Phân tích sâu ở mục 5 và áp vào A14, A15 |
| Người xem ở Sài Gòn, tính chặng đi từ nơi mình ở | Đề bài: A11 cho "chọn ngày và số người"; không nói về nơi xuất phát | Đề xuất bổ sung: chọn **điểm xuất phát** → thay chặng bay trong lộ trình bằng chặng từ thành phố của người xem (nếu dữ liệu có chặng đó) |

**Kết luận**: khoảng 80% ý tưởng **trùng khớp** với đề bài. Đây là tín hiệu tốt, vì đề bài thực chất mô tả đúng loại sản phẩm này. Phần vượt phạm vi (ngoài Vinpearl, thưởng creator) nên tách thành **"tầm nhìn sau dự án"**, hoặc làm ở mức đề xuất có kiểm soát sau khi người hướng dẫn đồng ý.

---

## 3. Đã có ai làm chưa? Khảo sát thị trường

**Trả lời ngắn: có, và đang nóng.** Mô hình "video ngắn → đặt được" (shoppable travel video) đã được nhiều công ty lớn và startup triển khai từ 2024–2026. Đây là tin tốt (thị trường đã được chứng minh) và cũng là cảnh báo (phải có khác biệt).

| Sản phẩm | Mô hình | Điểm đáng học | Điểm yếu / khoảng trống |
|---|---|---|---|
| **TikTok GO** (thử từ 2025 ở Mỹ, Indonesia, Nhật; mở rộng ở Mỹ 2026) | Creator gắn thẻ khách sạn/tour trong video; người xem bấm để đặt; đối tác gồm Booking.com, Expedia, Viator, GetYourGuide, Trip.com; creator nhận hoa hồng khi có người đặt qua link | Gắn thẻ địa điểm trong video; hoa hồng theo lượt đặt; đặt chỗ chỉ cho người từ 18 tuổi | TikTok **không tự xử lý thanh toán** mà chuyển sang đối tác; **không xác minh người đăng đã ở thật**; chưa thấy thông tin ra mắt tại Việt Nam |
| **Trip.com – Trip.Moments / Trip Community** (12/2025) | Cộng đồng nội dung gắn với đặt chỗ; hơn 2,5 triệu người dùng Trip.Moments, hơn 12.000 Trip.Pals được xác minh; quỹ khuyến khích 100 triệu NDT; chương trình Tripblazers trả hoa hồng khi có người đặt | Creator được xác minh; quỹ thưởng gồm traffic, hoa hồng, hợp tác thương hiệu; nội dung và trợ lý lập kế hoạch đi chung một hệ | Nội dung vẫn pha lẫn quảng bá; sản phẩm đa ngành, không tập trung video |
| **Xiaohongshu / RedNote** (Trung Quốc) | Công cụ tìm kiếm và khám phá du lịch số 1 của người Trung Quốc; khách sạn bán qua mini-program bên thứ ba | Chứng minh "**nội dung cộng đồng là nơi ra quyết định**" | Khâu đặt chỗ rời rạc; seeding phổ biến |
| **Travly** (startup, 2024) | Nền tảng đặt chỗ bằng video ngắn; **người đăng video nhận hoa hồng** khi có người đặt khách sạn từ video của họ; **chỉ giữ một video cho mỗi khách sạn**, video kém thì thay | Trả thưởng theo lượt đặt; giới hạn số video mỗi khách sạn để chống spam | Không xác minh lưu trú |
| **Roomer** | Xem clip thật của khách rồi **đặt đúng phòng đó** bằng một chạm | Đặt *đúng phòng* xuất hiện trong video | — |
| **ReelStay** | Reel video là trung tâm khám phá và đặt chỗ; creator đăng reel và nhận thưởng khi nội dung có tương tác | Mô hình gần nhất với ý tưởng của đội | Thưởng theo tương tác thì dễ bị đẩy ảo |
| **Tripbtoz** (Hàn Quốc) | Người dùng **kiếm tiền khi chia sẻ video du lịch**, chia sẻ càng nhiều thưởng càng nhiều; có review video ngắn | Biến người đi du lịch thành người làm nội dung | Không thấy thông tin video phải gắn với đơn đã xác minh |
| **Unravel** (London, Series A 7 triệu USD) | Bên B2B làm video "mua được" bằng AI, cung cấp cho app ngân hàng, bảo hiểm, nhà mạng | Video có thể sản xuất và cá nhân hóa bằng AI | Nội dung do nền tảng làm, không phải người thật |
| **Booking.com – review đã xác minh**, **Airbnb – review hai chiều** | Chỉ người đã ở mới được review; chủ và khách đánh giá nhau | Chuẩn mực niềm tin của ngành | Review dạng chữ, không phải video |

**Khoảng trống cho đội**:
1. Gần như **không ai kết hợp cả ba thứ**: (a) video ngắn, (b) **người đăng bắt buộc đã đặt và đã đi thật**, (c) **một chạm đặt cả lộ trình kể cả chặng đi từ thành phố người xem**. TikTok GO có (a) và một phần (c); Booking có (b) nhưng không có (a).
2. **Chưa thấy TikTok GO ở Việt Nam** (theo các nguồn tìm được), nên vẫn còn cửa sổ thời gian.
3. **Thưởng theo view hoặc tương tác** (ReelStay, Tripbtoz) chính là nguồn sinh seeding. Thiết kế thưởng **theo lượt đặt hoàn thành và độ hữu ích** là điểm khác biệt có lý lẽ.

---

## 4. "Cop nhặt" tính năng hay và cách đưa vào app của mình

| Tính năng | Học từ | Áp vào app của mình | Yêu cầu đề bài liên quan |
|---|---|---|---|
| **Bảng tin cuộn dọc toàn màn hình**, tự phát video | TikTok, Reels | Bảng tin video-first. Trên máy yếu, mạng yếu thì tự hạ chất lượng, hiện ảnh bìa trước | A1 |
| **Thẻ địa điểm gắn trong video** | TikTok GO | Mỗi video có thẻ chỗ (khách sạn, VinWonders…) hiện **đúng giây** chỗ đó xuất hiện; bấm thẻ thì tới trang chi tiết | A4 |
| **Đặt đúng phòng trong video** | Roomer | Người đăng chọn *loại phòng* đã ở từ đơn của mình, nên người xem đặt đúng loại đó | A4, A11 |
| **Đặt cả lộ trình một lần** | Gói Flight + Hotel của Expedia | Nút "Đi y chang" có cả chặng đi từ thành phố của người xem | A11 |
| **Review đã xác minh** | Booking.com | Dấu **"Đã đi thật"** bắt buộc | A2 |
| **Creator được xác minh + quỹ thưởng** | Trip.com Trip.Pals, Tripblazers | Hạng **"Người dẫn đường"** cho người đăng có nhiều người đi theo thật | (đề xuất mở rộng) |
| **Một video nổi bật mỗi chỗ / mỗi góc** | Travly | Gom các video cùng chỗ thành **"bộ sưu tập theo chỗ"**, xếp theo độ hữu ích; tránh 50 video na ná nhau chiếm bảng tin | A1 (không lặp, đa dạng) |
| **Nhãn hợp tác trả phí** | TikTok, Instagram (Paid partnership) | Nhãn **"Hợp tác"** bắt buộc với nội dung có tài trợ | Mục 6, Luật Quảng cáo |
| **Tóm tắt review bằng AI** | Amazon, Booking | "50 người đã đi nói gì": điểm cộng, điểm trừ, có trích dẫn | B1 |
| **Hỏi người đã đi** | Q&A của Tripadvisor, Amazon | Câu hỏi dưới video được chuyển tới những người **đã đi chỗ đó** để họ trả lời | A10 (mở rộng) |
| **Ghi chú cộng đồng** | Community Notes của X | Người đã đi chỗ đó thêm ghi chú bổ sung ("phòng thật nhỏ hơn góc quay"); ghi chú chỉ hiện khi được nhiều người có quan điểm khác nhau cùng đánh giá là hữu ích | A14 (giảm tải kiểm duyệt) |
| **Trợ lý lên lịch trình** | Trip.com TripGenie | Trợ lý gợi ý chỗ và video có thật | A12 |
| **Huy hiệu, cấp độ đóng góp** | Google Maps Local Guides | Cấp độ dựa trên đóng góp hữu ích, không dựa trên view | (đề xuất mở rộng) |
| **"Mùa đẹp nhất"** | Nội dung du lịch theo mùa | Mỗi video ghi *mùa quay*; trang chỗ gom "video theo mùa" (ví dụ mùa săn mây khác mùa mưa) | A1 (lý do ngắn), A11 |

---

## 5. Phân tích sâu: seeding bẩn

### 5.1. Phân loại nội dung (cần hiểu đúng trước khi chặn)

| Loại | Mô tả | Hợp pháp? | Xử lý |
|---|---|---|---|
| **Trải nghiệm thật, tự nguyện** | Người đã đi kể lại, khen hoặc chê | ✅ | Ưu tiên cao nhất |
| **Seeding sạch** (hợp tác minh bạch) | Được tài trợ (miễn phí phòng, trả tiền) nhưng **ghi rõ "Hợp tác"** và vẫn là trải nghiệm thật | ✅ (phải ghi nhãn) | Cho phép, gắn nhãn, xếp hạng thấp hơn trải nghiệm tự nguyện một chút |
| **Seeding bẩn** | Quảng cáo trá hình **không ghi nhãn**; người chưa từng đến mà vẫn quay; video dàn dựng hoặc chỉnh sửa quá mức gây hiểu sai; bình luận và lượt xem mua | ❌ (vi phạm quy định quảng cáo, gây hiểu nhầm người tiêu dùng) | Chặn, gỡ, phạt |
| **Seeding đen** (phá đối thủ) | Review xấu giả, báo cáo hàng loạt để gỡ video thật của đối thủ | ❌ | Phát hiện, phạt tài khoản |
| **Lừa đảo** | Homestay ảo, xin chuyển khoản cọc ngoài nền tảng, giả danh chủ chỗ | ❌ (hình sự) | Chặn ngay, báo cáo |
| **Farm thưởng** | Dựng chuỗi tài khoản tự đặt, tự đi, tự đẩy để ăn thưởng hoặc voucher | ❌ (gian lận chương trình) | Phát hiện, thu hồi thưởng |

**Bối cảnh pháp lý**: Luật Quảng cáo sửa đổi 2025 (Luật số 75/2025/QH15, hiệu lực 1/1/2026) lần đầu đưa KOL/KOC vào khung pháp lý với vai trò "người chuyển tải sản phẩm quảng cáo". Luật yêu cầu bảo đảm trung thực và xác minh nội dung quảng cáo; quảng cáo phải trung thực, chính xác, không gây hiểu nhầm. *(Cần đối chiếu văn bản luật gốc.)* → Nhãn "Hợp tác" **không chỉ là tính năng hay mà là nghĩa vụ**.

### 5.2. Ai seeding bẩn và vì sao (mô hình động cơ)

| Tác nhân | Động cơ | Chiêu thường gặp |
|---|---|---|
| Chủ chỗ, đại lý | Tăng đơn, đặc biệt mùa thấp điểm | Thuê KOC quay không ghi nhãn; mua view, like, bình luận; nhân viên tự đăng |
| Creator săn thưởng | Thưởng, voucher, follower | Đăng video người khác (reup); chỉnh màu ảo; câu view; tạo nhiều tài khoản |
| Đối thủ | Kéo khách về mình | Review xấu giả, báo cáo hàng loạt |
| Kẻ lừa đảo | Chiếm tiền cọc | Video đẹp + "inbox giá rẻ hơn app", số điện thoại, link ngoài |
| Nhóm farm | Ăn thưởng | Chuỗi tài khoản tự đặt và tự "đi theo" lẫn nhau |

### 5.3. Các vector tấn công và biện pháp

| # | Vector | Biện pháp | Mức chắc chắn |
|---|---|---|---|
| V1 | **Người chưa đến vẫn đăng** | Bài phải gắn với **đơn của chính người đó đã hoàn thành** (yêu cầu bắt buộc A2). Server kiểm tra lại | **Rất cao**: chặn tận gốc |
| V2 | **Đến thật nhưng là quảng cáo không ghi nhãn** | (a) Bắt buộc tự khai "có nhận tài trợ không", khai sai thì phạt nặng; (b) phát hiện tài trợ ẩn: đơn có giá 0đ hoặc giảm sâu bất thường, đơn do tài khoản nhà cung cấp đặt hộ, nhiều creator cùng ở một chỗ cùng tuần rồi cùng đăng; (c) **người xem báo cáo "quảng cáo ẩn"**; (d) phạt: gắn nhãn hồi tố, thu hồi thưởng, hạ uy tín | Trung bình–cao |
| V3 | **Video dàn dựng, chỉnh màu quá mức, góc quay đánh lừa** | (a) **"Đối chiếu thực tế"**: hiện song song ảnh chính thức của chỗ và ảnh từ nhiều người đã đi khác; (b) **Ghi chú cộng đồng** từ người đã đi; (c) chấm điểm "khớp thực tế" từ người xem *sau khi đi theo*; (d) huy hiệu **"Quay tại chỗ"** nếu video quay trong app, trong thời gian lưu trú và tại vị trí của chỗ (người dùng đồng ý chia sẻ vị trí) | Trung bình |
| V4 | **Mua view, like, bình luận, nhiều tài khoản đẩy xu hướng** | Điểm xu hướng tính theo **người khác nhau × độ tin cậy tài khoản**; tác giả tự tương tác không tính; gom cụm thiết bị/mạng; phát hiện bùng nổ bất thường; **tín hiệu nặng nhất là lượt đặt hoàn thành**, không phải view (A15) | Cao |
| V5 | **Bình luận seeding** ("đẹp quá, mình cũng ở đây rồi") | Bình luận của người **đã đi chỗ đó** có huy hiệu riêng và được xếp trên; bình luận lặp mẫu hoặc từ tài khoản mới hàng loạt thì gom và ẩn | Trung bình–cao |
| V6 | **Review xấu giả, báo cáo hàng loạt** | Chỉ người đã đi mới được *đánh giá*; báo cáo được tính theo độ tin cậy người báo cáo; tài khoản báo cáo sai nhiều lần bị giảm trọng số | Cao |
| V7 | **Lừa đảo kéo ra ngoài nền tảng** (số điện thoại, "chuyển khoản cọc", link lạ) | Sàng lọc AI và luật khi đăng (số điện thoại kể cả viết bằng chữ, link, mã QR trong ảnh); cảnh báo người xem "chỉ thanh toán trong app"; giữ lại chờ duyệt | Cao |
| V8 | **Reup video của người khác** | Dấu vân tay video (perceptual hash) so với kho đã có; huy hiệu "Quay tại chỗ" | Trung bình–cao |
| V9 | **Farm thưởng** (tự đặt, tự đi theo) | Lượt "đi theo" **không tính** nếu người đặt có liên hệ với người đăng: cùng thiết bị, cùng phương thức thanh toán, cùng người đi kèm, hoặc tương tác qua lại bất thường; thưởng chỉ **mở khóa sau khi chuyến của người đi theo hoàn thành và qua hạn hủy**; có trần thưởng | Cao |
| V10 | **Chủ chỗ tạo tài khoản khách giả, tự đặt rồi tự đăng** | Đơn đặt có giá trị thật (trong thực tế là tiền thật; trong dự án là giả lập nên cần ghi chú giới hạn); tỷ lệ bài/đơn bất thường của một chỗ; tài khoản chỉ đăng về một chỗ | Trung bình |

### 5.4. Nguyên tắc thiết kế chống seeding
1. **Bằng chứng trải nghiệm (proof-of-stay)** là nền móng, vì đề bài đã bắt buộc. Seeding bẩn kiểu "chưa đến mà quay" bị loại tận gốc.
2. **Minh bạch thay vì cấm**: cho phép hợp tác nhưng phải gắn nhãn. Cấm hẳn chỉ đẩy hợp tác thành ngầm.
3. **Thưởng không bao giờ phụ thuộc việc khen**: video chê thật, hữu ích được thưởng như video khen.
4. **Tín hiệu đắt quyết định**: lượt đặt hoàn thành và đánh giá "khớp thực tế" sau chuyến nặng hơn view hay like rất nhiều.
5. **Cộng đồng người đã đi là người kiểm chứng**: ghi chú cộng đồng, hỏi người đã đi, chấm "khớp thực tế".
6. **Phạt đủ đau, có thể kháng nghị**: thu hồi thưởng, nhãn hồi tố, hạ uy tín, khóa; mọi quyết định có lý do và audit log (A14).

### 5.5. Điểm uy tín (Trust Score) của tài khoản và nội dung
- **Tài khoản**: tuổi tài khoản, đã xác minh, số chuyến đã đi thật, tỷ lệ bài bị gỡ, độ chính xác của các báo cáo, mức đa dạng hành vi, có liên hệ bất thường với nhà cung cấp hay không.
- **Video**: uy tín người đăng, có huy hiệu "Quay tại chỗ", điểm "khớp thực tế" từ người đi theo, ghi chú cộng đồng, nhãn hợp tác.
- **Dùng ở đâu**: trọng số khi xếp bảng tin và xu hướng; mức thưởng; độ ưu tiên trong hàng chờ kiểm duyệt; có hiện ghi chú cộng đồng hay không.

---

## 6. Thiết kế cơ chế thưởng cho video viral

### 6.1. Vấn đề với "thưởng theo view"
Thưởng theo view hoặc like **chính là động cơ tạo ra seeding bẩn**: câu view, mua view, nội dung giật gân, ảnh ảo. Nó cũng không đo giá trị thật cho cả người xem lẫn Vinpearl.

### 6.2. Đề xuất: thưởng theo **"người đi theo thật"** và **độ hữu ích**

| Thành phần | Cách tính | Trọng số gợi ý |
|---|---|---|
| **Lượt đi theo hoàn thành** | Người khác đặt qua "Đi y chang" hoặc qua thẻ chỗ trong video, **và chuyến đó đã hoàn thành** (không hủy) | Chính (khoảng 70%) |
| **Độ hữu ích** | Lượt lưu từ tài khoản uy tín, câu trả lời được đánh giá hữu ích trong "Hỏi người đã đi", điểm "khớp thực tế" cao | Phụ (khoảng 30%) |
| View, like | Chỉ dùng để xếp hạng hiển thị, **không quy ra thưởng** | 0 |

**Quy tắc**:
- **Hình thức thưởng**: điểm hoặc voucher **Vinpearl có thật** (ví dụ giảm giá lần đặt sau, vé VinWonders), đúng quy tắc "không bịa khuyến mãi". Tiền mặt chỉ cân nhắc ở bản tầm nhìn vì kéo theo vấn đề thuế và pháp lý.
- **Điều kiện**: tài khoản ≥ 18 tuổi, đã xác minh; video có dấu "Đã đi thật"; nội dung hợp tác thì không nhận thưởng chương trình (đã được nhà tài trợ trả).
- **Mở khóa chậm (vesting)**: thưởng ở trạng thái *chờ* cho đến khi chuyến của người đi theo hoàn thành và qua hạn hủy. Phát hiện gian lận thì hủy phần chờ.
- **Trần**: giới hạn theo tháng cho mỗi người và tổng ngân sách chương trình theo % doanh thu từ các lượt đặt được ghi nhận.
- **Không phụ thuộc cảm xúc**: video chê thật vẫn được thưởng nếu hữu ích. Có người đọc chê mà vẫn đặt, vì họ biết trước mình sẽ gặp gì.
- **Minh bạch**: người đăng thấy bảng thu nhập (bao nhiêu người đi theo, thưởng đang chờ hay đã mở). Người xem thấy dòng *"Người đăng nhận điểm thưởng khi bạn đặt theo video này"* để không bị "dắt mũi" ngầm.

### 6.3. Hạng "Người dẫn đường" (học từ Trip.Pals, Local Guides)
| Hạng | Điều kiện (gợi ý) | Quyền lợi |
|---|---|---|
| Lữ khách | Có ≥ 1 bài "Đã đi thật" | Được thưởng cơ bản |
| Người dẫn đường | ≥ 10 lượt đi theo hoàn thành, uy tín cao | Hệ số thưởng cao hơn, huy hiệu |
| Đại sứ | Top theo độ hữu ích hằng quý, không vi phạm | Mời trải nghiệm (bắt buộc gắn nhãn "Hợp tác"), sự kiện |

### 6.4. Rủi ro của chính cơ chế thưởng
| Rủi ro | Giảm thiểu |
|---|---|
| Biến thành "kênh quảng cáo trá hình" | Hiện dòng thông tin về thưởng; nội dung hợp tác không nhận thưởng chương trình |
| Farm thưởng | V9 (mục 5.3), mở khóa chậm, trần |
| Người đăng chỉ khen để được đặt nhiều | Một phần thưởng dựa trên điểm "khớp thực tế" *sau chuyến* của người đi theo, nên khen sai sự thật sẽ bị người đi theo chấm thấp |
| Đội không đủ thời gian làm trong 4 tuần | Bản dự thi chỉ làm **ghi nhận lượt đi theo + bảng thu nhập + điểm thưởng giả lập**; phần phát hiện farm ở mức luật đơn giản |

---

## 7. Giải pháp mới đề xuất (điểm nhấn riêng)

| # | Ý tưởng | Mô tả | Vì sao đáng làm |
|---|---|---|---|
| 1 | **Video neo theo giây** | Khi đăng, người đăng kéo thẻ chỗ (lấy từ đơn của họ) vào đúng đoạn video; người xem thấy thẻ hiện lên đúng lúc | Giải bài toán "video này là chỗ nào" **tại nguồn**, không phải đoán |
| 2 | **"Đi y chang" tính từ nơi tôi ở** | Lộ trình lưu theo ngày tương đối; chặng đi được thay bằng chặng từ thành phố của người xem; các mục khác dời theo ngày mới | Đúng kịch bản gốc (Sài Gòn → điểm đến) mà TikTok GO không làm |
| 3 | **Huy hiệu "Quay tại chỗ"** | Video quay bằng camera trong app, trong khoảng ngày lưu trú, ở gần vị trí của chỗ (có đồng ý chia sẻ vị trí) | Bằng chứng mạnh chống reup và dàn dựng |
| 4 | **Đối chiếu thực tế** | Trên trang chỗ: ảnh chính thức đặt cạnh ảnh của nhiều người đã đi | Giúp persona Hà; trị bài "quá đẹp" |
| 5 | **Ghi chú cộng đồng từ người đã đi** | Ghi chú bổ sung, chỉ hiện khi đủ người đã đi đánh giá là hữu ích | Giảm tải kiểm duyệt; tăng niềm tin |
| 6 | **Bộ sưu tập theo chỗ** | Gom video cùng chỗ, chọn video đại diện theo độ hữu ích | Tránh lặp, chống spam |
| 7 | **"X người đã đi theo lộ trình này"** | Bằng chứng xã hội **thật** (đếm lượt đi theo hoàn thành) | Thay cho khan hiếm giả |
| 8 | **Video theo mùa** | Ghi mùa quay; gợi ý mùa đẹp; cảnh báo "video quay mùa khác thời điểm bạn chọn" | Trung thực; tránh thất vọng |
| 9 | **Thưởng theo người đi theo** | Mục 6 | Biến động cơ của creator thành chất lượng thay vì view |

---

## 8. Hai phiên bản phạm vi

| | **Bản dự thi** (bám đề bài, 4 tuần) | **Bản tầm nhìn** (sau dự án) |
|---|---|---|
| Nguồn hàng | Chỉ dịch vụ Vinpearl (khách sạn, VinWonders, golf, vé máy bay, combo) | Mở cho nhà cung cấp ngoài: homestay (ví dụ Tà Xùa), quán, tour địa phương, nhà xe, qua extranet hoặc đối tác |
| Ví dụ kịch bản | Ở Sài Gòn, xem video Vinpearl Phú Quốc + VinWonders → "Đi y chang" | Ở Sài Gòn, xem video homestay săn mây Tà Xùa → vé máy bay + xe + homestay |
| Người đăng | Chỉ người đã đặt và đã đi (bắt buộc) | Như trên + tài khoản thương hiệu đăng nội dung gắn nhãn "Hợp tác" |
| Thưởng | Điểm/voucher Vinpearl giả lập, theo lượt đi theo hoàn thành (**cần người hướng dẫn đồng ý**) | Voucher + tiền mặt, có xử lý thuế; chương trình đại sứ |
| Chống seeding | Proof-of-stay, nhãn hợp tác, trust score, xu hướng chống đẩy ảo, sàng lọc lừa đảo, kiểm duyệt có AI | Thêm vân tay video, "Quay tại chỗ", ghi chú cộng đồng, phát hiện tài trợ ẩn nâng cao |
| Điểm không đặt được (quán cà phê, điểm ngắm cảnh) | Chỉ là "điểm gợi ý ghé thăm" trong lộ trình, không có nút đặt (cần hỏi) | Có thể đặt bàn, vé |

---

## 9. Câu hỏi cần hỏi người hướng dẫn (theo mẫu của đề bài)

1. *"Đội muốn làm app theo hướng video-first (bảng tin cuộn dọc như TikTok) vì người trẻ tìm cảm hứng qua video ngắn, và A2 đã cho phép video. Em thấy hai cách: (A) video-first, ảnh và chữ là phụ; (B) bài viết dạng thẻ có ảnh, video là phụ. Em nghiêng về A nhưng sẽ tự hạ chất lượng trên mạng yếu và máy yếu. Anh/chị thấy sao?"*
2. *"Đội muốn có chương trình thưởng điểm/voucher cho người đăng khi có người **đặt và đi** theo lộ trình của họ (không thưởng theo view để tránh seeding). Voucher là giả lập nhưng có thật trong hệ thống. Việc này có nằm trong phạm vi được phép không, hay nên để thành đề xuất ở mốc 4?"*
3. *"Lộ trình của người đăng có thể có những điểm không đặt được (quán cà phê, điểm ngắm hoàng hôn). Em đề xuất cho phép thêm chúng như 'điểm gợi ý ghé thăm' không có nút đặt. Như vậy có vi phạm 'không đặt dịch vụ ngoài Vinpearl' không ạ?"*
4. *"Kịch bản người xem ở thành phố khác với người đăng: em đề xuất cho chọn điểm xuất phát và thay chặng bay tương ứng nếu dữ liệu có. Dữ liệu vé máy bay có đủ chặng từ các thành phố lớn không ạ?"*
5. *"Huy hiệu 'Quay tại chỗ' cần dùng vị trí GPS khi quay (có xin phép). Việc thu thập vị trí như vậy có chấp nhận được trong dự án không?"*

---

## 10. Nguồn tham khảo

- TikTok GO: [Tubefilter – TikTok bán phòng khách sạn và trả tiền cho creator](https://www.tubefilter.com/?p=187752) · [Skift – TikTok biến video du lịch thành chỗ đặt được](https://skift.com/2026/05/12/tiktok-go-travel-booking-social-media/) · [AltexSoft – TikTok Go ở Mỹ](https://www.altexsoft.com/travel-industry-news/tiktok-go-brings-bookable-hotels--tours-to-us-travel-feed/) · [Engadget – TikTok thêm đặt chỗ du lịch trong app](https://www.engadget.com/2170275/tiktok-adds-in-app-travel-booking-services/?rand=26687) · [TechNode – TikTok GO ở Indonesia, Mỹ, Nhật](https://technode.com/2026/05/14/tiktok-launches-tiktok-go-lifestyle-services-brand-in-indonesia-us-and-japan/) · [Hotel-Online – TikTok Go và chương trình creator](https://www.hotel-online.com/index.php/news/tiktok-go-just-did-what-every-creator-program-has-been-building-toward)
- Trip.com: [Trip Community (PR Newswire, 12/2025)](https://tools.prnewswire.com/en-us/live/20813/release/20251218EN49637) · [Netinfluencer – hệ sinh thái creator của Trip.com](https://www.netinfluencer.com/online-travel-agency-trip-com-launches-creator-ecosystem-with-12k-influencers-14m-incentive-fund/) · [Trip.com Tripblazers](https://uk.trip.com/blog/tripblazers) · [Trip.com Creator Fund](https://us.trip.com/blog/creatorfund)
- RedNote: [Skift – điều thương hiệu du lịch cần biết về RedNote](https://skift.com/?p=729229) · [SCMP – RedNote và du lịch Trung Quốc](https://www.scmp.com/lifestyle/travel-leisure/article/3360458/indispensable-me-how-rednote-has-taken-over-chinas-travel-scene) · [Smart Order – mini-program đặt phòng trên RedNote](https://www.smartorder.ai/resources/support/authorize-and-publish-red-mini-program/)
- Startup video du lịch: [TechCrunch – Travly](https://techcrunch.com/2024/08/25/travly-social-discovery-booking-platform-travel-influencers) · [ReelStay (App Store)](https://apps.apple.com/app/id6751936146) · [Roomer (App Store)](https://apps.apple.com/il/app/roomer-travel-save-earn/id877222676) · [Tripbtoz (App Store)](https://apps.apple.com/app/id1206972376) · [Vestbee – Unravel gọi vốn 7 triệu USD](https://vestbee.com/insights/articles/unravel-raises-7-m) · [Expedia – nền tảng streaming mua được](https://connectingtravel.com/news/expedia-group-launches-shoppable-streaming-travel-platform)
- Luật Quảng cáo 2025: [LuatVietnam – 4 quy định mới với KOL từ 01/01/2026](https://luatvietnam.vn/linh-vuc-khac/kols-quang-cao-san-pham-can-luu-y-04-quy-dinh-moi-tu-01-01-2026-883-106335-article.html) · [Báo Pháp luật – Từ "đỉnh sóng KOL" đến Luật Quảng cáo sửa đổi](https://baophapluat.vn/tu-dinh-song-kol-den-luat-quang-cao-sua-doi.html) · [Advertising Vietnam – Luật Quảng cáo áp dụng từ 2026 với influencer marketing](https://advertisingvietnam.com/article/luat-quang-cao-ap-dung-tu-2026-doi-voi-influencer-marketing-p27549)

> Lưu ý: một số thông tin về sản phẩm của các công ty (thời điểm ra mắt, phạm vi quốc gia, cơ chế thưởng) lấy từ báo chí và trang giới thiệu, có chỗ các nguồn không thống nhất về thời gian. Cần kiểm tra lại trang chính thức trước khi trích dẫn trong buổi trình bày.
