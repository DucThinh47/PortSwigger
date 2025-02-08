# Server-side parameter pollution

Một số hệ thống chứa APIs nội bộ không thể truy cập trực tiếp từ Internet. *Server-side parameter pollution* xảy ra khi 1 website nhúng thông tin input từ user trong *server-side request* vào APIs nội bộ mà không được encode đầy đủ. Điều này cho phép attacker thao túng hoặc chèn params, thực hiện hành vi sau: 

- Ghi đè các param hiện có.
- Sửa đổi hành vi ứng dụng.
- Truy cập dữ liệu trái phép.

Có thể kiểm tra bất kỳ thông tin đầu vào nào của người dùng để tìm bất kỳ loại *parameter polution* nào. Ví dụ: query parameters, form fields, headers và URL path parameters đều có thể dễ bị tấn công.

![img](https://imgur.com/ORj5nCs.png)

**Lưu ý**

Lỗ hổng này đôi khi được gọi là *HTTP parameter pollution*. Tuy nhiên, thuật ngữ này cũng được dùng để chỉ kỹ thuật *web application firewall (WAF) bypass*. Để tránh nhầm lẫn, trong chủ đề này chúng tôi sẽ chỉ đề cập đến *Server-side parameter pollution*.

Ngoài ra, mặc dù có tên tương tự nhưng lớp lỗ hổng này có rất ít điểm chung với *server-side prototype pollution*.

