---
title: "Kiến trúc chi tiết (Architecture & Data Flow)"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

<img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture.png" alt="Kiến trúc AUREL trên AWS" style="max-width:100%; width:800px;">

**Luồng dữ liệu**
1. Khách truy cập website Next.js được host trên AWS Amplify Hosting.
2. Các yêu cầu công khai (thực đơn, đặt bàn, liên hệ) đi thẳng từ trình duyệt tới API Gateway qua `NEXT_PUBLIC_API_BASE_URL`, rồi tới Lambda và DynamoDB. Sau khi lưu đặt bàn mới, Lambda cố gửi thư nhận yêu cầu qua SES.
3. Đăng nhập admin do Next.js server thực hiện với Cognito user pool (`USER_PASSWORD_AUTH`). Backend-for-frontend (BFF) lưu access token trong cookie `HttpOnly`, `SameSite=Strict` (`Secure` ở production), không lưu vào localStorage.
4. Các yêu cầu admin đi qua BFF `/api/admin/*` tới API Gateway kèm access token. API Gateway kiểm tra JWT; Lambda còn yêu cầu `token_use=access` và thuộc nhóm `admin`.
5. Với upload media, Lambda trả URL ký: PUT hiệu lực 2 phút và xem trước 15 phút. Ảnh được lưu trong S3 bucket riêng tư.

**Mô hình dữ liệu:** một bảng DynamoDB khóa `pk` và `sk`, chứa `menu`, `reservations`, `contacts`, `slots` (bộ đếm ghế theo khung giờ, tối đa 24 khách mỗi khung) và `limits` (giới hạn tần suất, có TTL).
