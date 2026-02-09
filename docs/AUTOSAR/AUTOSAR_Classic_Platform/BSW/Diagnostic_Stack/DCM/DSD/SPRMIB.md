---
title: SPRMIB
parent: DSD
nav_order: 8
---

# Suppress Positive Response Message Indication Bit (SPRMIB)
*Bit thông báo phản hồi tích cực bị triệt tiêu*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Giới thiệu

{: .note }
**suppressPosRspMsgIndicationBit (SPRMIB)** là bit dùng để thông báo cho ECU có cần gửi positive response hay không. SPRMIB chỉ hỗ trợ các Sub-function, và là bit thứ 7 của Sub-function.

<table class="hover-table">
  <tbody>
    <tr>
      <td>SPRMIB = 0</td>
      <td>ECU sẽ gửi positive/negative response tới tester.</td>
    </tr>
    <tr>
      <td>SPRMIB = 1</td>
      <td>ECU không gửi positive response tới tester; nhưng vẫn gửi nếu negative response.</td>
    </tr>
  </tbody>
</table>

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSD\SPRMIB.svg"
    alt="suppressPosRspMsgIndicationBit (SPRMIB)"
    width="50%"
  />
</figure>

---

# 2. Danh sách các Service có Sub-function


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
    <tr>
      <td>0x2C</td>
      <td>0x6C</td>
      <td>Dynamically Defined Data Identifier</td>
      <td>Dynamically define data for use in 0x22 or 0x2A</td>
      <td>Definition type</td>
      <td>0x01 – 0x03</td>
    </tr>
    <tr>
    <tr>
      <td>0x19</td>
      <td>0x59</td>
      <td>Read DTC Information</td>
      <td>Read stored DTCs and related information</td>
      <td>Report type</td>
      <td>0x01 – 0x0D</td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>0x71</td>
      <td>Routine Control</td>
      <td>Start or stop ECU routines</td>
      <td>Routine control type</td>
      <td>0x01 – 0x03</td>
    </tr>
  </tbody>
</table>
