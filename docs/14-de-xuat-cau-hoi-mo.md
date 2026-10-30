# 14. Đề xuất cho các câu hỏi mở (Mục 5 đề bài)

> Đề bài yêu cầu: với mỗi câu hỏi mở, đội **đưa ra đề xuất, giải thích vì sao, và thể hiện nó trong sản phẩm**.
> Mỗi mục dưới đây có 3 phần: **Đề xuất** → **Vì sao** → **Thể hiện trong sản phẩm (để demo/kiểm chứng)**.
> Các con số (thời gian, trọng số, ngưỡng) là **giá trị khởi điểm**, sẽ được hiệu chỉnh bằng đo đạc và ghi lại lý do thay đổi.

---

## 1. Về xu hướng

### 1.1. "Đang được quan tâm" tính theo cái gì?

**Đề xuất**: điểm xu hướng **kết hợp nhiều tín hiệu**. Mỗi tín hiệu được tính **theo số người dùng khác nhau** (không theo số lần bấm), có **trọng số theo độ "đắt"** của hành động, nhân với **độ tin cậy của tài khoản**, và **giảm dần theo thời gian**.

| Tín hiệu | Trọng số khởi điểm | Lý do |
|---|---|---|
| Xem đủ lâu (dừng ≥ 3 giây hoặc mở chi tiết) | 1 | Rẻ, nhiều, dễ bị đẩy → trọng số thấp |
| Thả cảm xúc | 2 | Có chủ đích nhưng tốn ít công |
| Bình luận (người khác nhau, nội dung không rác) | 3 | Tốn công hơn, thể hiện quan tâm thật |
| Lưu bài | 4 | Thể hiện *ý định đi*, gần với đặt chỗ |
| Chia sẻ đường dẫn (có người mở) | 4 | Lan truyền thật |
| **Đặt theo lộ trình của bài** | 10 | Tín hiệu mạnh nhất và khó giả nhất (cần tài khoản ≥ 18 tuổi, phải đi hết luồng đặt) |
| Bỏ qua nhanh / ẩn bài | –1 | Phản hồi tiêu cực |

```
Điểm_bài(t) = Σ_tín hiệu [ trọng_số × Σ_người dùng ( tin_cậy(người) × suy_giảm(t − thời_điểm) ) ]
```
Ngoài ra còn **xu hướng địa điểm**: tổng hợp điểm của các bài nói về cùng một chỗ (khách sạn, khu vui chơi, điểm đến) cộng với lượt xem trang chi tiết và lượt đặt của chỗ đó.

**Vì sao**: dùng một tín hiệu duy nhất (ví dụ lượt xem) thì dễ bị đẩy ảo và không phản ánh ý định đi. Kết hợp có trọng số cho phép tín hiệu "đắt" (lưu, đặt) quyết định nhiều hơn tín hiệu "rẻ" (xem).

**Thể hiện trong sản phẩm**:
- Mỗi bài trên bảng tin có **lý do ngắn** sinh từ tín hiệu nổi bật nhất, ví dụ: *"32 người lưu trong 24h qua"*, *"5 người đã đặt theo lộ trình này tuần này"*, *"Đang được bàn tán ở Phú Quốc"*. Lý do luôn là **số thật**, không phóng đại (theo quy tắc không thao túng).
- Trang quản trị (A15) hiển thị **bảng phân rã điểm** của từng bài: bao nhiêu điểm từ xem, cảm xúc, lưu, đặt…

### 1.2. Một bài bỗng được quan tâm, sau bao lâu lên bảng tin? Sau bao lâu hạ nhiệt?

**Đề xuất**:
- **Lên bảng tin**: điểm xu hướng cập nhật **mỗi 5 phút**. Ngoài ra có **phát hiện tăng tốc** (velocity): tín hiệu trong 1 giờ gần nhất so với mức nền 7 ngày. Một bài tăng vọt sẽ xuất hiện trên bảng tin **trong khoảng 5–15 phút**.
- **Hạ nhiệt**: suy giảm theo hàm mũ với **chu kỳ bán rã 24 giờ** cho bài đăng (sau 24h tín hiệu cũ còn 50%, sau 3 ngày còn khoảng 12%) và **72 giờ** cho địa điểm. Một bài không còn tương tác mới sẽ rời nhóm xu hướng sau khoảng **2–3 ngày**.

