# 07. Nghiên cứu Mindtrip & thiết kế chi tiết tính năng "Đi y chang"

> Mục tiêu: học từ **Mindtrip** (và một số app lập lịch trình khác) để thiết kế đầy đủ luồng:
> **người đăng soạn chuyến đi có cấu trúc** (đi gì đến, ở đâu, ngày 1 chơi đâu, ngày 2 chơi đâu…) → **tải video, trình bày kiểu TikTok** → **người xem bấm "Đi y chang"** → **màn lộ trình có giá, lịch trình, bản đồ** → đặt.
> Tài liệu này **mở rộng** [06 – Luồng use case](06-luong-use-case.md) và vẫn bám [02 – Đề bài](02-de-bai-vinpearl-cho-nguoi-tre.md).
>
> ⚠️ **Giới hạn nghiên cứu**: trang mindtrip.ai bị chặn trong cấu hình mạng của phiên làm việc, nên không đọc được trực tiếp. Thông tin dưới đây lấy từ App Store, báo công nghệ và các bài review. Nhiều bài review là trang so sánh hoặc affiliate, một số chi tiết mâu thuẫn nhau. Cần mở app hoặc web Mindtrip để kiểm chứng trước khi trích dẫn trong buổi trình bày.

---

## 1. Mindtrip là gì

Mindtrip là **trợ lý du lịch dùng AI**. Người dùng trò chuyện để lên kế hoạch, nhận lịch trình theo từng ngày kèm bản đồ, lưu cảm hứng vào bộ sưu tập, lên kế hoạch cùng bạn bè, và đặt chỗ qua đối tác. Core planning **miễn phí**; Mindtrip kiếm tiền qua đối tác đặt chỗ. Một nguồn cho biết tổng vốn gọi được khoảng 19 triệu USD.

## 2. Danh mục tính năng của Mindtrip

| # | Tính năng | Mô tả (theo nguồn) | Ghi chú độ tin cậy |
|---|---|---|---|
| 1 | **Lập kế hoạch bằng trò chuyện** | Mô tả chuyến đi bằng lời, AI dựng lịch trình; người dùng **sửa lịch trình chủ yếu bằng cách trò chuyện**, AI viết lại | Nhiều review cùng mô tả; chưa thấy nguồn xác nhận kéo-thả để sắp xếp |
| 2 | **Lịch trình theo ngày, chia buổi** | Một review thấy lịch trình chia khối **sáng / chiều / tối**; một bài thử Tokyo nhận 7 ngày có ngày cụ thể và chủ đề riêng trong khoảng 90 giây | Có lịch, đặt chỗ, ảnh và bản đồ đi kèm |
| 3 | **Bản đồ là trung tâm** | Thấy khách sạn nằm ở đâu so với các điểm khác; **giá khách sạn hiện trực tiếp trên bản đồ** (ví dụ 92–198 USD/đêm), bấm vào thì hiện lựa chọn đặt | Được khen nhiều nhất |
| 4 | **Start Anywhere** | Biến cảm hứng từ **bài viết, video, ảnh, Google Maps** thành cuộc trò chuyện, bộ sưu tập hoặc kế hoạch; dán link YouTube/TikTok/Instagram để lấy danh sách địa điểm trong video | Một bài báo cũ ghi bản đầu chỉ đọc được video TikTok có chữ trên màn hình; có thể đã được cải thiện |
| 5 | **Collections (Bộ sưu tập)** | Lưu cảm hứng và địa điểm yêu thích theo chủ đề hoặc điểm đến | — |
| 6 | **Lên kế hoạch nhóm** | Mời bạn vào group chat, cùng sửa lịch trình; một review nói có **bình chọn** khi cả nhóm không thống nhất | — |
| 7 | **Đặt chỗ** | Đối tác được nhắc: Priceline, Viator, Sabre; một nguồn nói **từ 6/5/2026 có đặt vé máy bay ngay trong chat**, bên cạnh khách sạn và hoạt động | Các nguồn **mâu thuẫn**: có bài nói chỉ đặt được khách sạn, một số vé tham quan vẫn chuyển sang trang ngoài |
| 8 | **Ước tính chi phí** | Một review nhận được ước tính sơ bộ (giá vé được ghi là "ước tính"); bài thử khác chỉ có giá khách sạn, không có tổng chi phí hay chi phí theo ngày | Không ổn định |
| 9 | **Magic Camera** | Chụp ảnh để biết đang nhìn thấy gì, hoặc dịch biển hiệu, thực đơn, nhãn | Trên app iOS |
| 10 | **Near Me** | Gợi ý theo vị trí hiện tại | — |
| 11 | **Chương trình Creator** | Creator đăng ký, tạo **"magic link"** riêng gắn vào mọi nội dung du lịch; có thể **thêm ảnh, video và gợi ý của mình** vào lịch trình được tạo | Mức trả tiền được đưa tin không thống nhất (trả theo người đăng ký; có bài nói creator kiếm tới 10.000 USD/tháng; dự kiến chia doanh thu khi có đặt chỗ). Nguồn đã cũ, cần kiểm tra lại |
| 12 | **Hướng dẫn cộng đồng** | Một review nhắc tới hướng dẫn do người đi thật viết | Chưa xác nhận có "sao chép lịch trình" |

