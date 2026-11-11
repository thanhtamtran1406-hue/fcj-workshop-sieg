---
title: "Học AWS mà không “cháy túi”: những khoản phí dễ bị bỏ quên khi làm lab"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

Khi bắt đầu làm lab, mình lo nhất không phải là cấu hình sai mà là hóa đơn cuối tháng. Câu hỏi của mình là: dịch vụ nào dễ tốn tiền ngoài ý muốn, và làm sao phát hiện sớm?

Các khoản phí hay bị bỏ quên thường nằm ở tài nguyên “nhàn rỗi nhưng vẫn tính tiền”. EC2 chạy quên tắt là ví dụ dễ thấy nhất. NAT Gateway bị tính theo mỗi giờ tồn tại và theo mỗi GB dữ liệu đi qua, giờ lẻ cũng tính tròn thành một giờ, và muốn ngừng bị tính phí phải xóa hẳn NAT Gateway. EBS volume vẫn bị tính phí lưu trữ khi instance đã dừng, và volume đã tháo khỏi instance vẫn bị tính cho đến khi bị xóa. Địa chỉ public IPv4 cũng có thể phát sinh phí theo giờ, kể cả khi không dùng. Mức giá cụ thể thay đổi theo Region và theo thời gian, nên mình luôn đối chiếu với trang giá chính thức của AWS trước khi kết luận.

Để thấy tiền đang đi đâu, mình dùng AWS Cost Explorer và nhóm chi phí theo tag. Muốn vậy phải gắn tag ngay khi tạo tài nguyên, ví dụ project=lab, rồi kích hoạt tag đó làm cost allocation tag trong Billing and Cost Management. Theo tài liệu AWS, tag có thể mất tới 24 giờ để xuất hiện và thêm tối đa 24 giờ để kích hoạt. Dữ liệu chi phí của kỳ hiện tại cũng có thể cập nhật chậm hơn 24 giờ, nên Cost Explorer không phải công cụ cảnh báo tức thời.

Để được báo trước, mình dùng AWS Budgets. Mình đặt ngân sách tháng nhỏ và thiết lập cảnh báo qua email khi chi phí thực tế hoặc dự báo chạm một ngưỡng nhất định. Đánh đổi là AWS cho biết chi phí hoặc mức dùng có thể vượt ngưỡng trước khi Budgets kịp thông báo, và Budgets chỉ cảnh báo chứ không tự dừng tài nguyên.

Mình sẽ kiểm chứng bằng một thử nghiệm nhỏ:

1. Dựng một lab gồm vài tài nguyên và gắn tag thống nhất.
2. Ghi lại chi phí theo ngày trong Cost Explorer.
3. So sánh hai tình huống: dọn ngay sau lab và để quên vài ngày.
4. Ghi nhận chênh lệch và thời gian từ lúc phát sinh đến lúc cảnh báo gửi về.

Về vận hành, mình lập checklist dọn dẹp sau mỗi buổi: dừng hoặc xóa EC2, xóa NAT Gateway, xóa volume và snapshot không dùng, giải phóng Elastic IP. Mình cũng kiểm tra các Region khác, vì tài nguyên tạo nhầm ở Region lạ rất dễ bị quên.

Nhận định của mình: công cụ giám sát giúp phát hiện sớm, nhưng thói quen dọn dẹp mới là thứ giữ hóa đơn thấp. Mức tiết kiệm thực tế chỉ kết luận được sau khi có số liệu từ chính tài khoản của mình.

<img src="/fcj-workshop-sieg/images/3-BlogsPosted/3.2-Blog2/cost-optimization-learning-aws.png" alt="Sơ đồ các khoản phí dễ bị bỏ quên khi làm lab AWS" style="max-width:100%; width:700px;">

Nguồn tham khảo:

1. Analyzing your costs and usage with AWS Cost Explorer: <https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html>
2. Exploring your data using Cost Explorer (độ trễ dữ liệu): <https://docs.aws.amazon.com/cost-management/latest/userguide/ce-exploring-data.html>
3. Managing your costs with AWS Budgets: <https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html>
4. Activating user-defined cost allocation tags: <https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/activating-tags.html>
5. Amazon VPC pricing (NAT Gateway, public IPv4): <https://aws.amazon.com/vpc/pricing/>
6. Why am I charged for Amazon EBS when all my instances are stopped?: <https://www.repost.aws/knowledge-center/ebs-charge-stopped-instance>
