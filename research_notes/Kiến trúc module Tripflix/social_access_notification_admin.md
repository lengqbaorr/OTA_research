# Mẫu kiến trúc từ các nền tảng mạng xã hội cho 3 module Tripflix: Access, Notification, Administration

> Phạm vi: Tripflix = app du lịch dạng video ngắn kiểu TikTok, gồm App Android + Web OTA + Admin panel dùng chung một backend REST/HTTPS (theo `so-do-module.png`: Business layer có Access, Notification, Administration, AI, Booking, Social, Payment, Search; Database layer = Database + Cache; có External APIs).
> Ràng buộc: modular monolith (PostgreSQL + Redis), demo sinh viên 4 tuần, người dùng trẻ Việt Nam, mạng yếu.
>
> **Ghi chú phương pháp (quan trọng cho người viết báo cáo):** Trong phiên này, WebFetch/curl bị chặn tới firebase.google.com, auth0.com, microservices.io, cheatsheetseries.owasp.org, discord.com (lỗi DNS/403 của proxy). Vì vậy:
> - OWASP Cheat Sheets được đọc **toàn văn** từ bản gốc trên GitHub (`raw.githubusercontent.com/OWASP/CheatSheetSeries`) — độ tin cậy cao.
> - Các nguồn còn lại (Firebase, Slack, Discord, LinkedIn, Reddit/PRAW, Meta, OpenAI, Perspective, Rekognition, Decree 147, DSA) chỉ được tiếp cận qua **snippet của WebSearch** — đã trích dẫn URL gốc, nhưng chưa đọc toàn trang. Những chỗ chỉ có nguồn thứ cấp được ghi rõ.
> - Các con số "biết từ training nhưng chưa kiểm chứng được trong phiên" được đưa vào mục **Gaps**, không đưa vào Cited Findings.
> - Mục **Inferences** chứa đề xuất thiết kế (endpoint, entity, event) do người nghiên cứu suy ra từ các finding — không phải trích dẫn.

---

## 1. Access: xác thực token cho mobile + web, OAuth/OTP, RBAC vs ABAC, kiểm tra quyền phía server, giữ "intent" sau đăng nhập, gộp phiên ẩn danh, rate limiting, giới hạn độ tuổi

### Takeaway
Mẫu chuẩn hiện nay là access token ngắn hạn + refresh token **xoay vòng (rotation) có phát hiện tái sử dụng (reuse detection)** theo "token family", OAuth Authorization Code + PKCE cho mọi loại client, web giữ token trong cookie `HttpOnly; Secure; SameSite` (không dùng localStorage), và kiểm tra quyền **deny-by-default trên mọi request** phía server. OWASP khuyến nghị ưu tiên ABAC/ReBAC hơn RBAC thuần — với Tripflix, cách thực dụng là **RBAC cho vai trò hệ thống (Member/Moderator/Admin) + thuộc tính (ABAC) cho tuổi, trạng thái "Traveled", và quan hệ sở hữu (ReBAC) cho nội dung**. Ở Việt Nam, Nghị định 147/2024 bắt buộc xác thực tài khoản MXH bằng số điện thoại VN và yêu cầu cha mẹ đăng ký tài khoản cho trẻ dưới 16 tuổi — ảnh hưởng trực tiếp đến luồng phone OTP và vai trò "Under-18 member".

### Cited Findings

