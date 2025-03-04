# Subverting application logic

Kịch bản: một ứng dụng cho phép người dùng đăng nhập bằng tên người dùng (username) và mật khẩu (password). Khi người dùng nhập username: wiener và password: bluecheese, ứng dụng sẽ kiểm tra thông tin đăng nhập bằng cách thực hiện truy vấn SQL sau:

    SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'

Nếu truy vấn trả về thông tin của một người dùng, đăng nhập sẽ thành công. Ngược lại, yêu cầu đăng nhập sẽ bị từ chối.

Trong trường hợp này, kẻ tấn công có thể đăng nhập vào bất kỳ tài khoản nào mà không cần biết mật khẩu. Điều này có thể thực hiện bằng cách sử dụng chuỗi bình luận SQL (--) để loại bỏ điều kiện kiểm tra mật khẩu trong mệnh đề WHERE.

Ví dụ, nếu kẻ tấn công nhập username: administrator'-- và để trống mật khẩu, truy vấn SQL sẽ trở thành:

    SELECT * FROM users WHERE username = 'administrator'--' AND password = ''

Vì dấu -- biến phần còn lại của truy vấn thành bình luận, điều kiện kiểm tra mật khẩu (AND password = '') bị loại bỏ. Điều này khiến ứng dụng xác thực thành công với tài khoản có username = administrator, cho phép kẻ tấn công đăng nhập trái phép vào tài khoản quản trị viên.

# Lab: SQL injection vulnerability allowing login bypass

![img](8)

Truy cập lab: 

![img](9)

Click My account:

![img](10)

Nhập username là administrator'-- và password là 123 (tùy chọn), trước khi click Login, Intercept request này trong Burp Suite: 

![img](11)

Send request này:

![img](12)

Đăng nhập vào tài khoản administrator.

Solved the lab!

![img](13)



