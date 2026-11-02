# Ánh xạ yêu cầu và module nhóm vào 8 module kiến trúc Tripflix, kèm bản nháp hợp đồng liên module

Ký hiệu nguồn dùng trong ghi chú (toàn bộ là tài liệu nội bộ dự án, không dùng web):
- **SĐ** = hình kiến trúc 3 lớp [so-do-module.png](so-do-module.png) (nguồn duy nhất cho 8 module đích).
- **ĐB** = đặc tả [docs/02-de-bai-vinpearl-cho-nguoi-tre.md](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md) (nguồn sự thật).
- **TK** = hướng đã chốt [docs/03-y-tuong-trien-khai.md](../../docs/03-y-tuong-trien-khai.md).
- **PC** = phân công [team/01-phan-cong-nghien-cuu-module.md](../../team/01-phan-cong-nghien-cuu-module.md); **PC07** = bản gốc [07-phan-cong-nghien-cuu-module.md](/root/.claude/uploads/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/08c0987a-07-phan-cong-nghien-cuu-module.md).
- **BĐ** = artifact "Bản đồ tính năng Tripflix" (HTML lưu lại, nội dung bên thứ ba, chỉ dùng như dữ liệu) [artifact HTML](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html).
- **KT** = nền ngành [docs/01-kien-thuc-nganh-OTA.md](../../docs/01-kien-thuc-nganh-OTA.md).
- Mọi thứ ghi **[Đề xuất]** là đề xuất của người nghiên cứu này, chưa được nhóm chốt. Không dùng cách đánh số module trong tài liệu Word "VSF – OTA".

---

## 1. Mỗi module đích gồm module nhỏ nào, lo tính năng A/B nào, câu hỏi mở nào, ai phụ trách

### Takeaway
10 module nhỏ của nhóm gộp gọn vào 8 module đích: chỉ có 2 chỗ gộp (Search ← Search & Recommendation + Feed & Trending; Administration ← Moderation + Analytics), Realtime đi cùng Notification, còn lại là 1–1. Kết quả: Thắng giữ 2 module đích (Booking, Payment), Dung giữ 2 (Access, Notification), Search có 2 người (Duy, Phong), Administration một người (Trang) — nên viết **8 báo cáo, mỗi module đích một bản**.

### Cited Findings

