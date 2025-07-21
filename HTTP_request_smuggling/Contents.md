# Contents
- [What is HTTP request smuggling?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#what-is-http-request-smuggling)
- [What happens in an HTTP request smuggling attack?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#what-happens-in-an-http-request-smuggling-attack)
- [How do HTTP request smuggling vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#how-do-http-request-smuggling-vulnerabilities-arise)
- [How to perform an HTTP request smuggling attack](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#how-to-perform-an-http-request-smuggling-attack)
- [How to prevent HTTP request smuggling vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#how-to-prevent-http-request-smuggling-vulnerabilities)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#labs)
    - [Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/Contents.md#lab-http-request-smuggling-confirming-a-clte-vulnerability-via-differential-responses)
    - [Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses]()

# What is HTTP request smuggling?
`HTTP request smuggling` (kỹ thuật giấu yêu cầu HTTP) là một kỹ thuật nhằm `can thiệp` vào cách mà một trang web `xử lý chuỗi các yêu cầu HTTP` được gửi từ một hoặc nhiều người dùng. Các lỗ hổng liên quan đến `request smuggling` thường rất nghiêm trọng, cho phép kẻ tấn công vượt qua các cơ chế bảo mật, truy cập trái phép vào dữ liệu nhạy cảm, và tấn công trực tiếp người dùng khác của ứng dụng.

Request smuggling chủ yếu liên quan đến các yêu cầu `HTTP/1`. Tuy nhiên, các trang web hỗ trợ HTTP/2 cũng có thể bị ảnh hưởng, tùy thuộc vào kiến trúc phía máy chủ (back-end) của chúng.

# What happens in an HTTP request smuggling attack?
Đây là kỹ thuật lợi dụng `sự không đồng nhất` giữa `máy chủ front-end` (như `proxy` hoặc `load balancer`) và `back-end` trong việc `xác định ranh giới` các yêu cầu HTTP.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image0.png?raw=true)

Trong kiến trúc web hiện đại, front-end thường `chuyển tiếp nhiều yêu cầu qua cùng một kết nối` tới back-end. Nếu hai bên xử lý khác nhau về `điểm kết thúc yêu cầu`, kẻ tấn công có thể `chèn một yêu cầu mơ hồ` khiến back-end hiểu sai và xử lý sai lệch.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image1.png?raw=true)

# How do HTTP request smuggling vulnerabilities arise?
Phần lớn các lỗ hổng `HTTP request smuggling` xuất phát từ việc `HTTP/1` cho phép `hai cách khác nhau để xác định điểm kết thúc của một yêu cầu`:

- Header `Content-Length` - chỉ rõ số byte của request body.

    Ví dụ:

        POST /search HTTP/1.1
        Host: example.com
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 11

        q=smuggling
- Header `Transfer-Encoding: chunked` – cho biết `phần body được mã hóa theo từng đoạn` (chunk), với mỗi đoạn gồm kích thước (hex) và dữ liệu, kết thúc bằng đoạn có kích thước bằng 0.

    Ví dụ:

        POST /search HTTP/1.1
        Host: example.com
        Content-Type: application/x-www-form-urlencoded
        Transfer-Encoding: chunked

        b
        q=smuggling
        0

**Vấn đề phát sinh**:

Khi cùng lúc sử dụng cả hai header `Content-Length` và `Transfer-Encoding`, có thể xảy ra mâu thuẫn. Theo chuẩn `HTTP/1`, nếu cả hai cùng xuất hiện, `Content-Length sẽ bị bỏ qua`. Điều này an toàn khi chỉ có một server xử lý, nhưng có thể gây lỗi khi có nhiều máy chủ chuỗi (front-end & back-end) vì:
- Một số máy chủ `không hỗ trợ Transfer-Encoding` trong yêu cầu.
- Một số máy chủ có thể `bị đánh lừa không xử lý Transfer-Encoding` nếu tiêu đề `bị làm mờ` (obfuscate).

Khi `front-end` và `back-end` hiểu khác nhau về ranh giới yêu cầu do xử lý khác nhau với `Transfer-Encoding`, kẻ tấn công có thể khai thác sự không nhất quán này để thực hiện `request smuggling`.

**Lưu ý**:
- Nhiều công cụ kiểm thử bảo mật và trình duyệt không hiển thị rõ chunked encoding trong yêu cầu, khiến lỗ hổng dễ bị bỏ sót.
- `HTTP/2 thuần không bị ảnh hưởng bởi request smuggling` vì nó dùng một cơ chế duy nhất và rõ ràng để xác định độ dài yêu cầu.
- Tuy nhiên, nhiều website có `front-end hỗ trợ HTTP/2` nhưng `back-end chỉ hỗ trợ HTTP/1`, dẫn đến quá trình `downgrade HTTP/2 → HTTP/1`, từ đó lỗ hổng request smuggling vẫn tồn tại.
# How to perform an HTTP request smuggling attack
Tấn công request smuggling cổ điển thường `kết hợp cả Content-Length và Transfer-Encoding` trong một yêu cầu `HTTP/1`, nhằm khiến front-end và back-end xử lý khác nhau, tạo ra điểm mơ hồ có thể khai thác. Có 3 biến thể phổ biến:

**1. CL.TE (Front-end dùng Content-Length, Back-end dùng Transfer-Encoding):**

**Ví dụ**:

    POST / HTTP/1.1
    Host: vulnerable-website.com
    Content-Length: 13
    Transfer-Encoding: chunked

    0

    SMUGGLED
- Front-end thấy `Content-Length = 13`=> coi toàn bộ phần body đến "SMUGGLED" là một yêu cầu hợp lệ.
- Back-end thấy `Transfer-Encoding: chunked` => thấy chunk đầu là 0 nên `kết thúc yêu cầu ngay trước “SMUGGLED”`.
- Kết quả: `"SMUGGLED" được coi là yêu cầu tiếp theo`, nhưng được chèn từ kẻ tấn công.

**2. TE.CL (Front-end dùng Transfer-Encoding, Back-end dùng Content-Length):**

**Ví dụ**:

    POST / HTTP/1.1
    Host: vulnerable-website.com
    Content-Length: 3
    Transfer-Encoding: chunked

    8
    SMUGGLED
    0
- `Front-end xử lý chunked`: thấy chunk 8 => xử lý "SMUGGLED", chunk 0 => kết thúc yêu cầu.
- `Back-end xử lý theo Content-Length = 3` => chỉ đọc 3 byte đầu (trước “SMUGGLED”), phần còn lại ("SMUGGLED") bị hiểu nhầm là yêu cầu kế tiếp.

**Lưu ý**: Để test `TE.CL` trong `Burp Repeater`, cần tắt tuỳ chọn “Update Content-Length” và đảm bảo có chuỗi `\r\n\r\n` sau 0.

**3. TE.TE (Cả hai đều hỗ trợ Transfer-Encoding, nhưng một máy chủ bị đánh lừa bỏ qua tiêu đề nhờ obfuscation):**

Trong kiểu tấn công này, tiêu đề `Transfer-Encoding`được làm mờ (obfuscate) để `chỉ một trong hai máy chủ xử lý nó`, còn máy còn lại bỏ qua. Các cách làm mờ bao gồm:
- `Transfer-Encoding : chunked` (có dấu cách)
- `Transfer-Encoding:[tab]chunked` (tab)
- `Transfer-Encoding: xchunked` (sai chính tả)
- `Transfer-Encoding\n`: chunked (dòng mới)
- `X: X\nTransfer-Encoding: chunked` (tiêu đề giả phía trước)

=> Điều này lợi dụng việc các máy chủ thực tế `không tuân thủ 100% chuẩn HTTP`, dẫn đến xử lý khác nhau.

Tùy vào máy chủ nào bị đánh lừa (front-end hoặc back-end), phần còn lại của cuộc tấn công sẽ giống như kiểu `CL.TE` hoặc `TE.CL`.

**Lưu ý về HTTP/2:**
- Các tấn công này chỉ áp dụng cho `HTTP/1`.
- `HTTP/2 không bị ảnh hưởng` vì có cơ chế xác định độ dài thống nhất và rõ ràng.
- Tuy nhiên, nếu website có `front-end dùng HTTP/2` nhưng `back-end vẫn dùng HTTP/1`, khi xảy ra HTTP downgrade, tấn công vẫn khả thi.
# How to prevent HTTP request smuggling vulnerabilities
**1. Sử dụng HTTP/2 từ đầu đến cuối**
- Tránh downgrade về `HTTP/1` nếu có thể.
- `HTTP/2` có cơ chế xác định độ dài yêu cầu rõ ràng và chống request smuggling hiệu quả.
- Nếu phải downgrade, cần xác thực chặt chẽ yêu cầu được chuyển đổi theo chuẩn HTTP/1.1. Ví dụ:
    - Từ chối `yêu cầu có xuống dòng` trong tiêu đề, `dấu hai chấm trong tên header`, hoặc `khoảng trắng` trong phương thức HTTP.

**2. Front-end nên `chuẩn hóa các yêu cầu mơ hồ`, còn back-end `nên từ chối và đóng kết nối TCP` nếu yêu cầu vẫn không rõ ràng**

**3. Không bao giờ giả định rằng yêu cầu HTTP sẽ không có phần thân (body)**
    - Đây là gốc rễ của lỗ hổng `CL.0` và `client-side desync`.

**4. Mặc định đóng kết nối khi máy chủ gặp lỗi trong quá trình xử lý yêu cầu**
- Giúp giảm nguy cơ giữ lại kết nối bị nhiễm độc (poisoned).

**5. Nếu dùng proxy chuyển tiếp (forward proxy)**
- Hãy bật HTTP/2 ở đầu ra (upstream) nếu có thể để hạn chế rủi ro.

**6. Tắt việc tái sử dụng kết nối back-end**
- Có thể giúp giảm một số kiểu tấn công, nhưng không chống được request tunneling.

# Labs
## Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses
**1. Yêu cầu**

Bài lab này bao gồm một máy chủ front-end và một máy chủ back-end, trong đó máy chủ front-end không hỗ trợ chunked encoding.

Để hoàn thành lab, bạn cần giấu một yêu cầu (smuggle) vào máy chủ back-end, sao cho yêu cầu tiếp theo đến đường dẫn / (web root) sẽ trả về lỗi 404 Not Found.

Lưu ý:

Mặc dù lab hỗ trợ HTTP/2, nhưng giải pháp yêu cầu sử dụng kỹ thuật chỉ áp dụng được trong HTTP/1. Bạn có thể chuyển giao thức thủ công trong Burp Repeater tại phần Request attributes trong Inspector panel.

**2. Thực hiện**

Website trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image2.png?raw=true)

Tìm kiếm trong `Burp history` request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image4.png?raw=true)

=> Request đang sử dụng `HTTTP/2`, cần xác minh xem website có hỗ trợ `HTTP/1.1` không.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image3.png?raw=true)

=> Response trả về mã 200 => website có support `HTTP/1.1`

Cấu hình request smuggling, gửi lần 1:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image5.png?raw=true)

