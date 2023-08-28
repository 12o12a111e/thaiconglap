***
**Cài đặt Linux, Apache, MySQL, PHP (LAMP) trên Ubuntu 22.04**
1. Cài đặt web server Apache
   - Cật nhật danh sách gói cục bộ và cài đặt Apache2:
      ~~~
      apt update -y && apt install apache2 -y
      ~~~
   - Điều chỉnh tường lửa cho phép lưu lượng HTTP và HTTPS:
      ~~~
      ufw allow in "Apache Full"
      ~~~
   - Khởi động dịch vụ Apache:
      ~~~
      systemctl start apache2
      ~~~
2. Cài đặt MySQL (MariaDB), tạo database cho Wordpress và Laravel
   - Cài đặt MariaDB:
      ~~~
      apt install mariadb-server -y
      ~~~
   - Truy cập vào MariaDB:
      ~~~
      mysql -u root -p
      ~~~
   - Tạo database cho Wordpress:
      ~~~
      CREATE DATABASE wordpress_db;
      CREATE USER 'wordpress_user'@'%' IDENTIFIED BY 'wordpress';
      GRANT ALL ON wordpress_db.* TO 'wordpress_user'@'%';
      FLUSH PRIVILEGES;
      ~~~
   - Tạo database cho Laravel:
      ~~~
      CREATE DATABASE laravel_db;
      CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'laravel';
      GRANT ALL ON laravel_db.* TO 'laravel_user'@'localhost';
      FLUSH PRIVILEGES;
      ~~~ 
   - Thoát khỏi dấu nhắc MySQL sử dụng: ```exit```
3. Cài đặt PHP và các phần mở rộng PHP bổ sung
   - Cài đặt PHP:
      ~~~
      apt install php -y
      ~~~
   - Cài đặt các mô-đun PHP 8 cần thiết:
      ~~~
      apt install libapache2-mod-php php-mbstring php-mysql php-curl php-cli php-dev php-imagick php-soap php-zip php-xml php-imap php-xmlrpc php-gd php-opcache php-intl -y
      ~~~
   - Kiểm tra phiên bản PHP bằng lệnh sau: ```php -v```
   - Để áp dụng các thay đổi và làm cho Apache hoạt động với phần mềm PHP, khởi động lại apache2:
      ~~~
      systemctl restart apache2
      ~~~
4. Cài đặt Composer
   - Cần cài đặt Composer chịu trách nhiệm cài đặt tất cả các thành phần của Laravel:
      ~~~
      apt-get install git composer -y
      ~~~
   - Kiểm tra phiên bản cài đặt Composer:
      ~~~
      composer --version
      ~~~
5. Tạo tệp Apache Virtual Host cho Wordpress và Laravel
   - ```Wordpress: lap.wordpress.vietnix.co```
     - Tạo thư mục "lap.wordpress.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.wordpress.vietnix.co
     ~~~
     - Chỉ định quyền sở hữu thư mục với biến môi trường $USER, sẽ tham chiếu người dùng hệ thống hiện tại:
     ~~~
        chown -R $USER:$USER /var/www/lap.wordpress.vietnix.co
     ~~~
     - Tạo một tệp cấu hình Virtual Host mới trong thư mục "sites-available" của Apache:
     ~~~
        vi /etc/apache2/sites-available/lap.wordpress.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        <VirtualHost *:80>
            ServerName lap.wordpress.vietnix.co
            ServerAlias www.lap.wordpress.vietnix.co
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/lap.wordpress.vietnix.co/public
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
     ~~~
   - ```Laravel: lap.laravel.vietnix.co```
     - Tạo thư mục "lap.laravel.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.laravel.vietnix.co
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel.vietnix.co
        chmod -R 775 /var/www/lap.laravel.vietnix.co
     ~~~
     - Tạo một tệp cấu hình Virtual Host mới trong thư mục "sites-available" của Apache:
     ~~~
        vi /etc/apache2/sites-available/lap.laravel.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        <VirtualHost *:80>
            ServerName lap.laravel.vietnix.co
            ServerAlias www.lap.laravel.vietnix.co
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/lap.laravel.vietnix.co/public
            <Directory /var/www/lap.laravel.vietnix.co>
                # Don't show directory index
                Options -Indexes +FollowSymLinks +MultiViews
                # Allow .htaccess files
                AllowOverride All
                # Allow web access to this directory
                Require all granted
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
     ~~~
   - Kích hoạt module "rewrite":
        ~~~
            a2enmod rewrite
        ~~~
   - Vô hiệu hóa trang web mặc định của Apache:
        ~~~
            a2dissite 000-default
        ~~~
   - Sử dụng "a2ensite" để kích hoạt máy chủ ảo mới:
        ~~~
            a2ensite lap.wordpress.vietnix.co.conf
            a2ensite lap.laravel.vietnix.co.conf
        ~~~
   - Để đảm bảo tệp cấu hình của không chứa lỗi cú pháp, hãy chạy lệnh sau:
        ~~~
            apache2ctl configtest
        ~~~
   - Tải lại Apache để những thay đổi này có hiệu lực:
        ~~~
            systemctl reload apache2
        ~~~
6. Source wordpress và laravel 
   - ```Wordpress: -> lap.wordpress.vietnix.co```
     - Tải gói cài đặt wordpress mới nhất:
     ~~~
        wget https://wordpress.org/latest.tar.gz
     ~~~
     - Giải nén bộ cài wordpress được tải về:
     ~~~
        tar -xvf latest.tar.gz
     ~~~
     - Sao chép toàn bộ thư mục "wordpress" vào thư mục "lap.wordpress.vietnix.co" và đổi tên thư mục thành "public":
     ~~~
        cp -r wordpress /var/www/lap.wordpress.vietnix.co/public
     ~~~ 
     - Gán quyền sở hữu trên toàn bộ source code wordpress:
     ~~~
        chown -R $USER:$USER /var/www/lap.wordpress.vietnix.co
     ~~~
   - ```Laravel: -> lap.laravel.vietnix.co```
     - Điều hướng đến thư mục /var/www/lap.laravel.vietnix.co:
     ~~~
        cd /var/www/lap.laravel.vietnix.co
     ~~~
     - Cài đặt Laravel bằng cách sử dụng lệnh "composer create project":
     ~~~
        composer create-project laravel/laravel .
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel và gán các quyền thích hợp:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel.vietnix.co/
        chmod -R 775 /var/www/lap.laravel.vietnix.co/
     ~~~
   - Áp dụng các thay đổi, khởi động lại máy chủ web Apache:
        ~~~
            systemctl restart apache2
        ~~~
