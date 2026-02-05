---
title: Concurrent TesterPresent
parent: DSL
nav_order: 3
---

# Concurrent ”TesterPresent” (”keep alive logic”)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---


## Mô tả cơ chế

**Concurrent "TesterPresent"** là cơ chế cho phép tester gửi các lệnh TesterPresent (SID 0x3E) song song với các physical requests hoặc responses, được gọi là "Keep-Alive-Logic" trong ISO 14229-1. Mục tiêu là để giữ cho session hiện tại không bị ngắt quãng.

Khi DSL nhận được thông báo hoàn tất việc nhận dữ liệu (tức là PduR gọi <span class="text-orange">Dcm_TpRxIndication</span> với result=E_OK) cho một request TesterPresent (0x3E) có subfunction là 0x80 (SPRMIB=1), DSL phải reset session timeout timer (**S3Server**).

Đồng thời, DSL sẽ không chuyển tiếp request này tới DSD để phân tích thêm. Việc bypass (bỏ qua) này giúp Dcm có thể tiếp nhận và xử lý các physical requests tiếp theo ngay lập tức mà không gặp bất kỳ sự chậm trễ nào.

Dcm chỉ coi một request là concurrent request (yêu cầu đồng thời/yêu cầu song song) nếu:
- Dcm nhận request này qua functional address.
- SPRMIB=1.

> Việc tester gửi request TesterPresent (3E 80) qua functional address sẽ giữa

{: .note}
Ngay khi nhận thành công một request message, DSL sẽ chặn (block) <span class="text-blue">DcmPduId</span> tương ứng cho đến khi phản hồi được gửi đi và <span class="text-blue">DcmPduId</span> được giải phóng. Ngoại lệ của **cơ chế chặn PduId** này là **concurrent request**. Nên để duy trì session trong lúc tester chờ phản hồi từ ECU, tester sẽ gửi request TesterPresent (0x3E) như một concurrent request (functional address, SPRMIB=1).

--- 

### Dcm_CopyTxData - Quy trình sao chép dữ liệu truyền

Khi bắt đầu một quá trình truyền data từ Dcm sang PduR, DSL gọi <span class="text-orange">PduR_DcmTransmit()</span> để copy data từ buffer Dcm sang buffer PduR. Nếu độ dài data đã copy sang buffer PduR nhỏ hơn độ dài data thực tế cần truyền, PduR sẽ truyền phần data đã copy trước, sau đó yêu cầu Dcm cung cấp thêm data thông qua <span class="text-orange">Dcm_CopyTxData()</span>.

Một số lưu ý của <span class="text-orange">Dcm_CopyTxData()</span>:
- Giá trị của parameter **availableDataPtr** trong function này không được phép vượt quá số lượng byte còn lại thực tế cần gửi.
- Function này sẽ return **BUFREQ_OK** nếu Dcm copy data thành công vào buffer của PduR được cung cấp thông qua parameter **info** trong function này.
- Nếu function return **BUFREQ_E_NOT_OK**, yêu cầu truyền dữ liệu vẫn chưa kết thúc. Cần phải có một xác nhận cuối cùng (PduR thông báo lỗi qua <span class="text-orange">Dcm_TpTxConfirmation()</span>) để hoàn thành dịch vụ hiện tại trước khi có thể bắt đầu một quá trình truyền mới.
- Function này phải được gọi trong ngữ cảnh ngắt (interrupt context).

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

---

### Dcm_TpTxConfirmation - Xác nhận truyền dữ liệu

Đây là bước cuối cùng để hoàn tất quy trình truyền một PDU.

Khi <span class="text-orange">Dcm_TpTxConfirmation()</span> được gọi, Dcm thực hiện việc mở khóa (unlock) bộ đệm truyền (transmit buffer).

Dcm phải ngừng các quy trình xử lý lỗi thời gian, bao gồm lỗi quá thời gian bộ đệm trang (Page buffer timeout) và các lỗi quá thời gian phản hồi server (P2ServerMax/P2*ServerMax timeout).

<span class="text-orange">Dcm_TpTxConfirmation()</span> phải được gọi trong ngữ cảnh ngắt (interrupt context) (ví dụ: từ transmit interrupt).

> Lưu ý: Đối với mạng FlexRay, do đặc thù không bắt buộc có transmit interrupt, ý nghĩa của xác nhận này (dữ liệu đã vào bộ đệm của controller hay đã thực sự lên mạng) sẽ tùy thuộc vào khả năng của FlexRay communication controller và cấu hình của FlexRay Interface.