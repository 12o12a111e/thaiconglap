- **telnet command**:  ```telnet [ip] [port]```
  - telnet đến port 22: ```telnet 192.168.0.69 22```, kết quả cho thấy port 22 mở.
  - telnet đến port 20: ```telnet 192.168.0.69 20```, kết quả cho thấy port 20 đóng.
***
- **ping/hping3** đến domain vietnix.vn:  
  1. ```ping [ip/domain_name]```
  2. ```hping3 [option] [ip/domain_name]```
  - TTL là "Time To Live", được sử dụng để hạn chế số lần chuyển tiếp gói tin qua các mạng hoặc thiết bị mạng trên đường đi đến đích cuối cùng. Điều này giúp tránh việc gói tin bị lạc trong vòng lặp vô hạn.
  - Time thường được sử dụng để hiển thị thời gian mất đi từ khi gói tin đi, đến khi nhận được phản hồi từ mục tiêu. Đồng thời, time có thể biểu thị độ trễ của kết nối và có thể sử dụng để đánh giá hiệu suất của mạng (đơn vị milisecond). 
*** 
- **ssh command**:
  - Dùng passwond: ```ssh username@remote_host```

  - Dùng key: ```ssh -i path/to/private_key username@remote_host```

  - Dùng port custom: ```ssh -p custom_port username@remote_host```
***
- **scp command**:
  - scp 1 file: ```scp /path/to/local/file username@remote_host:/path/to/remote/directory```
  - scp 1 folder: ```scp -r /path/to/local/folder username@remote_host:/path/to/remote/directory```
***
- **rsyn command**:
  - rsync file: ```rsync /path/to/local/file username@remote_host:/path/to/remote/directory```
  - rsync folder: ```rsync -r /path/to/local/folder username@remote_host:/path/to/remote/directory```
  - rsync increamental là thực hiện chạy lệnh rsync này lần thứ hai sau khi đã sao chép lần đầu tiên, rsync sẽ chỉ sao chép những phần thay đổi trong thư mục và các tệp con của nó.
***
- **cat command**:
  - cat nội dung 1 file: ```cat path/to/file```
  - cat dòng thứ \<n> trong file: ```cat path/to/file | head -n <n> | tail -n 1```
  - cat nhiều dòng vào 1 file bằng EOF:
    ~~~ 
    cat << EOF > path/to/output_file
        Dòng 1
        Dòng 2
        Dòng 3
        ...
    EOF
    ~~~
***
- **echo command**:
  - Dùng echo để chèn thêm dòng vào cuối file:  ```echo "Dòng mới cần thêm" >> path/to/file```
  - Dùng echo để overwrite nội dung của file: ```echo "Nội dung mới" > path/to/file```
***
- **tail/head command**:
  1. tail và tail -f:
   -  tail command: ```tail [option] path/to/file``` 
   -  ```tail -f path/to/file```, liên tục hiển thị các dòng cuối cùng của tệp tin và tự động cập nhật khi có thêm dữ liệu được thêm vào tệp.
  2. head command: ```head [option] path/to/file```
***
- **sed command**:
  - Dùng sed để find và replace một string trong file: ```sed 's/pattern/replacement/' path/to/file```
***
- **netstat command**:
  - Hiển thị các socket đang listen: ```netstat -l```
  - Don't resolve hostname: ```netstat --numeric-hosts```
  - Don't resolve portname: ```netstat --numeric-ports```
  - Display process name/PID: ```netstat -p```
  - Only show tcp socket: ```netstat -t```
  - Only show udp socket: ```netstat -u```
***
- **sort command**:
  - Theo thứ tự tăng dần: ```sort path/to/file```
  - Theo thứ tự giảm dần: ```sort -r path/to/file```
  - Theo column: ```sort -k [column_index + 1] path/to/file```, cột được tính từ 1,không phải từ 0.