**Vì sao**:
- 5–15 phút đủ nhanh để người dùng cảm nhận "đang diễn ra lúc này" (A1), nhưng không cập nhật liên tục từng giây (tốn tài nguyên, dễ nhiễu, dễ bị đẩy).
- Người trẻ thường **quyết trước 1–2 ngày** (persona Khang) và đi theo nhịp cuối tuần. Bán rã 24h giữ bài hot trong đúng cửa sổ ra quyết định, đồng thời bảo đảm bảng tin "mới" mỗi ngày.
- Địa điểm thay đổi chậm hơn bài đăng nên bán rã dài hơn (72h).

**Thể hiện trong sản phẩm**: trang quản trị có **biểu đồ điểm theo thời gian** của từng bài/địa điểm. Có kịch bản demo: dùng tài khoản thử tương tác dồn vào một bài → sau ≤ 15 phút bài lên bảng tin → ngừng tương tác → theo dõi bài hạ nhiệt.

### 1.3. Làm sao để một người tạo nhiều tài khoản cũng không đẩy được bài lên xu hướng?

**Đề xuất: phòng thủ nhiều lớp**

| Lớp | Cách làm |
|---|---|
| **1. Một người một phiếu** | Mỗi tài khoản chỉ đóng góp *một lần* cho mỗi loại tín hiệu trên mỗi bài (thả tim 100 lần = 1). Bấm liên tục không tăng điểm |
| **2. Điểm tin cậy tài khoản** (0–1) | Tăng theo: tuổi tài khoản, đã xác minh số điện thoại/email, **đã có đơn đặt hoặc đã đi thật**, hành vi đa dạng (không chỉ tương tác một bài). Tài khoản mới tạo, chưa xác minh, chỉ tương tác một tác giả → tin cậy rất thấp (ví dụ 0,1) |
| **3. Gom cụm thiết bị/mạng** | Nhiều tài khoản cùng thiết bị hoặc cùng địa chỉ mạng tương tác một bài → tính như một hoặc vài người |
| **4. Phát hiện bùng nổ bất thường** | Tỷ lệ tương tác từ tài khoản tin cậy thấp > ngưỡng (ví dụ 60%), hoặc nhiều tài khoản mới tạo trong cùng khung giờ cùng tương tác một bài → gắn cờ "nghi đẩy ảo", **tạm đóng băng điểm** chờ kiểm duyệt |
| **5. Tín hiệu khó giả nặng ký hơn** | Đặt theo lộ trình (trọng số 10) chỉ đến từ tài khoản ≥ 18 tuổi đã đi hết luồng đặt |
| **6. Tự tương tác không tính** | Tác giả tự thả tim/lưu bài mình: 0 điểm |

**Vì sao**: không có cách nào chặn tuyệt đối việc tạo nhiều tài khoản. Mục tiêu là làm cho việc đẩy ảo **tốn công mà không hiệu quả**: 50 tài khoản mới chỉ có sức nặng xấp xỉ 5 người thật.

**Thể hiện trong sản phẩm**:
- A15: danh sách "**Xu hướng đáng ngờ**" kèm lý do (ví dụ: *"78% lượt lưu từ tài khoản tạo < 24h, 12 tài khoản cùng 1 thiết bị"*), nút bỏ cờ hoặc xác nhận đẩy ảo.
- Kịch bản kiểm chứng: script tạo 50 tài khoản giả tương tác một bài, so sánh thứ hạng với một bài có 10 người dùng thật (xem tài liệu 15).

### 1.4. Người mới chưa có gu thì bảng tin hiện gì?

**Đề xuất**:
1. Hiện ngay **xu hướng chung** (không bắt đăng nhập, không bắt chọn sở thích), có **đa dạng hóa**: không quá 2 bài liên tiếp cùng một điểm đến, trộn nhiều mức giá (ưu tiên mức giá người trẻ, ví dụ dưới 3 triệu/người).
2. Một **thẻ hỏi gu tùy chọn** chen giữa bảng tin (không chặn màn hình): *"Bạn thích kiểu nào?"* với các lựa chọn chill / quẩy / biển / vui chơi / ngân sách. Bấm thì cá nhân hóa ngay, bỏ qua cũng được.
3. Học nhanh từ **các tương tác đầu tiên** (thích, lưu, bỏ qua, thời gian dừng) trên một định danh ẩn danh lưu trên thiết bị. Khi đăng nhập, **gộp** lịch sử ẩn danh vào tài khoản.
4. Dành khoảng **20% bảng tin cho khám phá** (bài ngoài gu) để tránh "bong bóng" và tiếp tục học gu.

