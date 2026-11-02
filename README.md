# Tripflix – Vinpearl cho người trẻ (Dự án VSF)

Nền tảng du lịch lấy nội dung làm trung tâm: **người đã đi** đăng video và lộ trình thật của chuyến đi; người khác xem trên bảng tin kiểu TikTok và bấm **"Đặt y chang"** để đặt cả lộ trình (chỗ ở, vé vui chơi, vé máy bay, combo) bằng một chạm. Bốn sản phẩm: app Android, web OTA, trợ lý gợi ý, trang quản trị.

| # | Tài liệu | Nội dung |
|---|---|---|
| 01 | [Kiến thức nền ngành OTA](docs/01-kien-thuc-nganh-OTA.md) | Lý thuyết ngành |
| 02 | [Đề bài](docs/02-de-bai-vinpearl-cho-nguoi-tre.md) | Giữ nguyên văn |
| 03 | [Tripflix – ý tưởng triển khai (bản đã chốt)](docs/03-y-tuong-trien-khai.md) | Quyết định đã chốt, vai trò và phân quyền, tính năng A1–A16, luồng Đặt y chang, đăng bài và dấu "đã đi thật", quy tắc thanh toán, đề xuất cho câu hỏi mở, module và phân công, kiến trúc, dữ liệu, sơ đồ, cách đo, lộ trình 4 tuần, rủi ro, câu hỏi mentor |

**Tài liệu làm việc nhóm**:
- [team/01-phan-cong-nghien-cuu-module.md](team/01-phan-cong-nghien-cuu-module.md): phân công 10 module cho 7 người, khung báo cáo chung, ranh giới module, trạng thái các điểm cần chốt.
- [reports/Kiến trúc module Tripflix.md](<reports/Kiến trúc module Tripflix.md>): nghiên cứu kiến trúc 8 module theo ảnh 3 tầng (Access, Notification, Administration, AI, Booking, Social, Payment, Search), mapping với 10 module nhỏ, quy ước API, danh mục sự kiện, luồng liên module, và 8 bản báo cáo theo khung 8 mục. Ghi chú nghiên cứu gốc ở `research_notes/Kiến trúc module Tripflix/`.
- [reports/Module AI Tripflix chuyên sâu.md](<reports/Module AI Tripflix chuyên sâu.md>): nghiên cứu chuyên sâu module AI (Bảo): AI đặt ở đâu và phần nào không cần LLM, bài học từ TikTok/Meta/OTA, định nghĩa trợ lý như một product (dữ liệu thu, công cụ, đầu ra), chi phí/độ trễ/chất lượng/tradeoff, guardrail và bộ đánh giá, API, sự kiện và móc nối với các module khác, kế hoạch 4 tuần. Ghi chú gốc ở `research_notes/Module AI Tripflix chuyên sâu/`.
- [reports/Thiết kế database AI Agent Tripflix.md](<reports/Thiết kế database AI Agent Tripflix.md>): cấu trúc database (PostgreSQL + PostGIS + pgvector, 110 bảng, 920 trường, DDL đã chạy thử, có từ điển dữ liệu liệt kê từng trường) để AI Agent đọc video, lộ trình, khách sạn, bản đồ, đánh giá, vé bay/tàu/xe; lên lịch trình, tìm video phù hợp, đổi phương tiện, đổi khách sạn gần đó, so sánh giá; lớp đọc an toàn cho agent, bộ 26 công cụ, kịch bản mẫu, MVP và mở rộng.
- [reports/Chatbot agent tối giản Tripflix.md](<reports/Chatbot agent tối giản Tripflix.md>): bản tối giản cho demo 4 tuần: nói đi đâu, mấy người, bao nhiêu tiền → gợi ý lộ trình lấy từ lộ trình người đã đi đăng (không quét video) kèm video; đổi từng mục có bảng so sánh; 4 công cụ, ~15 bảng có sẵn, nút bấm thay câu nói, chuyển sang Đặt y chang để đặt.

**Trình bày**:
- [demo/workflow-diagram.html](demo/workflow-diagram.html): sơ đồ workflow Tripflix (cần mạng để tải thư viện vẽ).
- [demo/mo-phong-di-ngay.html](demo/mo-phong-di-ngay.html): bản mô phỏng **thử nghiệm ý tưởng "Đi ngay"** trước khi chốt hướng. Không phải giao diện Tripflix đã chốt; giữ lại làm tham khảo cho hướng phát triển (03, mục 16).

**Thứ tự đọc**: 03 → 02 → team/01 → 01.

Các tài liệu nghiên cứu trước đây vẫn còn trong lịch sử git.
