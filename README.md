# Track 1 — Day 20: Metrics Pack

## Thông tin học viên

- **Họ và tên:** Phạm Hương Giang
- **Mã học viên:** 2A202602359
- **Dự án:** FixIt Agent — AI hỗ trợ giám sát tuân thủ hợp đồng dịch vụ vệ sinh
- **Metrics Pack:** [Bản làm việc trong repository](./metrics-pack.md)
- **Tệp trình bày:** [Day20 Metrics Pack — 8 slides](./Day20_Metrics_Pack_PhamHuongGiang.pptx)
- **Link tệp trình bày đã cấp quyền xem:** [Google Drive — Day 20 Metrics Pack](https://drive.google.com/file/d/1C-M2yo7Cs5GMXBSspqSpZXbv8Y3QsOeJ/view?usp=sharing)

## Điều tôi mang về áp dụng cho dự án thật

Tôi chuyển từ cách nhìn “người dùng mở màn hình và xem kết quả AI” sang một core action có completion rule rõ ràng: BQL hoàn tất đánh giá một ca và lưu kết luận có căn cứ. Tôi chọn cadence hằng ngày theo lịch vận hành thay vì cố tăng frequency, dùng `Daily evaluation coverage` để chuẩn hóa theo số ca thực sự đủ điều kiện, và định nghĩa retention chỉ trên những ngày có cơ hội đánh giá. Product loop quay lại nhờ ca mới theo lịch hợp đồng hoặc ca bất thường, không phụ thuộc notification. Với tracking, tôi chỉ giữ sáu event gắn trực tiếp với metric và thêm quy tắc transition/idempotency để dữ liệu không bị tăng giả do reload hoặc retry.

## Trạng thái hoàn thành

- [x] Tệp trình bày đã cấp quyền xem
- [x] Metrics Pack đủ các mục 00–06
- [x] Qua Gate 1 — Core Action
- [x] Qua Gate 2 — Cadence
- [x] Qua Gate 3 — Metric & Retention
- [x] Qua Gate 4 — Loop
- [x] Qua Gate 5 — Tracking và tự soi lỗi
- [x] Hoàn thành `ai-support-log.md`
