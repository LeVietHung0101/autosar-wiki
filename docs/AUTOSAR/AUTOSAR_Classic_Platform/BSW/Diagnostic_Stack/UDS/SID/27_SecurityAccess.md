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

## Mô tả

{: .note }
Dịch vụ **SecurityAccess (0x27)** cung cấp cơ chế để truy cập dữ liệu hoặc dịch vụ chẩn đoán bị hạn chế do lý do bảo mật, khí thải hoặc an toàn.

Các dịch vụ chẩn đoán này bao gồm: downloading/uploading routine hoặc dữ liệu vào server, hoặc đọc memory ở vị trí cụ thể từ server. Việc downloading routine hoặc dữ liệu không đúng có thể gây hỏng phần cứng hoặc vi phạm tiêu chuẩn khí thải hoặc an toàn. 

Một số chức năng/dịch vụ chẩn đoán được yêu cầu trong một diagnostic session cụ thể có thể yêu cầu một chuỗi security access thành công (<i>ví dụ: Request Download (0x34), Request Upload (0x35), TransferData (0x36),...</i>). Trong trường hợp như vậy, chuỗi các dịch vụ sau phải được yêu cầu:
- DiagnosticSessionControl service
- SecurityAccess service service
- Secured diagnostic service.

---

### Quá trình thực hiện:

1. Client request 'seed' từ server.
2. Server gửi 'seed'.
3. Client tính toán và gửi 'key' tương ứng.
4. Server kiểm tra 'key' (so sánh với một giá trị được lưu trữ/tính toán nội bộ):
    1. Nếu 'key' đúng, server mở khóa (unlock) quyền truy cập của client vào các dịch vụ/dữ liệu cụ thể.
    2. Nếu 'key' không đúng, server xem đây là một lần truy cập thất bại.

---

## Server chỉ hỗ trợ một security level tại một thời điểm

Một khi một security level được mở khoá, nó phải vẫn được mở khóa ngay cả sau khi server nhận request 'seed' cho một security level khác.
Nó chỉ bị khoá khi một security level mới được mở khóa hoặc SecurityAccess bị thoát vì lý do khác (ví dụ: DiagnosticSessionControl được chấp nhận hoặc timeout session xảy ra).

### Yêu cầu security level KHÁC với security level đang hoạt động

Nếu security level liên quan đến requestSeed **0x03** đang hoạt động và yêu cầu từ client thành công trong việc mở khóa security level liên quan đến requestSeed **0x01** thì:
- Bất kỳ chức năng bảo mật bổ sung nào trước đó đã được mở khóa bởi security level liên quan đến requestSeed **0x03** sẽ không còn hoạt động nữa.
- Chỉ các chức năng bảo mật được hỗ trợ bởi security level liên quan đến requestSeed **0x01** mới được mở khóa tại thời điểm đó.

### Yêu cầu security level TRÙNG với security level đang hoạt động

Nếu client yêu cầu mở khoá một security level đã được mở khoá, server sẽ gửi positive response với 'seed' = 0x00.

Server không bao giờ được gửi một 'seed' toàn bộ là 0x00 cho một security level đang bị khóa.
Do đó, client sẽ biết được một security level cụ thể có bị khoá hay không bằng cách so sánh 'seed' với 0x00.

---

## Cơ chế khi truy cập thất bại

Server có thể kích hoạt một thời gian delay (**Delay_Timer**) sau khi power-up/reset hoặc sau nhiều lần truy cập thất bại (**Att_Cnt**).
Trong khoảng thời gian delay này, các dịch vụ SecurityAccess từ client sẽ bị server từ chối thông qua negative response (NRC 0x37).
Điều này sẽ không ảnh hưởng đến giao tiếp/dịch vụ chẩn đoán thông thường.

**Server sẽ kích hoạt Delay_Timer** (nếu được OEM hỗ trợ) khi:
1. Đạt đến số lần truy cập thất bại.
2. Server được power-up/reset và một dịch vụ SecurityAccess trước đó đã thất bại một lần duy nhất <i>hoặc</i> không thể xác định có thất bại hay không.
    - Nếu dịch vụ SecurityAccess tiếp theo thành công, thì thông tin chỉ báo nội bộ của server cho việc kích hoạt timer delay khi bật nguồn/reset phải được xóa bởi server.

