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

![img](41)

Truy cập lab: 

![img](42)

Chọn category Gifts và intercept request trong Burp Suite: 

![img](43)

Send request và quan sát response: 

![img](44)

Thử thêm payload **'+UNION+SELECT+NULL--** vào giá trị tham số category: 

![img](45)

Send request và quan sát response: 

![img](46)

Có vẻ số lượng cột chưa khớp với truy vấn gốc, thử thêm 1 giá trị NULL: 

![img](47)

Send request và quan sát response: 

![img](48)

=> Tìm ra số lượng cột của truy vấn gốc là 2. Thử thay 2 giá trị NULL thành username, password và thêm FROM users: 

![img](49)

Send request và quan sát response: 

![img](50)

=> Tìm được username: administrator và password: hzz2lhkguo4in0qrff6a

Đăng nhập vào tài khoản administrator: 

![img](51)

Solved the lab!

![img](52)