**Vì sao**: persona "người vừa tải ứng dụng" chưa biết đi đâu. Bắt chọn sở thích trước là thêm một bước ma sát. Xu hướng chung là điểm khởi đầu an toàn nhất, và hành vi thật là tín hiệu gu chính xác hơn lời khai.

**Thể hiện trong sản phẩm**: cài app mới → thấy bảng tin ngay. Thích 3 bài về biển Nha Trang → kéo xuống làm mới, bảng tin nghiêng về biển/Nha Trang, lý do hiện *"Vì bạn thích bài về biển Nha Trang"*.

---

## 2. Về bài đăng và tương tác

### 2.1. Làm sao biết chắc người đăng đã đi thật?

**Đề xuất**: **ràng buộc bài đăng với đơn đặt chỗ**, không dựa vào lời khai.
- Nút "Viết bài" chỉ cho chọn **địa điểm từ các đơn của chính mình** có trạng thái **Đã hoàn thành**: đã qua ngày trả phòng/ngày sử dụng, không bị hủy, đã thanh toán (giả lập). Trong demo có thể dùng thêm trạng thái check-in giả lập (giả định G2, tài liệu 13).
- Server kiểm tra lại điều kiện này khi đăng, không chỉ ẩn nút trên giao diện.
- Một bài gắn với **một đơn cụ thể**. Lộ trình được sinh từ các mục trong đơn đó (và các đơn khác của cùng chuyến nếu có).
- Dấu **"Đã đi thật"** hiển thị kèm *tháng/mùa đi* ở mức thô (ví dụ "Đã đi thật · Hè 2026"), **không hiện ngày cụ thể** (riêng tư, A11).

**Vì sao**: đây là cách duy nhất "biết chắc" trong phạm vi hệ thống: dữ liệu đơn là của Vinpearl, người dùng không giả được. Nó trực tiếp phục vụ persona Hà (không tin bài quá đẹp) và quy tắc "không bài đăng giả".

**Thể hiện trong sản phẩm**: tài khoản không có đơn hoàn thành bấm "Viết bài" → thông báo *"Bạn cần đi một chuyến với Vinpearl để kể lại chuyến đi"* kèm gợi ý xem chuyến sắp tới. Gọi thẳng API đăng bài với địa điểm chưa đi → bị từ chối.

### 2.2. Bị báo cáo thì ẩn ngay hay chờ duyệt? Có tự động kiểm duyệt không? AI Agent tham gia được không?

**Đề xuất: kiểm duyệt 3 tầng, AI hỗ trợ, con người quyết định cuối**

| Tầng | Khi nào | Hành động |
|---|---|---|
| **Sàng lọc tự động khi đăng** (AI + luật) | Mọi bài/bình luận mới | Phân loại: an toàn / cần xem / nguy hiểm. Các nhóm: xúc phạm, quảng cáo/spam, lừa đảo (số điện thoại, link lạ, "chuyển khoản trước"), cổ vũ hành vi nguy hiểm, **thông tin cá nhân** (CCCD, mã đặt chỗ, thẻ lên máy bay), **mặt người lạ** trong ảnh |
| → An toàn | | Hiện ngay |
| → Cần xem | | Hiện ngay nhưng vào hàng chờ (ưu tiên trung bình) |
| → Nguy hiểm / độ tin cậy cao | | **Giữ lại chờ duyệt**, báo người đăng "đang được xem xét" |
| **Báo cáo từ người dùng** | Có báo cáo | Vào hàng chờ, xếp ưu tiên theo số người báo cáo × độ tin cậy người báo cáo × mức nghiêm trọng của lý do |
| → Tự ẩn tạm thời | ≥ 3 người báo cáo *có độ tin cậy* khác nhau, **hoặc** AI xác nhận lại nhóm nghiêm trọng (lừa đảo, nguy hiểm, lộ thông tin cá nhân) | Ẩn tạm (trạng thái "Đang xem xét"), chờ người duyệt xác nhận |
| → Còn lại | | Vẫn hiện, chờ duyệt |
| **Con người** | Hàng chờ | Kiểm duyệt viên quyết định: hiện / ẩn / xóa, **bắt buộc chọn lý do**. AI Agent gợi ý hành động + lý do + đoạn vi phạm, kiểm duyệt viên bấm xác nhận hoặc sửa |

**Vì sao**:
- Ẩn ngay mọi bài bị báo cáo thì dễ bị lạm dụng (báo cáo để "đánh" đối thủ, đánh bài thật). Chờ hoàn toàn thì nội dung lừa đảo/nguy hiểm tồn tại lâu.
- Kết hợp tự động + ngưỡng + con người giúp một kiểm duyệt viên xử lý được "hàng trăm mục mỗi ngày" (persona kiểm duyệt).
- AI Agent **chỉ gợi ý**, không tự xóa vĩnh viễn: giữ trách nhiệm giải trình (ai làm, vì sao, theo A14) và tránh AI sai mà không ai kiểm.

