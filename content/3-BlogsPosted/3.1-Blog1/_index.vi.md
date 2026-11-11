---
title: "IAM cho người mới: nếu một access key bị lộ, thiệt hại tối đa là bao nhiêu?"
date: 2026-11-11
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Khi mới học AWS, mình thường làm mọi thứ bằng một tài khoản duy nhất, và tài khoản đó có toàn quyền. Cách này tiện cho đến khi mình tự hỏi: nếu access key trong máy hoặc trên GitHub bị lộ thì kẻ nhặt được nó làm được những gì? Câu hỏi này dẫn mình đến AWS Identity and Access Management (IAM).

Điểm đầu tiên là phân biệt các loại danh tính. Root user có quyền cao nhất trên tài khoản và theo khuyến nghị của AWS chỉ nên dùng cho một số tác vụ bắt buộc, kèm bật MFA. Công việc hằng ngày nên dùng danh tính khác, được cấp quyền vừa đủ.

Điểm thứ hai là loại thông tin xác thực. Access key dài hạn tiện nhưng rủi ro, vì nó không tự hết hạn và có thể nằm lại trong mã nguồn, tệp cấu hình hay lịch sử commit. Với người dùng, AWS khuyến nghị đăng nhập qua IAM Identity Center. Với ứng dụng chạy trên AWS (EC2, Lambda), nên dùng IAM role để nhận thông tin xác thực tạm thời. Đánh đổi là cách thiết lập ban đầu phức tạp hơn một chút, nhưng đổi lại không có khóa cố định nào để làm lộ.

Điểm thứ ba là nguyên tắc đặc quyền tối thiểu. Thay vì gắn policy cho phép mọi hành động trên mọi tài nguyên, ta chỉ cho phép đúng hành động và đúng tài nguyên cần dùng. Hình minh họa bên dưới mô tả ý này: IAM user và IAM role cùng gắn một policy, policy cho phép s3:GetObject nhưng từ chối s3:DeleteObject trên một S3 bucket. Theo logic đánh giá policy của AWS, một lệnh từ chối tường minh (explicit deny) luôn thắng lệnh cho phép, nên có thể dùng để chặn các hành động nhạy cảm.

<img src="/fcj-workshop-sieg/images/3-BlogsPosted/3.1-Blog1/iam-least-privilege.png" alt="Sơ đồ: IAM user và IAM role cùng gắn một policy cho phép s3:GetObject và từ chối s3:DeleteObject" style="max-width:100%; width:700px;">

Mình sẽ kiểm chứng bằng một lab nhỏ, không dựa vào kết luận từ lý thuyết:

1. Tạo hai IAM identity, một gắn quyền rộng và một gắn quyền hẹp.
2. Cùng chạy một tập lệnh AWS CLI: liệt kê bucket, đọc object, xóa object, tạo EC2.
3. Ghi lại lệnh nào thành công và lệnh nào bị từ chối (AccessDenied).
4. Dùng IAM Access Analyzer để xem quyền hoặc access key nào được cấp nhưng chưa từng dùng. Lưu ý: phân tích quyền không dùng cần tạo một unused access analyzer riêng và được tính phí theo số IAM user và role được phân tích mỗi tháng, nên mình xem trang giá trước và xóa analyzer sau khi làm xong.

Kết quả của bước 3 cho biết “bán kính thiệt hại” thực tế nếu khóa của từng identity bị lộ.

Về vận hành, mình sẽ bật MFA, xoay vòng hoặc xóa access key không dùng, không đưa khóa vào mã nguồn, và rà soát định kỳ các policy quá rộng. Khi kết thúc lab, cần xóa user, role và key tạm thời.

Nhận định của mình: đặc quyền tối thiểu không ngăn được việc lộ khóa, nhưng nó giới hạn hậu quả khi sự cố xảy ra. Mức giới hạn thực tế phụ thuộc vào policy cụ thể, nên cần thử như trên rồi mới kết luận.

Nguồn tham khảo:

1. Security best practices in IAM: <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html>
2. IAM roles: <https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html>
3. Policy evaluation logic (explicit deny): <https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic_policy-eval-denyallow.html>
4. IAM Access Analyzer: <https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html>

#AWS #AWSStudyGroup #FCAJ #FirstCloudAIJourney #IAM #CloudSecurity #LeastPrivilege
