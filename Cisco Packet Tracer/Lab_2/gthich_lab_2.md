===================================================Đề bài lab 2===========================================================================================

Tự luyện mà chủ động vắt óc ra hỏi bản chất như lúc nãy là siêu chuẩn luôn!

Vậy bài Lab 2 này mình gợi ý một đề bài cực kỳ thực tế để nâng cấp kĩ năng cho bạn nhé.

**Tên bài Lab 2: Kết nối 2 phòng phòng ban qua Switch**

* **Mục tiêu:** Học cách dùng **Switch** để nối nhiều máy tính lại với nhau và dùng **cáp thẳng (Straight-Through)**.
* **Sơ đồ thiết bị:**
* **1 Switch** (chọn loại `2960`).
* **4 PC** (đặt tên lần lượt là PC0, PC1, PC2, PC3).



**Nhiệm vụ dành cho bạn:**

1. **1. Lấy thiết bị:** 1 Switch, 4 PC.
Kéo 1 Switch 2960 và 4 máy PC ra màn hình làm việc.


2. **2. Cắm dây cáp:** Copper Straight-Through.
Nối tất cả 4 PC vào các cổng FastEthernet (`Fa0/1`, `Fa0/2`, `Fa0/3`, `Fa0/4`) của Switch.
*(Lưu ý: PC nối với Switch là 2 thiết bị KHÁC loại, thử đoán xem dùng cáp gì nhé!)*


3. **3. Cài địa chỉ IP:** Cùng dải 192.168.1.x.
* PC0: `192.168.1.10`
* PC1: `192.168.1.20`
* PC2: `192.168.1.30`
* PC3: `192.168.1.40`
*(Subnet Mask giữ nguyên `255.255.255.0`)*


4. **4. Test Ping:** Kiểm tra.
Vào PC0 ping sang PC3 (`192.168.1.40`) xem có `Reply` không.


Bạn xếp sơ đồ xong rồi chụp ảnh màn hình hoặc có thắc mắc gì trong lúc làm cứ quăng vào đây nhé!

===================================================Lý thuyết==============================================================================================

Giải thích tường tận từng thắc mắc của bạn đây!

**1. Tại sao dùng Switch 2960 và dây cáp thẳng (Straight-Through)?**

* **Tại sao dùng Switch 2960?** Vì PC chỉ có 1 cổng cắm mạng, nếu muốn nối từ 3 máy trở lên thành một mạng LAN thì bắt buộc phải qua một "bộ chia" tập trung, và Switch 2960 (dòng Switch Layer 2 phổ biến của Cisco) chính là thiết bị đóng vai trò đó.
* **Tại sao dùng cáp thẳng?**
* Theo chuẩn mạng, **PC và Switch là 2 loại thiết bị KHÁC nhau** về cấu trúc chân cắm bên trong.
* Cổng trên PC có chân 1-2 dùng để **Gửi (TX)**, chân 3-6 dùng để **Nhận (RX)**.
* Cổng trên Switch thì ngược lại: chân 1-2 là **Nhận (RX)**, chân 3-6 là **Gửi (TX)**.
* Vì thiết kế chân cắm đã ngược nhau sẵn rồi, nên ta chỉ cần dùng **cáp thẳng** (chân 1 nối 1, chân 2 nối 2...) là chân TX của PC sẽ cắm đúng vào chân RX của Switch $\rightarrow$ Truyền tin được ngay! *(Khác với Lab 1 là 2 PC cùng loại nên phải dùng cáp chéo để tự đảo chân).*



---

**2. FastEthernet0/1, 0/2 là gì? Có thể chọn cổng khác không?**

* **FastEthernet0/1, 0/2** chính là tên/mã số của từng ổ cắm (cổng/port) trên Switch.
* `FastEthernet`: Chuẩn tốc độ của cổng.
* `0/`: Số thứ tự của khay/module (Slot). Switch 2960 là dạng cố định nên khay luôn là `0`.
* `/1`, `/2`, `/3`...: Số thứ tự của chính ổ cắm đó trên mặt Switch (từ cổng 1 đến cổng 24).


