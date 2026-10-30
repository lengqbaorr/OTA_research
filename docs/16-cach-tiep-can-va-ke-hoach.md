# 16. Cách tiếp cận giải pháp, phần mở rộng & kế hoạch 4 mốc

> Đề bài mốc 1 yêu cầu trả lời: *"Chọn cách làm cho từng sản phẩm ra sao, vì sao?"* và *"Thống nhất từ tuần 1 cách các phần trao đổi dữ liệu với nhau"*.
> Tài liệu này ở mức **phương án & lý do**. Lựa chọn công nghệ cụ thể là **đề xuất để đội chốt** dựa trên kỹ năng thực tế của các thành viên.

---

## 1. Nguyên tắc thiết kế rút ra từ đề bài

1. **Một backend, một cơ sở dữ liệu, nhiều giao diện.** App, web, trợ lý và admin đều gọi chung một bộ API. Không giao diện nào giữ dữ liệu riêng (ràng buộc "một tài khoản, một nguồn dữ liệu").
2. **Server là nguồn sự thật cho tiền và chỗ.** Mọi kiểm tra (tuổi, chỗ trống, giá, chống trùng thanh toán, quyền) đều ở server, không tin giao diện.
3. **Offline-first cho thao tác viết trên app.** Bài nháp, bình luận, tương tác được lưu trên máy trước rồi gửi khi có mạng.
4. **Nhẹ cho máy yếu, mạng yếu.** Ảnh nhiều kích cỡ, video nén nhiều mức chất lượng, phân trang, tải trước có kiểm soát.
5. **AI không phải nguồn sự thật.** Tìm kiếm ngữ nghĩa và trợ lý chỉ *diễn giải* và *xếp hạng*; dữ liệu hiển thị lấy từ hệ thống.
6. **Mọi thứ đo được.** Ghi nhận sự kiện (xem, thích, lưu, tìm, đặt, chi phí trợ lý) từ ngày đầu để phục vụ xu hướng, cá nhân hóa và báo cáo mục 9.

---

## 2. Kiến trúc khái niệm

```
┌──────────────┐  ┌──────────────┐  ┌───────────────┐
│ Android App  │  │  Web OTA     │  │ Trang quản trị │
│ (offline-    │  │ (SSR cho     │  │ (RBAC: quản    │
│  first)      │  │  link chia sẻ)│  │  trị/kiểm duyệt)│
└──────┬───────┘  └──────┬───────┘  └───────┬───────┘
       │  REST/JSON + kênh realtime (WebSocket/SSE)   │
       └─────────────────┼──────────────────┘
                         ▼
┌──────────────────────────── BACKEND (modular monolith) ─────────────────────────────┐
│ Identity & Auth │ Catalog & Inventory │ Pricing & Quote │ Booking & Hold │ Payment Sim │
│ Posts & Media   │ Interactions        │ Moderation      │ Trending       │ Personalization │
│ Search (hiểu tiếng Việt người trẻ + ngữ nghĩa) │ Assistant (LLM + tools + validator)      │
│ Notifications   │ Event log / Analytics │ Audit log                                       │
└───────┬──────────────────┬───────────────────┬───────────────────┬──────────────────┘
        ▼                  ▼                   ▼                   ▼
  CSDL quan hệ        Bộ nhớ đệm &          Chỉ mục tìm kiếm     Lưu trữ media +
  (giao dịch: đơn,    bộ đếm, hàng đợi      (từ khóa + vector)   CDN (ảnh/video
  thanh toán, chỗ)    sự kiện                                    nhiều kích cỡ)
                                         ▲
                              Dịch vụ mô hình ngôn ngữ (bên ngoài) – qua lớp đo chi phí
```

**Vì sao modular monolith (một khối, chia module rõ)**: đội nhỏ, 4 tuần, cần giao dịch chặt chẽ giữa giữ chỗ – thanh toán – đơn. Một khối triển khai giảm lỗi phân tán và công vận hành. Các module tách rõ ranh giới để sau này có thể tách dịch vụ (ví dụ trợ lý, xử lý media) nếu cần.

---

## 3. Phương án cho từng sản phẩm

### 3.1. Ứng dụng Android

