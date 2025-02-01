# How does SameSite work?

Trước khi cơ chế Samesite được giới thiệu, các trình duyệt đã gửi cookie trong mọi request đến domain đã phát hành chúng, ngay cả khi request được kích hoạt bởi một trang web của bên thứ ba không liên quan. Samesite hoạt động bằng cách cho phép các trình duyệt và chủ sở hữu trang web giới hạn các cross-site requests nào, nếu có, nên bao gồm các cookie cụ thể. Điều này có thể giúp giảm sự tiếp xúc của người dùng với các cuộc tấn công CSRF, điều này khiến trình duyệt của nạn nhân đưa ra request kích hoạt hành động có hại trên trang web dễ bị tổn thương. Vì các request này thường yêu cầu một cookie liên quan đến authenticated session của nạn nhân, cuộc tấn công sẽ thất bại nếu trình duyệt không bao gồm điều này.

Tất cả các trình duyệt chính hiện đang hỗ trợ các cấp độ SameSite restriction sau:

- Strict
- Lax
- None

# How does SameSite work? - Continued

Các nhà phát triển có thể định cấu hình thủ công restriction level cho mỗi cookie họ đặt, giúp họ kiểm soát nhiều hơn khi các cookie này được sử dụng. Để làm điều này, họ chỉ cần bao gồm thuộc tính SameSite trong Set-Cookie response header , cùng với giá trị ưa thích của chúng:

*Set-Cookie: session=0F8tgdOhi9ynR1M9wa3ODa; SameSite=Strict*

Mặc dù điều này cung cấp một số bảo vệ chống lại các cuộc tấn công CSRF, nhưng không có hạn chế nào trong số này cung cấp khả năng miễn dịch được đảm bảo, vì chúng tôi sẽ chứng minh việc sử dụng các phòng thí nghiệm tương tác dễ bị tổn thương có chủ ý sau này trong phần này.

**Lưu ý**

Nếu trang web phát hành cookie không đặt thuộc tính SameSite một cách rõ ràng, Chrome sẽ tự động áp dụng Lax restrictions theo mặc định. Điều này có nghĩa là cookie chỉ được gửi trong cross-site requests đáp ứng các tiêu chí cụ thể, mặc dù các nhà phát triển không bao giờ cấu hình hành vi này. Vì đây là một tiêu chuẩn mới được đề xuất, chúng tôi hy vọng các trình duyệt lớn khác sẽ áp dụng hành vi này trong tương lai.

# Strict

Nếu cookie được set với thuộc tính SameSite=Strict, trình duyệt sẽ không gửi nó trong bất kỳ cross-site requests nào. Nói một cách đơn giản, điều này có nghĩa là nếu site mục tiêu cho request không khớp với site hiện đang hiển thị trong thanh địa chỉ của trình duyệt, nó sẽ không bao gồm cookie.

Điều này được khuyến nghị khi đặt cookie cho phép bearer sửa đổi dữ liệu hoặc thực hiện các hành động nhạy cảm khác, chẳng hạn như truy cập các trang cụ thể chỉ có sẵn cho người dùng được xác thực.

Mặc dù đây là tùy chọn an toàn nhất, nhưng nó có thể tác động tiêu cực đến trải nghiệm người dùng trong trường hợp chức năng cross-site được mong muốn.

# Lax

Lax SameSite restrictions có nghĩa là các trình duyệt sẽ gửi cookie trong cross-site requests, nhưng chỉ khi cả hai điều kiện sau đây được đáp ứng:

- Yêu cầu sử dụng GET method.
- Yêu cầu là kết quả từ một top-level navigation của người dùng, chẳng hạn như nhấp vào liên kết.

Điều này có nghĩa là cookie không được bao gồm trong cross-site POST requests, ví dụ. Vì POST requests thường được sử dụng để thực hiện các hành động sửa đổi dữ liệu hoặc trạng thái (ít nhất là theo thông lệ tốt nhất), chúng có nhiều khả năng là mục tiêu của các cuộc tấn công CSRF.

Tương tự như vậy, cookie không được bao gồm trong background requests, chẳng hạn như requests được bắt đầu bởi scripts, iframes hoặc tham chiếu đến hình ảnh và các tài nguyên khác.

# None

Nếu một cookie được set với thuộc tính SameSite=None, điều này sẽ vô hiệu hóa hoàn toàn SameSite restrictions, bất kể trình duyệt. Do đó, các trình duyệt sẽ gửi cookie này trong tất cả requests đến site đã phát hành nó, ngay cả những người được kích hoạt bởi sites của bên thứ ba hoàn toàn không liên quan.

Ngoại trừ Chrome, đây là hành vi mặc định được sử dụng bởi các trình duyệt chính nếu không có thuộc tính SameSite nào được cung cấp khi set cookie.

Có những lý do chính đáng để vô hiệu hóa SameSite, chẳng hạn như khi cookie được dự định sẽ được sử dụng từ bối cảnh của bên thứ ba và không cho phép người mang quyền truy cập vào bất kỳ dữ liệu hoặc chức năng nhạy cảm nào. Tracking cookies là một ví dụ điển hình.

# None - Continued

Nếu bạn gặp phải một cookie được set SameSite=None hoặc không có restrictions rõ ràng, nên điều tra xem nó có sử dụng được không. Khi hành vi "Lax-by-default" lần đầu tiên được Chrome áp dụng, điều này có tác dụng phụ là phá vỡ rất nhiều chức năng web hiện có. Là một cách giải quyết nhanh chóng, một số trang web đã chọn chỉ đơn giản là vô hiệu hóa các hạn chế của SameSite đối với tất cả các cookie, bao gồm cả các trang web có khả năng nhạy cảm.

Khi setting cookie với SameSite=None, trang web cũng phải bao gồm thuộc tính *Secure*, đảm bảo rằng cookie chỉ được gửi trong các tin nhắn được mã hóa qua HTTPS. Nếu không, trình duyệt sẽ từ chối cookie và nó sẽ không được set.

*Set-Cookie: trackingId=0F8tgdOhi9ynR1M9wa3ODa; SameSite=None; Secure*