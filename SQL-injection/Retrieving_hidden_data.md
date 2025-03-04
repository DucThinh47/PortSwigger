# Retrieving hidden data

Kịch bản như sau: một ứng dụng mua sắm hiển thị sản phẩm theo danh mục. Khi người dùng nhấp vào danh mục Gifts (Quà tặng), trình duyệt của họ sẽ gửi yêu cầu đến URL sau:

    https://insecure-website.com/products?category=Gifts

Điều này khiến ứng dụng thực hiện truy vấn SQL để lấy thông tin sản phẩm tương ứng từ cơ sở dữ liệu:

    SELECT * FROM products WHERE category = 'Gifts' AND released = 1

Truy vấn SQL này yêu cầu cơ sở dữ liệu trả về:

- Tất cả thông tin (*)
- Từ bảng products
- Với điều kiện danh mục (category) là Gifts
- Và sản phẩm đã được phát hành (released = 1).

Điều kiện released = 1 được sử dụng để ẩn những sản phẩm chưa được phát hành. Có thể giả định rằng đối với các sản phẩm chưa phát hành, giá trị released = 0.

# Retrieving hidden data - Continued

Ứng dụng trong kịch bản trước không có bất kỳ cơ chế bảo vệ nào chống lại SQL injection. Điều này cho phép kẻ tấn công có thể tạo ra một truy vấn tấn công như sau:

    https://insecure-website.com/products?category=Gifts'--

Truy vấn SQL tương ứng sẽ trở thành:

    SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

Điểm quan trọng cần lưu ý là -- là ký hiệu bình luận trong SQL. Điều này có nghĩa là phần còn lại của truy vấn sẽ bị bỏ qua như một bình luận, về cơ bản là loại bỏ điều kiện AND released = 1. Do đó, tất cả sản phẩm, bao gồm cả những sản phẩm chưa phát hành, sẽ được hiển thị.

Ngoài ra, kẻ tấn công có thể sử dụng một truy vấn khác để hiển thị tất cả sản phẩm, kể cả những danh mục mà họ không biết:

    https://insecure-website.com/products?category=Gifts'+OR+1=1--

Truy vấn SQL tương ứng sẽ trở thành:

    SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1

Truy vấn này sẽ trả về tất cả các sản phẩm vì điều kiện 1=1 luôn đúng, làm cho toàn bộ điều kiện WHERE luôn hợp lệ.

**Lưu ý**

Hãy cẩn trọng khi chèn điều kiện OR 1=1 vào truy vấn SQL. Ngay cả khi điều này có vẻ vô hại trong bối cảnh đang thực hiện, nhiều ứng dụng có thể tái sử dụng dữ liệu từ một yêu cầu trong nhiều truy vấn khác nhau. Nếu điều kiện này lọt vào một câu lệnh UPDATE hoặc DELETE, nó có thể gây mất dữ liệu nghiêm trọng.

# Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

![img](0)

Truy cập lab: 

![img](1)

Ở phần Refine your search, click chọn Accessories: 

![img](2)

Intercept request này trong Burp Suite: 

![img](3)

Chèn điều kiện '+OR+1=1-- vào đằng sau category=Accessories: 

![img](4)

Số sản phẩm hiển thị khi chưa chèn: 

![img](5)

Sau khi chèn payload: 

![img](6)

Solved the lab!

![img](7)









