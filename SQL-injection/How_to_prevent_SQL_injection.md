# How to prevent SQL injection

Hầu hết các lỗ hổng SQL Injection có thể được ngăn chặn bằng cách sử dụng truy vấn có tham số (Parameterized Queries) thay vì nối chuỗi trực tiếp trong truy vấn.
Các truy vấn có tham số này còn được gọi là "Prepared Statements".

=> Mã nguồn dễ bị tấn công SQL Injection:

Dưới đây là một đoạn mã dễ bị tấn công vì đầu vào của người dùng được nối trực tiếp vào truy vấn SQL:

    String query = "SELECT * FROM products WHERE category = '"+ input + "'";
    Statement statement = connection.createStatement();
    ResultSet resultSet = statement.executeQuery(query);

Mã nguồn an toàn, phòng chống SQL Injection:

Để ngăn chặn đầu vào của người dùng làm thay đổi cấu trúc truy vấn, có thể viết lại mã nguồn như sau:

    PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
    statement.setString(1, input);
    ResultSet resultSet = statement.executeQuery();

Với cách này, đầu vào của người dùng được xử lý như một tham số thay vì trực tiếp ghép vào chuỗi truy vấn, giúp ngăn chặn SQL Injection. 

# How to prevent SQL injection - Continued

Có thể sử dụng truy vấn có tham số (Parameterized Queries) cho bất kỳ trường hợp nào mà đầu vào không tin cậy xuất hiện dưới dạng dữ liệu trong truy vấn, bao gồm:

- Mệnh đề WHERE
- Giá trị trong câu lệnh INSERT hoặc UPDATE

Tuy nhiên, truy vấn có tham số không thể được sử dụng để xử lý đầu vào không tin cậy trong các phần khác của truy vấn, chẳng hạn như:

- Tên bảng hoặc tên cột
- Mệnh đề ORDER BY

Với những trường hợp này, cần áp dụng một phương pháp khác, ví dụ như:

-  Danh sách trắng (Whitelisting): Chỉ cho phép các giá trị đầu vào hợp lệ.
- Sử dụng logic khác để thực hiện hành vi mong muốn mà không cần đưa dữ liệu không tin cậy vào truy vấn SQL.

Để truy vấn có tham số thực sự hiệu quả trong việc ngăn chặn SQL Injection, chuỗi truy vấn SQL phải luôn là một hằng số được mã hóa cứng.

Tuyệt đối không sử dụng bất kỳ dữ liệu biến đổi nào từ bất kỳ nguồn nào để tạo chuỗi truy vấn SQL.

Không nên tự quyết định trường hợp nào là dữ liệu "đáng tin cậy" và tiếp tục sử dụng cách nối chuỗi (string concatenation) trong các tình huống cho là an toàn. Điều này dễ dẫn đến sai lầm hoặc bị khai thác do thay đổi trong mã nguồn khác.

