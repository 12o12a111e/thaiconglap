Compile nginx với 3 module testcookie, pagespeed, real_ip. 
***
***Centos 7***
1. **Tải mã nguồn và các phụ thuộc của mã nguồn**
   - NGINX được viết bằng ngôn ngữ C, vì vậy để compile NGINX cần phải cài C compiler (GCC) và các thành phần khác:
   ~~~
   yum install gcc gcc-c++ glibc-devel make openssl-devel zlib-devel pcre-devel libuuid-devel unzip git wget -y
   ~~~
   - Tạo một thư mục mới trong thư mục home để tải source package cho Nginx và di chuyển đến thư mục vừa tạo:
   ~~~
   mkdir /home/custom-nginx && cd /home/custom-nginx
   ~~~
   - Tải xuống source package Nginx từ trang web chính thức của Nginx, sử dụng phiên bản 1.9.11:
   ~~~
   wget 'http://nginx.org/download/nginx-1.9.11.tar.gz'
   ~~~
   - Giải nén package vừa tải xuống:
   ~~~
   tar -xzvf nginx-1.9.11.tar.gz
   ~~~
   - Để bắt đầu thêm các module, cần truy cập vào thư mục modules trong thư mục được giải nén nginx-1.9.11:
   ~~~
   cd nginx-1.9.11/src/http/modules/
   ~~~
   - Tải module testcookie:
   ~~~
   git clone https://github.com/kyprizel/testcookie-nginx-module.git
   ~~~
   - Tải module pagespeed mới nhất từ Github repository:
   ~~~
   wget https://github.com/apache/incubator-pagespeed-ngx/archive/latest-stable.zip
   ~~~
   - Giải nén package vừa tải xuống của module pagespeed:
   ~~~
   unzip latest-stable.zip
   ~~~
   - Để thuận tiện, đổi tên thư mục vừa giải nén thành ngx_pagespeed và truy cập vào thư mục mới ngx_pagespeed:
   ~~~
   mv incubator-pagespeed-ngx-latest-stable ngx_pagespeed && cd ngx_pagespeed
   ~~~
   - Tải xuống các PageSpeed Optimization Libraries (psol) cần thiết cho việc biên dịch:
   ~~~
   wget https://dl.google.com/dl/page-speed/psol/1.13.35.2-x64.tar.gz
   ~~~
   - Giải nén psol package vừa tải xuống:
   ~~~
   tar -xzvf 1.13.35.2-x64.tar.gz
   ~~~ 
2. **Cấu hình mã nguồn và biên dịch**
   - Đến thư mục chính của mã nguồn Nginx:
   ~~~
   cd /home/custom-nginx/nginx-1.9.11
   ~~~
   - Cấu hình mã nguồn:
     - Với ```--with-http_realip_module```, thiết lập module realip.
     - Với ```--add-dynamic-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/testcookie-nginx-module/```, thiết lập module testcookie.
     - Với ```--add-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/ngx_pagespeed```, thiết lập module pagespeed.
   ~~~
   ./configure --with-http_realip_module --add-dynamic-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/testcookie-nginx-module/ --add-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/ngx_pagespeed --user=nobody --group=nobody --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid
   ~~~
   - Cấu hình hoàn tất, bắt đầu quá trình biên dịch và cài đặt phần mềm:
   ~~~
   make && make install
   ~~~
   - Nginx đã tùy chỉnh được cài đặt trong thư mục /usr/local/nginx, tạo hai symbolic link.
   ~~~
   ln -s /usr/local/nginx/conf/ /etc/nginx
   ln -s /usr/local/nginx/sbin/nginx /usr/sbin/nginx
   ~~~
