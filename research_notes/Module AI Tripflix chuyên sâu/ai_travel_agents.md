# AI assistant / AI agent trong sản phẩm du lịch & OTA — tham chiếu cho module AI của Tripflix

> Phạm vi: 2023–11/2026. Ngày nghiên cứu: 02/11/2026.
> **Lưu ý phương pháp (quan trọng cho người tổng hợp):** Trong phiên này WebFetch bị chặn ở mức DNS với các host agoda.com, openai.com, phocuswire.com (lỗi `ENOTFOUND`, cần được cho phép trong network settings của session). Vì vậy **toàn bộ dữ kiện dưới đây lấy từ snippet/tóm tắt của công cụ WebSearch**, chưa đọc toàn văn bài gốc. Mỗi dòng có gắn URL nguồn mà snippet dẫn tới; các con số nên được kiểm tra lại trên trang gốc trước khi đưa vào báo cáo chính thức. Hầu hết số liệu hiệu quả đều do chính công ty công bố (earnings call, press release), không có kiểm chứng độc lập.

---

## 1. Phạm vi sản phẩm: assistant làm được gì / không làm gì, entry point, UX, liên kết tới inventory bookable

### Takeaway
Thị trường đi theo phổ: (a) **query understanding / smart filter** gắn vào search sẵn có (Booking Smart Filter, Kayak AI Mode) → (b) **chat lên kế hoạch trả về thẻ (card) bookable** nhưng người dùng tự bấm đặt (Booking AI Trip Planner 2023, Expedia trong ChatGPT, TripGenie, Agoda AI Assistant) → (c) **agent hành động thay người dùng** (Airbnb CS agent tự hủy booking; Lola của Booking Holdings và Google AI Mode hướng tới đặt chỗ trong chat). Điểm chung của mọi sản phẩm “an toàn”: câu trả lời luôn neo về inventory thật của nền tảng và nút đặt nằm trong luồng booking chuẩn.

### Cited Findings

