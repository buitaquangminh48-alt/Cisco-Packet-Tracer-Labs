# Đề bài lab 8

Hào khí đang lên! Tiến thẳng vào **Lab 8: Dynamic Trunking Protocol (DTP) & Native VLAN** — bài này sẽ giúp bạn hiểu sâu hơn nữa về cách các cổng trên Switch "thỏa thuận" đường Trunk với nhau và cách xử lý các gói tin không mang nhãn.

---

### Khái niệm cốt lõi

* **DTP (Dynamic Trunking Protocol):** Giao thức độc quyền của Cisco, giúp 2 cổng Switch tự động "nói chuyện" và thương lượng để quyết định xem có nên biến đường nối giữa chúng thành đường Trunk hay không.
* **Native VLAN:** Là VLAN duy nhất trên đường Trunk mà dữ liệu đi qua **KHÔNG BỊ DÁN NHÃN (Untagged)**. Mặc định luôn là VLAN 1. Nếu 2 bên Switch lệch Native VLAN, mạng sẽ bị loop hoặc rò rỉ dữ liệu.

---

### Sơ đồ & Cấu hình Lab 8

* **2 Switch 2960** (Nối với nhau qua cổng `Gi0/1`)
* **2 PC**:
* PC0 (thuộc SW1, cắm `f0/1` - VLAN 10)
* PC1 (thuộc SW2, cắm `f0/1` - VLAN 10)



---

1. **1. Dựng sơ đồ & Nối dây:** 2 Switch nối nhau.
* Nối cổng `Gi0/1` của **Switch1** sang cổng `Gi0/1` của **Switch2** (dùng dây chéo hoặc dây thẳng, Switch đời mới tự MDIX).
* PC0 cắm `f0/1` Switch1 (`192.168.10.2/24`).
* PC1 cắm `f0/1` Switch2 (`192.168.10.3/24`).


2. **2. Cấu hình DTP trên 2 Switch:** Thử nghiệm chế độ Dynamic.
Mở CLI trên **Switch1**:

```text
Switch1(config)# interface GigabitEthernet0/1
Switch1(config-if)# switchport mode dynamic desirable

```

Mở CLI trên **Switch2**:

```text
Switch2(config)# interface GigabitEthernet0/1
Switch2(config-if)# switchport mode dynamic auto

```


3. **3. Đổi Native VLAN mặc định (Tránh VLAN Hopping):** Bảo mật Native VLAN.
Mặc định Native VLAN là 1 (rất dễ bị tấn công). Ta đổi sang VLAN 99 trên cả 2 Switch:

On **Switch1** & **Switch2**:

```text
(config)# vlan 99
(config-vlan)# name NATIVE_MGT
(config-vlan)# exit
(config)# interface GigabitEthernet0/1
(config-if)# switchport trunk native vlan 99

```


4. **4. Kiểm tra trạng thái DTP & Native VLAN:** Show commands.
Trên Switch1, gõ câu lệnh thần thánh:

```text
Switch1# show interfaces GigabitEthernet0/1 switchport

```

Quan sát các dòng: `Administrative Mode`, `Operational Mode`, và `Native VLAN mode`.

# Bảng ma trận thương lượng DTP (DTP Negotiation Matrix)

Vì sao một bên dynamic desirable và một bên dynamic auto lại ra được Trunk? Đây là quy tắc kết hợp của Cisco:

| Chế độ trên SW1 | Chế độ trên SW2 | Kết quả đường liên kết | Giải thích |
| --- | --- | --- | ---|
| **Dynamic Desirable** | **Dynamic Auto** | **Trunk** | "SW1 chủ động xin lên Trunk SW2 bật chế độ ""sao cũng được"" → Đồng ý lên Trunk!" |
| **Dynamic Auto** | **Dynamic Auto** | **Access** | "Cả 2 bên cùng thụ động chờ bên kia mở lời → Không ai nói gì, giữ nguyên Access." |
|**Dynamic Desirable** | **Dynamic Desirable** | **Trunk** | Cả 2 bên cùng chủ động đòi lên Trunk → Bắt tay lên Trunk ngay lập tức. |
| **Trunk (Cứng)** | **Dynamic Auto / Desirable** | **Trunk** | 1 bên ép Trunk cứng → Bên Dynamic tự động chuyển sang Trunk theo. |

# Lý thuyết 

Lên bài đặc sản lý thuyết tiếp luôn! Tinh thần cày bản chất đến tận cùng thế này thì sau này làm hệ thống lớn cực kỳ vững tay. Giải đáp sạch sẽ 2 cụm thắc mắc của bạn đây:

---

### 1. Tại sao phải nối 2 Switch lại với nhau? Nó có tác dụng gì?

Trong thực tế, một doanh nghiệp hay một tòa nhà không bao giờ chỉ dùng **1 cái Switch**:

* **Mở rộng quy mô (Scale):** Tòa nhà có 5 tầng, mỗi tầng cắm 1-2 cái Switch. Để PC ở tầng 1 nói chuyện được với PC ở tầng 5, bạn bắt buộc phải nối các Switch ở các tầng lại với nhau (tạo thành đường liên kết **Switch-to-Switch**).
* **Truyền tải đa VLAN qua không gian:** Nhờ nối 2 Switch bằng **đường Trunk**, phòng Kế toán (VLAN 10) ngồi ở tầng 1 vẫn kết nối trực tiếp mượt mà với phòng Kế toán ngồi ở tầng 5 trên cùng một phân mạng LAN, dù họ cắm vào 2 Switch hoàn toàn khác nhau!