**Thể hiện trong sản phẩm**: hàng chờ A14 sắp xếp theo ưu tiên, mỗi mục có nhãn AI ("Nghi lừa đảo – 0,92") và lý do gợi ý. Mọi thao tác ghi nhật ký (ai, lúc nào, lý do, gợi ý AI là gì, có chấp nhận gợi ý không). Người đăng nhận thông báo khi bị ẩn, kèm lý do.

**Xử lý ảnh có mặt người lạ / thông tin cá nhân** (mục 6 đề bài): khi tải ảnh lên, hệ thống phát hiện khuôn mặt và các vùng có chữ nhạy cảm, **đề xuất làm mờ** ngay trên màn hình soạn bài. Người đăng chọn mặt nào là mình/bạn mình (giữ), còn lại làm mờ. Ảnh có giấy tờ/thẻ lên máy bay chưa làm mờ → không cho đăng.

### 2.3. Sửa bài sau khi đăng có phải duyệt lại không?

**Đề xuất**:
- **Mọi lần sửa đều qua lại sàng lọc tự động** (tầng 1).
- Sửa **chữ nhỏ** và AI đánh giá an toàn → hiện ngay, không cần người duyệt.
- **Thêm/đổi ảnh, video, lộ trình**, hoặc AI gắn cờ → vào hàng chờ. Bài vẫn hiện **bản cũ đã duyệt** cho đến khi bản mới được duyệt.
- Lưu **lịch sử phiên bản**. Kiểm duyệt viên xem được khác biệt (diff) giữa các bản. Bài hiện nhãn "Đã chỉnh sửa".

**Vì sao**: chặn chiêu "đăng bài sạch để được duyệt rồi sửa thành quảng cáo/lừa đảo", nhưng không bắt người dùng chờ duyệt khi chỉ sửa lỗi chính tả.

**Thể hiện trong sản phẩm**: sửa bài thêm số điện thoại "inbox giá rẻ" → bản mới bị giữ lại, người xem vẫn thấy bản cũ, hàng chờ admin hiện diff.

### 2.4. Số lượt cảm xúc phải đúng tuyệt đối ngay lập tức, hay chấp nhận lệch vài giây?

**Đề xuất**: **đúng tuyệt đối về kết quả cuối cùng, chấp nhận độ trễ hiển thị ≤ 2 giây** giữa các thiết bị.
- **Nguồn sự thật** là bảng "ai đã thả gì vào bài nào", mỗi người một bản ghi duy nhất cho mỗi bài. Bấm liên tục chỉ bật/tắt bản ghi đó, không bao giờ cộng dồn.
- Bộ đếm hiển thị được cập nhật từ nguồn sự thật đó, và đối soát định kỳ để tự sửa nếu lệch.
- **Người bấm thấy kết quả của mình ngay lập tức** (cập nhật lạc quan trên máy). Người khác thấy thay đổi qua cơ chế đẩy realtime trong ≤ 2 giây.

**Vì sao**:
- "Đúng tuyệt đối" và "ngay lập tức trên mọi thiết bị" cùng lúc đòi hỏi khóa đồng bộ rất tốn kém mà người dùng không cảm nhận được khác biệt giữa 0 và 2 giây.
- Điều người dùng *cảm nhận được* là: hành động của mình phản hồi ngay, và con số **không bao giờ sai** (không nhảy lung tung, không đếm trùng). Đề bài yêu cầu "luôn đúng, kể cả khi nhiều người cùng thả hoặc một người bấm liên tục", và thiết kế trên thỏa mãn điều đó.

**Thể hiện trong sản phẩm**: kịch bản kiểm thử 100 tài khoản cùng thả cảm xúc và 1 tài khoản bấm 50 lần → số cuối cùng khớp đúng số người (tài liệu 15). Demo hai thiết bị cạnh nhau: thả trên web → app cập nhật trong ≤ 2 giây.

---

## 3. Về đặt theo lộ trình

### 3.1. Lộ trình đính kèm hiện những gì? Cái gì là riêng tư?

**Đề xuất**: lộ trình được lưu theo **"ngày tương đối"** (Ngày 1, Ngày 2, Ngày 3…), **không lưu ngày thật** trong phần công khai.