| Phương án | Ưu | Nhược | Hợp khi |
|---|---|---|---|
| **Native (Kotlin + Jetpack Compose)** | Hiệu năng tốt nhất trên máy yếu; công cụ offline/nền chuẩn của Android (lưu cục bộ, tác vụ nền tự gửi lại khi có mạng, App Links); đo hiệu năng dễ | Không chia sẻ code với web | Đội có người biết Kotlin. **Khuyến nghị mặc định** vì A1 đặt nặng máy yếu/mạng yếu |
| **Flutter** | Hiệu năng khá; giao diện nhất quán; có thể dùng sau này cho iOS | Kích thước app lớn hơn; tác vụ nền/offline cần thêm thư viện | Đội mạnh Dart/Flutter |
| **React Native** | Chia sẻ logic/ngôn ngữ với web (TypeScript) | Cần tối ưu kỹ cho máy yếu; cuộn danh sách dài dễ giật nếu làm ẩu | Đội mạnh React, ít người Android |
| PWA thay app | Không phải làm app riêng | **Không đáp ứng** "Ứng dụng Android" như đề bài; deep link vào app không có ý nghĩa | Không chọn |

**Các kỹ thuật bắt buộc (bất kể chọn gì)**:
- **Bảng tin**: phân trang dạng con trỏ (cursor) để không lặp bài khi bảng tin thay đổi; server nhớ "đã xem" theo phiên.
- **Ảnh**: nhiều kích cỡ (thumbnail ~20–60KB), định dạng nén hiện đại, tải lười.
- **Video**: phát chất lượng thích ứng theo mạng, chỉ tự phát khi đang nhìn thấy, chỉ tải trước đoạn đầu.
- **Lưu cục bộ** cho bảng tin đã xem (nền tảng cho B4), nháp bài/bình luận.
- **Hàng đợi gửi lại**: nháp và bình luận được gửi tự động khi có mạng, có mã chống trùng.
- **Tải video lên theo từng phần** (resumable): mất mạng thì tiếp tục từ chỗ dừng (A2).
- **Đăng nhập giữa chừng**: lưu "ý định" (đang đặt gì, đang ở bài nào) rồi khôi phục sau khi đăng nhập (A4, A10).

### 3.2. Trang web OTA & Trang quản trị

| Phương án | Ưu | Nhược |
|---|---|---|
| **Framework web có render phía server (SSR)**, giao diện responsive | Link chia sẻ có ảnh xem trước khi dán lên mạng xã hội; tải nhanh trên điện thoại; tốt cho SEO | Cần hiểu cơ chế SSR |
| SPA thuần (render phía trình duyệt) | Đơn giản | Link chia sẻ không có ảnh xem trước tốt; tải đầu chậm hơn trên điện thoại |

**Khuyến nghị**: dùng framework có SSR cho web người dùng. Admin có thể dùng cùng framework (ứng dụng riêng, tên miền/đường dẫn riêng) để tái dùng thành phần giao diện và mô hình dữ liệu. Admin **không cần SSR**.

### 3.3. Backend & dữ liệu

| Thành phần | Đề xuất | Vì sao |
|---|---|---|
| CSDL chính | **CSDL quan hệ có giao dịch** (ví dụ PostgreSQL) | Giữ chỗ, đơn, thanh toán cần giao dịch ACID và ràng buộc duy nhất (chống trùng, chống bán vượt) |
| Bộ đếm, hàng đợi, giới hạn tần suất | Bộ nhớ đệm khóa–giá trị (ví dụ Redis) | Đếm nhanh, phân phối sự kiện realtime, giới hạn số tin nhắn trợ lý |
| Tìm kiếm | **Kết hợp từ khóa + ngữ nghĩa (vector)**, ví dụ phần mở rộng vector của PostgreSQL hoặc công cụ tìm kiếm chuyên dụng | A3/A6 cần cả khớp chính xác (tên địa điểm) lẫn hiểu ý ("không đông" ~ "vắng") |
| Media | Lưu trữ đối tượng + CDN, xử lý nén/chuyển mã ở nền | Mạng yếu, máy yếu |
| Realtime | WebSocket hoặc Server-Sent Events | "Thấy ngay" giữa app và web |
| Ngôn ngữ backend | Chọn theo kỹ năng đội (TypeScript/Node, Kotlin/Java, Python, Go đều được) | Quan trọng là làm đúng giao dịch và idempotency |

