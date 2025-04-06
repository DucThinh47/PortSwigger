# Race conditions

## Content

- [Limit overrun race conditions]():

    - [Limit overrun race conditions]()
    - []()
    - []()

### Limit overrun race conditions

#### Limit overrun race conditions

Loại điều kiện tranh chấp (race condition) được biết đến nhiều nhất cho phép `vượt qua một giới hạn nào đó` do logic nghiệp vụ của ứng dụng đặt ra.

Ví dụ, hãy xem xét một cửa hàng trực tuyến cho phép `nhập mã khuyến mãi` khi thanh toán để nhận một lần giảm giá cho đơn hàng của mình. Để áp dụng mã giảm giá này, ứng dụng có thể thực hiện các bước chính sau:

- `Kiểm tra` xem khách hàng đã sử dụng mã này trước đó chưa.
- `Áp dụng` giảm giá vào tổng số tiền đơn hàng.
- `Cập nhật` bản ghi trong cơ sở dữ liệu để phản ánh rằng khách hàng đã sử dụng mã này.




