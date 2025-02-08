# Common defences against CSRF

Ngày nay, việc tìm kiếm và khai thác thành công các lỗ hổng CSRF thường liên quan đến việc bypassing các biện pháp chống CSRF được triển khai bởi trang web mục tiêu, trình duyệt của nạn nhân hoặc cả hai. Các cách phòng thủ phổ biến nhất thường sẽ gặp:

- **CSRF token** - CSRF token là một giá trị duy nhất, bí mật và không thể đoán trước được, tạo bởi server-side application và được chia sẻ với client. Khi cố gắng thực hiện một hành động nhạy cảm, chẳng hạn như gửi form, client phải bao gồm CSRF token chính xác trong request. Điều này làm cho một kẻ tấn công rất khó khăn trong việc xây dựng một request hợp lệ thay mặt cho nạn nhân.

- **SameSite cookies** - Samesite là một cơ chế bảo mật browser xác định khi nào cookie của website được bao gồm trong requests có nguồn gốc từ các website khác. Vì các request thực hiện các hành động nhạy cảm thường yêu cầu session cookie được xác thực, các hạn chế về khả năng phù hợp có thể ngăn kẻ tấn công kích hoạt các hành động này cross-site. Từ năm 2021, Chrome thực thi các hạn chế LAX Samesite theo mặc định. Vì đây là tiêu chuẩn được đề xuất, hy vọng các trình duyệt lớn khác sẽ áp dụng hành vi này trong tương lai.

- **Referer-based validation** - Một số ứng dụng sử dụng HTTP Referer header để cố gắng bảo vệ chống lại các cuộc tấn công CSRF, thông thường bằng cách xác minh rằng request bắt nguồn từ domain riêng của ứng dụng. Điều này thường kém hiệu quả hơn so với xác thực CSRF token.

