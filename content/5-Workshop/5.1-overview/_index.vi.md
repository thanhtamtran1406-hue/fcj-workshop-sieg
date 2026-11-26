---
title: "Tổng quan và điều kiện tiên quyết"
date: 2026-11-26
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

AUREL là website nhà hàng minh họa theo phong cách tối và vàng. Khách xem thực đơn 100 món, xem ảnh không gian có ghi nguồn, gửi yêu cầu đặt bàn hoặc lời nhắn. Nhân viên đăng nhập bằng tài khoản Cognito thuộc nhóm admin để quản lý dữ liệu. Workshop này là dự án chung của nhóm FCAJ.

**Công nghệ**
- Frontend: Next.js 15.5.28, React 19, TypeScript, Tailwind CSS, Zod, Node.js 22, pnpm 11.25.0.
- Backend tại Singapore (ap-southeast-1): API Gateway HTTP API, Lambda (arm64, 256 MB, timeout 20 giây), DynamoDB on-demand, Cognito, S3 và SES. Amplify Hosting phục vụ ứng dụng Next.js có render phía server.

**Quy tắc nghiệp vụ:** yêu cầu đặt bàn mới chỉ là một yêu cầu ở trạng thái pending. Nhân viên xác nhận hoặc hủy sau đó, nên gửi form thành công chưa có nghĩa là bàn đã được xác nhận.

**Tiêu chí hoàn thành (đầu ra mong đợi)**
- Website 7 trang có form hoạt động với backend.
- Thực đơn 100 món với ảnh WebP tự lưu trữ.
- API công khai và API admin có ranh giới quyền rõ ràng.
- Transaction bảo vệ sức chứa theo khung giờ và UUID chống tạo yêu cầu trùng.
- Template hạ tầng, các bước triển khai, kiểm thử, giám sát và hướng dẫn dọn dẹp.

**Giới hạn đã biết:** SES còn ở sandbox, chưa có outbox hay queue cho email, chưa có màn hình MFA cho admin, sức chứa chỉ tính theo giờ bắt đầu, ảnh không gian là tư liệu tham khảo chứ không phải ảnh của một nhà hàng AUREL có thật.

Mã nguồn: <https://github.com/ndthuy21/aurel-restaurant> · Website: <https://main.d2rkhtb7c0k5r7.amplifyapp.com/>

### Điều kiện tiên quyết

**Tài khoản và quyền truy cập**
- Một tài khoản AWS lab riêng ở ap-southeast-1. Không dùng lại tên stack của dự án khác.
- Quyền với CloudFormation, IAM, DynamoDB, S3, Cognito, SES, Lambda, API Gateway, Amplify, CodeCommit và CloudWatch.
- Một địa chỉ email thuộc quyền sở hữu của bạn, dùng làm SenderEmail và được xác minh trong SES.

**Công cụ**
- Node.js 22.13 trở lên và pnpm 11.25.0 (Corepack).
- AWS CLI v2 và Python 3 (script phụ trợ tạo admin và giám sát).
- Git. Trên Windows, chạy lệnh shell trong WSL hoặc Git Bash, hoặc đổi cú pháp biến sang PowerShell.

**Quy tắc:** không đưa AWS key hay mật khẩu vào mã nguồn, và dùng ID tài nguyên của chính bạn thay vì ID của bản demo.
