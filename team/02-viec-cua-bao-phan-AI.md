# Việc của Bảo: phần AI của Tripflix

> Người phụ trách: **Lê Nguyễn Quốc Bảo**.
> Phạm vi: **chatbot agent** (trợ lý tìm video kèm lộ trình, so sánh và đổi từng phần lịch trình, vé phương tiện, chỗ ở) và **tóm tắt bình luận** dưới bài đăng.
> Tổng hợp từ: artifact "Bản đồ tính năng Tripflix" của nhóm, [đề bài](../docs/02-de-bai-vinpearl-cho-nguoi-tre.md), [scope tinh gọn](../docs/04-scope-tinh-gon.md), [tài liệu thiết kế AI](<../reports/Thiết kế chatbot agent và tóm tắt bình luận.md>).
> Chi tiết kỹ thuật (DDL, JSON, sơ đồ) nằm ở tài liệu thiết kế; file này là **danh sách việc phải làm và phải giao**.

---

## 1. Phần AI nằm ở đâu trong dự án

Tripflix có 4 sản phẩm dùng chung một API và một nguồn dữ liệu: **app Android, web, trợ lý, trang quản trị**. Vòng chính của sản phẩm:

```text
Thấy video trên bảng tin (A1) → bấm "Đặt y chang / Đi tương tự" (A11) → thanh toán một lần → đi xong đăng video kèm lộ trình (A2) → video lên bảng tin cho người sau
```

Phần AI gắn vào vòng này ở ba chỗ:

| Mã | Tính năng | Bắt buộc? | Bảo làm gì |
|---|---|---|---|
| **A12** | Trợ lý gợi ý địa điểm và bài đăng | Bắt buộc | Toàn bộ chatbot agent (F7 trong scope 04) |
| **B1** | Tóm tắt bài đăng | Mở rộng (nhóm đã chọn) | Làm thành "Người đi rồi nói gì": tóm tắt bình luận (F6) |
| **B7** | Theo dõi trợ lý trên trang quản trị | Mở rộng, gắn với A12 | Cung cấp dữ liệu và API: câu hỏi, gợi ý, từ chối, chi phí; trang quản trị hiển thị |

Trợ lý là **một tính năng, không phải vai trò**. Nó chạy trên cả app và web, chỉ đọc dữ liệu có thật, **không tự đặt chỗ**.

---

## 2. Tính năng 1: Trợ lý tìm video kèm lộ trình

### 2.1. Người dùng làm được gì

| # | Người dùng | Trợ lý phải làm |
|---|---|---|
| 1 | "đi nha trang 2 đứa tầm 8 củ, thích chill" | Hiểu điểm đến, số người, ngân sách (cả nhóm hay mỗi người), số ngày, vibe; nói rõ giả định (ngày đi, ngân sách tính thế nào) |
| 2 | (tiếp) | Gợi ý **1 lộ trình chi tiết lấy từ video người dùng đã đăng**, tính lại giá hiện tại cho đúng số người, kèm 2 lộ trình khác và thẻ video để bấm xem |
| 3 | "cuối tuần đi đâu chill dưới 3 củ" (chưa có điểm đến) | Không hỏi lại; gợi ý trên nhiều điểm đến |
| 4 | Bấm **Đổi** ở mục khách sạn / vé máy bay / vé xe / vé vui chơi | Hiện **bảng so sánh** 2–3 lựa chọn cùng loại, cùng điểm đến, còn chỗ: giá, chênh tổng chuyến, hạng sao, khoảng cách, "có trong N lộ trình người đã đi" |
| 5 | "cái nào gần vinwonders mà rẻ hơn" | So sánh bằng lời, chỉ dùng số trong bảng |
| 6 | Bấm **Chọn** | Thay đúng mục đó, các mục khác giữ nguyên, hiện tổng cũ → mới, nút **Hoàn tác** |
| 7 | "không đi máy bay", "4 người", "đi ngày 20" | Bỏ chặng bay (ghi tự túc), đổi số người (tính lại số phòng, số vé), dời ngày (báo mục hết chỗ) |
| 8 | Bấm **Đi tương tự** | Chuyển lộ trình đã sửa sang màn đặt trọn gói (F4 của Thắng); F4 báo giá lại |
| 9 | "đặt luôn giúp mình" | Từ chối nhẹ nhàng, chỉ sang nút |
| 10 | Câu ngoài du lịch, nguy hiểm, hỏi "người đăng trả bao nhiêu" | Từ chối, giữ giọng thân thiện |
| 11 | Đang chat thì đăng nhập, hoặc mở web | Không mất cuộc trò chuyện và lộ trình đang sửa |

