---
title: Unified Diagnostic Services (UDS)
parent: Diagnostic Stack
nav_order: 1
has_children: true
---

# Unified Diagnostic Services (UDS)

<!-- <strong>Mục lục</strong>
1. TOC
{:toc} -->

---

## UDS là gì?

{: .note}
>**Unified Diagnostic Services (UDS)** là một giao thức truyền thông (communication protocol) được sử dụng trong các ECU của ô tô nhằm:
>- Chẩn đoán lỗi.
>- Cập nhật firmware.
>- Thực hiện các kiểm nghiệm định kỳ (routine test).

**Giao thức UDS (ISO 14229)** được chuẩn hóa và có thể hoạt động trên nhiều tiêu chuẩn truyền thông khác nhau (như CAN, KWP2000, Ethernet, LIN). Hiện nay, UDS được sử dụng rộng rãi trong ECU của tất cả các OEM Tier 1.

---

## Cấu trúc logic của một UDS request message

Ở mức UDS layer, một UDS request có dạng: **[SID] [Sub-function] [Data parameter]**

<table class="hover-table">
  <thead>
    <tr>
      <th></th>
      <th>SID</th>
      <th>Sub-function</th>
      <th>Data parameter</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Kích thước</strong></td>
      <td>1 byte</td>
      <td>1 byte (optional)</td>
      <td>0…N byte</td>
    </tr>
    <tr>
      <td><strong>Mục đích</strong></td>
      <td>Xác định dịch vụ chẩn đoán mà tester yêu cầu ECU thực hiện</td>
      <td>Xác định chế độ hoạt động (hoặc thông tin) cụ thể cho dịch vụ chuẩn đoán</td>
      <td>Dữ liệu cụ thể của service</td>
    </tr>
    <tr>
      <td><strong>Đặc điểm</strong></td>
      <td>Byte đầu tiên</td>
      <td>Nằm ngay sau SID (nếu có);<br>
          Bit 7 (MSB) thường dùng làm SPRMIB;<br>
          7 bit còn lại định nghĩa tối đa 128 giá trị sub-function
      </td>
      <td>Độ dài thay đổi tuỳ vào dữ liệu</td>
    </tr>
  </tbody>
</table>

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

Ví dụ: bạn có thể kết nối một thiết bị giao tiếp CAN vào cổng OBD-II (ODB2) của xe và gửi các yêu cầu UDS vào hệ thống. Nếu ECU mục tiêu hỗ trợ các dịch vụ UDS, nó sẽ phản hồi tương ứng.

<figure class="figure-grid">
  <div class="image-row">
    <img
      src="{{ site.baseurl }}\assets\images\OBDII_port_on_vehicle.png"
      alt="Vị trí cổng OBD-II trên xe"
    />
    <img
      src="{{ site.baseurl }}\assets\images\OBDII_Scaner_BOSCH.png"
      alt="Máy quét OBD-II BOSH"
    />
  </div>
  <figcaption>Vị trí cổng OBD-II trên xe; Máy quét OBD-II BOSH</figcaption>
</figure>

---

## UDS không có đầu nối tiêu chuẩn

UDS không quy định một đầu nối tiêu chuẩn để kết nối các thiết bị chẩn đoán bên ngoài; không giống như các giao thức CAN tầng cao khác như OBD-II, J1939 và ISOBUS.

Tuy nhiên, trong thực tế, đầu nối OBD-II (SAE J1962) vẫn được sử dụng trên phần lớn các phương tiện. Ví dụ, các dòng xe điện của Nissan, Hyundai và Volkswagen (VW) chỉ hỗ trợ giao tiếp OBD-II ở mức hạn chế hoặc thậm chí không hỗ trợ, nhưng vẫn phản hồi các yêu cầu UDS thông qua cổng OBD-II.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\ODBII_connector_pinout_socket.svg"
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
    src="{{ site.baseurl }}\assets\images\UDS-Unified-Diagnostic-Services-OSI-Model-7-Layer.svg"
    alt="Mô hình OSI và UDS"
  />
  <figcaption>Mô hình OSI và UDS</figcaption>
</figure>

---

## Bảo mật UDS thông qua session control (xác thực)

UDS cho phép kiểm soát rộng rãi đối với các ECU của xe.
Vì lý do bảo mật, các dịch vụ UDS quan trọng sẽ bị hạn chế thông qua quy trình xác thực.
1. Tester tool (client) gửi một yêu cầu đến ECU (server) để yêu cầu 'seed'.
1. ECU phản hồi bằng cách gửi một 'seed' đến tester tool.
1. Tester tool sẽ tạo một 'key' dựa trên 'seed' (bằng thuật toán bí mật) và gửi 'key' này đến ECU.
1. Nếu 'key' đúng, ECU sẽ phản hồi rằng 'key' hợp lệ và mở khoá security-level tương ứng.
1. Mỗi security-level cấp quyền truy cập đến một số dịch vụ UDS quan trọng.
1. Để duy trì quyền truy cập này, tester tool cần gửi thông báo 'tester present' định kỳ đến ECU.



Trong thực tế, quy trình xác thực này cho phép các OEM đảm bảo ngăn chặn truy cập trái phép từ hacker hoặc công cụ không chính hãng.

---
<!-- 
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
- Diagnostic Trouble Code - DTC -->

<!--
https://avtoad.com.ua/en/base/uds-protocol-unified-diagnostic-services 
[UDS Explained - A Simple Intro (Unified Diagnostic Services)](https://www.csselectronics.com/pages/uds-protocol-tutorial-unified-diagnostic-services)
-->

---
