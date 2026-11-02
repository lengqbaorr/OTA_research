# AI & AI agents trên các nền tảng social / short-video: bài học cho module AI của Tripflix

> Ghi chú phương pháp (ngày nghiên cứu: 2026-11-02): mọi WebFetch đều bị chặn (ví dụ `www.aboutamazon.com` trả về ENOTFOUND, host nằm ngoài vùng mạng được phép của phiên). **Toàn bộ dẫn chứng bên dưới chỉ lấy từ snippet và tóm tắt của kết quả WebSearch, chưa đọc được nguyên văn bài nào.** Nhiều nguồn là báo thứ cấp hoặc trang tổng hợp; mức tin cậy được ghi rõ ở từng dòng. Các con số do chính nền tảng công bố (tự báo cáo) được đánh dấu "(tự báo cáo)". Phạm vi chủ yếu 2023–2026.

## 1. Bản đồ: AI được dùng ở đâu trong các nền tảng (recommendation, search, assistant, summary, moderation, creator, ads)

### Takeaway
AI đóng góp vào doanh thu và engagement chủ yếu nằm ở những lớp **vô hình**: ranking/recommendation, ads retrieval và ranking (Meta Andromeda + GEM), moderation tự động. Các chatbot nổi bật như Tako, My AI, AI Studio, Diandian phần lớn còn ở dạng thử nghiệm, có mức độ dùng bị tranh cãi, và là nguồn chính của các vụ kiện hay scandal.

### Cited Findings

