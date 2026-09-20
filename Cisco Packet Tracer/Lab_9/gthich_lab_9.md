# Đề bài lab 9

Lên luôn! **Lab 9: VTP (VLAN Trunking Protocol) & STP (Spanning Tree Protocol) cơ bản** sẽ giúp bạn trải nghiệm cảm giác quản trị mạng quy mô lớn: chỉ tạo VLAN trên con Switch Server, toàn bộ các Switch Client tự động cập nhật!

---

### Khái niệm cốt lõi

* **VTP (VLAN Trunking Protocol):** Giúp đồng bộ cấu hình VLAN (tạo, xóa, đổi tên) tự động giữa các Switch trong cùng một **VTP Domain**.
* **VTP Server:** Switch quản lý chính (cho phép tạo/sóa VLAN, quảng bá thông tin cho mạng).
* **VTP Client:** Switch con (chỉ nhận đồng bộ VLAN từ Server, **không** cho tạo VLAN trực tiếp).


* **STP (Spanning Tree Protocol):** Cơ chế tự động khóa 1 cổng bị dư thừa (Redundant link) để chống **vòng lặp mạng (Loop)** gây đơ giật hệ thống khi ta nối các Switch thành hình vòng tròn.

---

### Sơ đồ & Cấu hình Lab 9

Ta dựng mô hình gồm **3 Switch 2960** nối thành hình tam giác (để kích hoạt vòng lặp dự phòng cho STP) và **2 PC**:

```text
               [ Switch 1 ] (VTP Server)
                /        \
          Gi0/1/          \Gi0/2
              /            \
  (VTP Client)              (VTP Client)
  [ Switch 2 ]--------------[ Switch 3 ]
       | f0/1       Gi0/2      | f0/1
     [ PC0 ]                 [ PC1 ]
   (VLAN 10)               (VLAN 20)

```

---

1. **1. Dựng sơ đồ & Nối dây:** 3 Switch 2960 & 2 PC.
* Cắm dây cáp nối 3 Switch với nhau thành hình tam giác:
* **Switch1** `Gi0/1` $\leftrightarrow$ **Switch2** `Gi0/1`
* **Switch1** `Gi0/2` $\leftrightarrow$ **Switch3** `Gi0/1`
* **Switch2** `Gi0/2` $\leftrightarrow$ **Switch3** `Gi0/2`


* **PC0** cắm vào `f0/1` của **Switch2** (`192.168.10.2/24`, Gateway `192.168.10.1`).
* **PC1** cắm vào `f0/1` của **Switch3** (`192.168.20.2/24`, Gateway `192.168.20.1`).

> **Quan sát STP:** Vừa nối xong 3 Switch thành vòng tròn, bạn sẽ thấy 1 cổng lập tức bị đổi sang **Màu Cam (Block)**. Đó chính là STP đang làm việc để chống lặp mạng!


2. **2. Mở Trunk trên tất cả đường nối giữa các Switch:** Bắt buộc cho VTP.
VTP **chỉ chạy được trên đường Trunk**. Hãy bật Trunk trên cả 3 Switch:

On **Switch1**:

```text
Switch1(config)# interface range GigabitEthernet0/1 - 2
Switch1(config-if-range)# switchport mode trunk

```

On **Switch2** & **Switch3**:

```text
(config)# interface range GigabitEthernet0/1 - 2
(config-if-range)# switchport mode trunk

```


3. **3. Cấu hình VTP trên 3 Switch:** Tạo VTP Domain & Server.
* **Tại Switch1 (Đóng vai trò VTP Server):**

```text
Switch1(config)# vlan domain LAB9_DOMAIN
Switch1(config)# vtp mode server
Switch1(config)# vtp password cisco

```

* **Tại Switch2 & Switch3 (Đóng vai trò VTP Client):**

```text
(config)# vtp domain LAB9_DOMAIN
(config)# vtp mode client
(config)# vtp password cisco

```


4. **4. Thử nghiệm đồng bộ VLAN tự động:** Phát huy sức mạnh VTP.
Chỉ đứng tại **Switch1 (Server)** gõ tạo 2 VLAN:

```text
Switch1(config)# vlan 10
Switch1(config-vlan)# name KET_TOAN
Switch1(config-vlan)# exit

Switch1(config)# vlan 20
Switch1(config-vlan)# name NHAN_SU
Switch1(config-vlan)# exit

```

Sau đó sang **Switch2** hoặc **Switch3** (Client) gõ lệnh:

