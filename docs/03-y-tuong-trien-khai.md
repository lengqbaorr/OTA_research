# 03. Ý tưởng triển khai: "Vinpearl cho người trẻ"

> Tài liệu này kết hợp hai nguồn:
> - [02 – Đề bài của ban tổ chức](02-de-bai-vinpearl-cho-nguoi-tre.md): **nguồn sự thật cao nhất, bắt buộc tuân theo**. Mọi chỗ mâu thuẫn, đề bài thắng.
> - [01 – Kiến thức nền ngành OTA](01-kien-thuc-nganh-OTA.md): dùng để đưa ra lựa chọn có cơ sở.
>
> Ký hiệu: **A1–A16, B1–B7** là mã yêu cầu trong đề bài; "mục X" là mục của đề bài.
> Các con số (thời gian, trọng số, ngưỡng) là **giá trị khởi điểm**. Chúng sẽ được hiệu chỉnh bằng đo đạc, và mọi thay đổi đều ghi lại lý do.

---

## 1. Hiểu bài toán

> Biến Vinpearl từ *"chỗ bố mẹ đi"* thành nơi người trẻ **thấy chuyến đi thật của người thật, rồi đặt y chang chỉ với một chạm**, trong ngân sách của họ và bằng ngôn ngữ của họ.

| Trụ cột | Ý nghĩa | Yêu cầu |
|---|---|---|
| **Nội dung thật** | Chỉ người đã đặt và đã đi mới đăng bài. Lộ trình lấy từ đơn thật | A2, A10, A14, mục 5 |
| **Khám phá theo gu & xu hướng** | Bảng tin xu hướng, tìm bằng lời, gợi ý cá nhân, trợ lý | A1, A3, A6, A7, A12, A15 |
| **Đặt nhanh, đặt đúng** | Một chạm tới trang chi tiết, đặt theo lộ trình, giá thật, không trừ tiền hai lần, không bán trùng chỗ cuối | A4, A8, A9, A11, A16 |

**Bốn sản phẩm, một nguồn dữ liệu**: Android app, Web OTA, Trợ lý (chạy trong app và web), Trang quản trị. Tất cả dùng chung một tài khoản và một backend (mục 7).

**Phạm vi**: chỉ dịch vụ Vinpearl (khách sạn/resort, VinWonders, golf, vé máy bay, combo); thanh toán giả lập; bộ dữ liệu sản phẩm và khách hàng mô phỏng **không có bài đăng**. Ngoài phạm vi: iOS, tính năng nhóm, thanh toán thật, hệ thống khách hàng thật, dịch vụ ngoài Vinpearl, ngôn ngữ khác, CS người thật.

---

## 2. Áp dụng tri thức OTA vào đề bài

Phần này cho thấy *vì sao* các lựa chọn ở sau là đúng hướng. Mỗi dòng là một bài học của ngành được áp vào một yêu cầu cụ thể.

| Tri thức ngành (tài liệu 01) | Áp vào đề bài |
|---|---|
| **Tồn kho dễ hỏng, giá động, giữ chỗ có hạn (Hold/TTL)** | Giữ chỗ 10 phút khi vào màn xác nhận; giá chốt theo báo giá có hạn; giá đổi thì báo trước khi trả (A4) |
| **Trạng thái "đã trả mà không có chỗ" là khiếu nại số 1** | Máy trạng thái đơn chặt chẽ, tự hoàn tiền, A16 phát hiện đơn bất thường |
| **Idempotency, đối soát đơn ↔ thanh toán** | Không trừ tiền hai lần (A4, A8, A11); bộ phát hiện bất thường đối chiếu đơn với giao dịch |
| **Overbooking, tranh chỗ cuối** | Trừ tồn kho nguyên tử: hai người tranh chỗ cuối thì đúng một người được |
| **Dynamic packaging / combo: một mục hỏng kéo cả gói** | Đặt theo lộ trình = giỏ nhiều mục, giữ chỗ tất cả trước rồi mới trả tiền, có chính sách khi một mục hỏng (A11) |
| **Review đã xác minh** (Booking, Airbnb) | Bài đăng gắn với đơn đã hoàn thành, kèm dấu "Đã đi thật" (A2), phục vụ persona Hà |
| **UGC + AI của Trip.com** (Trip Moments, TripGenie) | Bảng tin bài đăng + trợ lý gợi ý (A1, A12) là hướng đã được thị trường chứng minh |
| **Chống gian lận: lạm dụng nhiều tài khoản** | Chống đẩy xu hướng ảo bằng điểm tin cậy tài khoản và gom cụm thiết bị (A15) |
| **Dark patterns bị xử phạt** (khan hiếm giả, giá nhỏ giọt) | Không "chỉ còn 1 phòng" sai sự thật, không đếm ngược giả, luôn hiện giá cuối (mục 5, mục 6) |
| **Look-to-book cao, chi phí tra cứu** | Cache kết quả tìm kiếm và trợ lý; giới hạn tần suất; đo chi phí trợ lý (mục 7) |
| **Back-office là "khối chìm" quyết định vận hành** | Trang quản trị không làm qua loa: hàng chờ ưu tiên, audit log, phân quyền (A13–A16) |
| **Hành trình khách: Mơ ước → Kế hoạch → Đặt → Trải nghiệm → Chia sẻ** | App phủ trọn vòng: bảng tin (mơ ước), tìm và trợ lý (kế hoạch), đặt ngay (đặt), Chuyến đi của tôi (trải nghiệm), đăng bài (chia sẻ, quay lại nuôi bảng tin) |
| **Bán kèm tăng biên, connected trip** | Gợi ý sau khi đặt: có phòng thì gợi ý vé máy bay và hoạt động, không gợi ý thêm phòng (A9) |
| **Pháp lý: dữ liệu cá nhân, người tiêu dùng, khuyến mại** | Tối thiểu hóa dữ liệu gửi cho trợ lý; lộ trình không lộ thông tin riêng; không bịa khuyến mãi; ≥ 18 tuổi mới tự đặt |
| **Đặc điểm người Việt: nhạy giá, nói lóng, mạng di động** | Hiểu "2 củ", "4 đứa", không dấu; tối ưu cho máy yếu và mạng yếu |

---

## 3. Persona → Yêu cầu → Kịch bản sẽ bị thử

