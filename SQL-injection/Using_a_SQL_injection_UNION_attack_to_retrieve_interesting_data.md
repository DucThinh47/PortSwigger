# Using a SQL injection UNION attack to retrieve interesting data

Sau khi xác định được **số lượng cột** trong truy vấn gốc và tìm ra **các cột có thể chứa dữ liệu chuỗi**, có thể bắt đầu truy xuất dữ liệu quan trọng từ cơ sở dữ liệu.

Giả sử:

- Truy vấn gốc trả về **hai cột**, cả hai đều có thể chứa dữ liệu chuỗi.
- Điểm injection nằm trong **một chuỗi được trích dẫn** bên trong mệnh đề **WHERE**.
- Cơ sở dữ liệu có một bảng **users**, chứa hai cột: **username** và **password**.

Trong trường hợp này, có thể lấy nội dung của bảng **users** bằng cách gửi payload sau:

    ' UNION SELECT username, password FROM users--

Để thực hiện tấn công này, cần biết rằng tồn tại một bảng tên **users** với hai cột là **username** và **password**. Nếu không có thông tin này, sẽ phải **đoán** tên của bảng và các cột.

Tuy nhiên, hầu hết các hệ quản trị cơ sở dữ liệu hiện đại đều cung cấp các phương thức để khám phá cấu trúc cơ sở dữ liệu, giúp xác định được các bảng và cột mà chúng chứa.

# Lab: SQL injection UNION attack, retrieving data from other tables

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image41.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image42.png?raw=true)

Chọn category Gifts và intercept request trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image43.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image44.png?raw=true)

Thử thêm payload **'+UNION+SELECT+NULL--** vào giá trị tham số category: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image45.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image46.png?raw=true)

Có vẻ số lượng cột chưa khớp với truy vấn gốc, thử thêm 1 giá trị NULL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image47.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image48.png?raw=true)

=> Tìm ra số lượng cột của truy vấn gốc là 2. Thử thay 2 giá trị NULL thành username, password và thêm FROM users: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image49.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image50.png?raw=true)

=> Tìm được username: administrator và password: hzz2lhkguo4in0qrff6a

Đăng nhập vào tài khoản administrator: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image51.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image52.png?raw=true)









