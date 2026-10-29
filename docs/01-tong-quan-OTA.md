# 01. Tổng quan về OTA (Online Travel Agency)

> Góc nhìn: PM / BA / PO — hiểu ngành trước khi hiểu công nghệ.

## 1. Định nghĩa

**OTA (Online Travel Agency – Đại lý du lịch trực tuyến)** là doanh nghiệp *trung gian* bán các sản phẩm/dịch vụ du lịch (vé máy bay, phòng khách sạn, tour, hoạt động trải nghiệm, xe đưa đón, thuê xe, bảo hiểm, visa…) cho khách hàng thông qua kênh số (website, app), thay cho văn phòng đại lý truyền thống.

Ba đặc tính cốt lõi:

| Đặc tính | Giải thích |
|---|---|
| **Trung gian (Intermediary)** | OTA thường *không sở hữu* tài sản (máy bay, khách sạn). OTA kết nối **Cung (Supplier)** và **Cầu (Traveler)**. |
| **Tồn kho dễ hỏng (Perishable inventory)** | Một đêm phòng hoặc một ghế máy bay không bán được hôm nay thì mất vĩnh viễn → toàn ngành xoay quanh *giá động*, *phân phối* và *tối ưu doanh thu*. |
| **Mua trước – dùng sau (Pre-purchase, deferred fulfillment)** | Khách trả tiền/đặt chỗ hôm nay, trải nghiệm dịch vụ sau vài ngày đến vài tháng → phát sinh rủi ro hủy, hoàn tiền, thay đổi, gian lận, dòng tiền (float). |

### Phân biệt với các khái niệm gần

| Khái niệm | Bản chất | Ví dụ |
|---|---|---|
| **OTA** | Bán & xử lý giao dịch đặt chỗ (khách thanh toán/đặt trên nền tảng của OTA) | Booking.com, Agoda, Expedia, Trip.com, Traveloka |
| **Metasearch (Công cụ so sánh giá)** | Không bán; tổng hợp giá từ nhiều OTA/nhà cung cấp rồi chuyển khách sang nơi bán, thu phí click (CPC) hoặc hoa hồng | Google Hotels/Flights, Skyscanner, Kayak, Trivago, Tripadvisor |
| **Supplier Direct (Kênh trực tiếp)** | Nhà cung cấp tự bán qua website/app của mình (brand.com) | Vietnam Airlines app, Marriott Bonvoy, Vinpearl.com |
| **TA truyền thống (Brick & mortar travel agent)** | Đại lý có văn phòng, tư vấn trực tiếp | Các đại lý vé, công ty lữ hành |
| **Tour Operator / Công ty lữ hành** | *Tự tạo ra sản phẩm* tour (gom dịch vụ + chịu trách nhiệm toàn trình) | Vietravel, Saigontourist, TUI |
| **TMC (Travel Management Company)** | OTA/đại lý cho doanh nghiệp: quản lý công tác phí, chính sách đi lại | Amex GBT, CWT, BCD, Navan, TripActions |
| **Vacation Rental Platform / Marketplace** | Nền tảng cho thuê nhà/căn hộ của cá nhân (P2P) | Airbnb, Vrbo |
| **Super App du lịch** | OTA mở rộng thành hệ sinh thái nhiều ngành hàng + tài chính + nội dung | Trip.com, Traveloka, Klook |
| **DMC (Destination Management Company)** | Công ty dịch vụ tại điểm đến, cung cấp land tour, xe, HDV cho đối tác B2B | Các DMC inbound Việt Nam |
| **Bedbank / Wholesaler** | Mua phòng số lượng lớn với giá net rồi bán lại cho đại lý/OTA (B2B) | Hotelbeds, WebBeds, TBO, RateHawk |

## 2. Lịch sử phát triển (rút gọn)

