---
title: SecurityAccess (0x27) service
parent: Service Identifier (SID)
permalink: /SecurityAccess_service/
nav_order: 3
---

# SecurityAccess (0x27) service

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Service description

{: .note }
Dịch vụ **SecurityAccess (0x27)** cung cấp cơ chế để truy cập dữ liệu hoặc dịch vụ chẩn đoán bị hạn chế do lý do bảo mật, khí thải hoặc an toàn.

Các dịch vụ chẩn đoán này bao gồm: downloading/uploading routine hoặc dữ liệu vào server, hoặc đọc memory ở vị trí cụ thể từ server. Việc downloading routine hoặc dữ liệu không đúng có thể gây hỏng phần cứng hoặc vi phạm tiêu chuẩn khí thải hoặc an toàn. 

Một số chức năng/dịch vụ chẩn đoán được yêu cầu trong một diagnostic session cụ thể có thể yêu cầu một chuỗi security access thành công (<i>ví dụ: Request Download (0x34), Request Upload (0x35), TransferData (0x36),...</i>). Trong trường hợp như vậy, chuỗi các dịch vụ sau phải được yêu cầu:
- DiagnosticSessionControl service
- SecurityAccess service service
- Secured diagnostic service.

---

### Quá trình thực hiện dịch vụ:

1. Client yêu cầu 'seed' từ server.
2. Server gửi 'seed'.
3. Client tính toán và gửi 'key' tương ứng.
4. Server kiểm tra 'key' (so sánh với một giá trị được lưu trữ/tính toán nội bộ):
    1. Nếu 'key' khớp, server mở khóa (unlock) quyền truy cập của client vào các dịch vụ/dữ liệu cụ thể.
    2. Nếu 'key' không khớp, server xem đây là một lần truy cập thất bại.

---

### Chỉ hỗ trợ một security level tại một thời điểm

Các security level được xác định bởi giá trị của sub-function.
Việc đánh số các security level là tùy ý và không ngụ ý bất kỳ mối quan hệ nào giữa chúng.

##### Yêu cầu security level KHÁC với security level đang hoạt động

Nếu security level liên quan đến requestSeed **0x03** đang hoạt động và yêu cầu từ tester thành công trong việc mở khóa security level liên quan đến requestSeed **0x01** thì:
- Bất kỳ chức năng bảo mật bổ sung nào trước đó đã được mở khóa bởi security level liên quan đến requestSeed **0x03** sẽ không còn hoạt động nữa.
- Chỉ các chức năng bảo mật được hỗ trợ bởi security level liên quan đến requestSeed **0x01** mới được mở khóa tại thời điểm đó.

##### Yêu cầu security level TRÙNG với security level đang hoạt động

Nếu client yêu cầu mở khoá một security level đã được mở khoá, server sẽ gửi positive response với 'seed' = 0x00.

Server không bao giờ được gửi một seed toàn bộ là 0x00 cho một security level đang bị khóa.
Do đó, client sẽ biết được một security level cụ thể có bị khoá hay không bằng cách so sánh 'seed' với 0x00.

---

### Cơ chế khi truy cập thất bại

Server có thể kích hoạt một thời gian delay (**Delay_Timer**) sau khi power-up/reset hoặc sau nhiều lần truy cập thất bại (**Att_Cnt**).
Delay_Timer và Att_Cnt (Attempts Counter) đều do OEM quy định.

Trong thời gian delay này, các dịch vụ SecurityAccess từ client sẽ bị server từ chối thông qua negative response (NRC 0x37); điều này sẽ không ảnh hưởng đến giao tiếp/dịch vụ chẩn đoán thông thường.

Nếu Delay_Timer được hỗ trợ bởi OEM, **Server sẽ kích hoạt Delay_Timer khi**:
1. Đạt đến số lần truy cập thất bại (**Att_Cnt**).
2. Server được power-up/reset và một dịch vụ SecurityAccess trước đó đã thất bại một lần duy nhất <i>hoặc</i> không thể xác định có thất bại hay không.
    - Nếu dịch vụ SecurityAccess tiếp theo thành công, thì thông tin chỉ báo nội bộ của server cho việc kích hoạt timer delay khi bật nguồn/reset phải được xóa bởi server.