### Điểm yếu được review nêu
- Lọc nhiều điều kiện cùng lúc còn yếu: hỏi "gần tàu điện và khoảng 400 USD/đêm" thì kết quả chỉ đạt một trong hai.
- Có lịch trình 12 ngày không nêu tên quán ăn nào.
- Chi phí chỉ là ước tính, không phải giá thật để đặt ngay.
- Sửa bằng trò chuyện thì tiện nhưng khó chỉnh nhỏ.

### Các app tham khảo thêm
- **Wanderlog**: mọi địa điểm thêm vào được ghim ngay lên bản đồ; có quản lý ngân sách, ghi chi tiêu và chia tiền nhóm; bản trả phí có tối ưu đường đi và xuất sang Google Maps; nhiều người cùng sửa.
- **Trip.com Trip.Planner, TikTok GO**: xem [tài liệu 04](04-dinh-nghia-y-tuong-video-du-lich.md).

---

## 3. Bài học từ Mindtrip cho "Đi y chang"

| Mindtrip làm tốt | Áp vào app của mình | Mindtrip còn yếu, mình làm tốt hơn |
|---|---|---|
| Lịch trình theo ngày, chia **sáng / chiều / tối** | Người đăng soạn lịch trình theo khối buổi; người xem thấy đúng cấu trúc đó | — |
| **Bản đồ trung tâm, giá trên bản đồ** | Màn "Đi y chang" có tab Bản đồ: ghim từng điểm, đường đi giữa các điểm, **giá thật** trên ghim chỗ ở và vé | Giá của mình là **giá thật có chỗ** cho ngày người xem chọn, không phải ước tính |
| **Start Anywhere** (link/video → kế hoạch) | Ở mức đề bài: B2 (gửi ảnh hoặc ảnh chụp màn hình → tìm nơi tương tự trong Vinpearl); trợ lý hiểu lời mô tả | Lịch trình của mình đến từ **chuyến đi thật đã đặt**, không phải AI đoán từ video |
| **Sửa lịch trình bằng trò chuyện** | Trợ lý trong màn "Đi y chang": *"bỏ ngày 2, thêm spa"*, *"rẻ hơn chút"* → đề xuất thay đổi, người dùng bấm áp dụng | Kết hợp **sửa bằng chat + chỉnh tay** (bật/tắt mục, đổi phòng, đổi chuyến) để chỉnh nhỏ dễ |
| **Collections** | "Chuyến đi của tôi → Đã lưu", nhóm theo điểm đến; lưu cả **bản nháp chuyến đi** | — |
| **Creator thêm ảnh/video vào lịch trình** | Mỗi điểm dừng trong lịch trình **gắn với đoạn video** của người đăng | Creator của mình **bắt buộc đã đi thật** |
| **Magic link cho creator** | Link chia sẻ bài (A10) đồng thời dùng để **ghi nhận nguồn** khi có người đặt | Thưởng theo người đi theo đã hoàn thành chuyến, không theo lượt đăng ký |
| Lên kế hoạch nhóm, bình chọn | — | ⚠️ **Ngoài phạm vi đề bài** (mục 8). Để vào bản tầm nhìn |
| Magic Camera, Near Me | Để vào bản tầm nhìn (hỗ trợ khi đang ở điểm đến) | — |

