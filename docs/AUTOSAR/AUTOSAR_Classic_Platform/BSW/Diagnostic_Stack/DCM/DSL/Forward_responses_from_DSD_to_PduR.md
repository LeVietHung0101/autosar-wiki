---
title: Forward responses from DSD to PduR
parent: DSL
nav_order: 4
---

# Forward responses from DSD to PduR

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\Interfaces_for_forward_responses_from_DSD_to_PduR.png"
    alt="Interfaces for manage session state"
  />
  <figcaption>Interfaces for manage session state</figcaption>
</figure>

## Trường hợp truyền  dữ liệu thông thường

**DSD** phải yêu cầu **DSL** thực hiện việc truyền các response đến **PduR**.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\DSL_Forward_responses_from_DSD_to_PduR.svg"
    alt="DSL forward responses from DSD to PduR"
  />
  <figcaption>DSL forward responses from DSD to PduR</figcaption>
</figure>

Khi diagnostic response của một <span class="text-blue">DcmDslMainConnection</span> đã sẵn sàng, **DSL** sẽ kích hoạt việc truyền diagnostic response này đến **PduR** bằng cách gọi <span class="text-orange">PduR_DcmTransmit()</span> (với <span class="text-blue">DcmDslProtocolTxPduRef</span> làm định danh <span class="text-blue">PduId</span>).

- Các response được gửi đi với một <span class="text-blue">DcmTxPduId</span> đã được liên kết trong cấu hình với <span class="text-blue">DcmRxPduId</span> (ID mà yêu cầu đã nhận được trước đó).

- Trong <span class="text-orange">PduR_DcmTransmit()</span>, Dcm chỉ cung cấp thông tin về độ dài dữ liệu và thông tin địa chỉ (đối với các kết nối chung - generic connections).

Sau khi **DSL** gọi <span class="text-orange">PduR_DcmTransmit()</span> thành công, **PduR** sẽ gọi <span class="text-orange">Dcm_CopyTxData()</span> để yêu cầu Dcm cung cấp dữ liệu thực tế cần truyền.

Sau khi toàn bộ PDU đã được truyền thành công (hoặc xảy ra lỗi), **PduR** sẽ gọi <span class="text-orange">Dcm_TpTxConfirmation()</span> để thông báo đến **DSL**. **DSL** phải chuyển tiếp kết quả này đến **DSD**.

**DSD** không được phép truyền lại diagnostic response đó, nếu:
- **DSL** truyền dữ liệu đến **PduR** thất bại (<span class="text-orange">PduR_DcmTransmit()</span> thất bại).
- Nhận được xác nhận lỗi từ **PduR** (<span class="text-orange">Dcm_TpTxConfirmation()</span> có lỗi).

> Lưu ý: <span class="text-orange">Dcm_TpTxConfirmation()</span> chỉ được mong đợi sau khi <span class="text-orange">PduR_DcmTransmit()</span> thành công.

---

## Trường hợp truyền dữ liệu định kỳ (Periodic Transmission)

Dcm phải cung cấp toàn bộ dữ liệu (full payload) ngay trong lệnh gọi <span class="text-orange">PduR_DcmTransmit()</span>. Dcm không mong đợi lệnh gọi đến <span class="text-orange">Dcm_CopyTxData()</span>.

Kết quả truyền dữ liệu định kỳ sẽ được thông báo qua <span class="text-orange">Dcm_TxConfirmation()</span>.

---

## Cấu hình không gửi phản hồi

Nếu DcmDslProtocolTx có Multiplicity = 0, Dcm sẽ xử lý yêu cầu chẩn đoán nhận được nhưng không gửi bất kỳ response nào.

> Trong cấu hình, DcmDslProtocolTx có Multiplicity là **0..1**, nghĩa là đối với mỗi DcmDslMainConnection, chỉ được phép tồn tại duy nhất một cấu hình kênh truyền phản hồi.

---

<details markdown="block">
<summary><span class="text-orange">PduR_DcmTransmit()</span></summary>

> <strong>Syntax</strong>: Std_ReturnType <strong>PduR_\<User\:Up\>Transmit</strong> (  
> <span class="indent">PduIdType <strong>TxPduId</strong>,</span>  
> <span class="indent">const PduInfoType* <strong>PduInfoPtr</strong></span>  
> )
>
> <strong>Description</strong>: Yêu cầu truyền một PDU từ Upper layer đến PduR.
>
> <strong>Parameters (in)</strong>
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>TxPduId</td>
>    <td>Id của PDU được transmit.</td>
>    </tr>
>    <tr>
>    <td>PduInfoPtr</td>
>    <td>Pointer trỏ đến PduInfoType chứa:<br>  
>    <strong>- SduDataPtr</strong>: Pointer to the SDU (i.e. payload data) of the PDU<br>
>    <strong>- MetaDataPtr</strong>: Pointer to the meta data (e.g. CAN ID, socket ID, diagnostic addresses)
of the PDU.<br>
>    <strong>- SduLength</strong>: Length of the SDU in bytes.</td>
>    </tr>
>   </tbody>
> </table>
>
> **Return value:** Std_ReturnType
> <table class="hover-table">  
>   <tbody>  
>     <tr>
>    <td>E_OK</td>
>    <td>Yêu cầu truyền đã được chấp nhận.</td>
>    </tr>
>    <tr>
>    <td>E_NOT_OK</td>
>    <td>Yêu cầu truyền không được chấp nhận.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>