<!-- Một thời gian delay cụ thể của nhà sản xuất xe có thể được yêu cầu trước khi server có thể phản hồi tích cực đối với thông điệp dịch vụ SecurityAccess 'requestSeed' từ client sau khi server bật nguồn/reset và sau một số lần truy cập sai nhất định (xem mô tả thêm bên dưới).

Nếu timer delay này được hỗ trợ, thì delay phải được kích hoạt sau khi đạt đến số lần truy cập sai được quy định bởi nhà sản xuất xe hoặc khi server được bật nguồn/reset và một dịch vụ SecurityAccess trước đó đã thất bại do một lần truy cập sai duy nhất.

Trong trường hợp server hỗ trợ timer delay này, thì sau khi thực hiện thành công dịch vụ SecurityAccess 'sendKey', thông tin chỉ báo nội bộ của server cho việc kích hoạt timer delay khi bật nguồn/reset phải được xóa bởi server. Trong trường hợp server hỗ trợ timer delay này và không thể xác định xem dịch vụ SecurityAccess trước đó trước khi bật nguồn/reset có thất bại hay không, thì timer delay phải luôn hoạt động sau khi bật nguồn/reset. Delay chỉ được yêu cầu nếu server bị khóa khi bật nguồn/reset. Nhà sản xuất xe phải chọn xem timer delay có được hỗ trợ hay không. -->

<!-- A vehicle manufacturer specific time delay may be required before the server can positively respond to a
service SecurityAccess ‘requestSeed’ message from the client after server power up/reset and after a certain
number of false access attempts (see further description below).

If this delay timer is supported then the delay shall be activated after a vehicle manufacturer specified number of false access attempts has been reached or when the server is powered up/reset and a previously performed SecurityAccess service has failed due to a single false access attempt.

In case the server supports this delay timer then after a successful
SecurityAccess service 'sendKey' execution the server internal indication information for a delay timer
invocation on a power up/reset shall be cleared by the server.

In case the server supports this delay timer and
cannot determine if a previously performed SecurityAccess service prior to the power up/reset has failed then
the delay timer shall always be active after power up/reset. The delay is only required if the server is locked
when powered up/reset. The vehicle manufacturer shall select if the delay timer is supported.  -->

<!-- Nếu server đã mở khóa security level, nó sẽ gửi seed = 0x00.  Truy cập bảo mật không ảnh hưởng đến giao tiếp thông thường.
Dịch vụ này thường dùng trong session chẩn đoán cụ thể, theo thứ tự: DiagnosticSessionControl → SecurityAccess → Dịch vụ bảo mật. -->


---

## Request message

Bảng sau mô tả các data-parameter dùng trong request message:

<table class="hover-table">
  <thead>
    <tr>
      <th>Data-parameter</th>
      <th>Mô tả</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>securityAccessDataRecord</strong></td>
      <td>Tuỳ chọn của người dùng để truyền dữ liệu đến server khi client yêu cầu 'seed'.<br>
          Ví dụ: Nó có thể chứa thông tin nhận dạng (identification) của client được xác minh trên server.</td>
    </tr>
    <tr>
      <td><strong>securityKey</strong></td>
      <td>Giá trị 'key' được tạo bởi thuật toán bảo mật tương ứng với một giá trị 'seed' cụ thể.</td>
    </tr>
  </tbody>
</table>

Các giá trị sub-function cũng dùng để xác định security level được kích hoạt (tại một thời điểm).
- **requestSeed**: luôn là số lẻ (0x01, 0x03, ..., 0x7D).
- **sendKey** = requestSeed + 1: luôn là số chẵn (0x02, 0x04,..., 0x7E).

#### sub-function = requestSeed

