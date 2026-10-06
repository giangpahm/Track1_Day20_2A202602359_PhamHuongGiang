# Metrics Pack — Track 1 Day 20

> Đây là bản làm việc. Sau khi hoàn thành, chuyển nội dung sang tệp trình bày trực quan và dán link đã cấp quyền xem vào `README.md`.

## 00 — Dự án, persona, core job

- **Dự án:** FixIt Agent — hệ thống AI giúp Ban quản lý chuyển cam kết trong hợp đồng dịch vụ vệ sinh thành rule có thể giám sát, đối chiếu rule với dữ liệu vận hành và truy xuất kết quả về điều khoản gốc.
- **Use case được chọn:** BQL đánh giá một ca vệ sinh do nhân viên vệ sinh thực hiện, có kết quả hoặc bằng chứng kiểm tra của Vendor Supervisor, dựa trên rule được xác định từ hợp đồng và các bằng chứng liên quan.
- **Persona:** BQL – Quản lý vận hành chung cư/tòa nhà, người chịu trách nhiệm đánh giá vendor có thực hiện đúng cam kết hợp đồng hay không.
- **Core job (bằng lời người dùng):** “Khi cần kiểm tra một ca vệ sinh của vendor, tôi muốn đối chiếu việc thực hiện của nhân viên vệ sinh và kết quả kiểm tra của giám sát với cam kết trong hợp đồng và các bằng chứng liên quan, để xác định ca đó đạt, vi phạm hay cần kiểm tra thêm.”

### Các vai trò liên quan trong use case

| Vai trò | Trách nhiệm trong luồng |
|---|---|
| BQL – Quản lý vận hành | Người dùng chính; đánh giá ca vệ sinh dựa trên rule hợp đồng và bằng chứng. |
| Vendor Supervisor | Phân công, giám sát nhân viên vệ sinh và hoàn tất bước hậu kiểm. Bước này chỉ hoàn tất khi supervisor đã cung cấp ảnh minh chứng; sau đó ca mới được chuyển cho BQL đánh giá. |
| Cleaning Worker | Trực tiếp thực hiện công việc, check-in/out và cung cấp bằng chứng theo quy trình. |

## 01 — Core Action

### Phân biệt bốn khái niệm

| Khái niệm | Câu trả lời |
|---|---|
| Core job | Khi cần kiểm tra một ca vệ sinh của vendor, BQL muốn đối chiếu việc thực hiện, kết quả kiểm tra của giám sát, cam kết hợp đồng và các bằng chứng liên quan để xác định ca đó đạt, vi phạm hay cần kiểm tra thêm. |
| Core action | BQL hoàn tất đánh giá một ca vệ sinh và ghi nhận một trong ba kết luận: `Đạt`, `Vi phạm` hoặc `Cần kiểm tra thêm`. |
| Core value | BQL có được kết luận có căn cứ về mức độ tuân thủ hợp đồng của từng ca vệ sinh để giám sát vendor và thực hiện bước xử lý phù hợp. |
| Core value event | `cleaning_session_evaluation_completed` |

### Core Action Card