```text
Switch# show vlan brief

```

Bạn sẽ thấy **VLAN 10** và **VLAN 20** tự động xuất hiện mà không cần gõ tay!


5. **5. Kiểm tra trạng thái STP:** Soi cổng bị STP khóa.
Trên cả 3 Switch, gõ lệnh:

```text
Switch# show spanning-tree

```

Quan sát xem con Switch nào là **Root Bridge** và cổng nào đang bị đổi sang trạng thái **BLK (Blocking)**.

Thử ngay cho nóng bạn ơi!

Bạn chỉ cần gán 2 cổng cắm PC trên **Switch 2** và **Switch 3** vào VLAN 10 là gói tin sẽ tự tìm đường chạy qua cây Spanning Tree mượt mà ngay.

---

### Các bước gán cổng nhanh:

**Trên Switch 2:**

```text
Switch2(config)# interface FastEthernet0/1
Switch2(config-if)# switchport mode access
Switch2(config-if)# switchport access vlan 10

```

**Trên Switch 3:**

```text
Switch3(config)# interface FastEthernet0/1
Switch3(config-if)# switchport mode access
Switch3(config-if)# switchport access vlan 10

```

# Lý thuyết

### 1. Tại sao lại cần tới 3 cái Switch? Nó dùng để làm gì?

Trong bài **Lab 9**, việc dùng 3 Switch nối thành **hình tam giác** phục vụ cho 2 mục đích cốt lõi của mạng doanh nghiệp:

* **Tạo đường đi dự phòng (Redundancy):** Nếu chỉ có 2 Switch nối 1 sợi dây, sợi dây đó đứt là mạng sập. Khi dùng 3 Switch nối hình tam giác: nếu đường nối giữa Switch 2 và Switch 3 bị hỏng, dữ liệu vẫn có thể đi vòng qua Switch 1 để tới nơi.
* **Tạo ra mô hình thử nghiệm STP:** Khi nối 3 Switch thành vòng tròn khép kín, một hiện tượng nguy hiểm gọi là **Loop (Vòng lặp Layer 2)** sẽ xuất hiện. Nhờ mô hình 3 Switch này, bạn mới tận mắt thấy **Spanning Tree Protocol (STP)** ra tay "khóa" 1 cổng lại (chuyển sang màu cam / trạng thái `BLK`) để cứu hệ thống khỏi bị đơ giật do bão tin nhắn (Broadcast Storm).

---

### 2. Bản chất các câu lệnh CLI & Tại sao lại chia Server / Client?

#### A. Cụm lệnh cấu hình Trunk & VTP

* **`interface range GigabitEthernet0/1 - 2`**:
* *Bản chất:* Chọn **cùng lúc nhiều cổng** (từ `Gi0/1` đến `Gi0/2`) để gõ lệnh một lần cho nhanh, thay vì phải chui vào từng cổng gõ lặp đi lặp lại.


* **`vtp domain LAB9_DOMAIN`**:
* *Bản chất:* Đặt tên "khu vực quản lý" chung cho các Switch. Các Switch muốn chia sẻ bảng VLAN tự động với nhau thì **bắt buộc phải nằm chung một VTP Domain** (giống như gia nhập cùng một nhóm Wi-Fi hay một lớp học).


* **`vtp mode server` (Tại sao lại gõ ở Switch 1?)**:
* *Bản chất:* Đánh dấu Switch 1 làm **Sếp Tổng (Trung tâm điều hành VLAN)**. Chỉ có mode Server mới có quyền: **Tạo mới, Xóa, hoặc Đổi tên VLAN**. Mọi thay đổi về VLAN bạn gõ trên con Server này sẽ được "phóng" bản tin quảng bá qua đường Trunk tới tất cả các Switch khác.


* **`vtp mode client` (Tại sao lại gõ ở Switch 2 và Switch 3?)**:
* *Bản chất:* Đánh dấu Switch 2 và 3 là **Nhân viên (Chỉ nghe và làm theo)**. Mode Client **không cho phép người dùng tự tạo hay xóa VLAN trực tiếp trên nó**. Nó chỉ thụ động lắng nghe cập nhật từ Server và tự động lưu danh sách VLAN đó vào bộ nhớ.



> **Lợi ích:** Trong mạng 100 cái Switch, bạn chỉ cần đứng ở con **Server (Switch 1)** tạo 20 cái VLAN, 99 con **Client** còn lại sẽ tự có VLAN ngay lập tức — không cần chạy gõ tay 99 lần!

