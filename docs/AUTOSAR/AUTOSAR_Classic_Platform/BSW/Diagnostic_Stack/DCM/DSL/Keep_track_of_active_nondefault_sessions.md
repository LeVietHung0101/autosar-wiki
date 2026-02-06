---
title: Keep track of active nondefault sessions
parent: DSL
nav_order: 14
---

# Keep track of active nondefault sessions
*Theo dõi các non-default session đang hoạt động.*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Cơ chế reset session khi hết timeout

Khi một non-default session đang hoạt động và S3Server đạt giới hạn mà không nhận được bất kỳ diagnostic request nào, **DSL** phải thực hiện:
- Thiết lập lại trạng thái về DefaultSession (0x01) bằng <span class="text-orange">Dcm_ResetToDefaultSession()</span>.
- Function trên sẽ kích hoạt việc chuyển đổi chế độ (mode switch) cho ModeDeclarationGroupPrototype <span class="text-blue">DcmDiagnosticSessionControl</span> bằng cách gọi hàm <span class="text-orange">SchM_Switch</span> tương ứng.


<details markdown="block">
<summary><span class="text-blue">S3Server</span></summary>

> <table class="hover-table">
>   <thead>
>    <tr>
>    <th>Protocol timing parameter</th>
>    <th>Description</th>
>    <th>Default value</th>
>    </tr>
>   </thead>
>   <tbody>
>    <tr>
>    <td>S3Server</td>
>    <td>Bộ định thời session (session timeout). Khi ECU đang ở non-default session, nếu không nhận được diagnostic request nào trong khoảng thời gian S3Server này, ECU sẽ tự động quay về default session.
>    </td>
>    <td>5s</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>


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

---

## 2. Logic vận hành S3Server timeout timer

Quy trình start/stop S3Server timeout timer được quy định như sau:

### Các trường hợp Bắt đầu (Reset và đếm lại từ đầu):
- Khi hoàn tất việc gửi response cuối cùng hoặc có thông báo lỗi truyền dữ liệu (<span class="text-orange">Dcm_TpTxConfirmation()</span> có lỗi).
- Khi hoàn tất một hành động được yêu cầu trong trường hợp **không cần gửi response** (ví dụ: SPRMIB=1, request được gửi bằng Functional Addressing) hoặc **không được phép gửi response** (ví dụ: DcmDslProtocolTx có Multiplicity = 0).
- Khi có thông báo lỗi trong quá trình nhận multi-frame request qua <span class="text-orange">Dcm_TpRxIndication()</span>.

> *Trong cấu hình, DcmDslProtocolTx có Multiplicity là **0..1**, nghĩa là đối với mỗi DcmDslMainConnection, chỉ được phép tồn tại tối đa một cấu hình kênh truyền phản hồi.*

### Các trường hợp Dừng:
- Khi bắt đầu nhận một multi-frame request.
- Khi tiếp nhận một single-frame request.

> *PduR thông báo Dcm bắt đầu nhận PDU bằng việc gọi <span class="text-orange">Dcm_StartOfReception</span>.*





<details markdown="block">
<summary><span class="text-orange">Dcm_TpRxIndication()</span></summary>

> **Syntax:**  
> BufReq_ReturnType **Dcm_TpRxIndication**(  
> <span class="indent">PduIdType <strong>id</strong>,</span>  
> <span class="indent">Std_ReturnType <strong>result</strong></span>  
> )  
>  
> <strong>Description</strong>: function này được gọi sau khi nhận được I-PDU thông qua Transport Protocol API, và trả về kết quả cho biết quá trình truyền có thành công hay không.
>
> <strong>Parameters (in)</strong>
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>id</td>
>    <td>Id của received I-PDU</td>
>    </tr>
>    <tr>
>    <td>result</td>
>    <td>Kết quả của quá trình truyền.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>





<details markdown="block">
<summary><span class="text-orange">Dcm_StartOfReception()</span></summary>

> **Syntax:**  
> BufReq_ReturnType **Dcm_StartOfReception**(  
> <span class="indent">PduIdType <strong>id</strong>,</span>  
> <span class="indent">const PduInfoType* <strong>info</strong>,</span>  
> <span class="indent">PduLengthType <strong>TpSduLength</strong>,</span>  
> <span class="indent">PduLengthType* <strong>bufferSizePtr</strong></span>  
> )  
>  
> <strong>Description</strong>: Function này được gọi ở đầu việc nhận một N-SDU. N-SDU có thể được phân mảnh thành nhiều N-PDUs (FF với một hoặc nhiều CF theo sau) hoặc có thể bao gồm một N-PDU duy nhất (SF). Dịch vụ phải cung cấp kích thước buffer tối đa hiện có khi được gọi với TpSduLength bằng 0.
>
> <strong>Parameters (in)</strong>
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>id</td>
>    <td>Id của I-PDU</td>
>    </tr>
>    <tr>
>    <td>info</td>
>    <td>Con trỏ đến PduInfoType structure chứa <strong>payload data</strong> (không chứa thông tin protocol) và <strong>payload length</strong> của FF (hoặc SF) của việc nhận I-PDU giao thức vận chuyển, và <strong>MetaData</strong> liên quan đến PDU này. Nếu không có data của FF (hoặc SF) hoặc MetaData khả dụng, tham số này được đặt thành NULL_PTR.</td>
>    </tr>
>    <tr>
>    <td>TpSduLength</td>
>    <td>Tổng độ dài của N-SDU sẽ nhận.</td>
>    </tr>
>   </tbody>
> </table>
>
> <strong>Parameters (out)</strong>
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>bufferSizePtr</td>
>    <td>Available receive buffer trong module nhận. Tham số này sẽ được sử dụng để tính toán BlockSize (BS) trong mô-đun giao thức vận chuyển.</td>
>    </tr>
>   </tbody>
> </table>
>
> **Return value:** BufReq_ReturnType
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>BUFREQ_OK</td>
>    <td>Kết nối đã được chấp nhận.<br>
>    <strong>bufferSizePtr</strong> chỉ ra buffer nhận khả dụng; việc nhận tiếp tục.<br>
>    Nếu không có buffer với kích thước được yêu cầu khả dụng, kích thước buffer nhận là 0 phải được chỉ ra bởi <strong>bufferSizePtr</strong>.
>    </td>
>    </tr>
>    <tr>
>    <td>BUFREQ_E_NOT_OK</td>
>    <td>Kết nối đã bị từ chối; việc nhận bị hủy bỏ. <strong>bufferSizePtr</strong> vẫn không thay đổi.</td>
>    </tr>
>    <tr>
>    <td>BUFREQ_E_OVFL</td>
>    <td>Không thể cung cấp buffer với độ dài yêu cầu; việc nhận bị hủy bỏ. <strong>bufferSizePtr</strong> vẫn không thay đổi.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>