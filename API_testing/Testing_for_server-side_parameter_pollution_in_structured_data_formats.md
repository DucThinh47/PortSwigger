# Testing for server-side parameter pollution in structured data formats

Attacker có thể thao túng params để khai thác lỗ hổng trong quá trình xử lý các định dạng dữ liệu có cấu trúc khác của server, chẳng hạn như *JSON* hoặc *XML*. Để kiểm tra điều này, đưa dữ liệu có cấu trúc không mong muốn vào thông tin user input và xem server response như thế nào.

Xem xét một ứng dụng cho phép người dùng chỉnh sửa hồ sơ của họ, sau đó áp dụng các thay đổi của họ bằng 1 request tới server-side API. Khi chỉnh sửa tên của mình, trình duyệt sẽ đưa ra request sau:

![img](https://imgur.com/1nPaCaB.png)

Điều này dẫn đến server-side request sau:

![img](https://imgur.com/hqKAIP1.png)

Có thể thử thêm access_level param vào request như sau:

![img](https://imgur.com/wWfx0O4.png)

Nếu input data của user được thêm vào dữ liệu JSON phía máy chủ mà không được xác thực hoặc sàng lọc đầy đủ thì điều này sẽ dẫn đến server-side request sau:

![img](https://imgur.com/ta3cmkT.png)

Điều này có thể dẫn đến việc người dùng peter được cấp quyền truy cập quản trị viên.

# Testing for server-side parameter pollution in structured data formats - Continued

Xem xét một ví dụ tương tự, nhưng input data của user phía máy khách nằm trong dữ liệu JSON. Khi chỉnh sửa tên của mình, trình duyệt sẽ đưa ra request sau:

![img](https://imgur.com/574qrVx.png)

Điều này dẫn đến server-side request sau:

![img](https://imgur.com/ZhskdPg.png)

Có thể thử thêm *access_level param* vào request như sau:

![img](https://imgur.com/05ODSW1.png)

Nếu input data của user được giải mã, sau đó được thêm vào dữ liệu JSON phía máy chủ mà không được mã hóa đầy đủ, điều này sẽ dẫn đến server-side request sau:

![img](https://imgur.com/VVgAWhy.png)

Một lần nữa, điều này có thể dẫn đến việc người dùng peter được cấp quyền truy cập quản trị viên.

**Lưu ý**

Việc chèn định dạng có cấu trúc cũng có thể xảy ra trong responses. Ví dụ: điều này có thể xảy ra nếu thông tin đầu vào của người dùng được lưu trữ an toàn trong cơ sở dữ liệu, sau đó được nhúng vào phản hồi JSON từ API phụ trợ mà không được mã hóa đầy đủ. Thường có thể phát hiện và khai thác việc chèn định dạng có cấu trúc trong các response giống như cách có thể thực hiện trong các request.
