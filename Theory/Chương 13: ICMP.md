# Chương 13: ICMP

## 1. Tổng quan về ICMP

**ICMP (Internet Control Message Protocol)** là giao thức cung cấp thông tin phản hồi và báo lỗi liên quan đến quá trình xử lý gói tin IP.

* **ICMPv4**: Dành cho mạng IPv4. (Đôi khi bị quản trị viên chặn trong mạng nội bộ vì lý do bảo mật).


* **ICMPv6**: Dành cho mạng IPv6, bổ sung nhiều tính năng mới mạnh mẽ hơn.



---

## 2. Các thông điệp ICMP chung (Cả ICMPv4 & ICMPv6)

* **Host Reachability (Kiểm tra khả năng kết nối)**:
* Sử dụng cặp thông điệp **ICMP Echo Request** (yêu cầu) và **ICMP Echo Reply** (phản hồi).




* **Destination / Service Unreachable (Không thể tới đích)**:
* Router/Host gửi thông điệp này báo cho nguồn biết gói tin không thể chuyển tới nơi kèm theo **Code** nguyên nhân:


* *ICMPv4*: Code 0 (Net unreachable), Code 1 (Host unreachable), Code 3 (Port unreachable)...


* *ICMPv6*: Code 0 (No route), Code 1 (Prohibited by firewall), Code 3 (Address unreachable)...






* **Time Exceeded (Quá thời gian / Hết Hop)**:
* Phát ra khi giá trị **TTL** (IPv4) hoặc **Hop Limit** (IPv6) của gói tin giảm về `0`. Đây chính là cơ chế cốt lõi để lệnh `traceroute` hoạt động.





---

## 3. Các thông điệp ICMPv6 đặc thù (Neighbor Discovery Protocol - NDP)

ICMPv6 hỗ trợ giao thức NDP giúp các thiết bị IPv6 tự động trao đổi thông tin với nhau:

### Giữa Router và Client:

1. **Router Solicitation (RS)**: Client gửi để hỏi "Có Router IPv6 nào ở đây không?"


2. **Router Advertisement (RA)**: Router gửi định kỳ (mỗi 200 giây) hoặc phản hồi RS để cấp Prefix, Prefix Length và Gateway cho Client.



### Giữa các Client với nhau:

3. **Neighbor Solicitation (NS)**: Gửi đi để hỏi MAC Address của một IP cụ thể (thay thế cho ARP của IPv4) hoặc dùng cho DAD.


4. **Neighbor Advertisement (NA)**: Phản hồi lại NS kèm theo thông tin MAC Address.


5. **DAD (Duplicate Address Detection)**: Thiết bị tự gửi NS kiểm tra xem IP mình định dùng có bị trùng với máy nào trong mạng không trước khi gán.



---

## 4. Công cụ Ping & Traceroute

### Công cụ `ping`

Dùng cặp thông điệp ICMP Echo Request / Reply để kiểm tra khả năng kết nối:

* **Ping Loopback (`127.0.0.1` với IPv4 / `::1` với IPv6)**: Kiểm tra xem giao thức TCP/IP đã được cài đặt và hoạt động chuẩn trên máy chưa.


* **Ping Default Gateway**: Kiểm tra kết nối từ Host ra tới Router trong mạng LAN.


* **Ping Remote Host**: Kiểm tra kết nối xuyên qua Router sang mạng khác/Internet.


* *Lưu ý*: Phát ping đầu tiên thường bị **Timeout** do thiết bị còn mất thời gian phân giải MAC (ARP hoặc NDP).



### Công cụ `traceroute` (Windows dùng `tracert`)

Dùng để liệt kê **danh sách các Hop (Router)** trên đường đi từ nguồn tới đích:

* **Cơ chế hoạt động**:
* Gửi gói tin đầu tiên với `TTL = 1` $\rightarrow$ Router đầu tiên giảm TTL về 0, hủy gói tin và trả về `ICMP Time Exceeded`.


* Tiếp tục tăng `TTL = 2, 3, 4...` để khám phá từng Router tiếp theo cho đến khi tới đích.




* Dấu sao `*` trên màn hình báo hiệu Router đó bị nghẽn, lỗi hoặc chặn ICMP.



---

## 📝 Bảng so sánh nhanh câu lệnh Ping & Traceroute

| Tính năng | Windows Command Prompt | Cisco IOS (Router/Switch) |
| --- | --- | --- |
| **Ping** | `ping <IP>` | `ping <IP>` |
| **Traceroute** | `tracert <IP>` | `traceroute <IP>` |