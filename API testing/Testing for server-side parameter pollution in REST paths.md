# Testing for server-side parameter pollution in REST paths

Một RESTful API có thể đặt tên và giá trị params trong đường dẫn URL, thay vì chuỗi truy vấn. Ví dụ: xem xét đường dẫn sau:

![img](https://imgur.com/9MMNzCd.png)

Đường dẫn URL có thể được chia nhỏ như sau:

- /api là root API endpoint
- /users đại diện cho resource, trong TH này là users
- /123 đại diện cho param, ở đây là mã định danh cụ thể cho 1 user

Xem xét một ứng dụng cho phép chỉnh sửa hồ sơ người dùng dựa trên tên người dùng của họ. Request được gửi đến endpoint sau:

![img](https://imgur.com/b8ersNv.png)

Điều này dẫn đến server-side request sau:

![img](https://imgur.com/9OWcG9n.png)

Attacker có thể thao túng params đường dẫn URL phía máy chủ để khai thác API. Để kiểm tra lỗ hổng này, thêm trình tự truyền tải đường dẫn để sửa đổi params và quan sát cách ứng dụng response.

Có thể gửi *peter/../admin* được mã hóa URL làm giá trị của *name param*:

![img](https://imgur.com/PIVyHso.png)

Điều này dẫn đến server-side request sau:

![img](https://imgur.com/c2wwGhH.png)

Nếu server-side client hoặc back-end API bình thường hóa đường dẫn này thì đường dẫn này có thể được phân giải thành /api/private/users/admin.
