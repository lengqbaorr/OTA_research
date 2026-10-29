# 04. Các ngành hàng (Product Verticals) & kiến thức nghiệp vụ

Mỗi ngành hàng có *ngôn ngữ, quy tắc giá, vòng đời đơn và rủi ro riêng*. Đây là phần BA phải nắm kỹ nhất.

---

## 4.1. Vé máy bay (Flights / Air)

### Khái niệm cốt lõi
| Thuật ngữ | Giải thích |
|---|---|
| **Itinerary / Hành trình** | One-way (1 chiều), Round-trip (khứ hồi), Multi-city/Open-jaw (nhiều chặng/đi một nơi về nơi khác) |
| **Segment / Leg** | Segment: một chặng có một số hiệu chuyến bay; Leg: đoạn cất-hạ cánh thực tế (một segment có thể có điểm dừng kỹ thuật) |
| **Direct / Non-stop / Connecting** | Bay thẳng / không dừng / nối chuyến |
| **Self-transfer / Virtual interlining** | Ghép 2 vé riêng của 2 hãng không liên danh – rẻ nhưng rủi ro lỡ chuyến (Kiwi.com nổi tiếng mô hình này) |
| **Codeshare / Interline** | Liên danh (một chuyến bán dưới nhiều số hiệu) / Hợp tác nối chuyến giữa hãng |
| **Cabin class** | Economy, Premium Economy, Business, First |
| **Booking class / RBD (Fare class)** | Hạng đặt chỗ bằng một chữ cái (Y, B, M, H, K, L…) quyết định giá & điều kiện |
| **Fare basis** | Mã điều kiện giá vé |
| **Fare family / Branded fares** | Gói giá có tên: Eco Lite, Eco Classic, Eco Flex (VNA: Phổ thông Tiết kiệm/Tiêu chuẩn/Linh hoạt); Vietjet: Eco, Deluxe, SkyBoss, Business |
| **Fare rules** | Điều kiện: đổi, hoàn, hành lý, thời hạn, phạt, no-show |
| **Taxes & fees / YQ-YR** | Thuế, phí sân bay, phụ thu nhiên liệu – thường chiếm phần lớn giá vé nội địa |
| **PNR (Passenger Name Record)** | Hồ sơ đặt chỗ, có mã 6 ký tự (booking code / record locator) |
| **Ticketing / E-ticket** | Xuất vé: PNR chỉ là giữ chỗ; *số vé điện tử* (13 số) mới là vé thật |
| **TTL (Ticketing Time Limit)** | Hạn xuất vé – giữ chỗ quá hạn sẽ bị hủy. Cho phép tính năng "giữ chỗ, thanh toán sau" |
| **EMD (Electronic Miscellaneous Document)** | Chứng từ cho dịch vụ bổ sung (hành lý, ghế) |
| **Ancillaries** | Hành lý ký gửi, chọn ghế, suất ăn, ưu tiên lên máy bay, phòng chờ |
| **PAX type** | ADT (người lớn ≥12), CHD (trẻ em 2–<12), INF (em bé <2, không ghế) |
| **APIS / SSR** | Thông tin hộ chiếu cho chuyến quốc tế / yêu cầu đặc biệt (xe lăn, suất ăn chay…) |
| **Void / Refund / Reissue / Exchange** | Hủy vé trong ngày (void, thường miễn phí) / hoàn vé / xuất lại vé khi đổi |
| **Schedule change / IRROPS** | Hãng thay đổi lịch / sự cố bất thường (hoãn, hủy) → OTA phải thông báo & xử lý cho khách |
| **Online check-in / Boarding pass** | Làm thủ tục trực tuyến |
| **GDS fare vs Private/Net fare** | Giá công bố vs giá ưu đãi riêng hãng cấp cho đại lý |
| **Look-to-book** | Tỷ lệ tìm kiếm/đặt – GDS có thể tính phí nếu tỷ lệ quá cao |
| **Caching** | OTA lưu tạm kết quả giá để giảm chi phí tra cứu – rủi ro "giá thay đổi khi đặt" (price change at booking) |

### Đặc thù kinh doanh
- Biên lợi nhuận thấp, cạnh tranh giá khốc liệt; khách so sánh trên Google Flights/Skyscanner.
- Tiền vé phải trả gần như ngay cho hãng/BSP → *không có float*, cần hạn mức ký quỹ/deposit với hãng LCC.
- Rủi ro lớn nhất: giá thay đổi khi thanh toán, xuất vé lỗi, sai tên hành khách (gần như không sửa được), hoàn vé chậm.
- Việt Nam: hãng chính Vietnam Airlines, Vietjet Air, Bamboo Airways, Vietravel Airlines, Pacific Airlines, Sun PhuQuoc Airways (mới ra mắt). Vietjet chiếm thị phần lớn nội địa.

---

## 4.2. Lưu trú (Accommodation / Hotels)

