# Types of NoSQL injection

Có hai loại tấn công NoSQL injection:

- **Syntax injection** – Xảy ra khi kẻ tấn công có thể phá vỡ cú pháp truy vấn NoSQL, cho phép chèn payload tùy chỉnh. Phương pháp này tương tự với SQL injection, nhưng bản chất tấn công có sự khác biệt đáng kể do NoSQL sử dụng nhiều ngôn ngữ truy vấn, cú pháp truy vấn khác nhau và các cấu trúc dữ liệu đa dạng.

- **Operator injection** – Xảy ra khi kẻ tấn công có thể sử dụng các toán tử truy vấn của NoSQL để thao túng truy vấn theo ý muốn.

Trong chủ đề này, tìm hiểu cách kiểm tra lỗ hổng NoSQL nói chung, sau đó tập trung khai thác lỗ hổng trên MongoDB – cơ sở dữ liệu NoSQL phổ biến nhất. 