| Thành phần | Câu trả lời |
|---|---|
| Target user | BQL – Quản lý vận hành chung cư/tòa nhà. |
| Core job | Đối chiếu việc thực hiện một ca vệ sinh và kết quả kiểm tra của Vendor Supervisor với rule hợp đồng cùng các bằng chứng liên quan để xác định mức độ tuân thủ. |
| Core action | Hoàn tất đánh giá một ca vệ sinh và ghi nhận kết luận `Đạt`, `Vi phạm` hoặc `Cần kiểm tra thêm`. |
| Object | Một ca vệ sinh cụ thể (`cleaning_session`) của vendor tại một khu vực và thời điểm xác định. |
| Preconditions | Ca vệ sinh đã được ghi nhận; có rule hợp đồng áp dụng; có dữ liệu thực hiện của Cleaning Worker; Vendor Supervisor đã hoàn tất bước hậu kiểm bằng cách gửi ít nhất một ảnh minh chứng gắn với đúng ca; và ca đã được chuyển sang trạng thái chờ BQL đánh giá. Nếu thiếu ảnh hậu kiểm, bước của supervisor chưa hoàn tất và ca không được chuyển cho BQL. |
| Completion rule | Action của BQL hoàn tất khi BQL gửi và hệ thống lưu thành công đúng một kết luận đánh giá cho ca (`Đạt`, `Vi phạm` hoặc `Cần kiểm tra thêm`), kèm định danh ca, rule hợp đồng áp dụng, ảnh hậu kiểm đã được supervisor gửi, các bằng chứng liên quan khác và người đánh giá. Nếu kết luận là `Vi phạm` hoặc `Cần kiểm tra thêm`, lý do phải được ghi nhận. Việc chỉ mở ca hoặc xem đề xuất của AI chưa được tính là hoàn tất. |
| Core value | BQL có kết luận có căn cứ và truy xuất được về mức độ tuân thủ của ca vệ sinh, từ đó biết ca nào đạt, ca nào cần xử lý với vendor và ca nào cần bổ sung kiểm tra. |
| Evidence of value | Một evaluation record đã được lưu, liên kết tới `cleaning_session`, rule/điều khoản hợp đồng, ảnh minh chứng hậu kiểm của Vendor Supervisor và các bằng chứng liên quan; record có kết luận, thời điểm và danh tính BQL đánh giá. |
| Candidate event | `cleaning_session_evaluation_completed` |

### Tự kiểm năm tiêu chí

| Tiêu chí | Đạt? | Lập luận ngắn |
|---|---:|---|
| Gần core value | [x] | Sau action, BQL đã có kết luận tuân thủ có căn cứ cho ca vệ sinh, đúng với core job cần hoàn thành. |
| Có thể lặp lại | [x] | Action lặp lại mỗi khi có một ca vệ sinh mới cần BQL đánh giá theo rule hợp đồng. |
| Có thể quan sát | [x] | Có thời điểm hoàn tất rõ ràng khi evaluation record hợp lệ được hệ thống lưu thành công. |
| Có ý nghĩa | [x] | Số ca được đánh giá có căn cứ tăng cho thấy BQL bao phủ được nhiều nghĩa vụ vận hành hơn; chất lượng kết luận vẫn cần được bảo vệ bằng counter-metric ở Phase 3. |
| Có thể tác động | [x] | Team có thể cải thiện việc liên kết rule–bằng chứng, độ rõ của giải thích AI, chất lượng dữ liệu và luồng review để BQL hoàn tất đánh giá dễ và chính xác hơn. |

**Vì sao đây không chỉ là “mở app”, “đăng nhập” hoặc thao tác giao diện:** Core action chỉ được công nhận khi BQL hoàn thành một quyết định nghiệp vụ về mức độ tuân thủ của một ca cụ thể và hệ thống lưu được căn cứ của quyết định. Mở màn hình, xem bằng chứng, bấm nút hoặc đọc kết quả AI nhưng chưa ghi nhận kết luận đều không được tính.

**Gate 1:** [x] Có actor là BQL – Quản lý vận hành, object là một `cleaning_session`, completion rule kiểm chứng được và đạt 5/5 tiêu chí.

## 02 — Action Nature Card và cadence

### Action Nature Card

