---
title: Send a positive response message without corresponding request
parent: DSD
nav_order: 6
---

# Send a positive response message without corresponding request
*Gửi một thông điệp phản hồi tích cực mà không có yêu cầu tương ứng*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Có hai dịch vụ trong giao thức UDS mà ECU có thể gửi nhiều phản hồi chỉ cho một yêu cầu duy nhất. Thông thường, một dịch vụ được dùng để kích hoạt (hoặc hủy bỏ) việc truyền tin theo sự kiện hoặc theo thời gian của một dịch vụ khác.

Mặc dù việc xử lý này chủ yếu do **DSL** điều khiển, **DSD** vẫn cung cấp khả năng tạo ra phản hồi mà không cần có yêu cầu trực tiếp tương ứng từ tester.

> *Đây là cơ chế **subscription** kiểu chẩn đoán: client "subscribe" một lần → ECU gửi dữ liệu nhiều lần theo thời gian hoặc sự kiện. Tuân thủ ISO 14229-1 (phần 7.3 về periodic/event-triggered).*
> 
> *Ví dụ: Trong xe, client subscribe dữ liệu tốc độ động cơ → ECU gửi định kỳ mà không cần hỏi lại, giúp theo dõi liên tục mà không làm nghẽn bus.*

---

## Dịch vụ ReadDataByPeriodicIdentifier (0x2A)

Cho phép tester yêu cầu truyền định kỳ các giá trị dữ liệu. Phản hồi định kỳ này là [UUDT]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/UUDT_USDT/#1-uudt-unacknowledged-unsegmented-data-transfer" | relative_url }}) message được gửi trên một <span class="text-blue"><code>DcmTxPduId</code></span> riêng biệt (Type 2).

> *Ví dụ: Client gửi 0x2A + list PID (như PID tốc độ, nhiệt độ) → ECU gửi định kỳ dữ liệu đó (ví dụ: mỗi 100ms). **Type 2** dùng kênh riêng để tránh xung đột với giao tiếp chẩn đoán bình thường. Hữu ích cho logging dữ liệu liên tục trong kiểm tra xe hoặc phát triển.*

---

## Dịch vụ ResponseOnEvent (0x86)

Yêu cầu ECU bắt đầu hoặc dừng truyền phản hồi khi có một sự kiện cụ thể xảy ra. Có hai loại:

- **Type 1:** [USDT]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/UUDT_USDT/#2-usdt-unacknowledged-segmented-data-transfer" | relative_url }})  message trên cùng một <span class="text-blue"><code>DcmTxPduId</code></span> dùng cho các phản hồi thông thường; các message này phải được đồng bộ hóa với luồng phản hồi chính vốn có ưu tiên cao hơn.

- **Type 2:** [USDT]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/UUDT_USDT/#2-usdt-unacknowledged-segmented-data-transfer" | relative_url }})  message trên một <span class="text-blue"><code>DcmTxPduId</code></span> riêng biệt.


> <i>**Type 1:** DSL quản lý hàng đợi truyền (transmission queue) với ưu tiên: Normal diagnostic responses (như 0x10, 0x22) **ưu tiên cao hơn** unsolicited responses (phản hồi không được yêu cầu). Nếu bus bận, unsolicited response chờ, tránh mất thông tin quan trọng.</i>
>  
>  <i>Ví dụ: Nếu đang trả lời yêu cầu đọc dữ liệu, thì event-triggered response phải chờ đến lượt.</i>
