**Nginx là gì**
- NGINX là một máy chủ web và máy chủ reverse proxy mã nguồn mở được phát triển bởi Igor Sysoev. 
- NGINX thường được sử dụng để phục vụ các trang web và ứng dụng web, cũng như làm máy chủ proxy để điều hướng yêu cầu từ người dùng đến các máy chủ ứng dụng hoặc máy chủ dự án khác trong một môi trường mạng phức tạp.
- NGINX được thiết kế để xử lý đồng thời một số lượng lớn kết nối với tài nguyên thấp.
- Với khả năng điều hướng yêu cầu và khả năng xử lý tải trọng lớn, NGINX thường được sử dụng trong các mô hình phân phối tải để đảm bảo rằng các máy chủ ứng dụng được tải cân đối.
**Một số tính năng của Nginx**
- Tính năng của máy chủ HTTP:
  - Khả năng xử lý lớn với hơn 10.000 kết nối cùng một thời điểm với bộ nhớ thấp.
  - Phục vụ cho các tập tin tĩnh (static files) và thực hiện lập chỉ mục tập tin.
  - Gia tăng tốc độ reverse proxy thông qua bộ nhớ đệm (cache).
  - Cân bằng quá trình tải đơn giản cùng khả năng chịu lỗi.
  - Giúp nâng cao tốc độ cùng bộ nhớ đệm của FastCGI, uwsgi, SCGI, và loạt máy chủ memcached.
  - Hỗ trợ Kiến trúc modular, nâng cao tốc độ nạp trang thông qua nén gzip tự động.
  - Giúp mã hoá SSL cùng TLS.
- Tính năng máy chủ mail proxy, gồm các phương pháp xác thực như sau:
  - POP3: USER/PASS, APOP, AUTH LOGIN/PLAIN/CRAM-MD5.
  - IMAP: LOGIN, AUTH LOGIN/PLAIN/CRAM-MD5.
  - SMTP: AUTH LOGIN/PLAIN/CRAM-MD5.
  - Hỗ trợ SSL, STARTTLS cùng STLS.
***
**Apache là gì**
- Apache (Apache HTTP Server) là phần mềm web server miễn phí mã nguồn mở, được sáng lập và điều hành bởi Apache Software Foundation.
- Apache là chương trình máy chủ HTTP – một chương trình dành cho máy chủ đối thoại qua giao thức HTTP.
-  Apache chạy trên các hệ điều hành tương tự như Unix, Microsoft Windows, Novell Netware và các hệ điều hành khác. 
-  Nó có thể được tùy chỉnh cao để đáp ứng nhu cầu của nhiều môi trường khác nhau bằng cách sử dụng các phần mở rộng và mô-đun.
**Cách hoạt động của Apache Web Server**
- Apache HTTP Server hoạt động theo mô hình máy chủ-web (web server). Chức năng chính của nó là lắng nghe và xử lý các yêu cầu từ máy tính khác thông qua giao thức HTTP (Hypertext Transfer Protocol). 
- Cách hoạt động cơ bản của Apache:
  1. **Lắng nghe và chờ yêu cầu**: Khi khởi động Apache, nó sẽ lắng nghe trên một cổng (thường là cổng 80 cho HTTP hoặc 443 cho HTTPS) trên máy chủ. Điều này cho phép nó sẵn sàng để nhận yêu cầu từ trình duyệt web hoặc các ứng dụng khác trên Internet.
  2. **Nhận yêu cầu HTTP**: Khi một trình duyệt web hoặc ứng dụng khác gửi yêu cầu HTTP đến máy chủ (bằng cách nhập một URL hoặc nhấp vào liên kết), yêu cầu này được gửi đến cổng mà Apache đang lắng nghe.
  3. **Phân tích yêu cầu**: Apache phân tích yêu cầu nhận được để biết chính xác trang web hoặc tài liệu nào đang được yêu cầu. Nó kiểm tra xem tệp này tồn tại trong hệ thống tệp của máy chủ không.
  4. **Xử lý yêu cầu**: Nếu tệp được yêu cầu tồn tại, Apache sẽ xử lý yêu cầu này. Nếu đó là một tệp HTML, Apache có thể đọc nội dung của tệp và gửi nó về trình duyệt để hiển thị. Nếu đó là một tệp hình ảnh, Apache sẽ gửi tệp hình ảnh về để trình duyệt hiển thị.
  5. **Phản hồi**: Sau khi xử lý yêu cầu, Apache tạo ra một phản hồi HTTP, bao gồm mã trạng thái (như 200 OK cho yêu cầu thành công) và nội dung cụ thể (nếu có). Phản hồi này được gửi trở lại cho trình duyệt hoặc ứng dụng gốc, chúng sẽ hiển thị nội dung hoặc thực hiện các hành động tương ứng.
  6. **Chờ và lắng nghe yêu cầu tiếp theo**: Apache tiếp tục lắng nghe và chờ các yêu cầu mới từ trình duyệt hoặc ứng dụng khác, lặp lại quá trình trên để phục vụ các trang web và tài liệu khác.

