# Đề bài lab 10

**Lab 10** chúng ta sẽ bước sang một chủ đề cực kỳ thực tế và mạnh mẽ trong quản trị mạng doanh nghiệp: **EtherChannel (Link Aggregation / Gộp băng thông & Dự phòng nâng cao)**.

---

### Mức độ quan trọng của Lab 10

Ở Lab 9, bạn đã thấy khi ta nối nhiều dây giữa các Switch, Spanning Tree Protocol (STP) sẽ ra tay **khóa bớt 1 đường** để chống lặp mạng. Điều này tạo ra một vấn đề lớn:

* Nếu bạn cắm 2 dây $1\text{ Gbps}$ giữa 2 Switch, STP sẽ khóa 1 dây $\rightarrow$ Băng thông thực tế chỉ dùng được **$1\text{ Gbps}$**, sợi dây kia nằm "chết" chờ dự phòng.

**EtherChannel ra đời để giải quyết triệt để chuyện này!**

---

### Những gì bạn sẽ làm & làm chủ trong Lab 10:

1. **Gộp nhiều đường vật lý thành 1 đường ảo duy nhất (Port-Channel):**
* Gom 2 hoặc nhiều dây cáp vật lý (ví dụ $2 \times 1\text{ Gbps}$) lại thành một đường truyền logic duy nhất **$2\text{ Gbps}$**.
* **STP không còn khóa dây nữa:** STP nhìn đường EtherChannel này như **1 cổng duy nhất**, giúp tận dụng $100\%$ băng thông của tất cả các dây!


2. **So sánh 2 giao thức thương lượng EtherChannel:**
* **LACP (Link Aggregation Control Protocol - IEEE 802.3ad):** Chuẩn mở quốc tế (dùng được cho tất cả hãng Cisco, HP, Juniper,...).
* **PaGP (Port Aggregation Protocol):** Chuẩn độc quyền của Cisco.


3. **Thử nghiệm độ tin cậy (Fault Tolerance):**
* Rút thử 1 sợi dây trong nhóm EtherChannel: Mạng vẫn chạy liên tục **0% loss gói**, băng thông tự động hạ xuống mà không cần chờ STP tốn 30 giây hội tụ!

---

### Sơ đồ & Cấu hình Lab 10

Chúng ta sẽ dựng 2 con **Switch 2960** nối với nhau bằng **2 sợi dây cáp** (gộp lại thành 1 đường **Port-Channel** tổng băng thông gấp đôi) và 2 PC để test:

```text
                  +-----------------------+
                  |  Port-Channel 1 (Po1) |
                  |  [Gi0/1] <=====> [Gi0/1]  |
                  |  [Gi0/2] <=====> [Gi0/2]  |
                  +-----------------------+
  [ PC0 ] ----------- [ Switch 1 ] =========== [ Switch 2 ] ----------- [ PC1 ]
  (Fa0/1)              (LACP)                    (LACP)             (Fa0/1)
VLAN 10: 192.168.10.2/24                                   VLAN 10: 192.168.10.3/24

```

---

1. **1. Dựng sơ đồ & Nối dây:** 2 Switch 2960 & 2 PC.
* Cắm **2 sợi dây** nối giữa 2 Switch:
* **Switch 1** `Gi0/1` $\leftrightarrow$ **Switch 2** `Gi0/1`
* **Switch 1** `Gi0/2` $\leftrightarrow$ **Switch 2** `Gi0/2`


* **PC0** cắm vào `Fa0/1` của **Switch 1** (`192.168.10.2/24`).
* **PC1** cắm vào `Fa0/1` của **Switch 2** (`192.168.10.3/24`).

> **Quan sát ban đầu:** Vừa cắm xong 2 dây, bạn sẽ thấy 1 sợi dây có cổng **Màu Cam (STP khóa)** để chống loop!