| Persona | Nhu cầu | Yêu cầu | Người hướng dẫn có thể thử |
|---|---|---|---|
| **Vy, 21** | Đúng ngân sách, nói "dưới 2 củ" | A3, A6, A7, A12 | Gõ không dấu "cho chill duoi 2 cu": có lẫn resort 5 sao không? |
| **Khang, 25** | Thấy bài Phú Quốc 3 ngày là đặt y vậy, quyết sát ngày | A1, A4, A11 | Bấm "Đặt ngay": bao nhiêu thao tác? Mục hết chỗ thì sao? |
| **Linh, 22** | Đăng ảnh/video + lộ trình | A2, A10 | Chưa đi mà đăng bài được không? Mất mạng giữa chừng thì mất bài không? |
| **Hà, 23** | Không tin bài quá đẹp | A2, A14, B1 | Có dấu "đã đi thật" không? Bài quảng cáo có lọt không? |
| **Bạn 17 tuổi** | Muốn đặt phòng | A8, mục 5 | Tự đặt được không? Gọi thẳng API có lách được không? |
| **Người mới tải app** | Chưa có tài khoản, chưa biết đi đâu | A1, A12 | Có thấy bảng tin ngay không? Bấm đặt rồi đăng nhập thì có quay lại đúng chỗ không? |
| **Nhân viên kiểm duyệt** | Hàng trăm mục/ngày | A13, A14 | Hàng chờ có ưu tiên không? Ẩn bài thì bao lâu biến mất? Có xem được thanh toán không (phải là không)? |

**Bảng truy vết rút gọn** (mốc theo mục 10 đề bài):

| Mốc | Yêu cầu | Độ rủi ro |
|---|---|---|
| 1 | A1 (v1), A4 (trang chi tiết), A6, A7, A12 (thử nghiệm), A13 | A1, A6: cao |
| 2 | A2, A3, A4 (đặt nhanh), A8, A9, A10, A12 (gợi ý địa điểm), A14, A16 | **A4, A8 rất cao**; A2, A3, A10 cao |
| 3 | A5, A11, A12 (gợi ý bài đăng, đa nền tảng), A15, phần B | **A11 rất cao** |

---

## 4. Đề xuất cho các câu hỏi mở (mục 5)

Mỗi đề xuất có ba phần: **Đề xuất → Vì sao → Thể hiện trong sản phẩm**.

### 4.1. Xu hướng

**"Đang được quan tâm" tính theo gì?** Kết hợp nhiều tín hiệu. Mỗi tín hiệu tính **theo số người khác nhau**, nhân **trọng số** của hành động, nhân **độ tin cậy tài khoản**, rồi **giảm dần theo thời gian**.

| Tín hiệu | Trọng số | Lý do |
|---|---|---|
| Xem đủ lâu (≥ 3 giây hoặc mở chi tiết) | 1 | Rẻ, dễ giả |
| Thả cảm xúc | 2 | Ít công |
| Bình luận (người khác nhau, không rác) | 3 | Tốn công hơn |
| Lưu bài / Chia sẻ có người mở | 4 | Thể hiện ý định đi |
| **Đặt theo lộ trình** | 10 | Mạnh nhất, khó giả nhất (cần tài khoản ≥ 18 tuổi, đi hết luồng đặt) |
| Bỏ qua nhanh / ẩn bài | –1 | Phản hồi tiêu cực |

`Điểm(t) = Σ tín hiệu [ trọng số × Σ người ( tin cậy × suy giảm theo thời gian ) ]`. Xu hướng *địa điểm* = tổng điểm các bài về chỗ đó + lượt xem trang chi tiết + lượt đặt.
→ *Trong sản phẩm*: mỗi bài có **lý do ngắn bằng số thật** ("32 người lưu trong 24h", "5 người đã đặt theo lộ trình này tuần này"); admin có bảng phân rã điểm.

**Bao lâu lên bảng tin, bao lâu hạ nhiệt?**
- Tính lại **mỗi 5 phút**, có phát hiện tăng tốc (1 giờ gần nhất so với nền 7 ngày) → bài tăng vọt lên bảng tin trong **5–15 phút**.
- Suy giảm theo **chu kỳ bán rã 24 giờ** cho bài đăng và 72 giờ cho địa điểm → bài không còn tương tác rời nhóm xu hướng sau **2–3 ngày**.
- *Vì sao*: người trẻ quyết trước 1–2 ngày (persona Khang) và đi theo nhịp cuối tuần, nên bài hot cần ở trên bảng tin đúng cửa sổ ra quyết định đó, đồng thời bảng tin vẫn mới mỗi ngày. Địa điểm thay đổi chậm hơn bài đăng.
- *Trong sản phẩm*: admin có biểu đồ điểm theo thời gian; có kịch bản demo dồn tương tác vào một bài rồi xem nó lên và xuống.

**Chống một người tạo nhiều tài khoản để đẩy bài**:
1. Một tài khoản chỉ được tính một lần mỗi loại tín hiệu trên mỗi bài (bấm 100 lần vẫn là 1).
2. **Điểm tin cậy tài khoản** (0–1): tăng theo tuổi tài khoản, đã xác minh, **đã có đơn hoặc đã đi**, hành vi đa dạng.
3. Gom cụm theo thiết bị/mạng: nhiều tài khoản cùng nguồn tính như một vài người.
4. Phát hiện bùng nổ: tỷ lệ tương tác từ tài khoản tin cậy thấp vượt ngưỡng → gắn cờ "nghi đẩy ảo", đóng băng điểm.
5. Tác giả tự tương tác bài mình: 0 điểm.

*Vì sao*: không thể chặn tuyệt đối việc tạo nhiều tài khoản, nhưng có thể làm việc đó **tốn công mà vô hiệu** (50 tài khoản mới có sức nặng xấp xỉ 5 người thật). *Trong sản phẩm*: A15 có danh sách "Xu hướng đáng ngờ" kèm lý do.

**Người mới chưa có gu**: hiện xu hướng chung ngay, không bắt đăng nhập, có đa dạng điểm đến và mức giá. Chen một thẻ "Bạn thích kiểu nào?" có thể bỏ qua. Học từ các tương tác đầu trên định danh ẩn danh, đăng nhập thì gộp vào tài khoản. Bảng tin trộn ~60% hợp gu, ~20% xu hướng chung, ~20% khám phá để tránh bong bóng thông tin.

### 4.2. Bài đăng & tương tác

**Biết chắc người đăng đã đi thật**: bài **gắn với một đơn của chính người đó** ở trạng thái *Đã hoàn thành* (đã qua ngày trả phòng/sử dụng, không hủy, đã thanh toán giả lập). Server kiểm tra lại khi đăng, không chỉ ẩn nút. Dấu hiển thị "Đã đi thật · Hè 2026" ở mức mùa, **không hiện ngày cụ thể**.
*Vì sao*: dữ liệu đơn thuộc Vinpearl nên không giả được, đúng như cơ chế review đã xác minh của các OTA lớn.

**Báo cáo thì ẩn ngay hay chờ? Tự động kiểm duyệt? AI Agent?** Kiểm duyệt 3 tầng:

| Tầng | Quy tắc |
|---|---|
| **Sàng lọc tự động khi đăng** (AI + luật) | Phân loại an toàn / cần xem / nguy hiểm. Các nhóm: xúc phạm, quảng cáo, lừa đảo (số điện thoại, link lạ, "chuyển khoản trước"), nguy hiểm, **thông tin cá nhân**, **mặt người lạ**. An toàn thì hiện ngay; cần xem thì hiện và vào hàng chờ; nguy hiểm thì giữ lại chờ duyệt |
| **Báo cáo** | Ưu tiên theo số người báo × độ tin cậy người báo × mức nghiêm trọng. **Tự ẩn tạm** khi có ≥ 3 người báo cáo tin cậy khác nhau, hoặc AI xác nhận nhóm nghiêm trọng (lừa đảo, nguy hiểm, lộ thông tin cá nhân) |
| **Con người quyết định** | Kiểm duyệt viên hiện/ẩn/xóa và **bắt buộc chọn lý do**. AI Agent gợi ý hành động, lý do và đoạn vi phạm; người bấm xác nhận |

