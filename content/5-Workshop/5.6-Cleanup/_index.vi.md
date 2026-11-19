---
title: "Dọn dẹp tài nguyên (Clean-up)"
date: 2026-11-17
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Chỉ chạy trong tài khoản lab của riêng bạn và chỉ sau khi đã quyết định ngừng dịch vụ. Xóa stack không xóa hết mọi thứ: bảng DynamoDB, bucket media, bucket artifacts và repository CodeCommit dùng chính sách Retain.

1. Liệt kê tài nguyên vật lý của từng stack (`aurel-frontend`, `aurel-backend`, `aurel-artifacts`, `aurel-observability`) và ghi chủ sở hữu. Sao lưu dữ liệu cần giữ và kiểm tra bản sao đọc được.
2. Dừng các build mới và xóa stack nhánh report riêng nếu bạn có tạo; không xóa Amplify app dùng chung.
3. Xóa các stack theo thứ tự này, chờ `DELETE_COMPLETE` sau mỗi lần: frontend, backend, observability, rồi artifacts.
```sh
aws cloudformation delete-stack --stack-name aurel-frontend
aws cloudformation wait stack-delete-complete --stack-name aurel-frontend
```
4. Xử lý tài nguyên Retain thủ công: với bucket bật versioning, xóa toàn bộ versions, delete markers và multipart uploads trước khi xóa bucket (lệnh xóa đệ quy thông thường là không đủ), sau đó xóa bảng và repository theo danh sách đã kiểm kê.
5. Kiểm tra log Amplify, SNS subscription hoặc Budget nếu có thêm, và các Region khác. Xem lại Billing sau khi dữ liệu cập nhật.

Không dùng ký tự đại diện để xóa tài nguyên Retain và không đụng tới tài nguyên của dự án khác.
