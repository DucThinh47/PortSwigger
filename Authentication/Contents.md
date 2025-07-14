# Contents

- [Authentication vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#authentication-vulnerabilities)
- [What is authentication?](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#what-is-authentication)
    - [What is the difference between authentication and authorization?](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#what-is-the-difference-between-authentication-and-authorization)
- [How do authentication vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#how-do-authentication-vulnerabilities-arise)
- [What is the impact of vulnerable authentication?](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#what-is-the-impact-of-vulnerable-authentication)
- [Vulnerabilities in authentication mechanisms](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#vulnerabilities-in-authentication-mechanisms)
    - [Vulnerabilities in third-party authentication mechanisms](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#vulnerabilities-in-third-party-authentication-mechanisms)
- [Preventing attacks on your own authentication mechanisms](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#preventing-attacks-on-your-own-authentication-mechanisms)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#labs)
    - [Lab: Username enumeration via different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-different-responses)
    - [Lab: 2FA simple bypass](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-2fa-simple-bypass)
    - [Lab: Password reset broken logic](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-password-reset-broken-logic)
    - [Lab: Username enumeration via subtly different responses](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-subtly-different-responses)
    - [Lab: Username enumeration via response timing](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-response-timing)
    - [Lab: Broken brute-force protection, IP block](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-broken-brute-force-protection-ip-block)
    - [Lab: Username enumeration via account lock](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-username-enumeration-via-account-lock)
    - [Lab: 2FA broken logic](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/Contents.md#lab-2fa-broken-logic)

# Authentication vulnerabilities
Về mặt khái niệm, `các lỗ hổng xác thực` rất dễ hiểu. Tuy nhiên, chúng thường cực kỳ nghiêm trọng vì có mối quan hệ rõ ràng giữa xác thực và bảo mật.

Các lỗ hổng xác thực có thể cho phép kẻ tấn công `truy cập vào dữ liệu và chức năng nhạy cảm`. Chúng cũng phơi bày thêm `bề mặt tấn công` cho các khai thác tiếp theo. Vì lý do này, điều quan trọng là phải tìm hiểu cách `xác định và khai thác các lỗ hổng xác thực`, cũng như cách `vượt qua các biện pháp bảo vệ phổ biến`.

Trong phần này, tập trung vào tìm hiểu:

- Các `cơ chế xác thực phổ biến nhất` được các trang web sử dụng
- Các `lỗ hổng tiềm ẩn` trong các cơ chế này.
- Các `lỗ hổng cố hữu` trong các cơ chế xác thực khác nhau.
- Các `lỗ hổng điển hình` được tạo ra do việc triển khai không đúng cách.
- Cách có thể tạo ra các cơ chế xác thực của riêng mình `chắc chắn nhất` có thể.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image0.png?raw=true)

# What is authentication?
`Xác thực` là quá trình `xác minh danh tính` của người dùng hoặc máy khách. Các trang web có khả năng bị lộ diện với bất kỳ ai kết nối internet, điều này khiến `các cơ chế xác thực mạnh mẽ` trở thành yếu tố không thể thiếu đối với `bảo mật web hiệu quả`.

Có ba loại xác thực chính:

- `Thứ bạn biết`: Đây có thể là `mật khẩu` hoặc `câu trả lời cho câu hỏi bảo mật`. Chúng đôi khi được gọi là "yếu tố kiến thức".
- `Thứ bạn có`: Đây là `một vật thể vật lý` như điện thoại di động hoặc khóa bảo mật. Chúng đôi khi được gọi là "yếu tố sở hữu".
- `Thứ bạn là (hoặc làm)`: Ví dụ, `dữ liệu sinh trắc học` của bạn hoặc các kiểu hành vi. Chúng đôi khi được gọi là "yếu tố cố hữu".

Các cơ chế xác thực dựa vào một loạt các công nghệ để xác minh một hoặc nhiều yếu tố này.

## What is the difference between authentication and authorization?
`Xác thực (Authentication)` là quá trình `kiểm tra xem người dùng có đúng là người mà họ tuyên bố hay không`. Chẳng hạn, xác thực sẽ xác định liệu người đang cố gắng truy cập trang web với tên người dùng `Carlos123` có thực sự là người đã tạo tài khoản đó hay không.

Trong khi đó, `Ủy quyền (Authorization)` lại là việc `xác minh xem người dùng có được phép thực hiện một hành động nào đó hay không`. Một khi `Carlos123` đã được xác thực, các quyền hạn của họ sẽ xác định những gì họ được phép làm. Ví dụ, họ có thể được ủy quyền để truy cập thông tin cá nhân của người dùng khác, hoặc thực hiện các hành động như xóa tài khoản của người dùng khác.

# How do authentication vulnerabilities arise?
Hầu hết các lỗ hổng trong cơ chế xác thực thường xuất hiện theo `một trong hai cách` sau:
- `Cơ chế xác thực yếu`: Chúng không đủ khả năng bảo vệ chống lại các cuộc tấn công `brute-force`.
- `Lỗi logic hoặc lập trình kém`: Các lỗi này trong quá trình triển khai cho phép kẻ tấn công `vượt qua hoàn toàn` cơ chế xác thực. Tình trạng này đôi khi được gọi là "xác thực bị phá vỡ" (broken authentication).

Trong nhiều lĩnh vực phát triển web, các lỗi logic khiến trang web hoạt động không như mong đợi, điều này có thể là hoặc không phải là vấn đề bảo mật. Tuy nhiên, vì xác thực rất quan trọng đối với bảo mật, nên rất có khả năng logic xác thực bị lỗi sẽ khiến trang web gặp phải các vấn đề bảo mật nghiêm trọng.

# What is the impact of vulnerable authentication?
Tác động của các lỗ hổng xác thực có thể rất nghiêm trọng. Nếu kẻ tấn công `vượt qua xác thực` hoặc `tấn công vét cạn` để truy cập vào tài khoản của người dùng khác, chúng sẽ có quyền truy cập vào tất cả dữ liệu và chức năng mà tài khoản bị xâm nhập đó có. Nếu chúng có thể `chiếm đoạt một tài khoản có đặc quyền cao`, như tài khoản quản trị hệ thống, chúng có thể `kiểm soát hoàn toàn toàn bộ ứng dụng` và thậm chí có khả năng `truy cập vào cơ sở hạ tầng nội bộ`.

Ngay cả khi chỉ chiếm đoạt một tài khoản có `đặc quyền thấp`, kẻ tấn công vẫn có thể truy cập vào dữ liệu mà lẽ ra chúng không được phép, chẳng hạn như `thông tin kinh doanh nhạy cảm về mặt thương mại`. Dù tài khoản đó không có quyền truy cập vào bất kỳ dữ liệu nhạy cảm nào, nó vẫn có thể cho phép kẻ tấn công truy cập các trang bổ sung, từ đó tạo ra `bề mặt tấn công rộng hơn`. Thông thường, các cuộc tấn công mức độ nghiêm trọng cao không thể thực hiện được từ các trang công khai, nhưng chúng có thể khả thi từ một trang nội bộ.

# Vulnerabilities in authentication mechanisms
Hệ thống xác thực của một trang web thường bao gồm nhiều cơ chế riêng biệt, đây là nơi các lỗ hổng có thể phát sinh. Một số lỗ hổng có thể áp dụng chung cho tất cả các ngữ cảnh này, trong khi những lỗ hổng khác lại đặc trưng hơn cho chức năng cụ thể được cung cấp.

Một số lỗ hổng phổ biến nhất tồn tại trong các lĩnh vực sau:
- Lỗ hổng trong đăng nhập bằng mật khẩu
- Lỗ hổng trong xác thực đa yếu tố
- Lỗ hổng trong các cơ chế xác thực khác

Một số bài lab yêu cầu bạn liệt kê username và brute-force password. Để hỗ trợ quá trình này, chúng tôi cung cấp danh sách rút gọn các username và password gợi ý mà bạn nên sử dụng để giải quyết các bài lab.

## Vulnerabilities in third-party authentication mechanisms
Nếu bạn đam mê việc "hack" các cơ chế xác thực và đã hoàn thành các bài lab xác thực chính của chúng tôi, bạn có thể muốn thử sức với các bài lab xác thực OAuth.

# Preventing attacks on your own authentication mechanisms
Chúng ta đã thấy nhiều cách mà các trang web có thể dễ bị tấn công do cách họ triển khai xác thực. Để giảm thiểu rủi ro từ những cuộc tấn công như vậy trên các trang web của riêng bạn, có một số nguyên tắc mà bạn nên luôn cố gắng tuân thủ.

# Labs
## Lab: Username enumeration via different responses
**1. Yêu cầu**

Phòng lab này đang tồn tại lỗ hổng liệt kê username và brute-force password. Có một tài khoản với username và password dễ đoán.

Để hoàn thành phòng lab, bạn cần liệt kê một username hợp lệ, sau đó brute-force password của người dùng này cuối cùng truy cập vào trang tài khoản của họ.

**2. Thực hiện**

Truy cập bài lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image1.png?raw=true)

Lab đã cung cấp cho tôi danh sách các username và password phổ biến.

Trang login: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image2.png?raw=true)

Với 2 danh sách username và password mà labs đã cung cấp, tôi có thể sử dụng Burp Intruder để brute-force. 

Thử login vào 1 tài khoản bất kì: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image3.png?raw=true)

Request login sẽ trông như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image4.png?raw=true)

Send request này sang tab Intruder, trước tiên sẽ brute-force username trước: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image5.png?raw=true)

Chọn Attack mode là Sniper attack, cấu hình Payloads là danh sách usernames lab cung cấp: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image6.png?raw=true)

Start attack!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image7.png?raw=true)

Tìm kiếm response nào có độ dài khác biệt so với các response khác: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image8.png?raw=true)