**Ba cơ chế "sống còn" cho đặt chỗ** (giải thích được nguyên lý khi bị hỏi):
1. **Giữ chỗ nguyên tử**: trừ tồn kho bằng một câu lệnh có điều kiện ("chỉ trừ nếu còn ≥ 1") trong giao dịch. Hai người tranh chỗ cuối thì đúng một người thành công (R1, R2).
2. **Idempotency key**: mỗi lần bấm "Thanh toán" mang một mã duy nhất cho phiên đặt đó. Server và cổng giả lập chỉ xử lý mã đó một lần (P1–P5).
3. **Máy trạng thái đơn**: Giữ chỗ → Chờ thanh toán → Đã thanh toán → Đã xác nhận / Thất bại → Hoàn tiền. Chuyển trạng thái chỉ theo đường hợp lệ. Đơn lệch trạng thái (đã trả mà không có chỗ, trả hai lần, lộ trình một phần) được A16 phát hiện.

### 3.4. Tìm bằng lời của mình (A3, A6) — pipeline

```
Câu người dùng
  → Chuẩn hóa: bỏ dấu song song (giữ cả bản có dấu), từ điển teencode/viết tắt
    ("k"→không, "cn"→chỗ nào, "củ"→triệu, "4 đứa"→4 người), emoji → nghĩa (🏖️→biển)
  → Hiểu ý (mô hình ngôn ngữ nhỏ hoặc luật): trích điều kiện có cấu trúc
    {điểm đến, ngày, số người, ngân sách (mỗi người | cả nhóm), gu: [yên tĩnh, hoàng hôn, view biển]}
  → Lọc cứng: ngân sách, sức chứa, còn chỗ, bài đang hiển thị
  → Xếp hạng lai: điểm từ khóa + điểm ngữ nghĩa (vector) + gu cá nhân + xu hướng
  → Ngưỡng liên quan: dưới ngưỡng thì KHÔNG hiện
      → "Chưa có chỗ phù hợp" + gợi ý nới điều kiện (tăng ngân sách, đổi ngày, bỏ một tiêu chí)
```

### 3.5. Trợ lý (A12)

- **Kiến trúc**: mô hình ngôn ngữ + **gọi công cụ** (tìm địa điểm, kiểm tra giá/chỗ, tìm bài đăng, đọc gu người dùng) + **validator** sau khi sinh (tài liệu 14, mục 4.1).
- **Bộ phân loại an toàn** chạy trước: ngoài phạm vi / nguy hiểm / injection → trả lời từ chối mẫu, thân thiện, **không tốn lượt gọi mô hình lớn**.
- **Không có công cụ "đặt chỗ"**: trợ lý không có khả năng kỹ thuật để tự đặt (A12 "không tự đặt chỗ thay người dùng"). Nó chỉ trả về thẻ có nút để người dùng tự bấm.
- **Lớp đo chi phí** bao quanh mọi lần gọi mô hình: token, thời gian, mô hình nào, chi phí → cung cấp dữ liệu cho báo cáo và B7.
- **Chọn mô hình**: đánh giá 2–3 mô hình trên bộ câu hỏi thử (độ chính xác tiếng Việt kiểu người trẻ, tỷ lệ gọi công cụ đúng, chi phí, độ trễ). Chọn dựa trên số đo (đây là nội dung trình bày mốc 1: "thử nghiệm cách làm").

### 3.6. Xu hướng & cá nhân hóa (A1, A7, A15)

- Tính điểm xu hướng theo lịch **mỗi 5 phút** (tài liệu 14, mục 1) từ nhật ký sự kiện.
- **Hồ sơ gu** của người dùng: vector sở thích cập nhật từ thích/lưu/bỏ qua/thời gian dừng trên **cả app và web**, kèm ngân sách đã nói.
- **Bảng tin = trộn**: ~60% hợp gu, ~20% xu hướng chung, ~20% khám phá; lọc bài đã xem; đa dạng điểm đến.
- **Lý do ngắn** cho mỗi bài/gợi ý sinh từ tín hiệu thật ("Vì bạn đã lưu 2 bài về Hội An", "32 người lưu trong 24h").

### 3.7. Trang quản trị (A13–A16)