***
- **uniq command**:
  - Vì uniq chỉ xử lý các dòng liền kề giống nhau nên cần sắp xếp tệp tin trước đó.
  - Lọc ra các dòng lặp lại trong một file: ```sort path/to/file | uniq```
  - Lọc ra các dòng lặp lại trong file và đếm số lượng các dòng lặp lại: ```sort path/to/file | uniq -c```
***
- **wc command**:
  - Đếm số dòng trong file: ```wc -l path/to/file```
  - Đếm số kí tự trong file: ```wc -c path/to/file```
***
- **chmod, chown, chattr command**:
  - Phân quyền bằng số, phân quyền bằng chữ: 
      1. Phân quyền bằng số: ```chmod xyz path/to/file```
          - Mỗi giá trị x, y, z có thể là một trong các số sau: 0 (không có quyền), 1 (quyền thực thi), 2 (quyền ghi), 4 (quyền đọc) hoặc kết hợp của chúng (ví dụ: 7 = 4 + 2 + 1).
      2. Phân quyền bằng chữ: ```chmod [ugoa][+-=][rwx] path/to/file```
         - Sử dụng các ký hiệu u, g, o và a để biểu thị chủ sở hữu (user), nhóm (group), người dùng khác (others) và tất cả (all).
         - Các ký hiệu +, - và = dùng để thêm, bớt hoặc thiết lập quyền.
         - r là quyền read, w là quyền write, x là quyền excute.
  - Đổi owner user/group: ```chown new_owner:new_group path/to/file```
  - Set Immutable Attribute:   ```chattr +i path/to/file```
***
- **find command**:
  - find các file có đuôi .log: ```find . -type f -name "*.log"```
  - find các folder có tên abc: ```find . -type d -name "abc"```
  - find các file có tên abc: ```find . -type f -name "abc"```
  - find các file có tên abc và thực hiện phần quyền read only cho file: ```find . -type f -name "abc" -exec chmod a=r {} \;```
***
- **cp command**:
  - cp file: ```cp path/to/source_file path/to/destination_directory```
  - cp folder: ```cp -r path/to/source_directory path/to/destination_directory```
***
- **mv command**:
  - mv file: ```mv path/to/source_file path/to/destination_directory```
  - mv folder: ```mv path/to/source_directory path/to/destination_directory```
***
- **cut command**:
  - cut kí tự thứ \<n> trong string: ```cut -c n path/to/file```
  - cut từ kí tự thứ \<n> trở về sau: ```cut -c n- path/to/file```
  - cut từ kí tự thứ \<n> trở về trước: ```cut -c 1-n path/to/file```
***
- **dig command**:
  - Dùng Dig command để kiểm tra resolv record A, MX, NS: ```dig [ip/domain_name]```
  - Dùng Dig command để kiểm tra resolv record A, MX, NS với custom DNS: ```dig [ip/domain_name] @[ip_custom_dns_server]```
***
- **tar/zip/unzip command**:
  - Nén/Giải nén file tar.gz:
    - Nén: ```tar -czvf archive_name.tar.gz path/to/file_or_directory```
    - Giải nén: ```tar -xzvf archive_name.tar.gz```
  - Nén/Giải nén file .zip:
    - Nén:  ```zip archive_name.zip path/to/file_or_directory```
    - Giải nén: ```unzip archive_name.zip```
***
- **mount/umount command**:
  - Add thêm một ổ cứng sdb ~ 5gb:
    - Tạo phân vùng 'dev/sdb1' và định dạng hệ thống tệp tin etx4.
  - Kiểm tra được có bao nhiêu ổ cứng trên máy chủ: ```lsblk``` hoặc ```fdisk -l```
  - Mount ổ cứng vào /mnt/test: ```mount /dev/sdb1 /mnt/test``` 
  - Umount /mnt/test: ```umount /mnt/test```
