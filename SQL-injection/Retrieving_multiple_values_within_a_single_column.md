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

![img](53)

Truy cập lab: 

![img](54)

Click chọn category Pets và intercept request trong Burp Suite: 

![img](55)

Send request và quan sát response: 

![img](56)

Thử chèn payload **'UNION+SELECT+NULL--** vào giá trị tham số category: 

![img](57)

Send request và quan sát response: 

![img](58)

Lỗi, thử thêm một giá trị NULL: 

![img](59)

Send request và quan sát response: 

![img](60)

=> Thành công tìm ra số lượng cột trong truy vấn gốc là 2. Thử thay 2 giá trị NULL thành 'abc' và 'def':

![img](61)

Send request và quan sát response: 

![img](62)

Bị lỗi, có vẻ 1 trong 2 cột không phải kiểu dữ liệu chuỗi. Thử thay 'abc' thành NULL: 

![img](63)

Send request và quan sát response: 

![img](64)

=> Cột thứ 2 mới là cột có kiểu dữ liệu chuỗi. Cần trích xuất cả username và password trong cùng một cột này. Sửa 'def' thành **username||'~'||password** và thêm **+FROM+users**: 

![img](65)

Send request và xem resposne: 

![img](66)

=> Tìm được username: administrator và password: lev8hj12jssratpvcwl1

Login vào tài khoản administrator: 

![img](67)

Solved the lab!

![img](68)

