### Phân loại
Khách sạn (hotel), resort, căn hộ dịch vụ (serviced apartment), homestay, villa, hostel, nhà nghỉ, glamping, nhà cho thuê theo kỳ nghỉ (vacation rental / alternative accommodation).

### Khái niệm cốt lõi
| Thuật ngữ | Giải thích |
|---|---|
| **Property** | Một cơ sở lưu trú |
| **Room type** | Loại phòng (Deluxe Double, Superior Twin…) |
| **Rate plan** | Gói giá áp cho loại phòng: kèm/không kèm ăn sáng, hoàn/không hoàn, trả trước/trả sau, giá thành viên… |
| **Product = Room type × Rate plan** | Đơn vị bán thực tế |
| **Board / Meal plan** | RO (room only), BB (bed & breakfast), HB (half board), FB (full board), AI (all inclusive) |
| **BAR (Best Available Rate)** | Giá công khai tốt nhất tại thời điểm |
| **Rack rate** | Giá niêm yết (thường rất cao, ít khi bán) |
| **Net rate / Sell rate** | Giá net OTA trả khách sạn / giá bán cho khách |
| **Allotment / Allocation** | Số phòng giữ cho OTA |
| **Free sale / Freesell** | Bán không cần hỏi lại khách sạn đến khi khách sạn đóng (stop sell) |
| **On request** | Phải chờ khách sạn xác nhận |
| **Stop sell / Close out** | Ngừng bán |
| **Restrictions** | MinLOS/MaxLOS (số đêm tối thiểu/tối đa), CTA/CTD (đóng ngày đến/ngày đi), cut-off |
| **LOS (Length of Stay)** | Số đêm lưu trú |
| **Occupancy** | Số khách tối đa/phòng, chính sách trẻ em, giường phụ |
| **Cancellation policy** | Miễn phí đến X ngày trước, phạt 1 đêm / 100%, không hoàn (Non-refundable – NRF) |
| **No-show** | Khách không đến – thường tính phí đêm đầu hoặc toàn bộ |
| **Check-in / Check-out** | Ngày giờ nhận/trả phòng |
| **Rate parity** | Cam kết giá trên OTA không cao hơn kênh khác. EU và nhiều nước đã cấm "wide parity"; "narrow parity" (so với website khách sạn) vẫn còn ở một số nơi |
| **Overbooking / Walk** | Khách sạn bán vượt số phòng → phải chuyển khách sang nơi khác (walk) |
| **Mapping** | Ghép cùng một khách sạn/loại phòng từ nhiều nguồn (bedbank A, B, direct) về một bản ghi duy nhất – nghiệp vụ rất tốn công nhưng quyết định chất lượng |
| **Content** | Ảnh, mô tả, tiện ích (amenities), vị trí, chính sách, đánh giá |
| **Star rating vs Review score** | Hạng sao chính thức vs điểm đánh giá của khách |
| **City tax / Resort fee** | Thuế, phí phát sinh tại chỗ – cần hiển thị minh bạch |
| **VAT & phí dịch vụ** | Tại VN giá khách sạn thường "++" (chưa gồm 5% phí phục vụ + VAT) – cần hiển thị giá cuối |

### Đặc thù kinh doanh
- Biên tốt nhất, là trụ cột lợi nhuận.
- Long tail: hàng nghìn khách sạn nhỏ cần đội **Market Manager/BD** tại địa phương để ký hợp đồng, chụp ảnh, đào tạo dùng extranet.
- Bài toán chất lượng: giá sai (rate error), phòng ảo, ảnh không thật, khách sạn từ chối khách đã đặt.

---

## 4.3. Nhà cho thuê / Homestay (Vacation Rentals)
- Chủ nhà là cá nhân, quy mô nhỏ, ít dùng công nghệ.
- Khái niệm: *Instant book* vs *Request to book*, phí dọn dẹp (cleaning fee), tiền cọc (security deposit), quy định nhà (house rules), đánh giá hai chiều (khách chấm chủ, chủ chấm khách), xác minh danh tính.
- Pháp lý VN: đăng ký kinh doanh lưu trú, khai báo tạm trú khách với công an (nghĩa vụ của cơ sở lưu trú), PCCC.

---

## 4.4. Tour, hoạt động & trải nghiệm (Tours, Activities & Attractions – "Things to do")
| Loại | Ví dụ |
|---|---|
| Vé tham quan (Attraction tickets) | VinWonders, Sun World Bà Nà, bảo tàng, show |
| Tour ngày (Day tour) | Tour Hạ Long 1 ngày, city tour |
| Tour nhiều ngày / Package tour | Tour trọn gói 4N3Đ |
| Trải nghiệm (Experiences) | Lớp nấu ăn, lặn biển, spa |
| Dịch vụ du lịch (Travel services) | eSIM/SIM, Wi-Fi, đưa đón sân bay, thuê xe máy, fast-track, phòng chờ |
| Vé sự kiện | Concert, lễ hội |

