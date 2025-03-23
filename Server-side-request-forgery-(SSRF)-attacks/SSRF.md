# Server-side request forgery (SSRF) attacks 

## Content

- [What is SSRF?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-ssrf)

- [What is the impact of SSRF attacks?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-the-impact-of-ssrf-attacks)

- [Common SSRF attacks](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#common-ssrf-attacks)

- [Circumventing common SSRF defenses]()

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

![img](25)

Access the lab: 

![img](26)

Theo mô tả bài lab, tiếp tục tìm request check stock:

![img](27)

Thử thay tham số `stockApi` thành URL dẫn đến trang admin `http://localhost/admin`:

![img](29)

Send request, quan sát response: 

![img](29)

-> Website trả về thông báo *"External stock check blocked for security reasons"*. Có vẻ request chứa `localhost` đã bị đưa vào danh sách đen và bị lọc. Thử thay thành `http://127.0.0.1/admin` và send request:

![img](30)

Vẫn không được, thử thay thành `http://2130706433/admin` và send request: 

![img](31)

Vẫn không được, thử thay thành `http://017700000001/admin` và send request: 

![img](32)

Vẫn không được, thử thay thành `http://127.1/admin` và send request: 

![img](33)

Vẫn không được, thử thay thành `http://127.1/AdMin` và send request: 

![img](34)

-> Thành công truy nhập trang admin, như vậy có thể sử dụng chuỗi IP thay thế và làm mờ chuỗi bị chặn `admin` thì có thể bypass bộ lọc sử dụng `blacklist`. 

Tìm ra URL tương ứng với việc xóa user `carlos`: `http://127.1/AdMin/delete?username=carlos`

![img](35)

Thay thế giá trị tham số stockApi thành URL này trong Burp Proxy và Forward request: 

![img](36)

Solved the lab!

![img](37)

























