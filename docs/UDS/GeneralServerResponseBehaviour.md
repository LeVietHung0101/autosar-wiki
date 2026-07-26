---
title: General server response behaviour
parent: UDS
nav_order: 4
---

<h1>General server response behaviour</h1>

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# General server response behaviour

Khi Server nhận được một Diagnostic Request, Server sẽ thực hiện luồng xác thực tổng quát trước khi đi vào xử lý logic cụ thể của từng dịch vụ (SID). Đây là quy trình nền tảng áp dụng cho hầu hết các dịch vụ UDS trong ISO 14229-1.

Các bước xác thực được chia thành 3 loại:
- **Mandatory**: các xác thực bắt buộc theo ISO 14229-1.
- **Optional**: các xác thực tùy chọn mà ECU có thể triển khai.
- **Manufacturer/Supplier Specific**: các xác thực do nhà sản xuất hoặc nhà cung cấp tự định nghĩa.

Bảng sau mô tả hành vi phản hồi tổng quát của Server. Nếu một bước xác thực thất bại, Server sẽ phản hồi với NRC tương ứng. Ngược lại, server sẽ thực hiện bước xác thực tiếp theo.

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Server is busy?</td>
      <td>-</td>
      <td>NRC 0x21</td>
      <td>-</td>
      <td>Busy Repeat Request</td>
    </tr>
    <tr>
      <td>Manufacturer-specific failure detected?</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer-specific NRC</td>
    </tr>
    <tr>
      <td>SID supported?</td>
      <td>NRC 0x11</td>
      <td>-</td>
      <td>-</td>
      <td>Service Not Supported</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>SID supported in active session?</td>
      <td>NRC 0x7F</td>
      <td>-</td>
      <td>-</td>
      <td>Service Not Supported in Active Session</td>
    </tr>
    <tr>
      <td>SID security check OK?</td>
      <td>-</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Secure data transmission required?<br>(A_Mtype ≠ secure)</td>
      <td>-</td>
      <td>NRC 0x38</td>
      <td>-</td>
      <td>Secure Data Transmission Required</td>
    </tr>
    <tr>
      <td>Secure data transmission required?<br>(A_Mtype = secure)</td>
      <td>-</td>
      <td>NRC 0x39</td>
      <td>-</td>
      <td>Secure Data Transmission Not Allowed</td>
    </tr>
    <tr>
      <td>Supplier-specific failure detected?</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Supplier-specific NRC</td>
    </tr>
    <tr>
      <td>Service with SubFunction (except SID 0x31)?</td>
      <td>Thực hiện Specific SID validation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>Specific SID validation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- **Bước xác thực "Server is busy?"**: Server phản hồi **NRC 0x21** nếu một yêu cầu chẩn đoán không thể được chấp nhận vì Server đang xử lý một yêu cầu chẩn đoán khác do một Client khác gửi.

- **Bước xác thực "Service with SubFunction (except SID 0x31)?"**: nếu thoả điều kiện, Server thực hiện [quy trình xác thực với yêu cầu chẩn đoán có SubFunction]({{ "/docs/AUTOSAR/AUTOSAR_Classic_Platform/BSW/Diagnostic_Stack/UDS/GeneralServerResponseBehaviour/#general-server-response-behaviour-for-service-with-subfunction-except-sid-0x31" | relative_url }}).

- **Bước xác thực "Specific SID validation"**: Việc xác thực riêng từng SID phải tuân theo quy định về response behaviour và các NRC được hỗ trợ của chính dịch vụ đó.

- **A_Mtype (Application Message Type)** là tham số xác định định dạng (format) của dịch vụ tầng ứng dụng (Application Layer Service), quyết định cách Client định địa chỉ (addressing) đến Server. Ví dụ:
  - **A_Mtype = diagnostics**: Sử dụng định dạng mặc định, khi Client chỉ cần A_SA (Source Address) và A_TA (Target Address) để xác định Server.
  - **A_Mtype = remote diagnostics**: Sử dụng định dạng mở rộng, khi ngoài A_SA và A_TA, Client cần thêm thông tin địa chỉ để truy cập đến một Server cụ thể.

---

