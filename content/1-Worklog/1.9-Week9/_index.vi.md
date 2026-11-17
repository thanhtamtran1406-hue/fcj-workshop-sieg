---
title: "Worklog Tuần 9"
date: 2026-11-17
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Kiểm thử workshop
* Tạo website báo cáo từ template FCAJ

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Kiểm thử trên máy<br>&emsp;+ pnpm check, pnpm build, pnpm test:smoke<br>&emsp;+ Kiểm tra /menu trả về 100 món và không có lỗi CORS | 02/11/2026 | 02/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 3 | - Tạo repository và website báo cáo từ template FCAJ<br>&emsp;+ Cài Hugo và clone template<br>&emsp;+ Bật GitHub Pages bằng workflow deploy | 03/11/2026 | 03/11/2026 | [https://github.com/thienluhoan/fcj-workshop-template](https://github.com/thienluhoan/fcj-workshop-template)<br>[https://gohugo.io/documentation/](https://gohugo.io/documentation/)<br>[https://docs.github.com/en/pages](https://docs.github.com/en/pages) |
| 4 | - Cấu hình website báo cáo<br>&emsp;+ baseURL, ảnh đại diện, ngôn ngữ | 04/11/2026 | 04/11/2026 | [https://gohugo.io/documentation/](https://gohugo.io/documentation/) |
| 5 | - Kiểm thử đặt bàn và admin trên AWS<br>&emsp;+ Đặt bàn, kiểm tra DynamoDB và email SES<br>&emsp;+ Admin đăng nhập và xác nhận đặt bàn | 05/11/2026 | 05/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 6 | - Kiểm thử upload ảnh S3 và kiểm tra log<br>&emsp;+ PUT bằng URL ký và xem trước ảnh<br>&emsp;+ Log Lambda trong CloudWatch và log build, SSR của Amplify | 06/11/2026 | 06/11/2026 | [https://github.com/ndthuy21/aurel-restaurant](https://github.com/ndthuy21/aurel-restaurant) |
| 7 | - Thêm 3 event đã tham dự vào website báo cáo<br>&emsp;+ Nội dung chính và ảnh check-in | 07/11/2026 | 07/11/2026 | [https://hcm-rules.awsfcaj.com/project/content](https://hcm-rules.awsfcaj.com/project/content) |

### Kết quả đạt được tuần 9:

* Hoàn thành kiểm thử trên máy và trên AWS
* Website báo cáo chạy trên GitHub Pages
