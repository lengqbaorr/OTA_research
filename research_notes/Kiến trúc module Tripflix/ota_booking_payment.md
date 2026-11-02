# Kiến trúc Booking và Payment của các OTA: tham chiếu cho module M3 Booking và M4 Payment của Tripflix

> **Phạm vi**: tồn kho và giá phòng (availability), giữ chỗ có thời hạn, kiểm giá lại trước khi trả tiền, thanh toán nhiều mục trong một lần (saga), idempotency, webhook, đối soát, và thiết kế REST, thực thể, máy trạng thái, sự kiện cho Tripflix (modular monolith, PostgreSQL + Redis, cổng thanh toán giả lập, làm trong 4 tuần).
>
> **Hạn chế khi nghiên cứu (đọc trước)**: trong phiên này, WebFetch bị chặn với `stripe.com` và `medium.com` (lỗi DNS do chính sách mạng của phiên). Mọi trích dẫn bên dưới lấy từ **tóm tắt kết quả WebSearch** của trang gốc, không phải từ bản đọc đầy đủ. Khi nguồn là bài viết thứ cấp (blog system design, bản tóm tắt), ghi chú rõ ngay cạnh. Muốn kiểm lại nguyên văn thì cần cho phép `stripe.com` và `medium.com` trong cài đặt mạng của phiên.
>
> **Ràng buộc nội bộ đã chốt** (dùng làm khung khi chuyển thiết kế): giữ chỗ 10 phút; Idempotency-Key cho mỗi lần thanh toán; máy trạng thái đơn "Giữ chỗ → Chờ thanh toán → Đã thanh toán → Đã xác nhận / Thất bại → Hoàn tiền"; chính sách khi một mục lỗi là "giữ phần đặt được" hoặc "tất cả hoặc không"; mã lỗi `PRICE_CHANGED`, `SOLD_OUT`, `HOLD_EXPIRED`, `UNDER_18`; sự kiện `booking.held`, `booking.confirmed`, `booking.failed`, `hold.expired`, `payment.succeeded`, `payment.refunded` — [docs/03-y-tuong-trien-khai.md, mục 7, 9, 11](../../docs/03-y-tuong-trien-khai.md); [team/01-phan-cong-nghien-cuu-module.md](../../team/01-phan-cong-nghien-cuu-module.md)

---

## 1. Tồn kho và availability: mô hình room type × rate plan × ngày, cache hay kiểm thật, chống bán trùng

### Takeaway
Booking.com tách **inventory** (số phòng bán theo room type theo ngày) khỏi **giá** (theo rate plan theo ngày) và quản lý availability chủ yếu ở **mức phòng**, không phải mức rate. Để chống bán trùng, cách được mô tả nhiều nhất là để **database tự bảo vệ** (ràng buộc hoặc khóa dòng trong một transaction), vì code ứng dụng có thể bị vượt qua. Không tìm được bài kỹ thuật gốc của Airbnb về chống bán trùng; các mô tả "kiểu Airbnb" đều là nguồn thứ cấp.