---

## 4. Thiết kế lại phần người đăng: "Trình soạn chuyến đi"

### 4.1. Nguyên tắc: hai lớp dữ liệu
Bạn muốn người đăng **tự setup** "đi gì đến, ở đâu, ngày 1 chơi đâu, ngày 2 chơi đâu". Đề bài A2 lại yêu cầu lộ trình **"lấy từ những gì đã đặt, không phải gõ lại"**. Cách dung hòa là tách hai lớp:

| Lớp | Nội dung | Ai tạo | Dấu hiển thị |
|---|---|---|---|
| **Lớp 1 – Khung đã đặt (xác minh)** | Phương tiện đã đặt (vé máy bay), chỗ ở, vé vui chơi, combo | **Hệ thống tự điền** từ đơn đã hoàn thành; người đăng chỉ bật/tắt, ghi chú | ✅ **"Đã đặt qua Vinpearl"** |
| **Lớp 2 – Kế hoạch ngày (người đăng soạn)** | Ngày 1 sáng/chiều/tối làm gì; ăn ở đâu; di chuyển tại chỗ bằng gì; mẹo; ước tính chi phí cho phần không đặt qua app | **Người đăng soạn** bằng các trường có cấu trúc | 📝 **"Gợi ý của người đi"** (không có dấu xác minh, không có nút đặt nếu ngoài Vinpearl) |

→ Người xem luôn phân biệt được đâu là **phần đã đặt thật** (đặt lại được, giá thật) và đâu là **kinh nghiệm cá nhân** của người đăng.

### 4.2. Các trường thông tin người đăng setup

**A. Thông tin chung của chuyến**

| Trường | Kiểu | Bắt buộc | Ghi chú |
|---|---|---|---|
| Tiêu đề chuyến | Chữ (≤ 80 ký tự) | ✅ | "Phú Quốc 3N2Đ chill hết nấc" |
| Điểm đến | Tự điền từ đơn | ✅ | Không sửa được |
| Số ngày / số đêm | Tự tính từ đơn | ✅ | Không sửa được |
| Mùa đi | Tự điền ("Hè 2026") | ✅ | Không hiện ngày cụ thể |
| Kiểu chuyến (vibe) | Chọn nhiều: chill, quẩy, sống ảo, nghỉ dưỡng, khám phá, ăn uống | ✅ ít nhất 1 | Dùng cho tìm kiếm và gợi ý |
| Hợp với | Cặp đôi / nhóm bạn / gia đình có trẻ nhỏ / đi một mình | ⬜ | Người đăng chọn, không phải số người thật |
| Mức chi tiêu cảm nhận | Chọn khoảng: dưới 3 triệu / 3–5 triệu / 5–8 triệu / trên 8 triệu mỗi người | ⬜ | Do người đăng **tự nguyện** ước lượng, hiện kèm *"do người đăng ước lượng; giá hiện tại xem khi chọn ngày"*. Không lấy từ giá đã trả |
| Có nhận tài trợ không | Có / Không | ✅ | Có → nhãn "Hợp tác" |

**B. Đi gì đến** (phương tiện đến điểm đến)

