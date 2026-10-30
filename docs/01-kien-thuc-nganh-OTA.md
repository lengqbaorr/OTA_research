# 01. Kiến thức nền ngành OTA (Online Travel Agency)

> Bản tóm gọn kiến thức ngành, ở góc nhìn PM / BA / PO, chưa đi vào công nghệ.
> Số liệu thị trường là số công bố gần nhất mà đội biết (chủ yếu năm 2024), làm tròn, chỉ dùng để định hướng. Phần pháp lý mang tính định hướng, cần người có chuyên môn xác nhận.

---

## 1. OTA là gì

**OTA** là doanh nghiệp **trung gian** bán dịch vụ du lịch (vé máy bay, phòng, tour, vé vui chơi, xe, bảo hiểm…) qua web/app. Ba đặc tính cốt lõi:

| Đặc tính | Hệ quả cho sản phẩm |
|---|---|
| **Trung gian**: thường không sở hữu tài sản, kết nối nhà cung cấp với khách | Phải quản lý nguồn hàng, nội dung, giá từ nhiều nơi |
| **Tồn kho dễ hỏng**: một đêm phòng hay một ghế máy bay không bán hôm nay là mất | Giá động, giữ chỗ, chống bán vượt (overbooking) |
| **Mua trước, dùng sau** | Rủi ro hủy, hoàn tiền, đổi lịch, gian lận; tiền khách trả trước (float) |

**Phân biệt nhanh**

| Khái niệm | Bản chất | Ví dụ |
|---|---|---|
| OTA | Bán và xử lý giao dịch | Booking.com, Agoda, Expedia, Trip.com, Traveloka |
| Metasearch | So sánh giá, chuyển khách đi nơi khác, thu phí click | Google Hotels/Flights, Skyscanner, Trivago |
| Kênh trực tiếp (Direct) | Nhà cung cấp tự bán | Vinpearl.com, app Vietnam Airlines |
| Tour operator / Lữ hành | Tự tạo sản phẩm tour, chịu trách nhiệm toàn trình | Vietravel, Saigontourist |
| Bedbank / Wholesaler | Bán buôn phòng B2B | Hotelbeds, WebBeds |
| Marketplace lưu trú | Cho thuê nhà P2P | Airbnb |
| Super app du lịch | OTA + tài chính + nội dung + loyalty | Trip.com, Traveloka, Klook |

**Phân loại OTA**: theo khách (B2C, B2B, B2B2C/white-label, corporate); theo phạm vi sản phẩm (chuyên biệt hoặc đa ngành); theo địa lý (toàn cầu, khu vực, nội địa); theo dòng khách (nội địa, inbound, outbound).

**Lịch sử rút gọn**: hệ thống đặt chỗ hàng không và GDS (1960–80) → OTA thế hệ đầu trên web (Expedia, Booking, Ctrip, cuối 1990) → hợp nhất và mô hình hoa hồng (2000s) → mobile, Airbnb, metasearch (2010s) → COVID làm doanh thu sụt mạnh, chính sách hủy linh hoạt lên ngôi (2020–22) → phục hồi, AI lên lịch trình, super app (2023–nay).

---

## 2. Hệ sinh thái & các bên tham gia

```
NHÀ CUNG CẤP            TRUNG GIAN PHÂN PHỐI               KÊNH BÁN              KHÁCH
Hãng bay      ──► GDS (Amadeus, Sabre), NDC, API hãng ──► OTA (B2C)       ──► Khách lẻ (FIT)
Khách sạn     ──► Channel Manager / CRS / Bedbank      ──► Metasearch      ──► Gia đình, nhóm
Khu vui chơi  ──► Hệ thống bán vé / API                ──► Đại lý, CTV     ──► Doanh nghiệp
Tour, xe      ──► DMC / Tour operator                  ──► App ví, ngân hàng
```

