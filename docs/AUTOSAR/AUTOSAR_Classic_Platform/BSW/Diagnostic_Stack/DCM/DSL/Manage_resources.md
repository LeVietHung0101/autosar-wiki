---
title: Manage resources
parent: DSL
nav_order: 17
---

# Manage resources
*Quản lý tài nguyên.*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

*Do tài nguyên hạn chế, các điểm sau đây cần được xem như những gợi ý cho thiết kế*

**Sử dụng duy nhất một diagnostic buffer**: Dcm chỉ được phép sử dụng và cấp phát duy nhất một diagnostic buffer. Buffer này sẽ được dùng chung để xử lý tất cả diagnostic requests và responses.

**Separate buffer (bộ đệm riêng) cho response NRC 0x78 (Response pending)**: Việc gửi các response NRC 0x78 (Response pending) phải được thực hiện thông qua một bộ đệm riêng biệt. Điều này là cần thiết để tránh việc ghi đè (overwrite) lên dữ liệu đang được xử lý trong diagnostic buffer.

**Xử lý paged-buffer**: Nếu <span class="text-blue">DcmPagedBufferEnabled</span> = TRUE, Dcm phải cung cấp cơ chế để gửi các responses có kích thước lớn hơn diagnostic buffer đã được cấu hình và cấp phát. 

