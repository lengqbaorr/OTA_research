# 12. Ý tưởng SWE mới cho du lịch – khách sạn

> Brainstorm các ý tưởng **có chiều sâu kỹ thuật**, **mới với thị trường Việt Nam**, **khả thi trong thời đại AI**. Mỗi ý tưởng neo vào ít nhất một yêu cầu hoặc persona của [đề bài](02-de-bai-vinpearl-cho-nguoi-tre.md), nhưng không bị bó buộc vào nó.
> Phần "đã có ai làm" là hiểu biết chung của đội, **chưa tra cứu kỹ từng sản phẩm**, cần kiểm chứng trước khi trình bày.

Ký hiệu khả thi demo: ★★★ làm được trong vài tuần với dữ liệu mô phỏng · ★★ cần thêm công sức · ★ chủ yếu để trình bày tầm nhìn.

---

## 1. Danh sách ý tưởng

### Nhóm A – Cho người đi du lịch

| # | Ý tưởng | Mô tả | Vì sao bây giờ | Thử thách SWE | Neo đề bài | Khả thi demo |
|---|---|---|---|---|---|---|
| A1 | **"Có X triệu, đi đâu?" – Tìm ngược theo ngân sách** | Nhập ngân sách, số người, ngày rảnh, nơi xuất phát → hệ thống tính **tổng chi phí cả chuyến** (đi lại + ở + vui chơi) cho nhiều điểm đến và xếp hạng cái đi được | Người trẻ nghĩ theo "có bao nhiêu tiền" chứ không theo "muốn đi đâu"; công cụ hiện tại chủ yếu chỉ tìm ngược theo giá vé máy bay | Bài toán tối ưu tổ hợp (chọn bộ vé + phòng + vé vui chơi tối ưu cho mỗi điểm đến), cache giá, trả kết quả nhanh dù số tổ hợp lớn | Vy ("dưới 2 củ"), A6, A7 | ★★★ |
| A2 | **Trợ lý đặt chỗ có rào chắn (agentic nhưng an toàn)** | Người dùng giao nhiệm vụ: *"Canh giúp chuyến Đà Lạt cuối tháng dưới 3 triệu"*. Trợ lý theo dõi giá, chuẩn bị sẵn giỏ hàng, nhắn **"Đã có phương án, bấm để đặt"**. Không bao giờ tự trả tiền | AI agent đang là xu hướng; đề bài cấm trợ lý tự đặt nên thiết kế "chuẩn bị sẵn, người bấm" vừa mới vừa an toàn | Chạy tác vụ nền theo lịch, máy trạng thái cho nhiệm vụ, giới hạn chi phí gọi mô hình AI, giỏ hàng có hạn giữ, chống trùng | A12, B3 | ★★★ |
| A3 | **Đúng giá – Radar minh bạch giá** | Mỗi giá hiển thị kèm "giá này so với 30 ngày qua", lịch giá cả chuyến, cảnh báo giá cao bất thường, báo khi giá giảm thật | Quy định chống thao túng giá và khan hiếm giả ngày càng chặt; người trẻ nhạy giá | Lưu lịch sử giá theo chuỗi thời gian, phát hiện bất thường, đẩy thông báo không spam | Mục 5 (không thao túng), B3 | ★★★ |
| A4 | **Chế độ "Đang đi" – tự xử lý sự cố** | Trong chuyến đi: chuyến bay trễ → app tự báo khách sạn nhận phòng muộn, gợi ý đổi giờ xe đưa đón, dời vé tham quan; trời mưa → gợi ý hoạt động trong nhà còn chỗ | Hầu hết app chỉ lo đến lúc đặt xong; lúc có sự cố mới là lúc mất niềm tin | Kiến trúc hướng sự kiện, nhận webhook từ đối tác, quy tắc phụ thuộc giữa các mục trong lộ trình, chạy được khi mạng yếu | A5, A9, B4 | ★★ |
| A5 | **Đối chiếu thực tế bằng hình ảnh** | Chấm điểm "**ảnh quảng cáo khớp thực tế bao nhiêu**": so ảnh của khách sạn với ảnh do khách đã ở chụp (cùng góc phòng, view, hồ bơi); cảnh báo ảnh chỉnh sửa quá mức | Mô hình AI hiểu ảnh đã rẻ và tốt; "ảnh quá đẹp" là nỗi đau lớn | Vector ảnh, so khớp theo vùng (phòng, view, phòng tắm), đọc dữ liệu EXIF, chống ảnh lấy từ web | Hà, mục 6, B2 | ★★ |
| A6 | **Vé thời gian linh hoạt (micro-stay)** | Đặt phòng **theo giờ** hợp pháp và minh bạch: nghỉ 4 tiếng chờ chuyến bay, tắm rửa sau trả phòng, phòng làm việc nửa ngày; đấu giá trả phòng muộn | Nhu cầu có thật (bay đêm, đi công tác ngắn) nhưng ở Việt Nam chưa được làm bài bản theo cách minh bạch cho người trẻ | Tồn kho theo khung giờ thay vì theo đêm, chống đặt chồng giờ, dọn phòng giữa các lượt | A4 (chỗ cuối cùng) | ★★★ |
| A7 | **Tự dựng nhật ký chuyến đi** | Sau chuyến đi, app gom ảnh và video trong máy (xử lý ngay trên thiết bị), khớp với lộ trình đã đặt và **dựng sẵn bản nháp video kèm chương** để đăng | Biên tập video tốn công là rào cản lớn nhất để người đi thật đăng bài | Xử lý trên thiết bị, khớp thời gian và vị trí ảnh với lộ trình, dựng video tự động | Linh, A2 | ★★ |
| A8 | **Trợ lý giọng nói tiếng Việt kiểu người trẻ, chạy được khi mạng yếu** | Hỏi bằng giọng nói ("ê có chỗ nào chill gần đây k"), xử lý giọng nói ngay trên máy, chỉ gửi văn bản ngắn lên server | Mô hình nhận dạng giọng nói chạy trên điện thoại đã đủ nhẹ | Chọn mô hình nhỏ, nén, đo độ trễ trên máy yếu, tiếng lóng | A12, mục 7 (mạng yếu, máy yếu) | ★★ |