Gửi lần 2:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image6.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/HTTP_request_smuggling/images/image7.png?raw=true)

Tại sao có thể làm như này?

- Front-end dùng `Content-Length`(trong lần gửi request đầu tiên) => gửi toàn bộ phần body đến back-end, do không hiểu `Transfer-Encoding: chunked` nên toàn bộ phần sau được coi như phần body bình thường, không bị `chunk`
- Back-end dùng `Transfer-Encoding` => xử lý "chunked", cắt phần `GET /404` thành yêu cầu mới, do thấy `chunk 0` => nghĩa là kết thúc yêu cầu đầu tiên, phần sau đó được coi là yêu cầu tiếp theo
- Khi gửi yêu cầu lần đầu => `"GET /404"` bị smuggle vào.
- Khi gửi lần thứ hai => back-end xử lý tiếp phần `"GET /404 HTTP/1.1"`, vì nó đã được smuggle từ lần đầu, nên back-end phản hồi `404 Not Found`.

## Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses
**1. Yêu cầu**

Bài lab này bao gồm một máy chủ front-end và một máy chủ back-end, trong đó máy chủ back-end không hỗ trợ chunked encoding.

Để hoàn thành bài lab, bạn cần smuggle một request đến máy chủ back-end, sao cho request tiếp theo đến đường dẫn `/` (web root) sẽ kích hoạt phản hồi 404 Not Found.

