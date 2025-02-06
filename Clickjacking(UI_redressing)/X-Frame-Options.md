# X-Frame-Options

X-Frame-Options ban đầu được giới thiệu như một response header không chính thức trong Internet Explorer 8 và nó đã nhanh chóng được áp dụng trong các trình duyệt khác. Header này cung cấp cho chủ sở hữu trang web kiểm soát việc sử dụng iframes hoặc các đối tượng để việc đưa một trang web trong frame có thể bị cấm với Chỉ thị *deny*:

    X-Frame-Options: deny

Ngoài ra, framing có thể được giới hạn ở cùng nguồn gốc với trang web, sử dụng Chỉ thị *sameorigin*:

    X-Frame-Options: sameorigin

hoặc đến một trang web được đặt tên bằng cách sử dụng Chỉ thị *allow-from*:

    X-Frame-Options: allow-from https://normal-website.com

X-Frame-Options không được triển khai một cách nhất quán trên các trình duyệt (ví dụ như Chỉ thị *allow-from* không được hỗ trợ trong phiên bản Chrome 76 hoặc Safari 12). Tuy nhiên, khi được áp dụng đúng cách kết hợp với Content Security Policy như là một phần của chiến lược *multi-layer defense*, nó có thể cung cấp bảo vệ hiệu quả chống lại các cuộc tấn công clickjacking.



