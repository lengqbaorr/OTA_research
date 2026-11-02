# Kiến trúc module "Search" và "AI" cho Tripflix (semantic search tiếng Việt, recommendation, image search, trợ lý LLM có grounding)

> Phạm vi: modular monolith (PostgreSQL + Redis), demo sinh viên 4 tuần.
> Lưu ý về nguồn: trong phiên này **chỉ fetch được github.com và các nguồn qua WebSearch**; các host `arxiv.org`, `huggingface.co`, `docs.anthropic.com`, `aclanthology.org` bị chặn bởi network allowlist của session. Vì vậy các con số từ model card / paper gốc phần lớn được lấy qua snippet tìm kiếm hoặc nguồn thứ cấp và được đánh dấu rõ. Những điều tôi biết từ kiến thức trước (không xác minh được trong phiên) được đặt ở mục **Inferences** kèm nhãn "[chưa xác minh trong phiên]".

---

## 1. Xử lý văn bản tiếng Việt: không dấu, teencode ("2 củ", "k"), emoji, trộn tiếng Anh; unaccent + pg_trgm; tokenizer (pyvi, underthesea); embedding đa ngôn ngữ và benchmark

### Takeaway
Pipeline nên có 2 nhánh song song: (a) nhánh **lexical** chuẩn hoá về dạng không dấu + lowercase rồi dùng `pg_trgm` (chịu lỗi gõ, không dấu) và (b) nhánh **semantic** dùng embedding đa ngôn ngữ (multilingual-e5 / BGE-M3 hoặc bản fine-tune tiếng Việt) trên văn bản *gốc có dấu*. Teencode/số tiền ("2 củ") phải xử lý bằng một bước chuẩn hoá + trích xuất có luật (rule-based) trước khi embed, vì không có thư viện chuẩn nào bao trọn phần này.

