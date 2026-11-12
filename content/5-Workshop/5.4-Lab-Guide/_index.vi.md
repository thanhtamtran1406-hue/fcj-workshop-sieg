---
title: "Các bước triển khai thực hành (Step-by-step Lab Guide)"
date: 2026-11-11
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Bước 1. Chạy thử trên máy (kiểm tra tùy chọn)
```sh
cd aurel-restaurant
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
```
Mở http://127.0.0.1:3000. `pnpm setup` tạo `.env.local` với mật khẩu và khóa phiên ngẫu nhiên. Không đưa file này vào Git.

### Bước 2. Triển khai backend AWS
```sh
aws configure sso
aws sso login --profile YOUR_PROFILE
export AWS_PROFILE=YOUR_PROFILE
export AWS_REGION=ap-southeast-1
pnpm build:lambda
sam validate --lint --template-file infra/template.yaml
sam build --template-file infra/template.yaml
sam deploy --guided
```
Các lựa chọn khi deploy guided lần đầu:
- Stack name: `aurel-backend`; Region: `ap-southeast-1`.
- `FrontendOrigin`: tạm dùng `https://pending.invalid` (sẽ cập nhật ở Bước 7).
- `SenderEmail`: email thật thuộc quyền sở hữu của bạn. Mở thư xác minh từ AWS và xác nhận ngay trong lúc stack đang tạo.
- Cho phép SAM tạo IAM role.

Đọc outputs của stack (`ApiUrl`, `TableName`, `MediaBucket`, `UserPoolId`, `CognitoClientId`):
```sh
aws cloudformation describe-stacks --stack-name aurel-backend \
  --query 'Stacks[0].Outputs' --output table
```

### Bước 3. Nạp thực đơn
```sh
export TABLE_NAME=YOUR_TABLE_OUTPUT
node dist/seed-aws.mjs
```
Script chỉ thêm các ID chưa tồn tại và có thể chạy lại.

### Bước 4. Tạo admin trong Cognito
```sh
export USER_POOL_ID=YOUR_POOL_OUTPUT
export ADMIN_EMAIL=YOUR_REAL_EMAIL
node scripts/create-admin.mjs
```
Mật khẩu ít nhất 14 ký tự gồm chữ hoa, chữ thường, số và ký hiệu. Script thêm user vào nhóm `admin`.

### Bước 5. Chuẩn bị SES
Trong SES sandbox, cả người gửi và người nhận thử nghiệm đều phải được xác minh. Để gửi cho khách thật, cần xin SES production access ở cùng Region.

### Bước 6. Triển khai frontend trên Amplify Hosting
1. Đẩy thư mục dự án làm gốc của repository GitHub (không đẩy `node_modules`, `.next`, `.env.local`, `.data`, `dist`, `.aws-sam`).
2. Trong Amplify, chọn **Create new app**, kết nối repository và branch, chọn Node 22 trong build image. Repository đã có sẵn `amplify.yml`.
3. Thêm các biến môi trường sau trước khi build (không thêm AWS access key):

| Biến | Giá trị |
| --- | --- |
| `DATA_MODE` | `aws` |
| `API_BASE_URL` | output `ApiUrl` |
| `NEXT_PUBLIC_API_BASE_URL` | cùng `ApiUrl`, không dấu `/` cuối |
| `COGNITO_CLIENT_ID` | output `CognitoClientId` |
| `COGNITO_USER_POOL_ID` | output `UserPoolId` |
| `APP_AWS_REGION` | `ap-southeast-1` |
| `APP_ORIGIN` | origin HTTPS chính xác của frontend, không dấu `/` cuối |

Origin mặc định có dạng `https://main.APP_ID.amplifyapp.com`. Nếu build đã chạy trước khi đủ biến, hãy điền biến rồi chạy lại build.

### Bước 7. Cập nhật CORS origin của backend
Chạy lại `sam deploy --guided` và thay `FrontendOrigin` bằng URL Amplify thật. Deploy lại frontend nếu có đổi biến, vì các giá trị `NEXT_PUBLIC_*` được đóng gói lúc build.
