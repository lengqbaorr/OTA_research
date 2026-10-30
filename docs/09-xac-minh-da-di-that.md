# 09. Xác minh "đã đi thật" khi khách không đặt qua app

> **Vấn đề**: đề bài yêu cầu *"Chỉ người đã đặt chỗ và đã đi mới đăng bài được về chỗ đó"* (A2). Nếu khách đặt phòng qua Agoda, Booking, đại lý, mua vé VinWonders tại cổng, hay đặt vé máy bay trên web hãng, thì app làm sao biết họ đã đi?
> **Có phải tự xây cả nền tảng đặt vé máy bay, xe, phòng để track không?** Không hẳn. Tài liệu này giải thích vì sao, và đưa ra các cách xác minh không cần khách đặt qua app.

---

## 1. Điểm mấu chốt: với dịch vụ Vinpearl, **Vinpearl là nguồn sự thật**, dù khách đặt qua kênh nào

Theo kiến thức ngành ([tài liệu 01](01-kien-thuc-nganh-OTA.md), mục 2), một phòng khách sạn bán qua bất kỳ kênh nào cuối cùng đều **chảy về hệ thống của chính khách sạn**:

```
Khách đặt qua Agoda / Booking / đại lý / gọi điện / walk-in
        │
        ▼
Channel Manager / CRS của Vinpearl  →  PMS (hệ thống lễ tân) của resort
        │
        ▼
Khi khách đến: lễ tân check-in (có tên, giấy tờ, số phòng, ngày ở)
```

- **Phòng Vinpearl**: dù đặt ở đâu, resort vẫn có **đặt chỗ** (mã xác nhận, tên khách) và **bản ghi check-in/check-out**.
- **Vé VinWonders**: dù mua online hay tại quầy, vé có mã và có **bản ghi quét vé tại cổng** (redemption).
- **Sân golf, nhà hàng trong resort**: có hóa đơn hoặc lượt sử dụng trong hệ thống Vinpearl.

→ **Không cần khách đặt qua app**. Chỉ cần **liên kết** lần lưu trú đó với tài khoản app, và Vinpearl tự xác nhận được từ dữ liệu của mình.

Ngoại lệ duy nhất là **dịch vụ không thuộc Vinpearl** (vé máy bay đặt trên web hãng, xe khách, homestay ngoài). Mục 4 bàn riêng phần này. Riêng **vé máy bay** thì không cần dùng để xác minh "đã đi": bài đăng xác minh *chỗ* đã đến (resort, khu vui chơi), không phải chặng bay.

> **Trong phạm vi dự án**: đề bài cho "dữ liệu khách hàng mô phỏng" và cấm kết nối hệ thống khách hàng thật (mục 7). Vì vậy hệ thống PMS, check-in, quét vé được **giả lập** trong dữ liệu mô phỏng. Cách làm này cần người hướng dẫn xác nhận (mục 7).

---

## 2. Các cách xác minh, xếp theo độ tin cậy

| Mức | Cách xác minh | Áp dụng khi | Độ tin cậy | Dấu hiển thị trên bài |
|---|---|---|---|---|
| **A1** | **Đặt qua app**, đơn đã hoàn thành | Khách đặt trong app | Rất cao, tự động | **Đã đi thật** |
| **A2** | **Liên kết đặt chỗ từ kênh khác**: nhập *mã xác nhận đặt phòng* + *họ tên* hoặc *số điện thoại/email đã dùng khi đặt* → đối chiếu với hệ thống đặt chỗ của Vinpearl | Đặt qua Agoda, Booking, đại lý, gọi điện | Rất cao (đối chiếu với dữ liệu gốc của Vinpearl) | **Đã đi thật** |
| **A3** | **Quét mã tại lễ tân hoặc cổng**: khi check-in, khách mở app cho lễ tân quét mã QR cá nhân (hoặc khách quét **mã QR động** trên màn hình quầy) → lần lưu trú tự gắn vào tài khoản | Walk-in, hoặc khách quên mã đặt | Rất cao (có nhân viên hoặc mã động xác nhận) | **Đã đi thật** |
| **A4** | **Mã vé VinWonders / golf**: nhập hoặc quét mã trên vé → đối chiếu bản ghi quét vé tại cổng | Mua vé tại quầy hoặc kênh khác | Rất cao | **Đã đi thật** |
| **A5** | **Số thành viên Pearl Club** (chương trình khách hàng thân thiết của Vinpearl) đã gắn với lần lưu trú | Khách là thành viên | Cao | **Đã đi thật** |
| **B** | **Có mặt tại chỗ**: quay hoặc chụp **bằng camera trong app**, trong vùng định vị của chỗ đó; tốt hơn nữa là kèm quét **mã QR đặt cố định** tại sảnh hoặc điểm check-in ảnh | Không có đặt chỗ nào (ví dụ chỉ ghé chơi, đi cùng người đặt) | Trung bình (định vị có thể bị giả; QR cố định có thể bị chụp lại) | **Có mặt tại chỗ** (không phải "Đã đi thật") |
| **C** | **Tải chứng từ lên**: email xác nhận, hóa đơn → AI đọc và đối chiếu; không đối chiếu được thì người kiểm duyệt xem | Trường hợp còn lại | Thấp đến trung bình, tốn công duyệt | Chỉ dùng để **mở khóa A2** (đọc mã từ email); không có dấu riêng |

