# Finding hidden parameters

Khi thực hiện điều chỉnh API, có thể tìm thấy *undocumented parameters* mà API hỗ trợ. Có thể sử dụng những thứ này để thay đổi hành vi của applications. Burp bao gồm nhiều công cụ giúp phát hiện *hidden parameters*.

- *Burp Intruder* cho phép tự động khám phá *hidden parameters*, sử dụng *wordlists* gồm các tên param phổ biến để thay thế các param hiện có hoặc thêm param mới. Đảm bảo bao gồm các tên có liên quan đến ứng dụng, dựa trên thông tin điều tra ban đầu.
- *Param miner BApp* cho phép tự động đoán tới 65,536 tên param cho mỗi request. *Param miner* tự động đoán các tên có liên quan đến applications, dựa trên thông tin lấy từ scope.
- *Content discovery tool* cho phép khám phá nội dung không được liên kết từ nội dung hiển thị mà có thể duyệt tới, bao gồm cả params.