7. Thực hiện cài đặt Wordpress
   - Truy cập server trên trình duyệt và tiến hành cài đặt WordPress bằng cách nhập  trên thanh địa chỉ: 
        ~~~
            http://lap.wordpress.vietnix.co
        ~~~
   - Màn hình cài đặt WordPress sẽ hiện ra chọn "Let's go!" để tiếp qua trang nhập thông tin cài đặt gồm:
     -  Database Name: Tên của cơ sở dữ liệu muốn sử dụng với WordPress, [wordpress_db]
     -  Username: Tên người dùng cơ sở dữ liệu, [wordpress_user]
     -  Password: Mật khẩu cuả người dùng cơ sở dữ liệu, [wordpress]
     -  Database Host [localhost] và Table Prefix [wp_] sử dụng mặc định.
   - Chọn "Submit", sau đó trên giao diện web thực hiện sao chép nội dung của tệp "wp-config.php" mà wordpress cung cấp.
     - Tạo tệp "wp-config.php":
     ~~~
        vi /var/www/lap.wordpress.vietnix.co/public/wp-config.php
     ~~~
     - Dán nội dung đã có ở trên vào tệp "wp-config.php", ví dụ nội dung như sau:
     ~~~
        <?php
        /**
        * The base configuration for WordPress
        *
        * The wp-config.php creation script uses this file during the installation.
        * You don't have to use the web site, you can copy this file to "wp-config.php"
        * and fill in the values.
        *
        * This file contains the following configurations:
        *
        * * Database settings
        * * Secret keys
        * * Database table prefix
        * * ABSPATH
        *
        * @link https://wordpress.org/documentation/article/editing-wp-config-php/
        *
        * @package WordPress
        */

        // ** Database settings - You can get this info from your web host ** //
        /** The name of the database for WordPress */
        define( 'DB_NAME', 'wordpress_db' );

        /** Database username */
        define( 'DB_USER', 'wordpress_user' );

        /** Database password */
        define( 'DB_PASSWORD', 'wordpress' );

        /** Database hostname */
        define( 'DB_HOST', 'localhost' );

        /** Database charset to use in creating database tables. */
        define( 'DB_CHARSET', 'utf8mb4' );

        /** The database collate type. Don't change this if in doubt. */
        define( 'DB_COLLATE', '' );

        /**#@+
        * Authentication unique keys and salts.
        *
        * Change these to different unique phrases! You can generate these using
        * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
        *
        * You can change these at any point in time to invalidate all existing cookies.
        * This will force all users to have to log in again.
        *
        * @since 2.6.0
        */
        define( 'AUTH_KEY',         '.{}XI[WyBqE#JeGN{;I{eTAm;@)ho^ pwhyWuP-rK13-mdde .r2|7mkG=c!9E^m' );
        define( 'SECURE_AUTH_KEY',  '--&>J+H77a]+>Od]8/O|/V&.bV|Y0i*Bg[l_pMca;Gm7[a1#CEmcYgklhB6TbbrT' );
        define( 'LOGGED_IN_KEY',    'QmHN>Mn+oO0(H7&?18Y2Q^.N}fCwTtb#{xKy{m&Yo$R7`91GM5-?r=`zJK-09qch' );
        define( 'NONCE_KEY',        ',ca~0.G0p`&Czn$Um-kn-3s9f9FLJlC5D#uGzJr_DV;4;LkWt<DJj7M+Epp+#<43' );
        define( 'AUTH_SALT',        'X!ns]PlU/U(U_-Hr?Rm1Y!K.jCV[!HzB*AapdOGm6p,+/skDn}4N-UIf>patl:9/' );
        define( 'SECURE_AUTH_SALT', 'Qk92</_4;Y) ]] Rdv&-Ec)T#L9}QoT8(#81g[D`|&{c4*&=W%g,psT-.!gKBP.>' );
        define( 'LOGGED_IN_SALT',   '<$K%v:-m5t->Dym>`If|To/OkIfb/Sa~yQLC7PlG!vzp/-{!lmtH A-<Vvo~kf)S' );
        define( 'NONCE_SALT',       '.H1POnTmhe(T!;)$XV[bd8KB,tsRc&?&LP^~{Hg;}oQ%/mDdPzo0.W4}}g};5[Ba' );

        /**#@-*/

        /**
        * WordPress database table prefix.
        *
        * You can have multiple installations in one database if you give each
        * a unique prefix. Only numbers, letters, and underscores please!
        */
        $table_prefix = 'wp_';

        /**
        * For developers: WordPress debugging mode.
        *
        * Change this to true to enable the display of notices during development.
        * It is strongly recommended that plugin and theme developers use WP_DEBUG
        * in their development environments.
        *
        * For information on other constants that can be used for debugging,
        * visit the documentation.
        *
        * @link https://wordpress.org/documentation/article/debugging-in-wordpress/
        */
        define( 'WP_DEBUG', false );

        /* Add any custom values between this line and the "stop editing" line. */



        /* That's all, stop editing! Happy publishing. */

        /** Absolute path to the WordPress directory. */
        if ( ! defined( 'ABSPATH' ) ) {
            define( 'ABSPATH', __DIR__ . '/' );
        }

        /** Sets up WordPress vars and included files. */
        require_once ABSPATH . 'wp-settings.php';
     ~~~
    - Trên giao diện web của wordpress, chọn "Run the installation".
    - Tiến hành điền thông tin của website chính như Site Title, Username, Password, Email (nên dùng mail thật để cài đặt vì có thể sẽ cần để đặt lại mật khẩu nếu bị mất) và có thể tích chọn Search engine visibility.
    - Chọn "Install Wordpress" để tiến hành cài đặt.
    - Sau khi cài đặt thành công, để đăng nhập truy cập vào: 
        ~~~
           http://lap.wordpress.vietnix.co/wp-login.php 
        ~~~