#### B. Bản chất các câu lệnh `show`

* **`show vlan brief`**:
* *Bản chất:* Yêu cầu Switch mở "Sổ danh bạ VLAN" hiện tại. Cho bạn biết trên Switch này đang có những VLAN nào (ID và Tên), và cổng nào đang thuộc VLAN nào.


* **`show spanning-tree`**:
* *Bản chất:* Yêu cầu Switch mở "Bản đồ cây Spanning Tree". Cho bạn biết ai đang là Root Bridge (Sếp lớn), vai trò của từng cổng (`Root Port`, `Designated Port`, hay `Alternate Port`), và cổng nào đang chạy (`FWD`) hay đang bị khóa (`BLK`).



---

### 3. Bản chất của các thông tin hiện ra sau khi `show`

#### A. Thông tin từ `show vlan brief`

Nó cho bạn thấy **"kết quả của ma thuật VTP"**: Mặc dù bạn hoàn toàn không gõ lệnh `vlan 10` hay `vlan 20` trên Switch 2 và Switch 3, nhưng khi `show` lên vẫn thấy `VLAN 10 KET_TOAN` xuất hiện. Đó là bằng chứng VTP đã đồng bộ dữ liệu thành công từ Server xuống.

#### B. Thông tin từ `show spanning-tree`

Nó cho bạn thấy **"bản đồ bầu chọn và phân công nhiệm vụ của STP"**:

* **`Root ID Address 0001.9783.982A`**: Địa chỉ MAC của con Switch làm Root Bridge.
* **`This bridge is the root`** (khi show trên Switch 3): Khẳng định Switch 3 thắng cuộc bầu chọn (vì MAC nhỏ nhất) và làm "trung tâm điều phối" cho toàn mạng.
* **`Altn BLK`** (trên cổng `Gi0/1` của Switch 2): Dòng chữ này giải thích tại sao cổng đó lại hiện **màu cam**. STP đã chủ động hy sinh cổng này, không cho dữ liệu người dùng đi qua để triệt hạ vòng lặp mạng.

---

### 4. Tại sao chỉ gán cổng trên Switch 2 & Switch 3? Và tại sao lại phải gán VLAN 10 hết?

* **Tại sao chỉ gán cổng trên Switch 2 và 3?**
* Vì theo sơ đồ, **PC0 cắm trực tiếp vào Switch 2** và **PC1 cắm trực tiếp vào Switch 3**. Switch 1 ở đây đóng vai trò là con Switch trung tâm (Core/Distribution) truyền dẫn ở tầng trên, không có PC nào cắm trực tiếp vào nó. Cổng nào cắm vào PC thì cổng đó mới cần gán vào VLAN của PC đó (`access vlan`).


* **Tại sao lại gán cùng VLAN 10 hết?**
* **Nguyên tắc Layer 2:** Các thiết bị muốn giao tiếp, nói chuyện trực tiếp (ping) được với nhau qua Switch ở Layer 2 **bắt buộc phải nằm cùng một VLAN** (cùng một Broadcast Domain).
* Bạn gán cả PC0 (trên Switch 2) và PC1 (trên Switch 3) vào chung **VLAN 10**, gói tin từ PC0 sẽ được dán nhãn VLAN 10, chạy qua đường Trunk giữa các Switch, đến Switch 3 và chui tọt vào PC1 ngon lành.
* *(Nếu 1 máy gán VLAN 10, 1 máy gán VLAN 20 thì gói tin sẽ bị chặn ngay tại Switch, muốn ping thông bắt buộc phải lắp thêm Router để làm Inter-VLAN Routing như bài Lab 7!)*

# Câu hỏi thêm

Đây chính là lúc **Spanning Tree Protocol (STP)** phô diễn sức mạnh thực sự của nó: **Khả năng tự chữa lành (Self-Healing / Fault Tolerance)** của hệ thống mạng!

Chuyện gì sẽ xảy ra khi bạn rút dây `Gi0/2` kết nối giữa **Switch 2** và **Switch 3**? Dưới đây là diễn biến chính xác từng bước theo thời gian:

---

### 1. Hiện tượng tức thì: Mất đường đi trực tiếp

* Ngay khi dây bị rút, cổng `Gi0/2` trên cả Switch 2 và Switch 3 chuyển sang trạng thái `Down`.
* Đã mất hoàn toàn kết nối trực tiếp giữa Switch 2 và Switch 3.

---

### 2. STP kích hoạt cơ chế "Tự chữa lành" (Convergence)

