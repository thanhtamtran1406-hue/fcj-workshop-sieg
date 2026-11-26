---
title: "Logs, Metrics, Alarms và runbook"
date: 2026-11-26
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

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