- **RBAC**: vai trò *Quản trị* (mọi thứ, gồm đơn và thanh toán) và *Kiểm duyệt* (hàng chờ, bài, bình luận, xu hướng; **không** có quyền gọi API thanh toán). Kiểm tra quyền ở server cho từng API.
- **Audit log** chỉ ghi thêm (append-only): ai, lúc nào, hành động, đối tượng, lý do, gợi ý AI.
- **Bộ phát hiện bất thường đơn** (A16): chạy định kỳ, so sánh đơn ↔ giao dịch ↔ tồn kho, đánh dấu "trả 2 lần", "đã trả không có chỗ", "lộ trình một phần".

---

## 4. Mô hình dữ liệu chung (thống nhất từ tuần 1)

Đề bài mục 11: *"Thống nhất từ tuần 1 cách các phần trao đổi dữ liệu với nhau, để không phải làm lại."*

### 4.1. Thực thể chính

| Nhóm | Thực thể | Ghi chú quan trọng |
|---|---|---|
| **Người dùng** | `User` (ngày sinh **bắt buộc, khóa sửa**, cờ đủ 18), `Profile` (gu, ngân sách), `Device`, `AnonymousSession` | Ẩn danh gộp vào User khi đăng nhập |
| **Nhân viên** | `StaffUser`, `Role` (admin, moderator) | Tách khỏi User |
| **Sản phẩm** | `Destination`, `Property` (khách sạn/resort), `RoomType`, `Attraction` (VinWonders, golf…), `TicketType`, `FlightRoute` / `Flight`, `Combo` (+ `ComboItem`) | Từ bộ dữ liệu được cấp |
| **Tồn kho & giá** | `Inventory` (sản phẩm × ngày × số lượng còn), `Price` (sản phẩm × ngày), `PriceQuote` (báo giá có hạn, gắn với phiên giữ chỗ) | Giá đổi → quote cũ hết hiệu lực |
| **Đặt chỗ** | `Cart` / `BookingSession`, `Hold` (hết hạn 10 phút), `Booking`, `BookingItem` (trạng thái riêng từng mục), `Payment` (idempotency_key duy nhất), `Refund` | Booking có `source_post_id` nếu đặt theo lộ trình (A16) |
| **Chuyến đi** | `Trip` (gom các Booking của một chuyến) | Nguồn cho "Chuyến đi của tôi" và lộ trình bài đăng |
| **Bài đăng** | `Post` (gắn `booking_id` đã hoàn thành, nhãn mô phỏng), `PostVersion`, `PostMedia` (nhiều kích cỡ, cờ đã làm mờ), `Itinerary` + `ItineraryItem` (**day_offset**, product_id, ghi chú, cờ hiển thị; **không lưu ngày thật/giá đã trả**) | Ngày tương đối → dời ngày + riêng tư |
| **Tương tác** | `Reaction` (duy nhất theo user × post), `Comment` (cây trả lời), `Save`, `Share`, `Report` | Ràng buộc duy nhất chống đếm trùng |
| **Kiểm duyệt** | `ModerationItem` (hàng chờ, độ ưu tiên, nhãn AI), `ModerationAction` (audit) | — |
| **Xu hướng** | `InteractionEvent` (nhật ký sự kiện), `TrendScore` (post/place × thời điểm, phân rã theo tín hiệu), `TrustScore` (user), `TrendFlag` (nghi đẩy ảo) | — |
| **Trợ lý** | `Conversation`, `Message`, `AssistantMemory` (người dùng xem/sửa được), `AssistantUsage` (token, chi phí) | — |
| **Thông báo** | `Notification` | Bị ẩn bài, (B3, B6) |

### 4.2. Hợp đồng API (thống nhất quy ước)

- Mọi API có phiên bản (`/v1/...`). Dữ liệu JSON, ngày giờ theo chuẩn ISO 8601 có múi giờ, tiền là **số nguyên VNĐ** (không dùng số thực).
- **Phân trang bằng con trỏ** (cursor) cho bảng tin, bình luận, tìm kiếm.
- **Idempotency-Key** bắt buộc cho: tạo giữ chỗ, thanh toán, đăng bài, bình luận, tương tác.
- Lỗi có mã nghiệp vụ thống nhất (ví dụ `PRICE_CHANGED`, `SOLD_OUT`, `HOLD_EXPIRED`, `UNDER_18`, `NOT_TRAVELED`, `RATE_LIMITED`) để app và web hiển thị cùng thông điệp.
- **Sự kiện realtime** có tên thống nhất (ví dụ `post.reaction.updated`, `booking.status.changed`, `post.hidden`).
- Viết đặc tả API (ví dụ OpenAPI) **trước khi** làm giao diện, để app, web, admin làm song song với dữ liệu giả.