# General server response behaviour for Service with SubFunction (except SID 0x31)

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>SubFunction supported for the SID?</td>
      <td>NRC 0x12</td>
      <td>-</td>
      <td>-</td>
      <td>Sub-Function Not Supported</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>SubFunction supported in active session for the SID?</td>
      <td>NRC 0x7E</td>
      <td>-</td>
      <td>-</td>
      <td>Sub-Function Not Supported in Active Session</td>
    </tr>
    <tr>
      <td>SubFunction security check OK?</td>
      <td>-</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Request sequence respected for the SubFunction?</td>
      <td>-</td>
      <td>NRC 0x24</td>
      <td>-</td>
      <td>Request Sequence Error</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer-/Supplier-specific NRC</td>
    </tr>
    <tr>
      <td>Specific SID checks</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- **Bước xác thực "Minimum length check"**: Request phải có ít nhất 2 byte dữ liệu.
```c
Minimum request length = 1 (SID) + 1 (SubFunction)
```

- **Bước xác thực "Request sequence respected for the SubFunction?"**: không áp dụng cho mọi SubFunction. Nó chỉ được thực hiện nếu SubFunction thuộc loại yêu cầu xác thực thứ tự (sequence check). Nếu trình tự sai, Server trả về NRC 0x24 (Request Sequence Error). Ví dụ: 
  - SecurityAccess (SID 0x27): phải gửi Request Seed trước rồi mới được gửi Send Key.
  - LinkControl (SID 0x87): một số SubFunction phải được gọi theo đúng trình tự quy định.

- **Bước xác thực "Manufacturer / Supplier-specific check"**: Sau khi vượt qua các xác thực chung, Server sẽ thực hiện các xác thực riêng của từng dịch vụ (**Specific SID checks**). Các xác thực và NRC tại bước này không được quy định chung trong các bảng ở trên, mà phải xem phần mô tả của từng dịch vụ trong ISO 14229-1. Ngoài ra, nhà sản xuất hoặc nhà cung cấp cũng có thể bổ sung các xác thực riêng và trả về manufacturer/supplier-specific NRC (NRC 0xXX) nếu phát hiện lỗi.

---

# NRC handling for Authentication service (SID 0x29) service 

Bảng sau trình bày các bước đánh giá (evaluation sequence) cho dịch vụ Authentication (SID 0x29):

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Request sequence error</td>
      <td>NRC 0x24</td>
      <td>-</td>
      <td>-</td>
      <td>Request Sequence Error</td>
    </tr>
    <tr>
      <td>Certificate signature verification OK or vehicle safety systems condition OK?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Certificate data values OK?</td>
      <td>-</td>
      <td>NRC 0x50 - 0x5D</td>
      <td>-</td>
      <td>Certificate verification/authentication-related NRCs</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- **NRC 0x50 đến 0x5D** là các Negative Response Code chi tiết dành riêng cho dịch vụ Authentication (SID 0x29), dùng để chỉ rõ nguyên nhân đánh giá thất bại (ví dụ: chứng chỉ hết hạn, chữ ký không hợp lệ, chuỗi chứng chỉ không tin cậy,...).

- Nếu ECU không hỗ trợ các NRC 0x50 đến 0x5D, nó có thể trả về **NRC 0x10 (General Reject)**.

---

# NRC handling for ReadDataByIdentifier (SID 0x22) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check + modulo 2 division</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Maximum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>DID #n support service 0x22 in active session?<br>(Check all DID)</td>
      <td>Xác thực "At least one DID is supported in the active session?"</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>DID #n security check OK?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>DID #n condition check OK?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>At least one DID is supported in the active session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Total response length exceeded (available in the server)?</td>
      <td>NRC 0x14</td>
      <td>-</td>
      <td>-</td>
      <td>Response Too Long</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- **dataldentifier (DID)**: xác định các server data record được yêu cầu bởi client. Một request ReadDataByIdentifier (SID 0x22) có thể chứa 1 hoặc nhiều DID.

- ReadDataByIdentifier Request (SID 0x22) phải có tối thiểu 3 byte: 1 byte SID (0x22) và 2 byte DID. Đồng thời, sau SID, số byte DID còn lại phải là bội số của 2, vì mỗi DID luôn có độ dài 2 byte.
```c
Request length = 1 (SID) + 2*n (DID)   (n ≥ 1)
```

- Sau khi kết thúc **bước xác thực "DID #n condition check OK?"**, quy trình xác thực sẽ quay lại **bước "DID #n supported in active session?"** với các DID chưa được xác thực (nếu có).

- Nếu bước xác thực cuối cùng thoả mãn, ECU gửi positive response cùng với các DID được hỗ trợ trong session hiện tại.

---