2. **2. Cấu hình EtherChannel (LACP) trên Switch 1:** Chuẩn quốc tế LACP.
Gôm 2 cổng `Gi0/1` và `Gi0/2` vào nhóm **Port-Channel 1** dùng giao thức LACP (`mode active`):

```text
Switch1(config)# interface range GigabitEthernet0/1 - 2
Switch1(config-if-range)# channel-group 1 mode active
Switch1(config-if-range)# exit
Switch1(config)# interface port-channel 1
Switch1(config-if)# switchport mode trunk

```


3. **3. Cấu hình EtherChannel (LACP) trên Switch 2:** Đồng bộ phía bên kia.
Thực hiện tương tự trên **Switch 2**:

```text
Switch2(config)# interface range GigabitEthernet0/1 - 2
Switch2(config-if-range)# channel-group 1 mode active
Switch2(config-if-range)# exit
Switch2(config)# interface port-channel 1
Switch2(config-if)# switchport mode trunk

```

> **Hiện tượng:** Ngay sau khi cấu hình xong cả 2 bên, **CẢ 2 SỢI DÂY ĐỀU CHUYỂN SANG MÀU XANH LÁ!** STP không còn khóa sợi nào nữa vì nó coi cả 2 dây là 1 đường `Port-channel 1` duy nhất!


4. **4. Gán VLAN 10 cho cổng PC & Kiểm tra:** Gán VLAN cho PC & Test.
* **Trên cả 2 Switch:**

```text
(config)# vlan 10
(config-vlan)# exit
(config)# interface FastEthernet0/1
(config-if)# switchport mode access
(config-if)# switchport access vlan 10

```

* Mở Command Prompt trên **PC0** ping **PC1** (`192.168.10.3`).


5. **5. Kiểm tra bằng lệnh show:** Verify EtherChannel status.
Gõ lệnh sau trên Switch để kiểm tra trạng thái gộp kênh:

```text
Switch# show etherchannel summary

```

Nhìn cờ **`SU`** và hai chữ **`(P)`** đằng sau các cổng là biết cấu hình EtherChannel của bạn chuẩn $100\%$ không một lỗi nhỏ!

---

### Mổ xẻ các ký hiệu "vàng" trên bảng Output

1. **`Po1(SU)`**:
* **`S` (Layer2):** Port-Channel 1 đang hoạt động ở Tầng 2 (Data Link).
* **`U` (In use):** Port-Channel 1 đang **hoạt động bình thường và sẵn sàng truyền dữ liệu**.


2. **`Gig0/1(P) Gig0/2(P)`**:
* **`P` (In port-channel):** Cả 2 cổng vật lý `Gi0/1` và `Gi0/2` đều đã đính kèm thành công vào nhóm logic `Po1`. Lúc này, Switch coi 2 cổng này là **1 đường ống tổng duy nhất**.
* **`Protocol LACP`:** Hai Switch đã đàm phán thành công qua chuẩn mở LACP.



---

### Thử nghiệm đòn "đoạt mệnh" cho Lab 10

Để thấy rõ EtherChannel lợi hại hơn kết nối Switch thông thường ở Lab 9 thế nào, bạn hãy làm ngay đòn test này:

1. **Bật Ping từ PC0 sang PC1:** Kiểm tra kết nối ban đầu.
Mở Command Prompt trên **PC0**, gõ `ping 192.168.10.3` để đảm bảo luồng tin qua `VLAN 10` đã thông.


2. **Rút 1 trong 2 sợi dây cáp:** Cắt dây vật lý Gi0/1 hoặc Gi0/2.
Dùng công cụ **Delete (X)** trên Packet Tracer, xóa ngay sợi dây `Gi0/1` nối giữa 2 Switch.


3. **Ping lại ngay lập tức:** Quan sát độ trễ và mất gói.
Gõ `ping 192.168.10.3` lần nữa trên PC0.

