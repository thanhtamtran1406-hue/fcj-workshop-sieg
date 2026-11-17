---
title: "Worklog Tuần 8"
date: 2026-11-17
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Tham dự sự kiện
* Triển khai frontend trên Amplify Hosting, chuẩn bị SES và cập nhật CORS

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Tham dự sự kiện Smart Plushies for Children (09:00, tầng 36 Bitexco)<br>&emsp;+ Làm mô hình gấu nhồi bông thông minh phát ra âm thanh khi tác động<br>&emsp;+ Dùng Microsoft MakeCode | 26/10/2026 | 26/10/2026 |  |
| 3 | - Chuẩn bị SES<br>&emsp;+ Xác minh SenderEmail<br>&emsp;+ Hiểu giới hạn của SES sandbox | 27/10/2026 | 27/10/2026 | [https://docs.aws.amazon.com/ses/latest/dg/Welcome.html](https://docs.aws.amazon.com/ses/latest/dg/Welcome.html) |
| 4 | - Kết nối repository với Amplify Hosting<br>&emsp;+ Chọn Node 22, dùng amplify.yml | 28/10/2026 | 28/10/2026 | [https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html) |
| 5 | - Cấu hình biến môi trường và build<br>&emsp;+ DATA_MODE, API_BASE_URL, NEXT_PUBLIC_API_BASE_URL<br>&emsp;+ COGNITO_CLIENT_ID, COGNITO_USER_POOL_ID, APP_AWS_REGION, APP_ORIGIN | 29/10/2026 | 29/10/2026 | [https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html)<br>[https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 6 | - Cập nhật FrontendOrigin (CORS) bằng sam deploy --guided<br>- Deploy lại frontend và kiểm tra website kết nối với backend | 30/10/2026 | 30/10/2026 | [https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)<br>[https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html) |

### Kết quả đạt được tuần 8:

* Tham dự Event 2
* Website chạy trên Amplify và kết nối được với backend
