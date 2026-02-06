---
title: Allow to modify timings
parent: DSL
nav_order: 15
---

# Allow to modify timings
*Cho phép thay đổi các tham số timing.*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

**Dcm** phải chịu trách nhiệm xử lý các tham số thời gian của protocol (tuân thủ theo ISO 15765-3:2004), bao gồm: P2ServerMin, P2ServerMax, P2*ServerMin, P2*ServerMax và S3Server. Các tham số thời gian này chỉ có ảnh hưởng đến Session Layer timing và hoàn toàn không ảnh hưởng đến Transport Layer timing.

<table class="hover-table">
  <thead>
    <tr>
      <th>Protocol timing parameter</th>
      <th>Description</th>
      <th>Default value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>P2ServerMin (P2min)</td>
      <td>Khoảng thời gian tối thiểu mà ECU phải đợi trước khi bắt đầu gửi response sau khi nhận được request.</td>
      <td>0ms</td>
    </tr>
    <tr>
      <td>P2ServerMax</td>
      <td>Khoảng thời gian tối đa mà ECU có để bắt đầu truyền response sau khi đã nhận thành công một diagnostic request.</td>
      <td></td>
    </tr>
    <tr>
      <td>P2*ServerMin (P2*min)</td>
      <td>Khoảng thời gian tối thiểu ECU phải đợi sau khi gửi NRC 0x78 trước khi gửi phản hồi tiếp theo.</td>
      <td>0ms</td>
    </tr>
    <tr>
      <td>P2*ServerMax</td>
      <td>Khoảng thời gian tối đa mà ECU có để bắt đầu truyền response sau khi đã gửi một negative response "Response Pending" (NRC 0x78) trước đó.</td>
      <td></td>
    </tr>
    <tr>
      <td>S3Server</td>
      <td>Bộ định thời session (session timeout). Khi ECU đang ở non-default session, nếu không nhận được diagnostic request nào trong khoảng thời gian S3Server này, ECU sẽ tự động quay về default session.</td>
      <td>5s</td>
    </tr>
  </tbody>
</table>

> **ISO 15765-3:2004**: Road vehicles — Diagnostics on Controller Area Networks (CAN) - Part 3: Implementation of unified diagnostic services (UDS on CAN)

Một số tham số thời gian có thể được sửa đổi trong khi protocol đang hoạt động thông qua hai dịch vụ **UDS** sau:
<details markdown="block">
<summary>Dịch vụ DiagnosticSessionControl (0x10)</summary>

> <table class="hover-table">
>   <thead>
>     <tr>
>       <th>Diagnostic session type<br>(sub function value)</th>
>       <th>Diagnostic session</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>   <tbody>
>     <tr>
>       <td>0x01</td>
>       <td>Default Session</td>
>       <td>On Start of ECU, ECU runs in to default session.</td>
>     </tr>
>     <tr>
>       <td>0x02</td>
>       <td>Programming Session</td>
>       <td>Used to upload software.</td>
>     </tr>
>     <tr>
>       <td>0x03</td>
>       <td>Extended Diagnostic Session</td>
>       <td>Used to unlock additional diagnostic functions.</td>
>     </tr>
>     <tr>
>       <td>0x04</td>
>       <td>Safety System Diagnostic System</td>
>       <td>Used to test all safety critical diagnostic functions.</td>
>     </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>
<details markdown="block">
<summary>Dịch vụ AccessTimingParameter (0x83)</summary>

> <table class="hover-table">
>   <thead>
>     <tr>
>       <th>Timing parameter access Type<br>(Sub function value)</th>
>       <th>Timing parameter access</th>
>       <th>Description</th>
>     </tr>
>   </thead>
>   <tbody>
>     <tr>
>       <td>0x01</td>
>       <td>Set Timing Parameters</td>
>       <td>Sets specific timing parameters.</td>
>     </tr>
>     <tr>
>       <td>0x02</td>
>       <td>Get Timing Parameters</td>
>       <td>Retrieves current timing parameters.</td>
>     </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>

Để hỗ trợ việc thay đổi này, **DSL** cung cấp các chức năng:
- Cung cấp các tham số thời gian đang hoạt động hiện tại.
- Thiết lập các tham số thời gian mới. Tuy nhiên, việc kích hoạt các giá trị thời gian mới chỉ được phép thực hiện sau khi đã gửi xong response.

