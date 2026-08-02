---
title: SPRMIB
parent: UDS
nav_order: 5
---

<h1>Suppress Positive Response Message Indication Bit (SPRMIB)</h1>
*Bit thông báo phản hồi tích cực bị triệt tiêu*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


---

# 1. Định nghĩa

{: .note }
**suppressPosRspMsgIndicationBit (SPRMIB)** là bit dùng để thông báo cho server có cần gửi positive response hay không. SPRMIB chỉ hỗ trợ các SubFunction, và là bit thứ 7 của SubFunction.

<table class="hover-table">
  <tbody>
    <tr>
      <td>SPRMIB = 0 (FALSE)</td>
      <td>server sẽ gửi PosRsp/NegRsp tới tester.</td>
    </tr>
    <tr>
      <td>SPRMIB = 1 (TRUE)</td>
      <td>server không gửi PosRsp tới tester; nhưng vẫn gửi NegRsp (nếu có).</td>
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

Ví dụ:

Tester gửi message `[10 81]` (Diagnostic Session Control - Default Session) tới 1 ECU.
- Nếu thành công, ECU sẽ không phản hồi.
- Nếu thất bại, ECU phản hồi NegRsp `[7F 10 XX]` với NRC 0xXX.

## Ngoại lệ

**NRC 0x78 (RCRRP)** là một trường hợp đặc biệt của SPRMIB. Khi server gửi NRC `0x78`, điều đó đồng nghĩa với việc request đã được chấp nhận và đang được xử lý. Do đó, sau khi xử lý hoàn tất, server luôn phải gửi một final response (PosRsp/NegRsp), ngay cả khi client đã đặt SPRMIB = 1 hoặc NegRsp thông thường đáng lẽ sẽ bị suppress trong Functional Addressing.

Khi client dùng **Functional Addressing** để gửi resquest message, các NRC sau sẽ **không được gửi** (suppress) bởi server:
- 0x11 - ServiceNotSupported (SNS)
- 0x12 - SubFunctionNotSupported (SFNS)
- 0x31 - RequestOutOfRange (ROOR)
- 0x7E - SubFunctionNotSupportedInActiveSession (SFNSIAS)
- 0x7F - ServiceNotSupportedInActiveSession (SNSIAS)

## Hạn chế

Nếu **SPRMIB = 1 (TRUE)** đối với các service có **response chứa lượng dữ liệu lớn** và cần sử dụng **paged-buffer-handling**, có thể xảy ra tình huống sau:

- Việc truyền phần dữ liệu đầu tiên (first batch of data) vẫn có thể bắt đầu trong khoảng thời gian phản hồi (response timing window).

- Tuy nhiên, toàn bộ quá trình thực thi service chỉ hoàn thành sau khi response timing window đã hết.

Trong trường hợp response bị suppress, server sẽ không có cách nào thông báo cho client rằng việc xử lý đang bị kéo dài. Mặc dù client không nhận được phản hồi nào, server vẫn đang bận xử lý yêu cầu và chưa sẵn sàng nhận một request mới. Điều này có thể dẫn đến nguy cơ client gửi request mới trong khi server vẫn chưa hoàn thành yêu cầu trước đó.

### Khuyến nghị đối với Client

Client không nên vừa yêu cầu một response chứa lượng dữ liệu lớn, vừa đặt SPRMIB = 1 trong cùng một request.
Ví dụ: `[19 0A]` Read DTC Information - Reports the supported DTCs.

### Khuyến nghị đối với Server

Nếu server phải sử dụng paged-buffer-handling để xử lý response, trong khi SPRMIB = 1, thì server nên:
- Gửi NRC 0x78 (RCRRP) để thông báo rằng request đã được chấp nhận nhưng vẫn đang được xử lý.
- Sau khi xử lý hoàn tất, tiếp tục gửi PosRsp.


---

# 2. Danh sách các Service có SubFunction

*SPRMIB chỉ hỗ trợ các SubFunction*

<table class="hover-table">
  <thead>
    <tr>
      <th>SID</th>
      <th>Service</th>
      <th>Details</th>
      <th>SubFunction type</th>
      <th>SubFunction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0x10</td>
      <td>Diagnostic Session Control</td>
      <td>Control which UDS services are available</td>
      <td>Diagnostic session type</td>
      <td>0x01 – 0x04</td>
    </tr>
    <tr>
      <td>0x11</td>
      <td>ECU Reset</td>
      <td>Reset the ECU (hard reset, key off, soft reset)</td>
      <td>Reset type</td>
      <td>0x01 – 0x05</td>
    </tr>
    <tr>
      <td>0x27</td>
      <td>Security Access</td>
      <td>Enable use of security-critical services via authentication</td>
      <td>Security access type</td>
      <td>0x01 – 0x10</td>
    </tr>
    <tr>
      <td>0x28</td>
      <td>Communication Control</td>
      <td>Turn sending/receiving of messages on/off in the ECU</td>
      <td>Control type</td>
      <td>0x00 – 0x03</td>
    </tr>
    <tr>
      <td>0x3E</td>
      <td>Tester Present</td>
      <td>Send periodic heartbeat to remain in current session</td>
      <td>Zero sub-function</td>
      <td>0x00</td>
    </tr>
    <tr>
      <td>0x83</td>
      <td>Access Timing Parameters</td>
      <td>View or modify timing parameters used in communication</td>
      <td>Timing parameter access type</td>
      <td>0x01 – 0x02</td>
    </tr>
    <tr>
      <td>0x85</td>
      <td>Control DTC Settings</td>
      <td>Enable or disable DTC detection</td>
      <td>DTC setting type</td>
      <td>0x01 – 0x02</td>
    </tr>
    <tr>
      <td>0x86</td>
      <td>Response On Event</td>
      <td>Trigger service execution when an event occurs</td>
      <td>Event type</td>
      <td>0x00 – 0x02</td>
    </tr>
    <tr>
      <td>0x87</td>
      <td>Link Control</td>
      <td>Set baud rate for diagnostic communication</td>
      <td>Link control type</td>
      <td>0x01 – 0x05</td>
    </tr>
    <tr>
      <td>0x2C</td>
      <td>Dynamically Defined Data Identifier</td>
      <td>Dynamically define data for use in 0x22 or 0x2A</td>
      <td>Definition type</td>
      <td>0x01 – 0x03</td>
    </tr>
    <tr>
      <td>0x19</td>
      <td>Read DTC Information</td>
      <td>Read stored DTCs and related information</td>
      <td>Report type</td>
      <td>0x01 – 0x0D</td>
    </tr>
    <tr>
      <td>0x31</td>
      <td>Routine Control</td>
      <td>Start or stop ECU routines</td>
      <td>Routine control type</td>
      <td>0x01 – 0x03</td>
    </tr>
  </tbody>
</table>
