---
title: "Kiểm thử và xác thực"
date: 2026-11-26
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

- **Trên máy:** 30 test tự động, kiểm tra TypeScript và build đều đạt; bộ smoke trên bản build cục bộ đạt 41 kiểm tra HTTP (đặt bàn, gửi lại yêu cầu, phân quyền admin, tải ảnh). Transaction DynamoDB được kiểm thử bằng mock.
- **Backend trên AWS:** 14 kiểm tra đạt, gồm access token Cognito, từ chối ID token, lưu và đọc đặt bàn, đổi trạng thái, idempotency, S3 PUT/GET thật và lời nhắn liên hệ.
- **Kiểm tra bản phát hành:** API công khai và API admin đều trả 100 món, 7 trang trả HTTP 200 và ảnh khớp từng byte. Đây là kiểm tra HTTP, không phải tự động hóa trình duyệt.

Các request âm tính ai cũng chạy lại được (kết quả mong đợi ở cuối dòng):
```sh
curl -i "$API_URL/admin/reservations"                              # 401
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{}'         # 400
curl -i -X POST "$API_URL/reservations" -H 'Content-Type: application/json' --data '{bad-json'  # 400
```
Chưa thực hiện: thử tranh chấp chỗ thật trên DynamoDB, thử tải (mục tiêu p95 dưới 2 giây ở 5 request/giây trong 5 phút) và thử khôi phục theo thời điểm. Các mục này là kế hoạch, không tuyên bố đã đạt.