| Thành phần | Câu trả lời |
|---|---|
| Actor | BQL – Quản lý vận hành thực hiện action; unit nghiệp vụ được đánh giá là một `cleaning_session`. |
| Intent | Xác định một ca vệ sinh của vendor có tuân thủ cam kết hợp đồng hay không để BQL biết ca nào đạt, vi phạm hoặc cần kiểm tra thêm. |
| Trigger | Trigger chính là thời điểm rà soát cuối ngày, khi các ca đã hoàn tất hậu kiểm của Vendor Supervisor được gom vào hàng chờ BQL đánh giá. Trigger phụ theo sự kiện xuất hiện khi hệ thống phát hiện dấu hiệu bất thường như thiếu check-in/check-out, quá thời gian, hậu kiểm chưa đạt hoặc bằng chứng không khớp cam kết và ưu tiên ca đó để BQL xem sớm. Notification chỉ truyền đạt trigger, không phải lý do tự thân để quay lại. |
| Effort | Mỗi khu vực dự kiến có khoảng hai ca vệ sinh/ngày theo khung giờ cố định. BQL rà soát theo lô vào cuối ngày, đồng thời có thể xem sớm ca bất thường. Với mỗi ca, BQL phải xem rule hợp đồng, dữ liệu thực hiện, ảnh hậu kiểm bắt buộc và các bằng chứng liên quan; sau đó chọn kết luận và ghi lý do khi `Vi phạm` hoặc `Cần kiểm tra thêm`. Effort thay đổi theo số khu vực, độ đầy đủ/mâu thuẫn của bằng chứng và độ phức tạp của rule. |
| Value timing | Value cho từng ca xuất hiện ngay sau khi evaluation record được lưu: BQL có kết luận có căn cứ. Value ở cấp vendor/tòa nhà tích lũy qua nhiều ca để nhận biết mức độ tuân thủ và xu hướng vận hành. |
| State | Hệ thống giữ evaluation record gồm ca vệ sinh, kết luận, người và thời điểm đánh giá, rule/điều khoản áp dụng, ảnh hậu kiểm, bằng chứng khác và lý do khi cần. Trạng thái ca chuyển từ `Chờ BQL đánh giá` sang trạng thái kết luận tương ứng. |
| Dependency | Phụ thuộc Cleaning Worker hoàn tất dữ liệu thực hiện; Vendor Supervisor hoàn tất hậu kiểm với ít nhất một ảnh đúng ca; rule hợp đồng đã được xác định; bằng chứng có thể truy cập; và không có lỗi liên kết ca–rule–bằng chứng. |
| Repeat condition | Chu kỳ rà soát lặp lại vào cuối mỗi ngày vận hành vì mỗi khu vực dự kiến phát sinh khoảng hai ca theo lịch hợp đồng. Trong ngày, một ca có dấu hiệu bất thường tạo lý do để BQL thực hiện action sớm hơn; nhánh ngoại lệ này không thay đổi cadence chính hằng ngày. |

- **Dạng hành vi:** `workflow của team` theo chu kỳ hằng ngày, có nhánh `phản ứng theo sự kiện` cho ca bất thường. BQL đánh giá sau khi ca đi qua Cleaning Worker và Vendor Supervisor; lịch vệ sinh cố định tạo nhịp nền cho workflow.
- **Frequency cao hơn có luôn đồng nghĩa với value cao hơn không?** Không mặc nhiên. Nhiều evaluation hoàn tất hơn có thể phản ánh độ bao phủ giám sát cao hơn, nhưng cũng có thể chỉ do phát sinh nhiều ca, ca bị chia nhỏ hoặc BQL phải xử lý lại. Vì vậy frequency cần gắn với số ca đủ điều kiện cần đánh giá và chất lượng/căn cứ của kết luận.

### Kết luận cadence — học viên tự viết

> Đối với **BQL – Quản lý vận hành tại từng tòa nhà**, core action **hoàn tất đánh giá một ca vệ sinh và ghi nhận kết luận** thường xuất hiện **trong đợt rà soát cuối mỗi ngày vận hành, hoặc sớm hơn khi có ca bất thường** vì **mỗi khu vực phát sinh khoảng hai ca/ngày theo khung giờ cố định và BQL có thể gom các ca đã được supervisor bàn giao để rà soát cuối ngày, trong khi ca có dấu hiệu bất thường cần được ưu tiên xử lý**. Do đó, nhịp đo phù hợp là **hằng ngày** ở cấp **BQL trên từng tòa nhà**, đồng thời theo dõi riêng thời gian xử lý các ca bất thường.

