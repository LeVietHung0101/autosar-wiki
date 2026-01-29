---
title: Service Identifier (SID)
parent: Unified Diagnostic Services (UDS)
nav_order: 1
has_children: true
---

# Service Identifier (SID)

<!-- <strong>Mục lục</strong>
1. TOC
{:toc} -->

---

{: .note }
**Service Identifier (SID)** được dùng để xác định các dịch vụ chẩn đoán cụ thể trong giao thức UDS (ISO 14229).

SID được chia thành 2 loại: request SID và response SID; với **response SID = request SID + 0x40**.

ECU nhận yêu cầu chẩn đoán (chứa request SID) này và trả về phản hồi thích hợp (chứa response SID) cho dịch vụ tương ứng.

---

## Danh sách SIDs (theo loại dịch vụ)

Các dịch vụ UDS trên thực tế là một tập hợp con của một tập hợp lớn hơn các dịch vụ chẩn đoán - xem tổng quan bên dưới.

<table class="hover-table">
  <thead>
    <tr>
      <th>Service Identifier (SID)</th>
      <th>Service type</th>
      <th>Standard</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00 – 0x0F</td>
      <td>OBD service requests</td>
      <td>ISO 15031-5</td>
      <td>Chẩn đoán khí thải và giám sát pháp lý;<br>Không phải UDS, nhưng cùng chia sẻ không gian SID</td>
    </tr>
    <tr>
      <td>0x10 – 0x3E</td>
      <td>ISO 14229 (requests)</td>
      <td>ISO 14229</td>
      <td>Chẩn đoán, lập trình, bảo trì ECU</td>
    </tr>
    <tr>
      <td>0x3F</td>
      <td>Not applicable</td>
      <td>Reserved</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0x40 – 0x4F</td>
      <td>OBD service responses</td>
      <td>ISO 15031-5</td>
      <td>Phản hồi thành công cho OBD request (0x00 – 0x0F)</td>
    </tr>
    <tr>
      <td>0x50 – 0x7E</td>
      <td>ISO 14229 (responses)</td>
      <td>ISO 14229</td>
      <td>Phản hồi thành công cho UDS request (0x10 – 0x3E)</td>
    </tr>
    <tr>
      <td>0x7F</td>
      <td>Negative response SID</td>
      <td>ISO 14229</td>
      <td>Báo lỗi khi xử lý request</td>
    </tr>
    <tr>
      <td>0x80</td>
      <td>Not applicable</td>
      <td>ISO 14229 (reserved)</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0x81 – 0x82</td>
      <td>Not applicable</td>
      <td>ISO 14229 (reserved)</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0x83 – 0x88</td>
      <td>ISO 14229 (requests)</td>
      <td>ISO 14229</td>
      <td>Chẩn đoán, lập trình, bảo trì ECU;<br>Ít dùng trong thực tế; dùng để mở rộng và tương thích trong tương lai</td>
    </tr>
    <tr>
      <td>0x89 – 0x9F</td>
      <td>Service requests</td>
      <td>Reserved</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0xA0 – 0xB9</td>
      <td>Service requests</td>
      <td>Defined by vehicle OEM</td>
      <td>Chức năng riêng của vehicle OEM; Không nằm trong ISO</td>
    </tr>
    <tr>
      <td>0xBA – 0xBE</td>
      <td>Service requests</td>
      <td>Defined by systems OEM</td>
      <td>Chức năng riêng của systems OEM; Không nằm trong ISO</td>
    </tr>
    <tr>
      <td>0xBF</td>
      <td>Not applicable</td>
      <td>Reserved</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0xC0</td>
      <td>Not applicable</td>
      <td>ISO 14229 (reserved)</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0xC1 – 0xC2</td>
      <td>Not applicable</td>
      <td>ISO 14229 (reserved)</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0xC3 – 0xC8</td>
      <td>ISO 14229 (responses)</td>
      <td>ISO 14229</td>
      <td>Phản hồi cho UDS request (0x83 – 0x88)</td>
    </tr>
    <tr>
      <td>0xC9 – 0xDF</td>
      <td>Service responses</td>
      <td>Reserved</td>
      <td>Dành cho tương lai</td>
    </tr>
    <tr>
      <td>0xE0 – 0xF9</td>
      <td>Service responses</td>
      <td>Defined by vehicle OEM</td>
      <td>Phản hồi tương ứng cho vehicle OEM-defined requests<br>(0xA0 – 0xB9)</td>
    </tr>
    <tr>
      <td>0xFA – 0xFE</td>
      <td>Service responses</td>
      <td>Defined by systems OEM</td>
      <td>Phản hồi tương ứng cho systems OEM-defined requests<br>(0xBA – 0xBE)</td>
    </tr>
    <tr>
      <td>0xFF</td>
      <td>Not applicable</td>
      <td>Reserved</td>
      <td>Dành cho tương lai</td>
    </tr>
  </tbody>
