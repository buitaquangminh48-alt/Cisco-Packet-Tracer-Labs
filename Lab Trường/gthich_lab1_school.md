# Bản chất bài lab 1 school

### 1. Bản chất của bài Lab này là gì?

Nếu 10 bài lab sau này bạn làm sẽ đi sâu vào các kỹ thuật nâng cao (VLAN, Routing, NAT, ACL, DHCP...), thì bài Lab 1 này có bản chất là **"Nhập môn Cấu hình Thiết bị & Thiết lập Kết nối Mạng Cơ bản"**.

Nó giúp bạn chuyển từ một người dùng bình thường (chỉ biết cắm dây mạng vào là dùng) thành một **Quản trị viên mạng (Network Administrator)** bắt đầu học cách làm chủ thiết bị của Cisco.

---

### 2. Ý nghĩa cốt lõi của từng phần bạn vừa gõ:

Bài lab được chia làm 3 khối kiến thức chính:

#### 🔷 Khối 1: Quản trị & Bảo mật phần cứng Switch (Bài 1.1 & 1.2)

* **Đặt Hostname (`hostname Minh-333`):** Đặt tên định danh cho thiết bị. Khi quản lý hàng trăm cái switch trong một trung tâm dữ liệu (Data Center), bạn phải biết mình đang truy cập vào cái switch nào.
* **Bảo mật Console & Privilege (`cisco` / `class`):**
* Console là cổng cắm dây trực tiếp từ máy tính vào Switch. Đặt pass để tránh ai đó đi ngang qua cắm dây vào "vọc" linh tinh.
* Privilege EXEC (`enable secret`) là "quyền Root/Admin". Đặt pass ở đây để chặn người dùng thường can thiệp vào cấu hình hệ thống.


* **Mã hóa Password (`service password-encryption`):** Mặc định mật khẩu hiện rõ như ban ngày (plain-text) trong file cấu hình. Mã hóa nó lại để nếu ai đó lén nhìn màn hình cũng không đọc được pass.
* **Lưu NVRAM (`copy run start`):** RAM của Switch sẽ mất sạch dữ liệu khi tắt điện. Lưu vào NVRAM giống như bấm `Ctrl + S` lưu file lên ổ cứng vậy.

#### 🔷 Khối 2: Kết nối Vật lý (Physical Layer)

* Nối cáp Thẳng (Straight-through) từ PC vào Switch và cáp Chéo (Crossover) giữa Switch với Switch để tạo thành một mạng nội bộ (LAN) liên thông vật lý với nhau.

#### 🔷 Khối 3: Cấu hình Địa chỉ IP & SVI (Bài 1.4 - Đoạn quan trọng nhất!)

* **Đặt IP cho PC:** Để các máy tính có địa chỉ định danh ở Lớp 3 (Network Layer) và giao tiếp được với nhau.
* **Bản chất của SVI (VLAN 1):**
* Switch Layer 2 thông thường làm nhiệm vụ chuyển tiếp dữ liệu dựa trên địa chỉ MAC, **bản thân nó không cần địa chỉ IP vẫn chạy bình thường**.
* Tuy nhiên, nếu Switch không có IP, quản trị viên bắt buộc phải ôm laptop lại gần, cắm dây Console vào đít Switch thì mới cấu hình/kiểm tra được $\rightarrow$ Rất cực!
* Vì vậy, người ta tạo ra **SVI (Switch Virtual Interface - Giao diện ảo VLAN 1)** và gán cho nó một địa chỉ IP (ví dụ `192.168.1.253`). Mục đích duy nhất của IP này là **làm "địa chỉ nhà" cho Switch để quản trị viên có thể ngồi từ xa (qua Telnet/SSH/Ping) truy cập vào quản lý Switch**.



---

### 💡 Tóm lại câu chốt "đáng tiền" cho bài lab này:

> **Mục đích của Bài Lab 1:** Giúp bạn nắm vững **quy trình chuẩn hóa một thiết bị mạng mới khôi phục cài đặt gốc**: Đặt tên $\rightarrow$ Đặt mật khẩu bảo vệ $\rightarrow$ Bật IP quản lý (SVI) $\rightarrow$ Kiểm tra kết nối thông suốt (Ping) $\rightarrow$ Lưu cấu hình vào NVRAM.

--- 

# Các câu lệnh

``` text
=====================================================
  KHUNG CÂU LỆNH CẤU HÌNH CƠ BẢN SWITCH CISCO (LAB 1)
=====================================================

1. VÀO CHẾ ĐỘ CẤU HÌNH:
Enable
Configure terminal

2. TẮT TÍNH NĂNG TÌM KIẾM DOMAIN (CHỐNG ĐƠ KHI GÕ SAI):
No ip domain-lookup

3. ĐỔI TÊN THIẾT BỊ:
Hostname <Tên_Switch>

4. BẢO MẬT CỔNG CONSOLE:
Line console 0
Password cisco
Login
Exit

5. BẢO MẬT CHẾ ĐỘ PRIVILEGE (ENABLE):
Enable secret class

6. CẤU HÌNH BANNER MOTD:
Banner motd # Authorized Access Only! #

7. CẤU HÌNH IP QUẢN LÝ (SVI VLAN 1):
Interface vlan 1
Ip address <IP_Address> <Subnet_Mask>
No shutdown
Exit

8. THOÁT RA & KIỂM TRA:
End
Show ip interface brief
Show running-config

9. LƯU CẤU HÌNH VÀO NVRAM:
Copy running-config startup-config
```