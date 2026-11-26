---
title: "Demo, bài học và hướng phát triển"
date: 2026-11-26
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

### Sản phẩm đang chạy
- **Website (Amplify):** <https://main.d2rkhtb7c0k5r7.amplifyapp.com/>
- **Trang admin:** <https://main.d2rkhtb7c0k5r7.amplifyapp.com/admin>
- **Mã nguồn:** <https://github.com/ndthuy21/aurel-restaurant>

Mật khẩu admin không công khai trong báo cáo; người trình bày tự đăng nhập trên thiết bị của mình. Tên món, giá, thương hiệu và địa chỉ là dữ liệu minh họa nên không nhập thông tin khách thật khi thử demo.

### Video demo
<iframe width="720" height="405" src="https://www.youtube.com/embed/_Dpp0RJAG_w" title="AUREL demo video" allowfullscreen style="max-width:100%; border:0;"></iframe>

[Xem trên YouTube](https://www.youtube.com/watch?v=_Dpp0RJAG_w) · Video nhóm FantasticFive "Dự án AWS Nhóm FantasticFive", do Huỳnh Trí Nhân đăng ngày 24/11/2026 (khoảng 4 phút 21 giây theo báo cáo của nhóm). Video minh họa các trang dành cho khách, form liên hệ và đặt bàn, cùng màn hình quản trị thực đơn.

### Kịch bản trình diễn gợi ý
1. Nêu bài toán và mở sơ đồ kiến trúc.
2. Thực đơn: tìm kiếm, lọc và chuyển trang trong 100 món tải từ API.
3. Form đặt bàn: gửi một yêu cầu hợp lệ, rồi gửi body sai để thấy backend kiểm tra dữ liệu.
4. Admin: đăng nhập, mở yêu cầu đang chờ và đổi trạng thái.
5. Tạo một món thử kèm ảnh tải lên và kiểm tra ở trang thực đơn.
6. Mở CloudWatch: số request, 4xx và 5xx, log và alarm.
7. Hỏi đáp: SES sandbox, MFA cho admin, hạn mức và hàng đợi email đề xuất.

### Minh chứng bổ sung
Kết quả kiểm tra HTTP chi tiết và các tệp minh chứng demo được lưu trên trang báo cáo của nhóm: <https://report.d2rkhtb7c0k5r7.amplifyapp.com/vi/5-workshop/5.12-demo/>

### Sự cố thường gặp và cách xử lý
| Biểu hiện | Nguyên nhân có thể | Cách xử lý |
| --- | --- | --- |
| Trình duyệt báo lỗi CORS sau khi triển khai | FrontendOrigin vẫn là https://pending.invalid | Triển khai lại backend với origin thật của frontend |
| Thực đơn trống hoặc không gọi được API | API_BASE_URL hoặc NEXT_PUBLIC_API_BASE_URL thiếu hoặc có dấu / cuối; các giá trị NEXT_PUBLIC_* cố định lúc build | Sửa biến môi trường và chạy Amplify job mới |
| /admin/* trả 401 dù đã đăng nhập | Gửi nhầm ID token hoặc access token (hiệu lực 1 giờ) đã hết hạn | Đăng nhập lại; BFF phải chuyển tiếp access token |
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