</table>

Ghi chú:

- ECU phải trả **NRC 0x11 (ServiceNotSupported)** nếu nhận các SID **Not applicable/Reserved**.
- Positive Response SID = Request SID + 0x40
- **Vehicle OEM**: Hãng sản xuất xe hoàn chỉnh (Toyota, Hyundai, BMW, Ford).
- **System OEM**: Hãng cung cấp hệ thống / ECU cho xe (Bosch, Continental, Denso, ZF).

**Lý do tách 0x80 và 0x81–0x82 thành 2 nhóm riêng?**

**Lý do tách 0xC0 và 0xC1–0xC2 thành 2 nhóm riêng?**

---

## Danh sách SIDs

Bảng sau mô tả tất cả các SIDs.

<table class="hover-table">
  <thead>
    <tr>
      <th>UDS SID<br>(Request)</th>
      <th>UDS SID<br>(Response)</th>
      <th>Service</th>
      <th>Details</th>
      <th>Sub-function type</th>
      <th>Sub-function</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x10</td>
      <td>0x50</td>
      <td>Diagnostic Session Control</td>
      <td>Control which UDS services are available</td>
      <td>Diagnostic session type</td>
      <td>0x01 – 0x04</td>
    </tr>
    <tr>
      <td>0x11</td>
      <td>0x51</td>
      <td>ECU Reset</td>
      <td>Reset the ECU (hard reset, key off, soft reset)</td>
      <td>Reset type</td>
      <td>0x01 – 0x05</td>
    </tr>
    <tr>
      <td>0x27</td>
      <td>0x67</td>
      <td>Security Access</td>
      <td>Enable use of security-critical services via authentication</td>
      <td>Security access type</td>
      <td>0x01 – 0x10</td>
    </tr>
    <tr>
      <td>0x28</td>
      <td>0x68</td>
      <td>Communication Control</td>
      <td>Turn sending/receiving of messages on/off in the ECU</td>
      <td>Control type</td>
      <td>0x00 – 0x03</td>
    </tr>
    <tr>
      <td>0x29</td>
      <td>0x69</td>
      <td>Authentication</td>
      <td>Advanced authentication compared to 0x27 (PKI-based)</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x3E</td>
      <td>0x7E</td>
      <td>Tester Present</td>
      <td>Send periodic heartbeat to remain in current session</td>
      <td>Zero sub-function</td>
      <td>0x00</td>
    </tr>
    <tr>
      <td>0x83</td>
      <td>0xC3</td>
      <td>Access Timing Parameters</td>
      <td>View or modify timing parameters used in communication</td>
      <td>Timing parameter access type</td>
      <td>0x01 – 0x02</td>
    </tr>
    <tr>
      <td>0x84</td>
      <td>0xC4</td>
      <td>Secured Data Transmission</td>
      <td>Send encrypted data via ISO 15764 (EDLS)</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x85</td>
      <td>0xC5</td>
      <td>Control DTC Settings</td>
      <td>Enable or disable DTC detection</td>
      <td>DTC setting type</td>
      <td>0x01 – 0x02</td>
    </tr>
    <tr>
      <td>0x86</td>
      <td>0xC6</td>
      <td>Response On Event</td>
      <td>Trigger service execution when an event occurs</td>
      <td>Event type</td>
      <td>0x00 – 0x02</td>
    </tr>
    <tr>
      <td>0x87</td>
      <td>0xC7</td>
      <td>Link Control</td>
      <td>Set baud rate for diagnostic communication</td>
      <td>Link control type</td>
      <td>0x01 – 0x05</td>
    </tr>
    <tr>
      <td>0x22</td>
      <td>0x62</td>
      <td>Read Data By Identifier</td>
      <td>Read ECU data such as VIN or sensor values</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x23</td>
      <td>0x63</td>
      <td>Read Memory By Address</td>
      <td>Read data directly from ECU memory</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x24</td>
      <td>0x64</td>
      <td>Read Scaling Data By Identifier</td>
      <td>Read scaling information for data identifiers</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x2A</td>
      <td>0x6A</td>
      <td>Read Data By Identifier Periodic</td>
      <td>Request ECU to periodically transmit data</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x2C</td>
      <td>0x6C</td>
      <td>Dynamically Defined Data Identifier</td>
      <td>Dynamically define data for use in 0x22 or 0x2A</td>
      <td>Definition type</td>
      <td>0x01 – 0x03</td>
    </tr>
    <tr>
      <td>0x2E</td>
      <td>0x6E</td>
      <td>Write Data By Identifier</td>
      <td>Write data values to ECU parameters</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x3D</td>
      <td>0x7D</td>
      <td>Write Memory By Address</td>
      <td>Write data directly into ECU memory</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x14</td>
      <td>0x54</td>
      <td>Clear Diagnostic Information</td>
      <td>Clear stored Diagnostic Trouble Codes (DTCs)</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x19</td>
      <td>0x59</td>
      <td>Read DTC Information</td>
      <td>Read stored DTCs and related information</td>
      <td>Report type</td>
      <td>0x01 – 0x0D</td>
    </tr>
    <tr>
      <td>0x2F</td>
      <td>0x6F</td>
      <td>Input Output Control By Identifier</td>
      <td>Control ECU inputs and outputs</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>0x71</td>
      <td>Routine Control</td>
      <td>Start or stop ECU routines</td>
      <td>Routine control type</td>
      <td>0x01 – 0x03</td>
    </tr>
    <tr>
      <td>0x34</td>
      <td>0x74</td>
      <td>Request Download</td>
      <td>Request to download software or data to ECU</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x35</td>
      <td>0x75</td>
      <td>Request Upload</td>
      <td>Request to upload software or data from ECU</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x36</td>
      <td>0x76</td>
      <td>Transfer Data</td>
      <td>Transfer data during download/upload</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x37</td>
      <td>0x77</td>
      <td>Request Transfer Exit</td>
      <td>Terminate data transfer</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x38</td>
      <td>0x78</td>
      <td>Request File Transfer</td>
      <td>File-based download or upload</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
    <tr>
      <td>0x7F</td>
      <td>—</td>
      <td>Negative Response</td>
      <td>Returned when a request cannot be processed</td>
      <td>N/A</td>
      <td>N/A</td>
    </tr>
  </tbody>
