# What is a large language model?

Mô hình ngôn ngữ lớn - *Large Language Models* (LLM) là các thuật toán AI có thể xử lý thông tin user inputs và tạo ra response hợp lý bằng cách dự đoán chuỗi từ. Được đào tạo trên các tập dữ liệu bán công khổng lồ, sử dụng máy học để phân tích xem các bộ phận cấu thành của ngôn ngữ khớp với nhau như thế nào.

LLMs thường hiển thị *chat interface* để chấp nhận thông tin user input, được gọi là *prompt*. Input được phép được kiểm soát một phần bởi các quy tắc xác thực đầu vào.

LLMs có thể có nhiều trường hợp sử dụng trong *modern websites*:

- Dịch vụ khách hàng, chẳng hạn như một trợ lý ảo.
- Bản dịch - Translation.
- Cải thiện SEO.
- Phân tích nội dung do người dùng tạo, chẳng hạn như để theo dõi "giọng điệu" của các nhận xét trên trang.

# LLM attacks and prompt injection

Nhiều cuộc tấn công LLM trên web dựa vào một kỹ thuật được gọi là *prompt injection*. Đây là nơi attacker sử dụng các lời nhắc thủ công - *crafted prompts* để thao túng đầu ra của LLM. Việc chèn kịp thời có thể khiến AI thực hiện các hành động nằm ngoài mục đích đã định, chẳng hạn như thực hiện lệnh gọi không chính xác tới các API nhạy cảm hoặc trả về nội dung không tuân theo nguyên tắc của nó.

# Detecting LLM vulnerabilities

Phương pháp được đề xuất để phát hiện lỗ hổng LLM là:

1. Xác định LLM's input, bao gồm cả đầu vào trực tiếp (chẳng hạn như prompt) và gián tiếp (chẳng hạn như training data).
2. Tìm ra dữ liệu và APIs nào mà LLM có quyền truy cập.
3. Thăm dò bề mặt tấn công mới này để tìm lỗ hổng.




