# Retrieving multiple values within a single column

Trong một số trường hợp, truy vấn gốc có thể chỉ trả về **một cột** thay vì nhiều cột như ví dụ trước.

Để lấy nhiều giá trị trong cùng một cột, có thể **nối các giá trị lại với nhau** bằng cách sử dụng toán tử nối chuỗi của cơ sở dữ liệu. Việc thêm một ký tự **phân tách** giúp dễ dàng nhận diện từng giá trị riêng lẻ.

Ví dụ, trên **Oracle**, có thể sử dụng payload sau:

    ' UNION SELECT username || '~' || password FROM users--

Trong đó:

- **||** là toán tử nối chuỗi trên Oracle.
- Giá trị của cột **username** và **password** được nối lại với nhau bằng ký tự **~** để phân tách.

Khi truy vấn này được thực thi, kết quả thu được sẽ chứa danh sách tất cả tên người dùng và mật khẩu, ví dụ:

    administrator~s3cure
    wiener~peter
    carlos~montoya

Mỗi hệ quản trị cơ sở dữ liệu có cú pháp nối chuỗi khác nhau. Xem thêm ở https://portswigger.net/web-security/sql-injection/cheat-sheet

# Lab: SQL injection UNION attack, retrieving multiple values in a single column

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image53.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image54.png?raw=true)

Click chọn category Pets và intercept request trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image55.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image56.png?raw=true)

Thử chèn payload **'UNION+SELECT+NULL--** vào giá trị tham số category: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image57.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image58.png?raw=true)

Lỗi, thử thêm một giá trị NULL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image59.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image60.png?raw=true)

=> Thành công tìm ra số lượng cột trong truy vấn gốc là 2. Thử thay 2 giá trị NULL thành 'abc' và 'def':

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image61.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image62.png?raw=true)

Bị lỗi, có vẻ 1 trong 2 cột không phải kiểu dữ liệu chuỗi. Thử thay 'abc' thành NULL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image63.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image64.png?raw=true)

=> Cột thứ 2 mới là cột có kiểu dữ liệu chuỗi. Cần trích xuất cả username và password trong cùng một cột này. Sửa 'def' thành **username||'~'||password** và thêm **+FROM+users**: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image65.png?raw=true)

Send request và xem resposne: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image66.png?raw=true)

=> Tìm được username: administrator và password: lev8hj12jssratpvcwl1

Login vào tài khoản administrator: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image67.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image68.png?raw=true)

