</table>

---

# SID & Sub Function

---

## Diagnostic Session Control (0x10)

<table class="hover-table">
  <thead>
    <tr>
      <th>Diagnostic session type<br>(sub function value)</th>
      <th>Diagnostic session</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Default Session</td>
      <td>On Start of ECU, ECU runs in to default session.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Programming Session</td>
      <td>Used to upload software.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Extended Diagnostic Session</td>
      <td>Used to unlock additional diagnostic functions.</td>
    </tr>
    <tr>
      <td>0x04</td>
      <td>Safety System Diagnostic System</td>
      <td>Used to test all safety critical diagnostic functions.</td>
    </tr>
  </tbody>
</table>

---

## ECU Reset (0x11)

<table class="hover-table">
  <thead>
    <tr>
      <th>Diagnostic session type<br>(sub function value)</th>
      <th>Diagnostic session</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Default Session</td>
      <td>On Start of ECU, ECU runs in to default session.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Programming Session</td>
      <td>Used to upload software.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Extended Diagnostic Session</td>
      <td>Used to unlock additional diagnostic functions.</td>
    </tr>
    <tr>
      <td>0x04</td>
      <td>Safety System Diagnostic System</td>
      <td>Used to test all safety critical diagnostic functions.</td>
    </tr>
  </tbody>
</table>

---

## Security Access (0x27)

<table class="hover-table">
  <thead>
    <tr>
      <th>Security Access type<br>(Sub function value)</th>
      <th>Security Access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01<br>0x03<br>0x05<br>0x07<br>0x09<br>0x0B<br>0x0D<br>0x0F</td>
      <td>Request Seed</td>
      <td>Requests a seed for security access.</td>
    </tr>
    <tr>
      <td>0x02<br>0x04<br>0x06<br>0x08<br>0x0A<br>0x0C<br>0x0E<br>0x10</td>
      <td>Send Key</td>
      <td>Sends a key corresponding to the requested seed.</td>
    </tr>
  </tbody>
</table>

---

## Communication Control (0x28)