*Vì sao*: ẩn ngay mọi thứ bị báo cáo thì dễ bị lạm dụng để "đánh" bài thật; chờ hoàn toàn thì lừa đảo tồn tại lâu. AI chỉ gợi ý để giữ trách nhiệm giải trình theo A14.

**Sửa bài sau khi đăng**: mọi bản sửa đều qua sàng lọc tự động. Sửa chữ nhỏ và an toàn thì hiện ngay. Đổi ảnh, video, lộ trình hoặc bị AI gắn cờ thì vào hàng chờ, trong lúc đó vẫn hiện bản cũ đã duyệt. Lưu lịch sử phiên bản, admin xem được khác biệt giữa các bản. *Vì sao*: chặn chiêu "đăng sạch rồi sửa thành quảng cáo".

**Số lượt cảm xúc**: **kết quả cuối luôn đúng tuyệt đối, độ trễ hiển thị giữa các thiết bị ≤ 2 giây.** Nguồn sự thật là bản ghi duy nhất cho mỗi cặp người × bài; bấm liên tục chỉ bật/tắt bản ghi đó. Người bấm thấy kết quả ngay trên máy mình; nơi khác nhận qua kênh realtime. *Vì sao*: "ngay lập tức trên mọi máy" đòi khóa đồng bộ rất tốn kém mà mắt người không phân biệt được 0 với 2 giây. Điều người dùng cảm nhận được là con số không bao giờ sai.

### 4.3. Đặt theo lộ trình

**Lộ trình lưu theo "ngày tương đối"** (Ngày 1, 2, 3…), **không lưu ngày thật** ở phần công khai.

| Hiện (người đăng bật/tắt từng mục) | Luôn riêng tư |
|---|---|
| Điểm đến, tên khách sạn/khu vui chơi/golf, loại phòng/loại vé, thứ tự theo ngày, số đêm, chặng bay (nơi đi – nơi đến), ghi chú của người đăng, **giá hiện tại** lúc người xem mở bài | Ngày đi/về, **số tiền đã trả**, mã đặt chỗ, số hiệu chuyến kèm ngày cụ thể, thông tin người đi cùng, số người đi (mặc định ẩn), lịch sử đặt khác |

*Vì sao*: đủ để người khác đặt y chang, không lộ thông tin cá nhân; việc dời theo ngày mới trở nên tự nhiên.

**Người đăng xóa bài / mục ngừng bán**:
- Xóa bài → nội dung biến mất; trong "Bài đã lưu" của người khác hiện thẻ *"Bài này đã được tác giả gỡ"*. Các đơn đã đặt theo bài đó vẫn còn nguyên.
- Bài bị kiểm duyệt ẩn → *"Bài này không còn hiển thị"*.
- Mục ngừng bán hoặc hết chỗ → bài vẫn hiện, mục đó có nhãn rõ. Khi "Đặt ngay" thì gợi ý mục tương tự (cùng điểm đến, cùng loại, giá gần nhất) hoặc cho bỏ mục đó.

**Một mục thất bại khi thanh toán cả lộ trình**: áp dụng mô hình **"giữ chỗ tất cả → thanh toán một lần → xác nhận tất cả"** (cách xử lý combo của ngành):
1. Vào màn xác nhận thì **giữ chỗ và chốt giá mọi mục**. Mục nào giữ không được thì báo ngay, để bỏ, đổi hoặc chọn mục tương tự. Phần lớn thất bại được phát hiện **trước khi trả tiền**.
2. Thanh toán một giao dịch, có mã chống trùng.
3. Nếu vẫn hỏng sau khi trả (hiếm), áp dụng chính sách **người dùng đã chọn trước**:
   - **Mặc định "Giữ phần đặt được"**: mục lỗi tự hoàn tiền ngay (giả lập), báo rõ từng mục và số tiền; cho **hủy miễn phí phần còn lại trong 30 phút**.
   - **Tùy chọn "Tất cả hoặc không"**: hủy toàn bộ, hoàn toàn bộ.

*Vì sao*: không có lựa chọn đúng cho mọi người (mất vé vui chơi khác hẳn mất vé máy bay). Cho người dùng chọn trước là trung thực và không ép mua.

**"Chênh lệch nhiều" (A11)**: không so với giá người đăng đã trả (vì riêng tư). Thay vào đó so tổng giá hiện tại với **"mức giá thường thấy"** của chính các mục đó (trung vị 30 ngày). Lệch > 20% thì báo rõ và gợi ý dời 1–2 ngày.

### 4.4. Trợ lý

**Dựa vào đâu, làm sao không bịa?**
1. **Hiểu câu hỏi** → điều kiện có cấu trúc: điểm đến, ngày ("cuối tuần này"), số người ("4 đứa"), ngân sách ("3 củ", tính theo người hay cả nhóm), gu ("chill", "quẩy", "không đông").
2. **Chỉ lấy dữ liệu qua công cụ** của hệ thống: tìm địa điểm, giá và chỗ, tìm bài **đang hiển thị**. Đây là cùng bộ tìm kiếm với A3/A6.
3. **Gợi ý phải gắn ID có thật.** Thẻ hiển thị (tên, giá, ảnh, link) được dựng từ dữ liệu hệ thống; mô hình chỉ viết câu dẫn và lý do.
4. **Validator sau khi sinh**: kiểm ID tồn tại, đang bán hoặc đang hiển thị, giá trong câu chữ khớp dữ liệu. Sai thì loại gợi ý đó.
5. **Trích dẫn bài đăng phải là đoạn có thật** trong bài; không có thì không được gán.
6. Không có gì phù hợp → nói thật và gợi ý nới điều kiện.
7. **Không có công cụ đặt chỗ**, nên về mặt kỹ thuật trợ lý không thể tự đặt thay người dùng.

**Nhớ gì, bao lâu**:

| Loại | Thời hạn |
|---|---|
| Ngữ cảnh hội thoại | Theo cuộc trò chuyện, lưu 30 ngày (đã đăng nhập) |
| Sở thích bền (ngân sách, gu, điểm đến quan tâm) | Đến khi người dùng xóa. Hiển thị cho người dùng xem/sửa/xóa ở mục "Trợ lý nhớ về bạn" |
| Chưa đăng nhập | Gắn định danh ẩn danh trên thiết bị, 7 ngày |
| Đăng nhập giữa chừng | Gộp cuộc trò chuyện vào tài khoản, không mất |
| Đa thiết bị | Lưu ở server, nên mở web vẫn tiếp tục được cuộc trò chuyện đang dở trên app |