3. **Tạo Startup Script**
   - Tạo một file mới là 'nginx' trong thư mục /etc/init.d/:
   ~~~
   vi /etc/init.d/nginx
   ~~~ 
   - Thực hiện sao chép sao chép script bên dưới vào file nginx:
   ~~~
   #!/bin/sh
   #
   # nginx - this script starts and stops the nginx daemon
   #
   # chkconfig:   - 85 15
   # description:  NGINX is an HTTP(S) server, HTTP(S) reverse proxy and IMAP/POP3 proxy server
   # processname: nginx
   # config:      /etc/nginx/nginx.conf
   # config:      /etc/sysconfig/nginx
   # pidfile:     /var/run/nginx.pid

   # Source function library.
   . /etc/rc.d/init.d/functions

   # Source networking configuration.
   . /etc/sysconfig/network

   # Check that networking is up.
   [ "$NETWORKING" = "no" ] && exit 0

   nginx="/usr/sbin/nginx"
   prog=$(basename $nginx)

   NGINX_CONF_FILE="/etc/nginx/nginx.conf"

   [ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx

   lockfile=/var/lock/subsys/nginx

   make_dirs() {
      # make required directories
      user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
      if [ -n "$user" ]; then
         if [ -z "`grep $user /etc/passwd`" ]; then
            useradd -M -s /bin/nologin $user
         fi
         options=`$nginx -V 2>&1 | grep 'configure arguments:'`
         for opt in $options; do
            if [ `echo $opt | grep '.*-temp-path'` ]; then
               value=`echo $opt | cut -d "=" -f 2`
               if [ ! -d "$value" ]; then
                     # echo "creating" $value
                     mkdir -p $value && chown -R $user $value
               fi
            fi
         done
      fi
   }

   start() {
      [ -x $nginx ] || exit 5
      [ -f $NGINX_CONF_FILE ] || exit 6
      make_dirs
      echo -n $"Starting $prog: "
      daemon $nginx -c $NGINX_CONF_FILE
      retval=$?
      echo
      [ $retval -eq 0 ] && touch $lockfile
      return $retval
   }

   stop() {
      echo -n $"Stopping $prog: "
      killproc $prog -QUIT
      retval=$?
      echo
      [ $retval -eq 0 ] && rm -f $lockfile
      return $retval
   }

   restart() {
      configtest || return $?
      stop
      sleep 1
      start
   }

   reload() {
      configtest || return $?
      echo -n $"Reloading $prog: "
      killproc $prog -HUP
      retval=$?
      echo
   }

   force_reload() {
      restart
   }

   configtest() {
   $nginx -t -c $NGINX_CONF_FILE
   }

   rh_status() {
      status $prog
   }

   rh_status_q() {
      rh_status >/dev/null 2>&1
   }

   case "$1" in
      start)
         rh_status_q && exit 0
         $1
         ;;
      stop)
         rh_status_q || exit 0
         $1
         ;;
      restart|configtest)
         $1
         ;;
      reload)
         rh_status_q || exit 7
         $1
         ;;
      force-reload)
         force_reload
         ;;
      status)
         rh_status
         ;;
      condrestart|try-restart)
         rh_status_q || exit 0
               ;;
      *)
         echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
         exit 2
   esac
   ~~~
   - Thiết lập quyền thực thi cho script:
   ~~~
   chmod +x /etc/init.d/nginx
   ~~~
   - Khởi động Nginx:
   ~~~
   service nginx start
   ~~~
   - Thêm Nginx vào các runlevel mặc định:
   ~~~
   chkconfig nginx on
   ~~~
4. **Kích hoạt module**
   - Kích hoạt module ngx_pagespeed, tạo một thư mục để lưu trữ bộ nhớ cache cho trang web:
   ~~~
   mkdir -p /var/ngx_pagespeed_cache
   ~~~
   - Thay đổi quyền sở hữu của thư mục tren cho Nginx user để web server có thể lưu trữ các file:
   ~~~
   chown -R nobody:nobody /var/ngx_pagespeed_cache
   ~~~
   - Mở file cấu hình chính của Nginx tại /etc/nginx/nginx.conf:
   ~~~
   vi /etc/nginx/nginx.conf
   ~~~
   - Trong file cấu hình, thêm các dòng dưới đây vào block ```server```:
   ~~~
   pagespeed on;
   pagespeed FileCachePath /var/ngx_pagespeed_cache;
   location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" { add_header "" ""; }
   location ~ "^/ngx_pagespeed_static/" { }
   location ~ "^/ngx_pagespeed_beacon" { }
   ~~~
   - Kích hoạt module ngx_http_testcookie_access_module, thêm vào đầu tệp cấu hình nginx.conf dòng lệnh sau:
   ~~~
   load_module "modules/ngx_http_testcookie_access_module.so";
   ~~~
   - Khởi động lại máy chủ Nginx để các thay đổi có hiệu lực:
   ~~~
   sudo service nginx restart
   ~~~
