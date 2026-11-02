# Module AI của Tripflix "như một sản phẩm": danh mục use case, định nghĩa trợ lý, bộ công cụ, dữ liệu, API, sự kiện, lộ trình tuần (phụ trách: Le Nguyen Quoc Bao, M5)

Ký hiệu nguồn (toàn bộ là tài liệu nội bộ dự án, không dùng web):
- **ĐB** = đặc tả [docs/02-de-bai-vinpearl-cho-nguoi-tre.md](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md) (nguồn sự thật).
- **TK** = hướng đã chốt [docs/03-y-tuong-trien-khai.md](../../docs/03-y-tuong-trien-khai.md).
- **BC** = báo cáo [reports/Kiến trúc module Tripflix.md](../../reports/Kiến%20trúc%20module%20Tripflix.md) (Phần I quy ước + danh mục sự kiện; Phần II các bản module).
- **GC-SA** = ghi chú [search_ai.md](../Kiến%20trúc%20module%20Tripflix/search_ai.md); **GC-MM** = ghi chú [module_mapping.md](../Kiến%20trúc%20module%20Tripflix/module_mapping.md).
- **PC** = phân công [team/01-phan-cong-nghien-cuu-module.md](../../team/01-phan-cong-nghien-cuu-module.md).
- **[Đề xuất]** = đề xuất mới của ghi chú này (chưa có trong tài liệu nhóm). **[Đề xuất BC]** = đề xuất đã có trong báo cáo kiến trúc nhưng nhóm chưa chốt.

---

## 1. Danh mục use case AI/ML trong Tripflix: mã đặc tả, bắt buộc hay tùy chọn, kỹ thuật, module chủ, dữ liệu vào, đầu ra và màn hình, tuần giao

### Takeaway
Đặc tả có **ba nhóm việc "AI"**: (1) **trợ lý hội thoại A12** là việc duy nhất *bắt buộc* cần LLM, thuộc module AI; (2) một loạt việc *bắt buộc* nhưng nên làm bằng **embedding, luật hoặc thống kê** và thuộc **Search** (A1, A3, A6, A7, A9, A11 mục tương tự, A15) hoặc **Administration** (A14, riêng tư ảnh); (3) các phần mở rộng *tùy chọn* B1 (LLM, AI), B2 (embedding ảnh, Search), B7 (không cần ML, dữ liệu ở AI và màn hình ở Administration). Nguyên tắc chọn kỹ thuật: **luật trước, embedding sau, LLM cuối cùng**, và **mọi giá, chỗ, lý do gợi ý đều sinh từ dữ liệu, không do LLM viết**.