**Booking.com**
- AI Trip Planner ra mắt beta ngày 28/06/2023 cho một nhóm Genius member tại Mỹ; kết hợp các model ML recommendation có sẵn của Booking (gợi ý điểm đến & chỗ ở) với LLM qua ChatGPT API của OpenAI — [Business Today](https://www.businesstoday.in/amp/technology/news/story/ai-travel-planner-bookingcom-to-use-chatgpt-to-help-plan-itineraries-book-tickets-387352-2023-06-28); [Travolution](https://travolution.com/news/booking.com-unveil-ai-trip-planner-is-in-beta-in-the-us)
- Trip Planner nằm trong app, người dùng chạm vào bất kỳ chỗ ở nào được gợi ý để hoàn tất đặt phòng (handoff về luồng booking chuẩn); yêu cầu đăng nhập và đặt ngôn ngữ tiếng Anh khi early access — [Mobile Marketing Magazine](https://mobilemarketingmagazine.com/booking-com-launches-ai-trip-planner-in-beta/); [Business Traveller](https://www.businesstraveller.com/news/bookingcom-launches-chatgpt-trip-planner/)
- Skift: Booking chọn OpenAI (không phải Google Bard) làm nền, kết hợp công cụ AI nội bộ — [Skift](https://skift.com/?p=600993)
- 30/10/2024: ra Smart Filter + Property Q&A + Review Summaries; Smart Filter chạy trên app mobile tại Mỹ, Anh, Úc, New Zealand, Singapore — [PYMNTS](https://www.pymnts.com/artificial-intelligence-2/2024/booking-com-expands-ai-powered-travel-features/); [Hospitality Net](https://www.hospitalitynet.org/news/4124415/bookingcom-enhances-travel-planning-with-new-ai-powered-features-for-easier-smarter-decisions)

**Expedia**
- Tháng 4/2023 Expedia đã có beta trip planning dựa trên ChatGPT trong app (được nhắc như bối cảnh cạnh tranh khi Booking ra Trip Planner) — [Business Traveller](https://www.businesstraveller.com/news/bookingcom-launches-chatgpt-trip-planner/) (chỉ thấy qua snippet)
- Romie công bố 05/2024 tại EXPLORE (Las Vegas) trong gói spring release >40 tính năng; định vị là “travel agent + concierge + personal assistant”: hỗ trợ plan, shop, book và xử lý khi chuyến đi thay đổi (thời tiết, sự cố phút chót, đề xuất phương án thay thế) — [Hotel Dive](https://www.hoteldive.com/news/expedia-ai-assistant-romie/716315/); [Expedia newsroom](https://www.expedia.com/newsroom/spring-product-release-2024); [TechCrunch](https://techcrunch.com/2024/05/14/expedia-starts-testing-ai-powered-features-for-search-and-travel-planning)
- Romie có thể được mời vào SMS group chat để “nghe” cuộc thảo luận chuyến đi, gọi bằng @Romie; học sở thích người dùng theo thời gian — [BGR](https://bgr.com/tech/expedias-romie-wants-to-be-your-ai-travel-agent/); [Travel Market Report](https://travelmarketreport.com/RetailStrategies/articles/Expedia-Unveils-Romie-an-AI-Powered-Travel-Planner)
- Cùng đợt: công cụ so sánh giá theo ngày (cross-date), trang help có AI, tóm tắt review khách — [Loyalty360](https://loyalty360.org/Content-Gallery/Daily-News/Loyalty360-Reads-Expedia-Announces-AI-Powered-Inno)
- Không tìm thấy thông tin về trạng thái Romie sau 2024 (xem Gaps).

**ChatGPT apps (Apps SDK, 06/10/2025)**
- Booking.com và Expedia là đối tác đầu tiên của Apps SDK cùng Canva, Coursera, Figma, Spotify, Zillow; ra mắt 06/10/2025 — [Malay Mail](https://www.malaymail.com/amp/news/tech-gadgets/2025/10/07/chatgpt-levels-up-now-it-can-book-hotels-curate-playlists-and-more/193719); [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor)
- Expedia app trong ChatGPT: tìm hiểu điểm đến, so sánh khách sạn & chuyến bay với giá real-time, xem bản đồ tương tác; người dùng gọi app bằng tên hoặc ChatGPT tự gợi ý; app dùng Model Context Protocol (MCP) — [ShortTermRentalz](https://shorttermrentalz.com/news/chatgpt-launches-travel-apps-with-expedia-and-booking-com/); [eMarketer](https://www.emarketer.com/content/expedia-booking-com-integrations-push-chatgpt-deeper-commerce)
- Việc đặt Booking.com vẫn **handoff ra ngoài ChatGPT** (sang site/app Booking) — [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor) (qua snippet)

**Kayak**
- Kayak.ai (site thử nghiệm, 04/2025, công nghệ OpenAI) → AI Mode chính thức 15/10/2025 trên desktop & mobile web: bấm icon “AI Mode”, hỏi bằng ngôn ngữ tự nhiên, Kayak so sánh kết quả từ nhiều nhà cung cấp; ra mắt tiếng Anh tại Mỹ — [Skift](https://skift.com/2025/10/15/kayak-ai-mode-natural-language-search/); [TechCrunch](https://techcrunch.com/2025/10/16/kayak-launches-an-ai-mode-for-travel-questions-search-and-bookings/); [Travel Daily News](https://www.traveldailynews.com/technology/kayak-launches-ai-mode-to-enable-conversational-travel-search-with-chatgpt-integration/)

**Trip.com — TripGenie**
- TripGenie là trợ lý trong app; ~60% tương tác hiện liên quan tới đặt chỗ; có công cụ so sánh khách sạn và cho phép hỏi bằng ảnh (image input) — [Travolution](https://www.travolution.com/news/trip.com-sets-out-three-years-of-contrasting-ai-use-in-travel/); [Bolsamania (press release)](https://www.bolsamania.com/nota-de-prensa/mercados/three-years-of-tripgenie-how-travellers-around-the-world-are-using-ai-differently--22048166.html)

**Agoda**
- Limited public beta AI Assistant từ giữa 09/2026 trong app mobile; “trải nghiệm booking hoàn toàn mới đầu tiên sau hơn 20 năm”; nối trip planning hội thoại với inventory >6 triệu chỗ ở + vé máy bay, tour, điểm tham quan; đến 30/10/2026 mở rộng ra 21 thị trường (có Malaysia, Hàn Quốc; **không xác nhận được Việt Nam**) — [Agoda press](https://www.agoda.com/press/agoda-begins-limited-public-beta-of-new-ai-assistant-that-turns-travel-ideas-into-bookable-trips/); [IT Brief Australia](https://itbrief.com.au/story/agoda-rolls-out-ai-assistant-across-21-travel-markets); [Pokde](https://pokde.net/?p=265027)
- Nguồn mâu thuẫn về mức độ mở: một bài 09/2026 nói sẽ dần mở cho mọi user app; một bài 10/2026 mô tả là beta theo lời mời — [Kyodo PR Wire](https://kyodonewsprwire.jp/release/202609155918); [IT Brief Australia](https://itbrief.com.au/story/agoda-rolls-out-ai-assistant-across-21-travel-markets)

**Airbnb**
- Chọn làm **AI customer service trước** trip planning vì Chesky cho rằng rủi ro hallucination trong CS “rất, rất cao” nên khó hơn; agent xử lý được cả việc hủy (thực hiện hủy cho khách, không chỉ hướng dẫn); bản đầu chỉ tiếng Anh tại Mỹ — [TechCrunch](https://techcrunch.com/2025/08/07/ai-agents-arent-the-new-google-says-airbnb-ceo); [Customer Experience Dive](https://customerexperiencedive.com/news/airbnb-brings-ai-customer-service-agent-to-half-of-us-customers/747163)
- Lộ trình: agent sẽ cá nhân hóa hơn, thực hiện thêm hành động thay user, thêm ngôn ngữ, thêm voice; định hướng hợp nhất AI search + messaging thành một “concierge” — [Customer Experience Dive](https://www.customerexperiencedive.com/news/airbnb-ai-first-expands-customer-service-agent-us/757237/); [Constellation Research](https://www.constellationr.com/insights/news/airbnb-look-its-ai-strategy)

**Google (Gemini / AI Mode)**
- 17/11/2025: Canvas trong AI Mode dựng lịch trình ở side panel từ mô tả chuyến đi, kết hợp dữ liệu chuyến bay/khách sạn, Google Maps và nội dung web; so sánh khách sạn có giá và tiện ích; chỉ desktop Mỹ, user opt-in Labs. Flight Deals mở toàn cầu (>200 quốc gia, >60 ngôn ngữ). Đặt nhà hàng agentic đã có ở Mỹ; **đặt khách sạn/vé máy bay trực tiếp trong AI Mode vẫn đang phát triển** với đối tác (Booking.com, Marriott, Expedia…), chưa có timeline — [Search Engine Journal](https://www.searchenginejournal.com/google-extends-ai-travel-planning-and-agentic-booking-in-search/); [9to5Google](https://9to5google.com/2025/11/17/google-ai-mode-travel/); [eMarketer](https://www.emarketer.com/content/google-expands-ai-travel-planning-and-booking-tools)

**Startup & sản phẩm mới**
- Mindtrip: chat planner trên cơ sở dữ liệu >11 triệu POI; chat + bản đồ + ảnh + giá/availability; miễn phí cho người dùng, kiếm tiền bằng hoa hồng booking; ra Mindtrip for Hotels (B2B) 2025 — [Datastudios](https://www.datastudios.org/post/mindtrip-ai-travel-planning-in-chat-booking-and-pricing); [VentureBeat](https://venturebeat.com/ai/mindtrips-ai-travel-assistant-aims-to-be-your-one-stop-shop-for-trip-planning) (nguồn chủ yếu là review/aggregator)
- Layla (Berlin): itinerary theo ngày gồm vé máy bay, chỗ ở, xe, hoạt động; đặt qua đối tác (Booking.com, Skyscanner); gói Layla Prime ~49 USD/năm; tự công bố “>1 tỷ USD trips planned”; kết hợp chuyên gia người thật cho chuyến phức tạp — [PhocusWire](https://www.phocuswire.com/layla-launch-funding-ai-travel-planner); [IPS News (press release)](https://ipsnews.net/business/?p=248171)
- Klook: công bố K.AI (chatbot + trip planner) tại sự kiện sinh nhật 9 tuổi ở Bangkok; dùng GenAI của Google Cloud cho nội dung hoạt động và chatbot CS — [Google Cloud Press Corner, 24/10/2024](https://www.googlecloudpresscorner.com/2024-10-24-Klook-Deepens-Partnership-with-Google-Cloud-to-Enhance-Travel-Experiences-and-Supercharge-Developer-Productivity-with-Generative-AI); [PhocusWire](https://www.phocuswire.com/klook-k-ai-trip-planner-kreators-affiliate-program)
- Traveloka: chỉ thấy Traveloka 5.0 (07/2024) thêm **tìm kiếm cảm hứng dựa trên AI** và gợi ý điểm đến bằng AI; không thấy chatbot GenAI riêng — [Selular.ID](https://selular.id/2024/07/traveloka-5-0-hadirkan-interface-baru-dan-pencarian-berbasis-ai/); [SWA](https://swa.co.id/read/474460/traveloka-memanfaatkan-akal-imitasi-untuk-menangkap-perilaku-wisatawan-ke-destinasi-wisata-asia)
- Booking Holdings ra **Lola** (29/09/2026), trợ lý AI độc lập do hai đồng sáng lập Kayak (Steve Hafner, Paul English) dẫn dắt: đi từ ý tưởng mơ hồ tới chuyến đi đã đặt trong một cuộc chat (khách sạn, nhà hàng, vé sự kiện, đưa đón); không quảng cáo/paid placement; thành viên miễn phí + 2 gói trả phí; chỉ ở Mỹ; đối tác gồm Booking.com, Kayak, Priceline, Agoda, OpenTable, GetYourGuide… — [Skift](https://skift.com/2026/09/29/kayak-co-founders-launch-lola-booking-holdings-new-ai-assistant-exclusive/); [AltexSoft](https://www.altexsoft.com/travel-industry-news/booking-backs-lola-as-ai-rewrites-travel-search/)

### Inferences
- Pattern UX phổ biến nhất ở OTA lớn là **chat + rich card**: LLM viết lời dẫn ngắn, còn nội dung thật (khách sạn, giá, map) là component lấy từ inventory, nút “Đặt” dẫn về checkout chuẩn. Tripflix có thể áp dụng y hệt: card = post/video TikTok-style hoặc sản phẩm Vinpearl/VinWonders/combo có `id` thật.
- Việc Booking giữ checkout ngoài ChatGPT và Booking AI Trip Planner chỉ “tap để đặt” là tiền lệ trực tiếp cho yêu cầu “không đặt thay người dùng” của spec Tripflix.
- Sản phẩm của ông lớn đều bắt đầu ở **thị trường/ngôn ngữ hẹp** (EN, Mỹ) và beta có giới hạn → với demo sinh viên, hẹp hóa scope (chỉ tiếng Việt, chỉ inventory Vin, chỉ vài điểm đến) là hợp lý, không phải thiếu sót.

### Gaps
- Không đọc được toàn văn trang Agoda press, OpenAI case study, PhocusWire (host bị chặn) → chi tiết UX (card, filter chips) của Agoda và Expedia-in-ChatGPT chưa xác minh.
- Không tìm thấy thông tin Romie sau 2024 (còn hoạt động, bị gộp hay đóng?).
- Expedia ChatGPT plugin (2023) chỉ biết qua kiến thức nền, không có nguồn trong phiên này.
- Không xác nhận được Agoda AI Assistant có hỗ trợ Việt Nam/tiếng Việt; không tìm thấy chatbot GenAI của Traveloka.

---

## 2. Dữ liệu & kiến trúc: dữ liệu người dùng/nền tảng, grounding/retrieval, tool calling, kiểm tra giá/availability real-time

### Takeaway
Các sản phẩm thành công đều **tách LLM (hiểu ngôn ngữ, hội thoại) khỏi nguồn sự thật (inventory, giá, review, POI DB)**: LLM gọi tool/API nội bộ hoặc qua MCP, và dùng model rẻ cho tác vụ hẹp. Multi-model routing (Airbnb 13 model; Agoda GPT + Gemini Maps grounding; Booking GPT‑4o mini cho filter) là mẫu chung.

### Cited Findings
- Booking AI Trip Planner = model ML recommendation có sẵn của Booking (điểm đến, chỗ ở) + LLM ChatGPT API cho lớp hội thoại — [Business Today](https://www.businesstoday.in/amp/technology/news/story/ai-travel-planner-bookingcom-to-use-chatgpt-to-help-plan-itineraries-book-tickets-387352-2023-06-28)
- Booking Smart Filter dùng **GPT‑4o mini** để hiểu prompt như “sunset views”, “great gym”, rồi quét inventory và áp filter tự động — [OpenAI case study: Booking.com](https://openai.com/index/booking-com/) (qua snippet; trang gốc bị chặn)
- Airbnb CS agent xây trên **13 model khác nhau**, huấn luyện/tinh chỉnh trên hàng chục nghìn cuộc hội thoại CS — [Customer Experience Dive](https://customerexperiencedive.com/news/airbnb-brings-ai-customer-service-agent-to-half-of-us-customers/747163) (qua snippet); Airbnb dự định dùng kho dữ liệu danh tính & review để cá nhân hóa — [Constellation Research](https://www.constellationr.com/insights/news/airbnb-look-its-ai-strategy)
- Agoda AI Assistant: lõi hội thoại chạy **OpenAI GPT‑5.6**; với kế hoạch điểm đến phức tạp dùng thêm **Gemini “Grounding with Google Maps”**; nối tới inventory bookable của Agoda — [IT Brief Australia](https://itbrief.com.au/story/agoda-rolls-out-ai-assistant-across-21-travel-markets); [Nomad Lawyer](https://www.nomadlawyer.org/agoda-ai-assistant-beta-travel-planning-gpt-5-6-google-maps-2026)
- Expedia/Booking trong ChatGPT dùng **MCP** (Apps SDK) để ChatGPT gọi tool/data của OTA; Expedia trả **giá real-time** — [eMarketer](https://www.emarketer.com/content/expedia-booking-com-integrations-push-chatgpt-deeper-commerce); [ShortTermRentalz](https://shorttermrentalz.com/news/chatgpt-launches-travel-apps-with-expedia-and-booking-com/)
- Thử nghiệm thực tế tuần ra mắt: app Expedia không kích hoạt được; app Booking chạy một phần nhưng advanced search lỗi, có thể vì Booking chưa mở dữ liệu filter cho ChatGPT — [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor) (qua snippet)
- Kayak AI Mode kết hợp dữ liệu du lịch của Kayak (so sánh nhiều nhà cung cấp) với ChatGPT — [Travelling for Business](https://travellingforbusiness.co.uk/?p=22720); [TechCrunch](https://techcrunch.com/2025/10/16/kayak-launches-an-ai-mode-for-travel-questions-search-and-bookings/)
- Google Canvas grounding vào dữ liệu flight/hotel của Google, Maps và web — [Search Engine Journal](https://www.searchenginejournal.com/google-extends-ai-travel-planning-and-agentic-booking-in-search/)
- Mindtrip grounding trên DB >11 triệu POI + dữ liệu giá/availability; so sánh 2026 (blog đối thủ MonkeyTravel, độ tin thấp) cho rằng Layla mạnh hơn về độ chính xác giá live — [Datastudios](https://www.datastudios.org/post/mindtrip-ai-travel-planning-in-chat-booking-and-pricing); [MonkeyTravel blog](https://monkeytravel.app/blog/layla-vs-mindtrip-2026)
- Romie dùng ngữ cảnh group chat + học sở thích; BGR lưu ý Expedia không nói rõ dữ liệu người dùng mà Romie truy cập được xử lý ra sao — [BGR](https://bgr.com/tech/expedias-romie-wants-to-be-your-ai-travel-agent/)

### Inferences
- Kiến trúc tối thiểu hợp lý cho Tripflix: LLM + **function calling** với 3–5 tool hẹp (`search_inventory(filters)`, `search_posts(query, location)`, `get_price_availability(id, dates)`, `get_place(id)`), trả về `id` thật; frontend render card từ `id` chứ không từ text LLM viết → loại bỏ gần hết hallucination về địa điểm/giá.
- Giá/availability nên **không bao giờ để LLM sinh ra**; luôn lấy từ tool tại thời điểm render (theo pattern Expedia “real-time pricing”).
- Bài học từ thất bại ChatGPT app tuần đầu: nếu tool không expose đủ filter, trải nghiệm hỏng dù LLM tốt → schema filter của inventory là phần cần làm kỹ trước.
- Dùng model nhỏ/rẻ cho bước parse ý định (như Booking dùng GPT‑4o mini), chỉ dùng model lớn khi cần lập lịch trình nhiều bước → trực tiếp phục vụ yêu cầu đo & báo cáo chi phí.

### Gaps
- Không có tài liệu engineering công khai (trong phiên này) mô tả chi tiết RAG pipeline, prompt hay schema tool của Booking/Expedia/Trip.com.
- Không rõ sản phẩm nào dùng lịch sử tìm kiếm/booking cá nhân trong prompt và mức độ ra sao (ngoài tuyên bố chung của Airbnb, Romie).

---

## 3. Kết quả đo được (adoption, conversion, CS deflection, CSAT) và thất bại (hallucination, sai giá/availability)

### Takeaway
Con số mạnh và có ý nghĩa nhất nằm ở **customer service** (Airbnb −15% khách cần người thật, ~1/3 vấn đề tự giải quyết; Expedia >50% inquiry do virtual agent giải quyết), không phải ở trip planning. Số liệu conversion của planner hầu như chỉ có Trip.com tự công bố, không có baseline. Thất bại phổ biến là bịa địa điểm/giờ/giá khi LLM không được grounding.

### Cited Findings
**Customer service**
- Airbnb Q1/2025 (05/2025): AI CS agent được 50% user Mỹ dùng, kế hoạch phủ 100% Mỹ trong tháng; giảm **15%** số người cần liên hệ agent người thật — [PYMNTS](https://www.pymnts.com/artificial-intelligence-2/2025/airbnb-50-of-users-turn-to-ai-bot-for-customer-service/); [YourStory](https://yourstory.com/ai-story/airbnb-launches-ai-customer-support-bot-usa)
- Airbnb Q2/2025 (08/2025) nhắc lại −15% sau khi phủ toàn Mỹ — [MarketBeat transcript Q2 2025](https://www.marketbeat.com/earnings/reports/2025-8-6-airbnb-inc-stock); [TechCrunch](https://techcrunch.com/2025/08/07/ai-agents-arent-the-new-google-says-airbnb-ceo)
- Báo cáo sau đó: agent hoạt động toàn Bắc Mỹ, giải quyết khoảng **1/3** vấn đề support không cần người — [Constellation Research](https://www.constellationr.com/insights/news/airbnb-look-its-ai-strategy) (qua snippet; chưa rõ thời điểm chính xác)
- Lưu ý: 15% là giảm tương đối tỷ lệ khách cần người, không phải giảm tổng số contact — (phân tích của snippet tổng hợp, dựa trên các nguồn trên)
- Expedia Q3/2025 (11/2025): CFO Scott Schenkel nói virtual agent giải quyết **hơn một nửa** inquiry; AI tóm tắt hỗ trợ agent người, giảm chi phí/giao dịch — [PYMNTS](https://www.pymnts.com/earnings/2025/expedia-sees-fast-growing-traffic-from-chatbots/); [Hotel Dive](https://www.hoteldive.com/news/expedia-ai-enhance-customer-support-acquire-new-customers/819968/)
- Expedia Q2/2025: CEO Ariane Gorin nói AI CS góp phần đạt **NPS cao kỷ lục** đồng thời giảm chi phí — [PYMNTS](https://www.pymnts.com/earnings/2025/expedia-sees-fast-growing-traffic-from-chatbots/)
- Expedia Q1/2026: “>250 triệu tương tác dịch vụ/năm, hơn một nửa tự phục vụ… hơn 30% do AI và đang tăng”; mẫu số (toàn bộ hay chỉ self-service) không nhất quán giữa các bản tóm tắt — [Customer Experience Dive](https://www.customerexperiencedive.com/news/expedia-queries-ai-agents/805023/); [GeekWire](https://www.geekwire.com/2026/expedia-group-sees-reward-and-risk-in-the-rise-of-ai-powered-travel/)

**Planner / conversion**
- Trip.com (ITB 03/2024): ngày dùng TripGenie, user ở lại app thêm trung bình ~20 phút; gần 1 triệu inquiry; công ty nói **tỷ lệ chuyển đổi đơn tăng gấp đôi** và retention cải thiện (không công bố baseline) — [Travolution](https://www.travolution.com/news/travel-sectors/travel-agents/itb-2024-trip.com-group-reveals-results-from-personalisation-work/)
- Trip.com Q3/2025: user TripGenie tăng >200% YoY trong H1/2025 — [MarketBeat Q3 2025 transcript](https://www.marketbeat.com/earnings/reports/2025-11-18-tripcom-group-limited-stock/)
- Kỷ niệm 3 năm TripGenie: đơn hàng có AI hỗ trợ tăng ~**400% YoY**; ~60% tương tác liên quan booking; công cụ so sánh khách sạn giảm **80%** số click, gắn với +45% revisit 7 ngày; >50% user dùng công cụ so sánh chọn đúng khách sạn AI gợi ý; user dùng ảnh có revisit 7 ngày gấp 2 trung bình — [Travolution](https://www.travolution.com/news/trip.com-sets-out-three-years-of-contrasting-ai-use-in-travel/); [Bolsamania press release](https://www.bolsamania.com/nota-de-prensa/mercados/three-years-of-tripgenie-how-travellers-around-the-world-are-using-ai-differently--22048166.html)
- Agoda: beta cho thấy user có xu hướng đặt chuyến “liên kết” nhiều nhóm sản phẩm; **không công bố** số user, conversion hay doanh thu — [IT Brief Australia](https://itbrief.com.au/story/agoda-rolls-out-ai-assistant-across-21-travel-markets)
- Expedia ghi nhận traffic từ chatbot (ChatGPT…) tăng nhanh — [PYMNTS](https://www.pymnts.com/earnings/2025/expedia-sees-fast-growing-traffic-from-chatbots/)

**Thất bại / rủi ro**
- Peru (2025): du khách được AI chỉ tới “Sacred Canyon of Humantay” không tồn tại ở vùng Andes; ghi nhận trong OECD AI Incidents (an toàn & thiệt hại tài chính) — [OECD.AI incident](https://oecd.ai/en/incidents/2025-09-29-8b4f); [TechRadar](https://www.techradar.com/computing/artificial-intelligence/ai-is-tricking-people-into-traveling-to-places-that-dont-exist-and-we-all-need-to-learn-to-avoid-these-scams)
- CNN (được cơ quan di trú Đài Loan đăng lại) đưa tin về suối nước nóng không tồn tại do AI gợi ý — [Taiwan NIA](https://news.immigration.gov.tw/NewsSection/Detail/ff01a48b-cd65-4d81-9e89-8e2a76b77ff3?lang=EN)
- Skift (02/01/2026) gọi hiện tượng nội dung du lịch AI kém chất lượng là “travel slop” — [Skift](https://skift.com/2026/01/02/welcome-travel-slop-era/)
- Khảo sát (Greetwell, 08/2026, qua blog TripProf): 1/6 người dùng AI trip planner bị gửi tới nơi không tồn tại; 55% gặp gợi ý sai; sai giờ/giá phổ biến hơn bịa địa điểm — [TripProf](https://tripprof.com/en/blog/ai-travel-hallucination-survey/) (**chưa xác minh**, không truy cập được khảo sát gốc)
- Paper Zenodo (05/2026) phân loại hallucination du lịch của ChatGPT (sai địa điểm, sai địa lý…) — [Zenodo](https://zenodo.org/records/20125892)
- ChatGPT apps tuần đầu: Expedia app không trigger, Booking advanced search lỗi — [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor) (qua snippet)

### Inferences
- Với Tripflix, KPI demo nên học từ nhóm số liệu “có thể đo trong 4 tuần”: tỷ lệ phiên chat kết thúc bằng click vào card thật (click-through tới post/sản phẩm), số click tiết kiệm so với filter thủ công (như Trip.com −80% click), tỷ lệ câu trả lời chỉ chứa `id` có thật (grounding rate = 100% là mục tiêu cứng), tỷ lệ refusal đúng, chi phí/phiên.
- Các vụ hallucination ngoài đời đều đến từ LLM “trần” (ChatGPT chung, planner không grounding). Spec Tripflix “chỉ gợi ý địa điểm/post có thật trong hệ thống” chính là biện pháp đối phó trực tiếp; nên có **validator hậu kỳ** loại bỏ mọi entity không map được về DB.

### Gaps
- Không có số conversion độc lập/A-B test công khai cho bất kỳ AI trip planner nào; mọi số là tự công bố.
- Không tìm thấy bài kiểm thử báo chí có phương pháp rõ ràng (USA Today, The Verge…) so sánh các OTA assistant trong phiên này.
- Không có số liệu chi phí vận hành (cost/conversation) công khai của bất kỳ OTA nào.

---

## 4. Guardrails: refusal, người dùng vị thành niên, không tự động đặt chỗ, disclosure

### Takeaway
Bằng chứng công khai về guardrail rất mỏng. Những gì thấy được là guardrail “bằng thiết kế sản phẩm”: yêu cầu đăng nhập, giới hạn thị trường/ngôn ngữ, giữ bước thanh toán ngoài chat, bắt đầu ở domain hẹp. Xu hướng 2026 lại đi về phía agent **hành động thay** người dùng (Airbnb hủy booking, Lola, Google agentic booking), ngược với spec Tripflix.

### Cited Findings
- Chesky giải thích chọn CS trước vì rủi ro hallucination “very, very high” — phải chính xác hơn trip planning — [TechCrunch](https://techcrunch.com/2025/08/07/ai-agents-arent-the-new-google-says-airbnb-ceo)
- Airbnb agent **thực hiện** hủy chuyến cho khách và dự định “take more actions on behalf of the user” — [Customer Experience Dive](https://www.customerexperiencedive.com/news/airbnb-ai-first-expands-customer-service-agent-us/757237/)
- Booking Trip Planner early access: bắt buộc đăng nhập + tiếng Anh; đặt bằng cách user tự bấm vào chỗ ở — [Business Traveller](https://www.businesstraveller.com/news/bookingcom-launches-chatgpt-trip-planner/); [Mobile Marketing Magazine](https://mobilemarketingmagazine.com/booking-com-launches-ai-trip-planner-in-beta/)
- ChatGPT apps của Expedia chỉ cho user đăng nhập, ngoài EU, gói Free/Go/Plus/Pro; booking Booking.com handoff ra ngoài ChatGPT — [PhocusWire](https://www.phocuswire.com/openai-chatgpt-apps-expedia-booking-tripadvisor) (qua snippet)
- Google: đặt nhà hàng agentic đã live ở Mỹ, đặt vé sự kiện/lịch hẹn cho Labs users; đặt khách sạn/vé bay vẫn chưa live — [9to5Google](https://9to5google.com/2025/11/17/google-ai-mode-travel/)
- Lola: định vị “từ ý tưởng đến chuyến đã đặt trong một cuộc chat”; cam kết không quảng cáo/paid placement (một dạng minh bạch về xếp hạng) — [Skift](https://skift.com/2026/09/29/kayak-co-founders-launch-lola-booking-holdings-new-ai-assistant-exclusive/)
- Layla dùng chuyên gia người thật làm “backup” cho chuyến phức tạp (human-in-the-loop) — [IPS News](https://ipsnews.net/business/?p=248171)
- BGR chỉ ra thiếu minh bạch về dữ liệu người dùng Romie truy cập — [BGR](https://bgr.com/tech/expedias-romie-wants-to-be-your-ai-travel-agent/)

### Inferences
- Spec Tripflix (“không đặt thay”, “từ chối yêu cầu ngoài phạm vi/nguy hiểm”) là lựa chọn bảo thủ có tiền lệ (Booking Trip Planner 2023, Booking/Expedia trong ChatGPT giữ checkout ngoài chat). Nên trình bày đây là quyết định có chủ đích, không phải giới hạn kỹ thuật.
- Gợi ý guardrail tối thiểu cho demo: (1) system prompt định nghĩa phạm vi (du lịch + inventory Vin); (2) classifier intent rẻ ở đầu vào → nhánh refuse với câu mẫu tiếng Việt; (3) tool “booking” chỉ trả deep link tới trang checkout, không có tool tạo đơn; (4) validator đầu ra loại entity không có `id`; (5) nhãn disclosure “Trợ lý AI – có thể sai, hãy kiểm tra giá trước khi đặt”; (6) với yêu cầu nguy hiểm (vd. leo núi thời tiết xấu, chất cấm) → refuse + gợi ý kênh hỗ trợ.
- Yêu cầu “dùng được khi chưa đăng nhập và giữ hội thoại sau đăng nhập” **khác** với Booking/Expedia (bắt buộc login) — đây là điểm khác biệt sản phẩm; triển khai bằng `anonymous_session_id` lưu hội thoại, khi login thì merge vào `user_id` (pattern guest cart).

### Gaps
- Không tìm thấy chính sách công khai của OTA nào về người dùng vị thành niên trong AI assistant.
- Không tìm thấy mô tả cụ thể cơ chế refusal/content moderation của Booking, Expedia, Trip.com, Agoda.
- Chưa xác minh quy định Việt Nam (vd. luật bảo vệ dữ liệu cá nhân, quy định AI) áp dụng cho chatbot — ngoài phạm vi nghiên cứu này.

---

## 5. “Smart filter” / query understanding (ngôn ngữ tự nhiên → structured filters) như giải pháp rẻ hơn chatbot đầy đủ

### Takeaway
Booking Smart Filter (10/2024, GPT‑4o mini) và Kayak AI Mode (10/2025) cho thấy OTA lớn coi **NL → filter trên search sẵn có** là một sản phẩm AI đáng ship độc lập: rẻ (model nhỏ, một lượt gọi), khó hallucinate (kết quả vẫn do search engine trả về), dễ đo.

### Cited Findings
- Smart Filter: người dùng gõ mong muốn bằng ngôn ngữ thường (vd. “Hotels in Amsterdam with a great gym, rooftop bar, and canal views from the room”), hệ thống quét inventory và **tự áp filter**, người dùng gần như không cần lọc thủ công — [PYMNTS](https://www.pymnts.com/artificial-intelligence-2/2024/booking-com-expands-ai-powered-travel-features/); [Travel Tomorrow](https://traveltomorrow.com/booking-expands-ai-features-for-speedier-more-intuitive-travel-searches/)
- Model: GPT‑4o mini — [OpenAI case study](https://openai.com/index/booking-com/) (qua snippet)
- Smart Filter tiếp tục là sản phẩm 2025, có case study trình bày tại hội nghị Product-Led Alliance Amsterdam 13/05/2025 — [Product-Led Alliance](https://world.productledalliance.com/location/popsamsterdam/speaker/paolagambuzza)
- Kayak AI Mode: icon AI Mode trong trang search, câu hỏi tự nhiên → so sánh kết quả từ nhiều nhà cung cấp — [Travel Daily News](https://www.traveldailynews.com/technology/kayak-launches-ai-mode-to-enable-conversational-travel-search-with-chatgpt-integration/)
- Traveloka 5.0 (07/2024): tìm kiếm cảm hứng du lịch dựa trên AI trong giao diện mới — [Selular.ID](https://selular.id/2024/07/traveloka-5-0-hadirkan-interface-baru-dan-pencarian-berbasis-ai/)
- Trip.com: công cụ so sánh khách sạn giảm 80% click — [Travolution](https://www.travolution.com/news/trip.com-sets-out-three-years-of-contrasting-ai-use-in-travel/)

### Inferences
- Với câu ví dụ “cuối tuần đi đâu chill dưới 3 củ”, bước cốt lõi chính là smart filter: LLM (hoặc model nhỏ) trả JSON theo schema, ví dụ `{date_range: "next_weekend", budget_max_vnd: 3000000, vibe: ["relax"], origin: <hỏi lại hoặc lấy từ profile>, party_size: null}`; sau đó query DB. Phần “hiểu slang” (“3 củ” = 3 triệu, “chill” = thư giãn, “xõa”, “sống ảo”) xử lý bằng few-shot + bảng từ điển slang trong prompt, kiểm thử bằng bộ test câu hỏi tiếng Việt.
- “Hỏi lại để làm rõ” (clarifying question) có thể cài đặt đơn giản: nếu JSON thiếu trường bắt buộc (điểm xuất phát, số người, ngân sách) → trả 1 câu hỏi + quick-reply chips thay vì gọi search. Đây là ranh giới giữa smart filter và chatbot nhiều lượt.
- Chi phí: một lượt parse bằng model nhỏ có token in/out ngắn → chi phí mỗi query rất thấp, dễ log `prompt_tokens/completion_tokens` để báo cáo theo yêu cầu spec. (Số tiền cụ thể cần tra bảng giá model tại thời điểm làm — không có trong nguồn đã thu thập.)

### Gaps
- Không có số liệu công khai về tác động của Smart Filter (adoption, conversion, độ chính xác parse).
- Không có tài liệu về cách Booking xử lý filter không map được vào facet có sẵn (vd. “sunset views”) — có thể dùng review/text search, nhưng chưa xác minh.

---

## 6. Phạm vi AI tối thiểu có giá trị cho demo sinh viên 4 tuần vs. quá đà

### Takeaway
Các ông lớn mất nhiều năm, nhiều model, và vẫn bắt đầu bằng beta hẹp. Với 4 tuần, “minimal valuable AI” cho Tripflix là: **chat một luồng = smart filter + clarifying question + tool calling vào DB thật + card kết quả + refusal + log chi phí**. Agent đặt chỗ, itinerary nhiều ngày, voice, group chat, cá nhân hóa từ lịch sử là overkill.

### Cited Findings
(Các bằng chứng dùng làm căn cứ, đã dẫn ở trên)
- Booking ship Smart Filter như một tính năng độc lập bằng model nhỏ (GPT‑4o mini) — [OpenAI case study](https://openai.com/index/booking-com/) (qua snippet)
- Booking Trip Planner 2023 = ML có sẵn + LLM, user tự bấm đặt; beta hẹp (Mỹ, EN, đăng nhập) — [Business Today](https://www.businesstoday.in/amp/technology/news/story/ai-travel-planner-bookingcom-to-use-chatgpt-to-help-plan-itineraries-book-tickets-387352-2023-06-28); [Business Traveller](https://www.businesstraveller.com/news/bookingcom-launches-chatgpt-trip-planner/)
- Airbnb cần 13 model + hàng chục nghìn hội thoại huấn luyện cho CS agent — [Customer Experience Dive](https://customerexperiencedive.com/news/airbnb-brings-ai-customer-service-agent-to-half-of-us-customers/747163)
- Agoda mất tới 2026 mới ra AI Assistant beta, dùng 2 nhà cung cấp model — [IT Brief Australia](https://itbrief.com.au/story/agoda-rolls-out-ai-assistant-across-21-travel-markets)
- Giá trị đo được rõ nhất của Trip.com đến từ tính năng hẹp (so sánh khách sạn, −80% click) — [Travolution](https://www.travolution.com/news/trip.com-sets-out-three-years-of-contrasting-ai-use-in-travel/)
- Rủi ro hallucination địa điểm khi không grounding — [OECD.AI](https://oecd.ai/en/incidents/2025-09-29-8b4f)

### Inferences
**Nên làm (MVP 4 tuần, xếp theo ưu tiên):**
1. **NL → structured filter tiếng Việt có slang** (budget “củ/triệu/k”, thời gian “cuối tuần/lễ 30/4”, vibe “chill/xõa/sống ảo”, nhóm “đi cặp/đi nhóm”) → JSON schema cố định; bộ test ~50–100 câu tiếng Việt để đo accuracy.
2. **Clarifying question** khi thiếu trường bắt buộc, kèm quick-reply chips (tối đa 1–2 câu hỏi trước khi trả kết quả).
3. **Grounded retrieval**: tool `search_inventory` + `search_posts` trên DB Tripflix; LLM chỉ được trả `id`; frontend render card (video/post + hotel/combo + giá lấy từ DB); validator loại entity lạ.
4. **Không có tool tạo đơn**: card có nút “Xem & đặt” dẫn tới trang chi tiết/checkout (giống Booking Trip Planner / ChatGPT apps handoff).
5. **Refusal & disclosure**: classifier phạm vi đầu vào + câu từ chối mẫu; nhãn “Trợ lý AI”.
6. **Phiên ẩn danh → merge khi login** (anonymous session id).
7. **Đo chi phí**: log token in/out, model, latency, số tool call mỗi lượt; dashboard đơn giản (cost/phiên, cost/1.000 phiên, p50/p95 latency); dùng model nhỏ cho parse/refusal, chỉ gọi model lớn khi cần viết lời dẫn.

**Overkill cho 4 tuần:** agent tự đặt/hủy (Airbnb, Lola), itinerary nhiều ngày kiểu Canvas/Mindtrip, đa ngôn ngữ, voice, group chat (Romie), cá nhân hóa từ lịch sử booking, tích hợp ChatGPT Apps/MCP ra bên ngoài, input bằng ảnh (TripGenie), fine-tune model riêng, giá/availability real-time từ hệ thống bên thứ ba (dùng dữ liệu mock trong DB là đủ cho demo).

**Câu chuyện demo nên kể:** “Chúng tôi làm đúng phần mà Booking ship đầu tiên (smart filter + trip planner có handoff), cộng thêm điểm khác biệt Tripflix: kết quả là video/post thật của cộng đồng, hiểu slang Gen Z Việt, dùng được khi chưa login, và có số liệu chi phí.”

### Gaps
- Không có nguồn công khai về chi phí xây dựng/vận hành của các sản phẩm trên để so sánh trực tiếp với ước tính của demo.
- Không có benchmark công khai về hiểu tiếng lóng tiếng Việt của các LLM thương mại — cần tự xây bộ test.