<table class="hover-table">
  <thead>
    <tr>
      <th>Control type<br>(Sub function value)</th>
      <th>Control</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>Enable Rx and Tx</td>
      <td>Enables both reception (Rx) and transmission (Tx).</td>
    </tr>
    <tr>
      <td>0x01</td>
      <td>Enable Rx and Disable Tx</td>
      <td>Enables reception and disables transmission.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Disable Rx and Enable Tx</td>
      <td>Disables reception and enables transmission.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Disable Rx and Tx</td>
      <td>Disables both reception and transmission.</td>
    </tr>
  </tbody>
</table>

---

## Authentication (0x29)

---

## Tester Present (0x3E)

<table class="hover-table">
  <thead>
    <tr>
      <th>(Sub function value)</th>
      <th>function</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>Zero Sub-Function</td>
      <td>Indicates the tester is present.</td>
    </tr>
  </tbody>
</table>

---

## Access Timing Parameters (0x83)

<table class="hover-table">
  <thead>
    <tr>
      <th>Timing parameter access Type<br>(Sub function value)</th>
      <th>Timing parameter access</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Set Timing Parameters</td>
      <td>Sets specific timing parameters.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Get Timing Parameters</td>
      <td>Retrieves current timing parameters.</td>
    </tr>
  </tbody>
</table>

---

## Secured Data Transmission (0x84)

---

## Control DTC Settings (0x85)

<table class="hover-table">
  <thead>
    <tr>
      <th>DTC Setting type<br>(Sub function value)</th>
      <th>DTC Setting</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>DTC Setting On</td>
      <td>Activates DTC setting.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>DTC Setting Off</td>
      <td>Deactivates DTC setting.</td>
    </tr>
  </tbody>
</table>

---

## Response On Event (0x86)

<table class="hover-table">
  <thead>
    <tr>
      <th>Event type<br>(Sub function value)</th>
      <th>Event</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>Report Events</td>
      <td>Sends a response when specified events occur.</td>
    </tr>
    <tr>
      <td>0x01</td>
      <td>Clear Event Reporting</td>
      <td>Disables event reporting.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Report Events with Enhanced Data</td>
      <td>Sends a response with additional data when events occur.</td>
    </tr>
  </tbody>
</table>

---

## Link Control (0x87)

<table class="hover-table">
  <thead>
    <tr>
      <th>Link control type<br>(Sub function value)</th>
      <th>Link control</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Verify Link Control</td>
      <td>Verifies the link status.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Transition to Communication Mode</td>
      <td>Switches to communication mode.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Transition to Link Control Mode</td>
      <td>Switches to link control mode.</td>
    </tr>
    <tr>
      <td>0x04</td>
      <td>Transition to Sleep Mode</td>
      <td>Switches to sleep mode.</td>
    </tr>
    <tr>
      <td>0x05</td>
      <td>Terminate Communication</td>
      <td>Ends the communication session.</td>
    </tr>
  </tbody>
</table>

---

## Read Data By Identifier (0x22)


---

## Read Memory By Address (0x23)

---

## Read Scaling Data By Identifier (0x24)

---

## Read Data By Identifier Periodic (0x2A)

---

## Dynamically Define Data Identifier (0x2C)

<table class="hover-table">
  <thead>
    <tr>
      <th>Definition type<br>(Sub function value)</th>
      <th>Definition</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Define By Identifier</td>
      <td>Defines a data identifier dynamically using existing identifiers.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Define By Memory Address</td>
      <td>Defines a data identifier dynamically using memory addresses.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Clear Dynamically Defined Data Identifier</td>
      <td>Clears previously defined data identifiers.</td>
    </tr>
  </tbody>
</table>

---

## Write Data By Identifier (0x2E)

---

## Write Memory By Address (0x3D)

---

## Clear Diagnostic Information (0x14)

---

## Read DTC Information (0x19)

