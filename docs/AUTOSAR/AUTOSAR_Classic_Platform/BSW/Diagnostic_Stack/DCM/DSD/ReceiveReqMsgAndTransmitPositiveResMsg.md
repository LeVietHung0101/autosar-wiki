---
title: Receive a request message and transmit a positive response message
parent: DSD
nav_order: 2
---

# Receive a request message and transmit a positive response message
*Nhận một thông điệp yêu cầu và truyền một thông điệp phản hồi tích cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Quy trình xử lý cơ bản

Đây là trường hợp sử dụng tiêu chuẩn của giao tiếp chẩn đoán thông thường (giao thức "ping-pong"):

**1. Tiếp nhận**: ECU (server) nhận được một diagnostic request message.

**2. Xác thực:** DSD kiểm tra tính hợp lệ của request message đó. *Trong trường hợp này, yêu cầu được xác định là hợp lệ và phản hồi sẽ là positive response.*

**3. Xử lý dữ liệu:** Yêu cầu được chuyển tiếp đến bộ xử lý dữ liệu tương ứng trong DSP (*đồng bộ* hoặc *bất đồng bộ*).

**4. Kích hoạt phản hồi:** Khi bộ xử lý dữ liệu hoàn tất tất cả các hành động xử lý, nó sẽ kích hoạt việc truyền response message thông qua DSD.

## 2. Hai phương thức xử lý phản hồi

Tùy vào thời gian thực hiện tác vụ, bộ xử lý dữ liệu có thể kích hoạt truyền dẫn theo hai cách:

**1. Đồng bộ (Synchronous)**:<br>
Nếu dịch vụ được xử lý ngay lập tức trong hàm chỉ báo yêu cầu (request indication function), bộ xử lý dữ liệu có thể kích hoạt truyền dẫn ngay trong chính hàm đó.

**2. Bất đồng bộ (Asynchronous):**<br>
Nếu việc xử lý tốn nhiều thời gian hơn (ví dụ: phải đợi trình điều khiển EEPROM), bộ xử lý dữ liệu sẽ trì hoãn việc xử lý. Khi đó, bộ xử lý dữ liệu sẽ kích hoạt truyền dẫn từ chính luồng xử lý hoặc chu kỳ hoạt động của nó (ví dụ trong <span class="text-orange"><code>Dcm_MainFunction()</code></span>). Cơ chế [Response Pending (NRC 0x78)]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSL/Guarantee_timing_to_tester_by_sending_busy_responses/" | relative_url }}) trong các trường hợp xử lý lâu sẽ do **DSL** đảm nhận.

So sánh:

<table class="hover-table">
  <thead>
    <tr>
      <th>Đồng bộ (Synchronous)</th>
      <th>Bất đồng bộ (Asynchronous)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Xử lý ngay trong hàm nhận yêu cầu.</td>
      <td>Trì hoãn nếu cần thời gian.</td>
    </tr>
    <tr>
      <td>Phù hợp cho yêu cầu đơn giản như đọc biến, nhanh chóng (dưới 50ms).</td>
      <td>Cần cho tác vụ phức tạp như flash ECU, có thể mất vài giây hoặc vài phút.</td>
    </tr>
  </tbody>
</table>



---

## 3. Cơ chế khóa Protocol Data Unit (PDU)

Ngay khi nhận được một request message, <span class="text-blue"><code>DcmPduId</code></span> tương ứng sẽ bị DSL khóa lại.

Trong suốt quá trình xử lý yêu cầu này, ECU không thể tiếp nhận bất kỳ yêu cầu nào khác cùng loại giao thức (ví dụ: một phiên nâng cao có thể bị kết thúc bởi một phiên OBD) cho đến khi response message tương ứng được gửi đi và <span class="text-blue"><code>DcmPduId</code></span> được giải phóng. ([Blocking Mechanism]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSL/Forward_requests_from_PduR_to_DSD/#cơ-chế-chặn-pduid-blocking-mechanism" | relative_url }}))
