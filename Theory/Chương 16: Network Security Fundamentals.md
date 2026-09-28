# Chương 16: Network Security Fundamentals (Cơ bản về Bảo mật Mạng)

## 1. Mối đe dọa & Lỗ hổng bảo mật (Security Threats & Vulnerabilities)

### 4 Mối đe dọa chính sau khi bị thâm nhập:

* **Information Theft**: Đánh cắp thông tin bảo mật/bí mật.


* **Data Loss and Manipulation**: Mất mát hoặc làm sai lệch dữ liệu.


* **Identity Theft**: Giả mạo hoặc đánh cắp danh tính người dùng.


* **Disruption of Service**: Làm gián đoạn hoặc ngưng trệ dịch vụ (DoS).



### 3 Nhóm lỗ hổng hệ thống:

1. **Lỗ hổng công nghệ (Technological)**: Nhược điểm của giao thức TCP/IP, HĐH, hoặc phần cứng.


2. **Lỗ hổng cấu hình (Configuration)**: Tài khoản không bảo mật, mật khẩu yếu, cấu hình dịch vụ sai.


3. **Lỗ hổng chính sách (Security Policy)**: Thiếu chính sách bảo mật bằng văn bản, không có quy trình ứng phó sự cố.



### 4 Mối đe dọa an ninh vật lý (Physical Threats):

* **Hardware**: Tàn phá vật lý máy chủ, switch, router, cáp.


* **Environmental**: Nhiệt độ hoặc độ ẩm quá cao/quá thấp.


* **Electrical**: Điện áp tăng đột ngột, sụt áp, mất điện hoàn toàn.


* **Maintenance**: Xử lý linh kiện kém (tĩnh điện ESD), thiếu linh kiện dự phòng, dán nhãn sai.



---

## 2. Các hình thức tấn công mạng (Network Attacks)

### Mã độc (Malware)

* **Virus**: Cần gắn vào một tệp/chương trình lưu trữ và **cần sự tương tác của con người** để lây lan.


* **Worm (Sâu máy tính)**: Chương trình độc lập, **tự nhân bản và lây lan tự động** qua mạng mà không cần sự can thiệp của con người.


* **Trojan Horse**: Núp bóng phần mềm hợp pháp, **không tự nhân bản**, lây lan qua tương tác người dùng (mở file đính kèm, tải ứng dụng).



### 3 Loại tấn công mạng chính

1. **Reconnaissance (Thăm dò)**: Bố trí, thu thập thông tin hệ thống/vùng IP (dùng các công cụ như `nslookup`, `whois`, ping sweep).


2. **Access Attacks (Truy cập trái phép)**: Khai thác lỗ hổng để chiếm quyền điều khiển:


* *Password attack*: Tấn công vét cạn (brute-force), sniffer.


* *Trust exploitation*: Lợi dụng lòng tin giữa các thiết bị.


* *Port redirection*: Dùng thiết bị bị chiếm quyền làm bàn đạp tấn công thiết bị khác.


* *Man-in-the-middle (MitM)*: Đứng giữa 2 bên để đọc/chỉnh sửa dữ liệu.




3. **Denial of Service (DoS / DDoS)**: Làm kiệt quệ tài nguyên hệ thống để ngăn chặn người dùng hợp pháp.


* **DDoS**: Tấn công DoS phân tán từ mạng lưới các máy tính bị nhiễm độc (**Zombies**) dưới sự điều khiển của **Botnet**.





---

## 3. Các phương pháp giảm thiểu tấn công (Mitigation Techniques)

* **Chiến lược Phòng thủ chiều sâu (Defense-in-Depth)**: Áp dụng bảo mật nhiều lớp (Router, Switch, Firewall, IPS, VPN, AAA Server, ESA/WSA).


* **Cập nhật & Vá lỗi (Patches/Updates)**: Cách hiệu quả nhất để phòng chống Worm là cập nhật bản vá bảo mật cho HĐH.


* **Sao lưu dữ liệu (Backups)**: Thực hiện định kỳ, kiểm tra tính toàn vẹn, bảo vệ bằng mật khẩu mạnh và lưu trữ an toàn offsite.


* **Mô hình AAA (Authentication, Authorization, Accounting)**:
* **Authentication (Xác thực)**: Bạn là ai?


* **Authorization (Ủy quyền)**: Bạn được phép làm gì / truy cập tài nguyên nào?


* **Accounting (Ghi nhật ký)**: Bạn đã thực hiện những thao tác gì?




* **Tường lửa (Firewall)**: Kiểm soát lưu lượng truy cập giữa các vùng mạng (Inside, Outside, **DMZ** - vùng chứa Server công cộng).


* *Các cơ chế*: Packet filtering, Application filtering, URL filtering, Stateful Packet Inspection (SPI).





---

## 4. Tăng cường bảo mật thiết bị Cisco (Device Hardening)

### Quy tắc đặt mật khẩu mạnh & Passphrase

* Tối thiểu 8–10 ký tự, kết hợp chữ hoa, chữ thường, số và ký tự đặc biệt.


* Khuyên dùng **Passphrase** (cụm từ mật khẩu chứa khoảng trắng) vì dài hơn, dễ nhớ nhưng cực kỳ khó đoán.



### Các lệnh bảo mật nâng cao trên Cisco IOS:

```text
Router(config)# service password-encryption
! Mã hóa tất cả mật khẩu dạng rõ (plaintext) trong file cấu hình[cite: 16]

Router(config)# security passwords min-length 8
! Bắt buộc độ dài mật khẩu tối thiểu là 8 ký tự[cite: 16]

Router(config)# login block-for 120 attempts 3 within 60
! Khóa đăng nhập 120 giây nếu nhập sai 3 lần trong vòng 60 giây (chống Brute-force)[cite: 16]

Router(config-line)# exec-timeout 5 30
! Tự động ngắt phiên làm việc sau 5 phút 30 giây không hoạt động[cite: 16]

```

### Quy trình các bước cấu hình SSH (thay thế Telnet):

1. **Đổi Hostname duy nhất**: `hostname R1`

2. **Cấu hình Domain Name**: `ip domain-name cisco.com`

3. **Tạo RSA Key**: `crypto key generate rsa general-keys modulus 1024` (Tối thiểu 1024 bits)


4. **Tạo tài khoản cục bộ**: `username admin secret P@ssw0rd123`

5. **Cấu hình Line VTY**:
* `line vty 0 4`

* `login local` (Xác thực bằng database cục bộ)


* `transport input ssh` (Chỉ cho phép kết nối SSH)





### Tắt các dịch vụ không sử dụng

Tắt các service thừa để giải phóng tài nguyên CPU/RAM và giảm bề mặt tấn công.

* Kiểm tra port đang mở bằng lệnh: `show ip ports all` (trên IOS-XE) hoặc `show control-plane host open-ports`.