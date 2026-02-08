---
title: DSP
parent: DCM
nav_order: 4
---

# Diagnostic Service Processing (DSP)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Giới thiệu

{: .note }
**Diagnostic Service Processing (DSP)** là một submodule thuộc module Diagnostic Communication Manager (Dcm). Nó chịu trách nhiệm **xử lý các yêu cầu diagnostic service thực tế** (hoặc các Subservices) sau khi yêu cầu đó đã được xác thực bởi DSD.

**DSP** đóng vai trò là bộ não thực thi logic của các diagnostic service với các nhiệm vụ chính:
- **Phân tích yêu cầu:** Phân tích nội dung request message nhận được từ DSD.
- **Kiểm tra tính hợp lệ:** Kiểm tra định dạng message, độ dài và xác nhận xem subfunction được chỉ định có được hỗ trợ hay không.
- **Thực thi tác vụ:** Gọi các hàm cần thiết từ các module BSW khác hoặc các SW-C thông qua RTE để thực hiện lệnh chẩn đoán.
- **Chuẩn bị dữ liệu phản hồi:** Tập hợp các dữ liệu cần thiết và lắp ráp nội dung response message.

## 2. Tương tác của DSP với các module và submodule khác

**DSD:**<br>
DSD ủy thác việc xử lý dịch vụ cho DSP sau khi kiểm tra quyền truy cập. Khi DSP hoàn tất, nó sẽ thông báo cho DSD để bắt đầu quá trình lắp ráp và truyền phản hồi. DSD cũng gửi xác nhận truyền tin thành công hay thất bại ngược lại cho DSP.

**DSL:**<br>
DSL cung cấp cho DSP thông tin về session state và security level hiện tại để DSP thực hiện các bước kiểm tra logic liên quan.

**DEM (Diagnostic Event Manager):**<br>
DSP truy xuất thông tin từ bộ nhớ lỗi (fault memory) thông qua DEM để trả lời các yêu cầu của Tester (ví dụ: dịch vụ 0x19 - Read DTC Information).

**SW-Cs và RTE:**<br>
DSP yêu cầu dữ liệu hoặc trạng thái chức năng từ các SW-C thông qua các port-interface. Nó ủy quyền thực thi các Routine (dịch vụ 0x31 "Routine Control") hoặc IO-Control (dịch vụ 0x2F "Input Output Control By Identifier") cho các SW-C.

**Các module BSW khác:**<br>
DSP tương tác trực tiếp với **NvM** (để truy cập non-volatile memory), **IoHwAb** (để kiểm soát tín hiệu ECU), **Csm** và **KeyM** (để xử lý các tác vụ bảo mật và chứng chỉ).




<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSP\DSP_ interacte_with_others_module.png"
    alt="DSP interacte with modules and others submodule"
  />
  <figcaption>DSP interacte with modules and others submodule</figcaption>
</figure>


---

## 4. Các chức năng của DSP

Các chức năng quan trọng của DSP được chia thành các nhóm sau:

**Xử lý UDS service và OBD service:**<br>
Triển khai đầy đủ logic để hỗ trợ các:
- UDS service theo tiêu chuẩn ISO 14229-1
- OBD service ($01 đến $0A) nhằm đáp ứng các quy định về khí thải.

Xem danh sách service tại [SID]({{ "docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/UDS/SID/" | relative_url }}).


**Quản lý chế độ chẩn đoán (Diagnostic Mode Management):**<br>
DSP đóng vai trò là **người quản lý chế độ** (mode manager) cho các nhóm khai báo chế độ như: `DcmDiagnosticSessionControl`, `DcmEcuReset`, `DcmSecurityAccess`, và `DcmControlDTCSetting`.

**Lắp ráp response message:**<br>
DSP chuẩn bị nội dung dữ liệu phản hồi (không bao gồm SID vì byte này do DSD quản lý) và xác định độ dài phản hồi.

**Xử lý NRC:**<br>
Khi một API hoặc tác vụ thất bại, DSP sẽ kích hoạt negative response với NRC tương ứng (ví dụ: NRC 0x10 - generalReject).

**Hỗ trợ gọi không đồng bộ (Asynchronous call):**<br>
DSP có khả năng xử lý các lời gọi hàm tốn nhiều thời gian bằng cách sử dụng trạng thái `PENDING` và gọi lại trong các chu kỳ <span class="text-orange"><code>Dcm_MainFunction()</code></span> tiếp theo cho đến khi hoàn tất.

**Xác thực và Bảo mật:**<br>
Thực hiện quy trình xác thực dựa trên chứng chỉ (dịch vụ 0x29 "Authentication") thông qua việc phối hợp với **Csm** và **KeyM**.