**Được dùng thông tin gì**: ngân sách và gu người dùng đã nói; thích/lưu/bỏ qua trên app và web; điểm đến của các chuyến đã đặt (để không gợi ý trùng). **Không dùng**: thanh toán, số điện thoại, giấy tờ, ngày sinh cụ thể (chỉ dùng cờ "đủ 18"), dữ liệu người khác, bài đã ẩn. Dữ liệu gửi cho mô hình bên ngoài được tối thiểu hóa: không gửi tên, email, số điện thoại.

**Chi phí & giới hạn**:
- **Đo** token vào/ra mỗi lượt, quy ra VNĐ, tổng hợp theo lượt, cuộc trò chuyện, người, ngày.
- **Giảm chi phí**: mô hình nhỏ cho hiểu câu hỏi và phân loại an toàn, mô hình lớn chỉ khi cần diễn đạt; cache câu phổ biến; tóm tắt lịch sử; chỉ đưa top 10 kết quả vào mô hình.
- **Giới hạn khởi điểm**: chưa đăng nhập 20 tin nhắn/ngày/thiết bị; đã đăng nhập 50 tin nhắn/ngày; ~500 ký tự/tin; có trần chi phí theo ngày cho toàn hệ thống, vượt thì báo thân thiện và gợi ý dùng ô tìm kiếm.

### 4.5. Đặt chỗ

**Giữ chỗ bao lâu**: **10 phút**, áp dụng cho mọi loại sản phẩm và từng mục của lộ trình. Hiển thị giờ hết hạn **thật** ("giữ đến 14:32"); đây là thông tin trung thực chứ không phải khan hiếm giả. Hết hạn thì nhả chỗ, quay lại thì kiểm tra lại chỗ và giá. Mỗi tài khoản giữ tối đa 2 phiên cùng lúc để chống giữ chỗ ảo.

**App và web mở cùng lúc**: đang mở và có mạng thì đồng bộ realtime, **≤ 2–3 giây (p95)**; app ở nền hoặc vừa có mạng lại thì đồng bộ ngay khi mở. Xung đột: đơn đặt chỗ do server quyết (thao tác đến sau bị từ chối nếu trạng thái đã đổi); lưu và thả cảm xúc thì thao tác cuối thắng; bài nháp giữ bản mới nhất và cảnh báo nếu có bản mới hơn ở máy khác.

### 4.6. Các quyết định bổ sung

| Chủ đề | Đề xuất |
|---|---|
| **Dưới 18 tuổi** | Ngày sinh bắt buộc khi đăng ký và khóa không cho sửa; server kiểm tra ở bước giữ chỗ **và** thanh toán, áp dụng cho cả app, web, đặt thường, đặt theo lộ trình. Thông báo thân thiện: *"Bạn cần đủ 18 tuổi để tự đặt chỗ. Bạn vẫn có thể khám phá, lưu bài và lên ý tưởng chuyến đi!"* Giới hạn này cần nêu trung thực khi trình bày: tuổi chỉ dựa trên lời khai |
| **Ngân sách theo người hay cả nhóm** | Có từ "một người/mỗi người" thì hiểu là theo người; có "tổng/cả nhóm" thì hiểu là cả nhóm. Không rõ thì hiện chip chọn "2 củ mỗi người / 2 củ cả nhóm" thay vì hỏi dồn |
| **Giá đổi** | Báo giá có hiệu lực trong thời gian giữ chỗ; giá đổi thì hiện chênh lệch và bắt xác nhận lại trước khi trả |
| **Đường dẫn chia sẻ** | Là link web mở được ở mọi trình duyệt; máy đã cài app thì mở thẳng vào app (App Links) |
| **Ảnh có mặt người lạ / thông tin cá nhân** | Khi tải ảnh lên, phát hiện khuôn mặt và chữ nhạy cảm, đề xuất làm mờ; người đăng chọn giữ mặt mình và bạn mình. Ảnh có giấy tờ hoặc thẻ lên máy bay chưa làm mờ thì không cho đăng |

---

## 5. Giải pháp & kiến trúc

### 5.1. Nguyên tắc
1. **Một backend, một CSDL, nhiều giao diện.** Không giao diện nào giữ dữ liệu riêng.
2. **Server là nguồn sự thật cho tiền, chỗ, tuổi, quyền.** Không tin giao diện.
3. **Offline-first cho thao tác viết trên app**: nháp, bình luận, tương tác lưu trên máy trước, gửi khi có mạng.
4. **Nhẹ cho máy yếu, mạng yếu**: ảnh nhiều kích cỡ, video thích ứng theo mạng, phân trang, tải trước có kiểm soát.
5. **AI không phải nguồn sự thật**: chỉ diễn giải và xếp hạng; dữ liệu hiển thị lấy từ hệ thống.
6. **Ghi nhận sự kiện từ ngày đầu** cho xu hướng, cá nhân hóa và đo đạc.

### 5.2. Kiến trúc khái niệm

```
 Android App (offline-first)    Web OTA (SSR cho link chia sẻ)    Trang quản trị (RBAC)
            └──────────── REST/JSON + kênh realtime (WebSocket/SSE) ────────────┘
                                            ▼
 ┌─────────────────────────── BACKEND (modular monolith) ───────────────────────────┐
 │ Identity & Auth │ Catalog & Inventory │ Pricing & Quote │ Booking & Hold │ Payment Sim │
 │ Posts & Media │ Interactions │ Moderation │ Trending │ Personalization │ Notifications │
 │ Search (hiểu tiếng Việt người trẻ + ngữ nghĩa) │ Assistant (LLM + tools + validator) │
 │ Event log / Analytics │ Audit log                                                    │
 └─────┬───────────────────┬────────────────────┬────────────────────┬────────────────┘
   CSDL quan hệ       Cache, bộ đếm,        Chỉ mục tìm kiếm      Lưu trữ media + CDN
   (đơn, tiền, chỗ)   hàng đợi sự kiện      (từ khóa + vector)    (ảnh/video nhiều cỡ)
                                  ▲
                 Mô hình ngôn ngữ bên ngoài (qua lớp đo chi phí)
```

**Vì sao chọn modular monolith**: đội nhỏ, 4 tuần, cần giao dịch chặt giữa giữ chỗ, thanh toán và đơn. Một khối triển khai giảm lỗi phân tán; module ranh giới rõ để sau này tách ra được khi cần.

### 5.3. Phương án cho từng sản phẩm

**Android app**

| Phương án | Ưu | Nhược | Kết luận |
|---|---|---|---|
| **Native Kotlin + Jetpack Compose** | Hiệu năng tốt nhất trên máy yếu; công cụ chuẩn cho lưu cục bộ, tác vụ nền, App Links; đo hiệu năng dễ | Không chia sẻ code với web | **Khuyến nghị mặc định** nếu đội có người biết Kotlin |
| Flutter | Hiệu năng khá, giao diện nhất quán | App nặng hơn; offline và tác vụ nền cần thêm thư viện | Nếu đội mạnh Dart |
| React Native | Dùng chung TypeScript với web | Cần tối ưu kỹ cho danh sách dài trên máy yếu | Nếu đội mạnh React |
| PWA | — | Không đúng yêu cầu "Ứng dụng Android" | Không chọn |

