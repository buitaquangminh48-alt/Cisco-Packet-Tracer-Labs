# **Chương 2: Basic Switch and End Device Configuration**

---

## **1. Phương Thức Truy Cập Cisco IOS (Cisco IOS Access)**

* **Thành phần Hệ điều hành:**
* **Shell:** Giao diện người dùng (CLI hoặc GUI).


* **Kernel:** Giao tiếp giữa phần cứng và phần mềm, quản lý tài nguyên.


* **Hardware:** Thiết bị phần cứng vật lý.




* **Các phương thức kết nối/truy cập:**
* **Console:** Cổng quản lý vật lý, dùng cho cấu hình ban đầu.


* **SSH (Secure Shell):** Kết nối CLI từ xa an toàn qua mạng (được khuyến nghị).


* **Telnet:** Kết nối CLI từ xa không an toàn (truyền dữ liệu/mật khẩu dạng văn bản thuần - plaintext).





---

## **2. Điều Hướng Cisco IOS (IOS Navigation)**

* **Chế độ EXEC chính:**
* **User EXEC Mode:** Chỉ cho phép giám sát cơ bản; ký hiệu prompt: `Switch>` hoặc `Router>`.


* **Privileged EXEC Mode:** Cho phép truy cập toàn bộ lệnh và tính năng; ký hiệu prompt: `Switch#` hoặc `Router#`.




* **Chế độ cấu hình (Configuration Modes):**
* **Global Configuration Mode:** Cấu hình toàn cục thiết bị; prompt: `Switch(config)#`.


* **Subconfiguration Modes:**
* Line Configuration: `Switch(config-line)#`

* Interface Configuration: `Switch(config-if)#`





* **Chuyển đổi giữa các chế độ:**
* Từ User EXEC sang Privileged EXEC: Dùng lệnh `enable`.


* Từ Privileged EXEC sang Global Config: Dùng lệnh `configure terminal`.


* Quay lại Privileged EXEC từ bất kỳ subconfig mode nào: Dùng lệnh `end` hoặc phím tắt `Ctrl + Z`.


* Quay lại cấp phía trước: Dùng lệnh `exit`.





---

## **3. Cấu Trúc Lệnh (The Command Structure)**

* **Cú pháp lệnh cơ bản:** Bao gồm **Command** + **Space** + **Keyword / Argument**.


* **Keyword:** Tham số cố định được hệ điều hành định nghĩa trước.


* **Argument:** Giá trị hoặc biến do người dùng nhập (ví dụ: IP address).




* **Trợ giúp & Phím tắt (Hot Keys):**
* Trợ giúp theo ngữ cảnh: Nhập `?`.


* Phím `Tab`: Tự động hoàn thành lệnh.


* Phím `Up / Down Arrow`: Xem lại lịch sử lệnh.


* `Ctrl + Shift + 6`: Hủy thao tác đang thực hiện (ping, traceroute...).





---

## **4. Cấu Hình Thiết Bị Cơ Bản (Basic Device Configuration)**

* **Đặt tên thiết bị (Hostname):**
* Lệnh: `hostname <tên_thiết_bị>` (trong Global Config).




* **Bảo mật truy cập bằng mật khẩu:**
* **Mật khẩu Console:** `line console 0` $\rightarrow$ `password <mật_khẩu>` $\rightarrow$ `login`.


* **Mật khẩu Privileged EXEC:** `enable secret <mật_khẩu>`.


* **Mật khẩu VTY (Telnet/SSH):** `line vty 0 15` $\rightarrow$ `password <mật_khẩu>` $\rightarrow$ `login`.




* **Mã hóa mật khẩu dạng văn bản thuần:**
* Lệnh: `service password-encryption`.




* **Tạo thông điệp cảnh báo (Banner MOTD):**
* Lệnh: `banner motd # <Nội dung thông điệp> #`.





---

## **5. Lưu Cấu Hình (Save Configurations)**

* **Hai tệp cấu hình hệ thống:**
* **`running-config`:** Lưu trong RAM (mất khi tắt nguồn), phản ánh cấu hình hiện tại.


* **`startup-config`:** Lưu trong NVRAM (không mất khi tắt nguồn), dùng khi khởi động.




* **Lệnh thao tác:**
* Lưu từ RAM vào NVRAM: `copy running-config startup-config`.


* Xóa tệp startup-config: `erase startup-config`.


* Khởi động lại thiết bị: `reload`.





---

## **6. Cổng và Địa Chỉ IP (Ports and Addresses)**

* **Định dạng địa chỉ IP:**
* **IPv4:** 32-bit, viết dưới dạng 4 số thập phân phân cách bằng dấu chấm (dotted decimal). Dùng **Subnet Mask** để phân biệt phần Network và Host.


* **IPv6:** 128-bit, viết dưới dạng chuỗi các chữ số thập lục phân (hexadecimal) phân cách bởi dấu hai chấm `:`.




* **Gán địa chỉ IP thiết bị cuối (End Devices):** Có thể gán thủ công (Static) hoặc tự động qua DHCP.


* **Cấu hình IP cho Switch (SVI - Switch Virtual Interface):**
* Lệnh: `interface vlan 1` $\rightarrow$ `ip address <địa_chỉ_ip> <subnet_mask>` $\rightarrow$ `no shutdown`.





---

## **7. Kiểm Tra Kết Nối (Verify Connectivity)**

* Dùng lệnh `ping` để kiểm tra kết nối truyền nhận gói tin giữa hai thiết bị cuối.



---

## **Đáp Án Phần Bài Tập Trắc Nghiệm Cuối Slide**

**2.1 Cisco IOS Access:**

1. **Console**

2. **Console**

3. **Telnet/SSH**

4. **AUX**


**2.2 IOS Navigation:**

1. **Privileged EXEC mode**

2. **Global configuration mode**

3. **User EXEC mode**

4. **CTRL+Z** và **end**


**2.4 Basic Device Configuration:**

1. **`hostname Sw-Floor-2`**

2. **`enable secret class`**

3. **`login`**

4. **`service password-encryption`**

5. **`banner motd $ Keep out $`**


**2.6 Ports and Addresses:**

1. **Định dạng thập phân chấm**

2. **Bốn số thập phân từ 0 đến 255 được phân cách bằng dấu chấm.**

3. **Switch virtual interface (SVI)**


---

Bạn có cần làm rõ chi tiết phần lệnh CLI nào hay muốn chuyển sang tóm tắt slide chương tiếp theo không?