| Giai đoạn | Mốc chính | Ý nghĩa với người làm sản phẩm |
|---|---|---|
| **1960–1980: Kỷ nguyên CRS/GDS** | American Airlines + IBM tạo SABRE (1960); ra đời các GDS: Sabre, Amadeus (1987), Galileo, Worldspan | Hạ tầng đặt chỗ hàng không được chuẩn hóa — đến nay vẫn là xương sống phân phối vé máy bay |
| **1995–2000: OTA thế hệ 1** | Expedia (Microsoft, 1996), Travelocity (Sabre, 1996), Priceline (1997, "Name Your Own Price"), Booking.com (1996, Hà Lan), Ctrip (1999, Trung Quốc) | Đưa GDS lên web; khách tự tra cứu & đặt |
| **2000–2010: Hợp nhất & mô hình agency** | Priceline mua Booking.com (2005), Agoda (2007); Expedia tách khỏi IAC, mua Hotels.com, Trivago; TripAdvisor bùng nổ review | Mô hình **Agency (hoa hồng, trả tại khách sạn)** của Booking thắng thế ở thị trường khách sạn độc lập |
| **2008–2015: Sharing economy & Mobile** | Airbnb (2008); smartphone; metasearch (Kayak, Trivago, Skyscanner); Traveloka (2012); Klook (2014) | Mobile-first; người dùng đặt sát ngày (last-minute); nội dung UGC |
| **2015–2019: Super app & Châu Á trỗi dậy** | Ctrip đổi tên Trip.com Group, mua Skyscanner (2016); Expedia mua HomeAway; Booking mua Kayak; Google Flights/Hotels lớn mạnh | Cạnh tranh "chi phí thu hút khách" (CAC) với Google; mở rộng ngành hàng |
| **2020–2022: COVID-19** | Doanh thu ngành sụt 50–80%; hoàn tiền hàng loạt; chuyển hướng du lịch nội địa, staycation | Bài học về **chính sách hủy linh hoạt**, quản trị dòng tiền, voucher, "book now pay later" |
| **2023–nay: Phục hồi & AI** | Du lịch vượt mức 2019; Gen AI trip planner (Expedia, Booking, Trip.com TripGenie); NDC của IATA; luật DMA của EU ép Google/Booking | Cá nhân hóa, trợ lý hội thoại, kết nối trực tiếp hãng bay (NDC), connected trip, fintech du lịch |

## 3. Vị trí của OTA trong chuỗi giá trị du lịch

```
  [NHÀ CUNG CẤP / SUPPLIER]          [TRUNG GIAN PHÂN PHỐI]              [KÊNH BÁN]                [KHÁCH]
  - Hãng hàng không         ──►  GDS (Amadeus, Sabre, Travelport) ──►                        
  - Khách sạn / Resort      ──►  Channel Manager / CRS              ──►  OTA (B2C)       ──►   Khách lẻ (FIT)
  - Homestay / Villa        ──►  Bedbank / Wholesaler (B2B)         ──►  Metasearch      ──►   Gia đình, nhóm
  - Nhà xe / Đường sắt      ──►  Aggregator / Consolidator          ──►  Đại lý cấp 2    ──►   Doanh nghiệp (MICE/BTA)
  - Điểm tham quan / Tour   ──►  DMC / Tour Operator                ──►  TMC (Corporate) ──►   Đoàn (GIT)
  - Bảo hiểm, Visa, eSIM    ──►  API Partner                        ──►  Super App/Ví    
```

Nhận xét quan trọng cho PO:

1. **OTA có thể nằm ở nhiều vị trí**: vừa lấy hàng trực tiếp từ nhà cung cấp (direct contract), vừa lấy qua trung gian (GDS, bedbank), vừa bán lại cho đại lý khác (B2B/affiliate/white-label).
2. **Giá trị OTA mang lại cho nhà cung cấp**: khả năng tiếp cận khách toàn cầu, marketing, công nghệ, thanh toán đa phương thức, gánh rủi ro gian lận.
3. **Giá trị OTA mang lại cho khách**: lựa chọn rộng, so sánh minh bạch, đánh giá thật, thanh toán thuận tiện, hỗ trợ khi có sự cố, combo rẻ hơn mua lẻ.
4. **Mâu thuẫn cố hữu**: nhà cung cấp muốn kéo khách về kênh trực tiếp (giảm hoa hồng 15–25%), OTA muốn giữ khách → các chủ đề *rate parity*, *billboard effect*, *loyalty*.

