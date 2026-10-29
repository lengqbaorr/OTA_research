# 02. Hệ sinh thái & các bên tham gia (Stakeholders)

## 1. Bản đồ các bên liên quan

| Nhóm | Vai trò | Ví dụ | Điều OTA cần từ họ |
|---|---|---|---|
| **Khách du lịch (Traveler)** | Người dùng cuối, có thể khác người trả tiền (booker ≠ traveler) | Cá nhân, gia đình, nhóm bạn, doanh nghiệp | Nhu cầu, thanh toán, đánh giá |
| **Nhà cung cấp (Supplier / Principal)** | Sở hữu & cung cấp dịch vụ | Hãng bay, khách sạn, homestay, nhà xe, công ty tour, điểm vui chơi | Tồn kho (inventory), giá (rates), nội dung, chính sách |
| **Trung gian phân phối** | Gom hàng, chuẩn hóa, kết nối | GDS, bedbank, channel manager, consolidator | Kết nối API, giá net, phạm vi hàng |
| **Đối tác thanh toán** | Thu tiền, chuyển tiền, chống gian lận | Cổng thanh toán (VNPay, OnePay, MoMo, ZaloPay, Stripe, Adyen), ngân hàng, thẻ quốc tế, BNPL | Phương thức thanh toán, chi phí MDR, tỷ lệ thành công |
| **Đối tác tiếp thị & lưu lượng** | Đưa khách đến | Google, Meta, TikTok, Metasearch, Affiliate/KOL, ngân hàng (co-brand) | Traffic, chi phí CPC/CPA |
| **Đối tác bán lại (Distribution partners)** | Bán lại hàng của OTA | Đại lý cấp 2, CTV (cộng tác viên), white-label, app ngân hàng/ví | Hoa hồng, công cụ đặt chỗ |
| **Nhà cung cấp dịch vụ bổ trợ** | Bảo hiểm, visa, eSIM, đổi tiền, đưa đón | Bảo hiểm du lịch, Airalo... | Sản phẩm bán kèm (ancillary) |
| **Cơ quan quản lý** | Cấp phép, thuế, bảo vệ người tiêu dùng, dữ liệu | Bộ VHTTDL, Cục Du lịch Quốc gia, Bộ Công Thương (TMĐT), Thuế, Bộ Công an (dữ liệu cá nhân), Cục Hàng không | Giấy phép, tuân thủ |
| **Hiệp hội ngành** | Chuẩn mực, chứng nhận | IATA (vé máy bay, BSP), UFTAA, VITA (Hiệp hội Du lịch VN), HTTA | Chứng nhận, quyền xuất vé |
| **Nhà đầu tư** | Vốn | VC, tập đoàn | Tăng trưởng GBV, unit economics |
| **Nội bộ OTA** | Vận hành | Sales/Market manager (BD), Content, CS, Finance, Fraud, Marketing, Product/Tech | Công cụ back-office |

## 2. Các hệ thống/khái niệm phân phối (hiểu ở mức nghiệp vụ)

### 2.1. Phía hàng không
- **CRS của hãng (Airline Reservation System / PSS – Passenger Service System)**: hệ thống quản lý chỗ của chính hãng (ví dụ Amadeus Altéa, Navitaire cho LCC, Sabre SabreSonic).
- **GDS (Global Distribution System)**: "chợ đầu mối" kết nối hàng trăm hãng bay (và cả khách sạn, xe) với đại lý. Ba GDS lớn: **Amadeus**, **Sabre**, **Travelport** (Galileo/Apollo/Worldspan). Đại lý trả phí/được thưởng theo segment.
- **NDC (New Distribution Capability)**: chuẩn của IATA cho phép hãng phân phối trực tiếp qua API, bán được nội dung phong phú (gói giá, hành lý, chọn ghế, suất ăn) – thứ GDS truyền thống bán kém. Nhiều hãng lớn đang ép đại lý chuyển sang NDC (phụ phí khi đặt qua GDS cũ).
- **LCC Direct Connect**: hãng giá rẻ (Vietjet, AirAsia, Bamboo trước đây…) thường không/ít qua GDS mà cho đại lý kết nối API riêng hoặc đại lý nạp tiền (top-up/deposit) để đặt.
- **BSP (Billing and Settlement Plan) – IATA**: hệ thống thanh toán tập trung giữa đại lý được IATA công nhận và các hãng bay. Đại lý IATA có quyền xuất vé (ticketing authority).
- **Consolidator / Ticketing agent (Đại lý cấp 1)**: đại lý có quyền xuất vé, bán lại cho các đại lý nhỏ hơn (cấp 2) chưa có quyền xuất vé.

