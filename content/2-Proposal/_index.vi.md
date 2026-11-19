---
title: "Bản đề xuất"
date: 2026-11-17
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

### 1. Tổng quan bài toán và giải pháp
Nhà hàng nhỏ thường giới thiệu món trên mạng xã hội nhưng nhận đặt bàn qua nhiều kênh rời rạc. Nhân viên khó tổng hợp yêu cầu, khách không biết yêu cầu đã được tiếp nhận chưa, và việc đổi thực đơn phải sửa thủ công nhiều nơi. AUREL gom các luồng này vào một ứng dụng web có dữ liệu tập trung, xây trên các dịch vụ serverless của AWS (Next.js, API Gateway, Lambda, DynamoDB, Cognito, S3 và SES). Đây là dự án nhóm, có mã nguồn và bản triển khai đang chạy: https://main.d2rkhtb7c0k5r7.amplifyapp.com/

### 2. Mục tiêu và phạm vi
**Mục tiêu:** giới thiệu nhà hàng và thực đơn, nhận yêu cầu đặt bàn có trạng thái rõ ràng, cho nhân viên quản lý thực đơn và yêu cầu, và cung cấp cho người vận hành log, metrics và alarm.
**Trong phạm vi:** các trang chủ, thực đơn, không gian, giới thiệu, liên hệ, đặt bàn và admin; CRUD thực đơn; cập nhật trạng thái đặt bàn; tải ảnh món lên S3; email tiếp nhận trong giới hạn SES sandbox.
**Ngoài phạm vi:** thanh toán, POS, sơ đồ bàn vật lý, thời lượng dùng bữa chồng lấn, tài khoản khách, nhiều chi nhánh và SLA thương mại.

### 3. Vấn đề cần giải quyết
Đặt bàn nằm rải rác ở nhiều kênh, khách không nhận được xác nhận tiếp nhận rõ ràng, và nhân viên sửa thực đơn thủ công. Giải pháp cung cấp một nơi để nhận yêu cầu, bảo vệ sức chứa, cập nhật thực đơn và quan sát hệ thống.

### 4. Kiến trúc giải pháp sơ bộ
<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" alt="Kiến trúc website AUREL trên AWS" style="max-width:100%; width:900px;"></a>

Khách tải website từ Amplify Hosting và gọi API Gateway cho thực đơn, đặt bàn, liên hệ. Đăng nhập admin đi qua Next.js BFF tới Cognito, và các yêu cầu admin qua JWT authorizer. Lambda lưu dữ liệu vào DynamoDB, gửi email tiếp nhận qua SES và giữ ảnh món trong bucket S3 riêng tư. CloudWatch thu thập log và metrics. Chi tiết xem mục 5.3.

### 5. Kế hoạch và timeline thực hiện
| Giai đoạn | Đầu ra | Mốc |
| --- | --- | --- |
| Thiết kế và phát triển local | Các trang, service layer, kiểm tra đầu vào, test | Có mã nguồn và kết quả kiểm thử, 02–03/11 |
| Triển khai AWS | Ba stack ứng dụng, admin Cognito, dữ liệu seed | Có bằng chứng ngày 03/11 |
| Hoàn thiện nội dung | Font tiếng Việt, 100 món, ảnh không gian | Amplify jobs 3–5 thành công |
| Tài liệu và vận hành | Report song ngữ, sơ đồ, giám sát, dự toán | Có bằng chứng CloudWatch và Pricing Calculator |
| Rà soát cá nhân và nộp | Sơ đồ riêng, ảnh event, link blog | Đang thực hiện |

### 6. Bảng dự toán ngân sách và chi phí (Cost Estimation)
Giả định: mỗi tháng 10.000 lượt truy cập, 100.000 API request và 100.000 SSR request, 20 GB nội dung phân phối, Lambda 256 MB ở 200 ms, 1.000 email và 5 admin; Region Singapore, USD, chưa thuế và chưa tính Free Tier.

| Hạng mục | Chi phí mỗi tháng (USD) |
| --- | --- |
| Workload estimate trên AWS Pricing Calculator (20 dòng sử dụng, 8 dịch vụ) | khoảng 6,00 |
| Mô hình ngân sách độc lập, gồm khoản dự phòng transfer, dashboard và CodeCommit | 12,17 |
| Ngân sách độc lập cộng 25% dự phòng | 15,21 |

Hai con số khác nhau vì mô hình độc lập thêm các khoản dự phòng (dashboard, truyền dữ liệu, CodeCommit, build report) và dùng đơn giá thận trọng. Cả hai đều không phải hóa đơn, và con số Calculator chỉ gồm 20 dòng đã nhập. Chưa tính: WAF, NAT, SQS, tên miền riêng, hỗ trợ trả phí, khôi phục hay export DynamoDB.

### 7. Rủi ro và phương án giảm thiểu
| Rủi ro | Ảnh hưởng | Phương án giảm thiểu |
| --- | --- | --- |
| SES sandbox | Khách chưa xác minh không nhận email | Lưu yêu cầu trước, nêu rõ giới hạn, xin production access khi phù hợp |
| Gửi email lỗi sau khi ghi database | Có yêu cầu nhưng không có email | Nhân viên vẫn thấy yêu cầu; đề xuất outbox kèm SQS và DLQ |
| Tranh chấp chỗ | Nhận quá sức chứa | Transaction với cập nhật bộ đếm có điều kiện |
| Lạm dụng API | Tăng tải và chi phí | Kiểm tra đầu vào, honeypot, limiter và throttling; chưa có WAF |
| Quyền tài khoản quá rộng | Phạm vi ảnh hưởng lớn hơn | Runtime role theo tài nguyên; vận hành ngoài root user |
| Tài nguyên Retain | Tiếp tục tính phí sau khi xóa stack | Kiểm kê, sao lưu và dọn thủ công có kiểm soát |