# NRC handling for ReadMemoryByAddress (SID 0x22) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>addressAndLengthFormatIdentifier</code> is applicable?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>memoryAddress</code> and <code>memorySize</code> are correct AND supported in the current session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested memory interval?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check OK?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
    <tr>
      <td>Response too long check</td>
      <td>NRC 0x14</td>
      <td>-</td>
      <td>-</td>
      <td>Response Too Long</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `addressAndLengthFormatIdentifier` viết tắt là **ALFID**.

- Tổng chiều dài request phải thỏa mãn:
```c
Request length = 1 (SID) + 1 (ALFID) + n (memoryAddress) + m (memorySize)   (n ≥ 1, m ≥ 1)
```

---

# NRC handling for ReadScalingDataByIdentifier (SID 0x24) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Length check = 3 bytes</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>DID supports SID 0x24 in the active session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Scaling information is available for the specified DID?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for the requested DID?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check OK?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- Tổng chiều dài request phải thỏa mãn:
```c
Request length = 1 (SID) + 2 (DID)
```

---

# NRC handling for ReadDataByPeriodicIdentifier (SID 0x2A) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum &amp; maximum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>transmissionMode</code> supported?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Scheduler free at all?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>pDID #n supported in active session?<br>(Check all pDID)</td>
      <td>Xác thực "At least one pDID is supported in the active session?"</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>Security check OK for pDID #n?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check OK for pDID #n?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>At least one pDID is supported in the active session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Scheduler free to store all supported pDIDs requested?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- **periodicDataIdentifier** viết tắt là **pDID**.

- Độ dài Request phụ thuộc vào **TransmissionMode (TM)**:

  - Nếu TM = stopSending, độ dài tối thiểu là 2 byte (SID + TM).

  - Nếu TM ≠ stopSending, độ dài tối thiểu là 3 byte (SID + TM + 1 pDID).

  - Độ dài tối đa là:
  ```c
  Request length = 1 (SID) + 1 (TM) + n (pDID(s))   (n ≥ 1)
  ```

- Sau khi kết thúc **bước xác thực "Condition check OK for pDID #n?"**, quy trình xác thực sẽ quay lại **bước "pDID #n supported in active session?"** với các pDID chưa được xác thực (nếu có).

---

# NRC handling for WriteDataByIdentifier (SID 0x2E) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>DID supports service 0x2E in the active session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>DID security check OK?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>DID condition check OK?</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Data record is valid?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Was correctly altered into server's memory?</td>
      <td>NRC 0x72</td>
      <td>-</td>
      <td>-</td>
      <td>General Programming Failure</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- Data Record viết tắt là **DREC**.

- Tổng chiều dài request phải thỏa mãn:
```c
Request length = 1 (SID) + 2 (DID) + n (DREC)   (n ≥ 1)
```

---

# NRC handling for WriteMemoryByAddress (SID 0x3D) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>addressAndLengthFormatIdentifier</code> is applicable?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>memoryAddress</code> and <code>memorySize</code> are correct AND supported in the current session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested memory interval?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific negative response</td>
    </tr>
    <tr>
      <td>No error when writing to a memory location?</td>
      <td>NRC 0x72</td>
      <td>-</td>
      <td>-</td>
      <td>General Programming Failure</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `addressAndLengthFormatIdentifier` viết tắt là **ALFID**.

- Tổng chiều dài request phải thỏa mãn:
```c
Request length = 1 (SID) + 1 (ALFID) + m (memoryAddress) + k (memorySize) + r (dataRecord)
                   (m ≥ 1, k ≥ 1, r ≥ 1)
```

---

# NRC handling for ClearDiagnosticInformation (SID 0x14) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Condition check<br>(if <code>memorySelection</code> is available)</td>
      <td>-</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Memory selection supported?<br>(if <code>memorySelection</code> is available)</td>
      <td>-</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td><code>GODTC_</code> supported?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific negative response</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x72</td>
      <td>-</td>
      <td>General Programming Failure</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `groupOfDTC` viết tắt là **GODTC_**.

- Tham số `memorySelection` là tùy chọn và chỉ được sử dụng nếu ECU hỗ trợ lựa chọn vùng nhớ (memory selection).

- Tổng chiều dài request phải thỏa mãn:
```c
Request length = 1 (SID) + 3 (GODTC_) + 1 (memorySelection - optional)
```

---

# NRC handling for InputOutputControlByIdentifier (SID 0x2F) service 

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>DID supports service 0x2F in active session and <code>inputOutputControlParameter</code> is supported?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>controlState</code> is supported (if applicable) AND <code>controlMask</code> is supported (if applicable)?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested DID?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific negative response</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `inputOutputControlParameter` viết tắt là **IOCP**.

