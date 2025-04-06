# Race conditions

## Content

- [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions)

    - [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions-1)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued-1)

- [Detecting and exploiting limit overrun race conditions with Burp Repeater](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-burp-repeater)

    - [Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-burp-repeater---continued)
    - [Lab: Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-limit-overrun-race-conditions)

- [Detecting and exploiting limit overrun race conditions with Turbo Intruder]()

    - [Detecting and exploiting limit overrun race conditions with Turbo Intruder]()
    - [Detecting and exploiting limit overrun race conditions with Turbo Intruder - Continued]()
    - [Lab: Bypassing rate limits via race conditions]()


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

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image2.png?raw=true)

#### Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued

`Burp Suite 2023.9` bổ sung các tính năng mạnh mẽ mới cho `Burp Repeater`, cho phép dễ dàng `gửi một nhóm các yêu cầu song song` theo cách giúp giảm đáng kể ảnh hưởng của một yếu tố thường gặp – đó là độ trễ mạng không ổn định `(network jitter)`. Burp sẽ tự động điều chỉnh kỹ thuật gửi phù hợp với `phiên bản HTTP` mà máy chủ hỗ trợ:

- Với `HTTP/1`, Burp sử dụng kỹ thuật đồng bộ byte cuối cổ điển `(last-byte synchronization)`.
- Với `HTTP/2`, Burp áp dụng kỹ thuật `single-packet attack`, lần đầu được nhóm PortSwigger Research trình bày tại hội nghị Black Hat USA 2023.

Kỹ thuật `single-packet attack` cho phép `loại bỏ hoàn toàn sự can thiệp từ jitter mạng` bằng cách sử dụng một gói `TCP` duy nhất để hoàn tất `20–30` yêu cầu cùng lúc.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image3.png?raw=true)

Mặc dù, thông thường chỉ cần `hai yêu cầu` là có thể kích hoạt một lỗ hổng, nhưng việc gửi số lượng lớn yêu cầu như trên giúp giảm thiểu `độ trễ nội bộ` – còn gọi là `jitter phía máy chủ` (server-side jitter). Điều này đặc biệt hữu ích trong giai đoạn khám phá ban đầu. 

#### Lab: Limit overrun race conditions

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image4.png?raw=true)

Access the lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image5.png?raw=true)

Đăng nhập vào account được cung cấp `wiener:peter`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image6.png?raw=true)

Hiện tại có $50.00 trong tài khoản. Sản phẩm cần mua là:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image7.png?raw=true)

Áp dụng mã giảm giá `PROMO20`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image8.png?raw=true)

Thử áp dụng mã giảm giá một lần nữa:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image9.png?raw=true)

-> Website thông báo mã đã được sử dụng. 

Request khi áp dụng mã giảm giá lần 2:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image10.png?raw=true)

Request nếu áp dụng mã giảm giá thành công:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image11.png?raw=true)

Send request này tới repeater, ý tưởng để khai thác lỗ hổng `race conditions` là gửi nhiều request liên tiếp trong một khoảng thời gian ngắn, chọn `Create tab group`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image12.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image13.png?raw=true)

Click chuột phải vào request và chọn Send to repeater liên tục:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image14.png?raw=true)

Để gửi 32 request này cùng một lúc, chọn `Send group in parallel`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image15.png?raw=true)

Tiếp tục gửi lại cho đến khi nào đạt mức giá mong muốn:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image16.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image17.png?raw=true)

Mua sản phẩm và solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image18.png?raw=true)

### Detecting and exploiting limit overrun race conditions with Turbo Intruder

#### Detecting and exploiting limit overrun race conditions with Turbo Intruder

Bên cạnh việc hỗ trợ sẵn kỹ thuật `single-packet attack` trong `Burp Repeater`, tiện ích mở rộng `Turbo Intruder` cũng hỗ trợ kỹ thuật này. Có thể tải phiên bản mới nhất từ `BApp Store`.

