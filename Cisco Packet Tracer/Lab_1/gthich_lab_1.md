==============================================Đề bài lab 1======================================================

Môn Mạng máy tính mà có Cisco Packet Tracer (PT) thì thực hành cực kỳ trực quan! Tool này giúp bạn giả lập cả một hệ thống mạng thật (cắm dây, cấu hình router, switch, PC...) mà không sợ làm cháy thiết bị.

Để bạn bắt đầu nhanh nhất mà không bị "ngợp", dưới đây là hướng dẫn làm quen và bài lab đầu tiên:

**1. Giao diện cơ bản cần nhớ**

* **Góc dưới cùng bên trái (Thư viện thiết bị):** Nơi lấy PC, Laptop, Switch, Router, dây cáp...
* **Khu vực làm việc chính (Workspace):** Nơi bạn kéo thả thiết bị vào và nối dây.
* **Chế độ Realtime / Simulation (Góc dưới bên phải):**
* *Realtime:* Mạng hoạt động theo thời gian thực.
* *Simulation:* Cho phép bạn xem từng gói tin (packet) di chuyển qua các thiết bị như thế nào (rất tốt để hiểu bản chất).



---

**2. Bài Lab 01: Nối 2 máy tính và cho ping thông nhau**

1. **Tạo 2 máy tính và nối dây:**
* Kéo **2 PC** từ thư viện thiết bị ra màn hình làm việc.
* Chọn biểu tượng **Tia sét** (Connections) -> Chọn dây **Copper Straight-Through** (dây thẳng) hoặc chọn luôn dây màu cam (Auto) để phần mềm tự chọn dây.
* Click vào PC0 chọn cổng `FastEthernet0`, nối sang PC1 chọn cổng `FastEthernet0`. Dây xuất hiện 2 chấm xanh lá là nối thành công.


2. **Đặt địa chỉ IP cho 2 PC:**
* Click vào **PC0** -> chọn tab **Desktop** -> chọn **IP Configuration**.
* Đặt IP: `192.168.1.1`, Subnet Mask tự nhảy điền: `255.255.255.0`.
* Tương tự với **PC1**: Click vào -> **Desktop** -> **IP Configuration** -> Đặt IP: `192.168.1.2`.


3. **Kiểm tra kết nối (Ping):**
* Click vào **PC0** -> **Desktop** -> chọn **Command Prompt**.
* Gõ lệnh: `ping 192.168.1.2` rồi bấm Enter.
* Xem kết quả: Nếu hiện `Reply from 192.168.1.2: bytes=32 time...` là 2 máy đã thông nhau!


---

**Mẹo nhỏ khi học môn này:**

* Nếu muốn thấy gói tin "bay" qua lại: Chuyển sang chế độ **Simulation** ở góc dưới bên phải, bấm nút **Play** để xem gói tin ICMP chạy từ PC0 sang PC1.
* Đừng ngại click đúp vào thiết bị để khám phá các tab cài đặt.

==============================================Lý thuyết=========================================================

Giải thích chi tiết và ngắn gọn từng thắc mắc của bạn để nắm rõ bản chất bài lab nhé:

**1. Tại sao phải chọn cáp chéo (Copper Cross-Over) mà không chọn dây khác?**

* **Nguyên lý truyền tin:** Chuẩn mạng Ethernet dùng các cặp dây riêng biệt để **Truyền dữ liệu (TX - Transmit)** và **Nhận dữ liệu (RX - Receive)**.
* **Quy tắc thiết bị:** PC0 và PC1 là hai thiết bị cùng loại (cùng chân cắm).
* Nếu dùng cáp thẳng (Straight-Through), chân TX của PC0 sẽ nối đúng vào chân TX của PC1 $\rightarrow$ **Xung đột (cả 2 cùng nói, không ai nghe)**.
* Cáp chéo (Cross-Over) được bấm chéo đầu dây để chân TX (Gửi) của PC0 nối thẳng vào chân RX (Nhận) của PC1 và ngược lại $\rightarrow$ **Hai máy mới "nói chuyện" được với nhau**.



---

**2. Ý nghĩa địa chỉ IPv4 (192.168.1.1 & 192.168.1.2) và dãy số này có bắt buộc không?**

* **Ý nghĩa:** IPv4 giống như **địa chỉ nhà** hay **số điện thoại** định danh cho từng thiết bị trong mạng để dữ liệu gửi đi biết chính xác nơi đến.
* **Có bắt buộc không?:** **Không bắt buộc** phải ghi đúng dãy số đó. Bạn thích đặt là `10.0.0.1` với `10.0.0.2` hoặc `172.16.0.1` với `172.16.0.2` đều được.
* **Điều kiện duy nhất:** Hai máy phải nằm **cùng một dải mạng (Subnet)** thì mới thấy nhau nếu không có Router.

---

**3. Ý nghĩa của Subnet Mask (`255.255.255.0`)**

* **Ý nghĩa:** Subnet Mask đóng vai trò như **ranh giới quận/huyện** để xác định phần nào trong địa chỉ IP là "Mã mạng" (Network ID) và phần nào là "Mã máy" (Host ID).
* **Cách hiểu đơn giản:** Với `255.255.255.0`, 3 số đầu (`192.168.1`) là tên phố/xóm chung, còn số cuối (`.1` hay `.2`) là số nhà riêng.
* PC0 (`192.168.1.1`) và PC1 (`192.168.1.2`) thấy 3 số đầu giống nhau $\rightarrow$ Hiểu là **ở cùng xóm**, truyền tin trực tiếp cho nhau được.