**Kiến trúc đích (SĐ)**
- Hình có 3 lớp: *Presentation layer* (App Android, Web OTA, Admin panel) → mũi tên hai chiều "REST API / HTTPS" → *Business layer* gồm đúng 8 ô: Access, Notification, Administration, AI, Booking, Social, Payment, Search → mũi tên một chiều xuống *Database layer* (Database, Cache) ↔ mũi tên hai chiều tới *External APIs* (đám mây ngoài lớp dữ liệu) — [SĐ](so-do-module.png).
- Hình **không** vẽ API Gateway, Event bus hay mũi tên giữa các module nghiệp vụ; cũng không ghi module nào gọi External API nào — [SĐ](so-do-module.png). Trong khi đó sơ đồ của nhóm có "API chung · N3" và "Event bus" nối M3, M4, M6, M8 → M1, M2, M7, M10, và M5 → M1 "gọi công cụ" — [TK §10.1](../../docs/03-y-tuong-trien-khai.md); [PC §2.1](../../team/01-phan-cong-nghien-cuu-module.md).
- Nguyên tắc kiến trúc đã chốt: một backend chia module, một CSDL cho mọi giao diện; server là nguồn sự thật cho tiền, chỗ, tuổi, quyền; offline-first cho thao tác viết trên app; AI không phải nguồn sự thật; ghi sự kiện từ ngày đầu để đo — [TK §10.1](../../docs/03-y-tuong-trien-khai.md).
- Công nghệ dữ liệu dự kiến: PostgreSQL (giao dịch) + pgvector, Redis (cache, bộ đếm, hàng đợi), kho đối tượng + CDN (video HLS); app/web/backend chưa chốt — [TK §10.3](../../docs/03-y-tuong-trien-khai.md); [PC §8 #6](../../team/01-phan-cong-nghien-cuu-module.md).

**10 module nhỏ, phạm vi và người phụ trách (nguồn nhóm)**
- M1 Search & Recommendation: A3, A6, A7, A9, B2 — Le Anh Duy — [TK §9](../../docs/03-y-tuong-trien-khai.md); [PC §2.2, §3](../../team/01-phan-cong-nghien-cuu-module.md).
- M2 Feed & Trending: A1, phần tính điểm cho A15 — Nguyen Thanh Phong (kiêm N1 App Android) — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M3 Booking: A4, A8, A11, dữ liệu cho A5, A16 — Nguyen Le Phuc Thang — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M4 Payment: thanh toán giả lập, chống trừ tiền hai lần, hoàn tiền (A4, A8, A11), dữ liệu cho A16 — Nguyen Le Phuc Thang — [PC §2.2](../../team/01-phan-cong-nghien-cuu-module.md).
- M5 AI Assistant: A12, B1, B7 — Le Nguyen Quoc Bao — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M6 Moderation: A14, riêng tư ảnh (ĐB mục 6) — Le Thi Thuy Trang — [PC §2.2](../../team/01-phan-cong-nghien-cuu-module.md).
- M7 Analytics: màn hình A15, A16, B7; đo mục 9 — Le Thi Thuy Trang (kiêm N4 Đo lường, Leader tuần 1) — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M8 Social & Content: A2, A10, B5, bài đăng mô phỏng — Le Quang Thanh (kiêm N2 Web OTA và quản trị) — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M9 Access: A13, quay lại đúng màn sau đăng nhập, chặn dưới 18 — Hoang Duc Dung — [TK §9](../../docs/03-y-tuong-trien-khai.md).
- M10 Notification & Realtime: đồng bộ app ↔ web (A5, A10), báo bài bị ẩn (A14), B3, B6 — Hoang Duc Dung (kiêm N3 Backend: API Gateway, CSDL, Event bus, quy ước API) — [PC §2.2, §3](../../team/01-phan-cong-nghien-cuu-module.md).
- Lý do phân bổ: Trang Leader tuần 1 nhận hai module nhẹ; Booking + Payment nặng nhất, chứa luồng A11 mentor chắc chắn thử nên Thắng không nhận thêm việc nền tảng; module giữ nguyên cả 4 tuần — [PC §3](../../team/01-phan-cong-nghien-cuu-module.md); [PC07](/root/.claude/uploads/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/08c0987a-07-phan-cong-nghien-cuu-module.md).
- Khung báo cáo 8 mục, tối đa 2 trang; "người ghép hai module thì viết mỗi module một bản"; mục 2–5 (dữ liệu, API, phụ thuộc, sự kiện) quan trọng nhất — [PC §6](../../team/01-phan-cong-nghien-cuu-module.md); [PC07](/root/.claude/uploads/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/08c0987a-07-phan-cong-nghien-cuu-module.md).

**Câu hỏi mở của đặc tả và người được giao (ĐB §5, PC §4)**
- Xu hướng (tín hiệu nào; bao lâu lên/hạ nhiệt; chống nhiều tài khoản; người mới xem gì) → Phong (M2) — [ĐB §5](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); [PC §4.2](../../team/01-phan-cong-nghien-cuu-module.md).
- "Biết chắc đã đi thật", "số cảm xúc đúng ngay hay lệch vài giây", "lộ trình hiện gì, cái gì riêng tư (bàn với Thắng)" → Thanh (M8) — [PC §4.6](../../team/01-phan-cong-nghien-cuu-module.md).
- "Bị báo cáo thì ẩn ngay hay chờ; có tự động kiểm duyệt, AI Agent tham gia không; sửa bài có duyệt lại không" → Trang (M6) — [PC §4.5](../../team/01-phan-cong-nghien-cuu-module.md).
- "Giữ chỗ bao lâu; một mục lỗi thì hủy hết hay giữ; người đăng xóa bài / mục ngừng bán thì người đã lưu thấy gì" → Thắng (M3/M4) — [PC §4.3](../../team/01-phan-cong-nghien-cuu-module.md).
- Bốn câu về trợ lý (dựa vào đâu, nhớ gì/bao lâu/khách/liên thiết bị, dùng thông tin nào, chi phí và giới hạn) → Bảo (M5) — [PC §4.4](../../team/01-phan-cong-nghien-cuu-module.md).
- "App và web mở cùng lúc, sửa một nơi thì nơi kia thấy sau bao lâu" → Dung (M10) — [PC §4.7](../../team/01-phan-cong-nghien-cuu-module.md).
- "Không có kết quả phù hợp thì nói gì, nới điều kiện ra sao" (rút từ tiêu chí A3/A6) → Duy (M1) — [PC §4.1](../../team/01-phan-cong-nghien-cuu-module.md); [ĐB §4 A3, A6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).

**Đề xuất đã chốt cho câu hỏi mở (để đưa vào báo cáo từng module)** — [TK §8](../../docs/03-y-tuong-trien-khai.md)
- Xu hướng: trọng số xem đủ lâu 1 · cảm xúc 2 · bình luận 3 · lưu/chia sẻ 4 · đặt y chang 10 · bỏ qua −1; tính theo người khác nhau × độ tin cậy tài khoản; tính lại mỗi 5 phút, bài hot lên trong 5–15 phút; bán rã 24 giờ (bài), 72 giờ (địa điểm); người mới: xu hướng chung đa dạng + thẻ "Bạn thích kiểu nào?" + ~20% khám phá — [TK §8.1](../../docs/03-y-tuong-trien-khai.md).
- Kiểm duyệt: sàng lọc AI khi đăng; ẩn tạm khi ≥3 người báo cáo tin cậy hoặc AI xác nhận nhóm nghiêm trọng; người quyết định cuối; sửa chữ nhỏ hiện ngay, đổi video/lộ trình thì duyệt lại (hiện bản cũ khi chờ); số cảm xúc cuối cùng đúng tuyệt đối, lan giữa thiết bị ≤2 giây — [TK §8.2](../../docs/03-y-tuong-trien-khai.md).
- Lộ trình: hiện điểm đến, chỗ ở, loại phòng/vé, thứ tự ngày, chặng bay, ghi chú; riêng tư: ngày đi, giá đã trả, mã đặt chỗ, người đi cùng; bài bị gỡ → người đã lưu thấy "Bài này đã được tác giả gỡ"; đơn đã đặt theo bài giữ nguyên — [TK §8.3](../../docs/03-y-tuong-trien-khai.md).
- Trợ lý: gọi công cụ, chỉ trả lời bằng ID thật, bộ kiểm chứng; nhớ hội thoại 30 ngày, khách lưu trên thiết bị 7 ngày, đăng nhập thì gộp; giới hạn 20 tin/ngày (khách), 50 tin/ngày (thành viên) — [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- Đặt chỗ: giữ chỗ 10 phút; đồng bộ app–web p95 ≤ 2–3 giây — [TK §8.5](../../docs/03-y-tuong-trien-khai.md).

**Mốc theo tuần (dùng cho mục "Tuần 1/2/3" của từng báo cáo)**
- Tuần 1: app bảng tin xu hướng + trang chi tiết; web tìm bằng lời, gợi ý, trang chi tiết; trợ lý thử nghiệm + bộ câu hỏi thử; quản trị đăng nhập, phân quyền. Tuần 2: đăng bài, tương tác, tìm trong bài, đặt nhanh, đặt chỗ đầy đủ, gợi ý sau khi đặt, trợ lý gợi ý địa điểm, kiểm duyệt, đơn đặt chỗ. Tuần 3: Đặt y chang, Chuyến đi của tôi, phần mở rộng, trợ lý đề xuất bài trên app và web, theo dõi xu hướng; thứ Năm ngừng làm tính năng — [ĐB §10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); [TK §13](../../docs/03-y-tuong-trien-khai.md).
- Rủi ro: lộ trình theo ngày tương đối và giữ chỗ nhiều mục phải xong từ tuần 2 — [TK §13, §14](../../docs/03-y-tuong-trien-khai.md).
- Phần mở rộng đề xuất ban đầu (chờ chốt): B7, B1, B4; nhóm đề xuất thêm C1 "Trang cá nhân và theo dõi" — [TK §3.2](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Bảng ánh xạ chính (8 module đích)** — [Đề xuất] dựa trên các nguồn trên

| Module đích (SĐ) | Module nhỏ gộp vào | Người phụ trách | Tính năng chính (chủ sở hữu) | Tham gia (không sở hữu) | Câu hỏi mở ĐB §5 được giao | Chỉ số ĐB §9 |
|---|---|---|---|---|---|---|
| **Access** | M9 Access | Hoang Duc Dung | A13 (đăng nhập, phân quyền), quay lại đúng màn sau đăng nhập (A4, A10, A11, A12), chặn dưới 18 (nguồn sự thật tuổi), một tài khoản cho mọi sản phẩm (ĐB §7) | A5 (một tài khoản nhiều thiết bị), A8 (UNDER_18) | — (câu hỏi mentor: tuổi khóa sửa có đủ? TK §15 Q4; người đi cùng có đăng bài được? TK §15 Q3) | — |
| **Notification** | M10 Notification & Realtime | Hoang Duc Dung | Đồng bộ realtime app ↔ web (A5, A10), báo người đăng khi bị ẩn (A14), B3 báo giảm giá, B6 thông báo tương tác, đẩy gợi ý A9 "thấy ngay trên cả web và app" | A10 (lan bộ đếm), A14 (ẩn trên client) | "App và web mở cùng lúc, sửa một nơi thì nơi kia thấy sau bao lâu" | "Mọi sản phẩm luôn thấy cùng một dữ liệu" |
| **Administration** | M6 Moderation + M7 Analytics (+ N4 Đo lường) | Le Thi Thuy Trang | A14 (hàng chờ, ẩn/hiện/xóa, audit log), màn hình A15, A16, B7, riêng tư ảnh (ĐB §6), đo mục 9, nút "giả lập check-in" (TK §6) | A13 (giao diện quản trị dùng Access) | Báo cáo ẩn ngay hay chờ; tự động kiểm duyệt/AI Agent; sửa bài duyệt lại | Bài ẩn biến mất ≤60 giây (TK §12); gom cách đo cả nhóm |
| **AI** | M5 AI Assistant | Le Nguyen Quoc Bao | A12, B1 (tóm tắt bài), dữ liệu B7 (log hỏi/đáp/từ chối/chi phí), đo chi phí trợ lý (ĐB §7) | A14 (không gợi ý bài ẩn), sàng lọc AI cho M6 [Đề xuất: qua "cổng LLM" chung] | 4 câu về trợ lý | "Trợ lý không bịa" |
| **Booking** | M3 Booking | Nguyen Le Phuc Thang | A4, A8, A11 (Đặt y chang), dữ liệu A5 (chuyến đã đặt), dữ liệu A16, danh mục sản phẩm/giá/tồn kho, xác định "đã đi" | A2 (nguồn lộ trình, điều kiện NOT_TRAVELED), A9 (phát `booking.confirmed`) | Giữ chỗ bao lâu; một mục lỗi; xóa bài / mục ngừng bán | Số chạm & thời gian bài → thanh toán; không bán chỗ cuối hai lần |
| **Payment** | M4 Payment | Nguyen Le Phuc Thang | Thanh toán giả lập luồng như thật, idempotency, hoàn tiền, dữ liệu trừ tiền hai lần cho A16 | A4, A8, A11 | Một mục lỗi: tiền ra sao (chung với Booking) | "Không bao giờ trừ tiền hai lần" |
| **Social** | M8 Social & Content | Le Quang Thanh | A2 (đăng bài + lộ trình + dấu "đã đi thật"), A10 (cảm xúc, bình luận, lưu, chia sẻ/deep link, báo cáo), B5, bài mô phỏng có nhãn, C1 (nhóm đề xuất) | A5 (bài đã lưu, chỗ đã lưu, bài đã đăng), A11 (cung cấp lộ trình công khai) | Biết chắc đã đi; số cảm xúc đúng ngay hay lệch; lộ trình hiện gì | "Lượt tương tác luôn đúng" |
| **Search** | M1 Search & Recommendation + M2 Feed & Trending | Le Anh Duy (tìm kiếm, gợi ý) + Nguyen Thanh Phong (bảng tin, xu hướng) | A1, A3, A6, A7, A9, B2, điểm xu hướng + cờ đẩy ảo cho A15, mô hình embedding + vector index dùng chung (PC §5.1) | A12 (cung cấp công cụ tìm cho trợ lý), A4 ("vài bài đăng nổi bật"), A11 (gợi ý mục tương tự) | 4 câu về xu hướng; không có kết quả thì nói gì | Bảng tin nhanh/mượt; xu hướng đúng, khó đẩy ảo; tìm đúng kiểu người trẻ |

**Ánh xạ từng tính năng A/B → module chủ sở hữu** [Đề xuất]

| Mã | Module chủ | Module phụ (gọi/đọc) |
|---|---|---|
| A1 Bảng tin xu hướng | Search (Phong) | Social (bài, bộ đếm), Access (khách/thành viên), client N1 |
| A2 Đăng bài | Social (Thanh) | Booking (đơn đã hoàn tất → lộ trình, NOT_TRAVELED), Administration (sàng lọc), kho đối tượng/CDN |
| A3 Tìm trong bài đăng | Search (Duy) | Social (nội dung bài để lập chỉ mục) |
| A4 Một chạm, đặt nhanh | Booking (Thắng) | Payment, Access (quay lại đúng chỗ, UNDER_18), Search (bài nổi bật) |
| A5 Chuyến đi của tôi | Booking (chuyến) + Social (lưu, bài đã đăng) | Notification (đồng bộ đa thiết bị), Access (một tài khoản) |
| A6 Tìm bằng lời (web) | Search (Duy) | Booking (giá, còn chỗ thật) |
| A7 Gợi ý dành cho bạn | Search (Duy) | Access (ngân sách khai báo), Social (tín hiệu từ app) |
| A8 Đặt chỗ đầy đủ | Booking | Payment, Access (UNDER_18) |
| A9 Gợi ý sau khi đặt | Search (Duy) | Booking (`booking.confirmed`), Notification (đẩy ngay lên app/web) |
| A10 Tương tác | Social (Thanh) | Notification (realtime), Access (cổng đăng nhập), Administration (báo cáo) |
| A11 Đặt y chang | Booking (Thắng) | Social (lộ trình công khai), Payment, Search (mục tương tự), Access |
| A12 Trợ lý | AI (Bảo) | Search (công cụ), Booking (giá/chỗ), Access (gộp phiên khách) |
| A13 Đăng nhập, phân quyền quản trị | Access (Dung) | Administration (giao diện) |
| A14 Kiểm duyệt | Administration (Trang) | Social (trạng thái hiển thị), Search, AI, Notification |
| A15 Theo dõi xu hướng | Administration (màn hình) | Search (điểm xu hướng, `trend.flagged`) |
| A16 Đơn đặt chỗ | Administration (màn hình) | Booking (đơn, cờ bất thường), Payment (giao dịch) |
| B1 Tóm tắt bài đăng | AI | Social (bài hiển thị), Search |
| B2 Tìm bằng hình ảnh | Search (Duy) | External vision embedding |
| B3 Báo giảm giá | Notification (Dung) | Booking (đổi giá), Social (đã lưu) |
| B4 Dùng khi mất mạng | Client N1 (không phải module nghiệp vụ) | Search (bảng tin cache được), Social |
| B5 Chia sẻ dạng ảnh | Social (Thanh) | Booking (chuyến đã đặt, không lộ giá/ngày nếu chia sẻ công khai) |
| B6 Thông báo tương tác | Notification (Dung) | Social (`comment.created`), Booking (`booking.confirmed` có `source_post_id`) |
| B7 Theo dõi trợ lý | AI (dữ liệu) + Administration (màn hình) | — |
| C1 Trang cá nhân, theo dõi (nhóm đề xuất) | Social | Search (ưu tiên người theo dõi trong bảng tin) |

**Nền tảng N1–N4 không phải module nghiệp vụ**: N1 (App Android, Phong) và N2 (Web + Admin panel, Thanh) thuộc *Presentation layer*; N3 (Dung) là lớp "REST API / HTTPS" + Database layer (Database, Cache) + Event bus + quy ước API; N4 (Trang) gắn với Administration vì đo lường lấy dữ liệu sự kiện ở đó.

**Các chỗ xếp mơ hồ và khuyến nghị** [Đề xuất]
1. **Feed & Trending → Search** (không phải Social). Lý do: (a) khối dữ liệu "Tín hiệu xu hướng và gu" thuộc chung M2 và M1 ([TK §10.2](../../docs/03-y-tuong-trien-khai.md)); (b) nhóm đã quyết Phong–Duy–Bảo dùng chung một mô hình embedding và một vector index ([PC §5.1](../../team/01-phan-cong-nghien-cuu-module.md)); (c) M1 và M2 cùng nhận sự kiện tương tác từ Event bus ([TK §10.1](../../docs/03-y-tuong-trien-khai.md)) — bảng tin là bài toán *xếp hạng/khám phá phía đọc*, còn Social là *phía ghi* (bài, tương tác). Đặt vào Social sẽ làm Social (đã có A2, A10, B5, C1, bài mô phỏng) quá tải và tách điểm xu hướng khỏi gu cá nhân hóa. Ranh giới: Social giữ tương tác thô và bộ đếm; Search giữ tín hiệu đã khử trùng, điểm xu hướng, gu, điểm uy tín.
2. **Analytics → Administration**. Lý do: M7 chủ yếu là "màn hình A15, A16, B7" trên Admin panel ([PC §2.2](../../team/01-phan-cong-nghien-cuu-module.md)); SĐ chỉ có một module nối với Admin panel về mặt chức năng là Administration. Phần *tính toán* vẫn ở module nguồn: điểm xu hướng ở Search, cờ đơn bất thường ở Booking/Payment, chi phí trợ lý ở AI. Administration giữ bảng sự kiện đo lường (TK §10.1 nguyên tắc 6) và các bản ghi đo mục 9.
3. **Realtime → Notification**. Lý do: SĐ không có module realtime; cả hai đều là "đẩy dữ liệu ra client" (WebSocket/SSE và FCM — [PC §4.7](../../team/01-phan-cong-nghien-cuu-module.md)), cùng người phụ trách, cùng là bên nhận sự kiện của mọi module. Lưu ý: *tính đúng* của bộ đếm cảm xúc vẫn thuộc Social; Notification chỉ phân phát.
4. **Moderation → Administration** (không phải Social). Lý do: người dùng chỉ gửi báo cáo qua A10, mọi quyết định ẩn/hiện/xóa do nhân viên trên trang quản trị ([BĐ – dữ liệu A14](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html)); audit log là việc back-office ([KT §6](../../docs/01-kien-thuc-nganh-OTA.md)). Ranh giới: Social giữ bảng `reports` và trường `posts.visibility`; Administration giữ hàng chờ, quyết định, audit log và là bên **duy nhất** được đổi `visibility` sang `hidden/removed` (gọi hàm nội bộ của Social trong cùng giao dịch, rồi phát `post.hidden`, đúng như PC §5.2 ghi M6 phát `post.hidden`).
5. **Danh mục sản phẩm, trang chi tiết → Booking** (không phải Search). Khối "Sản phẩm, giá, tồn kho" thuộc M3 ([TK §10.2](../../docs/03-y-tuong-trien-khai.md)); tuần 1 của Thắng là "trang chi tiết với giá thật và còn chỗ" ([PC §4.3](../../team/01-phan-cong-nghien-cuu-module.md)). Search chỉ giữ *bản chỉ mục* của sản phẩm, luôn hỏi lại Booking giá/chỗ thật trước khi hiển thị giá.
6. **A5 "Chuyến đi của tôi" là màn ghép**: chuyến đã đặt (Booking) + chỗ đã lưu, bài đã lưu, bài đã đăng (Social). Khuyến nghị client gọi hai endpoint song song, không tạo module ghép riêng.
7. **Sở thích người dùng có ba loại, ba chủ**: ngân sách/gu *khai báo* → Access (khối "Tài khoản và hồ sơ", TK §10.2); gu *suy ra từ hành vi* (vector, trọng số tag) → Search; *bộ nhớ hội thoại* → AI. TK §8.4 nói người dùng xem/sửa/xóa "sở thích (ngân sách, gu)" trong trợ lý — khuyến nghị trợ lý đọc/ghi qua API của Access để có một nguồn duy nhất.
8. **Điểm uy tín tài khoản (chống nhiều tài khoản) → Search**, dùng dữ liệu thiết bị/IP do Access ghi khi đăng nhập; Social và Administration đọc điểm này để tính "người báo cáo tin cậy" (TK §8.2).
9. **Sàng lọc tự động khi đăng**: quyết định và chính sách thuộc Administration; lời gọi LLM/API kiểm duyệt đi qua "cổng LLM" của AI để chung một chỗ đo chi phí (ĐB §7 bắt đo chi phí trợ lý) — đây là đề xuất, nhóm có thể cho Administration gọi thẳng API kiểm duyệt.
10. **B2 ở Search** (theo PC) dù dùng mô hình thị giác; **B1 ở AI**; **B3, B6 ở Notification**; **B4 ở client**; **B7 chia AI (dữ liệu) / Administration (màn hình)**.

**Báo cáo nên viết** [Đề xuất]: 8 bản theo 8 module đích. Search do Duy + Phong viết chung (hai tiểu mục: "Tìm kiếm & gợi ý", "Bảng tin & xu hướng"); Administration do Trang viết một bản có hai tiểu mục (Kiểm duyệt, Phân tích & đo lường); Thắng viết 2 bản (Booking, Payment); Dung viết 2 bản (Access, Notification). Như vậy khớp quy tắc "mỗi module một bản" của [PC §6](../../team/01-phan-cong-nghien-cuu-module.md).

### Gaps
- Hình SĐ không cho biết quan hệ giữa các module nghiệp vụ, vị trí Event bus/API Gateway, hay module nào gọi External API — mọi phụ thuộc ở trên lấy từ TK/PC hoặc là đề xuất.
- SĐ vẽ External APIs nối với *Database layer* chứ không phải *Business layer*; có thể chỉ là giản lược của người vẽ. Cần người vẽ xác nhận (khuyến nghị: module nghiệp vụ gọi External API qua adapter).
- Không có tài liệu nào nói rõ C1 có được chốt hay không (TK §3.2 ghi "nhóm đề xuất thêm").
- Không rõ Analytics có cần tách module riêng khi đo đạc lớn lên; với 4 tuần thì không cần.

---

## 2. Mỗi module giữ dữ liệu gì, phụ thuộc module nào, phát/nhận sự kiện nào

### Takeaway
Tám khối dữ liệu đã chốt chia gọn cho 8 module đích; điểm mấu chốt cho hợp đồng là: lộ trình lưu theo **ngày tương đối + loại sản phẩm** (Social), đơn có **máy trạng thái + `source_post_id`** (Booking), thanh toán có **khóa idempotency duy nhất** (Payment), và trạng thái hiển thị bài có **một chủ ghi duy nhất** (Administration qua Social).

### Cited Findings
- Tám khối dữ liệu và module chủ: Tài khoản và hồ sơ (tuổi, ngân sách, gu, vai trò nhân viên, hồ sơ hành khách) → M9; Sản phẩm, giá, tồn kho → M3; Đơn đặt chỗ và thanh toán (giữ chỗ, thanh toán giả lập, trạng thái, đơn bất thường, bài nguồn) → M3, M4; Bài đăng và lộ trình (ngày tương đối, mục, ghi chú, gắn đơn đã đi) → M8; Tương tác (cảm xúc duy nhất theo người × bài, bình luận, lưu, chia sẻ, báo cáo) → M8; Tín hiệu xu hướng và gu (xem, thích, lưu, bỏ qua, đặt y chang; điểm xu hướng; điểm uy tín) → M2, M1; Kiểm duyệt và nhật ký (hàng chờ, ẩn/hiện, ai, lúc nào, vì sao) → M6; Hội thoại trợ lý (lịch sử, bộ nhớ sở thích, chi phí mỗi cuộc) → M5 — [TK §10.2](../../docs/03-y-tuong-trien-khai.md). Artifact có cùng 8 khối (Sơ đồ 6) — [BĐ](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html).
- Lộ trình lưu theo ngày tương đối (Ngày 1, 2, 3) và khung giờ, tham chiếu loại sản phẩm (loại phòng, loại vé, tuyến bay, combo); **không lưu** ngày thật, giá đã trả, mã đặt chỗ, người đi cùng — [TK §5](../../docs/03-y-tuong-trien-khai.md).
- Đăng bài: bắt đầu từ chuyến đã hoàn tất; khai báo có nhận tài trợ không (có thì gắn nhãn Hợp tác); lộ trình tự dựng từ đơn đã hoàn tất, bật/tắt từng mục, không thêm mục mình không đặt; tải lên chạy nền; sàng lọc tự động an toàn / cần xem / giữ lại; "đã đi" = đơn qua ngày trả phòng/ngày sử dụng, không hủy, đã thanh toán; có nút quản trị "giả lập check-in"; server kiểm tra lại khi đăng — [TK §6](../../docs/03-y-tuong-trien-khai.md).
- Máy trạng thái đơn: Giữ chỗ → Chờ thanh toán → Đã thanh toán → Đã xác nhận / Thất bại → Hoàn tiền; giữ chỗ 10 phút; báo giá có hạn; idempotency key cho mỗi lần thanh toán; trừ tồn kho có điều kiện trong giao dịch; lộ trình lỗi một mục áp chính sách người dùng chọn trước: giữ phần đặt được (hoàn mục lỗi, cho hủy miễn phí phần còn lại 30 phút) hoặc tất cả hoặc không — [TK §7](../../docs/03-y-tuong-trien-khai.md).
- Cấp hồ sơ "Sẵn sàng đặt": họ tên đúng giấy tờ, ngày sinh (chặn dưới 18, khóa không sửa), email, hồ sơ hành khách lưu sẵn; đăng nhập bằng SĐT (OTP) hoặc Google — [TK §7](../../docs/03-y-tuong-trien-khai.md).
- Trình tự thanh toán: App → Booking tạo phiên, giữ chỗ tất cả 10 phút, chốt giá → App → Payment kèm idempotency key → cổng giả lập trừ tiền một lần → `payment.succeeded` → Booking xác nhận từng mục, trừ tồn kho → `booking.confirmed` → Event bus đồng bộ app/web → kết quả + gợi ý phần còn thiếu — [TK §11.3](../../docs/03-y-tuong-trien-khai.md).
- Kiểm duyệt: bài/bình luận mới hoặc bản sửa → sàng lọc (an toàn → hiện; cần xem → hàng chờ; nguy hiểm → giữ lại/ẩn tạm) → bị báo cáo nhiều/nghiêm trọng → ẩn tạm → kiểm duyệt viên quyết định (AI gợi ý lý do) → audit log, báo người đăng, bài ẩn biến khỏi app, web, tìm kiếm, trợ lý — [TK §11.4](../../docs/03-y-tuong-trien-khai.md).
- Sự kiện gợi ý trên Event bus: `post.created`, `post.updated` (M8 → M6, M1, M2); `post.hidden`, `comment.hidden` (M6 → M1, M2, M5, M10); `interaction.created` gồm xem, thích, lưu, bỏ qua, bình luận (M8 → M2, M1, M7); `booking.held`, `booking.confirmed`, `booking.failed`, `hold.expired` (M3 → M10, M7, M1); `payment.succeeded`, `payment.refunded` (M4 → M3, M7); `user.logged_in` gộp phiên ẩn danh (M9 → M5, M1); `trend.flagged` (M2 → M6, M7) — [PC §5.2](../../team/01-phan-cong-nghien-cuu-module.md).
- A16 cần: tìm đơn, trạng thái đặt chỗ và thanh toán, đơn có đặt theo lộ trình bài nào không, nhận ra đơn trừ tiền hai lần, đã trả mà không có chỗ, lộ trình chỉ đặt được một phần — [ĐB §4 A16](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Ngành: sản phẩm lưu trú = loại phòng × gói giá; tồn kho allotment/stop sell; ràng buộc MinLOS/CTA; hành khách vé bay phân ADT/CHD/INF, sai tên gần như không sửa được; combo cố định hoặc động, "một mục hỏng kéo cả gói"; đơn "Thất bại — đã trả tiền mà không giữ được chỗ" là nguồn khiếu nại số 1 — [KT §4, §5](../../docs/01-kien-thuc-nganh-OTA.md).

### Inferences

Toàn bộ bảng/trường dưới đây là **[Đề xuất]** (tên bảng tiếng Anh cho code, chú thích tiếng Việt). Tiền là số nguyên VNĐ, thời gian ISO 8601 theo quy ước TK §10.2.

**Access**
- `users(id, phone, email, google_sub, legal_full_name, date_of_birth [khóa sau lần đầu], status, created_at)`; `is_minor` tính từ `date_of_birth` tại server, không lưu cứng.
- `user_preferences(user_id, budget_min_vnd, budget_max_vnd, budget_basis [per_person|group], tastes[], updated_at)` — ngân sách/gu khai báo (A7, A12).
- `passenger_profiles(id, user_id, full_name, date_of_birth, doc_type, doc_number [mã hóa], pax_type [ADT|CHD|INF])`.
- `staff_roles(staff_user_id, role [admin|moderator], granted_by, granted_at, revoked_at)` (A13).
- `sessions(id, user_id, device_id, platform [android|web|admin], refresh_token_hash, created_at, revoked_at)`; `anonymous_sessions(anon_id, created_at, merged_into_user_id, merged_at)`; `login_events(user_id, device_id, ip_hash, at)` (cấp tín hiệu cho điểm uy tín).
- "Người đã đi" **không lưu thành vai trò** (TK Q5): Access trả cờ `is_traveler` tính từ Booking; quyền đăng bài cho *từng chỗ* do Social hỏi Booking.
- Phụ thuộc: Booking (`completed-trips` để tính cờ). Phát: `user.logged_in {user_id, anon_id, platform}`, `user.preferences_updated`. Nhận: `booking.completed` (làm mới cache cờ `is_traveler`).

**Notification**
- `notifications(id, user_id, type [post_hidden|comment_hidden|new_comment|itinerary_booked|price_drop|trip_completed|booking_result], ref_type, ref_id, payload, created_at, read_at)`.
- `push_tokens(id, user_id, device_id, platform, fcm_token, updated_at)`; `notification_preferences(user_id, type, push_enabled)`.
- `user_change_log(user_id, seq, entity, entity_id, op, at)` để client "có mạng lại thì đồng bộ ngay" bằng `since=seq` (TK §8.5).
- `price_watches(user_id, target_type [product|itinerary], target_id, baseline_price_vnd, last_notified_price_vnd)` cho B3 (sinh từ `saves` của Social).
- Kênh realtime (Redis pub/sub, không lưu bảng): `user:{id}` (A5, kết quả đặt chỗ, A9), `post:{id}` (bộ đếm, ẩn bài).
- Phụ thuộc: Social (đọc saves, tác giả bài), Booking (giá hiện tại). Nhận: `post.hidden`, `comment.hidden`, `comment.created`, `interaction.created` (chỉ cập nhật bộ đếm realtime), `booking.*`, `hold.expired`, `product.price_changed`, `booking.completed`. Phát: không (hoặc `notification.sent` cho đo lường).

**Administration**
- `moderation_cases(id, target_type [post|comment|post_revision], target_id, source [new|report|edit|auto_flag|trend_flag], auto_verdict [safe|review|hold], ai_reason, priority_score, status [open|resolved], assigned_to, created_at, resolved_at)`.
- `moderation_actions` = **audit log chỉ ghi thêm**: `(id, actor_staff_id, action [approve|hide|unhide|delete|hold], target_type, target_id, reason_code, reason_text, prev_state, new_state, at)` (A14 "ai làm, lúc nào, vì sao").
- `trend_flag_reviews(flag_id, reviewer_id, verdict [real|inflated], note, at)` (A15).
- `analytics_events(id, name, user_id|anon_id, platform, build, device, network, props jsonb, at)` (TK §10.1 nguyên tắc 6) và `metric_runs(id, metric, build, device, network, p50, p95, sample_size, at)` (TK §12).
- Không lưu đơn/thanh toán: đọc qua API nội bộ của Booking/Payment và **lọc theo vai trò** (kiểm duyệt viên không thấy thanh toán).
- Phụ thuộc: Social (đổi `visibility`, xem bài/bình luận), Search (điểm xu hướng, cờ), Booking/Payment (A16), AI (B7, gợi ý lý do), Access (vai trò nhân viên). Nhận: `post.created`, `post.updated`, `comment.created`, `report.created`, `trend.flagged`, `booking.*`, `payment.*`, `assistant.turn_completed`, `interaction.created`. Phát: `post.hidden`, `post.restored`, `comment.hidden`, `comment.restored`, `post.approved`.

**AI**
- `conversations(id, user_id?, anon_id?, started_platform, created_at, expires_at [30 ngày thành viên / 7 ngày khách theo TK §8.4], merged_at)`.
- `messages(id, conversation_id, role [user|assistant|tool], content, tool_calls jsonb, cited_product_ids[], cited_post_ids[], clarifying bool, refused bool, refusal_reason, created_at)`.
- `llm_usage(id, conversation_id, message_id, purpose [assistant|summary|moderation_assist|query_parse], provider, model, input_tokens, output_tokens, cost_vnd, latency_ms, at)` (ĐB §7, B7).
- `assistant_quota` (Redis: khóa `user|anon` × ngày, 20/50 tin).
- `post_summaries(subject_type [place|product], subject_id, pros[], cons[], source_post_ids[], post_count, generated_at)` cho B1, vô hiệu khi có `post.hidden`.
- `eval_runs(id, question_set_version, n_questions, valid_id_rate, correct_refusal_rate, at)` (TK §12: ≥200 câu).
- Phụ thuộc: Search (công cụ tìm chỗ/bài — chỉ bài đang hiển thị), Booking (giá/chỗ thật để kiểm chứng), Social (đoạn trích thật của bài), Access (preferences). **Không có công cụ đặt chỗ** (TK §8.4). Nhận: `user.logged_in` (gộp hội thoại), `post.hidden` (xóa khỏi tóm tắt/bộ nhớ đệm). Phát: `assistant.turn_completed {tokens, cost_vnd, refused, cited_ids}`.

**Booking**
- Danh mục (nạp từ bộ dữ liệu được cấp): `destinations(id, name, city)`, `products(id, type [hotel|flight|ticket|combo], destination_id, name, media, attributes jsonb, status [active|discontinued])`, `skus(id, product_id, kind [room_type|rate_plan|route|ticket_type|combo_package], attributes)`.
- `inventory(sku_id, date, capacity, held, sold, price_vnd)` — trừ có điều kiện `held+sold+qty <= capacity` trong giao dịch.
- `quotes(id, owner, items jsonb, total_vnd, expires_at)` (báo giá có hạn).
- `booking_drafts(id, user_id?, anon_id?, source_post_id?, source_itinerary_id?, start_date, pax, items jsonb, partial_policy [keep_partial|all_or_nothing], created_at)` — giữ đúng chỗ đang đặt khi phải đăng nhập (A4).
- `bookings(id, user_id, status [HELD|PENDING_PAYMENT|PAID|CONFIRMED|PARTIALLY_CONFIRMED|FAILED|CANCELLED|REFUNDED], source_post_id?, source_itinerary_id?, partial_policy, total_vnd, hold_expires_at, trip_start, trip_end, completed_at?, checked_in_at?, anomaly_flags[], created_at)`.
- `booking_items(id, booking_id, sku_id, service_date_from, service_date_to, day_index?, qty, unit_price_vnd, status [held|confirmed|failed|refunded|cancelled])`; `booking_passengers(booking_id, passenger_profile_id)`; `holds(id, booking_item_id, sku_id, date, qty, expires_at)`.
- Phụ thuộc: Payment (tạo/hoàn), Social (đọc lộ trình công khai cho A11), Search (mục tương tự), Access (UNDER_18, hồ sơ hành khách). Nhận: `payment.succeeded`, `payment.failed`, `payment.refunded`. Phát: `booking.held`, `booking.confirmed`, `booking.partially_confirmed`, `booking.failed`, `booking.cancelled`, `hold.expired`, `booking.completed`, `booking.anomaly_detected`, `product.price_changed`, `product.discontinued`.

**Payment**
- `payments(id, booking_id, user_id, amount_vnd, status [created|requires_action|succeeded|failed|refunded|partially_refunded], method [card_sim|qr_sim|wallet_sim], idempotency_key UNIQUE, gateway_ref, created_at, updated_at)`.
- `payment_attempts(id, payment_id, gateway_txn_id UNIQUE, result, raw, at)`; `refunds(id, payment_id, booking_item_id?, amount_vnd, reason, status, idempotency_key UNIQUE, at)`.
- `idempotency_records(key, scope, request_hash, response_snapshot, created_at, expires_at)` — [Đề xuất] đặt ở nền tảng N3 dùng chung cho giữ chỗ, đăng bài, tương tác.
- Phụ thuộc: Booking (số tiền phải trả của đơn đã giữ chỗ). Nhận: `booking.failed` / mục lỗi sau khi trả (để hoàn), `hold.expired` (hủy payment chưa xong). Phát: `payment.succeeded`, `payment.failed`, `payment.refunded`, `payment.duplicate_detected`.

**Social**
- `posts(id, author_id, kind [video|photo|text], caption, place_ids[], verified_traveled bool, sponsored bool, is_simulated bool, visibility [pending|visible|held|hidden|removed|author_deleted], current_revision, client_post_id UNIQUE per author, created_at, updated_at)`; `post_private_sources(post_id, booking_id)` — chỉ server và quản trị đọc.
- `post_media(id, post_id, type, storage_key, hls_url, thumb_urls{sm,md,lg}, duration_s, privacy_blur_regions, status [uploading|processing|ready])`.
- `itineraries(id, post_id, destination_id, days)`; `itinerary_items(id, itinerary_id, day_index, time_slot, sku_kind, sku_ref, note, visible, sort_order)` — **không có** ngày thật, giá, mã đặt chỗ, người đi cùng.
- `post_revisions(id, post_id, change_type [minor_text|media|itinerary], payload, status [pending|approved|rejected])`.
- `reactions(post_id, user_id, type, created_at, UNIQUE(post_id,user_id))`; `comments(id, post_id, parent_id, author_id, body, visibility, client_comment_id UNIQUE per author, created_at)`; `post_counters(post_id, reactions, comments, saves, shares)` (Redis + đối soát DB); `saves(user_id, target_type [post|product], target_id, created_at)`; `shares(id, post_id, user_id?, channel, at)`; `reports(id, reporter_id, target_type, target_id, reason, note, at)`; `follows(follower_id, followee_id)` (C1).
- Phụ thuộc: Booking (đơn đã hoàn tất của tác giả → ứng viên lộ trình; kiểm tra NOT_TRAVELED), Administration (sàng lọc/quyết định), Search (điểm uy tín người báo cáo). Nhận: `post.hidden`/`post.restored` (nếu Administration không ghi trực tiếp), `product.discontinued` (gắn nhãn "ngừng bán" trên mục lộ trình, TK §8.3). Phát: `post.created`, `post.updated`, `post.deleted`, `comment.created`, `interaction.created`, `report.created`.

**Search**
- `post_index(post_id, embedding vector, text_norm, place_ids, price_band, visible bool, updated_at)`; `product_index(product_id, embedding, attributes, min_price_vnd, destination_id)`; `image_index(product_id|place_id, image_embedding)` (B2).
- `query_logs(id, user_id|anon_id, raw, normalized, parsed {budget_vnd, budget_basis, pax, dates, vibe_tags}, result_ids, zero_result, relax_suggestions, at)`; `lexicon(term, normalized, kind [teencode|slang|no_diacritic|emoji])` (ví dụ "củ" = triệu).
- `signals(user_id|anon_id, post_id, type, at)` khử trùng theo người × bài × loại; `user_taste(user_id, taste_vector, tag_weights, updated_at)`.
- `trend_scores(entity_type [post|place], entity_id, score, velocity, reason_code, computed_at)`; `account_trust(user_id, score, factors jsonb, updated_at)`; `trend_flags(id, entity_type, entity_id, evidence jsonb, status, created_at)`.
- `feed_seen` (Redis, theo người/khách, đảm bảo 0 bài lặp trong 300 bài theo TK §12).
- Phụ thuộc: Social (nội dung bài, bộ đếm), Booking (giá/chỗ thật khi trả kết quả có giá), Access (preferences, login_events). Nhận: `post.created/updated/deleted`, `post.hidden/restored`, `interaction.created`, `booking.confirmed` (A9 + tín hiệu "đặt y chang" trọng số 10), `user.logged_in` (gộp tín hiệu khách), `user.preferences_updated`, `product.price_changed/discontinued`. Phát: `trend.flagged`, `trend.updated` (tùy chọn), `recommendation.ready {user_id, booking_id}` cho A9.

### Gaps
- Chưa thấy cấu trúc thật của bộ dữ liệu sản phẩm và khách hàng mô phỏng được cấp — các bảng Booking/Access là giả định; combo cố định hay ghép từ mục lẻ còn là câu hỏi mentor — [TK §15 Q5](../../docs/03-y-tuong-trien-khai.md).
- Dữ liệu có trạng thái check-in không là câu hỏi mở — [TK §15 Q2](../../docs/03-y-tuong-trien-khai.md).
- Công nghệ Event bus chưa chốt (Redis Streams, outbox trong PostgreSQL… đều chưa được nhóm nêu cụ thể) — [PC §8 #6](../../team/01-phan-cong-nghien-cuu-module.md).
- Xung đột nhẹ chưa giải: TK §8.4 đặt "bộ nhớ sở thích" trong khối hội thoại trợ lý (M5), còn TK §10.2 đặt "ngân sách, gu" trong khối tài khoản (M9) — cần nhóm chốt một chủ.

---

## 3. Quy tắc xuyên module và ma trận phân quyền 6 vai trò

### Takeaway
Năm quy tắc xuyên module (một tài khoản/một nguồn dữ liệu, dưới 18 không tự đặt, bài ẩn biến mất khỏi bảng tin/tìm kiếm/trợ lý, idempotency, riêng tư lộ trình) đều phải được **kiểm tra ở server, ở module nguồn**, và mỗi quy tắc có một module "chủ" rõ ràng; ma trận 6 vai trò của TK và artifact khớp nhau trừ một ô (kiểm duyệt viên xem xu hướng).

### Cited Findings

**Một tài khoản, một nguồn dữ liệu**
- Ràng buộc "Một tài khoản, một nguồn dữ liệu cho tất cả sản phẩm" — [ĐB §7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); bốn sản phẩm dùng một tài khoản, một nguồn dữ liệu — [TK §0 Q2](../../docs/03-y-tuong-trien-khai.md).
- A5: một tài khoản trên hai điện thoại và web thấy cùng dữ liệu; A10: tương tác trên web thì app thấy **ngay** và ngược lại — [ĐB §4 A5, A10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Mục tiêu đo: đồng bộ p95 ≤ 3 giây; bài ẩn biến mất ≤ 60 giây; tương tác lan sang thiết bị khác p95 ≤ 2 giây — [TK §12](../../docs/03-y-tuong-trien-khai.md).
- Các sản phẩm dùng chung dữ liệu; thống nhất từ tuần 1 cách trao đổi dữ liệu — [ĐB §11](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).

**Dưới 18**
- Người đặt chỗ phải đủ 18 tuổi (quy tắc cố định); người dưới 18 không được tự đặt — [ĐB §5, §6, A8](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Ngày sinh khai khi đăng ký, khóa sửa, kiểm tra ở server; dưới 18: chặn, giải thích thân thiện, vẫn cho xem, lưu — [TK §7, §11.2, §2.2](../../docs/03-y-tuong-trien-khai.md). Tình huống thử bắt buộc gồm "dưới 18 gọi thẳng API" — [TK §12](../../docs/03-y-tuong-trien-khai.md).
- Artifact: dưới 18 dùng trợ lý được "nhưng bấm đặt sẽ bị chặn"; A2 không đăng được, "trường hợp đi cùng người lớn cần hỏi mentor" (*) — [BĐ – dữ liệu A12, A2](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html).

**Bài ẩn biến mất**
- "Một bài đăng đã bị ẩn thì biến mất khỏi ứng dụng, web và gợi ý của trợ lý trong thời gian ngắn"; người đăng được báo khi bị ẩn — [ĐB §4 A14](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); trợ lý "không gợi ý bài đăng đã bị ẩn" — [ĐB §4 A12](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Trang phải phối hợp với Phong, Duy, Bảo để bài ẩn biến mất khỏi bảng tin, tìm kiếm và trợ lý; Trang đo thời gian từ lúc ẩn tới lúc biến mất — [PC §5.1, §4.5](../../team/01-phan-cong-nghien-cuu-module.md).

**Idempotency và tính đúng**
- Không bao giờ trừ tiền hai lần; hai người đặt chỗ cuối: chỉ một người được, người kia được báo ngay — [ĐB §4 A4](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Số cảm xúc, bình luận luôn đúng kể cả nhiều người cùng thả hoặc một người bấm liên tục — [ĐB §4 A10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Idempotency-Key cho giữ chỗ, thanh toán, đăng bài, tương tác — [TK §10.2](../../docs/03-y-tuong-trien-khai.md). Tình huống thử: bấm hai lần, mất mạng rồi thử lại, xác nhận trùng, hai thiết bị cùng trả, 50 yêu cầu tranh chỗ cuối; 200 tài khoản cùng thả: sai số 0; "bấm 200 lần = 1 người" — [TK §12](../../docs/03-y-tuong-trien-khai.md).

**Riêng tư lộ trình**
- A11: không để lộ thông tin riêng của người đăng như ngày đi, số tiền đã trả; giá là giá hiện tại — [ĐB §4 A11](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Thông tin cá nhân và lịch sử đặt chỗ không hiện cho người khác — [ĐB §5](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); riêng tư kể cả qua lộ trình đính kèm; ảnh có mặt người lạ hoặc thông tin cá nhân phải xử lý được — [ĐB §6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Xem trước "người khác sẽ thấy gì": ngày đi, giá đã trả, mã đặt chỗ không bao giờ hiện — [TK §6](../../docs/03-y-tuong-trien-khai.md).

**Trung thực**
- Không bịa chỗ, giá, khuyến mãi, tình trạng còn chỗ, bài đăng; không khan hiếm giả; nội dung mô phỏng phải được đánh dấu rõ — [ĐB §5, §6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). "Giữ chỗ 10 phút, giờ hết hạn hiển thị là thật" — [TK §7](../../docs/03-y-tuong-trien-khai.md). Dark pattern cần tránh: khan hiếm giả, đếm ngược giả, drip pricing — [KT §7](../../docs/01-kien-thuc-nganh-OTA.md).

**Ma trận phân quyền** — [TK §2.1, §2.3](../../docs/03-y-tuong-trien-khai.md); chi tiết từng ô theo [BĐ – mảng FEATURES/ROLES](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html)

| Tính năng | Khách | Thành viên | Dưới 18 | Người đã đi | Kiểm duyệt | Quản trị |
|---|---|---|---|---|---|---|
| A1 Bảng tin | ✅ bảng tin chung | ✅ theo gu | ✅ | ✅ | — | — |
| A2 Đăng bài | ❌ phải đăng nhập | ❌ chưa đi chỗ đó | ❌\* (đi cùng người lớn: hỏi mentor) | ✅ chỉ chỗ đã đi | — | — |
| A3, A6 Tìm | ✅ | ✅ | ✅ | ✅ | — | — |
| A4 Đặt nhanh | Một phần: xem; đặt thì đăng nhập, quay lại đúng chỗ | ✅ | Một phần: xem, không đặt | ✅ | — | — |
| A5 Chuyến đi của tôi | ❌ | ✅ | Một phần\*: chỗ/bài đã lưu | ✅ + bài đã đăng | — | — |
| A7 Gợi ý | Một phần\*: gợi ý chung | ✅ | ✅ không đặt được | ✅ | — | — |
| A8 Đặt đầy đủ | Một phần: chọn; trả thì đăng nhập | ✅ | ❌ | ✅ | — | — |
| A9 Gợi ý sau đặt | ❌ | ✅ | ❌ | ✅ | — | — |
| A10 Tương tác | Một phần: xem, chia sẻ; còn lại đăng nhập | ✅ | ✅\* (đặc tả không cấm) | ✅ | — | — |
| A11 Đặt y chang | Một phần: xem lộ trình; bấm thì đăng nhập | ✅ | ❌ | ✅ | — | — |
| A12 Trợ lý | ✅ (không mất hội thoại khi đăng nhập) | ✅ dùng thêm gu, ngân sách | ✅ bấm đặt bị chặn | ✅ | — | — |
| A13 Vào trang quản trị | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ + cấp quyền\* |
| A14 Kiểm duyệt | chỉ báo cáo qua A10 | như Khách | như Khách | như Khách | ✅ | ✅\* |
| A15 Xu hướng | ❌ | ❌ | ❌ | ❌ | TK: ✅ · BĐ: một phần\* | ✅ |
| A16 Đơn, thanh toán | đơn của mình ở A5 | của mình | của mình | của mình | ❌ | ✅ |
| B3 Báo giảm giá | ❌ | ✅ | ✅ | ✅ | — | — |
| B5 Chia sẻ ảnh | Một phần\*: chỉ bài đăng | ✅ bài + chuyến | Một phần | ✅ | — | — |
| B6 Thông báo tương tác | ❌ | ❌ | ❌ | ✅ bài của mình | — | — |
| B7 Theo dõi trợ lý | ❌ | ❌ | ❌ | ❌ | ❌\* (BĐ đề xuất chỉ quản trị) | ✅ |

(\* = giả định của nhóm, cần hỏi mentor — theo ghi chú trong BĐ và TK.)
- Xung đột duy nhất giữa hai nguồn: TK §2.3 cho Kiểm duyệt ✅ ở "Xu hướng", còn BĐ ghi kiểm duyệt viên "Dùng một phần — Đề xuất: được xem để phát hiện đẩy ảo" kèm dấu \* — [TK §2.3](../../docs/03-y-tuong-trien-khai.md); contradicted by [BĐ – dữ liệu A15](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html).
- Artifact dùng nhãn nút "Đặt ngay" (theo chữ của đặc tả) trong khi hướng đã chốt gọi là "Đặt y chang"; artifact ghi nguồn là `docs/01-tai-lieu-dac-ta-san-pham.md` (tên file cũ; đặc tả hiện ở docs/02) — [BĐ](/root/.claude/projects/-home-user-OTA-research/bc353c81-4a5a-5724-916e-4cbdb4fdc04d/tool-results/artifact-dcf3e2f3-1793451870-80c8.html); [TK §0 Q3](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Module chủ của từng quy tắc** [Đề xuất]

| Quy tắc | Module chủ (nguồn sự thật) | Cách thực thi qua các module |
|---|---|---|
| Một tài khoản, một nguồn dữ liệu | Access (danh tính) + N3 (một CSDL) | Mọi module chỉ dùng `user_id` do Access cấp; khách dùng `anon_id`, gộp khi `user.logged_in`; Notification đẩy thay đổi theo kênh `user:{id}` và cung cấp `GET /v1/sync?since=` để bắt kịp. Nhân viên dùng cùng hệ danh tính nhưng token quản trị có `scope=admin` riêng, không gọi được API người dùng bằng token đó. |
| Dưới 18 | Access giữ `date_of_birth` (khóa); **Booking** chặn ở `POST /v1/bookings` và `POST /v1/itineraries/{id}/replicate`; **Payment** kiểm tra lại (phòng thủ nhiều lớp) | Lỗi `UNDER_18` (HTTP 403). Client hiển thị giải thích thân thiện, vẫn cho lưu. Trợ lý vẫn gợi ý; nút đặt dẫn tới cùng cổng chặn. |
| Bài ẩn biến mất | Administration (quyết định) → Social (`visibility`) | (1) Administration ghi quyết định + đổi `visibility` trong cùng giao dịch; (2) phát `post.hidden`; (3) Search gỡ khỏi `post_index`, xóa khỏi cache bảng tin; AI vô hiệu `post_summaries` và mọi kết quả công cụ; Notification báo tác giả + đẩy `post:{id} hidden` để client gỡ thẻ; (4) **lưới an toàn khi đọc**: Search/AI luôn "hydrate" bài qua Social và lọc `visibility=visible`, nên kể cả khi sự kiện trễ thì bài vẫn không hiện. `GET /v1/posts/{id}` trả 404 `POST_UNAVAILABLE` (bị ẩn) hoặc 410 (tác giả gỡ). Mục tiêu ≤60 giây (TK §12). |
| Idempotency | N3 (middleware chung) + từng module | Header `Idempotency-Key` bắt buộc cho: giữ chỗ (Booking), thanh toán/hoàn (Payment), đăng bài (Social, kèm `client_post_id`), bình luận (`client_comment_id`), cảm xúc (PUT theo người × bài nên tự idempotent). Người tiêu thụ sự kiện khử trùng theo `event_id`. Tồn kho trừ có điều kiện. |
| Riêng tư lộ trình | Social (mô hình lộ trình) + Booking (A11) | Lộ trình chỉ lưu `day_index`, `time_slot`, `sku_ref`, `note`; liên kết tới đơn gốc nằm ở bảng riêng chỉ server/quản trị đọc; A11 luôn báo giá hiện tại; B6 "có người đặt theo lộ trình" chỉ báo số lượng, không lộ người đặt; B5 chia sẻ chuyến đã đặt không in giá/ngày khi công khai; ảnh: Administration phát hiện mặt người lạ/thông tin cá nhân, Social lưu vùng làm mờ. |
| Quyền theo vai trò | Access (cấp token + claims) | Claims: `roles` (member/minor tính từ tuổi), `staff_roles` (admin/moderator), cờ `is_traveler`; mỗi module kiểm tra quyền ở server; quyền đăng bài **theo từng chỗ** do Social hỏi Booking (`NOT_TRAVELED`). Administration lọc trường thanh toán khi vai trò là moderator. |
| Không bịa | Booking (giá/chỗ thật), AI (bộ kiểm chứng ID) | Search và AI không tự lưu giá để hiển thị; mọi giá hiện ra lấy từ Booking tại thời điểm đó; bài mô phỏng có `is_simulated=true` và nhãn. |

### Gaps
- Chưa có quyết định nhãn "mô phỏng" có hiện cho người dùng cuối không — [TK §15 Q1](../../docs/03-y-tuong-trien-khai.md).
- Người đi cùng (không phải người đặt) có được đăng bài "đã đi thật" không — [TK §15 Q3](../../docs/03-y-tuong-trien-khai.md); ảnh hưởng trực tiếp tới hàm kiểm tra NOT_TRAVELED.
- Ô kiểm duyệt viên × A15 chưa thống nhất giữa TK và BĐ; quyền của quản trị viên với A14 và cấp quyền nhân viên là giả định (\*).
- Không có nguồn nào định nghĩa "thời gian ngắn" ở A14 ngoài mục tiêu khởi điểm ≤60 giây của nhóm.

---

## 4. Hợp đồng API: quy ước, danh sách endpoint theo module, danh mục sự kiện

### Takeaway
Quy ước API đã được nhóm chốt ở mức khung (`/v1`, VNĐ nguyên, ISO 8601, phân trang con trỏ, Idempotency-Key, 6 mã lỗi chung, tên sự kiện dạng `thuc_the.hanh_dong`); phần dưới đây là bản nháp đầy đủ ~90 endpoint và ~30 sự kiện cho 8 module, toàn bộ là **[Đề xuất]** để mang vào buổi họp ghép.

### Cited Findings
- Quy ước API (N3 soạn, chốt tuần 1): `/v1`; tiền là số nguyên VNĐ; ngày giờ ISO 8601; phân trang bằng con trỏ; Idempotency-Key cho giữ chỗ, thanh toán, đăng bài, tương tác; mã lỗi chung `PRICE_CHANGED`, `SOLD_OUT`, `HOLD_EXPIRED`, `UNDER_18`, `NOT_TRAVELED`, `RATE_LIMITED`; sự kiện đặt tên thống nhất ví dụ `post.hidden`, `booking.confirmed`, `interaction.created` — [TK §10.2](../../docs/03-y-tuong-trien-khai.md).
- Hình đích: Presentation ↔ Business qua "REST API / HTTPS" — [SĐ](so-do-module.png). Sơ đồ nhóm: "API chung · N3 — giữ hợp đồng API, đăng nhập, phân quyền, đẩy realtime" — [PC §2.1](../../team/01-phan-cong-nghien-cuu-module.md).
- Dung chủ trì chốt API, sự kiện, quyền trong tuần 1 để tránh vỡ ranh giới module khi ghép — [TK §14](../../docs/03-y-tuong-trien-khai.md); kết quả họp ghép phải có "danh sách API và sự kiện giữa các module" — [PC §7.2](../../team/01-phan-cong-nghien-cuu-module.md).
- Đường dẫn chia sẻ mở được trên trình duyệt và mở thẳng app nếu đã cài — [ĐB §4 A10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Trợ lý dùng được khi chưa đăng nhập, đăng nhập giữa chừng không mất hội thoại; tiếp tục giữa app và web — [ĐB §4 A12](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- Nhẹ cho máy yếu, mạng yếu: ảnh nhiều kích cỡ, video HLS tự hạ chất lượng, phân trang con trỏ — [TK §10.1](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**4.1. Quy ước bổ sung** [Đề xuất]
- Xác thực: `Authorization: Bearer <access_token>` (ngắn hạn) + refresh token; khách gửi `X-Anon-Id` (do `POST /v1/auth/anonymous` cấp) để bảng tin, trợ lý, nháp đặt chỗ hoạt động không cần đăng nhập. Token quản trị mang `scope=admin`.
- Phân trang: `?cursor=<opaque>&limit=20` → `{ "items": [...], "next_cursor": "..." | null }`. Không dùng offset.
- Lỗi: `{ "error": { "code": "SOLD_OUT", "message": "<tiếng Việt thân thiện>", "details": {...}, "retryable": false } }`. Ánh xạ HTTP: `AUTH_REQUIRED` 401 (kèm `return_to` để quay lại đúng màn), `FORBIDDEN` 403, `UNDER_18` 403, `NOT_TRAVELED` 403, `SOLD_OUT` 409, `PRICE_CHANGED` 409 (kèm giá cũ/mới), `HOLD_EXPIRED` 410, `QUOTE_EXPIRED` 410, `ITEM_DISCONTINUED` 409, `POST_UNAVAILABLE` 404/410, `IDEMPOTENCY_CONFLICT` 422 (cùng khóa, khác nội dung), `VALIDATION_FAILED` 422, `RATE_LIMITED` 429 (kèm `Retry-After`), `PAYMENT_FAILED` 402. Sáu mã đầu tiên của TK giữ nguyên tên; các mã còn lại là đề xuất thêm.
- Idempotency: khóa UUID do client sinh, lưu 24 giờ theo `(user, endpoint, key)`; gửi lại cùng khóa trả lại đúng phản hồi cũ; khác nội dung → `IDEMPOTENCY_CONFLICT`.
- Phiên bản sự kiện: phong bì `{ event_id, name, version, occurred_at, producer, actor: {user_id|staff_id|anon_id}, payload }`; giao ít nhất một lần; người nhận khử trùng theo `event_id`; ghi qua outbox trong cùng giao dịch CSDL để không mất sự kiện.
- Realtime: một kết nối WebSocket/SSE mỗi client tại `/v1/realtime`; thông điệp `{ channel, type, data, seq }`.
- Đường dẫn nội bộ giữa module: tiền tố `/internal/...` (không đi qua Internet; trong monolith có thể là lời gọi hàm, nhưng vẫn giữ chữ ký này làm hợp đồng).

**4.2. Danh sách endpoint theo module** [Đề xuất]
(Bên gọi: App = App Android, Web = Web OTA, Admin = Admin panel; "khách" = được gọi khi chưa đăng nhập.)

*Access*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| POST | /v1/auth/anonymous | App, Web (khách) | Cấp `anon_id` cho khách |
| POST | /v1/auth/otp/request | App, Web | Gửi OTP qua SMS |
| POST | /v1/auth/otp/verify | App, Web | Đăng nhập SĐT, nhận token; gửi kèm `anon_id` để gộp → phát `user.logged_in` |
| POST | /v1/auth/google | App, Web | Đăng nhập Google |
| POST | /v1/auth/refresh · /v1/auth/logout | App, Web, Admin | Làm mới / thu hồi phiên |
| GET | /v1/me | App, Web | Hồ sơ, `roles`, `is_minor`, `is_traveler`, `can_book` |
| PATCH | /v1/me | App, Web | Sửa họ tên, email; đặt ngày sinh một lần (khóa sau đó) |
| GET · PUT | /v1/me/preferences | App, Web, AI | Ngân sách (theo người/nhóm), gu khai báo |
| GET · POST · PATCH · DELETE | /v1/me/passengers[/{id}] | App, Web | Hồ sơ hành khách lưu sẵn |
| POST | /v1/admin/auth/login | Admin | Đăng nhập nhân viên |
| GET · POST · DELETE | /v1/admin/staff[/{id}/roles] | Admin (quản trị) | Cấp/thu quyền kiểm duyệt, quản trị (A13) |
| GET | /internal/users/{id}/authz | Mọi module | Claims, tuổi, cờ `can_book` |

*Notification*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| GET (WS/SSE) | /v1/realtime | App, Web, Admin | Kênh `user:{id}`, `post:{id}` (bộ đếm, ẩn bài, kết quả đặt chỗ, A9) |
| GET | /v1/sync?since={seq} | App, Web | Bắt kịp thay đổi sau khi mất mạng/mở lại (A5) |
| POST · DELETE | /v1/me/push-tokens | App | Đăng ký/xóa token FCM |
| GET | /v1/me/notifications?cursor= | App, Web | Hộp thông báo |
| POST | /v1/me/notifications/{id}/read · /read-all | App, Web | Đánh dấu đã đọc |
| GET · PUT | /v1/me/notification-preferences | App, Web | Bật/tắt từng loại (B3, B6) |

*Administration* (tất cả cần `scope=admin`; kiểm duyệt viên bị 403 ở các mục thanh toán)
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| GET | /v1/admin/moderation/queue?type=&source=&cursor= | Admin | Hàng chờ ưu tiên (A14) |
| GET | /v1/admin/moderation/cases/{id} | Admin | Chi tiết mục + gợi ý lý do của AI + lịch sử |
| POST | /v1/admin/moderation/cases/{id}/decision | Admin | `approve|hide|unhide|delete` + `reason_code` + ghi chú; Idempotency-Key; ghi audit; phát `post.hidden`… |
| GET | /v1/admin/audit-log?target=&actor=&cursor= | Admin | Tra nhật ký ai/lúc nào/vì sao |
| GET | /v1/admin/trends?entity=post|place&window= | Admin | Đang lên xu hướng và lý do (A15) |
| GET · POST | /v1/admin/trends/flags[/{id}/review] | Admin | Cờ nghi đẩy ảo, đánh giá |
| GET | /v1/admin/bookings?q=&status=&anomaly=&source_post_id=&cursor= | Admin (quản trị) | Tìm đơn, lọc đơn bất thường (A16) |
| GET | /v1/admin/bookings/{id} | Admin (quản trị) | Trạng thái đặt chỗ + thanh toán + bài nguồn |
| POST | /v1/admin/bookings/{id}/simulate-checkin | Admin (quản trị) | Nút demo "giả lập check-in" (TK §6) |
| GET | /v1/admin/assistant/metrics · /assistant/conversations | Admin (quản trị) | B7: hỏi gì, gợi ý gì, từ chối gì, chi phí |
| GET | /v1/admin/metrics?metric= | Admin | Kết quả đo mục 9 (p50, p95, theo build/thiết bị/mạng) |
| POST | /v1/analytics/events (batch) | App, Web | Sự kiện đo lường (thời gian thấy bài đầu, số chạm…) |

*AI*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| POST | /v1/assistant/conversations | App, Web (khách) | Mở hội thoại |
| POST | /v1/assistant/conversations/{id}/messages | App, Web (khách) | Gửi câu hỏi; phản hồi stream (SSE) gồm văn bản + thẻ `{type: product|place|post, id}` + câu hỏi lại / lời từ chối; 429 `RATE_LIMITED` khi vượt hạn mức |
| GET | /v1/assistant/conversations?cursor= · /{id} | App, Web | Tiếp tục hội thoại giữa app và web |
| DELETE | /v1/assistant/conversations/{id} | App, Web | Xóa hội thoại |
| GET | /v1/places/{id}/summary · /v1/products/{id}/summary | App, Web | B1 "N người đã đi nói gì", chỉ từ bài đang hiển thị |
| GET | /internal/assistant/usage?from=&to= | Administration | Dữ liệu B7 |
| POST | /internal/ai/classify | Administration | [Đề xuất] Gợi ý nhãn/lý do kiểm duyệt qua cổng LLM chung |

*Booking*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| GET | /v1/destinations · /v1/destinations/{id} | App, Web (khách) | Điểm đến |
| GET | /v1/products/{id} | App, Web (khách) | Trang chi tiết: ảnh lớn, giá thật, còn chỗ (A4) |
| GET | /v1/products/{id}/availability?from=&to=&pax= | App, Web (khách) | Giá và chỗ theo ngày |
| POST | /v1/quotes | App, Web | Báo giá có hạn cho danh sách mục (`PRICE_CHANGED`, `SOLD_OUT`) |
| POST | /v1/itineraries/{itinerary_id}/replicate | App, Web | **Đặt y chang** (A11): nhận `start_date`, `pax` → nháp đã dời ngày theo `day_index`, giá hiện tại, chênh lệch, mục hết chỗ/ngừng bán + gợi ý thay thế; khách được xem nháp |
| POST · GET · PATCH | /v1/booking-drafts[/{id}] | App, Web (khách được tạo) | Nháp đặt chỗ: thêm/bớt mục, đổi ngày/số người, chọn `partial_policy`; nháp gắn `anon_id` được nhận về tài khoản sau đăng nhập |
| POST | /v1/bookings | App, Web | Từ nháp: kiểm tra tuổi, giữ chỗ tất cả mục 10 phút; Idempotency-Key; lỗi `UNDER_18`, `SOLD_OUT`, `PRICE_CHANGED` |
| GET | /v1/bookings/{id} | App, Web | Trạng thái từng mục, `hold_expires_at`, tiền |
| POST | /v1/bookings/{id}/cancel | App, Web | Hủy (kể cả hủy miễn phí 30 phút phần còn lại khi đặt được một phần) |
| GET | /v1/me/trips?status=upcoming|completed&cursor= | App, Web | Chuyến đã đặt (A5) |
| GET | /internal/users/{id}/completed-trips?place_id= | Social, Access | Điều kiện đăng bài, cờ `is_traveler` |
| GET | /internal/users/{id}/itinerary-candidates | Social | Mục từ đơn đã hoàn tất để dựng lộ trình (không trả giá, mã đặt chỗ) |
| GET | /internal/bookings?…, /internal/bookings/{id} | Administration | Dữ liệu A16 kèm `anomaly_flags`, `source_post_id` |
| POST | /internal/bookings/{id}/simulate-checkin | Administration | Giả lập đã đi |
| GET | /internal/prices?sku_ids=&dates= | Search, AI, Notification | Giá/chỗ thật tại thời điểm hỏi |

*Payment*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| POST | /v1/payments | App, Web | Tạo thanh toán cho đơn đang giữ chỗ; **Idempotency-Key bắt buộc**; trả `next_action` (OTP/3-D Secure/QR giả lập) |
| POST | /v1/payments/{id}/confirm | App, Web | Gửi OTP giả lập; Idempotency-Key |
| GET | /v1/payments/{id} | App, Web | Trạng thái (để client mất mạng hỏi lại thay vì bấm trả lần nữa) |
| POST | /v1/payment-gateway/callback | Cổng giả lập | Kết quả từ cổng; khử trùng theo `gateway_txn_id` |
| POST | /internal/refunds | Booking | Hoàn mục lỗi / hủy; Idempotency-Key |
| GET | /internal/payments?booking_id= | Administration (chỉ quản trị) | Giao dịch, phát hiện trừ hai lần |

*Social*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| GET | /v1/posts/{id} | App, Web (khách) | Bài + bộ đếm + dấu "đã đi thật", nhãn Hợp tác/mô phỏng |
| GET | /v1/posts/{id}/itinerary | App, Web (khách) | Lộ trình công khai (không ngày, giá, mã, người đi cùng) |
| GET | /v1/me/itinerary-candidates | App | Chuyến đã hoàn tất để "Kể lại chuyến đi" |
| POST | /v1/media/uploads | App, Web | URL tải lên có thể tiếp tục (resumable) vào kho đối tượng |
| POST | /v1/posts | App, Web | Đăng bài (`client_post_id`, media, lộ trình bật/tắt mục, tài trợ); Idempotency-Key; `NOT_TRAVELED` |
| PATCH · DELETE | /v1/posts/{id} | App, Web (tác giả) | Sửa (chữ nhỏ hiện ngay; media/lộ trình tạo bản chờ duyệt) / gỡ bài |
| PUT · DELETE | /v1/posts/{id}/reaction | App, Web | Thả/bỏ cảm xúc (duy nhất theo người × bài) |
| GET · POST | /v1/posts/{id}/comments | App, Web (khách xem) | Danh sách / gửi bình luận, trả lời (`parent_id`, `client_comment_id`) |
| DELETE | /v1/comments/{id} | App, Web (tác giả) | Xóa bình luận của mình |
| PUT · DELETE | /v1/saves/{target_type}/{target_id} | App, Web | Lưu bài hoặc chỗ |
| GET | /v1/me/saves?type=post|product&cursor= | App, Web | Chỗ, bài đã lưu (A5) |
| GET | /v1/me/posts · /v1/users/{id}/posts | App, Web | Bài mình đăng (A5) / trang cá nhân (C1) |
| POST · DELETE | /v1/users/{id}/follow | App, Web | Theo dõi (C1, nhóm đề xuất) |
| POST | /v1/posts/{id}/shares | App, Web (khách) | Tạo link chia sẻ (mở web, mở thẳng app qua App Links) |
| POST | /v1/posts/{id}/share-card · /v1/bookings/{id}/share-card | App, Web | B5 ảnh chia sẻ |
| POST | /v1/reports | App, Web | Báo cáo bài/bình luận → `report.created` |
| POST | /v1/interactions/batch | App, Web (khách) | Gửi gộp tín hiệu xem, thời lượng xem, bỏ qua (gửi lại được khi có mạng) |
| GET | /internal/posts?ids= | Search, AI | Hydrate bài, chỉ trả bài `visible` |
| POST | /internal/posts/{id}/visibility | Administration | Đổi trạng thái hiển thị (chủ ghi duy nhất) |

*Search*
| Method | Path | Bên gọi | Mục đích |
|---|---|---|---|
| GET | /v1/feed?cursor=&limit= | App, Web (khách) | Bảng tin xu hướng + cá nhân hóa, mỗi bài kèm `reason` ngắn (A1), không lặp |
| GET | /v1/search/posts?q=&cursor= | App, Web (khách) | Tìm trong bài đăng theo ý (A3); `zero_result` nói thật |
| GET | /v1/search?q=&cursor= | Web, App (khách) | Tìm chỗ bằng lời (A6): trả `parsed` (ngân sách theo người/nhóm, số người, gu) + kết quả + `relax_suggestions` |
| GET | /v1/recommendations?context=home | Web, App | Gợi ý dành cho bạn kèm lý do (A7) |
| GET | /v1/recommendations?context=post_booking&booking_id= | Web, App | Phần còn thiếu của chuyến (A9) |
| GET | /v1/products/{id}/related-posts | App, Web (khách) | Vài bài nổi bật trên trang chi tiết (A4) |
| POST | /v1/search/image | App, Web | Tìm bằng ảnh (B2) |
| POST | /v1/feed/onboarding | App, Web | Thẻ "Bạn thích kiểu nào?" (có thể bỏ qua) |
| POST | /internal/search/tools/places · /tools/posts | AI | Công cụ của trợ lý; chỉ dữ liệu thật, chỉ bài đang hiển thị |
| GET | /internal/search/similar-products?sku_id=&date= | Booking | Gợi ý mục tương tự khi hết chỗ/ngừng bán (A11) |
| GET | /internal/trends · /internal/trust/{user_id} | Administration, Social | Điểm xu hướng/cờ (A15); điểm uy tín người báo cáo |

**4.3. Danh mục sự kiện** [Đề xuất; các sự kiện đánh dấu (PC) lấy nguyên từ PC §5.2]

| Sự kiện | Producer | Consumers | Payload chính |
|---|---|---|---|
| `user.logged_in` (PC) | Access | AI, Search | user_id, anon_id, platform |
| `user.preferences_updated` | Access | Search, AI | user_id, budget, budget_basis, tastes |
| `post.created` (PC) | Social | Administration (sàng lọc), Search (chỉ mục, bảng tin) | post_id, author_id, place_ids, kind, has_itinerary, is_simulated, visibility |
| `post.updated` (PC) | Social | Administration, Search | post_id, change_type, revision_id |
| `post.deleted` | Social | Search, AI, Notification | post_id, author_id |
| `post.approved` | Administration | Social, Search | post_id, case_id |
| `post.hidden` (PC) | Administration | Search, AI, Notification, Social | post_id, author_id, reason_code, hidden_at, action_id |
| `post.restored` | Administration | Search, AI, Social | post_id, action_id |
| `comment.created` | Social | Administration, Notification (B6) | comment_id, post_id, author_id, post_author_id, parent_id |
| `comment.hidden` (PC) | Administration | Notification, Social | comment_id, post_id, author_id, reason_code |
| `interaction.created` (PC) | Social | Search (xu hướng, gu), Administration (đo), Notification (bộ đếm realtime) | user_id/anon_id, post_id, type [view|dwell|reaction|comment|save|share|skip], value, device_id, at |
| `report.created` | Social | Administration | report_id, target_type, target_id, reporter_id, reason |
| `trend.flagged` (PC) | Search | Administration | entity_type, entity_id, evidence (cụm thiết bị, tài khoản mới, tốc độ) |
| `booking.held` (PC) | Booking | Notification, Administration | booking_id, user_id, item_ids, hold_expires_at, total_vnd, source_post_id |
| `hold.expired` (PC) | Booking | Notification, Payment, Administration | booking_id, user_id |
| `booking.confirmed` (PC) | Booking | Notification, Administration, Search (A9 + tín hiệu đặt y chang) | booking_id, user_id, items (sku, destination, dates), source_post_id |
| `booking.partially_confirmed` | Booking | Notification, Administration (A16), Payment (hoàn) | booking_id, confirmed_item_ids, failed_item_ids, refund_vnd, policy |
| `booking.failed` (PC) | Booking | Notification, Administration, Payment | booking_id, reason, refund_required |
| `booking.cancelled` | Booking | Payment, Notification, Administration | booking_id, item_ids, refund_vnd |
| `booking.completed` | Booking | Access (cờ đã đi), Notification (nhắc "Kể lại chuyến đi"), Social | booking_id, user_id, place_ids, completed_at |
| `booking.anomaly_detected` | Booking | Administration | booking_id, type [double_charge|paid_no_inventory|partial_itinerary] |
| `payment.succeeded` (PC) | Payment | Booking, Administration | payment_id, booking_id, amount_vnd, idempotency_key |
| `payment.failed` | Payment | Booking, Administration | payment_id, booking_id, reason |
| `payment.refunded` (PC) | Payment | Booking, Administration, Notification | payment_id, booking_id, booking_item_id, amount_vnd |
| `payment.duplicate_detected` | Payment | Administration | booking_id, payment_ids |
| `product.price_changed` | Booking | Notification (B3), Search | sku_id, date_range, old_price_vnd, new_price_vnd |
| `product.discontinued` | Booking | Social (nhãn "ngừng bán"), Search | sku_id |
| `assistant.turn_completed` | AI | Administration (B7) | conversation_id, input_tokens, output_tokens, cost_vnd, refused, cited_ids |
| `recommendation.ready` | Search | Notification | user_id, booking_id, items |

### Gaps
- Chưa có bản quy ước API do N3 soạn chính thức; mọi chi tiết ngoài 6 mã lỗi và 3 tên sự kiện mẫu của TK §10.2 là đề xuất.
- Chưa chốt WebSocket hay SSE (PC §4.7 để mở cả hai).
- Chưa rõ cổng thanh toán giả lập tự viết hay dùng sandbox của cổng thật; luồng OTP/3-D Secure/QR chỉ được nêu ở mức tên — [TK §7](../../docs/03-y-tuong-trien-khai.md).
- Không có tài liệu nào nói Admin panel gọi thẳng Booking/Payment hay qua Administration; khuyến nghị đi qua Administration để lọc trường theo vai trò.

---

## 5. Mỗi module cần External API nào

### Takeaway
Chỉ 5 module cần dịch vụ ngoài thật sự: Access (OTP SMS, Google), Notification (FCM), AI (nhà cung cấp LLM), Social (kho đối tượng + CDN + chuyển mã video), Search (mô hình embedding; vision cho B2); Administration cần API kiểm duyệt/phát hiện khuôn mặt; Payment dùng **cổng giả lập**; Booking không được nối hệ thống thật nào.

### Cited Findings
- Đăng nhập bằng SĐT (OTP) hoặc Google — [TK §7](../../docs/03-y-tuong-trien-khai.md).
- Thông báo đẩy qua FCM; đồng bộ thời gian thực bằng WebSocket hoặc SSE — [PC §4.7](../../team/01-phan-cong-nghien-cuu-module.md).
- So sánh các LLM về chất lượng tiếng Việt và chi phí; ghi log chi phí từng cuộc — [PC §4.4](../../team/01-phan-cong-nghien-cuu-module.md); câu hỏi mentor "ban tổ chức có cấp khóa API hay ngân sách cho mô hình ngôn ngữ không?" — [TK §15 Q7](../../docs/03-y-tuong-trien-khai.md); chi phí vận hành trợ lý phải đo và báo cáo — [ĐB §7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Kiểm duyệt tự động bằng API hoặc AI; xử lý ảnh có mặt người lạ và thông tin cá nhân — [PC §4.5](../../team/01-phan-cong-nghien-cuu-module.md); [ĐB §6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Kho đối tượng + CDN (video HLS); pgvector — [TK §10.3](../../docs/03-y-tuong-trien-khai.md); video HLS tự hạ chất lượng, ảnh nhiều kích cỡ — [TK §10.1](../../docs/03-y-tuong-trien-khai.md).
- Tìm theo nghĩa bằng embedding và vector index cho tiếng Việt; tìm bằng ảnh (B2) — [PC §4.1](../../team/01-phan-cong-nghien-cuu-module.md); dùng chung một mô hình embedding — [PC §5.1](../../team/01-phan-cong-nghien-cuu-module.md).
- Thanh toán giả lập; không kết nối hệ thống khách hàng thật của công ty; dùng bộ dữ liệu sản phẩm và khách hàng mô phỏng được cấp — [ĐB §7, §8](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Thanh toán: chỉ dùng trung gian được cấp phép (định hướng pháp lý ngành) — [KT §8](../../docs/01-kien-thuc-nganh-OTA.md).
- Hình ảnh và nội dung phải có quyền sử dụng — [ĐB §7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Hình đích chỉ ghi chung "External APIs" — [SĐ](so-do-module.png).

### Inferences

| Module | External API | Dùng cho | Ghi chú |
|---|---|---|---|
| Access | Nhà cung cấp OTP SMS; Google Sign-In | Đăng nhập SĐT, Google | [Đề xuất] Có thể dùng một dịch vụ xác thực làm cả hai; vẫn phải giữ `users` riêng vì ngày sinh/tuổi là dữ liệu nghiệp vụ |
| Notification | FCM; (tùy chọn) email | Đẩy B3, B6, bài bị ẩn, kết quả đặt chỗ | Realtime WebSocket/SSE là hạ tầng nội bộ, dùng Redis pub/sub (Cache layer) |
| Administration | API kiểm duyệt văn bản/ảnh; phát hiện khuôn mặt/thông tin cá nhân trong ảnh; LLM (qua cổng của AI) | Sàng lọc khi đăng, gợi ý lý do, riêng tư ảnh | Người quyết định cuối (TK §8.2) |
| AI | Nhà cung cấp LLM (có gọi công cụ) | A12, B1, phân tích câu hỏi | Đo token → VNĐ mỗi lượt; hạn mức 20/50 tin/ngày |
| Booking | **Không** (dữ liệu được cấp) | — | [Đề xuất tùy chọn] API bản đồ/địa điểm chỉ để hiện bản đồ trên trang chi tiết; không bắt buộc theo đặc tả |
| Payment | Cổng thanh toán **giả lập** (tự viết hoặc sandbox) | OTP/3-D Secure/QR giả lập, callback | [Đề xuất] Cổng giả lập có chế độ "tiêm lỗi" (trễ, timeout, callback trùng) để chứng minh không trừ tiền hai lần (TK §12) |
| Social | Kho đối tượng + CDN; chuyển mã video sang HLS và tạo ảnh nhiều cỡ; Android App Links (`assetlinks.json` trên domain web) | A2, A10 (deep link), B5 | Tải lên có thể tiếp tục khi mạng yếu |
| Search | Mô hình embedding đa ngôn ngữ/tiếng Việt (dùng chung với AI); mô hình embedding ảnh cho B2 | A1, A3, A6, A7, A9, B2 | Vector index đặt trong PostgreSQL (pgvector), không cần dịch vụ ngoài |

### Gaps
- Chưa biết ban tổ chức có cấp khóa/ngân sách LLM không (TK §15 Q7) — quyết định nhà cung cấp LLM và embedding phụ thuộc vào đó.
- Không tài liệu nào chỉ định nhà cung cấp OTP SMS, API kiểm duyệt, phát hiện khuôn mặt hay CDN cụ thể; không có yêu cầu API bản đồ trong đặc tả (chỉ xuất hiện ở hướng phát triển "Đi ngay", TK §16).
- Hình SĐ không phân biệt External API theo module.
