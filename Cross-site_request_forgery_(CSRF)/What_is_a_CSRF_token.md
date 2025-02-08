# What is a CSRF token ?

CSRF token là một giá trị unique, bí mật và không thể đoán trước được tạo bởi server-side application và được chia sẻ với client. Khi đưa ra request thực hiện một hành động nhạy cảm, chẳng hạn như gửi form, client phải bao gồm CSRF token chính xác. Nếu không, máy chủ sẽ từ chối thực hiện hành động được yêu cầu.

Một cách phổ biến để chia sẻ CSRF token với client là bao gồm chúng như một hidden param trong một HTML form, ví dụ:

![img](https://imgur.com/8WJnRsR.png)

# What is a CSRF token? - Continued

Submitting form sẽ có dạng request như sau: 

![img](https://imgur.com/crb0OH2.png)

Khi được thực hiện chính xác, CSRF token giúp bảo vệ chống lại các cuộc tấn công CSRF bằng cách gây khó khăn cho kẻ tấn công để xây dựng một request hợp lệ thay mặt cho nạn nhân. Vì kẻ tấn công không có cách nào để dự đoán giá trị chính xác cho CSRF token, họ sẽ không thể đưa nó vào request độc hại.

**Lưu ý**

CSRF token không cần phải được gửi dưới dạng các hidden param trong POST request. Ví dụ, một số ứng dụng đặt CSRF token trong HTTP header. Cách mà các token được truyền đi có tác động đáng kể đến bảo mật của toàn bộ cơ chế. Để biết thêm thông tin, hãy xem cách ngăn chặn các lỗ hổng CSRF.