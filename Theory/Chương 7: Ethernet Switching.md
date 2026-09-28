# Chương 7: Chuyển Mạch Ethernet (Ethernet Switching)

## 7.1 Khung Dữ Liệu Ethernet (Ethernet Frames)

* **Phạm vi hoạt động:** Ethernet hoạt động ở cả **Tầng Liên kết Dữ liệu (Data Link Layer)** và **Tầng Vật lý (Physical Layer)**, được định nghĩa theo chuẩn IEEE 802.2 và 802.3.


* **Phân tầng Data Link trong Ethernet:**
* **Phân tầng LLC (IEEE 802.2):** Xác định giao thức Tầng Mạng (Lớp 3) được sử dụng trong frame.


* **Phân tầng MAC (IEEE 802.3):** Đóng gói dữ liệu, định địa chỉ Lớp 2 và điều khiển truy cập phương tiện truyền dẫn.




* **Kích thước Ethernet Frame:**
* Kích thước tiêu chuẩn từ **64 bytes** đến **1518 bytes** (không tính trường Preamble).


* **Runt Frame / Collision Fragment:** Frame nhỏ hơn 64 bytes (thường do xung đột tín hiệu), thiết bị nhận sẽ tự động loại bỏ.


* **Jumbo / Baby Giant Frame:** Frame lớn hơn 1500 bytes dữ liệu.




* Cấu trúc các trường trong Ethernet Frame:



| Trường (Field) | Kích thước | Chức năng |
| --- | --- | --- |
| **Preamble & SFD** | 8 bytes | Đồng bộ hóa tín hiệu giữa thiết bị gửi và nhận.|
| **Destination MAC Address** | 6 bytes | Địa chỉ MAC của thiết bị nhận (đích).|
| **Source MAC Address** | 6 bytes | Địa chỉ MAC của thiết bị gửi (nguồn).|
| **Type / Length** | 2 bytes | Nhận diện giao thức Lớp 3 (ví dụ: IPv4 hoặc IPv6).|
| **Data (Payload)** | 45–1500 bytes | Tải trọng dữ liệu (chứa gói tin Lớp 3).|
| **FCS (Frame Check Sequence)** | 4 bytes | Kiểm tra lỗi truyền dẫn bằng thuật toán CRC.|

---

## 7.2 Địa Chỉ MAC Ethernet (Ethernet MAC Address)

* **Định dạng:** Đội dài **48 bits** (6 bytes), biểu diễn bằng **12 chữ số Thập lục phân (Hexadecimal)**.


* **Cấu trúc địa chỉ MAC:**
* **OUI (Organizationally Unique Identifier):** 24 bits (3 bytes) đầu tiên, do tổ chức IEEE cấp cho nhà sản xuất để định danh thương hiệu.


* **Vendor Assigned:** 24 bits (3 bytes) sau, do nhà sản xuất tự gán duy nhất cho từng thiết bị/NIC.




* **Các loại địa chỉ MAC:**
1. **Unicast MAC:** Địa chỉ duy nhất gửi từ 1 thiết bị đến 1 thiết bị khác. Địa chỉ MAC nguồn luôn luôn là Unicast.


2. **Broadcast MAC:** Gửi tới tất cả thiết bị trong cùng mạng LAN. Địa chỉ MAC đích là `FF-FF-FF-FF-FF-FF`.


3. **Multicast MAC:** Gửi tới một nhóm thiết bị trong mạng. Địa chỉ MAC đích bắt đầu bằng `01-00-5E` (với IPv4) hoặc `33-33` (với IPv6).





---

## 7.3 Bảng Địa Chỉ MAC (The MAC Address Table / CAM Table)

* **Cơ chế hoạt động của Switch Lớp 2:** Chuyển tiếp dữ liệu dựa **hoàn toàn vào địa chỉ MAC Lớp 2** (không quan tâm đến địa chỉ IP Lớp 3).


* **Cách Switch xây dựng bảng MAC:**
* **Học địa chỉ (Learning - Xem Source MAC):** Khi có frame đi vào một cổng, Switch kiểm tra địa chỉ MAC nguồn. Nếu chưa có trong bảng, Switch lưu địa chỉ MAC đó kèm theo số cổng tương ứng. Nếu đã có, Switch làm mới bộ đếm thời gian (refresh timer).


* **Chuyển tiếp (Forwarding - Xem Destination MAC):**
* Nếu MAC đích có trong bảng: Switch chuyển frame ra **đúng cổng đó** (Filtering).


* Nếu MAC đích **không có** trong bảng (Unknown Unicast), hoặc là địa chỉ **Broadcast/Multicast**: Switch sẽ đẩy frame ra **tất cả các cổng** trừ cổng nhận vào (Flooding).







---

## 7.4 Phương Thức Chuyển Tiếp & Cấu Hình Cổng Switch (Switch Forwarding Methods)

* **Phương thức chuyển tiếp Frame:**
1. **Store-and-Forward (Lưu và chuyển tiếp):**
* Nhận toàn bộ frame, kiểm tra lỗi bằng FCS (CRC) trước khi chuyển tiếp.


* Giảm lưu lượng rác trên mạng nhưng độ trễ (latency) cao hơn. Bắt buộc dùng cho dịch vụ QoS.




2. **Cut-Through (Cắt xuyên qua):**
* **Fast-Forward:** Đọc xong 6 bytes địa chỉ MAC đích là chuyển tiếp ngay. Độ trễ thấp nhất nhưng có thể truyền cả frame lỗi.


* **Fragment-Free:** Đọc và kiểm tra lỗi ở 64 bytes đầu tiên của frame (nơi dễ xảy ra va chạm/xung đột nhất) rồi mới chuyển tiếp.






* **Bộ nhớ đệm (Memory Buffering):**
* **Port-based Memory:** Lưu trữ theo hàng đợi gắn liền với từng cổng.


* **Shared Memory:** Bộ nhớ đệm chung động cho tất cả các cổng, tối ưu khi truyền bất đối xứng (Asymmetric switching).




* **Cài đặt Duplex & Auto-MDIX:**
* **Duplex Mismatch:** Xảy ra khi một đầu cổng cấu hình Full-Duplex còn đầu kia cấu hình Half-Duplex, gây ra xung đột và giảm hiệu năng mạng nghiêm trọng.


* **Auto-MDIX:** Tính năng tự động phát hiện loại cáp (thẳng hay chéo) để tự điều chỉnh giao diện kết nối.