---

## 5. Lựa chọn phần mở rộng (Phần B) và lý do

Đề bài: *"Đội tự chọn làm phần nào và phải giải thích vì sao chọn."* Tiêu chí chọn: (1) phục vụ persona nào, (2) tận dụng được hạ tầng đã làm ở phần A đến đâu (chi phí thêm thấp), (3) rủi ro, (4) giúp chứng minh mục 9.

| Mã | Tính năng | Persona | Tận dụng hạ tầng A | Rủi ro | Đề xuất |
|---|---|---|---|---|---|
| **B7** | Theo dõi trợ lý (admin) | Đội, kiểm duyệt | Rất cao: đề bài đã bắt **đo chi phí trợ lý** (mục 7) và chứng minh "không bịa" (mục 9). B7 chủ yếu là hiển thị dữ liệu đã có | Thấp | **Chọn – ưu tiên 1** |
| **B1** | Tóm tắt bài đăng "50 người đã đi nói gì" | **Hà** (đọc kỹ, không tin bài quá đẹp) | Cao: dùng lại trợ lý + tìm kiếm + validator trích dẫn | Trung bình (phải chỉ dựa trên bài có thật, có trích dẫn) | **Chọn – ưu tiên 2** |
| **B4** | Dùng khi mất mạng | Khang, mọi người dùng mạng yếu | Cao: A1/A2/A10 đã yêu cầu cache và offline cho nháp/bình luận | Thấp–TB | **Chọn – ưu tiên 3** |
| B6 | Thông báo tương tác | Linh (người đăng) | TB: đã có hệ thống thông báo cho việc ẩn bài (A14) | Thấp | Làm nếu còn thời gian (ưu tiên 4) |
| B2 | Tìm bằng hình ảnh | Khang, Vy | Thấp: cần thêm mô hình ảnh và chỉ mục ảnh | Cao (chất lượng, "không có thì nói thật") | Không chọn ở mốc 3; nêu là hướng tiếp theo (mốc 4) |
| B3 | Báo giảm giá | Vy | TB | Thấp, nhưng giá trong dữ liệu mô phỏng ít thay đổi nên khó demo thật | Không chọn |
| B5 | Chia sẻ thành hình ảnh đẹp | Linh | TB | Thấp, nhưng ít giá trị chứng minh mục 9 | Không chọn |

**Lý do tổng hợp** (để trả lời "Vì sao chọn những phần mở rộng này?" ở mốc 3): B7 + B1 + B4 đều là **phần nối dài tự nhiên của phần A**, chi phí thêm thấp, và **tăng sức thuyết phục cho các tiêu chí đánh giá**: B7 chứng minh trợ lý không bịa và minh bạch chi phí; B1 tăng niềm tin cho người dùng kỹ tính bằng nội dung có thật; B4 củng cố yêu cầu khó nhất về mạng yếu. B2 hấp dẫn nhưng rủi ro cao trong 4 tuần nên để làm hướng phát triển tiếp.

---

## 6. Kế hoạch theo 4 mốc (bám sát mục 10 đề bài)

