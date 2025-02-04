# Bypassing Referer-based CSRF defenses

**1. Cơ chế phòng thủ CSRF dựa trên Referer header**: 

- **Referer Header** là một header HTTP cho biết **trang web nào đã gửi request đến server**. Ví dụ: nếu bạn click 1 link từ **facebook.com** đến **bank.com**, **Referer: facebook.com** sẽ được gửi kèm. 

- Một số website dùng header này để **chống CSRF** bằng cách kiểm tra:

    - Nếu *Referer* **không thuộc domain của họ** --> Từ chối request. 

**2. Tại sao cơ chế này dễ bị bypass?**

- Trình duyệt ẩn Referer vì lý do riêng tư (ví dụ: chuyển từ HTTPs --> HTTP).

- Hacker chủ động xóa/ chỉnh sửa Referer bằng các kỹ thuật đơn giản.

- **Meta Tag** trong HTML:
    
        <meta name="referrer" content="no-referrer">  

    --> Trang web này sẽ **không gửi Referer header** khi gửi Request.

