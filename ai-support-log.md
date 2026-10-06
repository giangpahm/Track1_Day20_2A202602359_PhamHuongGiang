# AI Support Log

## AI đã giúp tôi ở đâu?

AI đã giúp tôi tạo cấu trúc repository và template Metrics Pack theo yêu cầu đề bài. Ở Phase 1, AI brainstorm ba ứng viên core action và đặt câu hỏi để phân biệt hành vi của BQL với output hệ thống. Sau khi tôi chọn phương án A, AI hỗ trợ chuẩn hóa Core Action Card, gợi ý tên event và thực hiện bản tự kiểm năm tiêu chí.

Ở Phase 2, AI hỗ trợ phân tích actor, intent, trigger, effort, value timing, state, dependency và repeat condition từ workflow đã chốt; đồng thời chỉ ra rằng frequency cao hơn không mặc nhiên đồng nghĩa với value cao hơn. AI để trống kết luận cadence để tôi tự quyết định theo nhịp vận hành thực tế.

Ở Phase 3, AI hỗ trợ cấu trúc activation, engagement, North Star, leading indicators, counter-metrics và retention đủ sáu thành phần từ core action/cadence tôi đã chốt. AI không sử dụng benchmark bên ngoài và đánh dấu các ngưỡng chưa có baseline là nội dung cần hiệu chỉnh bằng dữ liệu thật.

Ở Phase 4, AI hỗ trợ cấu trúc Product Loop thành hai chu kỳ liên tiếp, phân biệt trigger nghiệp vụ với notification, và gợi ý metric hypothesis trỏ về `Daily evaluation coverage`. Tôi rà lại để bảo đảm lý do BQL quay lại xuất phát từ ca mới theo lịch hợp đồng hoặc ca bất thường, không phải từ cơ chế nhắc việc.

Ở Phase 5, AI gợi ý bộ sáu core events và acceptance criteria cho thời điểm ghi nhận, trường dữ liệu bắt buộc, idempotency và cách xử lý timezone. Tôi đối chiếu từng event với metric ở Phase 3, loại các event giao diện không tạo value và giữ bộ tracking ở mức tối thiểu cần thiết.

## AI sai, hời hợt hoặc đề xuất metric sai nature ở đâu?

Đề xuất ban đầu dễ nghiêng về số evaluation hoàn tất như một chỉ số càng cao càng tốt, nhưng cách đo này có thể tăng chỉ vì số ca đầu vào tăng hoặc ca bị chia nhỏ. Tôi không dùng volume thuần làm bằng chứng duy nhất mà đọc NSM cùng `Daily evaluation coverage`. Ngoài ra, việc coi cảnh báo/notification là động lực quay lại là chưa đúng nature của sản phẩm; lý do thật là lịch vận hành tạo ca mới và rủi ro của ca bất thường. Các mức phần trăm mục tiêu hoặc benchmark retention khi chưa có dữ liệu cũng không đủ căn cứ nên tôi không sử dụng.

## Tôi đã tự sửa hoặc quyết định lại điều gì?

Tôi đã tự chọn core action: “BQL hoàn tất đánh giá một ca vệ sinh và ghi nhận kết luận Đạt, Vi phạm hoặc Cần kiểm tra thêm.”

Tôi đã tự xác định cadence chính là hằng ngày vì BQL gom các ca để rà soát cuối ngày; ca có dấu hiệu bất thường có thể được xem ngay khi có cảnh báo nhưng không thay đổi cadence chính.

Tôi chọn `workflow` là loại loop chính vì core action phụ thuộc chuỗi Cleaning Worker → Vendor Supervisor → BQL và lặp theo ngày vận hành. Tôi giữ nhánh `event-response` cho ca bất thường, đồng thời chọn `Daily evaluation coverage` làm metric hypothesis và không đặt mức tăng phần trăm khi chưa có baseline thực tế.

Tôi quyết định dùng cặp `BQL × tòa nhà` làm unit của activation và retention, vì một BQL có thể phụ trách nhiều tòa có cơ hội sử dụng khác nhau. Tôi yêu cầu event completion chỉ xuất hiện sau khi evaluation được lưu hợp lệ, dùng idempotency key để chống ghi trùng, và bổ sung event reopen/audit để đo chất lượng thay vì chỉ đo tốc độ hoặc số lượng.

## Ranh giới sử dụng AI

- Core action cuối cùng do tôi lựa chọn.
- Kết luận cadence do tôi tự viết và bảo vệ.
- Metric hypothesis, rationale và reflection do tôi tự viết.
- AI không được dùng để bịa benchmark hoặc số liệu retention không có nguồn.
