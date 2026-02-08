---
title: DSD
parent: DCM
nav_order: 3
---

# Diagnostic Service Dispatcher (DSD)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Giới thiệu

{: .note }
**Diagnostic Service Dispatcher (DSD)** là một submodule trung gian trong module Diagnostic Communication Manager (Dcm), đóng vai trò như một bộ điều phối luồng dữ liệu chẩn đoán. Nó chịu trách nhiệm kiểm tra tính hợp lệ của các yêu cầu chẩn đoán đến và theo dõi tiến trình thực thi của các dịch vụ.

DSD có các vai trò then chốt sau:
- **Tiếp nhận và Điều hướng:** Nhận yêu cầu chẩn đoán từ DSL và chuyển tiếp đến bộ xử lý dữ liệu tương ứng, thường là DSP hoặc các module bên ngoài (External module).
- **Xác thực quyền truy cập:** Kiểm tra xem yêu cầu có hợp lệ trong session hiện tại, security level hiện tại, và có được phép bởi OEM/Supplier hay không.
- **Quản lý phản hồi:** Lắp ráp positive/negative response message và kích hoạt việc truyền tin khi nhận được tín hiệu từ bộ xử lý dữ liệu.
- **Triệt tiêu phản hồi:** Thực hiện logic triệt tiêu phản hồi dựa trên yêu cầu của Tester (SPRMIB) hoặc cấu hình hệ thống.

---

## 2. Tương tác của DSD với các module và submodule khác

{: .note }
DSD hoạt động như một "trạm trung chuyển" dữ liệu.

**Submodule DSL**:
- DSL gọi DSD khi có một thông điệp chẩn đoán mới được công nhận.
- DSD lấy thông tin về session state và security level hiện tại từ DSL để thực hiện kiểm tra điều kiện.
- DSD gửi response message đã hoàn thiện cho DSL để thực hiện truyền dẫn ra mạng.

**Submodule DSP**:
- DSD ủy thác việc xử lý chi tiết các dịch vụ chẩn đoán cho DSP.
- DSP thông báo cho DSD khi quá trình xử lý dịch vụ đã hoàn tất.

**External module**:
- DSD ủy thác việc xử lý chi tiết các dịch vụ chẩn đoán cho các external module.
- DSD gọi các hàm kiểm tra quyền hạn của "App" trước khi cho phép thực thi dịch vụ.



<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSD\DSD_ interacte_with_others_module.png"
    alt="DSD interacte with modules and others submodule"
  />
  <figcaption>DSD interacte with modules and others submodule</figcaption>
</figure>



---

## 3. Chức năng

DSD cung cấp các chức năng sau:


**Kiểm tra Service Identifier (SID) và điều chỉnh thông điệp:**<br>
DSD phân tích byte đầu tiên của thông điệp để xác định SID và đối chiếu với "Service Identifier Table" để kiểm tra xem dịch vụ có được hỗ trợ hay không. Nếu SID không được hỗ trợ, DSD sẽ gửi negative response với NRC 0x11 (Service not supported).

**Xử lý bit triệt tiêu positive response (SPRMIB):**<br>
DSD chịu trách nhiệm kiểm tra SPRMIB trong request messsage; nếu bit này là TRUE, DSD sẽ không gửi positive response. Trước khi chuyển dữ liệu đi xử lý, DSD sẽ thực hiện xóa (masking) bit này khỏi diagnostic message.

**Thực thi chuỗi xác thực (Verification functionality):**<br>
Trước khi cho phép thực thi một dịch vụ, DSD thực hiện một loạt các kiểm tra theo thứ tự bắt buộc:
1. Quyền từ ứng dụng Nhà sản xuất
2. Kiểm tra SID
3. Trạng thái xác thực (Authentication)
4. Phiên chẩn đoán
5. Mức bảo mật
6. Quyền từ ứng dụng Nhà cung cấp\
7. Các quy tắc chế độ (Mode rules).

**Kiểm tra subfunction và định dạng của request message:**<br>
DSD xác thực xem subfunction được yêu cầu có nằm trong cấu hình hay không, nếu không sẽ trả về NRC  (Sub-Function Not Supported); và kiểm tra xem độ dài request message có thoả yêu cầu không, nếu không sẽ trả về NRC 0x13 (Incorrect Message Length or Invalid Format).

**Phân phối diagnostic message đến DSP:**<br>
Sau khi vượt qua các bước xác thực, DSD tìm kiếm chức năng thực thi tương ứng và gọi Service Interpreter (thông dịch dịch vụ) của DSP để xử lý yêu cầu.

**Lắp ráp response message:**<br>
Khi DSP hoàn tất xử lý, DSD sẽ lắp ráp phản hồi từ luồng dữ liệu nhận được. DSD cũng quản lý việc triệt tiêu các negative response nhất định (như NRC 0x11, 0x12, 0x31, 0x7E, 0x7F) trong trường hợp sử dụng Functional Addressing.

**Kích hoạt truyền dẫn (Initiate transmission):**<br>
DSD chuyển response message đã hoàn thiện cho DSL để thực hiện gửi tin qua PduR. Ngay cả khi phản hồi bị triệt tiêu, DSD vẫn phải gọi <span class="text-orange"><code>DspInternal_DcmConfirmation()</code></span> để thông báo cho DSP rằng quá trình xử lý đã kết thúc.

---

## Use case

- [Receive a request message and transmit a positive response message.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/SendPositiveResMsgWithoutCorrespondingReq/" | relative_url }})
- [Receive a request message and suppress a positive response.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/ReceiveReqMsgAndTransmitPositiveResMsg/" | relative_url }})
- [Receive a request message and suppress a negative response.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/ReceiveReqMsgAndSuppressPositiveRes/" | relative_url }})
- [Receive a request message and transmit a negative response message.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/ReceiveReqMsgAndSuppressNegativeRes/" | relative_url }})
- [Send a positive response message without corresponding request.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/ReceiveReqMsgAndTransmitNegativeResMsg/" | relative_url }})
- [Segmented Responses.]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/DCM/DSD/SegmentedResponses/" | relative_url }})