Kỹ thuật bắt buộc dù chọn phương án nào:
- Phân trang bằng **con trỏ** (cursor) để không lặp bài.
- Ảnh thumbnail ~20–60KB, tải lười.
- Video thích ứng theo mạng, chỉ tự phát khi đang nhìn thấy.
- Cache bảng tin đã xem (nền tảng cho B4).
- **Hàng đợi gửi lại** cho nháp và bình luận, có mã chống trùng.
- **Tải video lên theo từng phần** để tiếp tục được sau khi mất mạng.
- **Lưu "ý định"** trước khi đăng nhập (đang đặt gì, đang ở bài nào) để quay lại đúng chỗ.

**Web & Admin**: framework có **render phía server** cho web người dùng (link chia sẻ có ảnh xem trước, tải nhanh trên điện thoại). Admin dùng cùng framework, là ứng dụng riêng, không cần SSR.

**Backend & dữ liệu**

| Thành phần | Đề xuất | Vì sao |
|---|---|---|
| CSDL chính | CSDL quan hệ có giao dịch (ví dụ PostgreSQL) | ACID và ràng buộc duy nhất cho đơn, tiền, chỗ |
| Bộ đếm, hàng đợi, giới hạn tần suất | Kho khóa–giá trị (ví dụ Redis) | Đếm nhanh, phát sự kiện realtime |
| Tìm kiếm | Kết hợp từ khóa + vector (ví dụ phần mở rộng vector của PostgreSQL) | Vừa khớp chính xác tên chỗ, vừa hiểu ý ("không đông" ≈ "vắng") |
| Media | Lưu trữ đối tượng + CDN, xử lý nén ở nền | Mạng yếu, máy yếu |
| Realtime | WebSocket hoặc SSE | "Thấy ngay" giữa app và web |
| Ngôn ngữ backend | Theo kỹ năng của đội | Quan trọng là làm đúng giao dịch và idempotency |

### 5.4. Ba cơ chế sống còn cho đặt chỗ (phải giải thích được nguyên lý)
1. **Giữ chỗ nguyên tử**: trừ tồn kho bằng câu lệnh có điều kiện "chỉ trừ nếu còn ≥ 1" trong giao dịch. Hai người tranh chỗ cuối thì đúng một người được.
2. **Idempotency key**: mỗi lần thanh toán mang một mã duy nhất. Server và cổng giả lập chỉ xử lý mã đó một lần, nên bấm hai lần, thử lại hay webhook trùng đều không trừ tiền hai lần.
3. **Máy trạng thái đơn**: Giữ chỗ → Chờ thanh toán → Đã thanh toán → Đã xác nhận / Thất bại → Hoàn tiền. Chỉ cho chuyển theo đường hợp lệ. Đơn lệch trạng thái được A16 phát hiện.

**Cổng thanh toán giả lập** có công tắc tạo tình huống: thành công, thất bại, chậm phản hồi, gửi xác nhận trùng, làm lỗi một mục cụ thể. Dùng để demo và kiểm thử.

### 5.5. Tìm bằng lời của mình (A3, A6) và Trợ lý (A12)

```
Câu người dùng
 → Chuẩn hóa: giữ cả bản có dấu và không dấu; từ điển teencode ("k"→không, "cn"→chỗ nào,
   "củ"→triệu, "4 đứa"→4 người); emoji → nghĩa (🏖️→biển)
 → Hiểu ý: trích điều kiện {điểm đến, ngày, số người, ngân sách (mỗi người | cả nhóm), gu}
 → Lọc cứng: ngân sách, sức chứa, còn chỗ, bài đang hiển thị
 → Xếp hạng lai: từ khóa + ngữ nghĩa + gu cá nhân + xu hướng
 → Dưới ngưỡng liên quan thì KHÔNG hiện → "Chưa có chỗ phù hợp" + gợi ý nới điều kiện
```

Trợ lý dùng lại toàn bộ pipeline trên dưới dạng **công cụ**. Thêm:
- **Bộ phân loại an toàn** chạy trước: ngoài phạm vi, nguy hiểm, prompt injection → từ chối thân thiện, không gọi mô hình lớn.
- **Validator** kiểm tra mọi gợi ý trước khi hiện.
- **Lớp đo chi phí** bao quanh mọi lần gọi mô hình.
- **Chọn mô hình bằng đo đạc**: thử 2–3 mô hình trên bộ câu hỏi thử, so độ hiểu tiếng Việt kiểu người trẻ, độ gọi công cụ đúng, chi phí và độ trễ. Đây là nội dung "thử nghiệm cách làm" của mốc 1.

### 5.6. Xu hướng, cá nhân hóa, quản trị
- Điểm xu hướng tính mỗi 5 phút từ nhật ký sự kiện.
- Hồ sơ gu cập nhật từ hành vi trên **cả app và web**.
- Mỗi bài/gợi ý có lý do ngắn sinh từ tín hiệu thật.
- **RBAC**: Quản trị (toàn quyền, có đơn và thanh toán) và Kiểm duyệt (hàng chờ, bài, bình luận, xu hướng; **không** có quyền gọi API thanh toán). Kiểm tra quyền ở server cho từng API.
- **Audit log chỉ ghi thêm**: ai, lúc nào, hành động, đối tượng, lý do, gợi ý AI là gì.
- **Bộ phát hiện bất thường đơn** (A16): định kỳ đối chiếu đơn, giao dịch và tồn kho để tìm đơn trả tiền hai lần, đã trả mà không có chỗ, lộ trình chỉ đặt được một phần.

---

## 6. Mô hình dữ liệu & quy ước trao đổi (chốt trong tuần 1, theo mục 11)

| Nhóm | Thực thể | Lưu ý |
|---|---|---|
| Người dùng | `User` (ngày sinh bắt buộc, khóa sửa), `Profile` (gu, ngân sách), `Device`, `AnonymousSession` | Phiên ẩn danh gộp vào User khi đăng nhập |
| Nhân viên | `StaffUser`, `Role` (admin, moderator) | Tách riêng khỏi User |
| Sản phẩm | `Destination`, `Property`, `RoomType`, `Attraction`, `TicketType`, `Flight`, `Combo` + `ComboItem` | Lấy từ bộ dữ liệu được cấp |
| Tồn kho & giá | `Inventory` (sản phẩm × ngày × số còn), `Price` (sản phẩm × ngày), `PriceQuote` (có hạn) | Giá đổi thì báo giá cũ hết hiệu lực |
| Đặt chỗ | `BookingSession`, `Hold` (10 phút), `Booking`, `BookingItem` (trạng thái riêng từng mục), `Payment` (idempotency_key duy nhất), `Refund` | `Booking.source_post_id` nếu đặt theo lộ trình |
| Chuyến đi | `Trip` (gom các Booking của một chuyến) | Nguồn cho "Chuyến đi của tôi" và lộ trình bài đăng |
| Bài đăng | `Post` (gắn `booking_id` đã hoàn thành, cờ mô phỏng), `PostVersion`, `PostMedia` (nhiều cỡ, cờ đã làm mờ), `Itinerary` + `ItineraryItem` (**day_offset**, product_id, ghi chú, cờ hiển thị; **không có ngày thật hay giá đã trả**) | — |
| Tương tác | `Reaction` (duy nhất theo user × post), `Comment` (cây trả lời), `Save`, `Share`, `Report` | Ràng buộc duy nhất chống đếm trùng |
| Kiểm duyệt | `ModerationItem` (độ ưu tiên, nhãn AI), `ModerationAction` (audit) | — |
| Xu hướng | `InteractionEvent`, `TrendScore` (phân rã theo tín hiệu), `TrustScore`, `TrendFlag` | — |
| Trợ lý | `Conversation`, `Message`, `AssistantMemory`, `AssistantUsage` (token, chi phí) | — |
| Thông báo | `Notification` | — |

