---
title: UDS
nav_order: 3
has_children: true
---

# Unified Diagnostic Services (UDS)

---

## UDS là gì?

**Unified Diagnostic Services (UDS)** là một giao thức truyền thông (communication protocol) được sử dụng trong các ECU của ô tô nhằm:
- Chẩn đoán lỗi.
- Cập nhật firmware.
- Thực hiện các kiểm nghiệm định kỳ (routine test).

**Giao thức UDS (ISO 14229)** được chuẩn hóa và có thể hoạt động trên nhiều tiêu chuẩn truyền thông khác nhau (như CAN, KWP2000, Ethernet, LIN). Hiện nay, UDS được sử dụng rộng rãi trong ECU của tất cả các OEM Tier 1.

UDS đôi khi còn được gọi là “_vehicle manufacturer enhanced diagnostics_” hoặc “_enhanced diagnostics_”.

---

## Các dịch vụ mà UDS hỗ trợ

- Đọc/xóa mã lỗi chẩn đoán (Diagnostic Trouble Code - DTC) để xác định và xử lý sự cố.
- Truy xuất dữ liệu tham số (như nhiệt độ, trạng thái pin, số VIN,...).
- Khởi tạo các diagnostic session để kiểm tra các chức năng quan trọng liên quan đến an toàn.
- Thay đổi hành vi ECU thông qua reset, nạp firmware (flashing) hoặc chỉnh sửa cấu hình.

---

## Mô hình client–server

Trong thực tế, truyền thông UDS được thực hiện theo mô hình **client–server**:
- **Client**: thiết bị chẩn đoán (tester tool).
- **Server**: ECU trên xe.

Ví dụ: bạn có thể kết nối một thiết bị giao tiếp CAN vào cổng OBD-II của xe và gửi các yêu cầu UDS vào hệ thống. Nếu ECU mục tiêu hỗ trợ các dịch vụ UDS, nó sẽ phản hồi tương ứng.

<figure class="figure-grid">
  <div class="image-row">
    <img
      src="\assets\images\OBDII_port_on_vehicle.png"
      alt="Vị trí cổng OBD-II trên xe"
    />
    <img
      src="\assets\images\OBDII_Scaner_BOSCH.png"
      alt="Máy quét OBD-II BOSH"
    />
  </div>
  <figcaption>Vị trí cổng OBD-II trên xe; Máy quét OBD-II BOSH</figcaption>
</figure>

---

## UDS không có đầu nối tiêu chuẩn

UDS không quy định một đầu nối tiêu chuẩn để kết nối các thiết bị chẩn đoán bên ngoài; không giống như các giao thức CAN tầng cao khác như OBD2, J1939 và ISOBUS.

Tuy nhiên, trong thực tế, đầu nối OBD-II (SAE J1962) vẫn được sử dụng trên phần lớn các phương tiện. Ví dụ, các dòng xe điện của Nissan, Hyundai và Volkswagen (VW) chỉ hỗ trợ giao tiếp OBD2 ở mức hạn chế hoặc thậm chí không hỗ trợ, nhưng vẫn phản hồi các yêu cầu UDS thông qua cổng OBD-II.

<figure>
  <img
    src="\assets\images\ODBII_connector_pinout_socket.svg"
    alt="Sơ đồ chân cắm đầu nối OBD-II"
  />
  <figcaption>Sơ đồ chân cắm đầu nối OBD-II</figcaption>
</figure>

---

## UDS và CAN bus: Tiêu chuẩn và Mô hình OSI

**Controller Area Network (CAN)** đóng vai trò nền tảng cho việc giao tiếp; nằm ở  **Data-link Layer** và **Physical Layer** trong mô hình OSI (theo ISO 11898).

**UDS (ISO 14229)** là một higher-layer protocol  nằm ở **Session Layer** và **Application Layer** trong mô hình OSI.


<figure>
  <img
    src="\assets\images\UDS-Unified-Diagnostic-Services-OSI-Model-7-Layer.svg"
    alt="Mô hình OSI và UDS"
  />
  <figcaption>Mô hình OSI và UDS</figcaption>
</figure>

---

# Các khái niệm trong bài viết

- Open Systems Interconnection model (OSI model)
- Communication protocol
- OEM (Original Equipment Manufacturer)
- ISO 14229
- ISO 11898
- CAN
- KWP2000
- Ethernet
- LIN
- OBD-II / ODB2
- SAE J1962
- J1939
- ISOBUS
- Diagnostic Trouble Code - DTC

---
