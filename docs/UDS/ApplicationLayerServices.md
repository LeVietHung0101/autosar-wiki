---
title: Application layer services
parent: UDS
nav_order: 4
---

<h1>Application layer services</h1>

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Tổng quan

Các thành phần cơ bản của diagnostics application layer (đối với confirmed service):

<table class="hover-table">
  <thead>
    <tr>
      <th>Service Primitive</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Service request</td>
      <td>
        Được <strong>client function</strong> trong <strong>diagnostic tester application</strong> sử dụng để chuyển dữ liệu của <strong>diagnostic service</strong> được yêu cầu đến <strong>diagnostics application layer</strong>.
      </td>
    </tr>
    <tr>
      <td>Service request-confirmation</td>
      <td>
        Được <strong>client function</strong> trong <strong>diagnostic tester application</strong> sử dụng để thông báo rằng dữ liệu được truyền thông qua <strong>service request</strong> đã được gửi thành công lên <strong>vehicle communication bus</strong> mà <strong>diagnostic tester</strong> đang kết nối.
      </td>
    </tr>
    <tr>
      <td>Service indication</td>
      <td>
        Được <strong>diagnostics application layer</strong> sử dụng để chuyển dữ liệu đến <strong>server function</strong> của <strong>ECU diagnostic application</strong>.
      </td>
    </tr>
    <tr>
      <td>Service response</td>
      <td>
        Được <strong>server function</strong> trong <strong>ECU diagnostic application</strong> sử dụng để chuyển dữ liệu phản hồi (<strong>response data</strong>) do <strong>diagnostic service</strong> được yêu cầu tạo ra đến <strong>diagnostics application layer</strong>.
      </td>
    </tr>
    <tr>
      <td>Service response-confirmation</td>
      <td>
        Được <strong>server function</strong> trong <strong>ECU diagnostic application</strong> sử dụng để thông báo rằng dữ liệu được truyền thông qua <strong>service response</strong> đã được gửi thành công lên <strong>vehicle communication bus</strong> mà <strong>ECU</strong> đã nhận <strong>diagnostic request</strong>.
      </td>
    </tr>
    <tr>
      <td>Service confirmation</td>
      <td>
        Được <strong>diagnostics application layer</strong> sử dụng để chuyển dữ liệu đến <strong>client function</strong> trong <strong>diagnostic tester application</strong>.
      </td>
    </tr>
  </tbody>
</table>

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\TheApplicationLayerServicePrimitives_ConfirmedService.png"
  />
  <figcaption>Application layer service primitives - Confirmed service</a>
  </figcaption>
</figure>

---

# A_TA - Application layer target address

<details markdown="block">
<summary><i>Các khái niệm liên quan</i></summary>

> <table class="hover-table">
>   <thead>
>     <tr>
>       <th>Khái niệm</th>
>       <th>Định nghĩa</th>
>     </tr>
>   </thead>
>   <tbody>
>     <tr>
>       <td>local server</td>
>       <td>
>         Server được kết nối vào cùng mạng cục bộ với <b>client</b> và thuộc cùng không gian địa chỉ (<b>address space</b>) với client.
>       </td>
>     </tr>
>     <tr>
>       <td>remote server</td>
>       <td>
>         Server không kết nối trực tiếp với mạng chẩn đoán chính.
>         <br>Remote server được nhận diện bằng <b>remote address</b>, thuộc một không gian địa chỉ độc lập với mạng chính.
>         <br>Remote server được truy cập thông qua một <b>local server</b> trên mạng chính. Mỗi local server có thể đóng vai trò là <b>gateway</b> đến một nhóm remote server độc lập.
>         <br>Do đó, remote server luôn được xác định bằng hai địa chỉ: một <b>local address</b> xác định gateway và một <b>remote address</b> xác định remote server.
>       </td>
>     </tr>
>     <tr>
>       <td>remote client</td>
>       <td>
>         Client không kết nối trực tiếp với mạng chẩn đoán chính.
>         <br>Remote client được nhận diện bằng <b>remote address</b>, thuộc một không gian địa chỉ độc lập với mạng chính.
>       </td>
>     </tr>
>     <tr>
>       <td>TA</td>
>       <td>source address</td>
>     </tr>
>     <tr>
>       <td>SA</td>
>       <td>target address</td>
>     </tr>
>     <tr>
>       <td>A_TA</td>
>       <td>Application layer target address<br>Type: 2 byte unsigned integer value<br>Range: 0x0000-0xFFFF</td>
>     </tr>
>     <tr>
>       <td>A_SA</td>
>       <td>Application layer source address<br>Type: 2 byte unsigned integer value<br>Range: 0x0000-0xFFFF</td>
>     </tr>
>     <tr>
>       <td>A_TA_Type</td>
>       <td>Application layer target address type<br>Type: enumeration<br>Range: physical, functional<br>Description: Tham số A_TA_type là phần mở rộng của tham số A_TA; nó được sử dụng để biểu thị addressing method được chọn cho việc truyền tải thông điệp.</td>
>     </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>

