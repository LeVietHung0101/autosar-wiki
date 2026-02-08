---
title: Manage security level
parent: DSL
nav_order: 11
---

# Manage security level

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Quản lý actived security level

**DSL** phải lưu giữ level của security level đang hoạt động hiện tại.

**DSL** cung cấp các interface sau để tương tác với security level đó:
- <span class="text-orange"><code>Dcm_GetSecurityLevel()</code></span>: get the current active security level.
- <span class="text-orange"><code>DslInternal_SetSecurityLevel()</code></span>: set a new security level.

Trong quá trình init **Dcm**, security level mặc định là **0x00 (DCM_SEC_LEV_LOCKED)**.
Tại một thời điểm, chỉ có duy nhất một security level có thể được kích hoạt.
Với mỗi lần thay đổi security level, **Dcm** phải cập nhật `DcmSecurityAccess` tương ứng.



<details markdown="block">
<summary><span class="text-orange"><code>Dcm_GetSecurityLevel()</code></span></summary>

> <figure><img src="{{ site.baseurl }}\assets\images\DCM\DSL\Dcm_GetSecurityLevel.png"/></figure>
{: .codeBlock }
</details>


<details markdown="block">
<summary><span class="text-orange"><code>DslInternal_SetSecurityLevel()</code></span></summary>

> <figure><img src="{{ site.baseurl }}\assets\images\DCM\DSL\DslInternal_SetSecurityLevel.png"/></figure>
{: .codeBlock }
</details>





<details markdown="block">
<summary><code>DcmSecurityAccess</code></summary>


> ModeDeclarationGroup DcmSecurityAccess {  
> <span class="indent">{</span>  
> <span class="indent2">DCM_SEC_LEV_LOCKED</span>  
> <span class="indent2">DCM_SEC_LEV_1</span>  
> <span class="indent2">...</span>  
> <span class="indent2">DCM_SEC_LEV_63</span>  
> <span class="indent2">}</span>  
> initialMode = DCM_SEC_LEV_LOCKED  
> };  
{: .codeBlock }
</details>



<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\Interfaces_for_manage_security_level.png"
    alt="Interfaces for manage security level"
  />
  <figcaption>Interfaces for manage security level</figcaption>
</figure>




---

## 2. Cơ chế Reset security level về trạng thái khóa (Locked)

**DSL** sẽ thiết lập lại security level về giá trị **0x00 (DCM_SEC_LEV_LOCKED)** khi xảy ra một trong các điều kiện sau:
- Thực hiện chuyển đổi từ một non-default session sang một non-default session khác (bao gồm cả việc chuyển đổi sang chính session đang hoạt động đó).
- Thực hiện chuyển đổi từ một non-default session về lại default session thông qua dịch vụ UDS 0x10 (DiagnosticSessionControl) hoặc do S3Server timeout.

---

## 3. Quy trình khởi tạo (Initialization sequence)

Khi init, đối với mỗi `DcmDspSecurityRow` có bật tính năng đếm số lần thử (`AttemptCounterEnabled`), **Dcm** sẽ gọi <span class="text-orange"><code>Xxx_GetSecurityAttemptCounter()</code></span> để lấy giá trị AttemptCounter hiện tại từ bộ nhớ (thường là non-volatile memory).


Xử lý phản hồi từ **App** thông qua <span class="text-orange"><code>Xxx_GetSecurityAttemptCounter()</code></span>:
- Nếu trả về `E_NOT_OK`: AttemptCounter sẽ được đặt theo giá trị cấu hình `DcmDspSecurityNumAttDelay` của security level tương ứng..
- Nếu trả về `DCM_E_PENDING`: **Dcm** sẽ tạm dừng và tiếp tục thử lại trong chu kỳ kế tiếp của <span class="text-orange"><code>Dcm_MainFunction()</code></span>.


**Giới hạn thời gian:** Nếu đạt đến thời gian tối đa `DcmDspSecurityMaxAttemptCounterReadoutTime` mà chưa lấy được tất cả giá trị AttemptCounter, **Dcm** sẽ hủy các thao tác đang chờ (với `OpStatus` = `DCM_CANCEL`) và sử dụng các giá trị mặc định trong cấu hình cho các security level còn lại.


**Từ chối yêu cầu trong lúc khởi tạo:** Trong khi quy trình lấy AttemptCounter đang diễn ra và chưa hoàn tất, **Dcm** sẽ trả về **NRC 0x22 (ConditionsNotCorrect)** cho bất kỳ yêu cầu **requestSeed** nào của dịch vụ [SecurityAccess (0x27)]({{ "/docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/UDS/SID/27_SecurityAccess/" | relative_url }})


**Kích hoạt thời gian trễ:** Sau khi tất cả AttemptCounter được đọc thành công hoặc thất bại (Xxx_GetSecurityAttemptCounter() trả về giá trị khác `DCM_E_PENDING` hoặc bị huỷ), nếu có ít nhất một AttemptCounter đạt đến giới hạn cho phép (lớn hơn hoặc bằng `DcmDspSecurityNumAttDelay`), **Dcm** sẽ khởi chạy **SecurityDelayTimer** với giá trị lớn hơn `DcmDspSecurityDelayTimeOnBoot` / `DcmDspSecurityDelayTime` của `DcmDspSecurityRow` tương ứng.

---

## 4. Cập nhật bộ đếm số lần thử (AttemptCounter update)

AttemptCounter của một security level cụ thể sẽ được reset khi:
- Thực hiện thành công yêu cầu **sendKey**.
- **SecurityDelayTimer** kết thúc.

**Dcm** phải gọi <span class="text-orange"><code>Xxx_SetSecurityAttemptCounter()</code></span> để thông báo cho ứng dụng mỗi khi giá trị AttemptCounter thay đổi (nếu tính năng `AttemptCounterEnabled` được bật trong cấu hình).