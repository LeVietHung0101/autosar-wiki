---
title: Receive a request message and suppress a negative response
parent: DSD
nav_order: 4
---

# Receive a request message and suppress a negative response
*Nhận một thông điệp yêu cầu và loại bỏ một phản hồi tiêu cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

In case of functional addressing, the DSD submodule shall suppress the negative response for NRC 0x11, 0x12, 0x31, 0x7E and 0x7F.

Functional addressing: Đây là chế độ gửi yêu cầu đến nhiều ECU cùng lúc (broadcast), sử dụng địa chỉ chức năng (thường là 0x7DF trong CAN cho OBD). Khác với physical addressing (địa chỉ cụ thể cho một ECU, ví dụ: 0x7E0). 
Trong functional addressing, nhiều ECU có thể nhận yêu cầu, và nếu tất cả gửi phản hồi tiêu cực, bus sẽ bị flood (nghẽn). Do đó, suppress NRC giúp “chỉ ECU hỗ trợ dịch vụ” mới phản hồi tích cực, còn lại im lặng, tránh "noise" không cần thiết.

DSD kiểm tra loại addressing ngay sau khi nhận yêu cầu (trước khi gửi đến DSP).

0x11: Service Not Supported (Dịch vụ không được hỗ trợ).
0x12: SubFunction Not Supported (Chức năng phụ không được hỗ trợ).
0x31: Request Out Of Range (Yêu cầu ngoài phạm vi).
0x7E: SubFunction Not Supported In Active Session (Chức năng phụ không hỗ trợ trong phiên hiện tại).
0x7F: Service Not Supported In Active Session (Dịch vụ không hỗ trợ trong phiên hiện tại).