| Hiện công khai (người đăng chọn từng mục) | Luôn riêng tư (không bao giờ hiện) |
|---|---|
| Điểm đến, tên khách sạn/khu vui chơi/sân golf | Ngày đi/về cụ thể |
| Loại phòng / loại vé (hạng mục sản phẩm) | **Số tiền đã trả**, giá đã trả |
| Thứ tự theo ngày: "Ngày 1: bay HN → PQ, nhận phòng X; Ngày 2: VinWonders…" | Mã đặt chỗ, số vé, thông tin thanh toán |
| Số đêm | Tên, số điện thoại, giấy tờ của người đi cùng |
| Chặng bay (nơi đi – nơi đến), *không* số hiệu chuyến cụ thể theo ngày | Số hiệu chuyến bay + ngày cụ thể |
| Ghi chú của người đăng cho từng mục ("nên chọn phòng hướng biển") | Số người đi (chỉ hiện nếu người đăng chủ động bật, mặc định tắt) |
| Mức giá **hiện tại** của mục (tính lúc người xem mở bài) | Hạng thành viên, lịch sử đặt khác |

**Vì sao**: lộ trình phải đủ để người khác **đặt y chang** (A11), nhưng không được lộ thông tin cá nhân (A11, mục 6 đề bài). Ngày tương đối giải quyết cả hai: vừa giữ thứ tự ngày, vừa không lộ ngày thật, vừa làm việc "dời theo ngày mới" trở nên tự nhiên.

**Thể hiện trong sản phẩm**: màn soạn bài liệt kê các mục từ "Chuyến đi của tôi" với công tắc bật/tắt từng mục. Bài đăng hiển thị lộ trình theo Ngày 1/2/3. Kiểm thử: gọi API xem bài → phản hồi không chứa ngày thật, giá đã trả hay mã đặt chỗ.

### 3.2. Người đăng xóa bài, hoặc một mục ngừng bán, thì người đã lưu bài thấy gì?

**Đề xuất**:
- **Người đăng xóa bài** → tôn trọng quyền xóa: nội dung và lộ trình biến mất. Trong danh sách "Bài đã lưu" của người khác hiện một **thẻ ghi chú**: *"Bài này đã được tác giả gỡ"*, cho phép xóa khỏi danh sách. Các đơn *đã đặt* theo lộ trình đó vẫn còn nguyên (là đơn của người đặt, không phụ thuộc bài).
- **Bài bị kiểm duyệt ẩn** → tương tự: *"Bài này không còn hiển thị"* (không nêu chi tiết vi phạm cho người khác).
- **Một mục ngừng bán / hết chỗ** → bài vẫn hiện bình thường. Mục đó có nhãn **"Ngừng bán"** hoặc **"Hết chỗ ngày bạn chọn"**. Khi bấm "Đặt ngay", mục đó được đánh dấu rõ, kèm gợi ý **mục tương tự** (cùng điểm đến, cùng loại, giá gần nhất), hoặc cho bỏ qua mục đó.
- Người đã lưu bài có thể nhận thông báo (nếu làm B3/B6) khi mục trong lộ trình đã lưu thay đổi tình trạng.

**Vì sao**: quyền riêng tư và quyền xóa của người đăng được ưu tiên. Người lưu bài không bị "mất dấu" mà không hiểu vì sao. Lộ trình vẫn hữu ích dù một phần không còn bán.

### 3.3. Thanh toán cả lộ trình mà một mục thất bại: hủy hết hay giữ phần đã đặt được?

**Đề xuất: "Giữ chỗ tất cả trước – thanh toán một lần – xác nhận tất cả", và để người dùng chọn chính sách *trước* khi trả tiền.**

1. **Trước khi thanh toán**: hệ thống **giữ chỗ** (hold) tất cả mục cùng lúc, đồng thời chốt giá hiện tại. Mục nào giữ không được thì báo **ngay trên màn hình xác nhận** để người dùng bỏ, đổi hoặc chọn mục tương tự. → Phần lớn thất bại được phát hiện *trước khi* trả tiền.
2. **Khi thanh toán**: một giao dịch cho cả lộ trình, có mã chống trùng (idempotency).
3. **Nếu vẫn có mục thất bại sau khi đã trả** (trường hợp hiếm: hết hạn giữ chỗ, lỗi hệ thống), áp dụng **chính sách người dùng đã chọn ở màn xác nhận**:
   - **Mặc định "Giữ phần đặt được"**: mục lỗi được **hoàn tiền tự động ngay** (giả lập), người dùng được báo rõ mục nào thành công, mục nào thất bại, số tiền hoàn. Người dùng được **hủy miễn phí phần còn lại trong 30 phút** nếu thấy chuyến đi không còn ý nghĩa (ví dụ có phòng nhưng mất vé máy bay).
   - **Tùy chọn "Tất cả hoặc không"**: một mục lỗi thì hủy toàn bộ, hoàn toàn bộ tiền.

