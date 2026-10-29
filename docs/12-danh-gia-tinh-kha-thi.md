# 12. Đánh giá tính khả thi (Feasibility Study): ý tưởng Social-to-Booking

> **Mục tiêu**: không chứng minh ý tưởng "chắc chắn thành công". Mục tiêu là trả lời *"với phạm vi nào thì ý tưởng khả thi ở mức chấp nhận được (~70–80%)?"*, *"cần những điều kiện gì?"* và *"khi nào nên dừng?"*.
>
> **Lưu ý phương pháp**: điểm số và chi phí dưới đây là **ước lượng định hướng**, dựa trên hiểu biết chung về ngành (tài liệu 01–11). Chúng chưa được kiểm chứng bằng khảo sát thị trường hay báo giá thật. Mọi con số tiền đều là giả định để có thứ tự độ lớn, cần thay bằng số thật ở giai đoạn Pha 0.

---

## 1. Kết luận nhanh (Executive Summary)

| Hạng mục | Kết luận |
|---|---|
| **Mức khả thi tổng thể** | **~75%** với **phạm vi đề xuất** (mục 3): 1 điểm đến, homestay + xe, nhận link/ảnh chụp màn hình, AI kết hợp người duyệt, đặt qua affiliate và đặt hộ |
| **Nếu làm "đầy đủ tầm nhìn" ngay** (đọc tự động mọi bình luận trên mọi nền tảng, tự đặt trọn gói bay + xe + phòng, toàn quốc) | **~35–40%**: không khả thi ở giai đoạn đầu, chủ yếu do giới hạn truy cập dữ liệu, pháp lý combo và chi phí |
| **Rủi ro lớn nhất** | (1) Hành vi người dùng: họ có chịu share/gửi bài sang một nền tảng khác không; (2) khả năng tiếp cận bình luận; (3) nền tảng lớn tự làm tính năng tương tự |
| **Điểm mạnh nhất** | Công nghệ AI hiện đã đủ tốt để hiểu ảnh, chữ và video; nhu cầu "thấy trên mạng → muốn đi" là có thật; homestay nhỏ thiếu kênh bán là nguồn cung sẵn có |
| **Khuyến nghị** | **GO có điều kiện**: chạy Pha 0 (concierge 4–6 tuần, chi phí thấp) để kiểm chứng 3 giả thuyết sống còn trước khi đầu tư xây sản phẩm |

---

## 2. Khung đánh giá

Ý tưởng được đánh giá trên **7 chiều khả thi**, mỗi chiều chấm theo thang 0–100% *"xác suất chiều này không trở thành điểm chặn"*, có trọng số theo mức độ quan trọng.

| # | Chiều | Câu hỏi then chốt | Trọng số |
|---|---|---|---|
| 1 | **Thị trường & nhu cầu** (Market/Desirability) | Có đủ người muốn dùng và chịu đổi thói quen không? | 20% |
| 2 | **Kỹ thuật** (Technical) | Có làm được với công nghệ hiện tại, độ chính xác chấp nhận được không? | 15% |
| 3 | **Dữ liệu & nền tảng** (Data access / Platform) | Có lấy được nội dung và bình luận một cách hợp lệ, ổn định không? | 15% |
| 4 | **Nguồn cung & vận hành** (Supply/Operational) | Có đặt được chỗ thật, xử lý được sự cố không? | 15% |
| 5 | **Pháp lý** (Legal/Regulatory) | Có rào cản pháp lý làm dừng mô hình không? | 10% |
| 6 | **Kinh tế** (Economic/Viability) | Có ra tiền, unit economics có dương không? | 15% |
| 7 | **Cạnh tranh & thời điểm** (Competitive/Timing) | Có đủ cửa sổ thời gian và khác biệt không? | 10% |

---

## 3. Phạm vi được đánh giá (Scope giả định)

Để có kết luận có nghĩa, đánh giá dựa trên **phạm vi MVP đề xuất** (tài liệu 11):

