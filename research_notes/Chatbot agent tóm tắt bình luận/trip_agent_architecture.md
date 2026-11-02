# Kiến trúc agent lập kế hoạch chuyến đi dạng hội thoại (production) — tham chiếu cho chatbot Tripflix

> Ghi chú phương pháp (2026-11-02): Phiên làm việc này **chặn nhiều host** (blog.booking.com, medium.com/airbnb-engineering, openai.com, developers.openai.com, skift.com, cfp.pydata.org đều lỗi ENOTFOUND). Chỉ fetch đầy đủ được **anthropic.com** và **github.com**. Mọi phát hiện khác đến từ **snippet/tóm tắt của công cụ search** — được đánh dấu **[snippet]**. Không bịa số liệu; số nào chỉ thấy ở nguồn thứ cấp thì ghi rõ.

## 1. Các nền tảng lớn (Expedia, Booking.com, Trip.com, Airbnb, Google, Mindtrip, Layla, Kayak, Agoda) cấu trúc agent thế nào?

### Takeaway
Pattern chung 2023–2026: **LLM chỉ làm lớp hiểu ngôn ngữ + điều phối tool, còn dữ liệu (giá, tình trạng phòng, danh sách sản phẩm) luôn lấy từ API/kho dữ liệu nội bộ**; kết quả hiển thị thành **card có thể đặt**, và **checkout luôn chuyển sang luồng đặt chỗ truyền thống** (trang/app của OTA), agent không tự thanh toán. Xu hướng 2025–2026 là bỏ "một chatbot toàn năng" sang **agent nhỏ chuyên biệt theo từng bước** + lớp orchestration riêng.

