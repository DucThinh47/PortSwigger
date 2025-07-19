# Contents
- [What are JWTs?](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#what-are-jwts)
- [What are JWT attacks?](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#what-are-jwt-attacks)
- [What is the impact of JWT attacks?](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#what-is-the-impact-of-jwt-attacks)
- [How do vulnerabilities to JWT attacks arise?](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#how-do-vulnerabilities-to-jwt-attacks-arise)
- [Exploiting flawed JWT signature verification](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#exploiting-flawed-jwt-signature-verification)
- [Brute-forcing secret keys](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#brute-forcing-secret-keys)
- [JWT header parameter injections](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#jwt-header-parameter-injections)
- [JWT algorithm confusion](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#jwt-algorithm-confusion)
- [How to prevent JWT attacks](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#how-to-prevent-jwt-attacks)
- [Labs](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#labs)
    - [Lab: JWT authentication bypass via unverified signature](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-unverified-signature)
    - [Lab: JWT authentication bypass via flawed signature verification](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-flawed-signature-verification)
    - [Lab: JWT authentication bypass via weak signing key](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-weak-signing-key)
    - [Lab: JWT authentication bypass via jwk header injection](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/Contents.md#lab-jwt-authentication-bypass-via-jwk-header-injection)
# What are JWTs?

`JSON Web Token (JWT)` là một định dạng chuẩn hóa để `truyền dữ liệu JSON` đã được `ký mã hóa` giữa các hệ thống. Về cơ bản, chúng có thể chứa bất kỳ loại dữ liệu nào, nhưng thường được dùng để gửi thông tin (gọi là "claims") về người dùng trong các quy trình như `xác thực`, `quản lý phiên` và `kiểm soát quyền truy cập`.

Điểm khác biệt chính so với các phiên truyền thống là `toàn bộ dữ liệu mà máy chủ cần đều được lưu trữ ở phía máy khách (client-side) ngay trong JWT`. Điều này làm cho JWT trở thành lựa chọn phổ biến cho các trang web phân tán lớn, nơi người dùng cần tương tác mượt mà với nhiều máy chủ phụ trợ (back-end servers) khác nhau.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image0.png?raw=true)

**JWT format**

Một `JWT` bao gồm `3 phần`: `header`, `payload` và `signature`. Chúng được phân tách bằng một `dấu chấm`, như thể hiện trong ví dụ sau:

    eyJraWQiOiI5MTM2ZGRiMy1jYjBhLTRhMTktYTA3ZS1lYWRmNWE0NGM4YjUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTY0ODAzNzE2NCwibmFtZSI6IkNhcmxvcyBNb250b3lhIiwic3ViIjoiY2FybG9zIiwicm9sZSI6ImJsb2dfYXV0aG9yIiwiZW1haWwiOiJjYXJsb3NAY2FybG9zLW1vbnRveWEubmV0IiwiaWF0IjoxNTE2MjM5MDIyfQ.SYZBPIBg2CRjXAJ8vCER0LA_ENjII1JakvNQoP-Hw6GG1zfl4JyngsZReIfqRvIAEi5L4HV0q7_9qGhQZvy9ZdxEJbwTxRs_6Lb-fZTDpW6lKYNdMyjw45_alSCZ1fypsMWz_2mTpQzil0lOtps5Ei_z7mM7M8gCwe_AGpI53JxduQOaB5HkT5gVrv9cKu9CsW5MS6ZbqYXpGyOG5ehoxqm8DL5tFYaW3lB50ELxi0KsuTKEbD0t5BCl0aCR2MBJWAbN-xeLwEenaqBiwPVvKixYleeDQiBEIylFdNNIMviKRgXiYuAvMziVPbwSgkZVHeEdF5MQP1Oe2Spac-6IfA
Các phần `header` và `payload` của `JWT` chỉ là `các đối tượng JSON` được mã hóa `base64`. `Header` chứa `metadata` về token, trong khi `payload` chứa "claims" thực tế về người dùng. Ví dụ: bạn có thể giải mã `payload` từ token ở trên để tiết lộ các claims sau:

    {
        "iss": "portswigger",
        "exp": 1648037164,
        "name": "Carlos Montoya",
        "sub": "carlos",
        "role": "blog_author",
        "email": "carlos@carlos-montoya.net",
        "iat": 1516239022
    }

Trong hầu hết các trường hợp, dữ liệu này có thể `dễ dàng được đọc hoặc sửa đổi` bởi bất kỳ ai có quyền truy cập vào token. Do đó, sự bảo mật của bất kỳ cơ chế dựa trên JWT nào đều phụ thuộc rất nhiều vào `chữ ký mật mã`.

**JWT signature**

`Chữ ký JWT` được tạo bằng cách `băm `(và đôi khi `mã hóa`) `header` và `payload` cùng với `một khóa bí mật của máy chủ`. Nó giúp đảm bảo `tính toàn vẹn` của token, phát hiện mọi sự thay đổi dữ liệu, và ngăn chặn việc tạo token giả mạo nếu không có khóa bí mật.

**JWT vs JWS vs JWE**

- `JWT` chỉ là `một định dạng` dữ liệu `JSON`.
- Trên thực tế, `JWT` luôn được triển khai dưới dạng `JWS` (có chữ ký để xác thực tính toàn vẹn) hoặc `JWE` (có mã hóa để bảo mật nội dung).
- Khi nói "`JWT`", hầu hết mọi người đều ngụ ý `JWS`.
- `JWE` khác `JWS` ở chỗ nội dung `được mã hóa, không chỉ ký`.

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image1.png?raw=true)

# What are JWT attacks?
Các cuộc `tấn công JWT (JSON Web Token)` liên quan đến việc người dùng gửi `các JWT đã bị sửa đổi` đến máy chủ nhằm đạt được mục tiêu độc hại. Thông thường, mục tiêu này là `vượt qua cơ chế xác thực` và `kiểm soát quyền truy cập` bằng cách `mạo danh một người dùng khác` đã được xác thực trước đó.
# What is the impact of JWT attacks?
Tác động của các cuộc tấn công JWT thường `rất nghiêm trọng`. Nếu kẻ tấn công có thể `tạo ra các token hợp lệ của riêng mình` với các giá trị tùy ý, chúng có thể `nâng cao đặc quyền` của bản thân hoặc `mạo danh` người dùng khác, từ đó chiếm toàn quyền kiểm soát tài khoản của họ.
# How do vulnerabilities to JWT attacks arise?
- `Lỗ hổng JWT` xuất hiện do ứng dụng `xử lý JWT không đúng cách`, chứ không phải do bản thân JWT.
- Nguyên nhân chính là `chữ ký JWT không được xác minh đầy đủ` hoặc `khóa bí mật của máy chủ bị lộ`.
- Khi chữ ký không được kiểm tra kỹ hoặc khóa bị lộ, kẻ tấn công có thể `sửa đổi dữ liệu trong token` hoặc `tạo ra token giả mạo hợp lệ`, phá vỡ cơ chế xác thực.
# Exploiting flawed JWT signature verification
**1. JWT là tự chứa (self-contained)**:
- Server `không lưu thông tin` nào về JWT đã cấp.
- Nếu server không xác minh chữ ký đúng cách, kẻ tấn công có thể sửa đổi nội dung JWT tùy ý mà không bị phát hiện.

**2. Lỗi khi chấp nhận JWT không chữ ký (`alg: none`)**:
- Trường `alg` trong `header JWT` chỉ định `thuật toán dùng để ký và xác minh`.
- Kẻ tấn công có thể chỉnh `alg` thành `none` (JWT không có chữ ký), nếu server không kiểm tra kỹ, sẽ `chấp nhận token giả mạo`.
- Một số server có thể bị bypass bằng `kỹ thuật làm rối` như `viết hoa/lạ` hoặc `mã hóa` không mong đợi.

**3. Nguy cơ khi nội dung JWT bị thay đổi**:
- Ví dụ nếu JWT chứa:

        { "username": "carlos", "isAdmin": false }

- Nếu attacker `đổi username` để mạo danh người khác, hoặc đổi `isAdmin` thành `true` thì có thể `leo thang đặc quyền`.

**4. Lỗi do dùng sai hàm trong thư viện JWT**:
- Một số lập trình viên dùng hàm `decode()` thay vì `verify()`, dẫn đến việc `không kiểm tra chữ ký`, cho phép token giả mạo.

# Brute-forcing secret keys
**1. Tấn công brute-force khóa bí mật (secret key) trong JWT**
- Một số thuật toán như `HS256 (HMAC + SHA-256)` dùng chuỗi bí mật độc lập để ký JWT.
- Nếu chuỗi này `yếu hoặc dễ đoán`, kẻ tấn công có thể `brute-force` để tìm ra, rồi tự tạo JWT hợp lệ với nội dung tùy ý.

**2. Lỗi phổ biến của lập trình viên**
- Dùng `khóa mặc định`, quên thay khóa mẫu trong ví dụ hoặc sao chép mã từ mạng mà không đổi khóa.
- Điều này khiến server dễ bị `brute-force` bằng `wordlist` các secret phổ biến.

**3. Cách brute-force bằng công cụ Hashcat**
- Câu lệnh:
    
        hashcat -a 0 -m 16500 <jwt> <wordlist>

- Hashcat `dùng từng dòng trong wordlist` để `tạo chữ ký` và `so sánh với chữ ký gốc`.
- Nếu trùng, Hashcat sẽ `hiển thị secret` tìm được:

        <jwt>:<identified-secret>
- Lần chạy sau cần thêm tùy chọn `--show `để hiển thị kết quả đã tìm được.
- Hashcat rất nhanh vì chạy cục bộ, không gửi request lên server.

**4. Khai thác sau khi tìm được secret**
- Dùng `secret` đó để `ký lại JWT` có payload tùy chỉnh.
- Có thể sử dụng công cụ như Burp Suite để chỉnh sửa và ký lại JWT.
- Nếu `secret cực kỳ yếu`, có thể `brute-force từng ký tự` thay vì dùng wordlist.
# JWT header parameter injections
**1. JWT Header Parameter Injection – Tổng quan**
- Ngoài trường bắt buộc `alg`, `JWT header` còn có các trường khác mà attacker có thể lợi dụng như:
    - `jwk`: nhúng khóa công khai (public key) vào token.
    - `jku`: chỉ định URL chứa bộ khóa công khai (JWK Set).
    - `kid`: ID của khóa dùng để xác minh chữ ký.

***Lưu ý***: Các trường này có thể bị chỉnh sửa để đánh lừa server sử dụng khóa giả do attacker tự tạo, dẫn đến chấp nhận JWT giả mạo.

**2. Tấn công thông qua jwk (nhúng khóa công khai)**
- `jwk`: cho phép `nhúng trực tiếp khóa công khai` vào header JWT.
- Nếu server không kiểm soát kỹ, attacker có thể:
    - `Tự tạo JWT` với khóa cá nhân của mình.
    - Nhúng khóa công khai tương ứng vào `jwk`.
    - Server xác minh chữ ký bằng `khóa giả` => `JWT được chấp nhận`.

            {
                "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
                "typ": "JWT",
                "alg": "RS256",
                "jwk": {
                    "kty": "RSA",
                    "e": "AQAB",
                    "kid": "ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
                    "n": "yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9m"
                }
            }

***Lưu ý***: Burp Suite + extension JWT Editor hỗ trợ tạo, chỉnh sửa và thử nghiệm tự động kiểu tấn công này.

**3. Tấn công thông qua jku (trỏ đến URL chứa bộ khóa)**
- `jku`: server sẽ gửi `HTTP request đến URL` được chỉ định để tải khóa công khai.
- Nếu server không giới hạn domain đáng tin cậy:
    - Attacker có thể `host bộ khóa giả` trên server của mình.
    - `Chỉ định URL đó trong JWT` => Server dùng sai khóa để xác minh.

***Lưu ý***: Có thể khai thác thêm nếu server bị lỗi phân tích URL (giống SSRF).

**4. Tấn công thông qua kid (Key ID)**
- `kid`: `xác định khóa nào sẽ được dùng` khi có nhiều khóa.
- Nếu server tìm khóa dựa trên `tên file` hoặc `truy vấn DB`:
    - Có thể khai thác `Directory Traversal` (ví dụ: `"kid": "../../etc/passwd"`)
    - Hoặc `SQL Injection` nếu chèn vào truy vấn DB.

Đặc biệt nguy hiểm nếu `kết hợp với thuật toán HS256` (symmetric):
- Attacker `chỉ cần biết nội dung file` để dùng làm khóa bí mật (secret).
- Ví dụ: `kid` trỏ tới `/dev/null` (file rỗng), rồi ký JWT bằng `chuỗi rỗng` ("") => chữ ký hợp lệ.

        {
            "kid": "../../path/to/file",
            "typ": "JWT",
            "alg": "HS256",
            "k": "asGsADas3421-dfh9DGN-AFDFDbasfd8-anfjkvc"
        }

**5. Các header khác có thể khai thác**
- `cty`: Thay đổi loại nội dung (content type), có thể gây:
    - `XXE` (XML External Entity),
    - `Deserialization attacks` nếu dùng `application/x-java-serialized-object`.
- `x5c`: Nhúng chứng chỉ `X.509 tự ký`, nếu server dùng để xác minh chữ ký.
    - Gây ra lỗi `parsing` hoặc khai thác tương tự `jwk`.
    - Đã từng có CVE nghiêm trọng: CVE-2017-2800, CVE-2018-2633.
# JWT algorithm confusion
Ngay cả khi một máy chủ sử dụng bí mật mạnh mẽ mà bạn không thể sử dụng `brute-force`, bạn vẫn có thể tạo ra các JWT hợp lệ bằng cách `ký mã thông báo bằng thuật toán mà các nhà phát triển chưa dự đoán`. Đây được gọi là một cuộc `tấn công nhầm lẫn thuật toán`.
# How to prevent JWT attacks
**Biện pháp phòng chống tấn công JWT (mức cao)**:

**1. Sử dụng thư viện JWT cập nhật và an toàn**
- Chọn thư viện mới, đáng tin cậy.
- Lập trình viên cần hiểu rõ cách hoạt động và rủi ro bảo mật khi dùng JWT.

**2. Xác minh chữ ký JWT nghiêm ngặt**
- Kiểm tra kỹ chữ ký của tất cả JWT nhận được.
- Cảnh giác với các trường hợp dùng thuật toán bất ngờ (ví dụ: none, HS256 thay vì RS256).

**3. Giới hạn `jku` theo danh sách trắng (whitelist)**
- Chỉ cho phép tải khóa từ những domain đáng tin cậy.

**4. Ngăn chặn `kid` bị khai thác**
- Kiểm tra và lọc tránh tấn công directory traversal hoặc SQL injection thông qua trường kid.

**Best practices (thực hành tốt) khi dùng JWT**:
- Luôn thiết lập thời hạn (exp) cho JWT.
- Tránh gửi JWT qua URL, nên dùng trong header (ví dụ: Authorization).
- Dùng trường `aud` để xác định người nhận hợp lệ của JWT – tránh bị dùng lại ở trang khác.
- Hỗ trợ thu hồi token, ví dụ khi người dùng đăng xuất.

# Labs

## Lab: JWT authentication bypass via unverified signature
**1. Yêu cầu**

Phòng lab này sử dụng cơ chế `quản lý phiên dựa trên JWT`. Do có lỗi trong quá trình triển khai, máy chủ không kiểm tra chữ ký của bất kỳ JWT nào mà nó nhận được.

Để hoàn thành lab, bạn cần chỉnh sửa token phiên của mình để truy cập vào trang quản trị tại `/admin`, sau đó xóa người dùng carlos.

Bạn có thể đăng nhập vào tài khoản của mình bằng thông tin sau: `wiener:peter`

**2. Thực hiện**

Sau khi đăng nhập, server trả về Cookie dưới dạng JWT để xác thực người dùng hiện tại: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image2.png?raw=true)

Giải mã jwt này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image3.png?raw=true)

Có thể thấy, request GET xem trang My account có chứa Cookie header với giá trị là JWT này: 

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image4.png?raw=true)

Tôi đã thử sửa `wiener` thành `administrator` và send request này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image5.png?raw=true)

Đăng nhập thành công nhưng truy cập trang `/admin` bị chặn:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image6.png?raw=true)

Tiếp tục chỉnh sửa request truy cập trang `/admin`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image7.png?raw=true)

Truy cập thành công `/admin`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image8.png?raw=true)

Vẫn bị chặn khi xóa user `carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image9.png?raw=true)

Tiếp tục chỉnh sửa request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image10.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image11.png?raw=true)

=> Website không xác minh chữ ký JWT, có thể chỉnh sửa payload và thực hiện thao tác dưới quyền admin

## Lab: JWT authentication bypass via flawed signature verification
**1. Yêu cầu**
Bài lab này sử dụng cơ chế dựa trên JWT để xử lý các phiên (session). Máy chủ được cấu hình không an toàn để chấp nhận các JWT không có chữ ký.

Để giải bài lab, bạn cần sửa đổi token phiên của mình để có quyền truy cập vào bảng quản trị (admin panel) tại `/admin`, sau đó xóa người dùng `carlos`.

Bạn có thể đăng nhập vào tài khoản của mình bằng thông tin đăng nhập sau: `wiener:peter`.

**2. Thực hiện**

Đăng nhập vào tài khoản `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image12.png?raw=true)

=> Header cookie có giá trị JWT được sử dụng để xử lý phiên. Thử giải mã JWT này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image13.png?raw=true)

