# Đề bài lab 3

Phát hiện thú vị đấy!

Trong chế độ **Physical View**, Packet Tracer thiết kế giao diện đồ họa tường ngăn cho giống văn phòng thực tế thôi, chứ thuật toán bên dưới vẫn coi đường dây đó là cáp mạng nối từ PC qua lỗ hổng/ông gen âm tường vào tủ Rack (**Main Wiring Closet** - nơi chứa Switch).

Vì trong mô phỏng dây mạng cắm từ PC vào Switch không bị cản bởi vật cản đồ họa, nên tín hiệu điện vẫn chạy xèo xèo xuyên tường bình thường!

Thực tế ngoài đời nếu kéo dây từ phòng này sang phòng khác thì thợ kỹ thuật cũng sẽ đục tường, đi dây âm tường hoặc đi trên trần thạch cao để nối vào Switch trong phòng server y hệt như vậy =))

Bây giờ bạn đã sẵn sàng sang **Lab 3** chưa?

**Tên bài Lab 3: Nối 2 mạng LAN qua Router (Định tuyến cơ bản)**

* **Mục tiêu:** Học cách dùng **Router** để nối 2 mạng có dải IP khác nhau (ví dụ: Phòng Kinh doanh và Phòng Kỹ thuật không chung xóm).
* **Sơ đồ thiết bị:**
* **2 Switch 2960** (Switch0 và Switch1)
* **1 Router 2911** (hoặc 1941/2811 tùy chọn)
* **2 PC** (PC0 ở mạng 1, PC1 ở mạng 2)



**Nhiệm vụ dành cho bạn:**

1. **Xếp thiết bị:** 1 Router, 2 Switch, 2 PC.
* Bên trái: PC0 $\rightarrow$ Switch0.
* Bên phải: PC1 $\rightarrow$ Switch1.
* Ở giữa: Router0 (nối từ Switch0 sang Router0 và từ Switch1 sang Router0).


2. **Cắm dây cáp:** Cáp thẳng (Straight-Through).
* Nối PC0 vào Switch0, PC1 vào Switch1.
* Nối Switch0 vào cổng `GigabitEthernet0/0` (hoặc `FastEthernet0/0`) của Router.
* Nối Switch1 vào cổng `GigabitEthernet0/1` (hoặc `FastEthernet0/1`) của Router.


3. **Đặt IP cho 2 PC:** Khác dải mạng.
* **PC0:** IP `192.168.1.10`, Subnet Mask `255.255.255.0`
* **PC1:** IP `192.168.2.10`, Subnet Mask `255.255.255.0`
*(Để ý 2 PC thuộc 2 dải mạng khác nhau: `.1.x` và `.2.x`)*


Bạn kéo xong sơ đồ bài 3 rồi chụp ảnh lên nhé! Lúc này kết nối Router sẽ hiện **màu đỏ** và chúng ta sẽ tiến hành cấu hình lệnh đầu tiên trên Router.



Sơ đồ chuẩn đét luôn rồi!

Hai dây nối vào Router đang hiện **dấu chấm màu đỏ** là hoàn toàn chính xác. Mặc định các cổng của Router Cisco luôn ở trạng thái **Shutdown** (Tắt) để bảo mật, chúng ta phải vào bật nó lên và gán IP thì nó mới sáng đèn xanh.

Bây giờ bạn thực hiện cấu hình Router và gán Gateway theo các bước sau:

1. **1. Cấu hình cổng Gig0/0/0 (Mạng trái):** Bật cổng & Gán IP.
Click đôi vào **Router0** $\rightarrow$ Chọn thẻ **CLI** (hoặc thẻ **Config** $\rightarrow$ `GigabitEthernet0/0/0`):

* Gõ `no` nếu nó hỏi *Would you like to enter the initial configuration dialog? [yes/no]*
* Nhấn Enter và gõ các lệnh sau:

```text
enable
configure terminal
interface GigabitEthernet0/0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

```

*(Xác nhận thành công: Đèn bên trái chuyển sang màu xanh).*


2. **2. Cấu hình cổng Gig0/0/1 (Mạng phải):** Bật cổng & Gán IP.
Vẫn trong cửa sổ CLI của Router0, gõ tiếp:

```text
interface GigabitEthernet0/0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

```

*(Xác nhận thành công: Đèn bên phải chuyển sang màu xanh).*


3. **3. Khai báo Default Gateway cho 2 PC:** Cổng ra (Gateway).
Router giống như "cửa khẩu" để ra khỏi xóm. Ta phải báo cho PC biết địa chỉ cổng Router:

* **PC0:** Mở `IP Configuration` $\rightarrow$ Điền **Default Gateway**: `192.168.1.1`
* **PC1:** Mở `IP Configuration` $\rightarrow$ Điền **Default Gateway**: `192.168.2.1`


4. **4. Test Ping xuyên dải mạng:** Kiểm tra.
Mở **Command Prompt** trên **PC0** và gõ:
`ping 192.168.2.10`


*Lưu ý: Lần ping đầu tiên có thể bị mất 1 gói (`Request timed out`) do Router phải tìm ARP, 3 gói sau hiện `Reply...` là hoàn hảo!*

# Lý thuyết

Hỏi dồn một lần 7 câu chất lượng thế này chứng tỏ bạn đang học theo đúng kiểu **hiểu sâu bản chất** chứ không gật đầu cho qua! Giải đáp chi tiết từng câu cho bạn đây:

**1. Bản chất của Switch là gì?**
Switch là **thiết bị kết nối các máy tính trong CÙNG một mạng nội bộ (LAN)**.

