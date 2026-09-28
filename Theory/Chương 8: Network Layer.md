# Chương 8: Tầng Mạng (Network Layer)

## 8.1 Đặc Tính Tầng Mạng (Network Layer Characteristics)

* **Chức năng chính:** Cho phép các thiết bị cuối trao đổi dữ liệu qua mạng thông qua 4 thao tác cơ bản: Định địa chỉ (Addressing), Đóng gói (Encapsulation), Định tuyến (Routing) và Mở gói (De-encapsulated).


* **Hai giao thức chính:** IPv4 và IPv6.


* **Đặc tính cơ bản của IP:**
* **Connectionless (Không kết nối):** Không thiết lập kết nối trước khi gửi dữ liệu và không phát thông báo báo trước cho thiết bị nhận.


* **Best Effort (Nỗ lực tối đa):** Không đảm bảo gói tin sẽ đến đích, không có cơ chế gửi lại khi bị mất dữ liệu để giảm bớt overhead (chi phí quản lý).


* **Media Independent (Độc lập môi trường truyền):** Hoạt động không phụ thuộc vào phương tiện truyền dẫn ở Tầng Vật lý (cáp đồng, cáp quang, sóng không dây). Tầng Mạng điều chỉnh kích thước gói tin theo thông số **MTU (Maximum Transmission Unit)** do Tầng Liên kết Dữ liệu cung cấp.





---

## 8.2 Gói Tin IPv4 (IPv4 Packet)

* **Cấu trúc IPv4 Header:** Có độ dài mặc định **20 bytes**.


* Các trường quan trọng trong IPv4 Header:



| Trường (Field) | Chức năng |
| --- | --- |
| **Version** | Xác định phiên bản IP (luôn là `0100` đối với IPv4).|
| **Differentiated Services (DS)** | Sử dụng cho Quality of Service (QoS) để ưu tiên lưu lượng truy cập.|
| **Header Checksum** | Kiểm tra lỗi phát sinh trong phần Header của gói tin IPv4.|
| **Time-to-Live (TTL)** | Số chặng (hop count) Lớp 3 tối đa. Khi TTL giảm về 0, Router sẽ hủy gói tin để tránh vòng lặp.|
| **Protocol** | Nhận diện giao thức ở Tầng Giao vận (Lớp 4) tiếp theo (như ICMP, TCP, UDP).|
| **Source / Destination IP Address** | Địa chỉ IPv4 nguồn và đích (mỗi địa chỉ dài 32 bits).|

---

## 8.3 Gói Tin IPv6 (IPv6 Packet)

* **Lý do ra đời IPv6:**
* Cạn kiệt địa chỉ IPv4.


* Thiếu kết nối end-to-end trực tiếp do sử dụng NAT (Network Address Translation).


* Giảm sự phức tạp của mạng do hạn chế của NAT.




* **Cải tiến của IPv6:**
* Không gian địa chỉ khổng lồ (**128 bits** so với 32 bits của IPv4).


* Header đơn giản hóa (cố định **40 bytes**), loại bỏ các trường: *Flag*, *Fragment Offset*, *Header Checksum* giúp Router xử lý nhanh hơn.


* Không cần sử dụng NAT.




* Các trường chính trong IPv6 Header:



| Trường (Field) | Chức năng |
| --- | --- |
| **Version** | Xác định phiên bản IP (luôn là `0110` đối với IPv6).|
| **Traffic Class** | Tương đương trường DS trong IPv4 (dùng cho QoS).|
| **Flow Label** | Đánh dấu các gói tin thuộc cùng một luồng xử lý.|
| **Payload Length** | Độ dài phần dữ liệu (tải trọng) đi kèm.|
| **Next Header** | Tương đương trường Protocol trong IPv4 (chỉ định giao thức kế tiếp hoặc Extension Header).|
| **Hop Limit** | Thay thế trường TTL của IPv4.|
| **Source / Destination Address** | Địa chỉ IPv6 nguồn và đích (mỗi địa chỉ dài 128 bits).|

---

## 8.4 Cách Thiết Bị Host Định Tuyến (How a Host Routes)

* **Hướng chuyển tiếp của Host:**
* **Chính nó (Itself):** Sử dụng Loopback IP (`127.0.0.1` với IPv4, `::1` với IPv6).


* **Host nội bộ (Local Host):** Đích nằm trong cùng mạng LAN.


* **Host từ xa (Remote Host):** Đích nằm ở mạng LAN khác.




* **Cổng Mặc Định (Default Gateway - DGW):**
* Là giao diện Router/Layer 3 Switch kết nối trực tiếp với mạng LAN.


* Có địa chỉ IP cùng dải mạng với các Host trong LAN.


* Tiếp nhận dữ liệu từ LAN để định tuyến ra các mạng bên ngoài.




* **Bảng định tuyến trên Host (Host Routing Table):** Có thể kiểm tra trên Windows bằng lệnh `route print` hoặc `netstat -r`.



---

## 8.5 Giới Thiệu Về Định Tuyến Ở Router (Router Routing Tables)

* **Quá trình xử lý của Router:** Khi nhận frame, Router gỡ bỏ Header Lớp 2 (De-encapsulation), đọc địa chỉ IP đích ở Lớp 3, tra cứu bảng định tuyến, sau đó đóng gói lại Header Lớp 2 mới để chuyển tiếp (Encapsulation).


* 3 Loại Tuyến Đường trong Bảng Định Tuyến của Router:


1. **Directly Connected (Kết nối trực tiếp):** Tự động thêm vào khi giao diện (interface) hoạt động và được gán IP.


2. **Remote Networks (Mạng từ xa):** Các mạng không kết nối trực tiếp, Router học qua:
* **Static Routing (Định tuyến tĩnh):** Do quản trị viên cấu hình thủ công.


* **Dynamic Routing (Định tuyến động):** Tự động học và cập nhật qua các giao thức định tuyến (như OSPF, EIGRP).




3. **Default Route (Tuyến đường mặc định):** Chuyển tất cả lưu lượng không có trong bảng định tuyến về một hướng chỉ định.




* Ký hiệu nguồn tuyến đường (`show ip route`):


* `C`: Mạng kết nối trực tiếp (Directly connected).


* `L`: Địa chỉ IP cục bộ trên giao diện của Router.


* `S`: Định tuyến tĩnh (Static route).


* `O`: Học qua giao thức OSPF.


* `D`: Học qua giao thức EIGRP.