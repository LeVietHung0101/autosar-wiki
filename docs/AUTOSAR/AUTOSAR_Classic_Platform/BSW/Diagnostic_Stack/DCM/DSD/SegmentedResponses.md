---
title: Segmented Responses
parent: DSD
nav_order: 7
---

# Segmented Responses
*Phản hồi phân đoạn*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Trong giao thức chẩn đoán, một số dịch vụ cho phép trao đổi lượng dữ liệu lớn, ví dụ:
ReadDTCInformation (0x19) và TransferData (0x36).

Theo cách tiếp cận truyền thống (Linear buffer), bộ đệm nội bộ của ECU phải đủ lớn để chứa toàn bộ thông điệp dữ liệu dài nhất có thể được trao đổi (trường hợp xấu nhất), và toàn bộ bộ đệm này sẽ được nạp đầy trước khi bắt đầu truyền.

Tuy nhiên, bộ nhớ RAM trong ECU thường là tài nguyên hạn chế, đặc biệt đối với các vi điều khiển nhỏ. Với cách tiếp cận tiết kiệm bộ nhớ hơn, bộ đệm chỉ được nạp một phần, truyền một phần, sau đó lại được nạp tiếp một phần khác, và cứ thế lặp lại. 

Paged-buffer (segmented responses) là cơ chế phân trang (paging mechanism) chỉ cần một lượng bộ nhớ nhỏ hơn đáng kể, nhưng yêu cầu thời gian phản hồi để nạp lại bộ đệm phải được xác định và đảm bảo rõ ràng.

Người dùng có thể lựa chọn sử dụng “linear buffer” hoặc  “paged-buffer” cho chẩn đoán tùy theo đặc điểm ECU.
