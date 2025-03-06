# Determining the number of columns required

Khi thực hiện tấn công SQL injection kiểu **UNION**, có hai phương pháp hiệu quả để xác định số lượng cột mà truy vấn gốc trả về.

Một phương pháp là chèn lần lượt các mệnh đề ORDER BY và tăng dần chỉ số cột cho đến khi xảy ra lỗi. Ví dụ, nếu điểm injection nằm trong một chuỗi được trích dẫn bên trong mệnh đề **WHERE** của truy vấn gốc, có thể gửi:

    ' ORDER BY 1--  
    ' ORDER BY 2--  
    ' ORDER BY 3--  
    ...

Chuỗi payload này sẽ sửa đổi truy vấn gốc để sắp xếp kết quả theo các cột khác nhau trong tập kết quả. Cột trong mệnh đề ORDER BY có thể được chỉ định bằng chỉ số của nó, vì vậy không cần biết tên của bất kỳ cột nào.

Khi chỉ số cột vượt quá số lượng cột thực tế trong tập kết quả, cơ sở dữ liệu sẽ trả về lỗi, ví dụ:

    The ORDER BY position number 3 is out of range of the number of items in the select list.

Ứng dụng có thể hiển thị lỗi của cơ sở dữ liệu trong phản hồi HTTP, nhưng cũng có thể trả về một phản hồi lỗi chung chung hoặc không hiển thị kết quả nào cả. Dù bằng cách nào, miễn là có sự khác biệt trong phản hồi thì có thể suy ra số lượng cột mà truy vấn đang trả về.

# Determining the number of columns required - Continued

Phương pháp thứ hai để xác định số lượng cột là gửi một chuỗi payload **UNION SELECT** với số lượng giá trị **NULL** khác nhau:

    ' UNION SELECT NULL--  
    ' UNION SELECT NULL, NULL--  
    ' UNION SELECT NULL, NULL, NULL--  
    ...

Nếu số lượng giá trị **NULL** không khớp với số lượng cột, cơ sở dữ liệu sẽ trả về lỗi, ví dụ:

    All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.

Sử dụng **NULL** làm giá trị trả về từ truy vấn **SELECT** được chèn vào vì kiểu dữ liệu của từng cột trong truy vấn gốc và truy vấn chèn vào phải tương thích. **NULL** có thể chuyển đổi sang hầu hết các kiểu dữ liệu phổ biến, giúp tăng khả năng payload hoạt động thành công khi số lượng cột chính xác.

Tương tự như kỹ thuật **ORDER BY**, ứng dụng có thể hiển thị lỗi của cơ sở dữ liệu trong phản hồi HTTP, hoặc trả về một lỗi chung chung, hoặc thậm chí không hiển thị kết quả nào. Khi số lượng giá trị **NULL** khớp với số lượng cột, cơ sở dữ liệu sẽ trả về một dòng dữ liệu bổ sung trong tập kết quả, chứa các giá trị **NULL** trong từng cột. Ảnh hưởng của điều này đến phản hồi HTTP phụ thuộc vào cách ứng dụng xử lý dữ liệu.

- Nếu may mắn, có thể thấy một số nội dung bổ sung trong phản hồi, ví dụ như một dòng dữ liệu mới trong bảng HTML.

- Nếu không, giá trị **NULL** có thể kích hoạt một lỗi khác, chẳng hạn như *NullPointerException*.

- Trong trường hợp tệ nhất, phản hồi của ứng dụng có thể trông giống hệt như khi số lượng **NULL** không chính xác, khiến phương pháp này trở nên kém hiệu quả.

# Lab: SQL injection UNION attack, determining the number of columns returned by the query

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image14.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image15.png?raw=true)

Thử chọn Tech gifts và Intercept request trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image16.png?raw=true)

Send request và xem response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image17.png?raw=true)

Sử dụng phương pháp chèn payload **'UNINON SELECT NULL--**, chèn payload này vào giá trị tham số category: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image18.png?raw=true)

Thử send request này và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image19.png?raw=true)

Lỗi Internal Server Error, có vẻ số cột từ truy vấn chèn chưa khớp với truy vấn gốc. Thử thêm 1 giá trị NULL vào payload: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image20.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image21.png?raw=true)

Vẫn chưa được, thử thêm 1 giá trị NULL nữa:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image22.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image23.png?raw=true)

=> Thành công, website đã phản hồi lại, tìm ra số lượng cột trong truy vấn gốc là 3. 

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image24.png?raw=true)

# Database-specific syntax

Trên **Oracle**, mọi truy vấn **SELECT** đều phải chứa từ khóa **FROM** và chỉ định một bảng hợp lệ. Oracle có một bảng tích hợp sẵn gọi là **DUAL**, có thể được sử dụng cho mục đích này. Do đó, các truy vấn chèn vào trên Oracle cần có dạng:

    ' UNION SELECT NULL FROM DUAL--

Các payload trên sử dụng chuỗi ký tự **--** để tạo một chú thích, giúp loại bỏ phần còn lại của truy vấn gốc sau điểm injection.

Trên **MySQL**, chuỗi **--** phải được theo sau bởi một khoảng trắng (-- ). Ngoài ra, có thể sử dụng dấu **#** để tạo chú thích:

    ' UNION SELECT NULL #  






