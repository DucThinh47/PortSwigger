# Finding columns with a useful data type

Tấn công SQL injection kiểu **UNION** cho phép truy xuất dữ liệu từ truy vấn chèn vào. Thông tin quan trọng muốn lấy thường ở dạng chuỗi. Do đó, cần tìm một hoặc nhiều cột trong kết quả truy vấn gốc có kiểu dữ liệu là chuỗi hoặc tương thích với dữ liệu chuỗi.

Sau khi xác định số lượng cột, có thể kiểm tra từng cột để xem cột nào có thể chứa dữ liệu dạng chuỗi. Để làm điều này, gửi một chuỗi payload **UNION SELECT** với một giá trị chuỗi đặt vào từng cột lần lượt. Ví dụ, nếu truy vấn gốc trả về bốn cột, có thể thử:

    ' UNION SELECT 'a', NULL, NULL, NULL--  
    ' UNION SELECT NULL, 'a', NULL, NULL--  
    ' UNION SELECT NULL, NULL, 'a', NULL--  
    ' UNION SELECT NULL, NULL, NULL, 'a'--  

Nếu kiểu dữ liệu của một cột **không tương thích** với dữ liệu chuỗi, truy vấn chèn vào sẽ gây lỗi cơ sở dữ liệu, chẳng hạn như:

    Conversion failed when converting the varchar value 'a' to data type int.

Ngược lại, nếu **không có lỗi** xảy ra và phản hồi của ứng dụng hiển thị nội dung bổ sung chứa giá trị chuỗi đã chèn ('a'), thì cột đó phù hợp để truy xuất dữ liệu chuỗi.

# Lab: SQL injection UNION attack, finding a column containing text

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image25.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image26.png?raw=true)

Click chọn category Pets và intercept request này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image27.png?raw=true)

Send request này và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image28.png?raw=true)

Chèn payload **'UNION+SELECT+NULL--** vào giá trị tham số category: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image29.png?raw=true)

Send request này quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image30.png?raw=true)

Bị lỗi, có vẻ số lượng cột chưa khớp với truy vấn gốc. Thử thêm 1 giá trị NULL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image31.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image32.png?raw=true)

Chưa được, tiếp tục thêm 1 giá trị NULL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image33.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image34.png?raw=true)

=> Tìm ra được số lượng cột trong truy vấn gốc là 3. Tiếp theo cần kiểm tra từng cột để xem cột nào có thể chứa dữ liệu dạng chuỗi. Để làm điều này, gửi một chuỗi payload **UNION SELECT** với một giá trị chuỗi đặt vào từng cột lần lượt, thử chèn vào giá trị NULL đầu tiên: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image35.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image36.png?raw=true)

Chưa được, cột đầu tiên không phải dữ liệu dạng chuỗi. Thử cột thứ 2: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image37.png?raw=true)

Send request và quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image38.png?raw=true)

=> Thành công tìm ra cột thứ 2 là cột có kiểu dữ liệu chuỗi. Mở lại tab Proxy trong Burp Suite và chèn payload này vào request, sau đó forward request này: 

**Lưu ý, thay giá trị chuỗi thử thành 'CiPooV'**

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image39.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image40.png?raw=true)





