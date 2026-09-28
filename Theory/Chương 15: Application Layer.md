# Chương 15: Application Layer (Tầng Ứng dụng)

## 1. Mối quan hệ giữa Mô hình OSI và TCP/IP

Tầng Application trong mô hình TCP/IP gộp chức năng của **3 tầng trên cùng** trong mô hình OSI:

* **Application (Layer 7)**: Cung cấp giao diện giữa ứng dụng sử dụng để truyền thông và mạng bên dưới.


* **Presentation (Layer 6)**: Định dạng/trình diễn dữ liệu, nén dữ liệu và mã hóa/giải mã dữ liệu.


* **Session (Layer 5)**: Khởi tạo, duy trì và kết thúc các cuộc hội thoại (dialogs) giữa các ứng dụng.



---

## 2. Mô hình Client-Server và Peer-to-Peer (P2P)

* **Client-Server Model**: Thiết bị yêu cầu dữ liệu là **Client**, thiết bị phản hồi yêu cầu là **Server**.


* **Peer-to-Peer (P2P) Network**: Không cần Server cố định. Mọi thiết bị (Peer) vừa đóng vai trò Client vừa đóng vai trò Server tùy thuộc vào từng yêu cầu.


* *Các ứng dụng P2P phổ biến*: BitTorrent, Direct Connect, eDonkey, Freenet.





---

## 3. Các Giao thức Web và Email

### Web: HTTP & HTTPS

Làm việc theo cơ chế Request/Response thông qua URL.

* **HTTP (TCP 80 / 8080)**: Giao thức truyền tải dữ liệu web không mã hóa.


* **HTTPS (TCP 443)**: Phiên bản bảo mật có mã hóa của HTTP.


* **3 Phương thức HTTP cơ bản**:
* `GET`: Client yêu cầu lấy dữ liệu (trang HTML) từ Server.


* `POST`: Client tải dữ liệu lên Server (ví dụ: dữ liệu từ form biểu mẫu).


* `PUT`: Client tải tài nguyên/nội dung lên Server (ví dụ: tải ảnh).





### Email: SMTP, POP3, IMAP

Mô hình lưu trữ và chuyển tiếp (Store-and-forward):

* **SMTP (TCP 25)**: Dùng để **gửi mail** từ Client lên Server hoặc chuyển mail **giữa các Mail Server** với nhau.


* **POP3 (TCP 110)**: Dùng để **tải mail** về Client. Sau khi tải xong, mail thường bị **xóa khỏi Server**.


* **IMAP (TCP 143)**: Dùng để **truy xuất mail**. Bản gốc mail vẫn được **lưu trên Server** và đồng bộ hóa giữa nhiều thiết bị.



---

## 4. Các Dịch vụ Cấp phát IP & Ánh ánh Tên miền

### DNS (Domain Name System) – TCP/UDP Port 53

Chuyển đổi tên miền dễ nhớ (FQDN) thành địa chỉ IP.

* **Các bản ghi DNS (Resource Records) phổ biến**:
* `A`: Ánh xạ tên miền sang IPv4.


* `AAAA`: Ánh xạ tên miền sang IPv6 (Quad-A).


* `NS`: Chỉ định Name Server có thẩm quyền (Authoritative Name Server).


* `MX`: Chỉ định Mail Exchange Server.




* **Cấu trúc phân cấp DNS**: Root Level (`.`) $\rightarrow$ Top-Level Domain (`.com`, `.edu`, `.org`) $\rightarrow$ Second-Level Domain (`cisco.com`).


* **Lệnh kiểm tra**: `nslookup` (dùng để tra cứu DNS và khắc phục sự cố phân giải tên miền).



### DHCP (Dynamic Host Configuration Protocol)

Tự động cấp phát IP, Subnet Mask, Default Gateway và DNS cho thiết bị.

* **Cơ chế hoạt động DHCPv4 (Quy trình DORA)**:


1. **D**iscover: Client phát quảng bá (`DHCPDISCOVER`) tìm Server.


2. **O**ffer: Server phản hồi đề xuất cho thuê IP (`DHCPOFFER`).


3. **R**equest: Client gửi yêu cầu xác nhận thuê IP đã chọn (`DHCPREQUEST`).


4. **A**ck: Server gửi xác nhận hoàn tất cấp phép (`DHCPACK`).




* *Lưu ý DHCPv6*: Các thông điệp tương ứng của DHCPv6 là **SOLICIT**, **ADVERTISE**, **INFORMATION REQUEST**, và **REPLY**.



---

## 5. Dịch vụ Chia sẻ File (File Sharing Services)

* **FTP (File Transfer Protocol)**: Sử dụng **2 kết nối TCP** riêng biệt:


* **Port 21**: Kết nối điều khiển (Control Connection) – truyền lệnh và phản hồi.


* **Port 20**: Kết nối dữ liệu (Data Connection) – truyền tải file thực tế.




* **SMB (Server Message Block)**: Giao thức chia sẻ file/máy in trong mạng cục bộ (LAN). Khác với FTP, SMB tạo **kết nối lâu dài (long-term)** giúp Client truy cập tài nguyên trên Server như thư mục nội bộ.



---

## 📊 Bảng tổng hợp Port & Protocol Chương 15

| Protocol | Tên đầy đủ | Port | Transport Protocol |
| --- | --- | --- | --- |
| **DNS** | Domain Name System | **53**<br> | UDP / TCP|
| **DHCP (Client / Server)** | Dynamic Host Configuration Protocol | **68 / 67**<br> | UDP|
| **HTTP / HTTPS** | Hypertext Transfer Protocol (Secure) | **80 / 443**<br> | TCP|
| **SMTP** | Simple Mail Transfer Protocol | **25**<br> | TCP|
| **POP3** | Post Office Protocol v3 | **110**<br> | TCP|
| **IMAP** | Internet Message Access Protocol | **143** | TCP |
| **FTP (Control / Data)** | File Transfer Protocol | **21 / 20**<br> | TCP|
| **SMB** | Server Message Block | **445 / 139** | TCP |
