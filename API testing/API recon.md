# API recon

Để bắt đầu API testing, cần tìm hiểu càng nhiều thông tin về API càng tốt, để khám phá bề mặt tấn công của nó. 

Để bắt đầu, nên xác định API endpoints. Đây là những vị trí mà API nhận được yêu cầu về một tài nguyên cụ thể trên server của nó. Ví dụ, xem xét request sau: GET

![img](https://imgur.com/aITPXm8.png)

API endpoint cho request này là . Điều này dẫn đến sự tương tác với API để truy xuất books lists từ thư viện. Ví dụ, API endpoint khác có thể là , sẽ truy xuất mystery books list: 
![img](https://imgur.com/hKcc48O.png)

Khi đã xác định được endpoints, cần xác định cách tương tác với chúng. Điều này cho phép xây dựng HTTP request hợp lệ để kiểm tra API. Ví dụ, một số thông tin nên tìm hiểu: 
- Dữ liệu input mà API xử lý, bao gồm cả tham số bắt buộc và tùy chọn.
- Types of requests mà API chấp nhận, bao gồm HTTP methods và định dạng được hỗ trợ.
- Giới hạn tốc độ và cơ chế xác thực.
