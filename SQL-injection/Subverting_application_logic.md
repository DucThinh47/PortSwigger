# Subverting application logic

Kịch bản: một ứng dụng cho phép người dùng đăng nhập bằng tên người dùng (username) và mật khẩu (password). Khi người dùng nhập username: wiener và password: bluecheese, ứng dụng sẽ kiểm tra thông tin đăng nhập bằng cách thực hiện truy vấn SQL sau:

    SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'

Nếu truy vấn trả về thông tin của một người dùng, đăng nhập sẽ thành công. Ngược lại, yêu cầu đăng nhập sẽ bị từ chối.

Trong trường hợp này, kẻ tấn công có thể đăng nhập vào bất kỳ tài khoản nào mà không cần biết mật khẩu. Điều này có thể thực hiện bằng cách sử dụng chuỗi bình luận SQL (--) để loại bỏ điều kiện kiểm tra mật khẩu trong mệnh đề WHERE.

Ví dụ, nếu kẻ tấn công nhập username: administrator'-- và để trống mật khẩu, truy vấn SQL sẽ trở thành:

    SELECT * FROM users WHERE username = 'administrator'--' AND password = ''

Vì dấu -- biến phần còn lại của truy vấn thành bình luận, điều kiện kiểm tra mật khẩu (AND password = '') bị loại bỏ. Điều này khiến ứng dụng xác thực thành công với tài khoản có username = administrator, cho phép kẻ tấn công đăng nhập trái phép vào tài khoản quản trị viên.

# Lab: SQL injection vulnerability allowing login bypass

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image8.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image9.png?raw=true)

Click My account:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image10.png?raw=true)

Nhập username là administrator'-- và password là 123 (tùy chọn), trước khi click Login, Intercept request này trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image11.png?raw=true)

Send request này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image12.png?raw=true)

Đăng nhập vào tài khoản administrator.

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image13.png?raw=true)



