---
title: "CloudTrail và GuardDuty: làm sao biết ai đã làm gì trong tài khoản AWS?"
date: 2026-11-11
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Sau khi tìm hiểu IAM, mình có một câu hỏi tiếp theo: nếu ai đó dùng khóa của mình, làm sao mình biết họ đã làm gì và khi nào? Câu hỏi này dẫn mình đến AWS CloudTrail và Amazon GuardDuty.

CloudTrail ghi lại các lệnh gọi API trong tài khoản, dù đến từ console, CLI hay SDK. Mỗi sự kiện cho biết ai gọi, gọi gì, từ địa chỉ IP nào, vào lúc nào và có thành công hay không. Mặc định, trang Event history cho xem các management event trong 90 ngày gần nhất tại từng Region và không tính phí khi xem. Muốn có bản ghi liên tục quá 90 ngày, cần tạo trail để ghi log vào S3. AWS khuyến nghị dùng trail nhiều Region để không bỏ sót hoạt động. Đánh đổi là một bản sao management event gửi vào S3 không bị CloudTrail tính phí, nhưng mình vẫn phải trả phí lưu trữ S3 và chịu trách nhiệm bảo vệ bucket chứa log.

GuardDuty đảm nhận việc khác. Đây là dịch vụ phát hiện mối đe dọa, liên tục phân tích các nguồn dữ liệu như CloudTrail management events, VPC flow logs và DNS logs, rồi tạo ra các finding kèm mức độ nghiêm trọng từ 1.0 đến 8.9, mức High nằm trong khoảng 7.0 đến 8.9. Điểm khác biệt là CloudTrail cho bạn dữ liệu thô để tự điều tra, còn GuardDuty chủ động cảnh báo những điều bất thường. Hai dịch vụ bổ sung cho nhau chứ không thay thế nhau. Lần đầu bật GuardDuty trong một Region, tài khoản được dùng thử 30 ngày ở Region đó; sau thời gian thử sẽ tính phí theo cách sử dụng, nên mình xem trang giá và đặt nhắc lịch trước khi hết hạn dùng thử.

Mình sẽ kiểm chứng bằng một lab giả lập sự cố, tận dụng kiến thức digital forensics:

1. Tạo một IAM user lab và dùng khóa của user đó chạy vài lệnh CLI, trong đó có một lệnh cố tình bị từ chối.
2. Tìm các sự kiện này trong CloudTrail Event history theo tên user hoặc tên sự kiện.
3. Ghi lại thời gian, IP nguồn và mã lỗi AccessDenied để dựng timeline.
4. Tạo sample findings trong GuardDuty (tiêu đề bắt đầu bằng [SAMPLE]) để xem cấu trúc và mức độ nghiêm trọng của một cảnh báo.

Về vận hành, mình cân nhắc trail áp dụng cho nhiều Region, chặn truy cập công khai vào bucket log và giới hạn ai được xóa log. Một hệ thống log mà kẻ tấn công xóa được thì không còn đáng tin.

Nhận định của mình: CloudTrail trả lời câu hỏi “chuyện gì đã xảy ra”, GuardDuty giúp biết “có điều gì đáng ngờ không”. Việc cảnh báo có hữu ích đến đâu phải kiểm chứng bằng lab trên chính tài khoản của mình.

<img src="/fcj-workshop-sieg/images/3-BlogsPosted/3.3-Blog3/cloudtrail-guardduty.png" alt="Sơ đồ luồng CloudTrail và GuardDuty" style="max-width:100%; width:700px;">

Nguồn tham khảo:

1. Working with CloudTrail event history: <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html>
2. How CloudTrail works (trail và event history): <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/how-cloudtrail-works.html>
3. What is Amazon GuardDuty?: <https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html>
4. Understanding Amazon GuardDuty findings: <https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html>
5. Generating sample findings in GuardDuty: <https://docs.aws.amazon.com/guardduty/latest/ug/sample_findings.html>
6. Pricing in GuardDuty (30-day free trial): <https://docs.aws.amazon.com/guardduty/latest/ug/guardduty-pricing.html>

#AWS #AWSStudyGroup #FCAJ #FirstCloudAIJourney #CloudTrail #GuardDuty #CloudSecurity
