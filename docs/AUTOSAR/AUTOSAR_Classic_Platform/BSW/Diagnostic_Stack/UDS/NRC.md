---
title: Negative Response Code (NRC)
parent: Unified Diagnostic Services (UDS)
nav_order: 2
---

# Negative Response Code (NRC)

<!-- <strong>Mục lục</strong>
1. TOC
{:toc} -->

---

**Negative Response Code (NRC)**: là mã được sử dụng trong giao thức truyền thông chẩn đoán của xe, do **ECU phản hồi cho tester tool** khi một yêu cầu chẩn đoán cụ thể thất bại hoặc không thể xử lý.

NRCs được định nghĩa trong tiêu chuẩn **ISO 14229** (UDS – Unified Diagnostic Services) và được dùng trong hệ thống **OBD** (On-Board Diagnostics) của xe để xác định phản hồi chẩn đoán mang tính phủ định đối với một yêu cầu chẩn đoán.

---

## Danh sách NRCs

<table class="hover-table">
  <thead>
    <tr>
      <th>NRC (Hex)</th>
      <th>Mnemonic</th>
      <th>Use Case</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>0x10</td><td>GR</td><td>General Reject</td></tr>
    <tr><td>0x11</td><td>SNS</td><td>Service Not Supported</td></tr>
    <tr><td>0x12</td><td>SFNS</td><td>Sub-Function Not Supported</td></tr>
    <tr><td>0x13</td><td>IMLOIF</td><td>Incorrect Message Length or Invalid Format</td></tr>
    <tr><td>0x14</td><td>RTL</td><td>Response Too Long</td></tr>
    <tr><td>0x21</td><td>BRR</td><td>Busy Repeat Request</td></tr>
    <tr><td>0x22</td><td>CNC</td><td>Conditions Not Correct</td></tr>
    <tr><td>0x24</td><td>RSE</td><td>Request Sequence Error</td></tr>
    <tr><td>0x25</td><td>NRFSC</td><td>No Response From Subnet Component</td></tr>
    <tr><td>0x26</td><td>FPEORA</td><td>Failure Prevents Execution Of Requested Action</td></tr>
    <tr><td>0x31</td><td>ROOR</td><td>Request Out of Range</td></tr>
    <tr><td>0x33</td><td>SAD</td><td>Security Access Denied</td></tr>
    <tr><td>0x35</td><td>IK</td><td>Invalid Key</td></tr>
    <tr><td>0x36</td><td>ENOA</td><td>Exceed Number Of Attempts</td></tr>
    <tr><td>0x37</td><td>RTDNE</td><td>Required Time Delay Not Expired</td></tr>
    <tr><td>0x38–0x4F</td><td>RBEDLSD</td><td>Reserved by Extended Data Link Security Document</td></tr>
    <tr><td>0x70</td><td>UDNA</td><td>Upload / Download Not Accepted</td></tr>
    <tr><td>0x71</td><td>TDS</td><td>Transfer Data Suspended</td></tr>
    <tr><td>0x72</td><td>GPF</td><td>General Programming Failure</td></tr>
    <tr><td>0x73</td><td>WBSC</td><td>Wrong Block Sequence Counter</td></tr>
    <tr><td>0x78</td><td>RCRRP</td><td>Response Correctly Received, But Response is Pending</td></tr>
    <tr><td>0x7E</td><td>SFNSIAS</td><td>Sub-Function Not Supported in Active Session</td></tr>
    <tr><td>0x7F</td><td>SNSIAS</td><td>Service Not Supported in Active Session</td></tr>
    <tr><td>0x81</td><td>RTH</td><td>RPM Too High</td></tr>
    <tr><td>0x82</td><td>RTL</td><td>RPM Too Low</td></tr>
    <tr><td>0x83</td><td>EIR</td><td>Engine Is Running</td></tr>
    <tr><td>0x84</td><td>EINR</td><td>Engine Is Not Running</td></tr>
    <tr><td>0x85</td><td>ERTTL</td><td>Engine Run Time Too Low</td></tr>
    <tr><td>0x86</td><td>TTH</td><td>Temperature Too High</td></tr>
    <tr><td>0x87</td><td>TTL</td><td>Temperature Too Low</td></tr>
    <tr><td>0x88</td><td>VSTH</td><td>Vehicle Speed Too High</td></tr>
    <tr><td>0x89</td><td>VSTL</td><td>Vehicle Speed Too Low</td></tr>
    <tr><td>0x8A</td><td>TPTH</td><td>Throttle Pedal Too High</td></tr>
    <tr><td>0x8B</td><td>TPTL</td><td>Throttle Pedal Too Low</td></tr>
    <tr><td>0x8C</td><td>TRNIN</td><td>Transmission Range Not in Neutral</td></tr>
    <tr><td>0x8D</td><td>TRNIG</td><td>Transmission Range Not in Gear</td></tr>
    <tr><td>0x8F</td><td>BSNC</td><td>Brake Switches Not Closed</td></tr>
    <tr><td>0x90</td><td>SLNIP</td><td>Shifter Lever Not in Park</td></tr>
    <tr><td>0x91</td><td>TCCL</td><td>Torque Converter Clutch Locked</td></tr>
    <tr><td>0x92</td><td>VTH</td><td>Voltage Too High</td></tr>
    <tr><td>0x93</td><td>VTL</td><td>Voltage Too Low</td></tr>
    <tr><td>0xF0–0xFE</td><td>—</td><td>Manufacturer Specific Conditions Not Correct</td></tr>
  </tbody>