### Nhóm B – Cho khách sạn, homestay, nhà cung cấp

| # | Ý tưởng | Mô tả | Vì sao bây giờ | Thử thách SWE | Neo đề bài | Khả thi demo |
|---|---|---|---|---|---|---|
| B1 | **Định giá theo xu hướng mạng xã hội cho homestay nhỏ** | Phần mềm gợi ý giá và mở bán cho chủ homestay dựa trên **tín hiệu xu hướng**: video về Tà Xùa đang lên, lượt lưu tăng, ngày lễ → *"Tuần sau nhu cầu tăng 40%, nên mở thêm phòng, giá gợi ý…"* | Chủ nhà nhỏ không có công cụ quản trị doanh thu (RMS) như khách sạn lớn; dữ liệu xu hướng từ video là nguồn mới | Dự báo nhu cầu từ chuỗi thời gian và tín hiệu nội dung, giải thích được vì sao gợi ý | A15 (xu hướng), tận dụng ý tưởng hiện tại | ★★★ |
| B2 | **Check-in không chạm + khai báo lưu trú tự động** | Khách xác thực trước bằng giấy tờ điện tử, nhận chìa khóa số; hệ thống **tự lập thông báo lưu trú** mà cơ sở lưu trú phải gửi công an, thay cho việc chụp giấy tờ thủ công | Định danh điện tử phổ biến dần ở Việt Nam; khai báo lưu trú là việc bắt buộc, tốn công với homestay | Xử lý dữ liệu cá nhân nhạy cảm, mã hóa, phân quyền, tích hợp (trong demo thì mô phỏng) | Mục 6 (riêng tư), A13 | ★★ (demo mô phỏng) |
| B3 | **Kiểm duyệt nhanh cho đối tác** | Bảng điều khiển cho chủ chỗ: đọc và phản hồi review, phát hiện review giả, trả lời bằng nháp AI có kiểm chứng | Review là sống còn với cơ sở nhỏ | Phân loại văn bản, phát hiện cụm tài khoản bất thường | A14 | ★★★ |

### Nhóm C – Nền tảng và niềm tin