**Vì sao**:
- Giữ chỗ trước giúp tránh hầu hết tình huống "đã trả tiền mà không có chỗ".
- Không có lựa chọn đúng cho mọi người: với người đã có vé máy bay, mất 1 vé vui chơi không quan trọng (nên giữ); với người mất vé máy bay, giữ phòng là vô nghĩa (nên hủy hết). Cho người dùng chọn trước + cửa sổ hủy miễn phí 30 phút là trung thực và không ép mua (mục 6 đề bài).
- Đáp ứng đúng nghiệm thu A11: *"người dùng biết rõ chuyện gì xảy ra với các mục còn lại và với tiền của mình"*.

**Thể hiện trong sản phẩm**: màn xác nhận có lựa chọn chính sách. Cổng thanh toán giả lập có công tắc "làm lỗi mục X" để demo. A16 đánh dấu đơn "Đặt được một phần" là đơn bất thường cần theo dõi.

---

## 4. Về trợ lý

### 4.1. Trợ lý dựa vào đâu để chọn gợi ý? Làm sao chắc nó không bịa?

**Đề xuất: trợ lý không "biết" gì ngoài những gì hệ thống trả về**

1. **Hiểu câu hỏi**: mô hình ngôn ngữ chuyển câu hỏi kiểu người trẻ thành **điều kiện có cấu trúc**: điểm đến, ngày ("cuối tuần này" → ngày cụ thể), số người ("4 đứa"), ngân sách ("dưới 3 củ" → 3.000.000đ, mỗi người hay cả nhóm), gu ("chill", "quẩy", "không đông").
2. **Lấy dữ liệu bằng công cụ (tool)**: trợ lý chỉ được gọi các API tìm kiếm của hệ thống (tìm địa điểm, giá và chỗ trống, tìm bài đăng **đang hiển thị**). Đây là cùng bộ tìm kiếm dùng cho A3/A6.
3. **Trả lời có tham chiếu**: mỗi gợi ý phải gắn với **ID có thật** do công cụ trả về. Phần trình bày (thẻ địa điểm, giá, ảnh, link bài) được **dựng từ dữ liệu hệ thống**, không do mô hình tự viết. Mô hình chỉ viết câu dẫn và lý do.
4. **Kiểm tra sau khi sinh** (validator): trước khi hiện cho người dùng, kiểm tra mọi ID có tồn tại, đang bán/đang hiển thị không; giá nhắc trong câu chữ có khớp dữ liệu không. Sai thì loại gợi ý đó (hoặc sinh lại).
5. **Trích dẫn bài đăng**: khi nói "bài này khen yên tĩnh", trợ lý phải trích **đoạn có thật** trong bài. Không có đoạn nào nói vậy thì không được gán.
6. **Không có gì phù hợp** → nói thật, gợi ý nới điều kiện ("tăng ngân sách lên 3,5 triệu thì có 2 lựa chọn", "đổi sang tuần sau").

**Vì sao**: đề bài yêu cầu "chỉ gợi ý những gì có thật" và "không gán cho bài đăng điều nó không nói". Cách chắc chắn nhất là **không để mô hình là nguồn sự thật**: mô hình chỉ điều phối và diễn đạt, dữ liệu do hệ thống cung cấp và kiểm chứng.

**Thể hiện trong sản phẩm**: bộ câu hỏi thử tự động (tài liệu 15) chạy trước mỗi demo, báo cáo tỷ lệ gợi ý có ID hợp lệ (mục tiêu 100%). B7 (nếu làm) hiển thị trên admin những gợi ý bị validator loại.

### 4.2. Trợ lý nhớ gì, nhớ bao lâu? Chưa đăng nhập thì sao? Đang chat trên app, mở web có tiếp tục được không?

**Đề xuất**:

