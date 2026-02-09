---
title: Receive a request message and transmit a negative response message
parent: DSD
nav_order: 5
---

# Receive a request message and transmit a negative response message
*Nhận một thông điệp yêu cầu và truyền một thông điệp negative response*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Cơ chế

Một yêu cầu chẩn đoán có thể bị từ chối và phải gửi negative response vì nhiều lý do khác nhau:

- **DSD từ chối:** Nếu yêu cầu không hợp lệ hoặc không được phép thực thi trong session hiện tại, DSD sẽ từ chối xử lý và gửi negative response.

- **DSP từ chối:** Ngay cả khi thông điệp yêu cầu đúng định dạng, nó vẫn có thể bị từ chối nếu trạng thái của ECU hoặc hệ thống không cho phép thực thi. Khi đó, DSP sẽ kích hoạt negative response kèm theo NRC tương ứng để giải thích lý do.

- **Xử lý đa tham số:** Đối với các yêu cầu có nhiều tham số (ví dụ: dịch vụ 0x22 đọc nhiều DID cùng lúc), mỗi tham số được xử lý riêng biệt. Nếu có ít nhất một tham số được xử lý thành công, ECU sẽ gửi positive response.

- **Thứ tự kiểm tra:** DSD thực hiện kiểm tra yêu cầu theo thứ tự quy định bởi **ISO 14229-1**. Nếu bất kỳ bước kiểm tra nào thất bại, DSD sẽ dừng chuỗi kiểm tra, ngừng thực thi dịch vụ và truyền NRC tương ứng.

---

## Thứ tự kiểm tra NRC của DSD đối với request không có subfunction

1. 0x21 – Busy Repeat Request
1. 0xXX – Manufacturer specific failure detected.
1. 0x11 – Service Not Supported
1. 0x7F – Service Not Supported in Active Session
1. 0x33 – Security Access Denied (nếu áp dụng)
1. 0xXX – Supplier specific failure detected.
1. Chuyển sang specific SID checks nếu SID = 0x31.



<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSD\GeneralServerResponseBehaviour.png"
    alt="suppressPosRspMsgIndicationBit (SPRMIB)"
  />
  <figcaption>Hành vi phản hồi chung của server</figcaption>
</figure>

---

## Thứ tự kiểm tra NRC của DSD đối với request có subfunction:

1. 0x13 – Incorrect Message Length / Invalid Format
1. 0x12 – Sub-function Not Supported
1. 0x7E – Sub-function Not Supported in Active Session
1. 0x33 – Security Access Denied (for subfunction)
1. 0x24 – Request Sequence Error (for subfunction)
1. 0xXX – Manufacturer / Supplier specific failure detected.
1. Chuyển sang specific SID checks.


<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\DSD\GeneralServerResponseBehaviourForReqtMsgWithSubFuncPara.png"
    alt="suppressPosRspMsgIndicationBit (SPRMIB)"
  />
  <figcaption>Hành vi phản hồi chung của máy chủ đối với các thông báo yêu cầu có tham số Sub-function</figcaption>
</figure>