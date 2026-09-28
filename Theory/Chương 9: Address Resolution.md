# Chương 9: Phân Giải Địa Chỉ (Address Resolution)

## 9.1 Địa Chỉ MAC Và Địa Chỉ IP (MAC and IP)

* **Vai trò của hai loại địa chỉ:**
* **Địa chỉ MAC (Lớp 2 - Physical Address):** Dùng để chuyển giao khung dữ liệu (frame) giữa các card mạng (NIC) trong cùng một mạng LAN (cùng Ethernet network).


* **Địa chỉ IP (Lớp 3 - Logical Address):** Dùng để định tuyến gói tin (packet) từ thiết bị nguồn đến thiết bị đích cuối cùng xuyên qua các mạng.




* **Gửi dữ liệu trong cùng dải mạng (Destination on Same Network):**
* Địa chỉ IP đích: Địa chỉ của thiết bị nhận.


* Địa chỉ MAC đích: Địa chỉ MAC của chính thiết bị nhận đó.




* **Gửi dữ liệu khác dải mạng (Destination on Remote Network):**
* Địa chỉ IP đích: Địa chỉ của thiết bị nhận ở mạng từ xa.


* Địa chỉ MAC đích: Địa chỉ MAC của **Cổng Mặc Định (Default Gateway)** (tức giao diện Router kết nối trực tiếp với LAN).





---

## 9.2 Giao Thức ARP (Address Resolution Protocol)

* **Chức năng chính của ARP:**
* Ánh xạ địa chỉ IPv4 sang địa chỉ MAC tương ứng.


* Duy trì và lưu trữ bảng thông tin ánh xạ này trong **Bảng ARP (ARP Table / ARP Cache)**.




* **Cơ chế hoạt động:**
* **ARP Request (Yêu cầu ARP):** Được gửi dưới dạng **Broadcast** (đến tất cả thiết bị trong mạng LAN) để hỏi "Ai có địa chỉ IPv4 này, hãy cho tôi biết địa chỉ MAC".


* **ARP Reply (Phản hồi ARP):** Thiết bị nắm giữ địa chỉ IPv4 đó sẽ phản hồi lại bằng tin nhắn **Unicast** chứa địa chỉ MAC của mình.


* Nếu thiết bị đích nằm ở mạng từ xa, ARP Request sẽ hỏi địa chỉ MAC của Default Gateway.




* **Xóa thông tin trong Bảng ARP:**
* Bảng ARP lưu các bản ghi tạm thời. Thiết bị dùng bộ đếm thời gian (**ARP cache timer**) để tự động xóa các bản ghi không sử dụng sau một khoảng thời gian nhất định.


* Người quản trị cũng có thể xóa bảng ARP thủ công.




* **Các lệnh kiểm tra Bảng ARP:**
* Trên Router Cisco: `show ip arp`

* Trên máy tính Windows: `arp -a`



* **Vấn đề và rủi ro an ninh đối với ARP:**
* **ARP Broadcasting:** Quá nhiều lưu lượng ARP broadcast có thể làm giảm hiệu suất mạng.


* **ARP Spoofing / ARP Poisoning:** Kẻ tấn công có thể giả mạo phản hồi ARP Reply (giả làm Default Gateway) để chặn bắt hoặc sai lệch lưu lượng truy cập. Switch cấp doanh nghiệp thường có các tính năng bảo mật để phòng chống tấn công này.





---

## 9.3 Giao Thức IPv6 Neighbor Discovery (ND)

* **Khái niệm:** IPv6 không sử dụng ARP mà sử dụng giao thức **Neighbor Discovery (ND)** thông qua các thông điệp **ICMPv6** để phân giải địa chỉ và quản lý các thiết bị lân cận.


* **Chức năng của ND Protocol:** Phân giải địa chỉ (Address resolution), phát hiện Router (Router discovery) và dịch vụ chuyển hướng (Redirection services).


* **Các thông điệp ICMPv6 chính trong ND:**

| Thông điệp ICMPv6 | Tên gọi | Chức năng |
| --- | --- | --- |
| **NS** | Neighbor Solicitation | Gửi dưới dạng **Multicast** để hỏi địa chỉ MAC tương ứng với một IPv6.|
| **NA** | Neighbor Advertisement | Gửi dưới dạng Unicast để phản hồi lại địa chỉ MAC cho thiết bị yêu cầu.|
| **RS** | Router Solicitation | Host gửi để chủ động tìm kiếm các Router IPv6 trên mạng.|
| **RA** | Router Advertisement | Router gửi định kỳ hoặc phản hồi RS để cung cấp thông tin cấu hình mạng cho Host.|
| **Redirect** | Redirect Message | Router dùng để chỉ định chặng kế tiếp (next-hop) tối ưu hơn cho Host.|