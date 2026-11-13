---
title: "Kiến trúc chi tiết (Architecture & Data Flow)"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" alt="Kiến trúc AUREL trên AWS" style="max-width:100%; width:1200px;"></a>

**Luồng dữ liệu** (số thứ tự theo sơ đồ)
1. Khách truy cập website qua HTTPS. AWS Amplify Hosting phục vụ trang Next.js static và SSR qua CDN.
2. Các yêu cầu công khai (`GET /menu`, `POST /reservations`, `/contact`) đi từ trình duyệt tới API Gateway, rồi tới Lambda.
3. Quản trị viên mở dashboard qua HTTPS. Next.js Admin BFF (`/api/auth/*` và `/api/admin/*`) đăng nhập với Cognito và giữ phiên trong cookie `HttpOnly`, `Secure`, `SameSite`. Access token có hiệu lực 1 giờ.
4. BFF gửi Bearer access token tới API Gateway. Các route `/admin/*` dùng JWT authorizer (xác thực JWKS với Cognito); route công khai không cần xác thực. API Gateway áp dụng CORS đúng origin và throttling.
5. Lambda (Node.js 22, arm64, 256 MB) kiểm tra dữ liệu, giới hạn tần suất và truy vấn DynamoDB. Route admin yêu cầu access token và thuộc nhóm `admin`. Execution role của Lambda được giới hạn theo ARN: bảng DynamoDB, `uploads/*` trong S3 và SES identity.
6. Với media, Lambda trả URL ký; admin upload (PUT, 120 giây) hoặc xem (GET, 900 giây) file trực tiếp trên S3, file không đi qua Lambda. Bucket riêng tư, bật Block Public Access và SSE-S3.
7. Sau khi lưu đặt bàn, Lambda gọi SES `SendEmail` (best effort, SES đang ở sandbox). Chưa có queue để gửi lại khi thư lỗi.
8. Triển khai: người vận hành đẩy mã lên repository nguồn, Amplify tự build và publish frontend. Backend được triển khai riêng bằng AWS CLI và SAM qua CloudFormation, dùng Lambda ZIP lưu trong S3 artifacts bucket riêng.

**Mô hình dữ liệu:** một bảng DynamoDB khóa `pk` và `sk`, chứa `menu`, `reservations`, `contacts`, `slots` (bộ đếm ghế theo khung giờ, tối đa 24 khách mỗi khung) và `limits` (giới hạn tần suất, có TTL).
