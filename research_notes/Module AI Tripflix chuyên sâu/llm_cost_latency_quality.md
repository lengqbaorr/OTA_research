# Kinh tế LLM/Embedding & trade-off kỹ thuật (cost, latency, quality) cho module AI của Tripflix

> Ngày nghiên cứu: 2026-11-02. Phạm vi: 7 tác vụ AI (query understanding slang → filter; assistant chat có tool calling; tóm tắt post/review B1; semantic search embeddings; image similarity B2; moderation tiếng Việt; lý do gợi ý).
>
> **Giới hạn truy cập trong phiên này:** `openai.com` và `ai.google.dev` bị chặn ở tầng mạng (ENOTFOUND) nên KHÔNG đọc được trang giá chính thức của OpenAI và Google. Giá Anthropic lấy trực tiếp từ trang giá chính thức. Giá OpenAI/Google dưới đây chỉ lấy từ snippet tìm kiếm/aggregator bên thứ ba hoặc blog chính hãng xuất hiện trong kết quả tìm kiếm — phải kiểm tra lại trước khi đưa vào báo cáo cuối. Để fetch được, cần cho phép `openai.com`, `ai.google.dev`, `arxiv.org`, `huggingface.co`, `artificialanalysis.ai`, `langfuse.com` trong network settings của session.

## 1. Bảng giá & tier model 2026 (Anthropic, OpenAI, Google) + caching, batch, structured outputs

### Takeaway
Ở mức giá 11/2026, tier "nhỏ" (Claude Haiku 5.5 $0.10/$0.50 per MTok; GPT-5 nano ~$0.05/$0.40; Gemini Flash-Lite ~$0.10–0.30 input) rẻ hơn tier "giữa" (Sonnet 5.5 $2/$10) khoảng 20 lần — với traffic demo (vài nghìn lượt chat) toàn bộ chi phí LLM chỉ ở mức vài USD nếu dùng tier nhỏ, vài chục–trăm USD nếu dùng tier lớn; vì vậy rủi ro thật là *không đo được* chi phí và *latency do reasoning*, không phải số tiền.

