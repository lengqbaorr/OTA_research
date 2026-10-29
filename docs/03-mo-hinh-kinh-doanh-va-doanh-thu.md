# 03. Mô hình kinh doanh & nguồn doanh thu

## 1. Ba mô hình cốt lõi

### 1.1. Agency Model (Mô hình đại lý – hưởng hoa hồng)
- OTA chỉ là kênh hiển thị & nhận đặt chỗ. **Khách trả tiền tại khách sạn** (pay at hotel/pay at property) hoặc OTA thu hộ.
- Nhà cung cấp là "người bán" (merchant of record), xuất hóa đơn cho khách.
- OTA **xuất hóa đơn hoa hồng** cho nhà cung cấp hàng tháng (thường 15–25% với khách sạn).
- Ưu: không giữ tiền, ít rủi ro, dễ thu hút khách sạn nhỏ (không cần thẻ ảo). Booking.com phát triển nhờ mô hình này.
- Nhược: OTA khó kiểm soát giá cuối, rủi ro *không đến (no-show)* và *thu hồi công nợ hoa hồng*.

### 1.2. Merchant Model (Mô hình mua đi bán lại / thu tiền trước)
- OTA ký **giá net** với nhà cung cấp, tự định giá bán (cộng **markup**), **thu tiền khách trước** (prepaid), rồi thanh toán cho nhà cung cấp sau (thường sau check-out, qua chuyển khoản hoặc thẻ ảo VCC).
- OTA là merchant of record → chịu trách nhiệm hóa đơn, thuế, chargeback.
- Ưu: biên cao hơn, linh hoạt đóng gói (combo/package có thể "giấu" giá thành phần), **dòng tiền âm thuận (negative working capital/float)** — giữ tiền khách trước khi trả nhà cung cấp.
- Nhược: rủi ro gian lận, hoàn tiền, tỷ giá, thuế.
- Expedia, Agoda (phần lớn), Traveloka chủ yếu theo mô hình này.

### 1.3. Hybrid / Payments facilitation
- Kết hợp cả hai: cùng một khách sạn có thể bán *pay now* và *pay later*. Booking.com dần mở rộng "Payments by Booking.com" để thu hộ và kiếm thêm từ thanh toán.

### 1.4. Các mô hình phụ khác
| Mô hình | Mô tả |
|---|---|
| **Allotment / Hard block** | OTA mua trước một số phòng/ghế cố định (thường giá rẻ hơn), chịu rủi ro không bán hết. Phổ biến trong mùa cao điểm, combo, charter flight. |
| **Release period** | Allotment có thời hạn trả lại phòng chưa bán cho khách sạn (ví dụ 7 ngày trước ngày đến). |
| **Wholesale / B2B resale** | Bán hàng cho đại lý khác ở giá net + markup B2B. |
| **Marketplace (Sàn)** | Nhà cung cấp tự đăng, OTA thu phí giao dịch (Airbnb thu phí cả khách & chủ nhà; Klook/KKday với hoạt động). |
| **Dynamic Packaging** | Tự động ghép vé máy bay + khách sạn (+ xe, + hoạt động) thành gói với một giá → khách không thấy giá lẻ, OTA tối ưu biên. Expedia "Flight + Hotel", Traveloka "Combo tiết kiệm", Trip.com "Flight + Hotel". |
| **Opaque pricing (giá ẩn)** | Bán giá rẻ nhưng giấu tên khách sạn/hãng đến khi trả tiền (Priceline Express Deals, Hotwire). Giúp khách sạn xả phòng không phá giá công khai. |
| **Subscription / Membership** | Gói thành viên trả phí lấy ưu đãi (ít phổ biến ở OTA, phổ biến hơn ở loyalty miễn phí như Genius, OneKeyCash). |
| **SaaS cho nhà cung cấp** | Bán phần mềm (PMS, channel manager, BMS nhà xe) để khóa nguồn hàng – ví dụ Vexere, Trip.com, Yanolja. |
| **Advertising (Quảng cáo)** | Nhà cung cấp trả tiền để được ưu tiên hiển thị (Sponsored listing, Preferred Partner, Agoda AGP, Booking Visibility Booster). |

## 2. Danh mục nguồn doanh thu (Revenue Streams)

