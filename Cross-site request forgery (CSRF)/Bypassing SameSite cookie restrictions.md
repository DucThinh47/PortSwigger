# Bypassing SameSite cookie restrictions

Samesite là một cơ chế bảo mật trình duyệt xác định khi nào cookie của website được bao gồm trong các request có nguồn gốc từ các website khác. SameSite cookie restrictions cung cấp bảo vệ một phần chống lại nhiều cuộc tấn công cross-site, bao gồm CSRF, cross-site leaks và một số khai thác CORS.

Kể từ năm 2021, Chrome áp dụng *LAX Samesite restrictions* theo mặc định nếu website phát hành cookie không đặt rõ ràng mức độ hạn chế của riêng mình. Đây là một tiêu chuẩn được đề xuất và chúng tôi hy vọng các trình duyệt lớn khác sẽ áp dụng hành vi này trong tương lai. Do đó, điều cần thiết là phải nắm bắt vững chắc về cách các hạn chế này hoạt động, cũng như làm thế nào chúng có khả năng bị bypass, để kiểm tra kỹ lưỡng cross-site attack vectors.

Trong phần này, trước tiên chúng ta sẽ đề cập đến cách thức cơ chế Samesite hoạt động và làm rõ một số thuật ngữ liên quan. Sau đó, chúng tôi sẽ xem xét một số cách phổ biến nhất mà bạn có thể bypass các hạn chế này, cho phép CSRF và các cuộc tấn công cross-site khác trên các trang web ban đầu có thể xuất hiện an toàn.