### Cited Findings
- Trợ lý gợi ý là một trong bốn sản phẩm, có trên app và web: "Hỏi bằng lời của mình, nhận gợi ý địa điểm và những bài đăng đáng đọc" — [ĐB §1](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A12 (Phần A, bắt buộc) có 11 tiêu chí nghiệm thu: có trên cả app và web; hiểu câu kiểu người trẻ ("cuối tuần này đi đâu chill mà dưới 3 củ", "có chỗ nào cho 4 đứa quẩy mà k quá đông k"); hỏi lại khi thiếu thông tin quan trọng, không hỏi dồn; gợi ý **địa điểm** (khách sạn, khu vui chơi, điểm đến) và **bài đăng**, mỗi gợi ý bấm được; chỉ gợi ý thứ có thật, không bịa chỗ/giá/bài, không gán cho bài điều bài không nói; không gợi ý bài đã ẩn; hợp gu và ngân sách, dùng được cả thứ đã thích/lưu trên app; không có thì nói thật; không tự đặt chỗ; từ chối yêu cầu ngoài du lịch hoặc nguy hiểm, giữ giọng thân thiện; dùng được khi chưa đăng nhập, đăng nhập giữa chừng không mất hội thoại — [ĐB §4 A12](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A3 (tìm trong bài đăng theo ý, không chỉ từ khóa; hiểu không dấu, viết tắt, tiếng lóng, trộn tiếng Anh, emoji; không có thì nói thật) và A6 (tìm bằng lời trên web, hiểu ngân sách theo đầu người hay cả nhóm, không có thì nói thật và gợi ý nới điều kiện) là bắt buộc — [ĐB §4 A3, A6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A7: mỗi người thấy gợi ý riêng **kèm lý do ngắn**, hành vi trên app làm đổi gợi ý trên web, tôn trọng ngân sách — [ĐB §4 A7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A9: đặt xong một phần chuyến đi thì gợi ý phần còn thiếu ("đã có phòng ở Nha Trang thì gợi ý vé máy bay và hoạt động ở Nha Trang, không gợi ý thêm phòng"), thấy ngay trên cả web và app — [ĐB §4 A9](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A1: bảng tin đổi theo gu khi thích/lưu/bỏ qua, mỗi bài có lý do ngắn vì sao đang được quan tâm — [ĐB §4 A1](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A11: mục hết chỗ hoặc ngừng bán thì "cho bỏ mục đó hoặc **gợi ý mục tương tự**" — [ĐB §4 A11](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A14: hàng chờ có ưu tiên; bài đã ẩn biến mất khỏi app, web **và gợi ý của trợ lý** trong thời gian ngắn — [ĐB §4 A14](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Câu hỏi mở: "Có tự động kiểm duyệt không? AI Agent tham gia được không?" — [ĐB §5](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- A15: thấy chỗ/bài đang lên xu hướng, lên vì sao, nhận ra dấu hiệu bị đẩy ảo — [ĐB §4 A15](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- §6 (bắt buộc): ảnh có mặt người lạ hoặc thông tin cá nhân "phải xử lý được"; có cách xử lý bài/bình luận xúc phạm, quảng cáo, lừa đảo, cổ vũ nguy hiểm — [ĐB §6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Phần B (đội tự chọn, phải giải thích): B1 tóm tắt "50 người đã đi nói gì", chỉ dựa trên bài có thật; B2 tìm bằng ảnh, không có thì nói thật; B7 theo dõi trợ lý (hỏi gì, gợi ý gì, từ chối gì, tốn bao nhiêu) — [ĐB §4 Phần B](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Ràng buộc: "Chi phí vận hành trợ lý phải được đo và báo cáo" — [ĐB §7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); thước đo thành công "Trợ lý không bịa: mọi chỗ và bài đăng nó gợi ý đều có thật. Đã thử bao nhiêu câu hỏi, những kiểu nào?" — [ĐB §9](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Đề xuất ban đầu của nhóm cho Phần B: **B7** ("gần như có sẵn vì đề bài đã bắt đo chi phí trợ lý"), **B1** ("phục vụ Hà, dùng lại trợ lý"), B4 — chờ chốt — [TK §3.2](../../docs/03-y-tuong-trien-khai.md).
- Phân chia module: M1 Search & Recommendation lo A3, A6, A7, A9, B2; M2 Feed & Trending lo A1 và phần tính điểm A15; M5 AI Assistant lo A12, B1, B7; M6 Moderation lo A14, riêng tư ảnh; M7 Analytics lo màn hình A15, A16, B7 — [PC §2.2](../../team/01-phan-cong-nghien-cuu-module.md); [TK §9](../../docs/03-y-tuong-trien-khai.md).
- BC gộp M1+M2 vào **Search**, M6+M7 vào **Administration**; mỗi tính năng có đúng một chủ: A12 → AI (phụ: Search công cụ, Booking giá/chỗ, Access gộp phiên); B1 → AI (phụ: Social, Search); B7 → AI (dữ liệu) + Administration (màn hình); B2 → Search — [BC Phần I "Từng tính năng có đúng một chủ"](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Search hiểu câu hỏi theo "**luật trước, LLM sau**" (regex tiền "2 củ", "1tr5", "500k"; số người "4 đứa"; cơ sở ngân sách), dùng hybrid pgvector + full-text + RRF, có **cổng tuyệt đối** cho "không có kết quả"; gợi ý A7 dùng taste vector, **lý do sinh từ dữ liệu, không do LLM viết**; A9 dùng **luật bổ sung** `complement_rules`; xu hướng dùng công thức trọng số × trust × bán rã, cờ đẩy ảo từ vận tốc và cụm thiết bị — [BC Phần II Search §6, §7](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Kiểm duyệt chọn "hai tầng": máy chấm điểm (API kiểm duyệt + từ khóa tiếng Việt + regex SĐT/link bán tour), người quyết cuối; AI Agent chỉ tóm tắt case, gợi ý `reason_code`, không bấm nút — [BC Phần II Administration §6, §7](../../reports/Kiến%20trúc%20module%20Tripflix.md); [TK §8.2](../../docs/03-y-tuong-trien-khai.md).
- BC đề xuất AI giữ "**cổng LLM**" chung (`/internal/ai/classify`) để mọi chi phí mô hình ngôn ngữ nằm một chỗ; `llm_calls.purpose` gồm `assistant | summary | moderation_assist | query_parse` — [BC Phần II AI §1, §2, §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- B2 nên làm ảnh → ảnh trước vì mô hình ảnh–văn bản tiếng Việt còn non — [BC Phần II Search §8](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Mốc "Trợ lý": tuần 1 "Thử nghiệm cách làm, bộ câu hỏi thử"; tuần 2 "Gợi ý địa điểm"; tuần 3 "Đề xuất bài đăng, chạy trên cả ứng dụng và web" — [ĐB §10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Hướng phát triển ngoài 4 tuần có dùng AI: "Đi ngay" (AI hiểu yêu cầu + luật tính chặng), "Có X triệu, đi đâu?", "Trợ lý canh giá" — [TK §16](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Bảng danh mục [Đề xuất phân loại; cột "Module chủ" theo BC].** Ký hiệu kỹ thuật: **R** = luật/regex/công thức; **S** = thống kê/heuristic; **E** = embedding + vector index (pgvector dùng chung); **C** = bộ phân loại có sẵn (API kiểm duyệt, phát hiện khuôn mặt/chữ); **LLM-n** = LLM cỡ nhỏ/rẻ; **LLM-v** = LLM cỡ vừa/lớn có tool calling. Chọn model cụ thể là việc tuần 1, đo trên bộ câu thử (xem mục 7).

| # | Use case | Mã ĐB | Bắt buộc? | Kỹ thuật | Module chủ (phụ) | Dữ liệu vào ← module nguồn | Đầu ra → màn hình | Tuần |
|---|---|---|---|---|---|---|---|---|
| AI-01 | Trợ lý gợi ý **địa điểm** (khách sạn, khu vui chơi, điểm đến) | A12 | **Bắt buộc** | LLM-v tool calling + bộ kiểm chứng R; đường lùi R (chỉ tìm kiếm) | **AI** (Search, Booking, Access) | Câu hỏi ← client; ứng viên ← Search `tools/places`; giá/chỗ ← Booking `/internal/prices`; ngân sách/gu khai báo ← Access; gu suy ra ← Search | Đoạn văn + thẻ sản phẩm/điểm đến (ID thật, giá hiện tại) → tab/màn Trợ lý app, widget web; chạm thẻ → trang chi tiết A4 | **T2** |
| AI-02 | Trợ lý gợi ý **bài đăng** đáng đọc, trích đúng ý bài | A12 | **Bắt buộc** | LLM-v + công cụ E (hybrid của Search) + kiểm chứng trích dẫn R | **AI** (Search, Social) | Bài `visible` ← Search `tools/posts` hydrate qua Social `/internal/posts?ids=` | Thẻ bài (dấu "đã đi thật", nhãn mô phỏng/Hợp tác) + đoạn trích thật → chạm mở bài, có nút "Đặt y chang" của A11 | **T3** |
| AI-03 | Hỏi lại khi thiếu thông tin quan trọng, không hỏi dồn | A12 | **Bắt buộc** | R (QueryParser chung với Search: ngân sách, `budget_basis`, số người, ngày, nơi) quyết định *có hỏi không*; LLM chỉ diễn đạt | **AI** (Search parser) | Câu hỏi + ngữ cảnh hội thoại | Câu hỏi lại + chip lựa chọn | T2 |
| AI-04 | Từ chối ngoài phạm vi du lịch / nguy hiểm, giọng thân thiện | A12, §6 | **Bắt buộc** | R (danh sách chủ đề) + LLM-v theo system prompt; tùy chọn C (API kiểm duyệt cho đầu vào) | **AI** | Câu hỏi | Lời từ chối + gợi ý quay lại chủ đề du lịch; ghi `refused`, `refusal_reason` cho B7 | T2 (đo ≥95% ở T3) |
| AI-05 | Bộ kiểm chứng "không bịa" (ID có trong kết quả công cụ, số tiền khớp, trích dẫn là đoạn có thật, tên riêng lạ) | A12, §6, §9 | **Bắt buộc** | R | **AI** | Kết quả công cụ của lượt | `answer_validations` (pass/stripped/retried/fallback) | T1 nguyên mẫu, T2 chạy thật |
| AI-06 | Đo chi phí từng lần gọi, hạn mức, trần chi phí | §7, §5 câu 4 | **Bắt buộc** | R (đếm token × bảng giá có phiên bản) | **AI** (Administration hiển thị) | Usage do nhà cung cấp LLM trả | `llm_calls`, VNĐ/cuộc p50/p95 → B7 / báo cáo §9 | T1 khung, T2 đủ |
| AI-07 | Bộ câu hỏi thử + chạy đánh giá tự động | §9, §10 T1 | **Bắt buộc** | R tự chấm (ID hợp lệ, đúng loại hành vi) + chấm tay; LLM-n làm giám khảo là tùy chọn | **AI** | Bộ câu ≥ 200 (TK §12) + dữ liệu mô phỏng | `eval_runs` → slide demo, B7 | **T1** |
| AI-08 | Phân tích câu tìm bằng lời (ngân sách, số người, `budget_basis`, ngày tương đối, "vibe") | A3, A6 | **Bắt buộc** | R (`query_lexicon`) trước; LLM-n chỉ khi luật không đủ (`purpose=query_parse`) | **Search** (AI cấp cổng LLM [Đề xuất BC]) | Câu tìm ← client | `parsed`, `clarification`, `relax_suggestions` → ô tìm web/app | T1 (A6), T2 (A3) |
| AI-09 | Tìm theo nghĩa trong bài đăng và sản phẩm | A3, A6 | **Bắt buộc** | E + BM25/trigram, RRF, cổng tuyệt đối | **Search** | `search_documents` ← sự kiện của Social, Booking | Danh sách kết quả hoặc "không có" → màn tìm | T1–T2 |
| AI-10 | Gợi ý dành cho bạn + cá nhân hóa bảng tin | A7, A1 | **Bắt buộc** | E (taste vector) + công thức xếp hạng R; lý do từ dữ liệu ("Vì bạn đã lưu…") | **Search** | Tín hiệu ← Social `interaction.created`; ngân sách ← Access | Thẻ gợi ý + `reason` → trang chủ web, bảng tin app | T1 |
| AI-11 | Lý do "vì sao đang hot" | A1, A15 | **Bắt buộc** | R (`reason_code` từ vận tốc, đặt y chang…) + mẫu câu | **Search** | `trend_scores` | Dòng lý do trên thẻ bảng tin; màn A15 | T1 |
| AI-12 | Gợi ý phần còn thiếu sau khi đặt | A9 | **Bắt buộc** | R (`complement_rules`) + E để xếp hạng trong loại | **Search** (Notification đẩy) | `booking.confirmed` ← Booking | `recommendation.ready` → màn kết quả đặt chỗ, app + web | T2 |
| AI-13 | Mục tương tự khi hết chỗ / ngừng bán | A11 | **Bắt buộc** | E + lọc cùng loại/điểm đến/ngày còn chỗ | **Search** (Booking gọi) | `/internal/search/similar-products` | Gợi ý thay thế trên màn xác nhận "Đặt y chang" | T3 |
| AI-14 | Xu hướng + phát hiện đẩy ảo | A15 | **Bắt buộc** | S (một người một phiếu, trust, cụm thiết bị/mạng, vận tốc bất thường) — **không cần LLM** | **Search** tính, **Administration** hiển thị | `signals`, `login_events` ← Access | `trend.flagged` → màn A15 | T3 |
| AI-15 | Sàng lọc kiểm duyệt tự động + gợi ý lý do | A14, §5, §6 | Xử lý nội dung xấu là **bắt buộc**; *tự động* là lựa chọn của nhóm (đã chốt có) | C (API kiểm duyệt văn bản/ảnh) + R (từ khóa tiếng Việt, regex SĐT/link) ; LLM-n gợi ý `reason_code`, tóm tắt case (tùy chọn) | **Administration** (AI cấp `/internal/ai/classify`) | Bài/bình luận ← `post.created`, `comment.created` | `auto_verdict`, `priority_score`, lý do gợi ý → hàng chờ kiểm duyệt | T2 |
| AI-16 | Riêng tư ảnh: mặt người lạ, thông tin cá nhân trong ảnh | §6 | **Bắt buộc** ("phải xử lý được") | C (phát hiện khuôn mặt/chữ) + người quyết làm mờ | **Administration** (Social lưu `blur_regions`) | `post_media` ← Social | Đề xuất vùng làm mờ → màn case kiểm duyệt | T2–T3 |
| AI-17 | Tóm tắt "N người đã đi nói gì" (điểm cộng, trừ) | **B1** | Tùy chọn (nhóm nghiêng chọn) | LLM-n/v chạy **theo lô**, đầu ra có cấu trúc, mỗi ý trích `post_id` + kiểm chứng R; cache | **AI** (Social, Search) | Bài `visible` theo chỗ ← Social/Search | `post_summaries` → khối trên trang chi tiết A4; công cụ cho trợ lý | T3 |
| AI-18 | Tìm bằng hình ảnh | **B2** | Tùy chọn | E ảnh (ảnh → ảnh trước), ngưỡng "không có" | **Search** | `image_index` ← media Social, sản phẩm Booking | Kết quả tương tự hoặc "không có" → màn tìm | T3 nếu chọn |
| AI-19 | Theo dõi trợ lý (hỏi gì, gợi ý gì, từ chối gì, tốn bao nhiêu) | **B7** | Tùy chọn (nhóm nghiêng chọn) | Tổng hợp SQL; gom chủ đề câu hỏi bằng `parsed` tags hoặc E phân cụm [Đề xuất] — **không cần LLM** | **AI** dữ liệu, **Administration** màn hình | `messages`, `llm_calls`, `answer_validations`, `ai_feedback` | Dashboard B7 trên trang quản trị (chỉ quản trị) | T3 |
| AI-20 | Gợi ý lưu sở thích từ hội thoại ("Lưu ngân sách 2 triệu/người nhé?") | A12, A7 | Tùy chọn [Đề xuất] | LLM trích slot + **người dùng bấm xác nhận**; ghi qua Access | **AI** → Access | Ngữ cảnh hội thoại | Nút xác nhận trong chat; `user.preferences_updated` | T3 nếu kịp |
| AI-21 | Mở trợ lý từ một bài đăng / từ trang tìm rỗng | A12 | Tùy chọn [Đề xuất] | Như AI-01, kèm `context.post_id` hoặc `context.search_query` | **AI** | Bài ← Social; câu tìm + `relax_suggestions` ← Search | Hội thoại mở sẵn ngữ cảnh | T3 |
| — | Đơn bất thường (A16), báo giảm giá (B3), đồng bộ, đếm cảm xúc | A16, B3, A10 | Bắt buộc / tùy chọn | **R, không AI** | Booking/Payment, Notification, Social | — | — | — |
| — | "Đi ngay", "Có X triệu đi đâu", "Trợ lý canh giá" | TK §16 | Ngoài 4 tuần | LLM hiểu yêu cầu + R lập phương án | AI + Booking | — | — | Sau T4 |

**Kết luận phạm vi "AI như một sản phẩm" cho Bảo:**
- *Phải giao* (bắt buộc, module AI): AI-01 đến AI-07. Đây là toàn bộ A12 cộng nghĩa vụ đo chi phí (§7) và đo "không bịa" (§9).
- *Nên giao* (tùy chọn nhưng rẻ vì dùng lại hạ tầng): B7 (AI-19, chỉ là truy vấn trên dữ liệu đã bắt buộc phải log) và B1 (AI-17, dùng lại cổng LLM + bộ kiểm chứng).
- *Cung cấp hạ tầng cho người khác, không sở hữu nghiệm thu*: cổng LLM cho AI-08 (Search) và AI-15 (Administration) [Đề xuất BC]; mô hình embedding dùng chung do Search vận hành chỉ mục (PC §5.1).
- *Không phải việc AI* dù dễ bị gán nhầm: A15 đẩy ảo, A9, A16, lý do gợi ý A7. Dùng LLM ở đây vừa tốn vừa có nguy cơ bịa lý do; nên ghi rõ trong buổi họp ghép để tránh "AI hóa" mọi thứ.

### Gaps
- Đặc tả và tài liệu nhóm **không định lượng** quy mô bộ dữ liệu sản phẩm (bao nhiêu khách sạn, có mô tả/ảnh không), nên chưa biết công cụ `search_places` có đủ chất liệu cho câu kiểu "chill", "quẩy" hay không (BC Search §8 câu hỏi mentor 1).
- Chưa có quyết định chính thức của nhóm về **có chọn B1, B7 hay không** (TK §3.2 ghi "chờ chốt").
- Chưa rõ **khóa API / ngân sách LLM** có được ban tổ chức cấp không (TK §15 Q7) — quyết định LLM-n hay LLM-v, hay mô hình mở chạy CPU, phụ thuộc câu này.
- Không có tài liệu nội bộ nào so sánh cụ thể các LLM về tiếng Việt và giá; PC §4.4 giao việc này cho Bảo nhưng chưa có kết quả. Ghi chú này **không** nêu giá token cụ thể vì không có trong tài liệu dự án.

---

## 2. Định nghĩa sản phẩm trợ lý: điểm vào, luồng hội thoại, định dạng đầu ra, những gì trợ lý không bao giờ làm

### Takeaway
Trợ lý là **lớp điều phối và diễn đạt** trên Search/Booking/Social, không phải nguồn tri thức: mỗi lượt đi qua "hiểu câu (luật) → gọi công cụ chỉ đọc → LLM viết câu ngắn có trích dẫn ID → bộ kiểm chứng → trả đoạn văn + thẻ có ID thật". Có **4 điểm vào** (tab app, widget web, từ bài đăng, từ trang tìm rỗng) và **7 luồng** (gợi cảm hứng, hỏi lại, gợi ý chỗ + bài, tinh chỉnh, không có kết quả, từ chối, đòi đặt hộ). Danh sách "không bao giờ" lấy thẳng từ A12 và §6.

### Cited Findings
- Hành trình app: "Hoặc hỏi trợ lý: *cuối tuần này đi đâu chill mà dưới 3 củ?*, nhận gợi ý địa điểm và bài đăng"; hành trình web: "Tìm bằng lời của mình, nhận gợi ý hợp gu, hỏi trợ lý" — [ĐB §3](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Chân dung "người vừa tải ứng dụng, chưa có tài khoản, chưa từng đặt gì, chưa biết đi đâu" — [ĐB §2](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); TK đáp ứng bằng "bảng tin xu hướng không cần đăng nhập; trợ lý" — [TK §2.2](../../docs/03-y-tuong-trien-khai.md).
- Khách (chưa đăng nhập) được "xem bảng tin, bài đăng, tìm kiếm, hỏi trợ lý"; ma trận quyền: trợ lý mở cho Khách, Thành viên, Dưới 18, Người đã đi — [TK §2.1, §2.3](../../docs/03-y-tuong-trien-khai.md).
- Quyết định Q6: "Trợ lý là **tính năng**, không phải vai trò: chỉ dùng dữ liệu thật, không tự đặt chỗ" — [TK §0](../../docs/03-y-tuong-trien-khai.md).
- Không bịa: "hiểu câu hỏi → gọi công cụ tìm chỗ, giá, bài đang hiển thị → chỉ trả lời bằng ID có thật → bộ kiểm chứng (ID tồn tại, giá khớp, trích dẫn là đoạn có thật). Không có công cụ đặt chỗ" — [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- Phương án chọn: tool calling chỉ trả ID thật + đầu ra JSON `{segments: [{text, cites: [{type, id}]}], cards: [...], clarification?}` + bộ kiểm chứng; sai sau một lần thử lại thì câu mẫu "Mình chưa tìm thấy thông tin chắc chắn" kèm kết quả tìm thô; tối đa 3–4 vòng công cụ mỗi lượt; nội dung bài người khác bọc như dữ liệu để chống prompt injection; phương án C (không LLM) là đường lùi — [BC Phần II AI §6](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Khi mọi công cụ trả rỗng, chỉ cho phép kiểu trả lời "không có" hoặc "hỏi lại" — [BC Phần II AI §7](../../reports/Kiến%20trúc%20module%20Tripflix.md); [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Hỏi lại tái dùng QueryParser của Search; giới hạn 1 câu hỏi mỗi lượt; có ngân sách và số người > 1 mà không rõ cơ sở thì hỏi lại thay vì đoán — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md); [BC Phần II Search §6](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Giá, khả dụng không bao giờ lấy từ chữ LLM; UI vẽ từ thẻ (dữ liệu tại thời điểm hiển thị); validator dùng regex số tiền trong chữ so với kết quả công cụ — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- UI cần nhãn "giá và còn chỗ lấy từ hệ thống" (Expedia Romie còn "glitches" trong thử nghiệm USA TODAY) — [BC Phần II AI §6](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Quy tắc cố định: không bài đăng giả, nội dung mô phỏng phải đánh dấu; không khan hiếm giả, không bịa khuyến mãi; thông tin cá nhân và lịch sử đặt chỗ của một người không hiện cho người khác — [ĐB §5](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md). Không thao túng, không ép mua — [ĐB §6](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Lộ trình công khai không lộ ngày đi, giá đã trả, mã đặt chỗ, người đi cùng — [TK §8.3](../../docs/03-y-tuong-trien-khai.md); A11 "không để lộ thông tin riêng của người đăng bài" — [ĐB §4 A11](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Bài có nhãn "đã đi thật", nhãn Hợp tác nếu nhận tài trợ, bài mô phỏng có nhãn — [TK §6](../../docs/03-y-tuong-trien-khai.md); [BC Phần II Social §2](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Ngôn ngữ ngoài tiếng Việt và hỗ trợ khách hàng bằng người thật nằm **ngoài phạm vi**; đặt dịch vụ ngoài Vinpearl cũng ngoài phạm vi — [ĐB §8](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Giao diện app dự kiến là video dọc kiểu TikTok; tài liệu mô tả thanh bên, nút "Lộ trình", "Đặt y chang" nhưng **không nêu vị trí của trợ lý** — [TK §4](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Điểm vào [Đề xuất]**
| Điểm vào | Nền tảng | Ngữ cảnh gửi kèm | Ghi chú |
|---|---|---|---|
| Tab/nút "Hỏi Tripflix" cố định (thanh điều hướng dưới) | App | `entry_point=app_tab` | Đáp ứng ĐB §3 bước 4; TK §4 cần bổ sung vị trí này |
| Widget nổi + trang `/tro-ly` toàn màn | Web | `entry_point=web_widget` | Cùng `conversation_id` khi đã đăng nhập, nên app ↔ web tiếp tục được |
| Nút "Hỏi về chuyến này" trên bài có lộ trình | App, Web | `context.post_id` | Trợ lý mở đầu bằng các câu gợi ý ("Chỗ này hợp 4 người không?", "Còn chỗ tương tự rẻ hơn?") |
| Trang tìm không có kết quả (A3/A6) | App, Web | `context.search_query` + `parsed` + `relax_suggestions` của Search | Chuyển giao mềm: "Muốn mình hỏi thêm để tìm cho bạn không?" |
| (Tùy chọn) Màn kết quả đặt chỗ | App, Web | `context.booking_id` | Chỉ dùng công cụ "phần còn thiếu" của Search (A9), không trùng màn A9 |

**Luồng hội thoại [Đề xuất, khớp TK §8.4 và BC AI §6]**
1. **Gợi cảm hứng** ("chưa biết đi đâu", khách mới): không đủ điều kiện để lọc → gọi `get_trending` (Search) + `get_user_context` (nếu có gu) → đề xuất 2–3 điểm đến đa dạng giá, mỗi điểm 1 bài đáng đọc; kết thúc bằng 1 câu hỏi mở nhẹ (ngân sách hoặc thời gian), **không hỏi dồn**.
2. **Hỏi lại**: chỉ khi thiếu thông tin *làm đổi kết quả* theo QueryParser: (a) ngân sách có, số người > 1, không rõ theo người hay cả nhóm; (b) ngày mơ hồ mà sản phẩm có giá theo ngày; (c) yêu cầu đặt chỗ cụ thể không rõ điểm đến. Tối đa **1 câu hỏi/lượt** và **2 lượt hỏi liên tiếp**; lần thứ 3 thì đi tiếp với giả định nêu rõ ("Mình tạm hiểu 3 củ là cho 1 người nhé") — giả định hiện thành chip sửa được. "Cuối tuần này" được server quy ra ngày theo giờ Việt Nam, không hỏi lại.
3. **Gợi ý chỗ + bài**: `search_places` → `check_price_availability` cho tối đa 3–5 ứng viên → `search_posts` theo `place_ids` → LLM viết 1–3 câu, mỗi câu nhắc tới thực thể phải có `cites` → kiểm chứng → thẻ. Lý do trên thẻ lấy từ `match_reasons` của Search (dữ liệu), không phải câu của LLM.
4. **Tinh chỉnh** ("rẻ hơn", "gần biển hơn", "bỏ Phú Quốc"): cập nhật `conversations.context` (bộ slot đang dùng) rồi gọi lại công cụ; không bắt người dùng nhắc lại.
5. **Không có kết quả**: mọi công cụ rỗng → chỉ được trả kiểu `no_result` + `relax_suggestions` của Search ("nới ngân sách lên 3,5 triệu thì có 2 chỗ", "đổi sang tuần sau"), hoặc hỏi lại; **không** lấp chỗ bằng kết quả không liên quan (A3/A6/A12 "nói thật").
6. **Từ chối**: ngoài du lịch (code, bài tập, chính trị, y tế, pháp lý chi tiết), nguy hiểm (đi vào khu cấm, chất cấm, tự hại), xin thông tin người khác ("người đăng bài này đi ngày nào, trả bao nhiêu") → lời từ chối ngắn, thân thiện, gợi quay lại du lịch; ghi `refused=true`, `refusal_reason` (mã) cho B7.
7. **Đòi đặt hộ / thanh toán hộ**: giải thích trợ lý không đặt thay, đưa thẻ có nút mở trang chi tiết hoặc "Đặt y chang"; người dùng tự bấm (A12). Với người dưới 18, thẻ vẫn mở được trang chi tiết, việc chặn do Booking (`UNDER_18`); trợ lý không đưa câu thúc giục đặt.
8. (Tùy chọn B1) **"Người đi rồi nói gì về X"**: gọi `get_post_summary`; mỗi ý cộng/trừ kèm số bài và trích `post_id`.

**Định dạng đầu ra [Đề xuất, mở rộng schema của BC]**
- `segments[]`: `{text, cites:[{type: product|place|post, id}]}`. Câu không nhắc thực thể thì `cites` rỗng.
- `cards[]`: `{type, id, title, subtitle, media_thumb, price_from_vnd, price_basis, price_checked_at, availability, reason{type, text}, labels[verified_traveled|simulated|sponsored], deeplink}` — **mọi trường do server điền từ dữ liệu module nguồn**, LLM chỉ chọn `id` và thứ tự.
- `clarification?`: `{question, options[{label, value}], field}`.
- `assumptions[]`: giả định đã áp dụng, sửa được.
- `refusal?`: `{reason_code}`.
- `mode`: `llm | search_only` (đường lùi khi vượt trần chi phí hoặc nhà cung cấp lỗi).
- Nhãn cố định dưới khung chat: "Giá và tình trạng còn chỗ lấy từ hệ thống tại thời điểm hiển thị".

**Những gì trợ lý không bao giờ làm** (mỗi dòng gắn với một cơ chế kỹ thuật, không chỉ prompt):
| Không bao giờ | Nguồn | Cơ chế chặn |
|---|---|---|
| Nêu chỗ, giá, bài, khuyến mãi không có trong kết quả công cụ | ĐB A12, §6 | Kiểm chứng ID + regex tiền; giá chỉ hiện trên thẻ |
| Gán cho bài điều bài không nói | ĐB A12 | Chỉ được trích `snippet` có `span_id`; chấm tay faithfulness |
| Gợi ý bài đã ẩn/gỡ | ĐB A12, A14 | Công cụ chỉ trả bài `visible`; hydrate lại thẻ khi mở lại hội thoại |
| Đặt chỗ, giữ chỗ, thanh toán | ĐB A12; TK Q6 | Không có công cụ ghi vào Booking/Payment |
| Tạo khan hiếm giả ("chỉ còn 1 phòng, đặt ngay!") | ĐB §5, §6 | Không cho LLM nêu số lượng tồn; thẻ chỉ hiện `available/sold_out` |
| Lộ dữ liệu người khác (ngày đi, giá đã trả, người đi cùng) | ĐB §5, §6; TK §8.3 | Công cụ bài chỉ trả trường công khai |
| Dùng dữ liệu thanh toán, giấy tờ | TK §8.4 | Không có công cụ đọc Payment, hồ sơ hành khách |
| Làm theo lệnh nằm trong nội dung bài/bình luận | BC AI §6 | Bọc kết quả công cụ là dữ liệu; công cụ chỉ đọc |
| Trả lời bằng ngôn ngữ khác tiếng Việt, đặt dịch vụ ngoài Vinpearl | ĐB §8 | System prompt + phạm vi công cụ |
| Giấu bài mô phỏng / bài tài trợ | ĐB §5; TK §6 | Nhãn đi theo thẻ từ Social |

### Gaps
- TK §4 chưa có vị trí trợ lý trong giao diện app (video dọc) và web; cần N1 (Phong) và N2 (Thanh) chốt.
- Chưa có nguồn nội bộ nào xác định *danh sách chủ đề từ chối* và *mức "nguy hiểm"* trong ngữ cảnh du lịch (ví dụ lặn biển tự do, leo núi không hướng dẫn); cần hỏi mentor hoặc nhóm chốt.
- Chưa có quyết định nhóm về trợ lý có gợi ý **vé máy bay** không: A12 chỉ liệt kê "khách sạn, khu vui chơi, điểm đến".
- Mức "hỏi dồn dập" không được định lượng trong ĐB; con số 1 câu/lượt, 2 lượt liên tiếp là đề xuất.

---

## 3. Bộ công cụ (chỉ đọc) mà trợ lý được gọi: schema vào/ra và module đứng sau

### Takeaway
Trợ lý cần **8 công cụ chỉ đọc**, tất cả là adapter gọi `/internal/...` của module khác (trong monolith có thể là lời gọi hàm nhưng chữ ký giữ làm hợp đồng). BC mới có 2 endpoint công cụ của Search và `/internal/prices` của Booking; ghi chú này đề xuất thêm **5 endpoint nội bộ** (parse câu, gu, xu hướng cho trợ lý, ngữ cảnh người dùng gộp, phần còn thiếu) để không ai đọc thẳng bảng của module khác. **Không có** công cụ nào chạm Payment hay ghi vào Booking.

### Cited Findings
- Công cụ của trợ lý là adapter gọi Search (`/internal/search/tools/places`, `/tools/posts`, "chỉ trả bài đang hiển thị, dùng chung bộ lọc và ngưỡng 'không có kết quả'"), Booking (`/internal/prices` để thẻ hiện giá thật lúc hiển thị), Social (`/internal/posts?ids=` để lấy đoạn trích thật cho B1 và trích dẫn), Access (`/v1/me/preferences`) — [BC Phần II AI §4](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Search công bố `POST /internal/search/tools/places · /tools/posts` cho AI; `GET /internal/search/similar-products`; `GET /internal/trends`; `GET /v1/recommendations?context=post_booking&booking_id=` (A9) — [BC Phần II Search §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Booking công bố `GET /internal/prices?sku_ids=&dates=` cho "Search, AI, Notification" (giá/chỗ thật tại thời điểm hỏi), `GET /v1/products/{id}` (trang chi tiết A4), `GET /v1/me/trips` — [BC Phần II Booking §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Social `GET /internal/posts?ids=` cho Search, AI, **chỉ trả bài `visible`**; bảng `saves` (target_type post | product) — [BC Phần II Social §2, §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Access `GET · PUT /v1/me/preferences` (bên gọi: App, Web, AI); `GET /internal/users/{id}/authz` trả claims, tuổi, `can_book` — [BC Phần II Access §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Search giữ `user_taste` (taste_vector, tag_weights), `query_logs.parsed {budget_vnd, budget_basis, pax, dates, location, vibe_tags}`, `recommendation_logs`; `GET /v1/search` trả `parsed`, `clarification`, `relax_suggestions` — [BC Phần II Search §2, §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Danh sách công cụ gợi ý trong ghi chú trước: `search_places(q, province?, budget?, guests?)`, `search_posts(q, ...)`, `get_entity(entity_type, id)`, `get_recommendations(surface)`, `get_trip_gaps(booking_id)`, `ask_clarification(question, options[])`; tool là adapter in-process vào Search — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Thông tin trợ lý được dùng: "gu, ngân sách, thích/lưu, điểm đến đã đặt. Không dùng thanh toán, giấy tờ, dữ liệu người khác" — [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- A12: "dùng được cả những gì họ đã thích, lưu trên ứng dụng" — [ĐB §4 A12](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Quy ước: đường giữa module có tiền tố `/internal/...`; "chữ ký vẫn giữ làm hợp đồng để không ai đọc thẳng bảng của module khác" — [BC Phần I "Một bộ quy ước API"](../../reports/Kiến%20trúc%20module%20Tripflix.md).

### Inferences

Tất cả công cụ nhận ngầm `actor` (`user_id` hoặc `anon_id`) từ phiên, **không** để LLM tự điền định danh. Kết quả công cụ được ghi vào `messages` (role `tool`) và tạo tập `allowed_ids` của lượt cho bộ kiểm chứng.

| # | Công cụ (tên cho LLM) | Đầu vào (JSON schema rút gọn) | Đầu ra | Module + endpoint đứng sau | Trạng thái |
|---|---|---|---|---|---|
| T1 | `parse_query` (server tự gọi trước LLM, không cần cho LLM thấy) | `{text, conversation_context?}` | `{parsed:{destination_ids[], budget_max_vnd, budget_basis: per_person\|group\|unknown, pax, date_from, date_to, vibe_tags[], product_types[]}, missing_critical[], clarification?}` | **Search** `POST /internal/search/parse` | [Đề xuất] — dùng chung QueryParser của A6 để trợ lý và ô tìm hiểu câu giống nhau |
| T2 | `search_places` | `{query_text, filters:{destination_ids?, budget_max_vnd?, budget_basis?, pax?, date_from?, date_to?, product_types?: [hotel\|ticket\|combo\|destination], vibe_tags?}, limit≤5}` | `{request_id, items:[{entity_type: product\|place, id, name, destination_id, product_type, match_reasons:[{type, text}], score}], no_result_reason?, relax_suggestions?[]}` | **Search** `POST /internal/search/tools/places` | Có trong BC |
| T3 | `check_price_availability` | `{items:[{product_id, sku_id?}], date_from, date_to, pax}` | `{items:[{product_id, sku_id, price_vnd, price_basis, available: bool, checked_at}]}` | **Booking** `GET /internal/prices?sku_ids=&dates=` | Có trong BC; cần Booking hỗ trợ `pax` hoặc AI tự quy đổi [Đề xuất] |
| T4 | `search_posts` | `{query_text, place_ids?, destination_ids?, vibe_tags?, limit≤5}` | `{items:[{post_id, place_ids[], kind, author_public:{display_name, avatar}, labels:{verified_traveled, simulated, sponsored}, snippets:[{span_id, text}], has_itinerary, itinerary_public_summary?}], no_result_reason?}` | **Search** `POST /internal/search/tools/posts` (hydrate qua **Social** `/internal/posts?ids=`, chỉ `visible`) | Có trong BC; trường `snippets[].span_id` là [Đề xuất] để kiểm trích dẫn |
| T5 | `get_entity` | `{type: product\|place\|post, id}` | Trường công khai của thực thể (không giá đã trả, không ngày đi của người đăng) | **Booking** `GET /v1/products/{id}` (bản nội bộ) · **Social** `/internal/posts?ids=` | Có endpoint; tên công cụ theo GC-SA |
| T6 | `get_user_context` | `{}` (actor từ phiên) | `{declared:{budget_min_vnd, budget_max_vnd, budget_basis, tastes[]}, inferred:{top_tags[], recent_saved:[{type, id}]}, trips:[{destination_id, date_from, date_to, product_types[]}], consent:{use_activity_data}}` | Gộp từ **Access** `GET /internal/users/{id}/preferences` [Đề xuất, bản nội bộ của `/v1/me/preferences`] · **Search** `GET /internal/search/taste/{actor}` [Đề xuất] · **Social** `GET /internal/users/{id}/saves?limit=` [Đề xuất] · **Booking** `GET /internal/users/{id}/trip-destinations` [Đề xuất; chỉ điểm đến + khoảng ngày + loại, không giá, không mã] | [Đề xuất]; khách chỉ có `inferred` theo `anon_id` + slot hội thoại |
| T7 | `get_trending` | `{destination_id?, limit≤5}` | `{items:[{entity_type: post\|place, id, reason_code, reason_text}]}` | **Search** `GET /internal/trends` (đã có cho Administration) — bản lọc cho người dùng [Đề xuất] | Dùng cho luồng "gợi cảm hứng" |
| T8 | `get_trip_gaps` | `{booking_id}` | `{items:[{product_id, product_type, reason{type, text}}]}` | **Search** bản nội bộ của `GET /v1/recommendations?context=post_booking&booking_id=` | [Đề xuất]; Search kiểm đơn thuộc actor (`FORBIDDEN` nếu không) |
| T9 | `get_post_summary` (B1) | `{subject_type: place\|product, subject_id}` | `{pros:[{text, source_post_ids[]}], cons:[...], post_count, generated_at}` hoặc `not_enough_posts` | **AI** (bảng `post_summaries` của chính module) | Tùy chọn, tuần 3 |
| — | `ask_clarification` / `refuse` / `final_answer` | Đầu ra có cấu trúc | Kết thúc lượt | Không gọi module nào (pseudo-tool) | Theo GC-SA |

Quy tắc chung cho bộ công cụ [Đề xuất]:
- **Chỉ đọc**; công cụ "ghi" duy nhất có thể có là `propose_save_preference` sinh **nút xác nhận** trên UI; việc ghi thật do client gọi `PUT /v1/me/preferences` của Access sau khi người dùng bấm (giữ đúng tinh thần "không tự làm thay người dùng").
- **Payment: không có công cụ nào** (TK §8.4 cấm dùng dữ liệu thanh toán). **Notification: không có công cụ** (trợ lý không gửi thông báo).
- `get_user_context` tôn trọng cờ `use_activity_data` (cài đặt của người dùng, xem mục 4); nếu tắt thì chỉ trả `declared`.
- Giới hạn: tối đa 3–4 vòng công cụ/lượt (BC); mỗi công cụ timeout ~1,5–2 giây [Đề xuất]; cache kết quả T2/T4 theo `hash(parsed)` trong Redis TTL ngắn, xóa khi có `post.hidden`/`product.discontinued` (BC sơ đồ ẩn bài: "bỏ khỏi cache kết quả công cụ").
- Hai lớp chống bài ẩn: công cụ chỉ trả `visible` (lúc tìm) **và** thẻ được hydrate lại lúc trả (lúc hiển thị), khớp "lưới an toàn lúc đọc" của BC.

### Gaps
- Chưa có endpoint nào trong BC cho: parse câu nội bộ, gu theo actor, danh sách đã lưu nội bộ, điểm đến đã đặt rút gọn, xu hướng cho người dùng cuối. Năm endpoint này phải được Duy/Phong (Search), Thanh (Social), Thắng (Booking), Dung (Access) đồng ý ở buổi họp ghép.
- Chưa rõ `/internal/prices` trả giá theo đầu người hay theo phòng/đêm; trợ lý cần `price_basis` để so với ngân sách "dưới 2 củ một người" (A6) — phụ thuộc cấu trúc bộ dữ liệu chưa thấy (BC Phần III).
- Chưa có định nghĩa "đoạn trích" (`span_id`) ở Social; nếu không có, bộ kiểm chứng chỉ kiểm được ID bài, không kiểm được câu trích.

---

## 4. Dữ liệu module AI lưu, thời hạn lưu, xử lý phiên khách ẩn danh và gộp khi đăng nhập

### Takeaway
AI giữ **hội thoại, tin nhắn, slot ngữ cảnh, log từng lần gọi LLM + bảng giá có phiên bản, kết quả kiểm chứng, phản hồi, tóm tắt B1, bộ câu thử và kết quả chạy**; **không** giữ sở thích khai báo (để ở Access) hay gu suy ra (ở Search). Hội thoại thành viên 30 ngày (TK); hội thoại khách nên nằm **ở server theo `anon_id` 7 ngày** thay vì trên thiết bị (đề xuất của BC, khác TK). Gộp khi đăng nhập dựa trên sự kiện `user.logged_in {user_id, anon_id}` của Access, cộng **gộp nội tuyến** để tránh lỗi đua khi sự kiện đến muộn hơn request kế tiếp.

### Cited Findings
- Câu hỏi mở: "Trợ lý nhớ những gì, nhớ trong bao lâu? Chưa đăng nhập thì sao? Đang trò chuyện trên ứng dụng thì mở web có tiếp tục được không? Trợ lý được dùng những thông tin nào của người dùng?" — [ĐB §5](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Đề xuất đã chốt: "hội thoại 30 ngày; sở thích (ngân sách, gu) người dùng xem/sửa/xóa được; chưa đăng nhập thì **lưu trên thiết bị 7 ngày**, đăng nhập thì gộp; tiếp tục được giữa app và web" — [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- Khối dữ liệu "Hội thoại trợ lý: lịch sử, bộ nhớ sở thích, chi phí mỗi cuộc" thuộc M5; khối "Tài khoản và hồ sơ: tuổi, **ngân sách, gu**, vai trò nhân viên, hồ sơ hành khách" thuộc M9 — [TK §10.2](../../docs/03-y-tuong-trien-khai.md).
- BC: bảng `conversations` (id, user_id?, anon_id?, started_platform, title, created_at, last_message_at, expires_at, merged_at; 30 ngày thành viên, 7 ngày khách), `messages` (role, content JSONB, tool_name, tool_args, cited_product_ids[], cited_post_ids[], clarifying, refused, refusal_reason, client_msg_id), `llm_calls` (purpose, provider, model, input/output/cache_read/cache_write tokens, latency_ms, cost_vnd, price_version, status), `model_prices` (có phiên bản, nhập tay, source_url), `answer_validations`, `post_summaries`, Redis `ai:quota:{user|anon}:{yyyymmdd}`, `ai:cost:global:{yyyymmdd}`, `eval_runs`, `ai_feedback` — [BC Phần II AI §2](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- BC: "muốn gộp khi đăng nhập trên thiết bị khác thì hội thoại khách nên nằm ở server theo `anon_id` với hạn 7 ngày, thiết bị chỉ giữ `conversation_id` [Đề xuất]" — [BC Phần II AI §7](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Sở thích có ba chủ: khai báo ở Access, suy ra ở Search, bộ nhớ hội thoại ở AI; trợ lý đọc/ghi sở thích khai báo qua API của Access; "đây là điểm docs/03 đang nói hai cách" — [BC Phần I "Năm chỗ xếp dễ tranh cãi"](../../reports/Kiến%20trúc%20module%20Tripflix.md); [GC-MM §1 Inferences mục 7, §2 Gaps](../Kiến%20trúc%20module%20Tripflix/module_mapping.md).
- Access cấp `anon_id` qua `POST /v1/auth/anonymous`, client gửi `X-Anon-Id`; `POST /v1/auth/otp/verify` kèm `X-Anon-Id`: "1 transaction: tìm/tạo user, đánh dấu anon_id đã gộp, cấp phiên MỚI", phát `user.logged_in {user_id, anon_id, platform}`; AI: "UPDATE conversations SET user_id WHERE anon_id (idempotent)"; bảng `anonymous_identities (anon_id, first_seen, merged_user_id, merged_at)` — [BC Phần I sơ đồ "Đăng nhập giữa chừng"; Phần II Access §2, §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Phong bì sự kiện có `event_id`; giao **ít nhất một lần**, bên nhận khử trùng bằng `processed_events (consumer, event_id)`; không giả định thứ tự — [BC Phần I quy ước](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Ghi chú trước đề xuất long-term memory chỉ lưu "facts" có cấu trúc, không lưu nguyên văn chat vào prompt; context = N lượt cuối + tóm tắt cuộn; chi phí lưu theo micro-USD và quy VNĐ khi hiển thị qua tỷ giá cấu hình — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Câu hỏi cho mentor đã nêu: "Lưu hội thoại 30 ngày có cần cho người dùng tắt không?" — [BC Phần II AI §8](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Quy định bộ câu thử: ≥ 200 câu (TK §12); nhóm câu: có dấu, không dấu, teencode, trộn tiếng Anh, mơ hồ ngân sách, không có kết quả, ngoài phạm vi, nguy hiểm, red-team — [BC Phần II AI §7](../../reports/Kiến%20trúc%20module%20Tripflix.md); bảng `eval_queries`, `eval_judgments`, `eval_runs` — [GC-SA §6](../Kiến%20trúc%20module%20Tripflix/search_ai.md).

### Inferences

**Bảng dữ liệu của module AI [kế thừa BC; phần mới ghi Đề xuất]**
| Bảng / khóa | Trường bổ sung so với BC | Thời hạn lưu | Ai đọc |
|---|---|---|---|
| `conversations` | + `context JSONB` (slot đang dùng: destination_ids, budget, basis, pax, dates, vibe_tags) [Đề xuất]; + `entry_point`, `origin_post_id?` [Đề xuất] | Thành viên: 30 ngày kể từ `last_message_at` (TK). Khách: 7 ngày ở server theo `anon_id` (BC) | Chủ hội thoại; quản trị (B7) |
| `messages` | + `cards JSONB` (chỉ `{type, id}` + thứ tự, **không lưu giá**) [Đề xuất]; + `mode` llm/search_only | Theo hội thoại | Như trên |
| `llm_calls` | + `purpose` thêm `eval`, `judge` [Đề xuất]; + `cost_provider_currency` song song `cost_vnd` [Đề xuất, giải xung đột micro-USD/VNĐ] | Giữ tới hết dự án (≥ báo cáo tuần 4), **không chứa nội dung**; khi xóa hội thoại chỉ đặt `message_id = NULL` [Đề xuất] | AI, Administration (B7) |
| `model_prices`, `fx_rates` [fx_rates từ GC-SA] | — | Vĩnh viễn (có phiên bản) | AI |
| `answer_validations` | + `pre_validation_invalid_ids` để đo "mô hình muốn bịa" (BC §7 đo trước/sau) | Như `llm_calls` | AI, B7 |
| `ai_feedback` | `rating up/down, reason` | Theo hội thoại | B7 |
| `assistant_settings` [Đề xuất] | `user_id, use_activity_data bool (mặc định bật), save_history bool (mặc định bật)` | Tới khi xóa tài khoản | Người dùng, AI |
| `post_summaries` (B1) | + `source_post_ids[]` bắt buộc, `stale bool` | Tạo lại khi `stale`; vô hiệu ngay khi `post.hidden/deleted` | Công khai (chỉ ý có nguồn `visible`) |
| `eval_question_sets`, `eval_items`, `eval_runs`, `eval_results` | `eval_items(category, text, expected_behavior: suggest\|clarify\|no_result\|refuse, expected_ids?)`; `eval_results(run_id, item_id, pass, invalid_ids, cost_vnd, latency_ms)` | Vĩnh viễn, có phiên bản (cũng lưu trong repo) | Nhóm, slide demo |
| Redis | `ai:quota:{actor}:{yyyymmdd}`, `ai:cost:global:{yyyymmdd}` (BC); `ai:inflight:{conversation_id}` khóa 1 luồng/hội thoại [Đề xuất]; `ai:toolcache:{hash}` | TTL theo ngày / theo lượt | AI |

**Không lưu ở AI** (để tránh trùng nguồn): ngân sách/gu khai báo (Access `user_preferences`), gu suy ra và lượt thích/lưu (Search `user_taste`, Social `saves`), giá (Booking). Khi người dùng nói "mình thường đi 2 triệu/người", AI chỉ ghi vào `conversations.context` và **đề nghị** lưu sang Access qua nút xác nhận (AI-20).

**Hội thoại khách và gộp khi đăng nhập [Đề xuất chi tiết hóa BC]**
1. Mở trợ lý khi chưa đăng nhập: client đã có `anon_id` (Access) → `POST /v1/assistant/conversations` với `X-Anon-Id` → server lưu `conversations.anon_id`, `expires_at = now + 7 ngày`. Thiết bị chỉ giữ `conversation_id` (+ bản cache hiển thị để đọc lại khi mất mạng).
2. Đăng nhập giữa chừng (OTP/Google) kèm `X-Anon-Id` → Access phát `user.logged_in {user_id, anon_id, platform, device_id}`.
3. AI tiêu thụ sự kiện (khử trùng theo `event_id`): `UPDATE conversations SET user_id = :uid, merged_at = now(), expires_at = last_message_at + 30 ngày WHERE anon_id = :aid AND user_id IS NULL`.
4. **Chống lỗi đua**: request kế tiếp có thể đến trước sự kiện. Khi request mang token `user_id` hỏi một hội thoại có `user_id IS NULL`, AI hỏi Access `anonymous_identities` (qua `/internal/users/{id}/authz` mở rộng trả `merged_anon_ids[]` [Đề xuất]) và gộp nội tuyến nếu khớp; nếu không khớp → `AUTH_REQUIRED`/404.
5. Hạn mức trong ngày khi gộp: cộng số tin của `anon_id` vào `user_id` (tránh lách hạn mức bằng cách đăng nhập) [Đề xuất].
6. App ↔ web: thành viên gọi `GET /v1/assistant/conversations` ở thiết bị khác; tùy chọn Notification đẩy `user:{id}` type `assistant_message` để thiết bị đang mở thấy ngay (mục 6).
7. Khách không đăng nhập trên thiết bị khác → không tiếp tục được (không có định danh chung); đây là giới hạn chấp nhận được và phải nói rõ khi trả lời câu hỏi mở.

**Trả lời 4 câu hỏi mở (để đưa vào demo tuần 3)**
- *Dựa vào đâu, làm sao không bịa*: chỉ kết quả công cụ của Search/Booking/Social trong lượt; bộ kiểm chứng; số liệu `answer_validations` trước/sau (TK §8.4, BC §6).
- *Nhớ gì, bao lâu*: hội thoại + slot 30 ngày (thành viên), 7 ngày (khách, server); sở thích dài hạn chỉ khi người dùng xác nhận và nằm ở Access, xem/sửa/xóa được; người dùng xóa hội thoại bất kỳ lúc nào; tắt `save_history` thì hội thoại hết hạn khi đóng phiên [Đề xuất].
- *Dùng thông tin nào*: gu, ngân sách, thích/lưu, điểm đến đã đặt (TK §8.4) — có công tắc `use_activity_data`; không thanh toán, giấy tờ, dữ liệu người khác.
- *Tốn bao nhiêu, giới hạn*: đo từng lần gọi, VNĐ/cuộc p50/p95; 20/50 tin/ngày; trần chi phí ngày → chế độ chỉ tìm kiếm (TK §8.4, BC §7).

### Gaps
- TK §8.4 ("lưu trên thiết bị 7 ngày") và BC ("lưu server theo `anon_id` 7 ngày") **mâu thuẫn**; nhóm chưa chốt.
- Danh mục sự kiện của BC **không có sự kiện xóa tài khoản** (`user.deleted`), nên chưa có cách để AI xóa hội thoại khi người dùng xóa tài khoản.
- Chưa có quy định nội bộ về việc nhân viên quản trị đọc nội dung hội thoại (B7) — có cần che SĐT/email trong màn B7 không; ghi chú đề xuất che, nhưng chưa có nguồn nhóm.
- Chưa rõ thời hạn lưu `llm_calls`; ĐB §7 chỉ yêu cầu "đo và báo cáo".

---

## 5. Danh sách API đầy đủ của module AI (công khai, nội bộ, quản trị/B7), ví dụ JSON cho endpoint chat SSE, mã lỗi, giới hạn tần suất

### Takeaway
Giữ **đúng đường dẫn của BC** (`/v1/assistant/...`, `/internal/assistant/usage`, `/internal/ai/classify`, B1 tại `/v1/places|products/{id}/summary`), không dùng `/api/v1/ai/...` của ghi chú cũ. Bổ sung 7 endpoint [Đề xuất] (hạn mức, cài đặt, xóa hết, B7 chi tiết, eval, parse-query qua cổng). Chat dùng **SSE nhưng không stream chữ tự do**: stream `status` trong lúc gọi công cụ, rồi gửi `text`/`cards` **đã qua kiểm chứng**, cuối cùng `done` có usage và `cost_vnd`; có chế độ JSON một lần cho mạng yếu.

### Cited Findings
- Quy ước: `/v1`, tiền số nguyên VNĐ, ISO 8601, phân trang con trỏ `{items, next_cursor}`, `Idempotency-Key`, khung lỗi `{error:{code, message, details, retryable}}`; mã lỗi chung `RATE_LIMITED` (429 + `Retry-After`), `AUTH_REQUIRED` (401, kèm `return_to`), `FORBIDDEN`, `VALIDATION_FAILED` (422), `IDEMPOTENCY_CONFLICT` (422), `REQUEST_IN_PROGRESS` (409), `POST_UNAVAILABLE` — [BC Phần I "Một bộ quy ước API"](../../reports/Kiến%20trúc%20module%20Tripflix.md); 6 mã chung gồm `RATE_LIMITED` — [TK §10.2](../../docs/03-y-tuong-trien-khai.md).
- API của AI trong BC: `POST /v1/assistant/conversations`; `POST /v1/assistant/conversations/{id}/messages` `{text, client_msg_id}`, phản hồi gồm đoạn văn + thẻ `{type: product|place|post, id}` + câu hỏi lại hoặc lời từ chối, "luồng SSE các sự kiện `status`, `cards`, `clarification`, `done {usage, cost_vnd}`", lỗi `RATE_LIMITED`, `AUTH_REQUIRED` khi hội thoại thuộc người khác; `GET /v1/assistant/conversations?cursor= · /{id}`; `DELETE /v1/assistant/conversations/{id}`; `POST /v1/assistant/messages/{id}/feedback`; `GET /v1/places/{id}/summary · /v1/products/{id}/summary` (404 khi chưa đủ bài); `GET /internal/assistant/usage?from=&to=&group_by=`; `POST /internal/ai/classify` (503 khi vượt ngân sách) — [BC Phần II AI §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Administration làm mặt tiền B7: `GET /v1/admin/assistant/metrics · /assistant/conversations` (chỉ quản trị) — [BC Phần II Administration §3](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- "Bản demo nên sinh xong, kiểm, rồi mới trả (hoặc chỉ stream trạng thái 'đang tìm…'), thay vì stream chữ tự do" — [BC Phần II AI §6](../../reports/Kiến%20trúc%20module%20Tripflix.md); "khi stream text, validator chỉ chạy được trên phần cards/cites ở cuối" — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Hạn mức khởi điểm 20 tin/ngày (khách), 50 tin/ngày (thành viên) — [TK §8.4](../../docs/03-y-tuong-trien-khai.md); trần chi phí toàn hệ thống, vượt thì "chỉ tìm kiếm" — [BC Phần II AI §7](../../reports/Kiến%20trúc%20module%20Tripflix.md); `assistant.budget_exceeded` — [BC Phần II AI §5](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Ghi chú cũ đề xuất giới hạn khác ("ẩn danh 10 tin/giờ, đăng nhập 50 tin/ngày — số giả định") và cắt tin nhắn dài, giới hạn `max_tokens` — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Mạng di động yếu là chuyện thường — [ĐB §7](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md); môi trường đo chuẩn ~400 kbps, ~400 ms — [TK §12](../../docs/03-y-tuong-trien-khai.md).
- Câu hỏi cho mentor: "Có chấp nhận trợ lý trả lời sau 3–5 giây (không stream chữ) để đổi lấy kiểm chứng đầy đủ không?" — [BC Phần II AI §8](../../reports/Kiến%20trúc%20module%20Tripflix.md).

### Inferences

**A. API công khai (App, Web; khách dùng được trừ khi ghi khác)**
| Method | Path | Mục đích | Lỗi chính | Nguồn |
|---|---|---|---|---|
| POST | /v1/assistant/conversations | Mở hội thoại `{entry_point, context?{post_id?, product_id?, search_query?, booking_id?}}` → `{conversation_id, greeting, suggested_prompts[]}` | `RATE_LIMITED`, `POST_UNAVAILABLE` (bài ngữ cảnh đã ẩn) | BC + ngữ cảnh [Đề xuất] |
| POST | /v1/assistant/conversations/{id}/messages | Gửi câu `{client_msg_id, text}`; `Accept: text/event-stream` (SSE) hoặc `application/json` (một lần) | `RATE_LIMITED`, `AUTH_REQUIRED`, `VALIDATION_FAILED`, `IDEMPOTENCY_CONFLICT`, `REQUEST_IN_PROGRESS` | BC + JSON mode [Đề xuất] |
| GET | /v1/assistant/conversations?cursor= | Danh sách hội thoại (thành viên: mọi thiết bị; khách: theo `anon_id`) | — | BC |
| GET | /v1/assistant/conversations/{id}?cursor= | Tin nhắn; **thẻ được hydrate lại lúc đọc** (bài ẩn → `{unavailable:true}`, giá lấy mới) | 404, `AUTH_REQUIRED` | BC + hydrate [Đề xuất] |
| DELETE | /v1/assistant/conversations/{id} | Xóa một hội thoại | 404 | BC |
| DELETE | /v1/assistant/conversations | Xóa tất cả hội thoại của mình | `AUTH_REQUIRED` cho thành viên | [Đề xuất] |
| POST | /v1/assistant/messages/{id}/feedback | `{rating: up\|down, reason?}` | 404 | BC |
| GET | /v1/assistant/quota | `{limit, used, remaining, reset_at, mode}` để UI báo trước | — | [Đề xuất] |
| GET · PUT | /v1/me/assistant-settings | `{use_activity_data, save_history}` | `AUTH_REQUIRED` | [Đề xuất] |
| GET | /v1/places/{id}/summary · /v1/products/{id}/summary | B1, chỉ từ bài `visible`, kèm `source_post_ids` | 404 `SUMMARY_NOT_READY` (chưa đủ bài) [mã Đề xuất] | BC |

Lượt bấm thẻ: client gửi qua `POST /v1/analytics/events` của Administration với `props {surface: assistant, message_id, card_type, card_id, position}`; bấm thẻ bài đồng thời gửi `POST /v1/interactions/batch` của Social có `reco_request_id = message_id` để Search học gu [Đề xuất tái dùng endpoint có sẵn].

**B. API nội bộ (module khác gọi AI)**
| Method | Path | Bên gọi | Mục đích | Nguồn |
|---|---|---|---|---|
| GET | /internal/assistant/usage?from=&to=&group_by=day\|purpose\|model\|platform\|actor_type | Administration | B7: token, VNĐ, số lần gọi, p95 độ trễ, tỷ lệ fallback | BC (+ các `group_by` [Đề xuất]) |
| GET | /internal/assistant/conversations?filter=refused\|fallback\|stripped\|thumbs_down\|no_result&from=&to=&cursor= | Administration | B7 "trợ lý từ chối gì, gợi ý gì" | [Đề xuất] |
| GET | /internal/assistant/conversations/{id} | Administration | Xem chi tiết (che SĐT/email trong nội dung) | [Đề xuất] |
| GET | /internal/assistant/topics?from=&to= | Administration | B7 "người dùng hỏi gì": nhóm theo `parsed` (điểm đến, ngân sách, vibe) | [Đề xuất] |
| GET | /internal/assistant/eval-runs[/{id}] | Administration | Kết quả bộ câu thử cho báo cáo §9 | [Đề xuất] |
| POST | /internal/ai/classify | Administration | Gợi ý nhãn, `reason_code`, tóm tắt case qua cổng LLM chung; 503 `AI_BUDGET_EXCEEDED` | BC |
| POST | /internal/ai/parse-query | Search | LLM dự phòng khi luật của QueryParser không đủ (`purpose=query_parse`); Search vẫn kiểm lại kết quả | [Đề xuất; khớp `purpose` trong BC] |

**C. API quản trị (mặt tiền thuộc Administration, dữ liệu từ B)**: `GET /v1/admin/assistant/metrics`, `GET /v1/admin/assistant/conversations` (BC, chỉ quản trị); thêm [Đề xuất] `GET /v1/admin/assistant/eval-runs`, `GET · PUT /v1/admin/assistant/limits` (quản trị chỉnh hạn mức tin/ngày và trần VNĐ/ngày, ghi audit).

**D. Ví dụ chat SSE [Đề xuất; số tiền, ID là giá trị minh họa, không phải dữ liệu thật]**

Request:
```http
POST /v1/assistant/conversations/conv_7Hk2/messages
X-Anon-Id: anon_91c0          (hoặc Authorization: Bearer …)
Accept: text/event-stream
Content-Type: application/json

{ "client_msg_id": "4b1e9c2a-…", "text": "cuoi tuan nay di dau chill ma duoi 3 cu" }
```

Luồng SSE trả về:
```text
event: ack
data: {"message_id":"msg_01","conversation_id":"conv_7Hk2","quota":{"remaining":17,"reset_at":"2026-11-03T00:00:00+07:00"}}

event: status
data: {"stage":"understanding","text":"Đang hiểu câu hỏi…"}

event: status
data: {"stage":"tool","tool":"search_places","text":"Đang tìm chỗ dưới 3 triệu cho cuối tuần 7–8/11…"}

event: status
data: {"stage":"tool","tool":"search_posts","text":"Đang tìm bài của người đã đi…"}

event: text
data: {"message_id":"msg_02","segments":[
  {"text":"Cuối tuần này (7–8/11), dưới 3 triệu mỗi người, mình thấy 2 chỗ hợp kiểu chill:","cites":[]},
  {"text":"một resort yên tĩnh gần biển ở Phú Quốc","cites":[{"type":"product","id":"prd_1021"}]},
  {"text":"và một bạn đã đi kể là buổi sáng rất vắng người.","cites":[{"type":"post","id":"pst_5530"}]}],
 "assumptions":[{"field":"budget_basis","value":"per_person","text":"Mình hiểu 3 củ là cho 1 người"}]}

event: cards
data: {"cards":[
  {"type":"product","id":"prd_1021","title":"…","price_from_vnd":1850000,"price_basis":"per_person","price_checked_at":"2026-11-02T09:14:05+07:00","availability":"available","reason":{"type":"budget_fit","text":"Dưới 3 triệu/người cho 1 đêm"},"deeplink":"tripflix://products/prd_1021"},
  {"type":"post","id":"pst_5530","title":"…","labels":["verified_traveled","simulated"],"snippet":{"span_id":"pst_5530#s2","text":"…"},"has_itinerary":true,"deeplink":"tripflix://posts/pst_5530"}]}

event: done
data: {"message_id":"msg_02","mode":"llm","validation":{"action":"pass","invalid_ids_before":0,"invalid_ids_after":0},
       "usage":{"llm_calls":2,"tool_calls":3,"input_tokens":0,"output_tokens":0,"cache_read_tokens":0},
       "cost_vnd":0,"latency_ms":0}
```
(`usage`, `cost_vnd`, `latency_ms` để 0 vì chưa đo; khi chạy thật lấy từ `llm_calls`.)

Biến thể kết thúc lượt:
- Hỏi lại: `event: clarification` `{"question":"2 triệu là cho mỗi người hay cả nhóm 4 đứa?","field":"budget_basis","options":[{"label":"Mỗi người","value":"per_person"},{"label":"Cả nhóm","value":"group"}]}` rồi `done`.
- Không có kết quả: `event: text` (câu nói thật) + `event: no_result` `{"reason":"over_budget","relax_suggestions":[{"label":"Nới lên 3,5 triệu/người","patch":{"budget_max_vnd":3500000}}]}` (lấy từ Search) rồi `done`.
- Từ chối: `event: refusal` `{"reason_code":"OUT_OF_SCOPE","text":"Cái này mình chưa giúp được nè, mình chỉ rành chuyện đi chơi thôi. Bạn muốn tìm chỗ nào cho cuối tuần không?"}` rồi `done`.
- Chế độ chỉ tìm kiếm (vượt trần/nhà cung cấp lỗi): `done.mode = "search_only"`, `text` là câu mẫu, `cards` từ Search.
- Lỗi giữa luồng: `event: error` `{"code":"…","message":"…","retryable":true}`; client gửi lại **cùng** `client_msg_id` sẽ nhận lại kết quả đã có (không tốn thêm lượt, không gọi LLM lại).

JSON một lần (`Accept: application/json`, cho mạng yếu hoặc proxy cắt SSE): `200 {"message_id","segments","cards","clarification"|null,"no_result"|null,"refusal"|null,"assumptions","mode","usage","cost_vnd"}`.

Lỗi trước luồng (khung chung):
```json
HTTP 429  Retry-After: 52200
{ "error": { "code": "RATE_LIMITED", "message": "Hôm nay bạn đã hỏi đủ 20 câu rồi, mai hỏi tiếp nhé. Đăng nhập để được 50 câu/ngày.",
  "details": { "limit": 20, "window": "day", "reset_at": "2026-11-03T00:00:00+07:00", "actor_type": "anon" }, "retryable": true } }
```

**E. Mã lỗi của AI**
| Mã | HTTP | Khi nào | Nguồn |
|---|---|---|---|
| `RATE_LIMITED` | 429 | Vượt tin/ngày, vượt nhịp/phút | Chung |
| `AUTH_REQUIRED` | 401 | Hội thoại thuộc tài khoản khác / cần đăng nhập để xem hội thoại thành viên | BC |
| `FORBIDDEN` | 403 | Gọi `/internal`, B7 khi không phải quản trị | Chung |
| `VALIDATION_FAILED` | 422 | Thiếu `client_msg_id`, text rỗng hoặc quá dài | Chung |
| `IDEMPOTENCY_CONFLICT` | 422 | Cùng `client_msg_id`, khác `text` | Chung |
| `REQUEST_IN_PROGRESS` | 409 | Đang có một lượt chạy trong hội thoại / cùng `client_msg_id` đang xử lý | Chung |
| `POST_UNAVAILABLE` | 404/410 | Mở trợ lý từ bài đã ẩn/gỡ | Chung |
| `CONVERSATION_EXPIRED` | 410 | Hội thoại quá 7/30 ngày | [Đề xuất] |
| `SUMMARY_NOT_READY` | 404 | B1 chưa đủ bài | [Đề xuất; BC ghi "404 khi chưa đủ bài"] |
| `AI_BUDGET_EXCEEDED` | 503 | Chỉ cho `/internal/ai/*`; với chat thì **không lỗi** mà chuyển `search_only` | BC (503) + [Đề xuất] |

**F. Giới hạn**
| Giới hạn | Giá trị khởi điểm | Nguồn |
|---|---|---|
| Tin/ngày | Khách 20, thành viên 50 | TK §8.4 |
| Trần chi phí toàn hệ thống/ngày | Cấu hình; vượt → `search_only` + `assistant.budget_exceeded` | BC §5, §7 |
| Vòng công cụ/lượt | ≤ 3–4 | BC §6 |
| Nhịp | ≤ 5 tin/phút/actor; 1 lượt chạy đồng thời/hội thoại | [Đề xuất] |
| Độ dài câu hỏi | ≤ 500 ký tự; lịch sử đưa vào prompt: N lượt cuối + tóm tắt cuộn | [Đề xuất]; GC-SA |
| Hội thoại mới | Khách ≤ 10/ngày | [Đề xuất] |
| Thời gian một lượt | Mục tiêu p95 ≤ 5 giây trên mạng chuẩn (cần mentor đồng ý, BC §8 câu 2); `status` đầu tiên ≤ 1 giây | [Đề xuất] |

### Gaps
- Chưa biết mentor có chấp nhận "không stream chữ" không (BC §8 câu hỏi 2).
- Con số 5 tin/phút, 500 ký tự, 10 hội thoại/ngày, p95 ≤ 5 giây là đề xuất chưa đo; phải chốt tuần 2 sau khi chạy bộ câu thử (TK: "Các con số… chốt ở tuần 2 sau khi đo").
- Các mã lỗi mới (`CONVERSATION_EXPIRED`, `SUMMARY_NOT_READY`, `AI_BUDGET_EXCEEDED`) cần N3 (Dung) đưa vào bản quy ước chung.

---

## 6. Sự kiện AI nhận/phát và hợp đồng giữa AI với Access, Notification, Administration, Booking, Social, Payment, Search

### Takeaway
AI là **bên nhận nhỏ, bên phát rất ít**: nhận `user.logged_in`, `user.preferences_updated`, `post.hidden/deleted/restored` (và tùy chọn `post.approved/updated`, `product.discontinued/price_changed` để làm mới cache và B1); phát `assistant.turn_completed` (một sự kiện mỗi lượt, đủ cho B7 và đo chi phí) và `assistant.budget_exceeded`. Đề nghị **giữ tên `assistant.*` của BC** thay vì `ai.message.completed`/`ai.cost.recorded`, và chỉ thêm `assistant.answer_flagged`. Với **Payment: không có hợp đồng nào, theo thiết kế**.

### Cited Findings
- Danh mục 30 sự kiện của BC: `user.logged_in` (PC; Access → AI, Search, Booking; trường user_id, anon_id, platform, device_id); `user.preferences_updated` (Access → Search, AI); `post.deleted` (Social → Search, AI, Notification); `post.hidden` (PC; Administration → Search, AI, Notification, Social; post_id, author_id, reason_code, hidden_at, action_id); `post.restored` (Administration → Search, AI, Social); `assistant.turn_completed` (AI → Administration B7; conversation_id, input_tokens, output_tokens, cost_vnd, refused, cited_ids, validation_action) — [BC Phần I "Ba mươi sự kiện"](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- AI nhận `user.logged_in`, `post.hidden`, `post.deleted`, `post.restored`, `user.preferences_updated`; phát `assistant.turn_completed` và `assistant.budget_exceeded` — [BC Phần II AI §5](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Gợi ý sự kiện của phân công: `post.hidden`, `comment.hidden` (M6 → M1, M2, **M5**, M10); `user.logged_in` (gộp phiên ẩn danh) (M9 → **M5** giữ hội thoại, M1 gu) — [PC §5.2](../../team/01-phan-cong-nghien-cuu-module.md).
- Ghi chú cũ dùng tên khác: phát `ai.message.completed`, `ai.budget.exceeded`, `ai.answer.flagged`; nhận `identity.user.logged_in` — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Quy ước tên sự kiện "thống nhất, ví dụ `post.hidden`, `booking.confirmed`, `interaction.created`" — [TK §10.2](../../docs/03-y-tuong-trien-khai.md).
- Sơ đồ ẩn bài: AI nhận `post.hidden` → "Vô hiệu post_summaries chứa bài, bỏ khỏi cache kết quả công cụ"; lưới an toàn: mọi kết quả hydrate qua Social, chỉ `visible`; đo từ `moderation_actions.at` tới lúc bài không còn trong câu trả lời trợ lý — [BC Phần I "Một quyết định ẩn bài"](../../reports/Kiến%20trúc%20module%20Tripflix.md). Mục tiêu bài ẩn biến mất ≤ 60 giây — [TK §12](../../docs/03-y-tuong-trien-khai.md).
- Cặp phải trao đổi: Phong ↔ Duy ↔ Bảo "dùng chung một mô hình embedding và một vector index"; Trang ↔ Phong, Duy, Bảo "bài bị ẩn phải biến mất khỏi bảng tin, tìm kiếm và trợ lý"; Dung ↔ tất cả "quy ước API, định dạng sự kiện, cách kiểm tra quyền" — [PC §5.1](../../team/01-phan-cong-nghien-cuu-module.md).
- Notification nhận rất nhiều sự kiện nhưng **không** có `assistant.*` trong danh sách; kênh SSE `user:{id}` cho A5, kết quả đặt chỗ, A9 — [BC Phần II Notification §3, §5](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Administration nhận `assistant.turn_completed` (B7); gọi AI `/internal/ai/classify`, `/internal/assistant/usage` — [BC Phần II Administration §4, §5](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Administration đi qua "cổng LLM" của AI cho API kiểm duyệt "để chung một chỗ đo chi phí [Đề xuất]"; nhóm có thể cho Administration gọi thẳng — [BC Phần I "Bảy module cần dịch vụ ngoài"](../../reports/Kiến%20trúc%20module%20Tripflix.md); [GC-MM §1 mục 9](../Kiến%20trúc%20module%20Tripflix/module_mapping.md).
- Booking giữ `source_post_id` trên `booking_drafts` và `bookings` (đơn đặt theo bài) — [BC Phần II Booking §2](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Không dùng dữ liệu thanh toán cho trợ lý — [TK §8.4](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Sự kiện AI nhận**
| Sự kiện | Từ | AI làm gì | Ưu tiên |
|---|---|---|---|
| `user.logged_in` | Access | Gộp hội thoại `anon_id` → `user_id`, dời `expires_at` 30 ngày, cộng hạn mức (mục 4) | Bắt buộc (A12) |
| `post.hidden` | Administration | Vô hiệu `post_summaries` có bài; xóa `ai:toolcache` chứa bài; không cần sửa tin nhắn cũ vì thẻ hydrate lúc đọc | Bắt buộc (A12, A14) |
| `post.deleted` | Social | Như `post.hidden` | Bắt buộc |
| `post.restored` | Administration | Đánh `stale` các `post_summaries` của chỗ liên quan | Nên có |
| `user.preferences_updated` | Access | Làm mới cache `get_user_context` | Nên có |
| `post.approved`, `post.updated (itinerary/media)` | Administration, Social | Đánh `stale` B1 của chỗ; không bắt buộc | Tùy chọn (B1) |
| `product.discontinued`, `product.price_changed` | Booking | Xóa cache công cụ có sản phẩm đó (giá trên thẻ vốn lấy mới) | Tùy chọn |
| `user.deleted` | Access | Xóa hội thoại, cài đặt | [Đề xuất sự kiện mới] |

**Sự kiện AI phát [giữ tên BC]**
| Sự kiện | Bên nhận | Trường | Ghi chú |
|---|---|---|---|
| `assistant.turn_completed` | Administration (B7, đo), **Notification** [Đề xuất thêm] | conversation_id, message_id, actor_type, platform, entry_point, llm_calls, input/output/cache tokens, cost_vnd, latency_ms, mode, refused, refusal_reason, clarifying, no_result, cited_ids, validation_action | Một sự kiện/lượt thay cho cặp `ai.message.completed` + `ai.cost.recorded`; chi phí của `summary`, `moderation_assist`, `query_parse` lấy bằng **kéo** `/internal/assistant/usage` thay vì thêm sự kiện. Notification chỉ dùng conversation_id để đẩy `user:{id}` type `assistant_message` (app ↔ web thấy ngay) |
| `assistant.budget_exceeded` | Administration | scope (global \| actor), date, cost_vnd, cap_vnd | Có trong BC |
| `assistant.answer_flagged` | Administration | message_id, cause [fallback \| stripped \| thumbs_down \| unverified_mention] | [Đề xuất]; đưa vào danh sách B7 cần xem; **không** vào hàng chờ kiểm duyệt nội dung |

**Hợp đồng theo từng module**
| Module | AI gọi (đồng bộ) | Module đó gọi AI | Sự kiện | Ghi chú/đề xuất |
|---|---|---|---|---|
| **Access** | `/internal/users/{id}/authz` (tuổi, `merged_anon_ids[]` [Đề xuất]); `/internal/users/{id}/preferences` [Đề xuất bản nội bộ]; client gọi `PUT /v1/me/preferences` khi người dùng xác nhận lưu sở thích | — | Nhận `user.logged_in`, `user.preferences_updated`, (`user.deleted` đề xuất) | Access cấp `anon_id`, là nguồn duy nhất của sở thích khai báo |
| **Notification** | — | — | [Đề xuất] Nhận `assistant.turn_completed` để đẩy đồng bộ hội thoại | Trợ lý không gửi thông báo đẩy; thẻ bài trong chat tự thay placeholder khi client nhận `post:{id} hidden` qua SSE (nếu đang mở) |
| **Administration** | — | `/internal/ai/classify`, `/internal/assistant/usage`, `/internal/assistant/conversations…`, `/topics`, `/eval-runs` | AI phát `assistant.*` → Administration | Màn B7 và chỉ số §9 ở Administration; nội dung hội thoại chỉ cho vai trò quản trị [Đề xuất theo BC "chỉ quản trị"] |
| **Booking** | `/internal/prices`; chi tiết sản phẩm; `/internal/users/{id}/trip-destinations` [Đề xuất] | — | Tùy chọn nhận `product.*` | [Đề xuất] Nháp đặt chỗ từ thẻ trợ lý mang `attribution {surface: assistant, message_id}` để đo "từ trợ lý tới đặt chỗ" (tương tự `source_post_id`) |
| **Social** | `/internal/posts?ids=` (chỉ `visible`, có `snippets`), `/internal/users/{id}/saves` [Đề xuất] | — | Nhận `post.deleted` (và `post.updated` tùy chọn) | Client bấm thẻ bài → `interaction.created` có `reco_request_id = message_id` |
| **Payment** | **Không** | **Không** | **Không** | Cố ý: TK §8.4 cấm dùng thanh toán; chi phí trợ lý là chi phí LLM, không liên quan Payment |
| **Search** | `/internal/search/parse` [Đề xuất], `/tools/places`, `/tools/posts`, `/internal/search/taste/{actor}` [Đề xuất], `/internal/trends`, post_booking recommendations nội bộ [Đề xuất] | `/internal/ai/parse-query` [Đề xuất] | Không trực tiếp (cùng nhận `post.hidden`) | Dùng chung mô hình embedding + vector index (PC §5.1); Search sở hữu chỉ mục, AI không giữ vector riêng trừ B1 nếu cần |

**Đo "bài ẩn biến khỏi trợ lý ≤ 60 giây" [Đề xuất cách đo cho Trang + Bảo]**: script ẩn một bài đang được trợ lý gợi ý, rồi mỗi 5 giây gửi cùng câu hỏi (bỏ qua cache câu trả lời) và mở lại hội thoại cũ; ghi thời điểm đầu tiên bài không còn trong `cards` mới và thẻ cũ chuyển `unavailable`. Kỳ vọng ~0 giây vì công cụ và hydrate đều lọc `visible` lúc đọc.

### Gaps
- Tên sự kiện chưa thống nhất giữa GC-SA (`ai.*`, `identity.*`) và BC (`assistant.*`, `user.*`); ghi chú này chọn BC nhưng nhóm (Dung, N3) phải chốt.
- Danh mục BC không cho Notification nhận `assistant.turn_completed`; nếu nhóm không thêm, đồng bộ hội thoại giữa app và web chỉ xảy ra khi mở lại màn (kéo), vẫn đạt "tiếp tục được" nhưng không "ngay".
- Chưa có quyết định cổng LLM chung có bắt buộc với Administration hay không (BC ghi nhóm có thể cho Administration gọi thẳng API kiểm duyệt).
- `booking_drafts` chưa có trường attribution cho trợ lý; không đo được tỷ lệ chuyển đổi từ trợ lý nếu Booking không thêm.

---

## 7. Lộ trình giao từng tuần của Bảo, khớp mốc đặc tả

### Takeaway
Theo ĐB §10, cột "Trợ lý" là: **T1** thử nghiệm cách làm + bộ câu hỏi thử; **T2** gợi ý địa điểm; **T3** đề xuất bài đăng, chạy trên cả app và web (thứ Năm ngừng làm tính năng); **T4** báo cáo. Đường găng là **hợp đồng công cụ với Search** (T1) và **bài mô phỏng của Social** (T1), vì không có bài thì không có A12 phần bài đăng.

### Cited Findings
- Mốc: T1 "Thử nghiệm cách làm, bộ câu hỏi thử", phải trả lời "Các bạn hiểu bài toán thế nào? Chọn cách làm cho từng sản phẩm ra sao, vì sao? Bản đề xuất cách đo"; T2 "Gợi ý địa điểm", "Cách đo đã chốt"; T3 "Đề xuất bài đăng, chạy trên cả ứng dụng và web", phải trả lời "Đề xuất cho câu hỏi mở về đặt theo lộ trình **và trợ lý**. Kết quả đo theo mục 9. Vì sao chọn những phần mở rộng này? Thứ Năm ngừng làm thêm tính năng"; T4 "Báo cáo và trình bày… Nếu có thêm thời gian, các bạn làm gì tiếp?" — [ĐB §10](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Đội tự đề xuất cách đo cuối T1, chốt cuối T2, báo cáo cuối kỳ — [ĐB §9](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).
- Việc của Bảo: nghiên cứu "so sánh các LLM về chất lượng tiếng Việt và chi phí · chỉ trả lời từ dữ liệu thật (RAG, gọi công cụ tra cứu, kiểm tra ID trước khi trả) · hỏi lại khi thiếu thông tin · từ chối ngoài phạm vi · giữ hội thoại khi đăng nhập giữa chừng · ghi log chi phí từng cuộc"; cách đo "trợ lý không bịa, mọi gợi ý đều có thật · số câu hỏi đã thử và các kiểu câu hỏi"; tuần 1 "Thử nghiệm cách làm và bộ câu hỏi thử" — [PC §4.4](../../team/01-phan-cong-nghien-cuu-module.md).
- Khung báo cáo 8 mục, tối đa 2 trang; mục 2–5 (dữ liệu, API, phụ thuộc, sự kiện) quan trọng nhất cho buổi họp ghép tuần 1 — [PC §6](../../team/01-phan-cong-nghien-cuu-module.md).
- Chỉ số của Bảo: 100% gợi ý có ID hợp lệ trên ≥ 200 câu; từ chối đúng ≥ 95%; báo cáo VNĐ/cuộc — [TK §12](../../docs/03-y-tuong-trien-khai.md).
- Thanh tạo bộ bài đăng mô phỏng tuần 1; Duy làm tìm bằng lời và gợi ý trên web tuần 1; Thắng làm trang chi tiết với giá thật tuần 1; Dung làm backend chạy được cho cả nhóm tuần 1 — [PC §4.1, §4.3, §4.6, §4.7](../../team/01-phan-cong-nghien-cuu-module.md).
- Rủi ro "Trợ lý bịa hoặc tốn chi phí" mức **Cao**; giảm thiểu: "bộ kiểm chứng, bộ câu thử tự động, giới hạn tần suất, đo chi phí từ đầu" — [TK §14](../../docs/03-y-tuong-trien-khai.md).
- Mỗi demo chạy tất cả sản phẩm trên máy thật; người hướng dẫn tự thử; mỗi người phải giải thích được nguyên lý — [ĐB §11, §12](../../docs/02-de-bai-vinpearl-cho-nguoi-tre.md).

### Inferences

| Tuần | Giao (chạy được ở demo thứ Sáu) | Phải trả lời / tài liệu | Phụ thuộc |
|---|---|---|---|
| **T1** | (1) **Bộ câu thử v1 ≥ 200 câu** chia nhóm: có dấu, không dấu, teencode ("3 củ", "k", "đl"), trộn tiếng Anh, emoji, mơ hồ ngân sách (đúng = hỏi lại), không có kết quả (đúng = nói thật), ngoài phạm vi, nguy hiểm, đòi đặt hộ, red-team (lệnh chèn trong bài, "rẻ nhất Việt Nam", hỏi ngày đi của người đăng). Mỗi câu có `expected_behavior`. (2) **Thí nghiệm 3 cách làm** trên cùng bộ câu với công cụ giả lập trên dữ liệu mô phỏng: A RAG nhồi văn bản, B tool calling + kiểm chứng, C chỉ luật (BC §6) → bảng so sánh tỷ lệ ID sai trước/sau kiểm chứng, tỷ lệ hỏi lại đúng, từ chối đúng, VNĐ/câu, độ trễ. (3) **So sánh 2–3 LLM** về tiếng Việt + chi phí trên cùng thí nghiệm (PC §4.4). (4) Khung `llm_calls` + `model_prices` + `eval_runs`. (5) Bản báo cáo module 8 mục (dùng ghi chú này làm mục 2–5) | "Hiểu bài toán": trợ lý là lớp điều phối trên dữ liệu thật; cách đo "không bịa" (đề xuất → Trang gom) | Search: mock `tools/places`, `tools/posts`, `parse`; Social: bài mô phỏng; Booking: sản phẩm + `/internal/prices`; Mentor: khóa API/ngân sách (TK §15 Q7) |
| **T2** | **Gợi ý địa điểm** chạy thật trên web (và app nếu N1 sẵn): `POST /v1/assistant/conversations[/messages]` SSE + JSON; công cụ T1–T3, T5–T7; hỏi lại, từ chối, không có kết quả, đòi đặt hộ; bộ kiểm chứng; khách dùng được + gộp khi đăng nhập (`user.logged_in`); hạn mức 20/50, trần chi phí → `search_only`; `assistant.turn_completed`. Chạy eval v1 trên hệ thống thật | **Chốt cách đo** (ĐB §9): ngưỡng ID hợp lệ, từ chối đúng, VNĐ/cuộc; chốt hạn mức sau khi đo | Search công cụ thật; Access `anon_id` + sự kiện; Booking giá thật; Administration nhận sự kiện |
| **T3** | **Đề xuất bài đăng** (công cụ `search_posts` + trích dẫn `span_id`), chạy trên **cả app và web**; dùng gu/lượt lưu từ app (`get_user_context`, công tắc `use_activity_data`); bài ẩn biến khỏi trợ lý (đo cùng Trang); tiếp tục hội thoại app ↔ web; điểm vào từ bài đăng và từ trang tìm rỗng. **B7** (dữ liệu + endpoint nội bộ cho màn của Trang) và **B1** nếu nhóm chọn. Eval v2 + chấm tay 30–50 hội thoại (faithfulness). **Thứ Năm ngừng thêm tính năng** | 4 câu hỏi mở về trợ lý (mục 4), thể hiện trong sản phẩm; kết quả đo §9; vì sao chọn B1/B7 | Social `snippets`; Search taste; Notification (tùy chọn); Administration màn B7 |
| **T4** | Báo cáo: số câu đã thử theo nhóm, tỷ lệ ID sai trước/sau kiểm chứng, từ chối đúng, hỏi lại đúng, VNĐ/cuộc p50/p95, tỷ lệ token đọc từ cache, các ca thất bại tiêu biểu | "Nếu có thêm thời gian": "Đi ngay", "Có X triệu đi đâu", "Trợ lý canh giá" (TK §16) | — |

Kịch bản demo tối thiểu mentor sẽ tự thử (lấy từ tiêu chí A12): câu teencode → gợi ý có thẻ bấm được; "4 đứa dưới 2 củ" → hỏi lại cơ sở ngân sách đúng 1 lần; câu không có kết quả → nói thật + nới điều kiện; câu ngoài phạm vi → từ chối thân thiện; "đặt giúp mình" → không đặt, đưa nút; đăng nhập giữa chừng → không mất hội thoại; ẩn một bài trên trang quản trị → bài biến khỏi trợ lý; mở web cùng tài khoản → thấy hội thoại.

### Gaps
- Nếu ban tổ chức **không cấp khóa API/ngân sách** (TK §15 Q7), kế hoạch T1–T2 phải chuyển sang mô hình mở chạy cục bộ hoặc phương án C; chưa có câu trả lời.
- App Android (N1) chưa chốt công nghệ (PC §8 #6), nên chưa chắc trợ lý trên app sẵn sàng ở T2; ĐB chỉ bắt buộc "cả app và web" ở T3.
- Không có lịch cụ thể nào trong tài liệu nhóm cho việc Search giao công cụ thật (ngoài "tìm bằng lời và gợi ý trên web" tuần 1 của Duy).

---

## 8. Xung đột và lỗ hổng trong tài liệu nhóm liên quan tới module AI

### Takeaway
Có **6 xung đột cần chốt ở buổi họp ghép** (nơi giữ sở thích; hội thoại khách trên thiết bị hay server; tên sự kiện/đường dẫn; stream chữ hay không; hạn mức; đơn vị chi phí) và **7 lỗ hổng** (vị trí trợ lý trên UI, endpoint công cụ còn thiếu, sự kiện xóa tài khoản, đồng bộ hội thoại realtime, attribution đặt chỗ, quyền xem hội thoại ở B7, khóa API). Không xung đột nào chạm tới tiêu chí A12; tất cả là chuyện hợp đồng giữa module.

### Cited Findings
- Sở thích: TK §8.4 đặt "sở thích (ngân sách, gu) người dùng xem/sửa/xóa được" trong phần trợ lý; TK §10.2 đặt "ngân sách, gu" ở khối Tài khoản (M9) và "bộ nhớ sở thích" ở khối Hội thoại trợ lý (M5) — [TK §8.4, §10.2](../../docs/03-y-tuong-trien-khai.md); BC và GC-MM ghi nhận và đề xuất Access là chủ — [BC Phần I](../../reports/Kiến%20trúc%20module%20Tripflix.md); [GC-MM §2 Gaps](../Kiến%20trúc%20module%20Tripflix/module_mapping.md).
- Khách: TK §8.4 "lưu trên thiết bị 7 ngày"; BC §7 đề xuất server theo `anon_id` — [TK §8.4](../../docs/03-y-tuong-trien-khai.md); [BC Phần II AI §7](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Tên: GC-SA dùng `/api/v1/ai/...`, `ai.message.completed`, `identity.user.logged_in`, chi phí `cost_micro_usd`; BC dùng `/v1/assistant/...`, `assistant.turn_completed`, `user.logged_in`, `cost_vnd`; TK §8.4 "quy ra VNĐ" — [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md); [BC Phần II AI](../../reports/Kiến%20trúc%20module%20Tripflix.md); [TK §8.4](../../docs/03-y-tuong-trien-khai.md).
- Hạn mức: TK 20/ngày khách, 50/ngày thành viên; GC-SA ví dụ "ẩn danh 10 tin/giờ" — [TK §8.4](../../docs/03-y-tuong-trien-khai.md); [GC-SA §5](../Kiến%20trúc%20module%20Tripflix/search_ai.md).
- Stream: BC mô tả endpoint "luồng SSE các sự kiện status, cards, clarification, done" và đồng thời khuyên "sinh xong, kiểm, rồi mới trả" — [BC Phần II AI §3, §6](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Sơ đồ phân công chỉ vẽ M5 → M1 "gọi công cụ tìm", trong khi BC cho AI phụ thuộc cả Booking, Social, Access — [PC §2.1](../../team/01-phan-cong-nghien-cuu-module.md); [BC Phần II AI §4](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- B7 được ghi vừa ở M5 (PC §2.2 "M5: A12, B1, B7") vừa ở M7 ("Màn hình A15, A16, B7"); BC tách "AI (dữ liệu) + Administration (màn hình)" — [PC §2.2](../../team/01-phan-cong-nghien-cuu-module.md); [BC Phần I](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Phần "AI Agent tham gia kiểm duyệt" là câu hỏi mở giao cho Trang, nhưng cổng LLM (nếu dùng) ở AI — [PC §4.5](../../team/01-phan-cong-nghien-cuu-module.md); [BC Phần II AI §1](../../reports/Kiến%20trúc%20module%20Tripflix.md).
- Giao diện app/web dự kiến không nói vị trí trợ lý — [TK §4](../../docs/03-y-tuong-trien-khai.md).
- Câu hỏi mentor về khóa API LLM còn mở — [TK §15 Q7](../../docs/03-y-tuong-trien-khai.md).

### Inferences

**Xung đột cần chốt (kèm đề xuất)**
| # | Xung đột | Đề xuất chốt | Người cần đồng ý |
|---|---|---|---|
| X1 | Sở thích ngân sách/gu ở M5 hay M9 | **Access (M9)** giữ sở thích khai báo; AI chỉ giữ slot hội thoại; trợ lý đề nghị lưu qua nút xác nhận → `PUT /v1/me/preferences`. Sửa câu trong TK §8.4 thành "sở thích lưu ở tài khoản, trợ lý đọc và đề nghị cập nhật" | Dung, Duy |
| X2 | Hội thoại khách trên thiết bị (TK) hay server (BC) | **Server theo `anon_id`, 7 ngày**; thiết bị giữ `conversation_id` + bản cache hiển thị. Lý do: gộp khi đăng nhập trên thiết bị khác, đo B7/chi phí cho cả khách | Dung, cả nhóm |
| X3 | Tên đường dẫn/sự kiện/đơn vị (`/api/v1/ai` vs `/v1/assistant`; `ai.*` vs `assistant.*`; micro-USD vs VNĐ) | Dùng **BC**: `/v1/assistant/...`, `assistant.turn_completed`, `assistant.budget_exceeded`, `user.logged_in`; lưu cả tiền tệ nhà cung cấp và `cost_vnd` (VNĐ là đơn vị báo cáo, TK §8.4) | Dung (N3) |
| X4 | SSE stream chữ hay "sinh xong rồi trả" | SSE chỉ stream `status`; `text`/`cards` gửi sau kiểm chứng; có JSON một lần cho mạng yếu. Hỏi mentor có chấp nhận 3–5 giây không | Mentor, Thanh/Phong (client) |
| X5 | Hạn mức 20/50 tin/ngày (TK) vs 10 tin/giờ (GC-SA) | Giữ **TK**; thêm nhịp ≤ 5 tin/phút chống spam; chốt T2 sau đo | Cả nhóm |
| X6 | B7 thuộc M5 hay M7 | **Dữ liệu + endpoint nội bộ ở AI, màn hình ở Administration** (theo BC); Bảo giao `/internal/assistant/*`, Trang vẽ màn | Trang |

**Lỗ hổng cần bổ sung**
| # | Lỗ hổng | Đề xuất |
|---|---|---|
| G1 | Vị trí trợ lý trên app video dọc và trên web chưa có trong TK §4 | Tab "Hỏi Tripflix" ở thanh dưới app; widget + trang riêng trên web; nút từ bài đăng, từ trang tìm rỗng (mục 2) |
| G2 | Thiếu endpoint nội bộ cho công cụ: parse câu, gu theo actor, đã lưu, điểm đến đã đặt, phần còn thiếu, xu hướng cho người dùng | 5–6 endpoint ở mục 3, chốt với Duy, Phong, Thanh, Thắng, Dung |
| G3 | Không có `user.deleted` trong danh mục sự kiện | Access phát; AI, Search, Social xóa dữ liệu cá nhân |
| G4 | Notification không nhận sự kiện của AI → hội thoại không cập nhật "ngay" ở thiết bị thứ hai | Notification nhận `assistant.turn_completed`, đẩy `user:{id}` type `assistant_message` |
| G5 | Không đo được "từ trợ lý tới đặt chỗ" | `booking_drafts.attribution {surface, message_id}` ở Booking |
| G6 | Chưa có quy định ai được đọc nội dung hội thoại ở B7 | Chỉ vai trò quản trị; che SĐT/email; ghi audit khi mở hội thoại |
| G7 | Sơ đồ PC §2.1 chỉ có M5 → M1 | Cập nhật sơ đồ: AI → Search (công cụ), Booking (giá), Social (bài), Access (sở thích, authz); Event bus → AI (`user.logged_in`, `post.hidden`) |
| G8 | Thiếu định nghĩa "đoạn trích" của bài để kiểm câu trích | Social trả `snippets[{span_id, text}]` trong `/internal/posts` |
| G9 | Chưa có câu trả lời về khóa API/ngân sách LLM | Giữ câu hỏi mentor TK §15 Q7 ở đầu danh sách; chuẩn bị phương án C + mô hình mở |

### Gaps
- Ghi chú này không có quyền chốt; mọi đề xuất X1–X6, G1–G9 cần buổi họp ghép tuần 1 (PC §7.2) và mentor.
- Chưa kiểm tra artifact "Bản đồ tính năng Tripflix" (TK dẫn tới nhưng không có trong các tài liệu được giao đọc), nên có thể còn xung đột khác với artifact đó.