| # | Ý tưởng | Mô tả | Vì sao bây giờ | Thử thách SWE | Neo đề bài | Khả thi demo |
|---|---|---|---|---|---|---|
| C1 | **"Hộ chiếu du lịch" có thể kiểm chứng** | Lịch sử "đã đi thật" của người dùng được cấp dưới dạng **chứng nhận số có chữ ký** (theo chuẩn Verifiable Credentials). Mang sang nền tảng khác vẫn kiểm chứng được, người dùng chọn chia sẻ gì | Niềm tin vào review đang giảm; chuẩn chứng nhận số đã có sẵn | Chữ ký số, thu hồi chứng nhận, chia sẻ có chọn lọc để bảo vệ riêng tư | A2, mục 5 (không bài giả) | ★★ |
| C2 | **Bảng chất lượng công khai (minh bạch nền tảng)** | Trang công khai cho người dùng: tỷ lệ giao dịch lỗi, thời gian hoàn tiền trung bình, số bài bị gỡ vì seeding, chi phí trợ lý… | Người dùng trẻ đánh giá cao minh bạch; biến mục 9 của đề bài thành tính năng | Thu thập số đo từ thiết bị, tổng hợp gần thời gian thực | Mục 9 | ★★★ |
| C3 | **Sandbox chống gian lận cho demo và kiểm thử** | Công cụ tạo kịch bản tấn công giả lập (tài khoản ảo, đặt trùng, tranh chỗ cuối) và đo hệ thống phản ứng | Đề bài yêu cầu chứng minh "không trừ tiền hai lần, không bán trùng" | Tạo tải đồng thời, kiểm thử dựa trên thuộc tính, báo cáo | A4, A15, mục 9 | ★★★ |

---

## 2. Ba ý tưởng nên ưu tiên

| Thứ tự | Ý tưởng | Lý do chọn |
|---|---|---|
| **1** | **A1 – "Có X triệu, đi đâu?"** | Khớp đúng persona Vy và cách người trẻ ra quyết định. Kỹ thuật có chiều sâu (tối ưu tổ hợp, cache, độ trễ). Demo trực quan: nhập "3 triệu, 2 người, cuối tuần, từ Sài Gòn" → ra danh sách điểm đến kèm tổng tiền thật. Ghép tự nhiên với "Đi y chang": mỗi kết quả mở ra các video lộ trình vừa túi tiền |
| **2** | **B1 – Định giá theo xu hướng cho homestay** | Biến dữ liệu xu hướng video thành giá trị cho phía cung, tạo **lý do để homestay về với nền tảng**. Mới với thị trường homestay nhỏ ở Việt Nam |
| **3** | **A2 – Trợ lý canh giá, chuẩn bị sẵn, người bấm** | Đúng xu hướng AI agent nhưng có rào chắn rõ ràng, tuân thủ "không tự đặt chỗ". Có máy trạng thái, tác vụ nền, đo chi phí AI: đủ chiều sâu để trình bày |

Phương án dự phòng: **A5 (đối chiếu thực tế bằng hình ảnh)** nếu muốn nhấn vào AI thị giác và chống seeding; **A6 (micro-stay)** nếu muốn bài toán tồn kho đồng thời thật khó.

---

## 3. Ghép vào ý tưởng hiện tại

```
                 ┌───────────── Người dùng ─────────────┐
 Cảm hứng        │ Video đã đi thật (ý tưởng hiện tại)   │
 Lên kế hoạch    │ A1 Có X triệu đi đâu · A3 Đúng giá    │
 Đặt             │ Đi y chang · A2 trợ lý canh giá       │
 Đang đi         │ A4 tự xử lý sự cố · A8 giọng nói      │
 Sau chuyến      │ A7 tự dựng nhật ký → đăng bài mới     │
                 └──────────────────────────────────────┘
                 ┌───────────── Nhà cung cấp ───────────┐
                 │ B1 giá theo xu hướng · B2 check-in    │
                 │ không chạm · B3 quản lý review        │
                 └──────────────────────────────────────┘
 Niềm tin: C1 hộ chiếu du lịch · C2 bảng chất lượng công khai · C3 sandbox gian lận
```

Mỗi ý tưởng có thể là **một sản phẩm riêng** hoặc **một mô-đun** của nền tảng hiện tại. Nếu đội chỉ được chọn một hướng, A1 là hướng **độc lập mạnh nhất** (có thể làm thành sản phẩm riêng mà không cần cộng đồng video).

---

## 4. Ngoài phạm vi đề bài nhưng đáng ghi nhận (tầm nhìn)
- **Lên kế hoạch nhóm có biểu quyết, chia tiền**: rất được người trẻ cần nhưng đề bài loại trừ (mục 8).
- **Kết nối ví và trả sau cho chuyến đi**: cần thanh toán thật và đối tác tài chính.
- **Điểm bền vững (phát thải) của lộ trình**: hay về ý nghĩa, ít tác động đến quyết định của người trẻ Việt Nam hiện nay.