### 2.2. Phía lưu trú
- **PMS (Property Management System)**: phần mềm vận hành khách sạn (lễ tân, buồng phòng, hóa đơn). Ví dụ Opera, eZCloud, Cloudbeds.
- **CRS khách sạn (Central Reservation System)**: quản lý giá/tồn kho tập trung cho chuỗi.
- **Channel Manager**: công cụ để khách sạn cập nhật giá & phòng một lần, đồng bộ ra nhiều OTA (SiteMinder, STAAH, D-EDGE, RateGain…). OTA *phải* kết nối với channel manager để có hàng khách sạn độc lập.
- **Extranet**: cổng web của OTA để khách sạn tự quản lý giá, phòng, khuyến mãi, xem booking (Booking.com Extranet, Agoda YCS).
- **Bedbank**: kho phòng B2B (Hotelbeds, WebBeds, Expedia Partner Solutions/EPS Rapid, TBO, RateHawk, DidaTravel). OTA mới thường dùng bedbank để có ngay hàng triệu khách sạn → giải quyết bài toán cold start.
- **RMS (Revenue Management System)**: công cụ đề xuất giá tối ưu cho khách sạn (IDeaS, Duetto).
- **Booking Engine (IBE)**: module đặt phòng trên website riêng của khách sạn.

### 2.3. Phía hoạt động, tour, vận chuyển mặt đất
- **Reservation system cho tour/hoạt động**: Bókun, Rezdy, FareHarbor, TourCMS.
- **Hệ thống quản lý nhà xe / bán vé xe**: tại Việt Nam Vexere cung cấp cả phần mềm quản lý cho nhà xe (BMS) và kênh bán.
- **Đường sắt**: Đường sắt Việt Nam (dsvn.vn) có kênh bán điện tử; quốc tế có Rail Europe, Trainline, 12Go Asia (xe/tàu/phà Đông Nam Á).
- **Thuê xe**: Rentalcars, Discover Cars; hệ thống GDS cũng có car.
- **Đưa đón sân bay**: Klook, Kiwitaxi, GetTransfer, đối tác xe công nghệ (Grab, Xanh SM).

### 2.4. Phía thanh toán & tài chính
- **PSP / Payment Gateway**: cổng thanh toán.
- **Acquirer / Issuer**: ngân hàng thanh toán / ngân hàng phát hành thẻ.
- **Virtual Card (VCC – Thẻ ảo)**: OTA thanh toán cho khách sạn bằng thẻ ảo dùng một lần (merchant model).
- **Chargeback**: khách khiếu nại với ngân hàng phát hành để đòi tiền → OTA phải chứng minh.
- **3DS (3-D Secure)**: xác thực thẻ (OTP) giảm gian lận.
- **BNPL (Buy Now Pay Later)**: mua trước trả sau (Traveloka PayLater, Kredivo, Atome).
- **Multi-currency / FX**: hiển thị & thu nhiều loại tiền, rủi ro tỷ giá.

### 2.5. Phía marketing & traffic
- **Metasearch**: Google Hotel Ads, Google Flights, Trivago, Skyscanner, Kayak, Tripadvisor. Mô hình CPC (trả theo click), CPA (trả theo đơn), commission.
- **SEO / SEM**: tối ưu tìm kiếm tự nhiên và quảng cáo trả phí.
- **Affiliate**: website/blogger/KOL nhận hoa hồng.
- **CRM / Loyalty**: email, push notification, điểm thưởng, hạng thành viên.

## 3. Hai phía của một OTA = hai bộ sản phẩm

Một OTA trên thực tế là **ít nhất 3–4 sản phẩm** khác nhau:

1. **Consumer product (B2C)**: web/app cho khách tìm, đặt, quản lý chuyến đi.
2. **Supplier product (Extranet / Partner Hub)**: cho khách sạn/nhà xe/nhà tour quản lý giá, phòng, nội dung, khuyến mãi, booking, thanh toán, báo cáo.
3. **Partner / Agent product (B2B portal, API, white-label, affiliate)**: cho đại lý, CTV, đối tác bán lại.
4. **Internal back-office**: CS, vận hành đơn, kế toán–đối soát, quản trị nội dung, quản lý khuyến mãi, chống gian lận, quản lý hợp đồng nhà cung cấp, báo cáo BI.

> **Bài học PO**: rất nhiều OTA mới chỉ tập trung vào màn hình B2C, trong khi "khối chìm" (supplier & back-office) mới quyết định khả năng mở rộng và chi phí vận hành.

## 4. Mối quan hệ "đối tác – đối thủ" (Co-opetition)

- **Google**: là nguồn traffic lớn nhất *và* đối thủ (Google Flights/Hotels giữ khách trên trang).
- **Nhà cung cấp**: là đối tác *và* đối thủ (kênh direct).
- **Các OTA với nhau**: cạnh tranh B2C nhưng mua bán hàng của nhau qua B2B (ví dụ Agoda bán phòng có nguồn từ bedbank; Expedia bán hàng cho OTA khác qua EPS).
- **Super app/ví/ngân hàng**: vừa là kênh phân phối white-label, vừa có thể tự làm du lịch.
