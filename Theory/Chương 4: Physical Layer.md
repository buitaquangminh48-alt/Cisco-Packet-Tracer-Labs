# Chương 4: Tầng Vật Lý (Physical Layer) 

## 1 Mục đích của Tầng Vật Lý (Purpose of the Physical Layer)
 * Kết nối vật lý: Để truyền thông mạng, thiết bị phải tạo kết nối vật lý (có dây hoặc không dây) thông qua Card mạng (NIC).
 * Chức năng: Chuyển đổi các bit từ khung dữ liệu (frame) của tầng Data Link thành các tín hiệu truyền qua phương tiện truyền dẫn cục bộ. Đây là bước cuối cùng trong quá trình đóng gói dữ liệu (encapsulation).

---

## 2 Đặc điểm của Tầng Vật Lý (Physical Layer Characteristics)
 * Tiêu chuẩn (Standards): Tầng TCP/IP được xử lý bằng phần mềm (do IETF quản lý), còn tầng Vật lý được thực thi bằng phần cứng và do các tổ chức như ISO, TIA/EIA, ITU-T, ANSI, IEEE quy định.
 * 3 lĩnh vực chức năng chính:
   * Thành phần vật lý (Physical Components): Thiết bị phần cứng, đầu nối, chất liệu dây cáp.
   * Mã hóa (Encoding): Chuyển chuỗi bit thành chuỗi định dạng dự đoán được (ví dụ: Manchester, 4B/5B, 8B/10B).
   * Tạo tín hiệu (Signaling): Cách biểu diễn bit 0 và 1 trên phương tiện truyền dẫn (điện áp trên cáp đồng, xung ánh sáng trên cáp quang, sóng vô tuyến trên không dây).
 * Khái niệm về Băng thông (Bandwidth):
   * Bandwidth: Tốc độ tối đa lý thuyết mà phương tiện truyền dẫn có thể vận chuyển bit/giây (bps, Kbps, Mbps, Gbps, Tbps).
   * Latency: Độ trễ, thời gian cần thiết để dữ liệu di chuyển từ điểm này đến điểm khác.
   * Throughput: Băng thông thực tế đo được trong một khoảng thời gian.
   * Goodput: Lượng dữ liệu hữu ích thực sự truyền đi được (\text{Goodput} = \text{Throughput} - \text{Overhead}).

---

## 3 Cáp Đồng (Copper Cabling)
 * Ưu/Nhược điểm: Chi phí thấp, dễ lắp đặt, điện trở thấp; nhưng bị suy hao tín hiệu theo khoảng cách (Attenuation) và dễ bị nhiễu điện từ (EMI), nhiễu tần số vô tuyến (RFI) và nhiễu chéo (Crosstalk).
 * Các loại cáp đồng chính:
   * UTP (Unshielded Twisted-Pair): Cáp xoắn đôi không bọc kim loại, phổ biến nhất.
   * STP (Shielded Twisted-Pair): Cáp xoắn đôi có lớp vỏ bọc chống nhiễu EMI/RFI tốt hơn nhưng đắt và khó lắp đặt hơn.
   * Coaxial (Cáp đồng trục): Gồm lõi đồng, lớp cách điện, lớp lưới kim loại bảo vệ và vỏ ngoài; dùng cho ăng-ten không dây hoặc mạng truyền hình cáp.

---

## 4 Cáp UTP (UTP Cabling)
 * Cơ chế chống nhiễu: Sử dụng các cặp dây xoắn để triệt tiêu nhiễu (Cancellation - dòng điện ngược cực) và thay đổi độ xoắn giữa các cặp dây.
 * Tiêu chuẩn & Chuẩn bấm dây:
   * Định chuẩn bởi TIA/EIA-568 (chuẩn Cat3, Cat5/5e, Cat6) và đầu nối RJ-45.
   * Straight-through (Cáp thẳng): Cả 2 đầu bấm cùng chuẩn T568A hoặc T568B (dùng nối Host với Switch/Hub).
   * Crossover (Cáp chéo): 1 đầu T568A và 1 đầu T568B (dùng nối Host-Host, Switch-Switch, Router-Router — hiện nay ít dùng nhờ tính năng Auto-MDIX).
   * Rollover: Cáp độc quyền của Cisco để nối cổng Serial của PC vào cổng Console của Router/Switch.

---

## 5 Cáp Quang (Fiber-Optic Cabling)
 * Đặc điểm: Truyền dữ liệu bằng xung ánh sáng thông qua sợi thủy tinh tinh khiết; khoảng cách xa hơn, băng thông lớn hơn cáp đồng, hoàn toàn không bị nhiễu EMI/RFI.
 * Phân loại:
   * Single-Mode Fiber (SMF): Lõi nhỏ (9 microns), dùng tia Laser, truyền xa, chi phí thiết bị cao.
   * Multimode Fiber (MMF): Lõi lớn hơn (50/62.5 microns), dùng đèn LED truyền nhiều góc ánh sáng, tối đa 10 Gbps trong khoảng cách 550m.
 * Đầu nối cáp quang phổ biến: ST, SC, LC simplex, Duplex MMF LC.

---

## 6 Truyền Dẫn Không Dây (Wireless Media)
 * Đặc điểm: Truyền tín hiệu nhị phân qua sóng vô tuyến hoặc vi sóng, linh hoạt cao.
 * Hạn chế: Tầm phủ sóng bị ảnh hưởng bởi vật cản, dễ nhiễu sóng, rủi ro bảo mật và chia sẻ băng thông (Half-duplex).
 * Các chuẩn kết nối:
   * Wi-Fi (IEEE 802.11): Mạng LAN không dây (WLAN).
   * Bluetooth (IEEE 802.15): Mạng cá nhân (WPAN).
   * WiMAX (IEEE 802.16): Mạng băng rộng không dây.
   * Zigbee (IEEE 802.15.4): Tiêu thụ năng lượng thấp, dành cho thiết bị IoT.
 * Thiết bị WLAN cơ bản: Wireless Access Point (AP) và Card mạng không dây (Wireless NIC).
