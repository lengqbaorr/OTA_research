# 09. Xác minh "đã đi thật" trên nền tảng độc lập

> **Bối cảnh (cập nhật)**: đây là **đề tài mở, nền tảng của riêng đội**, không làm cho một tập đoàn khách sạn nào. Đề bài Vinpearl ([02](02-de-bai-vinpearl-cho-nguoi-tre.md)) chỉ dùng làm **tham chiếu yêu cầu**.
> Hệ quả: nền tảng **không sở hữu dữ liệu** của khách sạn, hãng bay, nhà xe. Khách có thể đặt phòng ở Agoda, mua vé máy bay trên web hãng, nhắn Zalo đặt homestay. Câu hỏi: **làm sao biết họ đã đi thật** để cho đăng bài, mà không bắt họ đặt mọi thứ qua app?

---

## 1. Nguyên tắc: tách "đã đặt" và "đã có mặt", rồi cộng bằng chứng

"Đã đi thật" = **bằng chứng có giao dịch thật** + **bằng chứng đã có mặt tại chỗ, đúng thời gian**. Không cần cả hai đến từ app của mình. Mỗi bằng chứng có độ tin cậy khác nhau, hệ thống **cộng dồn** để ra mức xác minh.

```
  Bằng chứng ĐÃ ĐẶT                        Bằng chứng ĐÃ CÓ MẶT
  ─────────────────                        ───────────────────
  • Đặt trong app                           • Quét QR tại cơ sở đối tác
  • Email xác nhận có chữ ký số (DKIM)      • Nhà cung cấp xác nhận check-in/out
  • Thẻ lên máy bay (mã vạch chuẩn IATA)    • Vị trí trong vùng của chỗ, đúng khoảng ngày
  • Nhà cung cấp đối tác xác nhận           • Quay/chụp bằng camera trong app tại chỗ
                    \                      /
                     ▼                    ▼
                  MỨC XÁC MINH CỦA LẦN ĐI (Stay)
```

---

## 2. Các nguồn bằng chứng

### 2.1. Bằng chứng "đã đặt"

| Nguồn | Cách làm | Vì sao tin được | Giới hạn |
|---|---|---|---|
| **E1 – Đặt trong app** | Như luồng đặt chỗ bình thường | Giao dịch của chính nền tảng | Chỉ áp dụng khi khách đặt ở đây |
| **E2 – Email xác nhận có chữ ký DKIM** | Khách **chuyển tiếp** (forward) email xác nhận từ Agoda, Booking, Traveloka, hãng bay… vào một địa chỉ riêng của tài khoản, hoặc tải file email gốc (.eml) | Email từ các nền tảng lớn được **ký số DKIM** bằng khóa của tên miền gửi. Hệ thống kiểm tra chữ ký: email *thật sự* do booking.com gửi và *không bị sửa*. Ảnh chụp màn hình thì giả được, chữ ký DKIM thì không | Phải là email gốc (không phải ảnh chụp); một số nơi gửi không ký; cần trích đúng tên khách, ngày, tên chỗ |
| **E3 – Thẻ lên máy bay** | Quét mã vạch trên thẻ lên máy bay (giấy hoặc trong ví điện thoại) | Mã vạch theo chuẩn **IATA BCBP**, chứa họ tên, mã đặt chỗ, chuyến bay, ngày bay, chặng. Đối chiếu tên với tài khoản | Không tự chứng minh đã lên máy bay; chỉ xác minh *phương tiện*, không xác minh chỗ ở |
| **E4 – Nhà cung cấp đối tác xác nhận** | Khách sạn, homestay, nhà xe **đã ký đối tác** có cổng quản lý: bấm "khách này đã ở" hoặc đồng bộ danh sách khách đã check-out | Nguồn gốc là chính nhà cung cấp | Cần xây mạng lưới đối tác; homestay nhỏ cần giao diện cực đơn giản (xác nhận qua Zalo hoặc link) |
| **E5 – Hóa đơn, chứng từ** | Tải ảnh hóa đơn điện tử, bill thanh toán | Hóa đơn điện tử Việt Nam có mã tra cứu trên cổng thuế (về nguyên tắc tra được tính hợp lệ) | Dễ giả nếu chỉ là ảnh; tốn công duyệt → chỉ dùng hỗ trợ, không cấp dấu một mình |

### 2.2. Bằng chứng "đã có mặt"