* **Có thể chọn 0/khác không?** **Hoàn toàn ĐƯỢC!** Switch 2960 có 24 cổng FastEthernet (`Fa0/1` đến `Fa0/24`). Bạn cắm vào cổng `Fa0/5`, `Fa0/12` hay `Fa0/24` thì các máy vẫn ping thấy nhau bình thường.

---

**3. FastEthernet là gì?**

* "Ethernet" là công nghệ mạng LAN. Dựa vào tốc độ truyền dữ liệu tối đa, người ta chia thành các chuẩn:
* **Ethernet:** Tốc độ $10\text{ Mbps}$ (Chuẩn cổ).
* **FastEthernet:** Tốc độ $100\text{ Mbps}$ (Chuẩn khá phổ biến trong các bài lab).
* **GigabitEthernet:** Tốc độ $1\text{ Gbps} = 1000\text{ Mbps}$ (Chuẩn dùng phổ biến hiện nay).



---

**4. Vì sao trên PC lại chỉ có `FastEthernet0`?**

* Số `0` ở cuối chỉ là **số chỉ mục (Index)** bắt đầu đếm từ 0 của máy tính (Card mạng đầu tiên = ` FastEthernet0`).
* Thông thường, một máy tính cá nhân (PC/Laptop) chỉ được trang bị **1 card mạng LAN duy nhất** để cắm dây, nên nó chỉ hiện `FastEthernet0`.
* Nếu sau này bạn gắn thêm 1 card mạng rời nữa vào PC, máy sẽ xuất hiện thêm cổng thứ hai tên là `FastEthernet1`.





=====================================Lỗi physical=====================================

Lý do cực kỳ đơn giản: Bạn đang bị dính ở chế độ **Simulation (Mô phỏng)** đấy =))

Bạn nhìn xuống góc dưới bên phải màn hình (chỗ thanh công cụ màu xanh lá/màu xanh dương):

* Nút **Simulation** đang được chọn (nó bị dìm xuống/sáng lên).

**Tại sao ở chế độ Simulation lại bị chờ lâu?**

* Ở chế độ **Realtime** (Thời gian thực), gói tin chạy dưới dạng xung điện cực nhanh ($< 1\text{ ms}$) nên gõ `ping` là thấy `Reply` ngay lập tức.
* Ở chế độ **Simulation**, phần mềm sẽ **"bấm dừng thời gian"** để bạn soi từng gói tin. Gói tin `ping` sẽ đứng yên một chỗ chờ bạn bấm nút **Play** (hoặc nút **Forward** $\rightarrow$) thì nó mới chịu nhích đi từng bước một. Do đó terminal sẽ đứng im chờ phản hồi!

**Cách khắc phục:**

1. Chuyển lại sang chế độ **Realtime** bằng cách nhấp chuột vào ô **Realtime** (ngay bên trái ô Simulation, phím tắt `Alt + R`).
2. Mở lại terminal và gõ `ping 192.168.1.40`, bạn sẽ thấy kết quả `Reply` hiện ra tức thì như bên tab Logical!


======================================Phát hiện mới===================================


Trong chế độ Physical View, Packet Tracer thiết kế giao diện đồ họa tường ngăn cho giống văn phòng thực tế thôi, chứ thuật toán bên dưới vẫn coi đường dây đó là cáp mạng nối từ PC qua lỗ hổng/ông gen âm tường vào tủ Rack (Main Wiring Closet - nơi chứa Switch).

Vì trong mô phỏng dây mạng cắm từ PC vào Switch không bị cản bởi vật cản đồ họa, nên tín hiệu điện vẫn chạy xèo xèo xuyên tường bình thường!

Thực tế ngoài đời nếu kéo dây từ phòng này sang phòng khác thì thợ kỹ thuật cũng sẽ đục tường, đi dây âm tường hoặc đi trên trần thạch cao để nối vào Switch trong phòng server y hệt như vậy 