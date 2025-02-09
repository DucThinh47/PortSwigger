# Discovering schema information 

Bước tiếp theo trong việc kiểm tra API là tổng hợp thông tin về schema cơ bản.

Cách tốt nhất để làm điều này là sử dụng các **truy vấn introspection**. Introspection là một chức năng tích hợp sẵn trong GraphQL cho phép chúng ta truy vấn máy chủ để lấy thông tin về schema.

Introspection giúp chúng ta hiểu cách tương tác với GraphQL API. Nó cũng có thể tiết lộ dữ liệu nhạy cảm tiềm ẩn, chẳng hạn như các trường mô tả.

# Using introspection

Để sử dụng **introspection** để khám phá thông tin schema, chúng ta cần truy vấn trường **__schema**. Trường này có sẵn trên root type của tất cả các truy vấn.

Giống như các truy vấn thông thường, chúng ta có thể chỉ định các trường và cấu trúc của phản hồi mà chúng ta muốn trả về khi chạy truy vấn introspection. Ví dụ, ta có thể muốn phản hồi chỉ chứa tên của các mutation có sẵn.

**Lưu ý**
Burp có thể tạo các truy vấn introspection cho chúng ta.

# Probing for introspection - Thăm dò introspection

Theo thông lệ, introspection nên bị vô hiệu hóa trong các môi trường sản xuất, nhưng lời khuyên này không phải lúc nào cũng được tuân theo.

Chúng ta có thể **thăm dò introspection** bằng cách sử dụng truy vấn đơn giản sau đây. Nếu introspection được kích hoạt, phản hồi sẽ trả về tên của tất cả các truy vấn có sẵn.

        #Introspection probe request

        {
            "query": "{__schema{queryType{name}}}"
        }

**Lưu ý**
Burp Scanner có thể tự động kiểm tra introspection trong các lần quét của nó. Nếu nó phát hiện rằng introspection được kích hoạt, nó sẽ báo cáo vấn đề "GraphQL introspection enabled".


