# Chương 11: Định địa chỉ IPv4 (IPv4 Addressing)

## 1. Cấu trúc địa chỉ IPv4 (IPv4 Address Structure)

* **Thành phần**: Địa chỉ IPv4 gồm 32 bit, chia làm 2 phần: phần Mạng (**Network portion**) và phần Host (**Host portion**).


* **Subnet Mask**: So sánh bit-với-bit từ trái sang phải với địa chỉ IPv4 thông qua phép toán logic **AND** để xác định phần Mạng và phần Host.


* **Prefix Length (Độ dài tiền tố)**: Viết tắt của Subnet Mask theo dạng Slash Notation (ví dụ: `/24` tương đương `255.255.255.0`).


* **Các loại địa chỉ trong một mạng**:


* **Network Address**: Các bit phần Host đều bằng `0`.


* **First Usable Address**: Địa chỉ Host đầu tiên (Network Address + 1).


* **Last Usable Address**: Địa chỉ Host cuối cùng (Broadcast Address - 1).


* **Broadcast Address**: Các bit phần Host đều bằng `1`.





---

## 2. Các phương thức truyền tin (Unicast, Broadcast, Multicast)

* **Unicast**: Gửi gói tin từ một thiết bị đến đúng một địa chỉ đích xác định (1-1).


* **Broadcast**: Gửi gói tin đến tất cả các thiết bị trong cùng một broadcast domain (1-ALL). Địa chỉ broadcast chung là `255.255.255.255`.


* **Multicast**: Gửi gói tin đến một nhóm thiết bị đã chọn (1-MANY), sử dụng dải địa chỉ multicast `224.0.0.0` - `239.255.255.255`.



---

## 3. Phân loại địa chỉ IPv4 (Types of IPv4 Addresses)

* **Public IP**: Được định tuyến toàn cầu trên Internet, do IANA/RIRs quản lý.


* **Private IP (RFC 1918)**: Dùng nội bộ, không thể định tuyến trên Internet:


* `10.0.0.0/8` (`10.0.0.0` - `10.255.255.255`)


* `172.16.0.0/12` (`172.16.0.0` - `172.31.255.255`)


* `192.168.0.0/16` (`192.168.0.0` - `192.168.255.255`)




* **Network Address Translation (NAT)**: Chuyển đổi IP Private thành IP Public để truy cập Internet.


* **Địa chỉ đặc biệt**:
* **Loopback (`127.0.0.1`)**: Kiểm tra hoạt động của TCP/IP trên chính thiết bị.


* **Link-Local / APIFA (`169.254.0.0/16`)**: Thiết bị Windows tự gán khi không nhận được IP từ DHCP server.




* **Classful Addressing (Truyền thống)**: Chia lớp Class A, B, C, D, E (đã được thay thế bằng Classless Addressing).



---

## 4. Phân đoạn mạng (Network Segmentation & Subnetting)

* **Vấn đề**: Broadcast domain quá lớn làm nghẽn mạng và giảm hiệu năng.


* **Giải pháp**: **Subnetting** (chia mạng con) để giảm kích thước miền quảng bá, tăng tính bảo mật và cải thiện hiệu năng.


* Router là thiết bị chặn truyền tin Broadcast giữa các subnet.


* Phân chia mạng con theo **Vị trí (Location)**, **Phòng ban/Chức năng (Group/Function)** hoặc **Loại thiết bị (Device Type)**.



---

## 5. Chia mạng con và kỹ thuật VLSM

* **Chia Subnet truyền thống**: Mượn các bit của phần Host để làm bit Network. Công thức tính:


* Số subnet tạo ra = $2^n$ ($n$ là số bit mượn).


* Số IP gán được cho host/subnet = $2^h - 2$ ($h$ là số bit host còn lại).




* **VLSM (Variable Length Subnet Mask)**:
* Giúp chia nhỏ các subnet thành các subnet có kích thước khác nhau tùy theo nhu cầu.


* Tránh lãng phí địa chỉ IP (đặc biệt là cho các kết nối Point-to-Point giữa các Router chỉ cần 2 IP, dùng mask `/30`).


* **Quy tắc chia VLSM**: Luôn ưu tiên đáp ứng cho subnet đòi hỏi số lượng host lớn nhất trước, sau đó tiếp tục chia cho các subnet nhỏ hơn.





---

## 6. Quy hoạch và gán địa chỉ IP (Device Address Assignment)

Một kế hoạch gán IP chuẩn chỉnh cần phân loại như sau:

* **End user clients**: Dùng **DHCP** động.


* **Servers & Peripherals**: Đặt **Static IP** (IP tĩnh).


* **Public Servers**: Dùng IP Public hoặc NAT.


* **Intermediary Devices (Switch, Router)**: Đặt Static IP cho quản lý/bảo mật.


* **Gateway**: Địa chỉ của giao diện Router kết nối trực tiếp với mạng LAN.

---

# Bài tập

Dưới đây là **1 Bài tập Tổng hợp VIP** chuẩn đề thi CCNA, ôm trọn gói cả 3 dạng từ đổi nhị phân, phân tích IP đến chia VLSM cho cả sơ đồ mạng.

---

### 📝 ĐỀ BÀI TỔNG HỢP

Cho dải IP ban đầu: **`192.168.10.0/24`**

Hệ thống mạng công ty bạn gồm 4 khu vực cần chia IP:

* **Khu vực A (Phòng Dev)**: Cần **60 hosts**
* **Khu vực B (Phòng Sale)**: Cần **28 hosts**
* **Khu vực C (Phòng HR)**: Cần **12 hosts**
* **Khu vực D (Link WAN)**: Kết nối Point-to-Point giữa 2 Router (cần **2 hosts**)

---

### 🚀 LỜI GIẢI CHI TIẾT TỪNG DẠNG

#### DẠNG 1: Đổi nhị phân & Chuyển đổi Prefix / Subnet Mask (Cho IP ban đầu)

Trước khi chia, cùng đổi nhanh địa chỉ ban đầu **`192.168.10.0/24`**:

* **Prefix `/24**`: Nghĩa là 24 bit $1$ ở phần Network và 8 bit $0$ ở phần Host.
* Nhị phân Mask: `11111111.11111111.11111111.00000000`
* Dạng Thập phân (Subnet Mask): **`255.255.255.0`**


* **Địa chỉ IP nhị phân**: `11000000.10101000.00001010.00000000`

---

#### DẠNG 2 & 3: Chia VLSM & Xác định thông số chi tiết cho từng Subnet

> **Quy tắc vàng của VLSM**: Luôn sắp xếp thứ tự ưu tiên từ **lớn nhất đến nhỏ nhất** theo số lượng Host: **A (60) $\rightarrow$ B (28) $\rightarrow$ C (12) $\rightarrow$ D (2)**.

---

##### 1. Chia cho Khu vực A (Phòng Dev - 60 hosts)

* **Tính số bit Host ($h$)**: Tìm $h$ sao cho $2^h - 2 \ge 60 \implies h = 6$ (vì $2^6 - 2 = 62 \ge 60$).
* **Prefix mới**: $32 - 6 = \mathbf{/26}$ (Mượn 2 bit: $24 + 2 = 26$).
* **Subnet Mask**: `/26` tương đương **`255.255.255.192`**.
* **Bước nhảy (Block Size)**: $2^6 = 64$.
* **Thông số Subnet A**:
* **Network Address**: `192.168.10.0/26`
* **First Usable IP**: `192.168.10.1`
* **Last Usable IP**: `192.168.10.62`
* **Broadcast Address**: `192.168.10.63`



---

##### 2. Chia cho Khu vực B (Phòng Sale - 28 hosts)

* IP tiếp theo bắt đầu từ: **`192.168.10.64`**.
* **Tính số bit Host ($h$)**: Tìm $h$ sao cho $2^h - 2 \ge 28 \implies h = 5$ (vì $2^5 - 2 = 30 \ge 28$).
* **Prefix mới**: $32 - 5 = \mathbf{/27}$ (Subnet Mask: **`255.255.255.224`**).
* **Bước nhảy**: $2^5 = 32$.
* **Thông số Subnet B**:
* **Network Address**: `192.168.10.64/27`
* **First Usable IP**: `192.168.10.65`
* **Last Usable IP**: `192.168.10.94`
* **Broadcast Address**: `192.168.10.95`



---

##### 3. Chia cho Khu vực C (Phòng HR - 12 hosts)

* IP tiếp theo bắt đầu từ: **`192.168.10.96`**.
* **Tính số bit Host ($h$)**: Tìm $h$ sao cho $2^h - 2 \ge 12 \implies h = 4$ (vì $2^4 - 2 = 14 \ge 12$).
* **Prefix mới**: $32 - 4 = \mathbf{/28}$ (Subnet Mask: **`255.255.255.240`**).
* **Bước nhảy**: $2^4 = 16$.
* **Thông số Subnet C**:
* **Network Address**: `192.168.10.96/28`
* **First Usable IP**: `192.168.10.97`
* **Last Usable IP**: `192.168.10.110`
* **Broadcast Address**: `192.168.10.111`



---

##### 4. Chia cho Khu vực D (Link WAN Router - 2 hosts)

* IP tiếp theo bắt đầu từ: **`192.168.10.112`**.
* **Tính số bit Host ($h$)**: Cần 2 host $\implies h = 2$ (vì $2^2 - 2 = 2$).
* **Prefix mới**: $32 - 2 = \mathbf{/30}$ (Subnet Mask: **`255.255.255.252`**).
* **Bước nhảy**: $2^2 = 4$.
* **Thông số Subnet D**:
* **Network Address**: `192.168.10.112/30`
* **First Usable IP**: `192.168.10.113` (Gán cho Cổng Router 1)
* **Last Usable IP**: `192.168.10.114` (Gán cho Cổng Router 2)
* **Broadcast Address**: `192.168.10.115`



---

### 📊 BẢNG TỔNG HỢP KẾT QUẢ QUY HOẠCH IP

| Subnet | Tên khu vực | Số Host cần | Prefix / Mask | Network IP | IP Usable Đầu | IP Usable Cuối | Broadcast IP |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Subnet A** | Phòng Dev | 60 | `/26` (`...192`) | `192.168.10.0` | `192.168.10.1` | `192.168.10.62` | `192.168.10.63` |
| **Subnet B** | Phòng Sale | 28 | `/27` (`...224`) | `192.168.10.64` | `192.168.10.65` | `192.168.10.94` | `192.168.10.95` |
| **Subnet C** | Phòng HR | 12 | `/28` (`...240`) | `192.168.10.96` | `192.168.10.97` | `192.168.10.110` | `192.168.10.111` |
| **Subnet D** | Link WAN | 2 | `/30` (`...252`) | `192.168.10.112` | `192.168.10.113` | `192.168.10.114` | `192.168.10.115` |