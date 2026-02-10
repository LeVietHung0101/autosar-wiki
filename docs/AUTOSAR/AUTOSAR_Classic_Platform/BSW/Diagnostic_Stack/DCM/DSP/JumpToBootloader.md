---
title: Jump to Bootloader
parent: DSP
nav_order: 2
---

# Jump to Bootloader

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Jump to Bootloader

{: .note }
**Jump to Bootloader** là quy trình trong Dcm để xử lý dịch vụ DiagnosticSessionControl (0x10) khi tester yêu cầu nhảy từ application sang bootloader (phần mềm lập trình/cập nhật firmware ECU).

Bootloader này có thể là OEM Bootloader hoặc System Supplier Bootloader

Quy trình này không reset ECU ngay mà chuẩn bị qua các bước sau:
1. Mode switch
1. Gửi response
1. Lưu dữ liệu
1. Thực hiện reset ECU.

Các parameter, function quan trọng trong quy trình:
1. <span class="text-blue"><code>DcmDspSessionForBoot</code></span>: Xác định App hay Bootloader sẽ gửi positive response và ECU sẽ nhảy đến loại bootloader nào (của OEM hay System Supplier).
  - `DCM_OEM_BOOT` / `DCM_SYS_BOOT`: lưu dữ liệu trước reset; bootloader gửi response cuối nếu cần.
  - `DCM_OEM_BOOT_RESPAPP` / `DCM_SYS_BOOT_RESPAPP`: Nhảy nhưng gửi response positive từ **App** trước reset.
2. <span class="text-blue"><code>DcmSendRespPendOnRestart</code></span>:
  - TRUE: gửi NRC 0x78 pending để reload timer P2* ở tester (client).
  - FALSE: không gửi NRC 0x78.
3. <span class="text-blue"><code>SPRMIB</code></span>
  - Nếu <span class="text-blue"><code>SPRMIB</code></span> = 1, suppress positive response (tùy implementation ở bootloader).
4. <span class="text-blue"><code>DcmEcuReset</code></span>: gồm các mode liên quan như:
  - `JUMPTOBOOTLOADER` / `JUMPTOSYSSUPPLIERBOOTLOADER`: chuẩn bị.
  - `EXECUTE`: thực hiện reset.
5. <span class="text-orange"><code>Dcm_SetProgConditions()</code></span>: cho phép integrator lưu trữ các thông tin liên quan trước khi ECU thực hiện lệnh nhảy sang bootloader hoặc thực hiện reset hệ thống (jumping to bootloader / jump due to ECUReset request).


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
<summary><span class="text-blue"><code>DcmEcuReset</code></span></summary>

> <strong>Description</strong>: Thể hiện các kiểu ECU reset khác nhau.
>  
> **Return value:**
> <table class="hover-table">  
>   <tbody>  
>    <tr>
>    <td>0</td>
>    <td>DCM_NONE</td>
>    <td></td>
>    </tr>
>    <tr>
>    <td>1</td>
>    <td>DCM_HARD</td>
>    <td>Báo hiệu một lần reset phần cứng hoàn toàn.<br>Tương ứng với Service EcuHardReset (0x11 0x01)</td>
>    </tr>
>    <tr>
>    <td>2</td>
>    <td>DCM_KEYONOFF</td>
>    <td>Chế độ này mô phỏng hành động tắt và bật chìa khóa xe.<br>Tương ứng với yêu cầu keyOffOnReset (0x11 0x02)</td>
>    </tr>  
>    <tr>
>    <td>3</td>
>    <td>DCM_SOFT</td>
>    <td>Báo hiệu một lần reset phần mềm, nơi các biến được khởi tạo lại nhưng phần cứng không bị ngắt điện.<br>Tương ứng với Service EcuSoftReset (0x11 0x03)</td>
>    </tr>
>    <tr>
>    <td>4</td>
>    <td>DCM_JUMPTOBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang OEM Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho OEM.</td>
>    </tr>
>    <tr>
>    <td>5</td>
>    <td>DCM_JUMPTOSYSSUPPLIERBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang System Supplier Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho System Supplier.</td>
>    </tr>
>    <tr>
>    <td>6</td>
>    <td>DCM_EXECUTE</td>
>    <td>Yêu cầu BswM thực hiện lệnh reset thực tế lên phần cứng.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>



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

---

## 2. Use Cases

