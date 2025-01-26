# Testing with automated tools

Burp bao gồm các công cụ tự động có thể giúp phát hiện server-side parameter pollution vulnerabilities.

*Burp Scanner* tự động phát hiện các biến đổi đầu vào đáng ngờ khi thực hiện kiểm tra. Điều này xảy ra khi một ứng dụng nhận đầu vào của người dùng, biến đổi nó theo một cách nào đó, sau đó thực hiện xử lý thêm kết quả. Hành vi này không nhất thiết tạo thành lỗ hổng, vì vậy sẽ cần phải thử nghiệm thêm bằng cách sử dụng các kỹ thuật thủ công được nêu ở trên. Để biết thêm thông tin, hãy xem định nghĩa vấn đề chuyển đổi đầu vào đáng ngờ.

Cũng có thể sử dụng *BApp Backslash Powered Scanner* để xác định server-side injection vulnerabilities. Máy quét phân loại đầu vào là boring, interesting, hoặc vulnerableg. Sẽ cần điều tra những thông tin đầu vào interesting bằng cách sử dụng các kỹ thuật thủ công đã nêu ở trên. Để biết thêm thông tin, hãy xem Báo cáo nghiên cứu chính thức về Backslash Powered Scanning: tìm kiếm các lớp lỗ hổng không xác định.