</table>

---

## Danh sách NRCs (kèm mô tả)

<table class="hover-table">
  <thead>
    <tr>
      <th>value</th>
      <th>responseCode</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>positiveResponse</td>
      <td>This response code shall not be used in a negative response message. This positiveResponse parameter value is reserved for server internal implementation.</td>
    </tr>
    <tr>
      <td>0x01 - 0x0F</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved by this document for future definition.</td>
    </tr>
    <tr>
      <td>0x10</td>
      <td>generalReject</td>
      <td>This response code indicates that the requested action has been rejected by the server. The generalReject response code shall only be implemented in the server if none of the negative response codes defined in this document meet the needs of the implementation. At no means shall this response code be a general replacement for other response codes defined.</td>
    </tr>
    <tr>
      <td>0x11</td>
      <td>serviceNotSupported</td>
      <td>This response code indicates that the requested action will not be taken because the server does not support the requested service. The server shall send this response code in case the client has sent a request message with a service identifier, which is either unknown or not supported by the server. Therefore this negative response code is not shown in the list of negative response codes to be supported for a diagnostic service, because this negative response code is not applicable for supported services.</td>
    </tr>
    <tr>
      <td>0x12</td>
      <td>subFunctionNotSupported</td>
      <td>This response code indicates that the requested action will not be taken because the server does not support the service specific parameters of the request message. The server shall send this response code in case the client has sent a request message with a known and supported service identifier but with “sub function“ which is either unknown or not supported.</td>
    </tr>
    <tr>
      <td>0x13</td>
      <td>incorrectMessageLengthOrInvalidFormat</td>
      <td>This response code indicates that the requested action will not be taken because the length of the received request message does not match the prescribed length for the specified service or the format of the parameters do not match the prescribed format for the specified service.</td>
    </tr>
    <tr>
      <td>0x14</td>
      <td>responseTooLong</td>
      <td>This response code shall be reported by the server if the response to be generated exceeds the maximum number of bytes available by the underlying network layer.</td>
    </tr>
    <tr>
      <td>0x15 - 0x20</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x21</td>
      <td>busyRepeatReques</td>
      <td>This response code indicates that the server is temporarily too busy to perform the requested operation. In this circumstance the client shall perform repetition of the “identical request message” or “another request message”. The repetition of the request shall be delayed by a time specified in the respective implementation documents.Example: In a multi-client environment the diagnostic request of one client might be blocked temporarily by a NRC $21 while a different client finishes a diagnostic task.NOTE If the server is able to perform the diagnostic task but needs additional time to finish the task and prepare the response, the NRC 0x78 shall be used instead of NRC 0x21.This response code is in general supported by each diagnostic service, as not otherwise stated in the data link specific implementation document, therefore it is not listed in the list of applicable response codes of the diagnostic services.</td>
    </tr>
    <tr>
      <td>0x22</td>
      <td>conditionsNotCorrect</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite conditions are not met.</td>
    </tr>
    <tr>
      <td>0x23</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x24</td>
      <td>requestSequenceError</td>
      <td>This response code indicates that the requested action will not be taken because the server expects a different sequence of request messages or message as sent by the client. This may occur when sequence sensitive requests are issued in the wrong order.EXAMPLE A successful SecurityAccess service specifies a sequence of requestSeed and sendKey as sub-functions in the request messages. If the sequence is sent different by the client the server shall send a negative response message with the negative response code 0x24 (requestSequenceError).</td>
    </tr>
    <tr>
      <td>0x25 - 0x30</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>requestOutOfRange</td>
      <td>This response code indicates that the requested action will not be taken because the server has detected that the request message contains a parameter which attempts to substitute a value beyond its range of authority (e.g. attempting to substitute a data byte of 111 when the data is only defined to 100), or which attempts to access a dataIdentifier/routineIdentifer that is not supported or not supported in active session. This response code shall be implemented for all services, which allow the client to read data, write data or adjust functions by data in the server.</td>
    </tr>
    <tr>
      <td>0x32</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x33</td>
      <td>securityAccessDenied</td>
      <td>This response code indicates that the requested action will not be taken because the server’s security strategy has not been satisfied by the client. The server shall send this response code if one of the following cases occur:the test conditions of the server are not met,the required message sequence e.g. DiagnosticSessionControl, securityAccess is not met,the client has sent a request message which requires an unlocked server.Beside the mandatory use of this negative response code as specified in the applicable services within this standard, this negative response code can also be used for any case where security is required and is not yet granted to perform the required service.</td>
    </tr>
    <tr>
      <td>0x34</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x35</td>
      <td>invalidKey</td>
      <td>This response code indicates that the server has not given security access because the key sent by the client did not match with the key in the server’s memory. This counts as an attempt to gain security. The server shall remain locked and increment its internal securityAccessFailed counter.</td>
    </tr>
    <tr>
      <td>0x36</td>
      <td>exceedNumberOfAttempts</td>
      <td>This response code indicates that the requested action will not be taken because the client has unsuccessfully attempted to gain security access more times than the server’s security strategy will allow.</td>
    </tr>
    <tr>
      <td>0x37</td>
      <td>requiredTimeDelayNotExpired</td>
      <td>This response code indicates that the requested action will not be taken because the client’s latest attempt to gain security access was initiated before the server’s required timeout period had elapsed.</td>
    </tr>
    <tr>
      <td>0x38 - 0x4F</td>
      <td>reservedByExtendedDataLinkSecurityDocument</td>
      <td>This range of values is reserved by ISO 15764 Extended data link security.</td>
    </tr>
    <tr>
      <td>0x50 - 0x6F</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x70</td>
      <td>uploadDownloadNotAccepted</td>
      <td>This response code indicates that an attempt to upload/download to a server’s memory cannot be accomplished due to some fault conditions.</td>
    </tr>
    <tr>
      <td>0x71</td>
      <td>transferDataSuspended</td>
      <td>This response code indicates that a data transfer operation was halted due to some fault. The active transferData sequence shall be aborted.</td>
    </tr>
    <tr>
      <td>0x72</td>
      <td>generalProgrammingFailure</td>
      <td>This response code indicates that the server detected an error when erasing or programming a memory location in the permanent memory device (e.g. Flash Memory).</td>
    </tr>
    <tr>
      <td>0x73</td>
      <td>wrongBlockSequenceCounter</td>
      <td>This response code indicates that the server detected an error in the sequence of blockSequenceCounter values. Note that the repetition of a TransferData request message with a blockSequenceCounter equal to the one included in the previous TransferData request message shall be accepted by the server.</td>
    </tr>
    <tr>
      <td>0x74 - 0x77</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x78</td>
      <td>requestCorrectlyReceived-ResponsePending</td>
      <td>This response code indicates that the request message was received correctly, and that all parameters in the request message were valid, but the action to be performed is not yet completed and the server is not yet ready to receive another request. As soon as the requested service has been completed, the server shall send a positive response message or negative response message with a response code different from this. The negative response message with this response code may be repeated by the server until the requested service is completed and the final response message is sent. This response code might impact the application layer timing parameter values. The detailed specification shall be included in the data link specific implementation document.This response code shall only be used in a negative response message if the server will not be able to receive further request messages from the client while completing the requested diagnostic service. When this response code is used, the server shall always send a final response (positive or negative) independent of the suppressPosRspMsgIndicationBit value. A typical example where this response code may be used is when the client has sent a request message, which includes data to be programmed or erased in flash memory of the server. If the programming/erasing routine (usually executed out of RAM) is not able to support serial communication while writing to the flash memory the server shall send a negative response message with this response code. This response code is in general supported by each diagnostic service, as not otherwise stated in the data link specific implementation document, therefore it is not listed in the list of applicable response codes of the diagnostic services.</td>
    </tr>
    <tr>
      <td>0x79 - 0x7D</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x7E</td>
      <td>subFunctionNotSupportedInActiveSession</td>
      <td>This response code indicates that the requested action will not be taken because the server does not support the requested sub-function in the session currently active. Within the programmingSession negative response code 0x12 (subFunctionNotSupported) may optionally be reported instead of negative response code 0x7F (subFunctionNotSupportedInActiveSession). This response code shall only be used when the requested sub-function is known to be supported in another session, otherwise response code 0x12 (subFunctionNotSupported) shall be used. This response code shall be supported by each diagnostic service with a sub-function parameter, if not otherwise stated in the data link specific implementation document, therefore it is not listed in the list of applicable response codes of the diagnostic services.</td>
    </tr>
    <tr>
      <td>0x7F</td>
      <td>serviceNotSupportedInActiveSession</td>
      <td>This response code indicates that the requested action will not be taken because the server does not support the requested service in the session currently active. This response code shall only be used when the requested service is known to be supported in another session, otherwise response code 0x11 (serviceNotSupported) shall be used. This response code is in general supported by each diagnostic service, as not otherwise stated in the data link specific implementation document, therefore it is not listed in the list of applicable response codes of the diagnostic services.</td>
    </tr>
    <tr>
      <td>0x80</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0x81</td>
      <td>rpmTooHigh</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for RPM is not met (current RPM is above a pre-programmed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x82</td>
      <td>rpmTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for RPM is not met (current RPM is below a pre-programmed minimum threshold).</td>
    </tr>
    <tr>
      <td>0x83</td>
      <td>engineIsRunning</td>
      <td>This is required for those actuator tests which cannot be actuated while the Engine is running. This is different from RPM too high negative response and needs to be allowed.</td>
    </tr>
    <tr>
      <td>0x84</td>
      <td>engineIsNotRunning</td>
      <td>This is required for those actuator tests which cannot be actuated unless the Engine is running. This is different from RPM too low negative response, and needs to be allowed.</td>
    </tr>
    <tr>
      <td>0x85</td>
      <td>engineRunTimeTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for engine run time is not met (current engine run time is below a preprogrammed limit).</td>
    </tr>
    <tr>
      <td>0x86</td>
      <td>temperatureTooHigh</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for temperature is not met (current temperature is above a preprogrammed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x87</td>
      <td>temperatureTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for temperature is not met (current temperature is below a preprogrammed minimum threshold).</td>
    </tr>
    <tr>
      <td>0x88</td>
      <td>vehicleSpeedTooHigh</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for vehicle speed is not met (current VS is above a pre-programmed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x89</td>
      <td>vehicleSpeedTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for vehicle speed is not met (current VS is below a pre-programmed minimum threshold).</td>
    </tr>
    <tr>
      <td>0x8A</td>
      <td>throttle/PedalTooHigh</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for throttle/pedal position is not met (current TP/APP is above a preprogrammed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x8B</td>
      <td>throttle/PedalTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for throttle/pedal position is not met (current TP/APP is below a preprogrammed minimum threshold).</td>
    </tr>
    <tr>
      <td>0x8C</td>
      <td>transmissionRangeNotInNeutral</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for being in neutral is not met (current transmission range is not in neutral).</td>
    </tr>
    <tr>
      <td>0x8D</td>
      <td>transmissionRangeNotInGear</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for being in gear is not met (current transmission range is not in gear).</td>
    </tr>
    <tr>
      <td>0x8E</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved by this document for future definition.</td>
    </tr>
    <tr>
      <td>0x8F</td>
      <td>brakeSwitch(es)NotClosed (Brake Pedal not pressed or not applied)</td>
      <td>For safety reasons, this is required for certain tests before it begins, and must be maintained for the entire duration of the test.</td>
    </tr>
    <tr>
      <td>0x90</td>
      <td>shifterLeverNotInPark</td>
      <td>For safety reasons, this is required for certain tests before it begins, and must be maintained for the entire duration of the test.</td>
    </tr>
    <tr>
      <td>0x91</td>
      <td>torqueConverterClutchLocked</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for torque converter clutch is not met (current TCC status above a preprogrammed limit or locked).</td>
    </tr>
    <tr>
      <td>0x92</td>
      <td>voltageTooHigh</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for voltage at the primary pin of the server (ECU) is not met (current voltage is above a pre-programmed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x93</td>
      <td>voltageTooLow</td>
      <td>This response code indicates that the requested action will not be taken because the server prerequisite condition for voltage at the primary pin of the server (ECU) is not met (current voltage is below a pre-programmed maximum threshold).</td>
    </tr>
    <tr>
      <td>0x94 - 0xFE</td>
      <td>reservedForSpecificConditionsNotCorrect</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
    <tr>
      <td>0xFF</td>
      <td>ISOSAEReserved</td>
      <td>This range of values is reserved for future definition.</td>
    </tr>
  </tbody>
</table>



---