5. **Kiểm tra cài đặt**
   - Kiểm tra xem các module đã được cài đặt thành công hay chưa:
   ~~~
   /usr/sbin/nginx -V
   ~~~
   - Kết quả cài đặt thành công, thấy các module được liệt kê giữa các đối số tùy chỉnh khác:
   ~~~
   nginx version: nginx/1.9.11
   built by gcc 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC) 
   configure arguments: --with-http_realip_module --add-dynamic-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/testcookie-nginx-module/ --add-module=/home/custom-nginx/nginx-1.9.11/src/http/modules/ngx_pagespeed --user=nobody --group=nobody --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid
   ~~~
   - Xác định pagespeed được kích hoạt và hoạt động cho trang web bằng cách sử dụng ```curl``` để kiểm tra tiêu đề ```X-Page-Speed```:
   ~~~
   curl -I -p http://localhost| grep X-Page-Speed
   ~~~
   - Module ngx_pagespeed hoạt động tốt, kết quả trả về X-Page-Speed xuất hiện cùng với phiên bản:
   ~~~
   X-Page-Speed: 1.13.35.2-0
   ~~~
***
***Ubuntu 22.04***
1. **Tải mã nguồn và các phụ thuộc của mã nguồn**
   - NGINX được viết bằng ngôn ngữ C, vì vậy để compile NGINX cần phải cài C compiler (GCC) và các thành phần khác:
   ~~~
   apt install build-essential gcc zlib1g-dev libpcre3 libpcre3-dev uuid-dev libssl-dev make unzip git wget -y
   ~~~
   - Di chuyển đến thư mục /opt để tải source package cho Nginx:
   ~~~
   cd /opt
   ~~~
   - Tải xuống source package Nginx từ trang web chính thức của Nginx, sử dụng phiên bản 1.23.4:
   ~~~
   wget 'http://nginx.org/download/nginx-1.23.4.tar.gz'
   ~~~
   - Giải nén package vừa tải xuống:
   ~~~
   tar -xzvf nginx-1.23.4.tar.gz
   ~~~
   - Để bắt đầu thêm các module, cần truy cập vào thư mục modules trong thư mục được giải nén nginx-1.23.4:
   ~~~
   cd nginx-1.23.4/src/http/modules/
   ~~~
   - Tải module testcookie:
   ~~~
   git clone https://github.com/kyprizel/testcookie-nginx-module.git
   ~~~
   - Tải module pagespeed từ Github repository:
   ~~~
   git clone --depth=1 https://github.com/apache/incubator-pagespeed-ngx.git
   ~~~
   - Để thuận tiện, đổi tên thư mục vừa giải nén thành ngx_pagespeed và truy cập vào thư mục mới ngx_pagespeed:
   ~~~
   mv incubator-pagespeed-ngx ngx_pagespeed && cd ngx_pagespeed
   ~~~
   - Tải xuống các PageSpeed Optimization Libraries (psol) cần thiết cho việc biên dịch:
   ~~~
   wget http://www.tiredofit.nl/psol-jammy.tar.xz
   ~~~
   - Giải nén psol package vừa tải xuống:
   ~~~
   tar xvf psol-jammy.tar.xz
   ~~~ 
2. **Cấu hình mã nguồn và biên dịch**
   - Đến thư mục chính của mã nguồn Nginx:
   ~~~
   cd /opt/nginx-1.23.4
   ~~~
   - Cấu hình mã nguồn:
     - Với ```--with-http_realip_module```, thiết lập module realip.
     - Với ```--add-dynamic-module=/opt/nginx-1.23.4/src/http/modules/testcookie-nginx-module/```, thiết lập module testcookie.
     - Với ```--add-module=/opt/nginx-1.23.4/src/http/modules/ngx_pagespeed```, thiết lập module pagespeed.
   ~~~
   ./configure --with-http_realip_module --add-dynamic-module=/opt/nginx-1.23.4/src/http/modules/testcookie-nginx-module/ --add-module=/opt/nginx-1.23.4/src/http/modules/ngx_pagespeed --prefix=/opt/nginx --user=nginx --group=nginx --with-compat
   ~~~
   - Cấu hình hoàn tất, bắt đầu quá trình biên dịch và cài đặt phần mềm:
   ~~~
   make && make install
   ~~~
   - Nginx đã tùy chỉnh được cài đặt trong thư mục /opt/nginx, tạo hai symbolic link.
   ~~~
   ln -s /opt/nginx/conf/ /etc/nginx
   ln -s /opt/nginx/sbin/nginx /usr/sbin/nginx
   ~~~
