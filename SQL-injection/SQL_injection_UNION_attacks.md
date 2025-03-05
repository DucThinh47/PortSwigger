# SQL injection UNION attacks

Khi một ứng dụng bị lỗi SQL injection và kết quả truy vấn được hiển thị trong phản hồi của ứng dụng, kẻ tấn công có thể sử dụng từ khóa **UNION** để lấy dữ liệu từ các bảng khác trong cơ sở dữ liệu. Đây được gọi là tấn công SQL injection kiểu UNION.

Từ khóa UNION cho phép thực thi một hoặc nhiều truy vấn SELECT bổ sung và ghép kết quả vào truy vấn ban đầu. Ví dụ:

    SELECT a, b FROM table1 UNION SELECT c, d FROM table2;

Truy vấn SQL này trả về một tập kết quả duy nhất với hai cột, chứa các giá trị từ cột a và b trong table1, cùng với các giá trị từ cột c và d trong table2.

# SQL injection UNION attacks - Continued

Để một truy vấn **UNION** hoạt động, cần đáp ứng hai yêu cầu quan trọng:

1. Các truy vấn riêng lẻ phải trả về cùng số lượng cột.
2. Kiểu dữ liệu của từng cột trong các truy vấn phải tương thích với nhau.

Để thực hiện một cuộc tấn công SQL injection kiểu UNION, cần đảm bảo rằng truy vấn tấn công đáp ứng hai yêu cầu này. Điều này thường bao gồm việc xác định:

- Số lượng cột mà truy vấn gốc trả về.
- Những cột nào trong truy vấn gốc có kiểu dữ liệu phù hợp để chứa kết quả từ truy vấn chèn vào.