| Nhóm | Khái niệm cần nắm |
|---|---|
| **Hàng không** | **PNR** (hồ sơ đặt chỗ, mã 6 ký tự); **xuất vé** (số vé điện tử 13 số mới là vé thật); **TTL** (hạn xuất vé); **GDS**; **NDC** (chuẩn IATA bán trực tiếp qua API, bán được gói hành lý/ghế); **BSP** (thanh toán tập trung giữa đại lý IATA và hãng); hãng giá rẻ thường kết nối API riêng hoặc đại lý ký quỹ |
| **Lưu trú** | **PMS** (vận hành khách sạn); **CRS** (giá/tồn kho tập trung của chuỗi); **Channel Manager** (đồng bộ giá/phòng ra nhiều kênh); **Extranet** (cổng để khách sạn tự quản lý trên OTA); **Bedbank** (giải bài toán thiếu hàng lúc đầu); **RMS** (đề xuất giá) |
| **Thanh toán** | Cổng thanh toán (PSP), 3-D Secure (OTP), **chargeback** (khách đòi tiền qua ngân hàng), **thẻ ảo (VCC)** trả cho khách sạn, trả sau (BNPL) |
| **Marketing** | Metasearch (trả theo click), SEO/SEM, affiliate/KOL, CRM, loyalty |

**Một OTA thực chất là 4 sản phẩm**: (1) app/web cho khách; (2) cổng cho nhà cung cấp; (3) kênh cho đối tác/đại lý; (4) **back-office** nội bộ (đơn hàng, CS, đối soát, kiểm duyệt, chống gian lận, báo cáo). Phần "chìm" (2) và (4) quyết định khả năng mở rộng.

**Quan hệ vừa hợp tác vừa cạnh tranh**: Google vừa là nguồn traffic lớn nhất vừa là đối thủ; khách sạn vừa là đối tác vừa muốn kéo khách về kênh trực tiếp để khỏi trả hoa hồng 15–25%.

---

## 3. Mô hình kinh doanh & kinh tế đơn vị

| Mô hình | Cách vận hành | Ưu / nhược |
|---|---|---|
| **Agency** (hoa hồng) | Khách trả tại khách sạn; OTA thu hoa hồng hằng tháng | Ít rủi ro, không giữ tiền / khó kiểm soát giá, rủi ro no-show |
| **Merchant** (mua giá net, bán giá có markup) | OTA thu tiền trước, là bên đứng tên bán (merchant of record), trả nhà cung cấp sau | Biên cao, có float, đóng gói được combo / chịu rủi ro gian lận, hoàn tiền, thuế |
| **Hybrid** | Cùng một sản phẩm bán cả trả trước lẫn trả sau | Linh hoạt |
| Allotment | Mua trước một số phòng/ghế, có hạn trả lại (release) | Giá tốt, chịu rủi ro tồn |
| **Dynamic packaging** | Ghép vé + phòng (+ hoạt động) thành một giá theo thời gian thực | Rẻ hơn mua lẻ, biên tốt; ở nhiều nơi chịu luật về "gói du lịch" |
| **Static package / Combo** | Gói cố định soạn sẵn | Dễ vận hành |
| Marketplace | Nhà cung cấp tự đăng, sàn thu phí | Mở rộng nhanh |
| Quảng cáo / tăng hiển thị | Nhà cung cấp trả phí để được ưu tiên | Doanh thu thêm, cần minh bạch |

**Nguồn doanh thu điển hình**: hoa hồng khách sạn (15–25%), markup, phí dịch vụ vé máy bay (biên thấp), bán kèm (hành lý, ghế, bảo hiểm, eSIM, đưa đón: biên cao), hoạt động/tour (15–30%), phí đổi/hủy, quảng cáo, fintech (trả sau, FX), B2B/white-label.

**Chỉ số kinh tế**: **GBV** (tổng giá trị giao dịch), **Net revenue**, **Take rate** = doanh thu thuần / GBV (các OTA lớn khoảng 12–14%), room nights, ADR (giá phòng trung bình/đêm), AOV (giá trị đơn), CAC, LTV, tỷ lệ hủy, **direct share** (tỷ lệ khách đến không qua kênh trả phí).

