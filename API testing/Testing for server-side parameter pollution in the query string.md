# Testing for server-side parameter pollution in the query string

Để kiểm tra *server-side parameter pollution* trong *query string*,  đặt các ký tự cú pháp truy vấn như #, & và = vào input và quan sát cách ứng dụng phản hồi.

Xem xét một ứng dụng dễ bị tấn công cho phép tìm kiếm những người dùng khác dựa trên tên người dùng của họ. Khi tìm kiếm một người dùng, trình duyệt sẽ đưa ra request sau:

![img](https://imgur.com/DxXx5jV.png)

Để truy xuất thông tin người dùng, server truy vấn API nội bộ với request sau:

![img](https://imgur.com/BIndCE6.png)

# Truncating query strings (cắt bớt chuỗi truy vấn)

Có thể sử dụng ký tự # được mã hóa URL để cố gắng cắt bớt server-side request. Để giúp diễn giải response, cũng có thể thêm một chuỗi sau ký tự #.

Ví dụ, có thể sửa đổi chuỗi truy vấn thành như sau:

![img](https://imgur.com/5C9ky7U.png)

Như vậy, phía front-end sẽ cố gắng truy cập URL sau:

![img](https://imgur.com/icuXcmz.png)

**Lưu ý**

Việc cần thiết là phải mã hóa URL ký tự #. Nếu không, Front-end application sẽ hiểu nó là mã định danh phân đoạn và nó sẽ không được chuyển tới API nội bộ.

Xem lại response để tìm manh mối về việc liệu truy vấn có bị cắt ngắn hay không. Ví dụ: nếu response trả về *user peter* thì server-side query có thể đã bị cắt bớt. Nếu thông báo lỗi *Invalid name* được trả về thì ứng dụng có thể đã coi *foo* như một phần của tên người dùng. Điều này cho thấy rằng *server-side request* có thể chưa bị cắt bớt.

Nếu có thể cắt ngắn server-side request, điều này sẽ loại bỏ việc request set trường *publicProfile* thành true.Có thể khai thác điều này để trả về hồ sơ người dùng không công khai.

# Injecting invalid parameters

Có thể sử dụng ký tự & được mã hóa URL để cố gắng thêm param thứ hai vào server-side request.

Ví dụ: Có thể sửa đổi chuỗi truy vấn thành như sau:

![img](https://imgur.com/oJS32JO.png)

Điều này dẫn đến request phía máy chủ sau tới API nội bộ:

![img](https://imgur.com/IXvWTdy.png)

Xem lại response để tìm manh mối về cách phân tích cú pháp param bổ sung. Ví dụ: nếu response không thay đổi, điều này có thể cho biết rằng param đã được đưa vào thành công nhưng bị ứng dụng bỏ qua.

Để xây dựng một bức tranh hoàn chỉnh hơn, cần phải kiểm tra thêm.

# Injecting valid parameters

Nếu có thể sửa đổi chuỗi truy vấn, có thể thử thêm param hợp lệ thứ hai vào server-side request.

Ví dụ: nếu đã xác định được *email* param, có thể thêm param đó vào chuỗi truy vấn như sau:

![img](https://imgur.com/ScfZVgP.png)

Điều này dẫn đến yêu cầu phía máy chủ sau tới API nội bộ:

![img](https://imgur.com/XBvuc2r.png)

Từ đó, xem lại response để tìm manh mối về cách phân tích cú pháp tham số bổ sung.

# Overriding existing parameters

Để xác nhận xem ứng dụng có dễ bị tấn công *server-side parameter pollution* hay không, có thể thử ghi đè tham số ban đầu. Thực hiện việc này bằng cách thêm tham số thứ hai có cùng tên.

Ví dụ: có thể sửa đổi chuỗi truy vấn thành như sau:

![img](https://imgur.com/xtxEtML.png)

Điều này dẫn đến yêu cầu phía máy chủ sau tới API nội bộ:

![img](https://imgur.com/fvybcIj.png)

API nội bộ diễn giải hai *name* param. Tác động của điều này phụ thuộc vào cách ứng dụng xử lý tham số thứ hai. Điều này thay đổi tùy theo công nghệ web khác nhau. Ví dụ:

- PHP chỉ phân tích tham số cuối cùng. Điều này sẽ dẫn đến việc người dùng tìm kiếm carlos.
- ASP.NET kết hợp cả hai tham số. Điều này sẽ dẫn đến việc người dùng tìm kiếm peter,carlos, điều này có thể dẫn đến thông báo lỗi *Invalid username*.
- Node.js/express chỉ phân tích tham số đầu tiên. Điều này sẽ dẫn đến việc người dùng tìm kiếm peter và đưa ra kết quả không thay đổi.

Nếu có thể ghi đè tham số ban đầu, có thể tiến hành khai thác. Ví dụ: có thể thêm *name=administrator* vào yêu cầu. Điều này có thể cho phép đăng nhập với tư cách người dùng quản trị viên.

# Lab: Exploiting server-side parameter pollution in a query string
![img](https://imgur.com/2NhbAnd.png)

### Web page:
![img](https://imgur.com/kAKRO2s.png)

Click vào My account:
![img](https://imgur.com/I8GX1OC.png)

Click vào Forgot password?

![img](https://imgur.com/P7sLkiN.png)

Nhập administrator và trước khi click Submit, Intercept bằng Burp Suite: 

![img](https://imgur.com/uVXHVYZ.png)

Send request này tới Repeater: 

![img](https://imgur.com/CYJOTwa.png)

--> Tìm được response dưới dạng JSON.

Thử thêm # vào sau administrator để cố gắng Truncating chuỗi truy vấn, ký tự này được mã hóa thành %23: 

![img](https://imgur.com/am3U8Hk.png)

--> Nhận được response báo rằng *Field not specified*.

Có thể đây là thông báo rằng request thiếu *param field*, do kí tự # đã thành công cắt bới query string. (phía sau username field).

Thử sửa username thành *username=administrator%26field=1%23*:

![img](https://imgur.com/AFSxo33.png)

--> Response báo rằng *Invalid field*.

Thay đổi field=abc:

![img](https://imgur.com/0xwjYjH.png)

--> vẫn không được, cho thấy rằng server-side nhận ra field param bị chèn. 

Thử brute-force giá trị *field param*, Send request này tới *Intruder*:

![img](https://imgur.com/jt58LPs.png)

Chọn giá trị field làm giá trị để brute-force. 

Phần payloads, chọn wordlist để config: 

(Truy cập *https://github.com/whiteknight7/wordlist/blob/main/fuzz-lfi-params-list.txt* để lấy wordlist)

![img](https://imgur.com/QALLVVH.png)

![img](https://imgur.com/M7OIwfS.png)

--> Start attack sau khi chọn xong wordlist.

![img](https://imgur.com/ZOG2ETo.png)

--> Payload *email* có status code = 200. Thử thay *field=email* vào Request:

![img](https://imgur.com/J8y6Nyw.png)

--> Response như ban đầu, nghĩa là *email* là field type hợp lệ.

Trong quá trình xem source page, tìm ra file *forgotPassword.js*, phát hiện */forgot-password?reset_token=${resetToken}*

![img](https://imgur.com/moFqX6t.png)

--> /forgot-password endpoint có 1 param ẩn là *reset-token*.

Thử thay đổi field thành *reset_token* và Send: 

![img](https://imgur.com/vIeqscE.png)

--> Tìm được reset_token là *n2xab6z8aaq17ovu86751r6bp90cpdph*. Thêm vào URL: */forgot-password?reset_token=n2xab6z8aaq17ovu86751r6bp90cpdph*:

![img](https://imgur.com/Fkz3tD8.png)

Nhập password mới và login:

![img](https://imgur.com/l6DxjUF.png)

![img](https://imgur.com/y5anRqy.png)

Solved the lab!

![img](https://imgur.com/wxeGMim.png)