8. Cấu hình SSL tự ký cho Apache trong Ubuntu 22.04
   - Kích hoạt "mod_ssl", một module Apache cung cấp hỗ trợ mã hóa SSL:
      ~~~
      a2enmod ssl
      ~~~
   - Khởi động lại Apache để kích hoạt module:
      ~~~
      systemctl restart apache2
      ~~~
   - Tạo chứng chỉ và khóa SSL bằng lệnh "openssl":
      ~~~
      openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
      ~~~
     - Cả hai tệp là apache-selfsigned.key và apache-selfsigned.crt đã tạo sẽ được đặt trong các thư mục con thích hợp trong /etc/ssl.
     - Sau khi nhập lệnh, sẽ được đưa đến một dấu nhắc nơi có thể nhập thông tin về trang web. Danh sách đầy đủ các lời nhắc:
     ~~~
        Country Name (2 letter code) [XX]: US 
        State or Province Name (full name) []: Example 
        Locality Name (eg, city) [Default City]: Example 
        Organization Name (eg, company) [Default Company Ltd]: Example Inc 
        Organizational Unit Name (eg, section) []: Example Dept 
        Common Name (eg, your name or your server's hostname) []: your_domain_or_ip 
        Email Address []: webmaster@example.com 
     ~~~
     - Điền vào các lời nhắc một cách thích hợp. Dòng quan trọng nhất là dòng yêu cầu Common Name, cần nhập tên máy chủ mà sẽ sử dụng để truy cập máy chủ hoặc IP công khai của máy chủ.
   - Thiết lập cấu hình Virtual Host để sử dụng SSL:
     - Với cầu hình của "lap.wordpress.vietnix.co", sửa đổi như sau:
     ~~~
      <VirtualHost *:443>
            ServerName lap.wordpress.vietnix.co
            ServerAlias www.lap.wordpress.vietnix.co
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/lap.wordpress.vietnix.co/public
            SSLEngine on
            SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
            SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
      <VirtualHost *:80>
         ServerName lap.wordpress.vietnix.co
         Redirect / https://lap.wordpress.vietnix.co/
      </VirtualHost>
     ~~~
     - Với cầu hình của "lap.laravel.vietnix.co", sửa đổi như sau:
     ~~~
      <VirtualHost *:443>
            ServerName lap.laravel.vietnix.co
            ServerAlias www.lap.laravel.vietnix.co
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/lap.laravel.vietnix.co/public
            <Directory /var/www/lap.laravel.vietnix.co>
                # Don't show directory index
                Options -Indexes +FollowSymLinks +MultiViews
                # Allow .htaccess files
                AllowOverride All
                # Allow web access to this directory
                Require all granted
            </Directory>
	         SSLEngine on
   	      SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   	      SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
      <VirtualHost *:80>
         ServerName lap.laravel.vietnix.co
         Redirect / https://lap.laravel.vietnix.co/
      </VirtualHost>
     ~~~
     - Trong đó, các tùy chọn SSL cần thiết để trỏ Apache tới chứng chỉ và khóa mới được tạo là:
      ~~~
         SSLEngine on
         SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
         SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
      ~~~
     - Cầu hình chuyển hướng HTTP sang HTTPS:
     ~~~
      <VirtualHost *:80>
         ServerName domain-name_or_ip
         Redirect / https://domain-name_or_ip/
      </VirtualHost>
     ~~~
   - Kiểm tra lại cú pháp cấu hình và tải lại Apache:
     ~~~
      apachectl configtest
      systemctl reload apache2
     ~~~
***
**Cài đặt Linux, Nginx, MySQL, PHP (LEMP) trên Ubuntu 22.04**
1. Cài đặt web server Nginx
   - Cật nhật danh sách gói cục bộ và cài đặt Nginx:
      ~~~
      apt update -y && apt install nginx -y
      ~~~
   - Điều chỉnh tường lửa cho phép lưu lượng HTTP và HTTPS:
      ~~~
      ufw allow in "Nginx Full"
      ~~~
   - Khởi động dịch vụ Nginx:
      ~~~
      systemctl start nginx
      ~~~
2. Cài đặt MySQL (MariaDB), tạo database cho Wordpress và Laravel
   - Cài đặt MariaDB:
      ~~~
      apt install mariadb-server -y
      ~~~
   - Truy cập vào MariaDB:
      ~~~
      mysql -u root -p
      ~~~
   - Tạo database cho Wordpress:
      ~~~
      CREATE DATABASE wordpress_db;
      CREATE USER 'wordpress_user'@'%' IDENTIFIED BY 'wordpress';
      GRANT ALL ON wordpress_db.* TO 'wordpress_user'@'%';
      FLUSH PRIVILEGES;
      ~~~
   - Tạo database cho Laravel:
      ~~~
      CREATE DATABASE laravel_db;
      CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'laravel';
      GRANT ALL ON laravel_db.* TO 'laravel_user'@'localhost';
      FLUSH PRIVILEGES;
      ~~~ 
   - Thoát khỏi dấu nhắc MySQL sử dụng: ```exit```
3. Cài đặt PHP và các phần mở rộng PHP bổ sung
   - Cài đặt PHP:
      ~~~
      apt install php -y
      ~~~
   - Cài đặt các mô-đun PHP 8 cần thiết:
      ~~~
      apt install php8.1-fpm php-mbstring php-mysql php-curl php-cli php-dev php-imagick php-soap php-zip php-xml php-imap php-xmlrpc php-gd php-opcache php-intl -y
      ~~~
   - Kiểm tra phiên bản PHP bằng lệnh sau: ```php -v```
   - Để áp dụng các thay đổi và làm cho Nginx hoạt động với phần mềm PHP, khởi động lại nginx:
      ~~~
      systemctl restart nginx
      ~~~
4. Cài đặt Composer
   - Cần cài đặt Composer chịu trách nhiệm cài đặt tất cả các thành phần của Laravel:
      ~~~
      apt-get install git composer -y
      ~~~
   - Kiểm tra phiên bản cài đặt Composer:
      ~~~
      composer --version
      ~~~
5. Tạo tệp Nginx server blocks cho Wordpress và Laravel
   - ```Wordpress: lap.wordpress2.vietnix.co```
     - Tạo thư mục "lap.wordpress2.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.wordpress2.vietnix.co
     ~~~
     - Chỉ định quyền sở hữu thư mục với biến môi trường $USER, sẽ tham chiếu người dùng hệ thống hiện tại:
     ~~~
        chown -R $USER:$USER /var/www/lap.wordpress2.vietnix.co
     ~~~
     - Tạo một tệp cấu hình server blocks mới trong thư mục "sites-available" của nginx:
     ~~~
        vi /etc/nginx/sites-available/lap.wordpress2.vietnix.co
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        server {
            listen 80;
            server_name lap.wordpress2.vietnix.co www.lap.wordpress2.vietnix.co;
            root /var/www/lap.wordpress2.vietnix.co/public;

            index index.php index.html index.htm;

            location / {
               try_files $uri $uri/ =404;
            }

            location ~ \.php$ {
               include snippets/fastcgi-php.conf;
               fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
            }

            location ~ /\.ht {
               deny all;
            }

         }
     ~~~
   - ```Laravel: lap.laravel2.vietnix.co```
     - Tạo thư mục "lap.laravel2.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.laravel2.vietnix.co
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel2.vietnix.co && \
        chmod -R 775 /var/www/lap.laravel2.vietnix.co
     ~~~
     - Tạo một tệp cấu hình server blocks mới trong thư mục "sites-available" của nginx:
     ~~~
        vi /etc/nginx/sites-available/lap.laravel2.vietnix.co
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        server {
            listen 80;
            server_name lap.laravel2.vietnix.co www.lap.laravel2.vietnix.co;
            root /var/www/lap.laravel2.vietnix.co/public;

            index index.php index.html index.htm;

            location / {
               try_files $uri $uri/ /index.php?$args;
            }

            location ~ \.php$ {
               include snippets/fastcgi-php.conf;
               fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
            }

            location ~ /\.ht {
               deny all;
            }

         }
     ~~~
   - Hủy liên kết tệp cấu hình mặc định khỏi thư mục /sites-enabled:
        ~~~
            unlink /etc/nginx/sites-enabled/default
        ~~~
   - Kích hoạt cấu hình bằng cách liên kết đến tệp cấu hình từ thư mục "sites-enabled" của Nginx:
        ~~~
            ln -s /etc/nginx/sites-available/lap.wordpress2.vietnix.co /etc/nginx/sites-enabled/
            ln -s /etc/nginx/sites-available/lap.laravel2.vietnix.co /etc/nginx/sites-enabled/
        ~~~
   - Để đảm bảo tệp cấu hình của không chứa lỗi cú pháp, hãy chạy lệnh sau:
        ~~~
            nginx -t
        ~~~
   - Tải lại Nginx để những thay đổi này có hiệu lực:
        ~~~
            systemctl reload nginx
        ~~~
6. Source wordpress và laravel 
   - ```Wordpress: -> lap.wordpress2.vietnix.co```
     - Tải gói cài đặt wordpress mới nhất:
     ~~~
        wget https://wordpress.org/latest.tar.gz
     ~~~
     - Giải nén bộ cài wordpress được tải về:
     ~~~
        tar -xvf latest.tar.gz
     ~~~
     - Sao chép toàn bộ thư mục "wordpress" vào thư mục "lap.wordpress2.vietnix.co" và đổi tên thư mục thành "public":
     ~~~
        cp -r wordpress /var/www/lap.wordpress2.vietnix.co/public
     ~~~ 
     - Gán quyền sở hữu trên toàn bộ source code wordpress:
     ~~~
        chown -R $USER:$USER /var/www/lap.wordpress2.vietnix.co
     ~~~
   - ```Laravel: -> lap.laravel.vietnix.co```
     - Điều hướng đến thư mục /var/www/lap.laravel2.vietnix.co:
     ~~~
        cd /var/www/lap.laravel2.vietnix.co
     ~~~
     - Cài đặt Laravel bằng cách sử dụng lệnh "composer create project":
     ~~~
        composer create-project laravel/laravel .
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel và gán các quyền thích hợp:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel2.vietnix.co/
        chmod -R 775 /var/www/lap.laravel2.vietnix.co/
     ~~~
   - Áp dụng các thay đổi, khởi động lại máy chủ web Apache:
        ~~~
            systemctl restart nginx
        ~~~
7. Thực hiện cài đặt Wordpress
   - Truy cập server trên trình duyệt và tiến hành cài đặt WordPress bằng cách nhập  trên thanh địa chỉ: 
        ~~~
            http://lap.wordpress2.vietnix.co
        ~~~
   - Màn hình cài đặt WordPress sẽ hiện ra chọn "Let's go!" để tiếp qua trang nhập thông tin cài đặt gồm:
     -  Database Name: Tên của cơ sở dữ liệu muốn sử dụng với WordPress, [wordpress_db]
     -  Username: Tên người dùng cơ sở dữ liệu, [wordpress_user]
     -  Password: Mật khẩu cuả người dùng cơ sở dữ liệu, [wordpress]
     -  Database Host [localhost] và Table Prefix [wp_] sử dụng mặc định.
   - Chọn "Submit", sau đó trên giao diện web thực hiện sao chép nội dung của tệp "wp-config.php" mà wordpress cung cấp.
     - Tạo tệp "wp-config.php":
     ~~~
        vi /var/www/lap.wordpress2.vietnix.co/public/wp-config.php
     ~~~
     - Dán nội dung đã có ở trên vào tệp "wp-config.php", ví dụ nội dung như sau:
     ~~~
        <?php
        /**
        * The base configuration for WordPress
        *
        * The wp-config.php creation script uses this file during the installation.
        * You don't have to use the web site, you can copy this file to "wp-config.php"
        * and fill in the values.
        *
        * This file contains the following configurations:
        *
        * * Database settings
        * * Secret keys
        * * Database table prefix
        * * ABSPATH
        *
        * @link https://wordpress.org/documentation/article/editing-wp-config-php/
        *
        * @package WordPress
        */

        // ** Database settings - You can get this info from your web host ** //
        /** The name of the database for WordPress */
        define( 'DB_NAME', 'wordpress_db' );

        /** Database username */
        define( 'DB_USER', 'wordpress_user' );

        /** Database password */
        define( 'DB_PASSWORD', 'wordpress' );

        /** Database hostname */
        define( 'DB_HOST', 'localhost' );

        /** Database charset to use in creating database tables. */
        define( 'DB_CHARSET', 'utf8mb4' );

        /** The database collate type. Don't change this if in doubt. */
        define( 'DB_COLLATE', '' );

        /**#@+
        * Authentication unique keys and salts.
        *
        * Change these to different unique phrases! You can generate these using
        * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
        *
        * You can change these at any point in time to invalidate all existing cookies.
        * This will force all users to have to log in again.
        *
        * @since 2.6.0
        */
        define( 'AUTH_KEY',         '.{}XI[WyBqE#JeGN{;I{eTAm;@)ho^ pwhyWuP-rK13-mdde .r2|7mkG=c!9E^m' );
        define( 'SECURE_AUTH_KEY',  '--&>J+H77a]+>Od]8/O|/V&.bV|Y0i*Bg[l_pMca;Gm7[a1#CEmcYgklhB6TbbrT' );
        define( 'LOGGED_IN_KEY',    'QmHN>Mn+oO0(H7&?18Y2Q^.N}fCwTtb#{xKy{m&Yo$R7`91GM5-?r=`zJK-09qch' );
        define( 'NONCE_KEY',        ',ca~0.G0p`&Czn$Um-kn-3s9f9FLJlC5D#uGzJr_DV;4;LkWt<DJj7M+Epp+#<43' );
        define( 'AUTH_SALT',        'X!ns]PlU/U(U_-Hr?Rm1Y!K.jCV[!HzB*AapdOGm6p,+/skDn}4N-UIf>patl:9/' );
        define( 'SECURE_AUTH_SALT', 'Qk92</_4;Y) ]] Rdv&-Ec)T#L9}QoT8(#81g[D`|&{c4*&=W%g,psT-.!gKBP.>' );
        define( 'LOGGED_IN_SALT',   '<$K%v:-m5t->Dym>`If|To/OkIfb/Sa~yQLC7PlG!vzp/-{!lmtH A-<Vvo~kf)S' );
        define( 'NONCE_SALT',       '.H1POnTmhe(T!;)$XV[bd8KB,tsRc&?&LP^~{Hg;}oQ%/mDdPzo0.W4}}g};5[Ba' );

        /**#@-*/

        /**
        * WordPress database table prefix.
        *
        * You can have multiple installations in one database if you give each
        * a unique prefix. Only numbers, letters, and underscores please!
        */
        $table_prefix = 'wp_';

        /**
        * For developers: WordPress debugging mode.
        *
        * Change this to true to enable the display of notices during development.
        * It is strongly recommended that plugin and theme developers use WP_DEBUG
        * in their development environments.
        *
        * For information on other constants that can be used for debugging,
        * visit the documentation.
        *
        * @link https://wordpress.org/documentation/article/debugging-in-wordpress/
        */
        define( 'WP_DEBUG', false );

        /* Add any custom values between this line and the "stop editing" line. */



        /* That's all, stop editing! Happy publishing. */

        /** Absolute path to the WordPress directory. */
        if ( ! defined( 'ABSPATH' ) ) {
            define( 'ABSPATH', __DIR__ . '/' );
        }

        /** Sets up WordPress vars and included files. */
        require_once ABSPATH . 'wp-settings.php';
     ~~~
    - Trên giao diện web của wordpress, chọn "Run the installation".
    - Tiến hành điền thông tin của website chính như Site Title, Username, Password, Email (nên dùng mail thật để cài đặt vì có thể sẽ cần để đặt lại mật khẩu nếu bị mất) và có thể tích chọn Search engine visibility.
    - Chọn "Install Wordpress" để tiến hành cài đặt.
    - Sau khi cài đặt thành công, để đăng nhập truy cập vào: 
        ~~~
           http://lap.wordpress2.vietnix.co/wp-login.php 
        ~~~
8. Cấu hình SSL tự ký cho Nginx trong Ubuntu 22.04
   - Tạo chứng chỉ và khóa SSL bằng lệnh "openssl":
      ~~~
      openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
      ~~~
     - Cả hai tệp là nginx-selfsigned.key và nginx-selfsigned.crt đã tạo sẽ được đặt trong các thư mục con thích hợp trong /etc/ssl.
     - Sau khi nhập lệnh, sẽ được đưa đến một dấu nhắc nơi có thể nhập thông tin về trang web. Danh sách đầy đủ các lời nhắc:
     ~~~
        Country Name (2 letter code) [XX]: US 
        State or Province Name (full name) []: Example 
        Locality Name (eg, city) [Default City]: Example 
        Organization Name (eg, company) [Default Company Ltd]: Example Inc 
        Organizational Unit Name (eg, section) []: Example Dept 
        Common Name (eg, your name or your server's hostname) []: your_domain_or_ip 
        Email Address []: webmaster@example.com 
     ~~~
     - Điền vào các lời nhắc một cách thích hợp. Dòng quan trọng nhất là dòng yêu cầu Common Name, cần nhập tên máy chủ mà sẽ sử dụng để truy cập máy chủ hoặc IP công khai của máy chủ.
   - Thiết lập cấu hình server blocks để sử dụng SSL:
     - Với cầu hình của "lap.wordpress2.vietnix.co", sửa đổi như sau:
     ~~~
      server {
         listen 443 ssl;

         ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
         ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
         ssl_protocols TLSv1.2 TLSv1.1 TLSv1;

         server_name lap.wordpress2.vietnix.co www.lap.wordpress2.vietnix.co;
         root /var/www/lap.wordpress2.vietnix.co/public;

         index index.php index.html index.htm;

         location / {
            try_files $uri $uri/ =404;
         }

         location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
         }

         location ~ /\.ht {
            deny all;
         }
      }
      server {
         listen 80;

         server_name lap.wordpress2.vietnix.co www.lap.wordpress2.vietnix.co;

         return 301 https://$server_name$request_uri;
      }
     ~~~
     - Với cầu hình của "lap.laravel2.vietnix.co", sửa đổi như sau:
     ~~~
      server {
         listen 443 ssl;

         ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
         ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
         ssl_protocols TLSv1.2 TLSv1.1 TLSv1;

         server_name lap.laravel2.vietnix.co www.lap.laravel2.vietnix.co;
         root /var/www/lap.laravel2.vietnix.co/public;

         index index.php index.html index.htm;

         location / {
            try_files $uri $uri/ /index.php?$args;
         }

         location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
         }

         location ~ /\.ht {
            deny all;
         }
      }
      server {
         listen 80;

         server_name lap.laravel2.vietnix.co www.lap.laravel2.vietnix.co;

         return 301 https://$server_name$request_uri;
      }
     ~~~
     - Trong đó, các tùy chọn SSL cần thiết để trỏ Apache tới chứng chỉ và khóa mới được tạo là:
      ~~~
         listen 443 ssl;
         ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
         ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
         ssl_protocols TLSv1.2 TLSv1.1 TLSv1;
      ~~~
     - Cầu hình chuyển hướng HTTP sang HTTPS:
     ~~~
      server {
            listen 80;
            server_name your_domain.com www.your_domain.com;
            return 301 https://$server_name$request_uri;
      }
     ~~~
   - Kiểm tra lại cú pháp cấu hình và tải lại Apache:
      ~~~
         nginx -t
         systemctl restart nginx
      ~~~