**"Định luật" của ngành**
1. Biên mỏng trên GBV nhưng quy mô lớn. Marketing thường chiếm 40–50% doanh thu của OTA lớn.
2. Vé máy bay kéo traffic, khách sạn và hoạt động tạo lợi nhuận, bán kèm tăng biên.
3. Chuyển đổi = **có chỗ thật + giá tốt + nội dung đủ + niềm tin**.
4. Hiệu ứng mạng hai phía: nhiều khách kéo nhà cung cấp và ngược lại. Khởi động khi chưa có gì (cold start) là bài toán khó nhất.
5. **Look-to-book** rất cao: hàng nghìn lượt tìm mới ra một đơn.
6. Trải nghiệm **khi có sự cố** (hủy chuyến, hết phòng, hoàn tiền) quyết định niềm tin.

---

## 4. Ngành hàng & thuật ngữ nghiệp vụ

### 4.1. Vé máy bay
- **Hành trình**: một chiều, khứ hồi, nhiều chặng. **Segment**: chặng có số hiệu bay. Bay thẳng hoặc nối chuyến; codeshare (liên danh).
- **Hạng**: cabin (Economy/Business…) khác **hạng đặt chỗ** (RBD: một chữ cái quyết định giá và điều kiện). **Fare family** là gói giá có tên (ví dụ Eco/Deluxe/SkyBoss).
- **Điều kiện vé**: đổi, hoàn, hành lý, no-show. Thuế phí và phụ thu chiếm phần lớn giá vé nội địa.
- **Hành khách**: ADT (≥ 12 tuổi), CHD (2 đến dưới 12), INF (dưới 2). Sai tên gần như không sửa được.
- **Thao tác**: void (hủy trong ngày), refund (hoàn), reissue (đổi). Hãng đổi lịch hoặc sự cố bất thường (IRROPS) thì phải báo khách và xử lý.
- **Rủi ro**: giá đổi lúc đặt (do cache), xuất vé lỗi, hoàn vé chậm, gian lận thẻ.

### 4.2. Lưu trú
- **Sản phẩm bán = loại phòng × gói giá (rate plan)**: kèm ăn sáng hay không, hoàn hủy hay không, trả trước hay trả sau.
- **Chế độ ăn**: RO / BB / HB / FB / AI. **BAR**: giá công khai tốt nhất. **Net rate / Sell rate**.
- **Tồn kho**: allotment, free sale, on request, stop sell. Ràng buộc: MinLOS/MaxLOS (số đêm tối thiểu/tối đa), CTA/CTD (đóng ngày đến/ngày đi).
- **Chính sách**: hủy miễn phí đến X ngày, không hoàn (NRF), no-show, sức chứa và trẻ em.
- **Rate parity**: cam kết ngang giá giữa các kênh, nhiều nơi đã hạn chế. **Overbooking/Walk**: bán vượt thì phải chuyển khách đi nơi khác. **Mapping**: ghép trùng cùng một khách sạn từ nhiều nguồn.
- Tại VN giá thường "++" (chưa phí phục vụ và VAT) → **phải hiện giá cuối**.

### 4.3. Hoạt động, vé vui chơi, trải nghiệm
- Biến thể/gói, khung giờ, sức chứa, xác nhận tức thì hay thủ công, e-voucher/QR, **redemption** (sử dụng tại cổng), thời hạn vé, giá theo độ tuổi.
- Biên cao. Nội dung (ảnh, video, review) là yếu tố quyết định.

### 4.4. Gói / Combo
- **Gói cố định** (soạn sẵn) hoặc **gói động** (ghép theo thời gian thực).
- Thường rẻ hơn mua lẻ nhờ giá đóng gói không công khai.
- Bán gói ở nhiều nơi kéo theo trách nhiệm như nhà tổ chức tour. Tại VN, tour trọn gói thuộc kinh doanh lữ hành, cần giấy phép.

### 4.5. So sánh nhanh

| | Vé máy bay | Khách sạn | Vé vui chơi / hoạt động | Combo |
|---|---|---|---|---|
| Tần suất | Cao | TB | TB | TB |
| Biên | Thấp | Cao | Cao | Cao |
| Độ phức tạp | Rất cao | Cao | TB | Cao (ghép nhiều mục) |
| Rủi ro chính | Đổi lịch, hoàn vé | Hết phòng, giá sai | Hủy do thời tiết | Một mục hỏng kéo cả gói |

