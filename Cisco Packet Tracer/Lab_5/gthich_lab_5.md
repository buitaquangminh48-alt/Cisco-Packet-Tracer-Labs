====================================================Đề bài lab 5=============================================================

Chào buổi sáng! Sạc lại 100% năng lượng rồi thì chiến tiếp **Lab 5: Cấu hình DNS & Web Server** thôi! 🚀

Ở bài Lab 4, máy tính đã tự nhận IP tự động qua DHCP. Tuy nhiên, trong thực tế không ai gõ IP `192.168.1.100` để vào web cả, mà chúng ta gõ `google.com` hoặc `facebook.com`.

Hôm nay bạn sẽ dựng 2 Server riêng biệt:

1. **Web Server (HTTP):** Chứa trang web hiển thị.
2. **DNS Server:** Đóng vai trò như "danh bạ điện thoại", dịch tên miền (`quangminh.com`) thành địa chỉ IP.

---

### Sơ đồ mạng Lab 5

* **1 Router 2911**
* **1 Switch 2960**
* **1 PC** (Client)
* **2 Server** (đặt tên là `Web-Server` và `DNS-Server`)

---

### Các bước thực hiện

1. **Dựng sơ đồ & Cắm dây:** 1 Router, 1 Switch, 1 PC, 2 Server.
Nối PC, Web-Server, và DNS-Server vào Switch 2960 bằng cáp thẳng. Nối Switch vào cổng `g0/0/0` của Router.


2. **Cấu hình IP cố định (Static) cho 2 Server:** Server luôn dùng IP cố định.
Vào **Desktop** $\rightarrow$ **IP Configuration** của từng Server:

* **Web-Server:** IP `192.168.1.10`, Subnet Mask `255.255.255.0`, Gateway `192.168.1.1`
* **DNS-Server:** IP `192.168.1.20`, Subnet Mask `255.255.255.0`, Gateway `192.168.1.1`


3. **Tạo trang web trên Web-Server:** Dịch vụ HTTP.
* Mở `Web-Server` $\rightarrow$ chọn tab **Services** $\rightarrow$ chọn **HTTP**.
* Bật `HTTP` và `HTTPS` sang trạng thái **On**.
* Tìm file `index.html`, nhấn **Edit** và chỉnh sửa dòng chữ chào mừng (ví dụ: `<h1>Welcome to Quang Minh Web!</h1>`) rồi nhấn **Save**.


4. **Cấu hình danh bạ tên miền trên DNS-Server:** Dịch vụ DNS.
* Mở `DNS-Server` $\rightarrow$ chọn tab **Services** $\rightarrow$ chọn **DNS**.
* Bật dịch vụ DNS sang **On**.
* Tạo 1 bản ghi (Record):
* **Name:** `quangminh.com`
* **Type:** `A Record`
* **Address:** `192.168.1.10` (IP của Web-Server)


* Nhấn **Add**.


5. **Cấu hình Router cấp DHCP kèm DNS:** CLI trên Router.
Vào CLI của Router 2911 để cấp IP tự động cho PC, đồng thời chỉ định cho PC biết IP của DNS Server:

```text
enable
configure terminal
interface GigabitEthernet0/0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

ip dhcp excluded-address 192.168.1.1 192.168.1.20
ip dhcp pool MANG_LAN
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 192.168.1.20
exit

```


6. **Kiểm tra kết quả trên PC:** Test Web Browser.
* Mở **PC** $\rightarrow$ **IP Configuration** $\rightarrow$ Chọn lại **DHCP** để nhận IP mới (lúc này mục *DNS Server* phải tự động hiện `192.168.1.20`).
* Mở **Web Browser** trên PC $\rightarrow$ Gõ `quangminh.com` vào ô địa chỉ và bấm **Go**. Trang web chào mừng bạn vừa chỉnh sửa hiện lên là thành công!

===========================================================Lý thuyết===========================================================

Hỏi dồn 6 câu chất lượng thế này chứng tỏ bạn đào rất sâu vào kiến thức hệ thống! Giải đáp chi tiết từng câu cho bạn ngay đây:

**1. Bản chất của Server là gì?**

* **Bản chất:** Server (Máy máy chủ) thực chất cũng là một máy tính (có CPU, RAM, ổ cứng, HĐH). Điểm khác biệt duy nhất: Máy tính thường (Client) dùng để **yêu cầu/tiêu thụ** dữ liệu, còn Server dùng để **cung cấp/phục vụ (Serve)** tài nguyên, dịch vụ cho các máy khác 24/7.
* Trong Packet Tracer, biểu tượng Server là một máy tính được cài sẵn các phần mềm chạy dịch vụ mạng (Web, DNS, DHCP, FTP...).

---

**2. Bản chất của HTTP / HTTPS và vì sao phải bật ON?**

* **Vì sao phải ON?** Muốn một máy tính chạy được dịch vụ gì thì phần mềm dịch vụ đó phải được khởi chạy. Chọn **ON** tương tự như việc bạn mở phần mềm Web Server (như Nginx hay Apache ngoài đời) lên thì nó mới bắt đầu lắng nghe yêu cầu từ client.
* **HTTP (HyperText Transfer Protocol):** Giao thức truyền tải văn bản siêu liên kết (HTML, hình ảnh, văn bản) giữa Web Server và Trình duyệt. Gửi dữ liệu dạng **dạng thô (Plaintext)**, không bảo mật.
* **HTTPS (HTTP Secure):** Chính là HTTP nhưng dữ liệu truyền đi được **mã hóa** (qua SSL/TLS). Người khác chặn giữa đường cũng không đọc được thông tin.

---

**3. Bản chất của Web-Server vs DNS-Server khác nhau thế nào?**

* **Web-Server (Chứa nội dung):** Nơi lưu trữ thực sự file giao diện (`index.html`, hình ảnh, video). Giống như **cửa hàng** bán hàng.
* **DNS-Server (Chỉ đường):** Không chứa giao diện web. Nó chỉ chứa bảng danh bạ để **dịch Tên miền thành địa chỉ IP**. Giống như **tổng đài 1080** chỉ đường cho bạn đến đúng địa chỉ cửa hàng.

---

**4. Dịch vụ DNS ON & Các loại Record (A, AAAA, CNAME...) là gì?**
Bật **ON** là để DNS Server mở "cuốn sổ danh bạ" ra làm việc. Các loại Record trong sổ danh bạ gồm:

* **A Record (Address):** Ánh xạ Tên miền $\rightarrow$ Địa chỉ **IPv4** (VD: `quangminh.com` $\rightarrow$ `192.168.1.10`). Đây là loại phổ biến nhất.
* **AAAA Record:** Ánh xạ Tên miền $\rightarrow$ Địa chỉ **IPv6** (dành cho chuẩn IP thế hệ mới).
* **CNAME (Canonical Name):** Tạo **tên bí danh (Alias)** trỏ về tên chính. (VD: gõ `[www.quangminh.com](https://www.quangminh.com)` thì CNAME trỏ về `quangminh.com`).
* **NS Record (Name Server):** Khai báo máy chủ DNS nào đang quản lý tên miền này.
* **SOA Record (Start of Authority):** Chứa thông tin quản trị cao nhất của dải tên miền (phiên bản cập nhật, thời gian hết hạn...).

---

**5. Bản chất lệnh `dns-server 192.168.1.20` trong DHCP là gì?**

* Khi PC xin IP từ DHCP Server (Router), Router không chỉ cho PC mỗi cái IP và Default Gateway.
* Lệnh này có nghĩa là Router dặn thêm PC: *"Sau này muốn tìm trang web nào thì hãy gửi câu hỏi đến địa chỉ `192.168.1.20` (DNS Server) để hỏi IP nhé!"*. Nếu thiếu lệnh này, PC sẽ không biết gửi câu hỏi dịch tên miền đi đâu.

---

**6. Vì sao Server trong bài chỉ có FastEthernet0 và nối vào Switch?**

