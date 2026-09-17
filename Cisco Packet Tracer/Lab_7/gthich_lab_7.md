# Đề bài lab 7

Chào ngày mới! Tinh thần cày lab thế này là quá tuyệt vời.

Hôm qua ở **Lab 6**, chúng ta đã dùng VLAN để **chia cắt** Switch, chặn hoàn toàn giao tiếp giữa phòng Kế toán và Nhân sự. Hôm nay ở **Lab 7: Inter-VLAN Routing (Router-on-a-Stick)**, nhiệm vụ của bạn là dựng một "cây cầu" để cho phép các VLAN giao tiếp với nhau một cách có kiểm soát thông qua **Router**.

---

### Mô hình khái niệm: Router-on-a-Stick (ROAS)

Vì Switch cắm vào Router chỉ qua **1 sợi dây cáp duy nhất**, ta sẽ chia sợi dây cáp vật lý đó thành nhiều **cổng ảo (Sub-interfaces)** trên Router. Mỗi cổng ảo sẽ làm **Default Gateway** cho một VLAN.

---

### Sơ đồ & Cấu hình Lab 7

* **1 Router 2911**
* **1 Switch 2960**
* **4 PC**:
* PC0, PC1: Thuộc **VLAN 10** (`192.168.10.x/24`, Gateway: `192.168.10.1`)
* PC2, PC3: Thuộc **VLAN 20** (`192.168.20.x/24`, Gateway: `192.168.20.1`)



---

1. **1. Nối dây từ Switch lên Router:** Tận dụng sơ đồ Lab 6.
* Cắm sợi dây thẳng từ cổng `Gi0/1` của Switch 2960 lên cổng `Gi0/0/0` của Router 2911.


2. **2. Cấu hình Trunking trên Switch:** Bắt buộc đối với đường nối Router-Switch.
Đường dây nối lên Router phải chở dữ liệu của **tất cả các VLAN**, nên cổng đó phải là cổng **Trunk**:

```text
Switch> enable
Switch# configure terminal
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

```


3. **3. Cấu hình Sub-interfaces trên Router:** Kỹ thuật Sub-interface.
Vào Router, bật cổng chính và tạo các cổng ảo tương ứng với từng VLAN:

```text
Router> enable
Router# configure terminal

! Bật cổng vật lý (không đặt IP ở cổng này)
Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# no shutdown
Router(config-if)# exit

! Tạo Sub-interface cho VLAN 10
Router(config)# interface GigabitEthernet0/0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

! Tạo Sub-interface cho VLAN 20
Router(config)# interface GigabitEthernet0/0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# exit

```


4. **4. Cập nhật Default Gateway trên các PC:** Điền Gateway cho PC.
* **PC0 & PC1 (VLAN 10):** Điền Default Gateway là `192.168.10.1`
* **PC2 & PC3 (VLAN 20):** Điền Default Gateway là `192.168.20.1`


5. **5. Kiểm tra kết quả (Test Ping):** Kiểm tra liên thông.
Từ **PC0 (VLAN 10 - 192.168.10.x)**, mở Command Prompt và ping sang **PC2 (VLAN 20 - 192.168.20.x)**.
Gói tin đầu tiên có thể bị `Request timed out` do quá trình ARP, nhưng từ gói thứ 2 sẽ hiện **`Reply`** thành công!

# Lý thuyết

Quá chuẩn! Làm xong bài lab mà "mổ xẻ" lại toàn bộ bản chất từng câu lệnh như thế này thì kiến thức mới thực sự là của mình. Giải đáp trọn gói 4 cụm thắc mắc "đặc sản" cho bạn đây:

**1. Vì sao IPv4 của PC không được đặt trùng với Gateway?**