---

## 5. Vận hành: vòng đời đơn & các nghiệp vụ lõi

```
Tìm → Chọn → Kiểm tra lại giá/chỗ → Giữ chỗ (có hạn) → Thanh toán → Xác nhận với nhà cung cấp
→ Phát hành vé/voucher → Thông báo → [Đổi / Hủy / Hoàn] → Sử dụng dịch vụ → Đối soát → Review
```

| Trạng thái đơn | Ý nghĩa |
|---|---|
| Chờ thanh toán | Đã giữ chỗ, có hạn |
| Đang xử lý | Đã nhận tiền, đang xác nhận |
| Đã xác nhận | Có chỗ / đã xuất vé |
| Thất bại | Đã trả tiền mà không giữ được chỗ → hoàn tiền hoặc đề xuất thay thế (**nguồn khiếu nại số 1**) |
| Đã hủy / Đang hoàn / Đã hoàn | — |
| Hoàn thành / No-show | Đã dùng / không đến |

| Nghiệp vụ | Điểm mấu chốt |
|---|---|
| **Giá & giữ chỗ** | Giá có thể đổi giữa lúc xem và lúc trả; cần báo giá có hạn và giữ chỗ có thời hạn |
| **Hủy / đổi / hoàn** | Chính sách của nhà cung cấp + phí của OTA; tính đúng tiền phạt và tiền hoàn; theo dõi thời gian hoàn |
| **Sự cố (IRROPS, overbooking)** | Báo khách, đưa lựa chọn, OTA có thể chịu chi phí khắc phục |
| **CS** | Đa kênh, 24/7 vì sự cố xảy ra ngoài giờ; chỉ số: thời gian phản hồi, tỷ lệ liên hệ trên mỗi đơn, CSAT; tự phục vụ giúp giảm tải |
| **Thanh toán & đối soát** | Đối soát 3 bên: đơn ↔ nhà cung cấp ↔ cổng thanh toán; lệch số liệu là chuyện hằng ngày |
| **Chống gian lận** | Thẻ đánh cắp, lạm dụng khuyến mãi bằng nhiều tài khoản, chargeback, chiếm tài khoản; biện pháp: 3DS, giới hạn theo thiết bị/IP, chấm điểm rủi ro, xem xét thủ công; luôn đánh đổi giữa chặn nhầm và lọt |
| **Nội dung & review** | Chuẩn hóa dữ liệu, kiểm duyệt ảnh, chống review giả; **review đã xác minh** (chỉ người đã ở mới được viết) là chuẩn của các OTA lớn |

---

## 6. Hành trình khách hàng & danh mục tính năng

| Giai đoạn | Khách nghĩ gì | OTA làm gì |
|---|---|---|
| **Mơ ước** | "Đi đâu?" (mạng xã hội, bạn bè) | Nội dung, gợi ý theo ngân sách/mùa, AI gợi ý |
| **Lên kế hoạch** | "Ở đâu, bao nhiêu tiền?" | Tìm kiếm, bộ lọc, review, bản đồ, lưu |
| **Đặt chỗ** | "An toàn không, rẻ nhất chưa?" | Checkout nhanh, giá minh bạch, nhiều phương thức thanh toán, xác nhận tức thì |
| **Chuẩn bị & trải nghiệm** | "Đi lại thế nào?" | Quản lý chuyến đi, nhắc lịch, báo thay đổi, bán thêm tại điểm |
| **Chia sẻ** | "Chuyến đi thế nào?" | Review, điểm thưởng, gợi ý chuyến tiếp theo |