***
**Cài đặt cấu hình Nginx như một Reverse Proxy cho Apache trên máy chủ Ubuntu 22.04**
1. Cài đặt web server Apache
   - Cật nhật danh sách gói cục bộ và cài đặt Apache2:
      ~~~
      apt update -y && apt install apache2 -y
      ~~~
   - Điều chỉnh tường lửa:
      ~~~
      ufw allow in "Apache"
      ufw allow 8080
      ~~~
   - Cấu hình Apache để nghe thêm một cổng 8080, truy cập vào tệp "ports.conf":
      ~~~
      vi /etc/apache2/ports.conf 
      #thêm dòng sau vào tệp
      Listen 8080
      ~~~
   - Khởi động dịch vụ Apache:
      ~~~
      systemctl start apache2
      ~~~
2. Cài đặt MySQL (MariaDB), tạo database cho Wordpress và Laravel
   - Cài đặt MariaDB:
      ~~~
      apt install mariadb-server -y
      ~~~
   - Truy cập vào MariaDB:
      ~~~
      mysql -u root -p
      ~~~
   - Tạo database cho Wordpress:
      ~~~
      CREATE DATABASE wordpress_db;
      CREATE USER 'wordpress_user'@'%' IDENTIFIED BY 'wordpress';
      GRANT ALL ON wordpress_db.* TO 'wordpress_user'@'%';
      FLUSH PRIVILEGES;
      ~~~
   - Tạo database cho Laravel:
      ~~~
      CREATE DATABASE laravel_db;
      CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'laravel';
      GRANT ALL ON laravel_db.* TO 'laravel_user'@'localhost';
      FLUSH PRIVILEGES;
      ~~~ 
   - Thoát khỏi dấu nhắc MySQL sử dụng: ```exit```