| Trường | Kiểu | Ghi chú |
|---|---|---|
| Chặng đi / về đã đặt | Tự điền từ vé máy bay (nếu có): tuyến + khung giờ | ✅ "Đã đặt qua Vinpearl" |
| Nếu không đặt vé qua app | Người đăng chọn: tự đi xe khách / ô tô / xe máy / tàu, kèm ghi chú | 📝 Gợi ý, không có nút đặt |
| Từ sân bay/bến về chỗ ở | Chọn: xe đưa đón của resort / taxi / xe công nghệ…, ghi chú, ước tính chi phí | 📝 |

**C. Ở đâu**

| Trường | Kiểu | Ghi chú |
|---|---|---|
| Chỗ ở, loại phòng, số đêm | Tự điền từ đơn | ✅ Không sửa được |
| Ghi chú phòng | Chữ | "Xin tầng cao hướng biển" |
| Đánh giá nhanh | Sạch / view / dịch vụ / đáng tiền (1–5) | ⬜ Đây là đánh giá đã xác minh, dùng cho B1 |

**D. Lịch trình từng ngày** (lõi của "Ngày 1 chơi đâu, Ngày 2 chơi đâu")

Mỗi ngày có ba khối **Sáng / Chiều / Tối**. Mỗi khối có một hoặc nhiều **điểm dừng**:

| Trường của điểm dừng | Kiểu | Bắt buộc | Ghi chú |
|---|---|---|---|
| Loại | Đã đặt (tự điền) / Dịch vụ Vinpearl khác / Điểm tự do / Ăn uống / Di chuyển / Nghỉ | ✅ | Xem 4.3 |
| Tên & vị trí | Chọn từ danh mục Vinpearl, **hoặc** ghim bản đồ + tên (điểm tự do) | ✅ | Điểm tự do chỉ có vị trí và tên, không có giá |
| Thời lượng gợi ý | 30 phút / 1 giờ / nửa ngày / cả ngày | ⬜ | Dùng để vẽ dòng thời gian |
| Ghi chú / mẹo | Chữ (≤ 300 ký tự) | ⬜ | "Vào lúc 9h cho vắng" |
| Ước tính chi phí | Số tiền mỗi người | ⬜ | **Chỉ cho điểm không đặt qua app**, ghi rõ "người đăng ước tính" |
| Đoạn video / ảnh | Chọn đoạn trong video chính, hoặc ảnh riêng | ⬜ | Dùng để chia chương video (4.4) |
| Di chuyển tới điểm tiếp theo | Đi bộ / xe điện nội khu / taxi…, thời gian | ⬜ | Hiện trên bản đồ |

**E. Video & media**
- **Một video chính** (dọc, gợi ý ≤ 60 giây), có thể kèm ảnh và **clip ngắn cho từng điểm dừng**.
- **Chia chương**: người đăng kéo các điểm dừng vào thanh thời gian video. Hệ thống gợi ý tự động bằng cách so khung hình với ảnh chính thức của các chỗ trong chuyến.
- Làm mờ mặt người lạ và giấy tờ (mục 6 đề bài), như [tài liệu 06](06-luong-use-case.md).

### 4.3. Các loại điểm dừng và giới hạn theo đề bài

| Loại | Ví dụ | Đặt được? | Trạng thái với đề bài |
|---|---|---|---|
| **Đã đặt (Lớp 1)** | Vinpearl Resort, VinWonders mà người đăng đã đặt | ✅ Có, giá thật | ✅ Đúng A2, A11 |
| **Dịch vụ Vinpearl khác mà người đăng không đặt qua app** | Người đăng mua vé VinWonders tại cổng | ✅ Có (là dịch vụ Vinpearl), nhưng **không có dấu "Đã đặt"** | ⚠️ Cần hỏi: có được thêm vào lộ trình không? Đề xuất: cho thêm, gắn nhãn "Người đăng không đặt qua app" |
| **Điểm tự do** | Sunset Town, chợ đêm, bãi biển | ❌ Không | ⚠️ Cần hỏi (dịch vụ ngoài Vinpearl nằm ngoài phạm vi). Đề xuất: chỉ là gợi ý ghé thăm, không giá, không nút đặt |
| **Ăn uống** | Quán ngoài | ❌ Không | ⚠️ Như trên |
| **Di chuyển tại chỗ** | Taxi, xe điện | ❌ Không | Chỉ là thông tin |

