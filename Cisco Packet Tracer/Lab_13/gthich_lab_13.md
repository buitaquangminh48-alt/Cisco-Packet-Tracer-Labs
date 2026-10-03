# Đề bài lab 13

Lỗi này rất phổ biến khi vừa vào tiến trình OSPF mà chưa bật/gán IP cho bất kỳ cổng (Interface) nào!

Cisco IOS thông báo `OSPF process 1 cannot start. There must be at least one "up" IP interface` vì OSPF cần ít nhất một Interface đang ở trạng thái **UP** (có gán IP) để lấy địa chỉ IP đó làm **Router-ID** tự động.

Tuy nhiên, do bạn đã gõ thủ công lệnh `router-id 1.1.1.1` ngay sau đó, tiến trình OSPF đã nhận diện được Router-ID và chấp nhận các lệnh `network` bình thường.

Để xử lý triệt để thông báo này và kích hoạt OSPF hoạt động, bạn chỉ cần cấu hình IP cho cổng kết nối và mở cổng (`no shutdown`).

1. **Cấu hình IP cho cổng Gi0/0 (Hướng LAN):** R1.
Gán IP Gateway cho mạng LAN và mở cổng:

```text
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

```

*Cách kiểm tra:* Kiểm tra đèn trên dây cáp chuyển sang màu xanh lá.


2. **Cấu hình IP cho cổng Gi0/1 (Hướng WAN):** R1.
Gán IP nối sang Router R2 và mở cổng:

```text
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 10.0.0.1 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

```

*Cách kiểm tra:* Chạy lệnh `show ip interface brief` ở chế độ Privileged EXEC, đảm bảo các cổng `Gi0/0` và `Gi0/1` hiển thị `Status: up` và `Protocol: up`.


3. **Xác nhận trạng thái OSPF:** R1.
Chạy lệnh kiểm tra tiến trình OSPF:

```text
Router# show ip ospf

```

*Cách kiểm tra:* Kết quả hiển thị dòng `Routing Process "ospf 1" with ID 1.1.1.1` tức là OSPF đã khởi chạy thành công.


Sau khi hoàn tất trên R1, bạn tiếp tục gán IP và cấu hình OSPF tương tự trên R2 để 2 Router thiết lập láng giềng (Neighbor) với nhau!

---

# Lý thuyết

Đúng là "đặc sản" đào sâu bản chất thứ mà bạn luôn thực hiện rất chuẩn mực! Mọi cấu hình CLI tưởng chừng như là những dòng lệnh học thuộc lòng thực ra đều tuân theo các nguyên tắc toán học và thiết kế mạng rất chặt chẽ.

Dưới đây là lời giải chi tiết theo từng câu hỏi của bạn:

---

### 1. Tại sao phải thiết lập IP (hoặc Loopback) trước khi cấu hình OSPF?

**Bản chất:** OSPF là một giao thức định tuyến động chạy dựa trên định danh các **Tiến trình (Process)** và các **Cột mốc (Router ID - RID)**.

1. **OSPF cần một RID để "xác định danh tính":** RID có định dạng giống một địa chỉ IPv4 (`x.x.x.x`). Nếu bạn gõ lệnh `router ospf 1` khi **chưa có bất kỳ cổng mạng nào có IP** (hoặc đang ở trạng thái `UP`), tiến trình OSPF không thể tự bầu chọn hoặc gán được RID. Đó là lý do bạn nhận được cảnh báo:
> `OSPF process 1 cannot start. There must be at least one "up" IP interface`


2. **Quy tắc chọn Router ID tự động của Cisco:**
* **Ưu tiên 1:** Lấy địa chỉ IP của cổng Loopback có IP cao nhất.
* **Ưu tiên 2:** Lấy địa chỉ IP lớn nhất trong số các cổng vật lý đang ở trạng thái `UP`.
* **Ưu tiên 3:** Do người dùng gán thủ công bằng lệnh `router-id x.x.x.x`.



Vì vậy, đặt IP cho giao diện (Interface) trước giúp tiến trình OSPF khởi động bình thường và xác định ngay "thẻ căn cước" (RID) của Router trong mạng.

---

### 2. Bản chất các câu lệnh OSPF & Giải mã các dãy số "bí ẩn"

#### A. Bản chất lệnh `network <IP> <Wildcard_Mask> area <Area_ID>`

Lệnh `network` trong OSPF **không phải là lệnh quảng bá một dải mạng ra bên ngoài**.

> **Bản chất thực sự:** Lệnh này là một **bộ lọc (filter) để kích hoạt OSPF trên cổng vật lý**. Nó bảo Router: *"Hãy kiểm tra xem cổng nào trên Router có IP khớp với dải này. Nếu khớp, hãy BẬT OSPF trên cổng đó để gửi/nhận gói tin OSPF Hello và đem dải mạng của cổng đó đi quảng bá cho các Router khác."*

