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

    - [Parameter-based access control methods](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#parameter-based-access-control-methods)

    - [Lab: User role controlled by request parameter](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-role-controlled-by-request-parameter)

    - [Horizontal privilege escalation](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#horizontal-privilege-escalation)

    - [Lab: User ID controlled by request parameter, with unpredictable user IDs](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)

    - [Horizontal to vertical privilege escalation](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#horizontal-to-vertical-privilege-escalation)

    - [Lab: User ID controlled by request parameter with password disclosure](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-user-id-controlled-by-request-parameter-with-password-disclosure)

- [Authentication](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#authentication)

    - [Authentication vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#authentication-vulnerabilities)

    - [What is the difference between authentication and authorization?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-the-difference-between-authentication-and-authorization)

    - [Brute-force attacks](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#brute-force-attacks)

    - [Brute-forcing usernames](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#brute-forcing-usernames)

    - [Brute-forcing passwords](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#brute-forcing-passwords)

    - [Brute-forcing passwords - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#brute-forcing-passwords---continued)

    - [Username enumeration](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#username-enumeration)

    - [Lab: Username enumeration via different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-username-enumeration-via-different-responses)

    - [Bypassing two-factor authentication](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#bypassing-two-factor-authentication)

    - [Lab: 2FA simple bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-2fa-simple-bypass)

- [Server-side request forgery (SSRF)](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#server-side-request-forgery-ssrf)

    - [What is SSRF?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-ssrf)

    - [SSRF attacks against the server](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#ssrf-attacks-against-the-server)

    - [SSRF attacks against the server - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#ssrf-attacks-against-the-server---continued)

    - [Lab: Basic SSRF against the local server](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-basic-ssrf-against-the-local-server)

    - [SSRF attacks against other back-end systems](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#ssrf-attacks-against-other-back-end-systems)

    - [Lab: Basic SSRF against another back-end system](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-basic-ssrf-against-another-back-end-system)

- [File upload vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#file-upload-vulnerabilities)

    - [What are file upload vulnerabilities?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-are-file-upload-vulnerabilities)

    - [How do file upload vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#how-do-file-upload-vulnerabilities-arise)

    - [Exploiting unrestricted file uploads to deploy a web shell](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#exploiting-unrestricted-file-uploads-to-deploy-a-web-shell)

    - [Lab: Remote code execution via web shell upload](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-remote-code-execution-via-web-shell-upload)

    - [Exploiting flawed validation of file uploads](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#exploiting-flawed-validation-of-file-uploads)

    - [Flawed file type validation](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#flawed-file-type-validation)

    - [Flawed file type validation - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#flawed-file-type-validation---continued)

    - [Flawed file type validation - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#flawed-file-type-validation---continued-1)

    - [Lab: Web shell upload via Content-Type restriction bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-web-shell-upload-via-content-type-restriction-bypass)

- [OS command injection](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#os-command-injection)

    - [What is OS command injection?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#what-is-os-command-injection)

    - [Useful commands](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#useful-commands)

    - [Injecting OS commands](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#injecting-os-commands)

    - [Lab: OS command injection, simple case](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/Server_side_vulnerabilities.md#lab-os-command-injection-simple-case)

- [SQL injection]()

    - [What is SQL injection (SQLi)?]()

    - [How to detect SQL injection vulnerabilities]()

    - [Retrieving hidden data]()

    - [Retrieving hidden data - Continued]()

    - [Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data]()

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

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image16.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image17.png?raw=true)

Đăng nhập vào account `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image18.png?raw=true)

Request yêu cầu trang thông tin account của `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image19.png?raw=true)

-> Tìm được cookie tên `Admin` có giá trị đang là `false`. Thay đổi giá trị này thành `true` và refresh lại page: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image20.png?raw=true)

-> Click vào `Admin panel`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image21.png?raw=true)

Xóa user `carlos`, Solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image22.png?raw=true)

#### Horizontal privilege escalation

`Nâng cấp đặc quyền theo chiều ngang` xảy ra khi một người dùng có thể truy cập vào tài nguyên của người dùng khác, thay vì chỉ truy cập vào tài nguyên của chính họ thuộc loại đó. Ví dụ, nếu một nhân viên có thể `truy cập vào hồ sơ của các nhân viên khác` ngoài hồ sơ của chính mình, thì đó là nâng cấp đặc quyền theo chiều ngang.

Các cuộc tấn công nâng cấp đặc quyền theo chiều ngang có thể sử dụng các phương pháp khai thác `tương tự` như nâng cấp đặc quyền theo chiều dọc. Chẳng hạn, một người dùng có thể `truy cập trang tài khoản của chính mình` bằng URL sau:

    https://insecure-website.com/myaccount?id=123

Nếu kẻ tấn công thay đổi giá trị `tham số id` sang giá trị của một người dùng khác, họ có thể truy cập vào trang tài khoản của người dùng đó, cùng với dữ liệu và chức năng liên quan.

*Note*: 

Đây là một ví dụ về lỗ hổng `tham chiếu đối tượng trực tiếp không an toàn` (Insecure Direct Object Reference - IDOR). Loại lỗ hổng này xuất hiện khi các giá trị tham số do người dùng kiểm soát được sử dụng để `truy cập trực tiếp` vào tài nguyên hoặc chức năng.

Trong một số ứng dụng, tham số có thể khai thác `không có giá trị dễ dự đoán`. Ví dụ, thay vì sử dụng số tăng dần, một ứng dụng có thể sử dụng các `định danh duy nhất toàn cầu` (GUIDs) để xác định người dùng. Điều này có thể ngăn kẻ tấn công đoán hoặc dự đoán định danh của người dùng khác. Tuy nhiên, các `GUID` thuộc về người dùng khác có thể bị tiết lộ `ở những nơi khác` trong ứng dụng, chẳng hạn như trong `tin nhắn của người dùng` hoặc `phần đánh giá`.

#### Lab: User ID controlled by request parameter, with unpredictable user IDs

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image23.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image24.png?raw=true)

Đăng nhập vào account `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image25.png?raw=true)

Tìm được blog được đăng bởi `carlos`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image26.png?raw=true)

Click vào `carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image27.png?raw=true)

-> Tìm được `GUID` của carlos là `fcbbd428-424a-4c99-b58c-2278803d399e`

Click `My account` và thay đổi giá trị tham số `id` hiện tại của `wiener` thành `GUID` của `carlos`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image28.png?raw=true)

-> Truy cập thành công account `carlos`, submit API key, solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image29.png?raw=true)

#### Horizontal to vertical privilege escalation

Thông thường, một cuộc tấn công nâng cấp đặc quyền theo chiều ngang có thể được chuyển thành nâng cấp đặc quyền theo chiều dọc bằng cách `xâm phạm một người dùng có đặc quyền cao hơn`. Ví dụ, một cuộc tấn công nâng cấp ngang có thể cho phép kẻ tấn công `đặt lại` hoặc `chiếm đoạt` mật khẩu của một người dùng khác. Nếu kẻ tấn công nhắm vào một `người dùng quản trị` và xâm phạm tài khoản của họ, thì họ có thể giành được quyền truy cập quản trị, từ đó thực hiện nâng cấp đặc quyền theo chiều dọc.

Kẻ tấn công có thể truy cập vào trang tài khoản của người dùng khác bằng kỹ thuật `thay đổi tham số` đã được mô tả trong nâng cấp đặc quyền theo chiều ngang:

    https://insecure-website.com/myaccount?id=456

Nếu người dùng mục tiêu là một quản trị viên ứng dụng, thì kẻ tấn công sẽ giành được quyền truy cập vào trang tài khoản quản trị. Trang này có thể tiết lộ `mật khẩu của quản trị viên`, cung cấp phương tiện để thay đổi mật khẩu, hoặc thậm chí cho phép truy cập trực tiếp vào các chức năng đặc quyền.

#### Lab: User ID controlled by request parameter with password disclosure

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image30.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image31.png?raw=true)

Đăng nhập vào account `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image32.png?raw=true)

Thử thay đổi giá trị tham số `id` thành `administrator`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image33.png?raw=true)

-> Truy cập thành công trang my account của `administrator`. Request yêu cầu trang my account của `administrator`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image34.png?raw=true)

-> Tìm được mật khẩu của `administrator` là `y1aq7w5lie7ev014szja`. Đăng nhập vào tài khoản `administrator`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image35.png?raw=true)

Xóa user `carlos`, solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image36.png?raw=true)

### Authentication

#### Authentication vulnerabilities

Về mặt khái niệm, các lỗ hổng xác thực khá dễ hiểu. Tuy nhiên, chúng thường rất nghiêm trọng vì mối liên hệ rõ ràng giữa xác thực và bảo mật.

Các lỗ hổng xác thực có thể cho phép kẻ tấn công truy cập vào dữ liệu nhạy cảm và các chức năng quan trọng. Chúng cũng `mở ra thêm bề mặt tấn công` để khai thác sâu hơn. Vì lý do này, việc học cách nhận diện và khai thác các lỗ hổng xác thực, cũng như cách vượt qua các biện pháp bảo vệ phổ biến, là rất quan trọng.

Trong phần này, giải thích:

- Các cơ chế xác thực phổ biến nhất được sử dụng bởi các trang web.
- Các lỗ hổng tiềm ẩn trong những cơ chế này.
- Các lỗ hổng vốn có trong các cơ chế xác thực khác nhau.
- Các lỗ hổng điển hình xuất hiện do việc triển khai không đúng cách.
- Cách có thể làm cho cơ chế xác thực trở nên mạnh mẽ nhất có thể.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image37.png?raw=true)

#### What is the difference between authentication and authorization?

`Xác thực` là quá trình `xác minh` rằng một người dùng `thực sự là người mà họ tuyên bố`. `Phân quyền` liên quan đến việc `xác minh` liệu một người dùng có `được phép thực hiện` điều gì đó hay không.

Ví dụ, xác thực xác định liệu một người đang cố gắng truy cập vào một trang web với tên người dùng `Carlos123` có thực sự `là người đã tạo tài khoản` đó hay không.

Khi `Carlos123` đã được xác thực, `các quyền` của họ sẽ quyết định những gì họ `được phép làm`. Chẳng hạn, họ có thể được phép truy cập thông tin cá nhân của người dùng khác hoặc thực hiện các hành động như xóa tài khoản của người dùng khác.

#### Brute-force attacks

`Tấn công vét cạn` là khi kẻ tấn công sử dụng phương pháp `thử và sai` để `đoán thông tin` đăng nhập hợp lệ của người dùng. Các cuộc tấn công này thường được `tự động hóa` bằng cách sử dụng `danh sách từ` (wordlists) chứa tên người dùng và mật khẩu. Việc tự động hóa quá trình này, đặc biệt khi sử dụng các công cụ chuyên dụng, có thể cho phép kẻ tấn công thực hiện một số lượng lớn các lần thử đăng nhập với tốc độ cao.

Việc vét cạn không phải lúc nào cũng chỉ là đoán ngẫu nhiên hoàn toàn về tên người dùng và mật khẩu. Bằng cách kết hợp logic cơ bản hoặc kiến thức công khai có sẵn, kẻ tấn công có thể tinh chỉnh các cuộc `tấn công vét cạn` để đưa ra những `phỏng đoán có cơ sở` hơn nhiều. Điều này làm tăng đáng kể hiệu quả của các cuộc tấn công như vậy. Các trang web `chỉ dựa vào đăng nhập bằng mật khẩu` làm phương pháp xác thực duy nhất có thể `rất dễ bị tổn thương` nếu không triển khai các biện pháp bảo vệ đầy đủ chống lại `tấn công vét cạn`.

#### Brute-forcing usernames

Tên người dùng `đặc biệt dễ đoán` nếu chúng tuân theo một `mô hình dễ nhận biết`, chẳng hạn như `địa chỉ email`. Ví dụ, rất phổ biến khi thấy các thông tin đăng nhập doanh nghiệp có định dạng `firstname.lastname@somecompany.com`. Tuy nhiên, ngay cả khi không có mô hình rõ ràng, đôi khi các `tài khoản có đặc quyền cao` vẫn được tạo bằng những `tên người dùng dễ dự đoán`, như `admin` hoặc `administrator`.

Trong quá trình kiểm tra, hãy xem xét liệu trang web có công khai tiết lộ `tên người dùng tiềm năng` hay không. Chẳng hạn, có thể truy cập hồ sơ người dùng mà `không cần đăng nhập` không? Ngay cả khi nội dung thực tế của hồ sơ bị ẩn, `tên được sử dụng trong hồ sơ` đôi khi `trùng với tên người dùng đăng nhập`. Cũng nên kiểm tra các `phản hồi HTTP` để xem liệu có `địa chỉ email` nào bị tiết lộ hay không. Thỉnh thoảng, các phản hồi chứa địa chỉ email của những người dùng có đặc quyền cao, chẳng hạn như quản trị viên hoặc bộ phận hỗ trợ IT.

#### Brute-forcing passwords

Mật khẩu cũng có thể bị vét cạn tương tự, với độ khó tùy thuộc vào độ mạnh của mật khẩu. Nhiều trang web áp dụng một số hình thức `chính sách mật khẩu`, buộc người dùng phải tạo mật khẩu có `entropy cao`, ít nhất về mặt lý thuyết, khó bị bẻ khóa chỉ bằng vét cạn. Điều này thường bao gồm việc yêu cầu mật khẩu có:

- Số ký tự tối thiểu
- Sự kết hợp giữa chữ cái in thường và in hoa
- Ít nhất một ký tự đặc biệt

#### Brute-forcing passwords - Continued

Tuy nhiên, mặc dù mật khẩu có entropy cao khó bị máy tính bẻ khóa chỉ bằng cách vét cạn, có thể sử dụng kiến thức cơ bản về hành vi con người để khai thác những lỗ hổng mà người dùng vô tình tạo ra trong hệ thống này. Thay vì tạo một mật khẩu mạnh với sự kết hợp ngẫu nhiên của các ký tự, người dùng thường chọn một mật khẩu mà họ có thể nhớ và cố gắng điều chỉnh nó để phù hợp với chính sách mật khẩu. Ví dụ, nếu `mypassword` không được chấp nhận, người dùng có thể thử biến nó thành `Mypassword1!` hoặc `Myp4$$w0rd`.

Trong trường hợp chính sách yêu cầu người dùng `thay đổi mật khẩu định kỳ`, cũng rất phổ biến khi người dùng chỉ thực hiện những thay đổi nhỏ, dễ đoán đối với mật khẩu ưa thích của họ. Chẳng hạn, `Mypassword1!` có thể trở thành `Mypassword1?` hoặc `Mypassword2!`.

Kiến thức về các thông tin đăng nhập có khả năng xảy ra và các mô hình dễ đoán này có nghĩa là các cuộc tấn công vét cạn thường có thể `tinh vi hơn nhiều`, và do đó hiệu quả hơn, so với việc chỉ đơn thuần lặp qua mọi tổ hợp ký tự có thể.

#### Username enumeration

`Liệt kê tên người dùng` là khi kẻ tấn công có thể quan sát `sự thay đổi trong hành vi` của trang web để xác định liệu một `tên người dùng cụ thể có hợp lệ` hay không.

Việc `liệt kê tên người dùng` thường xảy ra trên `trang đăng nhập`, chẳng hạn khi nhập một `tên người dùng hợp lệ` nhưng `mật khẩu không chính xác`, hoặc trên các biểu mẫu đăng ký khi bạn nhập một `tên người dùng đã được sử dụng`. Điều này giúp giảm đáng kể thời gian và công sức cần thiết để vét cạn thông tin đăng nhập, bởi vì kẻ tấn công có thể nhanh chóng tạo ra một `danh sách ngắn các tên người dùng hợp lệ`.

#### Lab: Username enumeration via different responses

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image38.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image39.png?raw=true)

Nhập 1 username và password bất kỳ: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image40.png?raw=true)

POST request `/login` sẽ như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image41.png?raw=true)

Send request này sang Burp Intruder: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image42.png?raw=true)

Chọn 2 payload positions là username và password -> Chọn Cluster bomb attack.

Payload options cho username: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image43.png?raw=true)

Payload options cho password: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image44.png?raw=true)

Start attack!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image45.png?raw=true)

Tìm được username trả về response "Incorrect password".

-> Username: `announcements`

Tiếp tục brute-force password với username này. Tìm được 1 password có Status code là 302:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image46.png?raw=true)

-> Password: `dallas`

Login vào account, solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image47.png?raw=true)

#### Bypassing two-factor authentication

Đôi khi, việc triển khai xác thực hai yếu tố `có sai sót` đến mức nó có thể bị vượt qua hoàn toàn.

Nếu người dùng được yêu cầu `nhập mật khẩu trước`, sau đó được nhắc `nhập mã xác minh trên một trang riêng biệt`, thì thực tế người dùng đã ở `trạng thái "đã đăng nhập"` trước khi họ nhập mã xác minh. Trong trường hợp này, nên thử nghiệm để xem liệu có thể `trực tiếp chuyển đến các trang "chỉ dành cho người đã đăng nhập"` sau khi hoàn thành bước xác thực đầu tiên hay không. Thỉnh thoảng, sẽ phát hiện ra rằng một trang web `không thực sự kiểm tra` xem đã hoàn thành bước thứ hai hay chưa trước khi tải trang.

#### Lab: 2FA simple bypass

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image48.png?raw=true)

Access the lab:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image49.png?raw=true)

Đăng nhập vào tài khoản wiener -> Đăng xuất -> Đăng nhập tài khoản carlos -> Khi yêu cầu nhập mã xác nhận -> Thay đổi URL thành `/my-account`.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image50.png?raw=true)

### Server-side request forgery (SSRF)

#### What is SSRF?

`Server-side request forgery` (giả mạo yêu cầu phía máy chủ) là một lỗ hổng bảo mật web cho phép kẻ tấn công khiến `ứng dụng phía máy chủ` thực hiện các yêu cầu tới `một vị trí không mong muốn`.

Trong một cuộc tấn công SSRF điển hình, kẻ tấn công có thể khiến máy chủ kết nối với `các dịch vụ chỉ dành cho nội bộ` trong cơ sở hạ tầng của tổ chức. Trong các trường hợp khác, họ có thể buộc máy chủ kết nối với `các hệ thống bên ngoài bất kỳ`. Điều này có thể làm rò rỉ dữ liệu nhạy cảm, chẳng hạn như thông tin xác thực ủy quyền.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image51.png?raw=true)

#### SSRF attacks against the server

Trong một cuộc tấn công SSRF nhắm vào máy chủ, kẻ tấn công khiến ứng dụng thực hiện một `yêu cầu HTTP quay lại chính máy chủ đang lưu trữ ứng dụng`, thông qua giao diện mạng `loopback` của nó. Điều này thường liên quan đến việc cung cấp một URL với tên máy chủ như `127.0.0.1` (một địa chỉ IP dành sẵn `trỏ đến bộ điều hợp loopback`) hoặc `localhost` (một tên thường được sử dụng cho cùng bộ điều hợp đó).

Ví dụ, một ứng dụng mua sắm cho phép người dùng xem liệu một mặt hàng `có còn trong kho tại một cửa hàng cụ thể hay không`. Để cung cấp thông tin tồn kho, ứng dụng phải truy vấn các `API REST` phía sau khác nhau. Nó thực hiện điều này bằng cách truyền URL tới `điểm cuối API phía sau` liên quan thông qua một yêu cầu HTTP từ phía trước. Khi người dùng xem trạng thái tồn kho của một mặt hàng, trình duyệt của họ gửi yêu cầu sau:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1

Điều này khiến máy chủ thực hiện một yêu cầu tới URL được chỉ định, lấy trạng thái tồn kho và trả lại thông tin này cho người dùng.

Trong ví dụ này, kẻ tấn công có thể `sửa đổi yêu cầu` để chỉ định một `URL cục bộ` trên máy chủ:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://localhost/admin

Máy chủ sẽ lấy nội dung của URL `/admin` và trả lại cho người dùng.

Kẻ tấn công có thể truy cập URL `/admin`, nhưng chức năng quản trị thường chỉ dành cho người dùng đã xác thực. Điều này có nghĩa là kẻ tấn công sẽ không thấy bất kỳ điều gì đáng chú ý. Tuy nhiên, nếu yêu cầu tới URL `/admin` đến từ chính `máy cục bộ`, các biện pháp kiểm soát truy cập thông thường sẽ bị vượt qua. Ứng dụng cấp quyền truy cập đầy đủ vào chức năng quản trị, bởi vì yêu cầu dường như xuất phát từ `một vị trí đáng tin cậy`.

#### SSRF attacks against the server - Continued

Tại sao các ứng dụng lại hoạt động theo cách này và `ngầm tin tưởng các yêu cầu đến từ máy cục bộ`? Điều này có thể xảy ra vì nhiều lý do khác nhau:

- Kiểm tra kiểm soát truy cập có thể được triển khai trong một thành phần khác `nằm trước` máy chủ ứng dụng. Khi một kết nối được thực hiện trở lại máy chủ, kiểm tra này bị bỏ qua.

- Vì mục đích `khôi phục sau thảm họa`, ứng dụng có thể cho phép truy cập quản trị mà `không cần đăng nhập`, đối với bất kỳ người dùng nào đến từ `máy cục bộ`. Điều này cung cấp một cách để quản trị viên khôi phục hệ thống nếu họ mất thông tin đăng nhập. Giả định ở đây là chỉ có người dùng hoàn toàn đáng tin cậy mới có thể đến trực tiếp từ máy chủ.

- Giao diện quản trị có thể lắng nghe trên `một số cổng khác` với ứng dụng chính và có thể `không thể truy cập trực tiếp` bởi người dùng.

Những mối quan hệ tin cậy kiểu này, nơi các `yêu cầu xuất phát từ máy cục bộ` được `xử lý khác biệt` so với các `yêu cầu thông thường`, thường khiến `SSRF` trở thành một lỗ hổng nghiêm trọng.

#### Lab: Basic SSRF against the local server

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image52.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image53.png?raw=true)

Click vào 1 sản phẩm bất kỳ:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image54.png?raw=true)

Sản phẩm có chức năng check stock. POST request trông như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image55.png?raw=true)

-> Request được gửi đến endpoint `/product/stock`, ứng dụng phía máy chủ sẽ sử dụng URL này để gửi một yêu cầu tới API tồn kho (stock.weliketoshop.net) và lấy thông tin về trạng thái tồn kho.

-> Tham số `stockApi` cho phép truyền một URL tùy ý, kẻ tấn công có thể thay đổi giá trị này để buộc máy chủ gửi yêu cầu tới một địa chỉ khác.

Thử thay giá trị tham số `stockApi` thành:

    http://localhost/admin

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image56.png?raw=true)

Send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image57.png?raw=true)

-> Truy cập thành công trang admin. Đồng thời tìm được URL để xóa user `carlos`: `/admin/delete?username=carlos`

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image58.png?raw=true)

Thay giá trị tham số `stockApi` thành:

    http://localhost/admin/delete?username=carlos

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image59.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image60.png?raw=true)

#### SSRF attacks against other back-end systems

Trong một số trường hợp, máy chủ ứng dụng có thể tương tác với `các hệ thống back-end` mà người dùng không thể truy cập trực tiếp. Những hệ thống này thường sử dụng `các địa chỉ IP riêng` không thể định tuyến được (non-routable private IP addresses). Các hệ thống back-end thường được bảo vệ bởi `cấu trúc mạng`, do đó chúng thường có `tư thế bảo mật yếu hơn`. Trong nhiều trường hợp, các hệ thống back-end nội bộ chứa các chức năng nhạy cảm mà bất kỳ ai có khả năng tương tác với hệ thống đều có thể truy cập mà `không cần xác thực`.

Trong ví dụ trước, tưởng tượng có một giao diện quản trị tại URL back-end `https://192.168.0.68/admin`. Kẻ tấn công có thể gửi yêu cầu sau để khai thác lỗ hổng SSRF và truy cập vào giao diện quản trị:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://192.168.0.68/admin

#### Lab: Basic SSRF against another back-end system

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image61.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image62.png?raw=true)

Click vào 1 sản phẩm bất kỳ: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image63.png?raw=true)

Website có chức năng check stock. POST request trông như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image64.png?raw=true)

Theo mô tả thử thách, trang admin có thể truy cập qua URL:

    http://192.168.0.X:8080

Thử thay giá trị tham số `stockApi` thành:

    http://192.168.0.10:8080

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image65.png?raw=true)

-> Cần tìm ra giá trị của `octet` cuối cùng. 

Send request tới Burp Intruder:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image66.png?raw=true)

-> Chọn payload position ở octet cuối cùng. Payload option: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image67.png?raw=true)

Start attack!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image68.png?raw=true)

Tìm được địa chỉ ip `192.168.0.55` trả về status code `404`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image69.png?raw=true)

Có thể là địa chỉ IP nội bộ của hệ thống back-end mà người dùng bình thường không thể tìm thấy hay truy cập trực tiếp. 

Thử gửi request với giá trị tham số `stockApi` là `http://192.168.0.55:8080/admin`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image70.png?raw=true)

-> Truy cập thành công trang admin. Tìm được URL dẫn đến việc xóa user `carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image71.png?raw=true)

Thay giá trị tham số `stockApi` thành `http://192.168.0.55:8080/admin/delete?username=carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image72.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image73.png?raw=true)

### File upload vulnerabilities

#### What are file upload vulnerabilities?

Các lỗ hổng tải tệp lên xảy ra khi một máy chủ web cho phép người dùng tải tệp lên hệ thống tệp của nó mà `không kiểm tra đầy đủ các yếu tố` như `tên`, `loại`, `nội dung` hoặc `kích thước` của tệp. Việc không áp dụng các hạn chế một cách nghiêm ngặt đối với những yếu tố này có thể dẫn đến việc ngay cả một `chức năng tải ảnh` cơ bản cũng có thể `bị lợi dụng` để tải lên các tệp tùy ý và tiềm ẩn nguy hiểm. Điều này thậm chí có thể bao gồm `các tệp mã kịch bản` phía máy chủ, cho phép `thực thi mã từ xa`.

Trong một số trường hợp, chỉ riêng hành động tải tệp lên đã đủ để gây thiệt hại. Các cuộc tấn công khác có thể liên quan đến một yêu cầu HTTP tiếp theo để truy cập tệp, thường nhằm kích hoạt việc thực thi tệp đó bởi máy chủ.

#### How do file upload vulnerabilities arise?

Với những nguy cơ khá rõ ràng, hiếm có trang web nào trong thực tế lại không áp dụng bất kỳ hạn chế nào đối với loại tệp mà người dùng được phép tải lên. Thường thì các nhà phát triển triển khai những gì họ tin là biện pháp xác thực mạnh mẽ, nhưng những biện pháp này hoặc là `có lỗ hổng cố hữu`, hoặc `có thể dễ dàng bị qua mặt`.

Ví dụ, họ có thể cố gắng chặn các loại tệp nguy hiểm bằng `danh sách đen`, nhưng không tính đến sự khác biệt trong cách phân tích khi kiểm tra `phần mở rộng của tệp`. Như với bất kỳ danh sách đen nào, việc `vô tình bỏ sót các loại tệp ít phổ biến` hơn nhưng vẫn tiềm ẩn nguy hiểm là điều dễ xảy ra.

Trong các trường hợp khác, trang web có thể cố gắng kiểm tra loại tệp bằng cách xác minh `các thuộc tính` mà kẻ tấn công có thể `dễ dàng thao túng` bằng các công cụ như `Burp Proxy` hoặc `Repeater`.

Cuối cùng, ngay cả những biện pháp xác thực mạnh mẽ cũng có thể được `áp dụng không đồng đều` trên mạng lưới các máy chủ và thư mục tạo nên trang web, dẫn đến những khác biệt mà kẻ tấn công có thể khai thác.

#### Exploiting unrestricted file uploads to deploy a web shell

Từ góc độ bảo mật, kịch bản `tồi tệ nhất` có thể xảy ra là khi một trang web cho phép tải lên `các tệp mã kịch bản phía máy chủ`, chẳng hạn như tệp `PHP`, `Java` hoặc `Python`, và đồng thời được cấu hình để `thực thi chúng như mã lệnh`. Điều này khiến việc tạo ra một `web shell` của trên máy chủ trở nên cực kỳ đơn giản.

**Web shell**

`Web shell` là một `mã kịch bản độc hại` cho phép kẻ tấn công `thực thi các lệnh tùy ý` trên máy chủ web `từ xa` chỉ bằng cách gửi các yêu cầu HTTP đến đúng điểm cuối (endpoint).

Nếu thành công trong việc tải lên một `web shell`, gần như có toàn quyền kiểm soát máy chủ. Điều này có nghĩa là có thể đọc và ghi các tệp tùy ý, trích xuất dữ liệu nhạy cảm, thậm chí sử dụng máy chủ để làm bàn đạp tấn công vào cả cơ sở hạ tầng nội bộ lẫn các máy chủ khác bên ngoài mạng. Chẳng hạn, đoạn mã `PHP` một dòng sau có thể được dùng để đọc các tệp tùy ý từ hệ thống tệp của máy chủ:

    <?php echo file_get_contents('/path/to/target/file'); ?>

Sau khi tải lên, việc gửi một yêu cầu đến tệp độc hại này sẽ trả về `nội dung của tệp mục tiêu` trong phản hồi.

Một `web shell` linh hoạt hơn có thể trông như thế này:

    <?php echo system($_GET['command']); ?>

Mã kịch bản này cho phép `truyền một lệnh hệ thống tùy ý` thông qua tham số truy vấn như sau:

    GET /example/exploit.php?command=id HTTP/1.1

*(Khi đó, lệnh "id" sẽ được thực thi trên máy chủ, và kết quả sẽ được trả về trong phản hồi.)*

#### Lab: Remote code execution via web shell upload

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image74.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image75.png?raw=true)

Đăng nhập vào tài khoản `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image76.png?raw=true)

Thử upload 1 file .jpg đơn giản: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image78.png?raw=true)

Theo mô tả bài lab, cần đọc nội dung của file `/home/carlos/secret`. Tạo 1 file `my_shell.php` đơn giản:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image77.png?raw=true)

Thay đổi GET request tìm nạp avatar thành: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image79.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image80.png?raw=true)

-> Tìm được nội dung file `/home/carlos/secret` là `D4rB4LKzG2lxVeHrzWrcyNUOK6mQV4nU`. Submit nội dung và solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image81.png?raw=true)

#### Exploiting flawed validation of file uploads

Trong thực tế, `hiếm có` trang web nào `hoàn toàn không có biện pháp bảo vệ` chống lại các cuộc tấn công tải tệp đã thấy trong phần trước. Tuy nhiên, việc có các biện pháp bảo vệ không đồng nghĩa với việc chúng đủ mạnh mẽ. Đôi khi `vẫn có thể khai thác` những lỗ hổng trong các cơ chế này để tạo ra một `web shell` nhằm `thực thi mã từ xa`.

#### Flawed file type validation

Khi gửi các `biểu mẫu HTML`, trình duyệt thường gửi dữ liệu được cung cấp trong một `POST request` với `content-type header` là `application/x-www-form-urlencoded`. Điều này phù hợp để gửi các `văn bản đơn giản` như tên hoặc địa chỉ. Tuy nhiên, nó không thích hợp để gửi lượng lớn dữ liệu nhị phân, chẳng hạn như `toàn bộ tệp hình ảnh` hoặc `tài liệu PDF`. Trong trường hợp này, `multipart/form-data` được ưu tiên sử dụng.

#### Flawed file type validation - Continued

Xem xét một biểu mẫu bao gồm các trường để `tải lên một hình ảnh`, `mô tả nội dung` và `nhập tên người dùng`. Khi gửi biểu mẫu này, yêu cầu HTTP có thể trông như sau:

    POST /images HTTP/1.1  
    Host: normal-website.com  
    Content-Length: 12345  
    Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456  

    ---------------------------012345678901234567890123456  
    Content-Disposition: form-data; name="image"; filename="example.jpg"  
    Content-Type: image/jpeg  

    [...nội dung nhị phân của example.jpg...]  

    ---------------------------012345678901234567890123456  
    Content-Disposition: form-data; name="description"  

    This is an interesting description of my image.  

    ---------------------------012345678901234567890123456  
    Content-Disposition: form-data; name="username"  

    wiener  
    ---------------------------012345678901234567890123456--  

Như có thể thấy, phần thân thông điệp được `chia thành nhiều phần riêng biệt` tương ứng với từng trường trong biểu mẫu. Mỗi phần chứa một tiêu đề `Content-Disposition`, cung cấp `thông tin cơ bản` về trường dữ liệu tương ứng. Các phần này cũng có thể chứa tiêu đề `Content-Type` riêng, giúp máy chủ xác định loại dữ liệu (MIME type) được gửi từ trường đó.

#### Flawed file type validation - Continued

Một cách mà các trang web có thể sử dụng để kiểm tra tệp tải lên là xác minh xem tiêu đề `Content-Type` (đặc thù cho từng trường) có khớp với `MIME type` được mong đợi hay không. Ví dụ, nếu máy chủ chỉ chấp nhận `tệp hình ảnh`, nó có thể chỉ cho phép các loại như `image/jpeg` hoặc `image/png`.

Tuy nhiên, vấn đề phát sinh khi máy chủ `tin tưởng mù quáng` vào giá trị của tiêu đề này. Nếu không có thêm bước kiểm tra để đảm bảo nội dung tệp thực sự khớp với loại MIME được khai báo, cơ chế bảo vệ này có thể dễ dàng bị bỏ qua bằng các công cụ như `Burp Repeater`.

#### Lab: Web shell upload via Content-Type restriction bypass

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image82.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image83.png?raw=true)

Đăng nhập vào account `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image84.png?raw=true)

Thử upload 1 file ảnh hợp lệ: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image85.png?raw=true)

Request tìm nạp image này sẽ trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image87.png?raw=true)

Tạo file `my_shell.php` có nội dung như sau: 

    <?php echo file_get_contents('/home/carlos/secret'); ?>

Thử upload file này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image86.png?raw=true)

Thông báo lỗi cho thấy chỉ hỗ trợ MIME type là `image/jpeg` hoặc `image/png`.

Request submit shell sẽ trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image88.png?raw=true)

Thử thay đổi Content-Type header từ `application/octet-stream` thành `image/jpeg`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image89.png?raw=true)

Send request này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image90.png?raw=true)

