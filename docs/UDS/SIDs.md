---
title: SIDs (Service Identifiers)
nav_order: 3
parent: UDS
---

# SIDs (Service Identifiers)

---

**Service Identifiers - SID**: Đây là các lệnh chính được sử dụng trong giao thức UDS (ISO 14229), dùng để gọi các dịch vụ chẩn đoán cụ thể. Mỗi SID đại diện cho một yêu cầu thực hiện một chức năng chẩn đoán cụ thể. ECU nhận yêu cầu này và trả về phản hồi thích hợp cho dịch vụ tương ứng.

---

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

