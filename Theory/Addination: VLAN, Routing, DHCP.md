# Addination: VLAN, Routing và DHCP

## 1. VLAN (Virtual LAN)

* **Khái niệm & Vai trò**: Chia một mạng Layer 2 vật lý thành nhiều mạng logic riêng biệt. Giúp thu nhỏ broadcast domain, tăng cường bảo mật và hiệu năng.


* **Loại VLAN chính**:
* **Default VLAN**: Mặc định là VLAN 1 trên các port.


* **Data VLAN**: Chứa dữ liệu người dùng.


* **Native VLAN**: Chứa các lưu lượng không gắn tag (untagged traffic, mặc định là VLAN 1).


* **Voice VLAN**: Ưu tiên băng thông và độ trễ (< 150ms) cho thoại VoIP.




* **Trunking (802.1Q)**: Đường liên kết giữa các switch mang theo traffic của nhiều VLAN bằng cách chèn thêm VLAN Tag (VID - 12 bits) vào Ethernet frame.


* **Lệnh cơ bản (Cisco IOS)**:
* Tạo VLAN: `vlan <id>` $\rightarrow$ `name <ten_vlan>`

* Gán port Access: `interface <port>` $\rightarrow$ `switchport mode access` $\rightarrow$ `switchport access vlan <id>`

* Gán port Trunk: `switchport mode trunk` $\rightarrow$ `switchport trunk native vlan <id>`




---

## 2. Routing Concepts & Inter-VLAN Routing

* **Chức năng chính của Router**: Xác định đường đi tốt nhất (Best Path / Longest Match - prefix có số bit khớp nhiều nhất) và chuyển tiếp gói tin (Packet Forwarding).


* **Cơ chế chuyển tiếp**: CEF (Cisco Express Forwarding) là cơ chế hiện đại và mặc định.


* **Inter-VLAN Routing (Tuyến giữa các VLAN)**:
* **Legacy**: Mỗi VLAN cắm vào 1 cổng vật lý trên Router (tốn cổng).


* **Router-on-a-Stick**: Chỉ dùng 1 cổng vật lý Router cắm vào cổng Trunk của Switch, tạo các **Sub-interface** cho từng VLAN.


* Lệnh Sub-interface: `interface g0/0/0.10` $\rightarrow$ `encapsulation dot1Q 10` $\rightarrow$ `ip address <ip> <mask>`.






* **Administrative Distance (AD)**: Độ tin cậy của nguồn tuyến (Directly Connected = 0, Static = 1, EIGRP = 90, OSPF = 110, RIP = 120).



---

## 3. DHCPv4 (Dynamic Host Configuration Protocol)

* **Quy trình cấp IP (D.O.R.A - 4 bước)**:
1. **D**iscover (Client broadcast)


2. **O**ffer (Server unicast/broadcast)


3. **R**equest (Client broadcast)


4. **A**ck (Server unicast/broadcast)




* **Cấu hình Router làm DHCP Server**:
* Loại trừ IP tĩnh: `ip dhcp excluded-address <low> <high>`

* Tạo pool: `ip dhcp pool <ten_pool>` $\rightarrow$ `network <ip_mang> <mask>` $\rightarrow$ `default-router <ip_gateway>` $\rightarrow$ `dns-server <ip_dns>`



* **DHCP Relay Agent**: Khi Client và DHCP Server nằm ở khác subnet, cấu hình trên cổng Router nhận request: `ip helper-address <ip_dhcp_server>` để chuyển đổi traffic broadcast thành unicast.



---

Chúc bạn ôn thi thật tốt và đạt điểm A+ tuyệt đối môn Mạng máy tính nhé! 🔥💯