Có **4 trường hợp** được xác định khi thực hiện nhảy sang bootloader (giả định `SPRMIB` là false):
1.  **App** gửi positive response cuối cùng ngay lập tức, sau đó nhảy sang bootloader.
2.  **App** gửi negative response với NRC 0x78 (Response Pending), sau đó gửi positive response cuối cùng và mới thực hiện nhảy.
3.  **App** nhảy sang bootloader ngay lập tức và **bootloader sẽ chịu trách nhiệm gửi positive response cuối cùng**.
4.  **App** gửi negative response với NRC 0x78, sau đó nhảy sang bootloader và bootloader gửi positive response cuối cùng.

> *Nếu `SPRMIB` là True, thì use case 1 và 3 sẽ không gửi positive response.*

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSP\JumpToBootloader_4UseCases.png"
    alt="4 use cases of Jump to Bootloader"
  />
  <figcaption>4 use cases of Jump to Bootloader</figcaption>
</figure>


**4 trường hợp** trên khác nhau ở:
- Ai gửi positive response (Application hay Bootloader) → Dựa vào <span class="text-blue"><code>DcmDspSessionForBoot</code></span>.
- App có gửi NRC 0x78 hay không? → Dựa vào <span class="text-blue"><code>DcmSendRespPendOnRestart</code></span>.




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



---

## 3. Cơ chế kích hoạt và chuẩn bị

**Kích hoạt chuyển đổi chế độ:**<br>Khi nhận dịch vụ Session Control (0x10) với session yêu cầu nhảy sang bootloader (thông qua <span class="text-blue"><code>DcmDspSessionForBoot</code></span>), **Dcm** sẽ chuẩn bị bằng cách set:
- <span class="text-blue"><code>DcmEcuReset</code></span> sang **JUMPTOBOOTLOADER** (đối với OEM bootloader), hoặc
- <span class="text-blue"><code>DcmEcuReset</code></span> sang **JUMPTOSYSSUPPLIERBOOTLOADER** (đối với bootloader của nhà cung cấp hệ thống).



<details markdown="block">
<summary><span class="text-blue"><code>DcmEcuReset</code></span></summary>

> <strong>Description</strong>: Thể hiện các kiểu ECU reset khác nhau.
>  
> **Return value:**
> <table class="hover-table">  
>   <tbody>  
>    <tr>
>    <td>0</td>
>    <td>DCM_NONE</td>
>    <td></td>
>    </tr>
>    <tr>
>    <td>1</td>
>    <td>DCM_HARD</td>
>    <td>Báo hiệu một lần reset phần cứng hoàn toàn.<br>Tương ứng với Service EcuHardReset (0x11 0x01)</td>
>    </tr>
>    <tr>
>    <td>2</td>
>    <td>DCM_KEYONOFF</td>
>    <td>Chế độ này mô phỏng hành động tắt và bật chìa khóa xe.<br>Tương ứng với yêu cầu keyOffOnReset (0x11 0x02)</td>
>    </tr>  
>    <tr>
>    <td>3</td>
>    <td>DCM_SOFT</td>
>    <td>Báo hiệu một lần reset phần mềm, nơi các biến được khởi tạo lại nhưng phần cứng không bị ngắt điện.<br>Tương ứng với Service EcuSoftReset (0x11 0x03)</td>
>    </tr>
>    <tr>
>    <td>4</td>
>    <td>DCM_JUMPTOBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang OEM Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho OEM.</td>
>    </tr>
>    <tr>
>    <td>5</td>
>    <td>DCM_JUMPTOSYSSUPPLIERBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang System Supplier Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho System Supplier.</td>
>    </tr>
>    <tr>
>    <td>6</td>
>    <td>DCM_EXECUTE</td>
>    <td>Yêu cầu BswM thực hiện lệnh reset thực tế lên phần cứng.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>


**Thông báo cho BswM:**<br>Việc chuyển đổi chế độ này nhằm thông báo cho module **BswM**, để nó phối hợp các module khác (như ComM cho giao tiếp, NvM cho bộ nhớ) chuẩn bị các điều kiện cần thiết để thực hiện nhảy sang bootloader một cách an toàn. Điều này tránh gián đoạn hệ thống và đảm bảo dữ liệu được lưu trữ trước khi nhảy.


**Từ chối yêu cầu:**<br>Trong quá trình xử lý reset để nhảy sang bootloader, **Dcm** phải bỏ qua mọi yêu cầu chẩn đoán khác. Nếu việc chuyển đổi chế độ không thể thực hiện, **Dcm** sẽ trả về negative response với **NRC 0x22 (Conditions not correct)**.

---

## 4. Quản lý phản hồi NRC 0x78 và tham số thời gian

