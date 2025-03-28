# Server-side request forgery (SSRF) attacks 

## Content

- [What is SSRF?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-ssrf)

- [What is the impact of SSRF attacks?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-the-impact-of-ssrf-attacks)

- [Common SSRF attacks](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#common-ssrf-attacks)

- [Circumventing common SSRF defenses](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#circumventing-common-ssrf-defenses)

- [Blind SSRF vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#blind-ssrf-vulnerabilities)

- [Finding hidden attack surface for SSRF vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#finding-hidden-attack-surface-for-ssrf-vulnerabilities)

### What is SSRF?

`SSRF (Server-Side Request Forgery)` là một lỗ hổng bảo mật web cho phép kẻ tấn công khiến ứng dụng phía máy chủ thực hiện các yêu cầu đến `một vị trí không mong muốn`.

Trong một cuộc tấn công `SSRF` điển hình, kẻ tấn công có thể buộc máy chủ kết nối đến các dịch vụ nội bộ chỉ dành cho tổ chức. Trong các trường hợp khác, kẻ tấn công có thể ép buộc máy chủ kết nối đến các hệ thống bên ngoài tùy ý. Điều này có thể dẫn đến việc rò rỉ dữ liệu nhạy cảm, chẳng hạn như thông tin xác thực.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image.png?raw=true)

### What is the impact of SSRF attacks?

Một cuộc tấn công `SSRF` thành công thường có thể dẫn đến các hành động trái phép hoặc truy cập vào dữ liệu bên trong tổ chức. Điều này có thể xảy ra trong ứng dụng dễ bị tấn công hoặc trên các hệ thống `back-end` khác mà ứng dụng có thể giao tiếp. Trong một số tình huống, lỗ hổng `SSRF` có thể cho phép kẻ tấn công `thực thi các lệnh tùy ý`.

Một khai thác `SSRF` gây ra kết nối đến các hệ thống bên ngoài của bên thứ ba có thể dẫn đến các cuộc tấn công độc hại tiếp theo. Những cuộc tấn công này có thể xuất phát từ tổ chức đang lưu trữ ứng dụng dễ bị tấn công.

### Common SSRF attacks

Các cuộc tấn công `SSRF` thường lợi dụng mối quan hệ tin cậy để `leo thang` tấn công từ ứng dụng dễ bị tấn công và thực hiện các hành động trái phép. Những mối quan hệ tin cậy này có thể tồn tại liên quan đến `máy chủ` hoặc các hệ thống `back-end` khác trong cùng một tổ chức.

#### SSRF attacks against the server

Trong một cuộc tấn công `SSRF` nhắm vào máy chủ, kẻ tấn công khiến ứng dụng thực hiện một yêu cầu HTTP `quay lại chính máy chủ đang lưu trữ ứng dụng` thông qua `giao diện mạng vòng lặp (loopback)`. Điều này thường liên quan đến việc cung cấp một `URL` với tên máy chủ như `127.0.0.1 `(một địa chỉ IP dự phòng trỏ đến bộ điều hợp vòng lặp) hoặc `localhost` (tên thông dụng cho cùng một bộ điều hợp).

**Ví dụ**

Kịch bản là một ứng dụng mua sắm cho phép người dùng xem liệu `một mặt hàng có còn trong kho` của một cửa hàng cụ thể hay không. Để cung cấp thông tin về kho hàng, ứng dụng phải truy vấn nhiều `API REST back-end` khác nhau. Nó thực hiện điều này bằng cách truyền `URL` đến `endpoint API back-end` tương ứng thông qua một `yêu cầu HTTP` từ phía `front-end`. Khi người dùng xem trạng thái kho của một mặt hàng, trình duyệt của họ gửi yêu cầu sau:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1

Yêu cầu này khiến máy chủ thực hiện một yêu cầu đến `URL` được chỉ định, truy xuất trạng thái kho hàng và trả về kết quả cho người dùng.

**Tấn công SSRF**

Trong ví dụ này, kẻ tấn công có thể `chỉnh sửa yêu cầu` để chỉ định một `URL cục bộ` trên máy chủ như sau:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://localhost/admin

Máy chủ sẽ truy xuất nội dung của `URL /admin` và trả về cho người dùng.

**Khai thác lỗ hổng**

Kẻ tấn công có thể truy cập `URL /admin`, nhưng chức năng quản trị thường chỉ có thể truy cập đối với người dùng đã xác thực. Điều này có nghĩa là kẻ tấn công sẽ không thấy bất kỳ thông tin quan trọng nào. Tuy nhiên, nếu yêu cầu đến `URL /admin` xuất phát từ `máy cục bộ`, các cơ chế kiểm soát truy cập thông thường sẽ bị bỏ qua.

Ứng dụng sẽ cấp quyền truy cập đầy đủ vào chức năng quản trị vì yêu cầu dường như xuất phát từ một vị trí đáng tin cậy.

#### SSRF attacks against the server - Continued

Tại sao các ứng dụng lại hoạt động theo cách này và mặc nhiên tin tưởng các yêu cầu đến từ máy cục bộ? Điều này có thể xảy ra vì nhiều lý do:

- Kiểm tra kiểm soát truy cập có thể được triển khai trong một thành phần khác `nằm phía trước` máy chủ ứng dụng. Khi một kết nối được thực hiện quay lại máy chủ, quá trình kiểm tra này bị bỏ qua.

- Vì mục đích `khôi phục thảm họa`, ứng dụng có thể cho phép truy cập quản trị mà `không cần` đăng nhập đối với bất kỳ người dùng nào đến từ `máy cục bộ`. Điều này cung cấp một cách để quản trị viên khôi phục hệ thống nếu họ mất thông tin đăng nhập. Điều này giả định rằng chỉ có người dùng hoàn toàn tin cậy mới đến trực tiếp từ máy chủ.

- Giao diện quản trị có thể lắng nghe trên một cổng (port) khác so với ứng dụng chính và có thể `không truy cập được trực tiếp` bởi người dùng.

Những mối quan hệ tin cậy kiểu này, trong đó các `yêu cầu xuất phát từ máy cục bộ` được `xử lý khác` với các `yêu cầu thông thường`, thường khiến `SSRF` trở thành một lỗ hổng nghiêm trọng.

#### Lab: Basic SSRF against the local server

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image1.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image2.png?raw=true)

Như mô tả bài lab, thử tìm chức năng kiểm tra hàng tồn kho. Click vào 1 sản phẩm bất kỳ, để ý có phần `Check stock`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image3.png?raw=true)

Click `Check stock`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image4.png?raw=true)

