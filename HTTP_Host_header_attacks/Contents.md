# Contents
- [What is the HTTP Host header?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#what-is-the-http-host-header)
- [What is the purpose of the HTTP Host header?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#what-is-the-purpose-of-the-http-host-header)
- [What is an HTTP Host header attack?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#what-is-an-http-host-header-attack)
- [How do HTTP Host header vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#how-do-http-host-header-vulnerabilities-arise)
- [How to prevent HTTP Host header attacks](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#how-to-prevent-http-host-header-attacks)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#labs)
    - [Lab: Basic password reset poisoning](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#lab-basic-password-reset-poisoning)
    - [Lab: Host header authentication bypass](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/Contents.md#lab-host-header-authentication-bypass)

# What is the HTTP Host header?
`Header Host` là một thành phần bắt buộc trong các yêu cầu HTTP kể từ phiên bản `HTTP/1.1`. Nó dùng để xác định `tên miền` mà `client (trình duyệt)` muốn truy cập.

Ví dụ: khi người dùng truy cập vào `https://portswigger.net/web-security`, trình duyệt sẽ gửi yêu cầu với `header` như sau:

    GET /web-security HTTP/1.1  
    Host: portswigger.net

Trong một số trường hợp, `giá trị Host có thể bị thay đổi`, ví dụ như khi yêu cầu đi qua một `hệ thống trung gian` (proxy, load balancer...). Phần này sẽ được giải thích kỹ hơn bên dưới.

# What is the purpose of the HTTP Host header?
Header `Host` giúp xác định `thành phần back-end` mà client muốn giao tiếp. Nếu không có `Host` header hoặc nếu nó bị sai định dạng, hệ thống có thể không biết nên gửi yêu cầu đến ứng dụng nào.

**Vì sao cần thiết?**

- Trước đây, `mỗi IP chỉ phục vụ một tên miền`, nên không có vấn đề định tuyến.
- Ngày nay, do sự phát triển của `dịch vụ đám mây` và tình trạng `thiếu hụt địa chỉ IPv4`, nhiều website `dùng chung một IP` là điều phổ biến.

**Hai kịch bản phổ biến khi nhiều ứng dụng dùng chung một IP:**

**1. Virtual Hosting (Lưu trữ ảo)**
- Một máy chủ chạy `nhiều website khác nhau` (cùng hoặc khác chủ sở hữu).
- Tất cả website này dùng `chung IP`, chỉ khác tên miền.
- Các website kiểu này gọi là `virtual host`.
- Đối với người dùng, chúng không khác gì website chạy trên server riêng.

**2. Trung gian định tuyến (proxy/load balancer/CDN)**
- Website chạy trên nhiều `back-end server riêng biệt`, nhưng `client truy cập thông qua một hệ thống trung gian` (như CDN hoặc proxy).
- Tất cả domain đều `trỏ về cùng một IP` của `proxy/load balancer`.
- Hệ thống trung gian cần biết gửi yêu cầu đến back-end nào => phụ thuộc vào `Host header`.

**Host Header giải quyết vấn đề như thế nào?**

Host header đóng vai trò như `số phòng trong địa chỉ chung`. Ví dụ:
- Nhiều căn hộ cùng ở một địa chỉ, thư cần ghi rõ số phòng hoặc tên người nhận.
- Tương tự, Host header giúp server `xác định đúng ứng dụng back-end` cần xử lý yêu cầu.

Khi trình duyệt gửi yêu cầu:
- URL sẽ được phân giải thành một IP.
- Server nhận yêu cầu sẽ `đọc Host header` để biết nên chuyển tiếp đến hệ thống nào.
# What is an HTTP Host header attack?
`Tấn công Host header` khai thác các website xử lý giá trị của `Host header không an toàn`.

Nếu máy chủ `tin tưởng và sử dụng trực tiếp` giá trị này mà `không kiểm tra` hoặc `mã hóa đúng cách`, kẻ tấn công có thể `chèn mã độc` để thay đổi hành vi của máy chủ.

Những cuộc tấn công kiểu này gọi là `Host Header Injection`.

**Vì sao có lỗ hổng này?**

Các ứng dụng web thường `không biết chính xác domain mà chúng đang chạy`, trừ khi được cấu hình thủ công.

Khi cần lấy tên miền để tạo `URL tuyệt đối` (ví dụ để gửi trong email), ứng dụng có thể `lấy từ Host header` như sau:

    <a href="https://_SERVER['HOST']/support">Contact support</a>

Hoặc `Host header` còn được dùng trong `giao tiếp giữa các thành phần nội bộ` của hệ thống.

**Nguy cơ bảo mật**

Vì `Host header là do người dùng gửi`, nếu không được xử lý đúng, nó có thể bị lợi dụng để tấn công nhiều kiểu khác nhau:
- `Web Cache Poisoning` (đầu độc bộ nhớ đệm)
- `Lỗi logic nghiệp vụ` (business logic flaws)
- `SSRF dựa trên định tuyến` (tấn công yêu cầu từ máy chủ)
- Các lỗ hổng truyền thống phía server như `SQL injection`

# How do HTTP Host header vulnerabilities arise?
Lỗ hổng Host header thường bắt nguồn từ `giả định sai lầm rằng header này không thể bị người dùng kiểm soát`.

Điều này khiến ứng dụng `tin tưởng Host header một cách mặc định` và không kiểm tra, mã hóa đúng cách, trong khi kẻ tấn công có thể dễ dàng thay đổi nó bằng các công cụ như Burp Suite.

**Vượt qua bảo vệ mặc định**

Ngay cả khi Host header được xử lý an toàn, trong một số cấu hình server, `giá trị này vẫn có thể bị ghi đè` bằng cách chèn các header khác (ví dụ: `X-Forwarded-Host`).

Nhiều quản trị viên `không biết rằng các header này được hỗ trợ mặc định`, dẫn đến việc `bỏ qua kiểm tra bảo mật` cho chúng.

**Lỗi cấu hình hạ tầng là nguyên nhân chính**

Phần lớn các lỗ hổng không phải do code sai, mà đến từ `lỗi cấu hình hệ thống` hoặc `thành phần tích hợp`.
Điều này thường xảy ra khi website sử dụng các `công nghệ bên thứ ba`, nhưng không hiểu rõ các tùy chọn cấu hình và rủi ro bảo mật liên quan.
# How to prevent HTTP Host header attacks
**1. Hạn chế sử dụng Host header**
- Tránh dùng Host header trong mã phía server nếu không cần thiết.
- Dùng `URL tương đối` thay vì `URL tuyệt đối` nếu có thể => giảm nguy cơ `web cache poisoning`.

**2. Bảo vệ URL tuyệt đối**
- Nếu bắt buộc dùng `URL tuyệt đối`, `cấu hình domain thủ công trong file cấu hình`, không lấy từ Host header.
=> Giúp ngăn chặn các tấn công như `password reset poisoning`.

**3. Xác thực Host header**
- Nếu phải dùng Host header, cần kiểm tra giá trị theo `danh sách cho phép` (whitelist).
- Ví dụ, `Django` dùng `ALLOWED_HOSTS` trong file cấu hình để giới hạn domain hợp lệ.

**4. Tắt các header ghi đè Host**
- Không nên hỗ trợ các header như `X-Forwarded-Host` (trừ khi thực sự cần).
- Nhiều framework `hỗ trợ sẵn mặc định`, cần kiểm tra và vô hiệu hóa nếu không dùng.

**5. Cấu hình danh sách domain hợp lệ**
- Trên `load balancer` hoặc `reverse proxy`, chỉ cho phép chuyển tiếp đến `domain hợp lệ` trong `whitelist`.

**6. Cẩn thận với virtual host nội bộ**
- Không nên chạy `ứng dụng nội bộ` trên cùng server với `nội dung công khai`. => Kẻ tấn công có thể truy cập hệ thống nội bộ qua thao tác với Host header.
# Labs

## Lab: Basic password reset poisoning
**1. Yêu cầu**

Phòng lab này tồn tại lỗ hổng password reset poisoning. Người dùng carlos sẽ bất cẩn nhấp vào bất kỳ liên kết nào trong email mà anh ta nhận được.

Để hoàn thành lab, bạn cần đăng nhập vào tài khoản của Carlos.

Bạn có thể đăng nhập vào tài khoản của mình bằng thông tin sau:
`wiener : peter`
Mọi email gửi đến tài khoản này có thể được đọc thông qua trình quản lý email trên exploit server.

**2. Thực hiện**

Website này có tính năng `Forgot password` để khôi phục mật khẩu. Thử khôi phục mật khẩu tài khoản `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image0.png?raw=true)

Một đường link khôi phục password sẽ được gửi tới email của `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image1.png?raw=true)

Có thể thấy đường link ứng với giá trị của tiêu đề `Host` trong request ứng với đường link gửi về email của `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image2.png?raw=true)

=> Nghĩa là có thể sửa đổi giá trị Host header, trỏ đến đường link mà tôi muốn:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image3.png?raw=true)

Thay đổi giá trị Host header thành đường link dẫn đến Exploit server đã được lab cung cấp, username thành `carlos` và send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image4.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image5.png?raw=true)

Kiểm tra Access log phía Exploit server:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image6.png?raw=true)

=> Thu được forgot password token của carlos. Thay giá trị token này vào request đặt mật khẩu mới:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image7.png?raw=true)

Thử log in vào tài khoản carlos:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image8.png?raw=true)

## Lab: Host header authentication bypass
**1. Yêu cầu**

Phòng thí nghiệm này đưa ra một giả định về mức đặc quyền của người dùng dựa trên Host Header.

Để giải quyết phòng thí nghiệm, truy cập bảng quản trị và xóa người dùng `Carlos`.

**2. Thực hiện**

Website không có gì cả, ngay cả chức năng `Forgot password`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image9.png?raw=true)

Làm sao để truy cập admin panel? Tôi thử truy cập `/robots.txt`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image10.png?raw=true)

=> Tìm thấy đường dẫn `/admin` đang bị cấm truy cập. Thử truy cập `/admin`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image11.png?raw=true)

=> Thông báo lỗi `Admin interface only available to local users`. Như vậy, chỉ người dùng cục bộ mới có thể truy cập Admin Panel. Sửa request thành như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image12.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image13.png?raw=true)

Khi chọn xóa carlos, vẫn hiển thị lỗi:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image14.png?raw=true)

Kiểm tra request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image15.png?raw=true)

=> Thay đổi giá trị Host header thành `localhost` và send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image16.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_Host_header_attacks/images/image17.png?raw=true)