`Turbo Intruder` yêu cầu có một chút kiến thức về Python, nhưng nó rất phù hợp cho các cuộc tấn công phức tạp hơn — chẳng hạn như:

- Những tình huống `cần thử lại nhiều lần` (multiple retries)
- Cần `căn chỉnh thời gian` gửi yêu cầu theo từng đợt (staggered request timing)
- Hoặc cần gửi một `lượng yêu cầu cực lớn`

#### Detecting and exploiting limit overrun race conditions with Turbo Intruder - Continued

Cách sử dụng `single-packet attack` trong Turbo Intruder:

- Đảm bảo rằng mục tiêu hỗ trợ `HTTP/2`. Kỹ thuật `single-packet attack` không tương thích với `HTTP/1`.

- Thiết lập các tùy chọn cấu hình `engine=Engine.BURP2` và `concurrentConnections=1` cho request engine.

- Khi đưa các yêu cầu vào hàng đợi, hãy nhóm chúng bằng cách gán một `tên gate` thông qua đối số `gate` trong phương thức `engine.queue()`.

- Để gửi tất cả các yêu cầu trong một nhóm cụ thể, bạn chỉ cần `mở gate tương ứng` bằng phương thức `engine.openGate()`.

        def queueRequests(target, wordlists):
            engine = RequestEngine(endpoint=target.endpoint,
                                    concurrentConnections=1,
                                    engine=Engine.BURP2
                                    )
            
            # xếp hàng 20 yêu cầu vào gate '1'
            for i in range(20):
                engine.queue(target.req, gate='1')
            
            # gửi tất cả yêu cầu trong gate '1' song song
            engine.openGate('1')

Để biết thêm chi tiết, có thể tham khảo file mẫu `race-single-packet-attack.py` nằm trong thư mục ví dụ mặc định của `Turbo Intruder`.

#### Lab: Bypassing rate limits via race conditions

![img](19)

Access the lab:

![img](20)

Truy cập trang `My account` và đăng nhập tài khoản `carlos` với password bất kỳ. request `/login` sẽ như sau:

![img](23)

Send request này tới `Turbo Intruder` (Right click -> Extensions -> Send to Turbo Intruer):

![img](24)

Chọn `examples/race-single-packet-attack.py`:

![img](25)

Chỉnh sửa code thành như sau:

    def queueRequests(target, wordlists):

        # if the target supports HTTP/2, use engine=Engine.BURP2 to trigger the single-packet attack
        # if they only support HTTP/1, use Engine.THREADED or Engine.BURP instead
        # for more information, check out https://portswigger.net/research/smashing-the-state-machine
        engine = RequestEngine(endpoint=target.endpoint,
                            concurrentConnections=1,
                            engine=Engine.BURP2
                            )

        passwords = ['123123', 'abc123', 'football', 'monkey', 'letmein', 'shadow', 'master', '666666', 'qwertyuiop', '123321', 'mustang', '123456', 'password', '12345678', 'qwerty', '123456789', '12345', '1234', '111111', '1234567', 'dragon', '1234567890', 'michael', 'x654321', 'superman', '1qaz2wsx', 'baseball', '7777777', '121212', '000000']
        
        # the 'gate' argument withholds part of each request until openGate is invoked
        # if you see a negative timestamp, the server responded before the request was complete
        for password in passwords:
            engine.queue(target.req, password, gate='1')

        # once every 'race1' tagged request has been queued
        # invoke engine.openGate() to send them in sync
        engine.openGate('1')


    def handleResponse(req, interesting):
        table.add(req)

Chọn vị trí cần brute-force là ở password, vì là string nên thay thành `%s`:

![img](26)

Click Attack:

![img](27)

Tìm được password cho carlos, tiến hành đăng nhập. 

Admin panel, chọn xóa carlos:

![img](21)

Solved the lab!

![img](22)







