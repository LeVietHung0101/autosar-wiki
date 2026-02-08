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

## 1. Giới thiệu

{: .note }
**Diagnostic Session Layer (DSL)** là một submodule thuộc module Diagnostic Communication Manager (Dcm). DSL hoạt động ở Session Layer và tuân thủ ISO 14229-1, ISO 15765-3.

**DSL** đóng vai trò trung tâm trong việc:
- Đảm bảo luồng dữ liệu liên quan đến các diagnostic requests/responses.
- Giám sát và đảm bảo các tham số thời gian (P2, P2*, S3) của giao thức chẩn đoán.
- Quản lý các trạng thái chẩn đoán, đặc biệt là các diagnostic sessions và các security level.

DSL chỉ xử lý logic chung, không quan tâm đến mạng cụ thể (CAN, LIN, FlexRay, Ethernet). Các phần phụ thuộc mạng (như timing CAN) được xử lý ở lớp dưới (transport layer, qua PduR).

---

## 2. Tương tác của DSL với các module và submodule khác

**Module PduR (PDU Router):**<br>
PduR cung cấp dữ liệu của các yêu cầu chẩn đoán đến; ngược lại, DSL kích hoạt việc gửi các thông điệp phản hồi ra mạng thông qua PduR.

**Module ComM (Communication Manager)**:<br>
DSL đảm bảo các hành vi giao tiếp theo yêu cầu từ ComM (như các chế độ Full-/Silent-/No-Communication).

**Submodule DSD (Diagnostic Service Dispatcher)**:<br>
DSL thông báo cho DSD về các yêu cầu mới nhận được và cung cấp dữ liệu tương ứng. DSD sau đó sẽ kích hoạt DSL khi có phản hồi chẩn đoán sẵn sàng để gửi đi.

**Submodule DSP (Diagnostic Service Processing) và SW-Cs**:<br>
DSL cung cấp khả năng truy cập vào session và security level hiện tại cho DSP và SW-Cs.


<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSL\DSL_interacte_with_others_module.png"
    alt="DSL interacte with modules and others submodule"
  />
  <figcaption>DSL interacte with modules and others submodule</figcaption>
</figure>

---

## 3. Chức năng

DSL cung cấp các chức năng sau.



**Xử lý yêu cầu (Request Handling)**
- [Chuyển tiếp các yêu cầu chẩn đoán từ PduR đến DSD.](Forward_requests_from_PduR_to_DSD/)
- [Concurrent TesterPresent ("keep alive logic").](Concurrent_TesterPresent/)

**Xử lý phản hồi (Response Handling)**
- [Chuyển tiếp các phản hồi từ DSD đến PduR.](Forward_responses_from_DSD_to_PduR/)
- [Đảm bảo thời gian phản hồi tới tester.](Guarantee_timing_to_tester_by_sending_busy_responses/)
- [Hỗ trợ truyền định kỳ (periodic transmission).](Support_of_periodic_transmission/)
- [Hỗ trợ Response On Event (ROE).](Support_of_ROE_transmission/)
- [Hỗ trợ phản hồi phân đoạn (segmented response).](Support_of_segmented_response/)
- [Hỗ trợ phản hồi ResponsePending do App kích hoạt.](Support_of_ResponsePending_response_triggered_by_App/)

**Xử lý mức bảo mật (Security Level Handling)**
- [Quản lý security level.](Manage_security_level/)

**Quản lý trạng thái session (Session State Handling)**
- [Quản lý session state.](Manage_session_state/)
- [Theo dõi các non-default session đang hoạt động.](Keep_track_of_active_nondefault_sessions/)
- [Cho phép thay đổi các tham số timing.](Allow_to_modify_timings/)

**Xử lý giao thức chẩn đoán (Diagnostic Protocol Handling)**
- [Xử lý nhiều diagnostic protocols khác nhau.](Handling_of_different_diagnostic_protocols/)
- [Quản lý tài nguyên.](Manage_resources/)

[**Xử lý chế độ truyền thông (Communication Mode Handling)**](Communication_Mode_Handling/)
- Xử lý các yêu cầu về chế độ truyền thông (Full-/Silent-/No-Communication).
- Thông báo trạng thái diagnostic đang active/inactive
- Cho phép enable/disbale mọi loại diagnostic transmissions.


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