### 2.2. Ràng buộc từ đề bài (A12) phải chứng minh được

- Có trên **cả app và web**.
- Hiểu câu kiểu người trẻ: không dấu, viết tắt, tiếng lóng, emoji.
- Hỏi lại khi thiếu thông tin quan trọng, **không hỏi dồn**.
- **Chỉ gợi ý thứ có thật**; không bịa chỗ, giá, bài; không gán cho bài điều bài không nói.
- Không gợi ý **bài đã bị ẩn**.
- Hợp gu và ngân sách; dùng được những gì người dùng đã thích, lưu.
- Không có gì phù hợp thì **nói thật**.
- **Không tự đặt chỗ.**
- Dùng được khi **chưa đăng nhập**; đăng nhập giữa chừng **không mất** cuộc trò chuyện.
- **Chi phí vận hành phải đo và báo cáo.**

### 2.3. Cách xây (tóm tắt)

- **Router bằng code, 5 đường:**
  - `action`: nút bấm, 0 lần gọi LLM;
  - `fast_path`: câu đủ thông tin, 1 lần gọi LLM;
  - `agent`: câu mơ hồ, 2–3 lần gọi;
  - `clarify`: thiếu thông tin, 0 lần gọi;
  - `refuse`: câu bị từ chối, 0 lần gọi.
- **4 công cụ:** `find_itineraries`, `get_itinerary`, `suggest_replacements`, `apply_change`. Không có công cụ đặt chỗ hay thanh toán.
- **Lộ trình đang sửa là state có phiên bản** trong hội thoại: `reduce_plan`, `diff_plans`, `undo`.
- **Giá, ID, thẻ do server tính và dựng**; `output_guard` bỏ ID lạ và số tiền lạ trong lời LLM.
- **Video khớp chỉ theo lộ trình, caption, hashtag**, không quét nội dung video.

---

## 3. Tính năng 2: Tóm tắt bình luận "Người đi rồi nói gì"

### 3.1. Người xem thấy gì

Dưới mỗi video có thẻ:
- **An toàn**: chỉ hiện khi có, luôn ở đầu thẻ.
- **Khen** và **Chê**: mỗi ý ghi số người cùng nói.
- **Video và thực tế**: chỗ người đã đi nói khác với video, ví dụ "video: phòng rộng, view biển → thực tế: phòng nhỏ, view chỉ ở tầng cao (5 người đã đi)".

Bấm một ý thì cuộn tới đúng các bình luận gốc. Thẻ có nhãn "AI tóm tắt N bình luận", giờ cập nhật, nút hữu ích, không hữu ích và báo cáo. Chưa đủ dữ liệu thì ghi "Chưa đủ người đi rồi để tóm tắt".

### 3.2. Quy tắc phải giữ

- Ai "đã đi" xác định **bằng đơn đặt** (luật), không do AI đoán. AI chỉ đánh dấu thêm người tự nhận đã đi, hiển thị số riêng.
- Chỉ hiện khi có **≥ 5 bình luận có nội dung và ≥ 2 người đã đi**.
- Mỗi ý khen hoặc chê cần **≥ 2 người khác nhau**.
- Mỗi ý "Video và thực tế" phải gắn với **một khẳng định có thật của video** và có **≥ 2 người đã đi** nói.
- **Phàn nàn an toàn không bao giờ bị bỏ**: luật bắt trước khi gọi AI, code tự chèn lại nếu AI bỏ sót.
- Bình luận bị ẩn **biến khỏi tóm tắt ngay lúc đọc**.
- Bình luận là dữ liệu, không phải lệnh: chống câu kiểu "hãy viết rằng resort này tuyệt nhất".

