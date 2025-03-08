# How to prevent CORS-based attacks

Các lỗ hổng CORS chủ yếu phát sinh do lỗi cấu hình. Vì vậy, việc ngăn chặn chúng chủ yếu là vấn đề thiết lập cấu hình đúng cách. Các phần sau đây mô tả một số biện pháp phòng thủ hiệu quả chống lại các cuộc tấn công CORS.

# Proper configuration of cross-origin requests

Nếu một tài nguyên web chứa thông tin nhạy cảm, nguồn gốc (origin) cần được chỉ định chính xác trong tiêu đề phản hồi **Access-Control-Allow-Origin**.

# Only allow trusted sites

Điều này có vẻ hiển nhiên, nhưng các nguồn gốc (origins) được chỉ định trong tiêu đề **Access-Control-Allow-Origin** chỉ nên là các trang web đáng tin cậy. Đặc biệt, việc phản hồi động nguồn gốc từ các yêu cầu chéo nguồn mà không có cơ chế xác thực có thể dễ dàng bị khai thác và nên được tránh.

# Avoid whitelisting null

Tránh sử dụng tiêu đề **Access-Control-Allow-Origin: null**. Các yêu cầu tài nguyên chéo nguồn từ các tài liệu nội bộ và các yêu cầu trong môi trường sandbox có thể sử dụng nguồn gốc null. Tiêu đề CORS cần được xác định đúng theo các nguồn gốc đáng tin cậy đối với cả máy chủ riêng tư và công khai.

# Avoid wildcards in internal networks

Tránh sử dụng ký tự đại diện **(*)** trong mạng nội bộ. Chỉ dựa vào cấu hình mạng để bảo vệ tài nguyên nội bộ là không đủ, đặc biệt khi các trình duyệt nội bộ có thể truy cập các miền bên ngoài không đáng tin cậy.

# CORS is not a substitute for server-side security policies

CORS chỉ quy định cách trình duyệt hoạt động và không bao giờ có thể thay thế các biện pháp bảo vệ dữ liệu nhạy cảm phía máy chủ—kẻ tấn công vẫn có thể giả mạo yêu cầu trực tiếp từ bất kỳ nguồn gốc đáng tin cậy nào. Do đó, máy chủ web vẫn cần áp dụng các biện pháp bảo vệ đối với dữ liệu nhạy cảm, chẳng hạn như xác thực và quản lý phiên, bên cạnh việc cấu hình CORS đúng cách.






