# Chương 14: Transport Layer (Tầng Giao vận)

## 1. Vai trò của Tầng Transport

Tầng Transport là cầu nối giữa Tầng Application và các tầng bên dưới chịu trách nhiệm truyền dữ liệu qua mạng.

* **Theo dõi cuộc hội thoại (Tracking conversations)**: Quản lý từng luồng dữ liệu riêng biệt giữa ứng dụng nguồn và đích.


* **Phân đoạn & Ghép luồng (Segmentation & Multiplexing)**: Chia nhỏ dữ liệu thành các đoạn (Segment/Datagram) và trộn chung trên cùng đường truyền mạng để nhiều ứng dụng cùng hoạt động đồng thời.



---

## 2. So sánh TCP và UDP

| Tiêu chí | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
| --- | --- | --- |
| **Đặc điểm** | Connection-oriented (Có kết nối), Stateful| Connectionless (Không kết nối), Stateless|
| **Độ tin cậy** | Đảm bảo truyền dữ liệu, gửi lại gói bị mất | Best-effort, không gửi lại gói bị mất|
| **Thứ tự gói** | Đảm bảo sắp xếp đúng thứ tự (Same-order delivery)| Nhận sao xử lý vậy, không sắp xếp lại|
| **Kiểm soát luồng** | Có (Flow Control & Congestion Avoidance)| Không có|
| **Kích thước Header** | **20 Bytes**<br> | **8 Bytes** (Low Overhead)|
| **Ứng dụng tiêu biểu** | HTTP/HTTPS (80/443), FTP (20/21), SSH (22), SMTP (25)| VoIP, Video Streaming, DNS (53), DHCP (67/68), TFTP (69)|

---

## 3. Cấu trúc Header & Số Cổng (Port Numbers)

### Cấu trúc Header

* **TCP Header (20 Bytes)**: Chứa Source/Dest Port, Sequence Number, Acknowledgment Number, Header Length, Reserved, Control Bits (Flags), Window size, Checksum, Urgent.


* **UDP Header (8 Bytes)**: Rất đơn giản, chỉ gồm 4 trường: Source Port, Destination Port, Length, Checksum.



### Các nhóm Port Number (0 - 65535)

1. **Well-Known Ports (0 – 1,023)**: Dành riêng cho các dịch vụ phổ biến (HTTP, FTP, SSH, SMTP...).


2. **Registered Ports (1,024 – 49,151)**: Cấp phát bởi IANA cho ứng dụng cụ thể (VD: RADIUS port 1812).


3. **Private/Dynamic/Ephemeral Ports (49,152 – 65,535)**: Cấp phát động bởi OS client khi khởi tạo kết nối.



> **Khái niệm Socket**: Là sự kết hợp giữa **IP Nguồn + Port Nguồn** hoặc **IP Đích + Port Đích** (Ví dụ: `192.168.1.5:1099`), giúp nhận diện chính xác từng tiến trình ứng dụng.
> 
> 

---

## 4. Cơ chế hoạt động của TCP

### Bắt tay 3 bước (TCP Three-Way Handshake)

Dùng để thiết lập kết nối trước khi truyền dữ liệu:

1. **Step 1 (SYN)**: Client gửi gói `SYN` để yêu cầu khởi tạo kết nối (kèm SEQ số khởi tạo).


2. **Step 2 (SYN-ACK)**: Server phản hồi `SYN, ACK` báo đã nhận và xin thiết lập chiều ngược lại.


3. **Step 3 (ACK)**: Client gửi `ACK` để xác nhận hoàn tất thiết lập kết nối.



### Kết thúc phiên kết nối (Session Termination)

Dùng quy trình **4 bước** sử dụng cờ **FIN** và **ACK** để đóng kết nối từ 2 chiều:

* Client gửi **FIN** $\rightarrow$ Server gửi **ACK**.


* Server gửi **FIN** $\rightarrow$ Client gửi **ACK**.



### Các cờ điều khiển TCP (Control Bits / Flags - 6 bits)

* **SYN**: Đồng bộ hóa Sequence Number.


* **ACK**: Xác nhận đã nhận dữ liệu.


* **FIN**: Kết thúc truyền dữ liệu/phiên kết nối.


* **RST**: Reset lại kết nối khi gặp lỗi/timeout.


* **PSH**: Đẩy dữ liệu lên ứng dụng ngay.


* **URG**: Dữ liệu khẩn cấp.



---

## 5. Độ tin cậy & Kiểm soát luồng trong TCP (Reliability & Flow Control)

* **Sequence & Acknowledgment Numbers**: Giúp reorder (sắp xếp đúng thứ tự các segment bị xáo trộn) và phát hiện gói bị mất.


* **Retransmission (Gửi lại dữ liệu)**: Nếu không nhận được ACK trong khoảng thời gian quy định, TCP sẽ gửi lại dữ liệu.


* **SACK (Selective Acknowledgment)**: Tính năng mở rộng cho phép bên nhận báo chính xác các segment bị thiếu để bên gửi chỉ cần gửi lại đúng segment đó thay vì gửi lại toàn bộ.


* **Window Size & Sliding Window**:
* **Window Size**: Số byte mà thiết bị nhận có thể xử lý tại một thời điểm trước khi cần gửi ACK.


* **MSS (Maximum Segment Size)**: Kích thước dữ liệu tối đa của 1 segment TCP (Thường là **1460 Bytes** đối với IPv4 vì MTU Ethernet = 1500 Bytes - 20 Bytes IP Header - 20 Bytes TCP Header).




* **Congestion Avoidance**: Khi xảy ra tắc nghẽn mạng (mất gói), TCP sẽ tự động giảm số lượng byte gửi đi trước khi nhận ACK tiếp theo.



---

## 📝 Lệnh quan trọng

* **`netstat`**: Lệnh kiểm tra các kết nối TCP đang hoạt động trên máy host (hiển thị Protocol, Local Address, Foreign Address, State).