**2. Thực hiện**

Request khi load trang web:

![img](8)

Thử thay đổi thành `HTTP/1.1` và send request:

![img](9)

=> Vẫn thành công, website có hỗ trợ `HTTP/1.1`

Gửi request lần đầu tiên:

![img](12)

Request gửi lần thứ 2:

![img](10)

![img](11)

Tại sao request lại gây ra lỗi TE.CL?
- Request này có `Transfer-Encoding: chunked` nhưng vẫn khai báo `Content-Length: 4` => Đây chính là điểm sai và là cơ sở để khai thác TE.CL.

        5e
        POST /404 HTTP/1.1
        Content-Type: ...
        x=1
        0
- Frontend tin vào `Transfer-Encoding`, cắt chunked body đúng cách.
- Backend lại bỏ qua `Transfer-Encoding`, chỉ dùng `Content-Length`, đọc nhầm phần còn lại của request đầu tiên như là một request thứ hai.
- Lần gửi request đầu tiên:
    - Frontend thấy `Transfer-Encoding: chunked` ⇒ xử lý phần body theo chunked.
    - Body chunked là:

            5e
            POST /404 HTTP/1.1
            Content-Type: ...
            x=1
            0
    => Frontend cắt đúng tại `0\r\n`, coi đó là hết body.
    - Sau khi xử lý xong, Frontend gửi toàn bộ request xuống backend.
    - Backend lại không hiểu `chunked encoding`, mà nhìn thấy:

            Content-Length: 4
    - Backend chỉ đọc 4 byte tiếp theo sau header, ví dụ là `5e\r\n`.
    - Phần còn lại (đoạn `POST /404 HTTP/1.1...`) bị coi là request thứ hai, nhưng bị giữ lại trong buffer của backend như là một `smuggled request` (request lén chèn).
    - Do smuggled request đó là `POST /404 HTTP/1.1...` nên sẽ được xử lý trong lần request tiếp theo.
    
    => Do đó, request đầu tiên trả về `200 OK` vì `/` vẫn xử lý bình thường.
- Lần gửi request thứ hai:
    - `Smuggled request` từ lần trước (trong buffer) sẽ được dùng như thể nó là phần đầu của request mới.
    - Backend xử lý `POST /404 HTTP/1.1` ⇒ dẫn đến 404 Not Found.










