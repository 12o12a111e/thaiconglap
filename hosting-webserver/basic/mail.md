- **Mail Server**
  - **MX Record**: là bản ghi DNS được sử dụng để định tuyến các thư điện tử đến các máy chủ thư tương ứng cho một tên miền. Mỗi bản ghi MX có hai phần chính:
    1. Tên máy chủ thư (Hostname): Tên miền đầy đủ của máy chủ thư nơi thư sẽ được gửi đến.
    2. Ưu tiên (Priority): Một giá trị số xác định ưu tiên của máy chủ thử, giá trị thấp cho biết ưu tiên cao.
  - **PTR Record**: là bản ghi DNS được sử dụng để ánh xạ địa chỉ IP thành tên miền.
    - Thường được sử dụng trong các tìm kiếm DNS ngược, trong đó địa chỉ IP được sử dụng để tìm kiếm tên miền liên quan. 
    - Trong ngữ cảnh xác thực thư điện tử, bản ghi PTR chủ yếu được sử dụng để xác minh tính hợp lệ của người gửi thư  
  - **DKIM (DomainKeys Identìied Mail)**:
    - Là một phương pháp xác thực thư điện tử giúp xác minh tính xác thực của người gửi và tính tòan vẹn của nội dung thư.
    - Hoạt động bằng cách thêm chữ ký số vào tiêu đề thư.
    - Máy chủ thư gửi tạo ra một chữ ký mật mã duy nhất dựa trên nội dung thư và một số tiêu đề cụ thể. Máy chủ thư của người nhận sau đó sử dụng khóa công khai đã được công bố trong bản ghi DNS của người gửi để xác minh tính hợp lệ của chữ ký
  - **SPF (Sender Policy Framework)**:
    - Là một phương pháp xác thực thư điện tử giúp ngăn chặn việc làm giả mạo thư bẳng cách xác định chính sách về máy chủ thư được phép gửi thư thay mặt cho một tên miền cụ thể.
    - Một bản ghi SPF liệt kê các máy chủ và địa chỉ IP được ủy quyền để gửi thư thay mặt cho tên miền. Nếu địa chỉ IP của người gửi không được liệt kê trong bản ghi SPF, máy chủ người nhận có thể xem thư là đáng ngờ hoặc từ chối.
***
- **Debug khi khách hàng không nhận được email**
1. Kiểm tra MX records:
   - Cần xác nhận MX records của tên miền đang hoạt động chính xác.
   - Kiểm tra các giá trị ưu tiên và tên máy chủ thư để đảm bảo đã cấu hình đúng.
   - Cần xác nhận máy chủ thư được chỉ định bởi MX records là đang hoạt động và có thể nhận thư. 
2. Kiểm tra DKIM:
   - Xác minh tên miền đã được cấu hình cho DKIM.
   - Kiểm tra xem chữ ký số trong email đã được tạo và ký bởi máy chủ gửi.
   - Cần xác nhận rằng bản ghi khóa công khai DKIM đã được xuất bản trong DNS.
3. Kiểm tra SPF:
   - Kiểm tra danh sách các máy chủ thư và địa chỉ IP được phép gửi thư thay cho tên miền trong bản ghi SPF của tên miền đó.
   - Cần xác nhận địa chỉ IP của máy chủ gửi thư đang được liệt kê trong bản ghi SPF.
4. Kiểm tra PTR records:
   - Kiểm tra xem tên miền trong bản ghi PTR của địa chỉ IP máy chủ thư gửi có khớp với tên miền gửi thư không.
   - Nếu bản ghi PTR không tồn tại hoặc không khớp cần cập nhật lại.
5. Kiểm tra danh sách đen (Blacklists):
   - Kiểm tra xem địa chỉ IP của máy chủ gửi thư có bị đưa vào danh sách đen (blacklist) nào không.
   - Nếu địa chỉ IP bị đưa vào danh sách đen, cần xóa nó khỏi danh sách đen.
6. Kiểm tra hộp thư Spam/Junk:
   - Kiểm tra hộp thư rác của khách hàng để xem liệu email có bị phân loại là thư rác hay không.
   - Kiểm tra nội dung mail có hợp tiêu chuẩn hay không.