* **Vì sao là FastEthernet0?** Thiết bị Server mặc định trong Packet Tracer được Cisco giả lập sẵn 1 card mạng `FastEthernet0` ($100\text{ Mbps}$). Bạn hoàn toàn có thể tắt Server đi và gắn thêm card `GigabitEthernet` vào nếu muốn.
* **Vì sao nối vào Switch mà không nối vào PC/Router?**
* Nối vào **PC**: Chỉ duy nhất PC đó dùng được Server, các máy khác bó tay.
* Nối vào **Router**: Router chỉ có ít cổng (thường 2-3 cổng) và nhiệm vụ chính là định tuyến giữa các mạng, không đủ cổng để cắm trực tiếp nhiều Server.
* Nối vào **Switch**: Switch có 24-48 cổng, đóng vai trò "ổ cắm trung tâm" của mạng LAN. Cắm Server vào Switch giúp **tất cả các máy tính trong mạng LAN** đều truy cập vào Server được cùng lúc.




Cho mình hỏi thêm là mã hóa HTTPS trong thực tế hoạt động thế nào vậy?






Mã hóa **HTTPS** trong thực tế hoạt động dựa trên sự kết hợp giữa **Mã hóa bất đối xứng (Asymmetric Encryption)** để khởi tạo bắt tay và **Mã hóa đối xứng (Symmetric Encryption)** để truyền dữ liệu thực tế. Quá trình này được gọi là **TLS/SSL Handshake** (Bắt tay TLS).

Hãy tưởng tượng bạn (Client) muốn gửi thư bảo mật cho Web Server qua 4 bước cơ bản:

---

### Quá trình TLS Handshake trong thực tế

* **1. Client Hello (Xin chào):** Trình duyệt gửi cho Web Server danh sách các thuật toán mã hóa mà nó hỗ trợ, kèm theo một chuỗi số ngẫu nhiên ($Client\_Random$).
* **2. Server Hello & Chứng chỉ số (Certificate):**
* Server chọn thuật toán mã hóa và gửi lại một chuỗi số ngẫu nhiên khác ($Server\_Random$).
* Server gửi kèm **Chứng chỉ số SSL/TLS (Digital Certificate)** chứa **Public Key** (Khóa công khai) của Server. Chứng chỉ này được cấp bởi tổ chức uy tín (CA - Certificate Authority).


* **3. Tạo Khóa Phiên (Session Key):**
* Trình duyệt kiểm tra xem Chứng chỉ số của Server có hợp lệ (xịn) không.
* Trình duyệt tạo ra một chìa khóa bí mật tạm thời (gọi là $Pre\_Master\_Secret$), sau đó dùng **Public Key** của Server để khóa nó lại và gửi sang Server.
* Server dùng **Private Key** (Khóa bảo mật riêng chỉ Server có) để giải mã và lấy ra $Pre\_Master\_Secret$.
* Cả Client và Server lúc này cùng dùng thuật toán toán học để kết hợp 3 yếu tố: $Client\_Random$ + $Server\_Random$ + $Pre\_Master\_Secret$ để tự tạo ra chung một **Session Key** (Khóa đối xứng dùng riêng cho phiên làm việc đó).


* **4. Truyền dữ liệu an toàn:** Từ giây phút này, toàn bộ dữ liệu trang web (tài khoản, mật khẩu, thông tin thẻ...) gửi qua lại đều được mã hóa và giải mã cực nhanh bằng chính **Session Key** đó.

---

### Tại sao lại kết hợp 2 loại mã hóa?

* **Mã hóa bất đối xứng (Public Key / Private Key):** Cực kỳ an toàn để trao đổi chìa khóa bí mật ban đầu mà không sợ kẻ ngồi giữa nghe lén, nhưng tốc độ tính toán **rất chậm**.
* **Mã hóa đối xứng (Session Key):** Tốc độ tính toán **siêu nhanh**, tiết kiệm CPU cho cả máy bạn và Server, nhưng phải giữ bí mật chìa khóa.

$\rightarrow$ **HTTPS kết hợp cả hai:** Dùng mã hóa bất đối xứng ở bước đầu để "trao chìa khóa an toàn", sau đó chuyển sang mã hóa đối xứng để "truyền tải dữ liệu siêu tốc".