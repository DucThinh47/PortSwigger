# Blind SQL injection

Trong phần này, tìm hiểu các **kỹ thuật phát hiện** và **khai thác** lỗ hổng **blind SQL injection**.

# What is blind SQL injection?

**Blind SQL injection** xảy ra khi một ứng dụng **bị tổn thương bởi SQL injection**, nhưng phản hồi HTTP **không hiển thị kết quả truy vấn SQL** hoặc không tiết lộ lỗi từ cơ sở dữ liệu.

Các kỹ thuật tấn công như **UNION-based SQL injection** thường không hiệu quả với **blind SQL injection**, vì chúng dựa vào việc xem được kết quả truy vấn trong phản hồi của ứng dụng. Tuy nhiên, vẫn có thể khai thác lỗ hổng **blind SQL injection** để truy cập dữ liệu trái phép bằng các kỹ thuật khác.