Khái niệm: *Option/Package/Variant*, *Timeslot*, *Capacity*, *Instant confirmation vs Manual confirmation*, *Voucher (e-voucher, QR)*, *Redemption* (sử dụng/check-in vé), *Meeting point*, *Pickup*, *Validity* (vé mở theo ngày hay theo khoảng), *Min participants*, *Pricing theo nhóm tuổi (adult/child/senior)*.

Đặc thù: biên cao, cạnh tranh ít hơn về giá; nội dung (ảnh/video/review) là yếu tố quyết định; nguồn hàng phân tán, nhiều nhà cung cấp chưa số hóa.

---

## 4.5. Vận chuyển mặt đất
- **Xe khách liên tỉnh**: sơ đồ ghế/giường, điểm đón/trả, trung chuyển, giữ chỗ có thời hạn, nhà xe hủy chuyến. Việt Nam: Vexere, Futa (Phương Trang) app riêng, Traveloka bus.
- **Tàu hỏa**: Đường sắt VN; vé tàu quốc tế (Nhật JR Pass, châu Âu).
- **Thuê xe tự lái / có tài xế**: bảo hiểm, đặt cọc, bằng lái, nhận/trả xe.
- **Đưa đón sân bay (Airport transfer)**: giá cố định theo tuyến/loại xe, theo dõi chuyến bay để đón đúng giờ.
- **Phà, du thuyền (Cruise)**: Hạ Long cruise là sản phẩm đặc thù VN.

---

## 4.6. Gói du lịch (Packages)
- **Static package (gói cố định)**: tour operator đóng gói sẵn, giá cố định, lịch khởi hành cố định.
- **Dynamic package**: hệ thống ghép theo thời gian thực (vé + phòng). Thường *giá gói rẻ hơn mua lẻ* nhờ khách sạn cho giá đóng gói (package rate) không công khai.
- **Pháp lý**: ở nhiều nước (EU Package Travel Directive) bán gói = chịu trách nhiệm như nhà tổ chức tour (bảo vệ khách nếu nhà cung cấp phá sản). Tại VN, tour trọn gói thuộc kinh doanh lữ hành – cần giấy phép.

---

## 4.7. Sản phẩm bổ trợ (Ancillaries & Non-travel)
- **Bảo hiểm du lịch**: trễ chuyến, hủy chuyến, y tế, hành lý.
- **Visa / e-Visa**: dịch vụ hỗ trợ làm visa.
- **eSIM / Wi-Fi / SIM**.
- **Đổi ngoại tệ, thẻ du lịch**.
- **"Flexi" / bảo vệ hủy**: trả thêm phí để được hủy linh hoạt.
- **Price freeze / Price lock**: trả phí nhỏ để giữ giá vé trong X ngày (Hopper nổi tiếng).
- **Phòng chờ sân bay, fast-track, dịch vụ hành lý**.

---

## 4.8. Du lịch doanh nghiệp & MICE
- **Corporate travel / Business travel**: chính sách công tác (travel policy), duyệt chi (approval workflow), hóa đơn VAT doanh nghiệp, thanh toán công nợ, báo cáo chi phí, duty of care (biết nhân viên đang ở đâu khi có sự cố).
- **MICE (Meetings, Incentives, Conferences, Exhibitions)**: hội nghị, du lịch khen thưởng, sự kiện – báo giá theo yêu cầu (RFQ), đoàn lớn, quản lý danh sách đoàn (rooming list).
- **GIT vs FIT**: Group Inclusive Tour (khách đoàn) vs Free Independent Traveler (khách lẻ tự túc). OTA chủ yếu phục vụ FIT; GIT cần quy trình báo giá riêng.

---

## 4.9. So sánh nhanh các ngành hàng

| Tiêu chí | Vé máy bay | Khách sạn | Hoạt động/Tour | Xe khách | Bảo hiểm/eSIM |
|---|---|---|---|---|---|
| Tần suất mua | Cao | Trung bình | Trung bình | Rất cao (nội địa) | Kèm theo |
| Giá trị đơn | Trung bình–cao | Trung bình–cao | Thấp–trung bình | Thấp | Thấp |
| Biên (take rate) | Thấp (0–5%) | Cao (12–25%) | Cao (15–30%) | Thấp–TB (5–15%) | Rất cao |
| Độ phức tạp tích hợp | Rất cao (GDS/NDC/LCC) | Cao (mapping, channel manager) | TB (phân tán) | TB | Thấp |
| Rủi ro vận hành | Đổi lịch, hoàn vé, sai tên | Hết phòng, giá sai, không nhận khách | Hủy do thời tiết, không xác nhận | Nhà xe hủy, trễ | Bồi thường |
| Vai trò chiến lược | Thu hút traffic | Lợi nhuận | Khác biệt hóa & bán kèm | Tần suất, giữ chân | Tăng biên |
