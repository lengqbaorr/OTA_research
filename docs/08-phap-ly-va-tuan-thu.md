# 08. Pháp lý & tuân thủ (Legal & Compliance)

> ⚠️ Đây là tổng quan định hướng để PM/BA biết *cần hỏi gì*, **không thay thế tư vấn pháp lý**. Văn bản pháp luật Việt Nam thay đổi thường xuyên — cần luật sư xác nhận phiên bản hiện hành trước khi ra quyết định.

## 1. Việt Nam

### 1.1. Kinh doanh du lịch – Luật Du lịch 2017 & văn bản hướng dẫn
- **Kinh doanh dịch vụ lữ hành** (tổ chức, bán chương trình du lịch/tour) là ngành nghề kinh doanh **có điều kiện**, cần **giấy phép kinh doanh dịch vụ lữ hành**:
  - Lữ hành **nội địa**: phục vụ khách nội địa.
  - Lữ hành **quốc tế**: phục vụ khách inbound và/hoặc outbound.
- **Điều kiện chính**: doanh nghiệp đăng ký ngành nghề; **ký quỹ** tại ngân hàng (mức theo Nghị định 94/2021 sửa Nghị định 168/2017, tham khảo: nội địa 100 triệu; inbound 250 triệu; outbound 500 triệu; inbound + outbound 500 triệu); người phụ trách kinh doanh lữ hành có trình độ/chứng chỉ nghiệp vụ theo quy định.
- **Câu hỏi then chốt cho mô hình OTA**: chỉ bán *dịch vụ riêng lẻ* (phòng khách sạn, vé máy bay) có phải là "lữ hành" không? Thông thường bán lẻ từng dịch vụ là *đại lý dịch vụ*, còn **đóng gói thành chương trình du lịch/combo/tour** thì thuộc lữ hành → cần làm rõ với luật sư khi thiết kế sản phẩm combo/dynamic package.
- **Đại lý lữ hành**: có thể bán tour của doanh nghiệp lữ hành khác theo hợp đồng đại lý (phải thông báo với cơ quan quản lý).
- **Cơ sở lưu trú**: phải đáp ứng điều kiện kinh doanh lưu trú, khai báo lưu trú khách với công an — OTA nên yêu cầu nhà cung cấp cam kết tính pháp lý.

### 1.2. Thương mại điện tử
- **Nghị định 52/2013/NĐ-CP** (sửa đổi bởi **Nghị định 85/2021/NĐ-CP**) về TMĐT: website/app bán hàng phải **thông báo** với Bộ Công Thương; nếu là **sàn giao dịch TMĐT** (cho bên thứ ba đăng bán – ví dụ khách sạn tự đăng) thì phải **đăng ký** và có quy chế hoạt động, cơ chế giải quyết khiếu nại, trách nhiệm kiểm soát người bán.
- **Luật Thương mại điện tử** mới (đã được Quốc hội xem xét/ban hành trong 2025 — cần kiểm tra hiệu lực và nghị định hướng dẫn) có thể thay đổi nghĩa vụ của nền tảng: định danh người bán, livestream, trách nhiệm nền tảng nước ngoài.
- Xác định rõ mô hình: **website bán hàng (tự bán)** hay **sàn (marketplace)** hay **cả hai** → ảnh hưởng nghĩa vụ pháp lý và thiết kế sản phẩm (hiển thị thông tin người bán, quy chế…).

### 1.3. Bảo vệ người tiêu dùng
- **Luật Bảo vệ quyền lợi người tiêu dùng 2023** (hiệu lực từ 1/7/2024): minh bạch thông tin, giá, điều kiện; hợp đồng theo mẫu & điều kiện giao dịch chung (điều khoản sử dụng, chính sách hủy) có thể phải đăng ký với cơ quan quản lý cạnh tranh trong một số lĩnh vực; nghĩa vụ của nền tảng số trung gian.
- Thực tế sản phẩm: hiển thị **giá cuối cùng** (đã gồm thuế, phí), không "phí ẩn"; quảng cáo khuyến mãi trung thực; chính sách hủy/hoàn rõ ràng trước khi thanh toán.

### 1.4. Dữ liệu cá nhân
- **Nghị định 13/2023/NĐ-CP** về bảo vệ dữ liệu cá nhân, và **Luật Bảo vệ dữ liệu cá nhân 2025** (hiệu lực dự kiến từ 1/1/2026 — kiểm tra lại cùng nghị định hướng dẫn).
- OTA xử lý nhiều **dữ liệu nhạy cảm**: hộ chiếu/CCCD, ngày sinh, thông tin trẻ em, vị trí, thông tin tài chính.
- Yêu cầu nghiệp vụ: đồng ý (consent) rõ ràng, mục đích sử dụng, quyền xóa/rút lại đồng ý, đánh giá tác động xử lý dữ liệu, **chuyển dữ liệu ra nước ngoài** (gửi cho khách sạn/hãng bay nước ngoài – cần hồ sơ đánh giá), thông báo vi phạm.
- **Luật An ninh mạng 2018** & Nghị định 53/2022: yêu cầu lưu trữ dữ liệu tại Việt Nam trong một số trường hợp.

