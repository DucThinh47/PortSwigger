# Preventing server-side parameter pollution

Để tránh  server-side parameter pollution, sử dụng allowlist để xác định các ký tự không cần mã hóa và đảm bảo tất cả thông tin đầu vào khác của người dùng đều được mã hóa trước khi đưa vào yêu cầu phía máy chủ. Cũng nên đảm bảo rằng tất cả dữ liệu đầu vào tuân thủ định dạng và cấu trúc dự kiến.