**Token, refresh token rotation, lưu trữ token**
- Refresh token rotation: mỗi lần refresh cấp refresh token mới và vô hiệu hoá token cũ ngay lập tức; reuse detection giám sát việc dùng lại token đã dùng/đã vô hiệu — [Descope: refresh token rotation](https://descope.com/blog/post/refresh-token-rotation); [Auth0 docs: Refresh Token Rotation](https://auth0.com/docs/secure/tokens/refresh-tokens/refresh-token-rotation.md) (Auth0 là nguồn vendor chính, chỉ đọc qua snippet).
- Khi phát hiện tái sử dụng, cách phổ biến là thu hồi **toàn bộ token family** và buộc đăng nhập lại; hệ thống lưu bản ghi phía server liên kết các refresh token kế tiếp nhau thuộc cùng một phiên logic — [Turku AMK: Refresh Token Rotation and Reuse Detection](https://talkbystudents.turkuamk.fi/ohjelmistotuotanto-ja-interaktiiviset-teknologiat/strengthening-authentication-security-with-refresh-token-rotation-and-reuse-detection/); [Gravitee: OAuth refresh token rotation and replay detection](https://gravitee.io/corpus/gen-1403/oauth/oauth-refresh-token-rotation-and-replay-detection-strategies.html).
- Đánh đổi: reuse detection quá chặt có thể đăng xuất người dùng hợp lệ khi client **retry** request refresh (rất dễ xảy ra trên mạng yếu) — [DZone: Node.js refresh tokens](https://dzone.com/articles/nodejs-refresh-tokens) (nguồn thứ cấp).
- OWASP OAuth2: "Refresh tokens are sender-constrained (using DPoP or mTLS) **or use refresh token rotation** (issuing new refresh tokens and invalidating old ones immediately to detect replay attempts)" — [OWASP OAuth2 Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/OAuth2_Cheat_Sheet.md).
- OWASP OAuth2: Implicit Grant đã bị **deprecated** (RFC 9700) và bị loại khỏi OAuth 2.1; client **phải** dùng Authorization Code + PKCE cho mọi loại client, kể cả SPA và native app; PKCE ban đầu dành cho native app để chống tấn công chặn authorization code — [OWASP OAuth2 Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/OAuth2_Cheat_Sheet.md).
- OWASP OAuth2: quyền của access token nên giới hạn tối thiểu; access token nên **audience-restricted** (tốt nhất là một Resource Server), và Resource Server phải kiểm tra trên **mỗi request** rằng token được phép cho tài nguyên/hành động đó — [OWASP OAuth2 Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/OAuth2_Cheat_Sheet.md).
- OWASP Session Management: "Do not store authentication tokens, session IDs, JWTs, refresh tokens, or any credential in `localStorage` or `sessionStorage`... a single XSS vulnerability discloses every token. Use `HttpOnly; Secure; SameSite=Strict`" cookie; ví dụ `Set-Cookie: __Host-SessionID=<value>; Secure; HttpOnly; SameSite=Strict; Path=/` — [OWASP Session Management Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Session_Management_Cheat_Sheet.md).
- OWASP: `SameSite=Strict` loại trừ request cross-site, `Lax` cho phép điều hướng top-level với method an toàn; coi SameSite là lớp phòng thủ bổ sung chống CSRF (không thay thế CSRF token) — [OWASP Session Management Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Session_Management_Cheat_Sheet.md).
- OWASP: session ID **phải được tạo lại sau mọi thay đổi mức đặc quyền**, đặc biệt khi đăng nhập, để chống session fixation — [OWASP Session Management Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Session_Management_Cheat_Sheet.md). (Liên quan trực tiếp tới việc gộp phiên Guest → Member.)
- OWASP: idle timeout phổ biến 2–5 phút cho ứng dụng giá trị cao và 15–30 phút cho ứng dụng rủi ro thấp; mọi phiên nên có **absolute timeout**; có thể "renewal timeout" đổi ID giữa phiên — [OWASP Session Management Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Session_Management_Cheat_Sheet.md).
- OWASP: phiên có thể lưu theo kiểu "identifier + secret verifier split" — server lưu identifier công khai và hash SHA-256 của verifier, mỗi request tra theo identifier rồi so hash — [OWASP Session Management Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Session_Management_Cheat_Sheet.md). (Áp dụng tốt cho bảng `refresh_tokens`: chỉ lưu hash.)

**Phân quyền (RBAC / ABAC / ReBAC), kiểm tra phía server**
- OWASP Authorization: **Deny by Default**; "Permission should be validated correctly on every request, regardless of whether the request was initiated by an AJAX script, server-side, or any other source"; cơ chế kiểm tra nên cấu hình global, toàn ứng dụng thay vì gắn từng chỗ — [OWASP Authorization Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authorization_Cheat_Sheet.md).
- OWASP Authorization: "Prefer Attribute and Relationship Based Access Control over RBAC". ABAC quyết định dựa trên thuộc tính của subject, object, môi trường; ReBAC dựa trên quan hệ (ví dụ "chỉ người tạo bài viết được sửa"), "especially necessary in social network applications, like Twitter or Facebook" — [OWASP Authorization Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authorization_Cheat_Sheet.md).
- OWASP: rủi ro RBAC gồm "role explosion" và khó hỗ trợ kiểm tra cấp đối tượng; tuy nhiên "the initial setup for RBAC is often simpler than ABAC", vài vai trò như User/Admin có thể đủ lúc đầu — [OWASP Authorization Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authorization_Cheat_Sheet.md).
- OWASP: ví dụ IDOR — đổi tham số `acct_id` trên URL để truy cập dữ liệu của người khác; cần kiểm tra quyền sở hữu đối tượng — [OWASP Authorization Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authorization_Cheat_Sheet.md).

**Quay lại màn hình trước khi đăng nhập (return URL) — an toàn redirect**
- OWASP: redirect không kiểm tra cho phép phishing (link trông giống site thật) và có thể vượt kiểm tra truy cập; cách phòng: ưu tiên đích nội bộ, nếu cần đích ngoài thì **allow-list** dựa trên URL đã parse (scheme, host chuẩn hoá, port), từ chối userinfo/đầu vào mơ hồ — [OWASP Unvalidated Redirects and Forwards Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.md).

**Rate limiting / chống brute force / enumeration**
- OWASP: account lockout là biện pháp phổ biến; bộ đếm đăng nhập sai nên gắn với **tài khoản** chứ không phải IP (để chống tấn công từ nhiều IP); cẩn thận lockout bị lợi dụng để DoS người dùng khác; ghi log mọi lần lockout — [OWASP Authentication Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authentication_Cheat_Sheet.md).
- OWASP: login/đặt lại mật khẩu/khôi phục phải trả **thông báo lỗi chung** để chống user enumeration (áp dụng cả cho đăng ký); CAPTCHA chỉ nên bật sau vài lần thất bại để thân thiện hơn — [OWASP Authentication Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authentication_Cheat_Sheet.md).
- OWASP: dùng device fingerprint, IP, cookie "remembered device" làm **tín hiệu rủi ro**, không thay thế xác thực (theo NIST 800-63-4) — [OWASP Authentication Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Authentication_Cheat_Sheet.md).

**Age gating và pháp lý Việt Nam**
- Nghị định 147/2024/NĐ-CP ban hành 9/11/2024, hiệu lực 25/12/2024; nền tảng phải xác thực tài khoản bằng **số điện thoại di động Việt Nam**; nếu không có thì xác thực bằng số định danh cá nhân theo luật định danh điện tử — [Văn bản tiếng Anh NĐ 147/2024 (QTSC)](https://www.qtsc.com.vn/uploads/files/2024/12/30/147_2024_ND-CP_636187-eng.pdf); [Vietnam Law Magazine](https://vietnamlawmagazine.vn/social-network-user-accounts-to-be-verified-with-subscriber-phone-numbers-73028.html); [Tilleke & Gibbins](https://www.tilleke.com/insights/a-closer-look-at-vietnams-decree-147-on-internet-services-and-online-information/).
- Trẻ **dưới 16 tuổi**: cha mẹ/người giám hộ phải đăng ký tài khoản bằng thông tin của mình (họ tên, ngày sinh, SĐT VN hoặc số định danh) và chịu trách nhiệm giám sát nội dung trẻ truy cập/đăng/chia sẻ — [VietnamNet](https://vietnamnet.vn/en/new-vietnam-law-mandates-parental-registration-for-child-online-accounts-2531595.html); [Tilleke & Gibbins](https://www.tilleke.com/insights/a-closer-look-at-vietnams-decree-147-on-internet-services-and-online-information/).
- Lexology mô tả: chỉ tài khoản đã xác thực bằng SĐT/số định danh mới được **đăng bài, bình luận, livestream** — [Lexology](https://www.lexology.com/library/detail.aspx?g=fa4ac0a7-19fe-4269-b9cd-5de9ccc9a19d); phạm vi hạn chế này được các nguồn khác mô tả hẹp hơn (mâu thuẫn, cần đối chiếu văn bản gốc).
- Có thông tin về dự thảo sửa đổi NĐ 147 (cuối 7/2026) siết quy định tài khoản trẻ dưới 16, và Luật An ninh mạng mở rộng đăng ký của phụ huynh sang dịch vụ trực tuyến khác — [atmedia](https://atmedia.uk/en/news/vietnam-decree-147-social-media-phone-verification); [VietnamNet](https://vietnamnet.vn/en/new-vietnam-law-mandates-parental-registration-for-child-online-accounts-2531595.html) — **chưa xác nhận đã được thông qua**.

### Inferences

**Thiết kế token cho Android + Web + Admin (một backend)**
- Access token: JWT ký RS256/EdDSA, TTL ~10–15 phút, claim tối thiểu: `sub`, `sid` (session/family id), `roles` (["member","moderator"]), `age_band` ("u16"|"16_17"|"adult"), `phone_verified`, `aud` ("tripflix-api"), `ver` (perm_version). Không nhét nhiều quyền chi tiết vào JWT (tránh "role explosion" trong header theo OWASP).
- Refresh token: chuỗi ngẫu nhiên opaque (không phải JWT), lưu **hash** trong PostgreSQL; rotation + reuse detection theo `family_id`. Để chịu được retry trên mạng yếu: cho **grace window ~10–30 giây** — nếu token cũ được dùng lại trong cửa sổ đó *và* token mới chưa từng được dùng, trả lại cùng cặp token đã cấp (idempotent) thay vì thu hồi cả family. Đây là cách giảm đúng rủi ro mà DZone nêu.
- Android: lưu refresh token trong EncryptedSharedPreferences/Keystore; gửi `Authorization: Bearer`. Web OTA: refresh token trong cookie `__Host-rt; HttpOnly; Secure; SameSite=Lax; Path=/api/v1/auth/refresh`; access token chỉ giữ trong bộ nhớ JS. Admin panel: tên miền/phân hệ riêng, `SameSite=Strict`, TTL ngắn hơn, bắt buộc 2FA (TOTP) cho Moderator/Admin.
- Thu hồi tức thời (ban user, đổi vai trò): Redis key `auth:revoked_sid:{sid}` (TTL = TTL access token) + `user:perm_version:{uid}`; middleware so `ver` trong JWT với Redis → lệch thì trả 401 `token_stale` để client refresh. Đây là cách rẻ thay cho denylist toàn cục.

**Mô hình quyền đề xuất (RBAC + ABAC + ReBAC "nhẹ")**
- Vai trò (RBAC, bảng `roles`, `user_roles`): `guest` (ngầm định, không có bản ghi), `member`, `moderator`, `admin`. "Under-18" và "Traveled" **không nên là role** mà là **thuộc tính** (ABAC): `users.birth_date` → `age_band`; `user_badges(type='traveled', source_booking_id, verified_at)` sinh ra khi Booking module phát event `booking.completed`/`trip.checked_out`.
- Bảng `permissions` dạng `resource:action` (ví dụ `video:create`, `comment:create`, `review:create`, `booking:create`, `report:create`, `mod_case:resolve`, `user:ban`, `audit_log:read`). Hàm duy nhất `authorize(principal, action, resource?)` gọi từ một middleware/guard toàn cục; mặc định deny.
- Ví dụ chính sách:
  - `review:create` ⇐ role member ∧ có badge `traveled` cho đúng `destination_id/hotel_id` (đánh giá "đã đi thật" — chống review giả).
  - `video:create`, `comment:create`, `livestream:*` ⇐ member ∧ `phone_verified = true` (theo NĐ 147).
  - Người dùng `age_band='u16'` ⇐ chỉ khi có `guardian_consents` hợp lệ; mặc định tài khoản riêng tư, tắt DM từ người lạ, ẩn khỏi gợi ý, cấm `booking:create`/`payment:*` nếu không có phụ huynh (suy luận theo tinh thần NĐ 147; cần tư vấn pháp lý).
  - `video:update|delete` ⇐ `resource.owner_id == principal.id` (ReBAC) ∨ role moderator/admin.
- Đơn giản hoá cho demo 4 tuần: hard-code ma trận `role → permissions` trong code + 2–3 hàm điều kiện ABAC; không cần OPA/Casbin/Keycloak. Ghi rõ trong báo cáo là "RBAC với policy hook ABAC".

**Luồng xác thực**
- Google: Android dùng Credential Manager / Google Sign-In lấy **ID token**, gửi lên `POST /api/v1/auth/oauth/google {id_token}`; backend verify chữ ký/`aud`/`iss`/`exp` rồi phát token Tripflix. Web dùng Authorization Code + PKCE (theo OWASP; không dùng implicit).
- Phone OTP: `POST /auth/otp/request {phone, purpose}` → OTP 6 số, hash lưu Redis TTL 5 phút, tối đa 5 lần thử; `POST /auth/otp/verify {phone, code, device_id}`. Rate limit theo **phone + IP + device**. Với demo: dùng nhà cung cấp SMS dev/sandbox hoặc Firebase Phone Auth (rẻ, hỗ trợ VN) — cân nhắc chi phí SMS.
- Trả lỗi chung "Mã không hợp lệ hoặc đã hết hạn" (anti-enumeration).

**Intent preservation ("quay lại màn hình đang xem")**
- Khi Guest bấm Like/Comment/Book: client lưu `pending_intent = {action:"like", target:"video:123", return_to:"/v/123?t=37"}` (Android: SavedStateHandle/DataStore; web: sessionStorage — *không chứa credential nên OWASP không cấm*). Sau login, client **tự thực hiện lại** hành động (idempotent nhờ `Idempotency-Key`) và điều hướng `return_to`.
- Nếu dùng server redirect (OAuth web): tham số `state` mang `return_to`, server chỉ chấp nhận **đường dẫn tương đối** khớp allow-list (`^/(v|place|hotel|booking)/…`) theo OWASP Unvalidated Redirects. Android: App Links `https://tripflix.vn/v/{id}` để deep link cùng URL với web.

**Gộp phiên ẩn danh (anonymous → account)**
- Guest nhận `anon_id` (UUID, cookie/thiết bị) và có thể có `guest token` ngắn hạn với scope hẹp (xem feed, lưu wishlist tạm). Các bảng hành vi (`watch_events`, `wishlist_items`, `search_history`, giỏ booking tạm) có cột `actor_anon_id` nullable.
- Khi đăng nhập: `POST /auth/merge-anonymous {anon_id}` (hoặc tự động trong `/auth/login` nếu header `X-Anon-Id`) → trong 1 transaction: `UPDATE ... SET user_id=:uid WHERE actor_anon_id=:anon AND user_id IS NULL`, xử lý trùng (wishlist: `ON CONFLICT DO NOTHING`), rồi **cấp session mới** (đúng nguyên tắc OWASP regenerate session sau khi đổi đặc quyền). Phát event `user.anonymous_merged`.

**Rate limiting (Redis)**
- Token bucket/sliding window bằng Redis `INCR`+`EXPIRE` hoặc Lua: key `rl:{scope}:{id}:{window}`. Mức gợi ý cho demo: login 5/phút/tài khoản + 20/phút/IP; OTP request 1/60s & 5/giờ/phone; comment 10/phút/user; report 20/giờ/user; upload video 10/ngày/user. Trả `429` + `Retry-After`. Lockout gắn với tài khoản (OWASP), có đường thoát qua OTP.

**REST endpoints module Access (đề xuất)**
```
POST   /api/v1/auth/otp/request            {phone, purpose: login|verify|guardian}
POST   /api/v1/auth/otp/verify             {phone, code, device_id, anon_id?} -> {access_token, refresh_token?, user}
POST   /api/v1/auth/oauth/google           {id_token | code+code_verifier, anon_id?}
POST   /api/v1/auth/refresh                (cookie hoặc body {refresh_token}) -> rotate
POST   /api/v1/auth/logout                 (thu hồi family hiện tại)
POST   /api/v1/auth/logout-all
GET    /api/v1/auth/sessions               (danh sách thiết bị) ; DELETE /api/v1/auth/sessions/{sid}
GET    /api/v1/me                          -> {user, roles, permissions[], age_band, badges[]}
PATCH  /api/v1/me/profile
POST   /api/v1/me/age-verification         {birth_date} ; POST /api/v1/me/guardian-consent {guardian_phone, otp}
POST   /api/v1/auth/merge-anonymous        {anon_id}
# Admin
GET    /api/v1/admin/users?q=&role=&status=
PUT    /api/v1/admin/users/{id}/roles      {roles[], reason}
POST   /api/v1/admin/users/{id}/suspend    {reason_code, note, until}
```
- `GET /me` trả kèm `permissions[]` để client ẩn/hiện nút — nhưng **server vẫn kiểm tra mọi request** (OWASP).

**Entity (PostgreSQL)**
- `users(id, phone_e164 UNIQUE, phone_verified_at, email, google_sub UNIQUE, display_name, birth_date, status[active|suspended|banned|deleted], perm_version, created_at)`
- `user_roles(user_id, role, granted_by, granted_at)`; `user_badges(user_id, type, ref_id, verified_at)`
- `auth_sessions(id=sid, user_id, device_id, platform[android|web|admin], user_agent, ip, created_at, last_seen_at, absolute_expires_at, revoked_at, revoke_reason)`
- `refresh_tokens(id, session_id, token_hash UNIQUE, parent_id, issued_at, expires_at, used_at, replaced_by)`
- `guardian_links(child_user_id, guardian_phone, guardian_name, verified_at)`; `anonymous_identities(anon_id, first_seen, merged_user_id, merged_at)`
- Events: `user.registered`, `user.phone_verified`, `user.role_changed`, `user.suspended`, `session.revoked`, `auth.refresh_reuse_detected`, `user.anonymous_merged`, `user.badge_granted`.

### Gaps
- Không đọc được toàn trang Auth0 (bị chặn) — các thông số cụ thể của Auth0 như "reuse interval/leeway" mặc định và inactivity/absolute lifetime **chưa được xác minh**; con số grace window 10–30 giây ở trên là đề xuất, không phải trích dẫn.
- OWASP JWT Cheat Sheet (bản Java) không tải được (sai đường dẫn 404) nên chưa trích các khuyến nghị "token sidejacking fingerprint", "alg none".
- Chưa tìm được tài liệu chính thức về cách Instagram/TikTok xử lý "return to screen after login" hay gộp phiên ẩn danh — phần này là suy luận thiết kế.
- Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân (đồng ý của cha mẹ cho dữ liệu trẻ em) chưa được tìm kiếm trong phiên này — cần bổ sung cho age gating.
- Chưa xác minh chi tiết Keycloak/Auth0 RBAC API (fine-grained authorization) do bị chặn truy cập.

---

## 2. Notification & realtime: WebSocket vs SSE vs polling, pub/sub fan-out, FCM, outbox, preferences & dedup

### Takeaway
Các nền tảng lớn dùng **kết nối bền (persistent connection)** cho realtime: Slack và Discord dùng WebSocket qua lớp gateway tách biệt khỏi lớp giữ trạng thái kênh; LinkedIn dùng **SSE** (HTTP thường, server đẩy một chiều) cho tin nhắn/typing/read receipts. Khi app ở nền, kênh là **FCM** (data message priority normal cho đồng bộ, high chỉ cho tin khẩn). Độ tin cậy đến từ **transactional outbox** (ghi event cùng transaction nghiệp vụ, relay at-least-once, consumer idempotent) — còn Redis Pub/Sub chỉ là at-most-once nên chỉ dùng để "đánh thức" kết nối, không phải nguồn sự thật. Với Tripflix (sync web ↔ app trong vài giây, mạng yếu): **SSE + Redis Pub/Sub + outbox trên PostgreSQL + FCM**, fallback polling `GET /sync?since=cursor`.

### Cited Findings

**Kiến trúc realtime tại các công ty**
- Slack: mỗi client có **một kết nối WebSocket bền** để nhận event realtime; 4 dịch vụ Java lõi: Channel Servers (giữ dữ liệu kênh trong bộ nhớ), Gateway Servers (đứng giữa client và Channel Servers), Admin Servers, Presence Servers — [Slack Engineering: Real-time Messaging](https://slack.engineering/real-time-messaging/).
- Slack: Gateway Servers triển khai ở nhiều vùng địa lý để client kết nối tới vùng gần nhất; sau khi kết nối, Gateway subscribe vào các Channel Server chứa kênh của user; dùng **consistent hashing** để chia tải Channel Server; Presence Servers giữ trạng thái online (chấm xanh) trong bộ nhớ — [Slack Engineering: Real-time Messaging](https://slack.engineering/real-time-messaging/).
- Slack được mô tả là giao tin nhắn toàn cầu trong khoảng ~500 ms cho hàng chục triệu client — [Slack Engineering: Real-time Messaging](https://slack.engineering/real-time-messaging/) (con số qua snippet, bài viết ~2023, có thể đã cũ).
- Discord: mỗi guild (server) chạy như **một process Elixir độc lập**; guild lớn hàng triệu thành viên khó theo kịp hoạt động — [Discord blog: Tracing Discord's Elixir Systems](https://discord.com/blog/tracing-discords-elixir-systems-without-melting-everything).
- Discord (2020): >12 triệu người dùng đồng thời, >26 triệu WebSocket event/giây gửi tới client, cụm 400–500 máy Elixir cho chat; dùng Rustler nhúng cấu trúc dữ liệu Rust vào Elixir — [Elixir blog: Real time communication at scale with Elixir at Discord](https://elixir-lang.org/blog/2020/10/08/real-time-communication-at-scale-with-elixir-at-discord/).
- Discord dùng consistent hash ring để đặt guild, thư viện Manifold để phân phối event giữa node; chỉ bật "relay" cho guild lớn, relay giữ kết nối session thay mặt guild — [Indie Hackers (tóm tắt thứ cấp)](https://www.indiehackers.com/post/how-discord-scaled-elixir-to-5-million-concurrent-users-three-bottlenecks-and-how-they-fixed-them-1c268b82b2); [ByteByteGo (thứ cấp)](https://blog.bytebytego.com/p/how-discord-serves-15-million-users). Con số "mỗi gateway giữ 500k vs 1 triệu kết nối" **mâu thuẫn giữa các nguồn thứ cấp** và chưa xác minh.
- LinkedIn (2016): dùng **Server-Sent Events** — client mở kết nối HTTP bình thường và server đẩy luồng dữ liệu liên tục khi có sự kiện — cho tin nhắn, typing indicator, read receipt; dựng trên Play Framework (hỗ trợ sẵn EventSource và WebSocket) với **một Akka actor cho mỗi kết nối**, đạt hàng trăm nghìn kết nối bền trên một máy — [LinkedIn Engineering: Instant Messaging at LinkedIn](https://engineering.linkedin.com/blog/2016/10/instant-messaging-at-linkedin--scaling-to-hundreds-of-thousands-); [InfoQ podcast](https://www.infoq.com/podcasts/linkedin-realtime-messaging-architecture).
- LinkedIn (2018) tái sử dụng stack Play + Akka cho nền tảng Presence theo dõi trạng thái online của hàng triệu thành viên — [LinkedIn Engineering: Real-Time Presence Platform](https://engineering.linkedin.com/blog/2018/01/now-you-see-me--now-you-dont--linkedins-real-time-presence-platf).

**Redis Pub/Sub vs Streams**
- Redis Pub/Sub không lưu trữ; subscriber không nhận lại tin bị lỡ khi không nghe (at-most-once). Streams + consumer groups dùng `XACK` và pending list → at-least-once, consumer phải xử lý trùng — [OneUptime: Redis Pub/Sub vs Streams](https://oneuptime.com/blog/post/2026-03-31-redis-when-to-use-redis-pubsub-vs-redis-streams/markdown); [Leapcell](https://leapcell.io/blog/redis-messaging-showdown-pub-sub-vs-streams-for-event-driven-architectures). GeeksforGeeks khẳng định consumer group cho "exactly-once" — **mâu thuẫn** với các nguồn khác, không nên tin ([GfG](https://www.geeksforgeeks.org/?p=1062333)). Chưa đối chiếu với redis.io.

**Transactional outbox**
- Polling publisher lấy batch outbox bằng `SELECT ... FOR UPDATE SKIP LOCKED` để nhiều relay chạy song song không chặn nhau; batch khuyến nghị ~50–200 dòng; tránh giữ lock/transaction mở trong lúc chờ broker; phương án thay thế là "lease" (owner + expiry) để phục hồi khi relay chết — [OneUptime: Outbox pattern implementation](https://oneuptime.com/blog/post/2026-01-30-outbox-pattern-implementation/view); [OneUptime: Outbox at-least-once & duplicates](https://oneuptime.com/blog/post/2026-07-22-transactional-outbox-duplicate-events/view); [DEV: Outbox in Go with PostgreSQL](https://dev.to/rosgluk/transactional-outbox-pattern-in-go-with-postgresql-59an).
- Trùng lặp là tất yếu (relay crash sau khi publish nhưng trước khi đánh dấu xong) → consumer phải idempotent; mẫu "inbox": consumer insert `(consumer_name, event_id)` dưới unique constraint trong **cùng transaction** với cập nhật nghiệp vụ — [OneUptime: Outbox duplicates](https://oneuptime.com/blog/post/2026-07-22-transactional-outbox-duplicate-events/view).
- Polling đơn giản, không mất event khi publisher restart, nhưng tốn query; CDC (Debezium đọc WAL) tránh overhead polling nhưng cũng không đem lại exactly-once cho hiệu ứng downstream — [OneUptime: Outbox pattern implementation](https://oneuptime.com/blog/post/2026-01-30-outbox-pattern-implementation/view). (Trang gốc microservices.io bị chặn trong phiên.)

**Firebase Cloud Messaging (FCM)**
- Mặc định: **notification message → priority high; data message → priority normal**; có thể đặt high/normal cho data message — [FCM HTTP protocol reference](https://firebase.google.com/docs/cloud-messaging/http-server-ref); [FCM REST v1 projects.messages](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages); [Android Developers Blog: Notifying your users with FCM](https://android-developers.googleblog.com/2018/09/notifying-your-users-with-fcm.html).
- High priority cho phép FCM **đánh thức thiết bị đang ngủ** và mở kết nối mạng; ở **Doze**, message normal bị hoãn tới maintenance window; Google khuyên chỉ dùng high cho tin cần gấp vì tốn pin hơn — [FCM REST v1 reference](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages); [Android Developers Blog](https://android-developers.googleblog.com/2018/09/notifying-your-users-with-fcm.html).
- Khi app ở nền, payload notification vào khay thông báo; data payload chỉ được xử lý khi người dùng bấm thông báo — [FCM: About messages](https://firebase.google.com/docs/cloud-messaging/concept-options.html).
- Tin "collapsible": Android dùng `collapse_key`, Apple dùng `apns-collapse-id`, Web dùng `Topic` — [FCM: About messages](https://firebase.google.com/docs/cloud-messaging/concept-options.html); [FCM REST v1](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages).
- HTTP v1 gửi tới topic bằng `topic` hoặc `condition` (ví dụ `"'foo' in topics && 'bar' in topics"`); legacy API cho phép target là registration token, device group, hoặc topic `/topics/...` — [FCM REST v1](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages); [FCM HTTP legacy reference](https://firebase.google.com/docs/cloud-messaging/http-server-ref).
- FCM trả message ID **không có nghĩa** là thiết bị đã nhận — [FCM HTTP reference](https://firebase.google.com/docs/cloud-messaging/http-server-ref).
- FCM v1 có thể dùng qua Amazon SNS với payload v1 — [AWS SNS FCM v1 payloads](https://docs.aws.amazon.com/sns/latest/dg/sns-fcm-v1-payloads.html).

### Inferences

**Chọn kênh realtime cho Tripflix (modular monolith)**
| Kênh | Dùng cho | Lý do |
|---|---|---|
| **SSE** `GET /api/v1/realtime/stream` | Web OTA + Admin panel (và app khi foreground) | Một chiều server→client là đủ (client gửi qua REST); chạy trên HTTP/1.1/2, qua proxy/CDN dễ; trình duyệt tự reconnect và gửi `Last-Event-ID` → khớp hoàn hảo với cursor resume trên mạng yếu. LinkedIn chứng minh SSE scale tốt. |
| WebSocket | Chỉ khi cần 2 chiều tần suất cao (chat live, livestream comment) | Phức tạp hơn (heartbeat, reconnect tự viết); để giai đoạn sau. |
| **FCM data message** | App Android ở nền/đã đóng | Không giữ kết nối khi app nền (pin). |
| **Polling** `GET /api/v1/sync?cursor=` | Fallback + "catch-up" sau khi reconnect | Bảo đảm không mất event vì Redis Pub/Sub là at-most-once. |

- Nguyên tắc "push là gợi ý, pull là sự thật": SSE/FCM chỉ mang `{type, id, cursor}` + payload nhỏ; client luôn có thể gọi `/sync` để lấy chính xác. Giống mô hình Slack/Discord tách gateway (kết nối) khỏi trạng thái.
- Luồng "like trên web → app cập nhật trong vài giây":
  1. `POST /videos/{id}/likes` → transaction: insert `likes` + insert `outbox_events(type='video.liked')` + insert `user_sync_log(user_id, cursor=seq, entity, op)`.
  2. Relay (worker trong cùng monolith, `@Scheduled` mỗi 200–500 ms hoặc `LISTEN/NOTIFY` để đánh thức) đọc outbox `FOR UPDATE SKIP LOCKED`, `PUBLISH rt:user:{uid}` lên Redis, đánh dấu `published_at`.
  3. Mọi instance API subscribe Redis pattern `rt:user:*`, đẩy tới các kết nối SSE của user đó (map `user_id → connections` trong bộ nhớ — tương tự Gateway Server của Slack).
  4. Nếu user không có kết nối SSE nào đang mở trên thiết bị Android → Notification module gửi FCM data message (`priority: normal`, `collapse_key: "sync"`) để app gọi `/sync` khi thức dậy.
- Dedup phía client: mỗi event có `event_id` (UUID) và `cursor` tăng đơn điệu theo user; client lưu `last_cursor`, bỏ event `cursor <= last_cursor`.

**Notification (thông báo người dùng)**
- Phân loại để quyết định kênh và priority:
  - Giao dịch/khẩn (booking xác nhận, thanh toán thất bại, check-in sắp tới, kết quả kháng nghị): FCM **notification** hoặc data high, luôn gửi, không gộp.
  - Xã hội (like, comment, follow): **gộp** (aggregation) "Lan và 12 người khác đã thích video" — key gộp `(recipient, type, target_id)` trong cửa sổ 10–30 phút; dùng `collapse_key`/tag Android để thay thế thông báo cũ thay vì xếp chồng.
  - Marketing/trending: **FCM topic** (`trend_hanoi`, `deals_vn`) — rẻ, không cần lưu token per user; tôn trọng opt-out và giờ yên lặng.
- Ưu tiên token-based cho thông báo cá nhân (cần preferences, age gating, dedup), topic-based cho broadcast.
- Thiết bị Under-16: tắt thông báo marketing mặc định; không gửi thông báo từ người lạ.
- Dedup: `notifications.dedup_key UNIQUE (recipient_id, dedup_key)` ví dụ `like:{video}:{actor}` → like/unlike/like lại không spam.
- Preferences: bảng `notification_preferences(user_id, category, channel[push|in_app|email], enabled, quiet_hours)`; Notification module tra trước khi gửi.

**REST endpoints module Notification (đề xuất)**
```
POST   /api/v1/devices                      {fcm_token, platform, app_version, locale}   (upsert theo token)
DELETE /api/v1/devices/{token}
GET    /api/v1/notifications?cursor=&limit=20   -> in-app inbox
POST   /api/v1/notifications/read           {ids[] | up_to_cursor}
GET    /api/v1/notifications/unread-count
GET    /api/v1/notification-preferences ; PUT /api/v1/notification-preferences
GET    /api/v1/realtime/stream              (SSE; Last-Event-ID; heartbeat ":\n\n" mỗi 15–25s)
GET    /api/v1/sync?cursor=                 -> {events[], next_cursor, has_more}
# Admin
POST   /api/v1/admin/broadcasts             {topic|segment, title, body, deeplink, schedule_at}
```

**Entity**
- `outbox_events(id BIGSERIAL, event_id UUID, aggregate_type, aggregate_id, type, payload JSONB, created_at, published_at NULL, attempts)` + index `WHERE published_at IS NULL`.
- `processed_events(consumer, event_id, processed_at, PRIMARY KEY(consumer,event_id))` (inbox/idempotent consumer).
- `notifications(id, recipient_id, type, actor_ids[], target_type, target_id, dedup_key, group_key, title, body, deeplink, priority, created_at, read_at, delivered_push_at)`.
- `devices(id, user_id, fcm_token UNIQUE, platform, last_seen_at, invalid_at)` — khi FCM trả lỗi token không hợp lệ (UNREGISTERED) thì đánh dấu `invalid_at`.
- `user_sync_log(user_id, seq BIGSERIAL, entity_type, entity_id, op, created_at)` cho `/sync`.
- Events tiêu thụ: `video.liked`, `comment.created`, `user.followed`, `booking.confirmed`, `payment.failed`, `mod.action_taken`, `appeal.resolved`, `trend.alert`.

**Đơn giản hoá cho demo 4 tuần**
- Không cần Kafka: outbox + worker trong cùng process Spring/NestJS + Redis Pub/Sub. Một instance API cũng chạy được (Redis Pub/Sub vẫn hữu ích khi scale 2 instance để demo).
- Có thể bỏ SSE trên Android, chỉ dùng FCM + `/sync` khi app resume; SSE cho web + admin.
- Nếu thiếu thời gian: polling 5–10 giây trên web vẫn đáp ứng "vài giây" — nhưng nêu rõ là trade-off.

### Gaps
- firebase.google.com bị chặn: chưa xác minh từ trang gốc các con số thường được biết như giới hạn payload (~4 KB), tối đa 4 collapse key đồng thời trên Android, cơ chế Android **hạ cấp** priority high nếu app không hiển thị thông báo, độ trễ fan-out của topic, TTL mặc định (4 tuần), và quyền `POST_NOTIFICATIONS` (Android 13+). Cần kiểm tra trước khi đưa vào báo cáo.
- Chưa đọc trực tiếp Discord Gateway docs (opcodes Hello/Heartbeat/Identify/Resume, sequence number) — discord.com bị chặn; đây là mẫu tham chiếu tốt cho "resume bằng sequence" nhưng chưa có trích dẫn.
- Chưa có nguồn chính thức redis.io cho ngữ nghĩa Pub/Sub/Streams; chưa có nguồn chính thức microservices.io cho outbox (bị chặn).
- Không tìm thấy nguồn công khai về kiến trúc realtime/notification của TikTok hay Instagram trong phiên này.

---

## 3. Fan-out & counters: fan-out-on-write vs read, bộ đếm like/comment nhất quán, ghi idempotent

### Takeaway
Twitter dùng mô hình **lai**: fan-out-on-write (đẩy ID tweet vào timeline dựng sẵn trong Redis) cho tài khoản thường, và **fan-out-on-read** cho tài khoản rất nhiều follower (celebrity), trộn lúc đọc. Với Tripflix — feed kiểu TikTok chủ yếu là **"For You" do recommendation**, không phải timeline theo follow — nên fan-out-on-read (query + cache) là đủ; fan-out-on-write chỉ có ý nghĩa cho tab "Following" và cho notification. Bộ đếm nên là **bảng nguồn sự thật + counter denormalized cập nhật bất đồng bộ/Redis**, ghi idempotent bằng unique constraint và `Idempotency-Key`.

### Cited Findings
- Mô hình phổ biến (theo nhiều nguồn thứ cấp dẫn talk "Timelines at Scale" của Raffi Krikorian, Twitter): tài khoản thường fan-out-on-write vào feed dựng sẵn (Redis), tài khoản nhiều follower được kéo lúc đọc rồi merge và rank; chi phí chính là merge lúc đọc — [TechInterview.org: Twitter news feed](https://www.techinterview.org/post/3233474168/system-design-twitter-news-feed-timeline-fanout-on-write-fanout-on-read-celebrity-problem-ranking-caching/); [DEV: Twitter's fanout strategy at scale](https://dev.to/gabrielanhaia/twitters-fanout-strategy-at-scale-the-trade-off-most-designs-miss-55oa); [Milan Jovanović: How do you build a social media feed](https://milanjovanovic.tech/blog/how-do-you-build-a-social-media-feed-system-design).
- Ngưỡng "celebrity" khác nhau giữa các nguồn (~10K, 100K hay 1M follower) → chỉ mang tính minh hoạ — [TechInterview.org](https://www.techinterview.org/post/3233474168/system-design-twitter-news-feed-timeline-fanout-on-write-fanout-on-read-celebrity-problem-ranking-caching/); [Software Design 101: Twitter Timeline](https://stackshala.medium.com/software-design-101-twitter-timeline-5912d31afada).
- Consumer idempotent bằng unique `(consumer, event_id)` trong cùng transaction với cập nhật nghiệp vụ — [OneUptime: Outbox duplicates](https://oneuptime.com/blog/post/2026-07-22-transactional-outbox-duplicate-events/view) (áp dụng cho worker cập nhật counter).

### Inferences
- **Feed Tripflix**:
  - "For You": fan-out-on-read — AI/Search module trả danh sách ứng viên; cache kết quả per-user trong Redis list `feed:fy:{uid}` (TTL 10–30 phút, 50–100 item) để cuộn mượt trên mạng yếu; phân trang bằng cursor, không dùng offset.
  - "Following": quy mô demo nhỏ → fan-out-on-read bằng SQL `WHERE author_id IN (following) ORDER BY created_at DESC` + index. Nếu muốn minh hoạ mẫu Twitter: fan-out-on-write vào Redis `ZADD feed:fo:{uid}` cho tác giả < N follower, tác giả lớn hơn N thì merge lúc đọc.
  - Lọc nội dung bị ẩn **lúc đọc** (xem mục 4) để không phải xoá khỏi hàng nghìn feed đã dựng sẵn.
- **Counters (like/comment/view/save)**:
  - Nguồn sự thật: `likes(user_id, video_id, created_at, PRIMARY KEY(user_id, video_id))` → `INSERT ... ON CONFLICT DO NOTHING` làm like idempotent tự nhiên; unlike = `DELETE`.
  - Hiển thị: `video_stats(video_id, like_count, comment_count, view_count, share_count, updated_at)`. Hai lựa chọn:
    1. Đơn giản (demo): trong cùng transaction `UPDATE video_stats SET like_count = like_count + 1` **chỉ khi** insert thực sự tạo dòng mới (`RETURNING`) → nhất quán mạnh; rủi ro hot-row cho video viral (chấp nhận được ở quy mô demo).
    2. Mở rộng: Redis `HINCRBY stats:video:{id} likes 1` + worker flush định kỳ vào PostgreSQL; job đối soát ban đêm `COUNT(*)` để sửa lệch. View count luôn dùng cách này (ghi nhiều, cho phép xấp xỉ).
  - Trạng thái "tôi đã like chưa" lấy từ bảng `likes` (hoặc Redis set ngắn hạn), không suy từ counter.
- **Ghi idempotent trên mạng yếu**: mọi `POST` có tác dụng phụ (comment, booking, payment, report) nhận header `Idempotency-Key: <uuid do client tạo>`; server lưu `idempotency_keys(key, user_id, request_hash, response_status, response_body, created_at)` TTL 24h (hoặc Redis) và trả lại response cũ khi retry. Like/follow/save dùng PUT/DELETE trên tài nguyên (`PUT /videos/{id}/like`) để idempotent theo ngữ nghĩa HTTP.
- **Events**: `video.liked/unliked`, `comment.created/deleted`, `video.viewed` (batch), `user.followed`; consumer: Notification (gộp thông báo), Stats (counter), Admin analytics (trending/anomaly).

### Gaps
- Không tìm thấy nguồn chính thức (Instagram Engineering) về cách Instagram lưu/denormalize like count — search không trả kết quả; mọi đề xuất về counter ở trên là suy luận thiết kế.
- Không truy cập được trực tiếp video/slide InfoQ "Timelines at Scale" — các chi tiết Twitter chỉ đến từ nguồn thứ cấp.

---

## 4. Administration / Moderation: hàng đợi kiểm duyệt, pre-screening tự động, human-in-the-loop, hành động có lý do, audit log, thông báo & kháng nghị, lan truyền trạng thái "ẩn", analytics & phát hiện bất thường

### Takeaway
Mẫu chuẩn: **pipeline 2 tầng** — classifier tự động (OpenAI omni-moderation cho text+ảnh, Perspective cho toxicity, Rekognition cho ảnh/video) chấm điểm ngay khi đăng → quyết định tự động theo ngưỡng (auto-allow / auto-hide / đưa vào hàng đợi) → **moderator xử lý hàng đợi có ưu tiên**, mỗi hành động có **mã lý do** (giống "removal reasons" của Reddit), ghi **mod log/audit log append-only**, gửi người dùng một **"statement of reasons"** (mẫu DSA của EU) và cho **kháng nghị**. Trạng thái "ẩn" nên là cờ trên bản ghi + lọc lúc đọc + xoá cache/search index qua event, thay vì xoá cứng.

### Cited Findings

**Classifier / API tự động**
- OpenAI `omni-moderation-latest` dựa trên GPT-4o, nhận **text và ảnh**; danh mục gồm hate, harassment, violence, self-harm, sexual, illicit (thêm `illicit` và `illicit/violent`); với ảnh chỉ violence, self-harm, sexual được hỗ trợ, các danh mục khác hiện chỉ cho text — [OpenAI: Upgrading the Moderation API](https://openai.com/index/upgrading-the-moderation-api-with-our-new-multimodal-moderation-model/); [OpenAI Moderation guide](https://developers.openai.com/api/docs/guides/moderation).
- Danh mục con gồm harassment/threatening, hate/threatening, self-harm/intent, sexual/minors… — [OpenAI omni-moderation information for developers (PDF)](https://cdn.openai.com/API/docs/omni_moderation_information_for_developers.pdf).
- Perspective API: thuộc tính chính TOXICITY = "a rude, disrespectful, or unreasonable comment that is likely to make you leave a discussion"; điểm là **xác suất** — 0.9 không có nghĩa là "độc hơn" 0.7 mà là nhiều khả năng bị coi là độc hơn; thuộc tính production khác: SEVERE_TOXICITY, INSULT, PROFANITY, THREAT, IDENTITY_ATTACK, SEXUALLY_EXPLICIT — [Perspective API FAQs](https://developers.perspectiveapi.com/s/about-the-api-faqs?language=en_US); [Lasso Moderation: Perspective toxicity scores](https://www.lassomoderation.com/blog/perspective-api-toxicity/).
- Một nguồn bên thứ ba nói Google sẽ dừng Perspective API vào 31/12/2026 — **chưa xác minh từ nguồn chính thức** — [Apify listing](https://apify.com/koyourmoon/perspective-api-alternative).
- Nghiên cứu ACM đánh giá khả năng chuyển giao (transferability) của API phát hiện toxicity, cho thấy cần thận trọng khi áp dụng sang ngữ cảnh khác — [ACM: Human-centered Evaluation of a Toxicity Detection API](https://dl.acm.org/doi/full/10.1145/3582568).
- AWS Rekognition `DetectModerationLabels`: phát hiện nội dung không an toàn trong ảnh JPEG/PNG; không trả nhãn có confidence thấp hơn `MinConfidence` do người gọi đặt; có API cho cả ảnh và video — [AWS: DetectModerationLabels](https://docs.aws.amazon.com/goto/WebAPI/rekognition/DetectModerationLabels); [AWS: Detecting inappropriate images](https://docs.aws.amazon.com/rekognition/latest/dg/procedure-moderate-images.html).

**API kiểm duyệt của Reddit và Meta (tham chiếu cấu trúc)**
- Reddit (qua PRAW): có listing **modqueue** (mục chờ duyệt), **removal reasons** cấp subreddit, **mod log** (`subreddit.mod.log(limit=5)` trả moderator + hành động); approve/remove là phương thức trên từng comment/submission, remove có thể đánh dấu spam; thêm removal reason cho đối tượng chưa bị remove sẽ lỗi `INVALID_ID` — [PRAW SubredditModeration](https://praw.readthedocs.io/en/stable/code_overview/other/subredditmoderation.html); [PRAW CommentModeration](https://praw.readthedocs.io/en/stable/code_overview/other/commentmoderation.html).
- REST thô (nguồn thứ cấp): `POST /api/approve`, `POST /api/remove`, `GET /r/{sub}/about/modqueue` — [Rapid Developers tutorial](https://rapidevelopers.com/api-automations/how-to-automate-reddit-community-moderation-using-the-api); danh sách đầy đủ ở reddit.com/dev/api (chưa đọc).
- Reddit Mod Queue (help center) mô tả hàng đợi gom nội dung bị báo cáo/bị lọc để mod xử lý — [Reddit Mods Help: Moderation Queue](https://mods.reddithelp.com/hc/en-us/articles/360010090132-Moderation-Queue).
- Instagram Graph API cho phép lấy comment, trả lời, xoá, **ẩn/bỏ ẩn** comment, bật/tắt comment trên media; ẩn/bỏ ẩn qua `POST /{ig-comment-id}` — [Meta: Instagram API comment moderation](https://developers.facebook.com/docs/instagram-api/guides/comment-moderation). (Tham số chính xác — thường là `hide=true|false` — chưa xác minh trên trang reference.)

**Thông báo người dùng, lý do, kháng nghị (mẫu DSA)**
- EU DSA Transparency Database (ra mắt 25/9/2023) thu thập **statement of reasons** gần như realtime; các trường ánh xạ tới Điều 17 DSA (loại nội dung bị ảnh hưởng, phạm vi lãnh thổ, …); một quyết định có thể gồm nhiều hạn chế; mỗi bản ghi có **PUID** do nền tảng gán, duy nhất; thông tin kênh khiếu nại (redress) **không công bố** vì chỉ liên quan tới người nhận; schema được cập nhật từ 1/7/2025 theo danh mục báo cáo minh bạch — [EC: DSA Transparency Database FAQ](https://digital-strategy.ec.europa.eu/en/faqs/dsa-transparency-database-questions-and-answers); [DSA Transparency DB documentation](https://transparency.dsa.ec.europa.eu/page/documentation); [EC press: launch](https://digital-strategy.ec.europa.eu/en/news/digital-services-act-commission-launches-transparency-database).

**Phát hiện tài khoản giả / tương tác giả**
- Meta Deep Entity Classification (DEC): mô hình ML dùng "deep features" hành vi — trang/nhóm tài khoản tương tác, đặc điểm các kết nối — >20.000 feature/hồ sơ; năm 2019 gỡ ~6,5–6,6 tỷ tài khoản giả, phần lớn chặn ngay lúc tạo tài khoản; ước tính ~5% MAU là giả; giảm 27% lượng tài khoản spam/scam ước tính — [MIT Technology Review](https://www.technologyreview.com/2020/03/04/905551/how-facebook-uses-machine-learning-to-detect-fake-accounts); [TechRadar](https://www.techradar.com/news/facebook-has-a-new-ai-powered-tool-to-take-down-fake-accounts); [Fortune](https://fortune.com/2020/03/04/facebook-a-i-fake-accounts-disinformation) (số liệu 2020; 6,5 vs 6,6 tỷ mâu thuẫn nhẹ giữa các nguồn).
- Meta phân loại hồ sơ giả: tài khoản không đại diện người thật, tài khoản thật bị chiếm quyền, spammer, scammer — [MIT Technology Review](https://www.technologyreview.com/2020/03/04/905551/how-facebook-uses-machine-learning-to-detect-fake-accounts).

**Quy định VN liên quan kiểm duyệt**
- NĐ 147/2024 áp dụng cả cho nhà cung cấp xuyên biên giới có >100.000 lượt truy cập/tháng từ VN trong 6 tháng liên tục; ITIF có phân tích về quy định kiểm duyệt nội dung của Việt Nam — [Tilleke & Gibbins](https://www.tilleke.com/insights/a-closer-look-at-vietnams-decree-147-on-internet-services-and-online-information/); [ITIF: Vietnam's Content Moderation Regulation](https://itif.org/publications/2025/06/02/vietnam-content-moderation-regulation/).

### Inferences

**Pipeline kiểm duyệt đề xuất cho Tripflix**
1. Upload video/caption/comment/review → lưu `status='pending_scan'` (video) hoặc `visible` tạm (comment, xem dưới) → outbox `content.submitted`.
2. Worker pre-screen (AI module): text (caption, comment, review, tên hiển thị) → OpenAI omni-moderation (miễn phí, đa ngôn ngữ — cần thử với tiếng Việt/teencode); ảnh thumbnail + 3–5 keyframe video → omni-moderation (image) hoặc Rekognition `DetectModerationLabels`; thêm rule-based: blacklist từ khoá tiếng Việt, regex SĐT/link (spam bán tour), trùng hash nội dung.
3. Quyết định theo ngưỡng (lưu `moderation_scores` để giải thích):
   - score < 0.3 tất cả danh mục → `published`.
   - 0.3–0.8 hoặc dính danh mục nhạy cảm → `published_limited` (không vào For You/Search) + vào **queue**.
   - > 0.8 hoặc `sexual/minors`, `self-harm/intent` → `auto_hidden` + queue ưu tiên cao nhất.
   - Tác giả `age_band='u16'` → ngưỡng chặt hơn.
4. **Hàng đợi có ưu tiên**: `priority = w1*max_score + w2*log(1+report_count) + w3*log(1+views_last_hour) + w4*severity(category) + w5*author_risk`; nội dung đang viral + nhiều report lên đầu (giảm thiệt hại phơi nhiễm). Claim có khoá: `UPDATE mod_cases SET assignee=:me, claimed_at=now() WHERE id IN (SELECT id ... ORDER BY priority DESC FOR UPDATE SKIP LOCKED LIMIT 1)` — cùng kỹ thuật với outbox, tránh 2 mod xử lý trùng.
5. Moderator hành động bằng **reason code** chuẩn (tương tự Reddit removal reasons): `SPAM`, `SCAM_FAKE_TOUR`, `NUDITY`, `VIOLENCE`, `HATE`, `HARASSMENT`, `MINOR_SAFETY`, `MISLEADING_LOCATION`, `FAKE_REVIEW`, `COPYRIGHT`, `PII_EXPOSED`… + ghi chú nội bộ + ghi chú cho người dùng.
6. Gửi người dùng **statement of reasons** (mẫu DSA): nội dung bị ảnh hưởng, hành động (ẩn/giới hạn/xoá/khoá), căn cứ (điều khoản), có dùng tự động hay không, cách kháng nghị, hạn kháng nghị.
7. **Kháng nghị** → tạo case mới loại `appeal`, giao cho moderator **khác** người ra quyết định gốc; kết quả `upheld|reversed` → event `appeal.resolved` → khôi phục nội dung + thông báo.
8. Cấp tài khoản: strike system (`user_strikes`), 3 strike/90 ngày → khoá tạm; ban → thu hồi toàn bộ session (liên kết module Access).

**Audit log append-only**
- Bảng `audit_logs(id BIGSERIAL, occurred_at, actor_id, actor_role, action, target_type, target_id, reason_code, note, before JSONB, after JSONB, ip, user_agent, request_id, prev_hash, hash)`.
- Append-only trong PostgreSQL: `REVOKE UPDATE, DELETE ON audit_logs FROM app_user;` + trigger `BEFORE UPDATE OR DELETE ... RAISE EXCEPTION`; tuỳ chọn hash-chain (`hash = sha256(prev_hash || row)`) để chứng minh không bị sửa — đơn giản, hợp demo.
- Ghi audit cho: mọi hành động mod/admin, đổi role, xem dữ liệu nhạy cảm (PII, booking của người khác), export dữ liệu, hoàn tiền thủ công. Admin panel có trang `mod log` giống Reddit.

**Lan truyền trạng thái "hidden" nhanh**
- Nguồn sự thật: `videos.visibility ∈ {public, limited, hidden, removed}` + `moderation_state`. Mọi query feed/search/detail lọc `visibility='public'` (hoặc owner xem được bản của mình kèm nhãn "đã bị ẩn").
- Khi đổi trạng thái → transaction ghi `videos` + `audit_logs` + `outbox(content.visibility_changed)`; consumer:
  - Cache: `DEL video:{id}`, `SADD hidden_content {id}` (Redis set dùng làm **bộ lọc lúc đọc** cho feed đã cache — rẻ hơn xoá khỏi mọi `feed:*`).
  - Search module: xoá/cập nhật document trong index (PostgreSQL FTS thì tự động; nếu Elasticsearch/Meilisearch thì gọi delete).
  - CDN: purge URL video/thumbnail hoặc dùng signed URL hết hạn ngắn để nội dung bị ẩn tự hết truy cập.
  - Realtime: đẩy SSE `content.hidden` tới client đang xem để thay bằng placeholder.
  - Counters/trending: loại khỏi bảng xếp hạng.
- Mục tiêu demo: < 5 giây từ lúc mod bấm "Ẩn" đến khi biến mất khỏi feed web và app.

**Admin analytics & phát hiện bất thường (đơn giản hoá kiểu DEC)**
- Trending dashboard: điểm trend theo cửa sổ trượt `score = (likes + 2*comments + 3*shares + 0.1*views)_1h / (baseline_7d + k)`; Redis sorted set `trend:place:{1h}`; biểu đồ theo địa điểm/hashtag.
- Fake engagement (rule + thống kê, không cần ML): tỉ lệ like/view bất thường (z-score so với video cùng kênh); nhiều like từ tài khoản mới tạo < 24h, cùng `device_id`/IP/ASN; burst like trong vài giây; tài khoản chưa xác thực SĐT tương tác dày đặc; review 5 sao từ tài khoản không có badge `traveled`. Lấy cảm hứng từ ý tưởng "deep features" của DEC (đặc trưng hành vi + đặc trưng của các kết nối).
- Abnormal orders (Booking/Payment): nhiều booking huỷ liên tiếp, nhiều thẻ khác nhau trên một thiết bị, giá trị đơn vượt p99 của user, đặt-huỷ để giữ phòng; xuất hiện như case loại `fraud_order` trong cùng moderation queue.

**REST endpoints module Administration (đề xuất, prefix `/api/v1/admin`, yêu cầu role moderator/admin + 2FA)**
```
POST   /api/v1/reports                         {target_type, target_id, reason_code, note}     (người dùng báo cáo; rate-limited)
GET    /api/v1/admin/mod-queue?type=&status=open&sort=priority&cursor=
POST   /api/v1/admin/mod-cases/{id}/claim
POST   /api/v1/admin/mod-cases/{id}/release
POST   /api/v1/admin/mod-cases/{id}/actions    {action: approve|limit|hide|remove|warn_user|suspend_user, reason_code, note_internal, note_to_user}
GET    /api/v1/admin/mod-cases/{id}            (kèm scores, reports, lịch sử tác giả)
GET    /api/v1/admin/moderation-reasons ; POST/PUT ... (quản lý reason code như Reddit removal reasons)
GET    /api/v1/admin/mod-log?actor=&target=&from=&to=     (đọc audit log)
POST   /api/v1/appeals                         {decision_id, statement}    (người dùng)
GET    /api/v1/me/moderation-decisions         (statement of reasons của tôi)
GET    /api/v1/admin/appeals?status=open ; POST /api/v1/admin/appeals/{id}/resolve {outcome: upheld|reversed, note}
GET    /api/v1/admin/analytics/trending?window=1h&dim=place|hashtag
GET    /api/v1/admin/analytics/anomalies?type=engagement|orders&status=open
POST   /api/v1/admin/anomalies/{id}/resolve    {verdict: fraud|benign, action}
```

**Entity**
- `reports(id, reporter_id, target_type, target_id, reason_code, note, created_at, UNIQUE(reporter_id,target_type,target_id))`
- `moderation_scores(id, target_type, target_id, provider[openai|perspective|rekognition|rules], model_version, categories JSONB, max_score, created_at)`
- `mod_cases(id, target_type, target_id, source[auto|report|appeal|anomaly], priority, status[open|claimed|resolved], assignee_id, claimed_at, resolved_at)`
- `mod_decisions(id PUID, case_id, action, reason_code, automated BOOL, note_internal, note_to_user, decided_by, decided_at, reversible_until)`
- `appeals(id, decision_id, user_id, statement, status, reviewer_id, outcome, resolved_at)`
- `user_strikes(user_id, decision_id, expires_at)`; `audit_logs` (như trên); `anomaly_alerts(id, type, subject_type, subject_id, signals JSONB, score, status)`.
- Events: `content.submitted`, `content.scanned`, `report.created`, `mod.case_opened`, `mod.action_taken`, `content.visibility_changed`, `user.suspended`, `appeal.created`, `appeal.resolved`, `anomaly.detected`.

**Đơn giản hoá cho demo 4 tuần**
- Chỉ dùng **một** API kiểm duyệt (OpenAI omni-moderation: miễn phí, text+ảnh) + blacklist từ khoá tiếng Việt; Rekognition/Perspective nêu như hướng mở rộng (Perspective có thể ngừng hoạt động — chưa xác minh).
- Video: chỉ quét thumbnail + 3 keyframe (ffmpeg) thay vì toàn bộ video.
- Priority score bằng công thức tuyến tính, không cần ML; anomaly detection bằng SQL + z-score chạy cron 5 phút.
- Audit log append-only bằng trigger PostgreSQL thay vì hệ thống WORM riêng.

### Gaps
- Không tìm được danh sách nhãn Rekognition (taxonomy cấp 1/2) và các API DetectFaces/DetectText (cho khuôn mặt/PII) từ nguồn chính thức trong phiên — cần tra trang "Using the image and video moderation APIs" của AWS.
- Chưa xác minh hiệu năng của OpenAI moderation / Perspective với **tiếng Việt và teencode**; nghiên cứu ACM gợi ý rủi ro chuyển giao ngữ cảnh — cần thử nghiệm thực tế.
- Không có nguồn chính thức về cách Meta/Reddit/TikTok **ưu tiên hàng đợi** (công thức priority) và thời gian xử lý mục tiêu (SLA) — công thức trên là suy luận.
- Chưa tìm được tài liệu Facebook SybilEdge hay cách LinkedIn phát hiện fake engagement (search không trả kết quả liên quan).
- Chưa đọc được các điều khoản cụ thể DSA về kháng nghị (Điều 20) hay quy định thời hạn gỡ nội dung theo NĐ 147 (ví dụ yêu cầu gỡ trong 24 giờ khi có yêu cầu cơ quan chức năng — thường được nhắc tới nhưng **chưa xác minh** trong phiên này).

---

## 5. Tham chiếu API công khai để định hình cấu trúc (FCM HTTP v1, Auth0/Keycloak RBAC, Reddit mod API, Meta Graph API, Discord Gateway)

### Takeaway
Các API công khai cho khuôn mẫu đặt tên và hành vi: FCM v1 (`POST /v1/projects/{project}/messages:send` với `message.token|topic|condition`, `android.priority`, `android.collapse_key`), Reddit (`modqueue`, `approve`, `remove` + removal reason, `modlog`), Meta Graph (`POST /{comment-id}` để ẩn/bỏ ẩn), Discord Gateway (WebSocket có heartbeat + resume theo sequence). Tripflix nên mô phỏng các khái niệm này nhưng gói gọn trong REST + SSE.

### Cited Findings
- FCM HTTP v1: tài nguyên `projects.messages`; message nhắm tới token, topic hoặc condition; hỗ trợ priority theo nền tảng và `collapse_key` cho Android — [FCM REST v1 projects.messages](https://firebase.google.com/docs/reference/fcm/rest/v1/projects.messages); [Go package google.golang.org/api/fcm/v1](https://pkg.go.dev/google.golang.org/api/fcm/v1); [google-apis-rs: projects.messages.send](https://byron.github.io/google-apis-rs/google_fcm1_cli/projects_messages-send/).
- Reddit mod API: modqueue listing, removal reasons, mod log, approve/remove trên từng item — [PRAW SubredditModeration](https://praw.readthedocs.io/en/stable/code_overview/other/subredditmoderation.html); REST `POST /api/approve`, `POST /api/remove`, `GET /r/{sub}/about/modqueue` theo [Rapid Developers](https://rapidevelopers.com/api-automations/how-to-automate-reddit-community-moderation-using-the-api) (thứ cấp).
- Meta Instagram Graph API: comment moderation (get/reply/delete/hide/unhide/disable comments), ẩn qua `POST /{ig-comment-id}` — [Meta comment moderation guide](https://developers.facebook.com/docs/instagram-api/guides/comment-moderation).
- Discord: kiến trúc gateway WebSocket, mỗi guild là một process, session process per user (thứ cấp) — [Elixir blog](https://elixir-lang.org/blog/2020/10/08/real-time-communication-at-scale-with-elixir-at-discord/); [ByteByteGo](https://blog.bytebytego.com/p/how-discord-serves-15-million-users).
- OAuth/OIDC: Authorization Code + PKCE bắt buộc cho SPA/native; audience restriction — [OWASP OAuth2 Cheat Sheet](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/OAuth2_Cheat_Sheet.md).

### Inferences
- Body FCM v1 mẫu cho Tripflix (dựng theo các trường đã trích; cần đối chiếu trang gốc trước khi code):
```json
POST https://fcm.googleapis.com/v1/projects/tripflix-demo/messages:send
{ "message": {
    "token": "<device_fcm_token>",
    "data": { "type": "sync", "cursor": "18342", "event_id": "b1f..." },
    "android": { "priority": "normal", "collapse_key": "sync", "ttl": "3600s" } } }
```
  Thông báo booking: thêm `"notification": {"title": "...", "body": "..."}`, `"android": {"priority": "high"}`, deeplink trong `data.deeplink = "tripflix://booking/123"`.
- Ánh xạ khái niệm: Reddit `modqueue` → `GET /admin/mod-queue`; `approve/remove + removal reason` → `POST /admin/mod-cases/{id}/actions {action, reason_code}`; `modlog` → `GET /admin/mod-log`; Meta `hide comment` → `visibility='hidden'` có thể đảo ngược; Discord `Resume` theo sequence → SSE `Last-Event-ID` + `/sync?cursor`.
- Keycloak/Auth0 có thể thay toàn bộ module Access nhưng tăng độ phức tạp vận hành; với demo 4 tuần, tự triển khai JWT + bảng role là hợp lý hơn, và nêu Keycloak như phương án production.

### Gaps
- Trang gốc Firebase, Discord Developer Docs (Gateway opcodes), Auth0 RBAC và Keycloak Authorization Services đều không truy cập được trong phiên (DNS/proxy chặn) — các trường JSON FCM ngoài `token/topic/condition/priority/collapse_key` (như `ttl`, `notification`, `data` phải là string map) dựa trên hiểu biết chung, **cần kiểm chứng** với tài liệu chính thức.
- Chưa đọc tài liệu chính thức reddit.com/dev/api để xác nhận tham số `spam`, `id` (fullname) của `/api/remove`.