| Nguồn | Cách làm | Vì sao tin được | Giới hạn |
|---|---|---|---|
| **P1 – QR tại cơ sở đối tác** | Đối tác đặt **mã QR động** (đổi mỗi 30 giây) trên màn hình quầy, hoặc nhân viên quét QR cá nhân của khách | Phải đứng tại quầy, đúng lúc | Chỉ có ở cơ sở đối tác |
| **P2 – Nhà cung cấp xác nhận check-in/out** | Như E4 | Nguồn gốc | Như E4 |
| **P3 – Vị trí trong vùng, đúng khoảng ngày** | Khi khách mở app trong khoảng ngày ở (có xin phép vị trí), ghi nhận "đã ở trong vùng X km quanh chỗ" ít nhất N lần ở các thời điểm khác nhau | Rất khó trùng hợp nhiều lần đúng nơi đúng ngày | GPS có thể bị giả; cần người dùng bật vị trí; nhạy cảm riêng tư nên chỉ lưu kết quả "có/không", không lưu đường đi |
| **P4 – Quay/chụp trong app tại chỗ** | Camera trong app ghi thời điểm và vị trí lúc quay | Gắn trực tiếp nội dung với nơi và lúc | Vẫn có thể giả vị trí; dùng làm tín hiệu cộng thêm |

---

## 3. Mức xác minh và quyền đăng bài

| Mức | Điều kiện | Dấu trên bài | Quyền |
|---|---|---|---|
| **Đã đi thật** | (E1 hoặc E2 hoặc E4) **và** (P1 hoặc P2 hoặc P3), cùng chỗ, khoảng ngày khớp | **Đã đi thật** | Đăng bài, gắn lộ trình đặt được, tính vào xu hướng với trọng số cao nhất, nhận thưởng (nếu có chương trình) |
| **Đã đặt** | Có E1/E2/E4 nhưng **chưa** có bằng chứng có mặt (ví dụ không bật vị trí) | **Đã đặt chỗ** | Đăng bài, gắn lộ trình; trọng số thấp hơn; nhắc bổ sung bằng chứng có mặt |
| **Có mặt tại chỗ** | Chỉ có P3/P4 (ví dụ ghé quán cà phê, điểm ngắm cảnh, đi cùng người đặt) | **Có mặt tại chỗ** | Đăng bài về *điểm đó*; lộ trình chỉ là gợi ý; không nhận thưởng |
| **Chưa xác minh** | Không có bằng chứng | — | **Không được đăng** bài về chỗ đó (giữ đúng tinh thần "chỉ người đã đi mới đăng") |

**Chặng bay (E3)** không quyết định mức của chỗ ở. Nó chỉ cho chặng bay trong lộ trình dấu **"Đã bay chặng này"**, giúp người xem tin phần "đi gì đến".

---

## 4. Luồng "Thêm chuyến đã đi ở nơi khác"

```
Chuyến đi → "Thêm chuyến đã đi ở nơi khác"
 1. Chọn cách thêm
    ├─ Chuyển tiếp email xác nhận (khuyến nghị)
    │     → kiểm tra chữ ký DKIM → trích tên chỗ, ngày, tên khách → so tên với tài khoản
    ├─ Quét thẻ lên máy bay      → giải mã BCBP → chặng, ngày, tên
    ├─ Quét QR tại cơ sở đối tác → xác minh ngay cả "đã đặt" và "đã có mặt"
    └─ Ghi nhận có mặt tại chỗ   → cần bật vị trí, chỉ cho mức "Có mặt tại chỗ"
 2. Hệ thống ghép các bằng chứng cùng chỗ, cùng khoảng ngày thành một "lần đi" (Stay)
 3. Hiện mức xác minh + gợi ý cách nâng mức
    ("Bật vị trí khi đang ở đó" / "Quét QR ở quầy" để lên "Đã đi thật")
 4. Qua ngày trả phòng → mở nút "Kể lại chuyến đi"; lộ trình lấy từ các Stay này, không gõ lại
```

Khi người khác bấm **"Đi y chang"**:
- Mục đặt được trong app (chỗ thuộc mạng lưới đối tác hoặc bán qua app) → đặt ngay, giá thật.
- Mục **không** bán trong app (người đăng đặt ở OTA khác, chỗ chưa là đối tác) → hiện là **"Đặt ở nơi khác"**: tên chỗ, vị trí, ghi chú của người đăng. Tùy chiến lược có thể kèm **link đối tác affiliate**, hoặc **"Mời chỗ này lên nền tảng"**. Không hiển thị giá nếu không có giá thật.

