---
title: "Prerequisite (Điều kiện tiên quyết)"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

**Tài khoản**
- Tài khoản AWS.
- Tài khoản GitHub và một repository chứa mã nguồn (thư mục dự án phải là gốc của repository).
- Một địa chỉ email thật thuộc quyền sở hữu của bạn, dùng làm `SenderEmail` cho SES.

**Công cụ**
- Node.js 22.13 trở lên và pnpm 11.25.0 (có thể bật bằng Corepack).
- AWS CLI v2 và AWS SAM CLI.

**Quyền hạn**
- Quyền với CloudFormation, IAM, DynamoDB, S3, Cognito, SES, Lambda và API Gateway.
- Cho phép SAM tạo IAM role khi được hỏi.
