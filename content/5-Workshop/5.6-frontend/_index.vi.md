---
title: "Bước 3 — Frontend và CI/CD"
date: 2026-11-26
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

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
| `DATA_MODE` | `aws` |
| `API_BASE_URL`, `NEXT_PUBLIC_API_BASE_URL` | `ApiUrl`, không có dấu `/` cuối |
| `COGNITO_CLIENT_ID`, `COGNITO_USER_POOL_ID` | outputs của backend |
| `APP_AWS_REGION` | `ap-southeast-1` |
| `APP_ORIGIN` | origin chính xác của frontend |

Sau đó đặt origin thật của frontend vào backend và chạy một Amplify job:
```sh
aws cloudformation deploy --stack-name aurel-backend --template-file packaged-backend.yaml \
  --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
  --parameter-overrides FrontendOrigin="$FRONTEND_ORIGIN" SenderEmail="$SENDER_EMAIL"
aws amplify start-job --app-id "$APP_ID" --branch-name main --job-type RELEASE
aws amplify list-jobs --app-id "$APP_ID" --branch-name main --max-results 5
```
Chỉ coi là triển khai thành công khi trạng thái job là `SUCCEED`. File ZIP chỉ nạp repository một lần; các lần cập nhật sau là Git push lên `main`, Amplify tự build.

**Kết quả mong đợi:** Amplify job kết thúc với trạng thái `SUCCEED`, website mở được ở địa chỉ Amplify, thực đơn tải từ API không có lỗi CORS và admin đăng nhập được.