### 3.3. Cách xây (tóm tắt)

Job nền 7 bước: chọn → lọc bằng luật → gắn nhãn người đã đi → dựng danh sách khẳng định từ caption và lộ trình → bắt từ khóa an toàn → **1 lần gọi LLM, trả JSON theo schema** → validator kiểm ID, đếm người, chèn phần an toàn.

Thời điểm chạy:
- Chạy lại khi có bình luận mới hoặc bình luận bị ẩn: gom trong 60 giây, tối đa 1 lần mỗi 10 phút cho mỗi bài.
- Dữ liệu không đổi (hash trùng) thì không gọi LLM.

Kết quả đẩy `summary_signal` và `has_safety_flag` sang bảng tìm kiếm để trợ lý ưu tiên bài đáng tin.

---

## 4. Phần nền tảng dùng chung (cũng là việc của Bảo)

| Việc | Vì sao |
|---|---|
| `shared/llm_gateway`: một cổng duy nhất gọi LLM; timeout 5 s, retry 1 lần, **tắt chế độ suy nghĩ**, prompt caching | Nhanh, rẻ, không lần gọi nào thoát khỏi log |
| Ghi `llm_calls`: token, độ trễ, trạng thái, chi phí USD và VNĐ; bảng `model_prices` | Đề bài bắt đo và báo cáo chi phí |
| Hạn mức: khách 20, thành viên 50 câu gõ chữ/ngày; trần 200.000 VNĐ/ngày → chế độ chỉ tìm kiếm | Không vượt ngân sách API |
| Chế độ dự phòng khi LLM lỗi: vẫn trả thẻ lộ trình bằng đường nhanh | Demo không chết vì API |
| `shared/vi_text`: chuẩn hóa, bỏ dấu (có đ → d) dùng chung với Search | Thanh tìm kiếm và trợ lý hiểu giống nhau |
| Bộ thử và CI | Đề bài hỏi "đã thử bao nhiêu câu, những kiểu nào" |
| API cho B7 (`/v1/admin/ai/usage`, danh sách hội thoại cần xem) | Trang quản trị hiển thị |

---

## 5. Những gì phải giao (deliverables)

### 5.1. Code

| Thư mục | Nội dung |
|---|---|
| `backend/modules/assistant/` | api, service, router, agent_loop, fallback, rule_parser, lexicon, clarify, ranking, related_videos, 4 công cụ, plan_state, pricing_adapter, input/output guard, cards, conversation_store, handoff, prompt |
| `backend/modules/comment_summary/` | api, triggers, job_worker, preprocess, summarizer, validator, store, prompt |
| `backend/shared/` | llm_gateway (client, pricing, call_log, budget), ratelimit, vi_text |
| `backend/workers/summary_worker` | Worker chạy job tóm tắt |

### 5.2. Bảng dữ liệu

| Bảng | Tôi tạo | Ghi chú |
|---|---|---|
| `conversations`, `messages`, `handoffs` | ✓ | Hội thoại, lượt, bàn giao |
| `comment_summaries`, `comment_summary_jobs`, `summary_feedback` | ✓ | Tóm tắt, hàng đợi, phản hồi |
| `llm_calls`, `model_prices`, `eval_runs` | ✓ | Chi phí và kết quả đo |
| `destination_aliases`, `slang_lexicon` | ✓ (soạn dữ liệu) | Dùng chung với Search |
| `search_docs` thêm cột `place_keys`, `summary_signal`, `has_safety_flag` | Đề xuất, Duy làm | |

### 5.3. Endpoint

