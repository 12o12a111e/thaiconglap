- **LVM** là:
  - Logical Volume Manager, là một công cụ trong hệ điều hành Linux dùng để quản lý các ổ đĩa và phân vùng ổ đĩa một cách linh hoạt. 
  - LVM cho phép tạo, quản lý, gộp và chia nhỏ các Logical Volume (LV) mà không cần phải thay đổi cấu trúc vật lý của ổ đĩa.
  1. **Physical Volume (PV)**: là các phân vùng trên ổ đĩa thực tế của hệ thống, có thể là ổ cứng hoặc ổ đĩa SSD. LVM sử dụng các PV để tạo thành các khối dữ liệu lớn hơn.
  2. **Volume Group (VG)**: là một nhóm gồm một hoặc nhiều PV được tổ chức lại với nhau. VG cung cấp một không gian lưu trữ lớn hơn để tạo và quản lý các LV.
  3. **Logical Volume (LV)**: là phân vùng dựa trên VG, có thể tạo LV với kích thước linh hoạt và chúng không cần phải liên tục nằm trên một PV cụ thể. LV có thể coi như các "ổ đĩa ảo" mà có thể sử dụng như các phân vùng thông thường.
***
- **Tạo PV, VG, LV, Extend LVM**:
  - Xác định các ổ đĩa cần sử dụng làm Physical Volume: ```fdisk -l```
  - Tạo PV trên ổ đĩa /dev/sdb: ```pvcreate /dev/sdb```
  - Sau khi tạo PV, có thể tạo một Volume Group (VG) và gộp các PV lại với nhau. 
    - Tạo VG có tên là "my_vg" và thêm PV /dev/sdb vào VG: ```vgcreate my_vg /dev/sdb```
  - Tạo các Logical Volume (LV) từ VG đã tạo.
    - Tạo một LV có tên là "my_lv" với kích thước 10GB từ VG "my_vg": ```lvcreate -n my_lv -L 10G my_vg```
  - Nếu muốn mở rộng dung lượng của LV này, có thể sử dụng lệnh lvextend.
    - Mở rộng LV "my_lv" lên 20GB: ```lvextend -L +10G /dev/sdb/my_vg/my_lv```
