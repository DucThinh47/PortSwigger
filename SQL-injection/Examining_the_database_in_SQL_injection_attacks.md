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

![img](69)

Truy cập lab: 

![img](70)

Click chọn category Gifts và intercept request trong Burp Suite: 

![img](71)

Send request và xem response: 

![img](72)

Thử thêm payload **'UNION+SELECT+NULL,NULL--** vào tham số category: 

![img](73)

Send request, quan sát response: 

![img](74)

Bị lỗi dù đã sử dụng giá trị NULL, có thể do cách sử dụng comment không khớp với kiểu CSDL, thử thay **--** thành **+#**: 

![img](75)

Send request, quan sát response: 

![img](76)

=> Thành công, tìm ra số lượng cột trong truy vấn gốc là 2. Tiếp theo vì version của DB là kiểu dữ liệu chuỗi, cần thử xem cột nào là cột có kiểu dữ liệu chuỗi. Thay giá trị NULL thứ 2 thành 'abcdf': 

![img](77)

Send request, quan sát response: 

![img](78)

=> Tìm ra cột thứ 2 có kiểu dữ liệu là chuỗi. Thử cả cột thứ nhất: 

![img](79)

Send request, quan sát response: 

![img](80)

=> Cột thứ nhất cũng có kiểu dữ liệu là chuỗi. Để truy xuất DB version, thử chèn giá trị **SELECT+@@version**: 

![img](81)

Send request, quan sát response: 

![img](82)

=> Thành công tìm ra DB version. Chuyển payload này sang request bên tab proxy và forward: 

![img](83)

Solved the lab!

![img](84)