---

### 2. Bản chất các câu lệnh & Con số 99 thần thánh

#### A. Các câu lệnh cấu hình (`dynamic desirable`, `dynamic auto`, `native vlan 99`)

* **`switchport mode dynamic desirable`**:
* *Bản chất:* Lệnh "chủ động tán tỉnh". Cổng Switch này sẽ **liên tục gửi bản tin DTP** sang bên kia và bảo: *"Tớ rất muốn lên Trunk, bên kia lên Trunk với tớ nhé!"*.


* **`switchport mode dynamic auto`**:
* *Bản chất:* Lệnh "thụ động bật đèn xanh". Cổng này **không chủ động hỏi**, nhưng đứng chờ. Nếu bên kia sang hỏi *"Lên Trunk nhé?"*, nó sẽ gật đầu *"Ok lên luôn!"*.


* **`switchport trunk native vlan 99`**:
* *Bản chất:* Đổi "Làn đường không dán nhãn" trên đường Trunk từ VLAN 1 mặc định sang **VLAN 99**.
* *Tại sao lại là con số 99?* $\rightarrow$ **Thực ra chọn số nào cũng được (từ 2 đến 4094)!** Người ta hay chọn các số đẹp như `99`, `999`, hay `888` theo thói quen đặt tên cho các VLAN đặc biệt (Management/Native). Quan trọng nhất là **chọn một con số VLAN mà KHÔNG CÓ BẤT KỲ PC NÀO CẮM VÀO ĐÓ**, để cô lập hoàn toàn luồng dữ liệu thô (untagged) này, chặn đứng kẻ gian hack VLAN Hopping.



---

#### B. Giải mã thông số hiển thị từ `show interfaces GigabitEthernet0/1 switchport`

Căn cước công dân của cổng `Gi0/1` hiển thị cực kỳ chi tiết:

| Thông số hiển thị | Ý nghĩa bản chất |
| --- | --- |
| **`Administrative Mode: dynamic auto`** | **Cấu hình mong muốn:** Cổng đang được người quản trị gán chế độ thụ động (`auto`). |
| **`Operational Mode: trunk`** | **Trạng thái thực tế:** Nhờ bên kia đòi lên Trunk (`desirable`), cổng này đã thương lượng thành công và **hiện tại đang chạy Trunk**. |
| **`Administrative Trunking Encapsulation: dot1q`** | Chuẩn đóng gói nhãn VLAN đang dùng là **IEEE 802.1Q** (chuẩn chung toàn thế giới). |
| **`Negotiation of Trunking: On`** | Giao thức **DTP đang BẬT** để trao đổi thông tin thương lượng giữa 2 Switch. |
| **`Trunking Native Mode VLAN: 99 (NATIVE_MGT)`** | Native VLAN hiện tại đã được chốt là **VLAN 99** (mang tên `NATIVE_MGT`). |

# Câu hỏi thêm

```text
Switch(config)#vlan 99
Switch(config-vlan)#name NATIVE_MG
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on GigabitEthernet0/1 (1), with Switch GigabitEthernet0/1 (99).
name NATIVE_MGT
                                       ^
% Invalid input detected at '^' marker.
	
Switch(config-vlan)#

Ghi name của 2 switch ko giống nhau thì có sao ko?

```

**Câu trả lời:**
Không sao hết! Việc đổi tên (`name`) khác nhau giữa 2 Switch **hoàn toàn KHÔNG làm ảnh hưởng** đến kỹ thuật mạng.

Bản chất của vấn đề nằm ở hai yếu tố sau:

1. **Tên VLAN (`name`) chỉ mang giá trị hiển thị:**
* Tên `NATIVE_MGT` hay `NATIVE_MG` chỉ giúp người quản trị đọc cho dễ hiểu thôi.
* Đối với Switch, nó **chỉ quan tâm đến CON SỐ ID của VLAN** (ở đây là số **`99`**). Hai bên Switch đều dùng chung VLAN ID là `99` là đúng chuẩn 100%.


2. **Dòng cảnh báo `%CDP-4-NATIVE_VLAN_MISMATCH` xuất hiện là vì sao?**
* Dòng đó xuất hiện khi bạn mới cấu hình Native VLAN 99 trên **một** Switch, còn con Switch còn lại vẫn đang dùng Native VLAN mặc định là `1`.
* Giao thức CDP (Cisco Discovery Protocol) phát hiện ra hai đầu dây bị lệch ID Native VLAN (1 bên là `1`, 1 bên là `99`) nên lập tức quăng cảnh báo ra màn hình.



---

### Cách sửa lại cho đẹp đội hình (Cách gõ đúng lệnh đổi tên)

Do lúc nãy bạn gõ câu lệnh `name NATIVE_MGT` khi đang bị dòng log cảnh báo nhảy đè lên, cursor bị lệch vị trí nên CLI mới báo `% Invalid input`.

Muốn đổi lại tên cho đúng chuẩn trên Switch đó, bạn chỉ cần gõ lại:

```text
Switch(config)# vlan 99
Switch(config-vlan)# name NATIVE_MGT
Switch(config-vlan)# exit

```

Khi bạn cấu hình `switchport trunk native vlan 99` đồng bộ trên cả 2 Switch xong, dòng cảnh báo `NATIVE_VLAN_MISMATCH` sẽ tự động biến mất!