# Mass assignment vulnerabilities

*Mass assignment* - Gán hàng loạt (còn gọi là *auto-binding* - tự động liên kết) có thể vô tình tạo ra *hidden param*. Nó xảy ra khi software framework tự động liên kết request params với các trường objects bên trong. Do đó, việc gán hàng loạt (Mass assignment) có thể dẫn đến các tham số hỗ trợ ứng dụng không bao giờ được developer xử lý. 

# Identifying hidden parameters

Vì *mass assignment* tạo ra các tham số từ *object fields* nên thường có thể xác định các tham số ẩn này bằng cách kiểm tra thủ công các đối tượng được API trả về.

Ví dụ, xem xét request *PATCH /api/users/*, cho phép người dùng cập nhật *username* và *email*, đồng thời bao gồm JSON sau: 

![img](https://imgur.com/kRkxmhZ.png)

Request *GET /api/users/123* đồng thời trả về JSON sau: 

![img](https://imgur.com/lOW5X4Y.png)

Điều này chỉ ra rằng hidden param *id* và *isAdmin* được liên kết với *internal user object*, cùng vời param *email* và *username* đã cập nhật.

# Testing mass assignment vulnerabilities

Để kiểm tra xem có thể sửa đổi giá trị param *isAdmin* hay không, thêm nó vào *PATCH request*:

![img](https://imgur.com/Yb9G1rj.png)

Thêm vào đó, gửi *PATCH request* với giá trị *isAdmin param* không hợp lệ:

![img](https://imgur.com/XG39mQ4.png)

Nếu ứng dụng hoạt động khác đi, điều này có thể gợi ý rằng giá trị không hợp lệ tác động đến logic truy vấn, nhưng giá trị hợp lệ thì không. Điều này có thể chỉ ra rằng người dùng có thể cập nhật thành công tham số này.

Sau đó, có thể gửi *PATCH request* với giá trị *isAdmin param* được đặt thành *true* để thử và khai thác lỗ hổng.

![img](https://imgur.com/H5tZFnw.png)

Nếu giá trị *isadmin* trong request được liên kết với *user object* mà không được xác thực và sàng lọc đầy đủ, thì *user wiener* có thể được cấp đặc quyền quản trị viên không chính xác. Để xác định xem trường hợp này có xảy ra hay không, hãy duyệt ứng dụng dưới quyền user wiener để xem liệu có thể truy cập chức năng quản trị hay không.

# Lab: Exploiting a mass assignment vulnerability
![img](https://imgur.com/DC2MtAs.png)

### Web page:
![img](https://imgur.com/ptJ6dTo.png)

Chọn sản phẩm *Lightweight "l33t" Leather Jacket* và thêm vào cart. Mở Burp Suite và Intercept, click vào giỏ hàng và kiểm tra: 

![img](https://imgur.com/JWdEJrp.png)

--> Tìm được Request liên quan đến 1 API endpoint. Send request này tới Repeater: 

![img](https://imgur.com/izchL7e.png)

Send request và nhận được response: 

![img](https://imgur.com/2Wm8upf.png)

Server trả về response dạng JSON. Thử gửi OPTIONS method để kiểm tra method nào được cho phép:

![img](https://imgur.com/8AM6Csv.png)

--> Server chỉ cho phép GET và POST method.

Thử dùng POST method với body như sau: 

![img](https://imgur.com/OTNmbaj.png)

--> Nhận được response thông báo lỗi: 

![img](https://imgur.com/L2wbl1w.png)

Lần này thử nhập 1 số integer: 

![img](https://imgur.com/2sWUpxO.png)

--> Nhận được response: 

![img](https://imgur.com/DJMPEW5.png)

Có vẻ đã chỉnh sửa thành công phần trăm giảm giắ, thử kiểm tra lại với GET method:

![img](https://imgur.com/LIi1DYD.png)

Solved lab!

![img](https://imgur.com/a72dpOu.png)