Website sử dụng tham số `alg`, cho biết thuật toán để ký token này là `RS256`. 

Tôi thử để nguyên giá trị của tham số `alg` và sửa phần body thành `administrator` thay vì `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image14.png?raw=true)

Send request, tài khoản của tôi vẫn là `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image15.png?raw=true)

Tiếp theo tôi thử thay `alg:none` trong JWT header và `administrator` trong JWT body, tham số `id` trong URL và xóa phần chữ ký của JWT (không xóa dấu `.`):

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image16.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image17.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image18.png?raw=true)

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image19.png?raw=true)

Thành công. Làm tương tự như bài lab trước để xóa `carlos`

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image20.png?raw=true)

## Lab: JWT authentication bypass via weak signing key
**1. Yêu cầu**
Bài lab này sử dụng cơ chế dựa trên JWT để quản lý các phiên. Nó dùng một khóa bí mật cực kỳ yếu để vừa ký vừa xác minh token. Điều này có thể dễ dàng bị tấn công vét cạn (brute-force) bằng cách sử dụng một danh sách các khóa bí mật thông dụng.

Để giải bài lab, đầu tiên bạn cần tấn công vét cạn để tìm khóa bí mật của website. Khi đã có khóa này, hãy sử dụng nó để ký một token phiên đã sửa đổi nhằm giúp bạn truy cập vào bảng quản trị (admin panel) tại /admin, sau đó xóa người dùng carlos.

