# Chương 10: Cấu Hình Router Cơ Bản (Basic Router Configuration)

## 10.1 Cấu Hình Ban Đầu Cho Router (Configure Initial Router Settings)

* Các bước cấu hình cơ bản cho Router IOS:


1. Đặt tên thiết bị (*Hostname*).


2. Bảo mật chế độ Privileged EXEC mode (mật khẩu `enable secret`).


3. Bảo mật truy cập cổng Console (User EXEC mode).


4. Bảo mật truy cập từ xa qua Telnet / SSH (các dòng `line vty`).


5. Mã hóa tất cả mật khẩu hiển thị dưới dạng văn bản thuần (*Plaintext*).


6. Tạo thông báo cảnh báo pháp lý (*Banner MOTD*).


7. Lưu cấu hình vào NVRAM.




* Ví dụ dòng lệnh cấu hình mẫu trên R1:


```text
Router(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
R1(config)# service password encryption
R1(config)# banner motd # WARNING: Unauthorized access is prohibited! #
R1(config)# exit
R1# copy running-config startup-config
```[cite: 10]


```



---

## 10.2 Cấu Hình Giao Diện (Configure Interfaces)

* **Cấu hình giao diện (Interface) trên Router:**
* Mặc định các giao diện trên Router Cisco ở trạng thái tắt (*Disabled / Shutdown*).


* Để Router có thể truyền dữ liệu, giao diện phải được gán IP và kích hoạt bằng lệnh `no shutdown`.


* Khuyến khích thêm lệnh `description` để mô tả mạng kết nối tới giao diện đó.




* Cú pháp dòng lệnh mẫu:


```text
R1(config)# interface gigabitEthernet 0/0/0
R1(config-if)# description Link to LAN
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:10::1/64
R1(config-if)# no shutdown
R1(config-if)# exit
```[cite: 10]


```


* Các lệnh kiểm tra (Verify) cấu hình giao diện:



| Lệnh (Command) | Chức năng |
| --- | --- |
| **`show ip interface brief`** / **`show ipv6 interface brief`** | Hiển thị tóm tắt tất cả giao diện, địa chỉ IP và trạng thái (Status/Protocol).|
| **`show ip route`** / **`show ipv6 route`** | Hiển thị nội dung bảng định tuyến IP được lưu trong RAM.|
| **`show interfaces`** | Hiển thị thông số thống kê chi tiết của tất cả giao diện trên thiết bị.|
| **`show ip interface`** / **`show ipv6 interface`** | Hiển thị thông số thống kê liên quan đến IPv4/IPv6 trên các giao diện Router.|

---

## 10.3 Cấu Hình Cổng Mặc Định (Configure the Default Gateway)

* **Default Gateway trên Host:**
* Là địa chỉ IP giao diện Router kết nối trực tiếp vào cùng dải mạng LAN với Host.


* Host bắt buộc phải cấu hình Default Gateway để có thể gửi gói tin ra dải mạng bên ngoài (Remote Network).


* Địa chỉ IP của Host và địa chỉ IP giao diện Router làm Gateway phải nằm trong cùng một Subnet.




* **Default Gateway trên Switch (Layer 2 Switch):**
* Tầng 2 Switch không tự định tuyến gói tin, nhưng cần cấu hình Default Gateway để người quản trị có thể **quản lý từ xa (Remote Management)** Switch từ một phân vùng mạng khác.


* Cú pháp cấu hình Default Gateway IPv4 trên Switch ở chế độ Global Configuration:


```text
Switch(config)# ip default-gateway <ip-address>
```[cite: 10]

```