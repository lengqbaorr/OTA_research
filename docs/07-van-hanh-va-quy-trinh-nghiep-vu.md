# 07. Vận hành & quy trình nghiệp vụ cốt lõi

## 1. Vòng đời một đơn đặt chỗ (Booking Lifecycle)

```
Search → Select → Price check (re-validate) → Hold/Pre-book → Payment → Confirm with supplier
   → Issue (ticket/voucher) → Notify customer → [Modify / Cancel / Refund] → Service delivery (travel date)
   → Post-stay/No-show → Supplier settlement → Reconciliation → Review
```

### Các trạng thái đơn điển hình (ở mức nghiệp vụ)
| Trạng thái | Ý nghĩa |
|---|---|
| Pending payment | Đã giữ chỗ, chờ thanh toán (có hạn) |
| Paid / Processing | Đã nhận tiền, đang xác nhận với nhà cung cấp |
| Confirmed | Nhà cung cấp đã xác nhận / đã xuất vé |
| On request | Chờ nhà cung cấp xác nhận thủ công |
| Failed | Thanh toán xong nhưng không giữ được chỗ → phải hoàn tiền hoặc đề xuất thay thế |
| Modified | Đã thay đổi |
| Cancelled | Đã hủy (có/không phí) |
| Refund pending / Refunded | Đang/đã hoàn tiền |
| Completed / Checked-out / Flown | Dịch vụ đã sử dụng |
| No-show | Khách không sử dụng |

> **Lưu ý nghiệp vụ quan trọng**: trường hợp "*khách đã trả tiền nhưng nhà cung cấp không xác nhận*" (booking failure) là nguồn khiếu nại số 1 → cần quy trình xử lý rõ (tự động hoàn, gợi ý phòng tương đương, CS gọi lại).

## 2. Quy trình theo từng nghiệp vụ

### 2.1. Thay đổi & Hủy (Amendment & Cancellation)
- Áp dụng **chính sách hủy của nhà cung cấp** + **chính sách/phí của OTA**.
- Phải tính được: phí phạt, số tiền hoàn, phương thức hoàn (về nguồn thanh toán, ví, điểm/credit), thời gian hoàn.
- Vé máy bay: phân biệt void (cùng ngày), refund (theo điều kiện giá), reissue (đổi), hoàn khi hãng hủy chuyến (involuntary refund – khách được hoàn đầy đủ).
- Khách sạn: hủy miễn phí trước hạn, phạt sau hạn; không hoàn (NRF) → có thể thương lượng thủ công.

### 2.2. Hoàn tiền (Refund)
- Nguồn thời gian: hãng bay có thể mất 30–90 ngày; cổng thanh toán 7–30 ngày với thẻ quốc tế.
- OTA có thể *ứng trước hoàn tiền* cho khách VIP (rủi ro dòng tiền) hoặc hoàn bằng credit tức thì.

### 2.3. Xử lý sự cố (Irregular operations – IRROPS)
- Hãng đổi giờ/hủy chuyến → thông báo khách, đưa lựa chọn (chấp nhận, đổi chuyến, hoàn tiền).
- Khách sạn overbooking/từ chối → tìm phòng tương đương hoặc tốt hơn, OTA chịu phần chênh (chi phí "service recovery").
- Thiên tai, dịch bệnh, bão (rất phổ biến ở miền Trung VN) → chính sách bất khả kháng (force majeure).

### 2.4. Chăm sóc khách hàng (Customer Service)
- Kênh: hotline, chat trong app, email, Zalo/Messenger, tổng đài 24/7 (ngành du lịch phát sinh sự cố ngoài giờ hành chính).
- Đo lường: thời gian phản hồi đầu tiên (FRT), thời gian xử lý (AHT), giải quyết ngay lần đầu (FCR), CSAT, tỷ lệ liên hệ/đơn (contact rate) — chỉ số quan trọng để giảm chi phí.
- Phân cấp: Tier 1 (FAQ, tra cứu) → Tier 2 (đổi/hủy) → Tier 3 (khiếu nại, đền bù) → Escalation với nhà cung cấp.
- Self-service là chìa khóa giảm contact rate.

### 2.5. Quản lý nhà cung cấp (Supply Management)
- **Acquisition**: tìm, đàm phán, ký hợp đồng (BD/Market manager).
- **Onboarding**: nhập nội dung, kết nối channel manager, đào tạo.
- **Account management**: tối ưu giá, khuyến mãi, xử lý vấn đề, đánh giá chất lượng.
- **Quality control**: kiểm tra giá sai, phòng ảo, phàn nàn nhiều → hạ hiển thị hoặc ngừng hợp tác.
- **Rate parity monitoring**: theo dõi giá so với đối thủ.

