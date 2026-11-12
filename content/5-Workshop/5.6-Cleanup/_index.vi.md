---
title: "Dọn dẹp tài nguyên (Clean-up)"
date: 2026-11-11
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

1. Xóa Amplify app khi không còn sử dụng.
2. Xóa backend stack:
```sh
sam delete --stack-name aurel-backend
```
3. DynamoDB và S3 có chính sách Retain nên vẫn tồn tại sau khi xóa stack và có thể tiếp tục bị tính phí. Sau khi sao lưu và xác nhận không cần dữ liệu, xóa thủ công bảng và bucket (dọn object trong bucket trước).
4. Log group, SES identity và Cognito trong stack không được Retain. Kiểm tra CloudFormation deletion events để biết tài nguyên nào còn lại.
