# Tripflix – Vinpearl cho người trẻ (Dự án VSF)

Ứng dụng video du lịch dọc kiểu TikTok: ai cũng đăng được video kèm lộ trình; người xem lướt và bấm **"Đi tương tự"** để đặt trọn gói vé và phòng qua "Traveloka lõi" (giả định có sẵn, demo dùng mock). Bài có mục đặt qua dịch vụ của app thì có huy hiệu **Verified**. Mở rộng AI: tóm tắt bình luận "Người đi rồi nói gì" và chatbot đơn giản. Scope hiện hành ở [docs/04](docs/04-scope-tinh-gon.md).

| # | Tài liệu | Nội dung |
|---|---|---|
| 01 | [Kiến thức nền ngành OTA](docs/01-kien-thuc-nganh-OTA.md) | Lý thuyết ngành |
| 02 | [Đề bài](docs/02-de-bai-vinpearl-cho-nguoi-tre.md) | Giữ nguyên văn |
| 03 | [Tripflix – ý tưởng triển khai (bản đã chốt)](docs/03-y-tuong-trien-khai.md) | Quyết định đã chốt, vai trò và phân quyền, tính năng A1–A16, luồng Đặt y chang, đăng bài và dấu "đã đi thật", quy tắc thanh toán, đề xuất cho câu hỏi mở, module và phân công, kiến trúc, dữ liệu, sơ đồ, cách đo, lộ trình 4 tuần, rủi ro, câu hỏi mentor |
| 04 | [**Scope tinh gọn (bản mới nhất)**](docs/04-scope-tinh-gon.md) | Lõi là feed video dọc kiểu TikTok; đăng video + lộ trình tự do, huy hiệu Verified khi dùng dịch vụ của app; "Đi tương tự" đặt trọn gói qua Traveloka lõi (mock); tóm tắt bình luận "Người đi rồi nói gì"; chatbot đơn giản; phân quyền JSON + if-else; dữ liệu mock; đối chiếu với đề bài |

**Tài liệu làm việc nhóm**:
- [team/01-phan-cong-nghien-cuu-module.md](team/01-phan-cong-nghien-cuu-module.md): phân công 10 module cho 7 người, khung báo cáo chung, ranh giới module, trạng thái các điểm cần chốt.
- [team/02-viec-cua-bao-phan-AI.md](team/02-viec-cua-bao-phan-AI.md): **danh sách việc của Bảo (AI)**: trợ lý tìm video kèm lộ trình, đổi và so sánh từng phần, tóm tắt bình luận, nền tảng dùng chung; những gì phải giao, phụ thuộc với từng người, câu hỏi mở, checklist 4 tuần.
- [reports/Kiến trúc module Tripflix.md](<reports/Kiến trúc module Tripflix.md>): nghiên cứu kiến trúc 8 module theo ảnh 3 tầng (Access, Notification, Administration, AI, Booking, Social, Payment, Search), mapping với 10 module nhỏ, quy ước API, danh mục sự kiện, luồng liên module, và 8 bản báo cáo theo khung 8 mục. Ghi chú nghiên cứu gốc ở `research_notes/Kiến trúc module Tripflix/`.
- [reports/Module AI Tripflix chuyên sâu.md](<reports/Module AI Tripflix chuyên sâu.md>): nghiên cứu chuyên sâu module AI (Bảo): AI đặt ở đâu và phần nào không cần LLM, bài học từ TikTok/Meta/OTA, định nghĩa trợ lý như một product (dữ liệu thu, công cụ, đầu ra), chi phí/độ trễ/chất lượng/tradeoff, guardrail và bộ đánh giá, API, sự kiện và móc nối với các module khác, kế hoạch 4 tuần. Ghi chú gốc ở `research_notes/Module AI Tripflix chuyên sâu/`.
- [reports/Thiết kế database AI Agent Tripflix.md](<reports/Thiết kế database AI Agent Tripflix.md>): cấu trúc database (PostgreSQL + PostGIS + pgvector, 110 bảng, 920 trường, DDL đã chạy thử, có từ điển dữ liệu liệt kê từng trường) để AI Agent đọc video, lộ trình, khách sạn, bản đồ, đánh giá, vé bay/tàu/xe; lên lịch trình, tìm video phù hợp, đổi phương tiện, đổi khách sạn gần đó, so sánh giá; lớp đọc an toàn cho agent, bộ 26 công cụ, kịch bản mẫu, MVP và mở rộng.
- [reports/Chatbot agent tối giản Tripflix.md](<reports/Chatbot agent tối giản Tripflix.md>): bản tối giản cho demo 4 tuần: nói đi đâu, mấy người, bao nhiêu tiền → gợi ý lộ trình lấy từ lộ trình người đã đi đăng (không quét video) kèm video; đổi từng mục có bảng so sánh; 4 công cụ, ~15 bảng có sẵn, nút bấm thay câu nói, chuyển sang Đặt y chang để đặt.
- [reports/Thiết kế chatbot agent và tóm tắt bình luận.md](<reports/Thiết kế chatbot agent và tóm tắt bình luận.md>): **tài liệu thiết kế để triển khai** F7 chatbot agent và F6 tóm tắt bình luận: kiến trúc, cây module, bảng dữ liệu (DDL), luồng xử lý, 4 công cụ, prompt, endpoint kèm JSON, danh sách hàm, yêu cầu chức năng và phi chức năng, bộ thử, kế hoạch 4 tuần, 10 sơ đồ.
- [reports/Chatbot agent tóm tắt bình luận.md](<reports/Chatbot agent tóm tắt bình luận.md>): **nghiên cứu AI engineering mới nhất** cho chatbot agent (F7) và tóm tắt bình luận "Người đi rồi nói gì" (F6): các nền tảng lớn làm gì và Tripflix lấy gì, module, endpoint kèm JSON, hàm, yêu cầu chức năng có nghiệm thu, yêu cầu phi chức năng có số, bộ thử và cổng CI, kế hoạch 4 tuần. Ghi chú gốc ở `research_notes/Chatbot agent tóm tắt bình luận/`.

**Trình bày**:
- [demo/tripflix-prototype.html](demo/tripflix-prototype.html): **prototype tương tác theo scope tinh gọn (docs/04)**: feed video dọc, lộ trình + Verified, tóm tắt bình luận, Đi tương tự tới thanh toán giả lập, đăng video, trợ lý, phân quyền accounts.json. Mở trực tiếp bằng trình duyệt.

**Thứ tự đọc**: 04 → 02 → 03 (tham khảo chi tiết) → team/01 → 01.

Các tài liệu nghiên cứu trước đây vẫn còn trong lịch sử git.
