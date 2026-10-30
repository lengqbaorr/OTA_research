# 10. Diagram workflow toàn bộ ý tưởng

> Nền tảng video du lịch "xem là đi được": người dùng đăng video kèm lộ trình dạng điểm, người xem bấm **"Đi ngay"** để hệ thống tự lên phương án đi lại và chỗ ở theo ngân sách rồi đặt luôn.
> Cập nhật theo [tài liệu 13](13-di-ngay-planner-hybrid.md): đã **bỏ bước xác minh "đã đi thật"**.
> Bản xem đẹp để trình bày: [demo/workflow-diagram.html](../demo/workflow-diagram.html) (cần mạng để tải thư viện vẽ sơ đồ).

---

## 1. Toàn cảnh: vòng lặp của nền tảng

```mermaid
flowchart LR
    A["Người dùng đi du lịch"] --> C["Đăng video +<br/>lộ trình dạng điểm"]
    C --> D["Kiểm duyệt AI + người,<br/>nhãn Hợp tác"]
    D --> E["Bảng tin xu hướng<br/>và cá nhân hóa"]
    E --> F["Người xem lướt video"]
    F --> G["Đi ngay"]
    G --> P["Lập kế hoạch lai:<br/>AI hiểu ý + luật tính toán"]
    P --> H["Đặt vé và phòng,<br/>thanh toán một lần"]
    H --> I["Đi chuyến đi"]
    I --> R["Đánh giá khớp thực tế"]
    R --> E
    I --> A
    H --> J["Ghi nhận nguồn:<br/>đặt từ bài nào"]
    J --> E
```

---

## 2. "Đi ngay": bộ lập kế hoạch lai

```mermaid
flowchart TD
    IN["Người xem: nơi đi, ngày, số người,<br/>ngân sách, ưu tiên, câu nói tự do"] --> AI["Lớp AI: hiểu câu nói<br/>thành điều kiện có cấu trúc"]
    AI --> RULE
    ROUTE["Lộ trình của người đăng:<br/>điểm đến, chỗ ở, điểm chơi, quán ăn"] --> RULE
    subgraph RULE["Lớp luật"]
        R1["Tìm đường đi trên đồ thị phương tiện:<br/>máy bay, tàu, xe, limousine"]
        R2["Ràng buộc giờ: nối chuyến ≥ 1,5h,<br/>tới nơi trước 23h, lùi ngày nếu cần"]
        R3["Chỗ ở: ưu tiên chỗ trong lộ trình<br/>nếu còn phòng và vừa ngân sách"]
        R4["Lọc: ngân sách, tránh đi đêm"]
        R5["Chọn 3 phương án:<br/>Tiết kiệm / Cân bằng / Nhanh"]
        R1 --> R2 --> R3 --> R4 --> R5
    end
    RULE --> OK{"Có phương án<br/>vừa ngân sách?"}
    OK -->|"Có"| SHOW["Hiện phương án + gợi ý ăn chơi:<br/>đi gì tới, tầm giá"]
    OK -->|"Không"| HONEST["Nói thật, đưa phương án gần nhất<br/>và mức cần nâng"]
    SHOW --> EXPL["AI giải thích bằng lời,<br/>nhận yêu cầu sửa"]
    EXPL --> CART["Giỏ hàng: vé các chặng + phòng"]
```

---

## 3. Người đăng: từ video đến bài đăng có lộ trình

```mermaid
flowchart TD
    S["Người dùng muốn chia sẻ chuyến đi"] --> M["Chọn hoặc quay video, ảnh"]
    M --> PR{"Có mặt người lạ<br/>hoặc giấy tờ?"}
    PR -->|"Có"| BL["Làm mờ"] --> TAG
    PR -->|"Không"| TAG["Gắn điểm dừng vào đoạn video"]
    TAG --> CAP["Caption, vibe,<br/>khai báo có nhận tài trợ"]
    CAP --> IT["Dựng lộ trình bằng điểm theo ngày:<br/>di chuyển, ở, chơi, ăn + tầm giá"]
    IT --> PV["Xem trước"]
    PV --> UP["Đăng: tải lên chạy nền,<br/>mất mạng vẫn tiếp tục"]
    UP --> PIPE["Xử lý: chuyển mã video,<br/>chép lời, đọc chữ, lập chỉ mục tìm kiếm"]
    PIPE --> MOD{"Sàng lọc tự động"}
    MOD -->|"An toàn"| LIVE["Hiện ngay trên bảng tin"]
    MOD -->|"Cần xem"| LIVE2["Hiện và vào hàng chờ"]
    MOD -->|"Nguy hiểm"| HOLD["Giữ lại chờ duyệt"]
```

---

## 4. Người xem: từ video đến "Đi ngay"

