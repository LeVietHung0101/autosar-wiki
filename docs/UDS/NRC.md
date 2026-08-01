---
title: NRC
parent: UDS
nav_order: 2
---

<h1>Negative Response Code (NRC)</h1>

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Định nghĩa

{: .note }
> **Negative Response Code (NRC)**: là mã lỗi do **server (ECU) phản hồi cho client (tester tool)** khi một dịch vụ chẩn đoán cụ thể thất bại hoặc không thể xử lý.

NRC được định nghĩa trong tiêu chuẩn **ISO 14229** (UDS – Unified Diagnostic Services) và được dùng trong hệ thống **OBD** (On-Board Diagnostics) của xe để xác định nguyên nhân thất bại đối với một yêu cầu dịch vụ chẩn đoán.

**Vai trò và Mục đích:**

- Khi client (tester tool) gửi một yêu cầu chẩn đoán, thông thường server (ECU) sẽ hoàn thành công việc và gửi PosRsp. Tuy nhiên, nếu do sự cố kỹ thuật, môi trường, hoặc yêu cầu không chính xác mà server không thể hoàn thành, server sẽ gửi NegRsp kèm theo NRC.

- Dùng để truyền đạt thông tin về sự thành công hay thất bại của các dịch vụ và yêu cầu chẩn đoán trong giao thức UDS.

- Giúp tester dễ dàng phân tích, khắc phục sự cố hoặc sửa chữa xe một cách thuận lợi.

- Mỗi dịch vụ chẩn đoán sẽ chỉ định các mã NRC áp dụng cho riêng nó. Ngoài ra, việc triển khai dịch vụ chẩn đoán trong server cũng có thể sử dụng thêm các NRC bổ sung được quy định bởi nhà sản xuất xe (vehicle manufacturer).

**Phân loại các dải mã NRC:**

Toàn bộ dải mã Negative Response Code từ 0x00 đến 0xFF được chia thành ba dải chính như sau:
<table class="hover-table">
  <thead>
    <tr>
      <th>Range</th>
      <th>Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>Dành cho giá trị tham số positiveResponse phục vụ việc triển khai nội bộ của server.</td>
    </tr>
    <tr>
      <td>0x01 - 0x7F</td>
      <td>Các mã phản hồi lỗi liên quan đến truyền thông (communication-related negative response codes).</td>
    </tr>
    <tr>
      <td>0x80 - 0xFF</td>
      <td>
        Các mã phản hồi lỗi dành cho các điều kiện cụ thể không chính xác tại thời điểm yêu cầu được server tiếp nhận.
        <br>Các mã phản hồi này có thể được sử dụng bất cứ khi nào mã phản hồi 0x22 (ConditionsNotCorrect) được liệt kê là hợp lệ, nhằm báo cáo cụ thể hơn lý do tại sao hành động được yêu cầu không thể thực hiện được.
      </td>
    </tr>
  </tbody>
</table>

---

# Danh sách NRCs

<details markdown="block">
<summary><i>Các khái niệm liên quan</i></summary>

