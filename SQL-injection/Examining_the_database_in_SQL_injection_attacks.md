# Examining the database in SQL injection attacks

Để khai thác các lỗ hổng SQL injection, cần thu thập thông tin về cơ sở dữ liệu. Điều này bao gồm:

- **Loại và phiên bản** của hệ quản trị cơ sở dữ liệu (DBMS).
- **Các bảng và cột** mà cơ sở dữ liệu chứa.

# Querying the database type and version

Có thể xác định **loại** và **phiên bản** của hệ quản trị cơ sở dữ liệu (DBMS) bằng cách chèn các truy vấn đặc trưng cho từng hệ thống và kiểm tra xem truy vấn nào hoạt động.

Dưới đây là một số truy vấn để xác định phiên bản của một số hệ quản trị cơ sở dữ liệu phổ biến:

| Loại CSDL         | Truy vấn                |
|-------------------|-------------------------|
| Mircrosoft, MySQL | SELECT @@version        |
| Oracle            | SELECT * FROM v$version |
| PostgreSQL        | SELECT version()        |

Ví dụ, có thể sử dụng tấn công **UNION** với payload sau:

    ' UNION SELECT @@version--

Nếu tấn công thành công, nó có thể trả về thông tin như sau, xác nhận rằng cơ sở dữ liệu đang sử dụng **Microsoft SQL Server** và hiển thị phiên bản của nó:

    Microsoft SQL Server 2016 (SP2) (KB4052908) - 13.0.5026.0 (X64)  
    Mar 18 2018 09:11:49  
    Copyright (c) Microsoft Corporation  
    Standard Edition (64-bit) on Windows Server 2016 Standard 10.0 <X64> (Build 14393: ) (Hypervisor)  

# Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image69.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image70.png?raw=true)

Click chọn category Gifts và intercept request trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image71.png?raw=true)

Send request và xem response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image72.png?raw=true)

Thử thêm payload **'UNION+SELECT+NULL,NULL--** vào tham số category: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image73.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image74.png?raw=true)

Bị lỗi dù đã sử dụng giá trị NULL, có thể do cách sử dụng comment không khớp với kiểu CSDL, thử thay **--** thành **+#**: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image75.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image76.png?raw=true)

=> Thành công, tìm ra số lượng cột trong truy vấn gốc là 2. Tiếp theo vì version của DB là kiểu dữ liệu chuỗi, cần thử xem cột nào là cột có kiểu dữ liệu chuỗi. Thay giá trị NULL thứ 2 thành 'abcdf': 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image77.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image78.png?raw=true)

=> Tìm ra cột thứ 2 có kiểu dữ liệu là chuỗi. Thử cả cột thứ nhất: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image79.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image80.png?raw=true)

=> Cột thứ nhất cũng có kiểu dữ liệu là chuỗi. Để truy xuất DB version, thử chèn giá trị **SELECT+@@version**: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image81.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image82.png?raw=true)

=> Thành công tìm ra DB version. Chuyển payload này sang request bên tab proxy và forward: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image83.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image84.png?raw=true)





