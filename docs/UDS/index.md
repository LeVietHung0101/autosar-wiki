---
title: UDS
nav_order: 4
has_children: true
---

# Unified Diagnostic Services (UDS)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

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
1. Tester tool sẽ tạo một 'key' dựa trên 'seed' (bằng thuật toán bảo mật) và gửi 'key' này đến ECU.
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




<!-- 
<table class="hover-table">
  <thead>
    <tr>
      <th>Khái niệm</th>
      <th>Định nghĩa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>client</td>
      <td>
        Chức năng thuộc <b>tester</b>, sử dụng các <b>diagnostic service</b>.
        <br>Một tester còn sử dụng các chức năng khác như quản lý cơ sở dữ liệu, diễn giải dữ liệu chuyên biệt và giao diện người-máy (human-machine interface - HMI).
      </td>
    </tr>
    <tr>
      <td>tester</td>
      <td>
        Hệ thống điều khiển các chức năng như kiểm thử (<b>testing</b>), kiểm tra (<b>inspection</b>), giám sát (<b>monitoring</b>) hoặc chẩn đoán (<b>diagnosis</b>) đối với <b>ECU</b> trên xe.
        <br>Tester có thể được thiết kế cho từng loại người sử dụng, ví dụ:
        <ul>
          <li><b>Off-board scan tool</b> dành cho kỹ thuật viên sửa chữa.</li>
          <li><b>Off-board test tool</b> dành cho nhà máy lắp ráp.</li>
          <li><b>On-board tester</b> tích hợp trên xe.</li>
        </ul>
        Tester cũng được gọi là <b>client</b>.
      </td>
    </tr>
    <tr>
      <td>server</td>
      <td>
        Chức năng nằm trong một <b>ECU</b>, cung cấp các <b>diagnostic service</b>.
        <br><b>ISO 14229</b> phân biệt giữa <b>server</b> (chức năng) và <b>ECU</b> (thiết bị) nhằm đảm bảo tính độc lập với cách triển khai.
      </td>
    </tr>
    <tr>
      <td>electronic control unit (ECU)</td>
      <td>
        Bộ điều khiển điện tử cung cấp thông tin liên quan đến các cảm biến và mạng điều khiển được kết nối.
        <br>Các hệ thống như <b>ABS (Anti-lock Braking System)</b> và <b>Engine Management System</b> được xem là ECU.
      </td>
    </tr>
    <tr>
      <td>diagnostic service</td>
      <td>
        Quá trình trao đổi thông tin do <b>client</b> khởi tạo nhằm yêu cầu thông tin chẩn đoán từ <b>server</b> và/hoặc thay đổi hành vi của server phục vụ mục đích chẩn đoán.
      </td>
    </tr>
    <tr>
      <td>diagnostic session</td>
      <td>
        Trạng thái của <b>server</b> trong đó một tập hợp xác định các <b>diagnostic service</b> và chức năng được kích hoạt.
      </td>
    </tr>
    <tr>
      <td>diagnostic trouble code (DTC)</td>
      <td>
        Mã số chuẩn dùng để định danh một tình trạng lỗi được hệ thống chẩn đoán trên xe (<b>On-board Diagnostic System - OBD</b>) phát hiện.
      </td>
    </tr>
    <tr>
      <td>security</td>
      <td>
        Cơ chế bảo vệ các module trên xe khỏi sự truy cập trái phép (<b>unauthorized intrusion</b>) thông qua liên kết truyền dữ liệu chẩn đoán (<b>vehicle diagnostic data link</b>).
      </td>
    </tr>
    <tr>
      <td>boot memory partition</td>
      <td>
        Vùng bộ nhớ của <b>server</b> chứa <b>boot software</b>.
      </td>
    </tr>
    <tr>
      <td>boot software</td>
      <td>
        Phần mềm được thực thi trong một vùng bộ nhớ đặc biệt của <b>server</b>, chủ yếu dùng để khởi động <b>ECU</b> và thực hiện quá trình lập trình (<b>programming</b>) cho server.
        <br>Vùng bộ nhớ này không bị xóa trong quá trình <b>programming</b> thông thường và sẽ được thực thi khi <b>server application</b> bị thiếu hoặc được xác định là không hợp lệ, nhằm luôn đảm bảo khả năng <b>reprogramming</b> cho server.
      </td>
    </tr>
    <tr>
      <td>diagnostic channel</td>
      <td>
        Đường truyền chuyên dụng từ <b>client</b> đến <b>server</b> dành cho giao tiếp chẩn đoán.
        <br>Nhiều client có thể đồng thời kết nối đến cùng một server và được phân biệt bằng <b>tester source address</b> riêng.
      </td>
    </tr>
    <tr>
      <td>diagnostic data</td>
      <td>
        Dữ liệu nằm trong bộ nhớ của <b>ECU</b>, có thể được <b>tester</b> đọc và/hoặc sửa đổi.
        <br>Diagnostic data bao gồm các tín hiệu đầu vào/đầu ra analog/digital, các giá trị trung gian và nhiều thông tin trạng thái khác.
        <br>Ví dụ: tốc độ xe, góc bướm ga (throttle angle), vị trí gương, trạng thái hệ thống,...
        <br>Có ba loại giá trị của diagnostic data:
        <ul>
          <li><b>Current value</b>: giá trị hiện tại đang được ECU sử dụng hoặc tạo ra trong quá trình hoạt động bình thường.</li>
          <li><b>Stored value</b>: bản sao nội bộ của current value được lưu tại những thời điểm xác định (ví dụ khi xảy ra lỗi hoặc theo chu kỳ), do ECU quản lý.</li>
          <li><b>Static value</b>: giá trị cố định, ví dụ như <b>VIN</b>.</li>
        </ul>
        Server không bắt buộc phải lưu bản sao nội bộ của dữ liệu cho mục đích chẩn đoán; trong trường hợp đó tester chỉ có thể yêu cầu current value.
        <br>Việc chọn <b>repair shop session</b> hoặc <b>development testing session</b> sẽ kích hoạt các chức năng khác nhau của server (ví dụ chỉ <b>development testing session</b> mới được phép truy cập toàn bộ vùng nhớ).
      </td>
    </tr>
    <tr>
      <td>functional unit</td>
      <td>
        Tập hợp các <b>diagnostic service</b> có chức năng tương đồng hoặc bổ sung cho nhau.
      </td>
    </tr>
    <tr>
      <td>local server</td>
      <td>
        Server được kết nối vào cùng mạng cục bộ với <b>client</b> và thuộc cùng không gian địa chỉ (<b>address space</b>) với client.
      </td>
    </tr>
    <tr>
      <td>permanent DTC</td>
      <td>
        <b>DTC</b> vẫn được lưu trong <b>non-volatile memory (NVM)</b> ngay cả sau khi nhận yêu cầu <b>ClearDTC</b>, cho đến khi các điều kiện khác (thường là yêu cầu pháp lý) được đáp ứng, ví dụ tất cả các monitor tương ứng với DTC đó đã chạy thành công.
        <br>Tham khảo các quy định pháp luật liên quan để biết đầy đủ các yêu cầu.
      </td>
    </tr>
    <tr>
      <td>record</td>
      <td>
        Một hoặc nhiều phần tử <b>diagnostic data</b> được nhóm lại và được nhận diện bằng một định danh duy nhất.
        <br>Một <b>snapshot</b> chứa nhiều dữ liệu đầu vào/đầu ra cùng các <b>DTC</b> là một ví dụ của record.
      </td>
    </tr>
    <tr>
      <td>remote server</td>
      <td>
        Server không kết nối trực tiếp với mạng chẩn đoán chính.
        <br>Remote server được nhận diện bằng <b>remote address</b>, thuộc một không gian địa chỉ độc lập với mạng chính.
        <br>Remote server được truy cập thông qua một <b>local server</b> trên mạng chính. Mỗi local server có thể đóng vai trò là <b>gateway</b> đến một nhóm remote server độc lập.
        <br>Do đó, remote server luôn được xác định bằng hai địa chỉ: một <b>local address</b> xác định gateway và một <b>remote address</b> xác định remote server.
      </td>
    </tr>
    <tr>
      <td>remote client</td>
      <td>
        Client không kết nối trực tiếp với mạng chẩn đoán chính.
        <br>Remote client được nhận diện bằng <b>remote address</b>, thuộc một không gian địa chỉ độc lập với mạng chính.
      </td>
    </tr>
    <tr>
      <td>reprogramming software</td>
      <td>
        Thành phần của <b>boot software</b> cho phép thực hiện <b>reprogramming</b> đối với <b>electronic control unit (ECU)</b>.
      </td>
    </tr>
    <tr>
      <td>supported DTC</td>
      <td>
        <b>DTC</b> hiện đang được cấu hình (<b>configured</b>), hiệu chỉnh (<b>calibrated</b>) và được kích hoạt để hoạt động dưới các điều kiện vận hành xác định của xe.
      </td>
    </tr>
  </tbody>
</table> -->


---