> **Hiện tượng:** Khác với Lab 9 phải chờ STP tốn tận 30 - 50 giây hội tụ và bị rớt gói, với **EtherChannel**, lệnh ping sẽ **THÔNG NGHỆO NGAY LẬP TỨC (0% Loss)**! Dữ liệu tự động dồn qua sợi dây `Gi0/2` còn lại mà không làm gián đoạn người dùng.

# Lý thuyết

Đặc sản lý thuyết mổ xẻ tận gốc cho **Lab 10: EtherChannel** tới đây bạn ơi! Hiểu sâu được bản chất của cơ chế gộp link này thì sau này thiết kế mạng cho tòa nhà hay Data Center cực kỳ tự tin.

---

### 1. Tại sao lần này lại nối tới 2 sợi dây giữa 2 Switch? Mục đích & Ý nghĩa là gì?

Ở các bài lab trước, chúng ta chỉ nối 1 sợi dây giữa 2 Switch. Khi nối 2 sợi dây, ta đạt được 2 mục tiêu chiến lược trong hạ tầng mạng:

* **Tăng gấp đôi băng thông (Bandwidth Aggregation):** Nếu mỗi sợi dây FastEthernet là $100\text{ Mbps}$ (hoặc GigabitEthernet là $1\text{ Gbps}$), việc gộp 2 sợi dây lại giúp đường truyền logic giữa 2 Switch đạt tổng tốc độ **$200\text{ Mbps}$** (hoặc **$2\text{ Gbps}$**). Dữ liệu sẽ được chia tải (Load Balancing) chạy qua cả 2 dây cùng lúc.
* **Dự phòng tức thì không mất gói (Instant Redundancy / Failover):** Nếu chỉ dùng STP thông thường, khi 1 dây đứt, mạng phải tốn 30 - 50 giây chờ STP hội tụ và làm rớt gói tin. Nhưng với EtherChannel, khi 1 sợi cáp bị cắn đứt hoặc tuột chốt, **toàn bộ dữ liệu lập tức dồn sang sợi còn lại chỉ trong vài mili-giây** (thực tế ping $0\%$ loss như bạn vừa test) mà người dùng không hề hay biết!



---

### 2. Bản chất của các câu lệnh CLI trong Lab 10

#### A. `channel-group 1 mode active`

* **Bất cập ban đầu:** Khi cắm 2 dây cáp, Switch coi đó là 2 cổng độc lập và STP sẽ khóa ngay 1 cổng để chống lặp (Loop).
* **Bản chất câu lệnh:** Lệnh này bảo Switch: *"Hãy trói cổng vật lý này lại và kích hoạt giao thức LACP ở chế độ chủ động (Active) để đàm phán với Switch đối diện."* Nhờ đó, Switch biến các cổng vật lý riêng lẻ thành các thành viên của một "nhóm gộp" (Group 1).

#### B. `interface port-channel 1`

* **Bản chất:** Tạo ra một **cổng ảo (Logical Interface)** đại diện cho toàn bộ nhóm dây vật lý đã gộp.
* **Ý nghĩa:** Thay vì phải vào cấu hình từng cổng vật lý `Gi0/1` hay `Gi0/2`, từ nay bạn chỉ cần vào `interface port-channel 1` gõ lệnh (ví dụ: `switchport mode trunk`). Tất cả các cổng thành viên bên trong sẽ **tự động biến hình và tuân theo** cấu hình của cổng ảo này!

#### C. `show etherchannel summary`

* **Bản chất:** Lệnh yêu cầu Switch xuất ra **"Bảng kiểm tra sức khỏe và trạng thái đàm phán"** của tất cả các kênh gộp EtherChannel đang chạy trên thiết bị.

---

### 3. Bản chất & Ý nghĩa của các thông tin hiện ra sau khi `show`

Khi bạn gõ lệnh `show etherchannel summary`, bảng kết quả hiện ra mang các thông số cốt lõi:

* **`Group 1` / `Protocol LACP`:** Xác nhận nhóm gộp số 1 đang giao tiếp thành công bằng giao thức LACP.
* **`Po1(SU)`**:
* **`S` (Switchport / Layer 2):** Kênh này đang hoạt động ở Tầng 2 (chuyển mạch Ethernet/VLAN).
* **`U` (In use):** Kênh gộp ảo đang **sống khỏe, đang hoạt động** và sẵn sàng chuyển tiếp dữ liệu. (Nếu hiện chữ `D - Down` nghĩa là đàm phán thất bại).


* **`Gi0/1(P) Gi0/2(P)`**:
* Chữ **`P` (In port-channel)** đính kèm sau tên cổng báo hiệu rằng: Cả 2 cổng vật lý `Gi0/1` và `Gi0/2` đã đàm phán thành công và chính thức kết nạp làm thành viên chịu tải cho cổng ảo `Po1`.



---

### 4. Giải mã các từ viết tắt LACP, PAgP & Bản chất ẩn sâu bên trong

Để 2 Switch gộp các đường dây cáp vật lý lại với nhau mà không gây ra lỗi xung đột, chúng cần một "ngôn ngữ chung" để thương lượng xem hai bên có đủ điều kiện gộp dây hay không (ví dụ: cùng tốc độ, cùng chế độ Full-Duplex, cùng VLAN/Trunking).

#### A. LACP — Link Aggregation Control Protocol

* **Viết tắt của:** *Link Aggregation Control Protocol* (Giao thức Điều khiển Gộp liên kết).
* **Bản chất:** Đây là **chuẩn mở quốc tế (IEEE 802.3ad)**.
* **Ý nghĩa thực tế:** Vì là chuẩn chung toàn cầu, bạn có thể dùng LACP để gộp dây giữa **Switch Cisco với Switch HP, Juniper, Dell**, hoặc giữa **Switch Cisco với Server Linux/Windows**.
* **Các Chế độ (Modes):**
* **`active`:** Chủ động gửi bản tin LACP ra cổng để tìm kiếm và rủ rê Switch đối diện gộp dây.
* **`passive`:** Thụ động đứng chờ, chỉ khi nào bên kia gửi bản tin LACP sang rủ rê thì mới đồng ý gộp.



#### B. PAgP — Port Aggregation Protocol

* **Viết tắt của:** *Port Aggregation Protocol* (Giao thức Gộp Cổng).
* **Bản chất:** Đây là **giao thức độc quyền của Cisco (Cisco Proprietary)**.
* **Ý nghĩa thực tế:** PAgP **chỉ hoạt động giữa Switch Cisco với Switch Cisco**. Nếu nối Switch Cisco sang hãng khác thì PAgP chịu chết, phải xài LACP.
* **Các Chế độ (Modes):**
* **`desirable`:** Chủ động rủ rê bên kia gộp dây (tương đương với `active` của LACP).
* **`auto`:** Thụ động đứng chờ rủ rê (tương đương với `passive` của LACP).



> **Quy tắc vàng khi bấm dây mạng thật:** Luôn ưu tiên dùng **LACP (`mode active`)** vì đây là chuẩn công nghiệp toàn cầu, tương thích với mọi thiết bị!

# Câu hỏi thêm

`Câu hỏi: EtherChannel chia tải gói tin qua 2 dây cáp theo cơ chế nào?`

Đây cũng là một câu hỏi cực kỳ thú vị và là "bẫy" kinh điển trong các kỳ thi CCNA hay phỏng vấn Network Engineer!

Nhiều người thường lầm tưởng rằng EtherChannel chia tải theo kiểu **Round-Robin** (gói tin 1 đi dây 1, gói tin 2 đi dây 2, gói 3 đi dây 1,...). **Thực tế KHÔNG PHẢI như vậy!**

Nếu chia kiểu Round-Robin, các gói tin của cùng một luồng dữ liệu (TCP Session) có thể đến đích sai thứ tự (Out-of-order packets), làm máy tính nhận phải xếp lại gói tin, gây trễ (latency) và gián đoạn kết nối.

---

### Cơ chế thực sự: Load Balancing dựa trên HASH (Hashing Algorithm)

