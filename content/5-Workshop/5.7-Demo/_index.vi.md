---
title: "Minh chứng sản phẩm chạy thực tế"
date: 2026-11-17
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
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
