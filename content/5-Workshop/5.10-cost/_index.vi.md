---
title: "Dự toán và tối ưu chi phí"
date: 2026-11-26
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

### Giả định
Mỗi tháng: 10.000 lượt truy cập, 100.000 API request và 100.000 SSR request, 20 GB nội dung phân phối, Lambda 256 MB ở 200 ms, 1.000 email và 5 admin; Region Singapore, USD, chưa thuế và chưa tính Free Tier.

### Các bản dự toán
| Hạng mục | Chi phí mỗi tháng (USD) |
| --- | --- |
| Workload estimate trên AWS Pricing Calculator (20 dòng sử dụng, 8 dịch vụ) | khoảng 6,00 |
| Mô hình ngân sách độc lập, gồm khoản dự phòng transfer, dashboard và CodeCommit | 12,17 |
| Ngân sách độc lập cộng 25% dự phòng | 15,21 |

Mô hình độc lập thêm các khoản dự phòng (dashboard, truyền dữ liệu, CodeCommit, build report) và dùng đơn giá thận trọng nên cao hơn con số Calculator, vốn chỉ gồm 20 dòng đã nhập. Cả hai đều không phải hóa đơn. Chưa tính: WAF, NAT, SQS, tên miền riêng, hỗ trợ trả phí, khôi phục hay export DynamoDB.

### Biện pháp tối ưu
- Dùng dịch vụ tính theo mức dùng (Lambda, API Gateway, DynamoDB on-demand) để site nhàn rỗi tốn ít chi phí.
- Chạy Lambda trên arm64 với bộ nhớ nhỏ và giữ log 14 ngày.
- Gắn tag tài nguyên và đặt ngân sách tháng kèm cảnh báo email trước khi làm lab.
- Dọn các stack không dùng và các tài nguyên Retain sau mỗi buổi lab (xem trang dọn dẹp).
