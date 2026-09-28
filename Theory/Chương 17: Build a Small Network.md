# Chương 17: Build a Small Network (Xây dựng một mạng nhỏ)

## 1. Thiết kế & Thành phần Mạng nhỏ (17.1 - 17.3)

* **Tiêu chí chọn thiết bị:** Chi phí (cost), tốc độ/loại cổng (speed/ports), khả năng mở rộng (expandability) và tính năng HĐH (OS features).


* **Lập kế hoạch IP:** Cần phân hoạch địa chỉ IP rõ ràng cho thiết bị người dùng, máy chủ, thiết bị ngoại vi và thiết bị trung gian (Switch/Router).


* **Tính dự phòng (Redundancy):** Lắp đặt phần cứng dư thừa (Switches, Routers, Servers) hoặc đường truyền kép (duplicate links) để loại bỏ điểm lỗi đơn lẻ (single points of failure).


* **Quản lý lưu lượng (QoS):** Sử dụng các hàng đợi ưu tiên (High, Medium, Normal, Low) để ưu tiên dữ liệu thời gian thực như Voice/Video (RTP/RTCP) trước lưu lượng thông thường (SMTP, Web, FTP).


* **Mở rộng mạng lớn hơn:** Cần 4 yếu tố — Sơ đồ mạng (Documentation), Danh mục thiết bị (Inventory), Ngân sách (Budget) và Phân tích lưu lượng (Traffic analysis).



---

## 2. Kiểm tra kết nối & Lệnh hệ thống (17.4 - 17.5)

### Lệnh Kiểm tra Kết nối

* **`ping`:** Dùng ICMP Echo (Type 8) & Echo Reply (Type 0).


* Ký tự phản hồi trên IOS: `!` (thành công), `.` (hết thời gian - timeout), `U` (không thể tới đích - Unreachable).


* **Extended Ping:** Gõ `ping` ở chế độ Privileged EXEC (không kèm IP) để tùy chỉnh Source IP, Packet size, Timeout...




* **`traceroute` (IOS) / `tracert` (Windows):** Xác định các chặng (hops) gói tin đi qua.


* Windows dùng ICMP Echo; IOS/Linux dùng UDP cổng cao.




* **Network Baseline:** Lưu trữ thông tin Ping/Trace phản hồi tiêu chuẩn tại thời điểm mạng chạy ổn định để đối chiếu khi có sự cố.



### Lệnh Hệ điều hành Máy tính & IOS

* **Windows Host:** `ipconfig`, `ipconfig /all`, `ipconfig /release`, `ipconfig /renew`, `ipconfig /displaydns`, `arp -a`.


* **Linux Host:** `ifconfig`, `ip address`, `arp`.


* **macOS Host:** `ifconfig`, `networksetup -getinfo <service>`.


* **Cisco IOS show commands:**
* `show running-config`

* `show ip interface brief` (xem nhanh trạng thái IP và cổng)


* `show cdp neighbors` / `show cdp neighbors detail` (xem thông tin thiết bị Cisco kết nối trực tiếp)


* `show ip route` / `show arp` / `show version`




---

## 3. Quy trình & Kịch bản Troubleshooting (17.6 - 17.7)

### 6 bước Troubleshooting chuẩn

1. **Identify the Problem** (Xác định sự cố)


2. **Establish a Theory of Probable Causes** (Xây dựng giả thuyết nguyên nhân)


3. **Test the Theory** (Kiểm tra giả thuyết)


4. **Establish a Plan of Action & Implement** (Lập kế hoạch & Thực hiện giải pháp)


5. **Verify Solution & Implement Preventive Measures** (Xác minh kết quả & Phòng ngừa)


6. **Document Findings, Actions, and Outcomes** (Ghi chép lại hồ sơ)



### Công cụ Giám sát Thời gian thực

* **`debug <process>`:** Xem thông điệp xử lý hệ thống theo thời gian thực (Cẩn thận: tốn nhiều CPU). Tắt bằng `no debug ...` hoặc `undebug all`.


* **`terminal monitor`:** Hiển thị các câu lệnh log/debug lên phiên làm việc từ xa (SSH/Telnet vty).



### Sự cố Thường gặp

* **Lỗi Duplex Mismatch:** Một bên Full-duplex, một bên Half-duplex làm suy giảm hiệu năng nghiêm trọng.


* **Địa chỉ APIPA (`169.254.x.x`):** Xuất hiện khi máy tính Windows không kết nối được với DHCP Server.


* **Default Gateway:** Sai Gateway dẫn đến việc không thể giao tiếp ra ngoài mạng nội bộ.


* **Lỗi DNS:** Dùng `nslookup` để kiểm tra khả năng phân giải tên miền.