### 2.6. Thanh toán & Đối soát (Settlement & Reconciliation)
- **Với khách**: thu tiền qua cổng thanh toán, đối soát giao dịch hằng ngày.
- **Với nhà cung cấp**: 
  - Merchant: trả theo kỳ (sau check-out, hằng tuần/tháng) hoặc bằng thẻ ảo.
  - Agency: xuất hóa đơn hoa hồng hằng tháng, thu công nợ.
- **Với hãng bay**: BSP (chu kỳ thanh toán) hoặc trừ ký quỹ (LCC).
- **Đối soát 3 bên**: đơn hàng OTA ↔ dữ liệu nhà cung cấp ↔ dữ liệu ngân hàng/cổng thanh toán. Lệch số liệu là chuyện hằng ngày.
- **Hóa đơn điện tử**: xuất hóa đơn cho khách (theo đúng mô hình doanh thu: toàn bộ giá trị hay chỉ phí dịch vụ/hoa hồng – vấn đề thuế quan trọng cần tư vấn).

### 2.7. Chống gian lận (Fraud & Risk)
- **Rủi ro chính**: thẻ đánh cắp mua vé máy bay (rất phổ biến vì vé có thể bán lại), lạm dụng khuyến mãi (tạo nhiều tài khoản), chargeback "thân thiện" (khách đã dùng nhưng khiếu nại), giả mạo nhà cung cấp, chiếm đoạt tài khoản điểm thưởng.
- **Biện pháp nghiệp vụ**: 3DS, giới hạn theo thiết bị/IP, đối chiếu tên chủ thẻ – tên hành khách, ngày bay sát, tuyến rủi ro, xem xét thủ công trước khi xuất vé, danh sách đen.
- **Đánh đổi**: chặn quá chặt → mất doanh thu (false positive); quá lỏng → mất tiền.

### 2.8. Quản lý giá & doanh thu (Pricing & Revenue)
- Thiết lập markup theo: ngành hàng, nhà cung cấp, thị trường, kênh (app thấp hơn web, thành viên), thời điểm.
- Giá thành viên/giá ẩn (member/closed user group rates) để không vi phạm parity.
- Khuyến mãi: ai chịu chi phí (OTA tài trợ hay nhà cung cấp tài trợ), ngân sách, thời gian, điều kiện.
- Theo dõi giá đối thủ (price benchmarking).

### 2.9. Quản lý nội dung (Content Operations)
- Chuẩn hóa tên, địa chỉ, tọa độ, tiện ích, ảnh (kiểm duyệt chất lượng), dịch đa ngôn ngữ.
- Review: thu thập, kiểm duyệt, chống review giả, cho nhà cung cấp phản hồi.

## 3. Tổ chức đội ngũ điển hình của một OTA

| Khối | Chức năng |
|---|---|
| **Supply / Partner (BD, Market Manager, Content)** | Lấy hàng, quản lý quan hệ nhà cung cấp |
| **Demand / Marketing (Performance, SEO, CRM, Brand, Partnership)** | Kéo khách, giữ chân |
| **Product & Tech (PM, PO, BA, Designer, Dev, QA, Data)** | Xây sản phẩm |
| **Operations & CS** | Xử lý đơn, hỗ trợ khách |
| **Finance & Accounting** | Thanh toán, đối soát, thuế |
| **Risk & Fraud** | Phòng chống gian lận |
| **B2B Sales** | Đại lý, doanh nghiệp, đối tác |
| **Legal & Compliance** | Giấy phép, hợp đồng, dữ liệu |

## 4. Quy trình cần BA mô tả kỹ (danh sách gợi ý cho giai đoạn requirement)

1. Tìm kiếm & hiển thị giá (bao gồm quy tắc làm tròn, tiền tệ, thuế phí).
2. Đặt chỗ & thanh toán (kể cả thanh toán thất bại, timeout, trùng giao dịch).
3. Xác nhận với nhà cung cấp (instant / on-request / thất bại).
4. Hủy – đổi – hoàn tiền cho từng ngành hàng.
5. Áp dụng khuyến mãi/voucher/điểm (cộng dồn? phân bổ chi phí?).
6. Xuất hóa đơn VAT.
7. Thông báo cho khách (kênh, thời điểm, nội dung).
8. Xử lý sự cố nhà cung cấp & đền bù.
9. Thanh toán cho nhà cung cấp & đối soát.
10. Onboarding nhà cung cấp.
11. Chống gian lận & xem xét thủ công.
12. Review & chấm điểm chất lượng.
