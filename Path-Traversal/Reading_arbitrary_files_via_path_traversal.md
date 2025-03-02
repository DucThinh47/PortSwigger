# Reading arbitrary files via path traversal

Tưởng tượng một ứng dụng mua sắm hiển thị hình ảnh của các sản phẩm đang được bán. Ứng dụng này có thể tải một hình ảnh bằng đoạn HTML sau:

    <img src="/loadImage?filename=218.png">

URL ***/loadImage*** nhận tham số filename và trả về nội dung của tệp được chỉ định. Các tệp hình ảnh được lưu trữ trong thư mục ***/var/www/images/***. Khi cần hiển thị hình ảnh, ứng dụng sẽ nối tên tệp được yêu cầu vào thư mục cơ sở này và sử dụng API hệ thống tệp để đọc nội dung, dẫn đến đường dẫn như sau:

    /var/www/images/218.png

Ứng dụng này không có bất kỳ biện pháp bảo vệ nào chống lại các cuộc tấn công path traversal. Do đó, kẻ tấn công có thể gửi yêu cầu sau để lấy tệp ***/etc/passwd*** từ hệ thống tệp của máy chủ:

    https://insecure-website.com/loadImage?filename=../../../etc/passwd

Yêu cầu này buộc ứng dụng đọc từ đường dẫn thực:

    /var/www/images/../../../etc/passwd

Trong đường dẫn tệp, chuỗi ../ có nghĩa là "lùi lại một cấp trong thư mục". Ba lần ../ liên tiếp sẽ đưa đường dẫn từ /var/www/images/ quay trở lại thư mục gốc của hệ thống (/), khiến tệp thực sự được đọc là:

    /etc/passwd

Trên các hệ điều hành Unix/Linux, tệp ***/etc/passwd*** chứa thông tin về các tài khoản người dùng trên máy chủ. Tuy nhiên, kẻ tấn công có thể sử dụng cùng một kỹ thuật để truy xuất nhiều tệp nhạy cảm khác.

**Tấn công trên hệ thống Windows**

Trên Windows, cả ../ và ..\ đều có thể được sử dụng để di chuyển giữa các thư mục. Ví dụ, kẻ tấn công có thể khai thác lỗ hổng trên máy chủ Windows bằng cách gửi yêu cầu sau:

    https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini

# Lab: File path traversal, simple case

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image.png?raw=true)

Truy cập Lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image1.png?raw=true)

Click vào xem 1 sản phẩm chi tiết: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image2.png?raw=true)

Website đã sử dụng tham số productId để tham chiếu đến sản phẩm trong cơ sở dữ liệu, có thể đường dẫn có dạng sau: /var/www/product/2. Để xem tệp /etc/passwd, thử thay giá trị productId thành /../../../etc/passwd:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image3.png?raw=true)

Không được, thử cách khác, lần này click vào hình ảnh của 1 sản phẩm: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image4.png?raw=true)

=> Website cũng sử dụng tham số filename để tham chiếu ID ảnh trong cơ sở dữ liệu, thử thay giá trị này thành /../../../etc/passwd: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image5.png?raw=true)

=> Đọc được nội dung file /etc/passwd.

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Path-Traversal/images/image6.png?raw=true)