> <table class="hover-table">
>   <thead>
>     <tr>
>       <th>Khái niệm</th>
>       <th>Định nghĩa</th>
>     </tr>
>   </thead>
>   <tbody>
>     <tr>
>       <td>client</td>
>       <td>
>         Chức năng thuộc <b>tester</b>, sử dụng các <b>diagnostic service</b>.
>         <br>Một tester còn sử dụng các chức năng khác như quản lý cơ sở dữ liệu, diễn giải dữ liệu chuyên biệt và giao diện người-máy (human-machine interface - HMI).
>       </td>
>     </tr>
>     <tr>
>       <td>tester</td>
>       <td>
>         Hệ thống điều khiển các chức năng như kiểm thử (<b>testing</b>), kiểm tra (<b>inspection</b>), giám sát (<b>monitoring</b>) hoặc chẩn đoán (<b>diagnosis</b>) đối với <b>ECU</b> trên xe.
>         <br>Tester có thể được thiết kế cho từng loại người sử dụng, ví dụ:
>         <ul>
>           <li><b>Off-board scan tool</b> dành cho kỹ thuật viên sửa chữa.</li>
>           <li><b>Off-board test tool</b> dành cho nhà máy lắp ráp.</li>
>           <li><b>On-board tester</b> tích hợp trên xe.</li>
>         </ul>
>         Tester cũng được gọi là <b>client</b>.
>       </td>
>     </tr>
>     <tr>
>       <td>server</td>
>       <td>
>         Chức năng nằm trong một <b>ECU</b>, cung cấp các <b>diagnostic service</b>.
>         <br><b>ISO 14229</b> phân biệt giữa <b>server</b> (chức năng) và <b>ECU</b> (thiết bị) nhằm đảm bảo tính độc lập với cách triển khai.
>       </td>
>     </tr>
>     <tr>
>       <td>electronic control unit (ECU)</td>
>       <td>
>         Bộ điều khiển điện tử cung cấp thông tin liên quan đến các cảm biến và mạng điều khiển được kết nối.
>         <br>Các hệ thống như <b>ABS (Anti-lock Braking System)</b> và <b>Engine Management System</b> được xem là ECU.
>       </td>
>     </tr>
>     <tr>
>       <td>diagnostic service</td>
>       <td>
>         Quá trình trao đổi thông tin do <b>client</b> khởi tạo nhằm yêu cầu thông tin chẩn đoán từ <b>server</b> và/hoặc thay đổi hành vi của server phục vụ mục đích chẩn đoán.
>       </td>
>     </tr>
>     <tr>
>       <td>diagnostic session</td>
>       <td>
>         Trạng thái của <b>server</b> trong đó một tập hợp xác định các <b>diagnostic service</b> và chức năng được kích hoạt.
>       </td>
>     </tr>
>     <tr>
>       <td>diagnostic trouble code (DTC)</td>
>       <td>
>         Mã số chuẩn dùng để định danh một tình trạng lỗi được hệ thống chẩn đoán trên xe (<b>On-board Diagnostic System - OBD</b>) phát hiện.
>       </td>
>     </tr>
>     <tr>
>       <td>security</td>
>       <td>
>         Cơ chế bảo vệ các module trên xe khỏi sự truy cập trái phép (<b>unauthorized intrusion</b>) thông qua liên kết truyền dữ liệu chẩn đoán (<b>vehicle diagnostic data link</b>).
>       </td>
>     </tr>
>   </tbody>
> </table>
{: .codeBlock }
</details>