### 1.5. Thanh toán & tài chính
- Chỉ dùng **trung gian thanh toán được NHNN cấp phép**; OTA không tự giữ tiền như ví nếu không có giấy phép.
- Ví/điểm thưởng quy đổi thành tiền, "credit hoàn tiền" cần xem xét để không bị coi là phát hành phương tiện thanh toán trái phép.
- Trả sau/BNPL cần hợp tác với tổ chức tín dụng/công ty tài chính.
- Thu ngoại tệ: hạn chế sử dụng ngoại hối trên lãnh thổ VN — giá niêm yết và thu bằng VNĐ cho khách trong nước.

### 1.6. Thuế & hóa đơn
- **Hóa đơn điện tử** (Nghị định 123/2020, sửa đổi bởi Nghị định 70/2025): xuất hóa đơn cho khách khi bán.
- Vấn đề kép: OTA xuất hóa đơn **toàn bộ giá trị** (mô hình merchant – doanh thu gộp) hay **chỉ phí dịch vụ/hoa hồng** (agency – doanh thu thuần)? Ảnh hưởng lớn đến thuế, kế toán và trải nghiệm khách (khách doanh nghiệp cần hóa đơn VAT của khách sạn).
- Vé máy bay: hãng/đại lý có quy định riêng về hóa đơn.
- Nền tảng TMĐT có thể có nghĩa vụ **khấu trừ, nộp thay thuế** cho hộ/cá nhân kinh doanh bán trên sàn (homestay cá nhân…) theo quy định mới về quản lý thuế TMĐT từ 2025 — cần kiểm tra.
- Nhà cung cấp nước ngoài không có cơ sở thường trú (Booking, Agoda…) đăng ký & nộp thuế qua cổng thuế điện tử cho nhà cung cấp nước ngoài.

### 1.7. Hàng không
- Đại lý bán vé phải có hợp đồng đại lý với hãng hoặc đại lý cấp trên; quyền xuất vé quốc tế thường thông qua IATA (BSP Việt Nam).
- Quy định về quyền lợi hành khách khi chậm/hủy chuyến (Thông tư của Bộ GTVT/Bộ Xây dựng – cơ quan quản lý hàng không), quy định giá vé trần nội địa.

### 1.8. Quảng cáo & khuyến mại
- **Luật Quảng cáo** (sửa đổi 2025 – kiểm tra hiệu lực): quảng cáo trên mạng, KOL/influencer có trách nhiệm.
- **Luật Thương mại & Nghị định 81/2018** về khuyến mại: hạn mức giảm giá (tối đa 50%, ngoại lệ trong chương trình khuyến mại tập trung), đăng ký/thông báo khuyến mại, quay thưởng.

## 2. Quốc tế (khi mở rộng hoặc phục vụ khách nước ngoài)

| Quy định/Chuẩn | Nội dung |
|---|---|
| **PCI DSS** | Chuẩn bảo mật dữ liệu thẻ; nếu không muốn gánh nặng, dùng cổng thanh toán lưu thẻ (tokenization) |
| **GDPR (EU), PDPA (Singapore, Thái Lan), PIPL (Trung Quốc)** | Bảo vệ dữ liệu khi phục vụ công dân các nước này |
| **EU Package Travel Directive** | Người bán gói (package/linked travel arrangement) chịu trách nhiệm toàn bộ & phải có bảo đảm phá sản |
| **EU DMA (Digital Markets Act)** | Booking.com bị xếp là "gatekeeper" (2024) – cấm parity clauses tại EU |
| **Quy định quyền hành khách (EU261, US DOT)** | Bồi thường khi chậm/hủy chuyến; US DOT yêu cầu hoàn tiền tự động khi hãng hủy, minh bạch phí |
| **Thuế VAT/GST số** | Nhiều nước yêu cầu nền tảng nước ngoài nộp thuế dịch vụ số |
| **IATA accreditation** | Để xuất vé qua BSP |

## 3. Checklist câu hỏi pháp lý cho giai đoạn khởi động dự án

1. Pháp nhân nào vận hành? Đăng ký ngành nghề gì?
2. Có bán tour/combo không → cần giấy phép lữ hành loại nào, ký quỹ bao nhiêu?
3. Website/app là "bán hàng" hay "sàn TMĐT" → thông báo hay đăng ký với Bộ Công Thương?
4. Mô hình doanh thu (agency/merchant) → xuất hóa đơn thế nào, thuế ra sao?
5. Đối tác thanh toán nào được cấp phép? Có cần ví/điểm thưởng?
6. Dữ liệu cá nhân: thu gì, lưu ở đâu, chuyển ra nước ngoài không, đồng ý thế nào?
7. Điều khoản sử dụng, chính sách bảo mật, chính sách hủy/hoàn, quy chế sàn — ai soạn, có cần đăng ký?
8. Hợp đồng mẫu với nhà cung cấp (khách sạn, nhà xe, tour), với đại lý/CTV.
9. Trách nhiệm khi nhà cung cấp không thực hiện dịch vụ (OTA có phải bồi thường?).
10. Quy định khuyến mại khi chạy flash sale/mã giảm giá.