=> Username `affiliates` trả về response `Incorrect password`

Tiếp theo brute-force password:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image9.png?raw=true)

Tìm ra được password là `password`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image10.png?raw=true)

Thử login account có username là `affiliates` và password là `password`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image11.png?raw=true)

## Lab: 2FA simple bypass
**1. Yêu cầu**

Phòng lab này có thể bị vượt qua xác thực hai yếu tố (2FA). Bạn đã có được tên người dùng và mật khẩu hợp lệ, nhưng không có quyền truy cập vào mã xác minh 2FA của người dùng. Để giải quyết phòng lab, hãy truy cập trang tài khoản của Carlos.

Thông tin đăng nhập của bạn: `wiener:peter`<br>
Thông tin đăng nhập của nạn nhân: `carlos:montoya`

**2. Thực hiện**

Thử login vào tài khoản `wiener` được cung cấp, yêu cầu nhập mã xác thực gửi về email:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image12.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image13.png?raw=true)

Sau khi nhập đúng mã xác thực 2FA: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image14.png?raw=true)

Đăng xuất và đăng nhập vào tài khoản carlos, khi yêu cầu nhập mã xác thực 2FA, thử thay đổi địa chỉ URL từ `/login2`thành `/my-account`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image15.png?raw=true)

=> Đăng nhập thành công mà không cần mã xác thực 2FA