3. Cài đặt PHP và các phần mở rộng PHP bổ sung
   - Cài đặt PHP:
      ~~~
      apt install software-properties-common
      add-apt-repository ppa:ondrej/php -y
      apt update
      apt install php8.2 -y
      ~~~
   - Cài đặt các mô-đun PHP 8 cần thiết:
      ~~~
      apt install php8.2-fpm libapache2-mod-php8.2 php8.2-common php8.2-mysql php8.2-xml php8.2-xmlrpc php8.2-curl php8.2-gd php8.2-imagick php8.2-cli php8.2-imap php8.2-mbstring php8.2-opcache php8.2-soap php8.2-zip php8.2-intl php8.2-bcmath unzip -y
      ~~~
   - Kiểm tra phiên bản PHP bằng lệnh sau: ```php -v```
   - Để áp dụng các thay đổi và làm cho Apache hoạt động với phần mềm PHP, khởi động lại apache2:
      ~~~
      systemctl restart apache2
      ~~~
4. Cài đặt Composer
   - Cần cài đặt Composer chịu trách nhiệm cài đặt tất cả các thành phần của Laravel:
      ~~~
      apt-get install git composer -y
      ~~~
   - Kiểm tra phiên bản cài đặt Composer:
      ~~~
      composer --version
      ~~~