### 4.4. Trình bày kiểu TikTok
```
┌──────────────────────────────┐
│  [Video dọc toàn màn hình]   │
│                              │
│  ▸ Ngày 2 · Chiều            │  ← chip chương: đổi theo đoạn video đang phát
│  🎢 VinWonders Phú Quốc      │  ← thẻ địa điểm (chạm → trang chi tiết)
│                              │
│ ✅ Đã đi thật · Hè 2026       │      ❤ 1,2k
│ "Phú Quốc 3N2Đ chill hết nấc"│      💬 86
│ 🔥 32 người lưu trong 24h     │      🔖
│ Từ ~4,2tr/người · giá đổi    │      ↗
│   theo ngày                  │
│ ▬▬▬▬▬●▬▬▬▬▬▬▬▬▬▬            │  ← thanh tiến trình có vạch chia chương (Ngày 1 | Ngày 2 | Ngày 3)
│ [ 🗺 Xem lộ trình ] [ ĐI Y CHANG ]│
└──────────────────────────────┘
```
- **Thanh tiến trình có chương**: mỗi vạch là một ngày hoặc một điểm dừng. Chạm vào vạch để nhảy tới đoạn đó.
- **Kéo lên** (hoặc nút "Xem lộ trình") → mở **bảng lộ trình** che nửa màn hình, video vẫn phát phía trên.
- **"Đi y chang"** luôn nằm ở vị trí cố định, dễ bấm bằng một tay.
- Mạng yếu: hiện ảnh bìa và chương trước, video tải sau (A1).

---

## 5. Thiết kế màn hình "Đi y chang"

Bấm "Đi y chang" → **tới thẳng màn xác nhận trước thanh toán** (A11), đã điền sẵn. Màn này có **3 tab**, học từ Mindtrip và Wanderlog:

### 5.1. Phần cố định phía trên
| Điều khiển | Mặc định | Ảnh hưởng |
|---|---|---|
| 📍 **Đi từ** | Thành phố trong hồ sơ hoặc vị trí (ví dụ Sài Gòn) | Thay chặng bay theo nơi xuất phát (nếu dữ liệu có) |
| 📅 **Ngày đi** | Ngày rẻ nhất *còn chỗ* trong 30 ngày tới; lịch có màu theo mức giá | Mọi mục tự dời theo số ngày tương đối, giữ đúng thứ tự |
| 👥 **Số người** | 2 người lớn | Tự tính số phòng, vé, ghế |
| ⏱ **Giữ chỗ** | Chỉ hiện sau khi bấm "Giữ chỗ" | Giờ hết hạn thật, 10 phút |

### 5.2. Tab 1 – Lịch trình
```
Ngày 1 · Thứ Bảy 15/11
  Sáng   ✈ SGN → PQC · 07:45–08:50 · 1.290.000đ/người    ✅ Còn chỗ   [Đổi chuyến]
         🚐 Xe đưa đón resort (người đăng gợi ý)           📝 ~ miễn phí
  Chiều  🏨 Vinpearl Resort Phú Quốc · Deluxe hướng biển · 2 đêm
         2 phòng × 2.950.000đ/đêm                            ✅ Còn chỗ   [Đổi phòng]
         💬 "Xin tầng cao, view hoàng hôn đỉnh" — Linh
  Tối    📍 Sunset Town (gợi ý ghé, không đặt được)          📝 ~200.000đ/người (người đăng ước tính)
Ngày 2 · Chủ Nhật 16/11
  Cả ngày 🎢 VinWonders Phú Quốc · vé 1 ngày · 4 vé           ⚠️ Hết vé ngày này
         → [Chọn ngày khác cho mục này] [Vé tương tự: Vinpearl Safari] [Bỏ mục]
Ngày 3 · Thứ Hai 17/11
  Tối    ✈ PQC → SGN · 18:30–19:35                           ✅ Còn chỗ
```
- Mỗi mục có **trạng thái thật**: ✅ còn chỗ, ⚠️ hết chỗ (kèm 3 lựa chọn), ⛔ ngừng bán.
- **Bật/tắt từng mục**; nút **"+ Thêm"** (thêm vé, đưa đón, hoạt động của Vinpearl).
- Mục 📝 (gợi ý của người đi) **không tính vào tiền phải trả**.

