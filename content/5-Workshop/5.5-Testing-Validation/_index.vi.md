---
title: "Kiểm thử và đo lường"
date: 2026-11-17
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Kết quả kiểm thử
- **Trên máy:** 30 test tự động, kiểm tra TypeScript và build đều đạt; bộ smoke trên bản build cục bộ đạt 41 kiểm tra HTTP (đặt bàn, gửi lại yêu cầu, phân quyền admin, tải ảnh). Transaction DynamoDB được kiểm thử bằng mock.
- **Backend trên AWS:** 14 kiểm tra đạt, gồm access token Cognito, từ chối ID token, lưu và đọc đặt bàn, đổi trạng thái, idempotency, S3 PUT/GET thật và lời nhắn liên hệ.
- **Kiểm tra bản phát hành:** API công khai và API admin đều trả 100 món, 7 trang trả HTTP 200 và ảnh khớp từng byte. Đây là kiểm tra HTTP, không phải tự động hóa trình duyệt.

Các request âm tính ai cũng chạy lại được (kết quả mong đợi ở cuối dòng):
```sh
curl -i "$API_URL/admin/reservations"                              # 401
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{}'         # 400
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{bad-json'  # 400
```
Chưa thực hiện: thử tranh chấp chỗ thật trên DynamoDB, thử tải (mục tiêu p95 dưới 2 giây ở 5 request/giây trong 5 phút) và thử khôi phục theo thời điểm. Các mục này là kế hoạch, không tuyên bố đã đạt.

### Logs
- Access log của API Gateway gồm `requestId`, `requestTime`, `routeKey`, `status`, `responseLength` và `integrationLatency`; không ghi cookie, header Authorization hay nội dung body. Lưu 14 ngày.
- Log group Lambda `/aws/lambda/aurel-backend-api`, cùng log build và SSR của Amplify.
```sh
aws logs tail /aws/lambda/aurel-backend-api --since 10m
```

### Metrics và alarm
Stack `aurel-observability` riêng tạo một dashboard và bốn alarm CloudWatch:

| Alarm | Metric | Điều kiện |
| --- | --- | --- |
| LambdaErrors | Lambda `Errors`, Sum | từ 1 trở lên trong 5 phút |
| LambdaThrottles | Lambda `Throttles`, Sum | từ 1 trở lên trong 5 phút |
| Api5xx | API Gateway `5xx`, Sum | từ 1 trở lên trong 5 phút |
| ApiLatency | API Gateway `Latency`, Average | từ 2000 ms trong 2 cửa sổ 5 phút liên tiếp |

### Alerts
Dữ liệu thiếu được coi là không vi phạm. Alarm chưa có action và chưa có SNS subscription, nên alarm đổi trạng thái trong CloudWatch nhưng chưa gửi email. Bài thử `SetAlarmState` đã chuyển `Api5xx` sang ALARM rồi về OK; đây là mô phỏng, không phải sự cố thật.

### Khi alarm kích hoạt
| Alarm | Kiểm tra đầu tiên |
| --- | --- |
| LambdaErrors | Mở log group của Lambda, tìm lần gọi lỗi theo request ID và xem có vừa triển khai lại không |
| LambdaThrottles | Kiểm tra số lần thực thi đồng thời, hạn mức tài khoản và xem trong access log có đợt tăng đột biến không |
| Api5xx | Lọc access log các dòng 5xx (`routeKey`, `status`), rồi lần theo `requestId` sang log Lambda |
| ApiLatency | So sánh `integrationLatency` với tổng độ trễ trong access log, sau đó kiểm tra thời gian chạy Lambda và các lệnh gọi DynamoDB |