<table class="hover-table">
  <thead>
    <tr>
      <th>Data byte</th>
      <th>Parameter Name</th>
      <th>Byte Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>SID</td>
      <td>0x27</td>
    </tr>
    <tr>
      <td>2</td>
      <td>sub-function = [ securityAccessType = <strong>requestSeed</strong> ]</td>
      <td>0x01, 0x03, ..., 0x7D</td>
    </tr>
    <tr>
      <td>3..n</td>
      <td>securityAccessDataRecord[]</td>
      <td></td>
    </tr>
  </tbody>
</table>

#### sub-function = sendKey

<table class="hover-table">
  <thead>
    <tr>
      <th>Data byte</th>
      <th>Parameter Name</th>
      <th>Byte Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>SID</td>
      <td>0x27</td>
    </tr>
    <tr>
      <td>2</td>
      <td>sub-function = [ securityAccessType = <strong>sendKey</strong> ]</td>
      <td>0x02, 0x04,..., 0x7E</td>
    </tr>
    <tr>
      <td>3..n</td>
      <td>securityKey[]</td>
      <td></td>
    </tr>
  </tbody>
</table>

---

### Request message sub-function parameter $Level (LEV_) definition



#### sub-function


<table class="hover-table">
  <thead>
    <tr>
      <th>Bits 6 – 0</th>
      <th>sub-function</th>
      <th>Mô tả</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>ISOSAEReserved</td>
      <td></td>
    </tr>
    <tr>
      <td>0x01, 0x03 – 0x41</td>
      <td>requestSeed</td>
      <td>Yêu cầu 'seed' với mức độ bảo mật được định nghĩa bởi OEM.</td>
    </tr>
    <tr>
      <td>0x02, 0x04 – 0x42</td>
      <td>sendKey</td>
      <td>Gửi 'key' với mức độ bảo mật được định nghĩa bởi OEM.</td>
    </tr>
    <tr>
      <td>0x43 – 0x5E</td>
      <td>ISOSAEReserved</td>
      <td>Dành cho tương lai.</td>
    </tr>
    <tr>
      <td>0x5F</td>
      <td>ISO26021-2 values</td>
      <td></td>
    </tr>
    <tr>
      <td>0x60</td>
      <td>ISO26021-2 sendKey values</td>
      <td></td>
    </tr>
    <tr>
      <td>0x61 – 0x7E</td>
      <td>systemSupplierSpecific</td>
      <td>Dành cho System Supplier định nghĩa.</td>
    </tr>
    <tr>
      <td>0x7F</td>
      <td>ISOSAEReserved</td>
      <td>Dành cho tương lai.</td>
    </tr>
  </tbody>
</table>

---

## Positive response message

### Positive response message definition

### Positive response message data-parameter definition

---

## Supported negative response codes (NRC_)

---

## Tình huống ví dụ

---

## SecurityAccess state chart

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\SecurityAccess_State_Chart.png"
    alt="SecurityAccess State Chart"
  />
  <figcaption>SecurityAccess State Chart</figcaption>
</figure>

Các trạng thái (state) chính:
- **(A)**: Tất cả các security level bị khóa. Không có seed hoạt động.
- **(B)**: Tất cả các security level bị khóa. Seed đã gửi. Đang chờ key.
- **(C)**: Một security level được mở khóa. Không có seed hoạt động.
- **(D)**: Một security level được mở khóa. Seed đã gửi. Đang chờ key.

<!-- Các chuyển trạng thái (state transitions):
No. 1 (Không có Operation cụ thể):
Condition: Khởi động/khởi động lại ứng dụng ECU (ví dụ: ECU reset, power cycle, key cycle, chuyển từ sleep sang wake).
Action: Khởi tạo Att_Cnt (nếu áp dụng). Bắt đầu Delay_Timer (nếu yêu cầu khi khởi động).

No. 2 (Operation: AND - Các điều kiện phải đồng thời đúng):
Condition: Nhận SecurityAccess requestSeed. Độ dài thông điệp OK. Các điều kiện tùy chọn được đáp ứng (optional pre-conditions fulfilled).
Action: Nếu Static_Seed == True thì tạo và lưu seed cho securityAccessType yêu cầu (nếu chưa tạo trong chu kỳ ECU hiện tại); nếu False thì tạo seed mới. Lưu sub-function: xx = securityAccessType. Gửi phản hồi tích cực SecurityAccess với seed hoạt động cho securityAccessType yêu cầu.