**Gate 2:** [x] Kết luận có lý do từ lịch vận hành và workflow thực tế; cadence hằng ngày không mâu thuẫn với hành vi theo chu kỳ, còn ca bất thường được tách thành nhánh event-response cần xử lý sớm.

## 03 — Metric System

### Activation

| Thành phần | Định nghĩa |
|---|---|
| Start event | `first_cleaning_session_ready_for_bql_review`: ca đầu tiên của một tòa đã hoàn tất bước hậu kiểm bắt buộc và được chuyển vào hàng chờ của BQL. Event này thể hiện cơ hội đầu tiên để BQL nhận core value, thay vì chỉ đăng nhập hoặc được tạo tài khoản. |
| Activation event / first value | `cleaning_session_evaluation_completed`: BQL hoàn tất lần đầu một đánh giá hợp lệ với kết luận, rule/điều khoản áp dụng và ảnh hậu kiểm của Vendor Supervisor. |
| Time window kể từ start event | Trong cùng ngày vận hành của ca đầu tiên, hoặc chậm nhất trước khi kết thúc đợt rà soát cuối ngày kế tiếp nếu ca được bàn giao sau giờ chốt. Đây là custom window bám workflow hằng ngày, cần chốt giờ vận hành cụ thể khi triển khai. |

**Activation metric hoàn chỉnh:** Tỷ lệ cặp `BQL × tòa nhà` hoàn tất đánh giá hợp lệ đầu tiên trong activation window, tính trên các cặp `BQL × tòa nhà` đã nhận ca đầu tiên ở trạng thái sẵn sàng để BQL đánh giá.

`Activation rate = số (BQL, tòa nhà) có first evaluation hợp lệ trong window / số (BQL, tòa nhà) có first ready-for-review session`

> Không dùng `account_created`, `login_completed` hay hoàn thành hướng dẫn làm activation vì các event này chưa chứng minh BQL đã nhận core value.

### Engagement — chọn tối đa hai góc đo

| Góc đo | Metric và cách tính |
|---|---|
| Frequency | **Daily evaluation coverage:** số ca được BQL hoàn tất đánh giá hợp lệ trong ngày / tổng số ca đã đủ điều kiện và được chuyển tới BQL trong ngày, tính theo từng tòa nhà. Dùng tỷ lệ thay vì số lượng thuần để không thưởng cho việc tạo thêm ca. |
| Depth | **Anomaly review completion:** tỷ lệ ca bất thường đã được BQL hoàn tất đánh giá trên tổng số ca được gắn cờ bất thường và đủ điều kiện chuyển tới BQL; báo cáo kèm median thời gian từ lúc ca được gắn cờ/ready đến lúc đánh giá hoàn tất. |

### North Star Metric

- **Unit of value:** Một ca vệ sinh đủ điều kiện đã được BQL hoàn tất đánh giá.
- **Quality threshold:** Evaluation có đúng một kết luận hợp lệ; liên kết được tới ca, rule/điều khoản hợp đồng và ảnh hậu kiểm bắt buộc của Vendor Supervisor; có danh tính và thời điểm BQL đánh giá; đồng thời có lý do nếu kết luận là `Vi phạm` hoặc `Cần kiểm tra thêm`.
- **Frequency:** Trong mỗi ngày vận hành, theo từng tòa nhà.
- **NSM hoàn chỉnh:** **Số ca vệ sinh được BQL hoàn tất đánh giá có căn cứ trong mỗi ngày vận hành trên từng tòa nhà** (`Daily qualified cleaning-session evaluations`). Luôn đọc NSM cùng `Daily evaluation coverage` để phân biệt tăng value với tăng số ca đầu vào.

### Leading indicators — tối đa ba

