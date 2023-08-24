- DNS (Domain Name System) là hệ thống phân giải tên miền. Cho phép thiết lập tương ứng giữa địa chỉ IP và tên miền trên Internet.
- Các loại recored DNS
    - A Record (Address Record): dùng để trỏ tên miền website tới một địa chỉ IP cụ thể . Cú pháp: ```[domain_name] IN A [IP]```
    - NS Record (Name Server Record): Xác định máy chủ tên miền (name server) có trách nhiệm cung cấp thông tin về tên miền. Cú pháp: ```[domain_name] IN CNAME [domain_server_name]```
    - CNAME Record (Canonical Name Record): quy định một tên miền là bí danh của một tên miền khác, một tên miền chính có thể có nhiều bí danh. Cú pháp: ```[alias_domain_name] IN CNAME [domain_name]```
    - PTR Record (Pointer Record): Được sử dụng trong việc đảo ngược để ánh xạ địa chỉ IP thành tên miền.
    - AAAA Record (IPv6 Address Record): dùng để trỏ tên miền đến địa chỉ IPv6
    - MX Record (Mail Exchange Record): Xác định máy chủ email xử lý thư điện tử cho một tên miền cụ thể. 
    - TXT Record: Chứa thông tin văn bản tùy ý về tên miền. Thường được sử dụng cho xác thực và các mục đích khác.
    Ngoài ra còn một số loại bản ghi khác như SOA, SRV, NAPTR,...
***
- Nguyên tắc làm việc của DNS
    - Query (Truy vấn): Khi nhập một địa chỉ URL vào trình duyệt, trình duyệt sẽ gửi một truy vấn DNS đến máy chủ DNS gần nhất yêu cầu giải quyết tên miền.
    - Resolution (Phân giải): Máy chủ DNS sẽ thực hiện quá trình phân giải bằng cách tìm kiếm trong các bản ghi DNS để tìm địa chỉ IP tương ứng với tên miền.
    - Response (Phản hồi): Máy chủ DNS trả về địa chỉ IP cho máy tính, cho phép trình duyệt kết nối đến máy chủ tương ứng để tải trang web.
***
- Cách phân giải địa chỉ DNS
    - Local Resolver: Trình giải quyết cục bộ trong mạng sẽ kiểm tra xem có thông tin về tên miền trong bộ nhớ đệm của nó không. Nếu không, nó sẽ thực hiện một truy vấn DNS đến máy chủ DNS gần nhất.
    - Recursive DNS Server: Máy chủ DNS đệ quy sẽ thực hiện quá trình phân giải tên miền bằng cách truy vấn từ các máy chủ DNS cao hơn cho đến khi tìm thấy thông tin cần thiết. Kết quả này được gửi trở lại cho trình giải quyết cục bộ và lưu vào bộ nhớ đệm.
    - Authoritative DNS Server: Máy chủ DNS chính quyền lưu trữ thông tin chính xác về tên miền. Khi máy chủ DNS đệ quy truy vấn các máy chủ chính quyền, chúng trả về địa chỉ IP liên quan đến tên miền.
    - Caching: Kết quả phân giải được lưu trữ tạm thời trong bộ nhớ đệm của máy chủ DNS để tăng tốc độ truy vấn trong tương lai.