{: .note }
>Nhiều team/công ty khác nhau sẽ chịu trách nhiệm về firmware của FBL và APP,
>dẫn đến sự **khác nhau về security level giữa các firmware** này (số lượng security được hỗ trợ, số lượng dịch vụ và loại dịch vụ được hỗ trợ trong từng security level cụ thể);
>thậm chí là **khác nhau về cách triển khai** (Delay_Timer, Att_Cnt, thuật toán nội bộ, giá trị 'key' lưu nội bộ,...).


<table class="hover-table">
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Mô tả</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Delay_Timer</strong></td>
      <td>Thời gian chờ tối thiểu giữa các lần thử truy cập an ninh.<br>
          Server có thể chọn triển khai một timer riêng cho mỗi security level hoặc sử dụng một timer duy nhất cho tất cả các security level.<br>
          Nó có thể mang giá trị cố định hoặc thay đổi dựa vào số lần truy cập sai 'Att_Cnt' (tuỳ thuộc vào OEM).<br>
          Timer này có thể được kích hoạt sau mỗi lần power-up/reset (tuỳ thuộc vào OEM).
      </td>
    </tr>
    <tr>
      <td><strong>Att_Cnt</strong></td>
      <td>Số lần truy cập an ninh sai trước khi chèn (insert) thời gian delay (Start_Delay).<br>
          Att_Cnt được lưu trong non-volatile memory.<br>
          Khi triển khai, bộ đếm này là bắt buộc cho từng security level riêng lẻ.<br>
          Nếu server nhận 'key' đúng, Att_Cnt được reset về 0.<br>
          Nó có thể dùng để quyết định sau reset liệu có cần bắt đầu Delay_Timer hay không.<br>
          Giá trị của Delay_Timer có thể phụ thuộc vào Att_Cnt.<br>
      </td>
    </tr>
    <tr>
      <td><strong>Att_Cnt_Limit</strong></td>
      <td>Số lần truy cập an ninh sai tối đa</td>
    </tr>
    <tr>
      <td><strong>Static_Seed</strong><br>(boolean)</td>
      <td><strong>True:</strong> seed được lưu trữ nội bộ (tạm thời) trong server và sử dụng lại (re-used) trong positive response của request seed cho từng security level;
          nhằm tối ưu xử lý request 'seed' lặp lại từ client. Seed này sẽ bị xoá khi server nhận 'key' hợp lệ (mở khoá security level) hoặc ECU power-down/reset.<br><br>
          <strong>False:</strong> seed ngẫu nhiên được sử dụng mỗi khi nhận request 'seed' mới.<br><br>
          Nếu Delay_Timer và Att_Cnt không được hỗ trợ bởi OEM, seed ngẫu nhiên phải luôn được sử dụng.
      </td>
    </tr>
  </tbody>
</table>

---

## Request message

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
      <td>#1</td>
      <td>SID</td>
      <td>0x27</td>
    </tr>
    <tr>
      <td>#2</td>
      <td>sub-function = [ securityAccessType = <strong>requestSeed</strong> ]</td>
      <td>0x01, 0x03, ..., 0x7D</td>
    </tr>
    <tr>
      <td>#3 .. #n</td>
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
      <td>#1</td>
      <td>SID</td>
      <td>0x27</td>
    </tr>
    <tr>
      <td>#2</td>
      <td>sub-function = [ securityAccessType = <strong>sendKey</strong> ]</td>
      <td>0x02, 0x04,..., 0x7E</td>
    </tr>
    <tr>
      <td>#3 .. #n</td>
      <td>securityKey[]</td>
      <td></td>
    </tr>
  </tbody>
</table>


Các giá trị sub-function cũng dùng để xác định security level được kích hoạt (tại một thời điểm).<br>
>sub-function = [SPRMIB] [securityAccessType]

Trong đó
- **SPRMIB** nằm ở bit thứ 7.
- **securityAccessType** gồm các bit 6 - 0, là requestSeed hoặc sendKey (tuỳ vào loại dịch vụ).
  - **requestSeed**: luôn là số lẻ (0x01, 0x03, ..., 0x7D).
  - **sendKey** = requestSeed + 1: luôn là số chẵn (0x02, 0x04,..., 0x7E).

Các data-parameter:
- **securityAccessDataRecord[]**: Tuỳ chọn của người dùng để truyền dữ liệu đến server khi client request 'seed'. Ví dụ: Nó có thể chứa thông tin nhận dạng (identification) của client được xác minh trên server.
- **securityKey[]**: Giá trị 'key' được tạo bởi thuật toán bảo mật tương ứng với một giá trị 'seed' cụ thể.