5. Tạo tệp Apache Virtual Host cho Wordpress và Laravel
   - ```Wordpress: lap.wordpress.vietnix.co```
     - Tạo thư mục "lap.wordpress.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.wordpress.vietnix.co
     ~~~
     - Chỉ định quyền sở hữu thư mục với biến môi trường $USER, sẽ tham chiếu người dùng hệ thống hiện tại:
     ~~~
        chown -R www-data:www-data /var/www/lap.wordpress.vietnix.co
        chmod -R 775 /var/www/lap.wordpress.vietnix.co
     ~~~
     - Tạo một tệp cấu hình Virtual Host mới trong thư mục "sites-available" của Apache:
     ~~~
        vi /etc/apache2/sites-available/lap.wordpress.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        <VirtualHost *:8080>
            ServerName lap.wordpress.vietnix.co
            DocumentRoot /var/www/lap.wordpress.vietnix.co/public
            <Directory />
               Options FollowSymLinks
               AllowOverride None
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
         </VirtualHost>
     ~~~
   - ```Laravel: lap.laravel.vietnix.co```
     - Tạo thư mục "lap.laravel.vietnix.co" vào /var/www/:
     ~~~
        mkdir /var/www/lap.laravel.vietnix.co
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel.vietnix.co
        chmod -R 775 /var/www/lap.laravel.vietnix.co
     ~~~
     - Tạo một tệp cấu hình Virtual Host mới trong thư mục "sites-available" của Apache:
     ~~~
        vi /etc/apache2/sites-available/lap.laravel.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        <VirtualHost *:80>
            ServerName lap.laravel.vietnix.co
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/lap.laravel.vietnix.co/public
            <Directory /var/www/lap.laravel.vietnix.co>           
                Options -Indexes +FollowSymLinks +MultiViews
                AllowOverride All
                Require all granted
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>
     ~~~
   - Kích hoạt module "rewrite":
        ~~~
            a2enmod rewrite
        ~~~
   - Vô hiệu hóa trang web mặc định của Apache:
        ~~~
            a2dissite 000-default
        ~~~
   - Sử dụng "a2ensite" để kích hoạt máy chủ ảo mới:
        ~~~
            a2ensite lap.wordpress.vietnix.co.conf
            a2ensite lap.laravel.vietnix.co.conf
        ~~~
   - Để đảm bảo tệp cấu hình của không chứa lỗi cú pháp:
        ~~~
            apache2ctl configtest
        ~~~
   - Khởi động lại Apache để những thay đổi này có hiệu lực:
        ~~~
            systemctl restart apache2
        ~~~
6. Source wordpress và laravel 
   - ```Wordpress: -> lap.wordpress.vietnix.co```
     - Tải gói cài đặt wordpress mới nhất:
     ~~~
        wget https://wordpress.org/latest.tar.gz
     ~~~
     - Giải nén bộ cài wordpress được tải về:
     ~~~
        tar -xvf latest.tar.gz
     ~~~
     - Sao chép toàn bộ thư mục "wordpress" vào thư mục "lap.wordpress.vietnix.co" và đổi tên thư mục thành "public":
     ~~~
        cp -r wordpress /var/www/lap.wordpress.vietnix.co/public
     ~~~ 
     - Gán quyền sở hữu trên toàn bộ source code wordpress:
     ~~~
        chown -R www-data:www-data /var/www/lap.wordpress.vietnix.co
        chmod -R 775 /var/www/lap.wordpress.vietnix.co
     ~~~
   - ```Laravel: -> lap.laravel.vietnix.co```
     - Điều hướng đến thư mục /var/www/lap.laravel.vietnix.co:
     ~~~
        cd /var/www/lap.laravel.vietnix.co
     ~~~
     - Cài đặt Laravel bằng cách sử dụng lệnh "composer create project":
     ~~~
        composer create-project laravel/laravel .
     ~~~
     - Cấu hình quyền sở hữu cần thiết của thư mục Laravel và gán các quyền thích hợp:
     ~~~
        chown -R www-data:www-data /var/www/lap.laravel.vietnix.co/
        chmod -R 775 /var/www/lap.laravel.vietnix.co/
     ~~~
   - Áp dụng các thay đổi, khởi động lại máy chủ web Apache:
        ~~~
            systemctl restart apache2
        ~~~
