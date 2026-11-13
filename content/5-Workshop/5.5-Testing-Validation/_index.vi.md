---
title: "Kiểm thử & đo lường (Testing & Validation)"
date: 2026-11-11
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Kiểm tra trên máy
```sh
pnpm check          # TypeScript, unit/integration tests, bundle Lambda + seed
pnpm build
pnpm test:smoke     # server riêng ở cổng 3100, dữ liệu tạm
```

### Kiểm tra trên AWS sau khi triển khai
1. `/menu` trả về 100 món sau khi nạp dữ liệu, và tab Network của trình duyệt không có lỗi CORS.
2. Đặt bàn bằng email đã xác minh trong SES sandbox; kiểm tra bản ghi DynamoDB, thư SES và trạng thái `pending`.
3. Đăng nhập admin thuộc nhóm, xác nhận một đặt bàn, tải lại trang và đối chiếu với database.
4. User không thuộc nhóm và token hết hạn phải bị từ chối; người dùng công khai không đọc được danh sách đặt bàn và lời nhắn.
5. Thêm, sửa, ẩn, xóa một món; trang thực đơn phản ánh đúng dữ liệu từ API.
6. Tải một ảnh thử ở tab S3; PUT thành công và xem trước hiển thị. Bucket không có quyền đọc công khai.
7. Kiểm tra log Lambda trong CloudWatch và log build, SSR của Amplify, không chỉ dựa vào thông báo trên frontend.

### Logs
- Log Lambda trong CloudWatch, lưu 14 ngày (lỗi gửi thư được ghi theo mã yêu cầu, không ghi nội dung email).
- Log build và SSR của Amplify.

### Metrics
- Basic metrics trong CloudWatch cho Lambda và Amplify.

### Alerts
- Chưa cấu hình alarm riêng cho AUREL.
