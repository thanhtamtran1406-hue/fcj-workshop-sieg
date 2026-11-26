---
title: "Bảo mật và Well-Architected"
date: 2026-11-26
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

### Các biện pháp bảo mật trong thiết kế
| Khía cạnh | Biện pháp |
| --- | --- |
| Danh tính | Cognito user pool có nhóm `admin`, không cho tự đăng ký; user admin được tạo bằng script hỏi mật khẩu khi chạy |
| Phân quyền | JWT authorizer cho `/admin/*` (chữ ký, issuer, audience, thời hạn); Lambda kiểm tra thêm `token_use=access` và nhóm `admin`, nên ID token bị từ chối |
| Phiên đăng nhập | Cookie `HttpOnly`, `SameSite=Strict`, `Secure` do Next.js BFF xử lý; không lưu token trong localStorage |
| Đặc quyền tối thiểu | Execution role của Lambda giới hạn theo ARN: bảng DynamoDB, các tiền tố ảnh trong bucket S3 riêng tư và SES identity |
| Dữ liệu lưu trữ | DynamoDB có mã hóa và khôi phục theo thời điểm; bucket S3 riêng tư bật Block Public Access và SSE-S3 |
| Dữ liệu truyền tải | HTTPS qua Amplify Hosting và API Gateway |
| Đầu vào và lạm dụng | Kiểm tra schema, đồng ý điều khoản, trường honeypot, giới hạn theo IP và email bằng bộ đếm có TTL, throttling ở API Gateway, CORS đúng origin của frontend |
| Riêng tư của log | Access log không lưu cookie, header Authorization hay nội dung body |

### Góc nhìn Well-Architected
| Trụ cột | Dự án đã làm | Khoảng trống còn lại |
| --- | --- | --- |
| Vận hành xuất sắc | Template CloudFormation, access log, dashboard, bốn alarm và runbook | Action của alarm chưa nối với SNS |
| Bảo mật | Các biện pháp ở trên | Chưa có WAF, chưa có màn hình MFA cho admin, SES còn ở sandbox |
| Độ tin cậy | DynamoDB on-demand có khôi phục theo thời điểm, đặt bàn idempotent, transaction cho sức chứa | Chưa thử khôi phục, chưa có hàng đợi email, chỉ một Region |
| Hiệu quả hiệu năng | Lambda arm64 256 MB, CDN trên Amplify Hosting, dung lượng on-demand | Chưa thử tải (mục tiêu p95 dưới 2 giây) |
| Tối ưu chi phí | Dịch vụ tính theo mức dùng, log lưu 14 ngày, hai bản dự toán | Chưa đối chiếu dự toán với hóa đơn thật |
| Bền vững | Không có máy chủ chạy nhàn rỗi; hàm arm64 | Chưa đo đạc |
