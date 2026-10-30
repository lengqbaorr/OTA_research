# 13. "Đi ngay": bộ lập kế hoạch lai (AI + luật) thay cho "Đi y chang"

> **Quyết định mới của đội** (thay thế một phần tài liệu 06, 07, 09):
> 1. **Bỏ bắt buộc xác minh "đã đi thật"** khi đăng bài. Tài liệu 09 chuyển thành tham khảo, không làm trong phạm vi hiện tại.
> 2. Người đăng **dựng lộ trình bằng các điểm** (đi từ đâu đến đâu, ở đâu, chơi đâu, ăn đâu) chứ không lấy từ đơn đặt chỗ.
> 3. Nút **"Đi ngay"** thay cho "Đi y chang". Hệ thống **tự lập kế hoạch đi lại và chỗ ở** theo nơi xuất phát, ngày, số người và **ngân sách của người xem**, rồi cho đặt luôn. Quán ăn, điểm chơi là **gợi ý** kèm cách đi và tầm giá.

---

## 1. Vì sao đổi

| "Đi y chang" (cũ) | "Đi ngay" (mới) |
|---|---|
| Sao chép đúng các mục người đăng đã đặt | Hiểu **điểm đến và các điểm muốn ghé**, rồi **tự lên phương án** hợp với người xem |
| Người xem ở Sài Gòn, người đăng ở Hà Nội thì phải thay chặng | Chặng đi luôn tính từ nơi người xem ở |
| Cần xác minh đơn để có lộ trình | Ai cũng dựng được lộ trình, nên nhiều nội dung hơn ngay từ đầu |
| Ngân sách chỉ dùng để cảnh báo | **Ngân sách là đầu vào chính** để chọn phương tiện, chỗ ở |

## 2. Đăng bài: hai bước

**Bước 1 – Một trang**: chọn video, viết caption, trả lời "có nhận tài trợ không" (có thì gắn nhãn Hợp tác). Không còn bước che mặt người lạ; nội dung vi phạm riêng tư được xử lý qua kiểm duyệt và báo cáo.

**Bước 2 – Dựng lộ trình bằng các ô có cấu trúc**, chọn từ **dữ liệu địa điểm sẵn có** (API bản đồ hoặc dữ liệu đã thu thập), không gõ tự do:

| Loại điểm | Ô nhập | Dữ liệu tự điền khi chọn từ gợi ý | Dùng cho "Đi ngay" |
|---|---|---|---|
| **Di chuyển** | Từ · Đến · Phương tiện (máy bay, tàu, xe khách, limousine, xe máy, ô tô) | Loại điểm (sân bay, ga, bến xe, điểm đến) và **địa chỉ chi tiết**, ví dụ "Bến xe Mỹ Đình · 20 Phạm Hùng, Nam Từ Liêm, Hà Nội" | Biết điểm trung chuyển của điểm đến; người xem vẫn được lên phương án từ nơi của họ |
| **Ở** | Chỗ ở · Số đêm | Loại chỗ, địa chỉ, giá tham khảo | Ưu tiên chỗ này nếu còn phòng và vừa ngân sách |
| **Chơi** | Điểm chơi · Tầm giá · Ghi chú | Loại điểm, địa chỉ, giá vé, khoảng cách tới chỗ ở | Gợi ý kèm "đi gì tới, bao lâu, bao nhiêu" |
| **Ăn** | Quán · Tầm giá · Ghi chú | Loại quán, địa chỉ, tầm giá | Như trên |

- Chọn từ gợi ý thì hiện ngay loại điểm và địa chỉ; gõ tên chưa có trong dữ liệu thì cảnh báo *"Chưa có trong dữ liệu, chọn từ gợi ý để hệ thống lên phương án được"*.
- Điều kiện đăng: ít nhất **một chặng di chuyển** và **một chỗ ở** chọn từ dữ liệu, để hệ thống xác định được điểm đến.
- Nguồn dữ liệu địa điểm (app thật): API bản đồ và tìm địa điểm; danh mục sân bay, ga, bến xe; dữ liệu chỗ ở từ đối tác; điểm chơi và quán ăn tích lũy từ chính các lộ trình người dùng (sau kiểm duyệt).

Người đăng **không cần đã đi**, nhưng nội dung có tài trợ **bắt buộc** gắn nhãn "Hợp tác".

## 3. Kiến trúc "Đi ngay": AI hiểu ý, luật tính toán

```
 Người xem: "từ Sài Gòn, 2 người, 3 củ mỗi người, không đi xe đêm"
        │
        ▼
 ┌─ LỚP AI (agent) ──────────────────────────────────────┐
 │ • Hiểu câu nói → điều kiện có cấu trúc                 │
 │   {origin: SGN, ppl: 2, budget: 3tr/người, noNight}    │
 │ • Hỏi lại 1 câu nếu thiếu thông tin quan trọng         │
 │ • Giải thích phương án bằng lời; nhận yêu cầu sửa      │
 │ • KHÔNG tự bịa giá, giờ, chỗ; KHÔNG tự đặt             │
 └───────────────┬───────────────────────────────────────┘
                 ▼  gọi công cụ
 ┌─ LỚP LUẬT (rule engine) ──────────────────────────────┐
 │ 1. Đọc lộ trình: điểm đến, số đêm, chỗ ở người đăng    │
 │ 2. Tìm đường đi: đồ thị phương tiện (bay, tàu, xe,     │
 │    limousine) + chặng cuối (xe ôm, taxi)               │
 │ 3. Ràng buộc giờ: nối chuyến ≥ 1,5 giờ; đến nơi trước  │
 │    22h ngày 1, nếu không thì khởi hành sớm hơn         │
 │ 4. Chỗ ở: ưu tiên chỗ của người đăng nếu còn phòng     │
 │    và vừa ngân sách; không thì chỗ tương tự gần đó     │
 │ 5. Lọc điều kiện: ngân sách, tránh xe đêm…             │
 │ 6. Chọn 3 phương án: Tiết kiệm / Cân bằng / Nhanh      │
 └───────────────┬───────────────────────────────────────┘
                 ▼
 Giá và chỗ thật từ nguồn hàng (kho riêng, API đối tác) → giỏ hàng → giữ chỗ → thanh toán
```