-> Website thông báo còn 112 sản phẩm.

Tìm kiếm trong Burp Proxy History request check stock: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image5.png?raw=true)

-> Trong request có tồn tại stock check URL, thử thay đổi thành `http://localhost/admin`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image6.png?raw=true)

Send request, quan sát response:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image7.png?raw=true)

-> Truy cập thành công trang của admin. Mở trên Browser:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image8.png?raw=true)

Tìm được URL dẫn đến việc xóa user carlos: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image9.png?raw=true)

Thay đổi stock check URL thành `http://localhost/admin/delete?username=carlos` và forward stock check request, sovle the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image10.png?raw=true)

#### SSRF attacks against other back-end systems

Trong một số trường hợp, máy chủ ứng dụng có khả năng `tương tác` với các hệ thống `back-end` mà người dùng không thể truy cập trực tiếp. Các hệ thống này thường có `địa chỉ IP riêng` không thể định tuyến được. Các hệ thống `back-end` thường được `bảo vệ bởi cấu trúc mạng`, vì vậy chúng thường có `tư thế bảo mật yếu hơn`. Trong nhiều trường hợp, các `hệ thống back-end nội bộ` chứa các chức năng nhạy cảm có thể được truy cập mà `không cần xác thực` bởi bất kỳ ai có khả năng tương tác với các hệ thống đó.

Trong ví dụ trước, tưởng tượng có một giao diện quản trị tại URL back-end `https://192.168.0.68/admin`. Kẻ tấn công có thể gửi yêu cầu sau để khai thác lỗ hổng `SSRF` và truy cập vào giao diện quản trị:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://192.168.0.68/admin

#### Lab: Basic SSRF against another back-end system

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image11.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image12.png?raw=true)

Như mô tả của bài lab, click vào 1 sản phẩm bất kì và kiểm tra hàng tồn kho:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image13.png?raw=true)