**Quy ước API**:
- API có phiên bản (`/v1`); ngày giờ theo ISO 8601 có múi giờ; **tiền là số nguyên VNĐ**.
- Phân trang bằng con trỏ.
- **Idempotency-Key** bắt buộc cho giữ chỗ, thanh toán, đăng bài, bình luận, tương tác.
- Mã lỗi nghiệp vụ dùng chung (`PRICE_CHANGED`, `SOLD_OUT`, `HOLD_EXPIRED`, `UNDER_18`, `NOT_TRAVELED`, `RATE_LIMITED`…) để app và web hiện cùng thông điệp.
- Sự kiện realtime đặt tên thống nhất (`post.reaction.updated`, `booking.status.changed`, `post.hidden`…).
- **Viết đặc tả API trước khi làm giao diện**, để app, web và admin làm song song với dữ liệu giả.

---

## 7. Trung thực & an toàn (mục 6): cách làm và cách kiểm chứng

| Yêu cầu | Cách làm | Cách kiểm chứng |
|---|---|---|
| Không bịa chỗ, giá, khuyến mãi, chỗ trống | Mọi số hiển thị lấy thẳng từ dữ liệu; trợ lý qua validator | So khớp ngẫu nhiên 100 màn hình với dữ liệu; bộ câu hỏi trợ lý |
| Không bài đăng giả | Bài gắn đơn đã hoàn thành; bài mô phỏng luôn có nhãn | Test API đăng bài khi chưa đi phải bị từ chối |
| Không khan hiếm giả, không ép mua | Chỉ hiện số liệu thật; không đếm ngược giả; không tự tick mua thêm; nút hủy rõ | Checklist rà soát giao diện |
| Riêng tư lộ trình & tài khoản | Ngày tương đối; kiểm quyền theo chủ sở hữu ở server | Test API: bài không chứa ngày/giá/mã; đổi ID để xem đơn người khác phải bị chặn; kiểm duyệt viên không xem được thanh toán |
| Ảnh có mặt người lạ / thông tin cá nhân | Phát hiện và đề xuất làm mờ; chặn đăng giấy tờ chưa làm mờ | Bộ 30 ảnh thử |
| Nội dung xấu | Sàng lọc 3 tầng (mục 4.2) | Bộ 100 bài/bình luận mẫu có gán nhãn, kể cả kiểu lách luật (không dấu, chèn ký tự, số điện thoại viết bằng chữ) |
| Dưới 18 tuổi | Kiểm ở server khi giữ chỗ và thanh toán | Test trên app, web, đặt thường, lộ trình, gọi thẳng API |

---

## 8. Cách đo thành công (mục 9) & kịch bản kiểm thử

**Môi trường đo chuẩn**:
- **Máy yếu**: Android 9–10, RAM 2–3GB. **Máy trung bình**: Android 12+, RAM 4–6GB.
- **Mạng yếu**: ~400 kbps, độ trễ ~400 ms, mất gói 2%. **4G**: ~10 Mbps.
- **Dữ liệu**: ≥ 500 bài mô phỏng (có nhãn), ≥ 5.000 tương tác.
- **Cách ghi**: mỗi phép đo ghi build, thiết bị, mạng, số lần lặp, p50 và p95.

| Mục thành công | Chỉ số & mục tiêu khởi điểm | Cách đo |
|---|---|---|
| **Bảng tin nhanh, mượt** | Thấy bài đầu tiên p95 ≤ **4 giây** (máy yếu, mạng yếu), ≤ 2 giây (máy TB, 4G); mở lại có cache ≤ 1 giây; khung hình giật ≤ 5%; **0 bài lặp** trong 300 bài; ≤ 3MB cho 50 bài đầu; RAM không tăng liên tục | Đo tự động 20 lần; công cụ đo khung hình; script cuộn so ID |
| **Xu hướng đúng & khó đẩy ảo** | Bài hot lên trong ≤ 15 phút; điểm giảm ≥ 50% sau 24h; **50 tài khoản ảo xếp dưới 10 người thật**; bấm 200 lần = 1 người; phát hiện đẩy ảo ≥ 90%, gắn cờ nhầm ≤ 5% | Thí nghiệm mô phỏng X1–X6 (dồn tương tác thật, hạ nhiệt, đẩy bằng nhiều tài khoản, bấm liên tục, tác giả tự đẩy, kiểm tra đúng gu) |
| **Tìm trong bài đăng ra đúng** | Precision@5 ≥ 0,8; ≥ 90% câu có kết quả đúng trong top 3; **nói thật khi không có ≥ 95%**; 0 kết quả vượt ngân sách; p95 ≤ 1,5 giây | Bộ ≥ 150 câu có gán nhãn: có dấu, không dấu, teencode, tiếng lóng, trộn tiếng Anh, emoji, theo ý không theo từ khóa, điều kiện kép, câu không có đáp án |
| **Trợ lý không bịa** | **100% gợi ý có ID hợp lệ và đang bán/hiển thị**; 100% giá khớp; trích dẫn đúng ≥ 95%; từ chối đúng ≥ 95%; hỏi lại khi thiếu ≥ 90% (không quá 1 câu/lượt); phản hồi đầu p95 ≤ 5 giây; báo cáo VNĐ/cuộc trò chuyện (mục tiêu khởi điểm ≤ 500đ) | Bộ ≥ 200 câu chạy tự động trước mỗi demo: gợi ý thường, thiếu thông tin, chỗ không tồn tại, giá/khuyến mãi, bài đã ẩn, ngoài phạm vi, nguy hiểm, prompt injection, hỏi thông tin người khác, dùng gu từ app, đăng nhập giữa chừng |
| **Từ bài đăng tới thanh toán xong** | Đặt theo lộ trình ≤ **6 chạm**, trung vị ≤ **60 giây**; đặt nhanh một chỗ ≤ 6 chạm, ≤ 45 giây; chưa đăng nhập thêm ≤ 3 thao tác và 0 lần phải chọn lại | Thử với ≥ 5 người ngoài đội (18–25 tuổi), quay màn hình, đếm chạm, bấm giờ |
| **Tương tác luôn đúng** | 200 tài khoản cùng thả trong 10 giây thì sai số **0**; bật/tắt 100 lần thì trạng thái cuối đúng; mạng chập chờn không đếm trùng; lan sang thiết bị khác p95 ≤ 2 giây; 50 bình luận đồng thời đủ và đúng thứ tự | Script tải đồng thời qua cả API của app và web |
| **Không trừ tiền hai lần, không bán trùng chỗ cuối** | **0 lần** trên mọi tình huống, mỗi tình huống chạy ≥ 20 lần | Danh sách P/R bên dưới |
| **Mọi sản phẩm cùng dữ liệu** | Đồng bộ app ↔ web ↔ admin p95 ≤ **3 giây**; ẩn bài biến khỏi app, web, tìm kiếm, trợ lý trong ≤ 60 giây (mục tiêu 10 giây khi đang mở); gợi ý web phản ánh hành vi trên app ≤ 5 phút | Ghi thời điểm ghi và thời điểm nhận ở hai nơi |

