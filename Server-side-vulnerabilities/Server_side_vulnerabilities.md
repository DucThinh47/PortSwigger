# Server-side vulnerabilities

## Content

- [What is path traversal?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-path-traversal)

### What is path traversal?

`Path traversal`, còn được gọi là `directory traversal`, là lỗ hổng bảo mật cho phép kẻ tấn công `đọc các tệp tùy ý trên máy chủ` đang chạy ứng dụng. Những tệp này có thể bao gồm:

- Mã nguồn và dữ liệu của ứng dụng.
- Thông tin xác thực của các hệ thống phụ trợ (back-end).
- Các tệp nhạy cảm của hệ điều hành.

Trong một số trường hợp, kẻ tấn công còn có thể `ghi vào các tệp tùy ý`trên máy chủ, cho phép chúng sửa đổi dữ liệu hoặc hành vi của ứng dụng, thậm chí `chiếm quyền kiểm soát` hoàn toàn máy chủ.



