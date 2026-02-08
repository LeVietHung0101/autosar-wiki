---
title: Receive a request message and suppress a positive response
parent: DSD
nav_order: 3
---

# Receive a request message and suppress a positive response
*Nhận một thông điệp yêu cầu và loại bỏ một phản hồi tích cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Đây là một trường hợp sử dụng phụ của trường hợp trước. Trong giao thức UDS, có thể loại bỏ phản hồi tích cực bằng cách đặt một bit đặc biệt (SPRMIB) trong request message. Việc xử lý loại bỏ đặc biệt này được thực hiện hoàn toàn bên trong DSD.

Quy trình tương tự: nhận yêu cầu, kiểm tra hợp lệ bởi DSD, chuyển đến DSP xử lý, nhưng không gửi phản hồi cuối cùng.

DSD kiểm tra SPRMIB ngay sau khi xác thực yêu cầu, trước khi chuyển đến DSP.

SPRMIB - SuppressPosRspMsgIndicationBit là bit 7 của SubFunction. Nếu set thành 1, DSD sẽ không gửi positive response nếu thành công, nhưng vẫn gửi negative response nếu thất bại. Điều này được định nghĩa trong ISO 14229-1, phần 7.5.