***
- **Symbolic Links, Hard Links command**:
  - Định nghĩ Sym Link:
    - Là một liên kết trỏ đến một tập tin hoặc thư mục khác bằng cách sử dụng một đường dẫn tương đối hoặc đường dẫn tuyệt đối.
    - Nếu xóa tập tin hoặc thư mục gốc mà liên kết trỏ đến, Sym Link vẫn tồn tại, nhưng không còn hợp lệ.
  - Định nghĩ Hard Link:
    - Là một liên kết trực tiếp đến một tập tin hoặc thư mục, không sử dụng đường dẫn như Sym Link.
    - Nếu xóa tập tin hoặc thư mục gốc, các Hard Link vẫn tồn tại và vẫn hợp lệ.
  - Ví dụ về Sym Link và Hard Link:
    - Tạo Sym link: ```ln -s file1.txt symlink_file```, khi mở "symlink_file", nó sẽ trỏ đến "file1.txt" và hiển thị nội dung của nó.
    - Tạo Hard link: ```ln file1.txt hardlink_file```, bất kỳ thay đổi nào trong một tập tin sẽ ảnh hưởng trực tiếp đến tập tin còn lại.
***
- **ls command**:
  - Liệt kê danh sách file/thư mục: ```ls```
  - Liệt kê danh sách file/thư mục và thuộc tính: ```ls -l```
  - Show file ẩn: ```ls -a```
***
- **ps command**:
  - Show tiến trình: ```ps```
  - Kill tiến trình: ```kill PID```
***
- **top command**:
  - Kiểm tra tài nguyên cpu đang sử dụng của một vài process đang chạy: ```top -p PID1,PID2,PID3,...```
  - Giải thích về Load average, us, sy, ni, id, wa, hi, si, st, zombie process, sleeping process:
    - Load average: chỉ số tải trung bình của hệ thống.
    - us (user): phần trăm thời gian CPU được sử dụng bởi các tiến trình của người dùng.
    - sy (system): phần trăm thời gian CPU được sử dụng bởi các tiến trình hệ thống.
    - ni (nice): phần trăm thời gian CPU được sử dụng bởi các tiến trình có độ ưu tiên cao.
    - id (idle): phần trăm thời gian CPU không sử dụng.
    - wa (wait): phần trăm thời gian CPU đang chờ đợi I\/O.
    - hi (hardware interrupts): phần trăm thời gian CPU đang xử lý các ngắt phần cứng.
    - si (software interrupts): phần trăm thời gian CPU đang xử lý các ngắt phần mềm.
    - st (stolen time): phần trăm thời gian CPU bị "stolen" bởi máy ảo khi sử dụng công nghệ ảo hóa.
    - Zombie process: là các tiến trình đã kết thúc nhưng vẫn còn lại trong bảng tiến trình với thông tin PID và sử dụng tài nguyên nhưng không thế chạy.
    - Sleeping process: là các tiến trình đang chờ một sự kiện nào đó hoặc hoạt động không gây ảnh hưởng đến CPU.
***
- **free command**: ```free```
  - Giải thích ram used, free, shared, buff/cache, free:
    - used: tổng dung lượng bộ nhớ đã được sử dụng.
    - free: tổng dung lượng bộ nhớ còn trống và sẵn sàng sử dụng.
    - shared: dung lượng bộ nhớ được sử dụng bởi các tiến trình được chia sẻ với các tiến trình khác.
    - buff/cache: dung lượng bộ nhớ được sử dụng cho bộ đệm và bộ nhớ cache.
***
- **df command**:
  - Xem dung lượng disk: ```df```
  - Phân vùng / là: 
    - Phân vùng gốc, nơi chứa hệ điều hành và các thư mục hệ thống chính như /bin, /etc, /home và nhiều thư mục quan trọng khác. 
    - Có thể là một phân vùng riêng biệt trên ổ cứng hoặc có thể được gộp vào phân vùng chung như (LVM) hoặc các giải pháp RAID.
  - Phân vùng tmpfs là:
    - Phân vùng được lưu trữ trong bộ nhớ RAM, vì nằm trên bộ nhớ RAM nên tốc độ truy cập vào phân vùng tmpfs rất nhanh.
    - Sử dụng để lưu trữ giữ liệu tạm thời và các tệp tin tạm thời.