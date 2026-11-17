---
title: "Worklog Tuần 2"
date: 2026-11-17
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

* Hiểu các loại danh tính và thông tin xác thực trong IAM
* Hiểu nguyên tắc đặc quyền tối thiểu

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu IAM: các loại danh tính<br>&emsp;+ Root user chỉ dùng cho các tác vụ bắt buộc và bật MFA<br>&emsp;+ Công việc hằng ngày dùng danh tính được cấp quyền vừa đủ | 14/09/2026 | 14/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) |
| 3 | - Tìm hiểu thông tin xác thực<br>&emsp;+ Access key dài hạn: không tự hết hạn, có thể nằm lại trong mã nguồn và lịch sử commit<br>&emsp;+ Đăng nhập người dùng qua IAM Identity Center | 15/09/2026 | 15/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)<br>[https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) |
| 4 | - Tìm hiểu IAM role cho ứng dụng chạy trên AWS (EC2, Lambda)<br>&emsp;+ Nhận thông tin xác thực tạm thời | 16/09/2026 | 16/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) |
| 5 | - Tìm hiểu nguyên tắc đặc quyền tối thiểu<br>&emsp;+ Chỉ cho phép đúng hành động trên đúng tài nguyên | 17/09/2026 | 17/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html) |
| 6 | - Tìm hiểu logic đánh giá policy<br>&emsp;+ Explicit deny luôn thắng lệnh cho phép<br>- Tổng hợp ghi chú | 18/09/2026 | 18/09/2026 | [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html) |

### Kết quả đạt được tuần 2:

* Phân biệt được các loại danh tính và rủi ro của access key
* Hiểu nguyên tắc đặc quyền tối thiểu và explicit deny