## Lab: Password reset broken logic
**1. Yêu cầu**
Chức năng đặt lại mật khẩu của phòng lab này đang có lỗ hổng. Để giải quyết, hãy đặt lại mật khẩu của Carlos, sau đó đăng nhập và truy cập trang "Tài khoản của tôi" của anh ấy.

Thông tin đăng nhập của bạn: `wiener:peter`<br>
Tên người dùng của nạn nhân: `carlos`

**2. Thực hiện**

Thử tính năng `Forgot password` của tài khoản `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image16.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image17.png?raw=true)

Click Submit và kiểm tra email:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image18.png?raw=true)

Click vào link reset password: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image19.png?raw=true)

Để ý POST request forgot-password có các tham số bao gồm cả `username`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image20.png?raw=true)

Thử thay `username` thành `carlos` và send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image21.png?raw=true)

Có vẻ như đã thành công, thử đăng nhập `carlos:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image22.png?raw=true)

=> Có thể thay đổi tham số trong POST request trỏ đến tài khoản mình muốn bypass

## Lab: Username enumeration via subtly different responses
**1. Yêu cầu**

Phòng lab này có lỗ hổng tinh vi cho phép liệt kê tên người dùng và tấn công vét cạn mật khẩu. Có một tài khoản với tên người dùng và mật khẩu dễ đoán.

