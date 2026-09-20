# Chương 5: Các Hệ Thống Số (Number Systems)

## 1 Hệ Thống Số Nhị Phân (Binary Number System)
 * Khái niệm cơ bản:
   * Hệ nhị phân (Binary - Cơ số 2) chỉ gồm 2 chữ số: 0 và 1 (mỗi chữ số gọi là một bit).
   * Hệ thập phân (Decimal - Cơ số 10) gồm các chữ số từ 0 đến 9.
 * Ứng dụng trong mạng (IPv4):
   * Máy tính, router và thiết bị mạng sử dụng địa chỉ nhị phân để nhận diện nhau.
   * Địa chỉ IPv4 gồm 32 bits, chia làm 4 phần (mỗi phần 8 bits gọi là một octet hoặc byte) ngăn cách bằng dấu chấm. Để con người dễ đọc, chuỗi nhị phân này được chuyển sang dạng thập phân phân cách bằng dấu chấm (dotted decimal).
 * Ký hiệu vị trí (Positional Notation) & Trọng số:
   * Giá trị mỗi bit phụ thuộc vào vị trí của nó (từ phải sang trái, tương ứng 2^0 đến 2^7):
| Trọng số (2^n) | 2^7 | 2^6 | 2^5 | 2^4 | 2^3 | 2^2 | 2^1 | 2^0 |
|---|---|---|---|---|---|---|---|---|
| Giá trị thập phân | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
 * Chuyển đổi Nhị phân \rightarrow Thập phân:
   * Cộng giá trị trọng số của tất cả các vị trí có bit 1.
   * Ví dụ: 11000000 = 128 + 64 + 0 + 0 + 0 + 0 + 0 + 0 = 192.
 * Chuyển đổi Thập phân \rightarrow Nhị phân:
   * So sánh số thập phân n với trọng số từ trái qua phải (bắt đầu từ 128):
     * Nếu n \ge \text{trọng số}: Ghi bit 1, lấy n trừ cho trọng số đó.
     * Nếu n < \text{trọng số}: Ghi bit 0.
   * Ví dụ: Chuyển 168 sang nhị phân \rightarrow 168 \ge 128 (bật 1, còn 40) \rightarrow 40 < 64 (bật 0) \rightarrow 40 \ge 32 (bật 1, còn 8) \rightarrow 8 < 16 (bật 0) \rightarrow 8 \ge 8 (bật 1, còn 0) \rightarrow Các bit còn lại là 0 \rightarrow Kết quả: 10101000.

---

## 2 Hệ Thống Số Thập Lục Phân (Hexadecimal Number System)
 * Khái niệm cơ bản:
   * Hệ thập lục phân (Hexadecimal - Cơ số 16) gồm 16 ký tự: 0–9 và A–F (với A=10, B=11, C=12, D=13, E=14, F=15).
   * Một ký tự Hex đại diện cho chuỗi 4 bits nhị phân.
 * Ứng dụng: Dùng để biểu diễn địa chỉ IPv6 và địa chỉ MAC.
 * Địa chỉ IPv6:
   * Độ dài 128 bits, tương ứng với 32 ký tự Hex.
   * Chia thành 8 nhóm, mỗi nhóm 4 ký tự Hex (mỗi nhóm gọi là một hextet = 16 bits) ngăn cách bởi dấu hai chấm (:).

---

## 3 Bảng Đối Chiếu & Quy Tắc Chuyển Đổi
Bảng đối chiếu Thập phân - Nhị phân - Hex:
| Thập phân (Decimal) | Nhị phân (Binary - 4 bits) | Thập lục phân (Hex) |
|---|---|---|
| 0 | 0000 | 0 |
| 1 | 0001 | 1 |
| 2 | 0010 | 2 |
| 3 | 0011 | 3 |
| 4 | 0100 | 4 |
| 5 | 0101 | 5 |
| 6 | 0110 | 6 |
| 7 | 0111 | 7 |
| 8 | 1000 | 8 |
| 9 | 1001 | 9 |
| 10 | 1010 | A |
| 11 | 1011 | B |
| 12 | 1100 | C |
| 13 | 1101 | D |
| 14 | 1110 | E |
| 15 | 1111 | F |
Quy tắc chuyển đổi qua lại giữa Thập phân & Hex:
 * Thập phân \rightarrow Hex:
   * Bước 1: Đổi số thập phân sang chuỗi nhị phân 8-bit.
   * Bước 2: Tách chuỗi nhị phân thành các nhóm 4-bit (từ phải sang trái).
   * Bước 3: Đổi từng nhóm 4-bit sang ký tự Hex tương ứng.
   * Ví dụ: 168 \rightarrow \text{Nhị phân: } 10101000 \rightarrow \text{Tách: } 1010 \ \text{và} \ 1000 \rightarrow \text{Hex: } \text{A8}.
 * Hex \rightarrow Thập phân:
   * Bước 1: Đổi từng ký tự Hex thành nhóm 4-bit nhị phân.
   * Bước 2: Ghép lại thành chuỗi nhị phân 8-bit.
   * Bước 3: Đổi chuỗi nhị phân 8-bit sang số thập phân.
   * Ví dụ: \text{D2} \rightarrow \text{D}=1101, 2=0010 \rightarrow \text{Chuỗi: } 11010010 \rightarrow 128 + 64 + 16 + 2 = 210.