| Method | Path | Ghi chú |
|---|---|---|
| POST | `/v1/assistant/conversations` | Mở hội thoại |
| GET | `/v1/assistant/conversations`, `/v1/assistant/conversations/{id}` | Danh sách, chi tiết |
| POST | `/v1/assistant/conversations/{id}/messages` | Một lượt: chữ hoặc nút bấm |
| GET | `/v1/assistant/conversations/{id}/messages?after=` | Lấy kết quả sau khi mất mạng |
| POST | `/v1/assistant/conversations/{id}/claim` | Gộp hội thoại khách khi đăng nhập |
| POST | `/v1/assistant/conversations/{id}/handoff` | Sang "Đi tương tự" |
| POST | `/v1/assistant/messages/{id}/feedback` | Thích, không thích |
| DELETE | `/v1/assistant/conversations/{id}` | Xóa hội thoại |
| GET | `/v1/assistant/quota` | Hạn mức còn lại |
| GET | `/v1/posts/{id}/comment-summary` | Tóm tắt bình luận |
| POST | `/v1/posts/{id}/comment-summary/feedback` | Phản hồi tóm tắt |
| POST | `/v1/admin/posts/{id}/comment-summary/refresh` | Ép làm mới |
| GET | `/v1/admin/ai/usage`, `/v1/admin/ai/eval-runs`, `/v1/admin/ai/summaries`, `/v1/admin/assistant/conversations` | Cho B7 và trang quản trị |

### 5.4. Bộ thử và số đo

| Bộ | Cỡ |
|---|---|
| Hội thoại trợ lý | 200 câu: tiếng lóng, không dấu, chưa có điểm đến, không có kết quả, đổi một chi tiết, hoàn tác, đòi đặt hộ, ngoài phạm vi, injection, bài bị ẩn, dò dữ liệu riêng, nhiều lượt tới "Đi tương tự" |
| Tìm kiếm | 60 câu có nhãn bài liên quan |
| Tóm tắt | 30 bài seed có nhãn tay: mâu thuẫn video và thực tế, dưới ngưỡng, spam/injection, phàn nàn an toàn |

**Số phải báo cáo cuối kỳ:**
- 100% ID có thật, 100% số tiền khớp server, 0 bài ẩn bị gợi ý.
- Từ chối đúng ≥ 95%, gọi đúng công cụ ≥ 90%, Recall@10 ≥ 0,8.
- Trích dẫn tóm tắt hợp lệ 100%, 0 phàn nàn an toàn bị bỏ sót.
- Độ trễ p50/p95 theo từng đường xử lý.
- VNĐ mỗi câu, VNĐ mỗi hội thoại, VNĐ mỗi lần tóm tắt.

### 5.5. Tài liệu và trình bày

- Bản đề xuất cách đo phần trợ lý (tuần 1), chốt ở tuần 2.
- Câu trả lời cho các câu hỏi mở về trợ lý (mục 7), **thể hiện được trong sản phẩm**.
- Slide: luồng hội thoại demo, số đo, chi phí, "nếu có thêm thời gian".

---

## 6. Cần người khác giao, và tôi giao lại gì

| Người | Họ giao cho tôi | Tôi giao cho họ |
|---|---|---|
| **Duy** (Search) | `search_docs` + worker cập nhật; hàm `find_itinerary_candidates(filters)`; `product_usage_stats` | Từ điển alias, tiếng lóng; `vi_text`; tín hiệu `summary_signal`, `has_safety_flag` |
| **Thắng** (Booking, lõi mock) | `core.quote`, `core.search`, `core.has_completed_booking`; `go_similar.build_package` nhận `overrides[]` | Lộ trình đã sửa dạng `overrides[]` qua `handoff` |
| **Thanh** (Đăng bài, bình luận) | Lộ trình có tiêu đề, hashtag, mẹo; sự kiện `comment.created`, `comment.hidden` qua outbox; `list_visible_comments`, `hidden_ids` | Thẻ "Người đi rồi nói gì" (dữ liệu); gợi ý màn đăng bài nhắc điền đủ thông tin |
| **Phong** (Feed, app) | Chỗ đặt thẻ tóm tắt dưới video; nút "Hỏi AI" trên video | Component dữ liệu thẻ, mô tả UI thẻ |
| **Dung** (Đăng nhập, web) | JWT, `X-Anon-Id`, khung chat trên web | API chat dùng chung app và web |
| **Trang** (Quản trị, dữ liệu mock) | Dữ liệu seed: bài, bình luận có khen/chê/mâu thuẫn/an toàn, đơn đã hoàn tất; trang B7 | API B7, danh sách tóm tắt bị báo cáo hoặc có cờ an toàn |

