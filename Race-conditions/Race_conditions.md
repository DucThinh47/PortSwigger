# Race conditions

## Content

- [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions):

    - [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions-1)
    - [Limit overrun race conditions - Continued]()
    - [Limit overrun race conditions - Continued]()

### Limit overrun race conditions

#### Limit overrun race conditions

Loại điều kiện tranh chấp (race condition) được biết đến nhiều nhất cho phép `vượt qua một giới hạn nào đó` do logic nghiệp vụ của ứng dụng đặt ra.

Ví dụ, hãy xem xét một cửa hàng trực tuyến cho phép `nhập mã khuyến mãi` khi thanh toán để nhận một lần giảm giá cho đơn hàng của mình. Để áp dụng mã giảm giá này, ứng dụng có thể thực hiện các bước chính sau:

- `Kiểm tra` xem khách hàng đã sử dụng mã này trước đó chưa.
- `Áp dụng` giảm giá vào tổng số tiền đơn hàng.
- `Cập nhật` bản ghi trong cơ sở dữ liệu để phản ánh rằng khách hàng đã sử dụng mã này.

#### Limit overrun race conditions - Continued

Nếu sau đó khách hàng `cố gắng sử dụng lại` mã này, các bước kiểm tra ban đầu được thực hiện ở đầu quy trình sẽ ngăn khách hàng làm điều này.

![img](0)

Bây giờ hãy xem điều gì sẽ xảy ra nếu một người dùng `chưa từng áp dụng mã giảm giá` này trước đó cố gắng áp dụng nó `hai lần gần như cùng một lúc`:

![img](1)

Ứng dụng sẽ chuyển qua một trạng thái `phụ tạm thời`; tức là một trạng thái mà ứng dụng đi vào rồi thoát ra `trước khi` quá trình xử lý yêu cầu hoàn tất. Trong trường hợp này, trạng thái phụ bắt đầu khi máy chủ bắt đầu xử lý yêu cầu đầu tiên, và kết thúc khi nó cập nhật cơ sở dữ liệu để ghi nhận rằng khách hàng đã sử dụng mã giảm giá. Điều này tạo ra một `khoảng thời gian nhỏ` (race window) trong đó khách hàng có thể lặp lại việc nhận giảm giá nhiều lần tùy ý.

#### Limit overrun race conditions - Continued

Có nhiều biến thể khác nhau của kiểu tấn công này, bao gồm:

- Đổi một thẻ quà tặng nhiều lần
- Đánh giá một sản phẩm nhiều lần
- Rút hoặc chuyển tiền vượt quá số dư tài khoản
- Tái sử dụng một mã CAPTCHA duy nhất
- Vượt qua giới hạn tốc độ chống brute-force