---

**4. Lệnh `ping 192.168.1.2` trong Command Prompt có ý nghĩa gì?**

* Lệnh `ping` gửi các gói tin kiểm tra (ICMP Echo Request) từ PC0 sang PC1.
* Nó giống như việc PC0 đứng cất tiếng gọi: *"Alo PC1 (`192.168.1.2`), cậu có nghe thấy tớ không?"*.
* Khi có `Reply...`, PC1 phản hồi: *"Có, tớ nghe rồi!"* $\rightarrow$ Xác nhận đường truyền vật lý lẫn IP đều đã thông suốt.

---

**5. Chế độ Simulation và công cụ lá thư (Simple PDU) dùng để làm gì?**

* **Thực tế:** Các gói tin di chuyển trong dây cáp là tín hiệu điện/quang rất nhanh, mắt thường không thấy được.
* **Chế độ Simulation:** Cho phép **"làm chậm thời gian"** để quan sát đường đi của gói tin.
* **Lá thư (Simple PDU):** Là công cụ giả lập một gói tin thử nghiệm. Khi bạn click PC0 rồi click PC1 và nhấn Play, phần mềm sẽ trình chiếu hoạt họa gói tin rời PC0, chạy qua dây cáp đến PC1, rồi PC1 gửi phản hồi ngược lại. Việc này giúp bạn trực quan hóa cách mạng vận hành.


===================================GIẢI THÍCH THÊM====================================


à khoan mình thắc mắc là cái ipv4 điền ntn cx đc đúng ko? điền random cx đc, nhưng muốn kết nốt thì 3 số đầu phải giống nhau? =))




Câu trả lời ngắn gọn là: **Gần đúng, nhưng có một vài quy tắc bắt buộc bạn cần lưu ý!**

Không phải điền hoàn toàn ngẫu nhiên số nào cũng được, nhưng ý của bạn về việc **3 số đầu phải giống nhau** là đúng trong ngữ cảnh bài lab này.

---

**1. Điều đúng trong ý của bạn:**
Với Subnet Mask mặc định là `255.255.255.0`:

* Bắt buộc 3 số đầu của 2 máy phải **hoàn toàn giống hệt nhau** (ví dụ: `192.168.1.x` hoặc `10.0.0.x` hoặc `123.45.67.x`).
* Số thứ 4 (số cuối) phải **khác nhau** giữa các máy (máy này là `.1` thì máy kia phải là `.2`, `.3`,...). Nếu đặt số cuối giống nhau sẽ bị đụng độ IP (IP Conflict).

---

**2. Điểm "KHÔNG ĐƯỢC" điền random tự do:**
Địa chỉ IPv4 gồm 4 nhóm số dạng `A.B.C.D`. Bạn **không thể** gõ đại bất kỳ số nào vì các lý do sau:

* **Mỗi nhóm số chỉ được nằm trong khoảng từ 0 đến 255:**
* Ví dụ: `192.168.1.300` $\rightarrow$ **Sai**, Packet Tracer sẽ báo lỗi ngay vì số `300` vượt quá giới hạn $255$.


* **Có những dải IP đặc biệt không được dùng để đặt cho máy tính:**
* **Địa chỉ Loopback (`127.x.x.x`):** Dùng cho máy tự kiểm tra chính nó (không gửi ra ngoài dây cáp được).
* **Địa chỉ Multicast (`224.0.0.0` đến `239.255.255.255`):** Dùng cho truyền tin nhóm, không đặt cho PC.
* **Số cuối cùng là `.0` hoặc `.255` (với Subnet Mask `255.255.255.0`):**
* Dạng `192.168.1.0`: Đây là **Địa chỉ mạng (Network ID)**, đại diện cho cả xóm, không gán cho máy cụ thể.
* Dạng `192.168.1.255`: Đây là **Địa chỉ quảng bá (Broadcast ID)**, dùng để gửi tin cho tất cả các máy trong xóm cùng lúc.





---

**Tóm lại:**
Khi làm Lab không nối Internet, bạn có thể tự do sáng tạo chọn các dải IP thông dụng như `192.168.x.x`, `10.x.x.x`, hoặc `172.16.x.x`. Chỉ cần nhớ **3 số đầu giống nhau**, **số cuối từ 1 đến 254 và khác nhau** giữa các PC là chắc chắn ping thông!




lỡ mình ghi 1 số nào quá 255 hay nhỏ hơn 0 là ko đc nhỉ? =))




Đúng rồi đấy =))

Nếu bạn cố tình nhập số nhỏ hơn 0 (số âm) hoặc lớn hơn 255, phần mềm Packet Tracer hay máy tính thật sẽ chặn ngay lập tức và báo lỗi.

Lý do cực kỳ đơn giản đến từ bản chất máy tính:

Mỗi nhóm số (Octet) trong IPv4 được lưu bằng đúng 1 Byte (8 bit binary).

Trong hệ nhị phân, 8 bit chỉ gồm các dãy số 0 và 1 (ví dụ: 00000000 đến 11111111).

Khi chuyển dãy 8 bit này sang hệ thập phân (số đếm bình thường của mình):

Nhỏ nhất là 00000000 = 0

Lớn nhất là 11111111 = 255 (128+64+32+16+8+4+2+1=255)

Vì bộ nhớ máy tính dành cho mỗi ô chỉ có tối đa 8 bit, nên nó không thể chứa số âm hay số lớn hơn 255 được. Bạn nhập 256 hay -1 là máy tính chịu chết, không dịch ra mã nhị phân 8 bit được!