---
title: Receive a request message and transmit a negative response message
parent: DSD
nav_order: 5
---

# Receive a request message and transmit a negative response message
*Nhận một thông điệp yêu cầu và truyền một thông điệp phản hồi tiêu cực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

DSD sẽ từ chối và gửi phản hồi tiêu cực nếu request
Không hợp lệ (sai định dạng).
Không được thực hiện trong session hiện tại.

Ngay cả yêu cầu định dạng đúng cũng có thể bị từ chối do trạng thái ECU/hệ thống (ví dụ: xe đang chạy, pin yếu). DSP sẽ kích hoạt phản hồi tiêu cực với NRC chi tiết.
Ví dụ:
Yêu cầu xóa DTC (0x14) nhưng không có DTC nào, DSP gửi NRC 0x31 (Request Out Of Range).
Trạng thái ECU/hệ thống đang bận (busy), DSP NRC 0x21 (Busy Repeat Request).

Với yêu cầu đa tham số (ví dụ: 0x22 với nhiều DID - Data Identifier), mỗi tham số xử lý riêng, và có thể lỗi riêng. Phản hồi tích cực nếu ít nhất một tham số thành công.

[SWS_Dcm_00827]
DSD phải kiểm tra yêu cầu theo thứ tự ISO 14229-1 (7.5.2, 7.5.3). Nếu thất bại, dừng chuỗi kiểm tra NRC sau đó, dừng thực hiện yêu cầu, và gửi NRC tương ứng.

Thứ tự kiểm tra NRC của DSD đối với request không có subfunction:
0x21 – Busy Repeat Request
0xXX – Manufacturer specific failure detected.
0x11 – Service Not Supported
0x7F – Service Not Supported in Active Session
0x33 – Security Access Denied (nếu áp dụng)
0xXX – Supplier specific failure detected.
→ Chuyển sang specific SID checks nếu SID = 0x31.

Thứ tự kiểm tra NRC của DSD đối với request có subfunction:
0x13 – Incorrect Message Length / Invalid Format
0x12 – Sub-function Not Supported
0x7E – Sub-function Not Supported in Active Session
0x33 – Security Access Denied (for subfunction)
0x24 – Request Sequence Error (for subfunction)
0xXX – Manufacturer / Supplier specific failure detected.
→ Chuyển sang specific SID checks.

Manufacturer (OEM)
Hãng sản xuất xe hoàn chỉnh (Vehicle Manufacturer).
Chủ sở hữu kiến trúc ECU tổng thể.
Toyota, Hyundai, Volkswagen, BMW, Ford
Supplier là:
Công ty cung cấp ECU, phần mềm nền (BSW), hoặc module chức năng.
Thực thi yêu cầu của manufacturer.