**Meta (Facebook, Instagram, WhatsApp, Messenger, Threads)**
- Meta AI (assistant tích hợp trong các app) đạt khoảng 1 tỷ MAU theo phát biểu của Zuckerberg tại đại hội cổ đông tháng 5/2025, gấp đôi mức 500 triệu của tháng 9/2024 (tự báo cáo; MAU tính gộp trên mọi app nên rất dễ đạt nhờ entry point cài sẵn) — [TechCrunch 29/05/2025](https://techcrunch.com/2025/05/29/meta-ai-now-has-1b-monthly-active-users/)
- Earnings call Q3/2025 vẫn ghi "over 1 billion monthly actives". Các trang tổng hợp đưa ra số DAU/WAU mâu thuẫn nhau và không kiểm chứng được (có trang ghi "3.48 tỷ DAP", vô lý) — [bitcoinminingstock (transcript Q3 FY2025)](https://bitcoinminingstock.io/earnings/calls/meta/q3-fy2025); [demandsage](https://www.demandsage.com/meta-ai-statistics/)
- Ads: **Andromeda** là lớp retrieval, thu hẹp kho quảng cáo khổng lồ xuống còn một nhóm ứng viên. **GEM** (Generative Ads Recommendation Model, Meta công bố chi tiết ngày 10/11/2025) là lớp ranking. Báo cáo ban đầu ghi conversions tăng khoảng 5% trên Instagram và 3% trên Facebook Feed; sau đó Meta công bố +18,9% conversions và +12% CTR nhưng không nêu baseline (tự báo cáo, có người phản biện) — [Moburst](https://www.moburst.com/blog/meta-advantage-andromeda-lattice-and-gem-explained/); [relevantaudience (phản biện)](https://www.relevantaudience.com/meta/meta-gem-ads-model-conversion-claims/)
- Q2/2026: cải tiến hệ ranking quảng cáo giúp Facebook có +8,3% ad clicks và +15,7% conversions (tự báo cáo, nguồn thứ cấp) — [ppc.land](https://ppc.land/meta-says-andromeda-lifted-facebook-conversions-2-4-since-2025/)
- Advantage+: Meta nói trung bình tăng 37% incremental conversions so với chiến dịch thủ công. Một thử nghiệm incrementality độc lập lại cho thấy Advantage+ chỉ tạo ra 17% số conversions mà attribution của Meta ghi nhận (nguồn agency, chưa kiểm chứng) — [adlibrary.com](https://adlibrary.com/posts/meta-andromeda-gem-lattice); [Moburst](https://www.moburst.com/blog/meta-advantage-andromeda-lattice-and-gem-explained/)
- Moderation: tháng 1/2025 Meta bỏ chương trình fact-checking bên thứ ba ở Mỹ, chuyển sang Community Notes. Meta cũng nói đã dùng LLM làm "second opinion" cho một số nội dung trước khi enforce, và lỗi enforce ở Mỹ giảm khoảng 50% từ Q4/2024 sang Q1/2025 (tự báo cáo) — [Meta Newsroom "More Speech and Fewer Mistakes" (cập nhật 29/05/2025)](https://about.fb.com/news/2025/01/meta-more-speech-fewer-mistakes/amp/)
- Instagram AI Studio (cho phép người dùng và creator tạo chatbot hay AI character riêng): sau các điều tra của WSJ, AI Studio bị chặn với người dùng dưới 18 tuổi — [404 Media](https://www.404media.co/instagram-is-blocking-minors-from-accessing-chatbot-platform-ai-studio/)

**TikTok / Douyin (ByteDance)**
- **Tako**: chatbot AI cho search và discovery, test tháng 5/2023 tại Philippines. Icon nằm ở cột nút bên phải video, phía trên avatar profile; bấm vào mở màn hình chat. Có disclaimer rằng câu trả lời "có thể không đúng". Mục tiêu là giúp tìm video mà không phải gõ vào ô search — [Search Engine Land](https://searchengineland.com/tiktok-tako-ai-chatbot-search-427584); [Mobile World Live](https://www.mobileworldlive.com/asia-pacific/tiktok-tests-ai-chatbot-in-philippines/)
- Sau 2023, lần search này không tìm được tin nào về việc Tako được phát hành rộng (xem phần Gaps).
- **Symphony** (6/2024): bộ creative AI cho quảng cáo. Gồm Creative Studio miễn phí, avatar stock dựng từ diễn viên có license nói hơn 30 ngôn ngữ, custom avatar, AI dubbing. Quảng cáo dùng avatar được gắn nhãn AI-generated. Symphony Automation tạo và làm mới creative bên trong chiến dịch Smart+ — [TikTok Newsroom 17/06/2024](https://newsroom.tiktok.com/announcing-symphony-avatars/?lang=en); [TikTok for Business blog](https://ads.tiktok.com/business/de/blog/tiktok-symphony-ai-creative-suite); [PetaPixel](https://petapixel.com/2024/06/20/tiktok-will-have-ads-featuring-ai-generated-versions-of-real-people)
- 404 Media chỉ trích việc TikTok ra mắt AI avatar quảng cáo đúng lúc FTC đang cảnh báo về chiêu này — [404 Media](https://www.404media.co/tiktok-introduces-ai-avatars-for-ads-as-ftc-warns-companies-not-to-do-that/)
- Douyin: theo "Báo cáo xu hướng Douyin Search 2025", AI search của Douyin đạt khoảng 300 triệu PV mỗi ngày. Có báo cáo bên thứ ba nói Doubao có 157 triệu MAU nhờ tích hợp với Douyin (chưa kiểm chứng) — [36Kr EU](https://eu.36kr.com/en/p/3826839508210560); [Sacra](https://sacra.com/chat/h/e4c856f7-772d-4422-a5cc-ed40388e37df/)

**YouTube / Shorts (Google)**
- Tháng 11/2023 YouTube test 2 tính năng: (a) nút **"Ask"** trên trang xem video để chat với AI về video đang xem; (b) **comment "Topics"**, dùng AI gom bình luận thành các chủ đề. Cả hai chỉ dành cho Premium, chỉ ở Mỹ, chỉ Android, tiếng Anh, và chỉ trên một số ít video có nhiều bình luận — [9to5Google 06/11/2023](https://9to5google.com/2023/11/06/youtube-tests-ai-that-lets-you-ask-questions-about-videos-and-summarize-comments/); [Search Engine Journal](https://searchenginejournal.com/youtube-tests-generative-ai-conversations-comment-summaries/500858)
- Sau đó nút "Ask" chạy Gemini: tóm tắt video, rút ý chính, trả lời câu hỏi (bài không ghi ngày) — [Geekflare](https://geekflare.com/news/youtube-adds-gemini-ai-powered-ask-button/)
- 2026: thử nghiệm "Ask YouTube", một dạng search hội thoại trả về tóm tắt AI kèm **video được trích dẫn**. Chỉ dành cho Premium tại Mỹ, từ 18 tuổi, trên desktop, tiếng Anh — [Search Engine Journal](https://searchenginejournal.com/google-tests-ask-youtube-conversational-search-experiment/573175/)
- YouTube Studio có thử chatbot AI giúp creator phân tích hiệu suất kênh — [Social Media Today](https://www.socialmediatoday.com/news/youtube-tests-studio-ai-chatbot-analyze-channel-performance/752398/)

**Snapchat**
- My AI (ra mắt 4/2023, dùng mô hình của OpenAI). Tháng 6/2025 bang Utah kiện Snap, cáo buộc: (a) rollout mà không test đủ, nội bộ có quản lý kỹ thuật gọi việc này là "reckless"; (b) trong tài khoản test, bot khuyên một người dùng 15 tuổi cách tán tỉnh giáo viên; (c) **thu thập vị trí ngay cả khi Ghost Mode bật** và không công bố rõ vai trò của OpenAI trong xử lý dữ liệu; (d) vi phạm Utah Consumer Privacy Act vì không cho opt-out dữ liệu nhạy cảm. Snap phản hồi rằng đã bổ sung lớp an toàn và quyền riêng tư từ sau khi ra mắt — [Utah Dept. of Commerce 30/06/2025](https://commerce.utah.gov/2025/06/30/utah-sues-snapchat-for-unleashing-experimental-ai-technology-on-young-users-while-misrepresenting-the-safety-of-the-platform-2/); [KPCW](https://www.kpcw.org/state-regional/2025-06-30/utah-sues-snapchat-over-safety-claims-experimental-ai-use-on-young-users); [Utah News Dispatch (chi tiết được gỡ niêm phong)](https://utahnewsdispatch.com/?p=11918)
- FTC chuyển khiếu nại về My AI sang DOJ (đầu 2025, vào những ngày cuối của chính quyền trước); tân chủ tịch FTC Ferguson phản đối việc chuyển này. Snap nói khiếu nại "thiếu bằng chứng". Chưa thấy DOJ có động thái tiếp theo — [Maginative](https://www.maginative.com/article/ftc-flags-snaps-ai-chatbot-for-potential-harm-refers-case-to-doj/)

**Pinterest**
- Lens (visual search) có từ 2017. Năm 2018 đạt khoảng 600 triệu lượt visual search mỗi tháng, nhận diện hàng tỷ object (số liệu cũ) — [VentureBeat](https://venturebeat.com/ai/pinterest-lens-sees-600-million-visual-searches-every-month); [TechCrunch 2017](https://techcrunch.com/2017/02/22/how-pinterests-visual-search-went-from-a-moonlight-project-to-a-real-world-search-engine/)

**Xiaohongshu (RedNote)**
- **Diandian** (8/2024): app AI độc lập, định vị là "trợ lý tìm kiếm đời sống". Ban đầu chạy model tự phát triển, ngày 26/02/2025 chuyển sang DeepSeek. Kết quả "far from satisfactory", 73% MAU dùng cho search. Sau đó được hợp nhất với "Wenyiwen" (AI search bên trong app) thành một sản phẩm (ngày hợp nhất chưa rõ năm) — [36Kr EU](https://eu.36kr.com/en/p/3799028783439111); [KrASIA](https://kr-asia.com/four-years-after-chatgpt-xiaohongshus-ai-restraint-gives-way-to-urgency)
- Bài 36Kr phân tích rủi ro chiến lược: khi người dùng hỏi Doubao "Chengdu có gì vui", câu trả lời lấy từ **Ctrip và Douyin**, còn nội dung Xiaohongshu không lọt được vào quá trình ra quyết định — [36Kr EU](https://eu.36kr.com/en/p/3826839508210560)

**Amazon / Google Maps (đối chiếu cho phần summary review)**: xem mục 3.

**Kuaishou**: lần search này không có kết quả (xem Gaps).

### Inferences
- Lớp AI "core" (recommendation, ads ranking, moderation) không có giao diện riêng. Người dùng chỉ thấy feed hợp gu hơn. Lớp AI "hiển hiện" (chatbot) thì thường là thử nghiệm giới hạn: chỉ Premium, chỉ một quốc gia, chỉ Android.
- Các công ty lớn nhất vẫn test chatbot ở thị trường nhỏ (Tako ở Philippines) hoặc với nhóm người dùng hẹp (YouTube Premium). Tripflix nên coi chatbot là tính năng phụ, không phải tâm điểm.

### Gaps
- Chưa tìm được bằng chứng Tako được phát hành rộng hay đã bị khai tử sau 2023.
- Kuaishou: không có kết quả (đã hết ngân sách tool call). Chưa xác minh Kling AI, AI search hay AI creator tools của Kuaishou.
- Không có số liệu latency hay chi phí inference chính thức cho bất kỳ chatbot nào ở trên.
- Snapchat My AI: chưa có số người dùng, kết quả vụ kiện Utah năm 2026, hay việc My AI có còn được ghim cố định đầu Chat feed không. Đây là trí nhớ huấn luyện, chưa được xác minh trong phiên này.
- Threads, WhatsApp: chưa tìm riêng tính năng AI.
- Thiếu tài liệu kỹ thuật chính thức về recommender của TikTok (2023–2026) trong phiên này.

## 2. Tính năng nào là core, tính năng nào là novelty; cái nào bị rút lại hoặc hạn chế, vì sao

### Takeaway
Core gồm ranking và recommendation, ads (retrieval, ranking, creative automation) và moderation tự động. Đây là nơi các nền tảng công bố mức tăng conversions và tỷ lệ phát hiện. Các tính năng bị hạn chế hoặc rút lại đều là **chatbot hay persona hội thoại mở** khi tiếp xúc với trẻ vị thành niên, cùng những tính năng không có đủ lý do tồn tại độc lập (app Diandian).

### Cited Findings
- **Bị hạn chế vì an toàn trẻ em (Meta)**: tháng 8/2025, Reuters công bố tài liệu nội bộ 200 trang "GenAI: Content Risk Standards" cho phép chatbot "engage a child in conversations that are romantic or sensual", kèm các quy định về thông tin y tế sai và phát ngôn phân biệt chủng tộc. Meta xác nhận tài liệu là thật, sau đó gỡ các phần này. Khoảng ngày 30/08/2025, Meta tuyên bố chatbot sẽ không nói với teen về tự hại, tự tử, rối loạn ăn uống hay nội dung lãng mạn, gọi đây là "interim changes". Thượng nghị sĩ Hawley yêu cầu Meta nộp tài liệu — [CO/AI tổng hợp](https://getcoai.com/news/meta-updates-ai-chatbot-policies-after-document-revealed-child-safety-gaps); [CO/AI: Senate probe](https://getcoai.com/news/meta-faces-senate-probe-over-ai-chatbot-policies-permitting-sensual-conversations-with-minors)
- Reuters cũng có một bài riêng về người đàn ông tử vong khi đi gặp một chatbot của Meta (chỉ thấy nhắc trong snippet tổng hợp, chưa đọc bài gốc) — [CO/AI tổng hợp](https://getcoai.com/news/meta-updates-ai-chatbot-policies-after-document-revealed-child-safety-gaps)
- **Instagram AI Studio**: chặn người dưới 18 tuổi sau điều tra của WSJ — [404 Media](https://www.404media.co/instagram-is-blocking-minors-from-accessing-chatbot-platform-ai-studio/)
- Tháng 10/2025 Meta công bố parental controls: phụ huynh có thể tắt hẳn chat 1-1 giữa teen và AI characters hoặc chặn từng character, và xem "insights" về chủ đề teen hay trò chuyện. **Không tắt được Meta AI assistant lõi**, assistant này vẫn giữ các lớp bảo vệ theo tuổi mặc định. Trải nghiệm teen gắn với chuẩn PG-13. Dự kiến ra mắt đầu 2026 cho tài khoản tiếng Anh ở US, UK, CA, AU — [iPhone in Canada 17/10/2025](https://www.iphoneincanada.ca/2025/10/17/meta-ai-characters/); [TechRadar](https://www.techradar.com/ai-platforms-assistants/meta-is-giving-parents-a-kill-switch-for-teen-ai-chats); [HackerNoon](https://hackernoon.com/meta-adds-teen-safety-controls-following-flirty-ai-chatbot-scandal)
- **Snapchat My AI**: không bị gỡ, nhưng thành trọng tâm của vụ kiện Utah (6/2025) và khiếu nại FTC → DOJ (đầu 2025), như đã nêu ở mục 1 — [Utah Commerce](https://commerce.utah.gov/2025/06/30/utah-sues-snapchat-for-unleashing-experimental-ai-technology-on-young-users-while-misrepresenting-the-safety-of-the-platform-2/); [Maginative](https://www.maginative.com/article/ftc-flags-snaps-ai-chatbot-for-potential-harm-refers-case-to-doj/)
- **Xiaohongshu Diandian**: app độc lập không hiệu quả, bị gộp lại thành AI search bên trong app chính — [36Kr EU](https://eu.36kr.com/en/p/3799028783439111)
- **Meta fact-checking**: bị bỏ ở Mỹ (có hiệu lực khoảng 07/04/2025) vì "over-enforcing" và "too many mistakes". IFCN chỉ trích quyết định này có nguy cơ "undo nearly a decade of progress" — [Meta Newsroom](https://about.fb.com/news/2025/01/meta-more-speech-fewer-mistakes/amp/); [ppc.land](https://ppc.land/meta-ends-fact-checking-program-in-favor-of-community-notes/)
- Core, có số tăng trưởng: Andromeda và GEM (+5%/+3% conversions, sau đó +15,7% conversions trên Facebook Q2/2026, tự báo cáo); Advantage+ (+37%, tự báo cáo, có phản biện) — [Moburst](https://www.moburst.com/blog/meta-advantage-andromeda-lattice-and-gem-explained/); [ppc.land](https://ppc.land/meta-says-andromeda-lifted-facebook-conversions-2-4-since-2025/)

### Inferences
- Mẫu chung của các thất bại: (1) persona hoặc companion hội thoại mở, (2) người dùng vị thành niên, (3) thu thập dữ liệu nhạy cảm như vị trí mà không công bố rõ, (4) rollout vội. Tripflix nhắm người dùng trẻ Việt Nam, có thể có cả người dưới 18. Vì vậy **không nên** làm AI "bạn đồng hành" hay persona flirty. Nên giới hạn chatbot vào domain du lịch và booking, kèm guardrail chặn chủ đề ngoài phạm vi.
- Bài học từ vụ Snap và Ghost Mode: nếu AI dùng vị trí (ví dụ gợi ý resort gần đây), phải tôn trọng mọi thiết lập quyền riêng tư sẵn có và công bố rõ bên thứ ba xử lý dữ liệu (OpenAI, Gemini...). Ở Việt Nam, Nghị định 13/2023 về bảo vệ dữ liệu cá nhân là khung tương ứng (đây là suy luận, chưa tra trong phiên này).
- Bài học từ Diandian: tách AI thành app riêng thì thất bại, nhúng vào luồng tìm kiếm sẵn có thì tốt hơn.

### Gaps
- Chưa thấy nguồn xác nhận parental controls của Meta đã thực sự ra mắt năm 2026, hay việc chặn AI Studio với người dưới 18 vẫn còn hiệu lực.
- Chưa có kết quả cuối của vụ Utah kiện Snap hay của hồ sơ FTC chuyển DOJ.

## 3. AI assistant được trình bày thế nào (entry point, grounding, citation, an toàn cho minor) và tóm tắt bình luận/review (liên quan B1 "50 người đã đi nói gì")

### Takeaway
Các entry point thành công gắn với **ngữ cảnh**: nút "Ask" ngay trên video đang xem, ô hỏi đặt ngay dưới review của một địa điểm, icon cạnh video. Chatbot tổng quát đặt ở DM thì gây rủi ro. Mẫu tóm tắt review tốt nhất (Amazon "Customers say", Google Maps) có 3 đặc điểm: chỉ dùng **review đã xác minh**, đòi hỏi **ngưỡng đồng thuận** (nhiều người cùng ý), và **cho xem nguồn hoặc lọc theo thuộc tính**.

### Cited Findings
**Entry point và grounding**
- Tako: icon ở cột nút phải của video, bấm vào mở chat, chuyên gợi ý video, có disclaimer "có thể không đúng" — [Search Engine Land](https://searchengineland.com/tiktok-tako-ai-chatbot-search-427584)
- YouTube "Ask": đặt trên trang xem video, hỏi về chính video đó (grounded vào video) — [9to5Google](https://9to5google.com/2023/11/06/youtube-tests-ai-that-lets-you-ask-questions-about-videos-and-summarize-comments/)
- "Ask YouTube" (2026): search hội thoại trả về tóm tắt kèm **video được trích dẫn**, giới hạn từ 18 tuổi trở lên — [Search Engine Journal](https://searchenginejournal.com/google-tests-ask-youtube-conversational-search-experiment/573175/)
- Google Maps: ô hỏi đặt **dưới phần review**, có sẵn vài câu hỏi gợi ý. Hỏi được cả câu sự thật (giờ mở cửa) lẫn câu cảm nhận (không khí). Gemini trả lời dựa trên review, ảnh, thông tin địa điểm và web, **đồng thời hiện các review làm căn cứ** để người dùng kiểm tra — [Android Authority](https://androidauthority.com/google-ask-maps-3495771); [9to5Google 31/10/2024](https://9to5google.com/2024/10/31/google-maps-gemini/); [Google India blog](https://blog.google/intl/en-in/products/explore-communicate/google-maps-in-india-supercharged-with-gemini/)
- Google Maps có Gemini tóm tắt review thành một "snapshot" về cảm nhận chung với địa điểm. Ra mắt ở Mỹ trước, sau đó đến Ấn Độ — [9to5Google](https://9to5google.com/2024/10/31/google-maps-gemini/); [Google India blog](https://blog.google/intl/en-in/products/explore-communicate/google-maps-in-india-supercharged-with-gemini/)
- Meta: teen không chat 1-1 được với AI characters theo mặc định; Meta AI lõi vẫn mở nhưng có bảo vệ theo tuổi và chuẩn PG-13 — [HackerNoon](https://hackernoon.com/meta-adds-teen-safety-controls-following-flirty-ai-chatbot-scandal); [TechRadar](https://www.techradar.com/ai-platforms-assistants/meta-is-giving-parents-a-kill-switch-for-teen-ai-chats)

**Tóm tắt bình luận và review**
- Amazon "Customers say" (8/2023): một đoạn văn do AI sinh, đặt đầu phần review. **Chỉ dùng review dạng chữ từ Verified Purchase.** **Chỉ sinh highlight khi nhiều khách cùng chung một ý.** Có các chip thuộc tính (ví dụ "ease of use"), bấm vào để lọc ra review nhắc đến thuộc tính đó. Sản phẩm không đủ review có ý trùng nhau thì không có tóm tắt. Đã mở ở US, UK, AU, IN, SG, UAE — [About Amazon](https://www.aboutamazon.com/news/amazon-ai/amazon-ai-generated-review-highlights); [Search Engine Land](https://searchengineland.com/amazon-ai-generated-customer-review-highlights-430693); [GeekWire](https://www.geekwire.com/2023/amazon-rolls-out-ai-generated-summaries-of-customer-reviews/)
- Amazon kỳ vọng cách làm này lọc được bot và review giả (đây là tuyên bố của Amazon, chưa có kiểm chứng độc lập) — [New Atlas](https://newatlas.com/technology/amazon-ai-customer-reviews/)
- YouTube comment "Topics" chỉ bật trên một số ít video tiếng Anh có **nhiều bình luận**, và chỉ cho Premium — [9to5Google](https://9to5google.com/2023/11/06/youtube-tests-ai-that-lets-you-ask-questions-about-videos-and-summarize-comments/)

### Inferences (áp dụng cho B1 "50 người đã đi nói gì")
- Thiết kế đề xuất, mô phỏng Amazon và Google Maps: (1) chỉ dùng review hoặc bình luận từ **người đã có booking hoàn thành** (tương đương Verified Purchase), coi đây là lợi thế OTA mà mạng xã hội thuần không có; (2) đặt **ngưỡng tối thiểu**, ví dụ từ khoảng 10–20 review mới hiện tóm tắt, và một ý chỉ được nêu khi có ít nhất N người cùng nhắc; (3) đầu ra gồm một đoạn ngắn và 3–6 **chip khía cạnh** (bãi biển, đồ ăn, trẻ em, giá, dịch vụ), mỗi chip có điểm tích cực/tiêu cực và bấm vào ra được review gốc; (4) ghi rõ "Tóm tắt bởi AI từ X đánh giá" để minh bạch; (5) tóm tắt sinh **theo lô (batch hoặc cache)** khi có review mới, không gọi LLM mỗi lần mở trang, nên chi phí và độ trễ gần như bằng 0 khi đọc.
- Rủi ro cần nêu trong demo: tóm tắt có thể "làm phẳng" ý kiến thiểu số (ví dụ một vụ ngộ độc thực phẩm); dễ bị thao túng nếu không xác minh người viết; dễ hallucinate thuộc tính không có thật. Cách giảm rủi ro là trích dẫn review gốc và giữ ngưỡng đồng thuận.
- Vì Tripflix gắn với Vinpearl, một bên có lợi ích trực tiếp, tóm tắt cần giữ cả điểm tiêu cực để tránh bị coi là quảng cáo trá hình. Đây là suy luận, chưa có nguồn.

### Gaps
- Chưa đọc được nguyên văn trang Amazon (fetch bị chặn) nên chưa có chi tiết về nhãn sentiment theo màu, tần suất cập nhật, hay số review tối thiểu cụ thể.
- Chưa tìm được bài phân tích độc lập nào về lỗi hoặc hallucination của Amazon review highlights hay Google Maps review summaries.
- YouTube comment summary: chưa rõ đã được phát hành rộng hay đã dừng.

## 4. Image / visual search (liên quan B2)

### Takeaway
Visual search trên các nền tảng social chủ yếu phục vụ **commerce**: TikTok Shop image search (6/2024), TikTok "Visual Search Tags" để tìm sản phẩm hoặc nội dung giống trong video (thử nghiệm, 10/2025), Pinterest Lens (từ 2017, quy mô hàng trăm triệu lượt mỗi tháng). Với Tripflix, cách tương đương là "chụp hoặc tải ảnh một bãi biển hay resort, tìm video hoặc phòng tương tự".

### Cited Findings
- TikTok Shop image search: icon camera trong thanh search của TikTok Shop, chụp hoặc tải ảnh lên để tìm sản phẩm tương tự. Có mặt ở Mỹ và Đông Nam Á (6/2024). Có nguồn mô tả đây chỉ là bản test, một nguồn sớm hơn còn nói chỉ test ngoài Mỹ, nên các nguồn mâu thuẫn — [TechCrunch 11/06/2024](https://techcrunch.com/2024/06/11/tiktok-comes-for-google-as-it-quietly-rolls-out-image-search-capabilities-in-tiktok-shop); [Tubefilter](https://www.tubefilter.com/2024/06/12/tiktok-shop-image-search-engine/); [Search Engine Land](https://searchengineland.com/tiktok-shop-visual-search-428235)
- TikTok "Visual Search Tags" / "Find Similar" (khoảng 10/2025): tạm dừng video, AI nhận diện object, tìm sản phẩm hoặc bài tương tự. Mới là thử nghiệm, chưa thấy thông báo chính thức — [onlinemarketing.de](https://onlinemarketing.de/social-media-marketing/tiktoks-visual-search-tagging); [MediaPost](https://www.mediapost.com/publications/article/396790/tiktok-testing-visual-search-for-ecommerce.html?edition=134602)
- Pinterest Lens: đội computer vision hình thành sau khi mua VisualGraph năm 2014. Lens nhận diện object và gợi ý Pin liên quan, có "Visual Guides" cho nhiều object. Năm 2018 đạt khoảng 600 triệu lượt search mỗi tháng — [TechCrunch 2017](https://techcrunch.com/2017/02/22/how-pinterests-visual-search-went-from-a-moonlight-project-to-a-real-world-search-engine/); [VentureBeat](https://venturebeat.com/ai/pinterest-lens-sees-600-million-visual-searches-every-month)

### Inferences
- Bản demo cho sinh viên: tạo embedding ảnh bằng CLIP hoặc SigLIP (hoặc API multimodal) cho thumbnail và keyframe video cùng ảnh phòng, lưu trong pgvector hoặc FAISS, rồi tìm k-NN theo cosine. Vài trăm đến vài nghìn ảnh là đủ. Không cần object detection hay crop vùng như Pinterest Lens.
- Ca dùng hợp với du lịch: "ảnh này ở đâu?" (nhận diện địa điểm Vinpearl) và "tìm phòng hay cảnh tương tự". Kết quả nên hiển thị dạng lưới card video hoặc phòng có nút "Đặt ngay", tận dụng lợi thế OTA.

### Gaps
- Không tìm được tài liệu kỹ thuật Pinterest giai đoạn 2024–2025, cũng không có thông tin về "Pinterest Assistant".
- Chưa xác minh được tích hợp Google Lens trong Instagram hay TikTok, hay "Circle to Search" với video ngắn.
- Không có số liệu adoption của TikTok visual search.

## 5. AI cho moderation ở quy mô lớn (liên quan admin)

### Takeaway
TikTok gỡ khoảng 87% video vi phạm bằng hệ thống tự động, với tỷ lệ proactive khoảng 99%. Dù vậy vẫn có hàng triệu video được **khôi phục sau khi người xem xét lại**, nên human-in-the-loop và kênh kháng nghị là bắt buộc. Meta đi theo hướng giảm enforce sai bằng cách dùng LLM làm "second opinion" và chuyển sang Community Notes.

### Cited Findings
- TikTok Q4/2025 (tháng 10–12): gỡ 175,3 triệu video, khoảng 0,5% tổng lượng upload. 152,6 triệu (khoảng 87%) do công cụ tự động phát hiện và gỡ. Tỷ lệ proactive 99,1%, 93,4% được gỡ trong 24 giờ. **Khoảng 8,4 triệu video được khôi phục sau human review.** Nguồn là báo thứ cấp; có bài khu vực ghi proactive 99,9%; một bài Korea Times đặt tít "AI catches 97%" nhưng phần thân bài lại ghi khoảng 87% — [Dhaka Tribune](https://www.dhakatribune.com/amp/feature/tech/408119/tiktok-releases-q4-2025-community-guidelines); [Korea Times 26/08/2026](https://www.koreatimes.co.kr/business/tech-science/20260826/inside-tiktoks-transparency-center-ai-catches-97-of-removed-videos); [Global Dating Insights](https://www.globaldatinginsights.com/news/tiktok-112-million-violative-pieces-of-content-removed-in-h2-2025/)
- TikTok Q2/2025: gỡ 189.578.228 video (khoảng 0,7% lượng upload), trong đó 163.962.241 (khoảng 86%) bằng tự động — [HapaKenya 04/11/2025](https://hapakenya.com/2025/11/04/tiktok-q2-2025-report-details-massive-global-content-enforcement/)
- TikTok công bố báo cáo minh bạch theo DSA tại EU (báo cáo thứ 5, 29/08/2025) — [TikTok Newsroom](https://newsroom.tiktok.com/digital-services-act-our-fifth-transparency-report-on-content-moderation-in-europe)
- Meta: LLM làm second opinion trước khi enforce; lỗi enforce ở Mỹ giảm khoảng 50% (Q4/2024 → Q1/2025, tự báo cáo); cam kết đưa chỉ số enforcement-mistake vào các báo cáo sau — [Meta Newsroom](https://about.fb.com/news/2025/01/meta-more-speech-fewer-mistakes/amp/)

### Inferences (cho admin Tripflix)
- Pipeline tối thiểu: khi upload, chạy qua moderation API miễn phí hoặc rẻ (ví dụ OpenAI Moderation, Google Perspective, hoặc LLM classifier cho text và caption; ảnh hoặc keyframe qua một classifier NSFW). Gán nhãn và điểm, rồi chia 3 ngưỡng: auto-approve / **hàng chờ admin duyệt** / auto-reject. Màn hình admin hiện lý do do AI gợi ý và có nút override, kèm luồng kháng nghị cho creator.
- Con số khoảng 8,4 triệu video được khôi phục của TikTok là luận điểm tốt để bảo vệ thiết kế human-in-the-loop trước hội đồng.
- Riêng cho travel: cần phát hiện review hoặc video giả và quảng cáo trá hình (ví dụ khách sạn đối thủ chèn link). LLM phân loại "spam/off-topic" là đủ.

### Gaps
- Chưa có số liệu chính thức từ báo cáo Community Standards Enforcement của Meta (tỷ lệ proactive theo hạng mục) trong phiên này.
- Chưa có số liệu về chi phí hay số lượng moderator người.
- Chưa đọc được bản gốc báo cáo của TikTok (chỉ có snippet thứ cấp).

## 6. Pattern nào "đủ cho student demo", pattern nào là "dao mổ trâu chặt thịt gà"

### Takeaway
Với một bản demo sinh viên, giá trị cao nhất đến từ những pattern **grounded, theo lô, có trích dẫn**: tóm tắt review có ngưỡng, Q&A trên trang resort dựa vào review và dữ liệu phòng, visual search bằng embedding, moderation qua API kèm hàng chờ admin. Training recommender quy mô lớn kiểu Andromeda hay GEM, persona hoặc companion, AI avatar quảng cáo, và agent tự động đặt phòng nhiều bước là quá tay và nhiều rủi ro.

### Cited Findings (căn cứ)
- Google Maps hiện các review làm căn cứ cho câu trả lời, nên grounding và citation là chuẩn mực — [9to5Google](https://9to5google.com/2024/10/31/google-maps-gemini/)
- Amazon chỉ dùng Verified Purchase và cần nhiều người cùng ý — [About Amazon](https://www.aboutamazon.com/news/amazon-ai/amazon-ai-generated-review-highlights)
- Ngay YouTube cũng chỉ bật tóm tắt bình luận khi video có nhiều bình luận, và test giới hạn — [9to5Google](https://9to5google.com/2023/11/06/youtube-tests-ai-that-lets-you-ask-questions-about-videos-and-summarize-comments/)
- Hệ ads ranking của Meta chấm điểm hơn 1 tỷ quảng cáo mỗi giây (Andromeda), quy mô hạ tầng không liên quan gì đến một bản demo — [ContentGrip](https://www.contentgrip.com/meta-andromeda-ad-retrieval-billion/)
- Chatbot mở cho người trẻ dẫn đến kiện tụng (Snap, Utah) và scandal (Meta, Reuters) — [Utah Commerce](https://commerce.utah.gov/2025/06/30/utah-sues-snapchat-for-unleashing-experimental-ai-technology-on-young-users-while-misrepresenting-the-safety-of-the-platform-2/); [CO/AI](https://getcoai.com/news/meta-updates-ai-chatbot-policies-after-document-revealed-child-safety-gaps)
- App AI tách riêng (Diandian) không thành công — [36Kr EU](https://eu.36kr.com/en/p/3799028783439111)
- Tako chủ yếu dùng chat để dẫn đến nội dung có sẵn, và có disclaimer — [Search Engine Land](https://searchengineland.com/tiktok-tako-ai-chatbot-search-427584)

### Inferences: bảng phân loại đề xuất cho Tripflix

| Pattern (nguồn cảm hứng) | Đủ cho demo? | Gợi ý triển khai |
|---|---|---|
| B1: tóm tắt "N người đã đi nói gì" (Amazon, Google Maps) | **Đủ, nên làm** | LLM batch theo resort, chip khía cạnh, ngưỡng tối thiểu, link về review gốc, nhãn "AI tóm tắt" |
| "Hỏi về resort này" ngay trên trang hoặc video (YouTube Ask, Maps Ask) | **Đủ** | RAG trên review cộng dữ liệu phòng và giá; trích dẫn; câu hỏi gợi ý sẵn; từ chối câu ngoài phạm vi |
| Chat search "tìm chuyến đi" ra card video hoặc phòng (Tako, Ask YouTube) | Đủ nếu giữ đơn giản | LLM trích slot (điểm đến, ngày, ngân sách, số người), tool-call vào search và DB, trả card; không cần agent đa bước |
| B2: visual search (TikTok Shop, Pinterest Lens) | **Đủ** | Embedding CLIP và kNN; vài nghìn ảnh |
| Moderation (TikTok, Meta) | **Đủ** | Moderation API, 3 ngưỡng, hàng chờ admin, kháng nghị |
| Recommendation feed | Đủ ở mức heuristic hoặc embedding | Content-based (embedding, tag) cộng tín hiệu xem, like, lưu; **không** train deep model hay two-tower quy mô lớn |
| Creator tools (auto-caption, dịch, dubbing, như Symphony) | Tuỳ chọn | Gọi API Whisper hoặc STT cho phụ đề tiếng Việt; dubbing hay avatar là thừa |
| Ads ranking kiểu Andromeda/GEM, Advantage+ | **Quá tay** | Bỏ |
| AI persona/companion, AI Studio | **Quá tay và rủi ro** | Bỏ, vì người dùng trẻ |
| Agent tự động đặt phòng và thanh toán | **Quá tay và rủi ro** | Chỉ "draft booking" để người dùng xác nhận |

- Nguyên tắc rút ra từ các nền tảng: (1) AI nằm trong ngữ cảnh, không thành app riêng; (2) có grounding và trích dẫn; (3) chỉ hiện khi dữ liệu đủ; (4) gắn nhãn AI; (5) có guardrail theo tuổi và phạm vi chủ đề; (6) xử lý theo lô hoặc cache để tiết kiệm chi phí; (7) đo mức tăng (CTR, chuyển đổi booking) như cách Meta và TikTok báo cáo.

### Gaps
- Không có số liệu latency hay chi phí công khai cho các tính năng trên, nên chưa thể đưa ngân sách token cụ thể từ nguồn.
- Chưa có nghiên cứu đo hiệu quả kinh doanh độc lập của chatbot social (Tako, My AI) so với search truyền thống.