-> Thông báo upload file thành công. Quay lại request tìm nạp image, thay đổi tên image thành `my_shell.php`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image91.png?raw=true)

-> Tìm được nội dung file `/home/carlos/secret` là `rRvsUSvG9Qh6yptMDvrD8SqeM0JaGmDz`. Submit và solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image92.png?raw=true)

### OS command injection

#### What is OS command injection?

`Lệnh tiêm OS` (OS command injection) còn được gọi là `tiêm shell` (shell injection). Đây là một lỗ hổng bảo mật cho phép kẻ tấn công `thực thi các lệnh hệ điều hành` (OS) trên máy chủ đang chạy ứng dụng, từ đó có thể `chiếm quyền kiểm soát` hoàn toàn ứng dụng và dữ liệu của nó. Thông thường, kẻ tấn công có thể lợi dụng lỗ hổng này để `xâm nhập` vào các phần khác của hạ tầng lưu trữ, `khai thác` mối quan hệ tin cậy để `mở rộng cuộc tấn công` sang các hệ thống khác trong tổ chức.

#### Useful commands

Sau khi phát hiện lỗ hổng `OS command injection`, có thể thực thi một số `lệnh ban đầu` để thu thập thông tin về hệ thống. Dưới đây là một số lệnh hữu ích trên nền tảng `Linux` và `Windows`:

