# Kiến trúc nền tảng short-video (TikTok/Douyin, YouTube Shorts, Instagram Reels, Kuaishou) — tham chiếu cho module Social & Search của Tripflix

> Phạm vi: feed/recommendation, video pipeline, interaction counters, feed API, trending & chống gian lận.
> Bối cảnh áp dụng: Tripflix = feed video dọc kiểu TikTok + OTA booking Vinpearl; người dùng trẻ Việt Nam, Android cấu hình thấp, mạng yếu; đội 7 sinh viên, demo trong 4 tuần, modular monolith + PostgreSQL + Redis + object storage + CDN.
>
> **Lưu ý về phương pháp:** Trong phiên nghiên cứu này, WebFetch bị chặn với hầu hết domain (arxiv.org, newsroom.tiktok.com, developers.tiktok.com, substack...) do giới hạn mạng của session. Các finding dưới đây dựa trên snippet/tóm tắt từ WebSearch trỏ tới nguồn gốc; mọi con số đều kèm URL nguồn gốc. Những chi tiết không xác minh được qua search đều được đưa vào mục *Gaps*. Nên mở trực tiếp các URL nguồn chính (đặc biệt Monolith PDF) để kiểm chứng trước khi trích dẫn trong báo cáo cuối.

---

## 1. Kiến trúc recommendation của TikTok/Douyin (candidate generation → ranking → re-ranking, Monolith, streaming signals, cold start, diversity)

### Takeaway
Các nền tảng short-video đều dùng pipeline nhiều tầng (召回 recall → 粗排 pre-rank → 精排 rank → 重排 re-rank) với tín hiệu hành vi (watch/completion, like, share, comment, follow, "Not interested") chảy gần real-time qua Kafka → Flink → online training (Monolith của ByteDance); tầng re-rank cuối áp các luật "không lặp video đã xem, không 2 video liền nhau cùng creator/sound, chèn nội dung đa dạng". Với Tripflix, chỉ cần giữ **khung 3 bước (candidate → score → re-rank/filter)** bằng SQL + Redis, thay ML bằng công thức điểm có trọng số; không cần embedding table hay online training.

### Cited Findings

