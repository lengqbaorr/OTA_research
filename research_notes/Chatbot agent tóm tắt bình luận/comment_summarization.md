# Tóm tắt bình luận bằng AI: các nền tảng production làm thế nào và nền tảng nghiên cứu phía sau (tham chiếu kỹ thuật cho card "Người đi rồi nói gì" của Tripflix)

> **Lưu ý phương pháp (đọc trước):** Trong phiên này WebFetch bị chặn DNS với mọi domain đã thử (machinelearning.apple.com, aboutamazon.com, phocuswire.com, arxiv.org). Vì vậy **toàn bộ dữ kiện dưới đây đến từ snippet/tóm tắt của WebSearch**, không phải đọc trọn trang gốc. Những chỗ được đánh dấu **[snippet]** là chỉ thấy qua snippet; những chỗ đánh dấu **[trí nhớ, chưa kiểm chứng]** là kiến thức nền của người nghiên cứu, không có nguồn xác thực trong phiên này, report writer nên coi là giả thuyết. Muốn kiểm chứng sâu hơn cần mở các domain trên trong network settings của session.

---

## 1. Thiết kế sản phẩm: các nền tảng production tóm tắt review/bình luận ra sao?

### Takeaway
Mọi hệ thống production đáng tin đều đi theo cùng một khung: **(a) chỉ lấy nguồn "đã xác thực" (verified purchase/stay), (b) có ngưỡng tối thiểu số review và cửa sổ thời gian, (c) lọc spam/fraud trước khi gọi LLM, (d) cấu trúc theo aspect/chủ đề có thể bấm để xem review gốc, (e) có nhãn "AI-generated" + nút báo cáo, (f) làm mới định kỳ**. Lỗi công khai lớn nhất (Tripadvisor, bị Which? phê bình 2026) là tóm tắt "làm mềm" các phàn nàn nghiêm trọng về an toàn — đúng rủi ro mà mục "Video vs thực tế" của Tripflix phải tránh.

### Cited Findings

