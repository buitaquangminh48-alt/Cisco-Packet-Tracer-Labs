# Đề bài lab 6

Thần tốc đấy! Vào luôn **Lab 6: Chia VLAN (Virtual LAN) trên Switch** — bài này cực kỳ quan trọng và thực tế trong môi trường doanh nghiệp.

Bình thường, cắm 10 máy vào cùng 1 Switch là 10 máy nhìn thấy nhau và nhận chung gói tin **Broadcast**. Nhưng thực tế, công ty sẽ muốn **Phòng Kế toán** và **Phòng IT** cắm chung 1 Switch mà **máy Kế toán không thể ping hay thấy máy IT** (để bảo mật). Đó là lý do ta chia **VLAN**.

---

**Sơ đồ & Cấu hình Lab 6**

* **1 Switch 2960**
* **4 PC**:
* PC0, PC1: Thuộc **VLAN 10** (Tên: `KE_TOAN` - Dải IP: `192.168.10.x/24`)
* PC2, PC3: Thuộc **VLAN 20** (Tên: `NHAN_SU` - Dải IP: `192.168.20.x/24`)



---

1. **1. Dựng sơ đồ & Cắm dây:** 4 PC cắm vào 1 Switch.
* PC0 cắm vào cổng `FastEthernet0/1` của Switch
* PC1 cắm vào cổng `FastEthernet0/2` của Switch
* PC2 cắm vào cổng `FastEthernet0/3` của Switch
* PC3 cắm vào cổng `FastEthernet0/4` của Switch


2. **2. Đặt IP thủ công cho 4 PC:** IP tĩnh trên các PC.
* **PC0 (VLAN 10):** IP `192.168.10.1`, Subnet Mask `255.255.255.0`
* **PC1 (VLAN 10):** IP `192.168.10.2`, Subnet Mask `255.255.255.0`
* **PC2 (VLAN 20):** IP `192.168.20.1`, Subnet Mask `255.255.255.0`
* **PC3 (VLAN 20):** IP `192.168.20.2`, Subnet Mask `255.255.255.0`


3. **3. Tạo VLAN & Gán cổng trên Switch:** CLI trên Switch.
Mở CLI của **Switch 2960** và gõ lần lượt:

```text
enable
configure terminal

! --- Tạo VLAN 10 ---
vlan 10
 name KE_TOAN
exit

! --- Tạo VLAN 20 ---
vlan 20
 name NHAN_SU
exit

! --- Gán cổng f0/1 và f0/2 vào VLAN 10 ---
interface range FastEthernet0/1 - 2
 switchport mode access
 switchport access vlan 10
exit

! --- Gán cổng f0/3 và f0/4 vào VLAN 20 ---
interface range FastEthernet0/3 - 4
 switchport mode access
 switchport access vlan 20
exit

```


4. **4. Kiểm tra kết quả (Test Ping):** Sử dụng lệnh Ping.
* Từ **PC0 (VLAN 10)**, ping sang **PC1 (VLAN 10)** $\rightarrow$ **Thành công (Reply)** vì cùng VLAN.
* Từ **PC0 (VLAN 10)**, ping sang **PC2 (VLAN 20)** $\rightarrow$ **Thất bại (Request timed out)** vì khác VLAN (dù cắm chung 1 Switch physical)!


---

Mở Packet Tracer làm thử xem PC0 có bị "cách ly" khỏi PC2 thành công chưa nhé!

# Lý thuyết

Đặc sản "spam câu hỏi" này mới đúng là thứ tạo nên gốc rễ kiến thức chắc chắn đấy! Giải đáp trọn gói 3 cụm thắc mắc lớn cho bạn đây:

**1. Bản chất của LAN và VLAN là gì?**

* **LAN (Local Area Network - Mạng cục bộ):** Là một tập hợp các thiết bị kết nối chung trong một phạm vi địa lý hẹp (văn phòng, nhà ở). **Bản chất của LAN là một Miền Quảng Báo (Broadcast Domain)** — nghĩa là khi một máy gửi một gói tin Broadcast (gửi cho tất cả mọi người), mọi máy khác cắm chung trong Switch đều sẽ nhận được.
* **VLAN (Virtual LAN - Mạng cục bộ ảo):** Là kỹ thuật **chia nhỏ 1 Switch vật lý thành nhiều Switch logic (ảo) độc lập**. Bản chất là Switch sẽ "dán nhãn" (Tagging - theo chuẩn IEEE 802.1Q) vào khung dữ liệu để đánh dấu gói tin thuộc về VLAN nào. Dữ liệu mang nhãn VLAN 10 sẽ không bao giờ tràn sang cổng mang nhãn VLAN 20.

---

**2. Bản chất các câu lệnh CLI mới trong Lab 6**

* `vlan 10` & `name KE_TOAN`: Khai báo và tạo ra một "phòng chứa ảo" có số hiệu 10 và đặt tên gợi nhớ là `KE_TOAN` trên hệ điều hành Switch.
* `interface range FastEthernet0/1 - 2`: Lệnh gom nhóm (batching). Thay vì gõ cấu hình từng cổng `f0/1` rồi `f0/2`, lệnh này giúp bạn áp đặt cấu hình cho **nhiều cổng cùng lúc** để tiết kiệm thời gian.
* `switchport mode access`: Thiết lập cổng này là **cổng Access (Cổng truy cập)**. Cổng Access là cổng chỉ phục vụ kết nối tới thiết bị cuối (PC, Server, Printer) và chỉ thuộc về **1 VLAN duy nhất**.
* `switchport access vlan 10`: Dặn Switch: *"Hễ có gói tin nào đi vào cổng này, hãy dán ngay cái nhãn VLAN 10 lên nó"*.

---

**3. Tại sao phải tách VLAN và điều gì xảy ra nếu KHÔNG tách?**

Nếu gom chung tất cả phòng ban vào một mạng LAN chung mà không tách VLAN, 3 vấn đề nghiêm trọng sẽ xảy ra:

* **Rủi ro bảo mật & Rò rỉ dữ liệu:** Một nhân viên mới hoặc thực tập sinh phòng Nhân sự chỉ cần dùng phần mềm soi mạng (như Wireshark) là có thể nghe lén, bắt gói tin chứa bảng lương, báo cáo tài chính hoặc thông tin nhạy cảm truyền qua lại giữa các máy Phòng Kế toán.
* **Bão Quảng Báo (Broadcast Storm) gây nghẽn mạng:** Các hệ điều hành máy tính liên tục phát tin Broadcast (ARP Request, DHCP Request...). Nếu công ty có 500 máy cắm chung 1 LAN, 500 máy này sẽ liên tục nhận rác của nhau, khiến Switch bị quá tải và đường truyền bị nghẽn (Lag mạng). Tách VLAN giúp khu lập bão: bão ở VLAN Kế toán thì chỉ ở trong Kế toán, không làm ảnh hưởng Nhân sự.
* **Nguy cơ lây nhiễm Mã độc (Ransomware/Virus):** Nếu PC phòng Nhân sự lỡ click vào file rác bị dính Virus, Virus sẽ quét toàn bộ dải IP xung quanh và lây lan sạch sang hệ thống Server và máy Kế toán trong vài giây. Tách VLAN sẽ chặn đứng con Virus lại ở đúng phạm vi phòng Nhân sự.

Bây giờ bạn đã thấy sức mạnh thực sự của cái lệnh `vlan 10` nhỏ bé chưa?