### Cited Findings
**Mô hình ARI (Availability, Rates, Inventory) của Booking.com**
- Rates & Availability API dùng để đặt inventory, giá và các ràng buộc (restrictions) cho từng phòng của khách sạn — [Booking.com Connectivity: Rates & Availability API Overview](https://developers.booking.com/connectivity/docs/ari)
- Định nghĩa: **inventory** là tổng số phòng khách sạn mở bán trên Booking.com (gồm mọi room type, rate plan, ràng buộc, giá); **availability** là phần inventory khách đặt được theo tiêu chí tìm kiếm (ngày, số khách) — [Booking.com ARI overview](https://developers.booking.com/connectivity/docs/ari)
- Phải tạo room type và rate plan trước; inventory được tạo bằng cách ghép một room type với một roomrate — [Booking.com ARI overview](https://developers.booking.com/connectivity/docs/ari)
- Ví dụ B.XML đẩy availability gồm: room ID, khoảng ngày, rate plan ID, `roomstosell` (số phòng còn bán) và giá; Booking.com khuyến nghị đẩy availability **ít nhất một năm** tới — [Booking.com ARI overview](https://developers.booking.com/connectivity/docs/ari); [Tutorial step 6](https://developers.booking.com/connectivity/docs/tut-ready-to-check/step-6)
- Endpoint `xml/roomrateavailability` trả về theo phòng, rate, ngày, kèm **số phòng đã đặt** và cờ **room type bị đóng**; khi room type bị đóng thì không đặt được trong khoảng đó **dù vẫn còn tổ hợp room-rate mở** — [Booking.com: Retrieve inventory and rate details](https://developers.booking.com/connectivity/docs/b_xml-roomrateavailability)
- Booking.com nhìn chung quản lý availability **ở mức phòng**; nếu hỏi availability ở mức rate cho khách sạn dùng inventory mức phòng thì kết quả "nhiều khả năng không chính xác" — [Booking.com: Retrieve inventory and rate details](https://developers.booking.com/connectivity/docs/b_xml-roomrateavailability)
- Có bốn kiểu tính giá: Standard (mặc định), Derived/Rate-Level Occupancy (RLO), Occupancy-Based Pricing (OBP), Length-of-Stay (LOS); ba kiểu sau cần chứng nhận, và đa số đối tác không hỗ trợ LOS — [Booking.com: Understanding pricing types](https://developers.booking.com/connectivity/docs/understanding-pricing-types)

**Kiểm giá và availability thật trước khi đặt (không tin giá trong kết quả tìm kiếm)**
- Expedia Rapid Lodging: bước shopping trả về các link đã mã hóa (tokenized link) cho bước tiếp theo; link **price check** dùng để xác minh lại rate đã chọn trước khi book — [Expedia Rapid Lodging API overview](https://developers.expediagroup.com/rapid/lodging)
- Amadeus: Flight Offers Price **xác nhận lại giá (gồm thuế, phí) và chỗ trống** của một offer lấy từ Flight Offers Search trước khi đặt — [Ballerina connector Amadeus Flight Create Orders](https://central.ballerina.io/ballerinax/amadeus.flightcreateorders/latest); [jentic: Amadeus Flight Create Orders](https://jentic.com/apis/amadeus.com/amadeus-flight-create-orders) (nguồn thứ cấp mô tả API Amadeus)

**Chống bán trùng (double booking)** — *toàn bộ là nguồn thứ cấp, không có bài gốc của Airbnb hay Booking.com*
- Đề xuất dùng **exclusion constraint** của PostgreSQL trên (listing_id, khoảng ngày) để từ chối các đơn chồng lấn ngay cả khi insert đồng thời; lập luận: ràng buộc ở tầng database được ưu tiên vì code ứng dụng có thể bị vượt qua còn ràng buộc DB thì không. Nguồn tự nhận đây là cách "hệ thống quy mô Airbnb" dùng nhưng **không đưa bằng chứng** — [techinterview.org: System Design Airbnb](https://www.techinterview.org/post/3233474326/system-design-design-airbnb-accommodation-booking-search-availability-calendar-pricing-review-system-geospatial/)
- Cách khác: trong một transaction, `SELECT` các ngày cần đặt **kèm khóa dòng** (row lock), cập nhật thành đã đặt, insert booking rồi commit; nhấn mạnh bước kiểm còn chỗ và bước cập nhật lịch phải **nguyên tử** — [techinterview.org: Design a Hotel / Airbnb Reservation System](https://www.techinterview.org/post/3233460029/design-a-hotel-airbnb-reservation-system/)
- Thiết kế lịch phổ biến: **một dòng cho mỗi listing mỗi ngày**, phân vùng theo listing ID; một nguồn ước tính khoảng 2,5 tỷ dòng cho 7 triệu listing — [techinterview.org: System Design Airbnb](https://www.techinterview.org/post/3233474326/system-design-design-airbnb-accommodation-booking-search-availability-calendar-pricing-review-system-geospatial/) (ước tính của nguồn thứ cấp)
- Với chủ nhà đăng trên nhiều nền tảng, độ trễ đồng bộ lịch iCal là nguyên nhân phổ biến gây bán trùng — [strspecialist.com](https://strspecialist.com/never-double-book-again-airbnb-direct-calendar-sync-that-actually-works-with-lodgify-) (blog của bên bán dịch vụ)

### Inferences
**Mô hình dữ liệu tồn kho cho Tripflix (theo cách Booking.com tách inventory khỏi giá)**
- `room_type_inventory(room_type_id, stay_date, total, held, sold, PRIMARY KEY(room_type_id, stay_date), CHECK (held + sold <= total))`: inventory ở **mức room type theo ngày** (giống Booking.com quản lý availability ở mức phòng). Rate plan (không hoàn hủy, kèm bữa sáng, combo) **chỉ mang giá và điều kiện**, dùng chung một bể phòng.
- `rate_plan_price(rate_plan_id, stay_date, price, currency, closed BOOLEAN)` và `rate_plan(id, room_type_id, name, refundable, cancel_deadline_hours, includes_breakfast)`.
- `ticket_inventory(product_id, visit_date, capacity, held, sold, CHECK (held + sold <= capacity))` cho vé VinWonders; `flight_inventory(flight_id, fare_class, seats_total, held, sold)` cho chuyến bay giả lập. Combo là **gói trỏ tới các mục con** chứ không có kho riêng, để không lệch số với kho thành phần.
- Một phòng nhiều đêm = giữ **mỗi đêm một dòng**. Đó là lý do chọn "một dòng mỗi ngày" thay vì exclusion constraint theo khoảng ngày: exclusion constraint hợp với listing **chỉ có 1 đơn vị** (kiểu Airbnb), còn khách sạn có `total = N` phòng cùng loại.

**Chống bán trùng trong PostgreSQL (đơn giản, đủ cho bài kiểm "50 yêu cầu tranh chỗ cuối")**
- Dùng **conditional update** thay vì đọc rồi ghi:
  `UPDATE room_type_inventory SET held = held + :q WHERE room_type_id = :rt AND stay_date >= :in AND stay_date < :out AND total - held - sold >= :q;` rồi so `rowcount` với số đêm; thiếu thì rollback cả transaction và trả `SOLD_OUT`. Câu `UPDATE` tự lấy row lock nên 50 yêu cầu đồng thời được xếp hàng, chỉ những yêu cầu còn đủ chỗ mới thành công.
- `CHECK (held + sold <= total)` là **lưới an toàn cuối**: nếu code có lỗi thì DB vẫn từ chối (đúng tinh thần "để database tự bảo vệ" ở nguồn trên).
- Khi một đơn giữ nhiều mục (phòng + vé + chuyến bay), **luôn khóa theo thứ tự cố định** (ví dụ sắp theo `inventory_kind, id, date`) để tránh deadlock giữa hai đơn cùng tranh hai mục.
- Optimistic concurrency (`version` column) dùng cho **bảng đơn** (chuyển trạng thái), không cần cho bảng tồn kho vì conditional update đã đủ.

**Cache hay kiểm thật**
- Trang tìm kiếm và trang chi tiết đọc availability/giá từ **Redis cache** (TTL ngắn, ví dụ 30–60 giây, xóa cache khi tồn kho đổi). Giá trên trang tìm kiếm chỉ để tham khảo, đúng như Expedia và Amadeus đều có bước kiểm giá riêng.
- **Quote/price check và giữ chỗ luôn đọc PostgreSQL**, không bao giờ đọc cache. Redis không phải nguồn sự thật cho tồn kho trong demo; không cần distributed lock vì row lock của Postgres đã đủ trong monolith.

### Gaps
- Không tìm được bài kỹ thuật gốc của **Airbnb** hoặc **Booking.com** mô tả cơ chế chống bán trùng thật (khóa, ràng buộc, hay hàng đợi). Các mô tả "kiểu Airbnb" đều đến từ blog phỏng vấn system design.
- Không tìm được tài liệu về cách **Agoda** hoặc **Trip.com** mô hình tồn kho và cache availability.
- Không xác minh được Booking.com có cache availability ở tầng tìm kiếm thế nào (thời gian sống, cách làm mới).

---

## 2. Giữ chỗ có thời hạn (hold TTL), chốt giá (quote/price lock), phát hiện đổi giá khi thanh toán

### Takeaway
Expedia Rapid có hẳn mô hình **book on hold → resume**, và xử lý đổi giá bằng **lỗi 409 price mismatch → chạy lại price check để lấy token mới**. Amadeus bắt buộc đặt chỗ bằng **đúng payload trả về từ bước Price**, không phải từ bước Search. Thời gian giữ 10–15 phút chỉ thấy ở nguồn thứ cấp; Tripflix đã chốt 10 phút.

### Cited Findings
- Expedia Rapid **Hold and Resume** hỗ trợ đặt hai bước bằng **cờ trong request** và **link mã hóa trong response**, không thêm API mới. Response book on-hold thành công trả thêm một link để resume, xác nhận và thanh toán đơn đang giữ — [Expedia Rapid: Hold and Resume](https://developers.expediagroup.com/rapid/lodging/booking/hold-resume)
- Resume dùng link resume trong response ban đầu, **không cần body**; resume thành công trả **HTTP 204**; lấy lại toàn bộ itinerary bằng link GET trong response ban đầu, giống cách lấy đơn thường — [Expedia Rapid: Hold and Resume](https://developers.expediagroup.com/rapid/lodging/booking/hold-resume); [Java SDK: Hold and Resume](https://developers.expediagroup.com/docs/products/rapid/sdk/java/usage-examples/hold-resume-booking)
- Hủy một đơn đang giữ thì **trả phòng về kho và rollback itinerary**; sau khi đã resume thì không được hủy cả itinerary nữa mà phải hủy từng phòng — [Expedia Rapid Lodging (kết quả tìm kiếm developers.expediagroup.com)](https://developers.expediagroup.com/rapid/lodging)
- Lỗi **409 price mismatch** được xử lý bằng cách **chạy lại price check để lấy booking token mới** — [Expedia Rapid: Common error responses](https://developers.expediagroup.com/rapid/lodging/reference/error-responses)
- Rapid Activities: bước price check trước khi đặt **xác nhận giá cuối cùng** và trả về **booking token cùng danh sách trường bắt buộc khi đặt**; bước tạo booking nhận booking token (query parameter) và **payment token** từ Payments API (body) — [Expedia Rapid Activities API overview](https://developers.expediagroup.com/rapid/activities)
- Amadeus: endpoint `/booking/flight-orders` cần **đúng offer trả về từ Flight Offers Price**; dùng thẳng response của Search thường gây lỗi giá hoặc chỗ — [jentic: Amadeus Flight Create Orders](https://jentic.com/apis/amadeus.com/amadeus-flight-create-orders) (nguồn thứ cấp)
- Nguồn system design khuyến nghị **giữ tạm thời ~10–15 phút** khi thanh toán, nhả ra nếu thanh toán lỗi hoặc hết giờ — [techinterview.org: System Design Airbnb](https://www.techinterview.org/post/3233474326/system-design-design-airbnb-accommodation-booking-search-availability-calendar-pricing-review-system-geospatial/) (thứ cấp)
- Tripflix đã chốt **giữ chỗ 10 phút, giờ hết hạn hiển thị là thật** — [docs/03, mục 7](../../docs/03-y-tuong-trien-khai.md)

### Inferences
**Thực thể**
- `quote(id, user_id, source_post_id NULL, items JSONB, total_amount, currency, price_fingerprint, expires_at, created_at)`: ảnh chụp giá đã kiểm từ DB. `price_fingerprint` = hash của (mã mục, ngày, số lượng, đơn giá) để so nhanh khi tạo đơn. Quote sống ngắn (ví dụ 10 phút) và **chưa giữ kho**: giống price check của Expedia/Amadeus.
- `inventory_hold(id, order_item_id, inventory_kind, inventory_ref, date, qty, status ACTIVE|CONSUMED|RELEASED|EXPIRED, expires_at)`: mỗi dòng ứng với phần `held` đã cộng vào bảng tồn kho, để nhả đúng số khi hết hạn.
- `booking_order.hold_expires_at` = thời điểm hết hạn chung của cả lộ trình (mọi mục cùng một đồng hồ, khớp quy tắc "giữ chỗ tất cả 10 phút").

**Luồng chốt giá và phát hiện đổi giá**
1. `POST /quotes`: đọc giá và chỗ trống từ Postgres, trả `quote_id`, tổng tiền, `expires_at`, và danh sách mục **đã đổi giá so với giá người dùng thấy** (client gửi kèm giá đã thấy, hoặc giá trong lộ trình của bài đăng). Đây là nơi hiện "giá đã thay đổi so với bài đăng".
2. `POST /orders {quote_id}`: trong **một transaction**: tính lại giá từ `rate_plan_price`; nếu khác `price_fingerprint` thì trả `409 PRICE_CHANGED` kèm quote mới (giống 409 price mismatch của Expedia → chạy lại price check). Nếu thiếu chỗ thì `409 SOLD_OUT` kèm danh sách mục hết. Nếu đủ thì cộng `held`, tạo `inventory_hold`, đơn ở trạng thái `HELD`, phát `booking.held`.
3. Giá được **khóa trên đơn** (lưu `unit_price` vào từng `booking_item`). Từ đây, đổi giá trong bảng giá không ảnh hưởng đơn đã giữ, giống token của Expedia/Amadeus gắn với một giá cụ thể.

**Hết hạn giữ chỗ (không cần Redis keyspace notification)**
- Job chạy mỗi 15–30 giây: `SELECT id FROM booking_order WHERE status IN ('HELD','PENDING_PAYMENT') AND hold_expires_at < now() FOR UPDATE SKIP LOCKED LIMIT 100`, trừ `held`, đặt hold `EXPIRED`, đơn `EXPIRED`, phát `hold.expired`. `SKIP LOCKED` giúp chạy nhiều instance không đụng nhau.
- **Kiểm lười (lazy check)**: khi xác nhận thanh toán, nếu `now() > hold_expires_at` thì không xác nhận, trả `HOLD_EXPIRED`. Nếu tiền đã bị trừ thì chuyển sang hoàn tiền (xem mục 4).
- Nên cho **ân hạn ngắn** (ví dụ 60–120 giây) cho đơn đang ở `PENDING_PAYMENT` có payment `PROCESSING`/`REQUIRES_ACTION`, để người đang nhập OTP ở giây thứ 9:55 không bị mất chỗ. Đồng hồ hiển thị vẫn là 10 phút.

### Gaps
- Không tìm được tài liệu công khai cho biết thời gian giữ thật của Booking.com, Agoda, Traveloka hay Trip.com, cũng như thời hạn của Expedia Rapid hold.
- Không đọc được nguyên văn mục "price check" của Rapid Lodging (chỉ có tóm tắt kết quả tìm kiếm); trường giá trả về và thời hạn token chưa xác minh.

---

## 3. Thanh toán nhiều mục / dynamic packaging (bay + khách sạn + vé): saga, orchestration hay choreography, bù trừ khi một mục lỗi

### Takeaway
Saga = chuỗi transaction cục bộ; nếu một bước vi phạm quy tắc nghiệp vụ thì chạy **compensating transaction** để hoàn tác các bước trước. Với luồng "đặt cả lộ trình" có điều kiện và chính sách lỗi do người dùng chọn, **orchestration** (một bộ điều phối giữ trạng thái) hợp hơn choreography. Expedia có dấu hiệu dùng saga trong tầng điều phối đơn (qua tin tuyển dụng), nhưng không tìm được bài kỹ thuật mô tả.

### Cited Findings
- Saga là chuỗi transaction cục bộ, mỗi bước phát message/sự kiện để kích bước tiếp theo; nếu một transaction cục bộ thất bại vì vi phạm quy tắc nghiệp vụ, saga chạy chuỗi **compensating transaction** để hoàn tác các thay đổi trước đó. Hai kiểu điều phối: **choreography** (các bên phản ứng với sự kiện của nhau) và **orchestration** (một bộ điều phối trung tâm chỉ định bước nào chạy) — [microservices.io: Pattern: Saga](https://microservices.io/patterns/data/saga)
- Choreography không tạo **điểm lỗi đơn lẻ** vì trách nhiệm phân tán, nhưng **khó kiểm thử tích hợp** vì cần chạy mọi service để mô phỏng một giao dịch; orchestrator **lưu và diễn giải trạng thái từng bước** và xử lý phục hồi bằng compensating transaction; **compensating transaction có thể không thành công**, có thể để hệ thống ở trạng thái không nhất quán — [Microsoft Azure Architecture Center: Saga pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/saga)
- Luồng đơn giản, tuyến tính hợp choreography; luồng phức tạp có rẽ nhánh, vòng lặp, xử lý lỗi tinh vi hợp orchestration — [SoftwareSeni: Saga Pattern](https://www.softwareseni.com/saga-pattern-managing-distributed-transactions-in-microservices) (thứ cấp)
- Hướng dẫn Temporal lấy ví dụ đặt chuyến đi: khách "muốn đặt được tất cả hoặc không gì cả"; workflow dùng một đối tượng Saga **đăng ký bước bù trừ** sau mỗi bước thành công — [Temporal docs: Trip booking saga (PHP)](https://docs.temporal.io/php/booking-saga-tutorial)
- Ví dụ mã nguồn mở "flowing trip booking saga" (Camunda) mô hình hóa đặt xe + khách sạn + vé máy bay với bù trừ — [GitHub: berndruecker/flowing-trip-booking-saga](https://github.com/berndruecker/flowing-trip-booking-saga)
- Một tin tuyển dụng của Expedia Group mô tả vai trò dẫn dắt thiết kế **tầng điều phối đơn hàng dựa trên workflow** dùng workflow engine và các mẫu như long-running transaction, **saga**, event sourcing — [PowerToFly: Expedia Group job](https://powertofly.com/jobs/detail/2509486); [Bayt: Expedia director job](https://www.bayt.com/en/india/jobs/director-software-development-engineering-74253904/) (kết quả tìm kiếm không nói rõ tin nào chứa đoạn này; chỉ là tin tuyển dụng, không phải mô tả kiến trúc)
- Expedia Rapid cho phép **multi room type booking** trong một itinerary, và hủy itinerary đang giữ thì rollback cả itinerary — [Expedia Rapid: Multi room type booking](https://developers.expediagroup.com/rapid/lodging/booking/multi-room-type); [Expedia Rapid: Hold and Resume](https://developers.expediagroup.com/rapid/lodging/booking/hold-resume)
- Bài blog chung về hệ thống đặt du lịch mô tả tình huống **thanh toán thành công nhưng nhà cung cấp không xác nhận** → kích hoạt hoàn tiền hoặc quy trình phục hồi, và khuyên tách xử lý thanh toán khỏi logic đặt chỗ — [DEV: real-time travel booking system using microservices](https://dev.to/lacey_glenn_e95da24922778/how-we-designed-a-real-time-travel-booking-system-using-microservices-2bp6) (thứ cấp, không phải của OTA)
- Chính sách đã chốt của Tripflix: giữ chỗ tất cả trước khi trả; nếu một mục lỗi sau khi trả thì theo lựa chọn trước của người dùng: **giữ phần đặt được** (hoàn mục lỗi, cho hủy miễn phí phần còn lại trong 30 phút) hoặc **tất cả hoặc không** — [docs/03, mục 7](../../docs/03-y-tuong-trien-khai.md)

### Inferences
**Thứ tự bước: đặt bước dễ hoàn tác lên trước, bước không đảo ngược được để cuối**
1. *Giữ chỗ tất cả mục* (hoàn tác được, rẻ: trừ `held`) → 2. *Thu tiền* (hoàn tác bằng refund, tốn kém hơn) → 3. *Xác nhận từng mục* (chuyển `held` → `sold`; trong demo không gọi nhà cung cấp thật nên gần như luôn thành công, nhưng vẫn phải giả lập được lỗi để mentor thử) → 4. *Phát hành vé/voucher* (chỉ cần thử lại, không bù trừ).
- Vì bước 1 giữ cả lộ trình trong **một transaction Postgres** (các bảng tồn kho cùng một DB trong monolith), "SOLD_OUT ở một mục" được xử lý bằng **rollback thường**, không cần saga. Saga chỉ thật sự cần **sau khi đã thu tiền**, khi bước 3 có thể lỗi từng phần.

**Orchestration trong modular monolith (bản đơn giản hóa)**
- Orchestrator = một service `CheckoutOrchestrator` trong module Booking; **trạng thái saga chính là `booking_order.status` + `booking_item.status`** lưu trong Postgres (không cần workflow engine như Temporal/Camunda).
- Giao tiếp giữa module bằng **transactional outbox**: ghi bảng `outbox_event` trong cùng transaction với thay đổi trạng thái; một worker đọc outbox (`FOR UPDATE SKIP LOCKED`) và gọi handler trong process (hoặc đẩy Redis Stream nếu muốn). Như vậy Payment phát `payment.succeeded` và Booking nhận được ngay cả khi process chết giữa chừng.
- Handler phải **idempotent** (bảng `processed_event(consumer, event_id)` có khóa chính), vì outbox có thể giao lại một sự kiện.

**Bù trừ theo chính sách người dùng chọn (`failure_policy` lưu trên đơn khi tạo)**
- `ALL_OR_NOTHING`: một mục `FAILED` sau khi đã thu tiền → hủy các mục đã `CONFIRMED` (trả `sold` về kho) → refund **toàn bộ** → đơn `FAILED` + `REFUNDED`.
- `KEEP_SUCCEEDED`: mục lỗi → refund **riêng số tiền mục đó** → đơn `PARTIALLY_CONFIRMED`, đặt `free_cancel_until = now() + 30 phút` cho các mục còn lại (theo docs/03).
- Bù trừ có thể lỗi (theo cảnh báo của Microsoft): refund giả lập lỗi → để `refund.status = FAILED`, job thử lại có giới hạn, quá số lần thì đưa vào danh sách **bất thường cần quản trị viên xử lý** (mục 4).

### Gaps
- Không tìm được bài kỹ thuật của **Expedia, Trip.com, Uber hay Airbnb** mô tả cụ thể saga cho đặt nhiều mục (dynamic packaging); thông tin về Expedia chỉ là tin tuyển dụng. Uber Cadence không xuất hiện trong kết quả.
- Phân loại bước saga thành compensatable / pivot / retriable (thường gắn với Chris Richardson) chưa được xác minh nguồn trong phiên này; phần "thứ tự bước" ở trên là suy luận thiết kế.

---

## 4. Idempotency thanh toán, máy trạng thái thanh toán, webhook chống trùng, đối soát và phát hiện bất thường

### Takeaway
Stripe: client gửi `Idempotency-Key` (≤ 255 ký tự); server lưu **response đầu tiên** và trả lại cho mọi lần thử lại cùng key; dùng lại key với **tham số khác** thì báo `idempotency_error`; key có thể bị xóa sau 24 giờ (API v1). Airbnb Orpheus thêm: đọc/ghi bảng idempotency **trên master DB** (không replica), khóa key bằng **lease có hạn**, phân loại lỗi **retryable / non-retryable**. Webhook phải xác thực chữ ký trên **raw body**, chống trùng theo **event ID** lưu bền, và **không dựa vào thứ tự** giao.

### Cited Findings
**Stripe Idempotency-Key**
- Key dài tối đa **255 ký tự**; với API v1, key có thể được xóa tự động khi **ít nhất 24 giờ tuổi**, dùng lại key sau khi bị xóa thì được coi là request mới; v2 dùng cửa sổ **30 ngày** — [Stripe API: Idempotent requests](https://docs.stripe.com/api/idempotent_requests)
- Tầng idempotency **so tham số request mới với request gốc** và báo lỗi nếu khác; loại lỗi `idempotency_error` dùng khi key được dùng lại cho request khác endpoint hoặc tham số — [Stripe API: Idempotent requests](https://docs.stripe.com/api/idempotent_requests); [Stripe API: Errors](https://docs.stripe.com/api/errors)
- Hướng dẫn thử lại an toàn: thử lại **cùng key, cùng tham số** trong 24 giờ đầu — [Stripe: Advanced error handling](https://docs.stripe.com/error-low-level)
- Server lưu mã trạng thái và body của response đầu tiên cho một key và trả lại cho các lần thử sau — [Stripe API: Idempotent requests](https://docs.stripe.com/api/idempotent_requests) (theo tóm tắt; một nguồn thứ cấp nói cùng ý: [dev.to: Idempotency keys in Stripe](https://dev.to/axrisi/idempotency-keys-in-stripe-safe-retries-after-lost-responses-5659))
- Client nên thử lại với **exponential backoff có giới hạn và jitter** để không dồn tải vào service đang quá tải — [dev.to: How to design idempotency keys for retry-prone web actions](https://dev.to/137foundry/how-to-design-idempotency-keys-for-retry-prone-web-actions-without-doubling-user-requests-1fon) (thứ cấp; bài gốc "Designing robust and predictable APIs with idempotency" của Stripe không đọc được trong phiên này)

**Airbnb Orpheus** (bài gốc: [Airbnb Engineering: Avoiding double payments in a distributed payments system](https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb), chỉ đọc qua tóm tắt tìm kiếm)
- Orpheus là framework idempotency dùng chung; client truyền vào một idempotency key đại diện cho **một request idempotent**; bảng idempotency được đọc/ghi từ **master database đã shard** — [Airbnb Engineering](https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb); thêm nhấn mạnh "không bao giờ đọc replica" trong [Biweekly Engineering ep. 32](https://biweekly-engineering.beehiiv.com/p/handling-double-payments-airbnb-biweekly-engineering-episode-32)
- Response lỗi được phân loại **retryable** hoặc **non-retryable** — [Airbnb Engineering](https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb)
- Mỗi request **khóa idempotency key**, được cấp một **lease** (quyền đi tiếp); lease có hạn, nên một request không có response chỉ được thử lại **sau khi lease hiện tại hết hạn** — [Airbnb Engineering](https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb)
- Airbnb ghi rằng bản hiện tại vẫn đang cải tiến, trong đó có việc **hạn chế truy cập database trong pha RPC** — [Airbnb Engineering](https://medium.com/airbnb-engineering/avoiding-double-payments-in-a-distributed-payments-system-2981f6b070bb)
- Cấu trúc ba pha **pre-RPC / RPC / post-RPC**: pre-RPC ghi trạng thái request (bỏ qua nếu là lần thử lại), RPC gọi dịch vụ ngoài (không đụng DB), post-RPC ghi response và nhả lease — [GitHub gist: orpheus_example_implementation.java](https://gist.github.com/jonjchew/da2b31e96922f52eed6026aa948c22ce) (**mã minh họa của cộng đồng, không phải mã Airbnb**); bài review: [Book Club: Avoiding Double Payments](https://medium.com/@supermanue/book-club-avoiding-double-payments-in-a-distributed-payments-system-f0f5b0055482)
- Cách làm chung: lưu request id cùng response vào database và trả lại response đó khi request lặp lại — [Byte-Sized Design: Why Airbnb won't charge you twice](https://bytesizeddesign.substack.com/p/why-airbnb-wont-charge-you-twice) (thứ cấp, có paywall)

**Máy trạng thái PaymentIntent của Stripe**
- `requires_payment_method` (chờ phương thức thanh toán; lần thử bị từ chối thì **quay lại đây** để khách thử lại) → `requires_confirmation` (đa số tích hợp bỏ qua) → `requires_action` (cần bước thêm, thường là 3-D Secure; bước tiếp theo nằm trong `next_action`) → `processing` (phương thức bất đồng bộ, có thể vài ngày) → `succeeded` ("có thể yên tâm giao hàng"); thêm `requires_capture` (tách ủy quyền và thu tiền) và `canceled` (không đảo ngược, nhả tiền đang giữ) — [Stripe: How PaymentIntents work](https://docs.stripe.com/payments/paymentintents/lifecycle); [Stripe API: PaymentIntent object](https://docs.stripe.com/api/payment_intents/object)
- Stripe khuyên dùng **webhook** thay vì kiểm phía client để biết kết quả: `payment_intent.succeeded`, `payment_intent.payment_failed`; `payment_intent.processing` chỉ có với phương thức thông báo trễ — [Stripe: Payment status updates](https://docs.stripe.com/payments/payment-intents/verifying-status)

**Webhook**
- Endpoint có thể **nhận cùng một sự kiện nhiều lần**; chống trùng bằng cách ghi lại event ID đã xử lý và bỏ qua sự kiện đã ghi; đôi khi có **hai Event object khác nhau** cho cùng một việc, khi đó nhận diện bằng ID của object trong `data.object` cùng `event.type` — [Stripe: Receive events in your webhook endpoint](https://docs.stripe.com/webhooks)
- **Không** dùng `created` để sắp thứ tự hay để quyết định đã xử lý chưa (độ phân giải giây, nhiều sự kiện trùng thời điểm); **không phụ thuộc thứ tự giao** — [Stripe Webhooks](https://docs.stripe.com/webhooks)
- Mỗi webhook có chữ ký trong header `Stripe-Signature`; xác thực cần **raw body** (framework parse JSON trước sẽ làm hỏng); timestamp đã ký giúp chặn replay, thư viện mặc định chấp nhận lệch **5 phút**, không nên đặt 0; mỗi lần gửi lại có chữ ký và timestamp mới — [Stripe Webhooks](https://docs.stripe.com/webhooks)
- Mẫu chống trùng bền: bảng idempotency có **khóa chính trên key**, **insert trước khi xử lý**, lỗi unique constraint nghĩa là sự kiện trùng; ví dụ lưu event ID trong bộ nhớ chỉ dùng để thử — [Stripe: Migrate from snapshot events to thin events](https://docs.stripe.com/webhooks/migrate-snapshot-to-thin-events)
- Stripe gửi webhook từ một danh sách IP cố định, có thể giới hạn tường lửa theo danh sách đó — [Stripe Webhooks](https://docs.stripe.com/webhooks)

**Traveloka (liên quan trạng thái sau thanh toán)**
- Trong kiến trúc event-driven của Traveloka, **sau khi thanh toán được xác nhận, service cập nhật `booking_status` thành `PAYMENT_VERIFIED`**; một scheduler quét database, và product aggregator đánh dấu booking đã **issued** để không bị quét lại — [Traveloka Engineering: How Event-Driven Architecture and Microservices Help Us to Scale](https://medium.com/traveloka-engineering/how-event-driven-architecture-and-microservices-help-us-to-scale-to-the-next-level-b5025f50ec7b) (chỉ qua tóm tắt tìm kiếm)

### Inferences
**Bảng idempotency dùng chung (gộp Stripe + Orpheus, chạy trên Postgres)**
```
idempotency_key(
  user_id, key VARCHAR(255), endpoint,          -- PRIMARY KEY(user_id, key)
  request_hash,                                  -- sha256 body → khác thì 422 IDEMPOTENCY_MISMATCH (giống idempotency_error)
  status IN_PROGRESS|COMPLETED,
  locked_until TIMESTAMPTZ,                      -- lease kiểu Orpheus, ví dụ 30s
  response_code, response_body JSONB,
  created_at                                     -- job xóa sau 24h như Stripe v1
)
```
- Luồng: `INSERT ... ON CONFLICT DO NOTHING` (luôn trên primary). Nếu đã `COMPLETED` → trả lại response đã lưu. Nếu `IN_PROGRESS` và lease còn hạn → `409 REQUEST_IN_PROGRESS` (client đợi rồi thử lại). Nếu lease hết hạn → được tiếp quản.
- Ba pha kiểu Orpheus: (pre) mở transaction, đổi trạng thái đơn/payment, commit; (RPC) gọi mock gateway **ngoài transaction**; (post) ghi kết quả + response vào `idempotency_key`, nhả lease. Lỗi mạng/5xx/timeout = retryable (không lưu response, nhả lease); lỗi nghiệp vụ (thẻ bị từ chối, `SOLD_OUT`) = non-retryable (**lưu response** để lần thử sau nhận đúng lỗi đó).
- Khóa chống trùng **ở tầng nghiệp vụ** làm lớp thứ hai: `UNIQUE (order_id) WHERE status = 'SUCCEEDED'` trên `payment_intent` (partial unique index) nên **một đơn không bao giờ có hai payment thành công**, kể cả khi hai thiết bị dùng hai key khác nhau. Đây là chỗ đáp ứng bài kiểm "hai thiết bị cùng trả" trong docs/03.
- Client (app/web) sinh key **một lần cho mỗi ý định trả tiền** (UUID v4, lưu ở state màn hình), dùng lại khi bấm hai lần hoặc mất mạng; chỉ đổi key khi người dùng đổi phương thức hoặc đơn đổi số tiền.

**Máy trạng thái `payment_intent` của Tripflix (rút gọn từ Stripe)**
```
REQUIRES_PAYMENT_METHOD --confirm--> REQUIRES_ACTION (OTP/3DS/QR giả lập)
REQUIRES_PAYMENT_METHOD --confirm (ví giả lập không cần OTP)--> PROCESSING
REQUIRES_ACTION --otp đúng--> PROCESSING
REQUIRES_ACTION --otp sai/hết giờ--> REQUIRES_PAYMENT_METHOD (cho thử lại, như Stripe)
PROCESSING --webhook payment.succeeded--> SUCCEEDED
PROCESSING --webhook payment.failed--> REQUIRES_PAYMENT_METHOD
(bất kỳ trạng thái chưa kết thúc) --đơn hết hạn/hủy--> CANCELED
```
- Bỏ `requires_confirmation` và `requires_capture` (đa số tích hợp bỏ qua; demo không cần tách ủy quyền và thu tiền).
- **Chỉ webhook** (hoặc job đối soát) được chuyển sang `SUCCEEDED`; response của `confirm` trả về cho client chỉ để hiển thị. Đây là bài học "dùng webhook thay vì kiểm phía client" của Stripe, và là thứ tạo ra tình huống thật "đã trả tiền nhưng chưa xác nhận" để demo.
- `refund(id, payment_intent_id, order_item_id NULL, amount, status PENDING|SUCCEEDED|FAILED, reason, idempotency_key)`; tổng refund ≤ số đã thu (CHECK hoặc kiểm trong transaction).

**Webhook của mock gateway**
- `POST /api/v1/webhooks/mock-gateway`: header `X-Mock-Signature: t=<ts>,v1=<HMAC-SHA256(secret, ts + "." + rawBody)>`; xác thực trên raw body, từ chối nếu lệch > 5 phút (như Stripe).
- `webhook_inbox(provider_event_id PRIMARY KEY, type, object_id, payload JSONB, received_at, processed_at)`: insert trước, trùng khóa thì trả 200 ngay. Xử lý trong cùng transaction với chuyển trạng thái payment. Trả 2xx nhanh; lỗi thì trả 5xx để gateway giả lập gửi lại.
- Chịu được **sai thứ tự**: chuyển trạng thái chỉ đi tới (ví dụ `UPDATE payment_intent SET status='SUCCEEDED' WHERE id=:id AND status IN ('PROCESSING','REQUIRES_ACTION')`); sự kiện `failed` đến sau `succeeded` thì bị bỏ qua và ghi log.
- Mock gateway nên có **nút giả lập lỗi** để demo: thành công, từ chối, timeout không phản hồi, **gửi webhook hai lần**, **gửi webhook trễ sau khi giữ chỗ hết hạn**, gửi sai chữ ký.

**Đối soát và phát hiện bất thường (thay cho đối soát file settlement của cổng thật)**
- Mock gateway giữ **sổ cái riêng** (`gateway_ledger`: charge/refund thật sự đã xảy ra phía cổng). Job đối soát mỗi 1–5 phút so sổ cái với bảng của Tripflix và ghi `reconciliation_anomaly(type, order_id, payment_intent_id, amount, detected_at, resolved_at, resolution)`:
  - `PAID_NOT_CONFIRMED`: payment `SUCCEEDED` (hoặc sổ cái có charge) nhưng đơn chưa `CONFIRMED` sau N phút → tự thử xác nhận lại; nếu kho đã nhả (hold hết hạn) và không giữ lại được → tự refund.
  - `DOUBLE_CHARGE`: hơn một charge thành công cho một đơn → tự refund phần thừa.
  - `MISSED_WEBHOOK`: sổ cái có charge mà payment còn `PROCESSING` → tự áp trạng thái (đối soát là đường dự phòng của webhook).
  - `CONFIRMED_NOT_PAID`, `AMOUNT_MISMATCH`, `REFUND_FAILED` → không tự xử lý, đưa lên trang quản trị.
- Đây cũng là dữ liệu cho tính năng "đơn bất thường" trên trang quản trị (A16 trong docs/03).

### Gaps
- Không đọc được nguyên văn bài Stripe "Designing robust and predictable APIs with idempotency" và bài Airbnb Orpheus (medium.com, stripe.com bị chặn fetch). Chi tiết về **recovery point / atomic phase** (bài "Implementing Stripe-like idempotency keys in Postgres" của Brandur Leach) chưa được xác minh trong phiên này nên không đưa vào phần trích dẫn.
- Không tìm được tài liệu gốc của Airbnb, Booking.com hay Traveloka về **đối soát thanh toán và phát hiện bất thường** (double charge, paid but not confirmed); thiết kế đối soát ở trên là suy luận.
- Không rõ Stripe có lưu cả response 5xx cho key hay không (một nguồn thứ cấp đề nghị nhả khóa khi lỗi hạ tầng); thiết kế trên chọn **không lưu** response lỗi retryable.

---

## 5. Bài kỹ thuật của Traveloka, Booking.com, Expedia, Trip.com, Agoda về kiến trúc

### Takeaway
Tư liệu công khai rất ít. Tìm được một bài của Traveloka Engineering về event-driven + microservices (booking chuyển trạng thái sau thanh toán, scheduler quét DB, đánh dấu đã phát hành), vài nguồn phụ về hệ thống chi trả của Traveloka, tài liệu API công khai của Booking.com và Expedia. **Không** tìm được bài kỹ thuật về booking/payment của Trip.com hay Agoda.

### Cited Findings
- Traveloka Engineering, "How Event-Driven Architecture and Microservices Help Us to Scale to the Next Level": sau khi thanh toán được xác nhận, service cập nhật `booking_status = PAYMENT_VERIFIED`; scheduler quét DB; product aggregator đánh dấu booking đã **issued** để không bị quét lại — [Traveloka Engineering (Medium)](https://medium.com/traveloka-engineering/how-event-driven-architecture-and-microservices-help-us-to-scale-to-the-next-level-b5025f50ec7b)
- Trang tag Traveloka trên Medium có bài "Booking Experience Using Credit Card Method (Pay at Hotel) at Traveloka", nhưng kết quả tìm kiếm không có nội dung — [Medium tag: Traveloka](https://medium.com/tag/traveloka)
- Báo cáo thực tập (2023, Universitas Multimedia Nusantara) về hệ thống **payment out** (chi trả) của Traveloka: Java, ưu tiên bảo mật, khả năng mở rộng, clean architecture; từ khóa Spring Boot, Postgres, AWS, microservices — [UMN Knowledge Center](https://kc.umn.ac.id/id/eprint/24862/) (phạm vi chi trả, không phải luồng khách thanh toán)
- Traveloka cần **thanh toán cho nhà cung cấp theo thời gian thực ngay khi đặt** để nhận xác nhận cho khách; giải bằng cấu trúc quản lý tiền mặt với Citi gồm **thẻ ảo (virtual card)** — [Treasury Today: Adam Smith Awards Asia 2023](https://treasurytoday.com/asa-asia-winners-2023/best-emerging-technology-solution-highly-commended-traveloka-pte-ltd/)
- Traveloka Engineering có bài về **Query Understanding Engine** cho Universal Search (ngoài phạm vi booking) — [Traveloka Engineering (Medium)](https://medium.com/traveloka-engineering/query-understanding-engine-in-traveloka-universal-search-62313439cce4)
- Expedia: có dấu hiệu dùng **tầng điều phối đơn dựa trên workflow** với saga và event sourcing (tin tuyển dụng) — [PowerToFly](https://powertofly.com/jobs/detail/2509486); [Bayt](https://www.bayt.com/en/india/jobs/director-software-development-engineering-74253904/)
- Booking.com và Expedia công bố chi tiết qua **tài liệu API** (Connectivity ARI, Rapid) — xem mục 1, 2, 6.

### Inferences
- Mẫu Traveloka (cập nhật trạng thái sau thanh toán + **scheduler quét DB** + cờ "đã xử lý") rất gần với **transactional outbox + worker poll** đề xuất ở mục 3. Đây là bằng chứng gián tiếp rằng cách "poll bảng Postgres" không phải lối tắt kiểu sinh viên mà là mẫu OTA thật dùng, phù hợp để giải thích với mentor.
- Trạng thái trung gian `PAYMENT_VERIFIED` của Traveloka ứng với `PAID` của Tripflix (đã thu tiền, chưa phát hành), tách khỏi `CONFIRMED`/issued (đã chốt với kho/nhà cung cấp).
- Việc Traveloka phải trả nhà cung cấp ngay khi đặt cho thấy trong OTA thật bước "xác nhận mục" có thể lỗi sau khi khách đã trả. Tripflix giả lập lỗi ở bước này là có cơ sở.

### Gaps
- Không tìm được bài kỹ thuật của **Booking.com** (blog kỹ thuật), **Expedia Group Technology**, **Trip.com**, **Agoda** về kiến trúc booking/payment. Có thể tồn tại nhưng không xuất hiện trong kết quả tìm kiếm của phiên này.
- Không đọc được toàn văn bài Traveloka (medium.com bị chặn fetch); chi tiết khác về event bus, broker và cách chống trùng của Traveloka chưa rõ.

---

## 6. Hình dạng REST API cho luồng đặt chỗ và đề xuất endpoint, thực thể, máy trạng thái, sự kiện cho Tripflix

### Takeaway
Các API công khai đều theo chung một khung: **search → price check/quote (token có hạn) → book (có thể on-hold) → retrieve/cancel**, cộng thêm thanh toán kiểu **PaymentIntent** (tạo intent → confirm → next_action → webhook). Tripflix nên giữ đúng khung này, gộp thành một **đơn (order) nhiều mục**.

### Cited Findings
- **Amadeus Self-Service**: Flight Offers Search → **Flight Offers Price** (xác nhận giá, thuế phí, chỗ trống; trả thêm giá dịch vụ phụ và thông tin thanh toán cần cho việc đặt) → **Flight Create Orders** (`POST /booking/flight-orders` với offer + thông tin hành khách và liên hệ; trả ID đơn và tạo PNR) → **Flight Order Management** (`GET /booking/flight-orders/{flight-orderId}` xem, `DELETE` cùng đường dẫn để hủy); xác thực OAuth 2.0 client_credentials — [jentic: Amadeus Flight Create Orders](https://jentic.com/apis/amadeus.com/amadeus-flight-create-orders); [Ballerina: amadeus.flightcreateorders](https://central.ballerina.io/ballerinax/amadeus.flightcreateorders/latest); [Amadeus Python SDK](https://docsearch.algolia.com/mcp/docs/repo/amadeus4dev/amadeus-python) (đều thứ cấp, không đọc trực tiếp developers.amadeus.com)
- **Expedia Rapid Lodging**: shopping trả link mã hóa → price check xác minh rate → book trả **itinerary ID** và link retrieve/cancel/resume → manage booking — [Expedia Rapid Lodging API overview](https://developers.expediagroup.com/rapid/lodging); [Hold and Resume](https://developers.expediagroup.com/rapid/lodging/booking/hold-resume)
- **Expedia Rapid Activities**: price check trả booking token + trường bắt buộc → create booking (booking token ở query, payment token ở body) → xem, hủy, phát hành voucher — [Expedia Rapid Activities API overview](https://developers.expediagroup.com/rapid/activities)
- Expedia có tài liệu riêng về hóa đơn và biên nhận booking — [Expedia Rapid: Invoices and booking receipts](https://developers.expediagroup.com/docs/products/rapid/lodging/manage-booking/booking-receipts)
- **Stripe PaymentIntents**: trạng thái và webhook như mục 4; có endpoint confirm riêng — [Stripe API: Confirm a PaymentIntent](https://docs.stripe.com/api/payment_intents/confirm); [The Payment Intents API](https://docs.stripe.com/payments/payment-intents)

### Inferences
#### 6.1. Endpoint (tiền tố `/api/v1`, mọi lệnh ghi quan trọng nhận header `Idempotency-Key`)

| # | Method + path | Việc | Mượn từ | Lỗi chính |
|---|---|---|---|---|
| 1 | `GET /hotels/search`, `GET /tickets/search`, `GET /flights/search` | Tìm, đọc cache Redis, giá chỉ tham khảo | Search của Amadeus/Rapid | — |
| 2 | `GET /hotels/{id}/offers?checkIn&checkOut&guests` | Các tổ hợp room type × rate plan còn bán, kèm `offerRef` | Rapid shopping | — |
| 3 | `POST /quotes` body `{items:[{type, offerRef, date(s), qty, seenPrice}]}` | Price check từ Postgres, trả `quoteId`, `total`, `expiresAt`, `priceChanges[]`, `unavailable[]` | Flight Offers Price; Rapid price check | `SOLD_OUT` |
| 4 | `POST /posts/{postId}/itinerary-quote` body `{startDate, travelers}` | "Đặt y chang": đổi lộ trình ngày tương đối thành ngày thật rồi gọi logic của #3; trả mục thay thế nếu mục ngừng bán | Riêng của Tripflix | `SOLD_OUT`, `ITEM_DISCONTINUED` |
| 5 | `POST /orders` + `Idempotency-Key`, body `{quoteId, travelerIds[], contact, failurePolicy}` | Kiểm tuổi, kiểm giá lại, **giữ tất cả mục trong 1 transaction**, trả order `HELD` + `holdExpiresAt` | Rapid book on-hold; Amadeus create order | `403 UNDER_18`, `409 PRICE_CHANGED` (kèm quote mới), `409 SOLD_OUT`, `410 QUOTE_EXPIRED` |
| 6 | `GET /orders/{id}`, `GET /orders?status=` | Xem đơn, timeline trạng thái | Rapid retrieve; Amadeus GET order | `404` |
| 7 | `POST /orders/{id}/cancel` + `Idempotency-Key` | Hủy: đang giữ thì nhả kho; đã xác nhận thì theo chính sách hoàn | Rapid cancel; Amadeus DELETE | `409 NOT_CANCELLABLE` |
| 8 | `POST /orders/{id}/payment-intents` + `Idempotency-Key` | Tạo intent, **số tiền do server tính** từ đơn; đơn → `PENDING_PAYMENT` | Stripe create PaymentIntent | `410 HOLD_EXPIRED` |
| 9 | `POST /payment-intents/{id}/confirm` + `Idempotency-Key`, body `{method: CARD|QR|WALLET, mockCard?}` | Gửi tới mock gateway; trả `REQUIRES_ACTION` + `nextAction` (OTP/QR) hoặc `PROCESSING` | Stripe confirm + `next_action` | `402 PAYMENT_DECLINED`, `410 HOLD_EXPIRED` |
| 10 | `POST /payment-intents/{id}/otp` body `{code}` | Hoàn tất bước 3-D Secure giả lập | Stripe `requires_action` | `422 OTP_INVALID` |
| 11 | `GET /payment-intents/{id}` | Client poll kết quả sau khi chờ webhook | Stripe retrieve | — |
| 12 | `POST /webhooks/mock-gateway` | Nhận `payment.succeeded/failed`, `refund.succeeded/failed`; xác thực HMAC, chống trùng | Stripe webhooks | `400 BAD_SIGNATURE` |
| 13 | `POST /orders/{id}/refunds` + `Idempotency-Key` (quản trị/hệ thống) body `{itemId?, amount, reason}` | Hoàn tiền toàn phần hoặc theo mục | Stripe refunds | `409 REFUND_EXCEEDS_CAPTURED` |
| 14 | `GET /admin/orders?anomaly=PAID_NOT_CONFIRMED` · `GET /admin/reconciliation/anomalies` · `POST /admin/reconciliation/run` · `POST /admin/orders/{id}/simulate-checkin` | Đơn bất thường, chạy đối soát, giả lập check-in để tạo "đã đi" | Riêng của Tripflix (docs/03) | `403` nếu là kiểm duyệt viên |
| 15 | `POST /mock-gateway/scenarios` (chỉ môi trường demo) | Chọn kịch bản: decline, timeout, duplicate webhook, delayed webhook | Riêng | — |

- Mỗi response của đơn nên trả **link hành động tiếp theo** (`links.pay`, `links.cancel`, `links.self`) như link mã hóa của Expedia Rapid, để app không phải tự suy luận trạng thái nào làm được gì.
- Lỗi theo một khung chung: `{ "error": { "code": "PRICE_CHANGED", "message": "...", "details": {...}, "retryable": false } }`, dùng đúng các mã đã chốt trong docs/03.

#### 6.2. Thực thể chính (PostgreSQL)

| Bảng | Cột chính | Ghi chú |
|---|---|---|
| `booking_order` | `id, code, user_id, status, failure_policy, total_amount, currency, hold_expires_at, free_cancel_until, source_post_id, version, created_at` | `version` để optimistic locking khi chuyển trạng thái |
| `booking_item` | `id, order_id, type HOTEL|TICKET|FLIGHT|COMBO, parent_item_id, product_ref, rate_plan_id, start_date, end_date, qty, unit_price, amount, status, supplier_ref` | Combo = item cha với các item con |
| `inventory_hold` | `id, order_item_id, inventory_kind, inventory_ref, date, qty, status, expires_at` | Nhả đúng số đã giữ |
| `quote` | `id, user_id, items JSONB, total_amount, price_fingerprint, expires_at` | Không giữ kho |
| `traveler_profile` | `id, user_id, full_name, date_of_birth, gender, nationality, id_doc_type, id_doc_no_enc, is_self` | Hồ sơ hành khách lưu sẵn |
| `booking_traveler` | `order_id, item_id, traveler snapshot JSONB` | Chụp lại lúc đặt; sửa hồ sơ sau không đổi đơn cũ |
| `payment_intent` | `id, order_id, amount, currency, status, method, gateway_ref, last_error, created_at` | Partial unique: 1 `SUCCEEDED` mỗi đơn |
| `refund` | `id, payment_intent_id, order_item_id, amount, status, reason` | |
| `idempotency_key` | như mục 4 | |
| `webhook_inbox`, `outbox_event`, `processed_event` | như mục 3, 4 | |
| `order_status_history` | `order_id, from_status, to_status, actor_type USER|SYSTEM|ADMIN, actor_id, reason, at` | Nhật ký đơn (audit) |
| `reconciliation_anomaly` | như mục 4 | |

#### 6.3. Máy trạng thái đơn (ánh xạ với docs/03)

```
HELD (Giữ chỗ) ──tạo payment intent──> PENDING_PAYMENT (Chờ thanh toán)
HELD / PENDING_PAYMENT ──hết 10 phút──> EXPIRED            [hold.expired]
HELD / PENDING_PAYMENT ──người dùng hủy──> CANCELLED
PENDING_PAYMENT ──payment.succeeded──> PAID (Đã thanh toán)  [payment.succeeded]
PAID ──mọi mục xác nhận──> CONFIRMED (Đã xác nhận)          [booking.confirmed]
PAID ──một số mục lỗi, KEEP_SUCCEEDED──> PARTIALLY_CONFIRMED [booking.partially_confirmed]
PAID ──mục lỗi, ALL_OR_NOTHING──> FAILED (Thất bại)          [booking.failed]
PAID ──payment đến khi hold đã EXPIRED và không giữ lại được──> FAILED
FAILED / PARTIALLY_CONFIRMED / CONFIRMED(hủy) ──refund xong──> REFUNDED | PARTIALLY_REFUNDED [payment.refunded]
CONFIRMED ──qua ngày trả phòng/sử dụng (hoặc giả lập check-in)──> COMPLETED [booking.completed → mở quyền "Người đã đi"]
```
- Trạng thái mục (`booking_item`): `HELD → CONFIRMED | FAILED`; `HELD → RELEASED | EXPIRED`; `CONFIRMED → CANCELLED → REFUNDED`.
- Mọi chuyển trạng thái đi qua **một hàm duy nhất** (`transition(order, to, actor, reason)`) kiểm bảng chuyển hợp lệ, cập nhật `WHERE id = ? AND version = ?`, ghi `order_status_history` và `outbox_event` trong cùng transaction.

#### 6.4. Sự kiện (tên theo quy ước `domain.verb` của docs/03)

| Sự kiện | Phát bởi | Nhận bởi | Payload tối thiểu |
|---|---|---|---|
| `booking.held` | M3 | M7 (thống kê), M1 (gợi ý phần thiếu) | orderId, userId, items, holdExpiresAt, sourcePostId |
| `hold.expired` | M3 | M4 (hủy intent), M10 (thông báo) | orderId |
| `payment.intent_created`, `payment.requires_action` | M4 | M10 | intentId, orderId |
| `payment.succeeded` / `payment.failed` | M4 | M3 (orchestrator), M7 | intentId, orderId, amount |
| `booking.confirmed` / `booking.partially_confirmed` / `booking.failed` | M3 | M10, M7, M4 (refund nếu cần) | orderId, itemStatuses |
| `payment.refunded` | M4 | M3, M7 | refundId, orderId, itemId, amount |
| `booking.completed` | M3 | Module bài đăng, M7 | orderId, userId, itemIds |
| `reconciliation.anomaly_detected` | M4 | Trang quản trị | type, orderId, intentId |

#### 6.5. Đơn giản hóa so với OTA thật (cho 4 tuần)
- **Không microservices, không Kafka, không Temporal**: một process, một Postgres; saga = trạng thái trong bảng + outbox + worker poll (giống scheduler quét DB của Traveloka).
- **Không distributed lock**: row lock + conditional update + CHECK constraint.
- **Redis chỉ dùng cho**: cache tìm kiếm, rate limit, (tùy chọn) đếm ngược hiển thị; không chứa trạng thái giữ chỗ hay thanh toán.
- **Không shard, không replica**: vẫn ghi lại nguyên tắc Orpheus "idempotency luôn đọc primary" để giải thích khi mentor hỏi về mở rộng.
- **Mock gateway là một module riêng trong cùng repo** với API, sổ cái và webhook thật (gọi HTTP vào chính app), để cơ chế chữ ký, gửi lại, gửi trùng được kiểm thật chứ không chỉ gọi hàm.

### Gaps
- Không truy cập được **Booking.com Demand API** (API cho đối tác bán phòng) nên chưa so được hình dạng endpoint đặt phòng của Booking.com.
- Tài liệu Amadeus lấy qua nguồn thứ cấp (SDK, connector), chưa đọc developers.amadeus.com trực tiếp; tên trường cụ thể (ví dụ `travelers[].dateOfBirth`) chưa xác minh.
- Không xác minh được Expedia Rapid dùng header idempotency riêng hay dựa vào `affiliate_reference_id`.

---

## 7. Kiểm tra dưới 18 tuổi, dữ liệu hành khách, nhật ký đơn, hoàn tiền

### Takeaway
Nguồn công khai cho thấy API đặt vé yêu cầu thông tin hành khách và liên hệ khi tạo đơn (Amadeus) và bước price check trả về **danh sách trường bắt buộc** (Expedia Activities). Không tìm được nguồn về cách các OTA chặn người dưới 18 tự đặt; quy tắc của Tripflix là quyết định nội bộ và phải được **server kiểm**, không chỉ ẩn nút.

### Cited Findings
- Amadeus Flight Create Orders nhận offer **cùng thông tin hành khách và liên hệ**, trả ID đơn và tạo PNR — [jentic: Amadeus Flight Create Orders](https://jentic.com/apis/amadeus.com/amadeus-flight-create-orders) (thứ cấp)
- Expedia Rapid Activities: price check trả booking token **cùng các trường bắt buộc khi đặt** — [Expedia Rapid Activities](https://developers.expediagroup.com/rapid/activities)
- Expedia Rapid hỗ trợ hủy (link hủy trong response tạo và lấy đơn), **Hard Change** (đổi đơn) và tùy chọn **Cancel For Any Reason** (kích hoạt chỉ bằng token dùng trong link đặt) — [Expedia: Hard Change](https://developers.expediagroup.com/rapid/lodging/manage-booking/hard-change); [Expedia: Cancel for any reason](https://developers.expediagroup.com/rapid/lodging/trip-protection/about-cancel-for-any-reason-api)
- Stripe: `canceled` nhả tiền đang giữ và không đảo ngược được — [Stripe: How PaymentIntents work](https://docs.stripe.com/payments/paymentintents/lifecycle)
- Tripflix: thành viên dưới 18 **không tự đặt chỗ**; ngày sinh nhập khi "sẵn sàng đặt", **khóa không sửa**; kiểm thử phải gồm "dưới 18 gọi thẳng API"; kiểm duyệt viên **không xem được thanh toán** — [docs/03, mục 3, 7, 13](../../docs/03-y-tuong-trien-khai.md)

### Inferences
- **Kiểm dưới 18 ở server, tại ba điểm**: `POST /orders`, `POST /orders/{id}/payment-intents`, `POST /payment-intents/{id}/confirm`. Tuổi tính theo `users.date_of_birth` (đã khóa) so với **ngày hiện tại** (người đặt phải đủ 18 khi giao dịch). Hành khách đi kèm có thể dưới 18 (trẻ em đi cùng); quy tắc này áp cho **người đặt**, không áp cho hành khách.
- `date_of_birth` khóa bằng cách chỉ cho ghi khi đang `NULL`; đổi sau đó phải qua quản trị viên và ghi `order_status_history`/audit riêng cho hồ sơ.
- **Hồ sơ hành khách**: lưu `traveler_profile` để chọn nhanh; khi đặt thì **chụp snapshot** vào `booking_traveler` (họ tên đúng giấy tờ, ngày sinh, quốc tịch, số giấy tờ đã mã hóa). Bước quote trả `requiredTravelerFields` theo loại mục (bay cần giấy tờ, vé VinWonders có thể chỉ cần họ tên), giống "trường bắt buộc" của Expedia Activities.
- **Nhật ký đơn**: `order_status_history` (ai, lúc nào, từ đâu sang đâu, vì sao) + `webhook_inbox` + `idempotency_key` cho phép dựng lại toàn bộ câu chuyện của một đơn trên trang quản trị khi mentor hỏi "vì sao đơn này bị trừ tiền mà không xác nhận". Phân quyền: chỉ quản trị viên xem payment và refund.
- **Hoàn tiền**: tách ba loại: (a) hoàn tự động do lỗi hệ thống hoặc bù trừ saga, (b) hoàn theo chính sách hủy của rate plan (`refundable`, `cancel_deadline_hours`), (c) hoàn thủ công bởi quản trị viên. Mọi refund đi qua `POST /orders/{id}/refunds` có Idempotency-Key, trạng thái `PENDING → SUCCEEDED | FAILED`, xác nhận bằng webhook như payment.

### Gaps
- Không tìm được nguồn về chính sách hoặc cách kỹ thuật các OTA (Traveloka, Booking.com, Agoda) **chặn người dưới 18 tự đặt** hay yêu cầu tuổi tối thiểu khi nhận phòng.
- Không tìm được tài liệu về thiết kế **audit log đơn hàng** của OTA lớn.
- Quy định pháp lý Việt Nam về lưu số giấy tờ tùy thân và tuổi được giao kết hợp đồng điện tử chưa được nghiên cứu trong phiên này (ngoài phạm vi).
