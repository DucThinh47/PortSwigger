# Contents
- [What is SQL injection (SQLi)?](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#what-is-sql-injection-sqli)
- [What is the impact of a successful SQL injection attack?](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#what-is-the-impact-of-a-successful-sql-injection-attack)
- [How to detect SQL injection vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#how-to-detect-sql-injection-vulnerabilities)
    - [SQL injection in different parts of the query](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#sql-injection-in-different-parts-of-the-query)
    - [SQL injection in different contexts](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#sql-injection-in-different-contexts)
- [SQL injection examples](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#sql-injection-examples)
    - [Retrieving hidden data](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#retrieving-hidden-data)
    - [Subverting application logic](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#subverting-application-logic)
    - [Retrieving data from other database tables](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#retrieving-data-from-other-database-tables)
    - [Blind SQL injection vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#blind-sql-injection-vulnerabilities)
    - [Second-order SQL injection](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#second-order-sql-injection)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#labs)
    - [Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#lab-sql-injection-vulnerability-in-where-clause-allowing-retrieval-of-hidden-data)
    - [Lab: SQL injection vulnerability allowing login bypass](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#lab-sql-injection-vulnerability-allowing-login-bypass)
    - [Lab: SQL injection with filter bypass via XML encoding](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/README.md#lab-sql-injection-with-filter-bypass-via-xml-encoding)

## What is SQL injection (SQLi)?
SQL injection (SQLi) là một lỗ hổng bảo mật web cho phép kẻ tấn công can thiệp vào các truy vấn mà ứng dụng thực hiện đối với cơ sở dữ liệu. Điều này có thể cho phép kẻ tấn công xem dữ liệu mà họ không được phép truy xuất, bao gồm dữ liệu của người dùng khác hoặc bất kỳ dữ liệu nào mà ứng dụng có quyền truy cập. Trong nhiều trường hợp, kẻ tấn công có thể chỉnh sửa hoặc xóa dữ liệu, gây ra những thay đổi lâu dài đối với nội dung hoặc hành vi của ứng dụng.

Trong một số tình huống, kẻ tấn công có thể nâng cao cuộc tấn công SQL injection để xâm phạm máy chủ nền hoặc cơ sở hạ tầng phía sau. Ngoài ra, chúng có thể thực hiện các cuộc tấn công từ chối dịch vụ (DoS).

## What is the impact of a successful SQL injection attack?
Một cuộc tấn công SQL injection thành công có thể dẫn đến hậu quả nghiêm trọng trong bảo mật thông tin, bao gồm:
- `Truy cập trái phép vào dữ liệu nhạy cảm`: Kẻ tấn công có thể đánh cắp các thông tin quan trọng như mật khẩu, thông tin thẻ tín dụng và dữ liệu cá nhân của người dùng.
- `Thiệt hại về uy tín và tài chính`: Nhiều vụ rò rỉ dữ liệu nổi tiếng do SQL injection đã gây ra những tổn thất nặng nề về danh tiếng cho các tổ chức và dẫn đến các khoản tiền phạt lớn từ các cơ quan quản lý.
- `Tạo cửa hậu (backdoor) lâu dài`: Trong một số trường hợp, kẻ tấn công có thể cài đặt một cửa hậu trong hệ thống. Điều này cho phép chúng truy cập liên tục vào hệ thống mà không bị phát hiện trong một thời gian dài, gây ra rủi ro thỏa hiệp lâu dài.

## How to detect SQL injection vulnerabilities
Bạn có thể phát hiện SQL injection thủ công bằng cách thực hiện một bộ kiểm tra có hệ thống trên mọi điểm nhập dữ liệu trong ứng dụng. Để làm điều này, thường sẽ gửi các dữ liệu sau:

- Dấu nháy đơn `'` và kiểm tra xem có lỗi hoặc bất thường nào xuất hiện không.
- `Cú pháp SQL đặc biệt` để kiểm tra xem giá trị đầu vào có thể bị thay đổi hay không bằng cách so sánh phản hồi của ứng dụng với giá trị gốc và giá trị đã bị chỉnh sửa.
- Các điều kiện Boolean như `OR 1=1` và `OR 1=2`, sau đó quan sát sự khác biệt trong phản hồi của ứng dụng.
- Payloads gây trễ thời gian, được thiết kế để làm chậm phản hồi nếu truy vấn SQL bị thực thi, nhằm phát hiện sự khác biệt về thời gian phản hồi.
- Payloads OAST (Out-of-Band Application Security Testing), nhằm kích hoạt tương tác mạng ngoài băng tần khi thực thi trong truy vấn SQL, sau đó giám sát các tương tác này.

Ngoài ra, có thể nhanh chóng và hiệu quả phát hiện hầu hết các lỗ hổng SQL injection bằng cách sử dụng `Burp Scanner`.

### SQL injection in different parts of the query
Hầu hết các lỗ hổng SQL injection xảy ra trong mệnh đề `WHERE` của truy vấn `SELECT`. Đây là loại SQL injection phổ biến mà hầu hết những người kiểm thử có kinh nghiệm đều quen thuộc.

Tuy nhiên, lỗ hổng SQL injection có thể xuất hiện ở bất kỳ vị trí nào trong truy vấn và trong nhiều loại truy vấn khác nhau. Một số vị trí phổ biến khác mà SQL injection có thể xảy ra bao gồm:
- Trong câu lệnh `UPDATE`: Xuất hiện trong giá trị cập nhật hoặc mệnh đề `WHERE`
- Trong câu lệnh `INSERT`: Xuất hiện trong giá trị được chèn vào
- Trong câu lệnh `SELECT`: Xuất hiện trong tên bảng hoặc tên cột
- `Trong câu lệnh SELECT`: Xuất hiện trong mệnh đề `ORDER BY`

### SQL injection in different contexts

Ngoài việc sử dụng sử dụng query string để tiêm payload SQL độc hại, bạn có thể thực hiện các cuộc tấn công SQL injection bằng bất kỳ đầu vào nào có thể kiểm soát được mà ứng dụng xử lý như một truy vấn SQL. Ví dụ: một số trang web chấp nhận đầu vào ở định dạng `JSON` hoặc `XML` và sử dụng chúng để truy vấn CSDL.

Những định dạng khác nhau này có thể cung cấp cho bạn nhiều cách thức để che giấu các cuộc tấn công vốn thường bị chặn bởi các cơ chế phòng thủ như Tường lửa ứng dụng Web (WAF). Các biện pháp triển khai yếu thường chỉ tìm kiếm các từ khóa SQL injection phổ biến trong request, vì vậy bạn có thể vượt qua các bộ lọc này bằng cách mã hóa hoặc thoát các ký tự trong các từ khóa bị cấm. Ví dụ: đoạn SQL injection dựa trên `XML` dưới đây sử dụng `chuỗi thoát XML` để mã hóa ký tự `S`trong từ khóa `SELECT`:

    <stockCheck>
        <productId>123</productId>
        <storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>
    </stockCheck>

## SQL injection examples
Trong bối cảnh an toàn thông tin, tồn tại rất nhiều lỗ hổng, kỹ thuật tấn công và phương thức SQL injection xuất hiện trong các tình huống khác nhau. Một số ví dụ phổ biến về SQL injection bao gồm:
- `Khôi phục dữ liệu ẩn`: Kẻ tấn công có thể sửa đổi truy vấn SQL để trả về các kết quả bổ sung.
- `Phá vỡ logic ứng dụng`: Thay đổi truy vấn nhằm can thiệp vào logic hoạt động của ứng dụng.
- `Tấn công UNION`: Cho phép truy xuất dữ liệu từ các bảng cơ sở dữ liệu khác nhau.
- `SQL injection mù`: Kết quả truy vấn do kẻ tấn công kiểm soát không được hiển thị trong phản hồi của ứng dụng.

### Retrieving hidden data
Kịch bản như sau: một ứng dụng shopping hiển thị sản phẩm theo danh mục. Khi user click chọn danh mục `Gifts`, trình duyệt của họ sẽ gửi yêu cầu đến URL sau:

    https://insecure-website.com/products?category=Gifts

Điều này khiến ứng dụng thực hiện truy vấn SQL để lấy thông tin sản phẩm tương ứng từ cơ sở dữ liệu:

    SELECT * FROM products WHERE category = 'Gifts' AND released = 1

Truy vấn SQL này yêu cầu cơ sở dữ liệu trả về:
- Tất cả thông tin (`*`)
- Từ bảng `products`
- Với điều kiện `category = Gifts`
- Và sản phẩm đã được phát hành (`released = 1`)

Điều kiện `released = 1` được sử dụng để ẩn những sản phẩm chưa được phát hành. Có thể giả định rằng đối với các sản phẩm chưa phát hành, giá trị `released = 0`.

Ứng dụng trong kịch bản này không có bất kỳ cơ chế bảo vệ nào chống lại SQL injection. Điều này cho phép kẻ tấn công có thể tạo ra một truy vấn tấn công như sau:

    https://insecure-website.com/products?category=Gifts'--

Truy vấn SQL tương ứng sẽ trở thành:

    SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

Điểm quan trọng cần lưu ý: `--` là ký hiệu comment trong SQL, nghĩa là phần còn lại của truy vấn sẽ bị bỏ qua như một comment, về cơ bản là loại bỏ điều kiện `AND released = 1`. Do đó, tất cả sản phẩm, bao gồm cả những sản phẩm chưa phát hành, sẽ được hiển thị.

Ngoài ra, attacker có thể sử dụng một truy vấn khác để hiển thị tất cả sản phẩm, kể cả những danh mục mà họ không biết:

    https://insecure-website.com/products?category=Gifts'+OR+1=1--

Truy vấn SQL tương ứng sẽ trở thành:

    SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1

Truy vấn này sẽ trả về tất cả các sản phẩm vì điều kiện `1=1` luôn đúng, làm cho toàn bộ điều kiện WHERE luôn hợp lệ.

**Lưu ý**: Hãy cẩn trọng khi chèn điều kiện `OR 1=1` vào truy vấn SQL. Ngay cả khi điều này có vẻ vô hại trong bối cảnh đang thực hiện, nhiều ứng dụng có thể tái sử dụng dữ liệu từ một yêu cầu trong nhiều truy vấn khác nhau. Nếu điều kiện này lọt vào một câu lệnh `UPDATE` hoặc `DELETE`, nó có thể gây mất dữ liệu nghiêm trọng.

### Subverting application logic
Kịch bản: một ứng dụng cho phép user login bằng username và password. Khi user nhập `wiener:bluecheese`, ứng dụng sẽ kiểm tra thông tin đăng nhập bằng cách thực hiện truy vấn SQL sau:

    SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'

Nếu truy vấn trả về thông tin của một user, login sẽ thành công. Ngược lại, login request sẽ bị từ chối.

Trong trường hợp này, attacker có thể login vào bất kỳ account nào mà không cần biết password. Điều này có thể thực hiện bằng cách sử dụng chuỗi SQL comment `--` để loại bỏ điều kiện kiểm tra password trong mệnh đề `WHERE`.

Ví dụ, nếu kẻ tấn công nhập username: `administrator'--` và để trống password, truy vấn SQL sẽ trở thành:

    SELECT * FROM users WHERE username = 'administrator'--' AND password = ''

Vì dấu `--` biến phần còn lại của truy vấn thành comment, điều kiện kiểm tra password `AND password = ''` bị loại bỏ. Điều này khiến ứng dụng xác thực thành công với account có username = administrator, cho phép attakcer login trái phép vào tài khoản quản trị viên.

### Retrieving data from other database tables
Trong những trường hợp ứng dụng phản hồi bằng kết quả của một truy vấn SQL, attacker có thể lợi dụng lỗ hổng SQL injection để truy xuất dữ liệu từ các bảng khác trong CSDL. Chúng có thể sử dụng từ khóa `UNION` để thực thi thêm một truy vấn `SELECT` và nối kết quả vào truy vấn ban đầu.

Ví dụ, nếu một ứng dụng thực thi truy vấn sau chứa đầu vào của user là `Gifts`:

    SELECT name, description FROM products WHERE category = 'Gifts'

Attackers có thể gửi đầu vào:

    ' UNION SELECT username, password FROM users--

Điều này khiến ứng dụng trả về tất cả username và password cùng với tên và mô tả của các sản phẩm.

### Blind SQL injection vulnerabilities
Nhiều trường hợp SQL injection là các lỗ hổng mù. Điều này có nghĩa là ứng dụng không trả về kết quả của truy vấn SQL hoặc chi tiết về bất kỳ lỗi CSDL nào trong phản hồi của nó. Các lỗ hổng mù vẫn có thể bị khai thác để truy cập dữ liệu trái phép, nhưng các kỹ thuật liên quan thường phức tạp hơn và khó thực hiện hơn.

Các kỹ thuật sau đây có thể được sử dụng để khai thác các lỗ hổng `Blind SQL Injection`, tùy thuộc vào tính chất của lỗ hổng và CSDL liên quan:
- Bạn có thể thay đổi logic của truy vấn để kích hoạt một sự khác biệt có thể phát hiện được trong phản hồi của ứng dụng, phụ thuộc vào` tính đúng/sai` của một điều kiện duy nhất. Điều này có thể liên quan đến việc tiêm một điều kiện mới vào một số `logic Boolean`, hoặc kích hoạt một lỗi có điều kiện như lỗi chia cho số không (`divide-by-zero`).
- Bạn có thể kích hoạt một cách có điều kiện một độ trễ thời gian trong quá trình xử lý truy vấn. Điều này cho phép bạn suy luận tính đúng đắn của điều kiện dựa trên thời gian ứng dụng phản hồi.
- Bạn có thể kích hoạt một tương tác mạng ngoại vi (out-of-band), sử dụng các kỹ thuật `OAST` (Out-of-band Application Security Testing). Kỹ thuật này cực kỳ mạnh mẽ và hoạt động trong những tình huống mà các kỹ thuật khác không thể. Thông thường, bạn có thể trực tiếp trích xuất dữ liệu thông qua kênh ngoại vi này. Ví dụ: bạn có thể đặt dữ liệu vào một truy vấn `DNS lookup` cho một tên miền mà bạn kiểm soát.

### Second-order SQL injection
SQL injection bậc nhất (`First-order SQL injection`) xảy ra khi ứng dụng xử lý dữ liệu đầu vào từ một `HTTP request` và kết hợp dữ liệu này vào một truy vấn SQL một cách không an toàn.

SQL injection bậc hai (`Second-order SQL injection`) xảy ra khi ứng dụng nhận dữ liệu đầu vào từ một `HTTP request` và `lưu trữ nó` để sử dụng trong tương lai. Việc này thường được thực hiện bằng cách đưa dữ liệu vào CSDL, và không có lỗ hổng nào xảy ra tại thời điểm lưu trữ. Về sau, khi xử lý một `HTTP request` khác, ứng dụng truy xuất dữ liệu đã lưu trữ và kết hợp nó vào một truy vấn SQL một cách không an toàn. Vì lý do này, SQL injection bậc hai còn được gọi là `stored SQL injection`.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image208.png?raw=true)

SQL injection bậc hai thường xảy ra trong các tình huống mà nhà phát triển nhận thức được các lỗ hổng SQL injection, và do đó xử lý an toàn việc đưa dữ liệu ban đầu vào CSDL. Khi dữ liệu này được xử lý sau đó, nó được coi là an toàn vì đã được đưa vào CSDL một cách an toàn trước đó. Ở thời điểm này, dữ liệu được xử lý một cách không an toàn, do nhà phát triển sai lầm khi coi nó là đáng tin cậy.

## Labs
### Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image1.png?raw=true)

Ở phần Refine your search, click chọn Accessories: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image2.png?raw=true)

Intercept request này trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image3.png?raw=true)

Chèn điều kiện `'+OR+1=1--` vào đằng sau `category=Accessories`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image4.png?raw=true)

Số sản phẩm hiển thị khi chưa chèn: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image5.png?raw=true)

Sau khi chèn payload: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image6.png?raw=true)

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image7.png?raw=true)

### Lab: SQL injection vulnerability allowing login bypass

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image8.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image9.png?raw=true)

Click My account:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image10.png?raw=true)

Nhập username là `administrator'--` và password là `123` (tùy chọn), trước khi click Login, Intercept request này trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image11.png?raw=true)

Send request này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image12.png?raw=true)

Đăng nhập vào tài khoản `administrator`.

Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image13.png?raw=true)

### Lab: SQL injection with filter bypass via XML encoding
![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image197.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image198.png?raw=true)

Click xem 1 sản phẩm chi tiết:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image199.png?raw=true)

Click check stock. Kiểm tra request khi check stock: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image200.png?raw=true)

