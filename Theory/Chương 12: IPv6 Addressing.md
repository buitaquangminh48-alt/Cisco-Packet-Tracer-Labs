# Chương 12: Định địa chỉ IPv6 (IPv6 Addressing)

## 1. Lý do ra đời & Sự đồng tồn tại với IPv4

* **Cạn kiệt IPv4**: Địa chỉ IPv4 (32-bit) đã cạn kiệt, không đủ cho sự bùng nổ của IoT và thiết bị di động. IPv6 có không gian địa chỉ khổng lồ với **128 bit**.


* **3 Phương thức chuyển đổi (Migration Techniques)**:


1. **Dual Stack**: Thiết bị chạy song song cả 2 giao thức IPv4 và IPv6 cùng lúc.


2. **Tunneling**: Đóng gói gói tin IPv6 bên trong gói tin IPv4 để gửi qua mạng IPv4.


3. **Translation (NAT64)**: Chuyển đổi gói tin giữa thiết bị IPv6 và thiết bị IPv4.





---

## 2. Quy tắc viết gọn địa chỉ IPv6

IPv6 gồm 128 bit, viết dưới dạng **Hexadecimal** (thập lục phân), chia thành 8 nhóm, mỗi nhóm 4 ký tự Hex gọi là **Hextet**.

* **Quy tắc 1 - Bỏ các số `0` ở đầu (Omit Leading Zeros)**: Chỉ bỏ các số `0` nằm ở **đầu** mỗi Hextet (không bỏ số `0` ở cuối).


* *Ví dụ*: `01ab` $\rightarrow$ `1ab`, `0000` $\rightarrow$ `0`.




* **Quy tắc 2 - Dấu hai dấu hai chấm `::` (Double Colon)**: Thay thế cho một chuỗi các Hextet toàn số `0` liên tiếp nhau.


* *Chú ý*: Chỉ được dùng `::` **đúng 1 lần** duy nhất trong toàn bộ địa chỉ IP.


* *Ví dụ*: `2001:db8:0000:0000:0000:0000:0000:0001` $\rightarrow$ `2001:db8::1`.





---

## 3. Các loại địa chỉ IPv6 Unicast

Không giống IPv4, một giao diện mạng (Interface) IPv6 thường có **nhiều hơn một địa chỉ**:

* **Global Unicast Address (GUA)**:
* Tương tự IP Public của IPv4, có thể định tuyến trên Internet.


* Bắt đầu bằng dải `2000::/3` (ký tự đầu tiên là `2` hoặc `3`).


* **Cấu trúc GUA**: Global Routing Prefix + Subnet ID + Interface ID.




* **Link-Local Address (LLA)**:
* Địa chỉ nội bộ chỉ truyền tin trong cùng 1 Subnet (không thể định tuyến qua Router khác).


*  Bắt buộc phải có trên mọi Interface IPv6.


* Bắt đầu bằng dải `fe80::/10`.




* **Unique Local Address (ULA)**: Tương tự IP Private (`fc00::/7` - `fdff::/7`), dùng trong mạng nội bộ lớn.


* **Loopback Address**: `::1/128`.



> **Lưu ý**: IPv6 **KHÔNG CÓ** địa chỉ Broadcast. Chức năng quảng bá được thay thế bằng **Multicast**.
> 
> 

---

## 4. Các phương thức gán IP động cho GUA (Dynamic IPv6)

Host nhận IP tự động thông qua gói tin ICMPv6 **RA (Router Advertisement)** từ Router:

1. **SLAAC (Stateless Address Autoconfiguration)**:
* Client tự tạo GUA bằng cách lấy **Prefix** từ Router + tự tạo **Interface ID** (64-bit).




2. **SLAAC + Stateless DHCPv6**:
* Client tự tạo IP/Gateway bằng SLAAC, đồng thời hỏi DHCPv6 Server để lấy thêm thông tin phụ (DNS Server, Domain Name).




3. **Stateful DHCPv6**:
* Giống DHCP ở IPv4: DHCPv6 Server cấp toàn bộ IP, Mask, DNS...





### Cách tạo 64-bit Interface ID trong SLAAC:

* **Random 64-bit**: Hệ điều hành (như Windows) tự sinh ngẫu nhiên.


* **Quy trình EUI-64**: Dựa trên MAC address 48-bit:


* Chèn chuỗi `ff:fe` vào chính giữa MAC Address.


* Đảo bit thứ 7 (từ trái sang) của MAC Address từ `0` thành `1`.





---

## 5. Địa chỉ Multicast (`ff00::/8`)

* **All-Nodes Multicast (`ff02::1`)**: Gửi gói tin đến **tất cả** thiết bị IPv6 trong LAN (thay thế cho Broadcast của IPv4).


* **All-Routers Multicast (`ff02::2`)**: Gửi gói tin đến **tất cả** Router IPv6 trong LAN.


* **Solicited-Node Multicast**: Giúp ánh xạ MAC Address nhằm thay thế cho giao thức ARP của IPv4.



---

## 6. Chia Subnet IPv6 (IPv6 Subnetting)

IPv6 cực kỳ dễ chia subnet vì được thiết kế sẵn trường **Subnet ID 16-bit** trong dải GUA `/64` chuẩn:

