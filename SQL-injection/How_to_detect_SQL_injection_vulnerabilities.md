# How to detect SQL injection vulnerabilities

Có thể phát hiện SQL injection thủ công bằng cách thực hiện một bộ kiểm tra có hệ thống trên mọi điểm nhập dữ liệu trong ứng dụng. Để làm điều này, thường sẽ gửi các dữ liệu sau:

- **Dấu nháy đơn** (') và kiểm tra xem có lỗi hoặc bất thường nào xuất hiện không.

- **Cú pháp SQL đặc biệt** để kiểm tra xem giá trị đầu vào có thể bị thay đổi hay không bằng cách so sánh phản hồi của ứng dụng với giá trị gốc và giá trị đã bị chỉnh sửa.

- **Các điều kiện Boolean** như OR 1=1 và OR 1=2, sau đó quan sát sự khác biệt trong phản hồi của ứng dụng.

- **Payloads gây trễ thời gian**, được thiết kế để làm chậm phản hồi nếu truy vấn SQL bị thực thi, nhằm phát hiện sự khác biệt về thời gian phản hồi.

- **Payloads OAST** (Out-of-Band Application Security Testing), nhằm kích hoạt tương tác mạng ngoài băng tần khi thực thi trong truy vấn SQL, sau đó giám sát các tương tác này.

Ngoài ra, có thể nhanh chóng và hiệu quả phát hiện hầu hết các lỗ hổng SQL injection bằng cách sử dụng Burp Scanner.

# SQL injection in different parts of the query

Hầu hết các lỗ hổng SQL injection xảy ra trong mệnh đề *WHERE* của truy vấn *SELECT*. Đây là loại SQL injection phổ biến mà hầu hết những người kiểm thử có kinh nghiệm đều quen thuộc.

Tuy nhiên, lỗ hổng SQL injection có thể xuất hiện ở bất kỳ vị trí nào trong truy vấn và trong nhiều loại truy vấn khác nhau. Một số vị trí phổ biến khác mà SQL injection có thể xảy ra bao gồm:

- **Trong câu lệnh UPDATE**: Xuất hiện trong giá trị cập nhật hoặc mệnh đề WHERE.

- **Trong câu lệnh INSERT**: Xuất hiện trong giá trị được chèn vào.

- **Trong câu lệnh SELECT**: Xuất hiện trong tên bảng hoặc tên cột

- **Trong câu lệnh SELECT**: Xuất hiện trong mệnh đề ORDER BY.