| Leading indicator | Vì sao tin rằng nó dự báo core action lặp lại? |
|---|---|
| **Ready-for-review rate:** số ca hoàn tất bước supervisor và chuyển thành công tới BQL / tổng số ca Cleaning Worker đã báo hoàn thành | Core action của BQL không thể xảy ra nếu supervisor chưa gửi ảnh hậu kiểm và hoàn tất bàn giao; tỷ lệ này đo trực tiếp nguồn ca hợp lệ đi vào hàng chờ. |
| **Evidence–rule match rate:** số ca ready có đủ liên kết hợp lệ giữa ca, rule hợp đồng, ảnh hậu kiểm và dữ liệu thực hiện / tổng số ca ready | BQL có khả năng hoàn tất đánh giá cao hơn khi dữ liệu cần thiết đã đầy đủ, đúng ca và truy xuất được, thay vì phải dừng để tìm hoặc yêu cầu bổ sung bằng chứng. |
| **Pending review age:** median thời gian từ `cleaning_session_ready_for_bql_review` đến thời điểm đo đối với các ca vẫn chưa được đánh giá, tách riêng ca thường và ca bất thường | Tuổi hàng chờ tăng báo hiệu BQL khó duy trì vòng rà soát hằng ngày và dự báo daily evaluation coverage/retention sẽ giảm. |

### Counter-metric — ít nhất một

| Counter-metric | Điều gì không được xấu đi khi core action/NSM tăng? |
|---|---|
| **Evaluation correction/reopen rate** | Tỷ lệ evaluation phải bị sửa kết luận hoặc mở lại sau khi hoàn tất không được tăng; nếu tăng, NSM có thể đang được đẩy bằng cách đánh giá nhanh nhưng thiếu chính xác. Theo dõi theo cửa sổ audit do nhóm quy định, chưa gán benchmark khi chưa có dữ liệu. |
| **Unsupported evaluation rate** | Tỷ lệ evaluation qua audit nhưng không đủ căn cứ để bảo vệ kết luận không được tăng. Metric này kiểm tra chất lượng thực chất, không chỉ việc các trường dữ liệu đã được điền. |
| **Median BQL review time per qualified session** | Thời gian BQL cần bỏ ra cho một ca không được tăng mất kiểm soát khi coverage tăng; sản phẩm phải giúp ra quyết định có căn cứ mà không tạo thêm gánh nặng vận hành. Tách ca thường và ca bất thường để so sánh công bằng. |

## 04 — Retention Definition

| Thành phần | Định nghĩa |
|---|---|
| Unit | Cặp `BQL × tòa nhà`, vì cùng một BQL có thể phụ trách nhiều tòa với lịch và khối lượng ca khác nhau. |
| Cohort entry | Ngày cặp `BQL × tòa nhà` phát sinh `cleaning_session_evaluation_completed` hợp lệ đầu tiên. |
| Return event | `cleaning_session_evaluation_completed` hợp lệ lặp lại cho một ca khác tại cùng tòa nhà. |
| Window | Mỗi **ngày vận hành có phát sinh ít nhất một ca ready-for-review**, theo timezone của tòa nhà. Không tính ngày không có cơ hội thực hiện core action vào mẫu số. |
| Threshold | Ít nhất một evaluation hợp lệ trong window để xác nhận return; đồng thời báo cáo `Daily evaluation coverage` để tránh việc chỉ xử lý một ca rồi bỏ lại phần lớn hàng chờ. Ngưỡng coverage mục tiêu sẽ được đặt sau khi có baseline, không bịa trước. |
| Segment | BQL – Quản lý vận hành tại các tòa đã có hợp đồng/rule hoạt động, workflow Cleaning Worker → Vendor Supervisor → BQL được cấu hình, và có ít nhất một ca ready-for-review trong ngày. Báo cáo tách tòa pilot/tòa triển khai ổn định và quy mô khối lượng ca. |