### Cited Findings
- `unaccent()` mặc định **không** được đánh dấu `IMMUTABLE` (maintainer chọn STABLE), nên không index trực tiếp được; cách phổ biến là tạo wrapper SQL `IMMUTABLE` cố định dictionary, ví dụ `CREATE FUNCTION immutable_unaccent(text) ... SELECT unaccent('unaccent', $1) ... LANGUAGE SQL IMMUTABLE PARALLEL SAFE STRICT`, rồi tạo index `gin (immutable_unaccent(lower(name)) gin_trgm_ops)` — [OneUptime: Accent-insensitive in PostgreSQL](https://oneuptime.com/blog/post/2026-01-25-postgresql-accent-insensitive-collation/view); [PostgreSQL bug #5781 thread](https://postgrespro.com/list/thread-id/1221052)
- Wrapper IMMUTABLE là "lời hứa" với DB: nếu đổi file rules của unaccent thì index cũ có thể sai và phải rebuild — [postgrespro thread stable vs immutable](https://postgrespro.ru/list/thread-id/1228066)
- Cộng đồng VN có các file rules unaccent riêng cho tiếng Việt (map Ấ, Ậ… → A, xử lý "đ") — [gist hieunguyentrung](https://gist.github.com/hieunguyentrung/029e6a0fe6d9a72478d3a97ffb685d72); [gist tridungpham](https://gist.github.com/tridungpham/d8c150caba017c47da10361ab90b9672) (nội dung đầy đủ chưa được kiểm tra)
- Extension `unaccent` mô tả trên Neon docs (cách bật, dùng với full-text search) — [Neon docs: unaccent](https://neon.com/docs/extensions/unaccent)
- underthesea: word segmentation đạt 97.5% F1 / 95.2% accuracy, ~2,800 câu/giây trong một benchmark 2026 (phương pháp hybrid của paper đạt 98.1% F1) — số liệu phụ thuộc setup của paper đó — [FreeTxt-Vi (arXiv 2603.05690)](https://arxiv.org/pdf/2603.05690)
- underthesea **không** làm number verbalization, currency expansion, acronym resolution; là toolkit "large-footprint" với dependency deep learning — [VietNormalizer (arXiv 2603.04145)](https://arxiv.org/html/2603.04145v1)
- Có công cụ riêng cho chuẩn hoá ngôn ngữ mạng xã hội (teencode, viết tắt, biến thể ngữ âm): **ViSoLex** — được nhắc trong [VietNormalizer paper](https://arxiv.org/pdf/2603.04145)
- Tài liệu chính thức underthesea (word_tokenize, v.v.) — [underthesea docs](https://underthesea.readthedocs.io/en/v1.1.7/readme.html); [undertheseanlp/word_tokenize](https://github.com/undertheseanlp/word_tokenize/blob/master/README.en.md)
- **VN-MTEB**: benchmark embedding tiếng Việt gồm **41 dataset, 6 task** (retrieval, reranking, classification, clustering, pair classification, STS), mở rộng từ MMTEB, đăng ở Findings of EACL 2026 (GreenNode AI) — [VN-MTEB arXiv 2507.21500](https://arxiv.org/html/2507.21500v1). Tôi **không lấy được bảng điểm** (arxiv/aclanthology bị chặn).
- NanoVNMTEB (leaderboard phụ): BM25 tốt nhất cho `quora_vn`; trên `msmarco_vn` dense retrieval mạnh nhất về nDCG@10/hit@10 còn reranking_hybrid có recall@100 tốt nhất — [hakari-bench NanoVNMTEB](https://hakari-bench-leaderboard.hf.space/docs/benchmark-tasks/NanoVNMTEB)
- SEA-BED (benchmark embedding cho ngôn ngữ Đông Nam Á) có so sánh multilingual-e5-large(-instruct) và bge-m3; snippet cho thấy e5-large-instruct có trung bình cao hơn bge-m3 (73.41 vs 69.55) ở một bảng, nhưng **không rõ cột/không tách riêng tiếng Việt** — [SEA-BED arXiv 2508.12243](https://arxiv.org/pdf/2508.12243)
- Có bản fine-tune BGE-M3 cho tiếng Việt (`nrl-ai/vietnamese-embedding-bge-m3`) tự báo cáo điểm cao hơn BGE-M3 gốc (self-reported) — [HF model card](https://huggingface.co/nrl-ai/vietnamese-embedding-bge-m3)
- `VietRAG-Embed` là fine-tune của multilingual-e5-base, báo cáo SciFact-VN 0.6130, TRECCOVID-VN 0.6068, Quora-VN 0.5652 (model card tự nhận là "in-domain diagnostic") — [HF VietRAG-Embed](https://huggingface.co/nhminh107/VietRAG-Embed)
- Bài báo về IR tiếng Việt với learning objective và benchmark mới — [arXiv 2503.07470](https://arxiv.org/pdf/2503.07470) (chưa đọc được nội dung)
- BGE-M3 hỗ trợ dense + multi-vector + sparse retrieval trong một model — [DeepInfra bge-m3-multi](https://deepinfra.com/BAAI/bge-m3-multi)

### Inferences
- **Chuẩn hoá 2 dạng cho mỗi document** (`posts`, `products`): `search_text_raw` (có dấu, để embed) và `search_text_norm` = `immutable_unaccent(lower(...))` (để trigram/FTS). Query cũng chuẩn hoá tương tự → người gõ "da lat" vẫn khớp "Đà Lạt". Cần thêm rule "đ→d" nếu file rules mặc định không có (kiểm tra bằng `SELECT unaccent('Đà Lạt')`).
- **Full-text search tiếng Việt trong PostgreSQL**: không có text search config tiếng Việt sẵn; dùng `simple` config trên text đã unaccent (từ đơn âm tiết) — hoặc tiền xử lý tách từ bằng pyvi/underthesea rồi nối bằng "_" trước khi đưa vào `tsvector`. Với demo 4 tuần, `pg_trgm` + `simple` tsvector trên text unaccent là đủ; tách từ chỉ là tối ưu.
- **Teencode/slang**: viết bảng `query_lexicon` (seed thủ công ~100–300 mục): `k|ko|hok → không`, `dc|đc → được`, `sg → Sài Gòn`, `hn → Hà Nội`, `ks → khách sạn`, và bộ regex tiền tệ: `(\d+([.,]\d+)?)\s*(củ|tr|triệu|m)` → ×1,000,000; `(\d+)\s*(k|nghìn|ngàn|lít)` → ×1,000 (chú ý "k" vừa là "không" vừa là "nghìn": chỉ coi là tiền khi đứng ngay sau số). "lít" = 100k là slang — nên đưa vào nhưng đánh dấu độ tin cậy thấp. Đây là suy luận thiết kế, không có nguồn chuẩn.
- **Emoji**: giữ trong text gốc để embed (model đa ngôn ngữ thường có token emoji), nhưng loại khỏi `search_text_norm`. Có thể map vài emoji phổ biến (🏖️→"biển", ⛰️→"núi") vào lexicon.
- **Trộn tiếng Anh** ("resort view biển chill"): embedding đa ngôn ngữ xử lý tốt hơn lexical; đây là lý do chính cần nhánh semantic.
- **Chọn model embedding cho demo** [chưa xác minh trong phiên — theo model card mà tôi biết trước]: `intfloat/multilingual-e5-small/base` (384/768 chiều, cần prefix `"query: "` / `"passage: "`, giới hạn 512 token) hoặc `BAAI/bge-m3` (1024 chiều, 8192 token, >100 ngôn ngữ, ~568M tham số). Cả hai đều < 2,000 chiều nên vừa giới hạn index HNSW của pgvector (xem mục 2). Với CPU-only demo, e5-small/base nhẹ hơn nhiều. Nên tự đo trên test set 50–100 query tiếng Việt của Tripflix thay vì tin leaderboard (do domain du lịch + teencode khác benchmark dịch máy như VN-MTEB).
- PhoBERT/Vietnamese SBERT yêu cầu input **đã tách từ** (word-segmented) [chưa xác minh trong phiên] → thêm dependency pyvi/underthesea vào pipeline; multilingual-e5/BGE-M3 thì không cần → đơn giản hơn cho demo.

### Gaps
- Không lấy được bảng điểm VN-MTEB (arxiv/aclanthology bị chặn) → không khẳng định được model nào "tốt nhất" cho retrieval tiếng Việt.
- Không tìm được benchmark độc lập cho pyvi, hay benchmark chuẩn hoá teencode/tiền tệ kiểu "2 củ".
- Không xác minh được nội dung model card BGE-M3 / multilingual-e5 trong phiên (huggingface.co bị chặn).

---

## 2. Hybrid search (BM25 + vector, RRF), pgvector HNSW vs engine chuyên dụng, ngưỡng "không có kết quả", query understanding (ngân sách, số người, per-person vs tổng)

### Takeaway
Với PostgreSQL sẵn có, **pgvector (HNSW) + FTS/pg_trgm + RRF viết bằng SQL** là đủ cho demo và đúng với khuyến nghị chính thức của pgvector; engine chuyên dụng (Elasticsearch/OpenSearch/Qdrant/Meilisearch) chỉ đáng khi dữ liệu/QPS lớn. Lọc cứng (giá, địa điểm, số người) phải làm *trước hoặc cùng* vector search (pgvector ≥0.8 iterative scan), và "không có kết quả" nên dựa trên ngưỡng tuyệt đối của cosine/trigram chứ không phải điểm RRF.

### Cited Findings
- pgvector v0.8.7 hỗ trợ Postgres 13+. **HNSW**: tốc độ–recall tốt hơn IVFFlat, build chậm hơn và tốn RAM hơn, không cần bước training nên tạo được trên bảng rỗng; mặc định `m=16`, `ef_construction=64`, `hnsw.ef_search=40` — [pgvector README](https://github.com/pgvector/pgvector)
- **IVFFlat**: build nhanh/ít RAM hơn; `lists` gợi ý rows/1000 (≤1M rows), `ivfflat.probes` mặc định 1; phải tạo index **sau** khi có dữ liệu — [pgvector README](https://github.com/pgvector/pgvector)
- **Iterative index scans** (từ 0.8.0) cho truy vấn có filter, vì index xấp xỉ áp filter *sau* khi scan: `hnsw.iterative_scan = strict_order | relaxed_order`; `hnsw.max_scan_tuples` mặc định 20,000. Ví dụ README: filter khớp 10% rows với `ef_search=40` thì trung bình chỉ còn ~4 kết quả — [pgvector README](https://github.com/pgvector/pgvector)
- Giới hạn chiều có thể index: `vector` 2,000; `halfvec` 4,000; `bit` 64,000; `sparsevec` 1,000 phần tử khác 0 — [pgvector README](https://github.com/pgvector/pgvector)
- Toán tử: `<->` L2, `<#>` negative inner product, `<=>` cosine distance, `<+>` L1, `<~>` Hamming, `<%>` Jaccard — [pgvector README](https://github.com/pgvector/pgvector)
- pgvector khuyến nghị **hybrid search** = pgvector + Postgres full-text search (`plainto_tsquery`, `ts_rank_cd`), kết hợp bằng **Reciprocal Rank Fusion** hoặc cross-encoder (có ví dụ pgvector-python) — [pgvector README](https://github.com/pgvector/pgvector)
- Elastic RRF: điểm = Σ 1/(k + rank); `rank_constant` quyết định ảnh hưởng của từng tập kết quả; `rank_window_size` phải ≥ `size` — [Elastic docs preview: RRF](https://docs-v3-preview.elastic.dev/elastic/docs-builder/docs/3677/reference/elasticsearch/rest-apis/reciprocal-rank-fusion.md); [Elastic RRF retriever](https://docs-v3-preview.elastic.dev/elastic/docs-builder/docs/3825/reference/elasticsearch/rest-apis/retrievers/rrf-retriever.md)
- Mặc định `rank_constant` = 60 theo một trang Tencent Cloud Elasticsearch (nguồn bên thứ ba; snippet Elastic không xác nhận) — [Tencent Cloud ES docs](https://proxy-hk.tencentcloud.com/ind/document/product/845/82242)
- Trên NanoVNMTEB, có task mà BM25 thắng (quora_vn) và task dense thắng (msmarco_vn) → bằng chứng thực nghiệm rằng tiếng Việt cũng cần cả hai nhánh — [hakari-bench NanoVNMTEB](https://hakari-bench-leaderboard.hf.space/docs/benchmark-tasks/NanoVNMTEB)

### Inferences
- **Thiết kế bảng** (schema `search`):
  - `search_documents(id, entity_type ENUM('post','product','place'), entity_id, locale, title, body_raw, search_text_norm, tsv tsvector, price_min, price_max, currency, province_code, capacity_min, capacity_max, tags text[], status, updated_at, embedding vector(384|768|1024), embedding_model, content_hash)` — một bảng "denormalized read model" được cập nhật từ event của module Catalog/Social. Index: `HNSW (embedding vector_cosine_ops)`, `GIN(tsv)`, `GIN(search_text_norm gin_trgm_ops)`, btree trên `(entity_type, province_code, price_min)`.
  - `embedding_jobs(id, entity_type, entity_id, content_hash, status, attempts, error, created_at)` — outbox/hàng đợi để re-embed khi content đổi (so `content_hash` tránh embed lại vô ích).
  - Dùng `halfvec` nếu chọn model >2,000 chiều (không cần với e5/BGE-M3).
- **Truy vấn hybrid trong SQL** (một CTE): `lexical` = top 50 theo `ts_rank_cd` + `similarity(search_text_norm, q_norm)`; `semantic` = top 50 theo `embedding <=> :qvec` (với `SET hnsw.iterative_scan = relaxed_order` khi có filter); `fused` = `SUM(1.0/(60 + rank))` group by document. k=60 là quy ước phổ biến, nên đặt cấu hình được.
- **"Nói thật là không có kết quả"**: điểm RRF chỉ phản ánh *thứ hạng tương đối* nên luôn có top-k ⇒ cần cổng tuyệt đối: trả `results=[]` + `no_result_reason` khi (max cosine similarity < τ_vec) **và** (max trigram similarity < τ_trgm **và** không có match FTS). τ phải hiệu chỉnh trên test set (ví dụ chọn τ sao cho precision của kết quả trên ngưỡng ≥ 0.8); con số cosine tuyệt đối phụ thuộc model (e5 có phân bố cosine dồn cao) [chưa xác minh trong phiên]. Khi filter cứng (giá/địa điểm) loại hết, trả thông điệp phân biệt: "có kết quả phù hợp nội dung nhưng vượt ngân sách" vs "không có nội dung phù hợp".
- **Query understanding** (module Search, `QueryParser`): rule-based trước, LLM chỉ là fallback (tiết kiệm chi phí, deterministic):
  - `budget`: regex tiền ("2 củ", "1tr5", "500k", "dưới 3 triệu") → `{amount_vnd, op: lte|gte|approx}`.
  - `group_size`: "(\d+) người|ng|đứa|bạn", "cặp đôi"→2, "gia đình 4"→4.
  - `basis`: "mỗi người|/người|1 người|per person|đầu người" → `per_person`; "tổng|cả nhóm|tất cả" → `total`; mặc định **ambiguous** → nếu có cả budget và group_size >1 mà không rõ basis thì trả `clarification` ("2 triệu là cho mỗi người hay cả nhóm 4 người?") thay vì đoán.
  - Chuẩn hoá: `budget_per_person = total / group_size`; filter `price_min * nights <= budget_per_person` (tuỳ đơn vị giá sản phẩm).
  - `location`: so khớp bảng `places` (tên có dấu + không dấu + alias "sg", "đl").
  - Lưu `parsed_query` (JSONB) trong log để đánh giá.
- **REST endpoints (Search)**:
  - `GET /api/v1/search?q=&type=post,product&province=&price_max=&guests=&page=` → `{query_id, parsed: {budget, group_size, basis, location}, clarification?, results:[{entity_type, entity_id, score, matched_by:['lexical','semantic'], highlight}], no_result_reason?}`
  - `POST /api/v1/search/parse` (debug/dev: chỉ trả parsed query).
  - `POST /api/v1/search/click` `{query_id, entity_id, position}` → phục vụ đánh giá/CTR.
  - Admin: `POST /api/v1/admin/search/reindex?entity_type=`.
- **Events**: consume `catalog.product.created|updated|deleted`, `social.post.published|updated|deleted` → upsert `search_documents` + enqueue `embedding_jobs`; publish `search.query.performed` `{query_id, user_id?, anon_id?, q, parsed, result_ids, latency_ms, zero_result}` và `search.result.clicked`. Trong monolith: dùng bảng outbox + worker (hoặc Redis Streams).
- **Khi nào cần engine riêng**: Meilisearch có typo tolerance tốt cho demo nhưng thêm một hạ tầng cần đồng bộ; Elasticsearch/OpenSearch có RRF built-in nhưng nặng. Với vài nghìn–vài trăm nghìn document của demo, pgvector đủ và giữ đúng ràng buộc "PostgreSQL + Redis". Đây là đánh giá, không có benchmark so sánh trong phiên.
- **Redis**: cache embedding của query (`emb:{model}:{sha1(q_norm)}`, TTL 1–7 ngày) và cache kết quả trang 1 cho query phổ biến (TTL ngắn, ví dụ 60s, invalidate theo version của index).

### Gaps
- Không có benchmark được trích dẫn so sánh latency/recall pgvector vs Qdrant/OpenSearch ở quy mô nhỏ; không lấy được docs Meilisearch/OpenSearch trong phiên.
- Không có nguồn cho giá trị ngưỡng cosine cụ thể cho từng model tiếng Việt — phải tự hiệu chỉnh.

---

## 3. Recommendation: taste vector từ like/save/skip, giải thích ("vì bạn đã lưu..."), cold start, gợi ý bổ sung sau khi đặt ("complete your trip")

### Takeaway
Cách thực tế nhất cho 4 tuần: **taste vector = trung bình có trọng số (và decay theo thời gian) của embedding các item đã tương tác** (dùng lại embedding của Search), lý do giải thích = item đã tương tác có cosine cao nhất với item được gợi ý; cold start = onboarding chọn sở thích + popularity theo địa điểm; "thiếu gì sau khi đặt" = luật dựa trên loại sản phẩm/địa điểm/ngày + đồng xuất hiện trong booking, không cần ML phức tạp. Airbnb là tiền lệ công nghiệp cho việc dùng embedding item + tín hiệu phiên để cá nhân hoá.

### Cited Findings
- Airbnb "Real-time Personalization using Embeddings for Search Ranking" (KDD 2018, Best Paper ADS track) — [Synced Review](https://syncedreview.com/2018/08/21/kdd-2018-announces-best-paper-other-awards/)
- Embedding nắm bắt sở thích ngắn hạn và dài hạn của khách, dùng cho search ranking và similar-listing; hai kênh này chiếm 99% conversion — [tóm tắt abstract, lib.nbt.edu.cn](https://lib.nbt.edu.cn/subject/show/showdetail.php?id=32695)
- Phiên mới bắt đầu khi khoảng cách giữa 2 click > 30 phút; loại click "vô nghĩa" (ở lại < 30 giây) — [Tencent Cloud blog (thứ cấp)](https://cloud.tencent.com/developer/article/1428763)
- Embedding listing 32 chiều, huấn luyện trên 800 triệu click session, 4.5 triệu listing; A/B test: +21% CTR carousel Similar Listing, +4.9% khách tìm thấy listing đã đặt qua carousel — [bestpractice.ai case study (thứ cấp)](https://www.bestpractice.ai/ai-case-study-best-practice/airbnb_increased_similar_property_listing_recommendations_click-through-rates_by_21%25_with_machine_learning_embeddings)
- Airbnb sau đó dùng sequence encoder chạy batch hằng ngày, lưu guest embedding và kết hợp với query lúc search — [Airbnb Tech Blog: Personalizing search by learning from the guest journey](https://airbnb.tech/ai-ml/personalizing-airbnb-search-by-learning-from-the-guest-journey/)
- Expedia Romie (5/2024) định vị là travel agent + concierge, hỗ trợ cả khi kế hoạch thay đổi trong chuyến đi (post-booking) — [Hotel Dive](https://www.hoteldive.com/news/expedia-ai-assistant-romie/716315/); [Travel Market Report](https://travelmarketreport.com/RetailStrategies/articles/Expedia-Unveils-Romie-an-AI-Powered-Travel-Planner)

### Inferences
- **Entities (schema `reco`)**:
  - `user_interactions(id, user_id|anon_id, entity_type, entity_id, action ENUM('view','like','save','skip','book','search_click'), weight, context JSONB, created_at)`.
  - `user_taste_profiles(user_id, vector vector(d), model, n_interactions, top_tags JSONB, updated_at)`.
  - `recommendation_logs(id, user_id, surface ENUM('home','post_detail','after_booking'), items JSONB [{entity_id, score, reason_type, reason_entity_id}], created_at)` — để đo CTR và tái hiện lời giải thích.
  - `complement_rules(id, trigger_product_type, trigger_conditions JSONB, suggest_product_type, priority, reason_template)` — ví dụ: đặt *vé máy bay* đến tỉnh X mà chưa có *lưu trú* trùng ngày → gợi ý khách sạn; đặt *homestay* ở vùng núi → gợi ý *thuê xe máy/tour*; đặt tour sáng sớm → gợi ý *xe đưa đón*.
- **Taste vector**: `v_user = normalize(Σ w_a · decay(t) · e_item)`, với trọng số gợi ý like=+1, save=+2, book=+3, skip=−0.5 (hoặc tách vector âm riêng); decay `exp(−Δt/30 ngày)`. Cập nhật online khi có event (rẻ, chỉ cộng vector) hoặc batch mỗi giờ. Các trọng số là giả định cần thử nghiệm — không có nguồn.
- **Candidate generation**: `ORDER BY embedding <=> v_user LIMIT 200` có filter (đã xem/đã skip loại ra) → re-rank bằng điểm đơn giản `0.7·sim + 0.2·popularity + 0.1·freshness` + đa dạng hoá (MMR hoặc tối đa N item/tỉnh).
- **Giải thích**: với mỗi item gợi ý, tìm `argmax cos(e_item, e_j)` trong các item user đã save/like → `reason = {type:'similar_to_saved', entity_id: j}` → UI render "Vì bạn đã lưu *Homestay Đà Lạt view đồi*". Các loại lý do khác: `popular_in_province`, `matches_onboarding_tag`, `complements_booking`. **Lý do sinh từ dữ liệu, không do LLM viết** ⇒ không thể "bịa".
- **Cold start**: (1) onboarding chọn 3–5 tag/ảnh (biển, núi, ẩm thực, ngân sách) → khởi tạo `v_user` = trung bình embedding của các tag/item đại diện; (2) user ẩn danh: dùng tương tác trong phiên (theo Airbnb: tín hiệu phiên rất mạnh); (3) item mới: có embedding nội dung ngay khi đăng ⇒ không bị item cold-start như collaborative filtering.
- **"Thiếu gì sau khi đặt" (complete your trip)**: khi nhận event `booking.confirmed`, worker lấy toàn bộ booking của trip (cùng user, khoảng ngày chồng nhau, cùng tỉnh) → so với "checklist" theo loại chuyến (lưu trú, di chuyển nội địa, hoạt động, ăn uống) → các slot trống ⇒ áp `complement_rules` + vector search giới hạn tỉnh/ngày/khả dụng. Bổ sung thống kê đồng xuất hiện `P(type B | đã đặt type A, cùng tỉnh)` khi có đủ dữ liệu.
- **REST endpoints**:
  - `GET /api/v1/recommendations?surface=home&limit=20` → `[{entity_type, entity_id, score, reason:{type, entity_id?, text}}]`
  - `GET /api/v1/recommendations/similar?entity_type=post&entity_id=`
  - `GET /api/v1/bookings/{bookingId}/complements` → `{missing_slots:['local_transport','activity'], suggestions:[...]}`
  - `POST /api/v1/interactions` `{entity_type, entity_id, action}` (like/save/skip đi qua module Social nhưng phát event).
  - `POST /api/v1/recommendations/feedback` `{recommendation_log_id, entity_id, action:'dismiss'|'not_interested'}`.
- **Events**: consume `social.post.liked|saved|skipped`, `booking.confirmed|cancelled`, `search.result.clicked`; publish `reco.profile.updated`, `reco.complements.generated` (để Notification module gửi push "Bạn đã có khách sạn ở Đà Lạt, còn thiếu phương tiện di chuyển?").

### Gaps
- Không tìm được tài liệu kỹ thuật công khai về cơ chế "complete your trip"/cross-sell của Trip.com hoặc Expedia (chỉ có thông cáo về Romie hỗ trợ post-booking). Thiết kế luật ở trên là suy luận.
- Không đọc được paper Airbnb gốc (ACM DL); số liệu lấy từ nguồn thứ cấp.

---

## 4. Image search (CLIP-style embedding) cho B2

### Takeaway
Image search dùng **một model ảnh–văn bản chung không gian** (CLIP/SigLIP đa ngôn ngữ) để hỗ trợ cả "ảnh → ảnh tương tự" và "text tiếng Việt → ảnh"; lưu vector ảnh vào pgvector như text. Hỗ trợ tiếng Việt trực tiếp của các model mở còn hạn chế/thiếu đánh giá, nên phương án an toàn là ảnh→ảnh (không phụ thuộc ngôn ngữ) và text→ảnh qua model đa ngôn ngữ hoặc dịch query sang tiếng Anh.

### Cited Findings
- **ViCLIP-OT** (2026, ĐH Cần Thơ): model ảnh–văn bản đầu tiên cho tiếng Việt, kết hợp contrastive (CLIP hoặc SigLIP) với optimal-transport loss; text encoder là Vietnamese Sentence-BERT. Trên UIT-OpenViIC: R@K trung bình 67.34% (CLIP-based), 68.96% (biến thể SigLIP); zero-shot trên Crossmodal-3600 hơn CLIP 11.72 điểm — [ViCLIP-OT arXiv 2602.22678](https://arxiv.org/pdf/2602.22678); [alphaXiv overview](https://www.alphaxiv.org/overview/2602.22678)
- Chưa thấy checkpoint/code chính thức của ViCLIP-OT được phát hành — [opentrain.ai listing](https://www.opentrain.ai/papers/viclip-ot-the-first-foundation-vision-language-model-for-vietnamese-image-text-r--arxiv-2602.22678/)
- SigLIP 2 là họ encoder vision–language đa ngôn ngữ, dùng tokenizer Gemma (vocab 256k); ~90% cặp ảnh–text huấn luyện từ trang tiếng Anh — [arXiv 2502.14786 (SigLIP 2)](https://arxiv.org/html/2502.14786v1)
- Có bản `siglip-base-patch16-256-multilingual` — [PromptLayer model page](https://www.promptlayer.com/models/siglip-base-patch16-256-multilingual)
- MEXMA-SigLIP2 (cộng đồng): text encoder đa ngôn ngữ + image encoder SigLIP2, 80 ngôn ngữ, R@1 62.54% (image retrieval) / 59.99% (text retrieval) trên Crossmodal-3600 — [PromptLayer: mexma-siglip2](https://www.promptlayer.com/models/mexma-siglip2)

### Inferences
- **Entities**: `media_embeddings(id, media_id, owner_entity_type, owner_entity_id, model, embedding vector(768|1152…), phash bigint, width, height, created_at)` + HNSW index. `phash` (perceptual hash) dùng để dedupe ảnh trùng — rẻ hơn vector.
- **Pipeline**: event `media.uploaded` → worker tải ảnh từ object storage → resize → encode → upsert `media_embeddings`; cùng worker infra với `embedding_jobs`. Chạy CPU vẫn được cho vài nghìn ảnh của demo (ước lượng, chưa đo).
- **REST**:
  - `POST /api/v1/search/image` (multipart `file` hoặc `{media_id}`) → `{results:[{entity_type, entity_id, media_id, score}]}`; giới hạn kích thước file, rate-limit chặt hơn text search.
  - `GET /api/v1/search?q=...&mode=image` (text→ảnh) — tuỳ chọn, chỉ bật nếu model có đánh giá tiếng Việt đạt yêu cầu trên test set nội bộ.
- Kết quả ảnh vẫn qua cùng filter (status, tỉnh, giá) và ngưỡng "không có kết quả" như mục 2.
- Kiểm duyệt: ảnh upload để tìm kiếm không nên lưu lâu dài (xoá sau khi encode) để giảm rủi ro dữ liệu cá nhân.

### Gaps
- Không có benchmark tiếng Việt cho SigLIP2-multilingual / multilingual CLIP; ViCLIP-OT chưa có weights công khai được xác minh.
- Không xác minh được số chiều/kích thước cụ thể của từng checkpoint (huggingface.co bị chặn).

---

## 5. Trợ lý LLM có grounding: tool calling, RAG có citation, kiểm tra entity ID sau sinh, refusal/an toàn, hỏi lại, memory đa thiết bị + gộp ẩn danh→đăng nhập, đo chi phí theo hội thoại, rate limit, cache; tham khảo Expedia/Booking/Trip.com/Airbnb

### Takeaway
"Không bao giờ bịa" phải được đảm bảo bằng **kiến trúc**, không chỉ prompt: LLM chỉ được nhắc tới địa điểm/bài viết/sản phẩm thông qua **tool trả về ID thật** (gọi chính Search/Reco module), output có cấu trúc (JSON chứa `entity_id` + `citation`), và **validator phía server** loại bỏ/viết lại mọi ID không nằm trong tập kết quả tool của lượt đó. Các OTA lớn (Trip.com, Booking.com) đều đặt LLM lên trên dữ liệu/ML nội bộ của họ; Trip.com công khai dùng retrieval + grounding check.

### Cited Findings
- Trip.com ra mắt TripGenie giữa 2023 (nâng cấp từ TripGen ra mắt 2/2023), tích hợp trong app, hỗ trợ lập kế hoạch và đặt chỗ — [Trip.com newsroom](https://www.trip.com/newsroom/introducing-tripgenie-groundbreaking-ai-travel-assistant); [Skift](https://skift.com/2023/07/25/trip-com-releases-new-trip-planning-chatbot-based-on-its-own-generative-ai-model/)
- TripGenie dùng nội dung xếp hạng của Trip.com (Trip.Best, Trip.Deals) để cải thiện chất lượng câu trả lời — [PhocusWire](https://www.phocuswire.com/Trip-com-unveils-AI-travel-assistant-focused-on-personalization)
- Theo case study (do Google Cloud công bố — nguồn vendor, tự báo cáo): Trip.com dùng Agent Search trên Gemini làm retrieval engine cho TripGenie và **Grounding** để kiểm tra phản hồi của Gemini với nguồn dữ liệu có thể xác minh; tăng trưởng người dùng gấp 3 sau khi nâng cấp — [Google Cloud customer story](https://cloud.google.com/customers/tripgenie) (trang không fetch được nội dung đầy đủ; thông tin lấy qua snippet tìm kiếm)
- Lịch sử thiết kế TripGenie 1.0→3.0 (tiếng Trung) — [woshipm.com](https://www.woshipm.com/ucd/6273711.html)
- Booking.com AI Trip Planner kết hợp các ML model của Booking.com với ChatGPT API của OpenAI; ban đầu chỉ trên app mobile ở các thị trường nói tiếng Anh (US, UK, AU, NZ); trong thử nghiệm của USA TODAY không đặt trực tiếp được trong trợ lý mà phải sang trang khách sạn — [Yahoo/USA TODAY](https://ca.news.yahoo.com/ai-trip-planning-tool-best-134756743.html); [OpenAI: Booking.com](https://openai.com/index/booking-com/)
- Expedia Romie: ra mắt 5/2024; tham gia SMS group chat để phân tích cả cuộc hội thoại; USA TODAY ghi nhận "glitches" và khuyên không nên dựa hoàn toàn vào nó — [Yahoo/USA TODAY](https://ca.news.yahoo.com/ai-trip-planning-tool-best-134756743.html); [Hotel Dive](https://www.hoteldive.com/news/expedia-ai-assistant-romie/716315/)
- Expedia: case study LLMOps về trợ lý du lịch (trip planning + personalization) — [ZenML LLMOps database](https://www.zenml.io/llmops-database/ai-powered-travel-assistant-for-trip-planning-and-personalization) (chưa đọc chi tiết)
- **Prompt caching (Anthropic)**: cache read = 0.1× giá input cơ bản; cache write 5 phút = 1.25×; cache write 1 giờ = 2× — [Anthropic docs: prompt caching](https://docs.claude.com/en/docs/build-with-claude/prompt-caching) (qua snippet tìm kiếm; trang không fetch được). Response trả các trường usage riêng `input_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens`, `output_tokens` và tính phí theo từng mức — mô tả bởi gateway bên thứ ba [Helicone docs](https://docs.helicone.ai/gateway/concepts/prompt-caching); cần đối chiếu docs chính thức. Prompt caching cũng có trên Vertex AI cho Claude — [Google Cloud Vertex AI docs](https://cloud.google.com/vertex-ai/generative-ai/docs/partner-models/claude/prompt-caching)
- Giá có thể thay đổi; phải xác nhận trên trang pricing chính thức trước khi dùng — (khuyến cáo chung; không ghi giá cụ thể ở đây theo ràng buộc đề bài)

### Inferences
- **Kiến trúc lượt hội thoại (agent loop có giới hạn)**:
  1. `POST /messages` → load memory (N lượt gần nhất + summary + profile tóm tắt) → gọi LLM với **tools**: `search_places(q, province?, budget?, guests?)`, `search_posts(q, ...)`, `get_entity(entity_type, id)`, `get_recommendations(surface)`, `get_trip_gaps(booking_id)`, `ask_clarification(question, options[])`. Tools là adapter gọi **in-process** vào Search/Reco module (monolith) ⇒ dùng chung filter/ngưỡng "không có kết quả".
  2. Giới hạn tối đa 3–4 vòng tool call/lượt để chặn chi phí và vòng lặp.
  3. Bắt LLM trả **structured output**: `{text_segments:[{text, cites:[{entity_type, entity_id}]}], cards:[{entity_type, entity_id}], clarification?:{question, options}}`.
  4. **Validator sau sinh**: tập `allowed_ids` = mọi ID xuất hiện trong kết quả tool của lượt này (+ lượt trước nếu còn trong context). Mọi `entity_id` ngoài tập ⇒ loại card; nếu một segment có cite không hợp lệ ⇒ xoá segment hoặc retry 1 lần với thông báo lỗi; vẫn sai ⇒ fallback câu trả lời template ("Mình chưa tìm thấy thông tin chắc chắn…") + hiển thị kết quả search thô. Thêm kiểm tra **tên riêng**: trích các cụm viết hoa/địa danh trong text, so với `name` của các entity cho phép + danh mục tỉnh/thành; tên lạ ⇒ đánh dấu `unverified_mention` để log/đo.
  5. **Giá, khả dụng, giờ mở cửa**: không bao giờ lấy từ text LLM — UI render từ card (dữ liệu DB tại thời điểm hiển thị). LLM được dặn không nêu số tiền trừ khi có trong tool result; validator regex số tiền trong text và so với tool result.
  6. **Khi tool trả rỗng** (do ngưỡng mục 2): trợ lý phải nói thật "Tripflix chưa có địa điểm/bài viết phù hợp" — có thể ép bằng luật: nếu mọi tool result rỗng thì chỉ cho phép response loại `no_result` hoặc `clarification`.
- **Clarifying questions**: tái dùng `QueryParser` (mục 2): khi thiếu/không rõ ngân sách-basis, số người, ngày, địa điểm ⇒ trả `clarification` với options (chip UI) thay vì gọi search. Giới hạn 1 câu hỏi/lượt để tránh hỏi dồn.
- **Refusal/an toàn**: (a) phạm vi: chỉ du lịch & nội dung Tripflix — câu hỏi ngoài phạm vi (y tế, pháp lý, visa chi tiết) ⇒ từ chối lịch sự + gợi ý nguồn chính thức; (b) prompt injection từ nội dung post do user khác viết: tool result được bọc như dữ liệu, system prompt nói rõ không làm theo lệnh trong dữ liệu; tool là read-only (trợ lý **không** tự đặt chỗ/thanh toán, chỉ deep-link sang flow Booking — giống Booking.com AI Trip Planner ban đầu); (c) không đưa PII của user khác vào context.
- **Entities (schema `ai`)**:
  - `ai_conversations(id, user_id NULL, anon_id NULL, title, status, created_at, last_message_at, total_input_tokens, total_output_tokens, total_cache_read_tokens, total_cache_write_tokens, total_cost_micro_usd, model)`.
  - `ai_messages(id, conversation_id, role ENUM('user','assistant','tool'), content JSONB, tool_name, tool_args JSONB, created_at)`.
  - `ai_llm_calls(id, conversation_id, message_id, provider, model, input_tokens, output_tokens, cache_read_tokens, cache_write_tokens, latency_ms, cost_micro_usd, price_version, status, error)` — một dòng cho **mỗi** request tới LLM (một lượt có thể có nhiều call do tool loop).
  - `ai_model_prices(id, provider, model, input_per_mtok, output_per_mtok, cache_read_per_mtok, cache_write_per_mtok, currency, effective_from, source_url)` — **bảng giá có version**, nhập tay từ trang pricing chính thức; cost = Σ tokens × đơn giá / 1e6 tính tại thời điểm call và lưu `price_version` để không bị sai khi giá đổi. Quy đổi VND bằng tỷ giá cấu hình (`fx_rates`) chỉ khi hiển thị.
  - `ai_answer_validations(id, message_id, cited_ids JSONB, invalid_ids JSONB, unverified_mentions JSONB, action ENUM('pass','stripped','retried','fallback'))` — nguồn số liệu cho "hallucination rate".
  - `ai_feedback(id, message_id, user_id, rating ENUM('up','down'), reason)`.
- **Memory đa thiết bị + gộp ẩn danh→đăng nhập**: hội thoại lưu server-side (không phải localStorage) theo `user_id` ⇒ đồng bộ đa thiết bị. Khách ẩn danh có `anon_id` (cookie/device id, ký HMAC). Khi login: event `identity.user.logged_in {user_id, anon_id}` ⇒ `UPDATE ai_conversations SET user_id=:uid, anon_id=NULL WHERE anon_id=:aid` (idempotent), đồng thời merge `user_interactions` vào profile reco. Long-term memory: chỉ lưu "facts" có cấu trúc (ngân sách thường dùng, sở thích tag) trong `user_taste_profiles`/`user_preferences`, không lưu nguyên văn chat vào prompt; context = N lượt cuối + summary cuộn (sinh khi vượt ngưỡng token).
- **Prompt caching**: đặt phần **tĩnh** (system prompt, định nghĩa tools, few-shot) ở đầu và đánh dấu cache; phần động (lịch sử, tool results) ở sau. Với TTL ngắn (5 phút) cache chỉ có lợi khi lưu lượng đủ dày hoặc trong cùng một hội thoại nhiều lượt — demo nên đo tỷ lệ `cache_read_tokens / input_tokens` thay vì giả định tiết kiệm.
- **Cache ứng dụng (Redis)**: (a) cache kết quả tool (search) theo `hash(parsed_query)` TTL ngắn; (b) cache câu trả lời cho câu hỏi gợi ý cố định trên UI ("Đi Đà Lạt 3 ngày 2 triệu?") theo `hash(normalized_q + index_version)` — chỉ cho user ẩn danh/không cá nhân hoá; (c) không cache câu trả lời có cá nhân hoá.
- **Rate limit & ngân sách** (Redis): token bucket per `user_id|anon_id|ip` cho `/messages` (ví dụ ẩn danh 10 tin/giờ, đăng nhập 50 tin/ngày — số giả định); **budget guard**: `INCRBY ai:cost:{user}:{yyyymmdd}` và `ai:cost:global:{yyyymmdd}` sau mỗi call; vượt trần ⇒ HTTP 429 với `retry_after` hoặc chuyển sang chế độ "chỉ search, không LLM". Giới hạn `max_tokens` output và độ dài input (cắt tin nhắn > N ký tự).
- **REST endpoints (AI)**:
  - `POST /api/v1/ai/conversations` → `{conversation_id}`
  - `GET /api/v1/ai/conversations?cursor=` ; `GET /api/v1/ai/conversations/{id}/messages`
  - `POST /api/v1/ai/conversations/{id}/messages` `{text, client_msg_id}` (idempotency) → SSE stream các event: `status` ("đang tìm…"), `delta`, `cards`, `clarification`, `done {usage:{input,output,cache_read,cache_write}, cost_micro_usd}`. Lưu ý: khi stream text, validator chỉ chạy được trên phần cards/cites ở cuối ⇒ phương án an toàn cho demo: **không stream text tự do**, mà sinh xong → validate → trả; hoặc stream nhưng chỉ render card sau validate.
  - `POST /api/v1/ai/messages/{id}/feedback`
  - `POST /api/v1/ai/conversations/merge` (gọi nội bộ khi login, hoặc xử lý qua event)
  - Admin: `GET /api/v1/admin/ai/usage?from=&to=&group_by=day|model|user` → tổng token, cost, số call, p95 latency, tỉ lệ `invalid_ids`/fallback.
- **Events**: publish `ai.message.completed {conversation_id, message_id, usage, cost, validation_action}`, `ai.budget.exceeded`, `ai.answer.flagged`; consume `identity.user.logged_in` (merge), `catalog.*`/`social.*` không cần (đi qua Search).
- **Bài học từ OTA**: Booking.com và Trip.com xây trên dữ liệu và ML sẵn có của họ (review, ranking list) ⇒ Tripflix nên coi LLM là lớp "điều phối + diễn đạt" trên Search/Reco, không phải nguồn tri thức. USA TODAY ghi nhận Romie có glitches ⇒ ngay cả sản phẩm lớn cũng chưa hoàn hảo, nên UI cần nhãn "AI có thể sai — thông tin giá/khả dụng lấy từ hệ thống".

### Gaps
- Không đọc được docs chính thức về tool use/structured outputs (docs.anthropic.com bị chặn; không tìm docs OpenAI trong phiên); chi tiết API (tên trường, chế độ strict schema) cần kiểm tra trực tiếp.
- Không có giá token cụ thể có nguồn trong ghi chú này (theo ràng buộc); chỉ có hệ số caching tương đối của Anthropic.
- Không tìm thấy tài liệu kỹ thuật công khai chi tiết về cách Expedia/Booking validate entity sau sinh; Airbnb không có tài liệu về trợ lý LLM được tìm thấy trong phiên.
- Case study Google Cloud về TripGenie là nguồn vendor, không fetch được toàn văn.

---

## 6. Đánh giá: test set, precision@k/nDCG cho search, tỷ lệ hallucination cho trợ lý

### Takeaway
Cần một **test set nhỏ nhưng thật** (50–150 query tiếng Việt gồm có dấu/không dấu/teencode/trộn Anh/"không có kết quả") với nhãn liên quan cấp độ (0–2), chạy offline mỗi lần đổi model/ngưỡng; với trợ lý, đo **tỷ lệ ID không hợp lệ (tự động, từ validator)** + **faithfulness theo claim** (RAGAS-style hoặc chấm tay mẫu nhỏ). Benchmark công khai (VN-MTEB) chỉ để chọn model ban đầu.

### Cited Findings
- RAGAS Faithfulness: LLM tách câu trả lời thành các claim và kiểm tra từng claim có suy ra được từ context đã truy xuất; điểm 0–1; claim không được context hỗ trợ được tính là hallucination — [letsdatascience: RAGAS & LLM-as-judge](https://letsdatascience.com/blog/llm-evaluation-ragas-llm-as-judge-and-production-evals); [klu.ai glossary](https://klu.ai/glossary/ragas)
- Ví dụ diễn giải: faithfulness 0.85 ≈ 15% claim không được hỗ trợ — [DEV Community](https://dev.to/sridhar_tondapi/master-rag-evaluation-with-ragas-5403)
- RAGAS có thể dùng Vectara HHEM-2.1-Open (classifier mã nguồn mở phát hiện hallucination); metric phụ thuộc judge LLM và nên kết hợp chấm tay — [letsdatascience](https://letsdatascience.com/blog/llm-evaluation-ragas-llm-as-judge-and-production-evals)
- Có nghiên cứu về cách chọn ngưỡng cho metric đánh giá LLM — [arXiv 2412.12148](https://arxiv.org/pdf/2412.12148)
- VN-MTEB dùng các task retrieval/reranking chuẩn kiểu MTEB (nDCG@10 là metric chính của retrieval trong MTEB [chưa xác minh trong phiên]) — [VN-MTEB](https://arxiv.org/html/2507.21500v1); NanoVNMTEB báo cáo nDCG@10, hit@10, recall@100 — [hakari-bench](https://hakari-bench-leaderboard.hf.space/docs/benchmark-tasks/NanoVNMTEB)

### Inferences
- **Entities (schema `eval`)**: `eval_queries(id, q, category ENUM('diacritic','no_diacritic','teencode','mixed_en','budget','no_result','image'), expected_parsed JSONB)`, `eval_judgments(query_id, entity_type, entity_id, grade 0|1|2)`, `eval_runs(id, config JSONB {model, k_rrf, tau_vec, tau_trgm}, metrics JSONB, created_at)`.
- **Metrics Search**: nDCG@10, Precision@5, Recall@20, MRR; **zero-result correctness** (với query loại `no_result`: đúng nếu trả rỗng; với query có đáp án: tỉ lệ bị trả rỗng sai) — dùng để chọn τ; **parser accuracy** cho budget/group_size/basis (exact match với `expected_parsed`). Báo cáo theo `category` để thấy nhánh không dấu/teencode yếu ở đâu. So sánh 3 cấu hình: lexical-only, vector-only, hybrid RRF.
- **Online**: CTR theo vị trí, tỉ lệ zero-result từ `search.query.performed`, tỉ lệ "reformulate" (query tiếp theo trong 30s).
- **Metrics Recommendation**: offline hit-rate@10 bằng leave-last-out trên `user_interactions` (save/book cuối cùng); online CTR/save-rate của `recommendation_logs`; với complement: tỉ lệ booking bổ sung trong 7 ngày (nếu đủ dữ liệu — demo có thể chỉ trình bày phương pháp).
- **Metrics Assistant**:
  - `invalid_entity_rate` = số `invalid_ids` / tổng cites (tự động, 100% traffic) — mục tiêu bằng 0 *sau* validator; đo cả *trước* validator để biết model "muốn bịa" bao nhiêu.
  - `unverified_mention_rate` (tên địa danh không khớp entity/danh mục).
  - `faithfulness` theo claim trên 30–50 hội thoại mẫu: RAGAS hoặc chấm tay (2 người, đo đồng thuận).
  - `honest_no_result_rate`: với prompt loại `no_result`, tỉ lệ trợ lý nói "không có" thay vì gợi ý ngoài DB.
  - `clarification_appropriateness`: với prompt mơ hồ ("2 triệu 4 người"), có hỏi lại basis không.
  - **Chi phí**: cost/hội thoại (p50/p95), tokens/lượt, cache hit ratio, latency p95 — từ `ai_llm_calls`.
- **Red-team set nhỏ**: prompt injection trong post, câu hỏi ngoài phạm vi, yêu cầu giá "rẻ nhất Việt Nam" khi DB không có, yêu cầu đặt chỗ hộ.

### Gaps
- Không có bộ test set du lịch tiếng Việt công khai phù hợp được tìm thấy; phải tự xây.
- Chưa có nguồn chính thức (docs RAGAS) được đọc trong phiên — mô tả metric dựa trên blog thứ cấp.
