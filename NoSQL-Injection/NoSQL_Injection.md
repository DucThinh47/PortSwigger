# NoSQL Injection

## Content

- [Types of NoSQL injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#types-of-nosql-injection)

- [NoSQL syntax injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#nosql-syntax-injection)

- [NoSQL operator injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#nosql-operator-injection)

- [Exploiting syntax injection to extract data](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#exploiting-syntax-injection-to-extract-data)

- [Exploiting NoSQL operator injection to extract data](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#exploiting-nosql-operator-injection-to-extract-data)

- [Timing based injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#timing-based-injection)

- [Preventing NoSQL injection](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/NoSQL_Injection.md#preventing-nosql-injection)

### Types of NoSQL injection

Có hai loại tấn công NoSQL injection:

- **Syntax injection** – Xảy ra khi kẻ tấn công có thể phá vỡ cú pháp truy vấn NoSQL, cho phép chèn payload tùy chỉnh. Phương pháp này tương tự với SQL injection, nhưng bản chất tấn công có sự khác biệt đáng kể do NoSQL sử dụng nhiều ngôn ngữ truy vấn, cú pháp truy vấn khác nhau và các cấu trúc dữ liệu đa dạng.

- **Operator injection** – Xảy ra khi kẻ tấn công có thể sử dụng các toán tử truy vấn của NoSQL để thao túng truy vấn theo ý muốn.

Trong chủ đề này, tìm hiểu cách kiểm tra lỗ hổng NoSQL nói chung, sau đó tập trung khai thác lỗ hổng trên MongoDB – cơ sở dữ liệu NoSQL phổ biến nhất. 

### NoSQL syntax injection

Có thể phát hiện lỗ hổng **NoSQL injection** bằng cách thử phá vỡ cú pháp truy vấn. Để làm điều này, hãy kiểm tra từng đầu vào một cách có hệ thống bằng cách gửi các **chuỗi fuzz** và các ký tự đặc biệt. Nếu những ký tự này không được lọc hoặc xử lý đúng cách bởi ứng dụng, chúng có thể kích hoạt lỗi cơ sở dữ liệu hoặc gây ra hành vi bất thường có thể phát hiện được.

Nếu biết **ngôn ngữ API** của cơ sở dữ liệu mục tiêu, sử dụng các ký tự đặc biệt và chuỗi fuzz phù hợp với ngôn ngữ đó. Nếu không, hãy thử nhiều chuỗi fuzz khác nhau để nhắm đến nhiều loại API khác nhau.

#### Detecting syntax injection in MongoDB

Kịch bản là một ứng dụng mua sắm hiển thị sản phẩm theo danh mục. Khi người dùng chọn **danh mục Fizzy drinks**, trình duyệt của họ sẽ gửi yêu cầu đến URL sau:

    https://insecure-website.com/product/lookup?category=fizzy

Điều này khiến ứng dụng gửi một **truy vấn JSON** để lấy dữ liệu từ bộ sưu tập (collection) sản phẩm trong cơ sở dữ liệu **MongoDB**:

    this.category == 'fizzy'

Để kiểm tra xem đầu vào có thể bị khai thác hay không, có thể thử gửi một **chuỗi fuzz** vào tham số **category**. Một chuỗi thử nghiệm dành cho **MongoDB** có thể là:

    '"`{
    ;$Foo}
    $Foo \xYZ

Sử dụng chuỗi này để xây dựng một cuộc tấn công:

    https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00

Nếu phản hồi của ứng dụng thay đổi so với ban đầu, điều đó có thể cho thấy dữ liệu đầu vào của người dùng không được lọc hoặc xử lý đúng cách.

**Lưu ý**:

Lỗ hổng **NoSQL injection** có thể xuất hiện trong nhiều ngữ cảnh khác nhau, vì vậy cần điều chỉnh **chuỗi fuzz** phù hợp. Nếu không, có thể chỉ gây ra lỗi xác thực mà không thực sự thực thi được truy vấn.

Trong kịch bản trên, payload được chèn qua URL nên đã được **mã hóa URL**. Trong một số ứng dụng khác, có thể cần chèn payload vào một **thuộc tính JSON**. Khi đó, payload sẽ có dạng:

    '\"`{\r;$Foo}\n$Foo \\xYZ\u0000

#### Determining which characters are processed

Để xác định các ký tự nào được ứng dụng hiểu là **một phần của cú pháp truy vấn**, có thể **chèn từng ký tự riêng lẻ** vào đầu vào. Ví dụ, có thể thử chèn ký tự **'**, khiến ứng dụng tạo truy vấn MongoDB sau:

    this.category == '''

Nếu phản hồi của ứng dụng thay đổi so với ban đầu, điều đó có thể cho thấy ký tự **'** đã phá vỡ cú pháp truy vấn và gây ra lỗi cú pháp. Có thể xác nhận điều này bằng cách gửi một chuỗi truy vấn hợp lệ, ví dụ bằng cách **escape** dấu nháy đơn:

    this.category == '\''

Nếu chuỗi này không gây ra lỗi cú pháp, điều đó có thể có nghĩa là ứng dụng dễ bị tấn công **NoSQL injection**.

#### Confirming conditional behavior

Sau khi phát hiện lỗ hổng, bước tiếp theo là kiểm tra xem có thể **tác động đến điều kiện boolean** bằng cú pháp **NoSQL** hay không.

Để kiểm tra điều này, gửi **hai yêu cầu**, một yêu cầu với **điều kiện sai** và một yêu cầu với **điều kiện đúng**. Ví dụ: có thể sử dụng các biểu thức điều kiện ***' && 0 && 'x*** và ***' && 1 && 'x*** như sau:

    https://insecure-website.com/product/lookup?category=fizzy'+%26%26+0+%26%26+'x

    https://insecure-website.com/product/lookup?category=fizzy'+%26%26+1+%26%26+'x

Nếu ứng dụng phản hồi khác nhau giữa hai yêu cầu này, điều đó cho thấy điều kiện sai ***(&& 0 &&)*** đã ảnh hưởng đến logic truy vấn, trong khi điều kiện đúng ***(&& 1 &&)*** không ảnh hưởng. Điều này chứng minh rằng việc chèn cú pháp này có tác động đến truy vấn phía máy chủ, nghĩa là ứng dụng có khả năng dễ bị **NoSQL injection**.

#### Overriding existing conditions

Bây giờ, khi đã xác định rằng có thể **tác động đến điều kiện boolean**, có thể thử **ghi đè các điều kiện hiện có** để khai thác lỗ hổng.

Ví dụ, có thể chèn một **điều kiện JavaScript luôn đúng**, như ***'||'1'=='1***:

    https://insecure-website.com/product/lookup?category=fizzy%27%7c%7c%27%31%27%3d%3d%27%31

Yêu cầu trên sẽ tạo ra truy vấn MongoDB sau:

    this.category == 'fizzy'||'1'=='1'

Vì điều kiện chèn vào ***('1'=='1')*** luôn đúng, truy vấn sẽ trả về tất cả các sản phẩm, ngay cả những sản phẩm thuộc danh mục ẩn hoặc không xác định.

**Lưu ý**:

Cẩn trọng khi chèn điều kiện luôn đúng vào truy vấn NoSQL.

- Trong một số tình huống, điều này có vẻ vô hại, nhưng nếu ứng dụng sử dụng dữ liệu từ một yêu cầu trong nhiều truy vấn khác nhau, nó có thể gây ra hậu quả nghiêm trọng.

- Nếu điều kiện này được sử dụng trong truy vấn **cập nhật** hoặc **xóa dữ liệu**, nó có thể gây mất toàn bộ dữ liệu.

#### Overriding existing conditions - Continued

Cũng có thể thêm ký tự null ***(\x00)*** vào sau giá trị của **category**. Trong một số trường hợp, MongoDB có thể **bỏ qua toàn bộ ký tự sau null**, điều này khiến các điều kiện khác trong truy vấn bị vô hiệu hóa.

Ví dụ, giả sử truy vấn MongoDB có thêm điều kiện hạn chế **chỉ hiển thị sản phẩm đã phát hành**:

    this.category == 'fizzy' && this.released == 1

Trong đó:

- ***this.released == 1*** => Chỉ hiển thị sản phẩm đã phát hành.
- Sản phẩm chưa phát hành có thể có giá trị ***this.released == 0***.

**Khai thác bằng ký tự null**

Một kẻ tấn công có thể chèn một ký tự null ***(%00)*** vào sau giá trị **category**:

    https://insecure-website.com/product/lookup?category=fizzy'%00

Nếu MongoDB **bỏ qua tất cả ký tự sau ký tự null**, truy vấn sẽ trở thành:

    this.category == 'fizzy'\u0000' && this.released == 1

Kết quả:

- Điều kiện ***this.released == 1*** bị vô hiệu hóa.
- Toàn bộ sản phẩm trong danh mục "fizzy" đều hiển thị, bao gồm cả những sản phẩm chưa phát hành mà đáng lẽ phải bị ẩn.

#### Lab: Detecting NoSQL injection

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image1.png?raw=true)

Chọn category Gifts và intercept request trong Burp Suite: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image2.png?raw=true)

Thử chèn ký tự **'** vào tham số **category**:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image3.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image4.png?raw=true)

=> website trả về lỗi cú pháp JavaScript, điều này có thể cho thấy đầu vào người dùng không được lọc hoặc kiểm tra đúng cách.

Thử chèn payload JS hợp lệ như ***Gifts'+'***: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image5.png?raw=true)

=> Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image6.png?raw=true)

=> Không còn lỗi, có thể website đang thực thi JavaScript do người dùng nhập vào, cho thấy lỗ hổng injection trên server-side.

Thử gửi payload để kiểm tra xem có thể ảnh hưởng đến logic truy vấn không, sử dụng điều kiện boolean sai ***Gifts' && 0 && 'x***

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image7.png?raw=true)

Send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image8.png?raw=true)

=> không có sản phẩm nào được trả về, như vậy điều kiện boolean sai đã ảnh hưởng đến truy vấn. 

Tiếp theo, thử nghiệm với điều kiện boolean luôn đúng: ***Gifts' && 1 && 'x***: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image9.png?raw=true)

Send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image10.png?raw=true)

=> website có trả về sản phẩm, có thể truy vấn đang bị thao túng thành công.

Tiếp theo, gửi payload ghi đè điều kiện truy vấn: ***Gifts'||1||'***:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image11.png?raw=true)

Send request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image12.png?raw=true)

=> Hiển thị thêm các sản phẩm chưa được phát hành. Solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image13.png?raw=true)

### NoSQL operator injection

Các cơ sở dữ liệu `NoSQL` thường sử dụng các `toán tử truy vấn` (query operators) để chỉ định các điều kiện mà dữ liệu phải đáp ứng để được đưa vào kết quả truy vấn. Một số ví dụ về `toán tử truy vấn` trong `MongoDB` bao gồm:

- `$where` - Khớp các tài liệu (documents) thỏa mãn một biểu thức JavaScript.

- `$ne` - Khớp tất cả các giá trị không bằng với một giá trị được chỉ định.

- `$in` - Khớp tất cả các giá trị được chỉ định trong một mảng.

- `$regex` - Lựa chọn các tài liệu có giá trị khớp với biểu thức chính quy (regular expression) được chỉ định.

Kẻ tấn công có thể lợi dụng lỗ hổng bằng cách `tiêm các toán tử truy vấn` (operator injection) để thao túng các truy vấn `NoSQL`. Để thực hiện điều này, cần gửi có hệ thống các toán tử khác nhau vào một loạt các đầu vào từ phía người dùng, sau đó quan sát các phản hồi để tìm kiếm thông báo lỗi hoặc các thay đổi khác.

#### Submitting query operators

Trong các thông điệp `JSON`, có thể chèn các `toán tử truy vấn` dưới dạng `các đối tượng lồng nhau` (nested objects). Ví dụ:

- Từ `{"username":"wiener"}`

- Thành `{"username":{"$ne":"invalid"}}`

Đối với các đầu vào dựa trên URL, có thể chèn các toán tử truy vấn thông qua các `tham số URL`. Ví dụ:

- Từ `username=wiener`

- Thành `username[$ne]=invalid`

Nếu cách trên không hoạt động, có thể thử các bước sau:

- Chuyển đổi phương thức yêu cầu từ `GET` sang `POST`.

- Thay đổi header `Content-Type` thành `application/json`.

- Thêm dữ liệu `JSON` vào phần thân (body) của thông điệp.

- Tiêm các toán tử truy vấn vào `JSON`.

**Lưu ý**:
Có thể sử dụng tiện ích mở rộng `Content Type Converter` để tự động chuyển đổi phương thức yêu cầu và thay đổi một yêu cầu `POST` được mã hóa URL thành định dạng `JSON`.

#### Detecting operator injection in MongoDB

Xem xét một ứng dụng dễ bị tấn công, chấp nhận username và password trong body của yêu cầu `POST`:

    {"username":"wiener","password":"peter"}

Kiểm tra từng đầu vào với nhiều toán tử khác nhau. Ví dụ, để kiểm tra xem đầu vào username có xử lý toán tử truy vấn hay không, có thể thử tiêm như sau:

    {"username":{"$ne":"invalid"},"password":"peter"}

Nếu toán tử `$ne` được áp dụng, truy vấn này sẽ trả về tất cả người dùng có tên không bằng `invalid`.

Nếu cả hai đầu vào username và password đều xử lý được toán tử, có thể bypass quá trình xác thực bằng cách sử dụng payload sau:

    {"username":{"$ne":"invalid"},"password":{"$ne":"invalid"}}

Truy vấn này trả về tất cả thông tin đăng nhập mà cả tên người dùng và mật khẩu đều không bằng `invalid`. Kết quả là có thể đăng nhập vào ứng dụng dưới tư cách người dùng đầu tiên trong tập hợp dữ liệu.

**Nhắm mục tiêu một tài khoản cụ thể**

Để nhắm đến một tài khoản cụ thể, có thể xây dựng payload bao gồm một username đã biết hoặc đã đoán được. Ví dụ:

    {"username":{"$in":["admin","administrator","superadmin"]},"password":{"$ne":""}}

Truy vấn này sẽ trả về tất cả các thông tin đăng nhập có username là `admin`, `administrator` hoặc `superadmin` và mật khẩu không rỗng.

#### Lab: Exploiting NoSQL operator injection to bypass authentication

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image14.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image15.png?raw=true)

Login vào tài khoản được cấp `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image16.png?raw=true)

`POST /login` request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image17.png?raw=true)

Thử injection với toán tử `$ne`. Thay đổi giá trị của tham số username từ `wiener` thành `{"$ne":""}`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image18.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image19.png?raw=true)

Có vẻ Login thành công. 

Thử injection với toán tử `$regex`, thay đổi giá trị username thành `{"$regex":"wien.*"}`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image20.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image21.png?raw=true)

Có vẻ vẫn có thể Login thành công. 

Tiếp theo thử injection đồng thời username và password, thay 2 giá trị này thành `{"$ne":""}`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image22.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image23.png?raw=true)

Thông báo lỗi cho thấy truy vấn trả về một số lượng bản ghi không mong muốn, cho thấy có nhiều người dùng được chọn.

Tiếp theo, thử login với quyền `Admin`, thay password thành `{"$ne":""}` và username thành `{"$regex":"admin.*"}`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image24.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image25.png?raw=true)

Login thành công dưới quyền `admin`. 

Trong tab Proxy, thay đổi payload trong POST /login request thành:

    {"username":{

    "$regex":"admin.*"

    },"password":{

    "$ne":""

    }}

Forward request và solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image26.png?raw=true)

### Exploiting syntax injection to extract data

Trong nhiều cơ sở dữ liệu `NoSQL`, một số toán tử truy vấn hoặc hàm có thể chạy mã JavaScript hạn chế, chẳng hạn như toán tử `$where` và hàm `mapReduce()` trong `MongoDB`. Điều này có nghĩa là, nếu một ứng dụng dễ bị tấn công sử dụng các toán tử hoặc hàm này, cơ sở dữ liệu có thể thực thi mã JavaScript như một phần của truy vấn. Do đó, có thể sử dụng các hàm JavaScript để trích xuất dữ liệu từ cơ sở dữ liệu.

#### Exfiltrating data in MongoDB

Xem xét một ứng dụng dễ bị tấn công cho phép người dùng tra cứu username đã đăng ký khác và hiển thị vai trò của họ. Điều này kích hoạt một yêu cầu đến URL:

    https://insecure-website.com/user/lookup?username=admin

Yêu cầu này dẫn đến truy vấn `NoSQL` sau đây của tập hợp người dùng:

    {"$where":"this.username == 'admin'"}

Vì truy vấn sử dụng toán tử `$where`, có thể cố gắng chèn các hàm JavaScript vào truy vấn này để trả về dữ liệu nhạy cảm. Ví dụ, có thể gửi payload sau:

    admin' && this.password[0] == 'a' || 'a'=='b

Payload này trả về `ký tự đầu tiên` của chuỗi mật khẩu của người dùng, cho phép trích xuất mật khẩu từng ký tự một.

Cũng có thể sử dụng hàm `match()` của JavaScript để trích xuất thông tin. Ví dụ, payload sau cho phép xác định xem mật khẩu có chứa chữ số hay không:

    admin' && this.password.match(/\d/) || 'a'=='b

#### Identifying field names

Vì `MongoDB` xử lý dữ liệu `bán cấu trúc` mà không yêu cầu một lược đồ cố định, có thể cần xác định các trường hợp hợp lệ trong tập hợp trước khi có thể trích xuất dữ liệu bằng cách chèn JavaScript.

Ví dụ, để xác định xem cơ sở dữ liệu `MongoDB` có chứa trường mật khẩu hay không, có thể gửi payload sau:

    https://insecure-website.com/user/lookup?username=admin'+%26%26+this.password!%3d'

Gửi lại payload này cho một trường `có tồn tại` và một trường `không tồn tại`. Trong ví dụ này, biết rằng trường username tồn tại, vì vậy có thể gửi các payload sau:

    admin' && this.username!='  
    admin' && this.foo!='

Nếu trường `password` tồn tại, phản hồi lý tưởng sẽ giống với phản hồi của trường tồn tại (`username`), nhưng khác với phản hồi của trường không tồn tại (`foo`).

#### Identifying field names - Continued

Nếu muốn kiểm tra các tên trường khác nhau, có thể thực hiện `tấn công từ điển` bằng cách sử dụng một `wordlist` để lặp qua các tên trường tiềm năng.

**Lưu ý**:

Ngoài ra, có thể sử dụng kỹ thuật `chèn toán tử NoSQL` để trích xuất tên trường từng ký tự một. Điều này cho phép xác định tên trường mà không cần phải đoán hoặc thực hiện tấn công từ điển. 


#### Lab: Exploiting NoSQL injection to extract data

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image27.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image28.png?raw=true)

Đăng nhập tài khoản `wiener:peter`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image29.png?raw=true)

Trong tab history proxy, tìm được request yêu cầu server tìm kiêm username `wiener`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image30.png?raw=true)

Send request tới Repeater và thử thêm ký tự ' vào giá trị tham số `user`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image31.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image32.png?raw=true)

=> Server thông báo lỗi `There was an error getting user details`, cho thấy đầu vào chưa được lọc hoặc xử lý đúng cách.

Thử chèn payload JavaScript hợp lệ `wiener'+'`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image33.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image34.png?raw=true)

=> Nhận được thông tin tài khoản wiener, có thể xảy ra lỗi Injection phía server.

Tiếp theo kiểm tra điều kiện Boolean, thử gửi một điều kiện sai trong tham số `user`: `wiener' && '1'=='2`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image35.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image36.png?raw=true)

=> Thông báo lỗi "Could not find user", như vậy điều kiện sai đã được xử lý. 

Tiếp theo thử gửi một điều kiện đúng `wiener' && '1'=='1`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image37.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image38.png?raw=true)

=> Nhận được chi tiết tài khoản `wiener`, chứng minh rằng có thể kiểm tra điều kiện Boolean để thay đổi phản hồi.

Tiếp theo cần xác định độ dài mật khẩu của tài khoản `administrator`, thử chèn điều kiện kiểm tra độ dài mật khẩu `administrator' && this.password.length < 30 || 'a'=='b`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image39.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image40.png?raw=true)

=> Trả về chi tiết tài khoản `administrator`, chứng minh rằng mật khẩu có độ dài nhỏ hơn 30 ký tự.

Tiếp theo giảm dần độ dài và tiếp tục gửi yêu cầu.

Khi sử dụng độ dài < 9, vẫn nhận được thông tin tài khoản:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image41.png?raw=true)

Khi sử dụng độ dài < 8, nhận được thông báo lỗi:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image42.png?raw=true)

=> Xác nhận mật khẩu có 8 ký tự.

Tiếp theo, tấn công brute-force để liệt kê mật khẩu. Send request tới Intruder, thiết lập như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image43.png?raw=true)

Payload position 1: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image44.png?raw=true)

Payload position 2: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image45.png?raw=true)

Start attack:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image46.png?raw=true)

Nếu Length của request = 151, nghĩa là kí tự tại vị trí tương ứng trong chuỗi mật khẩu không đúng:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image47.png?raw=true)

Nếu Length của request = 209, nghĩa là kí tự tại vị trí tương ứng trong chuỗi mật khẩu đúng:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image48.png?raw=true)

Sắp xếp request theo Length và tìm từng ký tự của chuỗi mật khẩu.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image49.png?raw=true)

=> Tìm được password của administrator: `obfbwpav`

Đăng nhập tài khoản và solved the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image50.png?raw=true)

### Exploiting NoSQL operator injection to extract data

Ngay cả khi truy vấn ban đầu không sử dụng bất kỳ toán tử nào cho phép chạy mã JavaScript tùy ý, vẫn có thể tự tiêm (inject) một trong những toán tử này.
Sau đó, có thể sử dụng các điều kiện `boolean` để xác định xem ứng dụng có thực thi bất kỳ mã JavaScript nào đã inject thông qua toán tử này hay không.

#### Injecting operators in MongoDB

Giả sử có một ứng dụng dễ bị tấn công chấp nhận `username` và `password` trong phần body của yêu cầu `POST` như sau:

    {"username":"wiener","password":"peter"}

Để kiểm tra xem có thể tiêm (inject) các toán tử hay không, có thể thử thêm toán tử `$where` làm tham số bổ sung, sau đó gửi hai yêu cầu: một yêu cầu với điều kiện đánh giá là `false`, và một yêu cầu với điều kiện đánh giá là `true`. Ví dụ:

- Điều kiện `false`: 

    {"username":"wiener","password":"peter", "$where":"0"}

- Điều kiện true:

    {"username":"wiener","password":"peter", "$where":"1"}

Nếu có sự khác biệt giữa các phản hồi, điều này có thể cho thấy rằng biểu thức JavaScript trong câu lệnh `$where` đang được thực thi.

#### Extracting field names

Nếu đã tiêm (inject) một toán tử cho phép chạy mã JavaScript, có thể sử dụng phương thức `keys()` để trích xuất tên các trường dữ liệu. Ví dụ, có thể gửi payload sau:

    "$where":"Object.keys(this)[0].match('^.{0}a.*')"

Payload này kiểm tra trường dữ liệu đầu tiên trong đối tượng `user` và trả về ký tự đầu tiên của tên trường. Điều này cho phép trích xuất tên trường từng ký tự một.

#### Exfiltrating data using operators

Ngoài ra, có thể trích xuất dữ liệu bằng cách sử dụng các toán tử không cho phép chạy JavaScript. Ví dụ, có thể sử dụng toán tử `$regex` để trích xuất dữ liệu từng ký tự một.

Giả sử có một ứng dụng dễ bị tấn công chấp nhận `username` và `password` trong phần body của yêu cầu `POST` như sau:

    {"username":"myuser","password":"mypass"}

Có thể bắt đầu bằng cách kiểm tra xem toán tử `$regex` có được xử lý hay không như sau:

    {"username":"admin","password":{"$regex":"^.*"}}

Nếu phản hồi của yêu cầu này khác với phản hồi khi gửi mật khẩu sai, điều đó cho thấy rằng ứng dụng có thể dễ bị tấn công. Có thể sử dụng toán tử `$regex` để trích xuất dữ liệu từng ký tự một. Ví dụ, payload sau kiểm tra xem mật khẩu có bắt đầu bằng ký tự `a` hay không:

    {"username":"admin","password":{"$regex":"^a*"}}

#### Lab: Exploiting NoSQL operator injection to extract unknown fields

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image51.png?raw=true)

Truy cập lab: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image52.png?raw=true)

Click My account, nhập `username` là `carlos` và `password` bất kỳ, website trả về thông báo "Invalid username or password":

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image53.png?raw=true)

Mở tab proxy history, chuyển request đăng nhập đến Repeater: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image54.png?raw=true)

Tiếp theo thử khai thác `NoSQL Injection` bằng toán tử `$ne`, thay đổi giá trị tham số `password` thành:

    "password": {"$ne": "invalid"}

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image55.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image56.png?raw=true)

=> Website phản hồi "Account locked", chứng minh rằng toán tử `$ne` đã được chấp nhận và ứng dụng dễ bị tấn công.

Tiếp theo, thử khai thác lỗ hổng `JavaScript Injection`. Thêm tham số `$where` vào yêu cầu JSON như sau:

    {
        "username": "carlos",
        "password": {"$ne": "invalid"},
        "$where": "0"
    }

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image57.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image58.png?raw=true)

=> Website phản hồi "Invalid username or password". Thử thay đổi `$where` thành 1 và send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image59.png?raw=true)

=> Website phản hồi "Account locked: please reset your password", điều này chứng minh rằng mã JavaScript được thực thi trong toán tử `$where`.

Tiếp theo thử lấy tên trường (Field Names), send request này sang Intruder và thay đổi $where thành:

    "$where":"Object.keys(this)[1].match('^.{}.*')"

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image60.png?raw=true)

Vị trí chèn payload thứ nhất là vị trí kí tự, vị trí chèn payload thứ hai là kí tự đó. 

Cấu hình payload options 1: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image61.png?raw=true)

Cấu hình payload options 2:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image62.png?raw=true)

Start attack: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image63.png?raw=true)

Quan sát có 2 kiểu phản hồi, với request có `Length = 3514` sẽ có phản hồi là "Account locked: please reset your password":

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image64.png?raw=true)

Còn request có `Length = 3500` sẽ có phản hồi là "Invalid username or password":

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image65.png?raw=true)

Sau khi tấn công xong, các ký tự tìm được ở payload 2 sẽ ghép lại thành `username`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image66.png?raw=true)

Tiếp theo thử xác định trường chứa Token đặt lại mật khẩu, thay đổi payload với các chỉ số field khác, có thể thay payload thành:

    "$where": "function(){ if(Object.keys(this)[3].match(/^a/) ) return 1; else 0; }"

Chọn payload position cần brute-force là "a".

[1] username
[2] password
[3] email
[4] pwResetTkn

Vị trí field name thứ 4 là hợp lý nhất.

Tiếp theo thử kiểm tra tên token trên endpoint reset password để đổi lại password của carlos. Request `/forgot-password`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image67.png?raw=true)

Thử thêm tham số `?foo=invalid`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image68.png?raw=true)

Send request, quan sát response:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image69.png?raw=true)

Response lại trang như ban đầu. Thay đổi tham số foo thành `pwResetTkn` (vừa tìm được) và send request, quan sát resposne:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image70.png?raw=true)

=> Thông báo lỗi "Invalid token", xác nhận `pwResetTkn` là tên token đúng. 

Tiếp theo, cần tìm giá trị token để reset lại mật khẩu của carlos, thay đổi payload trong Intruder thành: 

    "$where":"this.pwResetTkn.match('^.{§§}§§.*')"

Trước tiên, cần brute-force để xác định độ dài token, chọn payload position như sau: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image71.png?raw=true)

Xác nhận độ dài token là `16` (ký tự cuối không tính):

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image72.png?raw=true)

Tiến hành tấn công, tìm từng ký tự tương ứng từng vị trí. Tìm được giá trị token là `a3a1d407ba81c6cf`, thay vào request: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image73.png?raw=true)

Send request, quan sát response: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image74.png?raw=true)

=> Thành công. 

Chuột phải vào response > Request in browser > Original session để lấy url, truy cập url, đặt mật khẩu là `123`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image75.png?raw=true)

Đăng nhập vào tài khoản carlos với mật khẩu mới, solve the lab!

![img](https://github.com/DucThinh47/PortSwigger/blob/main/NoSQL-Injection/images/image76.png?raw=true)

### Timing based injection

Đôi khi việc kích hoạt lỗi cơ sở dữ liệu không gây ra sự khác biệt trong phản hồi của ứng dụng. Trong tình huống này, vẫn có thể phát hiện và khai thác lỗ hổng bằng cách sử dụng `tiêm mã JavaScript` để kích hoạt `sự trì hoãn có điều kiện` về thời gian.

**Để thực hiện NoSQL injection dựa trên thời gian**:

1. Tải trang nhiều lần để xác định `thời gian tải cơ sở` (baseline).
2. Chèn `payload dựa trên thời gian` vào đầu vào. Payload dựa trên thời gian sẽ gây ra sự trì hoãn có chủ ý trong phản hồi khi được thực thi. Ví dụ:

        {"$where": "sleep(5000)"}
    Payload này sẽ gây ra sự trì hoãn có chủ ý là `5000 ms` khi tiêm thành công.
3. Xác định xem phản hồi có tải chậm hơn hay không. Điều này cho thấy việc tiêm mã đã thành công.

**Ví dụ về payload dựa trên thời gian**:

Các payload dưới đây sẽ kích hoạt sự trì hoãn về thời gian nếu mật khẩu bắt đầu bằng chữ cái "a":

    admin'+function(x){var waitTill = new Date(new Date().getTime() + 5000);while((x.password[0]==="a") && waitTill > new Date()){};}(this)+'

hoặc

    admin'+function(x){if(x.password[0]==="a"){sleep(5000)};}(this)+'

Payload trên sử dụng `JavaScript` để tạo ra sự trì hoãn khi điều kiện được đáp ứng (mật khẩu bắt đầu bằng chữ "a"), giúp xác định lỗ hổng bảo mật bằng cách quan sát sự thay đổi về thời gian phản hồi.

### Preventing NoSQL injection

Cách phù hợp để ngăn chặn các cuộc tấn công `NoSQL injection` phụ thuộc vào công nghệ NoSQL cụ thể đang sử dụng. Do đó, khuyến nghị `đọc tài liệu bảo mật cho cơ sở dữ liệu NoSQL` đã chọn. Tuy nhiên, các hướng dẫn chung sau đây cũng sẽ giúp giảm thiểu rủi ro:

- `Làm sạch và xác thực đầu vào của người dùng`, sử dụng `danh sách cho phép` (allowlist) các ký tự được chấp nhận.

- `Chèn đầu vào của người dùng bằng cách sử dụng các truy vấn có tham số` (parameterized queries) thay vì nối trực tiếp đầu vào của người dùng vào truy vấn.

- `Để ngăn chặn tiêm toán tử` (operator injection), áp dụng danh sách cho phép các khóa (keys) được chấp nhận.










