**Retention metric hoàn chỉnh:** Daily opportunity-adjusted retention là tỷ lệ cặp `BQL × tòa nhà` thuộc cohort theo ngày hoàn tất evaluation hợp lệ đầu tiên, quay lại hoàn tất ít nhất một `cleaning_session_evaluation_completed` hợp lệ trong từng ngày vận hành tiếp theo có ít nhất một ca ready-for-review, áp dụng cho các tòa đã cấu hình đầy đủ workflow và rule; kết quả được đọc cùng `Daily evaluation coverage`.

**Ba mốc dùng để diễn giải:**

- Natural cycle: Một ngày vận hành, với khoảng hai ca/khu vực/ngày và đợt rà soát chính vào cuối ngày.
- Cohort đúng segment: Cặp `BQL × tòa nhà` có workflow hoạt động và có cơ hội đánh giá thực tế; không trộn với tài khoản demo, BQL chưa được bàn giao ca hoặc ngày không có ca ready.
- Benchmark category có nguồn (nếu dùng): Chưa sử dụng benchmark bên ngoài. Trước hết so với baseline của chính các tòa pilot có quy trình tương đương; chỉ thêm benchmark khi có nguồn và định nghĩa metric tương thích.

**Gate 3:** [x] Activation có start event, first-value event và custom time window; retention đủ sáu thành phần và khớp cadence hằng ngày; NSM có unit of value, quality threshold và frequency; có leading indicators và counter-metrics.

## 05 — Product Loop

- **Loại loop chính:** `workflow` theo chu kỳ hằng ngày; có nhánh `event-response` khi một ca đủ điều kiện bị gắn cờ bất thường.

### Hai chu kỳ

1. **Natural trigger — chu kỳ 1:** Đến đợt rà soát cuối ngày vận hành, hàng chờ của tòa nhà có các ca mới đã hoàn tất hậu kiểm của Vendor Supervisor và ở trạng thái `Chờ BQL đánh giá`. Với ca bất thường, dấu hiệu thiếu check-in/check-out, quá thời gian, hậu kiểm chưa đạt hoặc bằng chứng không khớp rule tạo trigger sớm hơn. Nhu cầu kiểm soát các ca theo lịch hợp đồng là lý do thực hiện; notification chỉ giúp truyền đạt trigger.
2. **Core action — chu kỳ 1:** BQL đối chiếu dữ liệu thực hiện, rule/điều khoản hợp đồng, ảnh hậu kiểm bắt buộc và các bằng chứng liên quan; sau đó hoàn tất evaluation cho từng ca với kết luận `Đạt`, `Vi phạm` hoặc `Cần kiểm tra thêm`, kèm lý do khi cần.
3. **Immediate value — chu kỳ 1:** BQL có ngay kết luận có căn cứ và truy xuất được cho ca, biết ca nào đạt và ca nào cần xử lý với vendor hoặc bổ sung kiểm tra; hàng chờ đánh giá trong ngày được giảm.
4. **Saved state / investment:** Mỗi evaluation được lưu cùng định danh ca, kết luận, rule áp dụng, bằng chứng, người và thời điểm đánh giá. Lịch sử này tạo hồ sơ tuân thủ của vendor theo tòa nhà, hỗ trợ audit, theo dõi xu hướng và làm rõ các lần đánh giá sau; việc ghi lý do cũng làm cho quyết định kế tiếp nhất quán hơn.
5. **Next natural trigger — chu kỳ 2:** Sang ngày vận hành tiếp theo, lịch vệ sinh theo hợp đồng phát sinh các ca mới; sau khi Cleaning Worker và Vendor Supervisor hoàn tất phần việc, các ca lại trở thành đối tượng BQL phải đánh giá. Ngoài ra, ca mới bị gắn cờ bất thường tạo nhu cầu xử lý ngay. BQL quay lại vì có nghĩa vụ và rủi ro vận hành mới cần ra quyết định, không phải chỉ vì nhận notification.
6. **Core action tiếp theo — chu kỳ 2:** BQL hoàn tất evaluation hợp lệ cho một ca mới tại cùng tòa nhà theo cùng completion rule; có thể tham chiếu lịch sử đánh giá và bằng chứng trước đó để nhận biết vi phạm lặp lại hoặc mẫu bất thường.
7. **Repeat value — sau hai chu kỳ:** Ngoài giá trị của từng kết luận, BQL tích lũy được bức tranh tuân thủ qua nhiều ngày, nhận diện xu hướng và vendor/khu vực có vấn đề, đồng thời duy trì độ bao phủ giám sát thay vì để ca đủ điều kiện tồn đọng.