**Danh mục tính năng tiêu chuẩn**
- **Khách**: tìm kiếm, bộ lọc, chi tiết, wishlist, cảnh báo giá, checkout, khách không cần tài khoản, mã giảm giá, quản lý đơn, tự hủy/đổi, theo dõi hoàn tiền, review, loyalty.
- **Nhà cung cấp**: nội dung, giá/tồn kho, khuyến mãi, đơn, phản hồi review, báo cáo.
- **Back-office**: quản lý đơn, CS, nội dung/mapping, giá & markup, khuyến mãi, thanh toán/hoàn, đối soát, gian lận, kiểm duyệt, BI, phân quyền & **nhật ký thao tác (audit log)**.

---

## 7. Đối thủ tiêu biểu & bài học

| Nền tảng | Thế mạnh | Bài học |
|---|---|---|
| **Booking.com** (Booking Holdings, doanh thu tập đoàn ~23,7 tỷ USD) | Phủ lưu trú toàn cầu, hủy miễn phí, review đã xác minh, Genius | Chính sách linh hoạt + review thật tạo niềm tin |
| **Agoda** (Booking Holdings) | Khách sạn châu Á, văn hóa thử nghiệm A/B | Ra quyết định bằng dữ liệu |
| **Expedia** (~13,7 tỷ USD) | Đa ngành, gói Flight + Hotel, B2B, One Key | Dynamic packaging, loyalty hợp nhất |
| **Trip.com** (~53 tỷ NDT) | Vé máy bay, CS 24/7, **Trip Moments** (nội dung người dùng), **TripGenie** (trợ lý AI) | Nội dung người dùng + AI + dịch vụ |
| **Traveloka** | Super app ĐNA, thanh toán bản địa, PayLater | Thanh toán bản địa quyết định chuyển đổi |
| **Klook / KKday** | Hoạt động và vé vui chơi châu Á | Ngách biên cao, kéo khách qua mạng xã hội |
| **Airbnb** | Cộng đồng, **review hai chiều**, Experiences | Niềm tin cộng đồng |
| **Google Travel** | Điểm khởi đầu tìm kiếm | Đối thủ "ngồi trên" mọi OTA |

**Thị trường Việt Nam**: OTA ngoại chiếm phần lớn mảng khách sạn. OTA nội (Mytour, iVIVU, Gotadi, Vexere…) cạnh tranh bằng combo, ngách và B2B. Nhà cung cấp lớn (Vinpearl, Sun Group, các hãng bay) có kênh trực tiếp và loyalty riêng. Đặc điểm: thanh toán QR/chuyển khoản phổ biến, mua qua mạng xã hội/Zalo, mùa vụ rõ (Tết, hè, lễ), nhạy giá, thích combo, cần hóa đơn VAT.

**Mẫu xấu cần tránh (dark patterns)**: khan hiếm giả ("chỉ còn 1 phòng!" sai sự thật), đồng hồ đếm ngược giả, giá nhỏ giọt (drip pricing: hiện giá thấp rồi cộng phí dần), tự tick mua thêm. Nhiều cơ quan quản lý trên thế giới đã xử phạt các OTA vì những hành vi này.

---

## 8. Pháp lý & tuân thủ (định hướng, Việt Nam)

| Chủ đề | Điểm chính |
|---|---|
| **Luật Du lịch 2017** | Kinh doanh lữ hành (tour, gói) là ngành có điều kiện: giấy phép nội địa/quốc tế, ký quỹ (tham khảo NĐ 94/2021: nội địa 100 triệu, inbound 250 triệu, outbound 500 triệu) |
| **Thương mại điện tử** (NĐ 52/2013, sửa đổi bởi NĐ 85/2021; luật TMĐT mới cần kiểm tra hiệu lực) | Website bán hàng: thông báo; sàn: đăng ký với Bộ Công Thương |
| **Bảo vệ người tiêu dùng 2023** | Minh bạch giá cuối, điều kiện; không quảng cáo sai |
| **Dữ liệu cá nhân** (NĐ 13/2023; Luật BVDLCN 2025, cần kiểm tra hiệu lực) | Đồng ý rõ ràng, mục đích, quyền xóa, chuyển dữ liệu ra nước ngoài, dữ liệu nhạy cảm (giấy tờ, trẻ em) |
| **Thanh toán** | Chỉ dùng trung gian được cấp phép; điểm thưởng quy đổi tiền phải cẩn trọng |
| **Thuế & hóa đơn điện tử** | Mô hình agency hay merchant quyết định cách xuất hóa đơn |
| **Khuyến mại & quảng cáo** | Hạn mức giảm giá, đăng ký khuyến mại; nội dung KOL/hợp tác phải ghi nhãn |
| **Quốc tế** | PCI DSS (dữ liệu thẻ), GDPR/PDPA, chỉ thị gói du lịch của EU, quyền lợi hành khách |