**Vì sao lai?**
- Chỉ dùng **luật**: chính xác, rẻ, giải thích được, nhưng không hiểu câu nói tự nhiên ("3 củ", "không muốn đi đêm", "đi cho đỡ mệt").
- Chỉ dùng **AI**: hiểu tốt nhưng có thể bịa giá, giờ, chuyến không tồn tại; tốn chi phí; khó kiểm thử.
- **Lai**: AI làm phần hiểu và giải thích; luật làm mọi phép tính và chỉ chọn từ dữ liệu thật. Đáp ứng yêu cầu "không bịa" (A12, mục 6 đề bài) và kiểm thử được.

## 4. Thuật toán lập phương án (lớp luật)

1. **Đầu vào**: nơi đi, ngày bắt đầu, số người, ngân sách mỗi người, ưu tiên (rẻ / cân bằng / nhanh), các điều kiện (tránh xe đêm, tối đa số giờ di chuyển).
2. **Đường đi**: tìm mọi đường từ nơi đi tới **điểm trung chuyển** của điểm đến (Tà Xùa → Bắc Yên; Phú Quốc → sân bay PQC), tối đa 2 chặng đặt được, cộng **chặng cuối** (xe ôm, taxi) là gợi ý có giá ước tính.
3. **Ràng buộc thời gian**: nối chuyến cách nhau ít nhất 1,5 giờ; phải tới chỗ ở trước 23h ngày 1. Chặng dài (tàu 33 giờ) thì **lùi ngày khởi hành** và báo rõ ("khởi hành từ 20/11").
4. **Chiều về**: rời điểm trung chuyển sau 12h ngày cuối (để còn chơi buổi sáng).
5. **Chỗ ở**: kiểm tra phòng trống thật theo ngày nhận phòng; số phòng = số người / 2 làm tròn lên.
6. **Tính tiền**: tiền trong app = vé các chặng × số người + tiền phòng; tiền ngoài app = chặng cuối + ăn, chơi theo tầm giá người đăng ghi.
7. **Chọn phương án**: Tiết kiệm = rẻ nhất; Nhanh = ít giờ di chuyển nhất; Cân bằng = tối thiểu tổng điểm chi phí và thời gian đã chuẩn hóa. Bỏ phương án trùng.
8. **Không có gì vừa ngân sách**: nói thật, đưa phương án gần nhất và cách nới (tăng ngân sách bao nhiêu, đổi ngày, đổi chỗ ở).

## 5. Gợi ý trong chuyến (không đặt trong app)

Với mỗi điểm ăn hoặc chơi trong lộ trình, hệ thống tự thêm:
- **Đi gì tới**: dưới 1 km thì đi bộ; dưới 5 km thì xe ôm hoặc xe máy thuê; xa hơn thì taxi. Kèm thời gian và giá ước tính.
- **Tầm giá**: lấy từ người đăng, ghi rõ "người đăng ghi".
- **Giờ nên đi**: từ ghi chú người đăng (ví dụ "đi 5h sáng để săn mây").

## 6. Chống seeding khi không còn xác minh

Bỏ xác minh làm tăng rủi ro bài quảng cáo trá hình. Các lớp bảo vệ còn lại:
- **Nhãn "Hợp tác" bắt buộc** cho nội dung có tài trợ (nghĩa vụ theo Luật Quảng cáo 2025); khai sai thì phạt.
- **Phản hồi sau chuyến** từ người đã bấm "Đi ngay" và đi thật: *"12 người đã đi theo lộ trình này · 4,6/5 khớp thực tế"*. Đây là tín hiệu tin cậy mạnh nhất, hiện trên bài.
- **Điểm uy tín tài khoản**, chống đẩy ảo xu hướng (một người một phiếu, gom cụm thiết bị).
- **Kiểm duyệt** AI + người như cũ.

## 7. Phạm vi làm trong 4 tuần

| Làm | Làm mỏng | Bỏ |
|---|---|---|
| Lộ trình dạng điểm; "Đi ngay" với đồ thị phương tiện mô phỏng cho vài thành phố; 3 phương án; chọn chỗ ở; giỏ hàng, giữ chỗ, thanh toán | Lớp AI: dùng mô hình ngôn ngữ để hiểu câu nói, có danh sách câu mẫu dự phòng; gợi ý "đi gì tới" theo khoảng cách đường chim bay | Xác minh đã đi (tài liệu 09), thưởng creator, API đối tác thật |
