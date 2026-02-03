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
- Chuyển tiếp các yêu cầu chẩn đoán từ PduR đến DSD.
- Hỗ trợ TesterPresent ("keep alive logic").

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
    - Tester có thể timeout.
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
