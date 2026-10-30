# OTA Research – Vinpearl cho người trẻ

Tài liệu nghiên cứu và đề xuất triển khai cho một **nền tảng du lịch video ngắn "xem là đi được"** (Android app, Web OTA, Trợ lý gợi ý, Trang quản trị).

> **Bối cảnh**: đây là **đề tài mở, nền tảng của riêng đội**, không làm cho Vinpearl hay tập đoàn nào. Tài liệu 02 (đề bài Vinpearl) được giữ nguyên văn và dùng làm **tham chiếu yêu cầu** (persona, tiêu chí nghiệm thu, cách đo). Các ràng buộc riêng của Vinpearl như "chỉ bán dịch vụ Vinpearl" không còn bắt buộc. Tài liệu 03–08 viết trước khi làm rõ điều này nên còn nhắc Vinpearl; tài liệu 09 và bản mô phỏng đã theo bối cảnh mới.

| # | Tài liệu | Nội dung |
|---|---|---|
| 01 | [Kiến thức nền ngành OTA](docs/01-kien-thuc-nganh-OTA.md) | Bản tóm gọn: khái niệm, hệ sinh thái, mô hình kinh doanh, ngành hàng, vận hành, hành trình khách, đối thủ, pháp lý, KPI, xu hướng, thuật ngữ |
| 02 | [Đề bài của ban tổ chức](docs/02-de-bai-vinpearl-cho-nguoi-tre.md) | **Giữ nguyên văn**. Nguồn sự thật cao nhất, bắt buộc tuân theo |
| 03 | [Ý tưởng triển khai](docs/03-y-tuong-trien-khai.md) | Kết hợp 01 + 02: hiểu bài toán, áp dụng tri thức OTA, đề xuất cho câu hỏi mở, giải pháp & kiến trúc, mô hình dữ liệu, an toàn, cách đo, phần mở rộng, kế hoạch 4 mốc, giả định & câu hỏi, checklist demo |
| 04 | [Định nghĩa ý tưởng: TikTok cho du lịch](docs/04-dinh-nghia-y-tuong-video-du-lich.md) | Định nghĩa lại ý tưởng video du lịch "xem là đặt được", đối chiếu đề bài, khảo sát thị trường (TikTok GO, Trip.com, Travly…), tính năng nên học, phân tích sâu seeding bẩn, cơ chế thưởng theo người đi theo thật |
| 05 | [Mindmap & roadmap](docs/05-mindmap-y-tuong.md) | Sơ đồ tư duy toàn bộ ý tưởng, cách triển khai, danh mục tính năng toàn platform (P0/P1/P2/V), roadmap theo mốc và sau dự án |
| 06 | [Luồng use case](docs/06-luong-use-case.md) | Luồng hoàn chỉnh người đăng (thông tin cần nhập, hệ thống dựng lộ trình, AI đọc video/caption để làm gì) và người xem (bảng tin → Đi y chang → xác nhận → thanh toán → sau khi đặt), xử lý giá theo thời điểm, ngoại lệ |
| 07 | [Nghiên cứu Mindtrip & thiết kế Đi y chang](docs/07-nghien-cuu-mindtrip-va-thiet-ke-di-y-chang.md) | Tính năng Mindtrip (và Wanderlog), bài học; trình soạn chuyến đi có cấu trúc (đi gì, ở đâu, ngày 1/2/3 sáng-chiều-tối); trình bày kiểu TikTok có chương; màn Đi y chang 3 tab Lịch trình/Bản đồ/Chi phí; tính năng thêm; mô hình dữ liệu |
| 08 | [Tính năng mở rộng](docs/08-tinh-nang-mo-rong.md) | Rà soát khoảng trống so với đề bài và persona; 30+ tính năng mở rộng cho người xem, người đăng, quản trị; thứ tự ưu tiên cho phần thi; câu hỏi cho người hướng dẫn |
| 09 | [Xác minh đã đi thật](docs/09-xac-minh-da-di-that.md) | Nền tảng độc lập không sở hữu dữ liệu nhà cung cấp: cộng bằng chứng "đã đặt" (đặt trong app, email có chữ ký DKIM, thẻ lên máy bay, đối tác xác nhận) và "đã có mặt" (QR động tại quầy, vị trí đúng ngày, quay trong app); 3 mức Đã đi thật / Đã đặt chỗ / Có mặt tại chỗ; chống gian lận; riêng tư |
| 10 | [Diagram workflow](docs/10-diagram-workflow.md) | 10 sơ đồ Mermaid: toàn cảnh vòng lặp, xác minh đã đi, người đăng, người xem và Đi y chang, giữ chỗ & thanh toán (kèm trình tự hệ thống), kiểm duyệt & chống seeding, xu hướng & thưởng, nguồn hàng, kiến trúc. Bản trình bày: [demo/workflow-diagram.html](demo/workflow-diagram.html) |
| 11 | [Tóm tắt & đánh giá](docs/11-tom-tat-va-danh-gia.md) | Ý tưởng trong một câu, 5 thành phần cốt lõi, so sánh với TikTok GO, Trip.com, OTA, Mindtrip và các startup; 7 lý do sự kết hợp này chưa xuất hiện; đánh giá mức độ mới, khả thi, điểm mạnh/yếu; thông điệp trình bày |
| 12 | [Ý tưởng SWE mới](docs/12-y-tuong-swe-moi.md) | 14 ý tưởng du lịch – khách sạn có chiều sâu kỹ thuật (tìm ngược theo ngân sách, trợ lý canh giá có rào chắn, đúng giá, tự xử lý sự cố, đối chiếu ảnh thực tế, micro-stay, định giá theo xu hướng cho homestay, check-in không chạm, hộ chiếu du lịch kiểm chứng được…), 3 ý tưởng nên ưu tiên, cách ghép vào nền tảng |

**Mô phỏng tương tác**: [demo/mo-phong-di-y-chang.html](demo/mo-phong-di-y-chang.html). Mở bằng trình duyệt, không cần cài gì. Giao diện tối kiểu TikTok, minh họa **hai mô hình nguồn hàng** trong màn "Đi y chang": lộ trình **Tà Xùa** dùng hàng **nền tảng tự cung cấp** (homestay, nhà xe đối tác, có màn quản lý cho chủ nhà ở Tôi → Chế độ đối tác; chọn trả trước hoặc trả tại homestay); lộ trình **Phú Quốc** dùng **API đối tác Traveloka** (mô phỏng tìm → kiểm tra lại → đặt, có nút xem luồng API) và **link affiliate** cho vé máy bay. Ngoài ra có tab Chuyến đi với "Thêm chuyến đã đi ở nơi khác" và nút + để đăng bài.

**Thứ tự đọc**: 11 (tóm tắt) → 05 (nhìn tổng quan) → 02 → 01 → 03 → 04 → 06 → 07 → 08 → 09 → 10.

**Lưu ý**: số liệu thị trường mang tính tham khảo; phần pháp lý mang tính định hướng. Các tài liệu nghiên cứu chi tiết trước đây (01–16 cũ) vẫn còn trong lịch sử git nếu cần tra lại.