| Mục đích                | Linux       | Windows       |
|-------------------------|-------------|---------------|
| Tên người dùng hiện tại | whoami      | whoami        |
| Hệ điều hành            | uname -a    | ver           |
| Cấu hình mạng           | ifconfig    | ipconfig /all |
| Kết nối mạng            | netstat -an | netstat -an   |
| Tiến trình đang chạy    | ps -ef      | tasklist      |

#### Injecting OS commands

Trong ví dụ này, một ứng dụng mua sắm cho phép người dùng kiểm tra xem một `mặt hàng có còn trong kho` của một cửa hàng cụ thể hay không. Thông tin này được truy cập thông qua URL:

    https://insecure-website.com/stockStatus?productID=381&storeID=29

Để cung cấp thông tin về hàng tồn kho, ứng dụng cần truy vấn `nhiều hệ thống cũ`. Vì lý do lịch sử, chức năng này được triển khai bằng cách gọi một `lệnh shell` với `ID sản phẩm` và `ID cửa hàng` làm tham số:

    stockreport.pl 381 29

Lệnh này sẽ xuất ra trạng thái tồn kho của mặt hàng được chỉ định và trả kết quả về cho người dùng.

Ứng dụng không có cơ chế bảo vệ chống lại `OS command injection`, vì vậy kẻ tấn công có thể gửi đầu vào sau để thực thi một lệnh tùy ý:

    & echo aiwefwlguh &