| Loại ghi nhớ | Nội dung | Thời hạn |
|---|---|---|
| **Ngữ cảnh cuộc trò chuyện** | Các tin nhắn và điều kiện đã nói (ngân sách, số người, ngày) | Theo cuộc trò chuyện. Lưu 30 ngày (đã đăng nhập) |
| **Sở thích bền** | Ngân sách thường dùng, gu, điểm đến quan tâm, được trích từ trò chuyện **và hiển thị cho người dùng xem/sửa/xóa** trong mục "Trợ lý nhớ về bạn" | Đến khi người dùng xóa |
| **Chưa đăng nhập** | Cuộc trò chuyện gắn với **định danh ẩn danh trên thiết bị** | 7 ngày trên thiết bị đó |
| **Đăng nhập giữa chừng** | Cuộc trò chuyện ẩn danh được **gộp vào tài khoản**, không mất (nghiệm thu A12) | Như đã đăng nhập |
| **Đa thiết bị** | Đã đăng nhập: cuộc trò chuyện lưu ở server → mở web thấy và **tiếp tục** cuộc trò chuyện từ app | — |

**Vì sao**: đáp ứng nghiệm thu A12 (không mất khi đăng nhập), ràng buộc "một tài khoản, một nguồn dữ liệu", và tôn trọng quyền riêng tư (người dùng thấy và kiểm soát được trợ lý nhớ gì).

### 4.3. Trợ lý được dùng những thông tin nào của người dùng?

**Đề xuất**:
- **Được dùng**: ngân sách/gu người dùng đã nói; các bài và chỗ đã thích/lưu/bỏ qua (A12 yêu cầu); điểm đến của các chuyến đã đặt (để không gợi ý lại thứ đã có, giống A9); vị trí thành phố nếu người dùng cho phép.
- **Không được dùng**: thông tin thanh toán, số điện thoại, giấy tờ, ngày sinh cụ thể (chỉ dùng cờ "đủ 18 tuổi" khi cần), dữ liệu của người dùng khác, nội dung bài đã bị ẩn.
- Dữ liệu gửi sang dịch vụ mô hình ngôn ngữ bên ngoài được **tối thiểu hóa**: không gửi tên, số điện thoại, email; chỉ gửi điều kiện và dữ liệu sản phẩm/bài đăng công khai.

**Vì sao**: nguyên tắc tối thiểu hóa dữ liệu, giảm rủi ro lộ thông tin qua bên thứ ba, và đúng mục 6 đề bài về riêng tư.

### 4.4. Một cuộc trò chuyện tốn bao nhiêu? Có giới hạn không?

**Đề xuất**:
- **Đo** (bắt buộc theo ràng buộc mục 7): ghi số token vào/ra của *mỗi lượt*, quy đổi ra tiền theo bảng giá của mô hình đang dùng; tổng hợp theo lượt, cuộc trò chuyện, người dùng và ngày.
- **Giảm chi phí**:
  - Dùng mô hình nhỏ/rẻ cho bước hiểu câu hỏi và phân loại (ngoài phạm vi/nguy hiểm).
  - Chỉ dùng mô hình lớn khi cần diễn đạt.
  - Cache kết quả cho câu hỏi phổ biến.
  - Cắt ngữ cảnh dài (tóm tắt lịch sử thay vì gửi toàn bộ).
  - Giới hạn số kết quả đưa vào mô hình (ví dụ top 10).
- **Giới hạn** (khởi điểm, hiệu chỉnh sau khi đo):
  - Chưa đăng nhập: **20 tin nhắn/ngày/thiết bị**.
  - Đã đăng nhập: **50 tin nhắn/ngày**.
  - Mỗi tin nhắn tối đa ~500 ký tự.
  - Có ngân sách trần theo ngày cho toàn hệ thống. Vượt ngưỡng thì trợ lý thông báo thân thiện và gợi ý dùng ô tìm kiếm.
- **Báo cáo**: chi phí trung bình/cuộc trò chuyện và chi phí/ngày trình bày ở mỗi demo (tài liệu 15). Nếu làm B7 thì hiển thị trên admin.

**Vì sao**: không giới hạn thì một người (hoặc một script) có thể đốt ngân sách. Giới hạn đặt rộng hơn nhu cầu thật của người dùng bình thường (vài chục tin nhắn cho một lần lên kế hoạch).

---

## 5. Về đặt chỗ

### 5.1. Đang thanh toán thì chỗ được giữ bao lâu?

**Đề xuất**: **giữ chỗ 10 phút** kể từ khi vào màn xác nhận/thanh toán, áp dụng cho mọi loại sản phẩm (phòng, vé máy bay, vé vui chơi, combo, và từng mục của lộ trình).
- Hiển thị thời gian giữ chỗ **thật** ("Chỗ của bạn được giữ đến 14:32"). Đây là thông tin trung thực, **không phải đồng hồ khan hiếm giả**.
- Hết 10 phút chưa trả → nhả chỗ. Quay lại thì kiểm tra lại chỗ trống và giá.
- Chống giữ chỗ ảo: mỗi tài khoản giữ tối đa một số lượng hợp lý cùng lúc (ví dụ 2 phiên giữ chỗ).

