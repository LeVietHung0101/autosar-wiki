---
title: Diagnostic Trouble Code (DTC)
parent: Unified Diagnostic Services (UDS)
nav_order: 3
---

# Diagnostic Trouble Code (DTC)
<!-- https://dmv.de.gov/VehicleServices/inspections/pdfs/dtc_list.pdf -->
<!-- https://www.linkedin.com/pulse/what-dtc-its-types-how-read-truckx-inc-zu6qf -->

<!-- <strong>Mục lục</strong>
1. TOC
{:toc} -->

---

**Diagnostic Trouble Code (DTC)**: Là mã được hệ thống chẩn đoán trên xe (On-Board Diagnostics - OBD) sử dụng để chẩn đoán và cảnh báo về sự cố. DTC được tạo ra khi ECU của xe phát hiện ra một sự cố cụ thể, cho phép kỹ thuật viên xác định và khắc phục sự cố đó.

---

## DTC Status Byte

**DTC Status Byte**: là một byte cho biết trạng thái của DTC; được trả về khi yêu cầu dịch vụ **Read DTC Information (0x19)**.

- Bao gồm 8-bit riêng lẻ, mỗi bit đại diện cho một trạng thái DTC cụ thể.
- Được ECU sử dụng để báo cáo trạng thái hiện tại của DTC cho tester tool.


<table class="hover-table">
  <thead>
    <tr>
      <th>Bit #</th>
      <th>Hex</th>
      <th>State</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0x01</td>
      <td>testFailed</td>
      <td>DTC failed at the time of the request</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0x02</td>
      <td>testFailedThisOperationCycle</td>
      <td>DTC failed on the current operation cycle</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0x04</td>
      <td>pendingDTC</td>
      <td>DTC failed on the current or previous operation cycle</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0x08</td>
      <td>confirmedDTC</td>
      <td>DTC is confirmed at the time of the request</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0x10</td>
      <td>testNotCompletedSinceLastClear</td>
      <td>DTC test not completed since the last code clear</td>
    </tr>
    <tr>
      <td>5</td>
      <td>0x20</td>
      <td>testFailedSinceLastClear</td>
      <td>DTC test failed at least once since last code clear</td>
    </tr>
    <tr>
      <td>6</td>
      <td>0x40</td>
      <td>testNotCompletedThisOperationCycle</td>
      <td>DTC test not completed this operation cycle</td>
    </tr>
    <tr>
      <td>7</td>
      <td>0x80</td>
      <td>warningIndicatorRequested</td>
      <td>Server is requesting warningIndicator to be active</td>
    </tr>
  </tbody>
</table>

---