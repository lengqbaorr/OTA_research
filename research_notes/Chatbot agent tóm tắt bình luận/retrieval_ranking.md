# Retrieval, query understanding & ranking cho chatbot Tripflix (ghép yêu cầu du lịch ↔ itinerary/video UGC chỉ bằng metadata, caption, hashtag)

> Ghi chú phương pháp: Trong phiên này WebFetch bị chặn DNS với arxiv.org, huggingface.co, paradedb.com, aclanthology.org (proxy chỉ cho WebSearch). Vì vậy **phần lớn findings dưới đây là "snippet-only"** — lấy từ đoạn trích/tóm tắt của kết quả tìm kiếm, chưa đọc toàn văn. Các con số được giữ nguyên như trong snippet; mục nào đáng ngờ đã ghi chú. Mục "Inferences" là suy luận kỹ thuật của researcher, không phải fact có nguồn.

## 1. Natural-language → structured filters (slot filling): các hãng làm thế nào, LLM vs rule-based, đặc thù tiếng Việt

### Takeaway
Các OTA lớn (Booking.com Smart Filter, Airbnb, Kayak AI Mode) đều dùng LLM để dịch câu tự nhiên thành filter/intent, nhưng pattern production phổ biến là **LLM + chuẩn hoá giá trị về catalog + cache/fallback model nhỏ**, không để LLM "tự do" trên hot path. Với Tripflix, cách hợp lý là: tiền xử lý rule-based (unaccent, teencode dict, regex tiền/người/số ngày) → LLM structured output (JSON schema) → validate/clamp bằng code; thư viện chuẩn hoá tiếng Việt (ViLexNorm/ViSoLex) hữu ích để tham khảo nhưng có thể overkill.