- `controlMask` có thể được áp dụng hoặc không.

- Chiều dài ngắn nhất của request phải thỏa mãn:
```c
Minimum request length = 1 (SID) + 2 (DID) + 1 (IOCP)
```

- Nếu ICOP $\neq$ shortTermAdjustment:
```c
Request length = 1 (SID) + 2 (DID) + 1 (IOCP) + r (controlMask)   (m ≥ 1, r ≥ 0)
```

- Nếu IOCP = shortTermAdjustment:
```c
Request length = 1 (SID) + 2 (DID) + 1 (IOCP) + m (controlState) + r (controlMask)
                   (m ≥ 1, r ≥ 0)
```

---

#  NRC handling for RoutineControl (SID 0x31) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Steps</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>RID supported in active session?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>RID security check OK?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>SubFunction supported for <code>routineIdentifier</code>?</td>
      <td>NRC 0x12</td>
      <td>-</td>
      <td>-</td>
      <td>Sub-Function Not Supported</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td><code>routineControlOptionRecord</code> contains valid data for the requested RID?</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Request sequence respected for the RID?</td>
      <td>-</td>
      <td>NRC 0x24</td>
      <td>-</td>
      <td>Request Sequence Error</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific negative response</td>
    </tr>
    <tr>
      <td>Further parameter checks</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `routineIdentifier` viết tắt là **RID**.

- Số byte của `routineControlOptionRecord` phụ thuộc vào từng RID cụ thể.

- Tổng chiều dài của request phải thỏa mãn:
```c
Request length = 1 (SID) + 1 (SubFunction) + 2 (RID) + n (routineControlOptionRecord)   
                   (n ≥ 0)
```
- **Bước "Condition check"**: là bước kiểm tra **tùy chọn** và **độc lập với nội dung dữ liệu** (*optional condition check independent from data content*). Điều này có nghĩa ECU có thể kiểm tra các điều kiện thực thi (ví dụ trạng thái hệ thống, chế độ hoạt động, điều kiện môi trường,...) trước khi tiếp tục xử lý yêu cầu, không phụ thuộc vào giá trị của `routineControlOptionRecord`.

---

# NRC handling for RequestDownload (SID 0x34) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Step</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>dataFormatIdentifier</code> is valid AND <code>addressAndLengthFormatIdentifier</code> is valid</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Full length check<sup>2</sup></td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>memoryAddress</code> / <code>memorySize</code> is valid</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested memory interval?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Download fault condition check</td>
      <td>-</td>
      <td>NRC 0x70</td>
      <td>-</td>
      <td>Upload / Download Not Accepted</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `dataFormatIdentifier` viết tắt là **DFI_**.

- `addressAndLengthFormatIdentifier` viết tắt là **ALFID**, tham số này được dùng để tính toán độ dài đầy đủ của request.

- Tổng chiều dài của request phải thỏa mãn:
```c
Request length = 1 (SID) + 1 (DFI) + 1 (ALFID) + m (memoryAddress) + k (memorySize)
                   (m ≥ 1, k ≥ 1)
```

---

# NRC handling for RequestUpload (SID 0x35) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Step</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>dataFormatIdentifier</code> is valid AND <code>addressAndLengthFormatIdentifier</code> is valid</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Full length check<sup>2</sup></td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>memoryAddress</code> / <code>memorySize</code> is valid</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested memory interval?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td>Upload fault condition check</td>
      <td>-</td>
      <td>NRC 0x70</td>
      <td>-</td>
      <td>Upload / Download Not Accepted</td>
    </tr>
    <tr>
      <td>Manufacturer / Supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `dataFormatIdentifier` viết tắt là **DFI_**.

- `addressAndLengthFormatIdentifier` viết tắt là **ALFID**, tham số này được dùng để tính toán độ dài đầy đủ của request.

- Tổng chiều dài của request phải thỏa mãn:
```c
Request length = 1 (SID) + 1 (DFI) + 1 (ALFID) + m (memoryAddress) + k (memorySize)
                   (m ≥ 1, k ≥ 1)
```

---

