---
title: "Overview (Tổng quan)"
date: 2026-11-17
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

AUREL là website nhà hàng minh họa theo phong cách tối và vàng. Khách xem thực đơn 100 món, xem ảnh không gian có ghi nguồn, gửi yêu cầu đặt bàn hoặc lời nhắn. Nhân viên đăng nhập bằng tài khoản Cognito thuộc nhóm `admin` để quản lý dữ liệu. Workshop này là dự án chung của nhóm FCAJ.

**Công nghệ**
- Frontend: Next.js 15.5.28, React 19, TypeScript, Tailwind CSS, Zod, Node.js 22, pnpm 11.25.0.
- Backend tại Singapore (`ap-southeast-1`): API Gateway HTTP API, Lambda (arm64, 256 MB, timeout 20 giây), DynamoDB on-demand, Cognito, S3 và SES. Amplify Hosting phục vụ ứng dụng Next.js có render phía server.

**Quy tắc nghiệp vụ:** yêu cầu đặt bàn mới chỉ là một yêu cầu ở trạng thái `pending`. Nhân viên xác nhận hoặc hủy sau đó, nên gửi form thành công chưa có nghĩa là bàn đã được xác nhận.

**Đầu ra mong đợi**
- Website 7 trang có form hoạt động với backend.
- Thực đơn 100 món với ảnh WebP tự lưu trữ.
- API công khai và API admin có ranh giới quyền rõ ràng.
- Transaction bảo vệ sức chứa theo khung giờ và UUID chống tạo yêu cầu trùng.
- Template hạ tầng, các bước triển khai, kiểm thử, giám sát và hướng dẫn dọn dẹp.

**Giới hạn đã biết:** SES còn ở sandbox, chưa có outbox hay queue cho email, chưa có màn hình MFA cho admin, sức chứa chỉ tính theo giờ bắt đầu, ảnh không gian là tư liệu tham khảo chứ không phải ảnh của một nhà hàng AUREL có thật.

Mã nguồn: <https://github.com/ndthuy21/aurel-restaurant> · Website: <https://main.d2rkhtb7c0k5r7.amplifyapp.com/>