**Quy tắc đăng bài**:
- Bài **có dấu "Đã đi thật"** chỉ khi có ít nhất một trong A1–A5. Đây là mức đúng nghĩa yêu cầu A2 của đề bài ("đã đặt chỗ và đã đi").
- Mức B (**"Có mặt tại chỗ"**) là **đề xuất mở rộng**, cho người đi cùng (bạn của người đặt phòng) được kể chuyện. ⚠️ Cần hỏi người hướng dẫn, vì đề bài nói "chỉ người đã đặt chỗ". Nếu không được phép thì bỏ mức B.
- Người đi cùng có cách hợp lệ khác: **người đặt chỗ mời người đi cùng vào chuyến** (mục 5), và lần lưu trú hiện trong tài khoản của cả hai.

---

## 3. Luồng "Thêm chuyến đã đi" (A2 – liên kết đặt chỗ kênh khác)

```
Chuyến đi của tôi → "Thêm chuyến đã đi ở nơi khác"
  1. Chọn loại: Phòng Vinpearl / Vé VinWonders / Golf
  2. Nhập mã xác nhận (hoặc dán email xác nhận, hoặc chụp màn hình → AI đọc mã)
  3. Nhập họ tên + số điện thoại hoặc email đã dùng khi đặt
  4. Hệ thống đối chiếu với dữ liệu đặt chỗ / check-in / quét vé của Vinpearl
       ├─ Khớp, đã check-out          → thêm vào "Chuyến đi của tôi" (nguồn: Agoda…), mở nút "Kể lại chuyến đi"
       ├─ Khớp, chưa đến ngày         → thêm vào chuyến sắp tới (để dùng "Chế độ Đang đi"); chưa cho đăng bài
       ├─ Khớp nhưng tên/SĐT không trùng → từ chối, gợi ý quét mã tại lễ tân (A3)
       └─ Không tìm thấy               → báo rõ; cho thử lại tối đa 5 lần/ngày
```

- Sau khi liên kết, lần lưu trú **trở thành một mục trong "Chuyến đi của tôi"**. Lộ trình bài đăng vẫn **lấy từ đây, không gõ lại** (đúng A2). Mục được đánh dấu nguồn: *"Đặt qua kênh khác"*.
- Khi người khác bấm **"Đi y chang"**, mục này vẫn đặt được trong app, vì sản phẩm là của Vinpearl và giá lấy theo giá hiện tại.
- **Vé máy bay đặt trên web hãng**: không xác minh được (không phải dữ liệu Vinpearl) và cũng không cần. Người đăng thêm vào lộ trình như **"Phương tiện tự sắp xếp"** (tuyến, khung giờ); người xem vẫn có thể đặt vé máy bay trong app vì đề bài có bán vé máy bay.

### Chống gian lận cho từng cách

| Cách | Kiểu gian lận | Biện pháp |
|---|---|---|
| A2 mã xác nhận | Xin mã của người khác | Bắt khớp **tên + SĐT/email**; mỗi đặt chỗ chỉ liên kết với người có tên trong đặt chỗ, hoặc người được chủ đặt chỗ mời; báo cho chủ đặt chỗ khi có người liên kết |
| A2 | Đoán mã hàng loạt | Giới hạn số lần thử; khóa tạm; ghi log |
| A3 QR tại quầy | Chụp ảnh QR rồi gửi người khác | **QR động** đổi mỗi 30 giây và chỉ hiệu lực khi đang check-in; hoặc **nhân viên quét QR của khách** (không thể làm từ xa) |
| A4 mã vé | Nhặt vé của người khác | Vé phải **đã quét tại cổng**; mỗi mã chỉ gắn một tài khoản |
| B định vị | Giả GPS | Chỉ cho dấu yếu "Có mặt tại chỗ"; kết hợp camera trong app + khung thời gian + QR cố định; tài khoản dùng mức B nhiều mà không có A thì giảm uy tín |
| C chứng từ | Ảnh xác nhận giả | Không cấp dấu từ chứng từ; chỉ dùng để đọc mã rồi **đối chiếu A2** |

---

## 4. Trả lời thẳng: có phải xây cả nền tảng đặt vé máy bay, xe, phòng không?