```mermaid
flowchart TD
    FEED["Bảng tin video dọc"] --> ACT{"Người xem làm gì?"}
    ACT -->|"Thả tim, lưu, bình luận"| SIG["Tín hiệu gu và xu hướng"]
    ACT -->|"Chạm thẻ địa điểm"| DET["Trang chi tiết chỗ"] --> QB["Đặt riêng một chỗ"]
    ACT -->|"Xem lộ trình"| SHEET["Bảng lộ trình theo ngày"]
    ACT -->|"Đi ngay"| FY
    SHEET --> FY["Màn Đi ngay"]
    FY --> CFG["Chọn nơi đi, ngày, số người,<br/>ngân sách, hoặc nói tự do"]
    CFG --> ITEMS["Bộ lập kế hoạch lai<br/>đưa ra 3 phương án"]
    ITEMS --> SRC{"Nguồn của từng mục"}
    SRC -->|"Bán trực tiếp"| OWN["Kho của nền tảng:<br/>còn bao nhiêu phòng, ghế"]
    SRC -->|"API đối tác"| API["Hỏi phòng trống và giá<br/>qua API đối tác"]
    SRC -->|"Liên kết affiliate"| AFF["Giá tham khảo,<br/>mở trang đối tác đã điền sẵn"]
    SRC -->|"Gợi ý ghé"| FREE["Điểm tham khảo, không bán"]
    OWN --> CHK{"Còn chỗ?"}
    API --> CHK
    CHK -->|"Không"| ALT["Đổi ngày, mục tương tự,<br/>giảm người hoặc bỏ mục"] --> ITEMS
    CHK -->|"Có"| TOT["Tổng tiền trả trong app<br/>+ cảnh báo nếu giá cao bất thường"]
    TOT --> POL["Chọn: giữ phần đặt được<br/>hoặc tất cả hoặc không"]
    POL --> PAY["Sang thanh toán"]
```

---

## 5. Giữ chỗ và thanh toán

```mermaid
flowchart TD
    PAY["Bấm Giữ chỗ và thanh toán"] --> LG{"Đã đăng nhập?"}
    LG -->|"Chưa"| LOGIN["Đăng nhập,<br/>quay lại đúng màn với lựa chọn cũ"] --> AGE
    LG -->|"Rồi"| AGE{"Đủ 18 tuổi?"}
    AGE -->|"Chưa"| BLOCK["Chặn tự đặt,<br/>cho lưu hoặc nhờ người lớn đặt"]
    AGE -->|"Đủ"| HOLD["Giữ chỗ tất cả mục 10 phút,<br/>chốt giá"]
    HOLD --> HOK{"Giữ được hết?"}
    HOK -->|"Không"| BACK["Báo trước khi trả tiền,<br/>quay lại sửa lộ trình"]
    HOK -->|"Có"| MODEL{"Phần bán trực tiếp<br/>trả thế nào?"}
    MODEL -->|"Trả trước trong app"| CHARGE["Thanh toán một lần,<br/>có mã chống trùng"]
    MODEL -->|"Trả tại chỗ"| CHARGE2["Chỉ thanh toán phần còn lại;<br/>nền tảng thu hoa hồng"]
    CHARGE --> CONF["Xác nhận từng mục"]
    CHARGE2 --> CONF
    CONF --> RES{"Kết quả"}
    RES -->|"Tất cả thành công"| OK["Đã đặt xong"]
    RES -->|"Một mục lỗi"| PART["Áp chính sách đã chọn:<br/>hoàn tiền mục lỗi, hủy miễn phí 30 phút<br/>hoặc hủy và hoàn toàn bộ"]
    OK --> AFTER["Chuyến đi của tôi<br/>+ gợi ý phần còn thiếu<br/>+ link đặt vé máy bay trên đối tác"]
    PART --> AFTER
```

### Trình tự các hệ thống khi thanh toán

```mermaid
sequenceDiagram
    actor U as Người xem
    participant APP as App
    participant BE as Backend
    participant INV as Kho của nền tảng
    participant TV as API đối tác
    participant PG as Cổng thanh toán
    participant HOST as Chủ nhà / nhà xe
    U->>APP: Giữ chỗ và thanh toán
    APP->>BE: Tạo phiên đặt chỗ
    BE->>INV: Giữ phòng, ghế, 10 phút
    BE->>TV: Kiểm tra lại giá và chỗ
    TV-->>BE: Còn hiệu lực
    BE-->>APP: Đã giữ chỗ, giá đã chốt
    U->>APP: Xác nhận thanh toán
    APP->>BE: Thanh toán, kèm mã chống trùng
    BE->>PG: Trừ tiền một lần
    PG-->>BE: Thành công
    BE->>INV: Chốt đơn, trừ tồn kho
    BE->>TV: Đặt chỗ
    TV-->>BE: Mã xác nhận
    BE->>HOST: Gửi đơn mới
    BE-->>APP: Đã đặt xong
    APP-->>U: Kết quả và gợi ý phần còn thiếu
```

---

## 6. Kiểm duyệt và chống seeding

