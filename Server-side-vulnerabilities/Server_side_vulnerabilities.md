# Server-side vulnerabilities

## Content

- [Path traversal](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#path-traversal)

    - [What is path traversal?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-path-traversal)

- [Access control](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#access-control)

    - [What is access control?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-access-control)

    - [Vertical privilege escalation](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#vertical-privilege-escalation)

    - [Unprotected functionality](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#unprotected-functionality)

    - [Lab: Unprotected admin functionality](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-unprotected-admin-functionality)

    - [Unprotected functionality - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#unprotected-functionality---continued)

    - [Lab: Unprotected admin functionality with unpredictable URL](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-unprotected-admin-functionality-with-unpredictable-url)

    - [Parameter-based access control methods]()

    - [Lab: User role controlled by request parameter]()

### Path traversal

#### What is path traversal?

`Path traversal`, còn được gọi là `directory traversal`, là lỗ hổng bảo mật cho phép kẻ tấn công `đọc các tệp tùy ý trên máy chủ` đang chạy ứng dụng. Những tệp này có thể bao gồm:

- Mã nguồn và dữ liệu của ứng dụng.
- Thông tin xác thực của các hệ thống phụ trợ (back-end).
- Các tệp nhạy cảm của hệ điều hành.

Trong một số trường hợp, kẻ tấn công còn có thể `ghi vào các tệp tùy ý`trên máy chủ, cho phép chúng sửa đổi dữ liệu hoặc hành vi của ứng dụng, thậm chí `chiếm quyền kiểm soát` hoàn toàn máy chủ.

#### Reading arbitrary files via path traversal

Kịch bản là một ứng dụng mua sắm hiển thị hình ảnh của các mặt hàng đang được bán. Ứng dụng này có thể tải một hình ảnh bằng cách sử dụng đoạn HTML sau:

    <img src="/loadImage?filename=218.png">

URL `loadImage` nhận tham số `filename` và trả về nội dung của tệp được chỉ định. Các tệp hình ảnh được lưu trữ trên đĩa tại vị trí:

    /var/www/images/

Để trả về hình ảnh, ứng dụng nối tên tệp được yêu cầu với thư mục gốc này và sử dụng `API` của hệ thống tệp để đọc nội dung của tệp. Nói cách khác, ứng dụng sẽ đọc từ đường dẫn tệp sau:

    /var/www/images/218.png

Ứng dụng này `không triển khai bất kỳ biện pháp bảo vệ nào` chống lại các cuộc tấn công `path traversal`. Do đó, kẻ tấn công có thể gửi yêu cầu URL sau để lấy tệp `/etc/passwd`từ hệ thống tệp của máy chủ:

    https://insecure-website.com/loadImage?filename=../../../etc/passwd

Yêu cầu này khiến ứng dụng đọc từ đường dẫn tệp sau:

    /var/www/images/../../../etc/passwd

Cụm từ `../` là hợp lệ trong một đường dẫn tệp và có nghĩa là "`lùi lại một cấp trong cấu trúc thư mục`". Ba cụm `../` liên tiếp giúp di chuyển từ thư mục `/var/www/images/` lên đến gốc của hệ thống tệp (/), do đó tệp thực sự được đọc là:

    /etc/passwd

Trên các hệ điều hành dựa trên `Unix`, đây là một tệp chuẩn chứa `thông tin về người dùng đã đăng ký trên máy chủ`. Tuy nhiên, kẻ tấn công cũng có thể sử dụng cùng một kỹ thuật để lấy các tệp tùy ý khác.

**Path traversal trên Windows**

Trên hệ điều hành Windows, cả hai cụm `../` và `..\` đều là các chuỗi hợp lệ để traversal thư mục. Dưới đây là một ví dụ về cuộc tấn công tương tự trên máy chủ chạy Windows:

    https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini

#### Lab: File path traversal, simple case

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image1.png?raw=true)

Mở hình ảnh của 1 sản phẩm bất kỳ trên tab mới: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image2.png?raw=true)

Thay đổi giá trị tham số `filename` thành `/../../../etc/passwd`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image3.png?raw=true)

Solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image4.png?raw=true)

### Access control

#### What is access control?

Kiểm soát truy cập là việc `áp dụng các ràng buộc` đối với ai hoặc cái gì được phép thực hiện hành động hoặc truy cập tài nguyên. Trong bối cảnh ứng dụng web, kiểm soát truy cập phụ thuộc vào `xác thực` và `quản lý phiên`:

- `Xác thực` xác nhận rằng người dùng thực sự là người mà họ khai báo.

- `Quản lý phiên` xác định các yêu cầu HTTP tiếp theo nào đang được thực hiện bởi cùng một người dùng đó.

- `Kiểm soát truy cập` quyết định liệu người dùng có được phép thực hiện hành động mà họ đang cố gắng thực hiện hay không.

Các kiểm soát truy cập `bị hỏng` là điều phổ biến và thường tạo ra lỗ hổng bảo mật nghiêm trọng. Việc thiết kế và quản lý kiểm soát truy cập là một vấn đề phức tạp và thay đổi liên tục, áp dụng các ràng buộc từ kinh doanh, tổ chức và pháp lý vào một triển khai kỹ thuật. Các quyết định thiết kế kiểm soát truy cập phải do con người thực hiện, vì vậy `khả năng xảy ra lỗi là rất cao`.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image5.png?raw=true)

#### Vertical privilege escalation

Nếu một người dùng có thể `truy cập vào các chức năng` mà họ `không được phép truy cập`, thì đây được gọi là `nâng cấp đặc quyền theo chiều dọc`. Ví dụ, nếu một người dùng không phải là quản trị viên có thể truy cập vào trang quản trị, nơi họ có thể `xóa tài khoản người dùng`, thì đó là `nâng cấp đặc quyền theo chiều dọc`.

#### Unprotected functionality

Ở mức cơ bản nhất, `nâng cấp đặc quyền theo chiều dọc` xảy ra khi một ứng dụng không áp dụng bất kỳ biện pháp bảo vệ nào cho các chức năng nhạy cảm. Ví dụ, các chức năng quản trị có thể được liên kết từ trang chào mừng của quản trị viên, nhưng không có trên trang chào mừng của người dùng thông thường. Tuy nhiên, một người dùng vẫn có thể truy cập vào các chức năng quản trị bằng cách `điều hướng trực tiếp đến URL quản trị tương ứng`.

Chẳng hạn, một trang web có thể `lưu trữ chức năng nhạy cảm` tại URL sau:

    https://insecure-website.com/admin

URL này có thể được truy cập bởi `bất kỳ người dùng nào`, không chỉ những người dùng quản trị có liên kết đến chức năng đó trong giao diện của họ. Trong một số trường hợp, URL quản trị có thể bị tiết lộ ở các vị trí khác, chẳng hạn như trong tệp `robots.txt`:

    https://insecure-website.com/robots.txt

Ngay cả khi URL không được tiết lộ ở bất kỳ đâu, kẻ tấn công vẫn có thể sử dụng `danh sách từ` (wordlist) để `dò tìm`(brute-force) vị trí của chức năng nhạy cảm.

#### Lab: Unprotected admin functionality

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image6.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image7.png?raw=true)

Thử truy cập `/robots.txt`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image8.png?raw=true)

-> Tìm được URL dẫn đến trang quản trị viên là `/administrator-panel`. Truy cập URL này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image9.png?raw=true)

Xóa user carlos và solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image10.png?raw=true)

#### Unprotected functionality - Continued

Trong một số trường hợp, chức năng nhạy cảm được che giấu bằng cách sử dụng một `URL khó dự đoán hơn`. Đây là một ví dụ về cái gọi là `"bảo mật bằng cách che giấu"` (security by obscurity). Tuy nhiên, việc ẩn chức năng nhạy cảm `không cung cấp kiểm soát truy cập hiệu quả`, vì người dùng có thể phát hiện ra URL được che giấu theo nhiều cách khác nhau.

Tưởng tượng một ứng dụng lưu trữ các chức năng quản trị tại URL sau:

    https://insecure-website.com/administrator-panel-yb556

URL này có thể không dễ dàng bị kẻ tấn công đoán ra. Tuy nhiên, ứng dụng vẫn có thể `vô tình để lộ URL` cho người dùng. URL có thể bị tiết lộ trong `mã JavaScript` dùng để xây dựng giao diện người dùng `dựa trên vai trò` của người dùng:

    <script>
        var isAdmin = false;
        if (isAdmin) {
            ...
            var adminPanelTag = document.createElement('a');
            adminPanelTag.setAttribute('href', 'https://insecure-website.com/administrator-panel-yb556');
            adminPanelTag.innerText = 'Admin panel';
            ...
        }
    </script>

Mã script này `thêm một liên kết` vào giao diện người dùng nếu người dùng là quản trị viên. Tuy nhiên, đoạn script chứa URL này lại `hiển thị cho tất cả người dùng`, bất kể vai trò của họ là gì.

#### Lab: Unprotected admin functionality with unpredictable URL

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image11.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image12.png?raw=true)

Xem source code của page: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image13.png?raw=true)

-> Tìm ra URL truy cập trang admin là `/admin-5cxugs`. Truy cập URL: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image14.png?raw=true)

Xóa user carlos, solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image15.png?raw=true)

#### Parameter-based access control methods

Một số ứng dụng xác định quyền truy cập hoặc vai trò của người dùng `khi đăng nhập`, sau đó lưu trữ thông tin này ở một vị trí mà người dùng có thể kiểm soát. Điều này có thể là:

- Một trường ẩn (hidden field).
- Một cookie.
- Một tham số chuỗi truy vấn được đặt trước (preset query string parameter).

Ứng dụng đưa ra quyết định kiểm soát truy cập dựa trên `giá trị được gửi lên`. Ví dụ:

    https://insecure-website.com/login/home.jsp?admin=true
    
    https://insecure-website.com/login/home.jsp?role=1

Cách tiếp cận này `không an toàn` vì người dùng có thể thay đổi giá trị và truy cập vào các chức năng mà họ không được phép, chẳng hạn như các chức năng quản trị.

#### Lab: User role controlled by request parameter

![img](16)

Access the lab: 

![img](17)

Đăng nhập vào account `wiener:peter`:

![img](18)

Request yêu cầu trang thông tin account của `wiener`:

![img](19)

-> Tìm được cookie tên `Admin` có giá trị đang là `false`. Thay đổi giá trị này thành `true` và refresh lại page: 

![img](20)

-> Click vào `Admin panel`: 

![img](21)

Xóa user `carlos`, Solve the lab!

![img](22)















