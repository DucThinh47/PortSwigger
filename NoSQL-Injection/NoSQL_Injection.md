# NoSQL Injection

## Content

- [Types of NoSQL injection]()

- [NoSQL syntax injection]()

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



















