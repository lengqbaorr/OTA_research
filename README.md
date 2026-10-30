# OTA Research – Nghiên cứu nền tảng ngành Online Travel Agency

Bộ tài liệu nghiên cứu **nghiệp vụ & ngành** (chưa đi vào công nghệ) phục vụ vai trò PM / BA / PO khi chuẩn bị xây dựng hệ thống (web, app) cho khối Dịch vụ du lịch.

## Đề bài dự án (bắt buộc tuân theo)

- [Tài liệu đặc tả sản phẩm: Vinpearl cho người trẻ](de-bai/01-tai-lieu-dac-ta-san-pham.md) — đề bài do ban tổ chức cung cấp, là **nguồn sự thật cao nhất**. Khi tài liệu nghiên cứu mâu thuẫn với đề bài thì đề bài thắng.

## Tài liệu bám theo đề bài

| # | Tài liệu | Nội dung chính |
|---|---|---|
| 13 | [Phân tích đề bài](docs/13-phan-tich-de-bai-vinpearl.md) | Hiểu bài toán, persona → yêu cầu, ma trận truy vết A1–A16, đối chiếu tài liệu cũ, điểm dễ trượt khi bị thử, giả định, câu hỏi cho người hướng dẫn |
| 14 | [Đề xuất cho câu hỏi mở](docs/14-de-xuat-cau-hoi-mo.md) | Trả lời toàn bộ câu hỏi mở mục 5: xu hướng, bài đăng, lộ trình, trợ lý, đặt chỗ — đề xuất, lý do, cách thể hiện trong sản phẩm |
| 15 | [Đề xuất cách đo & kiểm thử](docs/15-de-xuat-cach-do-va-kiem-thu.md) | Chỉ số và ngưỡng cho 8 mục thành công (mục 9), kịch bản kiểm thử thanh toán/chỗ cuối/đồng thời, kiểm chứng trung thực & an toàn (mục 6) |
| 16 | [Cách tiếp cận & kế hoạch](docs/16-cach-tiep-can-va-ke-hoach.md) | Nguyên tắc, kiến trúc khái niệm, phương án cho từng sản phẩm, mô hình dữ liệu & quy ước API, lựa chọn phần B, kế hoạch 4 mốc, rủi ro |

## Tài liệu nghiên cứu nền (trước khi có đề bài)

| # | Tài liệu | Nội dung chính |
|---|---|---|
| 01 | [Tổng quan OTA](docs/01-tong-quan-OTA.md) | Định nghĩa, phân biệt khái niệm, lịch sử, chuỗi giá trị, phân loại, các "định luật" của ngành |
| 02 | [Hệ sinh thái & các bên tham gia](docs/02-he-sinh-thai-va-cac-ben-tham-gia.md) | Stakeholders, GDS, NDC, BSP, channel manager, bedbank, PMS, thanh toán, metasearch |
| 03 | [Mô hình kinh doanh & doanh thu](docs/03-mo-hinh-kinh-doanh-va-doanh-thu.md) | Agency / Merchant / Hybrid, dynamic packaging, nguồn doanh thu, unit economics, chiến lược tăng trưởng |
| 04 | [Ngành hàng sản phẩm](docs/04-nganh-hang-san-pham.md) | Vé máy bay, lưu trú, homestay, tour/hoạt động, vận chuyển, gói, bổ trợ, corporate & MICE |
| 05 | [Phân tích đối thủ](docs/05-phan-tich-doi-thu.md) | Booking, Expedia, Trip.com, Agoda, Traveloka, Klook, Airbnb, thị trường Việt Nam, benchmark tính năng |
| 06 | [Hành trình khách hàng & tính năng](docs/06-hanh-trinh-khach-hang-va-tinh-nang.md) | Customer journey, persona, capability map (B2C, Extranet, B2B, Back-office), gợi ý phân pha |
| 07 | [Vận hành & quy trình nghiệp vụ](docs/07-van-hanh-va-quy-trinh-nghiep-vu.md) | Vòng đời đơn, hủy/hoàn, IRROPS, CS, đối soát, gian lận, pricing, tổ chức đội ngũ |
| 08 | [Pháp lý & tuân thủ](docs/08-phap-ly-va-tuan-thu.md) | Luật Du lịch, TMĐT, BVQLNTD, dữ liệu cá nhân, thanh toán, thuế/hóa đơn, quy định quốc tế |
| 09 | [KPI, xu hướng & định hướng](docs/09-kpi-xu-huong-va-dinh-huong.md) | Bộ KPI, xu hướng 2024–2026, các hướng định vị, câu hỏi cần trả lời trước khi viết requirement |
| 10 | [Từ điển thuật ngữ](docs/10-thuat-ngu-glossary.md) | Glossary Anh – Việt |
| 11 | [Ý tưởng Social-to-Booking](docs/11-y-tuong-social-to-booking.md) | *(Viết trước đề bài, một phần ngoài phạm vi)* Ý tưởng nội dung mạng xã hội → chuyến đi đã đặt |
| 12 | [Đánh giá tính khả thi](docs/12-danh-gia-tinh-kha-thi.md) | *(Viết trước đề bài, không áp dụng trực tiếp)* Feasibility study cho ý tưởng OTA độc lập |

## Cách đọc
- **Chuẩn bị demo theo đề bài: de-bai → 13 → 14 → 15 → 16.**
- Người mới vào ngành: 01 → 02 → 03 → 10.
- BA chuẩn bị viết requirement: 04 → 06 → 07 → 08.
- PO/PM định hướng sản phẩm: 05 → 09.

## Lưu ý
- Số liệu thị trường/tài chính mang tính tham khảo (chủ yếu số công bố năm 2024), cần kiểm tra lại nguồn chính thức mới nhất trước khi dùng trong tài liệu ra quyết định.
- Phần pháp lý mang tính định hướng, cần luật sư xác nhận văn bản hiện hành.