---

#### B. Các địa chỉ IP & Địa chỉ Mạng (Subnet / IP)

* **`1.1.1.1` & `2.2.2.2` (Router ID):** Là cái "Tên/Số định danh" của R1 và R2 trong bản đồ thuật toán OSPF (SPF Tree). Nó giúp các Router nhận biết ai là người đang gửi thông tin đường đi.
* **`10.0.0.1` & `10.0.0.2` (Interface IP):** Là địa chỉ IP cụ thể gán vào chân cổng WAN (GigabitEthernet 0/1) của R1 và R2 để hai Router nối dây trực tiếp và trò chuyện với nhau.
* **`192.168.10.0` hay `192.168.20.0` (Network Address - Địa chỉ Mạng):** Số `0` ở Octet cuối cùng đại diện cho **toàn bộ vùng mạng LAN** chứ không chỉ riêng một máy tính nào.
* `192.168.10.0/24` bao gồm tất cả IP từ `192.168.10.1` đến `192.168.10.254`.



---

#### C. Dãy số "bí ẩn" `0.0.0.255` và `0.0.0.3` (Wildcard Mask)

**Wildcard Mask** là hình ảnh đảo ngược (Invert) của Subnet Mask:


$$\text{Wildcard Mask} = 255.255.255.255 - \text{Subnet Mask}$$

* **Số `0` nghĩa là:** Bit này **bắt buộc phải khớp chính xác (Must Match)**.
* **Số `255` (hoặc `3`) nghĩa là:** Bit này **không quan tâm (Don't Care)**, giá trị nào cũng được.

**Ví dụ phân tích:**

1. **Mạng LAN B (`192.168.20.0 0.0.0.255`):**
* Subnet Mask gốc: `255.255.255.0`
* Wildcard Mask: `255.255.255.255 - 255.255.255.0 = 0.0.0.255`
* **Ý nghĩa:** Router kiểm tra 3 Octet đầu phải đúng chính xác là `192.168.20.x`, Octet cuối cùng là số mấy cũng khớp.


2. **Mạng WAN nối R1-R2 (`10.0.0.0 0.0.0.3`):**
* Subnet Mask dải `/30`: `255.255.255.252`
* Wildcard Mask: `255.255.255.255 - 255.255.255.252 = 0.0.0.3`
* **Ý nghĩa:** Router chỉ kiểm tra dải 4 IP thuộc `/30` (`10.0.0.0` đến `10.0.0.3`).



---

### 3. Bản chất thông tin hiện ra khi gõ các lệnh `show`

Các lệnh `show` thực chất là **truy vấn bộ nhớ RAM (Operational State)** của Router để xem dữ liệu mà các tiến trình hệ điều hành Cisco IOS đang lưu trữ:

1. **`show ip ospf neighbor` (Bản đồ láng giềng):**
* **Bản chất:** Đọc dữ liệu từ **Neighbor Table** trong RAM. Dữ liệu này được xây dựng từ việc trao đổi các gói tin **OSPF Hello** định kỳ qua cổng mạng. Nó cho biết ai đang nối trực tiếp với mình và trạng thái bắt tay đã đến đâu (`FULL`).


2. **`show ip route` (Bảng chuyển tiếp gói tin):**
* **Bản chất:** Đọc **Routing Table (FIB)**. Sau khi OSPF chạy thuật toán Dijkstra (Dijkstra's Shortest Path First) dựa trên cơ sở dữ liệu đường link (LSDB), nó chọn ra đường đi ngắn nhất/tốt nhất và nạp vào bảng này để Router biết: *"Muốn gửi gói tin tới IP X thì đẩy ra cổng Y"*.



---

### 4. Mục đích và Mục tiêu chung của Bài Lab 13 (OSPF Single-Area)

* **Mục tiêu kỹ thuật (Technical Goal):**
* Thay thế việc gõ thủ công từng đường đi (Static Route) bằng **Giao thức định tuyến động OSPF**.
* Giúp các Router **tự động chia sẻ và cập nhật sơ đồ mạng** với nhau. Nếu sau này bạn cắm thêm 10 mạng LAN mới vào R2, bạn chỉ cần báo cho R2, R1 sẽ tự động học được mà bạn không cần đụng tới R1.


* **Mục tiêu thực tế & Tư duy hệ thống (Real-world Purpose):**
* **Hiểu cơ chế Link-State:** Luyện tập cách OSPF duy trì mối quan hệ láng giềng (`Neighbor Adjacency`), giải quyết sự cố thực tế (đụng độ IP `DUPADDR`, gõ sai cú pháp, quên `no shutdown`).
* **Xây dựng Lab chuẩn mực:** Hoàn thiện mô hình mạng chạy OSPF Area 0 thông suốt từ PC0 sang PC1 làm tư liệu lưu trữ/documentation chất lượng trên GitHub.