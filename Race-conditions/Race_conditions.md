# Race conditions

## Content

- [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions)

    - [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions-1)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued-1)

- [Detecting and exploiting limit overrun race conditions with Burp Repeater]()

    - [Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued]()
    - [Lab: Limit overrun race conditions]()

### Limit overrun race conditions

#### Limit overrun race conditions

Loại điều kiện tranh chấp (race condition) được biết đến nhiều nhất cho phép `vượt qua một giới hạn nào đó` do logic nghiệp vụ của ứng dụng đặt ra.

Ví dụ, hãy xem xét một cửa hàng trực tuyến cho phép `nhập mã khuyến mãi` khi thanh toán để nhận một lần giảm giá cho đơn hàng của mình. Để áp dụng mã giảm giá này, ứng dụng có thể thực hiện các bước chính sau:

- `Kiểm tra` xem khách hàng đã sử dụng mã này trước đó chưa.
- `Áp dụng` giảm giá vào tổng số tiền đơn hàng.
- `Cập nhật` bản ghi trong cơ sở dữ liệu để phản ánh rằng khách hàng đã sử dụng mã này.

#### Limit overrun race conditions - Continued

Nếu sau đó khách hàng `cố gắng sử dụng lại` mã này, các bước kiểm tra ban đầu được thực hiện ở đầu quy trình sẽ ngăn khách hàng làm điều này.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image.png?raw=true)

Bây giờ hãy xem điều gì sẽ xảy ra nếu một người dùng `chưa từng áp dụng mã giảm giá` này trước đó cố gắng áp dụng nó `hai lần gần như cùng một lúc`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image1.png?raw=true)

Ứng dụng sẽ chuyển qua một trạng thái `phụ tạm thời`; tức là một trạng thái mà ứng dụng đi vào rồi thoát ra `trước khi` quá trình xử lý yêu cầu hoàn tất. Trong trường hợp này, trạng thái phụ bắt đầu khi máy chủ bắt đầu xử lý yêu cầu đầu tiên, và kết thúc khi nó cập nhật cơ sở dữ liệu để ghi nhận rằng khách hàng đã sử dụng mã giảm giá. Điều này tạo ra một `khoảng thời gian nhỏ` (race window) trong đó khách hàng có thể lặp lại việc nhận giảm giá nhiều lần tùy ý.

#### Limit overrun race conditions - Continued

Có nhiều biến thể khác nhau của kiểu tấn công này, bao gồm:

- Đổi một thẻ quà tặng nhiều lần
- Đánh giá một sản phẩm nhiều lần
- Rút hoặc chuyển tiền vượt quá số dư tài khoản
- Tái sử dụng một mã CAPTCHA duy nhất
- Vượt qua giới hạn tốc độ chống brute-force

### Detecting and exploiting limit overrun race conditions with Burp Repeater

Quy trình phát hiện và khai thác các điều kiện `tranh chấp vượt giới hạn` (limit overrun race conditions) tương đối đơn giản. Ở cấp độ tổng quát, chỉ cần:

- Xác định một `endpoint` chỉ được `sử dụng một lần` hoặc bị giới hạn `tần suất truy cập`, và có ảnh hưởng đến bảo mật hoặc mang lại lợi ích nào đó.
- Gửi nhiều yêu cầu tới `endpoint` đó một cách `liên tiếp` và `thật nhanh` để xem liệu có thể vượt qua giới hạn hay không.

Thách thức chính nằm ở việc `căn thời gian` sao cho ít nhất hai "cửa sổ tranh chấp" (race window) trùng nhau, gây ra xung đột. Khoảng thời gian này thường chỉ kéo dài `vài mili-giây`, thậm chí còn ngắn hơn.

Ngay cả khi gửi tất cả các yêu cầu cùng một lúc, thì trong thực tế vẫn tồn tại nhiều yếu tố bên ngoài không thể kiểm soát hoặc dự đoán được, ảnh hưởng đến thời điểm và thứ tự máy chủ xử lý từng yêu cầu.

![img](2)

#### Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued

`Burp Suite 2023.9` bổ sung các tính năng mạnh mẽ mới cho `Burp Repeater`, cho phép dễ dàng `gửi một nhóm các yêu cầu song song` theo cách giúp giảm đáng kể ảnh hưởng của một yếu tố thường gặp – đó là độ trễ mạng không ổn định `(network jitter)`. Burp sẽ tự động điều chỉnh kỹ thuật gửi phù hợp với `phiên bản HTTP` mà máy chủ hỗ trợ:

- Với `HTTP/1`, Burp sử dụng kỹ thuật đồng bộ byte cuối cổ điển `(last-byte synchronization)`.
- Với `HTTP/2`, Burp áp dụng kỹ thuật `single-packet attack`, lần đầu được nhóm PortSwigger Research trình bày tại hội nghị Black Hat USA 2023.

Kỹ thuật `single-packet attack` cho phép `loại bỏ hoàn toàn sự can thiệp từ jitter mạng` bằng cách sử dụng một gói `TCP` duy nhất để hoàn tất `20–30` yêu cầu cùng lúc.

![img](3)

Mặc dù, thông thường chỉ cần `hai yêu cầu` là có thể kích hoạt một lỗ hổng, nhưng việc gửi số lượng lớn yêu cầu như trên giúp giảm thiểu `độ trễ nội bộ` – còn gọi là `jitter phía máy chủ` (server-side jitter). Điều này đặc biệt hữu ích trong giai đoạn khám phá ban đầu. 

#### Lab: Limit overrun race conditions

![img](4)

Access the lab: 

![img](5)

Đăng nhập vào account được cung cấp `wiener:peter`: 

![img](6)

Hiện tại có $50.00 trong tài khoản. Sản phẩm cần mua là:

![img](7)

Áp dụng mã giảm giá `PROMO20`:

![img](8)

Thử áp dụng mã giảm giá một lần nữa:

![img](9)

-> Website thông báo mã đã được sử dụng. 

Request khi áp dụng mã giảm giá lần 2:

![img](10)

Request nếu áp dụng mã giảm giá thành công:

![img](11)

Send request này tới repeater, ý tưởng để khai thác lỗ hổng `race conditions` là gửi nhiều request liên tiếp trong một khoảng thời gian ngắn, chọn `Create tab group`:

![img](12)

![img](13)

Click chuột phải vào request và chọn Send to repeater liên tục:

![img](14)

Để gửi 32 request này cùng một lúc, chọn `Send group in parallel`:

![img](15)

Tiếp tục gửi lại cho đến khi nào đạt mức giá mong muốn:

![img](16)

![img](17)

Mua sản phẩm và solved the lab!

![img](18)