- **Điểm đến**: Đà Lạt (1 điểm).
- **Ngành hàng**: homestay/khách sạn nhỏ + xe khách/limousine (vé máy bay chỉ dùng affiliate).
- **Kịch bản**: K1 (đặt đúng chỗ trong bài) + K3 (Thẻ cộng đồng) trước; K2 (lên trọn chuyến đi) ở mức đơn giản.
- **Đầu vào**: dán/share link (TikTok, Instagram, Threads, Facebook) + **ảnh chụp màn hình** (bài và bình luận).
- **Kênh**: Web/PWA + Zalo OA + trang chuyến đi cho creator.
- **Đặt chỗ**: affiliate (M1) + đặt hộ có người hỗ trợ (M2). Chưa giữ tiền trọn gói.
- **Con người**: AI kết hợp người duyệt (human-in-the-loop).

---

## 4. Đánh giá chi tiết từng chiều

### 4.1. Thị trường & nhu cầu — **70%**

**Ủng hộ (+)**
- Hành vi "tìm điểm du lịch trên TikTok/Instagram" đã phổ biến ở Gen Z và millennials Việt Nam. Bình luận dạng "homestay tên gì ạ?", "xin info", "giá bao nhiêu" xuất hiện dày đặc dưới các bài du lịch. Đó là **bằng chứng nhu cầu có thể quan sát được**, kiểm chứng rẻ bằng cách đếm thủ công.
- Du lịch nội địa Việt Nam có quy mô lớn (trên 100 triệu lượt/năm, *cần kiểm chứng số liệu mới nhất*). Đà Lạt là một trong những điểm có nhiều nội dung "check-in" nhất.
- Nỗi đau rõ ràng: mất thời gian dò tìm, đặt rải rác, sợ bị lừa (ảnh filter, chủ nhà ảo).

**Phản biện (–)**
- **Rào cản đổi thói quen**: người dùng đang quen *nhắn tin thẳng cho chủ homestay* qua Zalo/Facebook. Vì sao họ phải qua một nền tảng trung gian? Giá trị gia tăng phải rõ (rẻ hơn, nhanh hơn, an toàn hơn, có gợi ý tốt hơn).
- Hành động "share sang app khác" có ma sát, và tỷ lệ chuyển từ "thích" sang "đặt" trong du lịch vốn thấp.
- Tần suất du lịch mỗi người chỉ 2–4 lần/năm nên giữ chân khó.

**Điều kiện để giữ mức 70%**: giá trị gia tăng phải là *"rẻ hơn hoặc tốt hơn nhờ cộng đồng"* và *"an toàn hơn (có xác minh, có hỗ trợ khi sự cố)"*, chứ không chỉ "tiện hơn".

### 4.2. Kỹ thuật — **80%**

**Ủng hộ (+)**
- Các mô hình AI đa phương thức hiện tại đã làm tốt: đọc chữ trên ảnh/ảnh chụp màn hình, chuyển giọng nói tiếng Việt thành chữ, hiểu ngữ cảnh caption và bình luận tiếng Việt (kể cả teencode, không dấu ở mức khá), trích thực thể địa danh và giá.
- Bản đồ, tìm kiếm địa điểm, tính khoảng cách đều là bài toán đã có dịch vụ sẵn.
- Phần đặt chỗ, thanh toán và quản lý đơn là nghiệp vụ OTA tiêu chuẩn, đã có nhiều mô hình tham khảo (tài liệu 06–07).

**Phản biện (–)**
- **Định danh đúng cơ sở cụ thể** (K1) là phần khó nhất. Nhiều homestay có view giống nhau, tên trùng hoặc không có tên trong bài. Ước lượng ban đầu: đúng *khu vực* ≥ 90%, đúng *cơ sở cụ thể* chỉ 50–70% nếu chỉ dùng AI; cần người duyệt và người dùng xác nhận để đạt độ tin cậy cao.
- Place Graph cần thời gian tích lũy: những tháng đầu dữ liệu mỏng.
- Phát hiện seeding trong bình luận là bài toán xác suất, không bao giờ hoàn hảo.

**Kết luận**: kỹ thuật **không phải điểm chặn** nếu chấp nhận *human-in-the-loop* giai đoạn đầu và hiển thị độ tin cậy minh bạch.

### 4.3. Dữ liệu & nền tảng — **60%** (chiều yếu nhất)

