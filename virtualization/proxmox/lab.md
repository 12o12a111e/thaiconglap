***
**QM agent**
1. command:
   - list VMs: `qm list`

   - Create or restore a virtual machine: `qm create <vmid>`

   - Create or restore a virtual machine with core, memory, disks specified: `qm create <vmid> --name <vm-name> --cores <number-of-cores> --memory <memory-size-in-bytes> --scsi0 file=<vg-name>:<size-in-gb> --cdrom local:<iso-name> --net0 virtio,bridge=<bridge-name>`

   - start a VM: `qm start <vmid>`

   - Suspend virtual machine: `qm suspend <vmid>`

   - shutdown a VM: `qm shutdown <vmid>`

   - reboot a VM: `qm reboot <vmid>`

   - reset a VM: `qm reset <vmid>`

   - stop a VM `qm stop <vmid>`

   - Enter Qemu Monitor interface: `qm monitor <vmid>`

   - Get the virtual machine configuration with both current and pending values: `qm pending <vmid>`

   - Send key event to virtual machine: `qm sendkey <vmid> <key> [OPTIONS]`

   - Show command line which is used to start the VM (debug info): `qm showcmd <vmid> [OPTIONS]`

   - Unlock the VM: `qm unlock <vmid>`

   - Clone a VM: `qm clone <vmid> <newid>`

   - Clone a VM in full clone mode and also set the name: `qm clone <vmid> <newid> --full --name <name>`

   - Migrate a VM: `qm migrate <vmid> <target-node>`

   - Show VM status: `qm status <vmid>`

   - Clean up resources for a VM: `qm cleanup <vmid> <clean-shutdown> <guest-requested>`

   - Create a Template: `qm template <vmid> [OPTIONS]`

   - Set virtual machine options (synchrounous API): `qm set <vmid> [OPTIONS]`
2. Disk:
   - Import an external disk image as an unused disk in a VM : `qm disk import <vmid> <source> <storage>`

   - Move volume to different storage or to a different VM: `qm disk move <vmid> <disk> [<storage>] [OPTIONS]`

   - Rescan all storages and update disk sizes and unused disk images: `qm disk rescan [OPTIONS]`

   - Extend volume size: `qm disk resize <vmid> <disk> <size> [OPTIONS]`

   - Unlink/delete disk images: `qm disk unlink <vmid> --idlist <string> [OPTIONS]`

   - rescan volumes: `qm rescan`

3. Snapshot:
   - List all snapshots: `qm listsnapshot <vmid>`

   - Snapshot a VM: `qm snapshot <vmid> <snapname>`

   - Delete a snapshot: `qm delsnapshot <vmid> <snapname>`

   - Rollback a snapshot: `qm rollback <vmid> <snapname>`

   - Open a terminal using a serial device (The VM need to have a serial device configured, for example serial0: socket): `qm terminal <vmid> [OPTIONS]`

   - Proxy VM VNC traffic to stdin/stdout: `qm vncproxy <vmid>`

4. Cloudinit:
   - Get automatically generated cloudinit config: `qm cloudinit dump <vmid> <type>`

   - Get the cloudinit configuration with both current and pending values: `qm cloudinit pending <vmid>`

   - Regenerate and change cloudinit config drive: `qm cloudinit update <vmid>`

***
Setup Node Proxmox
1. Tải xuống Proxmox ISO Image tại: https://www.proxmox.com/en/downloads
2. Khởi động từ USB hoặc CD/DVD
   - Menu Proxmox VE xuất hiện. Chọn "Install Proxmox VE" để bắt đầu cài đặt.
   - Đọc và chấp nhận EULA để tiếp tục.
   - Chọn đĩa muốn cài đặt Proxmox. Nhấp vào "Options" để chỉ định các tham số bổ sung, theo mặc định hệ thống tệp là ext4. Nhấn "Next".
   - Cài đặt vị trí, múi giờ và bố cục bàn phím. Nhấn "Next".
   - Tạo mật khẩu mạnh cho thông tin đăng nhập quản trị viên, nhập lại mật khẩu để xác nhận và nhập địa chỉ email để nhận thông báo của quản trị viên hệ thống.
   - Bước cuối cùng trong quá trình cài đặt Proxmox là thiết lập cấu hình mạng. 
     - Management interface
     - Hostname (FQDN) 
     - IP address
     - Netmask
     - Gateway 
     - DNS server 
   - Tại bảng tóm tắt cấu hình đã thiết lập, tích chọn tự khởi động lại sau khi cài đặt thành công và nhấn "Install".