3. **Tạo Startup Script**
   - Tạo một file mới là 'nginx' trong thư mục /etc/init.d/:
   ~~~
   vi /etc/init.d/nginx
   ~~~ 
   - Thực hiện sao chép sao chép script bên dưới vào file nginx:
   ~~~
   #! /bin/sh
   ### BEGIN INIT INFO
   # Provides:          nginx
   # Required-Start:    $all
   # Required-Stop:     $all
   # Default-Start:     2 3 4 5
   # Default-Stop:      0 1 6
   # Short-Description: starts the nginx web server
   # Description:       starts nginx using start-stop-daemon
   ### END INIT INFO

   PATH=/opt/nginx/sbin:/sbin:/bin:/usr/sbin:/usr/bin
   DAEMON=/opt/nginx/sbin/nginx
   NAME=nginx
   DESC=nginx

   test -x $DAEMON || exit 0

   # Include nginx defaults if available
   if [ -f /etc/default/nginx ] ; then
         . /etc/default/nginx
   fi

   set -e

   case "$1" in
   start)
         echo -n "Starting $DESC: "
         start-stop-daemon --start --quiet --pidfile /opt/nginx/logs/$NAME.pid \
                  --exec $DAEMON -- $DAEMON_OPTS
         echo "$NAME."
         ;;
   stop)
         echo -n "Stopping $DESC: "
         start-stop-daemon --stop --quiet --pidfile /opt/nginx/logs/$NAME.pid \
                  --exec $DAEMON
         echo "$NAME."
         ;;
   restart|force-reload)
         echo -n "Restarting $DESC: "
         start-stop-daemon --stop --quiet --pidfile \
                  /opt/nginx/logs/$NAME.pid --exec $DAEMON
         sleep 1
         start-stop-daemon --start --quiet --pidfile \
                  /opt/nginx/logs/$NAME.pid --exec $DAEMON -- $DAEMON_OPTS
         echo "$NAME."
         ;;
   reload)
            echo -n "Reloading $DESC configuration: "
            start-stop-daemon --stop --signal HUP --quiet --pidfile     /opt/nginx/logs/$NAME.pid \
               --exec $DAEMON
            echo "$NAME."
            ;;
         *)
               N=/etc/init.d/$NAME
               echo "Usage: $N {start|stop|restart|reload|force-reload}" >&2
               exit 1
               ;;
      esac

      exit 0
   ~~~
   - Thiết lập quyền thực thi cho script:
   ~~~
   chmod +x /etc/init.d/nginx
   ~~~
   - Để systemd cập nhật lại thông tin cấu hình mà không cần khởi động lại hệ thống:
   ~~~
   systemctl daemon-reload
   ~~~
   - Khởi động Nginx:
   ~~~
   service nginx start
   ~~~
   - Cấu hình cho việc khởi động tự động của dịch vụ nginx trong hệ thống khi khởi động:
   ~~~
   /usr/sbin/update-rc.d -f nginx defaults
   ~~~
4. **Kích hoạt module**
   - Tạo một tài khoản người dùng "nginx" thuộc nhóm "nginx" để thực hiện các tác vụ liên quan đến dịch vụ nginx, nhưng không cần đăng nhập hoặc có thư mục home:
   ~~~
   adduser --system --no-create-home --disabled-login --disabled-password --group nginx
   ~~~
   - Kích hoạt module ngx_pagespeed, tạo một thư mục để lưu trữ bộ nhớ cache cho trang web:
   ~~~
   mkdir -p /var/ngx_pagespeed_cache
   ~~~
    - Thay đổi quyền sở hữu của thư mục trên cho Nginx user để web server có thể lưu trữ các file:
   ~~~
   chown -R nginx:nginx /var/ngx_pagespeed_cache
   ~~~
   - Mở file cấu hình chính của Nginx tại /etc/nginx/nginx.conf:
   ~~~
   vi /etc/nginx/nginx.conf
   ~~~
   - Trong file cấu hình, thêm các dòng dưới đây vào block ```server```:
   ~~~
   pagespeed on;
   pagespeed FileCachePath /var/ngx_pagespeed_cache;
   location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" { add_header "" ""; }
   location ~ "^/ngx_pagespeed_static/" { }
   location ~ "^/ngx_pagespeed_beacon" { }
   ~~~
   - Kích hoạt module ngx_http_testcookie_access_module, thêm vào đầu tệp cấu hình nginx.conf dòng lệnh sau:
   ~~~
   load_module "modules/ngx_http_testcookie_access_module.so";
   ~~~
   - Khởi động lại máy chủ Nginx để các thay đổi có hiệu lực:
   ~~~
   sudo service nginx restart
   ~~~
