---
title: "Xử lý sự cố, bài học và hướng phát triển"
date: 2026-11-26
weight: 13
chapter: false
pre: " <b> 5.13. </b> "
---

### Sự cố thường gặp và cách xử lý
| Biểu hiện | Nguyên nhân có thể | Cách xử lý |
| --- | --- | --- |
| Trình duyệt báo lỗi CORS sau khi triển khai | `FrontendOrigin` vẫn là `https://pending.invalid` | Triển khai lại backend với origin thật của frontend |
| Thực đơn trống hoặc không gọi được API | `API_BASE_URL` hoặc `NEXT_PUBLIC_API_BASE_URL` thiếu hoặc có dấu `/` cuối; các giá trị `NEXT_PUBLIC_*` cố định lúc build | Sửa biến môi trường và chạy Amplify job mới |
| `/admin/*` trả 401 dù đã đăng nhập | Gửi nhầm ID token hoặc access token (hiệu lực 1 giờ) đã hết hạn | Đăng nhập lại; BFF phải chuyển tiếp access token |
| Đặt bàn đã lưu nhưng không có email | SES ở sandbox và người nhận chưa được xác minh | Xác minh địa chỉ hoặc dùng mailbox simulator |
| Amplify job thất bại | Sai phiên bản Node hoặc thiếu biến | Đọc build log, dùng Node 22 và bảng biến môi trường |
| Còn tài nguyên sau khi xóa stack | Chính sách Retain trên bảng, bucket và repository | Làm theo trang dọn dẹp |

### Bài học rút ra
- Ghi lại mọi output của stack ngay sau khi tạo; các bước sau cần dùng đến.
- Tách bucket chứa gói triển khai khỏi bucket media.
- Kiểm thử cả đường lỗi (401, 400), không chỉ đường chạy đúng.
- Nêu rõ các giới hạn (SES sandbox, chưa có WAF, chưa thử tải) thay vì che giấu.

### Hướng phát triển
- Outbox email với SQS và hàng đợi lỗi, sau đó xin SES production access.
- MFA cho admin và AWS WAF trước API.
- Thử tải và thử khôi phục theo thời điểm.
- Thông báo SNS cho bốn alarm.
