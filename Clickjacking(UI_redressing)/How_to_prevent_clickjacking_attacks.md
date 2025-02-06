# How to prevent clickjacking attacks

Chúng ta đã thảo luận về một cơ chế phòng chống phía trình duyệt thường gặp, cụ thể là **frame busting scripts**. Tuy nhiên, chúng tôi đã thấy rằng nó thường đơn giản đối với kẻ tấn công để phá vỡ các biện pháp bảo vệ này. Do đó, **server driven protocols** - các giao thức điều khiển máy chủ đã được sử dụng giúp hạn chế sử dụng trình duyệt iframe và giảm thiểu đối với clickjacking.

ClickJacking là một hành vi phía trình duyệt và thành công của nó hoặc phụ thuộc vào chức năng của trình duyệt và sự phù hợp với các tiêu chuẩn web hiện hành và thực tiễn tốt nhất. Server-side protection chống lại Clickjacking được cung cấp bằng cách xác định và truyền đạt các ràng buộc đối với việc sử dụng các thành phần như *iframe*. Tuy nhiên, việc thực hiện bảo vệ phụ thuộc vào việc tuân thủ trình duyệt và thực thi các ràng buộc này. Hai cơ chế để bảo vệ server-side clickjacking là X-Frame-Options và Content Security Policy.

