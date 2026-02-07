---
title: Guarantee timing to tester by sending busy responses
parent: DSL
nav_order: 6
---

# Guarantee timing to tester by sending busy responses
*Đảm bảo thời gian phản hồi tới tester.*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Cơ chế gửi response NRC 0x78 (Response Pending)

Khi **App** hoặc **DSP** có khả năng thực hiện tác vụ chẩn đoán được yêu cầu nhưng cần thêm thời gian để hoàn tất tác vụ và chuẩn bị dữ liệu phản hồi, **DSL** phải gửi một negative response với **NRC 0x78 (Response pending)**. Response này được gửi đi khi sắp đạt đến giới hạn thời gian phản hồi cho phép (được tính theo 2 công thức bên dưới). **DSL** thực hiện việc này để **đảm bảo tuân thủ các tham số thời gian** của giao thức chẩn đoán đối với tester.

**Công thức tính toán thời gian:**
- Trường hợp gửi response thông thường: .
> `DcmDspSessionP2ServerMax` - `DcmTimStrP2ServerAdjust`

- Trường hợp sau khi đã gửi NRC 0x78 trước đó:
> `DcmDspSessionP2StarServerMax` - `DcmTimStrP2StarServerAdjust`


<table class="hover-table">
  <thead>
    <tr>
      <th>Parameter</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>DcmDspSessionP2ServerMax</code><br><br>Float: [0 .. 1] (seconds)</td>
      <td>Giá trị thời gian <code>P2ServerMax</code> được cấu hình riêng cho từng session.<br><br>Nó là khoảng thời gian tối đa mà ECU có để bắt đầu truyền phản hồi sau khi đã nhận thành công một diagnostic request.<br><br>Giá trị này được thông báo cho tester trong response của service SessionControl (0x10). Ví dụ: [50 02 <strong>00 32</strong> 01 F4]<br><code>DcmDspSessionP2ServerMax</code> = 0x0032 = 50ms</td>
    </tr>
    <tr>
      <td><code>DcmDspSessionP2StarServerMax</code><br><br>Float: [0 .. 1] (seconds)</td>
      <td>Giá trị thời gian <code>P2*ServerMax</code> được cấu hình cho từng session.<br><br>Nó đại diện cho thời gian tối đa mà ECU có để gửi phản hồi tiếp theo sau khi một phản hồi NRC 0x78 đã được gửi đi trước đó.<br><br>Giá trị này được thông báo cho tester trong response của service SessionControl (0x10). Ví dụ: [50 02 00 32 <strong>01 F4</strong>]<br><code>DcmDspSessionP2StarServerMax</code> = 0x01F4 * 10 = 500 * 10 = 5000ms</td>
    </tr>
    <tr>
      <td><code>DcmTimStrP2ServerAdjust</code>/<br><code>DcmTimStrP2StarServerAdjust</code><br><br>Float: [0 .. 1] (seconds)</td>
      <td>Tham số <strong>điều chỉnh thời gian</strong> nhằm đảm bảo phản hồi thực tế sẽ xuất hiện trên bus trước khi giới hạn <code>P2ServerMax</code>/<code>P2*ServerMax</code> kết thúc.<br><br>Tham số này bù đắp cho các độ trễ giao tiếp nội bộ của kiến trúc phần mềm từ lúc <strong>Dcm</strong> khởi tạo quá trình truyền cho đến khi message thực sự lên bus.<br><br>Giá trị tham số được xác định bằng giây và phải là bội số của <code>DcmTaskTime</code>.</td>
    </tr>
  </tbody>
</table>




<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\P2&P2StartMessageTiming.drawio.svg"
    alt="Message timing with P2Server, P2StarServer*"
  />
  <figcaption>Message timing with P2Server, P2StarServer</figcaption>
</figure>


---

## 2. Quản lý tài nguyên và Giới hạn

Các response NRC 0x78 phải được gửi từ một **bộ đệm riêng biệt (separate buffer)**, không dùng chung với diagnostic buffer.

> **Lý do:** Để tránh việc ghi đè (overwriting) lên dữ liệu yêu cầu đang được xử lý hoặc dữ liệu phản hồi đang được **App** chuẩn bị dang dở trong bộ đệm chính (diagnostic buffer).

Số lượng response NRC 0x78 tối đa được phép gửi cho một diagnostic duy nhất bị giới hạn bởi tham số cấu hình <span class="text-blue">`DcmDslDiagRespMaxNumRespPend`</span>.

> **Lý do:** Giới hạn này nhằm ngăn chặn tình trạng **treo hệ thống (deadlocks)** trong **App**.

<details markdown="block">
<summary><span class="text-blue"><code>DcmDslDiagRespMaxNumRespPend</code></span></summary>

> <strong>Description</strong>: Số lượng tối đa các negative response với NRC 0x78 được cho phép đối với một request. Nếu Dcm đạt đến giới hạn này, một response cuối cùng với NRC 0x10 (generalReject) sẽ tự động được gửi đi và quá trình xử lý dịch vụ sẽ bị hủy bỏ.  
>  
> NRC 0x78: requestCorrectlyReceivedResponsePending
>  
> <strong>Range</strong>: 0 .. 255
{: .codeBlock }
</details>