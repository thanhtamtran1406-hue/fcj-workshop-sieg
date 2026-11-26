---
title: "Bước 2 — Triển khai backend AWS"
date: 2026-11-26
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Backend được triển khai bằng CloudFormation với hai stack: `aurel-artifacts` cho bucket chứa gói triển khai và `aurel-backend` cho API, cơ sở dữ liệu, xác thực và nơi lưu media.

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
Script seed chỉ thêm các món còn thiếu và có thể chạy lại. Script admin hỏi mật khẩu tương tác, thêm user vào nhóm `admin` và không cho phép tự đăng ký. Kiểm tra nhanh:
```sh
curl -i "$API_URL/menu"                 # 200
curl -i "$API_URL/admin/reservations"   # 401
```

**Kết quả mong đợi:** stack `aurel-backend` đạt `CREATE_COMPLETE` và in ra các outputs (`ApiUrl`, bảng, bucket, user pool). `/menu` trả 200 với 100 món đã nạp, còn `/admin/reservations` trả 401 khi không có token.