**Vì sao**: 10 phút đủ cho luồng thanh toán vài bước (kể cả mạng yếu, xác thực OTP giả lập), nhưng không khóa chỗ cuối quá lâu khiến người khác mất cơ hội. Đây cũng là khoảng phổ biến của các hệ thống đặt chỗ.

### 5.2. Mở cùng tài khoản trên app và web cùng lúc, sửa ở một nơi thì nơi kia thấy sau bao lâu?

**Đề xuất**:
- **Đang mở và có mạng**: thay đổi được **đẩy realtime**, nơi kia thấy trong **≤ 2–3 giây (p95)**. Áp dụng cho: tương tác, bài đã lưu, chuyến đi, đơn đặt chỗ, cuộc trò chuyện với trợ lý.
- **Ứng dụng ở nền / vừa có mạng lại**: đồng bộ ngay khi mở lại hoặc có mạng (dưới vài giây).
- **Xung đột** (sửa cùng một thứ ở hai nơi): quy tắc theo loại dữ liệu:
  - Đơn đặt chỗ: server là nguồn sự thật, thao tác thứ hai bị từ chối nếu trạng thái đã thay đổi.
  - Lưu/thả cảm xúc: thao tác cuối cùng thắng.
  - Bài nháp: giữ bản mới nhất, có cảnh báo khi phát hiện bản ở thiết bị khác mới hơn.

**Vì sao**: đề bài đòi "tương tác trên web thì app thấy **ngay**" và "mọi sản phẩm thấy cùng dữ liệu". 2–3 giây là mức người dùng cảm nhận là "ngay" khi đặt hai thiết bị cạnh nhau. Server là nguồn sự thật duy nhất nên không có hai phiên bản dữ liệu.

**Thể hiện trong sản phẩm**: demo hai thiết bị (app + web) cạnh nhau. Bộ đo đồng bộ ghi thời gian từ lúc ghi đến lúc thiết bị kia nhận (tài liệu 15).

---

## 6. Các quyết định bổ sung (không có trong câu hỏi mở nhưng cần chốt)

| Chủ đề | Đề xuất | Vì sao |
|---|---|---|
| **Chặn dưới 18 tuổi** | Ngày sinh bắt buộc khi đăng ký, không tự sửa được sau khi khai; server kiểm tra ≥ 18 khi tạo giữ chỗ **và** khi thanh toán; thông báo thân thiện: *"Bạn cần đủ 18 tuổi để tự đặt chỗ. Bạn vẫn có thể khám phá, lưu bài và lên ý tưởng chuyến đi!"* | Quy tắc cố định; persona 17 tuổi; chặn ở server để không lách qua giao diện |
| **Giá đổi khi thanh toán** | Báo giá có hiệu lực trong thời gian giữ chỗ; nếu giá thay đổi trước khi bấm trả → hiện chênh lệch, bắt xác nhận lại | A4 |
| **"Chênh lệch nhiều"** (A11) | **Không** so với giá người đăng đã trả (riêng tư). Thay vào đó so tổng giá hiện tại của lộ trình với **"mức giá thường thấy"** của chính các mục đó (ví dụ trung vị giá 30 ngày gần nhất). Lệch > 20% thì hiện cảnh báo rõ: *"Giá các ngày bạn chọn cao hơn khoảng 25% so với mức thường thấy, thử dời 1–2 ngày?"* | Đáp ứng "chênh lệch nhiều thì báo rõ" mà không lộ số tiền người đăng đã trả |
| **Ngân sách theo người hay cả nhóm** (A6) | Mặc định "theo người" khi câu có "một người/mỗi người/1ng"; "cả nhóm" khi có "tổng/cả nhóm/tất cả". Không rõ thì hiện cả hai cách hiểu bằng một chip để người dùng bấm chọn ("2 củ mỗi người" / "2 củ cả nhóm") thay vì hỏi dồn | A6, A12 "không hỏi dồn dập" |
| **Deep link** | Đường dẫn chia sẻ dạng web (mở được trên mọi trình duyệt), máy đã cài app thì mở thẳng vào app (Android App Links) | A10 |
| **Chỉ có lời khai về tuổi?** | Nêu rõ giới hạn này khi trình bày (đề bài khuyến khích trung thực) | Mục 12 đề bài |
