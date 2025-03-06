# Second-order SQL injection

SQL Injection Bậc Một (First-Order SQL Injection) xảy ra khi ứng dụng xử lý trực tiếp dữ liệu đầu vào từ một yêu cầu HTTP, sau đó chèn đầu vào này vào truy vấn SQL một cách không an toàn.

SQL Injection Bậc Hai (Second-Order SQL Injection) xảy ra khi ứng dụng lưu trữ dữ liệu đầu vào của người dùng để sử dụng sau này.

- Thông thường, đầu vào này sẽ được lưu vào cơ sở dữ liệu.
- Không có lỗ hổng ngay tại thời điểm lưu trữ dữ liệu.
- Khi xử lý một yêu cầu HTTP khác, ứng dụng truy xuất dữ liệu đã lưu và chèn nó vào một truy vấn SQL một cách không an toàn.

Vì lý do này, Second-Order SQL Injection còn được gọi là Stored SQL Injection.

Cách xảy ra Second-Order SQL Injection:

SQL Injection Bậc Hai thường xuất hiện trong các tình huống mà nhà phát triển đã có nhận thức về lỗ hổng SQL Injection.

- Họ xử lý an toàn khi lưu dữ liệu đầu vào ban đầu vào cơ sở dữ liệu.
- Tuy nhiên, khi dữ liệu được sử dụng lại sau này, họ cho rằng nó an toàn vì đã được lưu trước đó.
- Chính tại thời điểm này, dữ liệu bị xử lý một cách không an toàn, gây ra lỗ hổng SQL Injection. 