## 4. Phân loại OTA

### 4.1. Theo khách hàng mục tiêu
- **B2C**: bán trực tiếp cho người tiêu dùng (Agoda, Traveloka).
- **B2B**: cung cấp hàng/công cụ cho đại lý, công ty lữ hành (Hotelbeds, TBO, các "đại lý cấp 1" vé máy bay ở Việt Nam).
- **B2B2C**: cung cấp nền tảng/hàng hóa cho đối tác bán lại cho khách cuối (white-label cho ngân hàng, ví điện tử, hãng hàng không; ví dụ: phần du lịch trong app ngân hàng, MoMo, ZaloPay).
- **B2E / Corporate**: phục vụ nhân viên doanh nghiệp đi công tác (TMC, Self-booking tool).

### 4.2. Theo phạm vi sản phẩm
- **Chuyên biệt (Vertical/Specialist)**: chỉ khách sạn (Agoda thời đầu), chỉ vé xe (Vexere), chỉ hoạt động (Klook, GetYourGuide, Viator), chỉ vé máy bay.
- **Đa ngành (Full-service / Horizontal)**: Expedia, Trip.com, Traveloka.
- **Super app**: tích hợp thêm tài chính (trả sau, bảo hiểm, thẻ), nội dung, loyalty, dịch vụ đời sống.

### 4.3. Theo phạm vi địa lý
- **Toàn cầu (Global)**: Booking.com, Expedia.
- **Khu vực (Regional)**: Traveloka, Agoda (mạnh ở châu Á), MakeMyTrip (Ấn Độ), Despegar (Mỹ Latinh), Rakuten Travel (Nhật).
- **Nội địa (Local)**: Mytour, iVIVU, Vexere (Việt Nam); Yanolja (Hàn); Tiket.com (Indonesia).

### 4.4. Theo hướng dòng khách (rất quan trọng tại Việt Nam)
- **Outbound**: người Việt đi nước ngoài.
- **Inbound**: khách quốc tế vào Việt Nam.
- **Domestic (Nội địa)**: người Việt đi trong nước — phân khúc lớn nhất về số lượt.

## 5. Các "định luật" của ngành OTA cần nắm

1. **Đây là ngành biên lợi nhuận mỏng trên giá trị giao dịch, nhưng quy mô lớn**: take rate (doanh thu/GBV) thường 3–6% với vé máy bay, 12–20% với khách sạn, 15–30% với hoạt động/tour.
2. **Chi phí marketing là chi phí lớn nhất**: các OTA lớn chi 40–50% doanh thu cho marketing (chủ yếu Google Ads/Metasearch). Mấu chốt là **tăng tỷ trọng khách quay lại/trực tiếp (direct/app)**.
3. **Khách sạn là "mỏ vàng", vé máy bay là "mồi câu"**: vé máy bay có tần suất tìm kiếm cao, biên thấp → dùng để thu hút; khách sạn/hoạt động/bảo hiểm bán kèm (cross-sell) mới tạo lợi nhuận.
4. **Tồn kho + Giá + Nội dung + Niềm tin = Chuyển đổi**: khách chỉ đặt khi thấy *có phòng/chỗ*, *giá tốt*, *thông tin đủ*, *tin tưởng thanh toán & hỗ trợ*.
5. **Hiệu ứng mạng lưới hai phía (two-sided network effect)**: nhiều khách → nhiều nhà cung cấp muốn lên sàn → nhiều lựa chọn → thêm khách. Khởi động "cold start" là bài toán khó nhất của OTA mới.
6. **Look-to-book ratio rất cao**: hàng nghìn lượt tìm kiếm mới ra 1 đơn → chi phí tra cứu (đặc biệt GDS) là vấn đề kinh doanh, không chỉ kỹ thuật.
7. **Dịch vụ hậu mãi quyết định thương hiệu**: trải nghiệm khi *có sự cố* (chuyến bay hoãn, khách sạn hết phòng, hoàn tiền) tạo nên hay phá hủy niềm tin.
