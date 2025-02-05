# What is clickjacking?

**1. Định nghĩa**

Clickjacking (hay "UI Redress Attack") là kiểu tấn công dựa trên giao diện, trong đó kẻ tấn công đánh lừa người dùng nhấp vào một nội dung ẩn thông qua một trang web giả mạo. Hành động này khiến người dùng vô tình kích hoạt chức năng nhạy cảm trên một website khác mà họ không hề hay biết.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/Clickjacking(UI_redressing)/images/image.png?raw=true)

**2. Ví dụ cụ thể**

- **Bước 1**: Người dùng truy cập vào một **website giả mạo** (ví dụ: qua liên kết trong email) và thấy một button "Nhận phần thưởng" hấp dẫn.

- **Bước 2**: Khi click vào button đó, họ thực chất đang click vào một **button ẩn** được chèn trong iframe (một khung trang web vô hình). Button ẩn này có thể là nút "Xác nhận thanh toán" trên trang ngân hàng hoặc mạng xã hội mà người dùng đã đăng nhập trước đó.

- **Kết quả**: Giao dịch chuyển tiền/đổi mật khẩu được thực hiện mà người dùng không hề biết.

**3. Cách thức hoạt động**:

- **Iframe vô hình**: Kẻ tấn công nhúng trang web mục tiêu (ví dụ: trang thanh toán) vào trang giả mạo thông qua iframe, sau đó sử dụng CSS để làm iframe trở nên trong suốt (**opacity: 0**) hoặc che giấu bằng cách chồng lớp (**z-index** cao).

- **Chồng khung hình**: Button giả mạo (ví dụ: "Nhận phần thưởng") được đặt chính xác trên cùng vị trí với Button ẩn trong iframe. Khi người dùng click vào nút giả, họ thực chất click vào nút ẩn.

- **Lợi dụng login session**: Nếu người dùng đã đăng nhập vào trang web mục tiêu (ví dụ: ngân hàng), hành động trong iframe sẽ được thực thi với quyền của họ.

**4. Khác biệt với CSRF**:

- **Clickjacking**: Đòi hỏi người dùng **thực hiện một thao tác** (click chuột) nhưng bị lừa về vị trí/nội dung họ tương tác.

- **CSRF**: Không cần người dùng thao tác. Kẻ tấn công giả mạo toàn bộ HTTP request (ví dụ: qua hình ảnh độc) để thực hiện hành động trái phép.

# What is clickjacking? - Continued

Để bảo vệ chống lại các cuộc tấn công CSRF thường bằng cách sử dụng **CSRF tokens**: số cụ thể theo session, số sử dụng một lần hoặc nonce. Các cuộc tấn công Clickjacking không được giảm thiểu bởi CSRF tokens vì session mục tiêu được thiết lập với nội dung được tải từ một trang web xác thực và với tất cả các request xảy ra trên domain. CSRF tokens được đặt vào requests và được chuyển đến server như một phần của session hoạt động thông thường. Sự khác biệt so với session người dùng bình thường là quá trình xảy ra trong một **iframe ẩn**.