# NRC handling for TransferData (SID 0x36) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Step</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Sequence is respected for SID</td>
      <td>NRC 0x24</td>
      <td>-</td>
      <td>-</td>
      <td>Request Sequence Error</td>
    </tr>
    <tr>
      <td>Data transfer can continue AND <code>memorySize</code> is respected</td>
      <td>NRC 0x71</td>
      <td>-</td>
      <td>-</td>
      <td>Transfer Data Suspended</td>
    </tr>
    <tr>
      <td>Block sequence counter is OK</td>
      <td>NRC 0x73</td>
      <td>-</td>
      <td>-</td>
      <td>Wrong Block Sequence Counter</td>
    </tr>
    <tr>
      <td><code>transferRequestParameterRecord</code> is valid AND expected number of <code>transferRequestParameters</code> are included (if applicable)</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Data is correctly altered</td>
      <td>-</td>
      <td>NRC 0x72</td>
      <td>-</td>
      <td>General Programming Failure</td>
    </tr>
    <tr>
      <td>Voltage conditions are OK</td>
      <td>-</td>
      <td>NRC 0x92 or NRC 0x93</td>
      <td>-</td>
      <td>
        0x92 - Voltage Too High<br>
        0x93 - Voltage Too Low
      </td>
    </tr>
    <tr>
      <td>Manufacturer / supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `blockSequenceCounter` viết tắt là **BSC**.

- `transferRequestParameterRecord` viết tắt là **TRPR_**.

- Nếu **RequestUpload** đang được thực thi:
```c
Minimum request length = 1 (SID) + 1 (BSC)
```

- Nếu **RequestDownload** đang được thực thi:
```c
Request length = 1 (SID) + 1 (BSC) + n (TRPR_)   (n ≥ 1)
```

---

# NRC handling for RequestTransferExit (SID 0x37) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Step</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Sequence is respected for SID</td>
      <td>NRC 0x24</td>
      <td>-</td>
      <td>-</td>
      <td>Request Sequence Error</td>
    </tr>
    <tr>
      <td>Total length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td><code>transferRequestParameterRecord</code> is valid</td>
      <td>-</td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Error when finalizing</td>
      <td>-</td>
      <td>NRC 0x72</td>
      <td>-</td>
      <td>General Programming Failure</td>
    </tr>
    <tr>
      <td>Manufacturer / supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

---

# NRC handling for requestFileTransfer (SID 0x38) service

<table class="hover-table">
  <thead>
    <tr>
      <th>Validation Step</th>
      <th>Mandatory</th>
      <th>Optional</th>
      <th>Manufacturer / Supplier Specific</th>
      <th>NRC Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minimum length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>Message parameter validity check depends on the <code>modeOfOperation</code></td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Full length check</td>
      <td>NRC 0x13</td>
      <td>-</td>
      <td>-</td>
      <td>Incorrect Message Length or Invalid Format</td>
    </tr>
    <tr>
      <td>
        <code>fileSizeUncompressed</code> is valid AND
        <code>fileSizeCompressed</code> is valid AND
        <code>filePathAndName</code> is valid
      </td>
      <td>NRC 0x31</td>
      <td>-</td>
      <td>-</td>
      <td>Request Out Of Range</td>
    </tr>
    <tr>
      <td>Authentication check OK?</td>
      <td>NRC 0x34</td>
      <td>-</td>
      <td>-</td>
      <td>Authentication Required</td>
    </tr>
    <tr>
      <td>Security check OK for requested <code>filePathAndFileName</code>?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Condition check</td>
      <td>-</td>
      <td>NRC 0x22</td>
      <td>-</td>
      <td>Conditions Not Correct</td>
    </tr>
    <tr>
      <td><code>filePathAndFileName</code> is in an incomplete transfer state?</td>
      <td>NRC 0x33</td>
      <td>-</td>
      <td>-</td>
      <td>Security Access Denied</td>
    </tr>
    <tr>
      <td>Download fault condition check</td>
      <td>-</td>
      <td>NRC 0x70</td>
      <td>-</td>
      <td>Upload/Download Not Accepted</td>
    </tr>
    <tr>
      <td>Manufacturer / supplier-specific check</td>
      <td>-</td>
      <td>-</td>
      <td>NRC 0xXX</td>
      <td>Manufacturer / Supplier-specific NRC</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- `modeOfOperation` viết tắt là **MOOP**.

- `filePathAndNameLength` viết tắt là **FPL_B**.

- `filePathAndName` viết tắt là **FP_B**.

- Chiều dài ngắn nhất của request phải thỏa mãn:
```c
Minimum request length = 1 (SID) + 1 (MOOP) + 1 (FPL_B1) + 1 (FPL_B2) + 1 (FP_B1)
```

- Chiều dài đầy đủ của request có thể tính toán từ `fileSizeParamterLength` và `filePathAndNameLength`.