* **Cấu trúc chuẩn**: `/48 Global Routing Prefix` + `16-bit Subnet ID` + `64-bit Interface ID`.


* Tạo ra được **65,536 Subnet** riêng biệt chỉ bằng cách tăng giá trị Hex ở trường Subnet ID (`0000` $\rightarrow$ `ffff`) mà không cần mượn bit phức tạp như IPv4!



---

## 🛠️ Lệnh Cấu Hình IPv6 Cơ Bản (Cisco IOS)

```text
! Bật tính năng định tuyến IPv6 trên Router (Rất quan trọng!)
R1(config)# ipv6 unicast-routing

! Cấu hình GUA và LLA tĩnh cho Interface
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# no shutdown

! Kiểm tra cấu hình IPv6
R1# show ipv6 interface brief

```

---

# Bài tập

Lên luôn! Dưới đây là **3 Bài tập Thực hành VIP** quét sạch sẽ các dạng tính toán / xử lý IPv6 thường gặp nhất trong đề thi CCNA.

---

### DẠNG 1: Rút gọn & Khôi phục địa chỉ IPv6

#### 1. Rút gọn địa chỉ sau về dạng ngắn nhất:

`2001:0db8:0000:0000:00ab:0000:0000:0001`

* **Bước 1 (Omit Leading Zeros)**: Bỏ các số `0` ở đầu mỗi Hextet.
$\rightarrow$ `2001:db8:0:0:ab:0:0:1`


* **Bước 2 (Double Colon `::`)**: Thay thế nhóm số `0` liên tiếp dài nhất bằng `::`.


* Ở đây có 2 nhóm số `0` liên tiếp: nhóm 2 số `0` và nhóm 2 số `0`. Ta chọn nhóm ở đầu tiên.




* **Ket quả rút gọn**: **`2001:db8::ab:0:0:1`**


#### 2. Khôi phục lại dạng đầy đủ (32 ký tự Hex):

`fe80::1:20`

* Địa chỉ chuẩn có **8 Hextets**. Hiện tại ta thấy có 3 Hextets: `fe80`, `1`, `20`.


* Số Hextet bị ẩn trong `::` = $8 - 3 = \mathbf{5}$ Hextet toàn số `0`.


* Thêm lại các số `0` ở đầu để đủ 4 ký tự Hex cho mỗi Hextet.


* **Kết quả đầy đủ**: **`fe80:0000:0000:0000:0000:0000:0001:0020`**


---

### DẠNG 2: Biến đổi MAC Address thành EUI-64 Interface ID

#### Đề bài:

Cho PC1 có địa chỉ MAC: **`00:1A:2B:3C:4D:5E`**. Hãy tính **EUI-64 Interface ID** và tạo địa chỉ **Link-Local (LLA)** cho PC này.

#### Lời giải:

* **Bước 1**: Chèn chuỗi `FF:FE` vào chính giữa MAC Address.


* Ban đầu: `00:1A:2B` — `3C:4D:5E`

* Thêm `FF:FE`: `00:1A:2B:`**`FF:FE`**`:3C:4D:5E`



* **Bước 2**: Đổi 2 ký tự Hex đầu tiên (`00`) sang Nhị phân để **đảo bit thứ 7**:


* `00` (Hex) = `0000 0000` (Bin)


* Đảo bit thứ 7 từ `0` $\rightarrow$ `1`: `0000 00`**`1`**`0` (Bin) = **`02`** (Hex)




* **Bước 3**: Ghép lại thành Interface ID dạng IPv6 (chia nhóm 4 ký tự Hex):


* `021a:2bff:fe3c:4d5e`



* **Kết quả Link-Local Address (LLA)** (gắn thêm prefix `fe80::/10`):


* **`fe80::21a:2bff:fe3c:4d5e`**




---

### DẠNG 3: Chia Subnet IPv6 (IPv6 Subnetting)

#### Đề bài:

Công ty được ISP cấp cho Global Routing Prefix: **`2001:db8:1234::/48`**. Hãy chia 3 Subnet /64 cho:

1. LAN 1 (Phòng Kế toán)


2. LAN 2 (Phòng Nhập liệu)


3. Link WAN giữa 2 Router



#### Lời giải:

Cấu trúc GUA chuẩn `/64` gồm: `/48 Prefix` + `16-bit Subnet ID` + `64-bit Interface ID`.
Ta chỉ cần tăng giá trị Hex ở Hextet thứ 4 (Subnet ID) từ `0000` tăng dần lên:

* **LAN 1**: `2001:db8:1234:`**`0001`**`::/64` $\rightarrow$ Rút gọn: **`2001:db8:1234:1::/64`**

* **LAN 2**: `2001:db8:1234:`**`0002`**`::/64` $\rightarrow$ Rút gọn: **`2001:db8:1234:2::/64`**

* **Link WAN**: `2001:db8:1234:`**`0003`**`::/64` $\rightarrow$ Rút gọn: **`2001:db8:1234:3::/64`**


*(Cổng Router LAN 1 chỉ cần gán IP usable đầu tiên là `2001:db8:1234:1::1/64`)*