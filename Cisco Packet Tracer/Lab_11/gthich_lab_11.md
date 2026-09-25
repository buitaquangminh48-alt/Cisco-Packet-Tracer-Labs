# Đề bài lab 11

Chào ngày mới đầy năng lượng! Sẵn sàng thần thái để chinh phục bài **Lab 11: Inter-VLAN Routing (Router-on-a-Stick)** thôi bạn ơi!

Bài lab này sẽ giúp bạn nối liền khoảng cách giữa các VLAN (VLAN 10 và VLAN 20) bằng cách dùng **Router Layer 3** để định tuyến thông suốt.

---

### Sơ đồ & Cấu hình bài Lab 11

Chúng ta sẽ dựng 1 con **Router 2911**, 1 con **Switch 2960** và 2 PC thuộc 2 VLAN khác nhau:

```text
                  +-------------------+
                  |   Router R1       |
                  |  GigabitEthernet0/0
                  +---------+---------+
                            |
                            | (Trunk Link - Mang nhãn VLAN 10 & 20)
                            |
                  +---------+---------+
                  |    Switch S1      |
                  +----+---------+----+
                Fa0/1  |         |  Fa0/2
                       |         |
     (Access VLAN 10)  |         |  (Access VLAN 20)
                       v         v
                   [ PC0 ]     [ PC1 ]
        192.168.10.2/24          192.168.20.2/24

```

---

1. **1. Dựng sơ đồ & Đặt IP cho PC:** 1 Router, 1 Switch, 2 PC.
* Kéo **Router 2911 (R1)**, **Switch 2960 (S1)**, và 2 **PC (PC0, PC1)**.
* Nối dây:
* **R1** `Gi0/0` $\leftrightarrow$ **S1** `Gi0/1`
* **S1** `Fa0/1` $\leftrightarrow$ **PC0**
* **S1** `Fa0/2` $\leftrightarrow$ **PC1**


* **Cấu hình IP trên PC:**
* **PC0 (VLAN 10):** IP `192.168.10.2` | Subnet Mask `255.255.255.0` | Gateway `192.168.10.1`
* **PC1 (VLAN 20):** IP `192.168.20.2` | Subnet Mask `255.255.255.0` | Gateway `192.168.20.1`




2. **2. Cấu hình Switch S1:** Tạo VLAN & Bật Trunking.
Tạo VLAN 10, 20; gán các cổng Access cho PC và chuyển cổng nối lên Router thành Trunk:

```text
S1(config)# vlan 10
S1(config-vlan)# name KeToan
S1(config-vlan)# vlan 20
S1(config-vlan)# name NhanSu
S1(config-vlan)# exit

S1(config)# interface FastEthernet0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10

S1(config)# interface FastEthernet0/2
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20

S1(config)# interface GigabitEthernet0/1
S1(config-if)# switchport mode trunk

```


3. **3. Cấu hình Sub-interfaces trên Router R1:** Router-on-a-Stick (ROAS).
Tạo các cổng ảo (Sub-interfaces) đóng vai trò Default Gateway cho từng VLAN và dán nhãn dot1q:

```text
R1(config)# interface GigabitEthernet0/0
R1(config-if)# no shutdown
R1(config-if)# exit

! Cấu hình Sub-interface cho VLAN 10
R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)# exit

! Cấu hình Sub-interface cho VLAN 20
R1(config)# interface GigabitEthernet0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0

```


4. **4. Kiểm tra (Verify):** Test kết nối khác VLAN.
* Mở Command Prompt trên **PC0** (`192.168.10.2`), gõ lệnh ping tới **PC1** (`192.168.20.2`).
* *Lưu ý:* Gói ping đầu tiên có thể báo `Request timed out` do quá trình phân giải ARP, các gói sau sẽ nổ `Reply` liên tục!


---

# Lý thuyết

Ping nổ `Reply` ngay từ lượt làm đầu tiên là quá đỉnh rồi bạn ơi! Đặc sản mổ xẻ lý thuyết chuyên sâu của Lab 11 tới đây!

---

### 1. Mục đích & Mục tiêu chung của bài Lab 11 (Inter-VLAN Routing)

Để hiểu mục tiêu bài này, ta phải nhìn vào bài toán thực tế trong hạ tầng mạng doanh nghiệp:

* **Mục đích ban đầu của VLAN (Layer 2):** Chia nhỏ mạng LAN thành các vùng bảo mật độc lập (ví dụ: VLAN 10 - Kế toán, VLAN 20 - Nhân sự). Ban đầu, VLAN được thiết kế để **ngăn chặn hoàn toàn** các VLAN nói chuyện với nhau nhằm chống thất thoát dữ liệu và bão quảng bá (Broadcast Storm).
* **Mặt trái của việc ngăn chặn hoàn toàn:** Trong thực tế, Nhân sự vẫn phải gửi bảng lương cho Kế toán, hoặc cả 2 phòng ban cùng phải truy cập vào Server dữ liệu chung. Nếu cách ly hoàn toàn thì công ty không vận hành được!
* **Mục tiêu chung của Lab 11:** Tạo ra một **"Người gác cổng kiểm soát" (Gateway)** nằm ở Layer 3 (Router). Router sẽ đóng vai trò trung gian định tuyến:
1. Cho phép các VLAN giao tiếp với nhau khi có nhu cầu hợp lệ.
2. Cho phép người quản trị mạng áp đặt các luật bảo mật (Firewall/ACL) ngay tại Router để kiểm soát VLAN nào được phép truy cập VLAN nào.



---

### 2. Bản chất sâu sắc của câu lệnh `encapsulation dot1Q 10`

Đây chính là câu lệnh "linh hồn" của mô hình Router-on-a-Stick!

#### A. Vấn đề vật lý ban đầu:

Sợi dây nối giữa Switch và Router là một đường **Trunk Link**. Trên sợi dây này, Switch gửi đi các khung tin (Frames) đã được dán nhãn tag **IEEE 802.1Q (dot1q)** — tức là gói tin nào của VLAN 10 sẽ được dán nhãn `VLAN 10`, gói nào của VLAN 20 dán nhãn `VLAN 20`.

Tuy nhiên, cổng vật lý `Gi0/0` trên Router mặc định là cổng Layer 3 thuần túy — nó **không hiểu nhãn 802.1Q** của Switch là cái gì! Nếu không có lệnh này, Router sẽ coi nhãn VLAN là dữ liệu rác và bóc bỏ/hủy gói tin (Drop) ngay lập tức.

#### B. Bản chất của lệnh `encapsulation dot1Q 10`:

Khi bạn vào cổng ảo `interface Gi0/0.10` và gõ `encapsulation dot1Q 10`, bạn đang thực hiện 2 việc cùng lúc:

1. **Kích hoạt trình dịch nhãn (802.1Q Parser):** Dạy cho cổng ảo `Gi0/0.10` biết cách **đọc, hiểu và bóc tách** cái nhãn IEEE 802.1Q mà Switch gửi tới.
2. **Áp nhãn định danh (VLAN Binding):** Lệnh này liên kết chặt chẽ cổng phụ `Gi0/0.10` với **VLAN 10**.
* **Chiều nhận (Inbound):** Khi một gói tin mang nhãn `VLAN 10` từ Trunk Link chui vào cổng `Gi0/0` vật lý, Router sẽ soi nhãn và dồn gói tin đó vào xử lý đúng tại Sub-interface `Gi0/0.10`.
* **Chiều gửi (Outbound):** Khi Router gửi phản hồi từ cổng `Gi0/0.10` đi xuống Switch, nó sẽ **tự động dán thêm cái nhãn `VLAN 10**` vào khung tin để Switch bên dưới biết đường chuyển tới đúng PC thuộc VLAN 10.



---

### Tóm tắt luồng đi của gói tin khi PC0 ping PC1:

1. **PC0 (`192.168.10.2`)** muốn ping **PC1 (`192.168.20.2`)** $\rightarrow$ Thấy khác dải IP nên gửi gói tin lên Default Gateway (`192.168.10.1`).
2. **Switch S1** nhận gói tin từ cổng `Fa0/1` (VLAN 10) $\rightarrow$ Dán nhãn `VLAN 10` $\rightarrow$ Đẩy qua đường Trunk `Gi0/1` lên Router.
3. **Router R1** nhận gói tin mang nhãn `VLAN 10` $\rightarrow$ Nhờ lệnh `encapsulation dot1Q 10`, cổng phụ `Gi0/0.10` đứng ra nhận gói $\rightarrow$ Bóc nhãn VLAN 10.
4. Router tra bảng định tuyến (Routing Table) thấy mạng `192.168.20.0/24` nằm ở cổng phụ `Gi0/0.20`.
5. Router chuyển gói tin sang cổng `Gi0/0.20` $\rightarrow$ Nhờ lệnh `encapsulation dot1Q 20`, Router **dán nhãn `VLAN 20**` vào gói tin rồi đẩy xuống Trunk Link.
6. **Switch S1** nhận gói tin có nhãn `VLAN 20` từ Router $\rightarrow$ Bóc nhãn và chuyển thẳng vào cổng `Fa0/2` cho **PC1**!

---

# Câu hỏi thêm