**Ủng hộ (+)**
- **Ảnh chụp màn hình** và **dán link** do người dùng chủ động cung cấp là cách hợp lệ, không phụ thuộc API, dùng được với mọi nền tảng.
- oEmbed/API chính thức cho phép lấy thông tin cơ bản và nhúng bài ở nhiều nền tảng (*cần kiểm chứng từng nền tảng*).
- Creator kết nối tài khoản → hợp lệ, có quyền đọc bình luận trên bài của chính họ.

**Phản biện (–)**
- **Không lấy tự động được bình luận của mọi bài công khai** qua kênh chính thức. K3 ở MVP phụ thuộc vào việc người dùng chịu chụp màn hình bình luận.
- Chính sách API của nền tảng thay đổi thường xuyên, có thể bị thu hẹp bất ngờ.
- Cào dữ liệu (scraping) có rủi ro pháp lý và bị chặn nên **không được đưa vào kế hoạch**.

**Điều kiện để giữ mức 60%**: chấp nhận K3 là "tốt khi có dữ liệu" chứ không phải "luôn có"; đầu tư sớm vào cộng đồng review riêng.

### 4.4. Nguồn cung & vận hành — **75%**

**Ủng hộ (+)**
- Homestay nhỏ ở Đà Lạt *thiếu kênh bán ngoài mạng xã hội*. Hoa hồng hợp lý (10–15%) kèm khách mới là đề xuất hấp dẫn với họ.
- Xe khách/limousine và khách sạn lớn có thể lấy qua affiliate/đối tác ngay (tài liệu 02).
- Phạm vi 1 điểm đến cho phép một đội nhỏ (1–2 người BD tại chỗ) ký 20–50 cơ sở trong 1–2 tháng.

**Phản biện (–)**
- Homestay nhỏ vận hành *không chuyên nghiệp*: cập nhật phòng trống chậm, đổi giá tùy ý, có thể hủy sát ngày. Cần quy trình xác nhận và phương án dự phòng.
- Đặt hộ (concierge) tốn người, không mở rộng được nếu nhiều đơn → chỉ là bước chuyển tiếp.
- Hỗ trợ khách khi sự cố cần trực ngoài giờ (cuối tuần, lễ).

### 4.5. Pháp lý — **75%**

**Ủng hộ (+)**
- Phạm vi MVP (affiliate + đặt hộ dịch vụ riêng lẻ, không bán tour/combo trọn gói) có rủi ro pháp lý thấp hơn nhiều so với kinh doanh lữ hành.
- Dùng cổng thanh toán được cấp phép nên OTA không cần tự giữ tiền như ví.

**Phản biện (–)**
- Website/app có thể phải **thông báo hoặc đăng ký với Bộ Công Thương** (website bán hàng hay sàn TMĐT, tài liệu 08).
- **Dữ liệu cá nhân** trong ảnh chụp màn hình và bình luận (tên, avatar người khác) cần được xử lý cẩn thận theo quy định bảo vệ dữ liệu cá nhân.
- Khi chuyển sang **bán combo xe + phòng** thì có thể bị coi là kinh doanh lữ hành → cần giấy phép và ký quỹ.
- Gắn nhãn nội dung quảng cáo/hợp tác của creator theo quy định quảng cáo.

**Kết luận**: không có điểm chặn ở MVP, nhưng **phải có tư vấn pháp lý trước Pha 2**.

### 4.6. Kinh tế (Unit economics) — **65%**

**Mô hình doanh thu trên mỗi đơn (giả định để ước lượng)**

| Loại đơn | Giá trị đơn (GBV) | Tỷ lệ thu | Doanh thu thuần/đơn |
|---|---|---|---|
| Homestay ký trực tiếp, 2 đêm × 800k | 1.600.000đ | 12% | ~190.000đ |
| Khách sạn qua affiliate OTA, 2 đêm × 1tr | 2.000.000đ | ~3–5% (tùy chương trình) | ~60.000–100.000đ |
| Vé xe/limousine qua đối tác | 2 vé × 300k = 600.000đ | ~3–5% | ~20.000–30.000đ |
| **Đơn trung bình hỗn hợp (giả định)** | — | — | **~150.000đ** |

