# Race conditions

## Content

- [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions)

    - [Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions-1)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued)
    - [Limit overrun race conditions - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#limit-overrun-race-conditions---continued-1)

- [Detecting and exploiting limit overrun race conditions with Burp Repeater](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-burp-repeater)

    - [Detecting and exploiting limit overrun race conditions with Burp Repeater - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-burp-repeater---continued)
    - [Lab: Limit overrun race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-limit-overrun-race-conditions)

- [Detecting and exploiting limit overrun race conditions with Turbo Intruder](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-turbo-intruder)

    - [Detecting and exploiting limit overrun race conditions with Turbo Intruder](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-turbo-intruder-1)
    - [Detecting and exploiting limit overrun race conditions with Turbo Intruder - Continued](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#detecting-and-exploiting-limit-overrun-race-conditions-with-turbo-intruder---continued)
    - [Lab: Bypassing rate limits via race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#lab-bypassing-rate-limits-via-race-conditions)

- [Hidden multi-step sequences](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#hidden-multi-step-sequences)
- [Methodology](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#methodology)

    - [Predict potential collisions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#predict-potential-collisions)
    - [Probe for clues](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#probe-for-clues)
    - [Prove the concept](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#prove-the-concept)

- [Multi-endpoint race conditions](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/Race_conditions.md#multi-endpoint-race-conditions)

    - [Aligning multi-endpoint race windows]()
    - [Connection warming]()

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

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image19.png?raw=true)

Access the lab:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image20.png?raw=true)

Truy cập trang `My account` và đăng nhập tài khoản `carlos` với password bất kỳ. request `/login` sẽ như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image23.png?raw=true)

Send request này tới `Turbo Intruder` (Right click -> Extensions -> Send to Turbo Intruer):

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image24.png?raw=true)

Chọn `examples/race-single-packet-attack.py`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image25.png?raw=true)

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

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image26.png?raw=true)

Click Attack:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image27.png?raw=true)

Tìm được password cho carlos, tiến hành đăng nhập. 

Admin panel, chọn xóa carlos:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image21.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image22.png?raw=true)

### Hidden multi-step sequences

Trong thực tế, một `request` duy nhất có thể bắt đầu một chuỗi nhiều bước ẩn sau hậu trường, chuyển ứng dụng qua nhiều trạng thái ẩn mà nó sẽ vào và rồi thoát ra trước khi quá trình xử lý `request` hoàn tất. Gọi chúng là `"sub-states"` (trạng thái phụ).

Nếu có thể xác định một hoặc nhiều `HTTP request` gây ra sự tương tác với cùng một dữ liệu, có thể lợi dụng các trạng thái phụ này để lộ ra những biến thể nhạy cảm về thời gian của các lỗi logic mà thường gặp trong các quy trình làm việc nhiều bước. Điều này tạo ra các lỗ hổng điều kiện đua (race condition) vượt xa các lỗi tràn giới hạn.

Ví dụ, với các quy trình `xác thực đa yếu tố` (MFA) bị lỗi, cho phép thực hiện `phần đầu tiên` của `quá trình đăng nhập` bằng thông tin đăng nhập đã biết, sau đó điều hướng trực tiếp đến ứng dụng qua trình duyệt cưỡng chế, qua đó bỏ qua hoàn toàn `MFA`.

**Lưu ý**: Nếu không quen thuộc với lỗ hổng này, hãy xem thử bài tập bỏ qua 2FA đơn giản trong chủ đề Lỗ hổng xác thực.

Dưới đây là mã giả mô phỏng cách một website có thể dễ bị tấn công với một biến thể `race condition` của cuộc tấn công này:

    session['userid'] = user.userid
    if user.mfa_enabled:
        session['enforce_mfa'] = True
        # tạo mã MFA và gửi cho người dùng
        # chuyển hướng trình duyệt đến biểu mẫu nhập mã MFA

Đây thực sự là một chuỗi nhiều bước trong phạm vi của một request duy nhất. Quan trọng nhất, nó chuyển qua một `trạng thái phụ` trong đó người dùng `tạm thời có một phiên đăng nhập hợp lệ`, nhưng `MFA` chưa được thực thi. Kẻ tấn công có thể lợi dụng điều này bằng cách gửi một `request` đăng nhập cùng với `request` đến một điểm cuối nhạy cảm, đã được xác thực.

### Methodology

Để phát hiện và khai thác `các chuỗi nhiều bước ẩn`, khuyến nghị sử dụng phương pháp luận sau, được tóm tắt từ bài báo trắng Smashing the state machine: The true potential of web race conditions của PortSwigger Research.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image28.png?raw=true)

#### Predict potential collisions

Việc kiểm tra từng điểm cuối là `không thực tế`. Sau khi lập bản đồ trang web mục tiêu như bình thường, có thể giảm số lượng điểm cuối cần kiểm tra bằng cách tự hỏi những câu hỏi sau:

- `Điểm cuối này có quan trọng về mặt bảo mật không?` Nhiều điểm cuối không tác động đến chức năng quan trọng, vì vậy chúng không đáng để kiểm tra.
- `Có khả năng va chạm nào không?` Để xảy ra va chạm thành công, thường cần hai hoặc nhiều request kích hoạt các thao tác trên cùng một bản ghi. Ví dụ, xem xét các biến thể sau của một phương thức khôi phục mật khẩu:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Race-conditions/images/image29.png?raw=true)

Với ví dụ đầu tiên, việc yêu cầu `khôi phục mật khẩu song song` cho hai người dùng khác nhau khó có thể gây ra va chạm vì nó dẫn đến thay đổi hai bản ghi khác nhau. Tuy nhiên, phương thức thứ hai cho phép `chỉnh sửa cùng một bản ghi` với các yêu cầu từ `hai người dùng khác nhau`.