Nếu chuỗi này được chèn vào tham số `productID`, lệnh thực thi trong ứng dụng sẽ là:

    stockreport.pl & echo aiwefwlguh & 29

Lệnh `echo` sẽ in chuỗi "aiwefwlguh" ra màn hình. Đây là một cách hữu ích để kiểm tra xem ứng dụng có bị lỗ hổng `OS command injection` hay không.

Ký tự `&` là một dấu phân tách lệnh trong shell, do đó nó sẽ khiến ba lệnh riêng biệt thực thi lần lượt:

1. `stockreport.pl` mà không có đối số hợp lệ → Gây lỗi.

2. `echo aiwefwlguh` → In chuỗi kiểm tra ra đầu ra.

3. `29` được hiểu như một lệnh riêng lẻ → Gây lỗi vì `29` không phải là một lệnh hợp lệ.

Hoặc có thể sử dụng ký tự `|`, một toán tử trong shell cho phép nối nhiều lệnh. Ví dụ:

    stockreport.pl 1 | whoami

Trong đó: 

- `stockreport.pl 1` có thể là lệnh hợp lệ của ứng dụng.
- `| whoami` chuyển đầu ra của lệnh trước thành đầu vào của `whoami`, nhưng vì `whoami` không yêu cầu đầu vào, nó sẽ tự chạy và trả về tên người dùng của server.

