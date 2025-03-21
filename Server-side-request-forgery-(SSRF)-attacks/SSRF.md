# Server-side request forgery (SSRF) attacks 

## Content

- [What is SSRF?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-ssrf)

### What is SSRF?

`SSRF (Server-Side Request Forgery)` là một lỗ hổng bảo mật web cho phép kẻ tấn công khiến ứng dụng phía máy chủ thực hiện các yêu cầu đến `một vị trí không mong muốn`.

Trong một cuộc tấn công `SSRF` điển hình, kẻ tấn công có thể buộc máy chủ kết nối đến các dịch vụ nội bộ chỉ dành cho tổ chức. Trong các trường hợp khác, kẻ tấn công có thể ép buộc máy chủ kết nối đến các hệ thống bên ngoài tùy ý. Điều này có thể dẫn đến việc rò rỉ dữ liệu nhạy cảm, chẳng hạn như thông tin xác thực.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image.png?raw=true)