* **Bản chất của IP:** Địa chỉ IP giống như **Số nhà / Số định danh duy nhất** của thiết bị trong mạng. Nếu hai thiết bị trong cùng một mạng LAN đặt trùng IP, toàn bộ Switch và các máy khác sẽ bị "bấn loạn" không biết gói tin gửi đến đâu.
* **Bản chất của Gateway:** Gateway chính là địa chỉ IP đại diện cho cổng của Router.
* **Chuyện gì xảy ra nếu trùng?** Nếu PC0 đặt IP trùng với Gateway (`192.168.10.1`), khi PC1 muốn gửi gói tin ra ngoài internet, PC1 hỏi: *"Ai là Gateway `192.168.10.1`?"*. Lúc này cả Router lẫn PC0 đều giơ tay! Gói tin sẽ bị gửi nhầm sang PC0 thay vì Router, gây ra hiện tượng **Xung đột IP (IP Conflict)** và làm rớt mạng hoàn toàn.

---

**2. Vì sao lại cần Router trong trường hợp này?**

* **Switch Layer 2 chỉ hiểu "Nhãn VLAN":** Switch thông thường chỉ biết chuyển mạch dựa trên MAC và nhãn VLAN. Nó đứng ở **Layer 2 (Data Link)**. Nếu gói tin ở VLAN 10 đến Switch, Switch thấy nhãn 10 và **tuyệt đối không bao giờ** cho nó tự nhảy sang cổng VLAN 20.
* **Router hiểu "Định tuyến Layer 3 (Network)":** Router là thiết bị làm nhiệm vụ chuyển giao gói tin giữa các mạng/VLAN khác nhau dựa trên IP.
* **Nhiệm vụ của Router:** Khi PC0 (VLAN 10) muốn nói chuyện với PC2 (VLAN 20), gói tin phải đi qua Router. Router nhận gói tin từ VLAN 10, **bóc bỏ nhãn VLAN 10**, kiểm tra bảng định tuyến, **dán nhãn VLAN 20 vào**, rồi đẩy ngược xuống Switch để đến PC2.

---

**3. Bản chất của loạt lệnh Trunk, Sub-interface & Dot1Q**

* **`switchport mode trunk`**:
* *Bản chất:* Chuyển cổng Switch từ chế độ Access (chỉ chở 1 VLAN) sang chế độ **Trunk (Đường ống chứa nhiều VLAN)**.
* *Cách hoạt động:* Nó biến đường cáp đó thành một "đường cao tốc" cho phép các gói tin của VLAN 10, VLAN 20, VLAN 30... cùng chạy qua mà không bị trộn lẫn (nhờ nhãn VLAN).


* **`.10` và `.20` ở đằng sau tên cổng (`GigabitEthernet0/0.10`)**:
* *Bản chất:* Cổng `GigabitEthernet0/0` là cổng **vật lý** (sợi dây cáp thật). Dấu chấm `.10` hay `.20` chính là việc bạn chia cổng vật lý đó thành các **cổng ảo (Sub-interface / Cổng phụ)** trong phần mềm.
* *Tại sao lại cần?* Vì Router chỉ có 1 cổng cắm dây xuống Switch, nhưng bạn lại có 2 VLAN. Việc chia thành `g0/0.10` và `g0/0.20` giúp Router tạo ra 2 Gateway riêng biệt trên cùng 1 sợi dây!


* **`encapsulation dot1Q 10`**:
* *Bản chất:* Dặn cổng ảo `g0/0.10`: *"Hãy sử dụng chuẩn đóng gói nhãn **IEEE 802.1Q (gọi tắt là dot1Q)**, và cổng ảo này chuyên trách xử lý các gói tin mang **nhãn VLAN 10**"*.



---

**4. Bản chất câu lệnh `show interfaces trunk**`

* **Bản chất:** Đây là câu lệnh "soi" sức khỏe của đường ống Trunk trên Switch.
* **Ý nghĩa các chỉ số khi soi:**
* **Mode `on`:** Cổng đã mở chế độ Trunk cứng.
* **Encapsulation `802.1q`:** Đang dùng chuẩn dán nhãn chuẩn quốc tế IEEE 802.1Q.
* **Vlans allowed and active:** Danh sách các VLAN đã được tạo trên Switch và đang được phép "chạy" qua đường Trunk này. (Hôm nãy bài lab bị lỗi vì chỗ này chỉ có VLAN 1, thiếu VLAN 10 và 20 đấy!).