Bảng sau mô tả ý nghĩa của sub-function dựa vào securityAccessType (bỏ qua SPRMIB).
Việc đánh số các security level là tùy ý và không ngụ ý bất kỳ mối quan hệ nào giữa chúng.

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
      <td>Dành cho tương lai.</td>
    </tr>
    <tr>
      <td>0x01, 0x03 – 0x41</td>
      <td>requestSeed</td>
      <td>Yêu cầu 'seed' với security level được định nghĩa bởi OEM.</td>
    </tr>
    <tr>
      <td>0x02, 0x04 – 0x42</td>
      <td>sendKey</td>
      <td>Gửi 'key' với security level được định nghĩa bởi OEM.</td>
    </tr>
    <tr>
      <td>0x43 – 0x5E</td>
      <td>ISOSAEReserved</td>
      <td>Dành cho tương lai.</td>
    </tr>
    <tr>
      <td>0x5F</td>
      <td>ISO26021-2 values</td>
      <td>Request 'seed' với security level đặc biệt, dành cho việc kích hoạt an toàn các thiết bị pyrotechnic khi xe hết hạn sử dụng (theo ISO 26021-2).</td>
    </tr>
    <tr>
      <td>0x60</td>
      <td>ISO26021-2 sendKey values</td>
      <td>Gửi 'key' tương ứng với requestSeed 0x5F</td>
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

Ghi chú:
- **ISO 26021-2:2008** (Road vehicles — End-of-life activation of on-board pyrotechnic devices — Part 2: Communication requirements) là tiêu chuẩn quốc tế về kích hoạt các thiết bị pyrotechnic trên xe hơi ở giai đoạn kết thúc vòng đời (end-of-life, ví dụ: khi xe bị loại bỏ, tháo dỡ hoặc tái chế).
- **Thiết bị pyrotechnic**: các bộ phận an toàn sử dụng một lượng nhỏ thuốc nổ hoặc nhiên liệu rắn để tạo phản ứng cháy/nổ tức thì, kích hoạt cơ chế bảo vệ trong vài phần nghìn giây. Chúng được dùng phổ biến để thổi túi khí (airbags), siết chặt dây an toàn (seatbelt pretensioners) khi va chạm hoặc ngắt cầu chì cao áp (Pyro Fuse) trên xe điện. Những thiết bị này cần kích hoạt an toàn để tránh tai nạn khi tháo dỡ xe (ví dụ: kích hoạt từ xa để xả hết năng lượng trước khi cắt dây).

---

## Positive response message

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
      <td>#1</td>
      <td>SID</td>
      <td>0x67</td>
    </tr>
    <tr>
      <td>#2</td>
      <td>sub-function = [ securityAccessType ]</td>
      <td>0x00 - 0x7F</td>
    </tr>
    <tr>
      <td>#3 .. #n</td>
      <td>securitySeed[]</td>
      <td></td>
    </tr>
  </tbody>
</table>

**securitySeed[]** là giá trị được server gửi đi và được client sử dụng khi tính toán 'key' cần thiết để truy cập bảo mật.
Bắt buộc có trong response message nếu securityAccessType = requestSeed.

## Ví dụ

1. Tester tool request 'seed' từ ECU bằng UDS request [27 01].
2. ECU phản hồi bằng securitySeed = [DE AD BE EF].
3. Tester tool gửi securityKey = [C0 FF EE].
4. ECU xác nhận 'key' đúng và cấp quyền truy cập.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\27_SecurityAccess\SecurityAccess_example.svg"
    alt="Quy trình SecurityAccess khi server ở trạng thái locked"
  />
</figure>

---

## Supported NRC


