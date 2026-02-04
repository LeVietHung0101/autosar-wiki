---
title: DSL
parent: DCM
nav_order: 2
---

# Diagnostic Session Layer (DSL)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Giới thiệu

DSL cung cấp các chức năng:
- Session handling (ISO14229-1, ISO 15765-3)
- Application layer timing handling (ISO14229-1, ISO 15765-3)
- Specific response behavior (ISO14229-1, ISO 15765-3).
- Quản lý trạng thái xác thực cho mỗi kết nối chẩn đoán (ISO 14229-1:2018)
    - Cung cấp trạng thái xác thực cho mỗi kết nối
    - Quản lý chuyển đổi trạng thái xác thực

DSL chỉ xử lý logic chung, không quan tâm đến mạng cụ thể (CAN, LIN, FlexRay, Ethernet). Các phần phụ thuộc mạng (như timing CAN) được xử lý ở lớp dưới (transport layer, qua PduR).

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\SubmodulesOfDcm.png"
    alt="Khả năng tương tác giữa các submodule trong Dcm"
  />
  <figcaption>Khả năng tương tác giữa các submodule trong Dcm</figcaption>
</figure>

---

## Chức năng

DSL (Diagnostic Service Layer) cung cấp các chức năng sau.


**Xử lý yêu cầu (Request Handling)**
- [Chuyển tiếp các yêu cầu chẩn đoán từ PduR đến DSD.](#chuyển-tiếp-các-yêu-cầu-chẩn-đoán-từ-pdur-đến-dsd)
- [Hỗ trợ Concurrent TesterPresent ("keep alive logic").](#hỗ-trợ-concurrent-testerpresent-keep-alive-logic)

**Xử lý phản hồi (Response Handling)**
- Chuyển tiếp các phản hồi từ DSD đến PduR.
- Đảm bảo thời gian phản hồi tới tester.
- Hỗ trợ truyền định kỳ (periodic transmission).
- Hỗ trợ Response On Event (ROE).
- Hỗ trợ phản hồi phân đoạn (segmented response).
- Hỗ trợ phản hồi ResponsePending do App kích hoạt.

**Xử lý mức bảo mật (Security Level Handling)**
- Quản lý security level.

**Quản lý trạng thái session (Session State Handling)**
- Quản lý session state.
- Theo dõi các non-default session đang hoạt động.
- Cho phép thay đổi các tham số timing.

**Xử lý giao thức chẩn đoán (Diagnostic Protocol Handling)**
- Xử lý nhiều diagnostic protocols khác nhau.
- Quản lý tài nguyên.

**Xử lý chế độ truyền thông (Communication Mode Handling)**
- Xử lý các yêu cầu về chế độ truyền thông (Full- / Silent- / No-Communication).
- Thông báo trạng thái diagnostic đang active/inactive
- Cho phép enable/disbale mọi loại diagnostic transmissions.

---

## Chuyển tiếp các yêu cầu chẩn đoán từ PduR đến DSD.

### Quy trình

**1. Bắt đầu tiếp nhận**: Khi một yêu cầu chẩn đoán mới bắt đầu trên một <span class="text-blue">DcmDslProtocolRxPduId</span>, PduR sẽ gọi <span class="text-orange">Dcm_StartOfReception()</span> để thông báo cho Dcm về data size sẽ nhận và cung cấp data của first frame (FF) hoặc single frame (SF).

**2. Kiểm tra và chấp nhận**: Dcm có quyền từ chối tiếp nhận nếu data size vượt quá buffer size hoặc dịch vụ yêu cầu không khả dụng (not available). Nếu <span class="text-blue">TpSduLength</span> = 0, Dcm sẽ trả về lỗi BUFREQ_E_NOT_OK vì không hỗ trợ streaming protocol.

**3. Sao chép dữ liệu**: Sau khi chấp nhận, PduR gọi <span class="text-orange">Dcm_CopyRxData()</span> để yêu cầu Dcm sao chép data từ buffer của PduR vào buffer của Dcm.

**4. Hoàn tất tiếp nhận**: Khi việc nhận data kết thúc (<span class="text-orange">Dcm_StartOfReception()</span> thành công), PduR gọi <span class="text-orange">Dcm_TpRxIndication()</span> để thông báo kết quả cho Dcm.

**5. Điều kiện chuyển tiếp tới DSD**: DSL chỉ chuyển tiếp data nhận được tới DSD sau khi <span class="text-orange">Dcm_TpRxIndication()</span> được gọi với tham số result = E_OK. Nếu kết quả trả về khác E_OK, Dcm sẽ không phân tích data trong buffer vì không xác định được phần nào là data hợp lệ.

<details markdown="block">
<summary><span class="text-blue">DcmDslProtocolRxPduId</span></summary>

> <strong>Description</strong>: Id của PDU được sử dụng trong kênh nhận (reception channel).
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

<details markdown="block">
<summary><span class="text-orange">Dcm_CopyRxData()</span></summary>

> **Syntax:**  
> BufReq_ReturnType **Dcm_CopyRxData**(  
> <span class="indent">PduIdType <strong>id</strong>,</span>  
> <span class="indent">const PduInfoType* <strong>info</strong>,</span>  
> <span class="indent">PduLengthType* <strong>bufferSizePtr</strong></span>  
> )  
>  
> <strong>Description</strong>: function này cung cấp received data của một I-PDU segment (N-PDU) từ upper layer.
>
> <strong>Parameters (in)</strong>
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>id</td>
>    <td>Id của received I-PDU</td>
>    </tr>
>    <tr>
>    <td>info</td>
>    <td>Cung cấp source buffer (SduDataPtr) và số byte cần copy (SduLength).<br>SduLength = 0 có thể được sử dụng để truy vấn (query) dung lượng buffer hiện có (available) trong module layer trên. Trong trường hợp này, SduDataPtr có thể là NULL_PTR.</td>
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
>    <td>Available receive buffer sau khi copy data.</td>
>    </tr>
>   </tbody>
> </table>
>
> **Return value:** BufReq_ReturnType
> <table class="hover-table">
>   <tbody>
>    <tr>
>    <td>BUFREQ_OK</td>
>    <td>Copy data thành công.</td>
>    </tr>
>    <tr>
>    <td>BUFREQ_E_NOT_OK</td>
>    <td>Copy data thất bại do có lỗi xảy ra.</td>
>    </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>


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
<summary>Streaming protocol</summary>

> **Streaming protocol** là một loại giao thức mạng được thiết kế để truyền tải dữ liệu đa phương tiện (như video, audio hoặc nội dung tương tác) theo cách liên tục và thời gian thực, mà không cần tải toàn bộ tệp dữ liệu trước khi phát. Thay vì tải xuống toàn bộ file như trong tải thông thường, streaming cho phép dữ liệu được gửi dưới dạng các gói nhỏ (packets) và phát ngay lập tức, giúp giảm độ trễ và tiết kiệm băng thông.  
> Protocol này được sử dụng trong các nền tảng như Netflix, YouTube, Spotify, hoặc các cuộc gọi video (Zoom, Google Meet).  
{: .codeBlock }
</details>


<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\CanTpToDcmTpPduReception.png"
    alt="Quá trình truyền PDU từ CanTp đến Dcm thông qua PduR"
  />
  <figcaption>Quá trình truyền PDU từ CanTp đến Dcm thông qua PduR</figcaption>
</figure>


---

### Cơ chế chặn PduId (Blocking Mechanism)

Ngay khi nhận thành công một request message (result = E_OK), DSL sẽ chặn (block) <span class="text-blue">DcmPduId</span> tương ứng cho đến khi phản hồi được gửi đi và <span class="text-blue">DcmPduId</span> được giải phóng.

Trong quá trình xử lý request này, không có yêu cầu nào khác cùng kết nối (<span class="text-blue">DcmDslConnection</span>) có thể được tiếp nhận, ngoại trừ các yêu cầu [Concurrent TesterPresent](#hỗ-trợ-concurrent-testerpresent-keep-alive-logic).

<details markdown="block">
<summary><span class="text-blue">DcmDslConnection</span></summary>

> <strong>Container</strong>: DcmDslConnection  
> <strong>Description</strong>: Container này chứa cấu hình của communication channel cho một protocol cụ thể.
> Lưu ý: nó cho phép giao tiếp với nhiều tester, do đó có thể cấu hình nhiều kết nối cho một protocol.
{: .codeBlock }
</details>

---

### Quản lý đa tester (Multi-client handling)

Dcm gán các additional protocol resources (tài nguyên giao thức bổ sung) cho mỗi <span class="text-blue">DcmDslProtocolRow</span> với buffer riêng, cho phép xử lý các yêu cầu song song trong default session.

Nếu Dcm nhận được yêu cầu thứ hai trong khi đang xử lý một yêu cầu khác, việc xử lý sẽ phụ thuộc vào cấu hình <span class="text-blue">DcmDslDiagRespOnSecondDeclinedRequest</span>.

<details markdown="block">
<summary><span class="text-blue">DcmDslDiagRespOnSecondDeclinedRequest</span></summary>

> <strong>Parameter</strong>: DcmDslDiagRespOnSecondDeclinedRequest  
> <strong>Description</strong>: Xác định cách phản hồi khi nhận được yêu cầu thứ hai (ClientB) không thể xử lý được (ví dụ: do đánh giá mức độ ưu tiên).
> - TRUE : Trả về NRC 0x21 (BusyRepeatRequest).
> - FALSE: Không phản hồi
{: .codeBlock }
</details>

---

### Xử lý địa chỉ và kết nối chung (Generic Connections)

Đối với các kết nối chung (như chẩn đoán qua IP hoặc FlexRay), source address của tester được cung cấp qua <span class="text-blue">MetaData</span> trong <span class="text-orange">Dcm_StartOfReception()</span> và phải được Dcm lưu trữ để sử dụng cho phản hồi và phát hiện các yêu cầu khác từ tester này.

---

## Hỗ trợ Concurrent TesterPresent ("keep alive logic")







<!-- 



DSL cung cấp các chức năng sau:
- Session handling (Quản lý phiên chẩn đoán).
- Application layer timing handling (Quản lý timing tầng ứng dụng).
- Specific response behavior (Hành vi phản hồi đặc thù).
- Authentication state handling per diagnostic connection (Quản lý trạng thái xác thực cho từng kết nối).

&#x2BA9; DSL đảm bảo ECU giao tiếp với tester một cách đúng chuẩn, đúng thời gian, đúng quyền hạn và an toàn.


### Session handling

DSL chịu trách nhiệm:
- Theo dõi session hiện tại của mỗi kết nối chẩn đoán.
- Kiểm soát việc chuyển đổi session.
- Đảm bảo các dịch vụ UDS chỉ được phép thực hiện trong đúng session.

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

---

### Application layer timing handling

DSL có nhiệm vụ:
- Giám sát các timeout timer (P2, P2*, S3) trong quá trình xử lý request/response.
- Quyết định khi nào cần gửi:
    - Phản hồi bình thường
    - Phản hồi chờ (Response Pending – NRC 0x78)
- Đảm bảo ECU không vi phạm timing theo tiêu chuẩn; nếu không đúng timing:
    - tester có thể timeout.
    - Giao tiếp chẩn đoán trở nên không ổn định hoặc không tương thích tiêu chuẩn.

---

### Specific response behavior

UDS yêu cầu ECU phải phản hồi theo những quy tắc rất cụ thể, ví dụ:
- Khi nào gửi Negative Response Code (NRC).
- Trường hợp nào được phép im lặng (suppress response).
- Cách xử lý request không hợp lệ hoặc không được hỗ trợ.

DSL đảm nhiệm:
- Chuẩn hóa cách tạo response cho mọi dịch vụ.
- Đảm bảo phản hồi đúng định dạng, đúng mã lỗi.
- Giữ tính nhất quán giữa các service chẩn đoán khác nhau

---

### Authentication state handling per diagnostic connection

DSL phải:
- Duy trì trạng thái xác thực riêng cho từng kết nối chẩn đoán *(ví dụ: chưa xác thực, đang xác thực, đã xác thực)*.
- Quản lý chuyển đổi trạng thái xác thực *(ví dụ: sau khi xác thực thành công thì cho phép truy cập dịch vụ bảo mật)*.
 -->
