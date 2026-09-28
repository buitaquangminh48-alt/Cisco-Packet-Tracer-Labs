# Chương 6: Tầng Liên Kết Dữ Liệu (Data Link Layer)

## 6.1 Mục Đích của Tầng Liên Kết Dữ Liệu (Purpose of the Data Link Layer)

* **Vai trò:** Chịu trách nhiệm giao tiếp giữa các Card mạng (NIC) của các thiết bị cuối. Tầng này cho phép các giao thức lớp trên (như IP) truy cập vào phương tiện truyền dẫn vật lý và đóng gói gói tin Lớp 3 (IPv4/IPv6 packet) thành **Khung dữ liệu Lớp 2 (Layer 2 Frame)**. Ngoài ra, nó cũng thực hiện kiểm tra lỗi và loại bỏ các khung bị hỏng.


* **Hai phân tầng theo chuẩn IEEE 802 (LAN/MAN):**
1. **LLC (Logical Link Control - IEEE 802.2):** Làm cầu nối giao tiếp giữa phần mềm mạng ở các lớp trên và phần cứng thiết bị ở các lớp dưới.


2. **MAC (Media Access Control):** Chịu trách nhiệm đóng gói dữ liệu (data encapsulation) và điều khiển truy cập phương tiện truyền dẫn (media access control). Lớp MAC định nghĩa các chuẩn như Ethernet (IEEE 802.3), WLAN (IEEE 802.11), WPAN (IEEE 802.15).




* **Chuyển tiếp dữ liệu qua các Router:** Khi một gói tin đi qua nhiều chặng (hop) để tới đích, tại mỗi Router sẽ thực hiện 4 bước Lớp 2:


1. Nhận frame từ phương tiện truyền dẫn.


2. Mở gói (de-encapsulate) frame để lấy gói tin IP Lớp 3.


3. Đóng gói lại (re-encapsulate) gói tin IP vào một frame Lớp 2 mới phù hợp với chặng tiếp theo.


4. Chuyển tiếp frame mới lên phương tiện truyền dẫn của phân đoạn mạng kế tiếp.




* **Tổ chức tiêu chuẩn:** Định nghĩa bởi các tổ chức IEEE, ITU, ISO, ANSI.



---

## 6.2 Topo Mạng (Topologies)

* **Phân loại Topo:**
* **Physical Topology (Topo vật lý):** Hiển thị các kết nối vật lý và cách thiết bị được nối với nhau.


* **Logical Topology (Topo lô-gíc):** Định rõ các kết nối ảo giữa thiết bị bằng cách sử dụng giao diện (interface) và sơ đồ địa chỉ IP.




* **Topo mạng diện rộng (WAN Topologies):**
* **Point-to-Point (Điểm-tới-Điểm):** Kết nối trực tiếp giữa hai điểm cuối, đơn giản và phổ biến nhất.


* **Hub and Spoke:** Giống topo hình sao, trung tâm kết nối các chi nhánh qua các liên kết point-to-point.


* **Mesh (Lưới):** Độ sẵn sàng cao nhưng yêu cầu mọi hệ thống phải nối với tất cả hệ thống còn lại.




* **Topo mạng cục bộ (LAN Topologies):**
* **Star / Extended Star (Hình sao / Hình sao mở rộng):** Phổ biến nhất hiện nay, dễ lắp đặt, dễ mở rộng và xử lý lỗi.


* **Bus / Ring (Tuyến tính / Vòng):** Dùng trong các công nghệ Ethernet cũ hoặc Token Ring truyền thống.




* **Chế độ truyền dẫn (Duplexing):**
* **Half-duplex (Bán song công):** Tại một thời điểm chỉ có 1 thiết bị được gửi hoặc nhận (dùng trong Wi-Fi, Ethernet Hubs cũ).


* **Full-duplex (Toàn song công):** Cả 2 thiết bị có thể đồng thời truyền và nhận dữ liệu (dùng trên các Switch Ethernet hiện đại).




* **Phương pháp điều khiển truy cập đường truyền (Access Control Methods):**
* **Contention-based access (Cạnh tranh truy cập):** Các nút hoạt động ở chế độ half-duplex và cạnh tranh đường truyền:


* **CSMA/CD:** Dùng cho mạng Ethernet dạng Bus cũ. Nếu phát hiện xung đột (collision), các thiết bị dừng lại, chờ một khoảng thời gian ngẫu nhiên rồi truyền lại.


* **CSMA/CA:** Dùng cho mạng không dây (WLAN / Wi-Fi). Thiết bị thông báo khoảng thời gian cần truyền để các thiết bị khác biết và tránh gây xung đột.




* **Controlled access (Truy cập có kiểm soát):** Mỗi nút có khoảng thời gian truyền riêng (ví dụ: Token Ring).





---

## 6.3 Khung Dữ Liệu Tầng Liên Kết Dữ Liệu (Data Link Frame)

* **Cấu trúc của một Frame:**
* **Header (Phần đầu):**
* *Frame Start / Stop:* Dấu hiệu nhận biết điểm bắt đầu và kết thúc frame.


* *Addressing:* Địa chỉ Lớp 2 nguồn và đích (địa chỉ MAC).


* *Type:* Nhận diện giao thức Lớp 3 được đóng gói bên trong.


* *Control:* Thông tin điều khiển luồng.




* **Data (Dữ liệu):** Chứa tải trọng (payload) là gói tin Lớp 3.


* **Trailer (Phần đuôi):** Chứa trường **Error Detection** (Kiểm tra lỗi, thường dùng thuật toán CRC) để xác định xem frame có bị lỗi trong quá trình truyền hay không.




* **Đặc điểm Địa chỉ Lớp 2 (Địa chỉ vật lý / MAC):**
* Nằm trong phần Header của Frame.


* **Chỉ có giá trị trong phạm vi mạng cục bộ (link-local delivery)**.


* Khi frame đi qua mỗi Router, địa chỉ Lớp 2 nguồn và đích **sẽ thay đổi (được cập nhật)** tương ứng với từng phân đoạn mạng mới, trong khi địa chỉ IP Lớp 3 nguồn/đích ban đầu vẫn giữ nguyên.