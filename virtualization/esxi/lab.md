***
Setup Node ESXi
1. Tải xuống ESXi ISO Image tại: https://customerconnect.vmware.com/downloads
   - Cần đăng ký một tài khoản trên trang chủ của VMWare để có thể thực hiên tải tệp iso của  VMWare ESXi.
2. Khởi động từ USB hoặc CD/DVD
   - Trước khi khởi động cần tùy chỉnh cấu hình máy ảo VM trên Proxmox như sau:
     - Với "System", "SCSI Controller: MegaRAID SAS"
     - Với "Disks", "Bus/Device: SATA"
     - Với "CPU", "Type: host".
     - Với "Network", "Model: VMware vmxnet3".
   - Tại màn hình menu boot device, chọn bản cài đặt "ESXi-..-standard installer".
   - Chương trình cài đặt sẽ xuất nột dung chào mừng và đến với phần cài đặt ESXi. Nhấn "(Enter) Continue" để tiếp tục.
   - Chấp nhận End User License Agreement (EULA), nhấn "F11",
   - Chọn ổ cứng để tiến hành cài đặt chương trình ESXi. Để tiến hành cài đặt, nhấn "Enter".
   - Chọn ngôn ngữ hiển thị, nhấn "Enter".
   - Cấu hình mật khẩu cho user "root", nhấn "Enter".
   - Ổ cứng đã được tái phân vùng, xác nhận cài đặt nhấn "F11".
   - Quá trình cài đặt ESXi hoàn tất, nhấn "Enter" để tiến hành khởi động lại hệ thống.
   - Sau khi reboot hoàn tất, truy cập vào giao diện Direct Console User Interface (DCUI) của ESXi. 
   - Nhấn "F2" để trup cập vào bên trong DCUI gồm các chức năng cấu hình cơ bản cho ESXi, cần nhập user/pass của người dùng "root".
3. Truy cập ESXi Web Client
   -  ESXi Web Client dùng để quản lý ESXi.
   -  Truy cập địa chỉ IP quản lý trong giao diện DCUI, tiến hành đăng nhập bằng user "root".
***
**Tạo user mới Node ESXi**
- Trong giao diện ESXi Web Client, ở "Navigator" truy cập vào "Host" => "Manage".
- Tại "Manage", truy cập vào "Security & users".
- Tại "Security & users", truy cập vào "Users" và nhấn chọn "Add user".
- Điền các thông tin như User name, Description, Password, Confirm Password.
- Nhấn "Add" để tạo user mới.
***
**Tạo datastore**
- Trong giao diện ESXi Web Client, ở "Navigator" truy cập vào "Storage".
- Tại "Storage", truy cập vào "Datastores" và nhấn chọn "New datastore".
  - Với "Select creation type", chọn "Create new VMFS datastore" và nhấn "Next".
  - Với "Select device", điền tên của datastore và chọn ổ đĩa để tạo phân vùng VMFS datastore mới, sau đó nhấn "Next".
  - Với "Select partitioning options", chọn cách muốn phân vùng thiết bị: "User full disk" hoặc "Custom" và chuẩn của VMFS: "VMFS 5/6". Nhấn "Next" để tiếp tục.
  - Với "Ready to complete", kiểm tra lại cấu hình đã thiết lập và chọn "Finish" để tạo datastore mới.
***
**Tạo Port group**
- Trong giao diện ESXi Web Client, ở "Navigator" truy cập vào "Networking".
- Tại "Networking", truy cập vào "Port groups" và nhấn chọn "Add port group".
- Thực hiện điền tên của port group, chọn Vlan ID, chọn Virtual switch, chọn các chế độ Sercurity:
  -  Promiscuous Mode: Khi bật chế độ này, mọi máy ảo trong cùng mạng ảo đều có thể nhìn thấy tất cả các gói dữ liệu trên mạng ảo này, bất kể chúng có được gửi tới máy ảo đó hay không.
  -  MAC Address Changes: Chế độ này quyết định liệu các máy ảo có thể thay đổi địa chỉ MAC hay không.
  -  Forged Transmits: Tùy chọn này xác định xem máy ảo có được phép giả mạo nguồn của các gói dữ liệu gửi đi hay không.
