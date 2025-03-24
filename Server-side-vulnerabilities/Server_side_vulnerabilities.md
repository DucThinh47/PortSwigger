# Server-side vulnerabilities

## Content

- [Path traversal](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#path-traversal)

    - [What is path traversal?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-path-traversal)

### Path traversal

#### What is path traversal?

`Path traversal`, còn được gọi là `directory traversal`, là lỗ hổng bảo mật cho phép kẻ tấn công `đọc các tệp tùy ý trên máy chủ` đang chạy ứng dụng. Những tệp này có thể bao gồm:

- Mã nguồn và dữ liệu của ứng dụng.
- Thông tin xác thực của các hệ thống phụ trợ (back-end).
- Các tệp nhạy cảm của hệ điều hành.

Trong một số trường hợp, kẻ tấn công còn có thể `ghi vào các tệp tùy ý`trên máy chủ, cho phép chúng sửa đổi dữ liệu hoặc hành vi của ứng dụng, thậm chí `chiếm quyền kiểm soát` hoàn toàn máy chủ.

#### Reading arbitrary files via path traversal

Kịch bản là một ứng dụng mua sắm hiển thị hình ảnh của các mặt hàng đang được bán. Ứng dụng này có thể tải một hình ảnh bằng cách sử dụng đoạn HTML sau:

    <img src="/loadImage?filename=218.png">

URL `loadImage` nhận tham số `filename` và trả về nội dung của tệp được chỉ định. Các tệp hình ảnh được lưu trữ trên đĩa tại vị trí:

    /var/www/images/

Để trả về hình ảnh, ứng dụng nối tên tệp được yêu cầu với thư mục gốc này và sử dụng `API` của hệ thống tệp để đọc nội dung của tệp. Nói cách khác, ứng dụng sẽ đọc từ đường dẫn tệp sau:

    /var/www/images/218.png

Ứng dụng này `không triển khai bất kỳ biện pháp bảo vệ nào` chống lại các cuộc tấn công `path traversal`. Do đó, kẻ tấn công có thể gửi yêu cầu URL sau để lấy tệp `/etc/passwd`từ hệ thống tệp của máy chủ:

    https://insecure-website.com/loadImage?filename=../../../etc/passwd

Yêu cầu này khiến ứng dụng đọc từ đường dẫn tệp sau:

    /var/www/images/../../../etc/passwd

Cụm từ `../` là hợp lệ trong một đường dẫn tệp và có nghĩa là "`lùi lại một cấp trong cấu trúc thư mục`". Ba cụm `../` liên tiếp giúp di chuyển từ thư mục `/var/www/images/` lên đến gốc của hệ thống tệp (/), do đó tệp thực sự được đọc là:

    /etc/passwd

Trên các hệ điều hành dựa trên `Unix`, đây là một tệp chuẩn chứa `thông tin về người dùng đã đăng ký trên máy chủ`. Tuy nhiên, kẻ tấn công cũng có thể sử dụng cùng một kỹ thuật để lấy các tệp tùy ý khác.

**Path traversal trên Windows**

Trên hệ điều hành Windows, cả hai cụm `../` và `..\` đều là các chuỗi hợp lệ để traversal thư mục. Dưới đây là một ví dụ về cuộc tấn công tương tự trên máy chủ chạy Windows:

    https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini

#### Lab: File path traversal, simple case

![img](0)

Access the lab: 

![img](1)

Mở hình ảnh của 1 sản phẩm bất kỳ trên tab mới: 

![img](2)

Thay đổi giá trị tham số `filename` thành `/../../../etc/passwd`:

![img](3)

Solve the lab!

![img](4)

