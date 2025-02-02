# Bypassing SameSite restrictions using on-site gadgets

Nếu cookie được set với thuộc tính SameSite=Strict, các trình duyệt sẽ không đưa nó vào bất kỳ cross-site requests nào. Bạn có thể vượt qua giới hạn này nếu bạn có thể tìm thấy một gadget dẫn đến secondary request trong cùng một site.

Possible gadget là một client-side redirect, xây dựng động mục tiêu chuyển hướng bằng cách sử dụng đầu vào có thể điều khiển được của kẻ tấn công như tham số URL. Để biết một số ví dụ, hãy xem các tài liệu của chúng tôi về chuyển hướng mở dựa trên DOM.

# Bypassing SameSite restrictions using on-site gadgets - Continued

Theo như các trình duyệt, các client-side redirects này hoàn toàn không thực sự chuyển hướng; Requests kết quả chỉ được coi là một yêu cầu bình thường, độc lập. Quan trọng nhất, đây là một request cùng trang web và, như vậy, sẽ bao gồm tất cả các cookie liên quan đến trang web, bất kể bất kỳ hạn chế nào được đưa ra.

Nếu bạn có thể thao tác với gadget này để gợi ra secondary request độc hại, điều này có thể cho phép bạn bypass mọi SameSite cookie restrictions hoàn toàn.

# Lab: SameSite Strict bypass via client-side redirect
![img](https://imgur.com/kfPejdX.png)

### Web page: 
![img](https://imgur.com/DwuTUN0.png)

### Solution:

Log in bằng account được cấp wiener:peter:

![img](https://imgur.com/krRTNmP.png)

Trước khi update email, mở Burp Suite và Intercept request: 

![img](https://imgur.com/ORZhwz0.png)

Để ý thấy request không có bất kỳ tokens nào có thể đoán trước được nên có thể bị tấn công CSRF.

Thử Intercept Log in request: 

![img](https://imgur.com/gy56aBW.png)

Send Request to repeater và xem response: 

![img](https://imgur.com/Q0i7nO6.png)

--> Website đã set-cookie với thuộc tính SameSite=Strict để ngăn trình duyệt bao gồm cross-site requests.

Cần xác định một gadget phù hợp để bypass. 

Thử view post Making The Holidays Special Again:

![img](https://imgur.com/kY3AcUW.png)

Thử để lại 1 comment và Intercept request này: 

![img](https://imgur.com/zDXLlA0.png)

![img](https://imgur.com/yJoxnPV.png)

Forward request này: 

![img](https://imgur.com/bBnXbh4.png)

![img](https://imgur.com/tDnqYcT.png)

Trong Response của request, tìm ra file JS mà website dùng để xử lý việc confirm redirect comment: /resources/js/commentConfirmationRedirect.js

Xem qua nội dung của file JS: 

![img](https://imgur.com/FxsA05D.png)

--> Website sử dụng tham số *postId* để xây dựng đường dẫn tự động cho client-side redirect. 

Thử truy cập lại path confirm comment: 

![img](https://imgur.com/6XWqaPK.png)

Nhưng lần này thay postId=foo (1 giá trị ngẫu nhiên, không hợp lệ):

![img](https://imgur.com/BSZxZxQ.png)

![img](https://imgur.com/k3YzPWq.png)

![img](https://imgur.com/A37DH0i.png)

Nhận thấy trước khi chuyển hướng đến trang Not Found, Request đã yêu cầu đường dẫn /post/foo: 

![img](https://imgur.com/7ThCxoE.png)

Đây là path của post confirmation page trước khi client-side JS cố gắng chuyển hướng đến path chứa chuỗi được inject.

Thử inject path traversal để tự động chuyển hướng đến trang my-account: 

![img](https://imgur.com/DuJCidC.png)

![img](https://imgur.com/9N8Tl55.png)

![img](https://imgur.com/VbR4yZ3.png)

--> Chuyển hướng thành công. Có thể sử dụng tham số postId để khai thác. 

Tạo 1 payload có dạng như sau: 

    <script>
        document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=../my-account";
    </script>

Go to exploit server và chèn payload này vào phần body: 

![img](https://imgur.com/ZVKWiDm.png)

Thử Store và View exploit, xác nhận rằng trang my-account vẫn được chuyển hướng. 

Thử thay đổi change email request từ POST method thành GET method và xem response: 

![img](https://imgur.com/gO3IlGc.png)

--> Endpoint vẫn cho phép thay đổi email với GET method.

Để ý thấy trong change email request sử dụng 2 tham số là email và submit: 

![img](https://imgur.com/iLp7l3Y.png)

Thay đổi payload thành: 

    <script>
        document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";
    </script>

![img](https://imgur.com/IMWJAoB.png)


View exploit và Deliver exploit to victim.

Solved the lab!

![img](https://imgur.com/RXCu6sz.png)