**Pipeline nhiều tầng (chuẩn công nghiệp)**
- YouTube (Covington et al., RecSys 2016) chia hệ thống theo "two-stage information retrieval dichotomy": một mô hình deep *candidate generation* rồi một mô hình deep *ranking* riêng — [Google Research: Deep Neural Networks for YouTube Recommendations](https://research.google/pubs/pub45530)
- Mô tả chuỗi 4 tầng phổ biến trong ngành TQ (áp dụng cho Douyin/Kuaishou-style): recall dùng đa kênh (collaborative filtering, interest tags, hot content, địa lý…) chạy song song; recall trả về ~10K–100K ứng viên, pre-rank dùng mô hình nhẹ (thường two-tower, vector item tính sẵn offline) lọc còn ~1K; rank dùng DNN + feature crossing + multi-task; re-rank xử lý đa dạng, dedup, "打散" (tách rời nội dung cùng loại), chèn slot vận hành. **Nguồn là blog bên thứ ba, không phải tài liệu chính thức của ByteDance** — [Juejin: 万字拆解推荐算法](https://juejin.cn/post/7651927500886392859); [Juejin](https://juejin.cn/post/7560517862835470345)
- Kuaishou mô tả cascade truyền thống gồm "retrieval, pre-ranking, and ranking, each potentially employing multiple strategies or models", và lập luận cascade gây lãng phí: >50% tài nguyên serving dành cho communication & storage thay vì tính toán — [OneRec Technical Report, arXiv 2506.13695](https://arxiv.org/abs/2506.13695)
- OneRec (Kuaishou) thay cascade bằng 1 mô hình generative encoder-decoder; triển khai trên app Kuaishou/Kuaishou Lite, xử lý 25% QPS, tăng App Stay Time 0.54% và 1.24%; OPEX chỉ còn 10.6% so với pipeline truyền thống — [arXiv 2506.13695](https://arxiv.org/abs/2506.13695). Bản OneRec trước (02/2025) báo +1.6% watch-time ở main scene — [arXiv 2502.18965](https://arxiv.org/abs/2502.18965)
- Kuaishou TWIN: mô hình lifelong user-behavior 2 tầng (coarse search trên lịch sử dài → fine-grained scoring), tăng Watch Time +4.893% đến +6.249% so với SIM Hard; giảm 99.3% bottleneck tính toán để deploy được — [TWIN, arXiv 2302.02352](https://arxiv.org/abs/2302.02352)

**Monolith (ByteDance, arXiv 2209.07663)**
- Mục tiêu: hệ thống thiết kế cho *online training*, rút ngắn khoảng cách giữa feedback lúc serving và cập nhật mô hình — [Monolith PDF](https://arxiv.org/pdf/2209.07663)
- *Collisionless embedding table*: dùng hash table không va chạm (Cuckoo hashing, lookup/delete worst-case O(1)) thay vì hashing trick có collision, để giữ đủ "expressive power" cho sparse features (user ID, video ID) — [Monolith PDF](https://arxiv.org/pdf/2209.07663)
- Kiểm soát bộ nhớ bằng *expirable embeddings* (embedding lâu không dùng thì bị loại) và *frequency filtering* (ID xuất hiện quá ít lần chưa được cấp embedding) — [Monolith PDF](https://arxiv.org/pdf/2209.07663)
- Luồng streaming: 1 Kafka queue log user actions + 1 Kafka queue cho features → Flink streaming job làm *online feature joiner* (nối feature với label từ action) → ghi training examples vào Kafka queue khác, được dùng cho cả online training và batch training — [Monolith PDF](https://arxiv.org/pdf/2209.07663)
- Training-PS cập nhật tham số từ stream real-time rồi đồng bộ sang Serving-PS theo chu kỳ, *incremental*: chỉ gửi các key đã được train kể từ lần sync trước (hash set "touched keys") để nhẹ băng thông và tránh spike bộ nhớ — [Monolith PDF](https://arxiv.org/pdf/2209.07663)
- Kết quả: sync interval càng ngắn thì chất lượng mô hình càng tốt; hệ thống đã tích hợp vào BytePlus Recommend — [Monolith PDF](https://arxiv.org/pdf/2209.07663); [Analytics India Mag](https://analyticsindiamag.com/tiktok-parent-bytedance-reveals-its-sota-recommendation-engine)
- Pattern Kafka + Flink event joining cho real-time model update cũng được ShareChat mô tả trong production — [Real-time Event Joining in Practice With Kafka and Flink, arXiv 2410.15533](https://arxiv.org/abs/2410.15533)

**Tín hiệu người dùng (TikTok công bố chính thức, 2020)**
- 3 nhóm tín hiệu: (1) *user interactions* — like, share, follow, comment, nội dung user tạo; (2) *video information* — caption, sound, hashtag; (3) *device & account settings* — ngôn ngữ, quốc gia, loại thiết bị (trọng số thấp hơn vì không phải sở thích chủ động) — [TikTok Newsroom: How TikTok recommends videos #ForYou](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)
- Tín hiệu mạnh: user có xem hết một video dài từ đầu đến cuối hay không; tín hiệu tiêu cực trực tiếp: long-press → "Not Interested" — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/); [Social Media Today](https://www.socialmediatoday.com/news/how-tiktoks-algorithm-works-as-explained-by-tiktok/580153/)
- Follower count và lịch sử video "hit" của tài khoản *không* phải yếu tố trực tiếp — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)

**Cold start**
- User mới: chọn category quan tâm lúc onboarding; nếu không chọn thì bắt đầu bằng "generalized feed of popular videos" — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)
- Video mới: video vừa upload hoặc đang review, và spam tìm cách tăng traffic nhân tạo, có thể *không đủ điều kiện* vào For You feed — [TikTok Newsroom: safeguard and diversify recommendations (12/2021)](https://newsroom.tiktok.com/an-update-on-our-work-to-safeguard-and-diversify-recommendations?lang=en)

**Diversity / dedup**
- "Our systems won't recommend two videos in a row made by the same creator or with the same sound"; ở bài 2020 dùng từ "generally" (không tuyệt đối) — [TikTok Newsroom 2021](https://newsroom.tiktok.com/an-update-on-our-work-to-safeguard-and-diversify-recommendations?lang=en); [TikTok Newsroom 2020](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)
- "We also don't recommend duplicated content, content you've already seen before, or any content that's considered spam" — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)
- Hệ thống chủ động "intersperse diverse types of content" bên cạnh nội dung user đã thích — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)

### Inferences (→ thiết kế cho Tripflix, module **Search** sở hữu Feed/Trending/Reco)
- **Giữ khung 3 tầng, thay ML bằng SQL/heuristic:**
  1. *Candidate generation* (đa kênh, mỗi kênh 1 query đơn giản, gộp ~200–500 ứng viên): (a) trending toàn cục từ Redis ZSET; (b) video gắn destination/khách sạn Vinpearl user đã xem/search/wishlist (tag matching); (c) video của creator user follow; (d) video mới (<48h) cần "thăm dò" (exploration pool); (e) video gắn hotel đang có khuyến mãi (slot vận hành/kinh doanh).
  2. *Scoring*: `score = w1·completion_rate + w2·like_rate + w3·share_rate + w4·booking_click_rate + w5·tag_affinity(user, video) + w6·freshness − w7·đã_skip_creator`. Trọng số hard-code trong config, tinh chỉnh tay. Đây là "ranking" của Tripflix.
  3. *Re-rank/filter*: loại video đã xem (seen set), loại video bị report/chưa duyệt, không 2 video liền nhau cùng creator hoặc cùng khách sạn/destination, chèn 1 video exploration mỗi ~5 vị trí, giới hạn tần suất video quảng cáo hotel.
- **Thay Monolith/Kafka/Flink:** ghi event (`view_start`, `watch_progress`, `complete`, `like`, `share`, `skip`, `click_booking`) vào bảng `interaction_events` (Postgres, append-only, partition theo ngày nếu cần) + cập nhật ngay Redis (counter, ZSET trending, user-affinity hash `user:{id}:tag_affinity`). "Near real-time" đạt được nhờ Redis cập nhật đồng bộ khi nhận event; job định kỳ (1–5 phút, cron/BullMQ/Celery beat) tổng hợp lại vào Postgres. Đây là phiên bản thu nhỏ của "action log → joiner → model update": affinity của user thay đổi trong vài giây, không cần training.
- **Ý tưởng mượn từ Monolith áp dụng được:** (a) *frequency filtering* ≈ chỉ tính tag affinity khi user có ≥N tương tác với tag đó (tránh nhiễu); (b) *expirable embeddings* ≈ cho affinity decay theo thời gian (TTL hoặc nhân hệ số 0.9/ngày) để sở thích cũ phai dần; (c) *incremental sync touched keys* ≈ job flush chỉ đọc set `dirty:videos` trong Redis thay vì quét toàn bộ.
- **Cold start Tripflix:** onboarding chọn 3–5 "travel vibe" (biển, núi, ẩm thực, gia đình, nightlife…) + vị trí/khu vực → map sang tag destination; user không chọn → feed trending + video Vinpearl được curate. Video mới: trạng thái `pending_review` không vào feed; sau duyệt vào "exploration pool" được chèn có kiểm soát cho một nhóm nhỏ user, nếu completion/like rate vượt ngưỡng thì được đẩy lên trending candidate (mô phỏng kiểu "traffic pool" — lưu ý cơ chế cụ thể của TikTok không được công bố, xem Gaps).
- **Kuaishou OneRec/TWIN** chỉ nên dùng làm minh chứng "xu hướng ngành" trong báo cáo, không áp dụng cho demo 4 tuần.

### Gaps
- Không mở được toàn văn Monolith (arxiv.org bị chặn trong session) nên **không xác minh được** tần suất sync cụ thể (sparse params mức phút, dense params thưa hơn) và các con số AUC/benchmark — cần đọc trực tiếp https://arxiv.org/pdf/2209.07663 mục "Parameter Synchronization" & "Experiments".
- TikTok không công bố cách chọn tập khán giả đầu tiên cho video mới (số user, ngưỡng đẩy tiếp). Khái niệm "流量池/traffic pool" phổ biến trên blog TQ nhưng không tìm thấy nguồn chính thức.
- Con số "user và video biểu diễn bằng vector 512 chiều" trên blog TQ là không chính thức, không nên trích.
- Không tìm được tài liệu chính thức về kiến trúc recommendation của Instagram Reels hoặc YouTube Shorts riêng (ngoài bài YouTube 2016 tổng quát).

---

## 2. Công thức trending/hot-score và chống thao túng (fake engagement, sybil, device fingerprinting, rate limit)

### Takeaway
Công thức hot-score kinh điển là "điểm tương tác chia cho hàm tăng theo tuổi bài": Hacker News `(P−1)/(T+2)^1.8` và Reddit `log10(votes) + seconds/45000`; Tripflix nên dùng biến thể có trọng số theo loại tương tác + decay theo giờ, tính lại định kỳ vào Redis ZSET. Chống gian lận ở quy mô TikTok là bài toán lớn (hàng trăm triệu tài khoản giả/quý); Tripflix chỉ cần idempotent interaction, rate limit, trọng số thấp cho tài khoản mới/chưa xác thực và log device ID.

### Cited Findings
- **Hacker News:** `Score = (P−1) / (T+2)^G`, P = points, T = số giờ từ lúc đăng, G (gravity) mặc định 1.8 theo news.arc; phiên bản cập nhật của Paul Graham có `gravity* = 1.8, timebase* = 120` (phút) — [righto.com: How Hacker News ranking really works](https://www.righto.com/2013/11/how-hacker-news-ranking-really-works.html); [Medium: How HN ranking algorithm works](https://medium.com/hacking-and-gonzo/how-hacker-news-ranking-algorithm-works-1d9b0cf2c08d)
- **Reddit hot:** `round(order + sign·seconds/45000, 7)` với `order = log10(max(|ups−downs|,1))`, `seconds = post_time − 1134028003` (epoch 08/12/2005); 45000 giây = 12.5 giờ ⇒ cứ 12.5 giờ tuổi tương đương 1 bậc log (10× số vote). Hệ quả: 10 upvote đầu có trọng lượng bằng 100 upvote tiếp theo. Code gốc ở `r2/r2/lib/db/_sorts.pyx` (repo reddit/reddit), các nguồn tìm được là bản viết lại — [gist alfg/2484505](https://gist.github.com/alfg/2484505); [gist hellokaton](https://gist.github.com/hellokaton/9ab8d9e557c832632f822005f229c9d0); [Habr](https://habr.com/ru/post/150700/)
- **Lemmy:** `Rank = ScaleFactor · log(max(1, 3 + Score)) / (Time + 2)^Gravity`, gravity 1.8 — kết hợp log (như Reddit) với decay lũy thừa (như HN) — [Lemmy docs: ranking algorithm](https://join-lemmy.org/docs/contributors/07-ranking-algo.html)
- **Quy mô fake engagement ở TikTok:** Q4/2025 TikTok xóa 147,716,518 tài khoản giả; số like/follower giả bị xóa tăng — [Dhaka Tribune: TikTok Q4 2025 CGE report](https://www.dhakatribune.com/feature/tech/408119/tiktok-releases-q4-2025-community-guidelines). Q2/2021: chặn 148,759,987 tài khoản giả ngay lúc tạo, xóa 71.9 triệu tài khoản giả, xóa 9.6 tỷ like giả — [Tubefilter](https://www.tubefilter.com/2021/10/14/tiktok-community-guidelines-enforcement-report-april-june-2021/). Q2/2025 (Mỹ): phá 30 mạng lưới thao túng engagement, xóa >76,000 tài khoản và 4.8 triệu post — [Social Media Today](https://www.socialmediatoday.com/news/tiktok-us-shares-q2-account-enforcement-data/833907/)
- Chính sách TikTok cấm tài khoản thao túng nền tảng và mua bán dịch vụ tăng engagement nhân tạo/"game the recommendation system"; TikTok dựa vào "automated defenses" để phát hiện, chặn, xóa tài khoản và engagement không xác thực — [ConductAtlas tóm tắt Community Guidelines](https://conductatlas.com/platform/tiktok/tiktok-community-guidelines/fake-engagement-and-deceptive-behavior-prohibition/); [Dhaka Tribune](https://www.dhakatribune.com/feature/tech/408119/tiktok-releases-q4-2025-community-guidelines)
- Video spam "seeking to artificially increase traffic" bị loại khỏi điều kiện vào For You — [TikTok Newsroom 2021](https://newsroom.tiktok.com/an-update-on-our-work-to-safeguard-and-diversify-recommendations?lang=en)

### Inferences (→ module **Search**: Trending)
- **Công thức đề xuất cho Tripflix (kết hợp HN + Reddit + trọng số short-video):**
  `engagement = 1·views_qualified + 3·likes + 5·comments + 8·shares + 10·booking_clicks + 4·completions`
  `hot = log10(max(engagement, 1)) / (age_hours + 2)^1.5`
  (log để chống "1 video viral nuốt hết"; gravity 1.5 hơi thấp hơn HN vì nội dung du lịch "sống" lâu hơn tin tức). Có thể thêm biến thể *velocity*: `engagement_last_6h / max(engagement_prev_6h, k)` để bắt video đang tăng tốc.
  Tính lại mỗi 1–5 phút bằng job, ghi vào `ZADD trending:global` và `trending:dest:{destination_id}` (trending theo điểm đến — rất hợp OTA); feed đọc `ZREVRANGE`.
- `views_qualified` nên là view ≥3s hoặc ≥50% thời lượng (tương tự "engaged views" của YouTube, xem mục 4) để view-bot ngắn hạn không đẩy được trending.
- **Anti-manipulation phù hợp quy mô demo:**
  - Idempotency: `UNIQUE(user_id, video_id)` cho like; view tính tối đa 1 lần/user/video/khoảng 30 phút (Redis `SET view:{uid}:{vid} 1 NX EX 1800`).
  - Rate limit theo user + IP + device_id (Redis token bucket/fixed window: vd. ≤60 like/phút, ≤5 upload/giờ).
  - Trọng số tin cậy: tài khoản <24h hoặc chưa xác thực SĐT/email thì tương tác nhân 0.2–0.5 khi tính trending (không ảnh hưởng counter hiển thị).
  - Ghi `device_id` (Android ID/app-instance UUID) + IP vào event để sau này phát hiện nhiều tài khoản/1 thiết bị (sybil); demo chỉ cần log + 1 truy vấn báo cáo, không cần ML.
  - Trending không bao gồm video `pending_review`/bị report vượt ngưỡng.

### Gaps
- Không tìm được công thức trending chính thức của TikTok/Douyin/YouTube/Instagram (không công bố).
- Không tìm được tài liệu kỹ thuật công khai về device fingerprinting của TikTok; số liệu enforcement đến từ báo cáo minh bạch qua báo chí (nên kiểm chứng tại TikTok Transparency Center).
- Bản gốc `_sorts.pyx` của Reddit không được truy xuất trực tiếp; Reddit đã thay đổi thuật toán theo thời gian nên công thức chỉ mang tính lịch sử/tham khảo.

---

## 3. Pipeline upload & phân phối video (resumable upload, transcode HLS/DASH ABR ladder, thumbnail, CDN, prefetch, tối ưu cho mạng yếu/máy yếu)

### Takeaway
Chuẩn ngành: upload resumable (tus hoặc S3 multipart với presigned URL) → transcode bất đồng bộ ra nhiều rendition (ABR ladder, đáy ~234p/145 kbps) + thumbnail → phục vụ HLS qua CDN → client prefetch đoạn đầu của vài video kế tiếp. Nghiên cứu về Douyin/Kuaishou/TikTok cho thấy prefetch cố định gây lãng phí dữ liệu đáng kể; với user Việt dùng 3G/4G yếu, Tripflix nên dùng ladder thấp (240p/360p/480p), prefetch ít (1–2 video, chỉ segment đầu) và adaptive theo mạng.

### Cited Findings
**Resumable upload**
- tus 1.0.0: client gửi `HEAD` tới upload URL để biết offset tiếp tục; server *luôn* trả `Upload-Offset` (kể cả 0 hoặc đã hoàn tất); client gửi `PATCH` từ offset đó với `Content-Type: application/offset+octet-stream`; offset mới = offset cũ + số byte server đã nhận; PATCH tới resource không tồn tại → 404 — [tus protocol 1.0.0](https://github.com/tus/tus-resumable-upload-protocol/blob/main/protocol.md); [tus.io](https://www.tus.io/protocols/resumable-upload)
- Lệch offset → 409 Conflict, không sửa resource (chi tiết response kèm offset hiện tại nằm trong một nhánh extension cộng đồng, không phải spec chính) — [tus extension branch](https://github.com/felix-schwarz/tus-resumable-upload-protocol/blob/extension/partial-checksum/protocol.md)
- Ý tưởng tus đang được chuẩn hóa ở IETF HTTP WG (draft "Resumable Uploads", HEAD lấy offset, PATCH với `Upload-Offset`) — vẫn là draft — [IETF draft-ietf-httpbis-resumable-upload](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-resumable-upload-00)
- S3 multipart: 1–10,000 part, mỗi part 5 MB–5 GB; một PUT presigned đơn tối đa 5 GB; object lớn hơn cần multipart với presigned URL cho từng part. **Nguồn bên thứ ba, cần đối chiếu tài liệu AWS** — [fast.io: S3 presigned URL file size limit](https://fast.io/resources/s3-presigned-url-file-size-limit/); [AWS re:Post](https://repost.aws/questions/QUTjIMexV8TUqw539RLmEUZw/how-to-limit-s3-mutlipart-upload-filesize) (luồng: server tạo multipart upload rồi cấp presigned URL từng part cho client)

**ABR ladder**
- Ladder ví dụ từ Apple HLS Authoring Spec (theo trích dẫn của Unified Streaming): bậc thấp nhất 416×234 @ 145 kb/s, 640×360 @ 365 kb/s; Unified Streaming nhấn mạnh đây là *ví dụ*, ladder nên phụ thuộc nội dung — [Unified Streaming best practice](https://docs.unified-streaming.com/best-practice/content-preparation/improving-experience-recommendations.html); [Apple HLS Authoring Specification](https://developer.apple.com/documentation/http-live-streaming/hls-authoring-specification-for-apple-devices)
- Ví dụ stream của Apple: 416×234 @ 145 kbps là bậc thấp nhất ở nhiều stream; 640×360 dùng 340 kbps/660 kbps/730 kbps/1.2 Mbps tùy stream (365 kbps xuất hiện ở 480×270) — tức là có sai khác giữa nguồn thứ cấp và ví dụ của Apple — [Apple Streaming Examples](https://developer.apple.com/streaming/examples/)

**Instagram/Meta: encoding cho thiết bị yếu**
- Instagram tái cấu trúc cách tạo "basic ABR encodings", giảm 94% chi phí tạo chúng; dồn tài nguyên tiết kiệm sang advanced encodings, tăng 33% watch-time coverage của advanced encodings — [Meta Engineering: Instagram video processing encoding reduction (2022)](https://engineering.fb.com/2022/11/04/video-engineering/instagram-video-processing-encoding-reduction/)
- Basic (minimum-functionality) encodings tương thích mọi client Instagram; nén kém hiệu quả hơn nhưng *dễ decode hơn trên máy cũ* — [Meta Engineering (2022)](https://engineering.fb.com/2022/11/04/video-engineering/instagram-video-processing-encoding-reduction/)
- Meta dùng AV1 cho Reels với decoder phần mềm dav1d để đưa chất lượng tốt hơn tới máy Android low-end không có hardware AV1; thách thức lớn nhất là phân mảnh thiết bị (từ Galaxy Y đơn nhân đến Galaxy S22); thiết bị không có hardware decode thì software decoder tốn CPU/memory bandwidth — [Meta Engineering: AV1 for Reels (2023)](https://engineering.fb.com/2023/02/21/video-engineering/av1-codec-facebook-instagram-reels/); [AOMedia case study](https://aomedia.org/av1-adoption-showcase/meta-story)

**Prefetch/preload trong short-video**
- Nghiên cứu (2025, có tác giả thuộc team network của TikTok) đo Douyin và Kuaishou: cả hai prefetch *chunk đầu* của các video sắp tới vào buffer cục bộ; tác giả cho rằng cách này gây "remarkable video stalls and prefetched but discarded chunks" (lãng phí dữ liệu lớn) — [The Register (23/09/2025)](https://www.theregister.com/2025/09/23/short_video_prefetch_research/)
- Preload kích thước & thứ tự cố định gây stall cho user hay xem hết video, và lãng phí băng thông cho user hay lướt nhanh; DeLoad (ByteDance, triển khai trên Douyin) giảm rebuffering và giảm 3.76% băng thông; phương pháp network-aware báo giảm lãng phí 37–52% so với phương pháp khác (phần lớn là mô phỏng/lab) — [arXiv 2209.02927: Network-aware Prefetching for Short-Form Video](https://arxiv.org/pdf/2209.02927); [arXiv 2206.09839: Bandwidth-Efficient Multi-video Prefetching](https://arxiv.org/pdf/2206.09839)
- Nghiên cứu Dashlet đo TikTok: tải một chunk cố định của **5 video kế tiếp** bất kể mạng hay hành vi user ⇒ phạt QoE và tốn dữ liệu (thông tin lấy từ snippet tổng hợp; tham chiếu Dashlet, NSDI 2024 — URL gốc chưa xác minh trực tiếp: https://www.usenix.org/conference/nsdi24/presentation/li-zhuqi)
- Patent mô tả việc video đã pre-buffer bị bỏ qua khi user lướt là lãng phí tài nguyên — [USPTO patent 12732671](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/12732671)

### Inferences (→ module **Social**: video upload/processing)
- **Upload:** Dùng S3/MinIO multipart với presigned URL (part 5 MB) — client Android gửi từng part, lưu `upload_id` + danh sách ETag để resume khi rớt mạng; server chỉ cấp URL và gọi `CompleteMultipartUpload`. Nếu muốn đơn giản hơn: `tusd` (server tus có sẵn, backend S3) + thư viện tus-android-client. Giới hạn video ≤60s, ≤100 MB; **nén phía client trước khi upload** (vd. 720p, ~2 Mbps) để giảm thời gian upload trên mạng yếu.
- **Processing (async):** sau khi upload xong → ghi job vào hàng đợi (Redis list/BullMQ/Celery, hoặc bảng `jobs` Postgres với `SELECT … FOR UPDATE SKIP LOCKED`) → worker FFmpeg tạo:
  - HLS ladder rút gọn 3 bậc, segment 2–4s, keyframe khớp ranh giới segment: **240p (~250 kbps), 360p (~500 kbps), 480p/540p (~900 kbps)**; tùy chọn 720p (~1.8 Mbps) cho Wi-Fi. Bậc đáy tham chiếu 234p@145 kbps của Apple.
  - 1 file MP4 360p "fallback" (progressive) cho máy không chơi được HLS ổn.
  - Thumbnail/poster JPEG/WebP (frame giây ~1) + ảnh nhỏ blur cho placeholder.
  - Codec H.264 baseline/main (decode phần cứng gần như mọi Android rẻ); **không** dùng AV1/HEVC cho demo — Meta cho thấy software AV1 decode tốn CPU trên máy yếu.
  - Trạng thái video: `uploading → processing → pending_review → published | rejected | failed`.
- **Delivery:** object storage + CDN (CloudFront/Cloudflare/BunnyCDN), cache dài hạn cho segment (immutable URL có hash), playlist `.m3u8` cache ngắn. Client Android dùng ExoPlayer/Media3 với `DefaultBandwidthMeter` và bắt đầu ở bậc thấp nhất (start low, switch up).
- **Prefetch có kiểm soát (điểm khác biệt quan trọng cho user Việt):** chỉ preload **segment đầu (~2–4s) của 1–2 video kế tiếp** ở bậc 240p/360p; dừng preload khi mạng là cellular yếu hoặc bật "Tiết kiệm dữ liệu"; hủy request preload khi user lướt qua. Cache đĩa giới hạn (vd. 100–200 MB LRU). Lý do: nghiên cứu cho thấy prefetch cố định 5 video của TikTok gây lãng phí lớn — với gói data rẻ/hạn mức của sinh viên, đây là tính năng UX đáng kể.
- **Feed response nên trả sẵn** `hls_url`, `mp4_fallback_url`, `poster_url`, `duration`, `width/height` để client không cần thêm round-trip.

### Gaps
- Không truy xuất được bảng ladder chính thức trong Apple HLS Authoring Spec (chỉ có trích dẫn thứ cấp) và tài liệu AWS MediaConvert/Mux về ladder; các bitrate đề xuất cho Tripflix ở trên là suy luận, cần test thực tế.
- Không tìm được tài liệu chính thức về bitrate ladder/codec mà TikTok hoặc YouTube Shorts dùng, cũng như thông tin về TikTok Lite.
- Không xác minh trực tiếp được giới hạn S3 multipart từ tài liệu AWS (5 MiB tối thiểu trừ part cuối, 10,000 part).

---

## 4. Interaction counters ở quy mô lớn (likes, views, comments)

### Takeaway
Pattern chuẩn: bản ghi tương tác per-user (idempotent, UNIQUE) là nguồn sự thật; counter hiển thị được *denormalize* lên bài viết và cache, chấp nhận eventual consistency. Instagram denormalize like count trên post và xóa cache counter mỗi lần có like; YouTube/TikTok/Reels đếm view mỗi lần bắt đầu/replay, tách "engaged view" cho mục đích kinh doanh. Tripflix: `likes(user_id, video_id)` UNIQUE + Redis `INCR` + flush định kỳ vào cột `like_count`.

### Cited Findings
- Instagram: counter là một trong những key được cache nhiều nhất; ví dụ like count trên post của Justin Bieber — họ **denormalize like counter trên post** (tăng giá trị trong DB mỗi like mới) thay vì `COUNT(*)`, và do vài phút đầu sau khi celeb đăng thì view & like đều spike, counter trong cache bị xóa mỗi lần like mới — [Instagram Engineering: Instagration pt 2 (2015)](https://instagram-engineering.com/instagration-pt-2-scaling-our-infrastructure-to-multiple-data-centers-5745cbad7834)
- Bài Medium "Justin Bieber problem" đưa ra số liệu (vd. nhanh hơn 100×) và trích dẫn ẩn danh không kiểm chứng được — **không nên dùng** — [Medium (độ tin cậy thấp)](https://medium.com/@AVTUNEY/how-instagram-solved-the-justin-bieber-problem-using-postgresql-denormalization-86b0fdbad94b)
- Denormalized counter thường được khuyến nghị cho OLTP khi count được đọc nhiều hơn ghi rất nhiều; với bài viral, pattern phổ biến là buffer ghi trong Redis rồi flush theo lô vào Postgres để tránh tranh chấp khóa trên một dòng (đây là pattern chung, không phải chi tiết xác nhận của Instagram) — [Tiger Data blog](https://www.tigerdata.com/blog/postgresql/rss/jonatas); [AlgoMaster: Designing a scalable likes counting system](https://blog.algomaster.io/p/designing-a-scalable-likes-counting-system)
- YouTube Shorts từ 31/03/2025 đếm view **mỗi lần Short bắt đầu phát hoặc replay**, không yêu cầu thời lượng tối thiểu; vẫn theo dõi riêng "engaged views" (xem đủ một số giây nhất định) — YPP eligibility và chia sẻ doanh thu dựa trên engaged views; thay đổi này đưa Shorts về cùng cách đếm với TikTok và Instagram Reels (đếm số lần start/replay) — [TechCrunch (26/03/2025)](https://techcrunch.com/2025/03/26/youtube-is-changing-how-youtube-shorts-views-are-counted); [RouteNote](https://routenote.com/blog/how-are-views-counted-shorts-tiktok-reels/)
- TikTok Display API trả `like_count`, `comment_count`, `share_count`, `view_count` như các field tùy chọn chỉ trả khi được yêu cầu qua `fields` — [TikTok Developers: Video List v1](https://developers.tiktok.com/doc/tiktok-api-v1-video-list/); [v2](https://developers.tiktok.com/doc/tiktok-api-v2-video-list)

### Inferences (→ module **Social**: interactions)
- **Schema đề xuất (Postgres):**
  - `video_likes(user_id, video_id, created_at, PRIMARY KEY(user_id, video_id))` — like idempotent: `INSERT … ON CONFLICT DO NOTHING`; chỉ khi thật sự insert (rowcount=1) mới `INCR` counter ⇒ double-tap/retry trên mạng yếu không đếm trùng. Unlike: `DELETE … RETURNING` rồi `DECR`.
  - `comments(id, video_id, user_id, parent_id NULL, body, created_at, status)` — 1 cấp reply là đủ cho demo.
  - `videos.like_count / comment_count / share_count / view_count` (denormalized, cập nhật bởi job flush).
  - `video_views` hoặc `interaction_events` append-only cho analytics/reco (không dùng để đếm trực tiếp khi hiển thị).
- **Luồng counter:** API ghi bản ghi per-user → `HINCRBY video:{id}:counters likes 1` + `SADD dirty:videos {id}` → job mỗi 30–60s đọc `dirty:videos`, ghi `UPDATE videos SET like_count = …` theo lô (giống "touched keys" của Monolith). Đọc: ưu tiên Redis hash, fallback cột Postgres. Nếu Redis mất dữ liệu: rebuild từ `COUNT(*)` trên bảng per-user (nguồn sự thật).
- **Với quy mô demo**, thậm chí `UPDATE videos SET like_count = like_count + 1` trực tiếp trong cùng transaction là đủ; Redis buffer chỉ cần nếu muốn trình diễn "kiến trúc scale được". Sharded counter (N dòng con cộng lại) **không cần** cho demo — chỉ nhắc như hướng mở rộng.
- **View:** client gửi `view_start` khi video bắt đầu phát (đếm public `view_count`, giống TikTok/Reels/Shorts) và `qualified_view` khi xem ≥3s hoặc ≥50% (dùng cho trending/reco, giống "engaged views"). Batch event phía client (gửi mỗi 10–20s hoặc khi lướt qua 5 video) để giảm số request trên mạng yếu; event mang `client_event_id` (UUID) để server dedup khi retry.
- **Hiển thị:** làm tròn kiểu "1,2K", "3,4Tr" ⇒ sai lệch vài giây/vài đơn vị do eventual consistency là vô hình với user; riêng trạng thái "tôi đã like" phải đúng ngay (optimistic UI + nguồn từ bảng per-user).

### Gaps
- Không tìm được tài liệu chính thức của TikTok hay YouTube về kiến trúc counter nội bộ (sharded counter, độ trễ hiển thị). Bài nổi tiếng về "YouTube 301 views" (đóng băng view để kiểm tra) không được tìm/xác minh trong phiên này.
- Instagram blog 2015 mô tả bối cảnh hạ tầng cũ; không rõ hiện tại còn áp dụng.

---

## 5. Hình dạng Feed API (cursor pagination, theo dõi "seen", trường request/response; tham khảo TikTok Display/Research API)

### Takeaway
API công khai của TikTok đều dùng mô hình `cursor` + `has_more` + `max_count` (Display API: cursor là timestamp ms, max 20; Research API: thêm `search_id` để giữ phiên truy vấn), và cảnh báo có thể trả ít hơn `max_count` do lọc trust & safety. Slack cũng chuyển từ offset sang cursor vì offset trôi trang và chậm ở trang sâu. Feed cá nhân hóa của Tripflix nên dùng "session cursor" (opaque) + seen set trong Redis thay vì offset.

### Cited Findings
- TikTok Display API v2 `POST /v2/video/list/`: trả danh sách video công khai của user, sắp theo `create_time` giảm dần; `max_count` mặc định 10, tối đa 20; `cursor` là timestamp UTC (ms) — truyền cursor sẽ trả các video đăng *trước* thời điểm đó; response có `has_more` (boolean) và `cursor`; trường dữ liệu chọn qua query string `?fields=cover_image_url,id,title,…` — [TikTok Developers: Video List v2](https://developers.tiktok.com/doc/tiktok-api-v2-video-list); [Display API get started](https://developers.tiktok.com/doc/display-api-get-started/)
- Docs v1 lưu ý: do luật trust & safety, có thể trả ít hơn `max_count` video ngay cả khi `has_more = true` — [TikTok Developers: Video List v1](https://developers.tiktok.com/doc/tiktok-api-v1-video-list/)
- TikTok Research API query videos: request đầu không gửi `cursor`/`search_id`; response trả `cursor`, `search_id`, `has_more` để dùng cho request tiếp (ví dụ tiếp theo gửi `max_count`, `cursor: 10`, `start_date`, `end_date`, `search_id`); `end_date` không quá 30 ngày sau `start_date`; có thể trả ít hơn `max_count` (video bị xóa/riêng tư). Default `max_count` có mâu thuẫn giữa trang spec (20) và get-started (10), max 100 — [TikTok Research API spec](https://developers.tiktok.com/docs/en/research-api-specs-query-videos); [Research API get started](https://developers.tiktok.com/docs/en/research-api-get-started)
- Slack: tiến hóa từ không phân trang → offset → cursor; cursor là con trỏ tới một item, request sau trả kết quả *sau* con trỏ; đánh đổi: cursor phải dựa trên cột (tổ hợp cột) duy nhất & tuần tự, implement bằng `WHERE id > …` nên hiệu quả trên dataset lớn; API trả `response_metadata.next_cursor`, chuỗi rỗng = hết — [Slack Engineering: Evolving API Pagination at Slack](https://slack.engineering/evolving-api-pagination-at-slack/)
- Nhược điểm offset: page drift (dữ liệu chèn mới làm lặp/mất item) và chi phí tăng ở offset sâu; cursor mất khả năng nhảy tới trang tùy ý — [Apidog: cursor vs offset](https://apidog.com/blog/cursor-vs-offset-pagination/)
- TikTok không đề xuất lại "content you've already seen before" — [TikTok Newsroom](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/)

### Inferences (→ hợp đồng API giữa **Search** (Feed) và **Social** (video/interaction))
- **Hai loại feed, hai kiểu cursor:**
  1. *Feed có thứ tự cố định* (video của 1 creator, video theo hotel/destination, comment list): keyset cursor `(created_at, id)` mã hóa base64 — đúng kiểu Display API/Slack.
  2. *Feed For You cá nhân hóa*: không có thứ tự ổn định ⇒ cursor là **opaque session token** (giống `search_id`): server tạo `feed_session_id`, lưu danh sách ứng viên đã xếp hạng vào Redis `feed:{session}` (LIST, TTL 30 phút) và trả trang kế bằng `LRANGE`; hết danh sách thì sinh batch mới, loại trừ seen set.
- **Seen set:** `SADD seen:{user_id} {video_id}` khi client báo `view_start`/khi trả về trong feed (tùy chọn chặt/lỏng); TTL 7–14 ngày hoặc giới hạn kích thước (giữ ~1–2K gần nhất bằng ZSET theo timestamp + `ZREMRANGEBYRANK`). Với user rất nhiều, có thể thay bằng Bloom filter (RedisBloom) — chỉ là hướng mở rộng.
- **Request đề xuất** `GET /v1/feed/for-you?cursor=<opaque>&limit=8&network=cellular&data_saver=1`
  (limit nhỏ 5–10 vì mỗi item nặng; `network`/`data_saver` để server chọn rendition mặc định).
- **Response đề xuất:**
  ```json
  {
    "items": [{
      "id": "vid_123", "type": "video",
      "author": {"id": "u_1", "name": "...", "avatar_url": "...", "is_following": false},
      "caption": "...", "hashtags": ["#phuquoc"],
      "media": {"hls_url": "...m3u8", "mp4_fallback_url": "...360p.mp4", "poster_url": "...webp",
                "duration_ms": 23000, "width": 720, "height": 1280},
      "stats": {"like_count": 1203, "comment_count": 45, "share_count": 12, "view_count": 20311},
      "viewer_state": {"liked": false, "saved": false},
      "linked_product": {"hotel_id": "vinpearl_pq", "name": "Vinpearl Resort Phú Quốc", "price_from": 2490000, "currency": "VND", "deeplink": "tripflix://hotel/vinpearl_pq"},
      "reco": {"source": "trending|affinity|following|explore", "request_id": "req_abc"}
    }],
    "cursor": "eyJzIjoi...", "has_more": true
  }
  ```
  `reco.source` + `request_id` gắn ngược vào interaction event để đo kênh nào hiệu quả (attribution) — phiên bản tối giản của logging cho training. Giống TikTok, client phải chấp nhận số item < `limit` khi `has_more = true` (do lọc video bị xóa/report sau khi xếp hạng).
- **Ranh giới module:** Search/Feed chỉ giữ `video_id` + điểm; gọi Social qua interface nội bộ (in-process trong modular monolith) `SocialQuery.getVideoCards(ids, viewer_id)` để hydrate media/stats/viewer_state — tránh Feed truy cập trực tiếp bảng của Social. Social phát domain event `VideoPublished`, `VideoLiked`, `VideoViewed` (in-process event bus hoặc Redis Stream) để Search cập nhật trending/affinity.

### Gaps
- TikTok không công bố API nội bộ của For You feed; mọi đề xuất shape cho feed cá nhân hóa là suy luận từ API công khai (Display/Research) và pattern chung.
- Không tìm được tài liệu chính thức về cách TikTok/Instagram lưu "seen" (Bloom filter hay khác).

---

## 6. Nguồn kỹ thuật tham khảo (engineering blogs, papers, talks) và cách đơn giản hóa cho đội 7 người/4 tuần

### Takeaway
Nguồn chính thống đáng trích dẫn: Monolith (ByteDance), TikTok Newsroom về For You, TikTok Developer docs, YouTube RecSys 2016, Kuaishou TWIN/OneRec, Meta Engineering về Instagram/Reels video, tus spec, Slack pagination, các bài nghiên cứu prefetch short-video. Mỗi kỹ thuật quy mô lớn đều có "phiên bản sinh viên" dùng Postgres + Redis + FFmpeg + CDN.

### Cited Findings
- Monolith: Real Time Recommendation System With Collisionless Embedding Table — [arXiv 2209.07663](https://arxiv.org/pdf/2209.07663) (code liên quan được liệt kê tại [github.com/bytedance/LargeBatchCTR](https://github.com/bytedance/LargeBatchCTR) theo kết quả tìm kiếm; repo Monolith chính thức của ByteDance không được xác minh trong phiên này)
- TikTok For You: [Newsroom 2020](https://newsroom.tiktok.com/en-us/how-tiktok-recommends-videos-for-you/); [Newsroom 12/2021 về đa dạng hóa](https://newsroom.tiktok.com/an-update-on-our-work-to-safeguard-and-diversify-recommendations?lang=en)
- YouTube: [Deep Neural Networks for YouTube Recommendations (2016)](https://research.google/pubs/pub45530)
- Kuaishou: [TWIN arXiv 2302.02352](https://arxiv.org/abs/2302.02352); [OneRec arXiv 2502.18965](https://arxiv.org/abs/2502.18965); [OneRec Technical Report arXiv 2506.13695](https://arxiv.org/abs/2506.13695)
- Meta/Instagram: [Instagram video processing encoding reduction](https://engineering.fb.com/2022/11/04/video-engineering/instagram-video-processing-encoding-reduction/); [AV1 for Reels](https://engineering.fb.com/2023/02/21/video-engineering/av1-codec-facebook-instagram-reels/); [Instagration pt 2 (counters)](https://instagram-engineering.com/instagration-pt-2-scaling-our-infrastructure-to-multiple-data-centers-5745cbad7834)
- Streaming join production: [ShareChat, arXiv 2410.15533](https://arxiv.org/abs/2410.15533)
- Short-video prefetch: [The Register 2025](https://www.theregister.com/2025/09/23/short_video_prefetch_research/); [arXiv 2209.02927](https://arxiv.org/pdf/2209.02927); [arXiv 2206.09839](https://arxiv.org/pdf/2206.09839)
- Upload/pagination: [tus protocol](https://github.com/tus/tus-resumable-upload-protocol/blob/main/protocol.md); [Slack pagination](https://slack.engineering/evolving-api-pagination-at-slack/)

### Inferences — bảng "quy mô lớn → phiên bản Tripflix demo"

| Kỹ thuật ở TikTok/Kuaishou/Meta | Phiên bản Tripflix (4 tuần, 7 người) | Module |
|---|---|---|
| Recall đa kênh + two-tower ANN | 4–5 truy vấn SQL/Redis (trending, tag-affinity, following, exploration, hotel promo) gộp + dedup | Search |
| Ranking DNN multi-task (Monolith/TWIN) | Công thức điểm tuyến tính, trọng số trong config | Search |
| Re-rank đa dạng/dedup | Hàm Python/TS thuần: seen-filter, không 2 video liền cùng creator/hotel, chèn explore mỗi 5 vị trí | Search |
| Kafka + Flink joiner + online training | Bảng `interaction_events` + Redis cập nhật đồng bộ + job cron 1–5 phút | Social → Search |
| Collisionless embedding + expirable | Redis hash `tag_affinity` có decay/TTL | Search |
| Traffic pool cho video mới | `exploration pool` + ngưỡng completion/like để vào trending | Search |
| Sharded counters | Bảng per-user UNIQUE + cột denormalized (+ Redis HINCRBY & flush nếu muốn) | Social |
| Transcoding farm, AV1, per-title encoding | 1 worker FFmpeg, H.264, ladder 3 bậc HLS + MP4 fallback | Social |
| Adaptive prefetch (DeLoad) | Preload segment đầu của 1–2 video, tắt khi data-saver/mạng yếu | Client (Social) |
| Anti-fraud ML, device fingerprint | Idempotency + rate limit Redis + trọng số tài khoản mới + log device_id | Social/Search |

- Đề xuất phân công cho 4 tuần (gợi ý): Tuần 1 — schema Social (videos, likes, comments, events), upload presigned + worker FFmpeg; Tuần 2 — feed API (cursor session + seen set), trending ZSET + job; Tuần 3 — scoring/re-rank, liên kết video ↔ hotel Vinpearl (deeplink booking), counters; Tuần 4 — tối ưu mạng yếu (ladder, preload), rate limit, seed dữ liệu & demo.
- Để demo thuyết phục với ít user thật, cần **seed dữ liệu tương tác giả lập** (script tạo user/event) để trending và affinity có tín hiệu — đánh dấu rõ là dữ liệu mô phỏng.

### Gaps
- Không truy cập được TikTok engineering blog/ByteDance tech blog hay talk InfoQ/QCon cụ thể nào trong phiên (fetch bị chặn, search không trả về talk liên quan); nên tìm thêm nếu cần trích dẫn về hạ tầng phục vụ (serving) hoặc CDN của TikTok.
- Chưa có nguồn về điều kiện mạng/thiết bị cụ thể ở Việt Nam (tốc độ 4G trung bình, tỷ lệ Android RAM ≤3 GB) — nên bổ sung từ báo cáo Ookla/Opensignal/GSMA để biện minh cho ladder 240p/360p.