5. **Kiểm tra cài đặt**
   - Kiểm tra xem các module đã được cài đặt thành công hay chưa:
   ~~~
   nginx -V
   ~~~
   - Kết quả cài đặt thành công, thấy các module được liệt kê giữa các đối số tùy chỉnh khác:
   ~~~
   nginx version: nginx/1.23.4
   built by gcc 11.4.0 (Ubuntu 11.4.0-1ubuntu1~22.04) 
   configure arguments: --prefix=/opt/nginx --user=nginx --group=nginx --with-compat --with-http_realip_module --add-dynamic-module=/opt/nginx-1.23.4/src/http/modules/testcookie-nginx-module/ --add-module=/opt/nginx-1.23.4/src/http/modules/ngx_pagespeed
   ~~~
   - Xác định pagespeed được kích hoạt và hoạt động cho trang web bằng cách sử dụng ```curl``` để kiểm tra tiêu đề ```X-Page-Speed```:
   ~~~
   curl -I -p http://localhost| grep X-Page-Speed
   ~~~
   - Module ngx_pagespeed hoạt động tốt, kết quả trả về X-Page-Speed xuất hiện cùng với phiên bản:
   ~~~
   X-Page-Speed: 1.15.0.0-8917
   ~~~
***
 Cách hoạt động của từng module testcookie, pagespeed và real_ip trong Nginx:

1. **Module testcookie:**
    được sử dụng để bảo vệ trang web khỏi các tấn công bằng cách kiểm tra tính hợp lệ của các yêu cầu HTTP. Module này thường được sử dụng để phát hiện và ngăn chặn các tấn công chống lại dịch vụ (DDoS), tấn công quét (port scanning) và tấn công tràn bộ nhớ đệm (buffer overflow attacks). Cách hoạt động chính của module testcookie bao gồm:

   - **Thêm Cookie kiểm tra**: Khi một yêu cầu đến, Nginx sẽ thêm một Cookie có giá trị ngẫu nhiên vào header của yêu cầu này.
   - **Kiểm tra trong yêu cầu tiếp theo**: Khi yêu cầu tiếp theo đến, Nginx sẽ kiểm tra xem liệu Cookie với giá trị ngẫu nhiên có tồn tại hay không.
   - **Phân loại yêu cầu**: Nếu Cookie không tồn tại, hoặc nếu nó không hợp lệ, Nginx có thể giả sử đó là yêu cầu không hợp lệ và thực hiện các hành động như chặn yêu cầu đó hoặc đưa ra các biện pháp phòng thủ khác.

2. **Module pagespeed:**
    được sử dụng để tối ưu hóa tốc độ và hiệu suất của trang web bằng cách thực hiện các biện pháp tối ưu hóa tự động trong quá trình phản hồi HTTP. Các biện pháp tối ưu bao gồm tối ưu hóa ảnh, tối ưu hóa CSS và JavaScript, caching thông minh và nhiều biện pháp khác. Cách hoạt động chính của module pagespeed bao gồm:

   - **Chuyển đổi và tối ưu mã nguồn**: Module này có thể tự động chuyển đổi mã nguồn của trang web để loại bỏ khoảng trắng, tối ưu hóa mã CSS và JavaScript.
   - **Tối ưu hóa ảnh**: Module tự động tối ưu hóa ảnh để giảm dung lượng, cải thiện tốc độ tải.
   - **Minify CSS và JavaScript**: Tự động loại bỏ các dấu cách và ký tự không cần thiết từ mã nguồn CSS và JavaScript.
   - **Caching thông minh**: Lưu trữ tài nguyên tối ưu hóa trong bộ nhớ cache của trình duyệt để tăng tốc độ tải trang lần sau.

3. **Module real_ip:**
    được sử dụng để xác định địa chỉ IP thực sự của khách hàng khi máy chủ Nginx đứng sau một proxy hoặc tường lửa. Trong môi trường này, địa chỉ IP thực sự của người dùng sẽ bị che giấu bởi địa chỉ IP của proxy. Cách hoạt động chính của module real_ip bao gồm:

   - **Xác định địa chỉ IP từ header**: Module này cho phép chỉ định header HTTP chứa địa chỉ IP thực sự của người dùng.
   - **Sửa đổi địa chỉ IP trong yêu cầu**: Khi yêu cầu đến, module real_ip sẽ sửa đổi địa chỉ IP trong yêu cầu để thay thế bằng địa chỉ IP thực sự.
   - **Định dạng địa chỉ IP**: Có thể cấu hình module để xử lý các header proxy khác nhau và xác định địa chỉ IP dựa trên định dạng của header đó.