---

## 5. Chống gian lận

| Kiểu gian lận | Biện pháp |
|---|---|
| Ảnh chụp màn hình email/xác nhận giả | Không chấp nhận ảnh làm bằng chứng "đã đặt"; chỉ chấp nhận **email gốc có DKIM hợp lệ** |
| Chuyển tiếp email của người khác | Tên khách trong email phải khớp tên tài khoản đã xác minh; hoặc người có tên trong email phải **mời** người đi cùng |
| Dùng một email xác nhận cho nhiều tài khoản | Mỗi mã đặt chỗ chỉ gắn với một Stay; trùng thì báo người có tên trong đặt chỗ |
| Đặt rồi hủy, vẫn lấy email xác nhận | Kiểm tra thêm email hủy (nếu có); bắt buộc bằng chứng có mặt để lên "Đã đi thật" |
| Giả vị trí GPS | Vị trí chỉ cho mức yếu; cần nhiều lần ở thời điểm khác nhau; phát hiện dấu hiệu giả lập vị trí; tài khoản chỉ có mức "Có mặt" mà đăng nhiều thì giảm uy tín |
| Chụp lại QR tại quầy | QR động đổi mỗi 30 giây, hoặc nhân viên quét QR của khách |
| Chủ chỗ tạo khách giả | Đối tác xác nhận khách thì vẫn cần bằng chứng từ phía khách (vị trí, thanh toán); tỷ lệ bài/khách bất thường của một chỗ → kiểm tra |

---

## 6. Riêng tư

- Email chuyển tiếp: **chỉ xử lý email xác nhận** (lọc theo người gửi và nội dung); trích các trường cần thiết rồi **xóa nội dung email**. Chỉ lưu kết quả xác minh và mã đặt chỗ đã băm.
- Vị trí: chỉ lưu kết quả "đã ở trong vùng X vào ngày Y", **không lưu đường đi**. Người dùng xem và xóa được.
- Thẻ lên máy bay: chỉ lưu chặng, ngày, kết quả khớp tên; **không lưu mã đặt chỗ thô**.
- Không thông tin nào ở trên hiện cho người khác (giữ nguyên quy tắc lộ trình riêng tư ở tài liệu 06).

---

## 7. Có cần tự xây nền tảng đặt vé máy bay, xe, phòng không?

**Không bắt buộc cho việc xác minh**: email DKIM, thẻ lên máy bay, QR đối tác và vị trí đã đủ để xác minh mà khách không cần đặt qua app.

**Nhưng nên có ở mức nào đó cho "Đi y chang"**, vì người xem cần bấm là đặt được. Lộ trình gợi ý:

| Giai đoạn | Nguồn hàng để "Đi y chang" | Xác minh |
|---|---|---|
| Đầu | Liên kết affiliate tới OTA và hãng bay (ghi nhận nguồn đơn, nhận hoa hồng); một nhóm nhỏ homestay/khách sạn đối tác bán trực tiếp | Email DKIM + thẻ lên máy bay + vị trí; QR tại đối tác |
| Giữa | Bedbank/API khách sạn, đối tác vé xe, vé máy bay qua đại lý | Như trên + đối tác xác nhận check-in |
| Sau | Mạng lưới đối tác lớn có cổng quản lý riêng | Đối tác xác nhận tự động là chính |

(Xem thêm mô hình agency, merchant, affiliate ở [tài liệu 01](01-kien-thuc-nganh-OTA.md), mục 3.)

---

## 8. Mô hình dữ liệu

| Thực thể | Trường chính |
|---|---|
| `Evidence` | `user_id`, `type` (APP_BOOKING / EMAIL_DKIM / BOARDING_PASS / PARTNER_CONFIRM / INVOICE / PARTNER_QR / GEO_PRESENCE / IN_APP_CAPTURE), `place_ref?`, `route?`, `date_from`, `date_to`, `name_match` (bool), `strength`, `hashed_ref`, `created_at` |
| `Stay` | `user_id`, `place_ref`, `date_from`, `date_to`, `evidence_ids[]`, `level` (VERIFIED / BOOKED / PRESENT / NONE), `companions[]` |
| `Post.verification` | `level`, `stay_ids[]` |

Quy tắc: **được đăng về chỗ X khi có `Stay` của X với level ≠ NONE và đã qua `date_to`**. Dấu hiển thị lấy theo `level`.
