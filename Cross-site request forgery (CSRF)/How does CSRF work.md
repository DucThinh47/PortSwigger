# How does CSRF work?

Để CSRF attack xảy ra, 3 điều kiện chính phải được áp dụng: 

- **A relevant action**. Có một hành động trong ứng dụng mà kẻ tấn công có một lý do để gây ra. Đây có thể là một hành động đặc quyền (chẳng hạn như sửa đổi quyền cho người dùng khác) hoặc bất kỳ hành động nào trên dữ liệu dành riêng cho người dùng (chẳng hạn như thay đổi mật khẩu của chính người dùng).
- **Cookie-based session handling**. Thực hiện hành động liên quan đến việc phát hành một hoặc nhiều HTTP requests và ứng dụng chỉ dựa vào session cookie để xác định người dùng đã thực hiện các requests. Không có cơ chế nào khác để theo dõi sessions hoặc xác nhận requests của người dùng.
- **No unpredictable request parameters**. Requests thực hiện hành động không chứa bất kỳ param nào có giá trị mà kẻ tấn công không thể xác định hoặc đoán. Ví dụ: khi buộc người dùng thay đổi mật khẩu của họ, chức năng không dễ bị tổn thương nếu kẻ tấn công cần biết giá trị của mật khẩu hiện có.

# How does CSRF work? - Continued

Ví dụ: giả sử một ứng dụng chứa một chức năng cho phép người dùng thay đổi địa chỉ email trên tài khoản của họ. Khi người dùng thực hiện hành động này, họ sẽ thực hiện HTTP request như sau:

![img](https://imgur.com/DjzvQWD.png)

Điều này đáp ứng các điều kiện cần thiết cho CSRF diễn ra:

- Hành động thay đổi địa chỉ email trên tài khoản của người dùng được kẻ tấn công quan tâm. Theo hành động này, kẻ tấn công thường có thể reset password và kiểm soát toàn bộ tài khoản của người dùng.
- Web app sử dụng session cookie để xác định người dùng đã phát hành request nào. Không có tokens hoặc cơ chế nào khác để theo dõi sessions người dùng.
- Kẻ tấn công có thể dễ dàng xác định các giá trị của request params cần thiết để thực hiện hành động.

# How does CSRF work? - Continued

Với các điều kiện này, kẻ tấn công có thể xây dựng một trang web chứa HTML như sau:

![img](https://imgur.com/XKlDxN6.png)

# How does CSRF work? - Continued

Nếu người dùng truy cập trang web của kẻ tấn công, thì điều sau đây sẽ xảy ra:

- Page của kẻ tấn công sẽ kích hoạt HTTP request đến website dễ bị tổn thương.
- Nếu người dùng Log in vào website dễ bị tổn thương này, trình duyệt của họ sẽ tự động đưa session cookie của họ vào request (giả sử Samesite cookie không được sử dụng).
- Website dễ bị tổn thương này sẽ xử lý request theo cách thông thường, coi nó là do người dùng nạn nhân thực hiện và thay đổi địa chỉ email của họ.

**Lưu ý**

Mặc dù CSRF thường được mô tả liên quan đến xử lý session dựa trên cookie, nhưng nó cũng phát sinh trong các bối cảnh khác, trong đó web app tự động thêm một số thông tin đăng nhập của người dùng vào requests, chẳng hạn như HTTP Basic authentication và xác thực dựa trên chứng chỉ.