**Amazon — "Customers say" / review highlights (ra mắt 2023)**
- Một đoạn văn AI ngắn dưới tiêu đề in đậm "Customers say", kèm ghi chú rằng nội dung được sinh từ văn bản review **[snippet]** — [Search Engine Land](https://searchengineland.com/amazon-ai-generated-customer-review-highlights-430693); [About Amazon](https://www.aboutamazon.com/news/amazon-ai/amazon-ai-generated-review-highlights)
- Chỉ dùng **review dạng văn bản từ Amazon Verified Purchases**, và **yêu cầu nhiều khách hàng cùng chia sẻ một ý kiến** thì mới sinh một highlight **[snippet]** — [About Amazon](https://www.aboutamazon.com/news/amazon-ai/amazon-ai-generated-review-highlights); [About Amazon CA](https://www.aboutamazon.ca/news/customer-trust/how-amazon-continues-to-improve-the-customer-reviews-experience-with-generative-ai)
- Dưới đoạn văn có các **chip thuộc tính bấm được**; bấm vào sẽ lọc ra chỉ những review nhắc tới thuộc tính đó (tức là mỗi aspect có "trích dẫn" về review gốc) **[snippet]** — [About Amazon](https://www.aboutamazon.com/news/amazon-ai/amazon-ai-generated-review-highlights)
- Phạm vi: Mỹ, UK, Úc, Ấn Độ, Singapore, UAE, Canada (theo các bài thông cáo; có thể đã thay đổi) **[snippet]** — [About Amazon CA](https://www.aboutamazon.ca/news/customer-trust/how-amazon-continues-to-improve-the-customer-reviews-experience-with-generative-ai)
- Phê bình: một bài báo dẫn ví dụ tóm tắt cho AirPods ghi "mọi khách hàng nhắc tới độ ổn định đều có ý kiến tiêu cực" (khái quát hóa quá mức); người bán phàn nàn tóm tắt nghiêng về tiêu cực; Amazon đáp rằng phân tích của họ cho thấy tính năng giúp tăng doanh số **[snippet]** — [Gigazine](https://gigazine.net/gsc_news/en/20231220-amazon-ai-product-reviews); [PYMNTS](https://www.pymnts.com/?p=1649074)

**Apple — App Store review summaries (iOS 18.4, 2025)**
- Pipeline nhiều bước dựa trên LLM, **tóm tắt định kỳ** **[snippet]** — [Apple ML Research](https://machinelearning.apple.com/research/app-store-review)
  1. **Lọc**: loại review spam, tục tĩu, bị gắn cờ gian lận trước tiên — [Apple ML Research](https://machinelearning.apple.com/research/app-store-review); [heise](https://heise.de/-10363149)
  2. **Trích "insight"**: một LLM fine-tune bằng **LoRA adapters** chưng cất mỗi review thành tập các insight rời rạc (atomic statements) — [Apple ML Research](https://machinelearning.apple.com/research/app-store-review)
  3. **Gom chủ đề + chọn lọc**: một model khác xếp insight vào các topic, rồi chọn các topic liên quan nhất — [heise](https://heise.de/-10363149); [AppleInsider](https://appleinsider.com/articles/25/04/24/apple-explains-how-app-store-reviews-are-summarized-with-ai)
  4. **Sinh tóm tắt** từ các insight đã chọn, điều chỉnh độ dài, văn phong, giọng, bố cục; độ dài khoảng **100–300 ký tự** — [heise](https://heise.de/-10363149)
- Hàng nghìn bản tóm tắt được người đánh giá (human raters) xem xét trong quá trình phát triển **[snippet, nguồn thứ cấp]** — [Nerdschalk](https://nerdschalk.com/apple-details-multi-step-ai-system-behind-app-store-review-summaries-in-ios-18-4/)
- Làm mới **ít nhất mỗi tuần** (theo tài liệu developer, qua báo chí đưa lại) **[snippet]** — [Maginative](https://www.maginative.com/article/apple-is-bringing-ai-generated-app-review-summaries-to-ios-18-4/); [TechCrunch](https://techcrunch.com/2025/03/05/apple-adds-ai-powered-app-review-summaries-with-ios-18-4/)
- Phê bình: tóm tắt nêu điểm tốt trước, có thể gây cảm giác hơi gây hiểu lầm; developer lo bug tệ nhất bị "trưng" ra **[snippet]** — [Inventiv](https://inventiv.io/apples-ai-review-summaries-put-your-worst-bugs-on-display/)
- [trí nhớ, chưa kiểm chứng] Bài blog của Apple mô tả tiêu chí human eval gồm safety, groundedness, composition, helpfulness, và việc Apple ưu tiên insight được nhiều review đồng thuận. Cần mở trang gốc để xác nhận.

**Booking.com**
- Chỉ sinh tóm tắt cho chỗ nghỉ có **≥15 review văn bản trong 3 năm gần nhất**, nêu cả chủ đề tích cực lẫn tiêu cực **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)
- Chỉ khách đã hoàn tất lưu trú qua nền tảng mới được review (verified stayers); tóm tắt chỉ dùng các review đó **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)
- Khi phản hồi "not helpful" tăng, con người can thiệp điều chỉnh thuật toán (human-in-the-loop dựa trên feedback) **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)

**Tripadvisor**
- Review Summaries cho khách sạn (công bố 12/2024), cô đọng review gần đây theo các thuộc tính người dùng chọn, kèm **trích dẫn (quotes)**; tuyên bố chưng cất hơn 1 tỷ review **[snippet]** — [Hospitality Net](https://www.hospitalitynet.org/news/4118778/tripadvisor-launches-ai-powered-hotel-review-summaries)
- Theo Tripadvisor: AI phân tích cả review tích cực và tiêu cực **trong 12 tháng gần nhất**, có quote hỗ trợ, và **tắt tóm tắt AI cho listing có báo cáo sự cố an toàn nghiêm trọng** **[snippet]** — [Head for Points](https://www.headforpoints.com/2026/07/02/which-magazine-highlights-tripadvisor-ai-overviews/)
- Điều tra của **Which? (2026)**: tóm tắt AI che giấu báo cáo ngộ độc thực phẩm, quấy rối tình dục, vi phạm vệ sinh nghiêm trọng; Tripadvisor "fundamentally disagrees"; ít nhất một tóm tắt bị gỡ sau khi Which? nêu ra **[snippet]** — [Head for Points](https://www.headforpoints.com/2026/07/02/which-magazine-highlights-tripadvisor-ai-overviews/); [aiproductivity.ai](https://aiproductivity.ai/news/tripadvisor-ai-summaries-dangerous-hotels/)
- Không nền tảng nào công bố công khai cách xử lý review về an toàn khác với sentiment chung **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)

**Yelp**
- Đầu 2024: tóm tắt **một câu** cho trang nhà hàng/ăn uống/nightlife, dùng LLM đọc review gần đây; chỉ hiện khi có "reliable number of recent reviews" (không công bố con số); doanh nghiệp không sửa được, chỉ liên hệ support **[snippet]** — [Restaurant Business](https://www.restaurantbusinessonline.com/technology/yelp-will-use-ai-generate-restaurant-descriptions); [BGR](https://www.bgr.com/tech/yelp-is-now-using-ai-to-create-a-summary-of-your-business/)
- 12/2024: **Review Insights** — LLM phân tích sentiment theo aspect (food quality, service, ambiance), nhận diện chủ đề kể cả khi review không nói thẳng **[snippet]** — [TechCrunch](https://techcrunch.com/2024/12/10/yelp-adds-ai-powered-review-insights-to-restaurants); [Business Wire](https://www.businesswire.com/news/home/20241210611703/en/Yelp-Unveils-a-Series-of-New-AI-powered-Features-to-Enhance-Discovery-and-Connection-with-Local-Businesses)

**Google Maps / Ask Maps**
- Tóm tắt review bằng Gemini (2024): đọc nhanh người ta nghĩ gì về địa điểm, có thể hỏi tiếp (vd. có chỗ ngồi ngoài trời không) **[snippet]** — [TechCrunch](https://techcrunch.com/2024/10/31/google-maps-is-getting-new-ai-features-powered-by-gemini); [Google Blog](https://blog.google/products-and-platforms/products/maps/gemini-google-maps-navigation-updates/)
- Có nút **"Report this summary"** **[snippet]** — [PYMNTS](https://pymnts.com/?p=2287288)
- Ask Maps (2026): theo Google, phân tích thông tin từ hơn 300 triệu địa điểm và review của cộng đồng hơn 500 triệu người **[snippet, nguồn thứ cấp]** — [GSQI](https://www.gsqi.com/marketing-blog/ask-maps-gemini-meets-local-search/); [BGR](https://www.bgr.com/2166121/best-new-features-added-google-maps-2026/)
- Tóm tắt place/area/review đã GA qua Google Maps Platform API **[snippet]** — [Google Cloud Blog](https://cloud.google.com/blog/ja/products/maps-platform/discover-more-faster-ai-powered-summaries-for-places-areas-and-reviews-are-now-generally-available)

**YouTube — comment topics (gần nhất với bài toán Tripflix: bình luận dưới video)**
- 11/2023: thử nghiệm cho Premium, AI tổ chức bình luận thành **"easily digestible themes"**; tab "Topics" bên cạnh "Top"/"Newest", chỉ trên video có phần bình luận lớn **[snippet]** — [TechCrunch](https://techcrunch.com/2023/11/06/youtube-to-test-generative-ai-features-including-a-comments-summarizer-and-conversational-tool/); [Engadget](https://www.engadget.com/youtube-tests-ai-generated-comment-summaries-and-a-chatbot-for-videos-213405231.html)
- **Creator có thể xóa topic không muốn và xóa từng bình luận trong topic** **[snippet]** — [TechCrunch](https://techcrunch.com/2023/11/06/youtube-to-test-generative-ai-features-including-a-comments-summarizer-and-conversational-tool/)
- Sau đó mở rộng sang Shorts nhiều bình luận và thêm ngôn ngữ **[snippet]** — [Social Media Today](https://www.socialmediatoday.com/news/youtube-expands-ai-comment-summaries-more-languages/725584/)
- Một bài test nói tính năng chỉ chạy với video hơi cũ và có hàng chục nghìn bình luận (nguồn khác nhau về điều kiện) **[snippet]** — [Tom's Guide](https://tomsguide.com/how-to/how-to-use-youtube-ai-tools-ios-android)

**Shopee / Lazada / Taobao**
- Không tìm thấy nguồn mô tả tính năng tóm tắt review AI phía người mua của Shopee/Taobao. Có công cụ bên thứ ba AnyMind AnyX tóm tắt review Shopee/Lazada cho **brand** (topic extraction, sentiment, so sánh đối thủ), có ở Việt Nam **[snippet]** — [AnyMind](https://anymindgroup.com/news/press-release/anyx_review_analytics)
- Lazada có trợ lý AI Lazzie cho người mua, không phải review summary **[snippet]** — [Manila Times](https://www.manilatimes.net/2025/11/04/business/sunday-business-it/my-ai-lazzie-encounter-how-lazadas-ai-makes-shopping-smarter-faster-and-more-human/2214980)

### Inferences
- **Thiết kế hiện tại của Tripflix khớp với best practice**: lọc theo booking hoàn tất ≈ "verified purchase/stay" của Amazon/Booking; ngưỡng ≥5 bình luận + ≥2 traveler ≈ ngưỡng 15 review của Booking (của Tripflix thấp hơn nhiều, hợp lý với app sinh viên nhưng nên hiển thị số lượng nguồn rõ ràng); comment_ids ≈ chip bấm được của Amazon / quote của Tripadvisor.
- Nên thêm từ Amazon nguyên tắc **"nhiều người cùng ý mới thành 1 ý"**: mỗi pros/cons cần ≥2 comment_ids (code kiểm tra), riêng `reality_gaps` từ traveler đã xác minh có thể cho phép 1 nguồn nhưng gắn nhãn "1 người đã đi phản ánh".
- Bài học Tripadvisor/Which?: **phàn nàn nghiêm trọng (an toàn, lừa đảo, ngộ độc, quấy rối) không được để LLM "trung hòa"**. Nên có rule/classifier riêng đánh dấu `safety_flag`, khi có thì luôn hiển thị nổi bật (hoặc tắt card và đẩy sang moderation), không để LLM quyết định có đưa vào hay không.
- Từ YouTube: cho **chủ video quyền ẩn bình luận** nhưng **không** cho sửa/ẩn trực tiếp mục "Video vs thực tế" — nếu không, card mất giá trị chống "ảo ảnh video". Khi bình luận bị ẩn → regenerate (Tripflix đã có).
- Từ Google/Booking: thêm nhãn "Tóm tắt bởi AI từ N bình luận, M người đã đi" + nút "Báo cáo tóm tắt" + nút "hữu ích/không hữu ích" để có tín hiệu đánh giá.
- Từ Apple: tách "trích insight theo từng bình luận" (map) khỏi "gom chủ đề và viết" (reduce) giúp cache incremental — insight của bình luận cũ không cần tính lại.

### Gaps
- Airbnb (review highlights/AI summary), Reddit Answers: không có kết quả tìm kiếm trong phiên; không có dữ kiện.
- Ngưỡng tối thiểu của Apple, Amazon, Yelp (số review): không được công bố trong các snippet thấy được.
- Tần suất refresh của Amazon, Google, Tripadvisor: không tìm thấy.
- Không có số liệu tỷ lệ lỗi/hallucination công khai của bất kỳ nền tảng nào.

---

## 2. Phương pháp: ABSA, opinion summarization, phát hiện mâu thuẫn, trọng số người đã trải nghiệm, sarcasm, spam, prompt injection

### Takeaway
Hướng đã được kiểm chứng là **"chọn lọc/gom nhóm trước, LLM viết sau"** (extract → cluster → generate), không phải nhồi tất cả bình luận vào một prompt lớn hay tóm tắt đệ quy nhiều tầng — tóm tắt đệ quy làm GPT-3.5 sinh ý kiến khái quát và thiếu trung thực. Với "Video vs thực tế", bài toán bản chất là **stance/NLI giữa claim của video và bình luận**, nên tách thành bước có nhãn rõ (supports/contradicts/unrelated) thay vì để LLM tự do viết.

### Cited Findings
- **Bhaskar, Fabbri & Durrett, "Prompted Opinion Summarization with GPT-3.5" (Findings of ACL 2023)**: thử recursive summarization và chọn nội dung nổi bật qua clustering có giám sát hoặc extraction; bước T gán câu vào aspect, cluster liên quan được chunk-và-tóm-tắt lặp lại cho đến khi < 35 câu, rồi tóm tắt cuối **[snippet]** — [arXiv 2211.15914](https://arxiv.org/abs/2211.15914v2); [ACL Anthology](https://preview.aclanthology.org/dois-2013-emnlp/2023.findings-acl.591)
  - Kết quả: input ngắn thì prompt cơ bản tốt; **input lớn dần thì tóm tắt lặp làm GPT-3.5 sinh lựa chọn quan điểm khái quát và không trung thực** so với vòng đầu; lọc câu bằng extractive model (QFSumm) trước khi đưa vào GPT-3.5 giúp nhẹ về factuality/faithfulness **[snippet]** — [arXiv 2211.15914](https://arxiv.org/abs/2211.15914v2)
  - Human eval: GPT-3.5 rất mạnh trên SPACE (review khách sạn) và FewSum (Amazon/Yelp) **[snippet]** — [Papers with Code](https://paperswithcode.com/paper/zero-shot-opinion-summarization-with-gpt-3)
- **Hosking, Tang & Lapata, HIRO — "Hierarchical Indexing for Retrieval-Augmented Opinion Summarization" (2024)**: encoder nhẹ ánh xạ câu review vào đường đi trong một hierarchy rời rạc; chọn câu thông tin rồi đưa vào LLM zero-shot (kiểu RAG); human eval: mạch lạc và chính xác hơn công trình trước **[snippet]** — [arXiv 2403.00435](https://arxiv.org/html/2403.00435v1). [trí nhớ, chưa kiểm chứng] HIRO được đăng ở ACL 2024.
- **Apple** dùng đúng khung này ở production: LLM LoRA trích insight/review → model gom topic → chọn topic → model sinh tóm tắt **[snippet]** — [Apple ML Research](https://machinelearning.apple.com/research/app-store-review)
- **Yelp Review Insights**: LLM phân tích sentiment theo aspect (food, service, ambiance), nhận diện cả chủ đề ngầm **[snippet]** — [TechCrunch](https://techcrunch.com/2024/12/10/yelp-adds-ai-powered-review-insights-to-restaurants)
- **Phát hiện mâu thuẫn = NLI**: SummaC lập luận NLI thất bại trước đây do lệch độ hạt (NLI ở mức câu, inconsistency ở mức tài liệu); SummaCConv tách tài liệu thành câu và gộp điểm giữa các cặp câu **[snippet]** — [Microsoft Research](https://microsoft.com/en-us/research/?p=922785); [eScholarship](https://escholarship.org/uc/item/3dm9x6r5)
- **Spam review tiếng Việt**: ViSpamReviews (2022) ~19K review từ sàn TMĐT Việt Nam, gán nhãn thủ công theo đa số; 2 task (spam/không spam; loại spam); PhoBERT cao nhất 88.93% (bản khác ghi 86.89% — mâu thuẫn giữa các phiên bản paper) và 72.17% macro-F1 **[snippet]** — [arXiv 2207.14636](https://arxiv.org/abs/2207.14636); [Papers with Code](https://cs.paperswithcode.com/paper/detecting-spam-reviews-on-vietnamese-e)
  - ViSpamReviews v2: **chỉ dùng nội dung bình luận không đủ** để xác định spam; thêm metadata nâng lên 87.22% (+1.64%) **[snippet]** — [arXiv 2405.13292](https://arxiv.org/abs/2405.13292v2)
- **Prompt injection**: chưa thấy nghiên cứu đo lường riêng cho review summarization TMĐT. Gần nhất: chèn prompt ẩn vào bài báo khoa học khiến LLM reviewer cho điểm chấp nhận tới **100%** trên 1k review ICLR 2024 **[snippet]** — [arXiv 2509.10248](https://arxiv.org/pdf/2509.10248); Unit 42 ghi nhận indirect prompt injection ngoài thực tế nhằm vượt qua hệ thống AI duyệt quảng cáo sản phẩm **[snippet]** — [Unit 42](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- **Sarcasm**: không tìm thấy dataset sarcasm tiếng Việt trong phiên (xem Gaps). Teencode thường xử lý ở bước tiền xử lý; một project PhoBERT ABSA phân tích lỗi riêng cho phủ định, teencode, emoji, câu ngắn **[snippet]** — [ViGSA](https://www.researchgate.net/publication/396607468_ViGSA_A_Multi-Task_Aspect-Based_Sentiment_Analysis_Model_with_Auxiliary_Embedding_and_Global_Sentiment_Integration_for_Vietnamese_Restaurant_Reviews); [GitHub nghia-lh/vietnamese-absa](https://github.com/nghia-lh/vietnamese-absa)

### Inferences
- **Kiến trúc đề xuất cho Tripflix (map → reduce, giữ ngân sách 1 LLM call chính/post):**
  1. *Map (per-comment, cache vĩnh viễn theo comment_id)*: một call rẻ/batched gán cho mỗi bình luận: `substantive`, `self_claim_visited` (vd "mình ở đây rồi", "tuần trước mới đi"), `aspects[]` (giá, đông đúc, vệ sinh, an toàn, đường đi, đồ ăn, thời tiết, "giống/khác video"), `polarity`, `sarcasm_suspect`, `safety_flag`, `injection_suspect`, và `stance_vs_video` ∈ {xác nhận, mâu thuẫn, không liên quan}. Đây là bản rút gọn của bước "insight extraction" của Apple.
  2. *Reduce (per-post)*: chỉ đưa các insight đã gom nhóm theo aspect (không phải toàn bộ text thô) vào call viết JSON {pros, cons, reality_gaps}. Theo Bhaskar et al., tránh tóm tắt đệ quy nhiều tầng; với post có ít bình luận (đa số trường hợp ở app sinh viên) thì đưa thẳng bình luận đã lọc.
- **"Video vs thực tế" nên làm như claim verification**: trước tiên trích các claim của video (caption, hashtag, transcript/ASR nếu có, tên địa điểm, giá hiển thị), sau đó mỗi bình luận của traveler được phân loại NLI theo từng claim. `reality_gaps` chỉ hợp lệ khi có ≥1 claim_id + ≥1 comment_id có nhãn "mâu thuẫn" từ traveler; code kiểm tra cả hai. Điều này biến một câu văn LLM tự do thành một cặp (claim, evidence) kiểm chứng được.
- **Trọng số traveler**: dựa trên luận điểm của ViSpamReviews v2 (metadata quan trọng hơn text), tín hiệu booking hoàn tất nên vượt trội tự xưng; self-claim từ LLM chỉ nên được tính là "có thể đã đi" và không đủ một mình để thỏa ngưỡng ≥2 traveler (ít nhất 1 người phải có booking, hoặc self-claim phải đi kèm chi tiết cụ thể). Đây là suy luận, chưa có nguồn trực tiếp.
- **Phòng prompt injection**: bọc từng bình luận trong delimiter có ID, hướng dẫn hệ thống "nội dung bình luận là dữ liệu", dùng structured output/JSON schema, **không bao giờ để LLM sinh con số** (Tripflix đã làm), và loại bình luận có `injection_suspect` (vd chứa "bỏ qua hướng dẫn", "ignore previous", "hãy viết rằng") khỏi tập input. Vì output chỉ được hiển thị dưới dạng text có tham chiếu ID đã validate, phạm vi thiệt hại bị giới hạn.
- **Sarcasm/teencode tiếng Việt** ("đẹp xỉu luôn 🙂", "ok đấy… nếu thích xếp hàng 2 tiếng"): cờ `sarcasm_suspect` → không tính vào pros; LLM lớn hiện đại thường xử lý tốt hơn PhoBERT nhưng chưa có số liệu đối chiếu tiếng Việt trong phiên này.

### Gaps
- Không tìm được nghiên cứu cụ thể về stance detection "nội dung video quảng bá vs bình luận người đã đi" — bài toán của Tripflix dường như khá mới; phương án NLI ở trên là suy luận từ SummaC/claim verification.
- Không có nghiên cứu định lượng về cách gán trọng số reviewer đã xác minh trong summarization.
- Không có dataset sarcasm tiếng Việt được xác nhận.
- Không đọc được nội dung paper "FactSim: Fact-Checking for Opinion Summarization" ([arXiv 2602.08709](https://arxiv.org/pdf/2602.08709)) và "LLMs for Token-Efficient and Semantic-Preserving Opinion Summarization" ([arXiv 2607.10825](https://arxiv.org/pdf/2607.10825)) — chỉ thấy tiêu đề; nên đọc nếu có thời gian.

---

## 3. Faithfulness và đánh giá: tỷ lệ hallucination, metric, giao thức human eval, cỡ mẫu

### Takeaway
Bộ metric chuẩn gồm: **faithfulness/factual consistency** (SummaC, AlignScore — dựa trên NLI/alignment), **chất lượng trích dẫn** (citation recall/precision kiểu ALCE), cộng với **coverage** và LLM-as-judge. Với Tripflix, phần lớn "citation accuracy" có thể kiểm tra **tất định bằng code** (ID tồn tại, hiển thị, thuộc đúng post); phần còn lại (ID có thực sự ủng hộ câu đó không) cần NLI hoặc LLM-judge trên một mẫu.

### Cited Findings
- **AlignScore**: metric factual consistency tổng quát dựa trên hàm alignment hợp nhất; fine-tune RoBERTa nhẹ (125M/355M); trên benchmark SummaC, AlignScore-large AUC-ROC **88.6** vs **81.0** của SummaC-Conv; hạn chế: chỉ cho 1 điểm cho cả tóm tắt, không chỉ ra span sai **[snippet]** — [arXiv 2305.16739](https://arxiv.org/pdf/2305.16739); [ar5iv](https://ar5iv.labs.arxiv.org/html/2305.16739)
- **SummaC**: NLI mức câu + gộp điểm; benchmark gồm 6 dataset phát hiện inconsistency lớn **[snippet]** — [eScholarship](https://escholarship.org/uc/item/3dm9x6r5)
- **ALCE** (citation evaluation): **Citation Recall** = output có được tài liệu trích dẫn hỗ trợ toàn bộ không; **Citation Precision** = từng trích dẫn có hỗ trợ câu tương ứng không; entailment do TRUE (T5-11B fine-tune NLI) chấm **[snippet]** — [Hugging Face Papers 2305.14627](https://huggingface.co/papers/2305.14627); [Liner review](https://liner.com/review/enabling-large-language-models-to-generate-text-with-citations)
- **FaithBench (NAACL 2025)**: benchmark hallucination cho summarization với nhãn vùng xám "questionable"/"benign" **[snippet]** — [ACL Anthology](https://aclanthology.org/2025.naacl-short.38.pdf)
- **FaithJudge / Vectara leaderboard (EMNLP 2025 Industry)**: benchmark faithfulness LLM trong summarization, QA, data-to-text (không riêng review) **[snippet]** — [ACL Anthology](https://aclanthology.org/2025.emnlp-industry.54/)
- **OpinSummEval** (arXiv 2310.18122) xem lại đánh giá tự động cho opinion summarization (chỉ thấy qua trích dẫn trong FactSim) **[snippet, chỉ qua reference list]** — [FactSim arXiv 2602.08709](https://arxiv.org/pdf/2602.08709)
- Bhaskar et al. 2023 dùng human eval trên faithfulness/factuality/genericity khi so sánh pipeline **[snippet]** — [arXiv 2211.15914](https://arxiv.org/abs/2211.15914v2)
- Apple dùng human raters trên hàng nghìn tóm tắt **[snippet, thứ cấp]** — [Nerdschalk](https://nerdschalk.com/apple-details-multi-step-ai-system-behind-app-store-review-summaries-in-ios-18-4/)
- Booking.com dùng tỷ lệ "not helpful" như tín hiệu vận hành để con người can thiệp **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)

### Inferences
- **Bộ đánh giá đề xuất cho Tripflix (khả thi với đồ án sinh viên):**
  1. *Kiểm tra tất định (100% output, trong code)*: tỷ lệ JSON hợp lệ; tỷ lệ comment_id tồn tại/hiển thị/thuộc post; mỗi ý có ≥N nguồn; mỗi reality_gap có ≥1 traveler; không có số liệu do LLM viết. Lỗi → retry 1 lần → không hiển thị card.
  2. *Citation precision (mẫu)*: với mỗi (câu tóm tắt, comment_id), hỏi LLM-judge/NLI "bình luận này có ủng hộ câu này không" → % được hỗ trợ (cách làm kiểu ALCE). AlignScore/SummaC là model tiếng Anh; với tiếng Việt nên dùng LLM-judge đa ngữ hoặc dịch — cần kiểm chứng.
  3. *Coverage/recall*: các bình luận traveler có `stance=mâu thuẫn` hoặc `safety_flag` mà **không** xuất hiện trong output → đây chính là metric bắt lỗi kiểu Tripadvisor/Which?. Nên coi "phàn nàn an toàn bị bỏ sót" là lỗi nghiêm trọng (target 0).
  4. *Human eval nhỏ*: 2 người chấm độc lập ~30–50 post trên thang faithfulness / coverage / hữu ích, đo agreement. Con số 30–50 là đề xuất thực dụng, **không** có nguồn.
  5. *Tín hiệu production*: nút hữu ích/không hữu ích + báo cáo (Booking, Google).
- Nguyên tắc "nhiều người cùng ý" (Amazon) và "đếm bằng code" của Tripflix giải quyết trực tiếp lỗi khái quát hóa kiểu "mọi khách hàng nhắc tới… đều tiêu cực" của Amazon.

### Gaps
- **Không tìm thấy con số tỷ lệ hallucination cụ thể cho LLM review/opinion summarization** trong phiên này (không được bịa). FaithBench/FaithJudge có số nhưng cho summarization chung, và không đọc được bảng số.
- Không có nguồn về cỡ mẫu tối thiểu cho human eval hay số review tối thiểu "có ý nghĩa thống kê" cho một tóm tắt; ngưỡng công khai duy nhất tìm được là 15 review của Booking.com.
- Chưa xác nhận được metric faithfulness nào đã được kiểm định cho tiếng Việt.

---

## 4. Tài nguyên tiếng Việt: dataset sentiment/ABSA, teencode, lựa chọn model

### Takeaway
Có đủ dataset để **đánh giá** (không nhất thiết để train) các thành phần phân loại: VLSP 2018 ABSA (nhà hàng, khách sạn — gần du lịch nhất), UIT-ABSA (hotel/restaurant), UIT-ViSFD (smartphone), UIT-VSFC (phản hồi sinh viên, cấp câu), ViSpamReviews (spam), ViHOS (span ngôn từ thù ghét). Model encoder mạnh nhất cho văn bản mạng xã hội là **ViSoBERT**, nhỉnh hơn PhoBERT.

### Cited Findings
- **UIT-ViSFD**: 11,122 bình luận TMĐT smartphone gán nhãn ABSA; PhoBERT 86.03 F1 (phát hiện aspect) / 78.76 (sentiment theo aspect), ViSoBERT 88.63 / 83.55 (theo bảng trong paper BamiBERT) **[snippet]** — [BamiBERT arXiv 2607.02259](https://arxiv.org/html/2607.02259)
- **UIT-ABSA** (hotel, restaurant): ViSoBERT và PhoBERT tương đương, ViSoBERT dẫn ở hotel classification (74.24 F1) **[snippet]** — [BamiBERT arXiv 2607.02259](https://arxiv.org/html/2607.02259)
- **VLSP 2018 ABSA** (nhà hàng, khách sạn): aspect category detection + sentiment polarity; review dài và giàu ngữ cảnh hơn UIT-ABSA nên pattern lỗi khác **[snippet]** — [GitHub VLSP-2018-ABSA](https://github.com/HuynhXuanLam-IT44/VLSP-2018-ABSA); [ViGSA](https://www.researchgate.net/publication/396607468_ViGSA_A_Multi-Task_Aspect-Based_Sentiment_Analysis_Model_with_Auxiliary_Embedding_and_Global_Sentiment_Integration_for_Vietnamese_Restaurant_Reviews)
- **UIT-VSFC**: >16,000 câu phản hồi sinh viên gán nhãn sentiment và topic (cấp câu, không phải ABSA); PhoBERT 94.10 accuracy sentiment **[snippet]** — [GitHub kietnv/VietnameseDatasets](https://github.com/kietnv/VietnameseDatasets); [BamiBERT](https://arxiv.org/html/2607.02259)
- **ViHOS**: task phát hiện span ngôn từ thù ghét (hate speech spans), không phải ABSA **[snippet]** — [5CD-AI/Vietnamese-Sentiment-visobert](https://huggingface.co/5CD-AI/Vietnamese-Sentiment-visobert)
- **ViSoBERT**: pretrain trên mạng xã hội; biến thể continued-pretraining 14GB vượt ViSoBERT gốc và PhoBERT ở emotion, hate speech, spam review, hate span trên phần lớn task **[snippet]** — [5CD-AI/visobert-14gb-corpus](https://huggingface.co/5CD-AI/visobert-14gb-corpus)
- Model sentiment tiếng Việt dựng sẵn trên ViSoBERT: `5CD-AI/Vietnamese-Sentiment-visobert` **[snippet]** — [Hugging Face](https://huggingface.co/5CD-AI/Vietnamese-Sentiment-visobert)
- **ViSpamReviews** trên Hugging Face: `visolex/ViSpamReviews` **[snippet]** — [Hugging Face](https://huggingface.co/datasets/visolex/ViSpamReviews/blob/main/ViSpamReviews.csv)
- **Teencode**: xử lý như tiền xử lý (chuẩn hóa noise, teencode, dấu thanh không nhất quán) trong ViGSA; là một trong các nhóm lỗi chính khi phân tích PhoBERT ABSA **[snippet]** — [ViGSA](https://www.researchgate.net/publication/396607468_ViGSA_A_Multi-Task_Aspect-Based_Sentiment_Analysis_Model_with_Auxiliary_Embedding_and_Global_Sentiment_Integration_for_Vietnamese_Restaurant_Reviews); [GitHub nghia-lh/vietnamese-absa](https://github.com/nghia-lh/vietnamese-absa)

### Inferences
- Với phạm vi đồ án, **không cần train model ABSA riêng**: dùng LLM cho bước map/reduce, và dùng VLSP 2018 (hotel/restaurant) hoặc UIT-ABSA để **làm test set** đo độ chính xác gán aspect/polarity của prompt (lấy mẫu vài trăm câu). Điều này cho số liệu đánh giá có thể trích dẫn trong báo cáo đồ án.
- Có thể dùng ViSoBERT fine-tune trên ViSpamReviews làm bộ lọc spam rẻ, chạy trước LLM (giống bước lọc của Apple), nhưng kết hợp metadata Tripflix (tài khoản mới, bình luận trùng lặp, link) vì v2 cho thấy text không đủ.
- Taxonomy aspect gợi ý cho du lịch (ánh xạ từ VLSP hotel/restaurant): giá/chi phí, vệ sinh, đông đúc/xếp hàng, phục vụ, đồ ăn, vị trí/đi lại, an toàn, thời tiết/mùa, "khớp với video". Giữ danh sách cố định để code đếm và hiển thị chip như Amazon.
- Ví dụ teencode cần xử lý trong prompt: "k/ko/hong" = không, "dc/đc" = được, "ns" = nói, "j" = gì, "vl/vcl" (nhấn mạnh, có thể khen hoặc chê) — đây là kiến thức chung, không có nguồn trong phiên.

### Gaps
- Không có leaderboard ABSA tiếng Việt thống nhất; các con số đến từ các paper khác nhau với split/metric khác nhau.
- Không tìm thấy dataset sarcasm tiếng Việt hay dataset comment du lịch/video ngắn tiếng Việt.
- Không có đối chiếu LLM (GPT/Claude/Gemini) vs PhoBERT/ViSoBERT trên ABSA tiếng Việt trong phiên này.

---

## 5. Chi phí và mở rộng: batch vs on-demand, cập nhật incremental, caching

### Takeaway
Production làm mới **định kỳ, theo lô** (Apple: ít nhất hằng tuần) và dựa trên **cửa sổ thời gian + ngưỡng số lượng** (Booking 3 năm/≥15 review; Tripadvisor 12 tháng), chứ không sinh lại mỗi khi có bình luận mới. Trigger theo sự kiện của Tripflix (+10 bình luận hoặc khi có bình luận bị ẩn) phù hợp, nên bổ sung debounce và tách cache per-comment.

### Cited Findings
- Apple: "periodically summarizes"; làm mới ít nhất mỗi tuần **[snippet]** — [Apple ML Research](https://machinelearning.apple.com/research/app-store-review); [Maginative](https://www.maginative.com/article/apple-is-bringing-ai-generated-app-review-summaries-to-ios-18-4/)
- Booking.com: chỉ review 3 năm gần nhất, ≥15 review **[snippet]** — [PhocusWire](https://www.phocuswire.com/Technology/challenge-keeping-ai-generated-travel-review-summaries-accurate-objective-useful/63252)
- Tripadvisor: review 12 tháng gần nhất **[snippet]** — [Head for Points](https://www.headforpoints.com/2026/07/02/which-magazine-highlights-tripadvisor-ai-overviews/)
- YouTube: chỉ áp dụng cho video có phần bình luận lớn **[snippet]** — [TechCrunch](https://techcrunch.com/2023/11/06/youtube-to-test-generative-ai-features-including-a-comments-summarizer-and-conversational-tool/)
- Bhaskar et al.: chọn/lọc nội dung nổi bật trước khi đưa vào LLM thay vì đưa hết — vừa giảm token vừa tăng faithfulness **[snippet]** — [arXiv 2211.15914](https://arxiv.org/abs/2211.15914v2)
- HIRO: bộ index nhẹ + LLM zero-shot, kiểu RAG, để mở rộng tới tập review lớn **[snippet]** — [arXiv 2403.00435](https://arxiv.org/html/2403.00435v1)

### Inferences
- **Mô hình cache 2 tầng:**
  - `comment_insights(comment_id PK, model_version, insight_json, created_at)` — tính 1 lần khi bình luận được tạo (hoặc gom theo lô mỗi vài phút); không bao giờ tính lại trừ khi đổi `model_version`.
  - `post_summaries(post_id, input_hash, summary_json, comment_count_at_gen, generated_at, status)` — `input_hash` = hash của danh sách comment_id hiển thị + insight version; nếu hash không đổi thì bỏ qua sinh lại (tránh tốn call khi trigger trùng).
- **Trigger**: giữ "+10 bình luận" và "bình luận bị ẩn/xóa"; thêm **debounce** (vd. tối đa 1 lần/15–60 phút/post) và **refresh định kỳ hằng ngày/tuần cho post có hoạt động** (học từ Apple). Khi bình luận bị ẩn mà comment_id đó có trong summary hiện tại → **ẩn/lọc ngay** ý đó bằng code (bỏ các ID đã ẩn, nếu một ý còn < ngưỡng nguồn thì bỏ ý) trước khi chờ regenerate — không bao giờ hiển thị tóm tắt dựa trên bình luận đã bị ẩn.
- **Batch vs on-demand**: bước map per-comment và refresh định kỳ hợp với Batch API của nhà cung cấp LLM (thường rẻ hơn on-demand; cần kiểm tra bảng giá hiện tại — không xác minh trong phiên này). Không sinh on-demand khi người dùng mở video; luôn đọc từ cache.
- **Cửa sổ thời gian**: thực tế du lịch thay đổi (giá, mùa, công trình); nên ưu tiên/giới hạn bình luận traveler trong ~12 tháng (giống Tripadvisor) và hiển thị "cập nhật ngày …".
- **Ngưỡng hiển thị**: ≥5 substantive + ≥2 traveler là hợp lý cho quy mô nhỏ; vì thấp hơn nhiều so với 15 của Booking, nên hiển thị rõ "dựa trên N bình luận" để người dùng tự cân nhắc độ tin cậy.

### Gaps
- Không có số liệu chi phí/token cụ thể từ bất kỳ nền tảng nào; không xác minh được giá Batch API hiện tại trong phiên này.
- Không tìm thấy mô tả công khai về cơ chế cập nhật incremental của Amazon/Google/Tripadvisor.
