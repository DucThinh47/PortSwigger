# Server-side request forgery (SSRF) attacks 

## Content

- [What is SSRF?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-ssrf)

- [What is the impact of SSRF attacks?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side-request-forgery-(SSRF)-attacks/SSRF.md#what-is-the-impact-of-ssrf-attacks)

- [Common SSRF attacks]()

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