Click check stock: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image14.png?raw=true)

-> Website trả về 666 sản phẩm. Trong `Burp Proxy History`, tìm request yêu cầu `check stock`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image15.png?raw=true)

Thử thay `stockApi` thành `http://192.168.0.1:8080/admin` để truy cập giao diện admin:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image16.png?raw=true)

Send request này tới `Burp Intruder` để để brute-force octet cuối cùng: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image17.png?raw=true)

Payload option: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image18.png?raw=true)

Start attack:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image19.png?raw=true)

Tìm request nào trả về status code là `404` vì trang admin là 1 hệ thống `back-end` mà người dùng không thể truy cập trực tiếp. Các hệ thống này thường có `địa chỉ IP riêng` không thể định tuyến được:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image20.png?raw=true)

-> Tìm ra địa chỉ IP `192.168.0.149`, thử thay vào Burp Repeater và Send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image21.png?raw=true)

-> Thành công truy cập trang admin. Tìm ra URL để xóa user `carlos`: `http://192.168.0.149:8080/admin/delete?username=carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image22.png?raw=true)

Thay vào request trong Burp Proxy:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image23.png?raw=true)

Forward request này, solve the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image24.png?raw=true)

### Circumventing common SSRF defenses

Thông thường các ứng dụng có hành vi `SSRF` đi kèm với các biện pháp phòng thủ nhằm ngăn chặn việc khai thác độc hại. Tuy nhiên, các biện pháp phòng thủ này thường có thể bị vượt qua.

#### SSRF with blacklist-based input filters

Một số ứng dụng `chặn các đầu vào` chứa tên máy chủ như `127.0.0.1` và `localhost`, hoặc các URL nhạy cảm như `/admin`. Trong tình huống này, thường có thể vượt qua bộ lọc bằng cách sử dụng các kỹ thuật sau:

- Sử dụng biểu diễn `địa chỉ IP thay thế` cho `127.0.0.1`, chẳng hạn như `2130706433`, `017700000001`, hoặc `127.1`.

- Đăng ký `tên miền riêng` trỏ đến `127.0.0.1`. Có thể sử dụng `spoofed.burpcollaborator.net` cho mục đích này.

- `Làm mờ` các chuỗi bị chặn bằng cách sử dụng `mã hóa URL` hoặc `thay đổi kiểu chữ` (chữ hoa/chữ thường).

- Cung cấp một `URL được kiểm soát`, sau đó `chuyển hướng đến URL mục tiêu`. Thử sử dụng các `mã chuyển hướng khác nhau`, cũng như `các giao thức khác nhau` cho URL mục tiêu. Ví dụ: chuyển đổi từ URL `http:` sang `https:` trong quá trình chuyển hướng đã được chứng minh là có thể vượt qua một số bộ lọc chống `SSRF`.

#### Lab: SSRF with blacklist-based input filter

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image25.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image26.png?raw=true)

Theo mô tả bài lab, tiếp tục tìm request check stock:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image27.png?raw=true)

Thử thay tham số `stockApi` thành URL dẫn đến trang admin `http://localhost/admin`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image28.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image29.png?raw=true)

-> Website trả về thông báo *"External stock check blocked for security reasons"*. Có vẻ request chứa `localhost` đã bị đưa vào danh sách đen và bị lọc. Thử thay thành `http://127.0.0.1/admin` và send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image30.png?raw=true)

Vẫn không được, thử thay thành `http://2130706433/admin` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image31.png?raw=true)

Vẫn không được, thử thay thành `http://017700000001/admin` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image32.png?raw=true)

Vẫn không được, thử thay thành `http://127.1/admin` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image33.png?raw=true)

Vẫn không được, thử thay thành `http://127.1/AdMin` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image34.png?raw=true)

-> Thành công truy nhập trang admin, như vậy có thể sử dụng chuỗi IP thay thế và làm mờ chuỗi bị chặn `admin` thì có thể bypass bộ lọc sử dụng `blacklist`. 

Tìm ra URL tương ứng với việc xóa user `carlos`: `http://127.1/AdMin/delete?username=carlos`

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image35.png?raw=true)

Thay thế giá trị tham số stockApi thành URL này trong Burp Proxy và Forward request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image36.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image37.png?raw=true)

