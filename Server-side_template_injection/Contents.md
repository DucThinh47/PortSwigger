# Content
- [What is server-side template injection?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#what-is-server-side-template-injection-ssti)
- [What is the impact of server-side template injection?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#what-is-the-impact-of-server-side-template-injection)
- [How do server-side template injection vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#how-do-server-side-template-injection-vulnerabilities-arise)
- [Constructing a server-side template injection attack](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#constructing-a-server-side-template-injection-attack)
- [How to prevent server-side template injection vulnerabilities](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#how-to-prevent-server-side-template-injection-vulnerabilities)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#labs)
    - [Lab: Basic server-side template injection](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-basic-server-side-template-injection)
    - [Lab: Basic server-side template injection (code context)](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/Contents.md#lab-basic-server-side-template-injection-code-context)

# What is server-side template injection (SSTI)?
`Server-side template injection` xảy ra khi kẻ tấn công có thể sử dụng `cú pháp template gốc` để chèn một đoạn mã độc (payload) vào template, sau đó mã này sẽ được thực thi ở phía máy chủ.

Các công cụ template (template engines) được thiết kế để tạo ra các trang web bằng cách kết hợp các template cố định với dữ liệu thay đổi. Các cuộc tấn công `server-side template injection` có thể xảy ra khi `dữ liệu nhập từ người dùng được nối trực tiếp vào template`, thay vì được truyền vào dưới dạng dữ liệu riêng biệt. Điều này cho phép kẻ tấn công `chèn các chỉ thị template tùy ý` để thao túng công cụ template, thường là cho phép họ chiếm toàn quyền kiểm soát máy chủ. Đúng như tên gọi, các payload của `server-side template injection` được gửi và đánh giá ở phía máy chủ, điều này có khả năng làm cho chúng nguy hiểm hơn nhiều so với một cuộc tấn công client-side template injection thông thường.

# What is the impact of server-side template injection?
Các lỗ hổng `Server-side Template Injection` (SSTI) có thể khiến các trang web gặp phải nhiều cuộc tấn công khác nhau, tùy thuộc vào công cụ template được sử dụng và cách ứng dụng áp dụng nó. Trong một số trường hợp hiếm hoi, những lỗ hổng này không gây ra rủi ro bảo mật đáng kể. Tuy nhiên, trong hầu hết các trường hợp, tác động của `Server-side Template Injection` có thể gây ra hậu quả thảm khốc.

Ở mức độ nghiêm trọng, kẻ tấn công tiềm ẩn có thể thực hiện `RCE`, chiếm toàn quyền kiểm soát máy chủ phụ trợ và sử dụng nó để thực hiện các cuộc tấn công khác vào cơ sở hạ tầng nội bộ.

Ngay cả trong những trường hợp không thể thực thi mã từ xa hoàn toàn, kẻ tấn công thường vẫn có thể sử dụng `Server-side Template Injection` làm cơ sở cho nhiều cuộc tấn công khác, có khả năng đọc được dữ liệu nhạy cảm và các tệp tùy ý trên máy chủ.

# How do server-side template injection vulnerabilities arise?
Các lỗ hổng `SSTI` phát sinh khi `dữ liệu nhập từ người dùng được nối trực tiếp vào các template`, thay vì được truyền vào dưới dạng dữ liệu.

Các `template tĩnh`, chỉ đơn giản `cung cấp các vị trí giữ chỗ để hiển thị nội dung động`, thường không dễ bị tấn công SSTI. Ví dụ điển hình là một email chào hỏi người dùng bằng tên của họ, như đoạn trích sau từ một `template Twig`:

    $output = $twig->render("Dear {first_name},", array("first_name" => $user.first_name) );

Ví dụ này không dễ bị tấn công SSTI vì tên của người dùng chỉ đơn thuần được `truyền vào template dưới dạng dữ liệu`.

Tuy nhiên, vì template chỉ là các chuỗi (string), các nhà phát triển web đôi khi `nối trực tiếp dữ liệu nhập từ người dùng vào template trước khi hiển thị`. Hãy xem một ví dụ tương tự như trên, nhưng lần này, người dùng có thể `tùy chỉnh một phần của email` trước khi gửi đi. Ví dụ, họ có thể chọn tên được sử dụng:

    $output = $twig->render("Dear " . $_GET['name']);

Trong ví dụ này, thay vì một `giá trị tĩnh` được truyền vào template, một phần của bản thân template `đang được tạo động` bằng cách sử dụng `tham số GET` có tên `name`. Vì cú pháp template được đánh giá ở phía máy chủ, điều này có khả năng cho phép kẻ tấn công đặt một `payload SSTI` vào bên trong tham số name như sau:

    http://vulnerable-website.com/?name={{bad-stuff-here}}

Các lỗ hổng như thế này đôi khi xảy ra do vô tình, do thiết kế template kém của những người không quen thuộc với các tác động bảo mật. Giống như trong ví dụ trên, bạn có thể thấy các thành phần khác nhau, một số trong đó chứa dữ liệu nhập từ người dùng, được nối và nhúng vào một template. Theo một cách nào đó, điều này tương tự như các lỗ hổng `SQL injection` xảy ra trong các câu lệnh chuẩn bị (prepared statements) được viết kém.

Tuy nhiên, đôi khi hành vi này thực sự được triển khai có chủ ý. Ví dụ, một số trang web cố ý cho phép một số người dùng có đặc quyền nhất định, như biên tập viên nội dung, chỉnh sửa hoặc gửi các template tùy chỉnh theo thiết kế. Điều này rõ ràng gây ra rủi ro bảo mật rất lớn nếu kẻ tấn công có thể xâm nhập vào một tài khoản có những đặc quyền như vậy.

# Constructing a server-side template injection attack
Quy trình tổng thể để xác định các `lỗ hổng SSTI` và thực hiện một cuộc tấn công thành công thường bao gồm các bước cấp cao sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image0.png?raw=true)

**DETECT**

Các lỗ hổng `SSTI` thường không được chú ý, không phải vì chúng phức tạp mà vì chúng chỉ thực sự rõ ràng đối với những người kiểm toán viên đang tìm kiếm chúng một cách rõ ràng. Nếu bạn có thể phát hiện ra một lỗ hổng đang tồn tại, việc khai thác nó có thể dễ dàng đến bất ngờ. Điều này đặc biệt đúng trong các môi trường không có sandbox.

Như với bất kỳ lỗ hổng nào, bước đầu tiên để khai thác là `có thể tìm thấy nó`. Có lẽ cách tiếp cận ban đầu đơn giản nhất là thử `"fuzzing" template` bằng cách chèn một chuỗi các ký tự đặc biệt thường được sử dụng trong các biểu thức template, chẳng hạn như `${{<%[%'"}}%\`. Nếu một ngoại lệ được đưa ra, điều này cho thấy cú pháp template được chèn có khả năng đang được máy chủ diễn giải theo một cách nào đó. Đây là một dấu hiệu cho thấy có thể tồn tại một lỗ hổng `SSTI`.

Các lỗ hổng `SSTI` xảy ra trong hai ngữ cảnh riêng biệt, mỗi ngữ cảnh yêu cầu phương pháp phát hiện riêng. Bất kể kết quả của các nỗ lực fuzzing của bạn, điều quan trọng là phải thử các cách tiếp cận cụ thể theo ngữ cảnh sau đây. Nếu fuzzing không đưa ra kết luận, một lỗ hổng vẫn có thể bộc lộ bản thân bằng cách sử dụng một trong các cách tiếp cận này. Ngay cả khi fuzzing đã gợi ý một lỗ hổng injection template, bạn `vẫn cần xác định ngữ cảnh` của nó để khai thác:

***Plaintext context***

Hầu hết các ngôn ngữ template cho phép bạn `tự do nhập nội dung` bằng cách sử dụng trực tiếp các thẻ HTML hoặc bằng cách sử dụng cú pháp gốc của template, sẽ được render ra HTML ở phần backend trước khi phản hồi HTTP được gửi đi. Ví dụ, trong Freemarker, dòng `render('Hello ' + username)` sẽ được render thành một cái gì đó như `Hello Carlos`.

Điều này đôi khi có thể bị khai thác cho `XSS` và trên thực tế thường bị nhầm lẫn với một lỗ hổng `XSS `đơn giản. Tuy nhiên, bằng cách `đặt các phép toán học làm giá trị của tham số`, chúng ta có thể kiểm tra xem đây có phải là một điểm vào tiềm năng cho một cuộc tấn công `SSTI` hay không.

Ví dụ, hãy xem xét một template chứa đoạn mã dễ bị tấn công sau:

    render('Hello ' + username)

Trong quá trình kiểm toán, chúng ta có thể kiểm tra `SSTI` bằng cách yêu cầu một URL như:

    http://vulnerable-website.com/?username=${7*7}

Nếu kết quả đầu ra chứa `Hello 49`, điều này cho thấy phép toán học đang được đánh giá ở phía máy chủ. Đây là một bằng chứng khái niệm tốt cho lỗ hổng `SSTI`.

Lưu ý rằng cú pháp cụ thể cần thiết để đánh giá thành công phép toán học sẽ khác nhau tùy thuộc vào công cụ template đang được sử dụng. Chúng ta sẽ thảo luận chi tiết hơn về điều này trong bước Xác định (Identify).

***Code context***

Trong các trường hợp khác, lỗ hổng được bộc lộ do dữ liệu nhập từ người dùng được đặt trong `một biểu thức template`, như chúng ta đã thấy trước đó với ví dụ email của mình. Điều này có thể có dạng `một tên biến` do người dùng kiểm soát được `đặt bên trong một tham số`, chẳng hạn như:

    greeting = getQueryParameter('greeting')
    engine.render("Hello {{"+greeting+"}}", data)

Trên trang web, URL kết quả sẽ là một cái gì đó như:

    http://vulnerable-website.com/?greeting=data.username

Điều này sẽ được hiển thị trong đầu ra thành `Hello Carlos`, chẳng hạn.

Ngữ cảnh này dễ bị bỏ qua trong quá trình đánh giá vì nó không dẫn đến `XSS` rõ ràng và gần như không thể phân biệt được với một tra cứu hashmap đơn giản. Một phương pháp để kiểm tra `SSTI` trong ngữ cảnh này là trước tiên xác định rằng tham số không chứa lỗ hổng `XSS` trực tiếp bằng cách chèn HTML tùy ý vào giá trị:

    http://vulnerable-website.com/?greeting=data.username<tag>

Khi không có XSS, điều này thường sẽ dẫn đến một mục trống trong đầu ra (`chỉ Hello mà không có tên người dùng`), các thẻ được mã hóa, hoặc một thông báo lỗi. Bước tiếp theo là thử thoát khỏi câu lệnh bằng cách sử dụng cú pháp templating phổ biến và cố gắng chèn HTML tùy ý sau đó:

    http://vulnerable-website.com/?greeting=data.username}}<tag>

Nếu điều này lại dẫn đến `lỗi hoặc đầu ra trống`, bạn đã sử dụng cú pháp từ ngôn ngữ templating sai hoặc, nếu không có cú pháp kiểu template nào xuất hiện hợp lệ, thì `SSTI` là không thể. Ngoài ra, nếu đầu ra được hiển thị chính xác, cùng với HTML tùy ý, đây là một dấu hiệu chính cho thấy lỗ hổng `SSTI` đang tồn tại:

    Hello Carlos<tag>

**IDENTIFY**

Khi bạn đã phát hiện ra tiềm năng injection template, bước tiếp theo là `xác định công cụ template`.

Mặc dù có rất nhiều ngôn ngữ templating, nhiều trong số chúng sử dụng cú pháp rất giống nhau được chọn đặc biệt để không xung đột với các ký tự HTML. Do đó, việc tạo các payload dò tìm để kiểm tra công cụ template nào đang được sử dụng có thể tương đối đơn giản.

Chỉ cần `gửi cú pháp không hợp lệ` thường là đủ vì thông báo lỗi kết quả sẽ cho bạn biết chính xác `công cụ template là gì`, và đôi khi thậm chí cả phiên bản. Ví dụ, biểu thức không hợp lệ `<%=foobar%>` kích hoạt phản hồi sau từ `công cụ ERB` dựa trên `Ruby`:

    (erb):1:in `<main>': undefined local variable or method `foobar' for main:Object (NameError)
    from /usr/lib/ruby/2.5.0/erb.rb:876:in `eval'
    from /usr/lib/ruby/2.5.0/erb.rb:876:in `result'
    from -e:4:in `<main>'

Nếu không, bạn sẽ cần `kiểm tra thủ công` các payload dành riêng cho từng ngôn ngữ khác nhau và nghiên cứu cách chúng được công cụ template diễn giải. Sử dụng quy trình loại trừ dựa trên cú pháp nào dường như hợp lệ hoặc không hợp lệ, bạn có thể thu hẹp các lựa chọn nhanh hơn bạn nghĩ. Một cách phổ biến để làm điều này là `chèn các phép toán tùy ý` bằng cách `sử dụng cú pháp từ các công cụ template khác nhau`. Sau đó, bạn có thể quan sát xem chúng có được đánh giá thành công hay không. Để hỗ trợ quá trình này, bạn có thể sử dụng một cây quyết định tương tự như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image1.png?raw=true)

Bạn nên biết rằng `cùng một payload đôi khi có thể trả về phản hồi thành công trong nhiều hơn một ngôn ngữ template`. Ví dụ, payload `{{7*'7'}}` trả về `49` trong `Twig` và `7777777` trong `Jinja2`. Do đó, điều quan trọng là không nên vội vàng kết luận dựa trên một phản hồi thành công duy nhất.

**EXPLOIT**

Sau khi phát hiện ra rằng một lỗ hổng tiềm ẩn tồn tại và xác định thành công công cụ template, bạn có thể bắt đầu tìm cách khai thác nó.

# How to prevent server-side template injection vulnerabilities
Cách tốt nhất để ngăn chặn lỗ hổng `SSTI` là không cho phép bất kỳ người dùng nào sửa đổi hoặc gửi các template mới. Tuy nhiên, điều này đôi khi không thể tránh khỏi do các yêu cầu kinh doanh.

Một trong những cách đơn giản nhất để tránh đưa vào các lỗ hổng `SSTI` là luôn sử dụng một công cụ template "không logic" (logic-less), chẳng hạn như `Mustache`, trừ khi thực sự cần thiết. Việc tách logic khỏi phần trình bày càng nhiều càng tốt có thể giảm đáng kể khả năng bạn bị ảnh hưởng bởi các cuộc tấn công dựa trên template nguy hiểm nhất.

Một biện pháp khác là chỉ thực thi mã của người dùng trong một môi trường sandbox, nơi các module và hàm có khả năng nguy hiểm đã bị loại bỏ hoàn toàn. Đáng tiếc là việc sandboxing mã không đáng tin cậy vốn dĩ rất khó khăn và dễ bị qua mặt.

Cuối cùng, một cách tiếp cận bổ sung khác là chấp nhận rằng việc thực thi mã tùy ý là gần như không thể tránh khỏi và áp dụng sandboxing của riêng bạn bằng cách triển khai môi trường template của bạn trong một vùng chứa Docker bị khóa, chẳng hạn.

# Labs
## Lab: Basic server-side template injection
**1. Yêu cầu**

Lab này dễ bị tấn công bởi lỗ hổng Server-side Template Injection do cấu trúc không an toàn của một template ERB.

Để giải quyết lab, hãy xem lại tài liệu ERB để tìm cách thực thi mã tùy ý, sau đó xóa tệp `morale.txt` khỏi thư mục chính của `Carlos`.

**2. Thực hiện**

Khởi động bài lab:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image2.png?raw=true)

Thử click vào `View details` sản phẩm `Sprout More Brain Power`, thông báo hiển thị: `Unfortunately this product is out of stock`.

Request sẽ trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image4.png?raw=true)

Dựa vào thông tin bài lab, website này sử dụng template ERB, tài liệu về template: [ERB document](https://docs.ruby-lang.org/en/2.3.0/ERB.html). Tài liệu này nói rằng tempalte này được code bằng Ruby. Thử chèn 1 payload được tạo bằng ngôn ngữ này: `<%= 7 * 7 %>` vào tham số `message`: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image5.png?raw=true)

Send request này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image6.png?raw=true)

=> Thành công in ra kết quả của 7*7, website có thể dính lỗ hổng `SSTI`.

Theo yêu cầu bài lab, cần xóa tệp `morale.txt` khỏi thư mục chính của `Carlos`, tôi đã search chat gpt yêu cầu tạo payload bằng ngôn ngữ Ruby:

    <%= system('rm /home/carlos/morale.txt') %>

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image7.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image8.png?raw=true)

## Lab: Basic server-side template injection (code context)
**1. Yêu cầu**
Phòng lab này dễ bị tấn công lỗ hổng chèn template phía máy chủ (server-side template injection) do cách nó sử dụng template Tornado một cách không an toàn. Để giải quyết bài lab này, hãy xem lại tài liệu Tornado để khám phá cách thực thi mã tùy ý, sau đó xóa tệp `morale.txt` khỏi thư mục chính của `Carlos`.

Bạn có thể đăng nhập vào tài khoản của riêng mình bằng thông tin đăng nhập sau: `wiener:peter`

**2. Thực hiện**

Đăng nhập vào tài khoản `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image9.png?raw=true)

Tính năng `Preferred name` này để làm gì? 

Thử chọn 1 bài blog, tôi đã thử comment và thay đổi `Preferred name` giữa `First Name` và `Nickname` thì tên hiển thị ở phần comment trên blog sẽ hiển thị khác nhau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image10.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image11.png?raw=true)

=> Website có tính năng cho phép người dùng tác động tùy ý. 

Request khi thay đổi `Preferred name` trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image12.png?raw=true)

Theo yêu cầu bài lab, template sử dụng là [Tornado](https://www.tornadoweb.org/en/stable/template.html). Tôi sẽ thử thay thế giá trị tham số `blog-post-author-display` thành `{{7 * 7}}`, cần thêm `}}` để thoát khỏi biểu thức hiện tại:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image13.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image14.png?raw=true)

=> Website có thể bị tấn công `SSTI`. 

Payload để xóa thư mục `morale.txt`:

    {% import os %}
    {{os.system('rm /home/carlos/morale.txt')

Request sau khi thay đổi:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image15.png?raw=true)

Send request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Server-side_template_injection/images/image16.png?raw=true)
