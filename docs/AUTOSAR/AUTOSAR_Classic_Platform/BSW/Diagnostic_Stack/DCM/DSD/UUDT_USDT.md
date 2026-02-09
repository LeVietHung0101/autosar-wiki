---
title: UUDT and USDT
parent: DSD
nav_order: 9
---

# UUDT và USDT

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# 1. UUDT (Unacknowledged Unsegmented Data Transfer)

Loại thông điệp này được thiết kế để truyền tải dữ liệu một cách nhanh chóng và định kỳ mà không cần các thủ tục quản lý luồng phức tạp.

{: .note }
**UUDT** là dạng truyền dữ liệu không phân đoạn (unsegmented). Các thông điệp này được gửi đi mà không cần xác nhận ở tầng cao (unacknowledged).

**Cơ chế truyền dẫn:**
- Sử dụng các IF-PDU chuyên dụng (Interface PDUs) thay vì đi qua tầng TP.
- Phản hồi **UUDT** không chứa mã định danh dịch vụ (Service ID - SID) và không có byte PCI (Protocol Control Information) trong các lần truyền định kỳ.
- DCM gửi các thông điệp này qua một giao thức và bộ đệm riêng biệt để tránh nghẽn luồng chẩn đoán chính.

**Ứng dụng:**
- Chủ yếu được sử dụng cho dịch vụ ReadDataByPeriodicIdentifier (0x2A) để truyền định kỳ các giá trị dữ liệu (DID) lên bus.

---

## 2. USDT (Unacknowledged Segmented Data Transfer)

Đây là loại thông điệp chẩn đoán phổ biến nhất, được sử dụng cho các negative response và các yêu cầu chẩn đoán thông thường.

{: .note }
**USDT** hỗ trợ việc truyền dữ liệu phân đoạn (segmented). Điều này cho phép gửi các phản hồi có kích thước lớn hơn một khung tin CAN đơn lẻ bằng cách sử dụng Transport Protocol layer (TP) để quản lý luồng dữ liệu.

**Cơ chế truyền dẫn:** Trong **DCM**, USDT thường sử dụng <span class="text-blue"><code>DcmTxPduId</code></span> đã được thiết lập sẵn cho các phản hồi chẩn đoán thông thường.

**Ứng dụng:**
- Sử dụng cho các phản hồi chẩn đoán tiêu chuẩn (giao tiếp dạng "ping-pong").
- Sử dụng trong dịch vụ ResponseOnEvent (0x86) (cả Type 1 trên cùng ID phản hồi chính và Type 2 trên ID riêng biệt).

---

## So sánh

<table class="hover-table">
  <thead>
    <tr>
      <th>Đặc điểm</th>
      <th>UUDT</th>
      <th>USDT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Tính phân đoạn</td>
      <td>Không hỗ trợ (Unsegmented)</td>
      <td>Có hỗ trợ (Segmented)</td>
    </tr>
    <tr>
      <td>Tầng giao tiếp</td>
      <td>Interface layer (IF)</td>
      <td>Transport Protocol layer (TP)</td>
    </tr>
    <tr>
      <td>SID</td>
      <td>Không chứa SID trong phản hồi định kỳ</td>
      <td>Có chứa SID trong phản hồi</td>
    </tr>
    <tr>
      <td>ID truyền dẫn</td>
      <td>Dùng ID (<code>TxPduId</code>) riêng biệt</td>
      <td>Dùng chung hoặc riêng ID phản hồi</td>
    </tr>
    <tr>
      <td>Dịch vụ tiêu biểu</td>
      <td>ReadDataByPeriodicIdentifier (0x2A)</td>
      <td>ResponseOnEvent (0x86)</td>
    </tr>
  </tbody>
</table>

{: .note }
**USDT** yêu cầu các khung tin như Flow Control để điều phối việc truyền dữ liệu nhiều khung (Multi-frame), trong khi **UUDT** đơn giản là phát tán (broadcast) dữ liệu trong một khung đơn duy nhất mà không cần quan tâm đến trạng thái của phía nhận.


**Lưu ý về chế độ hoạt động:**<br>
Các phản hồi định kỳ (thường là **UUDT**) chỉ được phép thực hiện khi ECU ở chế độ Full Communication Mode. Nếu hệ thống chuyển sang chế độ Silent hoặc No Communication, các sự kiện truyền UUDT sẽ bị hủy bỏ và không được đưa vào hàng đợi.