```mermaid
flowchart TD
    NEW["Bài hoặc bình luận mới,<br/>hoặc bản sửa"] --> AI{"AI và luật sàng lọc"}
    AI -->|"An toàn"| SHOW["Hiện ngay"]
    AI -->|"Cần xem"| Q["Hàng chờ kiểm duyệt"]
    AI -->|"Nguy hiểm, lừa đảo,<br/>lộ thông tin cá nhân"| HIDE["Giữ lại hoặc ẩn tạm"] --> Q
    SHOW --> REP{"Bị báo cáo?"}
    REP -->|"Từ 3 người tin cậy trở lên<br/>hoặc nhóm nghiêm trọng"| HIDE
    REP -->|"Ít báo cáo"| Q
    Q --> MODR["Kiểm duyệt viên quyết định,<br/>AI gợi ý lý do"]
    MODR --> ACTN["Hiện / ẩn / xóa + lý do"]
    ACTN --> LOG["Ghi audit log"]
    ACTN --> NOTI["Báo người đăng,<br/>cho kháng nghị"]
    ACTN --> SYNC["Bài ẩn biến khỏi app, web,<br/>tìm kiếm và trợ lý"]
    subgraph SEED["Chống seeding và đẩy ảo"]
        T1["Đánh giá khớp thực tế<br/>từ người đã đi theo"]
        T2["Nhãn Hợp tác bắt buộc"]
        T3["Điểm uy tín tài khoản"]
        T4["Một người một phiếu,<br/>gom cụm thiết bị"]
        T5["Phát hiện tăng vọt bất thường"]
    end
    SEED --> TREND["Điểm xu hướng sạch"]
```

---

## 7. Xu hướng, cá nhân hóa và thưởng

```mermaid
flowchart LR
    EV["Sự kiện: xem, thả tim,<br/>bình luận, lưu, chia sẻ,<br/>đặt theo lộ trình"] --> W["Nhân trọng số<br/>và độ tin cậy người dùng"]
    W --> DECAY["Giảm dần theo thời gian,<br/>tính lại mỗi 5 phút"]
    DECAY --> TS["Điểm xu hướng bài và chỗ"]
    EV --> TASTE["Hồ sơ gu người dùng"]
    TS --> MIX["Bảng tin: 60% hợp gu,<br/>20% xu hướng, 20% khám phá"]
    TASTE --> MIX
    MIX --> REASON["Lý do ngắn bằng số thật"]
    BOOK["Đơn đặt theo lộ trình"] --> ATTR["Ghi nhận bài nguồn"]
    ATTR --> DONE{"Người đi theo<br/>hoàn thành chuyến?"}
    DONE -->|"Có, không gian lận"| REWARD["Mở thưởng cho người đăng"]
    DONE -->|"Hủy hoặc nghi farm"| NO["Không thưởng"]
    ATTR --> EV
```

---

## 8. Nguồn hàng: hai mô hình cùng tồn tại

```mermaid
flowchart TD
    FY["Phương án Đi ngay"] --> A["Bán trực tiếp<br/>hàng của nền tảng"]
    FY --> B["API đối tác<br/>OTA, bedbank"]
    FY --> C["Link affiliate"]
    A --> A1["Homestay, nhà xe ký hợp đồng"]
    A1 --> A2["Màn đối tác: lịch phòng,<br/>ngừng bán, giá, đơn mới"]
    A1 --> A3["Trả trước trong app<br/>hoặc trả tại chỗ"]
    B --> B1["Tìm, kiểm tra lại, đặt qua API"]
    B1 --> B2["Giá và chỗ thật,<br/>thanh toán trong app"]
    C --> C1["Giá tham khảo lưu tạm"]
    C1 --> C2["Mở trang đối tác đã điền sẵn,<br/>mã theo dõi theo bài"]
```

---

## 9. Kiến trúc hệ thống

```mermaid
flowchart LR
    subgraph CLIENT["Giao diện"]
        AND["Android app<br/>offline-first"]
        WEB["Web đặt chỗ"]
        ADM["Trang quản trị<br/>và màn đối tác"]
    end
    subgraph BACK["Backend: một khối chia module"]
        AUTH["Tài khoản và phân quyền"]
        VER["Xác minh đã đi"]
        POSTM["Bài đăng, media, tương tác"]
        MODM["Kiểm duyệt"]
        TRN["Xu hướng và cá nhân hóa"]
        SRCH["Tìm kiếm tiếng Việt kiểu người trẻ"]
        AST["Trợ lý: gọi công cụ + kiểm chứng"]
        BK["Đặt chỗ, giữ chỗ, đơn"]
        PAYM["Thanh toán, hoàn tiền"]
        SUP["Kết nối nguồn hàng"]
    end
    subgraph DATA["Dữ liệu"]
        DB[("CSDL giao dịch")]
        CACHE[("Cache, bộ đếm, hàng đợi")]
        IDX[("Chỉ mục tìm kiếm")]
        MEDIA[("Kho media + CDN")]
    end
    subgraph EXT["Bên ngoài"]
        OTA["API đối tác OTA"]
        AFFN["Mạng affiliate"]
        PGW["Cổng thanh toán"]
        LLM["Mô hình ngôn ngữ"]
        MAIL["Email chuyển tiếp"]
    end
    CLIENT -->|"API + realtime"| BACK
    BACK --> DATA
    SUP --> OTA
    SUP --> AFFN
    PAYM --> PGW
    AST --> LLM
    VER --> MAIL
```
