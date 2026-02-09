---
title: Receive a request message and suppress a positive response
parent: DSD
nav_order: 3
---

# Receive a request message and suppress a positive response
*Nhận một thông điệp yêu cầu và triệt tiêu một phản hồi tích cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Đây là một trường hợp sử dụng phụ của [trường hợp trước]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/SendPositiveResMsgWithoutCorrespondingReq/" | relative_url }}). Trong giao thức UDS, tester có thể yêu cầu ECU **không gửi positive response** bằng cách set [`SPRMIB`]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/SPRMIB/" | relative_url }}) = 1 trong request message. 

Việc xử lý triệt tiêu phản hồi đặc biệt này được thực hiện hoàn toàn bên trong **DSD**. **DSD** sẽ kiểm tra `SPRMIB` ngay sau khi xác thực yêu cầu, trước khi chuyển đến **DSP**.