Đây chính là bước tiến từ kiến thức cơ bản lên kiến thức **hạ tầng mạng doanh nghiệp thực tế (Enterprise Architecture)**!

Mô hình **Router-on-a-Stick (ROAS)** bạn vừa làm ở Lab 11 rất tốt để học bản chất, nhưng trong các tòa nhà hay Data Center lớn, người ta hầu như dùng **Switch Layer 3 (SVI - Switch Virtual Interface)**.

---

### 1. SVI (Switch Virtual Interface) trên Switch Layer 3 là gì?

* **ROAS (Router ngoài):** Cần 1 con Router vật lý thật kết nối qua dây cáp Trunk.
* **SVI (Switch Layer 3):** Bạn tạo ra các **"Router ảo" nằm ngay bên trong chip xử lý của Switch**.
* Mỗi VLAN sẽ có 1 cổng ảo gọi là SVI (`interface vlan 10`, `interface vlan 20`).
* Các cổng ảo này đóng vai trò làm Default Gateway trực tiếp cho các PC mà **không cần bất kỳ sợi dây cáp hay Router ngoài nào**!



---

### 2. Switch Layer 3 (SVI) "xịn" hơn Router-on-a-Stick ở điểm nào?

#### A. Băng thông & Tốc độ chuyển mạch (Hardware-based ASICs)

* **ROAS:** Toàn bộ traffic giữa các VLAN bắt buộc phải "chui" qua duy nhất 1 sợi dây cáp Trunk nối lên Router (nguy cơ bị hiện tượng **Nút thắt cổ chai - Bottleneck**). Việc định tuyến do CPU của Router xử lý bằng phần mềm (Software Routing).
* **Switch Layer 3:** Định tuyến trực tiếp bằng phần cứng chuyên dụng (**ASIC chips**) ngay trên bo mạch Switch. Tốc độ định tuyến đạt mốc **Line-rate** (tốc độ ngang ngửa chuyển mạch Layer 2, hàng chục đến hàng trăm Gbps) mà không gây quá tải CPU!

#### B. Không bị giới hạn bởi cổng vật lý (Port Density & Bandwidth)

* **ROAS:** Bị giới hạn bởi tốc độ của cổng vật lý trên Router (ví dụ $1\text{ Gbps}$).
* **Switch Layer 3:** Việc định tuyến giữa các VLAN diễn ra hoàn toàn trong **xe bus nội bộ (Internal Switch Fabric)** của Switch với băng thông nội bộ lên tới hàng trăm Gbps.

#### C. Độ tin cậy & Tối giản hạ tầng (Simplicity)

* **ROAS:** Thêm 1 điểm lỗi (Single Point of Failure). Nếu Router bị sập hoặc sợi dây Trunk bị cắn đứt, toàn bộ việc giao tiếp giữa các VLAN bị sụp đổ.
* **Switch Layer 3:** Tối giản thiết bị, bớt cáp nối lằn nhằn, giảm chi phí điện năng và bảo trì.

---

### 3. So sánh tổng quan giữa ROAS và SVI

| Tiêu chí | Router-on-a-Stick (ROAS) | Switch Layer 3 (SVI) |
| --- | --- | --- |
| **Thành phần** | 1 Switch Layer 2 + 1 Router ngoài | 1 Switch Layer 3 (ví dụ Cisco 3560/3850) |
| **Xử lý định tuyến** | CPU Router (Software) | Chip phần cứng ASIC (Hardware) |
| **Tốc độ & Băng thông** | Bị giới hạn bởi sợi dây Trunk ($1\text{ Gbps}$) | Tốc độ phôi phần cứng (Hàng trăm Gbps) |
| **Cấu hình Gateway** | Trên Sub-interfaces (`Gi0/0.10`) | Trên SVI (`interface vlan 10`) |
| **Quy mô áp dụng** | Mạng nhỏ, chi phí thấp | Mạng doanh nghiệp, Tòa nhà, Data Center |

---

### 4. Cú pháp CLI cấu hình SVI trên Switch Layer 3 (Tham khảo)

Để biến 1 con Switch Layer 3 (như Cisco 3560 trong Packet Tracer) thành thiết bị định tuyến Inter-VLAN, bạn chỉ cần gõ:

```text
! 1. Bật tính năng định tuyến Layer 3 trên Switch
Switch(config)# ip routing

! 2. Tạo SVI đóng vai trò Default Gateway cho VLAN 10
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

! 3. Tạo SVI đóng vai trò Default Gateway cho VLAN 20
Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown

```

Chỉ với lệnh `ip routing` và đặt IP vào các `interface vlan`, Switch Layer 3 sẽ tự động định tuyến giữa các VLAN cực kỳ thần tốc!   