**Kết quả trả về cho người dùng**:

    Error - productID was not provided
    aiwefwlguh
    29: command not found

**Phân tích**:

- *Dòng 1*: Ứng dụng thực thi `stockreport.pl` nhưng thiếu tham số hợp lệ, dẫn đến lỗi.
- *Dòng 2*: Lệnh `echo` đã được thực thi và hiển thị chuỗi "aiwefwlguh".
- *Dòng 3*: Shell cố gắng thực thi `29` như một lệnh, gây ra lỗi "command not found".

**Lưu ý**:

Việc đặt ký tự `&` sau lệnh tiêm giúp tách biệt lệnh tiêm với phần còn lại của lệnh gốc, giúp giảm nguy cơ lỗi làm lệnh không thực thi được.

#### Lab: OS command injection, simple case

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image93.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image94.png?raw=true)

Theo mô tả bài lab, lỗ hổng `OS command injection` tồn tại trong chức năng kiểm tra hàng tồn kho. Click vào 1 sản phẩm bất kỳ:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image95.png?raw=true)

POST request trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image96.png?raw=true)

Thử thêm `|whoami` vào sau tham số `storeId` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image97.png?raw=true)

-> Tìm được user hiện tại là `peter-4yOFO7`. Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-vulnerabilities/images/image98.png?raw=true)

