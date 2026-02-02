---
title: On-board & Off-board Diagnostic
parent: DCM
nav_order: 1
---

# On-board & Off-board Diagnostic

---

## On-board Diagnostic (OBD)

{: .note }
**On-board Diagnostic (OBD)** là chẩn đoán tự động trên xe (on-board), không cần công cụ bên ngoài. ECU tự giám sát và lưu lỗi (DTC), dữ liệu thời gian thực (freeze frame), chủ yếu cho khí thải và an toàn (theo quy định như OBD-II).

On-board Diagnostic được thực hiện khi xe đang di chuyển (trên đường), tập trung chủ yếu vào kiểm soát khí thải để tuân thủ quy định môi trường (như CARB ở Mỹ, EURO ở Châu Âu).

Nó giám sát thời gian thực các thông số liên quan đến khí thải (ví dụ: cảm biến oxy, kim phun nhiên liệu).
Nếu phát hiện lỗi, hệ thống kích hoạt đèn báo sự cố (Malfunction Indicator Lamp - MIL) trên bảng điều khiển để cảnh báo tài xế ngay lập tức. Lỗi được lưu dưới dạng DTC trong ECU, có thể truy xuất sau bằng công cụ kiểm tra.

Bắt buộc ở Mỹ từ năm 1996 cho tất cả ô tô, nhằm kiểm soát mức phát thải.

Các giao thức chính:
- **OBD**: là hệ thống chẩn đoán onboard cơ bản (Generation 1, từ 1988), định nghĩa bởi SAE J1930/ISO 15031, tập trung khí thải. ECU tự giám sát cảm biến/actuator, lưu DTC, và hỗ trợ đọc cơ bản qua cổng OBD.
- **OBD2 (OBD-II - SAE J1979/ISO 15031-5)**: Phiên bản nâng cao của OBD (từ 1996), bắt buộc ở Mỹ/EU, với 10 modes (dịch vụ $01-$0A) như $01 (Current Powertrain Data), $02 (Freeze Frame), $03 (Stored DTCs), $04 (Clear DTCs), $09 (Vehicle Info). OBD2 chuẩn hóa cổng kết nối (16-pin) và hỗ trợ nhiều mạng (CAN từ 2008).

---

## Off-board Diagnostic

{: .note }
**Off-board Diagnostic** là chẩn đoán ngoài xe (off-board), sử dụng công cụ bên ngoài (tester tool) kết nối với ECU qua OBD-II port hoặc CAN để đọc sâu dữ liệu, reprogramming, hoặc calibrate.

Off-board Diagnostic được thực hiện khi xe đứng yên (tại gara), xử lý tất cả các chẩn đoán (ngoài trừ khí thải) và các chức năng khác của ECU.

Lỗi được lưu dưới dạng DTC trong bộ nhớ EEPROM của ECU, nhưng không hiển thị tức thời qua đèn MIL. Thay vào đó, cần công cụ kiểm tra chuyên dụng để truy xuất và khắc phục.

Ví dụ: Kỹ thuật viên dùng tester kết nối với xe để đọc DTC chi tiết, xóa lỗi, hoặc cập nhật firmware. Dcm xử lý yêu cầu từ tester (UDS services), kiểm tra session/security trước khi thực thi.

Các giao thức chính:
- **Unified Diagnostic Services (UDS) - ISO 14229**: là giao thức chính cho chẩn đoán sâu, reprogramming, và bảo mật. UDS linh hoạt, hỗ trợ nhiều mạng (CAN, LIN, FlexRay, Ethernet).
- **Keyword Protocol (KWP/KWP2000) - ISO 14230**: là giao thức chẩn đoán cũ hơn UDS, dùng "keyword" (cặp byte đầu tiên) để xác định mode và service.
KWP đơn giản, tiết kiệm tài nguyên cho ECU thấp cấp; phù hợp với dòng xe cũ (trước 2008), đặc biệt trên mạng K-Line (ISO 9141) hoặc CAN.
KWP ít linh hoạt hơn UDS, nhưng vẫn dùng trong một số OEM (như Volkswagen, Audi) cho khả năng tương thích ngược (backward compatibility -  hoạt động ổn định với các phiên bản cũ hơn).

---

## So sánh

<table class="hover-table">
  <thead>
    <tr>
      <th></th>
      <th>On-board Diagnostic</th>
      <th>Off-board Diagnostic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Trạng thái xe</td>
      <td>Di chuyển (trên đường)</td>
      <td>Dừng (gara)</td>
    </tr>
    <tr>
      <td>Phạm vi</td>
      <td>Tập trung khí thải, ECU động cơ và hộp số (hệ thống truyền động).</td>
      <td>Lưu lỗi, hỗ trợ lập trình lại ECU, kích hoạt quy trình từ xa, ghi dữ liệu, cập nhật phần mềm, và chạy các bài kiểm tra chuyên sâu.</td>
    </tr>
    <tr>
      <td>Báo cáo lỗi</td>
      <td>Hiển thị ngay bằng MIL (đèn báo sự cố). Lỗi cũng được lưu để truy xuất sau (ở gara).</td>
      <td>Lưu lỗi để truy xuất sau (ở gara).</td>
    </tr>
    <tr>
      <td>Giao thức chuẩn đoán</td>
      <td>OBD/ODB2</td>
      <td>UDS, KWP,...</td>
    </tr>
  </tbody>
</table>


<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\Onboard_Offboard_Diagnostic.png"
    alt="On-board Diagnostic và Off-board Diagnostic"
  />
  <figcaption>On-board Diagnostic và Off-board Diagnostic</figcaption>
</figure>