7. Thực hiện cài đặt Wordpress
   - Truy cập server trên trình duyệt và tiến hành cài đặt WordPress bằng cách nhập  trên thanh địa chỉ: 
        ~~~
            http://lap.wordpress.vietnix.co
        ~~~
   - Màn hình cài đặt WordPress sẽ hiện ra chọn "Let's go!" để tiếp qua trang nhập thông tin cài đặt gồm:
     -  Database Name: Tên của cơ sở dữ liệu muốn sử dụng với WordPress, [wordpress_db]
     -  Username: Tên người dùng cơ sở dữ liệu, [wordpress_user]
     -  Password: Mật khẩu cuả người dùng cơ sở dữ liệu, [wordpress]
     -  Database Host [localhost] và Table Prefix [wp_] sử dụng mặc định.
   - Chọn "Submit".
   - Trên giao diện web của wordpress, chọn "Run the installation".
   - Tiến hành điền thông tin của website chính như Site Title, Username, Password, Email (nên dùng mail thật để cài đặt vì có thể sẽ cần để đặt lại mật khẩu nếu bị mất) và có thể tích chọn Search engine visibility.
   - Chọn "Install Wordpress" để tiến hành cài đặt.
   - Sau khi cài đặt thành công, để đăng nhập truy cập vào: 
        ~~~
           http://lap.wordpress.vietnix.co/wp-login.php 
        ~~~ 
   - Cấu hình WordPress để Reverse proxy sử dụng HTTPS bằng cách thêm vào trong wp-config.php:
      ~~~
         vi /var/www/lap.wordpress.vietnix.co/public/wp-config.php
         #Thêm những dòng này vào đầu tệp bên dưới <?php
         define('FORCE_SSL_ADMIN', true);
         if (strpos($_SERVER['HTTP_X_FORWARDED_PROTO'], 'https') !== false)
            $_SERVER['HTTPS']='on';
      ~~~    
8. Cài đặt web server Nginx
   - Cật nhật danh sách gói cục bộ và cài đặt Nginx:
      ~~~
      apt update -y && apt install nginx -y
      ~~~
   - Điều chỉnh tường lửa cho phép lưu lượng HTTPS:
      ~~~
      ufw allow in "Nginx HTTPS"
      ~~~
   - Để Nginx phục vụ PHP chính xác, cần đảm bảo lệnh fastcgi_param SCRIPT_FILENAME được đặt.
     -  Thực hiện mở tệp fastcgi_params:
     ~~~
      vi /etc/nginx/fastcgi_params
     ~~~
     -  Thêm phần sau vào cuối tập tin:
     ~~~
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
     ~~~
   - Khởi động lại nginx:
      ~~~
      systemctl restart nginx
      ~~~
