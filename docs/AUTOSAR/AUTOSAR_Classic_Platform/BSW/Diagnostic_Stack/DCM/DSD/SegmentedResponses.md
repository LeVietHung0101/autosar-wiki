---
title: Segmented Responses  (paged-buffer)
parent: DSD
nav_order: 7
---

# Segmented Responses (paged-buffer)
*Phản hồi phân đoạn*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Trong giao thức chẩn đoán, một số dịch vụ cho phép trao đổi lượng dữ liệu lớn, ví dụ như ReadDTCInformation (0x19) và TransferData (0x36).

---

## Cách tiếp cận truyền thống (Linear buffer)

Bộ đệm nội bộ của ECU phải đủ lớn để chứa toàn bộ thông điệp dữ liệu dài nhất có thể được trao đổi (trường hợp xấu nhất), và toàn bộ bộ đệm này sẽ được nạp đầy trước khi bắt đầu truyền.

**Hạn chế:** bộ nhớ RAM trong ECU thường là tài nguyên hạn chế, đặc biệt đối với các vi điều khiển nhỏ.

---

## Cách tiếp cận bộ đệm theo trang (Paged-buffer)

**Paged-buffer (segmented responses)** là cơ chế phân trang (paging mechanism) chỉ cần một lượng bộ nhớ nhỏ hơn đáng kể; thay vì lấp đầy toàn bộ, bộ đệm chỉ được lấp đầy một phần, truyền đi một phần, sau đó tiếp tục được nạp lại dữ liệu và truyền đi tiếp.

Cơ chế này đòi hỏi một thời gian phản ứng được xác định rõ ràng (well-defined reaction time) để thực hiện việc nạp lại dữ liệu vào bộ đệm (buffer refilling).

**Ưu điểm:** Giúp tiết kiệm đáng kể tài nguyên bộ nhớ RAM, vốn là tài nguyên cực kỳ quan trọng và hạn chế trong các ECU, đặc biệt là các vi điều khiển nhỏ.


Người dùng có thể lựa chọn sử dụng “linear buffer” hoặc  “paged-buffer” cho chẩn đoán tùy theo đặc điểm ECU.