<table class="hover-table">
  <thead>
    <tr>
      <th>Value</th>
      <th>ResponseCode & Mnemonic & Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>
        <b>positiveResponse</b>
        <br>This NRC shall not be used in a negative response message. This <code>positiveResponse</code> parameter value is reserved for server internal implementation.
      </td>
    </tr>
    <tr>
      <td>0x01 - 0x0F</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved by this document for future definition.
      </td>
    </tr>
    <tr>
      <td>0x10</td>
      <td>
        <b>GeneralReject (GR)</b>
        <br>This NRC indicates that the requested action has been rejected by the server, and it shall only be used when none of the other standard negative response codes (NRC) defined in the UDS document fit your implementation needs, meaning it must never serve as a general replacement for other specific response codes.
      </td>
    </tr>
    <tr>
      <td>0x11</td>
      <td>
        <b>ServiceNotSupported (SNS)</b>
        <br>This NRC indicates that the requested action will not be taken because the server does not support the requested service, which happens when the client sends a request message with an unknown, unsupported, or response-specified SID that is not recognized or supported by the ECU server.
      </td>
    </tr>
    <tr>
      <td>0x12</td>
      <td>
        <b>SubFunctionNotSupported (SFNS)</b>
        <br>This NRC indicates that the requested action will not be taken because the server does not support the service specific parameters of the request message, which happens when the client sends a request message with a known and supported SID but includes a SubFunction that is unknown, invalid, or not supported by the server.
      </td>
    </tr>
    <tr>
      <td>0x13</td>
      <td>
        <b>IncorrectMessageLengthOrInvalidFormat (IMLOIF)</b>
        <br>This NRC indicates that the requested action will not be taken because the length or frame format of the received request message does not match the prescribed length or format for the SID and its parameters.
      </td>
    </tr>
    <tr>
      <td>0x14</td>
      <td>
        <b>ResponseTooLong (RTL)</b>
        <br>This NRC indicates that the response message generated by the server exceeds the maximum number of bytes available by the underlying network layer, which can happen if the message surpasses the maximum size allowed by the underlying transport protocol (such as CAN-TP) or exceeds the server's allocated buffer size - often occurring when a client requests multiple DIDs at once and their combined response data crosses that size limit.
      </td>
    </tr>
    <tr>
      <td>0x15 - 0x20</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x21</td>
      <td>
        <b>BusyRepeatRequest (BRR)</b>
        <br>This NRC indicates that the server is temporarily too busy to perform the requested operation, such as in a multi-client environment where a diagnostic task from one client is blocked while another finishes processing, requiring the client to delay and repeat the identical request message or another request message after a time specified in implementation documents. In general, this NRC is supported by each diagnostic service (SID), though if the server is able to perform the task but needs additional time to finish and prepare the response, NRC 0x78 shall be used instead of NRC 0x21.
      </td>
    </tr>
    <tr>
      <td>0x22</td>
      <td>
        <b>ConditionsNotCorrect (CNC)</b>
        <br>This NRC indicates that the server cannot perform the requested action because its required operating conditions or prerequisites (such as proper vehicle voltage levels or temperature thresholds) have not been met.
      </td>
    </tr>
    <tr>
      <td>0x23</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x24</td>
      <td>
        <b>RequestSequenceError (RSE)</b>
        <br>This NRC indicates that the requested action will not be taken because the server expects a different sequence of request messages than the sequence sent by the client. This can happen when sequence-sensitive requests are sent in the wrong order. For example, in SecurityAccess (0x27), the client shall send requestSeed before sendKey. If the client sends sendKey first or sends the messages in a different order, the server will respond with this NRC.
      </td>
    </tr>
    <tr>
      <td>0x25</td>
      <td>
        <b>NoResponseFromSubnetComponent (NRFSC)</b>
        <br>This NRC indicates that the server has received the request, but it cannot perform the requested action because a subnet component required to provide the requested information did not respond within the specified time. This NRC is typically sent by a gateway ECU when it forwards the client's request to a target ECU but does not receive a response from the target ECU within the required time. This NRC is generally supported by all diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x26</td>
      <td>
        <b>FailurePreventsExecutionOfRequestedAction (FPEORA)</b>
        <br>This NRC indicates that the requested action will not be taken because a failure condition identified by a DTC prevents the server from performing the requested action. At least one DTC status bit (TestFailed, Pending, Confirmed, or TestFailedSinceLastClear) is set to 1. This NRC may indicate that the client should read the DTCs to identify and fix the problem. This NRC shall not be used by diagnostic services that access DTCs, and it is generally supported by other diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x27 - 0x30</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>
        <b>RequestOutOfRange (ROOR)</b>
        <br>This NRC indicates that the requested action will not be taken because the server has detected a parameter in the request message that is out of range. This may happen when the client sends a value outside the allowed range OR tries to access a DataIdentifier (DID) or RoutineIdentifier (RID) that is not supported or is not supported in the active session. This NRC shall be implemented for all services that allow the client to read data, write data, or adjust functions using data.
      </td>
    </tr>
    <tr>
      <td>0x32</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x33</td>
      <td>
        <b>SecurityAccessDenied (SAD)</b>
        <br>This NRC indicates that the requested action will not be taken because the server's security strategy has not been satisfied by the client. This NRC is sent when one of the following conditions is not met:
        <ul>
          <li>The server test conditions are not met.</li>
          <li>The required message sequence (for example, DiagnosticSessionControl before SecurityAccess) is not followed.</li>
          <li>The requested service requires an unlocked server, but the client has not completed the required SecurityAccess.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>0x34</td>
      <td>
        <b>AuthenticationRequired (AR)</b>
        <br>This NRC indicates that the requested service will not be taken because the client has insufficient rights based on its Authentication state. The client shall complete the required authentication and obtain the necessary rights before requesting the service. This NRC is generally supported by all diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x35</td>
      <td>
        <b>InvalidKey (IK)</b>
        <br>This NRC indicates that the server has not granted SecurityAccess because the key sent by the client does not match the key stored or calculated by the server. This is counted as a SecurityAccess attempt. The server remains in the locked state and increments its internal securityAccessFailed counter.
      </td>
    </tr>
    <tr>
      <td>0x36</td>
      <td>
        <b>ExceedNumberOfAttempts (ENOA)</b>
        <br>This NRC indicates that the requested action will not be taken because the client has unsuccessfully attempted to gain SecurityAccess more times than the server's security strategy allows. This usually happens when the client sends an incorrect security key more times than the maximum number of allowed attempts. The client shall wait until the server allows another SecurityAccess attempt.
      </td>
    </tr>
    <tr>
      <td>0x37</td>
      <td>
        <b>RequiredTimeDelayNotExpired (RTDNE)</b>
        <br>This NRC indicates that the requested action will not be taken because the client's latest SecurityAccess attempt was started before the server's required timeout period had elapsed. This usually happens when the client sends another SecurityAccess request before waiting for the required delay after a failed SecurityAccess attempt. The client shall wait until the timeout period expires before trying again.
      </td>
    </tr>
    <tr>
      <td>0x38</td>
      <td>
        <b>SecureDataTransmissionRequired (SDTR)</b>
        <br>This NRC indicates that the requested service will not be taken because the requested action must be sent using a secured communication channel (for example, <code>A_MType</code> = <i>secure {remote} diagnostics</i>). The client shall use the required secured communication channel before requesting the service. This NRC is generally supported by all diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x39</td>
      <td>
        <b>SecureDataTransmissionNotAllowed (SDTNA)</b>
        <br>This NRC indicates that the message was received using the SecuredDataTransmission (0x84) service, but the requested action is not allowed to be sent through the SecuredDataTransmission (0x84) service. The client shall send the request using the appropriate diagnostic service instead.
      </td>
    </tr>
    <tr>
      <td>0x3A</td>
      <td>
        <b>SecureDataVerificationFailed (SDVF)</b>
        <br>This NRC indicates that the message failed in the security sub-layer. This may happen because of an invalid <code>A_Par</code> (Administrative parameter), an invalid Signature/Encryption Calculation parameter, a verification failure of the anti-replay counter or Signature/MAC, or an encryption/decryption error.
      </td>
    </tr>
    <tr>
      <td>0x3B - 0x4F</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x50</td>
      <td>
        <b>CertificateVerificationFailed_InvalidTimePeriod (CVFITP)</b>
        <br>This NRC indicates that the date and time of the server do not match the validity period of the Certificate. The requested action is rejected because the Certificate is not valid at the server's current date and time.
      </td>
    </tr>
    <tr>
      <td>0x51</td>
      <td>
        <b>CertificateVerificationFailed_InvalidSignature (CVFIS)</b>
        <br>This NRC indicates that signature of the Certificate could not be verified.
      </td>
    </tr>
    <tr>
      <td>0x52</td>
      <td>
        <b>CertificateVerificationFailed_InvalidChainOfTrust (CVFICOT)</b>
        <br>This NRC indicates that The Certificate could not be verified against stored information about the issuing authority.
      </td>
    </tr>
    <tr>
      <td>0x53</td>
      <td>
        <b>CertificateVerificationFailed_InvalidType (CVFIT)</b>
        <br>This NRC indicates that the Certificate does not match the current requested use case.
      </td>
    </tr>
    <tr>
      <td>0x54</td>
      <td>
        <b>CertificateVerificationFailed_InvalidFormat (CVFIF)</b>
        <br>This NRC indicates that the Certificate could not be evaluated because the format requirement has not been met.
      </td>
    </tr>
    <tr>
      <td>0x55</td>
      <td>
        <b>CertificateVerificationFailed_InvalidContent (CVFIC)</b>
        <br>This NRC indicates that the Certificate could not be verified because the content does not match.
      </td>
    </tr>
    <tr>
      <td>0x56</td>
      <td>
        <b>CertificateVerificationFailed_InvalidScope (CVFIS)</b>
        <br>This NRC indicates that the scope of the Certificate does not match the contents of the server.
      </td>
    </tr>
    <tr>
      <td>0x57</td>
      <td>
        <b>CertificateVerificationFailed_InvalidCertificate (revoked) (CVFIC)</b>
        <br>This NRC indicates that the Certificate received from client is invalid, because the server has revoked access for some reason.
      </td>
    </tr>
    <tr>
      <td>0x58</td>
      <td>
        <b>OwnershipVerificationFailed (OVF)</b>
        <br>This NRC indicates that delivered Ownership does not match the provided challenge or could not verified with the own private key.
      </td>
    </tr>
    <tr>
      <td>0x59</td>
      <td>
        <b>ChallengeCalculationFailed (CCF)</b>
        <br>This NRC indicates that the challenge could not be calculated on the server side.
      </td>
    </tr><tr>
      <td>0x5A</td>
      <td>
        <b>SettingAccessRightsFailed (SARF)</b>
        <br>This NRC indicates that the server could not set the access rights.
      </td>
    </tr>
    <tr>
      <td>0x5B</td>
      <td>
        <b>SessionKeyCreationOrDerivationFailed (SKCDF)</b>
        <br>This NRC indicates that the server could not create or derive a session key.
      </td>
    </tr>
    <tr>
      <td>0x5C</td>
      <td>
        <b>ConfigurationDataUsageFailed (CDUF)</b>
        <br>This NRC indicates that the server could not work with the provided configuration data.
      </td>
    </tr>
    <tr>
      <td>0x5D</td>
      <td>
        <b>DeAuthenticationFailed (DAF)</b>
        <br>This NRC indicates that DeAuthentication was not successful, server could still be unprotected.
      </td>
    </tr>
    <tr>
      <td>0x5F - 0x6F</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x70</td>
      <td>
        <b>UploadDownloadNotAccepted (UDNA)</b>
        <br>This NRC indicates that an upload or download operation to the server's memory cannot be completed because of a fault condition. Due to this fault condition, the server rejects the upload or download request until the problem is resolved.
      </td>
    </tr>
    <tr>
      <td>0x71</td>
      <td>
        <b>TransferDataSuspended (TDS)</b>
        <br>This NRC indicates that a data transfer operation was stopped because of a fault. The active TransferData sequence shall be aborted. This may happen when the server detects a fault and cannot continue writing the transferred data to memory.
      </td>
    </tr>
    <tr>
      <td>0x72</td>
      <td>
        <b>GeneralProgrammingFailure (GPF)</b>
        <br>This NRC indicates that the server detected an error while erasing or programming a memory location in the permanent memory device (for example, Flash Memory, EEPROM, or other NVM). Because of this error, the requested memory operation cannot be completed.
      </td>
    </tr>
    <tr>
      <td>0x73</td>
      <td>
        <b>WrongBlockSequenceCounter (WBSC)</b>
        <br>This NRC indicates that the server detected an error in the sequence of blockSequenceCounter values during a TransferData operation. This may happen when the client sends a blockSequenceCounter value that is not in the expected sequence. However, if the client repeats the previous TransferData request with the same blockSequenceCounter value, the server shall accept the request.
      </td>
    </tr>
    <tr>
      <td>0x74 - 0x77</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr><tr>
      <td>0x78</td>
      <td>
        <b>RequestCorrectlyReceived_ResponsePending (RCRRP)</b>
        <br>This NRC indicates that the server has received the request message correctly and all request parameters are valid, but the requested action has not been completed yet and the server is not ready to receive another request. The server may send this NRC repeatedly while processing the request. After the requested service is completed, the server shall send a final positive response or a negative response with an NRC different from this one. This NRC is typically used for long operations, such as Flash memory programming or erasing, where the server cannot process new requests until the operation is finished.
      </td>
    </tr>
    <tr>
      <td>0x79 - 0x7D</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x7E</td>
      <td>
        <b>SubFunctionNotSupportedInActiveSession (SFNSIAS)</b>
        <br>This NRC indicates that the requested action will not be taken because the server does not support the requested SubFunction in the currently active session. This NRC shall only be used when the requested SubFunction is supported in another session. If the requested SubFunction is not supported in any session, the server shall respond with SubFunctionNotSupported (SFNS) instead. This NRC is generally supported by all diagnostic services that have a SubFunction parameter unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x7F</td>
      <td>
        <b>ServiceNotSupportedInActiveSession (SNSIAS)</b>
        <br>This NRC indicates that the requested action will not be taken because the server does not support the requested service in the currently active session. This NRC shall only be used when the requested service is supported in another session. If the requested service is not supported in any session, the server shall respond with ServiceNotSupported (SNS) instead. This NRC is generally supported by all diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x80</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0x81</td>
      <td>
        <b>RpmTooHigh (RPMTH)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for RPM is not met. The current RPM is above the preprogrammed maximum threshold, so the server rejects the requested action until the RPM is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x82</td>
      <td>
        <b>RpmTooLow (RPMTL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for RPM is not met. The current RPM is below the preprogrammed minimum threshold, so the server rejects the requested action until the RPM is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x83</td>
      <td>
        <b>EngineIsRunning (EIR)</b>
        <br>This NRC indicates that the requested action will not be taken because the requested actuator test cannot be performed while the engine is running. This condition is different from RPMTooHigh (NRC 0x81), because the engine running itself prevents the actuator test, even if the RPM is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x84</td>
      <td>
        <b>EngineIsNotRunning (EINR)</b>
        <br>This NRC indicates that the requested action will not be taken because the requested actuator test can only be performed while the engine is running. This condition is different from RPMTooLow (NRC 0x82), because the engine not running itself prevents the actuator test, even if the RPM is within the allowed range.
      </td>
    </tr><tr>
      <td>0x85</td>
      <td>
        <b>EngineRunTimeTooLow (ERTTL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for engine run time is not met. The current engine run time is below the preprogrammed minimum limit, so the server rejects the requested action until the required engine run time is reached.
      </td>
    </tr>
    <tr>
      <td>0x86</td>
      <td>
        <b>TemperatureTooHigh (TEMPTH)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for temperature is not met. The current temperature is above the preprogrammed maximum threshold, so the server rejects the requested action until the temperature is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x87</td>
      <td>
        <b>TemperatureTooLow (TEMPTL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for temperature is not met. The current temperature is below the preprogrammed minimum threshold, so the server rejects the requested action until the temperature is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x88</td>
      <td>
        <b>VehicleSpeedTooHigh (VSTH)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for vehicle speed (VS) is not met. The current vehicle speed is above the preprogrammed maximum threshold, so the server rejects the requested action until the vehicle speed is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x89</td>
      <td>
        <b>VehicleSpeedTooLow (VSTL)</b>
        <br>
      This NRC indicates that the requested action will not be taken because the server prerequisite condition for vehicle speed (VS) is not met. The current vehicle speed is below the preprogrammed minimum threshold, so the server rejects the requested action until the vehicle speed is within the allowed range.
    </td>
    </tr>
    <tr>
      <td>0x8A</td>
      <td>
        <b>ThrottleOrPedalTooHigh (TPTH)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for throttle/pedal position (TP/APP) is not met. The current TP/APP is above the preprogrammed maximum threshold, so the server rejects the requested action until the TP/APP is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x8B</td>
      <td>
        <b>ThrottleOrPedalTooLow (TPTL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for throttle/pedal position (TP/APP) is not met. The current TP/APP is below the preprogrammed minimum threshold, so the server rejects the requested action until the TP/APP is within the allowed range.
      </td>
    </tr><tr>
      <td>0x8C</td>
      <td>
        <b>TransmissionRangeNotInNeutral (TRNIN)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for being in neutral is not met. The current transmission range is not in the neutral position, so the server rejects the requested action until the transmission is shifted to neutral.
      </td>
    </tr>
    <tr>
      <td>0x8D</td>
      <td>
        <b>TransmissionRangeNotInGear (TRNIG)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for being in gear is not met. The current transmission range is not in a gear position, so the server rejects the requested action until the transmission is shifted into gear.
      </td>
    </tr>
    <tr>
      <td>0x8E</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved by this document for future definition.
      </td>
    </tr>
    <tr>
      <td>0x8F</td>
      <td>
        <b>BrakeSwitchOrSwitchesNotClosed (BSNC)</b>
        <br>This NRC indicates that the requested action will not be taken because a required safety condition is not met. For safety reasons, this condition shall be satisfied before the test starts and shall be maintained throughout the entire test.
      </td>
    </tr>
    <tr>
      <td>0x90</td>
      <td>
        <b>ShifterLeverNotInPark (SLNIP)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for the torque converter clutch (TCC) is not met. The current TCC status is above the preprogrammed limit or the TCC is locked, so the server rejects the requested action until the required TCC condition is met.
      </td>
    </tr>
    <tr>
      <td>0x91</td>
      <td>
        <b>TorqueConvertClutchLocked (TCCL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for the torque converter clutch (TCC) is not met. The current TCC status is above the preprogrammed limit or the TCC is locked, so the server rejects the requested action until the required TCC condition is met.
      </td>
    </tr>
    <tr>
      <td>0x92</td>
      <td>
        <b>VoltageTooHigh (VTH)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for the voltage at the primary pin of the server (ECU) is not met. The current voltage is above the preprogrammed maximum threshold, so the server rejects the requested action until the voltage is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x93</td>
      <td>
        <b>VoltageTooLow (VTL)</b>
        <br>This NRC indicates that the requested action will not be taken because the server prerequisite condition for the voltage at the primary pin of the server (ECU) is not met. The current voltage is below the preprogrammed minimum threshold, so the server rejects the requested action until the voltage is within the allowed range.
      </td>
    </tr>
    <tr>
      <td>0x94</td>
      <td>
        <b>ResourceTemporarilyNotAvailable (RTNA)</b>
        <br>This NRC indicates that the server has received the request, but the requested action cannot be performed because an application required to provide the requested information is temporarily not available. This NRC is generally supported by all diagnostic services unless otherwise specified.
      </td>
    </tr>
    <tr>
      <td>0x95 - 0xEF</td>
      <td>
        <b>reservedForSpecificConditionsNotCorrect (RFSCNC)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
    <tr>
      <td>0xF0 - 0xFE</td>
      <td>
        <b>vehicleManufacturerSpecificConditionsNotCorrect (VMSCNC)</b>
        <br>This range of values is reserved for vehicle manufacturer specific condition not correct scenarios.
      </td>
    </tr>
    <tr>
      <td>0xFF</td>
      <td>
        <b>ISOSAEReserved (ISOSAERESRVD)</b>
        <br>This range of values is reserved for future definition.
      </td>
    </tr>
  </tbody>
</table>

---

# Tham khảo

[1] ISO 14229-1-2020, "Road vehicles - Unified diagnostic services (UDS) - Part 1: Application layer

[2] [UDS documentation!, "uds.message.nrc - py-uds 4.0.0 documentation"](https://uds.readthedocs.io/en/latest/autoapi/uds/message/nrc/index.html#uds.message.nrc.NRC.RequestOutOfRange)

[3] [PiEmbSysTech, "Negative Response Codes (NRC): UDS Protocol"](https://piembsystech.com/negative-response-codes-nrc-uds-protocol/#google_vignette)
