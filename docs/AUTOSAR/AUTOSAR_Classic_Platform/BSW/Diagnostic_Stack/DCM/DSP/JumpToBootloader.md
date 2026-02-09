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

## 1. Use Cases

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

## 2. Cơ chế kích hoạt và chuẩn bị

**Kích hoạt chuyển đổi chế độ:** Khi nhận dịch vụ Session Control (0x10) với diagnostic session yêu cầu nhảy sang bootloader (thông qua <span class="text-blue"><code>DcmDspSessionForBoot</code></span>), **Dcm** sẽ chuẩn bị bằng cách set:
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


**Thông báo cho BswM:** Việc chuyển đổi chế độ này nhằm thông báo cho module **BswM** chuẩn bị các điều kiện cần thiết để thực hiện nhảy sang bootloader.


**Từ chối yêu cầu:** Trong quá trình xử lý reset để nhảy sang bootloader, **Dcm** phải **bỏ qua mọi yêu cầu chẩn đoán khác**. Nếu việc chuyển đổi chế độ không thể thực hiện, **Dcm** sẽ trả về negative response với **NRC 0x22 (Conditions not correct)**.

---

## 3. Quản lý phản hồi NRC 0x78 và tham số thời gian

**Gửi NRC 0x78:** Nếu <span class="text-blue"><code>DcmSendRespPendOnRestart</code></span> được đặt là TRUE, **Dcm** phải kích hoạt truyền negative response với  NRC 0x78 trước khi thực hiện reset. Việc này giúp Tester nạp lại bộ định thời **P2*** để duy trì kết nối.

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



**Xử lý lỗi truyền tin:** Nếu phản hồi NRC 0x78 không được gửi thành công, **Dcm** sẽ hủy yêu cầu hiện tại và **không thực hiện việc nhảy sang bootloader**.

---

## 4. Lưu trữ điều kiện lập trình (Programming Conditions)

**Gọi hàm Dcm_SetProgConditions:** Để bảo toàn dữ liệu trước khi nhảy, **Dcm** sẽ gọi hàm <span class="text-orange"><code>Dcm_SetProgConditions()</code></span>. 
- Nếu cấu hình yêu cầu **App** phản hồi (`DCM_OEM_BOOT` hoặc `DCM_SYS_BOOT`), hàm này được gọi sau khi gửi xong NRC 0x78.
- Nếu **App** gửi phản hồi cuối cùng (`RESPAPP`), hàm này được gọi sau khi phản hồi cuối cùng đã được gửi thành công.

**Vai trò:** Hàm này cho phép lưu trữ các thông tin quan trọng (như định danh kết nối, địa chỉ Tester, SID...) vào non-volatile memory hoặc vùng RAM không bị khởi tạo lại sau reset.


---

## 5. Thực thi lệnh nhảy (Jump execution)

**Kích hoạt EXECUTE:** Chỉ khi hàm <span class="text-orange"><code>Dcm_SetProgConditions()</code></span> trả về **E_OK**, **Dcm** mới kích hoạt chuyển đổi chế độ <span class="text-blue"><code>DcmEcuReset</code></span> sang **DCM_EXECUTE** để thực hiện lệnh nhảy thực tế.

**Hủy bỏ:** Nếu hàm <span class="text-orange"><code>Dcm_SetProgConditions()</code></span> trả về **E_NOT_OK**, **Dcm** sẽ không thực hiện reset, không nhảy sang bootloader và trả về **NRC 0x22 (Conditions not correct)** cho Tester.



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