### SQL injection

#### What is SQL injection (SQLi)?

`SQL Injection` (SQLi) là một lỗ hổng bảo mật web cho phép kẻ tấn công `can thiệp vào các truy vấn` mà ứng dụng gửi đến cơ sở dữ liệu. Điều này có thể dẫn đến:

- `Xem dữ liệu trái phép`: Kẻ tấn công có thể truy cập dữ liệu mà bình thường họ không thể xem được, bao gồm thông tin người dùng khác hoặc dữ liệu nhạy cảm của ứng dụng.
- `Chỉnh sửa hoặc xóa dữ liệu`: Tấn công SQLi có thể thay đổi hoặc xóa dữ liệu trong cơ sở dữ liệu, làm thay đổi nội dung hoặc hành vi của ứng dụng.
- `Tấn công leo thang` (Privilege Escalation): Kẻ tấn công có thể khai thác SQLi để chiếm quyền kiểm soát máy chủ hoặc hệ thống backend.
- `Tấn công từ chối dịch vụ` (DoS): Có thể thực hiện các truy vấn nặng khiến hệ thống bị quá tải và ngừng hoạt động.

Hậu quả của `SQL Injection` có thể rất nghiêm trọng, dẫn đến rò rỉ dữ liệu, chiếm quyền hệ thống, và phá hủy toàn bộ ứng dụng.