### Cited Findings
- Booking.com Smart Filter (công bố 10/2024) cho người dùng mô tả chỗ ở bằng lời và tự áp filter; theo case study của OpenAI dùng **GPT-4o mini** để hiểu prompt như "sunset views", "great gym"; bản tiếng Pháp của trang OpenAI lại nói LLM được fine-tune trên UGC và mô tả property (mâu thuẫn nhẹ giữa hai phiên bản trang) — snippet-only — [OpenAI: Booking.com](https://openai.com/index/booking-com/); [OpenAI FR](https://openai.com/fr-FR/index/booking-com/)
- Smart Filter "dùng generative AI quét inventory và áp filter liên quan"; trang OpenAI nói nó vượt ra ngoài filter định sẵn bằng cách phân tích review, ảnh, listing details. Báo cáo tăng engagement/conversion nhưng không có số liệu công khai — snippet-only — [10minhotel](https://en.10minhotel.com/?p=86819); [PYMNTS](https://www.pymnts.com/?p=2284382)
- Airbnb: chưa có bài engineering blog công khai về LLM query understanding; job posting đội Relevance & Personalization (Query Intelligence) nhắc tới **LLM-based sequence taggers, LLM-driven query/location expansion, intent classification, user-intent sequence modeling**. Báo chí ghi Airbnb có ô tìm kiếm ngôn ngữ tự nhiên (text/voice) và "AI filters" — snippet-only — [Airbnb careers](https://careers.airbnb.com/positions/8065789/); [Skift 2025](https://skift.com/2025/07/27/what-if-airbnb-builds-the-killer-ai-travel-search-app/); [RentalScaleUp](https://www.rentalscaleup.com/fr/la-recherche-en-langage-naturel-dairbnb-est-deja-en-ligne-aux-etats-unis-cinq-jours-avant-la-summer-release/)
- Kayak AI Mode (ra mắt 15/10/2025): tìm kiếm ngôn ngữ tự nhiên kết hợp dữ liệu Kayak với ChatGPT; hướng dẫn người dùng đưa tiện nghi, ngân sách, vị trí vào câu hỏi; không công bố cách ánh xạ sang filter truyền thống — snippet-only — [Skift](https://skift.com/2025/10/15/kayak-ai-mode-natural-language-search/); [Kayak news](https://www.kayak.com/news/ai-mode/)
- Amazon (paper "Building natural language interface for product search"): ánh xạ query tự nhiên → filter bằng few-shot prompting + chain-of-thought, median latency ~110 ms, +9.6% exact-match accuracy so với baseline — snippet-only — [Amazon Science PDF](https://cdn.amazon.science/a1/97/116ffa7f44e28bcc4bcdfccaceee/building-natural-language-interface-for-product-search.pdf)
- Pattern "LLM ngoài hot path": một paper 2026 dùng LLM 7B (Mistral) chạy nearline trên search log, ghi attribute vào cache, cache hit 98%, cache miss fallback BERT; F1 query-attribute 0.926 vs 0.887 của BERT — snippet-only — [arXiv 2605.05991](https://arxiv.org/pdf/2605.05991)
- Instacart: LLM call đắt, latency "hai đến ba chữ số ms"; Wayfair (ECNLP 2024) dùng NER transformer cho slot filling real-time và nhấn mạnh span trích ra phải **normalize** (lỗi chính tả, dạng không chuẩn) trước khi khớp dữ liệu có cấu trúc — snippet-only — [ZenML LLMOps DB](https://www.zenml.io/llmops-database/enhancing-e-commerce-search-with-llms-at-scale); [ACL 2024.ecnlp-1.13](https://aclanthology.org/2024.ecnlp-1.13)
- ViLexNorm (EACL 2024): corpus chuẩn hoá từ vựng tiếng Việt đầu tiên, 10.467 cặp câu từ bình luận Facebook & TikTok, người gán nhãn; hệ thống tốt nhất đạt ERR 57,74% (so baseline Leave-As-Is); chỉ cho mục đích nghiên cứu — [ACL Anthology 2024.eacl-long.85](https://aclanthology.org/2024.eacl-long.85); [arXiv 2401.16403](https://arxiv.org/html/2401.16403v2)
- ViSoLex (COLING 2025 demo): hai dịch vụ NSW Lookup và Lexical Normalization, dùng PLM + weak supervision (Rule Attention Network dựa trên từ điển NSW); theo một mô tả, từ không có trong từ điển được fallback sang GPT-4o rồi bổ sung vào từ điển; trả về từng NSW + dạng chuẩn + confidence; code tại GitHub HaDung2002/visolex — snippet-only — [arXiv 2501.07020](https://arxiv.org/html/2501.07020v1); [ACL 2025.coling-demos.18](https://preview.aclanthology.org/setup/2025.coling-demos.18)
- Khôi phục dấu (diacritics restoration): model card ViT5-base fine-tune (nrl-ai) tự báo cáo word accuracy 89,97%–99,52% tuỳ register, và gpt-4o-mini/tool rule-based ~95,37% trên một register của benchmark tác giả; các hướng MT báo 97,32% (phrase-based) và 96,15% (NMT); số liệu không so sánh trực tiếp được vì khác test set — snippet-only — [HF nrl-ai/vn-diacritic-vit5-base](https://huggingface.co/nrl-ai/vn-diacritic-vit5-base); [PACLIC 2020](https://aclweb.org/anthology/2020.paclic-1.9.pdf); [arXiv 2105.13578](https://arxiv.org/pdf/2105.13578)
- Teencode: "k/ko" = "không" (phủ định), "vk/ck" = vợ/chồng — tức "k" có nghĩa khác ngoài "nghìn" — [trykaiwa texting guide](https://trykaiwa.com/blog/vietnamese-texting-shortcuts-guide-2026); [Language Log](https://languagelog.ldc.upenn.edu/nll/?p=40426)

### Inferences
- **Pipeline đề xuất cho Tripflix** (đủ tốt, rẻ): (1) chuẩn hoá: lowercase, NFC, bỏ emoji (hoặc map emoji → tag như 🏖️→bien), (2) regex/rule cho slot "cứng": tiền (`8 củ`, `8tr`, `8 triệu`, `800k`, `8m`, `tầm/khoảng/dưới/max`), người (`2 đứa`, `2 ng`, `cặp đôi`, `nhóm 5`, `gia đình`), thời lượng (`3n2đ`, `2 ngày 1 đêm`, `cuối tuần`), điểm đến (dictionary alias không dấu: `nt`/`nha trang`, `dl`/`da lat`, `pq`/`phu quoc`, `sg`, `hn`), (3) gọi LLM với **JSON schema/function calling** để lấp slot mềm (vibe: chill, sống ảo, ăn uống, phượt; party type), truyền kèm kết quả rule làm "gợi ý", (4) validate bằng code (budget > 0, n_days ∈ [1,15], destination ∈ danh mục), (5) nếu LLM lỗi/timeout → dùng kết quả rule. Đây là phiên bản thu nhỏ của pattern "LLM + normalize về catalog + fallback" ở trên.
- "k" cần disambiguation theo ngữ cảnh: chỉ hiểu là nghìn khi đứng ngay sau số (`800k`); "củ" sau số = triệu. Ngân sách là tổng hay theo đầu người ("2 đứa tầm 8 củ") là mơ hồ — nên mặc định *tổng cho cả nhóm* và để chatbot hỏi lại/hiển thị giả định.
- Khôi phục dấu bằng model riêng là **overkill** cho search vì có thể làm ngược lại: unaccent cả query lẫn index (so khớp không dấu ↔ không dấu). Chỉ cần khôi phục dấu khi muốn hiển thị lại câu hỏi đã hiểu, và việc này LLM làm được trong cùng lời gọi slot-filling.
- ViLexNorm/ViSoLex: hữu ích làm nguồn từ điển teencode/test case; tích hợp model đầy đủ là overkill cho vài nghìn post — một từ điển ~100–300 mục cho domain du lịch + LLM là đủ.

### Gaps
- Không đọc được toàn văn bài về kiến trúc Smart Filter (chỉ có case study marketing); không có tài liệu kỹ thuật công khai của Airbnb/Kayak/Google về slot filling.
- Không tìm thấy nguồn học thuật/kỹ thuật nào chuẩn hoá tiếng lóng tiền tệ tiếng Việt ("củ", "lít", "k", "tr") — nghĩa của chúng ở trên là hiểu biết chung, chưa có nguồn xác nhận.
- Không tìm thấy benchmark slot-filling tiếng Việt cho domain du lịch.

## 2. Hybrid retrieval: full-text/BM25 + vector + RRF; lựa chọn trong PostgreSQL; embedding tiếng Việt; khi nào vector đáng giá

### Takeaway
Với vài trăm–vài nghìn post, `tsvector` (config `simple` + `unaccent`) + `pg_trgm` + filter SQL là đủ làm baseline; thêm **pgvector** (BGE-M3 hoặc multilingual-e5) khi query "vibe" (chill, lãng mạn, sống ảo) không khớp từ khoá. Nếu có 2 nguồn rank thì gộp bằng **RRF (k=60)** thay vì cộng điểm thô. ParadeDB/pg_search (BM25 thật) là tuỳ chọn, chưa cần ở quy mô này.

### Cited Findings
- RRF (Cormack, Clarke, Büttcher, SIGIR 2009): điểm = Σ 1/(k + rank), k=60 cố định từ pilot; RRF tốt hơn từng hệ thống đơn lẻ và tốt hơn Condorcet Fuse; bảng trong paper: best individual MAP 0,2016, Condorcet 0,2074, CombMNZ 0,2039, RRF k=60 ≈ 0,2145; hiệu năng khá phẳng với k từ 10–100 (số liệu từ đoạn PDF trích) — [Cormack et al. PDF](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf); [Google Research](https://research.google/pubs/pub36196/)
- ParadeDB: hybrid search cần fusion vì BM25 score và vector distance khác thang đo; RRF chỉ dùng thứ hạng; mẫu SQL gồm 2 CTE (BM25 qua `@@@` + `pdb.score(id)` top-20; vector qua `<=>` top-20) rồi FULL OUTER JOIN theo id và cộng 1/(k+rank); cần extension `pg_search` và `vector` — snippet-only — [ParadeDB blog](https://paradedb.com/blog/hybrid-search-in-postgresql-the-missing-manual); [pg_search 0.19.4 hybrid guide](https://api.pgxn.org/src/pg_search/pg_search-0.19.4/docs/documentation/guides/hybrid.mdx)
- pgvector 0.8.0 thêm **iterative index scans**: trước đó filter WHERE áp sau khi quét index nên trả ít kết quả; ví dụ AWS: `hnsw.ef_search=40`, nếu chỉ 10% dữ liệu khớp filter thì nhận ~4 kết quả; chế độ strict vs relaxed ordering; `hnsw.max_scan_tuples` mặc định 20.000 — [PostgreSQL news pgvector 0.8.0](https://www.postgresql.org/about/news/pgvector-0.8.0-released-2952/); [AWS Database Blog](https://aws.amazon.com/blogs/database/supercharging-vector-search-performance-and-relevance-with-pgvector-0-8-0-on-amazon-aurora-postgresql); [pgvector README 0.8.0](https://pgxn.org/dist/vector/0.8.0/README.html)
- Crunchy Data: có pattern hybrid search Postgres + pgvector; iterative scans có trade-off cần tuning — snippet-only — [Crunchy Data](https://www.crunchydata.com/blog/hybrid-vector-search)
- PostgreSQL không có text search config tiếng Việt; pattern phổ biến: copy config (`simple`) và thêm `unaccent` vào mapping, hoặc GIN index trên `to_tsvector(..., unaccent(col))`; `pg_trgm` + `similarity()` cho fuzzy (ví dụ similarity('posgresql','postgresql') ≈ 0,73, ngưỡng mặc định 0,3); file rules của unaccent phải UTF-8 — snippet-only — [Sling Academy](https://www.slingacademy.com/article/postgresql-full-text-search-handling-diacritics-and-accents/); [Viblo pg_trgm (VN)](https://viblo.asia/p/postgresql-bai-19-regular-expressions-va-fuzzy-search-voi-extension-pg-trgm-XRJ8RW0OVGq); [unaccent docs mirror](https://dev.begriffs.com/ai-pg/blob/5aafbcb28b3aca41d8f0e7965ac72316178f4ee8:/full-docs/html/unaccent.html)
- VN-MTEB (arXiv 2507.21500, EACL 2026 Findings): 41 dataset, 6 task (retrieval, reranking, classification, clustering, pair classification, STS), dịch từ MTEB tiếng Anh + có Zalo Legal; kết luận: model lớn dùng **RoPE** tốt hơn model dùng absolute positional embedding. Trích một phần bảng: multilingual-e5-large-instruct vs BGE-M3 — TRECCOVID-VN 80,56 vs 66,22; Climate-FEVER-VN 25,01 vs 21,27; BGE-M3 hơn ở ArguAna-VN 50,68 vs 48,15; multilingual-e5-large hơn BGE-M3 ở MSMARCO-VN 39,08 vs 36,22 — snippet-only, chỉ một phần bảng — [arXiv 2507.21500](https://arxiv.org/html/2507.21500v1)
- "Which Works Best for Vietnamese? A Practical Study of IR Methods across Domains" (EACL 2026 Findings): abstract gợi ý quy mô model ít dự báo được hiệu quả retrieval và nên cân nhắc hybrid lexical–semantic — snippet-only — [ACL Anthology preview](https://preview.aclanthology.org/new-sigs/2026.findings-eacl.110/)
- VCS benchmark (arXiv 2503.07470): so sánh vietnamese-bi-encoder, vietnamese-sbert…; vietnamese-bi-encoder thường tốt hơn trên retrieval tập lớn; model 20M tham số của tác giả xấp xỉ 3 model tiếng Việt lớn hơn — snippet-only — [arXiv 2503.07470](https://arxiv.org/html/2503.07470v1)
- AITeamVN/Vietnamese_Embedding: fine-tune từ BGE-M3 trên ~300.000 triplet tiếng Việt; tuyên bố SOTA chưa được kiểm chứng độc lập — snippet-only — [PromptLayer listing](https://www.promptlayer.com/models/vietnameseembedding)

### Inferences
- **Baseline Postgres đề xuất**: cột `search_doc` = unaccent(lower(caption ‖ hashtags ‖ destination ‖ tên item ‖ tips)), `tsvector` với config `simple` (tiếng Việt là ngôn ngữ đơn âm tiết, stemmer tiếng Anh vô nghĩa), set weight A cho destination/hashtag, B cho tên item, C cho caption/tips; GIN index. Dùng `phraseto_tsquery`/`<->` cho cụm 2 âm tiết ("nha trang", "da lat") để tránh khớp "trang" lẻ. Thêm `pg_trgm` trên destination/hashtag để chịu lỗi gõ ("nhatrang", "dalat").
- Lưu ý `ts_rank` của Postgres không phải BM25 (không có IDF chuẩn) — với vài nghìn post, khác biệt này nhỏ vì phần lớn tín hiệu đến từ filter + score nghiệp vụ; ParadeDB là **overkill** trừ khi đã dùng Postgres image có sẵn nó.
- **Khi nào thêm vector**: khi bộ test (mục 5) cho thấy query mô tả vibe/hoạt động ("chill", "săn mây", "healing", "đi bụi") có Recall@10 thấp với full-text. Ở quy mô ≤ vài nghìn post, có thể bỏ qua HNSW và quét tuần tự (exact) sau filter destination — nhanh và không gặp vấn đề recall do filter của HNSW. Embed văn bản *gốc có dấu* nếu có (caption tác giả thường có dấu) và query sau khi LLM khôi phục dấu; BGE-M3/multilingual-e5 là lựa chọn an toàn (đa ngôn ngữ, chạy được local/API).
- Thay thế rẻ hơn vector: cho LLM sinh "query expansion" (chill → yên tĩnh, cafe, biển, resort) rồi đưa vào full-text; hoặc LLM rerank top-20/30 ứng viên — với vài nghìn post, rerank bằng LLM thường đáng tiền hơn hạ tầng embedding.

### Gaps
- Không đọc được bảng trung bình tổng của VN-MTEB, nên không xếp hạng tổng thể BGE-M3 vs e5 vs model Việt; cần mở paper/leaderboard MTEB.
- Chưa xác minh `unaccent.rules` mặc định có map `đ→d`/`Đ→D` hay không (không truy cập được mã nguồn) — cần test `SELECT unaccent('Đà Lạt')` trên DB thật.
- Không có số liệu công khai về ngưỡng kích thước corpus mà vector bắt đầu có lợi.

## 3. TikTok/Douyin, Xiaohongshu, Pinterest, YouTube tìm video ngắn bằng text/metadata; travel UGC search

### Takeaway
Các nền tảng lớn dùng caption + hashtag + on-screen text (OCR) + transcript (ASR) và dense retrieval do LLM huấn luyện (Xiaohongshu: NoteLLM, ScalingNote), nhưng tài liệu công khai về TikTok/Douyin search relevance gần như không có (chỉ có blog SEO). Bài học chuyển giao được cho Tripflix: coi hashtag/category là tín hiệu nén nội dung, và dùng engagement như prior chứ không làm tín hiệu chính.

### Cited Findings
- NoteLLM (Xiaohongshu, WWW'24): LLM nén note thành embedding cho item-to-item recommendation, đồng thời học **sinh hashtag/category**; offline Recall@1 +15,1% so baseline BERT online; A/B online +12,8% (AUC/metric theo snippet) — snippet-only — [arXiv 2403.01744](https://arxiv.org/pdf/2403.01744); [GitHub NoteLLM](https://github.com/Applied-Machine-Learning-Lab/NoteLLM)
- ScalingNote (Xiaohongshu, 11/2024): dense retrieval cho note search, dual-tower khởi tạo từ cùng LLM, chỉ distill query tower để giữ latency thấp; personalized search với IVFPQ — snippet-only — [arXiv 2411.15766](https://arxiv.org/html/2411.15766v1)
- TikTok search: các blog SEO (không phải tài liệu chính thức) mô tả TikTok đọc caption, hashtag, text overlay (OCR) và transcript tự động (ASR); các con số kiểu "8× impressions" không có cơ sở — **độ tin cậy thấp** — [wetheflywheel](https://wetheflywheel.com/en/ai-search/how-to-rank-in-tiktok-search/); [OpenClip](https://openclip.app/blog/metadata-impacts-tiktok-seo)

### Inferences
- Tripflix có lợi thế mà TikTok không có: **itinerary có cấu trúc** (n_days, item type, product ID có giá). Đây là tín hiệu mạnh hơn caption nhiều → nên rank chủ yếu theo constraint fit, caption/hashtag chỉ dùng cho khớp chủ đề/vibe.
- Ý tưởng NoteLLM áp dụng rẻ: khi post được tạo, gọi LLM một lần để sinh **tag chuẩn hoá** (vibe: chill/phiêu lưu/ẩm thực/sống ảo; party fit: cặp đôi/nhóm bạn/gia đình; mức chi phí) và lưu vào bảng search — chuyển "hiểu nội dung" sang offline, query chỉ còn khớp tag.
- OCR/ASR video: overkill cho đồ án; nếu cần, chỉ ASR caption tự sinh cho video thiếu caption.
- Engagement (like/save/share) nên log-scale và có trọng số nhỏ, cộng thêm boost cho "Verified" (có booking thật → giá tính được, đáng tin hơn).

### Gaps
- Không tìm thấy paper/blog kỹ thuật chính thức của ByteDance/Douyin về search relevance video ngắn, Pinterest search, YouTube Shorts search trong giới hạn tìm kiếm.
- Không tìm thấy tài liệu kỹ thuật của Tripadvisor hay Mafengwo về tìm kiếm itinerary/travel notes.

## 4. Matching itinerary với ràng buộc người dùng: budget feasibility, duration, party type, diversity (MMR), ngưỡng "không có kết quả", relaxation

### Takeaway
Nghiên cứu học thuật về itinerary recommendation xử lý budget như **ràng buộc cứng loại bỏ ứng viên** (DeepAltTrip), còn đa dạng hoá kết quả dùng MMR (λ cân bằng relevance/novelty). Không tìm thấy nguồn chuẩn về ngưỡng "no result" hay thứ tự nới lỏng — đây phải là quyết định thiết kế, nên kiểm bằng bộ test.

### Cited Findings
- DeepAltTrip: sinh top-k itinerary thay thế và loại ứng viên vượt budget/thời gian; nhận xét ràng buộc như vậy không dễ thoả trong mô hình deep learning truyền thống — snippet-only — [arXiv 2109.03535](https://arxiv.org/pdf/2109.03535)
- Dự án sinh viên U of Toronto 2024 (PlanIt): agent LLM sinh itinerary, tinh chỉnh theo ràng buộc, chấm điểm satisfaction & feasibility, đánh giá trên 20 query — không phải peer-reviewed — [UofT report](https://www.eecg.utoronto.ca/~jayar/1786_24_projects/reports/planit.pdf)
- MMR (Carbonell & Goldstein, SIGIR'98): chọn tài liệu vừa liên quan query vừa ít giống tài liệu đã chọn; λ=1 trả lại ranking thuần relevance, λ thấp ưu tiên đa dạng — [SIGIR'98 abstract](https://people.eng.unimelb.edu.au/ammoffat/sigir98/abstracts/carbonell.html); [MathWorks mmrScores](https://www.mathworks.com/help/textanalytics/ref/mmrscores.html); [arXiv 2407.04573](https://arxiv.org/html/2407.04573v3)

### Inferences
- **Budget feasibility**: chỉ tính được chính xác với item có product ID (giá live). Đề xuất `est_cost = Σ giá live (item có ID) + ước lượng cho item thủ công (giá tác giả ghi hoặc trung bình theo type/destination)`, scale theo số người (vé/flight × người; hotel × ceil(người/2) phòng × đêm). Lưu `cost_confidence` = tỷ lệ chi phí từ item có ID — post Verified có confidence cao. Budget fit dạng mềm: 1 nếu est ≤ budget, giảm tuyến tính tới 0 ở ~120–130% budget; > ngưỡng → loại (hard filter).
- **Duration**: |n_days − yêu cầu| = 0 → 1; =1 → 0,5–0,7 (itinerary có thể cắt/ghép); ≥2 → loại hoặc rất thấp.
- **Party type**: dùng tag sinh offline (mục 3); chỉ là soft score.
- **Score** đề xuất (giữ kế hoạch hiện tại, chuẩn hoá về [0,1]): `0.35·budget_fit + 0.25·text_match(rank-normalized) + 0.2·days_fit + 0.1·log-popularity + 0.1·verified/cost_confidence`; trọng số là điểm khởi đầu, tune bằng bộ test. Nếu có cả full-text và vector, gộp hai cái đó bằng RRF trước rồi mới đưa vào score.
- **Diversity**: MMR trên top-20 với similarity = cùng tác giả / cùng khách sạn / cùng tập địa điểm (Jaccard product ID) — tránh 5 kết quả gần như trùng; λ ≈ 0,7 khởi đầu.
- **"No result" honesty**: đặt ngưỡng tuyệt đối (ví dụ không có post nào khớp destination, hoặc top-1 score < ngưỡng tune từ bộ test) → chatbot nói rõ "chưa có lịch trình phù hợp" thay vì trả kết quả lạc đề. **Relaxation** theo thứ tự ít "đau" nhất và luôn báo người dùng đã nới gì: (1) bỏ vibe keyword, (2) nới days ±1, (3) nới budget +20% (hiển thị chênh lệch), (4) gợi ý destination lân cận (Nha Trang → Cam Ranh/Ninh Thuận) — không bao giờ âm thầm đổi destination.
- Itinerary embedding (embed cả chuỗi item) là overkill ở quy mô này; Jaccard trên product ID/địa danh đủ cho cả "itinerary tương tự" và MMR.

### Gaps
- Không tìm thấy paper 2024–2026 về retrieval travelogue/itinerary UGC với ràng buộc ngân sách và giá live.
- Không có nguồn về ngưỡng "no-result" hay chiến lược relaxation chuẩn trong OTA (Booking/Airbnb không công bố).

## 5. Đánh giá chất lượng retrieval: bộ query tiếng Việt, metric, offline vs online

### Takeaway
Với đồ án, nên xây bộ test 50–100 query tiếng Việt thực tế (không dấu, teencode, emoji), gán nhãn graded relevance cho top-k (có thể dùng LLM-as-judge rồi người kiểm tra mẫu), đo Recall@10, nDCG@10, MRR và tỉ lệ slot-filling đúng; online chỉ cần log click/save. LLM judge xếp hạng hệ thống khá sát người nhưng có xu hướng chấm dễ hơn.

### Cited Findings
- Bing dùng LLM gán nhãn relevance cùng với chuyên gia từ cuối 2022, nhãn LLM được kiểm định định kỳ với assessor được huấn luyện (Thomas et al. 2024); UMBRELA (GPT-4o) tái lập kết quả và báo judgment LLM tương quan cao với ranking của hệ thống multi-stage hiệu quả — snippet-only — [arXiv 2406.06519](https://web3.arxiv.org/abs/2406.06519); [arXiv 2406.07299](https://arxiv.org/html/2406.07299v1)
- Mức đồng thuận người–LLM ở cấp từng item thấp-trung bình trong vài nghiên cứu (Cohen's kappa 0,26 và 0,31); LLM lớn đạt đồng thuận tương đương người–người với nhãn nhị phân, với thang graded chỉ LLM thương mại lớn nhất đạt; LLM có xu hướng chấm "relevant" nhiều hơn người (LLMJudge SIGIR 2024) — snippet-only — [arXiv 2503.19092](https://arxiv.org/pdf/2503.19092); [arXiv 2411.08275](https://arxiv.org/html/2411.08275v1); [CEUR Vol-3752](https://ceur-ws.org/Vol-3752/paper2.pdf)
- NoteLLM đánh giá cả offline (Recall@1) và online A/B — minh hoạ quy trình offline → online — snippet-only — [arXiv 2403.01744](https://arxiv.org/pdf/2403.01744)

### Inferences
- **Bộ test**: mỗi query gồm text gốc (vd `di nha trang 2 dua tam 8 cu thich chill 🏖️`), slot kỳ vọng (destination, budget, people, days, vibe) và danh sách post relevant (graded 0/1/2). Phủ các trường hợp: không dấu, teencode, viết tắt địa danh, ngân sách theo đầu người vs tổng, không có kết quả (để kiểm "honesty"), query mơ hồ (chỉ vibe). Đo riêng: (a) slot accuracy/exact-match của parser, (b) Recall@10/nDCG@10/MRR của retrieval+ranking, (c) tỉ lệ trả lời "không có" đúng.
- Dùng LLM-as-judge để gán nhãn nhanh nhưng kiểm tra tay ~20% vì LLM dễ dãi; nên dùng nhãn 0/1/2 với tiêu chí rõ (đúng destination + budget khả thi = tối thiểu 1).
- Online cho đồ án: log query → kết quả hiển thị → click/save/booking; CTR@k và "query không kết quả" là đủ; A/B testing là overkill với lượng user nhỏ.
- Chạy bộ test như regression test mỗi lần đổi trọng số/thêm vector — đây là cơ sở để quyết định "vector có đáng không" ở mục 2.

### Gaps
- Không có bộ test/benchmark công khai cho tìm kiếm du lịch tiếng Việt dạng hội thoại/teencode.
- Không đọc được số liệu agreement gốc của Thomas et al. 2024.