#### SSRF with whitelist-based input filters

Một số ứng dụng chỉ cho phép các đầu vào khớp với `danh sách trắng` của các giá trị được phép. Bộ lọc có thể tìm kiếm `sự khớp` ở đầu chuỗi đầu vào hoặc `bên trong chuỗi` đó. Có thể vượt qua bộ lọc này bằng cách khai thác `sự không nhất quán` trong quá trình phân tích cú pháp `URL`.

`Đặc tả URL` chứa một số tính năng dễ bị bỏ qua khi các `URL` được triển khai phân tích cú pháp và xác thực theo `phương pháp tự tạo`:

Có thể `nhúng thông tin xác thực` vào `URL` trước `tên máy chủ` bằng cách sử dụng ký tự `@`. Ví dụ:

    https://expected-host:fakepassword@evil-host

Có thể sử dụng ký tự `#` để chỉ định `một đoạn URL (fragment)`. Ví dụ:

    https://evil-host#expected-host

Có thể tận dụng `hệ thống phân giải tên miền DNS` để đặt đầu vào yêu cầu vào một `tên miền DNS đầy đủ` được kiểm soát. Ví dụ:

    https://expected-host.evil-host

Có thể `mã hóa URL các ký tự` để làm `rối loạn mã phân tích cú pháp URL`. Điều này đặc biệt hữu ích nếu `mã triển khai bộ lọc` xử lý các `ký tự được mã hóa URL` `khác` với `mã thực hiện yêu cầu HTTP back-end`. Cũng có thể thử `mã hóa kép các ký tự;` một số máy chủ `giải mã URL đệ quy` cho đầu vào mà chúng nhận được, điều này có thể dẫn đến sự `không nhất quán`.

Có thể `kết hợp các kỹ thuật trên` với nhau để tăng khả năng vượt qua bộ lọc.

#### Bypassing SSRF filters via open redirection

Đôi khi có thể vượt qua các biện pháp phòng thủ dựa trên bộ lọc bằng cách khai thác `lỗ hổng chuyển hướng mở` (Open Redirection).

Trong ví dụ trước, giả sử `URL` do người dùng cung cấp `được kiểm tra nghiêm ngặt` để ngăn chặn việc khai thác độc hại từ lỗ hổng `SSRF`. Tuy nhiên, ứng dụng mà các `URL` được phép truy cập lại chứa một `lỗ hổng chuyển hướng mở`. Nếu `API` được sử dụng để thực hiện `yêu cầu HTTP` từ `back-end` hỗ trợ chuyển hướng, có thể xây dựng một `URL` thỏa mãn bộ lọc nhưng vẫn `dẫn đến một yêu cầu chuyển hướng` tới mục tiêu `back-end` mong muốn.

Ví dụ: Ứng dụng chứa một `lỗ hổng chuyển hướng mở`, trong đó `URL` sau:

    /product/nextProduct?currentProductId=6&path=http://evil-user.net

Trả về một chuyển hướng đến:

    http://evil-user.net

Có thể tận dụng `lỗ hổng chuyển hướng mở` này để `vượt qua bộ lọc URL` và khai thác lỗ hổng `SSRF` như sau:

    POST /product/stock HTTP/1.0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 118

    stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin

**Cách thức khai thác SSRF**:

1. Ứng dụng đầu tiên sẽ kiểm tra và xác nhận rằng `URL stockApi` thuộc miền được phép (`weliketoshop.net`), và điều này là `hợp lệ`.

2. Sau đó, ứng dụng thực hiện yêu cầu đến `URL` đã cung cấp, điều này sẽ kích hoạt `quá trình chuyển hướng mở`.

3. Ứng dụng theo dõi chuyển hướng và cuối cùng gửi yêu cầu đến `URL nội bộ` do kẻ tấn công chỉ định (`http://192.168.0.68/admin`).

Nhờ vào lỗ hổng chuyển hướng mở, kẻ tấn công có thể vượt qua bộ lọc `URL` của ứng dụng và khai thác `SSRF` để truy cập vào các tài nguyên nội bộ mà bình thường không thể truy cập được.

#### Lab: SSRF with filter bypass via open redirection vulnerability

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image38.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image39.png?raw=true)