3. Khởi chạy Proxmox
   - Sau khi quá trình cài đặt hoàn tất và hệ thống tự khởi động lại, menu Proxmox GRUB sẽ tải. Chọn Môi trường ảo Proxmox GNU/Linux và nhấn Enter.
   - Tiếp theo, thông báo chào mừng Proxmox VE xuất hiện, bao gồm một địa chỉ IP tải Proxmox. Truy cập đến địa chỉ IP đó trong trình duyệt web.
   - Để truy cập vào giao diện, hãy đăng nhập bằng "root" và cung cấp mật khẩu đã đặt khi cài đặt Proxmox.
   - Một hộp thoại bật lên cho biết không có đăng ký hợp lệ cho máy chủ. Để bỏ qua tin nhắn, bấm OK.
   ***
**Upload/Boot ISO cho máy ảo**
- Chọn máy chủ đang chạy và nhấp vào "local (pve1)". 
- Chọn "ISO Images" từ menu. 
- Chọn giữa "Upload" hoặc "Download from URL".
  - Với "Upload", chọn "Select File" chọn tệp iso cần tải, nhấn "Open" và nhấn "Upload".
  - Với "Download from URL", nhập link tải iso, nhấn "Query URL" và nhấn "Download"
***
**Setup máy ảo trên proxmox**
-  Nhấp vào "Create VM" nằm ở phía bên phải của tiêu đề trong GUI.
-  Thiếp lập các thông số cấu hình cho máy ảo:
   -  Với "General": Cung cấp thông tin chung về VM
      - Bắt đầu bằng cách chọn Node. Proxmox sẽ tự động chọn nút 1 (pve1).
      - Cung cấp ID VM. Mỗi tài nguyên phải có một ID duy nhất.
      - Cuối cùng, tạo tên cho VM. Sử dụng một tên dễ nhận biết.
   - Với "OS": 
     - Chọn ảnh ISO muốn cho VM. 
     - Xác định Loại hệ điều hành 
     - Phiên bản kernel. 
     - Nhấn Next để tiếp tục.
   - Với "System", sửa đổi các tùy chọn hệ thống như Card đồ họa và bộ điều khiển SCSI hoặc để nguyên cài đặt mặc định.
   - Với "Hard Disk", cấu hình chọn đĩa cứng, có thể để tất cả các cài đặt mặc định hoặc tùy chỉnh dung lượng cấp cho VM.
   - Với "CPU", xác định số lượng lõi có thể cung cấp cho VM.
   - Với "Memory", chọn dung lượng bộ nhớ (MiB) cho VM.
   - Với "Network", chọn giao diện mạng cho VM.
   - Với "Confirm", tóm tắt các tùy chọn VM đã chọn. 
     - Để khởi động VM ngay lập tức, có thể tích chọn "Start after created" hoặc khởi động VM theo cách thủ công sau. 
     - Nhấp vào "Finish" để tạo VM.
   - Xem VM mới được tạo trong cây tài nguyên ở bên trái màn hình. Nhấp vào VM để xem thông số kỹ thuật và tùy chọn của nó.
***
**Tìm hiểu virtio driver**

- Virtio là một giao diện ảo hóa được sử dụng để tối ưu hóa hiệu suất và hiệu năng trong việc truyền thông giữa các máy ảo (VMs) và hypervisor (phần mềm quản lý máy ảo). Điều này giúp cải thiện tốc độ truyền thông và giảm thiểu tải cho CPU.