### 5.3. Tab 2 – Bản đồ (học từ Mindtrip)
- Ghim tất cả điểm dừng theo màu ngày (Ngày 1 xanh, Ngày 2 cam…), có đường nối theo thứ tự.
- **Giá thật hiện trên ghim** của chỗ ở và vé.
- Chạm ghim → thẻ nhỏ: ảnh, giá, trạng thái, đoạn video của người đăng tại điểm đó.
- Bật lớp **"Chỗ tương tự gần đây"** để xem phương án thay thế trên bản đồ.

### 5.4. Tab 3 – Chi phí (học từ Wanderlog, nhưng dùng giá thật)
| Nhóm | Số tiền | Nguồn |
|---|---|---|
| Vé máy bay (4 người, khứ hồi) | 10.320.000đ | ✅ Giá thật hôm nay |
| Chỗ ở (2 phòng × 2 đêm) | 11.800.000đ | ✅ Giá thật hôm nay |
| Vé vui chơi | — (đã bỏ) | — |
| **Tổng phải trả trong app** | **22.120.000đ (~5,5 triệu/người)** | ✅ |
| Chi phí ngoài app (ăn uống, đi lại tại chỗ) | ~1.600.000đ | 📝 Người đăng ước tính, chỉ tham khảo |

- So sánh: *"Giá ngày bạn chọn cao hơn ~25% so với mức thường thấy. Đi từ 22/11 tiết kiệm khoảng 3,1 triệu."* → chạm để đổi ngày.
- **Không bao giờ** hiện số tiền người đăng đã trả.

### 5.5. Thanh hành động phía dưới
- 💬 **"Chỉnh bằng trợ lý"** (học từ cách sửa bằng chat của Mindtrip): *"bỏ ngày 2"*, *"thay resort rẻ hơn"*, *"thêm 1 đêm"*. Trợ lý **đề xuất thay đổi** dưới dạng danh sách khác biệt; người dùng bấm **"Áp dụng"**. Trợ lý không tự đặt (A12).
- 🔖 **"Lưu thành bản nháp chuyến đi"**: lưu vào "Chuyến đi của tôi" (giống Collections), đồng bộ app và web. Nếu làm B3 thì có báo khi giá giảm.
- **"Giữ chỗ & thanh toán"**: luồng như [tài liệu 06](06-luong-use-case.md) mục 2.5–2.8. Có chọn chính sách khi một mục lỗi ("Giữ phần đặt được" / "Tất cả hoặc không").

---

## 6. Các tính năng thêm (đề xuất, xếp theo ưu tiên)