Tham số `A_TA` sẽ được sử dụng để mã hóa định danh của máy khách và máy chủ (client & server identifiers.).

Đối với **service request** và **service indication**, `A_TA` đại diện cho server identifier của server sẽ thực hiện diagnostic service được yêu cầu. Nếu đang addressing đến một remote server, thì A_TA đại diện cho local server đóng vai trò là gateway từ main network sang remote network.

Đối với **service response** và **service confirmation**, `A_TA` đại diện cho địa chỉ của client function đã gửi yêu cầu diagnostic service ban đầu và sẽ nhận dữ liệu được yêu cầu (tức là `A_SA` của request). Service response và service confirmation luôn phải sử dụng **physical addressing**. Nếu đang addressing đến một remote client, thì `A_TA` đại diện cho local server đóng vai trò là gateway từ main network sang remote network.

Giá trị `A_TA` của một response message sẽ luôn giống với giá trị `A_SA` của request message tương ứng. Điều này đảm bảo response message luôn được gửi ngược trở lại đúng client đã gửi request message ban đầu.

---

# Addressing method

<details markdown="block">
<summary><i>Các khái niệm liên quan</i></summary>

> <table class="hover-table">
>   <thead>
>     <tr>
>       <th>Khái niệm</th>
>       <th>Định nghĩa</th>
>     </tr>
>   </thead>
>   <tbody>
>     <tr>
>       <td>client</td>
>       <td>
>         Chức năng thuộc <b>tester</b>, sử dụng các <b>diagnostic service</b>.
>         <br>Một tester còn sử dụng các chức năng khác như quản lý cơ sở dữ liệu, diễn giải dữ liệu chuyên biệt và giao diện người-máy (human-machine interface - HMI).
>       </td>
>     </tr>
>     <tr>
>       <td>server</td>
>       <td>
>         Chức năng nằm trong một <b>ECU</b>, cung cấp các <b>diagnostic service</b>.
>         <br><b>ISO 14229</b> phân biệt giữa <b>server</b> (chức năng) và <b>ECU</b> (thiết bị) nhằm đảm bảo tính độc lập với cách triển khai.
>       </td>
>     </tr>
>     <tr>
>       <td>electronic control unit (ECU)</td>
>       <td>
>         Bộ điều khiển điện tử cung cấp thông tin liên quan đến các cảm biến và mạng điều khiển được kết nối.
>         <br>Các hệ thống như <b>ABS (Anti-lock Braking System)</b> và <b>Engine Management System</b> được xem là ECU.
>       </td>
>     </tr>
>     <tr>
>       <td>diagnostic service</td>
>       <td>
>         Quá trình trao đổi thông tin do <b>client</b> khởi tạo nhằm yêu cầu thông tin chẩn đoán từ <b>server</b> và/hoặc thay đổi hành vi của server phục vụ mục đích chẩn đoán.
>       </td>
>     </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>

Có hai phương pháp đánh địa chỉ (**addressing methods**) khác nhau được quy định cho diagnostics:
- Physical addressing
- Functional addressing

Một hệ thống xe có thể tồn tại hai không gian địa chỉ (**address space**) độc lập, dùng để định nghĩa hai tập địa chỉ đích (**target addresses**), mỗi tập tương ứng với một addressing method. Hai address spaces này không liên quan trực tiếp đến nhau và được cấu hình độc lập bởi OEM.

## Physical addressing

**Physical addressing** luôn là một message dành riêng cho một server được triển khai trong một ECU. Khi sử dụng physical addressing, việc truyền thông là **point-to-point communication** (1:1) giữa client và server. 

## Functional addressing

**Functional addressing** được client sử dụng khi:
- Client không biết physical address của server function sẽ phản hồi một diagnostic service request, hoặc
- Server function được triển khai dưới dạng một chức năng phân tán (distributed function) trên nhiều ECU.

Khi sử dụng functional addressing, việc truyền thông là **broadcast communication** (1:N) từ client đến một server được triển khai trong một hoặc nhiều ECU.

## So sánh

<table class="hover-table">
  <thead>
    <tr>
      <th>Physical Addressing</th>
      <th>Functional Addressing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Giao tiếp với một ECU cụ thể</td>
      <td>Giao tiếp với một nhóm ECU</td>
    </tr>
    <tr>
      <td>Point-to-point communication (1:1)<br>(Tester ↔ Specific ECU)</td>
      <td>Broadcast communication (1:N)<br>(Tester → Group of ECUs)</td>
    </tr>
    <tr>
      <td>ECU bắt buộc phản hồi (PosRsp / NegRsp)</td>
      <td>Chỉ các ECU hỗ trợ service và đáp ứng điều kiện mới xử lý yêu cầu; response (nếu có) luôn được gửi bằng Physical Addressing. Các ECU còn lại sẽ không phản hồi hoặc gửi NegRsp theo quy định của service.</td>
    </tr>
    <tr>
      <td>Dùng cho các service liên quan đến cho Programming, SecurityAccess, ECU-specific services</td>
      <td>Dùng cho các service như ReadDataByIdentifier (0x22), ReadDTCInformation (0x19), ECUReset (0x11)</td>
    </tr>
  </tbody>