#### Probe for clues

Để nhận diện dấu hiệu, trước tiên cần xác định cách điểm cuối hoạt động `trong điều kiện bình thường`. Có thể làm điều này trong `Burp Repeater` bằng cách `nhóm tất cả request` của mình và sử dụng tùy chọn `"Send group in sequence"` (kết nối riêng biệt).

Sau đó, `gửi cùng một nhóm request cùng lúc` bằng cách sử dụng `single-packet attack` (hoặc `last-byte sync `nếu `HTTP/2` không được hỗ trợ) để giảm thiểu độ nhiễu mạng. Có thể làm điều này trong `Burp Repeater` bằng cách chọn tùy chọn `"Send group in parallel"`. 

`Bất kỳ điều gì` cũng có thể là `dấu hiệu`. Hãy tìm kiếm bất kỳ sự thay đổi nào so với những gì quan sát được trong quá trình `benchmark`. Điều này bao gồm sự thay đổi trong một hoặc nhiều phản hồi, nhưng đừng quên các tác động thứ cấp như thay đổi nội dung email hoặc sự thay đổi có thể thấy được trong hành vi của ứng dụng sau đó.

#### Prove the concept

Cố gắng hiểu những gì đang xảy ra, loại bỏ các requests không cần thiết và đảm bảo rằng vẫn có thể tái tạo lại các tác động.

Các điều kiện đua nâng cao có thể tạo ra các nguyên lý bất thường và độc đáo, vì vậy con đường để đạt được tác động tối đa không phải lúc nào cũng rõ ràng ngay lập tức. Có thể giúp ích khi nghĩ về mỗi điều kiện đua như một điểm yếu cấu trúc thay vì một lỗ hổng đơn lẻ.

### Multi-endpoint race conditions

Một trong những dạng điều kiện tranh chấp (race condition) dễ hiểu nhất là khi gửi các `request` đến `nhiều endpoint` cùng lúc.

Hãy nghĩ đến một lỗi logic kinh điển trong các cửa hàng trực tuyến: thêm một sản phẩm vào giỏ hàng, thanh toán cho nó, rồi `nhanh chóng thêm thêm sản phẩm khác` vào giỏ trước khi cố ý truy cập thẳng vào trang xác nhận đơn hàng.

**Lưu ý**: Nếu chưa quen với kiểu tấn công này, hãy xem phần "Insufficient workflow validation" trong chủ đề "Business logic vulnerabilities".

Một biến thể của lỗ hổng này có thể xảy ra khi quá trình `xác thực thanh toán` và `xác nhận đơn hàng` được xử lý trong `cùng một request`. Khi đó, sơ đồ trạng thái (state machine) của đơn hàng có thể trông như sau:

![img](30)

Trong trường hợp này, có thể thêm nhiều sản phẩm vào giỏ hàng `trong khoảng thời gian ngắn` (race window) `giữa lúc` thanh toán được xác thực và lúc đơn hàng được xác nhận cuối cùng.

#### Aligning multi-endpoint race windows - Căn chỉnh thời điểm race giữa các endpoint

Khi kiểm thử điều kiện tranh chấp trên nhiều `endpoint`, có thể gặp khó khăn trong việc căn chỉnh "race window" của từng request — ngay cả khi đã gửi tất cả chúng cùng lúc bằng kỹ thuật `"single-packet"`.

![img](31)

Vấn đề phổ biến này chủ yếu bắt nguồn từ hai nguyên nhân sau:

- `Độ trễ do kiến trúc mạng gây ra` – Ví dụ, có thể xảy ra độ trễ khi `server front-end` thiết lập kết nối mới với `back-end`. Giao thức sử dụng cũng có thể ảnh hưởng đáng kể đến độ trễ này.
- `Độ trễ do quá trình xử lý riêng của từng endpoint` – Mỗi endpoint có thời gian xử lý khác nhau, đôi khi chênh lệch rất lớn, tùy thuộc vào các thao tác mà nó thực hiện.

May mắn, vẫn có những cách để khắc phục hoặc giảm thiểu cả hai vấn đề này.

#### Connection warming

Độ trễ khi kết nối với `back-end` thường không gây trở ngại cho các cuộc tấn công `race condition`, vì những độ trễ này thường ảnh hưởng đến các `request song song` một cách tương đương, giúp chúng vẫn được xử lý đồng bộ.

Tuy nhiên, điều quan trọng là phải phân biệt được những độ trễ do `kết nối mạng` với những độ trễ phát sinh từ `chính các endpoint`. Một cách để làm điều này là `"làm nóng"` kết nối bằng một hoặc vài `request không quan trọng` để xem liệu điều này có giúp thời gian xử lý còn lại trở nên ổn định hơn hay không. Trong `Burp Repeater`, có thể thử thêm một `request GET` tới trang chủ ở đầu nhóm tab, rồi sử dụng tùy chọn `Send group in sequence (single connection)`.

Nếu request đầu tiên vẫn có thời gian `xử lý lâu hơn`, nhưng các request còn lại được `xử lý gần như cùng lúc` trong một khoảng thời gian ngắn, thì có thể bỏ qua độ trễ ban đầu đó và tiếp tục kiểm thử như bình thường.

Tuy nhiên, nếu vẫn thấy thời gian phản hồi không ổn định trên một endpoint, ngay cả khi đã dùng kỹ thuật `single-packet`, điều này cho thấy `độ trễ từ back-end` đang ảnh hưởng đến cuộc tấn công. Lúc này, có thể sử dụng `Turbo Intruder` để gửi một vài request "làm nóng" trước khi gửi các request tấn công chính.