***
***HTTP return code***
- ***200 (OK)***:
  - Đây là phản hồi tiêu chuẩn cho các request HTTP thành công.
  - Cho biết request đã thành công và máy chủ đã trả về tài nguyên được yêu cầu. 
- ***301, 307***:
  - Cả hai mã này được sử dụng để chuyển hướng.
  - ***301 (Moved Permanently)***: cho biết chuyển hướng là vĩnh viễn, tức là các request sau này nên sử dụng URL mới. 
  - ***307 (Temporary Redirect)***: cũng là chuyển hướng, nhưng không yêu cầu thay đổi URL sau này, giữ nguyên request ban đầu.
- ***400, 401, 403, 404, 406, 408, 422***:
  - ***400 (Bad Request)***: Mã này cho biết request bị lỗi do sự cố từ phía máy khách, chẳng hạn như cú pháp request không đúng.
  - ***401 (Unauthorized)***: Mã này chỉ ra request yêu cầu xác thực người dùng hoặc thông tin đăng nhập cung cấp không hợp lệ. Máy khách nên cung cấp thông tin đăng nhập hợp lệ trong tiêu đề request.
  - ***403 (Forbidden)***: Mã này cho biết máy chủ hiểu request, nhưng từ chối ủy quyền. Không giống như 401, việc xác thực không có ý nghĩa.
  - ***404 (Not Found)***: Mã lỗi "Không tìm thấy" cho biết máy chủ không thể tìm thấy tài nguyên được yêu cầu. Điều này có thể do URL bị gõ sai hoặc tài nguyên đã bị xóa.
  - ***406 (Not Acceptable)***: Mã này cho biết máy chủ không thể tạo ra phản hồi phù hợp với danh sách các giá trị chấp nhận được, được xác định trong tiêu đề request.
  - ***408 (Request Timeout)***: Mã này cho biết máy chủ đã hết thời gian đợi request. Điều này có thể xảy ra khi máy khách không gửi request kịp thời hoặc khi quá trình xử lý request mất quá nhiều thời gian.
  - ***422 (Unprocessable Entity)***: Mã này cho biết máy chủ hiểu kiểu nội dung của thực thể request, nhưng không thể xử lý nội dung, thường do cú pháp không chính xác.
- ***500, 502, 504***:
  - ***500 (Internal Server Error)***: Mã này cho biết máy chủ gặp thông báo lỗi tổng quát trong quá trình xử lý yêu cầu và bản chất chính xác của lỗi không được chỉ định.
  - ***502 (Bad Gateway)***: Mã này cho biết máy chủ, khi hoạt động như một cổng điều phối hoặc proxy, đã nhận phản hồi không hợp lệ từ máy chủ phía trên nó truy cập để thực hiện yêu cầu.
  - ***504 (Gateway Timeout)***: Mã này cho biết trong khi hoạt động như một cổng điều phối hoặc proxy, máy chủ không nhận được phản hồi kịp thời từ máy chủ phía trên hoặc một máy chủ phụ khác mà nó cần truy cập để hoàn thành yêu cầu.