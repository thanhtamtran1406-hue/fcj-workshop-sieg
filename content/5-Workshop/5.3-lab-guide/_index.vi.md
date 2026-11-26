---
title: "Các bước triển khai thực hành"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Bước 1 — Chạy và kiểm tra trên máy

Hãy chạy ứng dụng trên máy của bạn trước, để biết mã nguồn, các bài kiểm thử và mô hình dữ liệu hoạt động tốt rồi mới tạo tài nguyên trên AWS.

```sh
pnpm install --frozen-lockfile
pnpm setup
pnpm dev
pnpm check && pnpm build && pnpm test:smoke
```
pnpm setup tạo file .env.local cục bộ; không đưa file này vào Git.

**Kết quả mong đợi:** pnpm check và pnpm build chạy không lỗi, bộ smoke đạt, và website mở được trên máy với thực đơn đã tải.

### Bước 2 — Triển khai backend AWS

Backend được triển khai bằng CloudFormation với hai stack: aurel-artifacts cho bucket chứa gói triển khai và aurel-backend cho API, cơ sở dữ liệu, xác thực và nơi lưu media.

```sh
export AWS_PROFILE=aurel-lab AWS_REGION=ap-southeast-1
export SENDER_EMAIL='EMAIL_CUA_BAN_DA_XAC_MINH'
pnpm build:lambda
aws cloudformation deploy --stack-name aurel-artifacts --template-file infra/artifacts.yaml
export ARTIFACT_BUCKET=$(aws cloudformation describe-stacks --stack-name aurel-artifacts \
  --query 'Stacks[0].Outputs[?OutputKey==`BucketName`].OutputValue | [0]' --output text)
aws cloudformation package --template-file infra/template.yaml \
  --s3-bucket "$ARTIFACT_BUCKET" --s3-prefix lambda --output-template-file packaged-backend.yaml
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin=https://pending.invalid SenderEmail="$SENDER_EMAIL"
aws cloudformation describe-stacks --stack-name aurel-backend --query 'Stacks[0].Outputs' --output table
```
Xác nhận email xác minh từ AWS ngay trong lúc stack đang tạo. Sau đó nạp thực đơn và tạo admin:
```sh
export TABLE_NAME=OUTPUT_TEN_BANG
node dist/seed-aws.mjs
python3 bootstrap-admin.py --pool USER_POOL_ID_CUA_BAN --email EMAIL_ADMIN
```
Script seed chỉ thêm các món còn thiếu và có thể chạy lại. Script admin hỏi mật khẩu tương tác, thêm user vào nhóm admin và không cho phép tự đăng ký. Kiểm tra nhanh:
```sh
curl -i "$API_URL/menu"                 # 200
curl -i "$API_URL/admin/reservations"   # 401
```

**Kết quả mong đợi:** stack aurel-backend đạt CREATE_COMPLETE và in ra các outputs (ApiUrl, bảng, bucket, user pool). /menu trả 200 với 100 món đã nạp, còn /admin/reservations trả 401 khi không có token.

### Bước 3 — Frontend và CI/CD

```sh
python3 scripts/package-source.py --output ../aurel-codecommit-source.zip
aws s3 cp ../aurel-codecommit-source.zip "s3://$ARTIFACT_BUCKET/source/aurel-lab.zip"
aws cloudformation deploy --stack-name aurel-frontend --template-file infra/amplify-codecommit.yaml \
  --capabilities CAPABILITY_IAM --parameter-overrides SourceBucket="$ARTIFACT_BUCKET" \
  SourceKey=source/aurel-lab.zip ApiUrl="$API_URL" CognitoClientId="$COGNITO_CLIENT_ID" \
  CognitoUserPoolId="$USER_POOL_ID"
```
Template tạo repository CodeCommit, role chỉ đọc và Amplify app. Dùng các biến môi trường sau (không đưa mật khẩu admin, session secret hay AWS key):

| Biến | Giá trị |
| --- | --- |
| DATA_MODE | aws |
| API_BASE_URL, NEXT_PUBLIC_API_BASE_URL | ApiUrl, không có dấu / cuối |
| COGNITO_CLIENT_ID, COGNITO_USER_POOL_ID | outputs của backend |
| APP_AWS_REGION | ap-southeast-1 |
| APP_ORIGIN | origin chính xác của frontend |

Sau đó đặt origin thật của frontend vào backend và chạy một Amplify job:
```sh
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin="$FRONTEND_ORIGIN" SenderEmail="$SENDER_EMAIL"
aws amplify start-job --app-id "$APP_ID" --branch-name main --job-type RELEASE
aws amplify list-jobs --app-id "$APP_ID" --branch-name main --max-results 5
```
Chỉ coi là triển khai thành công khi trạng thái job là SUCCEED. File ZIP chỉ nạp repository một lần; các lần cập nhật sau là Git push lên main, Amplify tự build.

**Kết quả mong đợi:** Amplify job kết thúc với trạng thái SUCCEED, website mở được ở địa chỉ Amplify, thực đơn tải từ API không có lỗi CORS và admin đăng nhập được.
