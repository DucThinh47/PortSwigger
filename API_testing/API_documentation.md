# API documentation

API thường được ghi lại như document để developers biết cách sử dụng và tích hợp chúng.

Document có thể có dạng human-readable hoặc machine-readable. Human-readable document được thiết kế để developers hiểu cách sử dụng API. Nó có thể bao gồm các giải thích chi tiết, ví dụ và kịch bản sử dụng. Machine-readable document được thiết kế để xử lý bằng phần mềm nhằm tự động hóa các tác vụ như tích hợp và xác thực API. Nó được viết ở định dạng như JSON hoặc XML.

API documents thường được cung cấp công khai, đặc biệt nếu APIs dành cho external developers. Hãy luôn bắt đầu quá trình điều tra bằng cách xem lại documents.

# Discovering API documentation

Ngay cả khi API documentation không có sẵn công khai, vẫn có thể truy cập tài liệu đó bằng cách duyệt qua applications sử dụng API đó.

Để thực hiện điều này, có thể sử dụng *Burp Scanner* để thu thập dữ liệu API. Cũng có thể duyệt applications theo cách thủ công bằng Burp's browser. Tìm kiếm endpoints có thể liên quan đến API documentation, ví dụ: 

- /api
- /swagger/index.html
- openapi.json

Nếu cần xác định endpoint cho 1 resource, cần đảm bảo điều tra *base path*. Ví dụ: nếu xác định resourse endpoint */api/swagger/v1/users/123* thì nên điều tra paths sau: 

- /api/swagger/v1
- /api/swagger
- /api

Cũng có thể sử dụng list path phổ biến để tìm documentation bằng *Intruder* trong Burp Suite.

# Lab: Exploiting an API endpoint using documentation
![img](https://imgur.com/DVGS4cv.png)

### Access the lab
![img](https://imgur.com/z38tbYb.png)

Thử truy cập path */api*:

![img](https://imgur.com/v0WdjjW.png)

Thử click vào DELETE: 

![img](https://imgur.com/tWRxfQ3.png)

Thử nhập *carlos*:

![img](https://imgur.com/D8eInch.png)

Click Send Request.

![img](https://imgur.com/zB0Hgdu.png)

Solved the lab!

![img](https://imgur.com/LW8gnA8.png)

Giải thích lệnh *curl -vgw "\n" -X DELETE 'https://0afe00c804407e9d839f472f0050008d.web-security-academy.net/api/user/carlos' -d '{}'*

Lệnh *curl* được dùng để thực hiện HTTP request từ dòng lệnh. 
- Tùy chọn -v: verbose (chế độ chi tiết) - hiển thị thông tin chi tiết của request và response
- Tùy chọn -g: Tắt xử lý các ký tự đặc biệt trong URL (như [] của curl)
- Tùy chọn -w "\n": Thêm xuống dòng cuối output, đọc kết quả dễ hơn
- -X DELETE: Xác định HTTP method
- URL: url 
- -d {}: data - gửi 1 payload rỗng trong phần body của request.

Tóm lại: Lệnh này thực hiện một yêu cầu HTTP DELETE đến URL https://0afe00c804407e9d839f472f0050008d.web-security-academy.net/api/user/carlos, gửi một payload rỗng ({}) trong body, và hiển thị thông tin chi tiết của yêu cầu và phản hồi. Dòng xuống dòng (\n) được thêm vào cuối để làm gọn đầu ra.

# Using machine-readable documentation

Có thể sử dụng nhiều công cụ tự động để phân tích mọi tài liệu API mà máy có thể đọc đượ tìm thấy.

Có thể sử dụng Burp Scanner để thu thập thông tin và kiểm tra tài liệu OpenAPI hoặc bất kỳ tài liệu nào khác ở định dạng JSON hoặc YAML. Cũng có thể phân tích tài liệu OpenAPI bằng BApp OpenAPI Parser.

Cũng có thể sử dụng một công cụ chuyên dụng để kiểm tra endpoints được ghi lại, chẳng hạn như Postman hoặc SoapUI.