**Chi phí biến đổi/đơn (giả định)**: AI xử lý (đã cache, rẻ dần theo quy mô) ~ vài nghìn đồng; thanh toán ~1–2% GBV; CS/vận hành ~20–40k khi còn concierge; hoa hồng creator (nếu có) ~20–30% doanh thu thuần.
→ **Contribution margin ước lượng ~60–90k/đơn** ở giai đoạn đầu. Mức này dương nhưng mỏng.

**Chi phí cố định giai đoạn MVP (giả định thứ tự độ lớn)**

| Hạng mục | Ước lượng |
|---|---|
| Đội 7–9 người (PO/PM, BA, 2–3 dev, designer bán thời gian, 2 vận hành/CS, 1 BD) | ~200–300 triệu/tháng |
| Hạ tầng, AI, công cụ | ~10–30 triệu/tháng (giai đoạn đầu) |
| Marketing thử nghiệm + hoa hồng creator | ~30–80 triệu/tháng |
| **Tổng "burn" ước lượng** | **~250–400 triệu/tháng** |

**Điểm hòa vốn vận hành (rất thô)**: 300 triệu ÷ ~75k contribution ≈ **4.000 đơn/tháng** (~130 đơn/ngày). Đây là con số **khó đạt chỉ với 1 điểm đến** → giai đoạn MVP *chắc chắn lỗ*. Mục tiêu của MVP là **chứng minh tỷ lệ chuyển đổi và unit economics**, không phải hòa vốn.

**Đòn bẩy cải thiện kinh tế**: tăng tỷ trọng homestay ký trực tiếp (biên cao); bán kèm (hoạt động, bảo hiểm, thuê xe máy); combo (Pha 2, sau khi có giấy phép); giảm chi phí CS nhờ tự động hóa; creator mang traffic với chi phí trả theo kết quả.

**Kết luận**: kinh tế **khả thi ở quy mô** (nhiều điểm đến, tỷ trọng hàng trực tiếp cao), **không khả thi ở quy mô MVP**. Cần vốn chạy 12–18 tháng.

### 4.7. Cạnh tranh & thời điểm — **65%**

**Ủng hộ (+)**
- Các OTA lớn mạnh ở *tồn kho và giá*, nhưng yếu ở *giai đoạn cảm hứng trên mạng xã hội* và *homestay nhỏ chưa lên OTA*.
- Lớp **"tổng hợp trí tuệ cộng đồng từ bình luận"** gần như chưa ai làm bài bản cho thị trường Việt Nam (*cần khảo sát lại đối thủ ở Pha 0*).
- AI vừa đủ rẻ và tốt để làm điều này, nên thời điểm phù hợp.

**Phản biện (–)**
- **Nền tảng mạng xã hội có thể tự làm** (đặt phòng ngay trong app, gắn link affiliate du lịch), và họ có lợi thế dữ liệu tuyệt đối.
- OTA lớn (Traveloka, Agoda, Trip.com) có thể thêm tính năng "dán link TikTok" khá nhanh.
- Ý tưởng dễ sao chép về giao diện.

**Hào phòng thủ (moat) khả thi**: Place Graph địa phương gắn với nội dung và cộng đồng; nguồn hàng homestay ký trực tiếp; mạng lưới creator; dữ liệu review đã xác minh. Cả bốn đều **cần thời gian tích lũy**, nên tốc độ thực thi là yếu tố sống còn.

---

## 5. Tổng hợp điểm khả thi

| Chiều | Trọng số | Phạm vi MVP đề xuất | Phạm vi "đầy đủ tầm nhìn" ngay từ đầu |
|---|---|---|---|
| Thị trường & nhu cầu | 20% | 70% | 70% |
| Kỹ thuật | 15% | 80% | 50% (tự động hoàn toàn, nhiều điểm đến, đa ngành) |
| Dữ liệu & nền tảng | 15% | 60% | 20% (đọc mọi bình luận tự động) |
| Nguồn cung & vận hành | 15% | 75% | 35% (toàn quốc, trọn gói) |
| Pháp lý | 10% | 75% | 40% (combo, giữ tiền trọn gói) |
| Kinh tế | 15% | 65% | 30% (burn rất lớn) |
| Cạnh tranh & thời điểm | 10% | 65% | 50% |
| **Điểm tổng (bình quân gia quyền)** | **100%** | **≈ 70%** | **≈ 43%** |