<table class="hover-table">
  <thead>
    <tr>
      <th>NRC</th>
      <th>Use case</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x12</td>
      <td>sub-functionNotSupported</td>
      <td>Server không hỗ trợ security level được yêu cầu trong request 'requestSeed'</td>
    </tr>
    <tr>
      <td>0x13</td>
      <td>incorrectMessageLengthOrInvalidFormat</td>
      <td></td>
    </tr>
    <tr>
      <td>0x22</td>
      <td>conditionsNotCorrect</td>
      <td>Các điều kiện cho dịch vụ SecurityAccess không được đáp ứng<br>
          (ví dụ: nhiệt độ, điện áp,...).</td>
    </tr>
    <tr>
      <td>0x24</td>
      <td>requestSequenceError</td>
      <td><strong>#1.</strong> Request 'sendKey' được gửi nhưng trước đó không có request 'requestSeed';<br>
          <strong>#2.</strong> Request 'sendKey' được gửi khi server đã mở khoá security level tương ứng;<br>
          <strong>#3.</strong> Sub-function của request 'sendKey' không khớp với sub-function của request 'requestSeed' (yy != xx + 1)</td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>requestOutOfRange</td>
      <td>securityAccessDataRecord[] chứa invalid data<br>(ví dụ: ID client sai, hoặc dữ liệu xác thực không khớp)</td>
    </tr>
    <tr>
      <td>0x35</td>
      <td>invalidKey</td>
      <td>Khi giá trị 'key' trong request 'sendKey' không khớp với giá trị được lưu trữ/tính toán nội bộ của server,
          và <strong>(Att_Cnt+1) < Att_Cnt_Limit</strong> (chưa đạt giới hạn số lần truy cập sai),
          server sẽ tăng Att_Cnt</td>
    </tr>
    <tr>
      <td>0x36</td>
      <td>exceededNumberOfAttempts</td>
      <td>Khi giá trị 'key' trong request 'sendKey' không khớp với giá trị được lưu trữ/tính toán nội bộ của server,
          và <strong>(Att_Cnt+1) >= Att_Cnt_Limit</strong> (đạt giới hạn số lần truy cập sai),
          server sẽ tăng Att_Cnt và kích hoạt Delay_Timer</td>
    </tr>
    <tr>
      <td>0x37</td>
      <td>requiredTimeDelayNotExpired</td>
      <td>Client gửi request 'requestSeed' nhưng server đã kích hoạt Delay_Timer và chưa hết thời gian chờ</td>
    </tr>
  </tbody>
</table>

---

---

## SecurityAccess state chart

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\27_SecurityAccess\SecurityAccess_State_Chart.png"
    alt="SecurityAccess State Chart"
    width="75%"
  />
  <figcaption>SecurityAccess State Chart</figcaption>
</figure>

---

### Cách trường hợp tăng Att_Cnt

<table class="hover-table">
  <thead>
    <tr>
      <th>State transitions</th>
      <th>Condition</th>
      <th>Action</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>9, 10</td>
      <td>SecurityAccess sendKey received.<br>
          sub-function: yy == xx+1.<br>
          Message length OK.<br>
          Key NOK.<br>
          (Att_Cnt+1) < Att_Cnt_Limit.
      </td>
      <td>Att_Cnt++ for sub-function xx.<br>
          Store Att_Cnt in non-volatile memory.<br>
          Transmit negative response NRC 0x35.
      </td>
    </tr>
    <tr>
      <td>9, 10</td>
      <td>SecurityAccess sendKey received.<br>
          sub-function: yy == xx+1.<br>
          Message length OK.<br>
          Key NOK.<br>
          (Att_Cnt+1) >= Att_Cnt_Limit.
      </td>
      <td>Att_Cnt++ for sub-function xx.<br>
          Start <strong>Delay_Timer</strong> for sub-function xx.<br>
          Store Att_Cnt in non-volatile memory.<br>
          Transmit negative response NRC 0x36.
      </td>
    </tr>
    <tr>
      <td>9, 10</td>
      <td>SecurityAccess sendKey received.<br>
          sub-function: yy <> xx+1.
      </td>
      <td>Transmit negative response NRC 0x24.<br>
          Store Att_Cnt in non-volatile memory.<br>
          Att_Cnt++ for sub-function xx (nếu State transitions = 9).
      </td>
    </tr>
    <tr>
      <td>9, 10</td>
      <td>SecurityAccess sendKey received.<br>
          sub-function: yy == xx+1.<br>
          Message length NOK.
      </td>
      <td>Transmit negative response NRC 0x13.<br>
          Store Att_Cnt in non-volatile memory.<br>
          Att_Cnt++ for sub-function xx (nếu State transitions = 9).
      </td>
    </tr>
  </tbody>
</table>

Ghi chú:
- **xx**: requestSeed securityAccessType *cuối cùng* mà server nhận.
- **yy**: sendKey securityAccessType *hiện tại* mà server nhận.
- **NOK**: Not OK.