---

## 9. KPI chuẩn ngành

| Nhóm | Chỉ số |
|---|---|
| Kinh doanh | GBV, Net revenue, Take rate, số đơn/room nights, AOV, tỷ lệ bán kèm, tỷ lệ hủy, contribution margin/đơn, CAC, LTV, tỷ lệ khách quay lại |
| Phễu sản phẩm | Traffic → tìm kiếm có kết quả → xem chi tiết → checkout → **thanh toán thành công** → **đặt thành công** (đã trả và xác nhận được) |
| Vận hành | Tỷ lệ xác nhận tức thì, thời gian hoàn tiền, tỷ lệ liên hệ/đơn, CSAT/NPS, tỷ lệ chargeback, tổn thất gian lận |

---

## 10. Xu hướng 2024–2026
1. **AI tạo sinh & AI agent**: trợ lý lên lịch trình, tìm bằng ngôn ngữ tự nhiên. Yêu cầu bắt buộc: không bịa giá và chỗ.
2. **Nội dung người dùng & video ngắn** là nơi hành trình bắt đầu (TikTok, Reels). OTA đưa UGC vào app (Trip Moments).
3. **Connected trip**: một giỏ hàng cho cả chuyến, một nơi hỗ trợ.
4. Fintech du lịch (trả sau, giữ giá), loyalty và app-first.
5. Trải nghiệm (experiences) tăng nhanh nhất.
6. Quy định chặt hơn về minh bạch giá, dark patterns, dữ liệu cá nhân.

---

## 11. Thuật ngữ cốt lõi

| Thuật ngữ | Nghĩa |
|---|---|
| ADR | Giá phòng trung bình/đêm |
| Allotment / Release | Phòng/ghế giữ riêng cho đại lý / hạn trả lại |
| Ancillary | Dịch vụ bán kèm |
| BAR / Rack rate / Net rate | Giá công khai tốt nhất / giá niêm yết / giá vốn |
| Booking engine | Công cụ đặt chỗ |
| Channel Manager / PMS / CRS | Đồng bộ kênh / quản lý khách sạn / đặt chỗ trung tâm |
| Chargeback | Khách đòi tiền qua ngân hàng phát hành thẻ |
| Combo / Dynamic packaging | Gói cố định / gói ghép động |
| FIT / GIT | Khách lẻ / khách đoàn |
| GBV / Take rate | Tổng giá trị giao dịch / doanh thu thuần trên GBV |
| GDS / NDC / BSP | Hệ phân phối toàn cầu / chuẩn bán trực tiếp của IATA / thanh toán đại lý–hãng |
| Hold / TTL | Giữ chỗ / hạn xuất vé |
| Idempotency | Một yêu cầu gửi nhiều lần chỉ được xử lý một lần (chống trừ tiền hai lần) |
| IRROPS | Sự cố khai thác (hoãn/hủy chuyến) |
| Look-to-book | Tỷ lệ lượt tìm / lượt đặt |
| Merchant of record | Bên đứng tên bán, chịu trách nhiệm giao dịch |
| MinLOS / CTA / CTD | Số đêm tối thiểu / đóng ngày đến / đóng ngày đi |
| No-show / NRF | Không đến / không hoàn |
| Overbooking / Walk | Bán vượt / chuyển khách |
| PNR | Hồ sơ đặt chỗ hàng không |
| Rate parity | Ngang giá giữa các kênh |
| Rate plan | Gói giá của loại phòng |
| Reconciliation | Đối soát |
| Redemption | Sử dụng vé/voucher tại điểm |
| UGC / Verified review | Nội dung người dùng / review đã xác minh lưu trú |
