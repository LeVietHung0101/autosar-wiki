---
title: Flags management
parent: DSP
nav_order: 5
---

# Flags management

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Khi nhảy sang Bootloader (Jump to Bootloader)

Cơ chế này được kích hoạt khi ECU nhận được yêu cầu chuyển sang Programming Session (0x10 0x02):

- **Thiết lập cờ:** Khi nhận request **0x10 0x02 (Start Programming Session)**, Dcm sẽ gọi <span class="text-orange"><code>Dcm_SetProgConditions()</code></span> để thiết lập cờ <span class="text-blue"><code>ReprogramingRequest</code></span> và cờ <span class="text-blue"><code>ResponseRequired</code></span> (nếu dịch vụ này yêu cầu).

- **Xử lý phản hồi và xóa cờ:** Tùy thuộc vào cấu hình <span class="text-blue"><code>DcmDspSessionForBoot</code></span>, App sẽ gửi positive response ngay (nếu `SPRMIB` = FALSE) hoặc sau khi reset ECU, bootloader sẽ gửi phản hồi và thực hiện xóa cờ <span class="text-blue"><code>ResponseRequired</code></span>.

- **Xóa cờ yêu cầu lập trình:** Trong bất kỳ trường hợp nào, bootloader có trách nhiệm xóa cờ <span class="text-blue"><code>ReprogramingRequest</code></span>.

- **Xử lý lỗi:** Nếu lệnh gọi API <span class="text-orange"><code>Dcm_SetProgConditions()</code></span> trả về `E_NOT_OK` trong quá trình nhảy sang Bootloader, một lỗi `DCM_E_SET_PROG_CONDITIONS_FAIL` sẽ được báo cáo qua **DET** và hệ thống sẽ quay lại hoạt động bình thường.


<details markdown="block">
<summary><span class="text-orange"><code>Dcm_SetProgConditions()</code></span></summary>

> <strong>Description</strong>: Hàm này cho phép integrator lưu trữ các thông tin liên quan trước khi ECU thực hiện lệnh nhảy sang bootloader hoặc thực hiện reset hệ thống (jumping to bootloader / jump due to ECUReset request).
>
> <strong>Parameters (in): OpStatus</strong> - Dùng để quản lý trạng thái của thao tác bất đồng bộ.  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>DCM_INITIAL</td>
>    <td>Cho biết lệnh gọi đến &lt;Module>_&lt;DiagnosticService> lần đầu tiên.</td>
>    </tr>
>    <tr>
>    <td>DCM_PENDING</td>
>    <td>Tiếp tục thực hiện nếu lần trước trả về trạng thái chờ.</td>
>    </tr>
>    <tr>
>    <td>DCM_CANCEL</td>
>    <td>Yêu cầu hủy bỏ thao tác đang diễn ra.</td>
>    </tr>
>    <tr>
>    <td>DCM_E_PENDING</td>
>    <td>Việc lưu trữ đang diễn ra. Dcm sẽ gọi lại hàm này trong chu kỳ <span class="text-orange"><code>Dcm_MainFunction()</code></span> tiếp theo.</td>
>    </tr>
>    <tr>
>    <td>DCM_FORCE_RCRRP_OK</td>
>    <td>Xác nhận sau khi <strong>App</strong> đã gửi xong phản hồi NRC 0x78.</td>
>    </tr>
>   </tbody>
> </table>
>
> <strong>Parameters (in): ProgConditions</strong> - Con trỏ tới cấu trúc chứa các điều kiện lập trình cần bảo toàn. Cấu trúc này bao gồm:  
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
>
> <strong>Return value:</strong>  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>E_OK</td>
>    <td>Các điều kiện đã được thiết lập/lưu trữ thành công. Dcm sẽ chuyển sang chế độ <code>DCM_EXECUTE</code> để thực hiện reset thực tế.</td>
>    </tr>
>    <tr>
>    <td>E_NOT_OK</td>
>    <td>Thiết lập thất bại. Dcm sẽ dừng quy trình reset, báo lỗi <code>DCM_E_SET_PROG_CONDITIONS_FAIL</code> qua <strong>DET</strong> và trả về <strong>NRC 0x22 (ConditionsNotCorrect)</strong> cho tester.</td>
>    </tr>
>    <tr>
>    <td>DCM_OEM_BOOT_RESPAPP</td>
>    <td>This diagnostic session allows to jump to <strong>OEM</strong> Bootloader and application sends final response.</td>
>    </tr>
>    <tr>
>    <td>DCM_E_PENDING</td>
>    <td>Việc lưu trữ đang diễn ra. Dcm sẽ gọi lại hàm này trong chu kỳ <span class="text-orange"><code>Dcm_MainFunction()</code></span> tiếp theo.</td>
>    </tr>
>    <tr>
>    <td>DCM_E_FORCE_RCRRP</td>
>    <td><strong>App</strong> yêu cầu Dcm gửi negative response NRC 0x78 ngay lập tức.</td>
>    </tr>
>   </tbody>
> </table>
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



<details markdown="block">
<summary><span class="text-blue"><code>DcmDspSessionForBoot</code></span></summary>

> <strong>Description</strong>: Tham số này xác định xem diagnostic session này có cho phép nhảy đến Bootloader (Bootloader của nhà sản xuất thiết bị gốc hoặc Bootloader của nhà cung cấp hệ thống) hay không và xác định đơn vị nào sẽ gửi phản hồi cuối cùng. Nếu pdiagnostic session này không cho phép nhảy đến Bootloader, giá trị <strong>DCM_NO_BOOT</strong> sẽ được chọn.
>
> <strong>Range:</strong>  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>DCM_NO_BOOT</td>
>    <td>This diagnostic session doesn’t allow to jump to Bootloader.</td>
>    </tr>
>    <tr>
>    <td>DCM_OEM_BOOT</td>
>    <td>This diagnostic session allows to jump to <strong>OEM</strong> Bootloader and bootloader sends final response.</td>
>    </tr>
>    <tr>
>    <td>DCM_OEM_BOOT_RESPAPP</td>
>    <td>This diagnostic session allows to jump to <strong>OEM</strong> Bootloader and application sends final response.</td>
>    </tr>
>    <tr>
>    <td>DCM_SYS_BOOT</td>
>    <td>This diagnostic session allows to jump to <strong>System Supplier</strong> Bootloader and bootloader sends final response.</td>
>    </tr>
>    <tr>
>    <td>DCM_SYS_BOOT_RESPAPP</td>
>    <td>This diagnostic session allows to jump to <strong>System Supplier</strong> Bootloader and application sends final response.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>



---

## 2. Khi nhảy từ Bootloader về App (Jump from Bootloader)

Quy trình này diễn ra sau khi quá trình reprogramming hoàn tất:

- **Cập nhật trạng thái:** Sau khi phần mềm ứng dụng được reprogramming thành công, bootloader sẽ cập nhật các cờ <span class="text-blue"><code>ApplUpdated</code></span> và <span class="text-blue"><code>ResponseRequired</code></span>.

- **Đọc trạng thái khi khởi động:** Khi ứng dụng mới được lập trình khởi động lần đầu tiên sau khi reset ECU, Dcm sẽ đọc giá trị của hai cờ này bằng cách gọi <span class="text-orange"><code>Dcm_GetProgConditions()</code></span>.

- **Xóa cờ khôi phục:** Các cờ <span class="text-blue"><code>ApplUpdated</code></span> và <span class="text-blue"><code>ResponseRequired</code></span> sẽ được **xóa bởi mã tích hợp (integration code)** ngay trong lần gọi <span class="text-orange"><code>Dcm_GetProgConditions()</code></span> này.




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