Cách tính MVP: 0,20×70 + 0,15×80 + 0,15×60 + 0,15×75 + 0,10×75 + 0,15×65 + 0,10×65 = 14 + 12 + 9 + 11,25 + 7,5 + 9,75 + 6,5 = **70,0%**.

Cách tính "đầy đủ tầm nhìn": 0,20×70 + 0,15×50 + 0,15×20 + 0,15×35 + 0,10×40 + 0,15×30 + 0,10×50 = 14 + 7,5 + 3 + 5,25 + 4 + 4,5 + 5 = **43,25%**.

> **Đọc kết quả**: phạm vi MVP đạt ~70%, *chạm ngưỡng dưới* của mục tiêu 70–80%. Nếu Pha 0 cho kết quả tốt ở 3 giả thuyết sống còn (mục 6), các chiều *Thị trường*, *Dữ liệu* và *Kinh tế* sẽ được nâng lên. Khi đó điểm khả thi ước tính lên **~75–80%**. Bảng dưới cho thấy cách nâng điểm.

### Điều gì nâng điểm lên 75–80%?

| Yếu tố | Chiều được nâng | Tác động ước tính lên điểm tổng |
|---|---|---|
| Pha 0 chứng minh ≥ 30% người vào gửi link/ảnh và ≥ 5–10% ra đơn | Thị trường 70 → 80 | +2 |
| Ký được ≥ 30 homestay hoa hồng ≥ 10% | Nguồn cung 75 → 85, Kinh tế 65 → 70 | +2,25 |
| ≥ 30% yêu cầu có ảnh chụp bình luận, hoặc có ≥ 10 creator kết nối tài khoản | Dữ liệu 60 → 75 | +2,25 |
| Đơn vị chủ quản **đã có sẵn** nguồn hàng/khách hàng/thương hiệu du lịch | Nguồn cung, Kinh tế, Cạnh tranh | +2–4 |
| Có tư vấn pháp lý và lộ trình giấy phép lữ hành rõ ràng | Pháp lý 75 → 85 | +1 |

→ Với 2–3 yếu tố trên đạt được, điểm khả thi đạt **~75–80%**, đúng mức mục tiêu.

---

## 6. Ba giả thuyết sống còn (Must-be-true)

Nếu một trong ba điều sau **sai**, ý tưởng không còn khả thi ở dạng hiện tại:

| # | Giả thuyết sống còn | Kiểm chứng ở Pha 0 | Ngưỡng "tiếp tục" | Ngưỡng "dừng / xoay hướng" |
|---|---|---|---|---|
| **S1** | Người dùng **chịu gửi** link/ảnh từ mạng xã hội để được giúp đặt | Landing page + Zalo OA + quảng cáo nhỏ / creator | ≥ 30% người truy cập gửi yêu cầu | < 10% |
| **S2** | Yêu cầu **chuyển thành đơn** đặt thật | Concierge xử lý trong 4–6 tuần | ≥ 5–10% yêu cầu ra đơn | < 2% |
| **S3** | Gợi ý (từ AI và cộng đồng) **tạo giá trị thật**: rẻ hơn, phù hợp hơn, hoặc người dùng đánh giá cao | So sánh với phương án gốc; khảo sát sau đơn | ≥ 40% bài có phương án tốt hơn kiểm chứng được; CSAT ≥ 4/5 | < 15% |

**Các phương án xoay hướng (pivot) nếu thất bại**
- S1 thất bại → bỏ hướng "người xem share", tập trung **công cụ cho creator/homestay** (B2B: biến nội dung mạng xã hội của họ thành kênh đặt phòng).
- S2 thất bại → trở thành **nền tảng gợi ý & nội dung** kiếm tiền bằng affiliate/quảng cáo, không làm OTA.
- S3 thất bại → bỏ K3, giữ K1/K2 như một công cụ tìm kiếm từ nội dung (giá trị thấp hơn, dễ bị sao chép hơn).

---

## 7. Tiêu chí ra quyết định (Go / No-go)

