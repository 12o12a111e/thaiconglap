***
**Cài đặt AAPanel**
- CentOS:
  ~~~
    yum install -y wget && wget -O install.sh http://www.aapanel.com/script/install_6.0_en.sh && bash install.sh
  ~~~
- Ubuntu:
  ~~~
    wget -O install.sh http://www.aapanel.com/script/install-ubuntu_6.0_en.sh && sudo bash install.sh
  ~~~
- Khi cài đặt sẽ hỏi các thông tin, gõ y và yes để tiếp tục cài đặt:
  ~~~
    Do you want to install aaPanel to the /www directory now?(y/n): y
    Do you need to enable the panel SSl ? (yes/n): yes
  ~~~
- Tiếp tục quá trình cài đặt sẽ diễn ra, đợi đến khi việc cài đặt kết thúc là nó sẽ cung cấp thông tin đăng nhập vào AAPanel vừa cài đặt.
  ~~~
    aaPanel Internet Address: https://[ip_public]:7800/a1ba67ba
    aaPanel Internal Address: https://192.168.0.158:7800/a1ba67ba
    username: 7uuiifny
    password: e2f21ef7
    Warning: If you cannot access the panel, release the following port (7800|888|80|443|20|21) in the security group
  ~~~
- Lưu lại thông tin đăng nhập trên, sau đó truy cập trên trình duyệt với thông tin trên để bắt đầu cài đặt webserver.
- Sau khi đăng nhập vào AAPanel lần đầu tiên, nó sẽ yêu cầu cài đặt loại webserver, ở đây có thể chọn loại webserver sử dụng phù hợp. Sử dụng bộ LNMP (Linux + NGINX + MySQL + PHP-FPM) để hoạt động ổn định và tối ưu nhất.
- Chọn loại webserver để cài đặt **LNMP/LAMP** và ấn nút **One-click** thì giao diện cài đặt sẽ hiển thị ra để xem tiến trình cài đặt. Thời gian cài đặt có thể mất 15 – 40 phút tùy tình trạng mạng.
***
- Sử dụng aaPanel để thực hiện các tác vụ cơ bản như tạo Domain, subdomain, tạo FTP Account, tạo database, mở remote MySQL, tạo user MySQL và cấp quyền cho user vào database.
1. **Tạo Domain và Subdomain:**
   - **Tạo domain**:
     - Trên giao diện quản lý tên miền "xyz.com" thêm một bản ghi A record mới để  trỏ tới địa chỉ IP của VPS Web server aaPanel.
     - Đăng nhập vào aaPanel.
     - Trên giao diện chính, nhấp vào "Website".
     - Chọn "Add site" .
     - Điền các thông tin như sau:
       - Domain name: Tên miền website cần sử dụng.
       - Description: Mô tả của website, mặc định nó ghi tên miền website vào và có thể sửa lại.
       - Website Path: Thư mục gốc của website này trên máy chủ.
       - FTP: Nếu muốn tạo một tài khoản riêng FTP cho website thì chọn Create.
       - Database: Nếu muốn tạo một database riêng cho website này thì chọn Create, hoặc có thể tạo sau.
       - PHP Version: Phiên bản PHP dành cho website, có thể cài thêm phiên bản PHP tại mục App Store trong AAPanel.
     - Bấm "Submit" để tạo domain.
   - **Tạo Subdomain**: 
      - Để tạo subdomain là "abc" cho domain "xyz.com": 
        - Trên giao diện quản lý tên miền "xyz.com" thêm một bản ghi A record mới để  trỏ subdomain "abc" tới địa chỉ IP của VPS Web server aaPanel.
        - Trên aaPanel, tiến hành tạo một site mới là "abc.xyz.com". 
2. **Tạo FTP Account:**
   - Trong aaPanel, nhấp vào "FTP".
   - Chọn "Add FTP".
   - Ở giao diện tiếp theo các cần điền đầy đủ các thông tin sau:
      - Username: Điền tên user FTP.
      - Password: Mật khẩu của user FTP.
      - Document Root: Đường dẫn mà user FTP sẽ được phép truy cập.
   - Bấm "Submit" để tạo tài khoản FTP.
