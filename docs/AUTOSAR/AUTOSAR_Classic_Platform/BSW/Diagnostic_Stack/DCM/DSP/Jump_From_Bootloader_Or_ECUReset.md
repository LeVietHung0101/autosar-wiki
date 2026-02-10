---
title: Jump from Bootloader / ECUReset
parent: DSP
nav_order: 4
---

# Jump from Bootloader / ECUReset

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Ngay khi <span class="text-orange"><code>Dcm_Init()</code></span> được gọi, **Dcm** phải gọi <span class="text-orange"><code>Dcm_GetProgConditions()</code></span> để xác định xem việc khởi động này có phải là kết quả của một cú nhảy từ Bootloader hoặc sau một lệnh ECUReset hay không.

Nếu việc init Dcm là do nhảy từ Bootloader/ECUReset, **Dcm** phải gọi <span class="text-orange"><code>ComM_DCM_ActiveDiagnostic(NetworkId)</code></span> để yêu cầu **ComM** chuyển sang Full Communication Mode.

Khi **ComM** xác nhận mạng đã ở chế độ Full Communication, **Dcm** sẽ response cho SID đã được lưu trữ trong cấu trúc <span class="text-blue"><code>Dcm_ProgConditionsType</code></span> từ trước khi reset.


Nếu việc khởi tạo là sau khi nhảy từ bootloader và App đã được cập nhật thành công (cờ <span class="text-blue"><code>Dcm_ProgConditionsType.ApplUpdated</code></span> = True), **Dcm** phải gọi <span class="text-orange"><code>BswM_Dcm_ApplicationUpdated()</code></span>  để thông báo cho **BswM** về sự kiện này.


Lưu ý quan trọng: Software integrator có trách nhiệm đảm bảo dữ liệu trong <span class="text-blue"><code>Dcm_ProgConditionsType</code></span> (thường lưu ở NVRAM) phải sẵn sàng và hợp lệ tại thời điểm <span class="text-orange"><code>Dcm_Init()</code></span> được gọi để quá trình này diễn ra chính xác.


<details markdown="block">
<summary><span class="text-orange"><code>Dcm_GetProgConditions()</code></span></summary>

> <strong>Syntax</strong>: Dcm_EcuStartModeType Dcm_GetProgConditions(    
> <span class="indent">Dcm_ProgConditionsType * <strong>ProgConditions</strong></span>  
> )
>
> <strong>Description</strong>: Hàm này được gọi khi khởi tạo Dcm và cho phép xác định xem có cần gửi phản hồi ($50 hoặc $51) hay không. Các tham số ngữ cảnh được định nghĩa trong <span class="text-blue"><code>Dcm_ProgConditionsType</code></span>.
>  
> <strong>Parameters (out): ProgConditions</strong> - Các điều kiện mà việc nhảy từ bộ nạp khởi động
đã được yêu cầu.  
{: .codeBlock }
</details>




<details markdown="block">
<summary><span class="text-blue"><code>Dcm_ProgConditionsType</code></span></summary>

> <strong>Description</strong>: Cấu trúc chứa các điều kiện lập trình cần bảo toàn. Cấu trúc này bao gồm:  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>ConnectionId</td>
>    <td>ID duy nhất của kết nối nhận yêu cầu.</td>
>    </tr>
>    <tr>
>    <td>TesterAddress</td>
>    <td>Địa chỉ nguồn của Tester.</td>
>    </tr>
>    <tr>
>    <td>Sid</td>
>    <td>Service ID của yêu cầu.</td>
>    </tr>
>    <tr>
>    <td>SubFncId</td>
>    <td>Sub-function ID của yêu cầu.</td>
>    </tr>
>    <tr>
>    <td>ReprogramingRequest</td>
>    <td>Cờ yêu cầu lập trình lại ECU.</td>
>    </tr>
>    <tr>
>    <td>ApplUpdated</td>
>    <td>Cờ báo hiệu App đã được cập nhật.</td>
>    </tr>
>    <tr>
>    <td>ResponseRequired</td>
>    <td>Cờ yêu cầu Bootloader hoặc App phải gửi phản hồi sau khi reset.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>