Bạn có thể đăng nhập vào tài khoản của mình bằng thông tin đăng nhập sau: `wiener:peter`.

**2. Thực hiện**

Đăng nhập tài khoản `wiener`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image21.png?raw=true)

Thử giải mã JWT này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image22.png?raw=true)

=> JWT lần này đã được ký và xác minh bằng một khóa bí mật

Tôi đã truy cập [JWT secret- key list](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list) để lấy danh sách secret key thông dụng. 

Tiếp theo sử dụng `hashcat` để brute-force khóa bí mật:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image23.png?raw=true)

Kết quả thu được secret key là `secret1`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image24.png?raw=true)

Sử dụng secret key này để tạo JWT mới:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image25.png?raw=true)

Thay JWT này vào request:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image26.png?raw=true)

=> Thành công.

Tiếp tục làm như các bài lab trước để xóa `carlos`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image27.png?raw=true)

## Lab: JWT authentication bypass via jwk header injection
**1. Yêu cầu**

Phòng lab này sử dụng cơ chế dựa trên JWT để quản lý phiên làm việc. Máy chủ hỗ trợ tham số `jwk` trong phần header của JWT. Tham số này đôi khi được sử dụng để `nhúng trực tiếp khóa xác minh vào trong token`. Tuy nhiên, máy chủ không kiểm tra xem khóa được cung cấp có đến từ nguồn đáng tin cậy hay không.