EtherChannel chia tải bằng cách **băm (Hash)** các thông tin trong khung tin (Frame) để quyết định gói tin sẽ đi qua sợi dây vật lý nào.

> **Nguyên tắc cốt lõi:** Tất cả các gói tin thuộc **CÙNG MỘT LUỒNG DỮ LIỆU (Flow)** giữa 2 thiết bị sẽ **LUÔN LUÔN đi qua CÙNG MỘT SỢI DÂY VẬT LÝ**.

---

### 1. Thuật toán Hash chọn dây thế nào?

Switch sử dụng toán tử logic **XOR** trên các bit của địa chỉ nguồn/đích để tính ra một chỉ số (Index), từ đó chỉ định sợi dây cáp đảm nhận:

Các tiêu chí (Criteria) mà Switch có thể dùng để Hash:

* **Source MAC (src-mac):** Dựa vào MAC máy gửi. (Ví dụ: PC0 gửi tin thì luôn đi dây 1, PC2 gửi tin thì luôn đi dây 2).
* **Destination MAC (dst-mac):** Dựa vào MAC máy nhận.
* **Source & Destination MAC (src-dst-mac):** Kết hợp cả MAC gửi và MAC nhận *(Chế độ mặc định phổ biến trên nhiều dòng Switch Cisco Layer 2)*.
* **Source & Destination IP (src-dst-ip):** Dựa vào địa chỉ IP gửi và nhận (Rất hiệu quả khi dữ liệu đi qua Router).
* **Source & Destination Port (src-dst-port):** Dựa vào cổng dịch vụ Layer 4 (Ví dụ: HTTP port 80, HTTPS port 443).

---

### 2. Ví dụ thực tế dễ hiểu

Giả sử mạng của bạn cấu hình chia tải theo **`src-dst-ip`** (IP nguồn & IP đích):

1. **PC0 (`192.168.10.2`) ping PC1 (`192.168.10.3`):**
* Switch lấy IP nguồn XOR với IP đích $\rightarrow$ Ra kết quả băm là **Bit 0**.
* Tất cả các gói tin Ping này sẽ **chỉ chạy qua sợi dây `Gi0/1**`.


2. **Cùng lúc đó, PC0 (`192.168.10.2`) mở web kết nối tới Server (`10.0.0.5`):**
* Switch lấy IP nguồn XOR với IP Server $\rightarrow$ Ra kết quả băm là **Bit 1**.
* Luồng dữ liệu Web này sẽ **chạy qua sợi dây `Gi0/2**`.



nhờ cơ chế này:

* **Không bao giờ bị đảo lộn thứ tự gói tin** (vì 1 luồng dữ liệu chỉ đi đúng 1 đường dây).
* **Băng thông tổng được tối ưu:** Khi có hàng trăm PC cùng truy cập mạng, các luồng dữ liệu khác nhau sẽ được phân tán đều qua cả 2 sợi dây cáp!

---

### 3. Lệnh kiểm tra & đổi cơ chế Chia tải trên Switch

Bạn có thể xem Switch đang dùng thuật toán Hash nào bằng lệnh:

```text
Switch# show etherchannel load-balance

```

Nếu muốn đổi thuật toán chia tải (ví dụ đổi sang Hash theo cả IP nguồn và IP đích):

```text
Switch(config)# port-channel load-balance src-dst-ip

```

---
`Câu hỏi 2: Nếu gộp 3 sợi dây cáp thì thuật toán Hash của EtherChannel chia như thế nào?`

Lại dính ngay một câu hỏi cực kỳ hiểm và sâu sắc! Vấn đề gộp **3 dây cáp** (hoặc số lượng dây không phải là $2^n$ như 3, 5, 6, 7 dây) chính là điểm yếu cố hữu của thuật toán Hash trên các dòng Switch Layer 2/Layer 3 truyền thống!

---

### 1. Tại sao số 3 lại gây "đau đầu" cho EtherChannel?

