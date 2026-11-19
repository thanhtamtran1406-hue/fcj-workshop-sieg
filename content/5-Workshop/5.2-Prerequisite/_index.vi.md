---
title: "Prerequisite (Điều kiện tiên quyết)"
date: 2026-11-17
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

**Tài khoản và quyền truy cập**
- Một tài khoản AWS lab riêng ở `ap-southeast-1`. Không dùng lại tên stack của dự án khác.
- Quyền với CloudFormation, IAM, DynamoDB, S3, Cognito, SES, Lambda, API Gateway, Amplify, CodeCommit và CloudWatch.
- Một địa chỉ email thuộc quyền sở hữu của bạn, dùng làm `SenderEmail` và được xác minh trong SES.

**Công cụ**
- Node.js 22.13 trở lên và pnpm 11.25.0 (Corepack).
- AWS CLI v2 và Python 3 (script phụ trợ tạo admin và giám sát).
- Git. Trên Windows, chạy lệnh shell trong WSL hoặc Git Bash, hoặc đổi cú pháp biến sang PowerShell.

**Quy tắc:** không đưa AWS key hay mật khẩu vào mã nguồn, và dùng ID tài nguyên của chính bạn thay vì ID của bản demo.
