---
title: "Worklog Tuần 7"
date: 2026-11-17
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Chạy thử dự án AUREL và triển khai backend bằng AWS SAM

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Chạy thử dự án AUREL trên máy<br>&emsp;+ pnpm install --frozen-lockfile<br>&emsp;+ pnpm setup<br>&emsp;+ pnpm dev | 19/10/2026 | 19/10/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 3 | - Tìm hiểu các thành phần backend<br>&emsp;+ API Gateway HTTP API<br>&emsp;+ Lambda Node.js 22<br>&emsp;+ DynamoDB và Cognito | 20/10/2026 | 20/10/2026 | [https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html)<br>[https://docs.aws.amazon.com/lambda/latest/dg/welcome.html](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)<br>[https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)<br>[https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) |
| 4 | - Build backend<br>&emsp;+ pnpm build:lambda<br>&emsp;+ sam validate --lint và sam build | 21/10/2026 | 21/10/2026 | [https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |
| 5 | - Deploy backend bằng sam deploy --guided<br>&emsp;+ Stack aurel-backend, Region ap-southeast-1<br>&emsp;+ Đọc outputs của stack | 22/10/2026 | 22/10/2026 | [https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) |
| 6 | - Nạp dữ liệu thực đơn vào DynamoDB (node dist/seed-aws.mjs)<br>- Tạo user admin trong Cognito và thêm vào nhóm admin (scripts/create-admin.mjs) | 23/10/2026 | 23/10/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant)<br>[https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)<br>[https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html](https://docs.aws.amazon.com/cognito/latest/developerguide/what-is-amazon-cognito.html) |

### Kết quả đạt được tuần 7:

* Dự án chạy được trên máy
* Backend chạy trên AWS, có dữ liệu thực đơn và tài khoản admin