Như mô tả bài lab, tìm request check stock: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image40.png?raw=true)

-> Tham số stockApi có giá trị là `/product/stock/check?productId=1&storeId=1`.

Thử thay đổi thành `/product/stock/check?productId=1&storeId=1&path=http://192.168.0.12:8080/admin` và send request này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image41.png?raw=true)

-> Không thể làm cho web server đưa ra yêu cầu trực tiếp đến một server khác. 

Thử click Next Product: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image43.png?raw=true)

Request và response khi click Next Product: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image44.png?raw=true)

-> Nhận thấy server phản hồi có `Location` header dẫn đến `endpoint` trong tham số `path` từ request. Header `Location` trong phản hồi HTTP được sử dụng để `chỉ định URL` mà `client nên chuyển hướng` (redirect) đến.

Thử thay đổi giá trị tham số `stockApi` thành `/product/nextProduct?path=http://192.168.0.12:8080/admin` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image45.png?raw=true)

-> Thành công truy cập trang admin. Tìm được URL dẫn đến việc xóa user `carlos`: `/admin/delete?username=carlos`

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image46.png?raw=true)

Thay đổi giá trị tham số stockApi thành `/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image47.png?raw=true)

-> Xóa `carlos` thành công. Solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/images/image48.png?raw=true)

### Blind SSRF vulnerabilities

Lỗ hổng `Blind SSRF` xảy ra khi có thể khiến ứng dụng `thực hiện` một `yêu cầu HTTP` đến một `URL` được cung cấp, nhưng phản hồi từ yêu cầu phía `back-end` `không được trả về` trong phản hồi phía `front-end` của ứng dụng.

#### What is the impact of blind SSRF vulnerabilities?

Lỗ hổng `Blind SSRF` thường có `tác động thấp hơn` so với `lỗ hổng SSRF thông thường` do tính chất một chiều của nó. Điều này có nghĩa là chúng `không dễ dàng bị khai thác` để thu thập dữ liệu nhạy cảm từ các hệ thống `back-end`.

Tuy nhiên, trong một số trường hợp, lỗ hổng `Blind SSRF` vẫn có thể bị lợi dụng để đạt được `thực thi mã từ xa`(Remote Code Execution - RCE) trên máy chủ hoặc các thành phần `back-end` khác.

#### How to find and exploit blind SSRF vulnerabilities

**Phát hiện lỗ hổng Blind SSRF**

Cách đáng tin cậy nhất để phát hiện lỗ hổng `Blind SSRF` là sử dụng `kỹ thuật out-of-band (OAST)`. Kỹ thuật này bao gồm:

1. Thực hiện một yêu cầu HTTP đến `một hệ thống bên ngoài` được kiểm soát.

2. Theo dõi các `tương tác mạng` với hệ thống đó để phát hiện phản hồi.

**Sử dụng Burp Collaborator để phát hiện Blind SSRF**

Cách dễ nhất và hiệu quả nhất để sử dụng kỹ thuật `out-of-band` là thông qua `Burp Collaborator`. Các bước thực hiện như sau:

1. Sử dụng `Burp Collaborator` để tạo ra `các tên miền duy nhất`.

2. Gửi các tên miền này dưới dạng `payload` vào ứng dụng đang kiểm tra.

3. Giám sát xem có bất kỳ `tương tác` nào với các tên miền đó hay không.

    - Nếu có `yêu cầu HTTP` đến từ ứng dụng, điều đó chứng tỏ ứng dụng `dễ bị tấn công SSRF`.

**Lưu ý khi kiểm tra Blind SSRF**

Khi kiểm tra lỗ hổng `SSRF`, có thể quan sát thấy một `truy vấn DNS` đối với tên miền của `Burp Collaborator`, nhưng không có yêu cầu HTTP nào tiếp theo. Điều này thường xảy ra vì:

- Ứng dụng `cố gắng thực hiện` yêu cầu HTTP tới tên miền, dẫn đến việc `tra cứu DNS`.
- Yêu cầu HTTP thực tế bị chặn bởi `bộ lọc cấp mạng`.

Nguyên nhân phổ biến:

- Hạ tầng mạng thường `cho phép lưu lượng DNS` đi ra ngoài vì cần thiết cho nhiều mục đích, nhưng lại `chặn kết nối HTTP` đến các đích không mong muốn.

#### How to find and exploit blind SSRF vulnerabilities - Continued

Việc `chỉ xác định` được một `lỗ hổng Blind SSRF` có khả năng `kích hoạt các yêu cầu HTTP` theo kỹ thuật `out-of-band` không tự nó cung cấp một lộ trình khai thác. Do không thể xem phản hồi từ yêu cầu phía `back-end`, hành vi này `không thể được sử dụng` để khám phá nội dung trên các hệ thống mà `máy chủ ứng dụng có thể truy cập`. Tuy nhiên, nó vẫn có thể được tận dụng để `thăm dò các lỗ hổng khác` trên chính máy chủ hoặc trên các hệ thống `back-end` khác. Có thể `quét một cách mù quáng` không gian `địa chỉ IP nội bộ`, gửi các `payload` được thiết kế để phát hiện các lỗ hổng đã biết. Nếu những `payload` đó cũng sử dụng các kỹ thuật `out-of-band`, có thể phát hiện một lỗ hổng nghiêm trọng trên một máy chủ nội bộ chưa được vá.

Một hướng khai thác khác cho lỗ hổng `Blind SSRF` là khiến ứng dụng `kết nối tới một hệ thống do kẻ tấn công kiểm soát` và trả về các `phản hồi độc hại cho HTTP client` thực hiện kết nối. Nếu có thể khai thác được một lỗ hổng nghiêm trọng phía client trong việc triển khai HTTP của máy chủ, thì có thể `đạt được quyền thực thi mã từ xa (RCE)` trong hạ tầng ứng dụng.

### Finding hidden attack surface for SSRF vulnerabilities

Nhiều lỗ hổng `Server-Side Request Forgery (SSRF)` rất `dễ phát hiện` vì lưu lượng truy cập thông thường của ứng dụng bao gồm các `tham số yêu cầu chứa đầy đủ URL`. Tuy nhiên, một số trường hợp `SSRF` khác lại khó tìm hơn.

#### Partial URLs in requests

Đôi khi, một ứng dụng `chỉ đặt một tên miền (hostname)` hoặc `một phần của đường dẫn URL` vào các `tham số yêu cầu`. Giá trị được gửi sau đó sẽ được máy chủ `kết hợp thành một URL đầy đủ`để thực hiện yêu cầu. Nếu giá trị này dễ dàng được nhận dạng là một `tên miền` hoặc `đường dẫn URL`, bề mặt tấn công tiềm năng có thể sẽ rõ ràng. Tuy nhiên, khả năng khai thác như một `lỗ hổng SSRF` đầy đủ có thể `bị hạn chế`vì không kiểm soát được `toàn bộ URL` được yêu cầu.

#### URLs within data formats

Một số ứng dụng truyền dữ liệu theo các định dạng `có đặc tả cho phép bao gồm URL`, những `URL` này có thể được `trình phân tích cú pháp dữ liệu yêu cầu`. Một ví dụ rõ ràng về điều này là `định dạng dữ liệu XML`, được sử dụng rộng rãi trong các ứng dụng web để truyền `dữ liệu có cấu trúc` từ client đến server.

Khi một ứng dụng chấp nhận dữ liệu ở `định dạng XML` và phân tích nó, ứng dụng có thể bị tổn thương trước `lỗ hổng chèn XXE (XML External Entity)`. Ngoài ra, nó cũng có thể dễ bị khai thác `SSRF` thông qua `XXE`.

#### SSRF via the Referer header

Một số ứng dụng sử dụng phần mềm phân tích phía máy chủ để `theo dõi client truy cập`. Phần mềm này thường ghi lại `header Referer` trong các yêu cầu nhằm theo dõi `các liên kết đến từ bên ngoài`.

Phần mềm phân tích thường sẽ truy cập vào `bất kỳ URL bên thứ ba nào` xuất hiện trong `header Referer`. Điều này thường được thực hiện để `phân tích nội dung` của các trang web giới thiệu, bao gồm cả văn bản liên kết (anchor text) được sử dụng trong các liên kết đến.

Do đó, `header Referer` thường trở thành một `bề mặt tấn công hữu ích` cho các `lỗ hổng SSRF`.












