9. Tạo tệp Nginx server blocks và cấu hình SSL tự ký cho Nginx
   - Tạo chứng chỉ và khóa SSL bằng lệnh "openssl":
      ~~~
      openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
      ~~~
     - Cả hai tệp là nginx-selfsigned.key và nginx-selfsigned.crt đã tạo sẽ được đặt trong các thư mục con thích hợp trong /etc/ssl.
     - Sau khi nhập lệnh, sẽ được đưa đến một dấu nhắc nơi có thể nhập thông tin về trang web. Danh sách đầy đủ các lời nhắc:
     ~~~
        Country Name (2 letter code) [XX]: US 
        State or Province Name (full name) []: Example 
        Locality Name (eg, city) [Default City]: Example 
        Organization Name (eg, company) [Default Company Ltd]: Example Inc 
        Organizational Unit Name (eg, section) []: Example Dept 
        Common Name (eg, your name or your server's hostname) []: your_domain_or_ip 
        Email Address []: webmaster@example.com 
     ~~~
     - Điền vào các lời nhắc một cách thích hợp. Dòng quan trọng nhất là dòng yêu cầu Common Name, cần nhập tên máy chủ mà sẽ sử dụng để truy cập máy chủ hoặc IP công khai của máy chủ.
   - ```Wordpress: rp.wp.vietnix.co```
     - Tạo một tệp cấu hình server blocks mới trong thư mục "sites-available" của nginx:
     ~~~
        vi /etc/nginx/sites-available/rp.wp.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
      server {
         
         listen 443 ssl;

         ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
         ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
         ssl_protocols TLSv1.2 TLSv1.1 TLSv1;

         server_name rp.wp.vietnix.co;

         location / {
            try_files $uri @apache;
         }

         location ~ ^/\.user\.ini {
            deny all;
         }

         location ~*  \.(svg|svgz)$ {
            types {}
            default_type image/svg+xml;
         }

         location = /favicon.ico {
            log_not_found off;
            access_log off;
         }

         location = /robots.txt {
            allow all;
            log_not_found off;
            access_log off;
         }

         location @apache {
            proxy_set_header X-Real-IP  $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host $host;
            proxy_pass http://127.0.0.1:8080;
         }

         location ~[^?]*/$ {
            proxy_set_header X-Real-IP  $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host $host;
            proxy_pass http://127.0.0.1:8080;
         }

         location ~ \.php$ {
            proxy_set_header X-Real-IP  $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host $host;
            proxy_pass http://127.0.0.1:8080;
         }

         location ~/\. {
            deny all;
            access_log off;
            log_not_found off;
         }
      }
     ~~~
   - ```Laravel: rp.la.vietnix.co```
     - Tạo một tệp cấu hình server blocks mới trong thư mục "sites-available" của nginx:
     ~~~
        vi /etc/nginx/sites-available/rp.la.vietnix.co.conf
     ~~~
     - Thêm vào cấu hình vào tệp:
     ~~~
        server {
            listen 443 ssl;

            ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
            ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
            ssl_protocols TLSv1.2 TLSv1.1 TLSv1;

            server_name rp.la.vietnix.co;

            location / {
            proxy_pass http://127.0.0.1:80;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_set_header X-Forwarded-Proto $scheme;
            }
        }
     ~~~
   - Hủy liên kết tệp cấu hình mặc định khỏi thư mục /sites-enabled:
        ~~~
            unlink /etc/nginx/sites-enabled/default
        ~~~
   - Kích hoạt cấu hình bằng cách liên kết đến tệp cấu hình từ thư mục "sites-enabled" của Nginx:
        ~~~
            ln -s /etc/nginx/sites-available/rp.wp.vietnix.co.conf /etc/nginx/sites-enabled/rp.wp.vietnix.co.conf 
            ln -s /etc/nginx/sites-available/rp.la.vietnix.co.conf /etc/nginx/sites-enabled/rp.la.vietnix.co.conf
        ~~~
   - Để đảm bảo tệp cấu hình của không chứa lỗi cú pháp:
        ~~~
            nginx -t
        ~~~
   - Khởi động lại Nginx để những thay đổi này có hiệu lực:
        ~~~
            systemctl restart nginx
        ~~~
***
1.  **Reset passwd mysql user root**
   - Dừng dịch vụ mysql: 
      ~~~
      service mysql stop
      ~~~
   - Cho phép xác thực "không mật khẩu" tạm thời và bắt đầu dịch vụ:
      ~~~
      cat << EOF >> /etc/mysql/my.cnf
      [mysqld]
      skip-grant-tables
      EOF
      
      service mysql start
      ~~~
   - Xác thực vào dịch vụ cơ sở dữ liệu và đặt mật khẩu mới:
      ~~~
      mysql
      FLUSH PRIVILEGES;
      ALTER USER 'root'@'localhost' IDENTIFIED BY 'New_Password';
      exit
      ~~~
   - Dừng dịch vụ MySQL:
      ~~~
      service mysql stop
      ~~~
   - Loại bỏ hai dòng cuối cùng trong tệp cấu hình my.cnf:
      ~~~
      sed -i -e '$d' /etc/mysql/my.cnf && sed -i -e '$d' /etc/mysql/my.cnf
      ~~~
   - Khởi động lại dịch vụ MySQL:
      ~~~
      service mysql start
      ~~~
   - Để kiểm tra thực hiện truy cập vào mysql:
      ~~~
      mysql -uroot -p<_new_password>
      ~~~
2.  **Mở remote mysql port 3306 cho phép bên ngoài truy cập**
   - Cấu hình máy chủ MariaDB để lắng nghe tất cả các địa chỉ IP trên hệ thống:
     - Chỉnh sửa tệp cấu hình mặc định của MariaDB:
     ~~~
      nano /etc/mysql/mariadb.conf.d/50-server.cnf
     ~~~
     - Thay đổi giá trị của "bind-address" từ 127.0.0.1 thành 0.0.0.0 để máy chủ MariaDB chấp nhận kết nối trên tất cả truy cập:
     ~~~
      bind-address = 0.0.0.0
     ~~~
     - Khởi động lại dịch vụ MariaDB để áp dụng các thay đổi:
     ~~~
      systemctl restart mariadb
     ~~~
   - Cấp quyền truy cập từ xa cho người dùng vào mysql:
     - Đăng nhập vào MariaDB shell:
     ~~~
      mysql -uroot -p
     ~~~
     - Tạo cơ sở dữ liệu "wpdb" và người dùng "wpuser" bằng lệnh sau:
     ~~~
      MariaDB [(none)]> CREATE DATABASE wpdb;
      MariaDB [(none)]> CREATE USER  'wpuser'@'localhost' IDENTIFIED BY 'password';
     ~~~
     - Cấp quyền cho truy cập từ xa để kết nối với cơ sở dữ liệu có tên wpdb là người dùng wpuser:
     ~~~
      MariaDB [(none)]> GRANT ALL ON wpdb.* to 'wpuser'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
     ~~~
     - Xóa các đặc quyền và thoát khỏi trình bao MariaDB bằng lệnh:
     ~~~
      MariaDB [(none)]> FLUSH PRIVILEGES;
      MariaDB [(none)]> EXIT;
     ~~~
     - Cấu hình tường lửa:
       - Cấp quyền truy cập từ bất kỳ địa chỉ IP tới port 3306 của mysql:
      ~~~
         ufw allow 3306
      ~~~
       - Tải lại cấu hình tường lửa:
      ~~~
         ufw reload
      ~~~
     - Kiểm tra kết nối từ hệ thống từ xa:
      ~~~
         mysql -u wpuser -h <ip_mysql_server> -p
      ~~~
3.  **Thực hiện reset pass user bất kì wordpress với Mysql**
    - Tạo mật khẩu theo mã MD5 để có thể sử dụng với WordPress, thực hiện lệnh:
    ~~~
      echo -n "matkhaumoi" | md5sum
    ~~~
      - Lưu lại kết quả đầu ra của lệnh: ```595d2ecb8a8f953ba61fb4f31729e072```  
    - Xem thông tin Database của website Worpress:
    ~~~
      grep -i 'DB_NAME' wp-config.php
    ~~~
      - Kết quả trả về có dạng như sau: ```define( 'DB_NAME', 'wordpress_db' );```
      - Trong đó "```wordpress_db```" là tên của cơ sở dữ liệu của Worpress cần xác định.
    - Truy cập vào root Mysql:
    ~~~
      mysql -u root -p
    ~~~
    - Truy cập vào database "wordpress_db":
    ~~~
      MariaDB [(none)]> use wordpress_db;
    ~~~
    - Liệt kê thông tin các user trong tables "wp_users" để xác định "ID" của user cần reset passowrd:
    ~~~
      MariaDB [wordpress_db]> SELECT ID, user_login, user_pass FROM wp_users;
    ~~~
      - Đầu ra của lệnh trên như sau:
      ~~~
         +----+------------+------------------------------------+
         | ID | user_login | user_pass                          |
         +----+------------+------------------------------------+
         |  1 | wordpress  | $P$BUXWyRGBpzxM2hmx/cSyAyvPp/Y7we/ |
         +----+------------+------------------------------------+
      ~~~
    - Reset pass của user "wordpress" có ID = 1:
    ~~~
      MariaDB [wordpress_db]> UPDATE wp_users SET user_pass= "595d2ecb8a8f953ba61fb4f31729e072" WHERE ID = 1;
    ~~~