Hợp đồng hàm public phải chốt ở **buổi họp tuần 1**. Trước khi có hàm thật thì dùng stub.

---

## 7. Câu hỏi mở về trợ lý mà đề bài bắt trả lời

| Câu hỏi (đề bài mục 5) | Đề xuất trả lời, thể hiện trong sản phẩm |
|---|---|
| Trợ lý dựa vào đâu để chọn gợi ý? Làm sao chắc nó không bịa? | Chỉ chọn từ lộ trình đã đăng trong `search_docs`, giá từ lõi. LLM chỉ được nhắc ID do công cụ trả; `output_guard` bỏ ID và số tiền lạ; thẻ do server dựng. Có bộ 200 câu và cổng CI 100% |
| Trợ lý nhớ gì, bao lâu? Chưa đăng nhập thì sao? App sang web có tiếp tục được không? | Nhớ hội thoại, bộ lọc, lộ trình đang sửa (10 phiên bản). Khách 7 ngày theo `anon_id` trên server; thành viên 30 ngày. Đăng nhập thì `claim` gộp vào tài khoản; web mở cùng hội thoại |
| Trợ lý được dùng thông tin nào của người dùng? | Câu đang hỏi, 6 tin gần nhất, bài đã thích/lưu nếu người dùng đồng ý. Không dùng thanh toán, giấy tờ, năm sinh; che SĐT, email trước khi gửi LLM |
| Một cuộc trò chuyện tốn bao nhiêu? Có giới hạn không? | ~13 VNĐ mỗi câu gõ chữ, 0 VNĐ nút bấm, ~65 VNĐ một hội thoại 10 lượt (ước tính, đo thật qua `llm_calls`). Giới hạn 20/50 câu mỗi ngày, trần 200.000 VNĐ mỗi ngày |
| (Bài đăng) AI Agent tham gia kiểm duyệt được không? | Tóm tắt đẩy cờ an toàn vào hàng chờ kiểm duyệt; quyết định ẩn/hiện vẫn do người |

---

## 8. Kế hoạch theo tuần

Khớp lịch của artifact: tuần 1 thử nghiệm cách làm và bộ câu hỏi thử, tuần 2 gợi ý địa điểm, tuần 3 đề xuất bài đăng trên cả app và web, tuần 4 báo cáo.

### Tuần 1: Nền móng

- [ ] Chốt hợp đồng hàm với Duy, Thắng, Thanh, Trang (mục 6); viết stub
- [ ] `llm_gateway` + `llm_calls` + `model_prices`; script đo độ trễ và token của model **khi đã tắt chế độ suy nghĩ**, trên câu tiếng Việt thật
- [ ] `rule_parser`, `destination_aliases`, `slang_lexicon` (củ, tr, k, đứa, 3n2đ, chill…)
- [ ] `find_itineraries` trên `search_docs`, gọi `core.quote`, chấm điểm, chọn đa dạng
- [ ] Bộ 200 câu hội thoại v0, 60 câu tìm kiếm, nhãn tay 30 bài tóm tắt
- [ ] Luật lọc bình luận, nhãn người đã đi, bắt từ khóa an toàn
- [ ] Bản đề xuất cách đo
- **Demo:** gõ "nha trang 2 dua 8 cu" ra 3 lộ trình **chưa cần LLM**; có số đo độ trễ thật

### Tuần 2: Gợi ý địa điểm

- [ ] Router, đường nhanh, `agent_loop`, `get_itinerary`, `output_guard`, fallback
- [ ] Thẻ lộ trình + thẻ video trên app
- [ ] Chống gửi trùng (`client_msg_id`), một lượt mỗi hội thoại, `claim` khi đăng nhập
- [ ] Hạn mức, trần ngày, view chi phí
- [ ] `summarizer` + `validator` chạy offline trên 30 bài; bảng job + worker
- [ ] CI chạy cổng cứng
- **Demo:** hỏi → lộ trình + video trên app; tóm tắt sinh cho bài seed; cách đo đã chốt