Để giải quyết bài lab, bạn cần liệt kê một tên người dùng hợp lệ, sau đó tấn công vét cạn mật khẩu của người dùng này và cuối cùng truy cập vào trang tài khoản của họ.

**2. Thực hiện**

Bài lab cung cấp sẵn 2 danh sách tài khoản và mật khẩu người dùng

Thử login vào 1 tài khoản bất kì: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image23.png?raw=true)

Request login trông như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image24.png?raw=true)

Thử brute-force username thông qua Bupe Intruder:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image25.png?raw=true)

Khác với bài lab trước, lần này thông báo trả về không rõ ràng là sai username hay password

Sử dụng setting `Grep-Extract` trong Burp Intruder, có thể bypass vấn đề này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image26.png?raw=true)

Bôi đen `Invalid username or password.` trong response > OK. Sắp xếp theo cột mới được thêm và để ý sẽ có một username trả về giá trị khác với các cột còn lại

Sau khi tìm được username là `adsl`, brute-force password như bình thường

=> Tài khoản tìm được `adsl:123qwe`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image27.png?raw=true)

## Lab: Username enumeration via response timing
**1. Yêu cầu**

Phòng lab này có lỗ hổng liệt kê tên người dùng dựa vào thời gian phản hồi của hệ thống. Để hoàn thành bài lab, bạn cần:

- Liệt kê một tên người dùng hợp lệ.
- Tấn công vét cạn mật khẩu của người dùng này.
- Cuối cùng, truy cập vào trang tài khoản của họ.

Thông tin đăng nhập của bạn: `wiener:peter`

**2. Thực hiện**

Thử brute-froce username, sử dụng danh sách username được lab cung cấp: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image28.png?raw=true)

Không brute-force được, website đã giới hạn số lần thử đăng nhập sai:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image29.png?raw=true)

Trong trường hợp này, có vẻ địa chỉ IP của người dùng đã bị chặn khi đăng nhập sai quá nhiều lần, như vậy có thể thử sử dụng `X-Forwarded-For` header để giả mạo địa chỉ IP. 

Thêm `X-Forwarded-For` header vào Login request và brute-force giá trị header này, chọn kiểu tấn công là Pitchfork attack, đồng thời đặt password thật dài để khoảng thời gian khác biệt giữa các request rõ ràng hơn:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image30.png?raw=true)

Cấu hình Payloads cho `X-Forwarded-For` header là các số từ 1-100 và Payloads cho username là danh sách được lab cung cấp. Bắt đầu tấn công:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image32.png?raw=true)

=> Username: al

Tiếp theo chuyển sang brute-force password:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image33.png?raw=true)

=> Password: 11111111

Thử đăng nhập với `al:11111111`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image34.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image35.png?raw=true)

## Lab: Broken brute-force protection, IP block
**1. Yêu cầu**
Phòng lab này có lỗ hổng do lỗi logic trong cơ chế bảo vệ chống tấn công vét cạn mật khẩu. Để giải quyết phòng lab, bạn cần tấn công vét cạn mật khẩu của nạn nhân, sau đó đăng nhập và truy cập trang tài khoản của họ.