=> Chức năng kiểm tra hàng tồn kho gửi `productId` và `storeId` đến ứng dụng theo định dạng XML.

Send request, xem response:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image201.png?raw=true)

Thử thay đổi `storeId` để kiểm tra xem ứng dụng có đánh giá đầu vào không. Ví dụ, thay ID bằng một biểu thức toán học:

    <storeId>1+1</storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image202.png?raw=true)

=> Webiste thực hiện phép toán này, trả về kết quả cho một cửa hàng khác, chứng tỏ dữ liệu đầu vào bị đánh giá.

Tiếp tục kiểm tra bằng cách thử khai thác SQL Injection với `UNION SELECT`:

    <storeId>1 UNION SELECT NULL</storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image203.png?raw=true)

=> Yêu cầu bị chặn với thông báo đây là một cuộc tấn công tiềm năng, chứng tỏ hệ thống có WAF (Web Application Firewall).

Download extension `Hackvertor`. 

=> Vượt qua WAF

Do đang tiêm SQL vào XML, thử ẩn payload bằng XML entities.

Sử dụng `Hackvertor Extension` trong Burp Suite:
- Tô đậm payload
- Chuột phải > Extensions > Hackvertor > Encode > dec_entities/hex_entities
- Gửi lại yêu cầu.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image204.png?raw=true)

=> Nhận được phản hồi bình thường từ ứng dụng, vượt qua WAF thành công.

=> Tạo khai thác (Exploit)

Tiếp tục thử nghiệm, nhận thấy rằng truy vấn trả về một cột duy nhất.

Nếu cố gắng trả về nhiều hơn một cột, website sẽ phản hồi `0 units`, nghĩa là có lỗi xảy ra.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image205.png?raw=true)

Do chỉ có thể trả về một cột,nối (concatenate) dữ liệu username và password bằng ký tự `~`

    <storeId><@hex_entities>1 UNION SELECT username || '~' || password FROM users</@hex_entities></storeId>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image206.png?raw=true)

=> Tìm ra tài khoản administrator. 

Login và solved bài lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/SQL-injection/images/image207.png?raw=true)

### Lab: SQL injection vulnerability allowing login bypass


