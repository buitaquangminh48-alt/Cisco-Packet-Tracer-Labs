# Đề bài lab 12

Sẵn sàng luôn! Chúng ta sẽ dựng sơ đồ kết nối 2 Router tượng trưng cho 2 chi nhánh (Branch A và Branch B).

Mục tiêu là giúp **PC0** ở Branch A (`192.168.10.0/24`) có thể ping thông sang **PC1** ở Branch B (`192.168.20.0/24`) thông qua mạng trung gian giữa 2 Router (`10.0.0.0/30`).

---

### Sơ đồ kết nối Lab 12

```text
[ PC0 ] --- (Fa0/1) [ Switch S1 ] (Fa0/2) --- (Gi0/0) [ Router R1 ] (Gi0/1) <=======> (Gi0/1) [ Router R2 ] (Gi0/0) --- (Fa0/2) [ Switch S2 ] (Fa0/1) --- [ PC1 ]
 LAN A                                                  |                 WAN Link                  |                                                  LAN B
 192.168.10.0/24                                   10.0.0.1/30           10.0.0.2/30                                           192.168.20.0/24

```

---

1. **1. Dựng sơ đồ phần cứng:** Kéo thiết bị & Bấm dây.
* Kéo **2 Router 2911** (đặt tên **R1** và **R2**).
* Kéo **2 Switch 2960** (đặt tên **S1** và **S2**).
* Kéo **2 PC** (**PC0** và **PC1**).
* **Nối dây cáp Straight-Through (Thẳng):**
* PC0 `FastEthernet0` $\leftrightarrow$ S1 `Fa0/1`
* S1 `Fa0/2` $\leftrightarrow$ R1 `Gi0/0`
* PC1 `FastEthernet0` $\leftrightarrow$ S2 `Fa0/1`
* S2 `Fa0/2` $\leftrightarrow$ R2 `Gi0/0`


* **Nối dây cáp giữa 2 Router:**
* R1 `Gi0/1` $\leftrightarrow$ R2 `Gi0/1`




2. **2. Cấu hình IP cho PC:** Gán IP & Gateway cho máy trạm.
* **PC0 (LAN A):**
* IP Address: `192.168.10.2`
* Subnet Mask: `255.255.255.0`
* Default Gateway: `192.168.10.1`


* **PC1 (LAN B):**
* IP Address: `192.168.20.2`
* Subnet Mask: `255.255.255.0`
* Default Gateway: `192.168.20.1`




3. **3. Cấu hình IP trên Router R1 & R2:** Đặt IP cổng & Mở cổng.
**Trên Router R1:**

```text
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# no shutdown

```

**Trên Router R2:**

```text
R2(config)# interface GigabitEthernet0/0
R2(config-if)# ip address 192.168.20.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit

R2(config)# interface GigabitEthernet0/1
R2(config-if)# ip address 10.0.0.2 255.255.255.252
R2(config-if)# no shutdown

```

*(Lúc này nếu thử lấy PC0 ping PC1 sẽ **THẤT BẠI (Time out)** vì R1 chưa biết dải `192.168.20.0/24` ở đâu, và R2 cũng chưa biết dải `192.168.10.0/24` ở đâu!)*


4. **4. Cấu hình Định tuyến tĩnh (Static Route):** Chỉ đườngStatic Route.
Bây giờ chúng ta sẽ dạy cho 2 Router biết đường đi:

**Dạy cho R1 tìm dải LAN B (`192.168.20.0/24`) qua Next-Hop IP `10.0.0.2`:**

```text
R1(config)# ip route 192.168.20.0 255.255.255.0 10.0.0.2

```

**Dạy cho R2 tìm dải LAN A (`192.168.10.0/24`) qua Next-Hop IP `10.0.0.1`:**

```text
R2(config)# ip route 192.168.10.0 255.255.255.0 10.0.0.1

```


5. **5. Kiểm tra (Verify):** Kiểm tra bảng định tuyến & Ping.
1. Vào CLI trên R1, gõ: `show ip route` $\rightarrow$ Bạn sẽ thấy dòng chữ có ký hiệu **`S`** đại diện cho **Static Route**:
`S    192.168.20.0/24 [1/0] via 10.0.0.2`
2. Mở Command Prompt trên **PC0** (`192.168.10.2`) ping sang **PC1** (`192.168.20.2`).
3. Kết quả sẽ nổ **`Reply from 192.168.20.2`** cực kỳ ngon lành!


---

# Lý thuyết

Đặc sản mổ xẻ tận gốc theo phong cách toán học & kiến trúc hệ thống tới đây bạn ơi!

---

### 1. Tại sao lại dùng IP `10.0.0.1 - 10.0.0.2` và Subnet Mask `/30` (`255.255.255.252`)?