Để hoàn thành lab, bạn cần chỉnh sửa và ký một JWT sao cho bạn có quyền truy cập vào trang quản trị tại đường dẫn /admin, sau đó xóa người dùng carlos.

Bạn có thể đăng nhập vào tài khoản của mình bằng thông tin sau: `wiener:peter`.

**2. Thực hiện**

Đăng nhập vào tài khoản `wiener`, request login trông như sau:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image28.png?raw=true)

Thử giải mã JWT này:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image29.png?raw=true)

Tiếp theo, tạo khóa RSAl, sử dụng extension `JWT Editor`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image30.png?raw=true)

Sửa phần JWT body thành `"sub": "administrator"` và chọn `Attack`:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image31.png?raw=true)

Chọn `Embedded JWK` vừa tạo:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image32.png?raw=true)

Sau khi nhúng, phần `JWT header` đã xuất hiện tham số `jwk`, giúp nhúng trực tiếp khóa xác minh mới, qua đó có thể sửa tham số `sub` trong body thành `administrator`, kiểm tra:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image34.png?raw=true)

=> Thành công. Thực hiện xóa `carlos` tương tự như các bài lab trước:

![img](https://github.com/DucThinh47/PortSwigger/blob/main/JWT_Attacks/images/image35.png?raw=true)