| # | Tính năng | Học từ | Mô tả | Ưu tiên |
|---|---|---|---|---|
| F1 | **Chương video theo lịch trình** | Chapters của YouTube | Mục 4.4 | P1 |
| F2 | **Bản đồ lộ trình có giá thật** | Mindtrip, Wanderlog | Mục 5.3 | P1 |
| F3 | **Tab chi phí** | Wanderlog | Mục 5.4, tách phần đã xác minh và phần ước tính | P1 |
| F4 | **Lịch giá** | OTA (Google Flights…) | Màu theo mức giá cho cả lộ trình, gợi ý ngày rẻ | P1 |
| F5 | **Chỉnh lộ trình bằng trợ lý** | Mindtrip | Mục 5.5 | P1 (dùng lại A12) |
| F6 | **Bản nháp chuyến đi / Bộ sưu tập** | Mindtrip Collections | Lưu nhiều phương án, so sánh | P1 |
| F7 | **"Remix" lộ trình** | Văn hóa remix của TikTok | Sau khi đi theo, người xem đăng bài **phiên bản của mình**, liên kết về bài gốc: *"Đi theo lộ trình của Linh, mình thêm 1 đêm"* | P2 |
| F8 | **Gợi ý từ ảnh / ảnh chụp màn hình** | Start Anywhere | Đúng B2 của đề bài: gửi ảnh → tìm chỗ tương tự trong Vinpearl, không có thì nói thật | P2 (B2) |
| F9 | **Danh sách chuẩn bị** | App lập lịch trình | Sinh từ lộ trình: giấy tờ, đồ mang theo, giờ check-in | P2 |
| F10 | **Xem lộ trình khi mất mạng** | — | Lộ trình đã đặt và đã lưu xem được offline | P1 (B4) |
| F11 | **Xuất lịch** | Mindtrip (lịch hẹn) | Thêm các mục đã đặt vào lịch điện thoại | P2 |
| F12 | **Ảnh chia sẻ lộ trình** | — | Biến lộ trình thành một ảnh đẹp để đăng mạng xã hội (B5) | P2 (B5) |
| F13 | **Magic link của người đăng** | Mindtrip Creator | Link chia sẻ có mã người đăng để ghi nhận người đi theo và thưởng | P2 (cần đồng ý) |
| F14 | **Lên kế hoạch nhóm, bình chọn** | Mindtrip | Mời bạn cùng sửa | V (ngoài phạm vi đề bài) |
| F15 | **Magic Camera / Near Me** | Mindtrip | Hỗ trợ khi đang ở điểm đến | V |

---

## 7. Mô hình dữ liệu bổ sung cho lộ trình có cấu trúc

```
TripPlan (thuộc một Post)
 ├─ title, vibes[], suitable_for, season_label, budget_bucket (tùy chọn, tự khai), sponsored (bool)
 ├─ origin_leg / return_leg: { mode: FLIGHT_BOOKED | BUS | CAR | MOTORBIKE | TRAIN | OTHER,
 │                              product_ref?, time_slot, note }
 └─ days[]: { day_offset }
      └─ blocks[]: { period: MORNING | AFTERNOON | EVENING | ALL_DAY }
           └─ stops[]: {
                type: BOOKED_ITEM | VINPEARL_UNBOOKED | FREE_POI | FOOD | TRANSFER | REST,
                product_ref?,         // có với BOOKED_ITEM, VINPEARL_UNBOOKED
                booking_item_ref?,    // nội bộ, chỉ để xác minh BOOKED_ITEM, KHÔNG công khai
                poi: { name, lat, lng }?,  // có với FREE_POI, FOOD
                duration_hint, note, est_cost_per_person? (chỉ cho mục không đặt qua app),
                media: { clip_ref?, video_start_s?, video_end_s?, photo_refs[] },
                move_to_next: { mode, minutes }?
              }
```

**Không có trong TripPlan**: ngày thật, giá đã trả, mã đặt chỗ, số hiệu chuyến kèm ngày, người đi cùng.

**Khi người xem bấm "Đi y chang"**: hệ thống tạo `BookingSession` từ TripPlan:
- Lấy các stop có `product_ref`.
- Ngày thật = ngày bắt đầu người xem chọn + `day_offset`.
- Chọn chuyến bay cùng `time_slot` từ nơi xuất phát của người xem.
- Tính số phòng theo sức chứa.
- Lấy giá hiện tại và chỗ trống.