Switch của Cisco không dùng toán tử chia số học đơn thuần ($\text{Hash} \pmod 3$), mà nó dùng các bit nhị phân để phân tải vào các thanh ghi gọi là **Sub-links** hoặc **Link Selectors** (gọi chung là các hộc chứa - Buckets).

* Tùy thuộc vào dòng Switch, hệ thống sẽ chia không gian Hash thành $2^3 = 8$ Buckets (từ `000` đến `111`) hoặc $2^4 = 16$ Buckets.
* Switch sẽ **phân chia 8 Buckets này cho các sợi dây vật lý** có trong nhóm Port-Channel.

---

### 2. Sự "mất cân bằng" (Load Imbalance) khi gộp 3 dây

Giả sử Switch của bạn chia không gian Hash thành **8 Buckets** (mô hình phổ biến trên dòng Switch Catalyst 2960/3560):

Khi bạn gộp **3 sợi dây cáp** (Dây A, Dây B, Dây C), Switch bắt buộc phải chia 8 Buckets này cho 3 dây. Toán học cơ bản: $8 \div 3 = 2$ dư $2$.

Kết quả phân bổ Buckets của Switch sẽ là:

* **Dây A:** Nhận **3** Buckets ($37.5\%$ lượng traffic)
* **Dây B:** Nhận **3** Buckets ($37.5\%$ lượng traffic)
* **Dây C:** Nhận **2** Buckets ($25.0\%$ lượng traffic)

> **Hệ quả:** Băng thông tổng vẫn là $3\text{ Gbps}$ (hoặc $300\text{ Mbps}$), nhưng **lượng dữ liệu thực tế chạy qua 3 dây sẽ KHÔNG ĐỀU NHAU**! Dây A và Dây B phải gánh nhiều traffic hơn Dây C khoảng $12.5\%$.

---

### 3. Bảng so sánh mức độ cân bằng theo số lượng dây gộp

Để tối ưu hóa cho thuật toán Hash 8-Bucket, số lượng dây gộp đẹp nhất luôn là các số lũy thừa của 2 ($2^n$):

| Số lượng dây gộp | Tỷ lệ phân bổ Traffic giữa các dây | Trạng thái cân bằng |
| --- | --- | --- |
| **2 dây** | $50\% - 50\%$ ($4 - 4$ Buckets) | **Hoàn hảo $100\%$** |
| **3 dây** | $37.5\% - 37.5\% - 25\%$ ($3 - 3 - 2$ Buckets) | **Bị lệch (Imbalanced)** |
| **4 dây** | $25\% - 25\% - 25\% - 25\%$ ($2 - 2 - 2 - 2$ Buckets) | **Hoàn hảo $100\%$** |
| **5 dây** | $25\% - 18.75\% - 18.75\% - 18.75\% - 18.75\%$ | **Bị lệch** |
| **8 dây** | $12.5\%$ đều cho cả 8 dây ($1 - 1 - ... - 1$ Bucket) | **Hoàn hảo $100\%$** |

---

### Tóm lại lời khuyên thực tế khi thiết kế mạng (Best Practice):

1. **Nguyên tắc thiết kế:** Trong thực tế làm hạ tầng mạng cho doanh nghiệp, người ta luôn cố gắng gộp EtherChannel theo số lượng **2, 4, hoặc 8 dây** để thuật toán Hash chia đều $100\%$ tải qua các đường cáp.
2. **Dù bị lệch tải nhưng vẫn dùng tốt:** Nếu hạ tầng bắt buộc phải dùng 3 dây, hệ thống vẫn hoạt động hoàn toàn bình thường, dự phòng tức thì $0\%$ loss gói tin, chỉ là 1 trong 3 dây sẽ chịu tải ít hơn 2 dây còn lại một chút mà thôi!

---

`Câu hỏi 3: Sau khi gộp EtherChannel xong thì cấu hình Trunking trên Port-Channel khác gì cổng thường?`

