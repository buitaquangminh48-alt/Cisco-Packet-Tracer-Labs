# **Chương 3: Protocols and Models (Giao thức và Mô hình)**

---

## **1. Quy tắc giao tiếp (The Rules)**

Để các thiết bị trao đổi dữ liệu thành công, chúng phải đồng ý về các quy tắc giao tiếp (Giao thức - Protocols).

* **3 Yếu tố cơ bản của truyền thông:** Nguồn (Source/Sender), Đích (Destination/Receiver), và Kênh truyền (Channel/Medium).


* **Các yêu cầu cốt lõi của giao thức mạng:**
* **Mã hóa tin nhắn (Message Encoding):** Chuyển đổi thông tin sang dạng tín hiệu thích hợp để truyền qua phương tiện và giải mã ở điểm đích.


* **Định dạng & Đóng gói (Formatting & Encapsulation):** Đặt dữ liệu vào khung chứa các thông tin địa chỉ đúng chuẩn.


* **Kích thước tin nhắn (Message Size):** Chia nhỏ dữ liệu lớn thành từng phần phù hợp với đường truyền.


* **Thời gian tin nhắn (Message Timing):** Bao gồm Kiểm soát lưu lượng (*Flow Control*), Thời gian chờ phản hồi (*Response Timeout*), và Phương thức truy cập (*Access Method* - giải quyết xung đột/collision).


* **Tùy chọn gửi tin (Message Delivery Options):**
* *Unicast:* 1 gửi 1.


* *Multicast:* 1 gửi nhiều (nhóm được chọn).


* *Broadcast:* 1 gửi tất cả (chỉ áp dụng cho IPv4).







---

## **2. Giao thức & Bộ giao thức (Protocols & Protocol Suites)**

* **Phân loại giao thức mạng:**
* *Network Communications:* Cho phép truyền thông giữa các thiết bị.


* *Network Security:* Bảo mật, xác thực, mã hóa dữ liệu.


* *Routing:* Giúp router trao đổi thông tin và tìm đường đi tối ưu.


* *Service Discovery:* Tự động phát hiện thiết bị hoặc dịch vụ.




* **Các chức năng cốt lõi của giao thức:** Địa chỉ hóa (*Addressing*), Độ tin cậy (*Reliability*), Kiểm soát lưu lượng (*Flow Control*), Đánh số thứ tự (*Sequencing*), Phát hiện lỗi (*Error Detection*), và Giao diện ứng dụng (*Application Interface*).


* **Bộ giao thức TCP/IP (TCP/IP Protocol Suite):**
* Là bộ giao thức chuẩn mở (*Open Standard*) được sử dụng rộng rãi nhất trên Internet.


* Bao gồm các giao thức tiêu biểu theo từng tầng:
* **Application:** HTTP, DNS, DHCP, FTP, SMTP.


* **Transport:** TCP (hướng kết nối, tin cậy), UDP (không kết nối).


* **Internet:** IPv4, IPv6, ICMP, OSPF, BGP.


* **Network Access:** Ethernet, WLAN (Wi-Fi), ARP.







---

## **3. Tổ chức tiêu chuẩn (Standards Organizations)**

Các tổ chức phi lợi nhuận, trung lập giúp đảm bảo tính tương thích và phát triển các chuẩn mở (*Open Standards*):

* **Tổ chức quản lý Internet:** ISOC (Internet Society), IAB, IETF (phát triển chuẩn TCP/IP), IRTF, ICANN (quản lý IP và tên miền), IANA (phân bổ IP cho ICANN).


* **Tổ chức chuẩn Điện & Viễn thông:**
* **IEEE:** Chuẩn về mạng LAN/WLAN (ví dụ: Ethernet 802.3, Wi-Fi 802.11).


* **EIA / TIA:** Chuẩn cáp truyền dẫn, đầu nối, tủ rack, thiết bị VoIP.


* **ITU-T:** Chuẩn nén video, IPTV, nén băng thông rộng (DSL).





---

## **4. Các Mô hình Tham chiếu (Reference Models)**

Giúp chia nhỏ chức năng mạng để dễ thiết kế, giảng dạy và khắc phục sự cố.

| Tầng | Mô hình OSI (7 tầng)

 | Mô hình TCP/IP (4 tầng)

 | Chức năng chính

 |
| --- | --- | --- | --- |
| **7 / 6 / 5** | Application, Presentation, Session | **Application** | Giao diện người dùng, mã hóa dữ liệu, quản lý phiên.

 |
| **4** | Transport | **Transport** | Phân đoạn dữ liệu, truyền thông cậy/không cậy giữa các tiến trình.

 |
| **3** | Network | **Internet** | Xác định đường đi tối ưu và định tuyến gói tin toàn mạng.

 |
| **2 / 1** | Data Link, Physical | **Network Access** | Điều khiển phần cứng, định dạng khung (Frame) và truyền tín hiệu vật lý.

 |

---

## **5. Đóng gói Dữ liệu (Data Encapsulation)**

* **Phân đoạn & Đa truy nhập (Segmentation & Multiplexing):** Chia nhỏ dữ liệu giúp tăng tốc độ truyền và tăng hiệu suất (chỉ cần gửi lại đoạn bị lỗi thay vì toàn bộ dữ liệu).


* **Đơn vị dữ liệu giao thức (PDU - Protocol Data Unit):** Khi dữ liệu đi từ trên xuống dưới qua các tầng, thông tin quản lý (Header) sẽ được thêm vào:


1. **Application:** Data (Dữ liệu gốc)


2. **Transport:** Segment (TCP) / Datagram (UDP)


3. **Internet:** Packet (Gói tin IP)


4. **Network Access (Data Link):** Frame (Khung dữ liệu)


5. **Physical:** Bits (Chuỗi Bit 0/1)




* **Giải đóng gói (De-encapsulation):** Quá trình ngược lại ở thiết bị nhận, tháo bỏ Header từ tầng dưới lên tầng trên.



---

## **6. Truy cập Dữ liệu (Data Access)**

Địa chỉ hóa được xử lý ở cả Tầng 2 (Data Link) và Tầng 3 (Network):

* **Địa chỉ Tầng 3 (IP Address):**
* Là địa chỉ logic toàn cầu (*Global Address*), gồm **Source IP** (Nguồn) và **Destination IP** (Đích).


* **Không thay đổi** suốt hành trình từ nguồn đến đích cuối cùng.


* Gồm 2 phần: **Network portion** (Phần mạng) và **Host portion** (Phần thiết bị).




* **Địa chỉ Tầng 2 (MAC Address):**
* Là địa chỉ vật lý cục bộ (*Local Address*), nhúng vào card mạng (NIC).


* **Thay đổi theo từng chặng (Hop-to-Hop)** trên đường đi.




* **Quy tắc gửi dữ liệu:**
* **Trong cùng mạng LAN:** Destination MAC là MAC trực tiếp của thiết bị đích.


* **Khác mạng LAN (Remote Network):**
* Packet L3 chứa IP của thiết bị đích xa.


* Frame L2 chứa **Destination MAC của Default Gateway (Router)** để thoát ra khỏi mạng cục bộ.
