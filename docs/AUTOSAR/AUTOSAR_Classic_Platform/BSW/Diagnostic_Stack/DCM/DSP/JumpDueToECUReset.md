---
title: Jump due to ECUReset
parent: DSP
nav_order: 3
---

# Jump due to ECUReset

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Khi nhận request **ECUReset (0x11**):
- Nếu <span class="text-blue"><code>DcmResponseToEcuReset</code></span> = <code>AFTER_RESET</code>: Dcm sẽ gọi hàm <span class="text-orange"><code>Dcm_SetProgConditions()</code></span> để đặt cờ <span class="text-blue"><code>ResponseRequired</code></span>; và gửi response sau khi quá trình reset hoàn tất.
- Nếu <span class="text-blue"><code>DcmResponseToEcuReset</code></span> = <code>BEFORE_RESET</code>: Dcm sẽ gửi response trước khi thực hiện lệnh reset.

Trong trường hợp Dcm khởi tạo lệnh reset và <span class="text-blue"><code>DcmSendRespPendOnRestart</code></span> = TRUE, Dcm phải kích hoạt việc truyền response NRC 0x78 (Response pending) trước khi lệnh reset thực tế diễn ra.


<details markdown="block">
<summary><span class="text-blue"><code>DcmSendRespPendOnRestart</code></span></summary>

> <strong>Description</strong>:  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>True</td>
>    <td>Dcm sẽ gửi NRC 0x78 trước khi chuyển sang chế độ bootloader hoặc thực hiện ECU reset.</td>
>    </tr>
>    <tr>
>    <td>False</td>
>    <td>Dcm sẽ không gửi NRC 0x78</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>


<details markdown="block">
<summary><span class="text-blue"><code>ResponseRequired</code></span></summary>

> <strong>Description</strong>: Đặt thành TRUE trong trường hợp flashloader hoặc App cần gửi phản hồi.  
{: .codeBlock }
</details>