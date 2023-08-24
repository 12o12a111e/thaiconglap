***
  - SSL (Secure Sockets Layer) là một giao thức bảo mật Internet dựa trên mã hóa, cho phép thiết lập kết nối được mã hóa an toàn giữa máy chủ web host và trình duyệt web của client và đảm bảo cho tất cả các dữ liệu được truyền không bị tách rời.
  - Có 5 cách chứng thực SSL gồm:
    - Domain Validation 
    - Organization Validation 
    - Extended Validation
    - Wildcard SSL Certìicate
    - Subject Alternative Names (SANs SSL)
***
  -  CSR (Certìicate Signing Request) là chứa thông tin của chủ sở hữu tên miền được mã hóa, thông tin này được gửi đến nhà cung cấp dịch vụ SSL để xác nhận.
***
  -  Pem (Privacy Enhanced Mail) là tệp dữ liệu chứa các thông tin liên quan đến SSL, sử dụng mã hóa Base64 đễ biểu diễn dữ liệu.
***
  -  Private key là tệp mã hóa được sinh ra cùng lúc tạo CSR, được sử dụng để ký và giải mã thông tin.
***
  -  PFX file 
     -  Personal Information Exchange là một định dạng tệp dữ liệu được sử dụng để lưu trữ khóa riêng tư cùng với chứng chỉ SSL, thường được bảo vệ bằng mật khẩu.
     -  Định dạng này thường được sử dụng trên hệ điều hành Windows. 
     -  Cách chuyển từ file CRT file sang PFX file cần sử dụng công cụ OpenSSL:
        1. Kiểm tra OpenSSL đã được cài đăt trên máy thông qua lệnh:
           ~~~
           openssl version
           ~~~
           Nếu kết quả trả về là **command not found** hay **shell command not found OpenSSL** thì cần cài đặt OpenSSL.
        2. Cài đặt OpenSSL trên Ubuntu: 
           ~~~
           apt install openssl
           ~~~
        3. Sử dụng lệnh openssl để thực hiện chuyển đổi:
            ~~~
            openssl pkcs12 -export -out certificate.pfx -inkey private.key -in certificate.crt
            ~~~
            Lệnh này sẽ tạo một tệp certificate.pfx bằng cách kết hợp tệp private.key và certificate.crt
        4. OpenSSL sẽ yêu cầu nhập mật khẩu cho tệp PFX.
***
   - Sử dụng OpenSSL để tạo file CSR sau đó request SSL cho domain \<name>.techtraining.vietnix.tech:
       1. Khởi tạo private key
          ~~~
          openssl genrsa -out lap.techtraining.vietnix.tech.key 2048
          ~~~ 
       2. Khởi tạo tệp CSR với đầu vào là private key vừa tạo:
            ~~~
            openssl req -new -key lap.techtraining.vietnix.tech.key -out lap.techtraining.vietnix.tech.csr
            ~~~
            Thực hiện điền các thông tin gồm:
            - Country Name (2 letter code) []: Mã quốc gia theo chuẩn ISO 3166-1 alpha-2 (2 ký tự : VN, UK, US)
            - State or Province Name (full name) []: Tên tỉnh thành
            - Locality Name (eg, city) []: Tên thành phố/quận huyện
            - Organization Name (eg, company) []: Tên công ty, doanh nghiệp
            - Organizational Unit Name (eg, section) []: Lĩnh vực hoạt động
            - Common Name (eg, fully qualified host name) []: Domain của website cần chứng thực 
            - Email Address []: Địa chỉ Email
            ~~~
            Country Name (2 letter code) [AU]:VN
            State or Province Name (full name) [Some-State]:HCM
            Locality Name (eg, city) []:Tan Binh
            Organization Name (eg, company) [Internet Widgits Pty Ltd]:Vietnix
            Organizational Unit Name (eg, section) []:IT
            Common Name (e.g. server FQDN or YOUR name) []:lap.techtraining.vietnix.tech
            Email Address []:lap.thai@vietnix.tech
            ~~~
       3. Yêu cầu chứng chỉ SSL
            - Sau khi tạo tệp CSR, tiếp tục yêu cầu chứng chỉ SSL từ một Certificate Authority (CA), như thực hiện với Let's Encrypt.
       4. Nhận và cài đặt chứng chỉ SSL
            - Sau khi nhận được chứng chỉ SSL từ CA, cần phải cài đặt trên máy chủ web, quá trình thay đổi tùy theo lại máy chủ và hệ thống đang sử dụng.