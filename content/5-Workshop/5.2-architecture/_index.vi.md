---
title: "Kiến trúc và luồng dữ liệu"
date: 2026-11-26
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

<a href="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" target="_blank"><img src="/fcj-workshop-sieg/images/5-Workshop/5.3-Architecture/architecture-vi.png" alt="Kiến trúc website AUREL trên AWS" style="max-width:100%; width:900px;"></a>

**Luồng của khách**
1. Trình duyệt tải trang, script, ảnh và font từ Amplify Hosting qua HTTPS.
2. GET /menu đi thẳng tới API Gateway; Lambda truy vấn partition menu và trả các món đang phục vụ.
3. POST /reservations được kiểm tra schema, đồng ý điều khoản, trường honeypot, giới hạn theo IP và email, và ngày giờ theo múi giờ Việt Nam.
4. DynamoDB lưu yêu cầu và tăng bộ đếm ghế trong một transaction (tối đa 24 ghế mỗi khung giờ). Gửi lại cùng UUID sẽ trả về yêu cầu đã lưu mà không cộng thêm ghế.
5. Sau khi lưu yêu cầu mới, Lambda cố gửi email tiếp nhận qua SES. Nếu gửi lỗi, yêu cầu vẫn được lưu. SES đang ở sandbox nên chỉ địa chỉ đã xác minh hoặc mailbox simulator nhận được thư.
6. POST /contact lưu lời nhắn. Dữ liệu của khách không đọc được qua API công khai.

**Luồng admin và media**
- Trình duyệt gửi thông tin đăng nhập tới Next.js BFF, BFF xác thực với Cognito và giữ access token trong cookie HttpOnly, SameSite=Strict (Secure khi triển khai). BFF chuyển token trong header Authorization. JWT authorizer của API Gateway kiểm tra chữ ký, issuer, audience và thời hạn; Lambda kiểm tra thêm token_use=access và nhóm admin.
- Ảnh món (JPEG, PNG hoặc WebP, tối đa 2 MB) đi qua BFF và API Gateway tới Lambda, Lambda lưu vào tiền tố menu-images/ của bucket S3 riêng tư và ghi đường dẫn vào DynamoDB. Khi khách xem thực đơn, Lambda cấp URL GET ký sẵn cho phiên bản ảnh hiện hành.
- Mũi tên 7 trong sơ đồ: Lambda ghi ảnh món vào S3 media bucket riêng tư bằng IAM execution role, và trình duyệt đọc ảnh bằng URL GET ký sẵn. Trang thử nghiệm riêng /admin/media dùng URL PUT (120 giây) và GET (900 giây) ký sẵn, tệp trong uploads/ hết hạn sau 30 ngày.

**Mô hình DynamoDB:** một bảng on-demand, bật mã hóa và khôi phục theo thời điểm.

| pk | sk | Nội dung |
| --- | --- | --- |
| menu | ID món | Món trong thực đơn, truy vấn công khai |
| reservations | UUID | Dữ liệu và trạng thái đặt bàn, chỉ admin đọc |
| contacts | UUID | Lời nhắn, chỉ admin đọc |
| slots | ngày#giờ | Bộ đếm ghế, cập nhật có điều kiện |
| limits | mã băm + khung thời gian | Bộ đếm yêu cầu, có TTL |

**Luồng triển khai:** git push lên nhánh main của repository CodeCommit riêng tư kích hoạt Amplify build và phát hành frontend cùng BFF. Backend được triển khai riêng bằng CloudFormation; một bucket S3 riêng chứa gói Lambda. CloudWatch thu thập log và metrics.