Thông tin đăng nhập của bạn: `wiener:peter` 
Tên người dùng của nạn nhân: `carlos`

**2. Thực hiện**

Thử brute-force password của carlos:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image36.png?raw=true)

=> Website đã giới hạn số lần đăng nhập sai. Thử thêm `X-Forwarded-For` header như bài lab trước:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image37.png?raw=true)

=> Không có gì khác biệt. Không tìm được response trả về status `302`. Website có thể đã chặn cả `X-Forwarded-For` header

Tôi để ý rằng website sẽ chặn truy cập sau 3 lần login sai. Tôi đã thử tìm cách đặt lại số lần giới hạn này, đó là đăng nhập vào tài khoản đúng, trước khi đạt đến số lần giới hạn. 

Cấu hình Resource pool, bằng cách chỉ gửi một request tại một thời điểm, đảm bảo các lần thử đăng nhập của tôi được gửi đến máy chủ theo đúng thứ tự.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image38.png?raw=true)

Chỉnh sửa danh sách username và password thành như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image39.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image40.png?raw=true)

Bắt đầu tấn công:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image41.png?raw=true)

=> Tài khoản `carlos:ranger`

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image42.png?raw=true)

## Lab: Username enumeration via account lock
**1. Yêu cầu**

Phòng lab này có lỗ hổng liệt kê tên người dùng. Nó sử dụng tính năng khóa tài khoản nhưng tính năng này lại chứa lỗi logic. Để giải quyết bài lab, bạn cần:
- Liệt kê một tên người dùng hợp lệ.
- Tấn công vét cạn mật khẩu của người dùng này.
- Cuối cùng, truy cập trang tài khoản của họ.

**2. Thực hiện**

Login request trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image43.png?raw=true)

Thử brute-force username:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image44.png?raw=true)

=> Các paylaods đều trả về response có độ dài giống nhau, không có gì đặc biệt

Ý tưởng của tôi là tôi sẽ thử cấu hình mỗi payload username được lặp lại 5 lần, có thể nếu username hợp lệ, một resposne khác sẽ được phản hồi. Để làm như vậy, đặt vị trí brute-force trong Burp Intruder như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image45.png?raw=true)

Chọn attack type là Cluster bomb, vị trí đầu tiên lấy payload là danh sách username được lab cung cấp. Ở vị trí thứ hai, cấu hình payload như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image46.png?raw=true)

Bắt đầu tấn công:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image47.png?raw=true)

=> Tìm được username `ads` trả về response khác. 

Tiếp theo brute-force password của username này, cần sử dụng Grep-Extract:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image48.png?raw=true)

=> Tìm ra mật khẩu `mobilemail` không trả về thông báo lỗi

Thử login tài khoản `ads:mobilemail`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image49.png?raw=true)

## Lab: 2FA broken logic
**1. Yêu cầu**

Hệ thống xác thực hai yếu tố (2FA) của phòng thí nghiệm này dễ bị tấn công do lỗi logic. Để hoàn thành bài lab, bạn cần truy cập vào trang tài khoản của Carlos.

Thông tin đăng nhập của bạn: `wiener:peter`
Tên người dùng của nạn nhân: `carlos`

Bạn cũng có quyền truy cập vào máy chủ email để nhận mã xác minh 2FA.

**2. Thực hiện**

Khi đăng nhập vào tài khoản `wiener`, một mã xác thực được gửi về email:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image50.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image51.png?raw=true)

Request khi nhập mã xác thực trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image52.png?raw=true)

=> Có xác thực ở Cookie header với 1 giá trị là `verify=wiener`. Thử thay thành `verify=carlos` và brute-force mã xác thực (4 chữ số), cần xóa session để không lưu lại phiên làm việc của `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image53.png?raw=true)

Cấu hình payload: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image54.png?raw=true)

Bắt đầu tấn công:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image55.png?raw=true)

=> Mã xác thực: 0988. Thử gửi request nhập mã xác thực này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image56.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Authentication/images/image57.png?raw=true)




