| Mốc | App Android | Web | Trợ lý | Admin | Đo & tài liệu | Trả lời được |
|---|---|---|---|---|---|---|
| **1** | Bảng tin xu hướng (v1: xu hướng chung + cuộn không lặp), trang chi tiết | Tìm bằng lời (v1), gợi ý (v1), trang chi tiết | **Thử 2–3 cách/mô hình** trên bộ câu hỏi thử v1 (≥ 50 câu), chọn hướng | Đăng nhập, phân quyền 2 vai trò | Môi trường đo chuẩn; bản đề xuất cách đo (tài liệu 15); mô hình dữ liệu & API (mục 4) chốt | Hiểu bài toán (tài liệu 13); cách làm từng sản phẩm & vì sao (tài liệu này); đề xuất cách đo |
| **2** | Đăng bài (đã đi thật, lộ trình, nháp offline), tương tác, tìm trong bài đăng, đặt nhanh | Xem & tương tác bài, đặt chỗ đầy đủ 4 loại, chặn < 18, gợi ý sau khi đặt | Gợi ý địa điểm (có validator) | Kiểm duyệt (hàng chờ, audit, ẩn lan truyền), đơn đặt chỗ | **Chốt** chỉ số; đo lần 1 (bảng tin, tìm kiếm, P1–P8, R1–R2, T1–T5) | Đề xuất câu hỏi mở về xu hướng, bài đăng, đặt chỗ (tài liệu 14), **thể hiện trong sản phẩm** |
| **3** | Đặt ngay theo lộ trình, Chuyến đi của tôi, B4 | Đặt ngay theo lộ trình, Chuyến đi của tôi | Đề xuất bài đăng; chạy trên app + web; nhớ hội thoại đa thiết bị; B1 | Theo dõi xu hướng + đẩy ảo; B7 | Đo đầy đủ theo mục 9; thử với ≥ 5 người ngoài đội; **thứ Năm ngừng làm tính năng** | Câu hỏi mở về lộ trình & trợ lý; kết quả đo; vì sao chọn B7/B1/B4 |
| **4** | — | — | — | — | Báo cáo cuối kỳ | Nếu có thêm thời gian: B2 tìm bằng hình ảnh, B6, tối ưu chi phí trợ lý, iOS (ngoài phạm vi hiện tại) |

### Gợi ý phân vai (đội tự điều chỉnh theo số người)

| Vai trò phụ trách chính | Phần việc |
|---|---|
| Backend – Đặt chỗ & thanh toán | Catalog, tồn kho, giữ chỗ, báo giá, thanh toán giả lập, đơn, A16 |
| Backend – Nội dung & xu hướng | Bài đăng, media, tương tác, kiểm duyệt, xu hướng, cá nhân hóa, realtime |
| Android | App (A1–A5, A10–A12 phía app), offline, hiệu năng máy yếu |
| Web & Admin | Web (A6–A11), Admin (A13–A16) |
| Tìm kiếm & Trợ lý | Chuẩn hóa tiếng Việt, tìm kiếm lai, trợ lý, validator, đo chi phí, B1, B7 |
| (Kiêm nhiệm) QA & Đo lường | Bộ test mục 15, môi trường đo, báo cáo |

Theo đề bài, **mỗi người phải hiểu và trả lời được về phần của người khác**. Nên có buổi "giải thích chéo" 30 phút trước mỗi demo, và **đổi Leader sau mỗi buổi demo**.

---

## 7. Rủi ro dự án (4 tuần) & cách giảm

| Rủi ro | Mức | Giảm thiểu |
|---|---|---|
| Không có bài đăng → bảng tin, xu hướng, tìm kiếm không có gì để demo | Cao | Tuần 1 tạo bộ bài mô phỏng (gắn nhãn) gắn với đơn mô phỏng đã hoàn thành (giả định G1) |
| Luồng đặt theo lộ trình (A11) phức tạp nhất, làm ở mốc 3 | Cao | Thiết kế mô hình dữ liệu lộ trình (day_offset) và giữ chỗ nhiều mục **ngay từ mốc 1–2**; mốc 3 chỉ ghép giao diện |
| Trợ lý bịa hoặc tốn chi phí | Cao | Validator + bộ câu hỏi thử tự động từ mốc 1; giới hạn tần suất; đo chi phí từ lần gọi đầu tiên |
| Hiệu năng máy yếu phát hiện muộn | TB | Mua/mượn máy chuẩn từ tuần 1; đo mỗi tuần |
| Các phần không khớp dữ liệu | TB | Đặc tả API + dữ liệu giả dùng chung từ tuần 1 |
| Yêu cầu thay đổi giữa chừng (đề bài đã báo trước) | TB | Kiến trúc module, ghi lại quyết định (decision log), hỏi sớm theo mẫu đề bài |
| Ảnh/video không có quyền sử dụng | TB | Chỉ dùng nguồn có giấy phép rõ ràng, ghi nguồn trong dữ liệu mô phỏng |