* **Bản chất:** Nó đóng vai trò như một "ông bưu điện nội bộ" làm việc ở **Layer 2 (Data Link)**. Switch ghi nhớ địa chỉ **MAC** (địa chỉ phần cứng) của từng máy cắm vào cổng của nó.
* Khi PC A gửi tin cho PC B, Switch nhìn địa chỉ MAC đích và chuyển thẳng gói tin sang đúng cổng PC B, không làm phiền các máy khác.

---

**2. Bản chất của Router là gì? Vì sao chọn Router 2911?**

* **Bản chất của Router:** Router (Bộ định tuyến) là thiết bị **kết nối các MẠNG ĐẠI DƯƠNG/MẠNG KHÁC NHAU** lại với nhau (làm việc ở **Layer 3 - Network**). Nếu Switch chỉ biết địa chỉ MAC trong nhà, thì Router biết địa chỉ **IP** để dẫn đường cho gói tin đi sang "xóm khác" hoặc ra Internet.
* **Vì sao chọn Router 2911?** Router 2911 là một dòng Router tích hợp dịch vụ (ISR) chuẩn của Cisco có sẵn các cổng **GigabitEthernet** tốc độ cao, rất phổ biến trong các bài Lab chuẩn CCNA vì nó hỗ trợ đầy đủ mọi lệnh cấu hình cơ bản đến nâng cao.

---

**3. GigabitEthernet là gì?**
Đây là chuẩn tốc độ của cổng mạng:

* **Ethernet:** $10\text{ Mbps}$ (cổ đại)
* **FastEthernet:** $100\text{ Mbps}$ (khá nhanh)
* **GigabitEthernet:** $1000\text{ Mbps} = 1\text{ Gbps}$ (siêu nhanh, chuẩn hiện đại). Cổng trên Router 2911 dùng chuẩn này để đảm bảo truyền dữ liệu giữa các mạng không bị thắt cổ chai.

---

**4. Giải thích 6 lệnh CLI trên Router:**

* `enable`: Chuyển từ chế độ người dùng (chỉ nhìn) sang chế độ **Quản trị viên** (cho phép xem các cấu hình ẩn).
* `configure terminal` (viết tắt `conf t`): Vào chế độ **Cấu hình toàn cục**, bắt đầu được phép chỉnh sửa cài đặt của Router.
* `interface GigabitEthernet0/0/0` (viết tắt `int g0/0/0`): Chọn đúng cổng mạng `g0/0/0` để chui vào bên trong cổng đó chuẩn bị cài đặt.
* `ip address 192.168.1.1 255.255.255.0`: Gán cho cái cổng vừa chọn một địa chỉ IP và Subnet Mask để làm "thẻ căn cước" cho cổng đó.
* `no shutdown` (viết tắt `no sh`): Mặc định cổng Router luôn bị TẮT (Shutdown) để bảo mật. Lệnh này có nghĩa là **"Không tắt" = "BẬT CỔNG LÊN"**.
* `exit`: Thoát ra ngoài một cấp menu.

---

**5 & 7. Bản chất của Default Gateway & Tại sao octet thứ 3 lại là số 1 và 2?**

* **Bản chất của Default Gateway:**
* Hãy tưởng tượng PC của bạn ở trong một **ngôi làng**. Muốn gửi thư cho người trong làng, bạn tự đi bộ sang đưa (qua Switch).
* Nhưng nếu gửi thư sang **làng khác**, bạn không biết đường. Bạn bắt buộc phải ra **Cửa khẩu của làng** gửi cho ông biên phòng (Router), nhờ ông ấy chuyển đi. Địa chỉ của "Cửa khẩu" đó chính là **Default Gateway**.


* **Tại sao octet thứ 3 lại khác nhau (1 vs 2)?**
* Với Subnet Mask chuẩn `255.255.255.0`, **3 nhóm số đầu (Network ID)** quy định tên của "Làng/Mạng". Nhóm số thứ 4 (Host ID) quy định số nhà.
* Mạng bên trái tên là làng `192.168.1.x` $\rightarrow$ Tất cả thiết bị bên trái (PC0, cổng trái Router) bắt buộc phải mang đầu số `192.168.1.x`. Do đó Router trái là `1.1`, PC0 là `1.10`.
* Mạng bên phải tên là làng `192.168.2.x` $\rightarrow$ Tất cả thiết bị bên phải (PC1, cổng phải Router) bắt buộc mang đầu số `192.168.2.x`. Do đó Router phải là `2.1`, PC1 là `2.10`.
* Số `1` và `2` ở vị trí thứ 3 khác nhau chính là dấu hiệu cho máy tính biết: **"Đây là 2 mạng hoàn toàn khác nhau!"**



---

**6. Vì sao lần này chọn dây đồng thẳng (Straight-Through)?**

* **Nối PC $\leftrightarrow$ Switch:** PC và Switch là 2 thiết bị KHÁC loại $\rightarrow$ Dùng **Cáp thẳng**.
* **Nối Switch $\leftrightarrow$ Router:** Switch và Router cũng là 2 thiết bị KHÁC loại về chức năng bảng mạch kết nối $\rightarrow$ Tiếp tục dùng **Cáp thẳng**.
* *(Chỉ khi nào nối 2 thiết bị CÙNG loại như PC $\leftrightarrow$ PC, Switch $\leftrightarrow$ Switch, hoặc Router $\leftrightarrow$ Router thì mới dùng cáp chéo Crossover).*