### Metric hypothesis — học viên tự viết

> Nếu loop này hoạt động, metric **Daily evaluation coverage** sẽ **tăng so với baseline của chính từng tòa nhà** trong các **ngày vận hành tiếp theo có ít nhất một ca ready-for-review**, vì mỗi evaluation tạo ra hồ sơ tuân thủ có thể dùng lại, còn các ca mới theo lịch hợp đồng và các ca bất thường tạo lý do nghiệp vụ tự nhiên để BQL quay lại và hoàn tất vòng đánh giá tiếp theo. Chưa đặt mức tăng phần trăm trước khi có baseline; hypothesis cần được kiểm chứng theo cohort `BQL × tòa nhà` và phải đọc cùng **Evaluation correction/reopen rate** để bảo đảm coverage không tăng bằng cách đánh giá vội hoặc thiếu chính xác.

**Gate 4 — Loop:** [x] Có ít nhất hai chu kỳ, có reason to return ngoài notification và hypothesis trỏ về `Daily evaluation coverage` ở Phase 3.

## 06 — Tracking nhanh

> Chỉ giữ 4–8 core events. Mỗi event phải map tới ít nhất một metric ở Phase 3.

| Tên event (`object_action`) | Ý nghĩa — điều đã xảy ra | Thời điểm ghi nhận chính xác | Metric sử dụng |
|---|---|---|---|
| `cleaning_session_work_completed` | Cleaning Worker đã báo hoàn tất một ca thuộc lịch hợp đồng. Event mang `session_id`, `building_id`, `vendor_id`, `completed_at`. | Ghi một lần khi trạng thái ca chuyển hợp lệ từ đang thực hiện sang chờ hậu kiểm; không ghi khi chỉ lưu nháp. | Mẫu số của **Ready-for-review rate**. |
| `cleaning_session_ready_for_bql_review` | Vendor Supervisor đã hoàn tất hậu kiểm, gửi ít nhất một ảnh đúng ca và ca đã đủ điều kiện vào hàng chờ BQL. Event mang `session_id`, `building_id`, `bql_id`, `ready_at`, `is_first_ready_for_bql_building`, `evidence_rule_match_status`. | Ghi khi transition sang `Chờ BQL đánh giá` được commit thành công và toàn bộ precondition đã qua validation. | Start event của **Activation**; tử số **Ready-for-review rate**; mẫu số **Daily evaluation coverage**; **Evidence–rule match rate**; **Pending review age**; retention opportunity window. |
| `cleaning_session_anomaly_flagged` | Một ca đủ điều kiện được hệ thống hoặc người có thẩm quyền gắn cờ bất thường. Event mang `session_id`, `building_id`, `flagged_at`, `anomaly_type`, `source`. | Ghi khi cờ bất thường mới chuyển từ không tồn tại sang active; thay đổi nội dung cùng một cờ không tạo event mới. | Mẫu số **Anomaly review completion** và mốc bắt đầu đo thời gian xử lý ca bất thường. |
| `cleaning_session_evaluation_completed` | BQL hoàn tất evaluation hợp lệ cho một ca với đúng một kết luận và đủ căn cứ bắt buộc. Event mang `evaluation_id`, `session_id`, `building_id`, `bql_id`, `conclusion`, `completed_at`, `rule_id`, `evidence_ids`, `reason_present`, `is_anomaly`. | Ghi một lần sau khi evaluation record hợp lệ được lưu và trạng thái ca chuyển khỏi `Chờ BQL đánh giá`; mở/xem ca hoặc xem đề xuất AI không được ghi. | Activation event; **Daily evaluation coverage**; **Anomaly review completion**; **NSM**; **Median BQL review time** khi ghép với ready/flagged event; return event của **Retention**. |
| `cleaning_session_evaluation_reopened` | Một evaluation đã hoàn tất bị mở lại để sửa hoặc đánh giá lại. Event mang `evaluation_id`, `session_id`, `reopened_at`, `reopened_by`, `reason_code`. | Ghi khi trạng thái evaluation chuyển từ completed sang reopened; thao tác reload hoặc mở màn hình không được tính. | **Evaluation correction/reopen rate**. |
| `cleaning_session_evaluation_audit_completed` | Audit một evaluation đã kết thúc và xác định kết luận có đủ căn cứ hay không. Event mang `evaluation_id`, `session_id`, `audited_at`, `auditor_id`, `is_supported`, `finding_code`. | Ghi sau khi audit result hợp lệ được lưu ở trạng thái final; bản nháp hoặc audit bị hủy không được ghi. | **Unsupported evaluation rate** và kiểm tra quality threshold của NSM. |