<table class="hover-table">
  <thead>
    <tr>
      <th>Report type<br>(sub function value)</th>
      <th>Report</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Number of DTC by Status Mask</td>
      <td>Reports the number of DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>DTC by Status Mask</td>
      <td>Reports the DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>DTC Snapshot Identification</td>
      <td>Reports the identification of DTC snapshots.</td>
    </tr>
    <tr>
      <td>0x04</td>
      <td>DTC Snapshot Record by DTC Number</td>
      <td>Reports the snapshot records by DTC number.</td>
    </tr>
    <tr>
      <td>0x05</td>
      <td>DTC Stored Data by Record Number</td>
      <td>Reports the stored DTC data by record number.</td>
    </tr>
    <tr>
      <td>0x06</td>
      <td>DTC Extended Data Record by DTC Number</td>
      <td>Reports the extended data records by DTC number.</td>
    </tr>
    <tr>
      <td>0x07</td>
      <td>Number of DTC by Severity Mask Record</td>
      <td>Reports the number of DTCs matching the severity mask.</td>
    </tr>
    <tr>
      <td>0x08</td>
      <td>DTC by Severity Mask Record</td>
      <td>Reports the DTCs matching the severity mask.</td>
    </tr>
    <tr>
      <td>0x09</td>
      <td>Severity Information of DTC</td>
      <td>Reports the severity information of a specific DTC.</td>
    </tr>
    <tr>
      <td>0x0A</td>
      <td>Supported DTC</td>
      <td>Reports the supported DTCs.</td>
    </tr>
    <tr>
      <td>0x0B</td>
      <td>First Failed DTC</td>
      <td>Reports the first failed DTC.</td>
    </tr>
    <tr>
      <td>0x0C</td>
      <td>First Confirmed DTC</td>
      <td>Reports the first confirmed DTC.</td>
    </tr>
    <tr>
      <td>0x0D</td>
      <td>Most Recent Failed DTC</td>
      <td>Reports the most recent failed DTC.</td>
    </tr>
    <tr>
      <td>0x0E</td>
      <td>Most Recent Confirmed DTC</td>
      <td>Reports the most recent confirmed DTC.</td>
    </tr>
    <tr>
      <td>0x0F</td>
      <td>Mirror Memory DTC by Status Mask</td>
      <td>Reports the mirror memory DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x10</td>
      <td>Mirror Memory DTC by DTC Number</td>
      <td>Reports the mirror memory DTCs by DTC number.</td>
    </tr>
    <tr>
      <td>0x11</td>
      <td>Number of Mirror Memory DTC by Status Mask</td>
      <td>Reports the number of mirror memory DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x12</td>
      <td>Number of Emissions OBD DTC by Status Mask</td>
      <td>Reports the number of emissions-related OBD DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x13</td>
      <td>Emissions OBD DTC by Status Mask</td>
      <td>Reports the emissions-related OBD DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x14</td>
      <td>DTC Fault Detection Counter</td>
      <td>Reports the DTC fault detection counter.</td>
    </tr>
    <tr>
      <td>0x15</td>
      <td>DTC with Permanent Status</td>
      <td>Reports the DTCs with permanent status.</td>
    </tr>
    <tr>
      <td>0x16</td>
      <td>DTC Extended Data Record by Record Number</td>
      <td>Reports the extended data records by record number.</td>
    </tr>
    <tr>
      <td>0x17</td>
      <td>User Defined Memory DTC by Status Mask</td>
      <td>Reports the user-defined memory DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x18</td>
      <td>User Defined Memory DTC Snapshot by Number</td>
      <td>Reports the user-defined memory DTC snapshots by number.</td>
    </tr>
    <tr>
      <td>0x19</td>
      <td>User Defined Memory DTC Record by Number</td>
      <td>Reports the user-defined memory DTC records by number.</td>
    </tr>
    <tr>
      <td>0x42</td>
      <td>WWH-OBD DTC by Status Mask Record</td>
      <td>Reports the WWH-OBD DTCs matching the status mask.</td>
    </tr>
    <tr>
      <td>0x55</td>
      <td>WWH-OBD DTCs with Permanent Status</td>
      <td>Reports the WWH-OBD DTCs with permanent status.</td>
    </tr>
  </tbody>
</table>

---

## Input Output Control By Identifier (0x2F)

---

## Routine Control (0x31)

<table class="hover-table">
  <thead>
    <tr>
      <th>Routine control type<br>(Sub function value)</th>
      <th>Routine control</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x01</td>
      <td>Start Routine</td>
      <td>Starts a specified routine.</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>Stop Routine</td>
      <td>Stops a specified routine.</td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>Request Routine Results</td>
      <td>Requests the results of a specified routine.</td>
    </tr>
  </tbody>
</table>

---

## Request Download (0x34)

---

## Request Upload (0x35)

---

## Transfer Data (0x36)

---

## Request Transfer Exit (0x37)

---

## Request File Transfer (0x38)

---

## Negative Response (0x7F)

---