Trước khi rút dây, cổng `Gi0/1` trên Switch 2 đang bị STP khóa (**`Altn BLK` - Màu cam**).

Khi mất đường đi chính về Root Bridge (Switch 3) qua cổng `Gi0/2`:

1. **Switch 2 phát hiện sự cố:** Nó không còn nhận được các bản tin **BPDU** (bản tin chào hỏi của STP) gửi từ Root Bridge qua cổng `Gi0/2` nữa.
2. **Kích hoạt cổng dự phòng:** Switch 2 lập tức nhận ra cổng `Gi0/1` (đang nối sang Switch 1) chính là lối thoát duy nhất còn lại để đi về Root Bridge!
3. **Chuyển đổi trạng thái:** Cổng `Gi0/1` từ **Màu cam (`BLK`)** sẽ mất khoảng **30 - 50 giây** (theo chuẩn PVST+ mặc định) để trải qua các giai đoạn:
* **Listening (15s):** Lắng nghe BPDU để đảm bảo không tạo loop mới.
* **Learning (15s):** Học lại bảng địa chỉ MAC.
* **Forwarding (FWD - Màu xanh):** Chính thức mở cổng truyền dữ liệu!



*(Lưu ý: Nếu dùng chuẩn STP đời mới hơn như Rapid STP - RSTP, quá trình này chỉ mất chưa tới **2 giây**!)*

---

### 3. Kết quả luồng dữ liệu (Ping) sau khi rút dây

* **Trong 30s đầu (đang chuyển đổi):** Nếu bạn đứng từ PC0 ping PC1, bạn sẽ thấy bị rớt vài gói tin (`Request timed out`) do cổng dự phòng chưa kịp mở hẳn.
* **Sau 30s (khi cổng `Gi0/1` chuyển sang màu Xanh/FWD):**
* Lệnh `ping` từ PC0 sang PC1 sẽ **THÔNG NGHỆO TRỞ LẠI (Reply)**!
* **Luồng đi mới:** Gói tin từ **PC0 (Switch 2)** $\rightarrow$ chạy lên **Switch 1** $\rightarrow$ chạy sang **Switch 3** $\rightarrow$ chui xuống **PC1**.



---

### Tóm lại bản chất

Nhờ có STP: **Hệ thống mạng không bị gián đoạn vĩnh viễn khi gặp sự cố đứt dây.** STP đã hy sinh 1 đường dây lúc bình thường để chống loop, nhưng sẵn sàng "mở khóa" đường dây đó ra làm cứu viện ngay khi mạng có sự cố!

Bạn có thể thử nghiệm rút dây ngay trong Packet Tracer để thấy cổng màu cam tự động chuyển sang màu xanh luôn nhé!

# Rút dây giữa switch 2 vs 3 thử

Tuyệt vời! Học mạng là phải "động tay động chân" phá mô hình thực tế như thế này mới ngấm sâu bản chất được.

Để quan sát được trọn vẹn quá trình STP hội tụ (Convergence) một cách trực quan nhất, bạn hãy làm theo kịch bản "phá hoại" có kiểm soát này nhé:

1. **Bật Ping liên tục trên PC:** Sử dụng tham số -t.
Mở Command Prompt của **PC0**, gõ lệnh `ping 192.168.10.3 -t` (thêm chữ `-t` để PC ping liên tục không ngừng).
Kéo cửa sổ Command Prompt sang một góc màn hình để tiện quan sát luồng dữ liệu.


2. **Cắt đứt liên kết chính:** Xóa dây nối SW2 - SW3.
Chọn công cụ **Delete** trong Packet Tracer (biểu tượng dấu X đỏ hoặc bấm phím `Del`), sau đó click thẳng vào sợi dây cáp đang nối giữa **Switch 2** và **Switch 3**.


3. **Quan sát phép màu của STP:** Đếm ngược 30 giây.
Ngay lập tức, bạn sẽ thấy:

1. Cửa sổ Ping báo lỗi `Request timed out` liên tục (do đường đi chính đã đứt).
2. Nhìn lên sơ đồ, cổng **màu cam** trên Switch 2 bắt đầu nhấp nháy. Nó đang trải qua các bước Listening và Learning để kiểm tra độ an toàn.
3. Khoảng 30 đến 50 giây sau, cổng đó bật sang **Màu Xanh lá (Forwarding)**.
4. Lúc này, cửa sổ Ping của PC0 lập tức nổ `Reply from 192.168.10.3` trở lại!