### Cited Findings
**Booking.com**
- AI Trip Planner (2023) ghép các model ML recommendation có sẵn với LLM; một phần chạy trên ChatGPT API của OpenAI; tích hợp GPT với dữ liệu độc quyền về property, giá, availability, và kéo **giá + availability real-time** từ database của Booking.com. [snippet] — [Booking.com newsroom](https://news.booking.com/bookingcom-launches-new-ai-trip-planner-to-enhance-travel-planning-experience/); [Booking.com Tech Blog (bài "8 Weeks to Innovation: Lessons from Building Booking.com's AI Trip Planner", chỉ thấy tiêu đề)](https://blog.booking.com/)
- Booking.com tự xây **orchestration layer** cho phép team chuyển đổi giữa OpenAI, Anthropic, Google và model open source (theo Fortune, qua snippet). [snippet] — [Yahoo Tech / Fortune](https://tech.yahoo.com/articles/booking-com-cto-prioritized-travelers-150646425.html)
- Talk PyData Tel Aviv 2025: chuyển sang **các component module điều phối LLM, retrieval service và Booking.com API** để giải truy vấn du lịch phức tạp; đánh giá bằng **LLM-as-Judge**; có nội dung về inference optimization. [snippet] — [PyData Tel Aviv 2025](https://cfp.pydata.org/pydata-tel-aviv-2025/talk/FZEQUJ/)
- **Smart Filter** (công bố 30/10/2024): người dùng gõ ngôn ngữ tự nhiên (vd. "Hotels in Amsterdam with a great gym, rooftop bar, and canal views"), GenAI **chuyển thành các filter có sẵn** trên inventory thay vì người dùng tự chọn filter; ra mắt ở US/UK/AU/NZ/SG trên app, cùng Property Q&A; Review Summaries rollout sau. Theo case study của OpenAI, dùng **GPT‑4o mini** để hiểu prompt như "sunset views", "great gym". [snippet] — [PYMNTS](https://www.pymnts.com/artificial-intelligence-2/2024/booking-com-expands-ai-powered-travel-features/); [Hospitality Net](https://www.hospitalitynet.org/news/4124415/bookingcom-enhances-travel-planning-with-new-ai-powered-features-for-easier-smarter-decisions); [OpenAI – Booking.com](https://openai.com/index/booking-com/)

**Expedia**
- Trip planning trong app (2023, beta iOS, tiếng Anh) dùng ChatGPT; **khách sạn và hoạt động được nhắc đến trong hội thoại tự động lưu vào "Trip"/Trip Planner** trong app → hội thoại sinh ra state có cấu trúc, không chỉ text. [snippet] — [Expedia newsroom](https://www.expedia.com/newsroom/expedia-launched-chatgpt); [Travolution](https://www.travolution.com/news/technology/expedia-group-rolls-out-chatgpt-across-travel-planning-and-shopping-features/)
- **Romie** (công bố 05/2024, alpha trên EG Labs): được mời vào group chat SMS, gắn tag để gợi ý, tóm tắt chat nhóm, **biến sở thích trong chat thành tham số shopping**; chạy trên mix model nội bộ + OpenAI; chưa có ngày launch rộng. [snippet] — [Hotel Dive](https://www.hoteldive.com/news/expedia-ai-assistant-romie/716315/); [Travel Weekly](https://www.travelweekly.com/Travel-News/Travel-Technology/Expedia-Group-unveils-Romie-AI-assistant); [ZenML LLMOps DB](https://www.zenml.io/llmops-database/ai-powered-travel-assistant-for-trip-planning-and-personalization)
- Skift: Expedia **chuyển từ một chatbot đa năng sang các "point agents" chuyên biệt cho từng giai đoạn chuyến đi**; virtual agent chạy từ 2020, >30 triệu cuộc hội thoại (số do Expedia công bố, qua snippet). [snippet] — [Skift](https://skift.com/?p=818836); [Expedia partner blog](https://partner.expediagroup.com/en-us/resources/blog/ai-advantage-in-travel)

**ChatGPT Apps (Expedia, Booking.com) – 06/10/2025, Apps SDK (dựa trên MCP)**
- Booking.com, Expedia là pilot partner của Apps SDK; user so sánh khách sạn/chuyến bay với **giá real-time và bản đồ tương tác ngay trong chat**; **khi đặt phải chuyển sang website partner, không chốt trong ChatGPT** ở thời điểm launch. [snippet] — [OpenAI: Introducing apps in ChatGPT](https://openai.com/index/introducing-apps-in-chatgpt/); [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor); [Skift](https://skift.com/2025/10/06/expedia-booking-chatgpt-apps-openai/); [The Paypers](https://thepaypers.com/payments/expert-views/travel-bookings-in-the-age-of-chatgpt-apps-the-good-the-bad-and-the-test-of-the-future)
- Một nguồn blog duy nhất nói OpenAI thử booking trực tiếp trong ChatGPT rồi rút lại (03/2026) — **chưa kiểm chứng**, mâu thuẫn/không xác nhận được từ OpenAI/partner. [snippet] — [Tourism Tribe](https://www.tourismtribe.com/chatgpt-instant-checkout-travel-operators/)

**Trip.com TripGenie**
- TripGenie gợi ý điểm tham quan, khu mua sắm, bản đồ, **kèm link đặt chỗ và hình ảnh** ngay trong câu trả lời; plan được lưu vào **"My Itinerary"** để quay lại sau. Trip.Planner (mới hơn) được mô tả là hub lịch trình "booking-oriented", dùng **giá live và dữ liệu partner đã kiểm tra**, điều chỉnh tuyến đường cho khả thi. Không có tài liệu kỹ thuật về component card/data flow. [snippet] — [Trip.com newsroom](https://www.trip.com/newsroom/introducing-tripgenie-groundbreaking-ai-travel-assistant); [WiT](https://www.webintravel.com/trip-com-unveils-tripgenie-its-next-gen-ai-travel-assistant-focusing-on-personalisation/); [PR Newswire ITB Berlin](https://www.prnewswire.com/news-releases/tripcom-group-reinforces-ai-innovation-and-european-collaboration-at-itb-berlin-302704121.html)

**Airbnb (agent CSKH, không phải trip planning)**
- Automation Platform v2: **lai LLM + workflow truyền thống**; LLM là "reasoning engine" quyết định gọi tool nào, thứ tự nào; response đầu tiên của LLM có thể yêu cầu thực thi tool (vd. lấy reservation mới nhất), **platform thực thi call đó và lưu kết quả vào context**; có Chain-of-Thought, context management (lịch sử, intent, thông tin chuyến đi hiện tại), **guardrails framework**, LLM observability. [snippet] — [Airbnb Engineering (Medium)](https://medium.com/airbnb-engineering/automation-platform-v2-improving-conversational-ai-at-airbnb-d86c9386e0cb); [ZenML](https://www.zenml.io/llmops-database/evolving-a-conversational-ai-platform-for-production-llm-applications)
- CEO Airbnb: agent CSKH xây trên **13 model khác nhau**, tune trên hàng chục nghìn hội thoại, giảm **15%** số người cần liên hệ nhân viên. [snippet, nguồn báo] — [CX Dive](https://www.customerexperiencedive.com/news/airbnb-ai-first-expands-customer-service-agent-us/757237/)

**Google (AI Mode / Canvas)**
- Canvas trong AI Mode (11/2025, US): xây itinerary từ **dữ liệu chuyến bay + khách sạn, Google Maps, nội dung web**; refine bằng câu hỏi tiếp theo; **lưu Canvas để quay lại không phải làm lại**, chia sẻ được. Agentic booking: đặt nhà hàng/vé sự kiện qua partner (OpenTable, Resy, Ticketmaster...), flight/hotel "trong tương lai" (tuyên bố 11/2025); nguồn sau nói hotel booking ra 08/2026 — **chưa xác nhận từ Google**. [snippet] — [9to5Google](https://9to5google.com/2025/11/17/google-ai-mode-travel/); [Google blog – Canvas travel](https://blog.google/products-and-platforms/products/search/tips-prompts-ai-mode-canvas-travel-planning/); [Search Engine Journal](https://www.searchenginejournal.com/google-extends-ai-travel-planning-and-agentic-booking-in-search/561251/)

**Kayak**
- Kayak.ai (04/2025, lab): chủ yếu là **chatbot search trên inventory flight/hotel của Kayak; click vào option thì mở link sang Kayak.com**. AI Mode (10/2025) là feature đầu tiên "tốt nghiệp" lên site chính, xây trên ChatGPT, trả kết quả real-time sau khi so giá qua nhiều provider; prompt mẫu "NYC hotels within a half mile of Rockefeller Center for one night, Dec. 23rd". [snippet] — [Skift 05/2025](https://skift.com/2025/05/28/can-kayak-ai-solve-travels-complex-problems-heres-how-it-works/); [TechCrunch 16/10/2025](https://techcrunch.com/2025/10/16/kayak-launches-an-ai-mode-for-travel-questions-search-and-bookings/); [PhocusWire](https://www.phocuswire.com/kayak-rolls-out-ai-mode-travel-search)

**Mindtrip, Layla**
- Layla: tạo itinerary theo ngày, sửa bằng chat follow-up ("swap an activity, reduce walking, remove a destination"); sửa transport/hotel/activity trực tiếp trong trip; mô hình **AI lập kế hoạch + chuyên viên người đóng gói và đặt** (handoff sang human). Mindtrip: sửa "block by block", tích hợp flight (Sabre, PayPal), hotel, activity để đặt trong chat. Nguồn đều là review bên thứ ba/marketing. [snippet] — [Layla App Store](https://apps.apple.com/us/app/layla-ai-trip-planner/id6758730467); [Layla blog](https://layla.ai/blog/news-and-tips/best-free-ai-trip-planner); [Stardrift](https://stardrift.ai/resources/best-ai-travel-planners)

### Inferences
- Pattern "LLM → filter có cấu trúc" của Booking Smart Filter khớp chính xác với nhu cầu Tripflix: câu slang "đi Đà Lạt 4 đứa 5 củ" chỉ cần LLM nhỏ **trích xuất slot (destination, pax, budget, days, exclusions)** rồi chạy query SQL/search thông thường trên bảng itinerary — không cần agent tự do.
- Pattern Expedia "nhắc trong chat → tự lưu vào Trip" = Tripflix nên giữ **plan state có cấu trúc (JSON) song song với transcript**, UI render từ state, không từ text LLM.
- Mọi OTA đều dừng ở handoff (Kayak mở link, ChatGPT apps chuyển website, Layla chuyển human) → nút "Đi tương tự" mở màn booking package là đúng chuẩn ngành, và đồng thời là cách giải trách nhiệm thanh toán.
- Agent Tripflix có lợi thế: retrieval source là **itinerary do user đăng (có cấu trúc)** + caption/hashtag — giống Trip.com/Google dùng "dữ liệu thật đã kiểm" làm grounding, nên giảm hallucination nếu bắt buộc mọi item phải trỏ về ID có thật.

### Gaps
- Không đọc được bài engineering Booking.com "8 Weeks to Innovation", bài Airbnb Automation Platform v2 gốc, và Apps SDK docs (host bị chặn) → thiếu chi tiết về schema tool, intent taxonomy, cách render card.
- Không tìm thấy thông tin nào về Agoda AI assistant trong lượt search này.
- Không có tài liệu kỹ thuật công khai về intent list hoặc tool list cụ thể của TripGenie, Romie, Mindtrip, Layla.
- Không có số liệu latency công khai của bất kỳ trip agent nào.

## 2. Hướng dẫn thiết kế agent (Anthropic, OpenAI, 12-factor agents): pattern nào hợp với chatbot "nhỏ nhưng polish"?

### Takeaway
Cả Anthropic, OpenAI và 12-factor agents đều khuyên: **bắt đầu đơn giản nhất — workflow định sẵn bằng code với LLM ở vài bước then chốt**, một agent duy nhất với ít tool rõ ràng, schema chặt, control flow do code nắm, state do ứng dụng sở hữu. Đây đúng là hướng thiết kế cũ của Tripflix (small LLM + 4 tool + state JSON + UI button bypass LLM).

### Cited Findings
- Anthropic (19/12/2024): "Workflows are systems where LLMs and tools are orchestrated through predefined code paths"; agent là khi LLM "dynamically direct their own processes and tool usage". Khuyên tìm giải pháp đơn giản nhất; với nhiều ứng dụng, **một LLM call tối ưu + retrieval + in-context examples là đủ**; workflow cho task xác định rõ, agent cho task mở. — [Anthropic – Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
- Anthropic – các pattern: prompt chaining (có programmatic check giữa các bước), **routing** (phân loại input rồi chuyển sang prompt chuyên biệt), parallelization, orchestrator-workers, **evaluator-optimizer**. — [Anthropic](https://www.anthropic.com/engineering/building-effective-agents)
- Anthropic – tool design (ACI): tài liệu tool phải có ví dụ, edge case, ranh giới rõ giữa các tool; **"Poka-yoke your tools"** — đổi tham số để khó dùng sai; test tool với nhiều input; agent cần "ground truth from the environment" mỗi bước, có thể **dừng chờ human feedback ở checkpoint**, có stopping condition (max iterations). Khuyên gọi LLM API trực tiếp, cẩn trọng với framework vì che giấu prompt. — [Anthropic](https://www.anthropic.com/engineering/building-effective-agents)
- 12-factor agents: "mostly deterministic code, with LLM steps sprinkled in at just the right points"; "I don't see a lot of frameworks in production customer-facing agents". Các factor: Own your prompts, **Own your context window**, **Tools are just structured outputs**, **Unify execution state and business state**, Launch/Pause/Resume, **Contact humans with tool calls**, **Own your control flow**, Compact errors into context, **Small focused agents**, **Stateless reducer**. — [12-factor agents (GitHub)](https://github.com/humanlayer/12-factor-agents)
- OpenAI "A practical guide to building agents" (2025, qua nguồn thứ cấp): mặc định **một agent + tools chạy vòng lặp**, chỉ lên multi-agent khi cần; tool chồng chéo gây chọn sai tool (một tóm tắt nói "15+ tool rõ ràng có thể tốt hơn <10 tool chồng chéo" — **chưa đối chiếu được PDF gốc**); manager pattern (agent-as-tool); **guardrails nhiều lớp** (relevance classifier, safety classifier, PII filter, moderation, **mức rủi ro của tool + escalation sang người**), kết hợp LLM classifier với rule-based. [snippet] — [Maginative](https://www.maginative.com/article/how-to-build-ai-agents-a-detailed-practical-guide-from-openai/); [agentic-ai docs](https://agentic-ai.readthedocs.io/en/latest/DesignPatterns/openai-patterns/)
- Airbnb thực hành đúng mô hình "LLM quyết định tool, platform thực thi + lưu kết quả vào context, guardrails framework". [snippet] — [Airbnb Engineering](https://medium.com/airbnb-engineering/automation-platform-v2-improving-conversational-ai-at-airbnb-d86c9386e0cb)

### Inferences
- Pattern phù hợp nhất cho Tripflix: **Routing workflow + một agent nhỏ có tool, state là reducer**:
  1. Router (rule + LLM nhỏ) phân loại turn: `new_trip` / `modify_item` / `ask_info` / `compare` / `handoff` / `out_of_scope`.
  2. `new_trip` = prompt chaining cố định: extract slot (structured output) → code gọi `find_itineraries` (SQL/search, không phải LLM) → code chấm điểm/ghép → LLM chỉ viết lời giới thiệu ngắn.
  3. `modify_item` = `suggest_replacements` (code lọc ứng viên theo vị trí/giá) → `apply_change` (code) → code tính diff/giá → LLM diễn giải.
  4. Nút UI (chọn phương án, hoàn tác, "Đi tương tự") gọi thẳng reducer, **không qua LLM** — đúng factor "Own your control flow" và giảm latency.
- 4 tool là hợp lý (Anthropic/OpenAI đều ủng hộ ít tool, ranh giới rõ). Poka-yoke cụ thể: tool nhận `item_id`/`itinerary_id` dạng enum lấy từ state hiện tại (không cho LLM tự gõ tên khách sạn); giá không bao giờ là tham số do LLM truyền.
- "Contact humans with tool calls" ↔ ở Tripflix là "handoff tool" mở màn "Đi tương tự" với payload đã validate — LLM chỉ đề xuất, user bấm xác nhận.
- Evaluator-optimizer/LLM-as-Judge (Booking dùng) có thể dùng offline để chấm bộ test câu slang, không cần trong runtime.

### Gaps
- Không fetch được PDF gốc của OpenAI guide và Google agent whitepaper → con số về số tool tối ưu chỉ là trích dẫn thứ cấp.
- Không có tài liệu chính thức về "generative UI" của Apps SDK (developers.openai.com bị chặn) — không trích được khuyến nghị cụ thể về inline card/carousel/fullscreen.

## 3. Biểu diễn và sửa lịch trình: day/slot/item, thay một item vs tạo lại, ràng buộc ngân sách/thời gian, diff/undo, giá live

### Takeaway
Nghiên cứu cho thấy LLM thuần **rất kém trong thỏa ràng buộc cứng** (ngân sách, ngày, thời gian), còn pipeline "LLM dịch yêu cầu → solver/code kiểm tra" đạt tỉ lệ cao hơn hẳn → ràng buộc giá/thời gian phải do code xử lý. Sản phẩm thực tế (Layla, Mindtrip, Google Canvas) cho sửa **từng block/item** qua chat, và đây cũng là chỗ dễ lỗi (mất/đổi khách sạn ngoài ý muốn).

### Cited Findings
- TravelPlanner (Xie et al., ICML 2024; 1.225 query): GPT-4 chỉ đạt **0,6% final pass rate** (thỏa hết ràng buộc), thường vi phạm ngân sách/ngày. [snippet] — [arXiv 2404.11891](https://arxiv.org/abs/2404.11891v3)
- Hao et al.: LLM dịch truy vấn thành bài toán **SMT** rồi gọi solver, nếu unsatisfiable thì LLM sửa query → **97%** (bản arXiv) / **93,9%** (bản NAACL 2025); bài cũng nêu o1-preview chỉ đạt ~10% khi có đủ thông tin. [snippet] — [arXiv 2404.11891](https://arxiv.org/abs/2404.11891v3); [project site](https://sites.google.com/view/llm-rwplanning/)
- ChinaTravel (ICLR 2026): TravelPlanner quá đơn giản; trên query người thật, neuro-symbolic agent đạt **37,0%** constraint satisfaction, gấp ~10 lần thuần neural. [snippet] — [ChinaTravel arXiv 2412.13682](https://arxiv.org/html/2412.13682v4)
- Layla: user sửa bằng "swap an activity, reduce walking, remove a destination"; review App Store phản ánh **khách sạn bị đặt sai chỗ, bị thay hoặc bị rớt khỏi itinerary**. [snippet] — [Layla App Store](https://apps.apple.com/us/app/layla-ai-trip-planner/id6758730467); [AnyGen showcase](https://www.anygen.io/showcase/layla-ai/index.html)
- Mindtrip: tạo itinerary thô rồi **chỉnh "block by block"**. [snippet] — [Stardrift](https://stardrift.ai/resources/best-ai-travel-planners)
- Google Canvas: refine bằng follow-up, plan được lưu. [snippet] — [Google blog](https://blog.google/products-and-platforms/products/search/tips-prompts-ai-mode-canvas-travel-planning/)
- Trip.com Trip.Planner nhấn mạnh **giá live + dữ liệu partner đã kiểm**, điều chỉnh tuyến cho khả thi. [snippet] — [PR Newswire](https://www.prnewswire.com/news-releases/tripcom-group-reinforces-ai-innovation-and-european-collaboration-at-itb-berlin-302704121.html)
- Booking.com AI Trip Planner kéo giá/availability real-time từ DB nội bộ. [snippet] — [Booking.com newsroom](https://news.booking.com/bookingcom-launches-new-ai-trip-planner-to-enhance-travel-planning-experience/)
- 12-factor: agent là **stateless reducer** (state + event → state mới) — nền tảng tự nhiên cho undo/diff. — [12-factor agents](https://github.com/humanlayer/12-factor-agents)

### Inferences
- Mô hình dữ liệu đề xuất cho Tripflix (suy ra từ các nguồn trên + thiết kế cũ):
  - `Plan { plan_id, version, source_itinerary_ids[], pax, budget, days[] }`, `Day { index, items[] }`, `Item { item_id, kind: flight|bus|hotel|ticket|food|place, origin: service|manual, ref_id (service product id hoặc null), title, location, time_slot, price_quote{amount, currency, quoted_at, valid_until, source} | null, video_ids[] }`.
  - Mỗi lệnh sửa là một **operation có cấu trúc** (`replace_item`, `remove_item`, `remove_kind` vd. "không bay", `change_pax`) áp vào state qua reducer → sinh `version+1`, lưu stack version để **undo** và để **so sánh (diff) trước/sau** (giá tổng, item đổi, khoảng cách).
- "Thay một item" phải mặc định là **phép sửa cục bộ**, khóa (pin) các item còn lại — tránh lỗi kiểu Layla làm rớt khách sạn. Chỉ "regenerate" khi user đổi điểm đến/số ngày.
- Ràng buộc ngân sách/thời gian: code kiểm tra (tổng giá ≤ budget × pax; không chồng giờ; hotel phủ đủ đêm) **sau mỗi operation**, trả lỗi gọn vào context để LLM giải thích hoặc đề xuất (factor "Compact errors into context"). Không cần SMT solver cho quy mô Tripflix; một hàm validate + chấm điểm là đủ.
- Item "manual" (free text) không có giá live → hiển thị là "giá tham khảo từ người đăng"/không tính vào tổng đặt được; chỉ item "service" có `price_quote` do server tính lại tại thời điểm hiển thị và **tính lại lần nữa khi handoff** sang "Đi tương tự".

### Gaps
- Không tìm thấy bài engineering công khai nào mô tả schema itinerary nội bộ (day/slot) hoặc cơ chế diff/undo của OTA lớn.
- Không tìm thấy nguồn nào về chính sách **quote validity** (thời hạn giá) trong trip agent; gợi ý trên là suy luận.

## 4. Bộ nhớ hội thoại xuyên phiên/thiết bị, ẩn danh → đăng nhập, chính sách hỏi lại

### Takeaway
Các sản phẩm lớn lưu **plan có cấu trúc** (Trip/My Itinerary/Canvas) chứ không chỉ lịch sử chat, để người dùng quay lại sau; ngoài ra hầu như không có tài liệu công khai về merge ẩn danh → đăng nhập hay chính sách hỏi lại.

### Cited Findings
- Expedia: khách sạn/hoạt động trong hội thoại tự lưu vào "Trip" trong app. [snippet] — [Expedia newsroom](https://www.expedia.com/newsroom/expedia-launched-chatgpt); [Travolution](https://www.travolution.com/news/technology/expedia-group-rolls-out-chatgpt-across-travel-planning-and-shopping-features/)
- Trip.com: plan lưu vào "My Itinerary" để tiếp tục khi ngày đi chưa chốt. [snippet] — [Trip.com newsroom](https://www.trip.com/newsroom/introducing-tripgenie-groundbreaking-ai-travel-assistant)
- Google: "AI Mode will save your Canvas itinerary, so you can jump back in without having to start from scratch"; chia sẻ được. [snippet] — [9to5Google](https://9to5google.com/2025/11/17/google-ai-mode-travel/)
- ChatGPT apps chỉ cho user đã đăng nhập (ngoài EEA/CH/UK) lúc launch. [snippet] — [OpenAI](https://openai.com/index/introducing-apps-in-chatgpt/)
- Expedia Romie: tóm tắt group chat thành tham số shopping (memory dạng tóm tắt có cấu trúc). [snippet] — [ZenML](https://www.zenml.io/llmops-database/ai-powered-travel-assistant-for-trip-planning-and-personalization)
- Airbnb: context gồm lịch sử tương tác, intent, thông tin chuyến hiện tại do platform quản lý. [snippet] — [Airbnb Engineering](https://medium.com/airbnb-engineering/automation-platform-v2-improving-conversational-ai-at-airbnb-d86c9386e0cb)
- 12-factor: "Unify execution state and business state", "Launch/Pause/Resume with simple APIs", "Own your context window". — [12-factor agents](https://github.com/humanlayer/12-factor-agents)
- Anthropic: agent dừng xin human feedback ở checkpoint hoặc khi gặp blocker. — [Anthropic](https://www.anthropic.com/engineering/building-effective-agents)

### Inferences
- Tripflix: lưu `conversation { id, owner_user_id | anon_device_id, plan_state(JSON, versioned), summary, last_messages[N] }` ở server; context gửi LLM = plan_state + slot đã biết + vài tin nhắn cuối (không gửi toàn bộ transcript) → rẻ và ổn định với LLM nhỏ.
- Ẩn danh → đăng nhập: gắn `anon_device_id` khi tạo hội thoại, lúc login chuyển owner sang user_id (một UPDATE). Bắt buộc đăng nhập trước khi handoff sang booking.
- Chính sách hỏi lại (đề xuất, không có nguồn trực tiếp): chỉ hỏi khi thiếu **slot bắt buộc** (điểm đến); số người/ngân sách/số ngày thiếu thì dùng mặc định hợp lý, nói rõ giả định và cho chip để sửa → tránh "over-asking". Tối đa 1 câu hỏi/turn, kèm quick-reply chip.

### Gaps
- Không tìm thấy nguồn công khai nào về cách OTA merge hội thoại ẩn danh vào tài khoản, hay về chính sách clarification (số câu hỏi tối đa, slot bắt buộc).
- Không có dữ liệu đo về tác hại của over-asking trong trip agent.

## 5. Thất bại / bị chỉ trích và cách giảm thiểu

### Takeaway
Các lỗi điển hình: **bịa địa điểm/thông tin vận hành**, **làm hỏng plan khi sửa**, **tool không được kích hoạt / tìm kiếm nâng cao lỗi** (ChatGPT apps), và trải nghiệm bị chia cắt khi checkout. Giảm thiểu chung: grounding vào inventory thật, giá từ server, ràng buộc kiểm bằng code, handoff có xác nhận.

### Cited Findings
- Peru: AI ghép tên địa danh thật thành "Sacred Canyon of Humantay" không tồn tại, du khách bị mắc kẹt ở độ cao nguy hiểm (AI Incident DB #1636); Nhật: chatbot đưa sai giờ cáp treo cuối ở núi Misen khiến cặp đôi mắc kẹt; các ca "hot springs" không tồn tại. Phần lớn là tin thứ cấp lặp lại nhau, không có điều tra gốc. [snippet] — [OECD AI incidents](https://oecd.ai/en/incidents/2025-09-29-8b4f); [AI Incident Database](https://incidentdatabase.ai/cite/1636); [CTV News](https://www.ctvnews.ca/lifestyle/article/ai-on-australian-travel-company-website-sends-tourists-to-nonexistent-hot-springs/); [TechRadar](https://www.techradar.com/computing/artificial-intelligence/ai-is-tricking-people-into-traveling-to-places-that-dont-exist-and-we-all-need-to-learn-to-avoid-these-scams)
- Khảo sát Abta (UK): dùng AI lập kế hoạch du lịch tăng từ 4% (2024) lên 8% (2025); khảo sát Global Rescue: 24% du khách dựa vào AI. [snippet] — [Resultsense](https://www.resultsense.com/news/2025-09-30-ai-travel-planning-risks-hallucinations/)
- Skift (02/01/2026) gọi là kỷ nguyên "travel slop" (nội dung du lịch do AI sinh chất lượng thấp). [snippet, chỉ thấy tiêu đề] — [Skift](https://skift.com/2026/01/02/welcome-travel-slop-era/)
- ChatGPT apps lúc đầu: một tester thấy Booking.com chạy khá nhưng **advanced search lỗi**, **Expedia không được kích hoạt**; trải nghiệm bị chia: hội thoại trong ChatGPT, checkout trên website. [snippet] — [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor); [The Paypers](https://thepaypers.com/payments/expert-views/travel-bookings-in-the-age-of-chatgpt-apps-the-good-the-bad-and-the-test-of-the-future); [Vivander Substack](https://vivander.substack.com/p/i-tested-three-hotel-booking-apps)
- Layla: review về khách sạn bị đặt sai/thay/rớt khỏi itinerary khi chỉnh. [snippet] — [Layla App Store](https://apps.apple.com/us/app/layla-ai-trip-planner/id6758730467)
- Phân tích bên thứ ba về TripGenie: itinerary/khách sạn gợi ý **không phải đặt chỗ đã xác nhận**, cần kiểm lại offer và confirmation. [snippet] — [Sequenced](https://sequenced.ai/articles/trip-com-group-blueprint)
- LLM-only thỏa ràng buộc rất kém (0,6% TravelPlanner) → lỗi ngân sách/ngày là lỗi hệ thống, không phải ngẫu nhiên. [snippet] — [arXiv 2404.11891](https://arxiv.org/abs/2404.11891v3)
- Giảm thiểu được nêu: Airbnb dùng guardrails framework + observability [snippet] — [ZenML](https://www.zenml.io/llmops-database/evolving-a-conversational-ai-platform-for-production-llm-applications); Booking dùng LLM-as-Judge để đánh giá [snippet] — [PyData](https://cfp.pydata.org/pydata-tel-aviv-2025/talk/FZEQUJ/); Anthropic: test tool trên nhiều input, sandbox, guardrails, stopping conditions — [Anthropic](https://www.anthropic.com/engineering/building-effective-agents); Expedia: nhúng guardrail pháp lý/đạo đức/kỹ thuật vào workflow AI [snippet] — [Expedia partner blog](https://partner.expediagroup.com/en-us/resources/blog/ai-advantage-in-travel).

### Inferences
- Ánh xạ sang Tripflix (mức độ ưu tiên cao → thấp):
  1. **Hallucinated places**: LLM không được tự tạo item; mọi item phải có `ref_id` hoặc copy nguyên văn từ một itinerary/video có thật (`source_itinerary_id`, `video_id`). Server validate ID trước khi render; item không validate được thì bỏ. Item manual hiển thị nhãn "theo chia sẻ của @user" để người dùng biết nguồn.
  2. **Stale prices**: giá chỉ do server tính; card hiển thị "giá tại thời điểm xx:xx"; re-quote khi bấm "Đi tương tự"; nếu chênh thì báo trước khi vào checkout.
  3. **Sửa làm hỏng plan**: reducer + pin item không đổi + diff hiển thị trước khi áp (nút "Áp dụng" / "Giữ như cũ") + undo.
  4. **Tool không kích hoạt / hiểu sai slang**: router rule-based cho các mẫu phổ biến ("củ" = triệu, "4 đứa" = 4 người), bộ test hội thoại slang chấm bằng LLM-as-Judge offline (theo cách Booking).
  5. **Latency**: LLM nhỏ chỉ chạy 1–2 lần/turn (extract + viết lời), phần tìm/chấm/tính giá là code; nút UI bypass LLM; stream text trong lúc card đang tải.
  6. **Checkout bị chia cắt**: handoff sang màn "Đi tương tự" trong cùng app, prefill đủ item service đã chọn → mượt hơn mô hình ChatGPT→website.

### Gaps
- Không có số liệu công khai về tỉ lệ hallucination, latency p50/p95 hay tỉ lệ chuyển đổi của trip agent nào.
- Không tìm thấy post-mortem kỹ thuật chính thức (của chính công ty) về lỗi trip agent; các ca lỗi chủ yếu từ báo chí/aggregator.