- Nhấn "Add" để thêm port group mới.     
***
**Upload/Boot ISO cho máy ảo**
- Trong giao diện ESXi Web Client, ở "Navigator" truy cập vào "Storage".
- Tại "Storage", truy cập vào "Datastores" và nhấn chọn "Datastore browser".
- Cửa sổ trình duyệt "datastore" mở ra, chọn "datastore" lưu trữ tệp ISO và nhấn "Upload".
- Chọn ISO cần tải lên và nhấn "Open" để thực hiện upload ISO.
***
**Setup máy ảo trên Vmware ESXi**
- Trong giao diện ESXi Web Client, ở "Navigator" truy cập vào "Virtual Machines".
- Tại "Virtual Machines", nhấn chọn "Create / Register VM".
- Trình hướng dẫn tạo máy ảo mở ra, thực hiện:
  - Với "Select creation type", chọn phương thức "Create a new virtual machine" cho phép cấu hình thủ công các cài đặt và cấu hình phần cứng của máy ảo. Nhấn "Next".
  - Với "Select a name and guest OS"
    - Name: nhập tên của máy ảo, tên phải là duy nhất trong số tên của tất cả các máy ảo hiện có.
    - Trong danh sách "Compatibility", chọn ESXi 6.5.
    - Trong danh sách "Guest OS Family", chọn Windows.
    - Trong danh sách "Guest OS Version", chọn Microsoft Windows Server 2016 (64-bit).
    - Nhấn "Next".
  - Với "Select storage", chọn nơi lưu trữ từ danh sách các datastore có sẵn.
  - Với "Customize settings", cấu hình phần cứng máy ảo:
    - Cài đặt CPU, chọn số lượng bộ xử lý ảo cần thiết.
    - Cài đặt RAM, chỉ định dung lượng RAM sẽ được phân bổ cho máy ảo.
    - Cài đặt "Hard Disk 1", chỉ định dung lượng ổ đĩa sẽ được phân bổ cho máy ảo.
    - Cài đặt "Network Adapter 1", chọn mạng ảo mà máy ảo sẽ kết nối.
    - Cài đặt "CD/DVD Drive 1", thực hiện:
      - Chọn loại của "Datastore ISO File".
      - Nhấn vào "Browse", cửa sổ chọn tập tin sẽ mở ra. 
      - Chọn tệp ISO đã được tải lên trước khi bắt đầu cài đặt và nhấp vào OK.
      - Chọn "Connect at power on".
    - Nhấn "Next".
  - Với "Ready to complete", xác minh rằng cài đặt máy ảo được cấu hình ở các bước trước đó là chính xác, nhấn vào "Finish".
***
**Tìm hiểu vmware tool**
- VMware Tools là một phần quan trọng để đảm bảo máy ảo hoạt động hiệu quả và đáng tin cậy trong môi trường ảo hóa.
- VMware Tools là một bộ công cụ cung cấp tích hợp và tối ưu hóa cho việc chạy hệ điều hành và ứng dụng trên các máy ảo được tạo ra bởi phần mềm ảo hóa VMware. 
- VMware Tools cung cấp các chức năng quan trọng để tăng hiệu suất, tích hợp chức năng và cải thiện trải nghiệm làm việc với máy ảo trong môi trường ảo hóa.
- Từ đó, VMware còn giúp tăng tương tác liền mạch của người dùng với các hệ điều hành khách.
- Một số tính năng chính của VMware Tools:
  1. **Hiệu suất tối ưu hóa:** VMware Tools cung cấp các trình điều khiển và chức năng tối ưu hóa để tăng hiệu suất của máy ảo. Các trình điều khiển này giúp cải thiện khả năng đồng bộ hóa giữa hệ điều hành máy ảo và máy chủ vật lý, giảm tải cho CPU và tối ưu hóa việc truyền dữ liệu giữa các máy ảo.
  2. **Khả năng tương tác giữa máy chủ vật lý và máy ảo:** VMware Tools cho phép tương tác giữa máy chủ vật lý và máy ảo, cho phép việc sao chép và dán dữ liệu giữa hai môi trường, cũng như điều khiển việc chia sẻ các tập tin và ổ đĩa giữa hệ thống vật lý và máy ảo.
  3. **Điều chỉnh kích thước màn hình tự động:** VMware Tools cho phép máy ảo tự động điều chỉnh kích thước màn hình dựa trên kích thước cửa sổ chương trình chạy máy ảo. Điều này giúp tối ưu hóa trải nghiệm người dùng khi thay đổi kích thước cửa sổ chương trình.
  4. **Tương thích âm thanh và đồ họa:** VMware Tools hỗ trợ tích hợp âm thanh và đồ họa trong máy ảo. Điều này đảm bảo rằng người dùng có thể trải nghiệm chất lượng âm thanh và hiển thị tốt nhất trong môi trường ảo hóa.
  5. **Chia sẻ thư mục và tệp tin:** VMware Tools cho phép chia sẻ các thư mục và tệp tin giữa máy ảo và máy chủ vật lý. Điều này tiện lợi khi cần di chuyển dữ liệu giữa các môi trường khác nhau.
  6. **Tự động cập nhật:** Trong một số phiên bản VMware, VMware Tools có khả năng tự động cập nhật để đảm bảo rằng luôn sử dụng phiên bản mới nhất và an toàn nhất của các thành phần này.
