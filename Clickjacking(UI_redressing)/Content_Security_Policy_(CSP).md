# Content Security Policy (CSP)

Content Security Policy (CSP) là một cơ chế phát hiện và phòng ngừa cung cấp giảm thiểu chống lại các cuộc tấn công như XSS và Clickjacking. CSP thường được triển khai trong máy chủ web dưới dạng return header của form:

    Content-Security-Policy: policy

trong đó *policy* là một chuỗi các chỉ thị chính sách được phân tách bằng dấu chấm phẩy. CSP cung cấp cho client browser thông tin về các nguồn tài nguyên web được phép mà trình duyệt có thể áp dụng cho việc phát hiện và đánh chặn các hành vi độc hại.

# Content Security Policy (CSP) - Continued

Bảo vệ Clickjacking được đề xuất là kết hợp Chỉ thị *frame-ancestors* trong Content Security Policy của web app. Chỉ thị *frame-ancestors 'none'* tương tự nhau về hành vi với chỉ thị *deny* của X-Frame-Options. Chỉ thị *frame-ancestors 'self'* là tương đương với Chỉ thị *sameorigin* của X-Frame-Options. Các khung danh sách trắng CSP sau đây chỉ với cùng một domain:

    Content-Security-Policy: frame-ancestors 'self';

Ngoài ra, framing có thể được giới hạn ở các trang web được đặt tên:

    Content-Security-Policy: frame-ancestors normal-website.com;

Để có hiệu quả chống lại Clickjacking và XSS, CSP cần phát triển, thực hiện và thử nghiệm cẩn thận và nên được sử dụng như một phần của chiến lược *multi-layer defense*.