### Acceptance criteria — ít nhất hai

1. `cleaning_session_evaluation_completed` chỉ được phát sau khi transaction lưu evaluation thành công và ca chuyển từ `Chờ BQL đánh giá` sang đúng trạng thái kết luận. Payload phải có `evaluation_id`, `session_id`, `building_id`, `bql_id`, đúng một `conclusion`, `rule_id`, ít nhất một ảnh hậu kiểm trong `evidence_ids`, `completed_at`; `reason_present=true` khi kết luận là `Vi phạm` hoặc `Cần kiểm tra thêm`. Nếu validation hoặc transaction thất bại thì không phát event.
2. Mỗi transition dùng idempotency key dạng `event_name:object_id:state_version`. Cùng một `session_id` và state version chỉ tạo tối đa một ready/completed/flagged event; cùng một `evaluation_id` và version chỉ tạo tối đa một completed/reopened/audit event. Reload, autosave, double-click và retry phải trả lại kết quả trước đó, không tạo bản ghi event mới.
3. Thời gian xử lý được tính bằng timestamp phía server: ca thường từ `ready_at` đến `completed_at`; ca bất thường từ `flagged_at` (hoặc `ready_at` nếu cờ có trước khi ca đủ điều kiện) đến `completed_at`. Mọi timestamp lưu ở UTC và khi gom theo ngày phải quy đổi theo timezone của tòa nhà.
4. `Daily evaluation coverage` và retention chỉ đưa một ca vào mẫu số khi có `cleaning_session_ready_for_bql_review`; nếu ca bị hủy hợp lệ sau đó, hệ thống phải lưu trạng thái loại trừ có audit trail thay vì xóa event gốc.

**Gate 5 — Tracking:** [x] Có 6 core events, mọi event map tới metric ở Phase 3 và có bốn acceptance criteria kiểm chứng được.

## Tự soi lỗi trước khi nộp

- [x] Core action không phải thao tác giao diện hoặc output hệ thống.
- [x] Activation không phải xem hướng dẫn hoặc đăng nhập.
- [x] Frequency không cao hơn nhu cầu thật.
- [x] Loop có reason to return ngoài notification.
- [x] Retention window khớp với cadence.
- [x] Mọi event đều map về một metric.
- [x] Mọi metric đều có event để tính.

## Revision rationale — học viên tự viết nếu có thay đổi lớn

Không có thay đổi lớn đối với core action hoặc cadence sau khi qua các gate. Tôi giữ unit đo là cặp `BQL × tòa nhà`, đổi cách đọc volume thuần sang `Daily evaluation coverage` để tránh thưởng cho việc tạo thêm ca, và bổ sung counter-metrics cùng event audit/reopen để bảo vệ chất lượng khi NSM tăng.