**Tình huống kiểm thử đặt chỗ & thanh toán**:

| # | Tình huống | Kết quả đúng |
|---|---|---|
| P1 | Bấm "Thanh toán" hai lần liên tiếp | Một giao dịch |
| P2 | Mất mạng ngay sau khi bấm, có mạng lại bấm tiếp | Một giao dịch, hiện đúng trạng thái |
| P3 | Cổng gửi xác nhận hai lần | Ghi nhận một lần |
| P4 | Cổng chậm phản hồi, người dùng thử lại | Một giao dịch |
| P5 | Cùng tài khoản thanh toán cùng đơn trên app và web | Một giao dịch; nơi còn lại báo "đã thanh toán" |
| P6 | Đã trả nhưng xác nhận chỗ lỗi | Tự hoàn tiền, A16 gắn cờ, người dùng được báo |
| P7 | Hết thời gian giữ chỗ khi đang ở trang thanh toán | Không cho trả, kiểm tra lại chỗ và giá |
| P8 | Giá đổi khi đang ở màn xác nhận | Báo chênh lệch, xác nhận lại |
| R1 | Hai máy cùng đặt phòng cuối | Một người được, người kia được báo ngay, không bị trừ tiền |
| R2 | 50 yêu cầu đồng thời tranh một chỗ | Đúng một yêu cầu thành công |
| R3 | Lộ trình 4 mục, mục 3 bị người khác lấy mất | Báo ngay; áp đúng chính sách đã chọn |
| R4 | Người dưới 18 tuổi gọi thẳng API tạo đơn | Bị từ chối |

**Lịch đo**:
- **Tuần 1**: trình bày cách đo này, dựng môi trường đo, bộ câu hỏi thử v1 (≥ 50 câu mỗi bộ).
- **Tuần 2**: **chốt** chỉ số; đo lần 1.
- **Tuần 3**: đo đầy đủ, thử với người ngoài đội.
- **Tuần 4**: báo cáo kết quả: đạt/chưa đạt, vì sao, và sản phẩm đã thay đổi gì nhờ đo.

---

## 9. Lựa chọn phần mở rộng (Phần B)

Tiêu chí chọn: phục vụ persona nào, tận dụng hạ tầng phần A đến đâu, rủi ro, có giúp chứng minh mục 9 không.

| Mã | Tính năng | Lý do | Quyết định |
|---|---|---|---|
| **B7** | Theo dõi trợ lý | Đề bài đã bắt đo chi phí trợ lý (mục 7) và chứng minh "không bịa" (mục 9); B7 chủ yếu là hiển thị dữ liệu đã có | **Chọn, ưu tiên 1** |
| **B1** | Tóm tắt "50 người đã đi nói gì" | Phục vụ Hà; dùng lại trợ lý, tìm kiếm và validator trích dẫn; tận dụng thế mạnh nội dung đã xác minh | **Chọn, ưu tiên 2** |
| **B4** | Dùng khi mất mạng | Củng cố yêu cầu khó nhất (mạng yếu); A1, A2, A10 đã cần cache | **Chọn, ưu tiên 3** |
| B6 | Thông báo tương tác | Phục vụ Linh; đã có hệ thống thông báo cho việc ẩn bài | Làm nếu còn thời gian |
| B2 | Tìm bằng hình ảnh | Hấp dẫn nhưng cần thêm mô hình ảnh, rủi ro cao trong 4 tuần | Hướng làm tiếp (mốc 4) |
| B3, B5 | Báo giảm giá, chia sẻ hình ảnh đẹp | Giá trị chứng minh mục 9 thấp; giá trong dữ liệu mô phỏng ít đổi nên khó demo | Không chọn |

---

## 10. Kế hoạch 4 mốc & phân vai

| Mốc | App | Web | Trợ lý | Admin | Đo & trả lời |
|---|---|---|---|---|---|
| **1** | Bảng tin v1 (xu hướng chung, cuộn không lặp), trang chi tiết | Tìm bằng lời v1, gợi ý v1, trang chi tiết | Thử 2–3 cách/mô hình trên ≥ 50 câu, chọn hướng | Đăng nhập, 2 vai trò | Hiểu bài toán (mục 1–3), cách làm & vì sao (mục 5), đề xuất cách đo (mục 8); **chốt mô hình dữ liệu & API (mục 6)** |
| **2** | Đăng bài (đã đi thật, lộ trình, nháp offline), tương tác, tìm trong bài, đặt nhanh | Xem & tương tác bài, đặt chỗ đầy đủ 4 loại, chặn dưới 18, gợi ý sau đặt | Gợi ý địa điểm có validator | Kiểm duyệt, đơn đặt chỗ | Câu hỏi mở về xu hướng, bài đăng, đặt chỗ (mục 4) **thể hiện trong sản phẩm**; chốt cách đo; đo lần 1 |
| **3** | Đặt theo lộ trình, Chuyến đi của tôi, B4 | Đặt theo lộ trình, Chuyến đi của tôi | Gợi ý bài đăng, chạy cả app và web, nhớ đa thiết bị, B1 | Theo dõi xu hướng & đẩy ảo, B7 | Câu hỏi mở về lộ trình và trợ lý; kết quả đo; vì sao chọn B7/B1/B4; **thứ Năm ngừng làm tính năng** |
| **4** | Báo cáo & trình bày | | | | Nếu có thêm thời gian: B2, B6, tối ưu chi phí trợ lý |

**Để giảm rủi ro A11 (khó nhất, rơi vào mốc 3)**: thiết kế lộ trình theo `day_offset` và cơ chế giữ chỗ nhiều mục **ngay từ mốc 1–2**, để mốc 3 chỉ còn ghép giao diện.