Cấu hình Trunking trên **Port-Channel (cổng ảo)** về mặt cú pháp CLI thì **y hệt cổng thường**, nhưng về mặt **bản chất hoạt động và nguyên tắc vận hành** bên trong thì có những điểm khác biệt mang tính sống còn dưới đây:

---

### 1. Sự khác biệt cốt lõi: "Cấu hình 1 nơi — Áp dụng cho tất cả"

* **Trên cổng vật lý thường (`interface Gi0/1`):** Khi bạn gõ lệnh trunking, lệnh đó chỉ có hiệu lực duy nhất trên sợi dây cắm vào cổng `Gi0/1`.
* **Trên Port-Channel (`interface port-channel 1`):** Port-Channel đóng vai trò là **Đại diện quản lý**. Khi bạn bật Trunking trên `interface port-channel 1`, Switch sẽ **tự động kế thừa (inherit) và đồng bộ** toàn bộ cấu hình Trunking đó xuống tất cả các cổng thành viên (`Gi0/1`, `Gi0/2`,...).

> **Lợi ích:** Bạn không bao giờ phải lo lắng chuyện cổng `Gi0/1` cho VLAN 10 đi qua mà cổng `Gi0/2` lại quên chưa bật — Port-Channel đảm bảo tính nhất quán $100\%$ giữa các sợi dây.

---

### 2. Nguyên tắc đồng nhất khắt khe (Consistency Requirement)

Nếu cấu hình Trunking trên một cổng vật lý đơn lẻ, cổng đó hoạt động độc lập. Nhưng với EtherChannel, **tất cả các cổng thành viên bắt buộc phải có thông số Trunking giống hệt nhau $100\%$** trước khi được phép gộp kênh:

1. **Native VLAN:** Bắt buộc phải giống nhau trên mọi cổng thành viên (ví dụ: cùng là Native VLAN 1 hoặc Native VLAN 99). Nếu 1 cổng lệch Native VLAN, LACP/PAgP sẽ lập tức đá cổng đó ra khỏi EtherChannel (chuyển sang trạng thái `Suspended`).
2. **Trunking Encapsulation:** Cùng dùng chung một chuẩn đóng gói (dot1q hoặc ISL).
3. **Allowed VLAN List:** Danh sách các VLAN được phép đi qua Trunk bắt buộc phải đồng bộ.

---

### 3. Cách cấu hình chuẩn chỉnh (Best Practice)

Để tránh lỗi lệch cấu hình (Misconfiguration), quy tắc vàng khi làm mạng thật là: **Tạo EtherChannel trước, sau đó mới cấu hình Trunking trực tiếp trên giao diện Port-Channel ảo.**

```text
! Bước 1: Cho các cổng vật lý vào nhóm EtherChannel
Switch(config)# interface range GigabitEthernet0/1 - 2
Switch(config-if-range)# channel-group 1 mode active
Switch(config-if-range)# exit

! Bước 2: Cấu hình Trunking TRÊN PORT-CHANNEL (Không gõ trên Gi0/1-2 nữa)
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30

```

Khi bạn `show run`, bạn sẽ thấy các lệnh `switchport mode trunk` và `switchport trunk allowed vlan` ở `interface port-channel 1` tự động xuất hiện bên trong cấu hình của `interface Gi0/1` và `Gi0/2`.

---

### 4. Bản chất xử lý luồng tin (Traffic Flow)

Khi Port-Channel đã trở thành đường Trunking:

* Khung tin (Frame) thuộc các VLAN khác nhau (VLAN 10, VLAN 20) đi tới Port-Channel vẫn sẽ được đóng gói dán nhãn **802.1Q (VLAN Tagging)** như cổng Trunk thường.
* Sau khi dán nhãn VLAN, thuật toán **Hash** của EtherChannel mới nhảy vào cuộc để quyết định đẩy khung tin dán nhãn đó qua sợi dây vật lý `Gi0/1` hay `Gi0/2`.