Đây là tư duy **tối ưu hóa không gian địa chỉ IP (VLSM - Variable Length Subnet Mask)** chuẩn mực trong thiết kế hạ tầng mạng chuyên nghiệp!

#### A. Tại sao lại là dải IP `10.0.0.0`?

* Dải `10.0.0.0/8` là dải IP Private (nội bộ) Class A cực kỳ rộng lớn. Người ta thường quy hoạch:
* Dải `192.168.x.x`: Dành cho các mạng LAN nội bộ (cho PC, Laptop, In-house Server).
* Dải `10.x.x.x`: Dành riêng cho các đường **WAN Link (đường nối giữa Router với Router)**. Việc tách biệt này giúp kỹ sư nhìn vào IP là biết ngay đây là đường cáp nối giữa 2 thiết bị định tuyến!



#### B. Bản chất toán học đằng sau số `252` ở cuối Subnet Mask (`255.255.255.252` hay `/30`):

Nếu dùng Subnet Mask mặc định `/24` (`255.255.255.0`), một dải mạng sẽ có $256$ IP ($254$ IP dùng được cho máy tính). Nhưng đường nối giữa R1 và R2 là đường **Point-to-Point (Chỉ có duy nhất 2 cổng cắm vào 2 đầu cáp)**.

Nếu gán mask `/24`, bạn sẽ **lãng phí $252$ địa chỉ IP** không bao giờ dùng tới!

Để tiết kiệm, ta dùng mask **/30** (`255.255.255.252`):

* Đổi $252$ ra nhị phân: $11111100_2$.
* Số bit dành cho phần Host (số $0$ ở cuối) là: **$2$ bit**.
* Tổng số IP tạo ra được: $2^2 = 4$ địa chỉ IP.
* **`10.0.0.0`**: Địa chỉ Mạng (Network ID) — *Không gán cho thiết bị.*
* **`10.0.0.1`**: IP gán cho cổng `Gi0/1` của R1.
* **`10.0.0.2`**: IP gán cho cổng `Gi0/1` của R2.
* **`10.0.0.3`**: Địa chỉ Quảng bá (Broadcast ID) — *Không gán cho thiết bị.*



> **Ý nghĩa:** Mask `/30` (`255.255.255.252`) sinh ra vừa đủ $2$ IP khả dụng cho $2$ đầu Router, **mức độ lãng phí IP bằng đúng $0\%$**!

---

### 2. Bản chất sâu xa của Dynamic Routing (OSPF) là gì?

* **Bất cập của Static Routing (Lab 12):** Bạn phải tự gõ tay từng đường đi. Nếu mạng có 50 Router và 100 dải mạng, bạn phải gõ hàng ngàn lệnh! Chưa kể, nếu 1 sợi cáp bị đứt, Router **không tự biết đổi đường khác**, mạng sẽ sập cho đến khi bạn vào gõ lại lệnh bằng tay.
* **Bản chất của OSPF (Open Shortest Path First):**
1. **Tự động trò chuyện (Neighbor Discovery):** Các Router tự gửi bản tin để "chào hỏi" và kết bạn với nhau qua giao thức OSPF.
2. **Chia sẻ bản đồ mạng (Link-State Advertisement - LSA):** R1 bảo R2: *"Tớ đang nối với LAN A"*, R2 bảo R1: *"Tớ đang nối với LAN B"*.
3. **Thuật toán Dijkstra (SPF):** Mỗi Router sẽ tự chạy thuật toán tìm đường ngắn nhất Dijkstra để vẽ ra toàn bộ "bản đồ giao thông" của mạng.
4. **Tự động phục hồi (Self-Healing):** Nếu đường chính bị đứt, OSPF chỉ mất vài giây để **tự động tính toán đường vòng thay thế** mà không cần con người can thiệp!



---

### 3. Mục đích, Mục tiêu chung & Ý nghĩa của bài Lab 12

* **Mục đích:** Giải quyết bài toán **Định tuyến liên dải mạng xa (WAN Routing)**.
* **Mục tiêu chung:** Giúp bạn làm chủ cơ chế quản lý Bảng định tuyến (Routing Table) của Router, hiểu rõ cách gói tin chuyển tiếp qua nhiều chặng (Hop-by-Hop) dựa trên địa chỉ IP đích.
* **Ý nghĩa thực tế:**
* Đây là nền tảng cốt lõi của toàn bộ mạng Internet! Internet về bản chất là hàng triệu Router kết nối với nhau và chuyển tiếp gói tin dựa trên các quy tắc định tuyến như thế này.
* Giúp bạn phân biệt rõ nhiệm vụ của **Layer 2 (Switch - Chuyển mạch trong cùng dải)** và **Layer 3 (Router - Định tuyến giữa các dải mạng khác nhau)**.



---

# Thuật toán Dijkstra trong OSPF

