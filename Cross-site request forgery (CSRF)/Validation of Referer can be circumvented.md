# Validation of Referer can be circumvented

Việc xác thực **Referer header** có thể bị bỏ qua nếu web app thực hiện kiểm tra một cách thiếu chặt chẽ. Dưới đây là giải thích chi tiết và ví dụ về cách tấn công này hoạt động:

**1. Trường hợp 1: Kiểm tra tiền tố (prefix) của domain**

**- Cách ứng dụng kiểm tra**: Web app chỉ xác nhận xem domain trong Referer header **bắt đầu bằng** một giá trị mong đợi (ví dụ: **vulnerable-website.com**).

**- Cách tấn công**: Kẻ tấn công có thể tạo một subdomain trên domain của họ, có tên bắt đầu bằng domain mục tiêu. Ví dụ:

*http://vulnerable-website.com.attacker-website.com/csrf-attack*

- Domain thực tế là *attacker-website.com*, nhưng subdomain *vulnerable-website.com* khiến ứng dụng nghĩ rằng Referer hợp lệ (vì nó bắt đầu bằng *vulnerable-website.com*).

**2. Trường hợp 2: Kiểm tra domain có xuất hiện ở bất kỳ đâu trong Referer**

**- Cách web app kiểm tra:** Web app chỉ xác nhận xem domain mục tiêu (ví dụ: *vulnerable-website.com*) **có tồn tại** trong *Referer header* không, bất kể vị trí.

**- Cách tấn công:** Kẻ tấn công đặt domain mục tiêu trong query string hoặc đường dẫn của URL độc hại. Ví dụ:

*http://attacker-website.com/csrf-attack?vulnerable-website.com*

- Referer header sẽ chứa *vulnerable-website.com* trong query string, khiến web app chấp nhận request.

**3. Vấn đề với trình duyệt hiện đại**

- **Cắt bỏ query string**: Nhiều trình duyệt (Chrome, Firefox...) tự động **xóa query string** khỏi Referer header để tránh rò rỉ thông tin nhạy cảm. Điều này có thể khiến kịch bản tấn công trên không hoạt động khi thử nghiệm trực tiếp trên trình duyệt.

- **Giải pháp cho kẻ tấn công**: Đặt header *Referrer-Policy: unsafe-url* trong response chứa mã khai thác. Header này buộc trình duyệt gửi toàn bộ URL (bao gồm cả query string) trong Referer header.

**Ví dụ thực tế:**

- **Bước 1**: Kẻ tấn công tạo trang web độc hại với URL:

    *http://attacker-website.com/csrf-attack?vulnerable-website.com*

- **Bước 2**: Trang web này chứa mã khai thác CSRF và thiết lập header:

    *Referrer-Policy: unsafe-url*

- **Bước 3**: Khi nạn nhân truy cập, trình duyệt gửi Referer đầy đủ:

    *Referer: http://attacker-website.com/csrf-attack?vulnerable-website.com*

- **Bước 4**: Ứng dụng kiểm tra thấy *vulnerable-website.com* trong Referer và cho phép request.

# Lab: CSRF with broken Referer validation
![img]()