#### How to detect SQL injection vulnerabilities

Có thể phát hiện `SQL Injection` theo cách thủ công bằng cách sử dụng một tập hợp các `thử nghiệm có hệ thống` trên từng điểm nhập dữ liệu của ứng dụng. Để làm điều này, thường sẽ gửi:

- Ký tự dấu nháy đơn `'` và quan sát lỗi hoặc các bất thường khác.
- Một số `cú pháp SQL cụ thể` để so sánh giá trị gốc (ban đầu) của điểm nhập với một giá trị khác và tìm kiếm sự khác biệt có hệ thống trong phản hồi của ứng dụng.
- Các điều kiện Boolean như `OR 1=1` và `OR 1=2`, rồi quan sát sự khác biệt trong phản hồi của ứng dụng.
- Các `payload` được thiết kế để `tạo độ trễ` khi thực thi trong truy vấn SQL và kiểm tra sự khác biệt trong thời gian phản hồi.
- Các `payload OAST` được thiết kế để kích hoạt một `tương tác mạng ngoài băng tần` khi thực thi trong truy vấn SQL và theo dõi các tương tác kết quả.

#### Retrieving hidden data

Tưởng tượng một ứng dụng mua sắm hiển thị các sản phẩm trong `các danh mục khác nhau`. Khi người dùng nhấp vào danh mục `Gifts`, trình duyệt của họ sẽ gửi yêu cầu đến URL sau:

    https://insecure-website.com/products?category=Gifts