- Virtio driver là một loạt các trình điều khiển ảo hóa, cho phép VMs truyền thông với các thiết bị ảo như card mạng, ổ cứng, thiết bị đồ họa, vv.
- Các trình điều khiển Virtio driver có thể được tìm thấy và cài đặt trên nhiều hệ điều hành và môi trường ảo hóa khác nhau, bao gồm KVM/QEMU (một hypervisor phổ biến trên Linux), VMware và nhiều nền tảng ảo hóa khác.
- Để cài đặt Virtio driver, thường cần:
  1. **Tải driver**: Tải các gói driver Virtio tương ứng với thiết bị ảo mà muốn cài đặt (ví dụ: card mạng, ổ cứng, vv). Các gói driver này thường có sẵn từ nhà cung cấp hypervisor hoặc dự án mã nguồn mở.

  2. **Cài đặt driver**: Theo hướng dẫn cài đặt từ nhà cung cấp hoặc dự án, cài đặt các gói driver Virtio tương ứng.
  3. **Cấu hình VM**: Khi tạo hoặc cấu hình máy ảo, cần chỉ định sử dụng các thiết bị ảo Virtio thay vì các thiết bị ảo thông thường. Điều này đảm bảo rằng VMs sẽ sử dụng Virtio driver để giao tiếp với hypervisor.
- Việc sử dụng Virtio driver có thể cải thiện hiệu suất và hiệu năng của máy ảo, đặc biệt là trong các môi trường ảo hóa có nhiều VMs hoạt động đồng thời. 
***
**Tìm hiểu cloudinit**
- CloudInit là một công cụ được sử dụng rộng rãi trong môi trường điện toán đám mây để cấu hình và tùy chỉnh các máy ảo (VMs) trong thời gian khởi đầu. 
- Nó cho phép tự động hóa quá trình triển khai và cấu hình các VMs một cách linh hoạt thông qua việc cung cấp thông tin cấu hình trong một tệp văn bản.
- CloudInit giúp tăng cường tính tự động hóa và hiệu suất trong quá trình triển khai và quản lý các máy ảo trên nền tảng điện toán đám mây hoặc hạ tầng ảo hóa.
- Các điểm chính cần biết về CloudInit:

  1. **Tự động cấu hình VMs**: CloudInit giúp tự động cấu hình các máy ảo khi chúng được khởi đầu. Thay vì phải thủ công cài đặt và cấu hình từng VM, có thể định nghĩa cấu hình trong tệp văn bản và CloudInit sẽ thực hiện các tác vụ cấu hình này một cách tự động.

  2. **Tệp dạng văn bản**: Cấu hình cho CloudInit thường được viết trong các tệp văn bản có định dạng như YAML hoặc JSON. Trong tệp này, có thể định nghĩa các tác vụ như cài đặt gói phần mềm, tạo người dùng, cấu hình mạng, thiết lập tường lửa, vv.

  3. **Đa nền tảng**: CloudInit không chỉ hỗ trợ một nền tảng điện toán đám mây cụ thể. Nó có thể hoạt động trên nhiều nền tảng bao gồm Amazon Web Services (AWS), Google Cloud Platform (GCP), Microsoft Azure và nhiều nền tảng ảo hóa khác.

  4. **User Data**: CloudInit sử dụng dữ liệu người dùng (user data) để truyền thông tin cấu hình cho VMs. Dữ liệu người dùng này có thể được cung cấp thông qua API khi tạo máy ảo hoặc thông qua tệp cấu hình nằm trên máy ảo.

  5. **Modules**: CloudInit sử dụng các modules để thực hiện các tác vụ cấu hình. Các modules bao gồm rất nhiều chức năng như ssh, package-update, package-install, write-files, runcmd, và nhiều hơn nữa.

  6. **Customization**: CloudInit cho phép tùy chỉnh và mở rộng các tác vụ cấu hình bằng cách viết các scripts hoặc tệp cấu hình tùy chỉnh.

 