**Gửi NRC 0x78:**<br>Nếu <span class="text-blue"><code>DcmSendRespPendOnRestart</code></span> = TRUE, **Dcm** phải kích hoạt truyền negative response với  NRC 0x78 trước khi thực hiện reset. Việc này giúp tester nạp lại bộ định thời **P2** để duy trì kết nối.

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



**Xử lý lỗi truyền tin:**<br>Nếu phản hồi NRC 0x78 không được gửi thành công, **Dcm** sẽ hủy yêu cầu hiện tại và **không thực hiện việc nhảy sang bootloader**.

---

## 5. Gửi positive response cuối cùng

Nếu <span class="text-blue"><code>DcmDspSessionForBoot</code></span> = <code>_RESPAPP</code>, **App** sẽ gửi positive response cuối cùng sau khi việc chuẩn bị reset thành công.

Nếu việc chuẩn bị reset thất bại, quy trình sẽ bị dừng và **App** gửi negative response.

---

## 6. Lưu trữ điều kiện lập trình (Programming Conditions)

**Gọi <span class="text-orange"><code>Dcm_SetProgConditions()</code></span>:**<br>
Để bảo toàn dữ liệu trước khi nhảy, **Dcm** sẽ gọi hàm này để lưu trữ các thông tin quan trọng (như định danh kết nối, địa chỉ Tester, SID...) vào non-volatile memory hoặc vùng RAM không bị khởi tạo lại sau reset.

**Điều kiện gọi**:
- Nếu <span class="text-blue"><code>DcmDspSessionForBoot</code></span> = <code>_BOOT</code>, hàm này được gọi ngay sau khi gửi thành công NRC 0x78; hoặc sau khi mode switch.
- Nếu <span class="text-blue"><code>DcmDspSessionForBoot</code></span> = <code>_RESPAPP</code>, hàm này được gọi sau positive response cuối cùng.


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

## 7. Thực thi lệnh nhảy (Jump execution)

<span class="text-orange"><code>Dcm_SetProgConditions()</code></span> trả về **E_OK**:  **Dcm** set <span class="text-blue"><code>DcmEcuReset</code></span> = **DCM_EXECUTE** để thực hiện lệnh nhảy thực tế.

<span class="text-orange"><code>Dcm_SetProgConditions()</code></span> trả về **E_NOT_OK**: **Dcm** sẽ không thực hiện reset, không nhảy sang bootloader và trả về **NRC 0x22 (Conditions not correct)** cho Tester.

<details markdown="block">
<summary><span class="text-blue"><code>DcmEcuReset</code></span></summary>

> <strong>Description</strong>: Thể hiện các kiểu ECU reset khác nhau.
>  
> **Return value:**
> <table class="hover-table">  
>   <tbody>  
>    <tr>
>    <td>0</td>
>    <td>DCM_NONE</td>
>    <td></td>
>    </tr>
>    <tr>
>    <td>1</td>
>    <td>DCM_HARD</td>
>    <td>Báo hiệu một lần reset phần cứng hoàn toàn.<br>Tương ứng với Service EcuHardReset (0x11 0x01)</td>
>    </tr>
>    <tr>
>    <td>2</td>
>    <td>DCM_KEYONOFF</td>
>    <td>Chế độ này mô phỏng hành động tắt và bật chìa khóa xe.<br>Tương ứng với yêu cầu keyOffOnReset (0x11 0x02)</td>
>    </tr>  
>    <tr>
>    <td>3</td>
>    <td>DCM_SOFT</td>
>    <td>Báo hiệu một lần reset phần mềm, nơi các biến được khởi tạo lại nhưng phần cứng không bị ngắt điện.<br>Tương ứng với Service EcuSoftReset (0x11 0x03)</td>
>    </tr>
>    <tr>
>    <td>4</td>
>    <td>DCM_JUMPTOBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang OEM Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho OEM.</td>
>    </tr>
>    <tr>
>    <td>5</td>
>    <td>DCM_JUMPTOSYSSUPPLIERBOOTLOADER</td>
>    <td>Chế độ này được kích hoạt khi có yêu cầu chuyển sang System Supplier Bootloader. Nó thường xảy ra khi nhận được dịch vụ DiagnosticSessionControl (0x10) với session được chỉ định cho System Supplier.</td>
>    </tr>
>    <tr>
>    <td>6</td>
>    <td>DCM_EXECUTE</td>
>    <td>Yêu cầu BswM thực hiện lệnh reset thực tế lên phần cứng.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>