Điều này khiến ứng dụng thực hiện một `truy vấn SQL` để lấy thông tin chi tiết về các sản phẩm liên quan từ cơ sở dữ liệu:

    SELECT * FROM products WHERE category = 'Gifts' AND released = 1

Truy vấn `SQL` này yêu cầu cơ sở dữ liệu trả về:

- Tất cả thông tin `(*)`
- Từ bảng `products`
- Với điều kiện `category` là "Gifts"
- Và `released = 1`

Điều kiện `released = 1` được sử dụng để ẩn các sản phẩm chưa được phát hành. Có thể giả định rằng đối với các sản phẩm chưa phát hành, giá trị của `released là 0`.

#### Retrieving hidden data - Continued

Ứng dụng không triển khai bất kỳ biện pháp bảo vệ nào chống lại các cuộc tấn công `SQL Injection`. Điều này có nghĩa là kẻ tấn công có thể thực hiện cuộc tấn công như sau:

    https://insecure-website.com/products?category=Gifts'--

Điều này dẫn đến truy vấn SQL sau:

    SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

*Lưu ý quan trọng*: `--` là ký hiệu `chú thích` trong `SQL`, có nghĩa là phần còn lại của truy vấn sẽ bị bỏ qua. Trong ví dụ này, điều kiện `AND released = 1` bị loại bỏ, dẫn đến việc hiển thị tất cả sản phẩm, bao gồm cả những sản phẩm chưa được phát hành.

Ngoài ra, kẻ tấn công có thể sử dụng một kỹ thuật tương tự để hiển thị tất cả sản phẩm từ bất kỳ danh mục nào, bao gồm cả những danh mục mà họ không biết:

    https://insecure-website.com/products?category=Gifts'+OR+1=1--

Điều này tạo ra `truy vấn SQL` sau:

    SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1

Truy vấn đã chỉnh sửa sẽ trả về tất cả sản phẩm vì điều kiện `1=1` luôn đúng, khiến tất cả dữ liệu trong bảng được hiển thị.

**Cảnh báo**

Hãy cẩn thận khi chèn điều kiện `OR 1=1` vào `truy vấn SQL`. Ngay cả khi nó có vẻ vô hại trong ngữ cảnh hiện tại, nhiều ứng dụng có thể sử dụng dữ liệu từ một yêu cầu duy nhất trong nhiều truy vấn khác nhau. Nếu điều kiện này ảnh hưởng đến một câu lệnh `UPDATE` hoặc `DELETE`, nó có thể dẫn đến `mất dữ liệu ngoài ý muốn`.

#### Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

![img](99)

Access the lab: 

![img](100)

Chọn category `Pets`:

![img](101)

GET request sẽ trông như sau: 

![img](102)

Thêm `'--` vào sau tham số `category` và send request:

![img](104)

Solved the lab!

![img](105)
























































































