---
title: Manage session state
parent: DSL
nav_order: 12
---

# Manage session state

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Cơ chế quản lý

UDS (ISO 14229-1) định nghĩa nhiều diagnostic session khác nhau:

<table class="hover-table">
  <thead>
    <tr>
      <th>ID</th>
      <th>Session</th>
      <th>Mục đích</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Default Session</td>
      <td>Cung cấp quyền truy cập cho các dịch vụ chẩn đoán cơ bản</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Programming Session</td>
      <td>Cung cấp quyền truy cập cho các dịch vụ liên quan đến flash fimware</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Extended Diagnostic Session</td>
      <td>Cung cấp quyền truy cập cho các dịch vụ chẩn đoán nâng cao</td>
    </tr>
    <tr>
      <td>0x04</td>
      <td>Safety System Diagnostic Session</td>
      <td>Phiên chẩn đoán hệ thống an toàn (opional), dành cho các hệ thống như túi khí hoặc ABS, với quy tắc nghiêm ngặt.</td>
    </tr>
  </tbody>
</table>

**DSL** có trách nhiệm lưu giữ trạng thái (state) của diagnostic session đang hoạt động hiện tại.
Để quản lý biến trạng thái này, DSL cung cấp các interfaces sau:
- <span class="text-orange">Dcm_GetSesCtrlType()</span>: Lấy trạng thái hiện tại.
- <span class="text-orange">DslInternal_SetSesCtrlType()</span>: Thiết lập một session mới.

Trong quá trình init **Dcm**, trạng thái session mặc định được đặt là 0x01 (Default Session).

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\Interfaces_for_manage_session_state.png"
    alt="Interfaces for manage session state"
  />
  <figcaption>Interfaces for manage session state</figcaption>
</figure>

---

## Cơ chế Reset

<span class="text-orange">Dcm_ResetToDefaultSession()</span> cho phép **App** chủ động đưa session hiện tại về lại **Default Session**. Khi function này được gọi, nó sẽ kích hoạt việc chuyển đổi chế độ (mode switch) của ModeDeclarationGroupPrototype <span class="text-blue">DcmDiagnosticSessionControl</span> bằng cách gọi <span class="text-orange">SchM_Switch</span> tương ứng.

Ví dụ thực tế: hệ thống tự động kết thúc một Extended Diagnostic Session khi xe vượt quá một giới hạn tốc độ nhất định.

<details markdown="block">
<summary><span class="text-blue">DcmDiagnosticSessionControl</span></summary>

> <strong>Kind</strong>: ModeDeclarationGroup  
> <strong>Description</strong>: ModeDeclarationGroup đại diện cho các diagnostic sessions khác nhau.  
> <strong>Mode</strong>:  
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>DCM_DEFAULT_SESSION</td>
>    <td>0</td>
>    </tr>
>    <tr>
>    <td>DCM_PROGRAMMING_SESSION</td>
>    <td>1</td>
>    </tr>
>    <tr>
>    <td>DCM_EXTENDED_DIAGNOSTIC_SESSION</td>
>    <td>2</td>
>    </tr>
>    <tr>
>    <td>DCM_SAFETY_SYSTEM_DIAGNOSTIC_SESSION</td>
>    <td>3</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>


<details markdown="block">
<summary><span class="text-orange">SchM_Switch</span></summary>

> <span class="text-orange">SchM_Switch_\<bsnp\>_DcmDiagnosticSessionControl( 
> RTE_MODE_DcmDiagnosticSessionControl__**DCM_DEFAULT_SESSION**)</span>  
>
> Lưu ý: \<bsnp\> là **B**SW **S**cheduler **N**ame **P**refix.  
{: .codeBlock }
</details>