### Cited Findings
**Anthropic (trang giá chính thức, đọc ngày 2026-11-02):**
- Claude Haiku 5.5 (prompt ≤100K token): input $0.10/MTok, cache write 5 phút $0.125, cache write 1 giờ $0.20, cache hit $0.01, output $0.50/MTok. Prompt >100K token: $0.50 / $0.625 / $1 / $0.05 / $2.50 — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Claude Sonnet 5.5: input $2, cache write 5m $2.50, 1h $4, cache hit $0.10, output $10/MTok — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Claude Opus 5.5: input $4, cache write 5m $5, 1h $8, cache hit $0.20, output $20/MTok; Fast mode Opus 5.5: $8/$40 — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Claude Haiku 4.5 (thế hệ trước): $1 input / $5 output; Claude Fable 5.1 (top tier): $10/$50 — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Sonnet 5: giá $2/$10 ban đầu là "introductory" nay thành giá chuẩn; đợt tăng lên $3/$15 dự kiến 1/9/2026 sẽ không xảy ra — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Cache hit thường = 0.1× giá input (Opus 5.5/Sonnet 5.5: 0.05×); cache write = 1.25× (TTL 5 phút) hoặc 2× (TTL 1 giờ); caching hòa vốn sau 1 lần đọc (5m) hoặc 2 lần đọc (1h); các hệ số này cộng dồn với Batch discount và data residency — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Batch API: giảm 50% cả input lẫn output (Haiku 5.5 batch: $0.05/$0.25; Sonnet 5.5 batch: $1/$5) — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Dùng tool làm tăng input: "tool use system prompt" = 286 token cho Haiku 5.5/Sonnet 5.5/Opus 5.5 (tool_choice auto/none), cộng thêm token của định nghĩa tool — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- `inference_geo: "us"` nhân 1.1× giá; mặc định global giá chuẩn — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Web search server tool: $10/1.000 lượt search + token — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Có thể có giảm giá học thuật/nghiên cứu (đàm phán từng trường hợp) — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- (Từ tài liệu tham chiếu Claude API đi kèm công cụ, bản cache 2026-10-06, chưa fetch live — cần kiểm tra tại [Models overview](https://platform.claude.com/docs/en/about-claude/models/overview) và [Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)): prefix tối thiểu để cache trên Haiku 5.5/Sonnet 5.5/Opus 5.5 là 512 token (prefix ngắn hơn sẽ âm thầm không cache); Haiku 5.5 có adaptive thinking bật mặc định, effort mặc định `medium`, chỉ tắt thinking được ở effort ≤ `high`; tokenizer mới của Haiku 5.5 đếm cùng văn bản nhiều hơn ~30% token so với Haiku 4.5; structured outputs dùng `output_config.format` và `strict: true` trên tool để đảm bảo JSON đúng schema; forced `tool_choice` (`any`/`tool`) bị 400 trên Sonnet 5.5/Opus 5.5.

**OpenAI (trang chính thức bị chặn — số liệu thứ cấp):**
- GPT-5 mini ≈ $0.25 input / $2.00 output / $0.025 cached input per 1M; GPT-5 nano ≈ $0.05 / $0.40 / $0.005 cached (aggregator, bảng "tính đến 9/2026") — [Silicondata](https://www.silicondata.com/use-cases/openai-api-pricing-per-1m-tokens); [Spendlint](https://spendlint.dev/pricing/openai-api); [Nicolalazzari](https://nicolalazzari.ai/articles/openai-api-pricing-explained-2026)
- Bài công bố chính hãng: GPT-5.4 mini $0.75 input / $4.50 output; GPT-5.4 nano (chỉ API) $0.20 / $1.25 per 1M — [OpenAI: Introducing GPT-5.4 mini and nano](https://openai.com/index/introducing-gpt-5-4-mini-and-nano/) (chỉ thấy qua snippet tìm kiếm)
- Batch API giảm 50% (một nguồn thứ cấp) — [Calcis](https://www.calcis.dev/pricing/openai)
- Các aggregator mâu thuẫn về line-up mới nhất (GPT-5.5, GPT-5.6, "GPT-6 Astra") — [Modelpricing.ai](https://modelpricing.ai/models/openai); [Amnic](https://amnic.com/blogs/openai-api-pricing) → coi là chưa xác minh.
- text-embedding-3-small ≈ $0.02/1M token (batch ~$0.01) — [Layer3labs](https://www.layer3labs.io/guides/openai-embedding-models-pricing); [OpenRouter](https://openrouter.ai/openai/text-embedding-3-small)
- Moderation API (`omni-moderation-latest`) miễn phí, giới hạn theo rate-limit tier — [Analytics Vidhya](https://www.analyticsvidhya.com/blog/2026/05/openai-omni-moderation/); [Evolink](https://evolink.ai/blog/omni-moderation-latest-guide)

**Google Gemini (trang chính thức bị chặn — số liệu thứ cấp, mâu thuẫn):**
- Gemini 2.5 Flash-Lite $0.10 / $0.40 nhưng được báo retire 16/10/2026; Gemini 3.1 Flash-Lite $0.25 / $1.50; Gemini 3.5 Flash-Lite $0.30 / $2.50 — [CloudZero](https://www.cloudzero.com/blog/gemini-pricing/); [Puter](https://developer.puter.com/tutorials/gemini-api-pricing/)
- Gemini 2.5 Flash $0.30 / $2.50; Gemini 3.5 Flash $1.50 / $9.00 — [MetaCTO](https://www.metacto.com/blogs/the-true-cost-of-google-gemini-a-guide-to-api-pricing-and-integration); [TLDL](https://www.tldl.io/resources/google-gemini-api-pricing)
- Trang giá Google Cloud (theo snippet) liệt kê giá giới thiệu $0.75 / $3.75 cho Gemini 3.6/3.7/3.8 Flash đến 31/12/2026 — [Google Cloud Agent Platform Pricing](https://cloud.google.com/gemini-enterprise-agent-platform/generative-ai/pricing)
- gemini-embedding-001: $0.15/1M input token (blog chính hãng); Gemini Embedding 2 (preview): $0.20/1M text token ($0.10 batch), ảnh $0.45/1M — [Google Developers Blog](https://developers.googleblog.com/gemini-embedding-available-gemini-api/); [Tokencost](https://tokencost.app/blog/gemini-embedding-2-pricing)
- Lưu ý chung: "thinking tokens" của Gemini bị tính như output và hay bị bỏ sót khi lập ngân sách — [CloudZero](https://www.cloudzero.com/blog/gemini-pricing/)

### Inferences
- Với 7 tác vụ của Tripflix, tier nhỏ (Haiku 5.5 / GPT-5 nano / Flash-Lite) là mặc định hợp lý; tier giữa (Sonnet 5.5) chỉ nên làm "fallback" cho câu hỏi khó hoặc để chấm (LLM-as-judge) bộ eval offline.
- Thinking/reasoning token là biến số chi phí và latency lớn nhất ở tier nhỏ năm 2026 (Haiku 5.5 bật thinking mặc định; GPT-5 nano "High" có TTFT rất lớn — xem mục 2). Báo cáo chi phí phải tách riêng "visible output" và "thinking output".
- Prompt caching gần như miễn phí lợi ích cho assistant chat (system prompt + tool definitions cố định ≥512 token); Batch chỉ dùng cho tác vụ offline (B1 tóm tắt, lý do gợi ý precompute, embedding hàng loạt).
- Giá rẻ đến mức chi phí demo không phải ràng buộc chính; ràng buộc là *rate limit tier của tài khoản mới* và *latency*.

### Gaps
- Không đọc được trang giá chính thức OpenAI/Google (host bị chặn) — mọi số OpenAI/Gemini cần xác minh lại; đặc biệt line-up GPT-5.x/Gemini 3.x thay đổi nhanh và nguồn thứ cấp mâu thuẫn.
- Chưa xác minh giá cache/batch chính thức của Gemini và OpenAI 2026.
- Free tier của Gemini API (giới hạn RPM/RPD) chưa có nguồn — quan trọng cho nhóm sinh viên.

## 2. Đặc tính latency: TTFT, tokens/sec, streaming SSE, mạng yếu

### Takeaway
Model nhỏ *không bật reasoning* có TTFT ~0.3–1 giây và 100–300+ token/giây; bật reasoning đẩy TTFT lên hàng chục giây — với chat trên mạng di động yếu, phải tắt/giảm reasoning ở đường nóng, stream bằng SSE, và giữ payload/round-trip ít nhất.

### Cited Findings
- Gemini 2.5 Flash-Lite (non-reasoning): TTFT 0.34s, 293.8 token/s; GPT-5 nano (High reasoning): TTFT 85.78s, 150.9 token/s — [Artificial Analysis: GPT-5 nano vs Gemini 2.5 Flash-Lite](https://artificialanalysis.ai/models/comparisons/gpt-5-nano-vs-gemini-2-5-flash-lite)
- Claude 4.5 Haiku (non-reasoning): TTFT 0.96s, ~95 token/s, so với Gemini 2.5 Flash (non-reasoning) 0.49s / 221 token/s — [Artificial Analysis: Haiku 4.5 vs Gemini 2.5 Flash](https://artificialanalysis.ai/models/comparisons/claude-4-5-haiku-vs-gemini-2-5-flash); một snapshot khác ghi Haiku 4.5 non-reasoning TTFT 0.52s — [Artificial Analysis: Gemini 3.1 Flash-Lite vs Haiku 4.5](https://artificialanalysis.ai/models/comparisons/gemini-3-1-flash-lite-preview-vs-claude-4-5-haiku)
- Claude 4.5 Haiku (reasoning): TTFT ~19.39s — [Artificial Analysis: Gemini 3.1 Flash-Lite vs Haiku 4.5 (Reasoning)](https://artificialanalysis.ai/models/comparisons/gemini-3-1-flash-lite-preview-vs-claude-4-5-haiku-reasoning)
- Gemini 3.5 Flash-Lite: 460.6 token/s trên AI Studio nhưng TTFT 6.59–8.13s (số liệu mâu thuẫn giữa các trang) — [Artificial Analysis: Gemini 3.5 Flash-Lite](https://artificialanalysis.ai/models/gemini-3-5-flash-lite)
- Model Google có TTFT thấp nhất trên AI Studio: Gemini 2.5 Flash-Lite non-reasoning 0.31s — [Artificial Analysis: Google provider](https://artificialanalysis.ai/providers/google)
- Artificial Analysis có trang benchmark riêng cho "Claude Haiku 5.5 (Medium)" (không lấy được số liệu cụ thể) — [Artificial Analysis: Haiku 5.5 providers](https://artificialanalysis.ai/models/claude-haiku-5-5-medium/providers)
- (Tài liệu tham chiếu Claude API, cache 2026-10-06) Haiku 5.5 effort mặc định `medium` với thinking bật; có thể tắt thinking ở effort ≤ high; thinking mặc định "omitted" nên khi stream người dùng thấy một khoảng lặng trước text — kiểm tra tại [Models overview](https://platform.claude.com/docs/en/about-claude/models/overview).

### Inferences
- Ngân sách latency gợi ý (giả định, cần đo): query understanding ≤1.0s p50 (model nhỏ, thinking tắt, output JSON ~100 token ≈ 0.3–1s TTFT + <1s generate); chat: TTFT ≤1.5s p50 — chỉ đạt được khi thinking tắt/low.
- Một turn chat có tool calling = ít nhất 2 lượt gọi model tuần tự (gọi tool → trả lời) ⇒ TTFT cảm nhận ≈ 2× TTFT model + thời gian tool. Nên stream ngay một "trạng thái" ("Đang tìm homestay Đà Lạt…") khi tool bắt đầu, và hiển thị kết quả tool (thẻ sản phẩm) trước khi model viết lời giải thích.
- Mạng yếu: (a) orchestration toàn bộ ở server — client chỉ gửi tin nhắn mới + conversation_id, không gửi lại history; (b) SSE qua HTTP/1.1/2 với heartbeat 15s và `Last-Event-ID` để resume; (c) gửi kết quả có cấu trúc nhỏ (ID + giá) và để client render; (d) timeout mềm (ví dụ 8s) → fallback sang kết quả search deterministic; (e) nén gzip/brotli. Đây là suy luận kỹ thuật, không có nguồn đo cụ thể cho mạng VN.
- p95 của API công cộng thường cao hơn p50 nhiều lần do queueing; nhóm nên tự đo p50/p95 bằng log (mục 6) thay vì dựa số benchmark.

### Gaps
- Không có số p50/p95 chính thức của các provider; Artificial Analysis chỉ đưa median và số dao động theo snapshot.
- Không có số TTFT/tokens-per-second cụ thể cho Claude Haiku 5.5 / Sonnet 5.5 / GPT-5.4 mini (không fetch được trang).
- Không tìm thấy nghiên cứu đo SSE trên mạng di động Việt Nam (3G/4G yếu).

## 3. Chất lượng tiếng Việt: benchmark, model chuyên tiếng Việt, teencode

### Takeaway
Benchmark tiếng Việt chủ yếu đo kiến thức kiểu thi trắc nghiệm (VMLU, ViLLM-Eval), không đo teencode/slang; model Việt 7B (Vistral, SeaLLM) chỉ đạt ~45–55% VMLU — kém xa model frontier — nên với 4 tuần, dùng API model lớn/nhỏ thương mại + bộ eval slang tự xây (50–100 câu) là thực tế hơn tự host model Việt.

### Cited Findings
- VMLU: 10.880 câu trắc nghiệm, 58 môn, công bố ACL 2025 (Zalo AI & JAIST), leaderboard tại vmlu.ai — [ACL 2025 paper](https://aclanthology.org/2025.acl-long.563.pdf); [VMLU explained](https://github.com/andreidhoang/vietnamese-llm-finetuning/blob/main/fine-tuning/docs/VMLU_EVALUATION_EXPLAINED.md)
- ViLLM-Eval: bộ đánh giá gồm trắc nghiệm + dự đoán từ tiếp theo, nhiều độ khó, xây cho VLSP 2023 — [arXiv 2404.11086](https://arxiv.org/abs/2404.11086v2)
- Vistral-7B-Chat tự báo cáo VMLU trung bình 50.07%, cao hơn ChatGPT (46.33%) — [HF Vistral-7B-Chat](https://huggingface.co/Viet-Mistral/Vistral-7B-Chat) (số tự báo cáo, so với ChatGPT đời cũ)
- SeaLLM-7B-v2: VMLU 45.74; model card khuyên dùng SeaLLM3 — [HF SeaLLM-7B-v2](https://huggingface.co/SeaLLMs/SeaLLM-7B-v2)
- Bảng cộng đồng: VNPTAI.IO-14B 55.83, SeaLLM-7B-v2.5 53.30, Vistral-7B-Chat 50.07 (ngày snapshot không rõ) — [GitHub VMLU mirror](https://github.com/thomasNguyen-196/VMLU)
- Aggregator dẫn snapshot leaderboard VMLU 2026-04-25 với model fine-tune V-LLM v1 (VinSmart Future) 85.11, và lưu ý model fine-tune theo format VMLU thường điểm cao hơn — [Megatek VMLU](https://megatek.ai/en/benchmark/vmlu/) (chưa xác minh cột metric)
- SeaLLM-7B-chat VMLU 5-shot ~0.35, VinaLLaMA-7B-chat ~0.41 — [VinaLLaMA paper](https://arxiv.org/pdf/2312.11011)
- ViLexNorm (EACL 2024): 10.467 cặp câu chuẩn hóa từ bình luận mạng xã hội VN, gồm cả thay thế OOV và IV; hệ thống tốt nhất đạt ERR 57.74%; chuẩn hóa giúp nhận diện cảm xúc và hate speech — [ACL Anthology ViLexNorm](https://aclanthology.org/2024.eacl-long.85/); [arXiv 2401.16403](https://arxiv.org/abs/2401.16403)
- ViSoLex: repo mã nguồn mở chuẩn hóa từ vựng mạng xã hội tiếng Việt (BARTpho, ViSoBERT, weak supervision); từ điển NSW được sinh định nghĩa bằng GPT-4 API — [ACL Anthology ViSoLex (COLING 2025 demo)](https://aclanthology.org/2025.coling-demos.18.pdf)
- Framework weak supervision cho lexical normalization tiếng Việt dùng ViLexNorm làm seed — [arXiv 2409.20467](https://arxiv.org/pdf/2409.20467)

### Inferences
- Không có benchmark công khai đánh giá LLM trên teencode/slang giới trẻ (ví dụ "đi đâu chill chill, 2 đứa, tầm 1 củ/ng") ⇒ Tripflix phải tự xây bộ eval nhỏ (gợi ý 60–100 câu: slang tiền "củ/lít/k/xị", "/ng" vs tổng, số người "2 đứa/cả nhóm 5 mạng", vibe "chill/sống ảo/healing") và chấm exact-match theo field JSON. Đây cũng là bằng chứng "đo lường chất lượng" cho báo cáo.
- Pipeline lai: tiền xử lý deterministic (regex tiền tệ "1tr5", "500k", "1 củ"; từ điển slang ~100 mục lấy ý tưởng từ ViLexNorm/ViSoLex) → LLM nhỏ chỉ xử lý phần còn mơ hồ (vibe, per-person vs total). Điều này giảm token và tăng độ ổn định.
- Model Việt 7B tự host (Vistral/SeaLLM) không đáng trong 4 tuần: cần GPU, điểm VMLU thấp hơn model thương mại, và không có lợi thế slang đã được chứng minh.

### Gaps
- Không tìm được điểm VMLU chính thức hiện tại cho GPT-5.x, Claude 5.x, Gemini 3.x (vmlu.ai không fetch được).
- Không có đánh giá Sailor/Sailor2 hay PhoGPT trên VMLU trong kết quả tìm kiếm.
- Không có bằng chứng định lượng model nhỏ (Haiku/nano/Flash-Lite) xử lý teencode tốt hay kém — cần tự đo.
- Không có số liệu về "token inflation" của tiếng Việt (số token/từ) cho từng tokenizer — nên đo bằng endpoint đếm token.

## 4. Embedding cho tiếng Việt (văn bản) & CLIP/SigLIP cho ảnh

### Takeaway
Với vài nghìn post/sản phẩm, chi phí embedding qua API chỉ vài cent (≈$0.02/1M token cho text-embedding-3-small; $0.15/1M cho gemini-embedding-001); BGE-M3 (và bản fine-tune tiếng Việt) là lựa chọn self-host mạnh nhất cho tiếng Việt; cho ảnh, SigLIP 2 / Meta CLIP 2 (multilingual) tốt hơn CLIP gốc.

### Cited Findings
- VN-MTEB: benchmark embedding tiếng Việt (dịch từ MTEB), gồm clustering, classification, retrieval; model dựa trên Qwen2 nhìn chung vượt model nhỏ hơn; bge-m3 và multilingual-e5-large cho kết quả retrieval tốt — [arXiv 2507.21500 VN-MTEB](https://arxiv.org/pdf/2507.21500)
- AITeamVN/Vietnamese_Embedding: fine-tune từ BGE-M3 trên ~300K triplet tiếng Việt; model card báo baseline BGE-M3 MRR@10 = 0.6822 — [HF AITeamVN/Vietnamese_Embedding](https://huggingface.co/AITeamVN/Vietnamese_Embedding)
- BGE-M3 model card — [HF BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3)
- ViRanker: cross-encoder reranker tiếng Việt dựa trên BGE-M3, cạnh tranh với PhoRanker trên MMARCO-VI — [arXiv 2509.09131](https://arxiv.org/pdf/2509.09131)
- Benchmark (tiếng Anh, không phải tiếng Việt): multilingual-e5-large 0.628 vs BGE-M3 dense 0.614 retrieval — [AIMultiple](https://aimultiple.com/open-source-embedding-models)
- text-embedding-3-small ≈ $0.02/1M; MIRACL (đa ngôn ngữ) 44.0 so với 3-large 54.9 — [Layer3labs](https://www.layer3labs.io/guides/openai-embedding-models-pricing); [PromptLayer](https://blog.promptlayer.com/text-embedding-3-small-high-quality-embeddings-at-scale/) (nguồn thứ cấp)
- gemini-embedding-001 $0.15/1M — [Google Developers Blog](https://developers.googleblog.com/gemini-embedding-available-gemini-api/); Gemini Embedding 2 hỗ trợ cả ảnh ($0.45/1M token ảnh) — [Tokencost](https://tokencost.app/blog/gemini-embedding-2-pricing)
- SigLIP 2 vượt SigLIP và CLIP trên dữ liệu đa ngôn ngữ, tiệm cận mSigLIP trên Crossmodal-3600 — [arXiv 2502.14786 SigLIP 2](https://arxiv.org/pdf/2502.14786); mâu thuẫn: Meta CLIP 2 cho rằng SigLIP 2 ưu tiên tiếng Anh (~90% dữ liệu) và đa ngôn ngữ kém hơn mSigLIP, còn Meta CLIP 2 ViT-H/14 đạt SOTA đa ngôn ngữ trên XM3600 — [arXiv 2507.22062 Meta CLIP 2](https://arxiv.org/html/2507.22062v3)

### Inferences
- Lựa chọn "đủ dùng" cho demo: (a) API: text-embedding-3-small hoặc gemini-embedding-001 — không cần GPU, chi phí ~0; hoặc (b) self-host BGE-M3 (~568M tham số) trên CPU cho vài nghìn tài liệu là khả thi (embedding offline một lần; query embedding vài trăm ms trên CPU — ước lượng, cần đo). Self-host tránh phụ thuộc mạng/quota nhưng tốn RAM và công vận hành.
- Search nên là hybrid: BM25/full-text (Postgres) + vector (pgvector) + filter có cấu trúc từ bước query understanding; LLM không cần cho mỗi lượt search.
- Ảnh (B2): image→image similarity chỉ cần image encoder (SigLIP/CLIP) embed offline + ANN; với text→image tiếng Việt nên ưu tiên model multilingual (SigLIP 2 / mSigLIP / Meta CLIP 2) và đánh giá trên 20–30 truy vấn tự tạo.

### Gaps
- Không có điểm VN-MTEB cụ thể từng model (không fetch được paper) và không có so sánh trực tiếp OpenAI/Gemini embedding trên tiếng Việt.
- Không có số latency CPU đo được cho BGE-M3/SigLIP trên cấu hình máy sinh viên.

## 5. Pattern tránh overkill: routing/cascade, caching, parser deterministic, rate limit

### Takeaway
Cascade/routing có thể giảm 2× tới >50% chi phí trên benchmark của tác giả, nhưng với Tripflix lợi ích lớn nhất đến từ việc *không gọi LLM* khi rule/parser đủ dùng, cache prompt cố định, và chặn hạn mức mỗi user.

### Cited Findings
- FrugalGPT (cascade): tiết kiệm 50%–98% tùy dataset (con số "98%" thường bị trích quá mức) — [arXiv 2305.05176 FrugalGPT](https://arxiv.org/pdf/2305.05176)
- RouteLLM: tác giả mô tả giảm chi phí "hơn 2 lần trong một số trường hợp" mà không giảm chất lượng; con số ">85%" là cách diễn đạt khác của blog thứ cấp — [LLM routing overview](https://intuitionlabs.ai/articles/ai-model-routing-cost-quality); [NeuralTrust](https://neuraltrust.ai/blog/llm-model-routing)
- Semantic cache: số liệu hit-rate duy nhất tìm được là vendor tự báo cáo 21% trung bình — [Portkey FrugalGPT blog](https://portkey.ai/blog/implementing-frugalgpt-smarter-llm-usage-for-lower-costs/)
- Prompt caching Anthropic: cache hit 0.1× (0.05× trên Sonnet/Opus 5.5), hòa vốn sau 1 lần đọc với TTL 5 phút — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- (Tài liệu tham chiếu Claude API, cache 2026-10-06) Trước khi xây cascade nhiều model, nên đo phương án đơn giản hơn là một model với effort thấp; cache là theo-model nên cascade làm mất tái sử dụng cache giữa các model — kiểm tra tại [Prompt caching docs](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).

### Inferences
Kiến trúc đề xuất theo tác vụ (suy luận cho Tripflix):
| Tác vụ | Mặc định | Khi nào gọi LLM | Model |
|---|---|---|---|
| (1) Query understanding | Regex/từ điển slang → JSON filter | Chỉ khi parser không chắc (thiếu field, có "vibe") | Tier nhỏ, thinking tắt, structured output |
| (2) Assistant chat | — | Mỗi lượt chat; tool = search/filter/get_product | Tier nhỏ; fallback tier giữa nếu tool lỗi 2 lần/độ tin cậy thấp |
| (3) Tóm tắt B1 | Precompute khi có ≥N review mới | Batch, offline | Tier nhỏ + Batch (−50%) |
| (4) Semantic search | Embedding precompute + hybrid search | Không gọi LLM khi search | Embedding API / BGE-M3 |
| (5) Image similarity | SigLIP/CLIP offline + ANN | Không | — |
| (6) Moderation | Blocklist từ tục/teencode + Moderation API miễn phí | LLM nhỏ chỉ cho ca xám | omni-moderation / tier nhỏ |
| (7) Lý do gợi ý | Template từ feature ("Hợp nhóm 4 người, dưới 500k/ng, gần biển") | Tùy chọn: precompute batch | Tier nhỏ + Batch |
- Response cache khóa theo (normalized query → filter JSON) cho tác vụ (1): truy vấn demo lặp lại nhiều nên exact cache sau chuẩn hóa đủ dùng; semantic cache không cần thiết cho demo.
- Token budgeting: giới hạn history (ví dụ 6 lượt gần nhất), tool result trả về tối đa 5 item với field rút gọn, `max_tokens` output ~400 cho chat, ~150 cho JSON.
- Rate limit theo user: ví dụ 20 tin nhắn/ngày/user + giới hạn chi phí cứng/ngày cho toàn hệ thống (circuit breaker) — tham số giả định.

### Gaps
- Không có số hit-rate độc lập cho GPTCache/semantic cache; không có nghiên cứu cascade trên tiếng Việt.

## 6. Đo lường & observability chi phí

### Takeaway
Chuẩn tối thiểu: log mỗi lượt gọi với `gen_ai.usage.input_tokens`/`output_tokens` (+ cache read/write, thinking) theo OpenTelemetry GenAI semantic conventions, nhân với bảng giá có ngày hiệu lực để ra cost/call, rồi tổng hợp cost/conversation, cost/user, p50/p95 latency; Langfuse nhận OTLP và hiển thị sẵn các số này.

### Cited Findings
- OTel GenAI conventions định nghĩa `gen_ai.usage.input_tokens` và `gen_ai.usage.output_tokens` làm thuộc tính token cốt lõi; chi phí (USD) KHÔNG nằm trong spec — các ví dụ dùng tên tự đặt như `gen_ai.cost.total` — [OpenObserve guide](https://openobserve.ai/blog/opentelemetry-genai-semantic-conventions/); [OneUptime](https://oneuptime.com/blog/post/2026-02-06-genai-semantic-conventions-llm-monitoring/view); [Uptrace](https://uptrace.dev/blog/opentelemetry-ai-systems)
- Langfuse cung cấp endpoint OTLP HTTP (không hỗ trợ gRPC trên endpoint đó) và map `gen_ai.*` vào mô hình trace của nó; Langfuse v3 SDK là OTel-native — [Agentic AI KB: OTel GenAI conventions](https://agentic-ai.readthedocs.io/en/latest/Observability/otel-genai-conventions/); [DEV: OTel GenAI conventions](https://dev.to/x4nent/opentelemetry-genai-semantic-conventions-the-standard-for-llm-observability-1o2a)
- Anthropic API trả về usage gồm `input_tokens`, `output_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens`; trang giá nêu các hệ số cache/batch/geo cộng dồn → cần đủ 4 trường để tính đúng chi phí — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)

### Inferences
- Schema log tối thiểu (bảng `ai_calls`): `ts, request_id, conversation_id, user_id, task (query_parse|chat|summarize|moderate|reason|embed), provider, model, input_tokens, cached_read_tokens, cache_write_tokens, output_tokens, thinking_tokens (nếu có), ttft_ms, total_ms, status, fallback_used, cost_usd (tính từ bảng price_table có cột effective_date)`.
- Dashboard báo cáo theo spec: tổng chi phí; cost/1.000 lượt chat; cost/conversation p50/p95; tỷ lệ cache hit; tỷ lệ query giải quyết bằng parser (không gọi LLM); TTFT p50/p95. Có thể làm bằng một view SQL — Langfuse (self-host hoặc cloud free tier) là tùy chọn nếu nhóm muốn trace UI.
- Đối chiếu số log với hóa đơn/console của provider ở cuối demo để chứng minh độ chính xác.

### Gaps
- Không đọc được tài liệu chính thức Langfuse/Helicone (cách khai báo giá model tùy chỉnh, free tier); không tìm thấy nguồn đáng tin về cách Helicone đọc `gen_ai.usage.*`.

## 7. Template ước tính chi phí (công thức + ví dụ — TẤT CẢ là GIẢ ĐỊNH)

### Takeaway
Với giả định 500 user × 10 tin nhắn, assistant chat tốn ≈ $4 trên Claude Haiku 5.5, ≈ $82 trên Sonnet 5.5, ≈ $164 trên Opus 5.5; các tác vụ khác (parse, tóm tắt, embedding, moderation, lý do gợi ý) cộng lại < $1 — chi phí demo bị chi phối hoàn toàn bởi lựa chọn model cho chat.

### Cited Findings
- Giá dùng trong tính toán (Anthropic, chính thức): Haiku 5.5 in $0.10 / cache write 5m $0.125 / cache hit $0.01 / out $0.50; batch $0.05/$0.25. Sonnet 5.5 in $2 / write $2.50 / hit $0.10 / out $10. Opus 5.5 in $4 / write $5 / hit $0.20 / out $20 — [Anthropic Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- Giá so sánh (thứ cấp, chưa xác minh): GPT-5 nano $0.05/$0.40 (cached $0.005); GPT-5 mini $0.25/$2.00 (cached $0.025) — [Silicondata](https://www.silicondata.com/use-cases/openai-api-pricing-per-1m-tokens); Gemini 3.1 Flash-Lite $0.25/$1.50 (cache ~$0.025) — [CloudZero](https://www.cloudzero.com/blog/gemini-pricing/); text-embedding-3-small $0.02/1M — [Layer3labs](https://www.layer3labs.io/guides/openai-embedding-models-pricing); omni-moderation miễn phí — [Analytics Vidhya](https://www.analyticsvidhya.com/blog/2026/05/openai-omni-moderation/)

### Inferences
**Công thức (mỗi lượt gọi model):**
```
cost_call = (T_in_uncached × P_in + T_cache_read × P_cache_read + T_cache_write × P_cache_write
             + (T_out_visible + T_thinking) × P_out) / 1e6        [× 0.5 nếu Batch; × 1.1 nếu inference_geo=us]
cost_turn = Σ cost_call trong turn  (chat có tool ≈ 2 call)
cost_total = Σ_task  N_events(task) × cost_per_event(task)
cost_ceiling_per_user = rate_limit_msgs_per_day × cost_turn_p95
```

**Giả định traffic (GIẢ ĐỊNH):** 500 user demo × 10 tin nhắn chat = 5.000 turn; 5.000 query search; 2.000 post/sản phẩm; 300 post cần tóm tắt.

**Giả định token cho 1 turn chat (GIẢ ĐỊNH):** 2 call/turn. Mỗi call: prefix cố định 2.000 token (system + tool defs + 286 token tool system prompt) → cache read; phần động 2.000 token (history + tool result) → uncached. Output: 350 token visible + 400 token thinking/turn = 750. Cache write: 1 lần/conversation × 2.000 token.

Per turn: uncached input 4.000; cache read 4.000; output 750.

| Model chat | Input 4.000 | Cache read 4.000 | Output 750 | /turn | ×5.000 turn | Cache write (500 × 2.000) | Tổng chat |
|---|---|---|---|---|---|---|---|
| Claude Haiku 5.5 | $0.00040 | $0.00004 | $0.000375 | $0.000815 | $4.08 | $0.13 | **≈ $4.2** |
| Claude Sonnet 5.5 | $0.0080 | $0.0004 | $0.0075 | $0.0159 | $79.5 | $2.50 | **≈ $82** |
| Claude Opus 5.5 | $0.0160 | $0.0008 | $0.0150 | $0.0318 | $159 | $5.00 | **≈ $164** |
| GPT-5 nano (giá chưa xác minh) | $0.00020 | $0.00002 | $0.00030 | $0.00052 | $2.6 | n/a | ≈ $2.6 |
| GPT-5 mini (giá chưa xác minh) | $0.00100 | $0.00010 | $0.00150 | $0.0026 | $13 | n/a | ≈ $13 |
| Gemini 3.1 Flash-Lite (giá chưa xác minh) | $0.00100 | $0.00010 | $0.001125 | $0.0022 | $11.1 | n/a | ≈ $11 |

**Các tác vụ khác (Haiku 5.5, GIẢ ĐỊNH):**
- (1) Query understanding: 5.000 query, mỗi query 600 token prefix cache + 200 token động + 150 output → (200×0.10 + 600×0.01 + 150×0.50)/1e6 = $0.000101/query ⇒ **≈ $0.50** (nếu parser xử lý 50% query không cần LLM ⇒ ≈ $0.25).
- (3) Tóm tắt B1 qua Batch: 300 post × (1.500 in + 200 out) → (450.000×0.05 + 60.000×0.25)/1e6 ⇒ **≈ $0.04**.
- (4) Embedding (text-embedding-3-small): 2.000 × 300 token + 5.000 × 20 token = 0,7M token ⇒ **≈ $0.014** (gemini-embedding-001: ≈ $0.11; BGE-M3 self-host: $0 API).
- (5) Image embedding: self-host SigLIP/CLIP ⇒ $0 API.
- (6) Moderation: omni-moderation ⇒ $0 (theo nguồn thứ cấp); nếu dùng Haiku 5.5 cho 1.000 nội dung × (300 in + 30 out) ⇒ ≈ $0.045.
- (7) Lý do gợi ý precompute Batch: 2.000 × (500 in + 60 out) ⇒ (1M×0.05 + 120K×0.25)/1e6 ⇒ **≈ $0.08**.

**Tổng ước tính demo:** ≈ $5 (Haiku 5.5 cho mọi thứ) / ≈ $83 (Sonnet 5.5 cho chat, phần còn lại Haiku) / ≈ $165 (Opus 5.5 cho chat).

**Độ nhạy (sensitivity):** chi phí chat tỷ lệ tuyến tính với số turn và với (thinking + output); nếu thinking tăng lên 2.000 token/turn, Haiku 5.5 chat ≈ $0.0015/turn ≈ $7.6 tổng; nếu tool trả về 5.000 token thay vì 1.500 thì input động tăng ~2.75×. Tiếng Việt có thể tốn nhiều token hơn tiếng Anh cho cùng nội dung và tokenizer Haiku 5.5 đếm nhiều hơn ~30% so với Haiku 4.5 — phải đo bằng token counting thay vì đoán.

### Gaps
- Số token/turn, tỷ lệ thinking, số call/turn đều là giả định — cần đo trên 50–100 hội thoại thử để thay thế.
- Giá OpenAI/Gemini trong bảng chưa xác minh từ trang chính thức (host bị chặn).
- Rate limit của tài khoản mới (RPM/TPM) có thể là ràng buộc thật khi demo đông người dùng đồng thời — không có nguồn trong phiên này.