---

## 8. Câu hỏi cần hỏi người hướng dẫn (bổ sung)

1. *"Người đăng muốn soạn lịch trình chi tiết theo ngày (sáng/chiều/tối, ăn ở đâu, đi lại thế nào). Em đề xuất tách hai lớp: phần đã đặt (hệ thống tự điền, có dấu xác minh) và phần kinh nghiệm (người đăng soạn, không có nút đặt). Như vậy có đúng tinh thần A2 'không phải gõ lại' không ạ?"*
2. *"Có được cho người đăng thêm dịch vụ Vinpearl mà họ không đặt qua app (ví dụ mua vé VinWonders tại cổng) vào lộ trình, gắn nhãn 'không đặt qua app', để người xem đặt được không ạ?"*
3. *"Điểm tự do và quán ăn ngoài Vinpearl có được hiện như 'gợi ý ghé thăm' (không giá, không nút đặt) không ạ?"*
4. *"Ước tính chi phí do người đăng tự khai cho phần ngoài app có bị coi là vi phạm 'không lộ số tiền người đăng đã trả' không? Em đề xuất chỉ cho khai theo khoảng và ghi rõ là ước tính."*

---

## 9. Nguồn tham khảo

- Mindtrip – tổng quan, Start Anywhere, creator: [Mindtrip homepage](https://mindtrip.ai/homepage) · [App Store – Mindtrip](https://apps.apple.com/us/app/mindtrip-ai-travel-companion/id6503107567) · [TechCrunch – Start Anywhere](https://techcrunch.com/?p=2816045) · [NewsBytes – tạo lịch trình từ ảnh chụp màn hình, video YouTube](https://www.newsbytesapp.com/news/science/travel-start-up-leverages-ai-to-build-itineraries/story) · [Offshore Travel Magazine](https://offshoretravelmagazine.com/new-ai-app-helps-travellers-build-an-itinerary-out-of-anything/) · [Turtle Fins Adventure – Start Anywhere](https://www.turtlefinsadventure.com/blog/1147/mindtrips-start-anywhere-the-future-of-flexible-travel-planning) · [Map Your Voyage – app lên kế hoạch từ mạng xã hội](https://mapyourvoyage.com/blog/best-apps-to-plan-trips-from-social-media)
- Mindtrip – review tính năng, đặt chỗ, giá: [aitravel.tools – review thực tế](https://aitravel.tools/mindtrip-review/) · [Teach Us Technology](https://teachustechnology.com/mindtrip-ai-trip-planner-with-in-chat-booking/) · [DataStudios](https://www.datastudios.org/post/mindtrip-ai-travel-planning-in-chat-booking-and-pricing) · [AI Tools HQ](https://www.ai-tools-hq.com/tools/mindtrip/) · [mwm.ai](https://mwm.ai/apps/mindtrip-ai-travel-companion/6503107567) · [Plotline – Mindtrip review](https://getplotline.app/blog/mindtrip-review) · [MonkeyTravel](https://monkeytravel.app/blog/mindtrip-review-2026) · [Monkey Eating Mango](https://monkeyeatingmango.com/blog/mindtrip-review-2026/) · [Jotform – AI trip planners](https://www.jotform.com/ai/best-ai-trip-planner/) · [SearchSpot](https://www.searchspot.ai/blog/mindtrip-ai-review-2026) · [Anywayr vs Mindtrip](https://anywayr.com/blog/anywayr-vs-mindtrip-ai-travel-planner) · [Tool Directory](https://tooldirectory.ai/tools/mindtrip) · [CB Insights](https://www.cbinsights.com/company/mindtrip)
- Wanderlog: [Wanderlog – trip planner app](https://wanderlog.com/trip-planner-mobile-app) · [Wanderlog – road trip planner](https://wanderlog.com/road-trip-planner) · [Stippl vs Wanderlog](https://www.stippl.io/blog/stippl-vs-wanderlog)
