# Identifying API endpoints

Cũng có thể thu thập nhiều thông tin bằng cách duyệt applications sử dụng API. Điều này nên làm, ngay cả khi đã có quyền access API documentation, vì đôi khi documentation có thể không chính xác hoặc đã quá cũ. 

Có thể sử dụng *Burp Scanner* để thu thập dữ liệu application, sau đó kiểm tra thủ công bề mặt tấn công bằng *Burp's browser*.

Trong khi duyệt application, tìm patterns mà đề xuất API endpoints trong cấu trúc URL, chẳng hạn như */api/*. Ngoài ra, chú ý đến các file JS. Tệp này có thể chứa tham chiếu đến API endpoints mà chưa kích hoạt trực tiếp qua web browser. *Burp Scanner* tự động trích xuất 1 số endpoints trong quá trình thu thập thông tin, nhưng để trích xuất kỹ hơn, sử dụng *JS Link Finder BApp*. Cũng có thể xem files JS 1 cách thủ công trong Burp.

# Interacting with API endpoints

Khi đã xác định được API endpoints, thử tương tác với chúng bằng cách sử dụng *Burp Repeater* và *Burp Intruder*. Điều này cho phép quan sát hành vi của API và khám phá thêm bề mặt tấn công. Ví dụ: có thể điều tra cách API respond khi thay đổi HTTP method và media type.

Khi đã tương tác với API endpoints, xem xét kỹ error messages và responses khác. Đôi khi những thông tin này có thể bao gồm thông tin có thể sử dụng để xây dựng HTTP request hợp lệ.

# Identifying supported HTTP methods

HTTP methods chỉ định hành động sẽ được thực hiện trên tài nguyên. Ví dụ: 

- GET - Lấy dữ liệu từ tài nguyên
- PATCH - Áp dụng các thay đổi một phần cho tài nguyên
- OPTIONS - Truy xuất thông tin về các loại request methods có thể được sử dụng, cho phép trên tài nguyên.

API endpoints có thể hỗ trợ HTTP methods khác nhau. Do đó, điều quan trọng là phải kiểm tra tất cả methods tiềm năng khi điều tra API endpoints. Điều này có thể giúp mở ra nhiều bề mặt tấn công hơn. 

Ví dụ, endpoint */api/tasks* có thể hỗ trợ methods: 

- GET /api/tasks - Truy xuất 1 list tasks
- POST /api/tasks - Tạo 1 task mới
- DELETE /api/tasks/1 - Xóa 1 task

Có thể sử dụng danh sách động từ HTTP tích hợp trong *Burp Intruder* để tự động chuyển đổi qua nhiều methods. 

**Chú ý**: 

Khi thử nghiệm HTTP methods khác nhau, nhắm vào low-priority objects. Điều này giúp đảm bảo tránh được hậu quả không lường trước được, ví dụ như thay đổi các mục quan trọng hoặc tạo quá nhiều hồ sơ.

# Identifying supported content types

API endpoints thường mong đợi data ở 1 format cụ thể. Do đó, chúng có thể hoạt động khác nhau, tùy thuộc vào *content type* của data được cung cấp trong Request. Việc thay đổi *content type* có thể cho phép: 

- Lỗi kích hoạt tiết lộ thông tin hữu ích.
- Bypass hàng phòng thủ thiếu sót.
- Tận dụng sự khác biệt trong logic xử lý. Ví dụ, API có thể an toàn khi xử lý JSON data nhưng dễ bị *injection attack* khi xử lý XML data.

Để thay đổi *content type*, chỉnh sửa *Content-Type header*, sau đó định dạng lại Request Body cho phù hợp. Có thể sử dụng *Content type converter BApp* để tự động chuyển đổi data được gửi trong Requests giữa XML và JSON.

# Lab: Finding and exploiting an unused API endpoint
![img](https://imgur.com/9ZftV22.png)

### Web page: 
![img](https://imgur.com/Sd3xVN9.png)

Thử login với account được cấp: 

![img](https://imgur.com/TgqOQrb.png)

Sau khi Login, click vào xem thử chi tiết 1 sản phẩm: 

![img](https://imgur.com/lLmNL0I.png)

Trong Burp Suite, Intercept Request này:

![img](https://imgur.com/i5vorON.png)

Forward Request: 

![img](https://imgur.com/8yimjnE.png)

Forward Request:

![img](https://imgur.com/8co1vPP.png)

--> Tìm được 1 API endpoint.

Send Request này tới Repeater:

![img](https://imgur.com/OSfZPpU.png)

Thử thay đổi GET method thành OPTIONS method để kiểm tra về các loại Request được cho phép sử dụng: 

![img](https://imgur.com/3ugiAlc.png)

--> 2 method được cho phép là GET và PATCH. Thử Send Request với GET method: 

![img](https://imgur.com/pbrVhd2.png)

--> Tìm được thông tin về price và message được lưu dưới dạng JSON.

Sử dụng PATCH method để áp dụng thay đổi một phần (ở đây là price): 

![img](https://imgur.com/xMJBqfN.png)

--> Response cho thấy price đã được chỉnh về 0.

Refresh lại page và Forward Request trong tab Proxy. Giá sản phẩm đã được chỉnh về 0: 

![img](https://imgur.com/LiDyitY.png)

Thêm sản phẩm vào giỏ và Place order:

![img](https://imgur.com/6M5XA2f.png)

Mua sản phẩm thành công!

![img](https://imgur.com/CKlfNCa.png)

# Using Intruder to find hidden endpoints

Khi đã xác định được 1 số API endpoints ban đầu, có thể sử dụng *Intruder* để khám phá hidden endpoints. Ví dụ: xem xét 1 tình huống trong đó đã xác định được API endpoint để cập nhật thông tin user: 
*PUT /api/user/update*

Để xác định *hidden endpoints*, có thể dùng *Burp Intruder* để tìm tài nguyên khác có cùng cấu trúc. Ví dụ: có thể thêm payload vào vị trí */update* của path cùng với list of other common fuctions, ví dụ như *delete* và *add*.

Khi tìm kiếm *hidden endpoints*, sử dụng *wordlist* dựa trên các quy ước đặt tên API phổ biến và thuật ngữ ngành. Đảm bảo cũng bao gồm các thuật ngữ có liên quan đến application, dựa trên sự điều tra ban đầu.