No. 3 (Operation: AND):
Condition: Nhận SecurityAccess sendKey. Sub-function: yy == xx+1 (key tương ứng với seed trước). Độ dài thông điệp OK. Key OK.
Action: Att_Cnt = 0 cho sub-function xx (nếu áp dụng). Lưu Att_Cnt vào bộ nhớ non-volatile (nếu áp dụng). Mở khóa security level cho sub-function xx. Nếu Static_Seed == True thì xóa seed đã tạo cho sub-function xx. Gửi phản hồi tích cực SecurityAccess cho sendKey.

No. 4 (Operation: OR - Một trong các điều kiện đúng):
Condition:
Nhận SecurityAccess requestSeed, độ dài thông điệp NOK (Not OK) → Gửi NRC 0x13.
Nhận SecurityAccess sendKey → Gửi NRC 0x24.
Các điều kiện tùy chọn KHÔNG đáp ứng (optional pre-conditions NOT fulfilled) → Gửi NRC 0x22.
Delay chưa hết hạn (nếu áp dụng) → Gửi NRC 0x37.
SecurityAccess yêu cầu dẫn đến phản hồi tiêu cực tổng quát (theo phần 7.5) → Gửi NRC tương ứng.

Action: Tùy theo condition, gửi phản hồi tiêu cực tương ứng (NRC 0x13, 0x24, 0x22, 0x37, hoặc theo 7.5).

No. 5 (Operation: OR - Nhiều trường hợp):
Condition & Action (các trường hợp riêng):
Nhận requestSeed, tạo seed mới và gửi phản hồi tích cực (nếu Static_Seed == False hoặc không có seed lưu).
Nhận requestSeed, gửi seed lưu sẵn (nếu Static_Seed == True và có seed hoạt động).
Nhận requestSeed với securityAccessType khác trước, tạo seed mới (nếu Static_Seed == True).
Các trường hợp khác: Gửi phản hồi với seed zero nếu đã mở khóa, hoặc xử lý delay/Att_Cnt.


No. 6 (Không có Operation):
Condition: DiagnosticSessionControl được chấp nhận hoặc session timeout xảy ra.
Action: Bắt đầu session chẩn đoán phù hợp. Khóa ECU.

No. 7 (Operation: OR):
Condition: Nhận sendKey/requestSeed và mức yêu cầu đã mở khóa.
Action: Gửi NRC 0x24 (nếu sendKey) hoặc phản hồi với seed zero (nếu requestSeed). Hoặc gửi NRC tổng quát theo 7.5.

No. 8 (Operation: AND):
Condition: Nhận requestSeed, mức yêu cầu CHƯA mở khóa, độ dài OK, điều kiện tùy chọn đáp ứng, delay hết hạn.
Action: Tạo và lưu seed (nếu chưa có). Lưu xx = securityAccessType. Gửi phản hồi tích cực với seed hoạt động.

No. 9 (Operation: OR - Nhiều trường hợp xử lý key sai hoặc lỗi):
Condition & Action (các trường hợp):
Key NOK, Att_Cnt chưa vượt giới hạn → Att_Cnt++, lưu vào non-volatile, gửi NRC 0x35.
Key NOK, Att_Cnt vượt giới hạn → Att_Cnt++, bắt đầu Delay_Timer, gửi NRC 0x36.
Sub-function yy != xx+1 → Gửi NRC 0x24.
Độ dài NOK → Gửi NRC 0x13.
Các phản hồi tiêu cực tổng quát → Gửi NRC theo 7.5.


No. 10 (Operation: AND):
Condition: DiagnosticSessionControl được chấp nhận hoặc session timeout.
Action: Bắt đầu session chẩn đoán phù hợp. Khóa tất cả security level (lock ECU). -->

<!-- 

https://munich.dissec.to/kb/chapters/hydravision/TestCases/SecurityAccess.html

https://www.linkedin.com/pulse/security-access-mechanism-ecus-using-uds-guide-imane-bougrine-5kkhe


 -->