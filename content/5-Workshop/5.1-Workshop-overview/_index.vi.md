---
title: "Overview (Tổng quan)"
date: 2026-11-11
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

AUREL Dining & Wine là website nhà hàng minh họa, xây dựng bằng Next.js 15.5.28, React 19, TypeScript và Tailwind CSS. Tên, địa chỉ và giá món là dữ liệu mẫu. Workshop này triển khai website lên AWS: frontend chạy trên AWS Amplify Hosting, backend dựng bằng AWS SAM (API Gateway HTTP API, Lambda Node.js 22, DynamoDB, Cognito, SES và một S3 bucket riêng tư).

**Chức năng của website**
- Trang chủ, Thực đơn (100 món đọc từ API, có lọc, tìm kiếm, phân trang), Gallery, Giới thiệu, Liên hệ.
- Đặt bàn: một đặt bàn mới là yêu cầu chờ xác nhận, chưa phải bàn đã được xác nhận.
- Trang admin: đăng nhập, danh sách đặt bàn với xác nhận/hủy, quản lý thực đơn (CRUD), đọc lời nhắn, upload ảnh lên S3 riêng tư.

**Mã nguồn:** <https://github.com/ndthuy21/aurel-restaurant>

Tài nguyên AWS có thể phát sinh phí theo mức sử dụng; cấu hình này không cam kết nằm trong Free Tier. Các ví dụ dùng Region `ap-southeast-1`.