| Phạm vi | Câu trả lời |
|---|---|
| **Dự án hiện tại (chỉ Vinpearl)** | **Không cần xây thêm.** App theo đề bài đã là kênh đặt chỗ của Vinpearl (A8: khách sạn, vé máy bay, vé vui chơi, combo). Khách đặt qua kênh khác thì xác minh bằng A2–A5, vì dữ liệu gốc vẫn thuộc Vinpearl. Xe khách nằm ngoài phạm vi |
| **Bản tầm nhìn (mở nhà cung cấp ngoài như homestay Tà Xùa)** | Vẫn **không bắt buộc** mọi giao dịch đi qua app. Có thể dùng: (1) **nhà cung cấp xác nhận khách** trên cổng đối tác (chủ homestay bấm "khách này đã ở"); (2) **QR tại cơ sở** cho khách quét khi đến; (3) **nhập email xác nhận** từ OTA khác (giống cách TripIt đọc email đặt chỗ) rồi nhờ nhà cung cấp đối chiếu; (4) **mức "Có mặt tại chỗ"** cho điểm không đặt được (quán cà phê, điểm ngắm cảnh) |

**Lợi ích kinh doanh của việc "liên kết đặt chỗ kênh khác"**: khách từng đặt Vinpearl qua Agoda hay Booking được **kéo về app của Vinpearl** để kể chuyện. Lần sau họ có lý do đặt trực tiếp. Đây chính là bài toán "tăng tỷ lệ khách trực tiếp, giảm phụ thuộc OTA" của ngành (tài liệu 01, mục 3).

---

## 5. Người đi cùng (không phải người đặt)

- Chủ đặt chỗ **mời người đi cùng** vào chuyến (nhập SĐT, hoặc gửi link). Người được mời chấp nhận thì lần lưu trú hiện trong tài khoản của họ, và họ được đăng bài với dấu "Đã đi thật".
- Đây **không phải tính năng đặt chung** (mục 8): không chia tiền, không cùng đặt. Chỉ là chia sẻ quyền kể chuyện về một chuyến đã có. ⚠️ Vẫn nên hỏi người hướng dẫn.
- Người dưới 18 tuổi đi cùng gia đình (persona 17 tuổi) được mời vào chuyến để **đăng bài**, dù không được **tự đặt**.

---

## 6. Bổ sung mô hình dữ liệu

| Thực thể | Trường chính | Ghi chú |
|---|---|---|
| `Stay` (lần lưu trú / sử dụng dịch vụ) | `user_id`, `product_ref`, `source` (APP / OTA_AGODA / OTA_BOOKING / AGENT / WALK_IN / GATE), `external_ref` (mã xác nhận, mã hóa), `checkin_at`, `checkout_at`, `verify_method` (A1–A5, B), `status` | Đơn trong app cũng sinh ra `Stay` → mọi nguồn dùng chung một luồng đăng bài |
| `StayClaim` (yêu cầu liên kết) | `user_id`, `input_type` (CODE / EMAIL / QR / TICKET / MEMBER), `matched_stay_id`, `result`, `attempts`, `created_at` | Có giới hạn số lần thử, có audit |
| `StayMember` (người đi cùng) | `stay_id`, `user_id`, `role` (OWNER / COMPANION), `invited_by`, `accepted_at` | Không có quyền thanh toán |
| `Post.verification` | `level` (VERIFIED / PRESENT / NONE), `stay_ids[]` | Quyết định dấu hiển thị |

Quy tắc đăng bài được viết lại: **được đăng về chỗ X khi tài khoản có `Stay` của chỗ X ở trạng thái đã hoàn thành, với `verify_method` thuộc A1–A5** (và B nếu người hướng dẫn cho phép, khi đó bài mang nhãn yếu hơn).

---

## 7. Câu hỏi cho người hướng dẫn

1. *"Khách đặt phòng Vinpearl qua Agoda/Booking vẫn có đặt chỗ và check-in trong hệ thống của Vinpearl. Em đề xuất cho họ **liên kết** bằng mã xác nhận + tên/SĐT để được đăng bài. Dữ liệu khách hàng mô phỏng có bao gồm các lần lưu trú qua kênh khác không ạ? Nếu không, em có được tự bổ sung (gắn nhãn mô phỏng) không?"*
2. *"Người đi cùng (không phải người đặt) có được đăng bài 'Đã đi thật' nếu được chủ đặt chỗ mời vào chuyến không ạ? Như vậy có bị coi là tính năng nhóm không?"*
3. *"Mức 'Có mặt tại chỗ' (xác minh bằng vị trí và camera trong app, không có đặt chỗ) có được phép, hay đề bài yêu cầu bắt buộc phải có đặt chỗ ạ?"*
