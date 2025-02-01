# What is a site in the context of SameSite cookies?

Trong bối cảnh SameSite cookie restrictions , một site được định nghĩa là *top-level domain* (TLD), thường là một cái gì đó như *.com* hoặc *.NET*, cộng với một level của domain name. Điều này thường được gọi là TLD+1.

Khi xác định liệu một request có là *same-site* hay không, URL scheme cũng được xem xét. Điều này có nghĩa là một liên kết từ http://app.example.com đến https://app.example.com được hầu hết các trình duyệt coi là cross-site.

- https: URL scheme
- .example: TLD+1
- .com: TLD

3 yếu tố này kết hợp tạo thành 1 site.

![img](https://imgur.com/LbHTnZy.png)

**Lưu ý**

Bạn có thể bắt gặp thuật ngữ "effective top-level domain" (ETLD). Đây chỉ là một cách kế toán cho các hậu tố đa điểm dành riêng được coi là top-level domains trong thực tế, như *.co.uk*.

# What's the difference between a site and an origin?

Sự khác biệt giữa một site và một origin là phạm vi của chúng; Một site bao gồm nhiều domain names, trong khi origin chỉ bao gồm một. Mặc dù chúng có liên quan chặt chẽ, nhưng điều quan trọng là không sử dụng các thuật ngữ có thể thay thế cho nhau vì cả hai có thể có ý nghĩa bảo mật nghiêm trọng.

Hai URL được coi là có cùng origin nếu chúng chia sẻ cùng một scheme, domain name và port. Mặc dù lưu ý rằng port thường được suy ra từ scheme.

![img](https://imgur.com/Yb6U0Kk.png)

Như bạn có thể thấy từ ví dụ này, thuật ngữ "site" ít cụ thể hơn nhiều vì nó chỉ chiếm scheme và phần cuối cùng của domain name. Điều quan trọng, điều này có nghĩa là một cross-origin request vẫn có thể là same-site, nhưng không phải là cách khác.


| Request from            | Request to                   | Same-site?            | Same-origin?               |
|-------------------------|------------------------------|-----------------------|----------------------------|
| https://example.com     | https://example.com          | Yes                   | Yes                        |
| https://app.example.com | https://intranet.example.com | Yes                   | No: mismatched domain name |
| https://example.com     | https://example.com:8080     | Yes                   | No: mismatched port        |
| https://example.com     | https://example.co.uk        | No: mismatched eTLD   | No: mismatched domain name |
| https://example.com     | http://example.com           | No: mismatched scheme | No: mismatched scheme      |


Đây là một sự khác biệt quan trọng vì nó có nghĩa là bất kỳ lỗ hổng nào cho phép thực thi JavaScript tùy ý có thể bị lạm dụng để bypass các phòng thủ dựa trên trang web trên các domains khác thuộc cùng một site. Chúng ta sẽ thấy một ví dụ về điều này trong một trong các phòng thí nghiệm sau.