### Tuần 3: Đề xuất bài đăng, chạy trên app và web

- [ ] `suggest_replacements`, `apply_change` (đổi mục, bỏ mục, đổi số người, đổi ngày, hoàn tác), thẻ so sánh, thẻ diff
- [ ] `handoff` sang "Đi tương tự"
- [ ] Chat trên web
- [ ] Thẻ "Người đi rồi nói gì" trên app và web; lọc bình luận ẩn lúc đọc; phản hồi; đẩy tín hiệu sang Search
- [ ] API B7 cho trang quản trị
- [ ] **Thứ Năm ngừng thêm tính năng**
- **Demo:** hỏi → lộ trình → đổi khách sạn → hoàn tác → Đi tương tự → màn xác nhận; ẩn một bình luận thì tóm tắt cập nhật

### Tuần 4: Đo và trình bày

- [ ] Chạy đủ 200 câu, 60 câu, 30 bài; sửa lỗi; tinh chỉnh trọng số xếp hạng
- [ ] Hai người chấm độc lập 30 bài tóm tắt
- [ ] Báo cáo số đo (mục 5.4), chi phí, độ trễ, fallback
- [ ] Slide và câu trả lời "nếu có thêm thời gian": vector cho câu kiểu "vibe", so sánh theo ngày rẻ nhất, đổi phương tiện thật (xe, tàu)

---

## 9. Không thuộc phần của Bảo

- Đặt chỗ, giữ chỗ, thanh toán, chống bán chỗ cuối hai lần: Thắng (F4, lõi).
- Feed video, xếp hạng bảng tin: Phong.
- Đăng video, dựng lộ trình, quy tắc Verified: Thanh.
- Giao diện trang quản trị: Trang. Bảo chỉ cung cấp API B7.
- Kiểm duyệt bài: người làm. AI chỉ đưa cờ an toàn vào hàng chờ.

---

## 10. Điểm còn chờ chốt

| # | Vấn đề | Ảnh hưởng tới phần AI |
|---|---|---|
| 1 | **Ai được đăng bài.** Artifact và đề bài: chỉ người đã đi. Scope 04: ai cũng đăng, bài đặt qua app thì có Verified | Nhãn "người đã đi" trong tóm tắt và điểm tin cậy khi xếp hạng; cả hai cách đều chạy được, chỉ đổi dữ liệu đầu vào |
| 2 | Tên nút: "Đặt y chang" / "Đặt ngay" / "Đi tương tự" | Chỉ đổi chữ trên thẻ và prompt |
| 3 | `go_similar.build_package` có nhận `overrides[]` không | Không có thì trợ lý chỉ bàn giao được lộ trình gốc, mất tính năng "đổi rồi đặt" |
| 4 | Có khóa API LLM và ngân sách không | Không có thì phải chạy chế độ luật toàn phần |
| 5 | Mentor: sửa lộ trình nháp trong chat rồi chuyển sang trang đặt có đúng "không tự đặt chỗ" không | Thiết kế hiện tại cho là đúng; cần xác nhận |

---

## 11. Tài liệu liên quan

- [Thiết kế chatbot agent và tóm tắt bình luận](<../reports/Thiết kế chatbot agent và tóm tắt bình luận.md>): DDL, JSON, schema công cụ, prompt, 10 sơ đồ, yêu cầu chức năng và phi chức năng.
- [Chatbot agent tóm tắt bình luận](<../reports/Chatbot agent tóm tắt bình luận.md>): nghiên cứu nền, các nền tảng khác làm thế nào.
- [Scope tinh gọn](../docs/04-scope-tinh-gon.md) và [prototype](../demo/tripflix-prototype.html).
- [Đề bài](../docs/02-de-bai-vinpearl-cho-nguoi-tre.md): A12, mục 5, 6, 7, 9, 10.