3. **Tạo Database và Mở Remote MySQL:**
   - Trong aaPanel, nhấp vào "Database".
   - Chọn "Add Database".
   - Tại cửa sổ tạo Database, nhập thông tin về DBName, DBUser và Password.
   - Tại Permission, có 3 tùy chọn:
      - Local server – Chỉ cho phép kết nối từ chính server aaPanel.
      - Everyone – Cho phép kết nối từ xa, từ tất cả các IP.
      - Specified IP – Chỉ cho phép từ các địa chỉ IP nhất định, mỗi IP cách nhau bằng dấu phẩy(,).
   - Chọn tùy chọn "Everyone".
   - Bấm "Submit" để xác nhận.
   - Để mở Remote MySQL, cần mở port 3306 (hoặc cổng đã cài đặt) trong tường lửa và cho phép truy cập từ xa trong cài đặt MySQL.
     - Nhấp vào "Security" => Nhấp vào "Firewall".
     - Tại "Port Rule" => Chọn "Add Rule".
     - Ở giao diện " Add port rule", thiết lập các thông tin cần thiết như:
       - Protocol: chọn TCP. 
       - Port: 3306.
       - Source IP: All.
       - Strategy: Allow.
       - Remarks: Default port of MySQL service.
     - Chọn "Confirm".
4. **Tạo User MySQL và Cấp Quyền:**
   - Trong aaPanel, điều hướng đến mục "Database" và chọn "MySQL".
   - Nhấp chọn vàp "phpMyAdmin" và tiến hành nhập username/password của tài khoản "root".
   - Chọn cơ sở dữ liệu mà muốn tạo user và cấp quyền, chọn "Privileges".
   - Nhấp chọn "Add user account".
   - Tại Login Information:
     - Điền tên người dùng. 
     - Chọn "Generate" hoặc điền để tạo mật khẩu. 
   - Tại trường Database for user account, tích chọn "Grant all privileges on database...".
   - Chọn "Go" ở cuối trang để tiến hành tạo user với quyền được cấp đã thiết lập.
5. **Cài đặt SSL**
 - **Miễn phí**: 
   - Đăng nhập vào aaPanel.
   - Trên giao diện chính, nhấp vào "Website".
   - Chọn tên miền mà muốn cài đặt SSL. => Chọn "Conf".
   - Chọn "SSL".
   - Chọn "Let's Encrypt" và chọn Verification mode (kiểu chứng thực):
     - File verification: Chứng thực bằng cách tự động tạo tập tin trên máy chủ hiện tại, chọn phương thức này nếu website đang trỏ trực tiếp về IP của máy chủ đang dùng AAPanel.
     - DNS verification: Chứng thực bằng cách tạo các bản ghi vào DNS của tên miền, nên chọn nếu tên miền đang trỏ về máy chủ khác nhưng cần cài SSL lên AAPanel.
   - Chọn Domain name cần cài đặt SSL 
   - Chọn "Apply" để aaPanel tự động cài đặt chứng chỉ SSL từ Let's Encrypt cho tên miền được chọn.
   - Sau khi quá trình hoàn thành, sẽ hiển thị thông báo kèm theo thông tin chi tiết về thời hạn của chứng chỉ, cũng như private key và chứng chỉ.
   - Chọn "Force HTTPS" để website tự động chuyển về giao thức HTTPS khi truy cập trên trình duyệt.
 - **Trả phí (đã có chứng chỉ)** 
   - Vào mục SSL => chọn là Other certificate. 
   - Nhập thông tin chứng chỉ đầy đủ vào như Private key (KEY) và Certificate (CRT/PEM). 
   - Chọn "Save" để hoàn thành.
   - Lưu ý ở mục Certificate (CRT/PEM) thì phải nhập cả CA Root của chứng chỉ.
6. **Cài đặt Source WordPress trên Hosting:**
   - Đăng nhập vào aaPanel.
   - Trên giao diện chính, nhấp vào "Website".
   - Chọn "Add site".
   - Chọn "Wordpress deploy"
   - Điền thông tin như:
     - Tên miền (domain). 
     - Tiêu đề trang website (Website title) .
     - Chọn ngôn ngữ (Language).
     - Chọn phiên bản PHP (PHP version).
     - Username/Password của wordpress admin.
     - Email.
     - Có thể tích chọn "Allow weak passwords" và "Enable cache".
   - Sau khi điền đầy đủ thông tin, chọn "Submit" để cài đặt Wordpress.
   - Để truy cập trang quản trị Website của Wordpress sử dụng thông tin User vừa tạo và login theo đường dẫn: http://[domain]/wp-admin
