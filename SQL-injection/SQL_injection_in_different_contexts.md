# SQL injection in different contexts

Trong các bài lab trước, đã tiêm payload SQL thông qua chuỗi truy vấn (query string). Tuy nhiên, có thể tấn công SQL injection bằng bất kỳ đầu vào nào mà ứng dụng xử lý dưới dạng truy vấn SQL. Ví dụ, một số trang web nhận dữ liệu ở định dạng JSON hoặc XML và sử dụng nó để truy vấn cơ sở dữ liệu.

Các định dạng đầu vào khác nhau này có thể giúp ẩn giấu cuộc tấn công, đặc biệt khi hệ thống có WAF hoặc cơ chế phòng thủ. Nhiều hệ thống bảo mật lọc các từ khóa phổ biến liên quan đến SQL injection, nhưng có thể vượt qua bộ lọc này bằng cách mã hóa hoặc escape các ký tự trong từ khóa bị cấm.

Ví dụ sau đây sử dụng **chuỗi escape XML** để mã hóa ký tự "S" trong từ SELECT, giúp tránh bị phát hiện:

    <stockCheck>
        <productId>123</productId>
        <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
    </stockCheck>

Trước khi gửi đến bộ phân tích SQL, máy chủ sẽ giải mã nội dung XML, khiến câu lệnh SELECT được thực thi bình thường.

# Lab: SQL injection with filter bypass via XML encoding

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image197.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image198.png?raw=true)

Click xem 1 sản phẩm chi tiết:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image199.png?raw=true)

Click check stock. Kiểm tra request khi check stock: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image200.png?raw=true)

=> Chức năng kiểm tra hàng tồn kho gửi productId và storeId đến ứng dụng theo định dạng XML.

Send request, xem response:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image201.png?raw=true)

Thử thay đổi storeId để kiểm tra xem ứng dụng có đánh giá đầu vào không. Ví dụ, thay ID bằng một biểu thức toán học:

    <storeId>1+1</storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image202.png?raw=true)

=> Webiste thực hiện phép toán này, trả về kết quả cho một cửa hàng khác, chứng tỏ dữ liệu đầu vào bị đánh giá.

Tiếp tục kiểm tra bằng cách thử khai thác SQL Injection với UNION SELECT:

    <storeId>1 UNION SELECT NULL</storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image203.png?raw=true)

=> Yêu cầu bị chặn với thông báo đây là một cuộc tấn công tiềm năng, chứng tỏ hệ thống có WAF (Web Application Firewall).

Download extension Hackvertor. 

=> Vượt qua WAF

Do đang tiêm SQL vào XML, thử ẩn payload bằng XML entities.

Sử dụng Hackvertor Extension trong Burp Suite:
- Tô đậm payload
- Chuột phải > Extensions > Hackvertor > Encode > dec_entities/hex_entities
- Gửi lại yêu cầu.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image204.png?raw=true)

=> Nhận được phản hồi bình thường từ ứng dụng, vượt qua WAF thành công.


=> Tạo khai thác (Exploit)

Tiếp tục thử nghiệm, nhận thấy rằng truy vấn trả về một cột duy nhất.

Nếu cố gắng trả về nhiều hơn một cột, website sẽ phản hồi 0 units, nghĩa là có lỗi xảy ra.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image205.png?raw=true)

Do chỉ có thể trả về một cột,nối (concatenate) dữ liệu username và password bằng ký tự ~

    <storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image206.png?raw=true)

=> Tìm ra tài khoản administrator. 

Login và solved bài lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image207.png?raw=true)