</table>

## Ví dụ

Giả sử trên xe có 5 ECU có giá trị Physical Address như sau:

<table class="hover-table">
  <thead>
    <tr>
      <th>ECU</th>
      <th>Physical Address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Engine ECU</td>
      <td>0x701</td>
    </tr>
    <tr>
      <td>Transmission ECU</td>
      <td>0x702</td>
    </tr>
    <tr>
      <td>ABS ECU (Anti-lock Brakes System)</td>
      <td>0x703</td>
    </tr>
    <tr>
      <td>BCM ECU (Body Control Module)</td>
      <td>0x704</td>
    </tr>
    <tr>
      <td>Gateway</td>
      <td>0x705</td>
    </tr>
  </tbody>
</table>

Đây là **physical target address space**. Khi tester muốn đọc VIN từ Engine ECU, tester sẽ gửi request message `[22 F1 90]` với `A_TA` = 0x71 và `A_TA_Type` = physical. Khi này, chỉ Engine ECU nhận request.

Trong khi đó OEM lại định nghĩa một **functional target address space** như sau:

<table class="hover-table">
  <thead>
    <tr>
      <th>Functional Address</th>
      <th>Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x7DF</td>
      <td>Tất cả ECU hỗ trợ diagnostics</td>
    </tr>
    <tr>
      <td>0x7E0</td>
      <td>Powertrain group</td>
    </tr>
    <tr>
      <td>0x7E5</td>
      <td>Body group</td>
    </tr>
  </tbody>
</table>

Nếu tester gửi message `[19 02]` (Read DTC by Status Mask) với `A_TA` = 0x7DF và `A_TA_Type` = functional, thì ECU nào hỗ trợ service đó sẽ phản hồi (theo quy định của service).

---

# Xử lý đồng thời nhiều Request sử dụng Physical Addressing và Functional Addressing

## Nguyên tắc chung

Trong hầu hết các server, chỉ có một diagnostic protocol instance được triển khai. Do đó, tại một thời điểm, diagnostic protocol instance chỉ có thể xử lý một diagnostic request.

> <i>**Diagnostic protocol instance** là một thực thể (instance) của diagnostic protocol trong ECU, chịu trách nhiệm quản lý toàn bộ vòng đời của một phiên xử lý diagnostic request.</i>

Vì vậy, bất kỳ request message nào được nhận (physical/functional addressing) đều sẽ chiếm quyền sử dụng của diagnostic protocol instance cho đến khi quá trình xử lý hoàn tất.

Quá trình xử lý được coi là hoàn tất khi xảy ra một trong các trường hợp sau:
- Final response đã được gửi.
- Application call đã hoàn thành và không yêu cầu gửi response.

## Ngoại lệ 1 - Keep-Alive Logic (TesterPresent)

**Keep-Alive Logic** được client sử dụng để duy trì một diagnostic session đã được kích hoạt trên một hoặc nhiều server.

Cơ chế này được định nghĩa là một TesterPresent message hợp lệ được gửi bằng Functional Addressing với SPRMIB = 1 (TRUE).
```c
0x3E 0x80   (A_TA_Type = functional)
```

Loại TesterPresent message này phải được xử lý thông qua **bypass logic**. Điều này có nghĩa là server phải đảm bảo TesterPresent không được phép chiếm dụng hoặc chặn diagnostics application layer, để các diagnostic request tiếp theo vẫn có thể được xử lý ngay lập tức.

## Ngoại lệ 2 - Nhận Request thuộc dải Service 0x00–0x0F

Nếu server hỗ trợ các service trong khoảng 0x00–0x0F và nhận được một diagnostic request cũng thuộc khoảng này, thì:
- Mọi service đang thực thi ngoài khoảng 0x00–0x0F phải bị hủy bỏ (abort).
- Default Session phải được kích hoạt.
- Diagnostic service thuộc khoảng 0x00–0x0F phải được xử lý.

**Ngoại lệ**: Quy tắc này không áp dụng khi Programming Session đang hoạt động.

Các service trong khoảng 0x00–0x0F:

<table class="hover-table">
  <thead>
    <tr>
      <th>Service Identifier (SID)</th>
      <th>Service type</th>
      <th>Standard</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>N/A</td>
      <td>Reserved</td>
      <td>Reserved</td>
    </tr>
    <tr>
      <td>0x01 – 0x0F</td>
      <td>Specified services</td>
      <td>ISO 15031-5 / SAE J1979</td>
      <td>Chẩn đoán khí thải và giám sát pháp lý (OBD);<br>Không phải UDS, nhưng cùng chia sẻ không gian SIDs</td>
    </tr>
  </tbody>
</table>