| Mốc | Thời điểm | Điều kiện GO | Điều kiện NO-GO / PIVOT |
|---|---|---|---|
| **Gate 0** – Khởi động Pha 0 | Ngay | Có ngân sách ~6 tuần cho đội nhỏ (3–4 người) + 20–30 homestay đồng ý thử | Không có người vận hành hoặc không ký được cơ sở nào |
| **Gate 1** – Xây MVP sản phẩm | Sau Pha 0 (tuần 6) | S1, S2 đạt ngưỡng tiếp tục; S3 có tín hiệu tích cực | S1 hoặc S2 dưới ngưỡng dừng |
| **Gate 2** – Mở rộng & đặt trực tiếp | Sau MVP (~tháng 5–6) | Conversion ổn định; contribution margin/đơn dương; độ chính xác nhận diện ≥ 70% đúng cơ sở; có lộ trình pháp lý | Contribution âm kéo dài; CAC > doanh thu thuần 3–4 đơn |
| **Gate 3** – Nhân rộng điểm đến | ~tháng 9–12 | Có playbook mở điểm đến mới trong ≤ 4 tuần; tỷ lệ khách quay lại tăng | Mỗi điểm đến mới tốn chi phí như làm lại từ đầu |

---

## 8. Ma trận rủi ro tóm tắt (Xác suất × Tác động)

| Rủi ro | Xác suất | Tác động | Ưu tiên |
|---|---|---|---|
| Người dùng không chịu đổi thói quen (vẫn nhắn trực tiếp chủ nhà) | Trung bình–Cao | Cao | **1** |
| Nền tảng mạng xã hội/OTA lớn tự làm tính năng tương tự | Trung bình | Cao | **2** |
| Không đủ dữ liệu bình luận cho K3 | Cao | Trung bình | **3** |
| Unit economics mỏng, burn kéo dài | Trung bình | Cao | **4** |
| Homestay vận hành kém gây sự cố, mất uy tín | Trung bình | Trung bình | 5 |
| AI nhận diện sai cơ sở | Trung bình | Trung bình | 6 |
| Pháp lý khi chuyển sang combo | Thấp (nếu chuẩn bị) | Cao | 7 |

---

## 9. Kết luận & khuyến nghị

1. **Ý tưởng khả thi ở mức ~70% với phạm vi MVP hẹp**, và có thể nâng lên **~75–80%** sau khi Pha 0 kiểm chứng ba giả thuyết sống còn và tận dụng nguồn lực sẵn có của đơn vị chủ quản.
2. **Không khả thi (~40%) nếu làm "đầy đủ tầm nhìn" ngay** (tự động hóa toàn bộ, đa nền tảng, trọn gói, toàn quốc).
3. **Rủi ro lớn nhất nằm ở hành vi người dùng và quyền truy cập dữ liệu, không nằm ở công nghệ.** Vì vậy bước đầu tiên nên là *thử nghiệm với người thật* (concierge), không phải viết code.
4. **Khuyến nghị: GO có điều kiện cho Pha 0** (4–6 tuần, đội 3–4 người, ngân sách nhỏ), đi kèm Gate 1 với tiêu chí dừng rõ ràng.

### Việc cần làm ngay để chính xác hóa đánh giá
- [ ] Khảo sát nhanh 30–50 người dùng mục tiêu: họ tìm điểm du lịch như thế nào, đặt qua đâu, nỗi đau là gì.
- [ ] Đếm thủ công trên 200 bài du lịch Đà Lạt: bao nhiêu % bình luận hỏi thông tin hoặc gợi ý thay thế (đo nhu cầu và giá trị K3).
- [ ] Phỏng vấn 15–20 chủ homestay: kênh bán hiện tại, mức hoa hồng chấp nhận được.
- [ ] Kiểm tra chính sách API hiện hành của TikTok, Meta (Instagram/Threads/Facebook), YouTube.
- [ ] Kiểm tra chương trình affiliate của các OTA và đối tác vé xe (tỷ lệ hoa hồng thực tế).
- [ ] Tham vấn pháp lý: thông báo/đăng ký TMĐT, dữ liệu cá nhân, ranh giới đại lý và lữ hành.
- [ ] Làm rõ nguồn lực sẵn có của đơn vị chủ quản (nguồn hàng, khách hàng, thương hiệu, ngân sách).
