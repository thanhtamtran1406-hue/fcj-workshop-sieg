---
title: "Kiến trúc chi tiết (Architecture & Data Flow)"
date: 2026-11-15
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" alt="Kiến trúc website AUREL trên AWS" style="max-width:100%; width:900px;"></a>

**Luồng dữ liệu** (số thứ tự theo sơ đồ)
1. Khách hàng hoặc admin truy cập website qua HTTPS. AWS Amplify Hosting phục vụ các trang Next.js (static và SSR) và Admin BFF.
2. Với đăng nhập admin, Next.js server xác thực với Amazon Cognito và giữ phiên trong cookie `HttpOnly`, `Secure`, `SameSite`. Access token có hiệu lực 1 giờ.
3. Các yêu cầu đi tới Amazon API Gateway (HTTP API). Route công khai không cần xác thực; route `/admin/*` dùng JWT authorizer với token do Cognito cấp. API Gateway áp dụng CORS đúng origin và throttling.
4. API Gateway gọi AWS Lambda (Node.js 22), nơi kiểm tra dữ liệu đầu vào và giới hạn tần suất. Route admin yêu cầu access token và thuộc nhóm `admin`.
5. Lambda đọc và ghi DynamoDB (thực đơn, đặt bàn, liên hệ, slot và giới hạn tần suất).
6. Sau khi lưu đặt bàn, Lambda gọi Amazon SES để gửi email (best effort; SES đang ở sandbox).
7. Với media, Lambda trả URL ký và admin upload hoặc xem ảnh trực tiếp trên S3 media bucket riêng tư (PUT hiệu lực 120 giây, GET hiệu lực 900 giây).

**Các thành phần hỗ trợ**
- **IAM execution role:** giới hạn Lambda theo ARN: bảng DynamoDB, `uploads/*` trong S3 và SES identity.
- **CloudWatch:** thu thập log và metrics từ API Gateway và Lambda.
- **Triển khai:** người vận hành đẩy mã lên repository nguồn, Amplify Build tự build và publish frontend. Backend được triển khai riêng bằng AWS CloudFormation và SAM, dùng Lambda ZIP lưu trong S3 artifacts bucket riêng.

**Mô hình dữ liệu:** một bảng DynamoDB khóa `pk` và `sk`, chứa `menu`, `reservations`, `contacts`, `slots` (bộ đếm ghế theo khung giờ, tối đa 24 khách mỗi khung) và `limits` (giới hạn tần suất, có TTL).
