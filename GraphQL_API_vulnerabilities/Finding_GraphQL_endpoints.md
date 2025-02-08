# Finding GraphQL endpoints

Trước khi chúng ta có thể kiểm tra một **GraphQL API**, cần phải tìm điểm cuối (endpoint) của nó. Điều quan trọng cần biết là các **GraphQL APIs** sử dụng **cùng một điểm cuối** cho tất cả các request, vì vậy việc tìm điểm cuối là một thông tin rất quý giá.

**Lưu ý**

Phần này giải thích cách tìm **GraphQL endpoint** thủ công. Tuy nhiên, **Burp Scanner** có thể tự động kiểm tra **GraphQL endpoints** trong quá trình quét của nó. Nếu phát hiện bất kỳ GraphQL endpoint nào, nó sẽ đưa ra vấn đề "GraphQL endpoint found".

# Universal queries

Nếu chúng ta gửi truy vấn **query{__typename}** tới bất kỳ GraphQL endpoint nào, response sẽ bao gồm chuỗi **{"data": {"__typename": "query"}}**. Đây được gọi là **universal query** và nó là công cụ hữu ích để kiểm tra xem một URL có tương ứng với dịch vụ GraphQL hay không.

Truy vấn này hoạt động vì mọi GraphQL endpoints **đều có một trường dành riêng gọi là __typename**, trường này trả về loại đối tượng được truy vấn dưới dạng chuỗi.

# Common endpoint names

Các dịch vụ GraphQL thường sử dụng các **hậu tố endpoint tương tự nhau**. Khi kiểm tra GraphQL endpoints, chúng ta nên gửi các universal query tới các vị trí sau:

- /graphql

- /api

- /api/graphql

- /graphql/api

- /graphql/graphql

Việc gửi **universal queries** tới những endpoints này sẽ giúp xác định liệu các URL này có **tương ứng** với dịch vụ GraphQL hay không.

# Common endpoint names - Continued

Nếu các endpoint phổ biến này không trả về phản hồi GraphQL, chúng ta có thể thử thêm hậu tố **/v1** vào đường dẫn.

**Lưu ý**
Các dịch vụ GraphQL thường phản hồi bất kỳ request nào không phải GraphQL bằng lỗi "query not present" (truy vấn không có) hoặc lỗi tương tự.

# Request methods

Bước tiếp theo trong việc tìm kiếm GraphQL endpoints là kiểm tra bằng các **Request methods** khác nhau.

Đối với các GraphQL endpoint sản xuất, tốt nhất là chỉ chấp nhận **POST requests** có content-type là **application/json**, vì điều này giúp bảo vệ chống lại các lỗ hổng CSRF. Tuy nhiên, một số endpoints có thể chấp nhận methods khác, chẳng hạn như **GET requests** hoặc **POST requests** sử dụng content-type là **x-www-form-urlencoded**.

Nếu không thể tìm thấy GraphQL endpoints bằng cách gửi **POST requests** tới endpoints phổ biến, hãy thử gửi lại **universal queries** bằng HTTP methods thay thế.


# Inital testing - Kiểm tra ban đầu

Khi đã phát hiện ra điểm cuối (endpoint), chúng ta có thể gửi một số requests kiểm tra để hiểu rõ hơn về cách nó hoạt động. Nếu endpoint này đang hỗ trợ một trang web, hãy thử khám phá giao diện web trong trình duyệt của Burp và sử dụng **HTTP history** để kiểm tra các truy vấn được gửi.

Điều này giúp hiểu rõ hơn về cấu trúc của các truy vấn cũng như phản hồi từ endpoint, từ đó đưa ra các kiểm tra và điều chỉnh phù hợp.