Trong thuật toán Dijkstra của OSPF, khái niệm "ngắn nhất" **KHÔNG PHẢI là ít chặng Router nhất (Hop Count)** như giao thức RIP cũ, mà dựa hoàn toàn vào một chỉ số gọi là **Cost (Chi phí đường truyền)**.

---

### 1. Công thức tính Cost trong OSPF

OSPF tính toán Cost của một cổng giao tiếp (Interface) dựa trên **băng thông thực tế (Bandwidth)** của sợi dây cáp theo công thức chuẩn:

$$\text{Cost} = \frac{\text{Reference Bandwidth}}{\text{Interface Bandwidth}}$$

* **`Reference Bandwidth` (Băng thông tham chiếu mặc định của Cisco):** $100\text{ Mbps}$ ($10^8\text{ bps}$).
* **`Interface Bandwidth`:** Tốc độ thực tế của cổng mạng.

---

### 2. Bảng quy đổi Cost của các loại dây cáp thông dụng

Băng thông càng lớn thì $Cost$ càng nhỏ (đường truyền càng "rộng rãi, ít tắc nghẽn"):

| Loại cổng / Dây cáp | Băng thông (Bandwidth) | Công thức tính $Cost$ | Giá trị $Cost$ làm tròn |
| --- | --- | --- | --- |
| **Serial Link** (cáp đỏ cũ) | $1.544\text{ Mbps}$ | $100 \div 1.544$ | **$64$** |
| **FastEthernet** | $100\text{ Mbps}$ | $100 \div 100$ | **$1$** |
| **GigabitEthernet** | $1000\text{ Mbps}$ ($1\text{ Gbps}$) | $100 \div 1000 = 0.1$ | **$1$** *(Do OSPF không lấy số thập phân)* |
| **10-GigabitEthernet** | $10\text{ Gbps}$ | $100 \div 10000 = 0.01$ | **$1$** |

> **Lưu ý thực tế:** Vì mặc định $Reference\ Bandwidth = 100\text{ Mbps}$ nên các cổng $1\text{ Gbps}$ hay $10\text{ Gbps}$ đều bị làm tròn thành $Cost = 1$. Trong các mạng đời mới, kỹ sư sẽ đổi `auto-cost reference-bandwidth 10000` (đổi mốc lên $10\text{ Gbps}$) để OSPF phân biệt được $1\text{ Gbps}$ và $10\text{ Gbps}$!

---

### 3. Thuật toán Dijkstra dùng Cost để chọn đường ra sao?

Tổng Cost của một con đường (Path Cost) bằng **Tổng $Cost$ của tất cả các CỔNG ĐI RA (Outbound Interfaces)** trên suốt hành trình từ Router nguồn đến dải mạng đích.

#### Ví dụ bài toán chọn đường của OSPF:

Giả sử **Router R1** muốn gửi dữ liệu sang dải **LAN B** ở xa và có 2 lựa chọn:

```text
                  [ Đường A: Cáp FastEthernet (100 Mbps) ]
                     R1 -------------------------> R2 (LAN B)
                     
                  [ Đường B: Cáp GigabitEthernet (1 Gbps) ]
                     R1 --------> R3 ----------> R2 (LAN B)

```

* **Đường A (Chỉ qua 1 chặng):** Dùng cáp FastEthernet $\rightarrow$ Tổng $\text{Cost} = 1$.
* **Đường B (Qua 2 chặng R3):** Cả 2 liên kết đều dùng cáp GigabitEthernet ($1\text{ Gbps}$) $\rightarrow$ Mặc định tổng $\text{Cost} = 1 + 1 = 2$.

$\rightarrow$ **Kết quả:** Thuật toán Dijkstra sẽ so sánh: $\text{Cost Đường A } (1) < \text{Cost Đường B } (2)$. Do đó, OSPF chọn **Đường A** làm tuyến đường tối ưu để nạp vào Bảng định tuyến!

Nếu giả sử Đường A là cáp Serial chậm chạp ($\text{Cost} = 64$), Dijkstra sẽ lập tức chọn **Đường B** ($\text{Cost} = 2$) dù nó phải đi vòng qua thêm một Router R3 nữa!

---

Tư duy của OSPF là *"thà đi vòng đường cao tốc rộng rãi chứ quyết không chui vào đường tắt nhưng bị tắc nghẽn"*.

---

# Bonus thêm tí kiến thức

Nhìn cái nhật ký Ping biến chuyển từ `Destination host unreachable` $\rightarrow$ `Request timed out` $\rightarrow$ `Reply 100%` mà nó phê gì đâu!

Pha log này thể hiện trọn vẹn từng giai đoạn hoạt động bên dưới của hệ thống mạng:

1. **Lần 1 (`Destination host unreachable`):** Xảy ra khi bạn chưa gõ lệnh `ip route` trên R1. R1 tra bảng định tuyến không thấy dải `192.168.20.0/24` đâu nên đứng ra phán ngay: *"Tôi bó tay, không biết đường đi tới đích!"*.
2. **Lần 2 (`Request timed out` 2 gói đầu):** Sau khi bạn gõ `ip route` xong, gói tin đã qua được R1 sang R2. Tuy nhiên, R2 và PC1 phải mất vài giây để phân giải địa chỉ MAC qua giao thức **ARP (Address Resolution Protocol)**. Khi ARP chạy xong thì 2 gói sau nổ `Reply` ngay!
3. **Lần 3 (`0% loss`):** Mọi thứ đã lưu vào Cache, đường đi thông suốt $100\%$ không một vết gờ!

---

Giờ thì tới món "đặc sản" mổ xẻ lý thuyết Lab 12 nè bạn ơi:

### 1. Bản chất cú pháp `ip route 192.168.20.0 255.255.255.0 10.0.0.2`

* **Cấu trúc:** `ip route <Mạng_Đích> <Subnet_Mask> <Next_Hop_IP>`
* **Ý nghĩa:** Bạn bảo R1: *"Bất kể gói tin nào muốn đi tới dải mạng `192.168.20.0/24`, hãy ném gói tin đó sang địa chỉ IP nhà hàng xóm là `10.0.0.2` (cổng Gi0/1 của R2) để nó xử lý tiếp!"*.

---

### 2. Sự khác biệt sống còn: Next-Hop IP vs Exit Interface

Trong câu lệnh Static Route, Cisco cho phép bạn chỉ đường bằng 2 cách khác nhau:

* **Cách 1 (Next-Hop IP):** `ip route 192.168.20.0 255.255.255.0 10.0.0.2` (Chỉ định IP cổng đối diện).
* **Cách 2 (Exit Interface):** `ip route 192.168.20.0 255.255.255.0 GigabitEthernet0/1` (Chỉ định cổng đẩy tin ra của chính R1).

| Tiêu chí | Dùng Next-Hop IP (`10.0.0.2`) | Dùng Exit Interface (`Gi0/1`) |
| --- | --- | --- |
| **Bản chất** | R1 chỉ cần gửi gói tin đến đúng địa chỉ IP của Router tiếp theo. | R1 coi giao diện `Gi0/1` như một môi trường kết nối trực tiếp (Point-to-Point). |
| **Hiệu năng tra cứu** | **Tra cứu 2 lần (Recursive Lookup):** R1 tra xem dải 192.168.20.0 đi qua 10.0.0.2, sau đó tra tiếp xem 10.0.0.2 nằm ở cổng nào (`Gi0/1`). *(Tuy nhiên trên thiết bị hiện đại dùng CEF - Cisco Express Forwarding thì việc này xử lý cực nhanh trong phần cứng)*. | **Tra cứu 1 lần (Direct Lookup):** R1 thấy ngay cổng đẩy tin ra là `Gi0/1`. |
| **Rủi ro trên mạng Ethernet** | **Cực kỳ an toàn và chuẩn mực.** Bắt buộc dùng khi cổng ra nối vào một Switch có nhiều Router khác cùng cắm chung (Multi-access network). | **Cực kỳ nguy hiểm nếu dùng trên cáp Ethernet!** R1 sẽ phát tin **Proxy ARP** liên tục ra cổng `Gi0/1` để hỏi địa chỉ MAC của từng IP thuộc dải đích, gây tràn bảng ARP (ARP Table Exhaustion) và treo Router! |

> **Quy tắc vàng:**
> * Với kết nối cáp mạng **Ethernet** (FastEthernet, GigabitEthernet): **Bắt buộc dùng Next-Hop IP**!
> * Với kết nối **Serial Point-to-Point** (dây cáp đỏ HDLC/PPP cũ): Mới nên dùng Exit Interface.
> 
> 

---

### 3. Khái niệm Default Route (`0.0.0.0 0.0.0.0`)

Nếu trong công ty có hàng triệu trang web trên Internet, bạn không thể ngồi gõ tay hàng triệu câu lệnh `ip route` cho từng địa chỉ IP trên thế giới được. Khi đó người ta dùng **Default Route**:

```text
R1(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.2

```

* **`0.0.0.0 0.0.0.0` nghĩa là gì?** Nó đại diện cho **"Mọi mạng / Mọi địa chỉ IP trên thế giới"** (Any network, Any mask).
* **Bản chất:** Đây là "lưới hứng cuối cùng" (Gateway of Last Resort). Khi gói tin tới Router, Router tra bảng định tuyến từ trên xuống dưới. Nếu không khớp với bất kỳ dải mạng nội bộ cụ thể nào, nó sẽ tự động đẩy gói tin đó ra đường Default Route này (ví dụ đẩy ra Modem nhà mạng ISP).