| # | Nguồn | Mô tả | Mức tham khảo (ước lượng ngành) |
|---|---|---|---|
| 1 | **Hoa hồng khách sạn** | % trên giá phòng | 15–25% |
| 2 | **Markup (Merchant)** | Chênh lệch giá bán – giá net | 10–25% |
| 3 | **Phí dịch vụ vé máy bay** | Service fee/booking fee cộng vào vé; hoa hồng hãng (hiện rất thấp, thường 0–3%) | vài chục đến vài trăm nghìn VNĐ/vé |
| 4 | **GDS incentive** | GDS trả thưởng cho đại lý theo số segment đặt | vài USD/segment |
| 5 | **Ancillary – bán kèm** | Hành lý, chọn ghế, suất ăn, bảo hiểm, eSIM, đưa đón, phòng chờ, fast-track | Biên cao (bảo hiểm 20–40%) |
| 6 | **Hoạt động/Tour/Vé tham quan** | Hoa hồng/markup | 15–30% |
| 7 | **Phí thay đổi/hủy** | Phí xử lý đổi vé, hoàn vé của OTA | Cố định/lần |
| 8 | **Quảng cáo & tăng hiển thị** | Nhà cung cấp trả phí ưu tiên | Thêm vài % hoa hồng |
| 9 | **Fintech** | Trả góp, BNPL, thẻ đồng thương hiệu, phí chuyển đổi tiền tệ (FX margin), lãi từ float | Đang tăng mạnh |
| 10 | **B2B / API / White-label** | Phí nền tảng, hoa hồng chia sẻ | Tùy hợp đồng |
| 11 | **Dữ liệu/SaaS** | Phần mềm cho nhà cung cấp, insight thị trường | Thuê bao |
| 12 | **Flexi/Protect products** | "Hủy linh hoạt", "Bảo vệ giá", "Bảo đảm chuyến bay nối chuyến" (Kiwi.com guarantee) | Phí bảo hiểm rủi ro do OTA tự định giá |

## 3. Kinh tế đơn vị (Unit Economics) – các khái niệm cần nắm

| Thuật ngữ | Định nghĩa |
|---|---|
| **GBV / GMV / Gross Bookings** | Tổng giá trị giao dịch khách trả (gồm thuế, phí) – thước đo quy mô |
| **Net Revenue** | Doanh thu thuần OTA giữ lại (hoa hồng, markup, phí) |
| **Take Rate** | Net Revenue / GBV. Booking Holdings ~14%, Expedia ~12–13% (do nhiều vé máy bay), Airbnb ~13–14% |
| **Room nights** | Số đêm phòng bán ra – KPI cốt lõi khách sạn |
| **Segments / Tickets / PAX** | Số chặng bay / số vé / số khách |
| **ADR (Average Daily Rate)** | Giá phòng trung bình/đêm |
| **AOV / ABV (Average Booking Value)** | Giá trị trung bình mỗi đơn |
| **Contribution margin / đơn** | Net revenue – chi phí biến đổi (thanh toán, CS, gian lận, marketing đơn đó) |
| **CAC** | Chi phí thu hút một khách mới |
| **LTV / CLV** | Giá trị vòng đời khách hàng |
| **ROAS** | Doanh thu/chi phí quảng cáo |
| **Direct share** | % booking đến từ kênh không mất phí (app, gõ trực tiếp, CRM) – OTA lớn đều cố nâng chỉ số này |
| **Cancellation rate** | Tỷ lệ hủy (khách sạn trên OTA có thể 30–40% với giá hủy miễn phí) |
| **Float / Working capital** | Tiền khách trả trước mà OTA chưa trả cho nhà cung cấp |

### Ví dụ minh họa unit economics (giả định)

```
Đơn khách sạn 2 đêm, ADR 1.500.000đ → GBV = 3.000.000đ
Mô hình merchant, markup 15%  → Net revenue ≈ 390.000đ
 - Phí thanh toán (1,8%)                      -54.000đ
 - Chi phí marketing (CPC Google/Meta)        -180.000đ
 - CS + vận hành                               -30.000đ
 - Dự phòng gian lận/chargeback/hủy            -20.000đ
 = Contribution ≈ 106.000đ (~3,5% GBV)
```
→ Bài toán lợi nhuận: giảm chi phí marketing trên mỗi đơn bằng khách quay lại, app, loyalty; tăng bán kèm.

## 4. Chiến lược tăng trưởng điển hình của OTA

1. **Supply first, demand later (hoặc ngược lại)**: chọn ngách để giải quyết cold start (ví dụ Vexere bắt đầu từ phần mềm nhà xe; Agoda từ khách sạn châu Á; Klook từ vé tham quan HK).
2. **Land & expand ngành hàng**: bắt đầu một ngành có tần suất cao (vé máy bay, xe khách) → bán chéo khách sạn, hoạt động, bảo hiểm.
3. **Connected trip**: gom toàn bộ chuyến đi trong một giỏ hàng/một hành trình (chiến lược Booking Holdings).
4. **Loyalty & app-first**: chuyển khách từ Google sang app.
5. **B2B2C**: dùng đối tác (ngân hàng, ví, hãng bay) làm kênh phân phối giá rẻ.
6. **Fintech**: thanh toán, trả sau, bảo hiểm → tăng chuyển đổi và doanh thu ngoài lề.
7. **Content & Community**: review, bài viết, video ngắn, influencer → thu hút từ giai đoạn "mơ ước" (inspiration).
8. **AI**: trợ lý lên lịch trình, tìm kiếm bằng ngôn ngữ tự nhiên, hỗ trợ khách tự động.
