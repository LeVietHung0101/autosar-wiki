---
title: Protocol Configuration 
parent: DSL
nav_order: 1
---

# Protocol Configuration

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Phân loại yêu cầu và Định danh PDU (DcmPduId)

Dcm cho phép thiết lập các <span class="text-blue">DcmPduId</span> khác nhau cho các ứng dụng giao tiếp chẩn đoán khác nhau. Ví dụ cụ thể trong cấu hình cho OBD và UDS:

<table class="hover-table">
  <tbody>
    <tr>
      <td>OBD DcmRxPduId</td>
      <td>Dùng để tiếp nhận các yêu cầu OBD.</td>
    </tr>
    <tr>
      <td>OBD DcmTxPduId</td>
      <td>Dùng để truyền các phản hồi OBD.</td>
    </tr>
    <tr>
      <td>UDS phys DcmRxPduId</td>
      <td>Dùng để nhận các yêu cầu UDS có địa chỉ vật lý (nhắm đến một ECU cụ thể).</td>
    </tr>
    <tr>
      <td>UDS func DcmRxPduId</td>
      <td>Dùng để nhận các yêu cầu UDS có địa chỉ chức năng (gửi cho một nhóm ECU).</td>
    </tr>
    <tr>
      <td>UDS DcmTxPduId</td>
      <td>Dùng để truyền các phản hồi UDS</td>
    </tr>
  </tbody>
</table>

**Address type** (physical/functional addressing) được cấu hình riêng biệt cho từng <span class="text-blue">DcmRxPduId</span> thông qua tham số cấu hình <span class="text-blue">DcmDslProtocolRx</span>. Cách tiếp cận này luôn khả thi vì hệ thống luôn sử dụng các giá trị <span class="text-blue">DcmRxPduId</span> khác nhau cho việc nhận functional và physical, bất kể định dạng địa chỉ của Transport Layer là extended addressing hay normal addressing.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\Examples_of_protocol_configuration_with_focus_on_buffer_DcmPduId_settings.png"
    alt="Ví dụ về cấu hình giao thức tập trung vào config buffer/DcmPduId"
  />
  <figcaption>Ví dụ về cấu hình giao thức tập trung vào config buffer/DcmPduId</figcaption>
</figure>

---

## Buffer Configuration

**Buffer riêng cho Functional request**: Đề xuất sử dụng một buffer nhận riêng cho các yêu cầu chức năng. Điều này nhằm hỗ trợ logic duy trì kết nối (keep-alive logic) thông qua các service TesterPresent (3E 80) được gửi bằng functional addressing.

**Buffer riêng cho OBD request**: Khuyến nghị sử dụng một buffer nhận riêng cho các OBD request để hỗ trợ tốt hơn tính năng chuyển đổi giao thức (protocol switch).

**Chia sẻ buffer truyền (Tx Buffer)**: Dcm cho phép cả hai giao thức (UDS và OBD) dùng chung một buffer truyền dữ liệu để tiết kiệm tài nguyên hệ thống.

---

## Connection Dependencies

Việc cấu hình các tham số nhận và truyền trong <span class="text-blue">DcmDslProtocol</span> phải tuân thủ các ràng buộc về số lượng trên mỗi <span class="text-blue">DcmDslMainConnection</span>:

<table class="hover-table">
  <thead>
    <tr>
      <th></th>
      <th>Quan hệ với DcmDslMainConnection</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Physical addressing</td>
      <td>1:1 (hoặc 1:0)</td>
      <td>một tham chiếu DcmDslProtocolRxPduRef kiểu vật lý chỉ được phép tồn tại tối đa một lần trên mỗi DcmDslMainConnection.</td>
    </tr>
    <tr>
      <td>Functional addressing</td>
      <td>1:n</td>
      <td>cho phép một tham chiếu DcmDslProtocolRxPduRef kiểu chức năng tồn tại nhiều lần trên mỗi DcmDslMainConnection.</td>
    </tr>
    <tr>
      <td>Giao thức truyền (Tx)</td>
      <td>1:1 (hoặc 1:0)</td>
      <td>Tham số DcmDslProtocolTx chỉ được phép tồn tại duy nhất một lần trên mỗi DcmDslMainConnection. Nếu cấu hình này không tồn tại (multiplicity = 0), Dcm sẽ xử lý yêu cầu nhận được mà không gửi lại phản hồi.</td>
    </tr>
  </tbody>
</table>

<details markdown="block">
<summary><span class="text-blue">DcmDslProtocol</span></summary>

> <strong>Container</strong>: DcmDslProtocol  
> <strong>Description</strong>: container chứa cấu hình cho các diagnostic protocol được dùng trong Dcm.  
{: .codeBlock }
</details>

<details markdown="block">
<summary><span class="text-blue">DcmDslMainConnection</span></summary>

> <strong>Container</strong>: DcmDslMainConnection  
> <strong>Description</strong>: container chứa cấu hình cho main connection của diagnostic protocol.  
> Ngoài ra, nó có thể chứa các tham chiếu đến các kết nối ResponseOnEvent (ROE) và Periodic (định kỳ) nếu loại giao thức (protocol type) hoặc loại truyền giao thức (protocol transmission type) yêu cầu chúng.  
{: .codeBlock }
</details>