| Vai trò phụ trách chính | Phần việc |
|---|---|
| Backend: đặt chỗ & thanh toán | Catalog, tồn kho, giữ chỗ, báo giá, cổng thanh toán giả lập, đơn, A16 |
| Backend: nội dung & xu hướng | Bài đăng, media, tương tác, kiểm duyệt, xu hướng, cá nhân hóa, realtime |
| Android | A1–A5 và phần app của A10–A12, offline, hiệu năng máy yếu |
| Web & Admin | A6–A11 trên web, A13–A16 |
| Tìm kiếm & Trợ lý | Chuẩn hóa tiếng Việt, tìm kiếm lai, trợ lý, validator, đo chi phí, B1, B7 |
| QA & Đo lường (kiêm nhiệm) | Bộ test ở mục 8, môi trường đo, báo cáo |

Mỗi người phải trả lời được về phần của người khác: nên có buổi "giải thích chéo" 30 phút trước mỗi demo. **Đổi Leader sau mỗi buổi demo** (mục 11).

---

## 11. Rủi ro dự án

| Rủi ro | Mức | Giảm thiểu |
|---|---|---|
| Không có bài đăng nên bảng tin, xu hướng, tìm kiếm trống | Cao | Tuần 1 tạo bài mô phỏng có nhãn, gắn với đơn mô phỏng đã hoàn thành (G1) |
| A11 phức tạp nhất mà rơi vào mốc 3 | Cao | Làm mô hình dữ liệu và giữ chỗ nhiều mục từ sớm |
| Trợ lý bịa hoặc tốn chi phí | Cao | Validator, bộ test tự động từ mốc 1, giới hạn tần suất, đo từ lần gọi đầu |
| Phát hiện muộn lỗi hiệu năng trên máy yếu | TB | Có máy chuẩn từ tuần 1, đo hằng tuần |
| Các phần không khớp dữ liệu | TB | Đặc tả API và dữ liệu giả dùng chung từ tuần 1 |
| Yêu cầu thay đổi giữa chừng | TB | Module rõ ranh giới, ghi lại quyết định (decision log), hỏi sớm |
| Ảnh/video không có quyền sử dụng | TB | Chỉ dùng nguồn có giấy phép rõ, ghi nguồn |

---

## 12. Giả định & câu hỏi cho người hướng dẫn

**Giả định đang dùng** (đề bài yêu cầu ghi lại và nêu ở buổi gặp gần nhất):

| # | Giả định |
|---|---|
| G1 | Bài đăng mẫu được tạo từ tài khoản mô phỏng và đơn mô phỏng đã hoàn thành, **gắn nhãn "Mô phỏng" rõ ràng** |
| G2 | "Đã đi" = qua ngày trả phòng/ngày sử dụng và không hủy; admin có nút giả lập check-in để demo |
| G3 | Tuổi dựa trên ngày sinh khai khi đăng ký, khóa sửa, kiểm tra ở server |
| G4 | Vé máy bay và combo lấy từ bộ dữ liệu được cấp, không kết nối hãng thật |
| G5 | Admin chỉnh được giá để tạo tình huống "giá đổi" khi demo |
| G6 | Ảnh/video mẫu chỉ dùng nguồn có quyền sử dụng, có ghi nguồn |
| G7 | Trợ lý dùng dịch vụ mô hình ngôn ngữ bên ngoài, chi phí tính theo token quy ra VNĐ |

**Câu hỏi** (theo mẫu đề bài: tình huống → hai cách → nghiêng về cách nào, vì sao):
1. **Bài đăng mẫu**: *"Dữ liệu không có bài đăng nên bảng tin sẽ trống. Có hai cách: (A) tạo bài mô phỏng gắn nhãn rõ, gắn với đơn mô phỏng đã hoàn thành; (B) chỉ dùng bài đội tự đặt và tự đăng. Em nghiêng về kết hợp A + B, vì cần đủ dữ liệu để thấy xu hướng mà vẫn chứng minh được luồng thật. Nhãn 'mô phỏng' có cần hiện cho người dùng cuối không ạ?"*
2. **"Đã đi"**: *"Dữ liệu có trạng thái check-in không ạ? Nếu không, em đề xuất coi đơn đã hoàn thành khi qua ngày trả phòng, kèm nút admin giả lập check-in."*
3. **Tuổi**: *"Ngày sinh khai khi đăng ký, khóa sửa, kiểm tra ở server: mức này đủ chưa, hay cần mô phỏng thêm xác minh giấy tờ?"*
4. **Thanh toán giả lập**: *"Em định tự làm cổng giả lập có các tình huống thành công, thất bại, chậm, xác nhận trùng. Có cần giống một cổng cụ thể nào không ạ?"*
5. **Trợ lý**: *"Ban tổ chức có cấp khóa API hoặc ngân sách cho mô hình ngôn ngữ không, có trần chi phí không ạ?"*
6. **Combo**: *"Combo trong dữ liệu là gói giá cố định hay ghép từ các mục lẻ? Khi đặt theo lộ trình, 'dời theo ngày mới' áp dụng cho combo thế nào?"*
7. **Video**: *"Em đề xuất video ngắn ≤ 60 giây, ≤ 50MB trước nén, vì mạng yếu và máy yếu. Có được không ạ?"*
8. **Thiết bị chuẩn**: *"'Máy cấu hình thấp' cụ thể là máy nào? Em đề xuất Android 9–10, RAM 2–3GB."*

---

## 13. Checklist tự kiểm tra trước mỗi buổi demo

1. Bấm thanh toán hai lần, mất mạng ngay sau khi bấm, hai thiết bị cùng thanh toán → **không trừ tiền hai lần**.
2. Hai máy tranh chỗ cuối → đúng một người được, người kia biết ngay.
3. Admin đổi giá trong lúc user đang ở màn xác nhận → user được báo trước khi trả.
4. Chưa đăng nhập mà đặt / thả cảm xúc / bình luận / chat với trợ lý → đăng nhập xong **quay lại đúng chỗ**, không mất gì.
5. Đang viết bài hoặc bình luận thì bật chế độ máy bay → nội dung còn nguyên, có mạng lại gửi tiếp được.
6. Nhiều người cùng thả cảm xúc, một người bấm liên tục → con số đúng.
7. Thả trên web → app thấy ngay; lưu trên app → gợi ý trên web đổi theo.
8. Hỏi trợ lý về chỗ không tồn tại, hỏi giá, hỏi bài đã ẩn, thử prompt injection → **không bịa, không tự đặt**.
9. Hỏi ngoài phạm vi hoặc nội dung nguy hiểm → từ chối thân thiện.
10. Người dưới 18 tuổi bị chặn ở app, web, đặt thường, đặt theo lộ trình và khi gọi thẳng API.
11. Bài đăng không lộ ngày đi, số tiền đã trả, mã đặt chỗ, người đi cùng.
12. Ẩn bài → bài biến mất khỏi app, web, tìm kiếm và gợi ý của trợ lý.
13. Kiểm duyệt viên không xem được thông tin thanh toán, kể cả qua URL hay API.
14. Không có khan hiếm giả hay đếm ngược giả.
15. Tìm hoặc hỏi điều không có → nói thật và gợi ý nới điều kiện.
