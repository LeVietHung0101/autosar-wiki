---
title: Receive a request message and suppress a negative response
parent: DSD
nav_order: 4
---

# Receive a request message and suppress a negative response
*Nhận một thông điệp yêu cầu và triệt tiêu một phản hồi tiêu cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Trong trường hợp sử dụng **Functional addressing**, **DSD** có trách nhiệm không gửi (triệt tiêu) negative response đối với các NRC sau:

<table class="hover-table">
  <thead>
    <tr>
      <th>NRC (Hex)</th>
      <th>Mnemonic</th>
      <th>Use Case</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>0x11</td><td>SNS</td><td>Service Not Supported</td></tr>
    <tr><td>0x12</td><td>SFNS</td><td>Sub-Function Not Supported</td></tr>
    <tr><td>0x31</td><td>ROOR</td><td>Request Out of Range</td></tr>
    <tr><td>0x7E</td><td>SFNSIAS</td><td>Sub-Function Not Supported in Active Session</td></tr>
    <tr><td>0x7F</td><td>SNSIAS</td><td>Service Not Supported in Active Session</td></tr>
  </tbody>
</table>

Lý do:

- **Functional addressing:** Đây là chế độ gửi yêu cầu đến nhiều ECU cùng lúc (broadcast), sử dụng địa chỉ chức năng (thường là 0x7DF trong CAN cho OBD). Khác với physical addressing (địa chỉ cụ thể cho một ECU, ví dụ: 0x7E0).

- Trong functional addressing, nhiều ECU có thể nhận yêu cầu, và nếu tất cả gửi phản hồi tiêu cực, bus sẽ bị flood (nghẽn). Do đó, suppress (triệt tiêu) NRC giúp “chỉ ECU hỗ trợ dịch vụ” mới gửi positive response, còn lại im lặng, **tránh "noise" không cần thiết**.


