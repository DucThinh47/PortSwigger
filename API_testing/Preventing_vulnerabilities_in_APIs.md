# Preventing vulnerabilities in APIs

Khi thiết kế API, đảm bảo rằng bảo mật được cân nhắc ngay từ đầu. Đặc biệt, đảm bảo: 

- Bảo mật documentation nếu không muốn API có thể truy cập công khai
- Đảm bảo documentation được cập nhật để testers hợp pháp có thể nhìn thấy đầy đủ bề mặt tấn công của API
- Áp dụng *allowlist* HTTP methods được cho phép.
- Xác thực *content-type* đúng như mong đợi cho mỗi Request hoặc Response
- Sử dụng *error messages* chung chung để tránh cung cấp thông tin có thể hữu ích cho attacker
- Sử dụng các biện pháp bảo vệ trên tất cả API versions, không chỉ version hiện tại

Để ngăn chặn *Mass assignment vulnerabilities*, đưa các thuộc tính mà user có thể cập nhật vào *allowlist* và đưa vào *blocklist* các thuộc tính nhạy cảm mà user không nên cập nhật.
