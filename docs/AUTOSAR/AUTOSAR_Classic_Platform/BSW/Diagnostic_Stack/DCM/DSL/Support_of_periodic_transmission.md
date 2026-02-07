---
title: Support of periodic transmission
parent: DSL
nav_order: 7
---

# Support of periodic transmission
*Hỗ trợ truyền định kỳ*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Cơ chế truyền định kỳ

Dịch vụ UDS **ReadDataByPeriodicIdentifier (0x2A)** cho phép tester yêu cầu ECU truyền định kỳ các data record values được xác định bởi một (hoặc nhiều) <span class="text-blue">`periodicDataIdentifiers`</span>.

**Cơ chế:**
- Dcm phải gửi các response cho việc truyền dữ liệu định kỳ bằng cách sử dụng một **giao thức riêng biệt (separate protocol)** và một **bộ đệm riêng biệt (separate buffer)** có kích thước có thể cấu hình.
- Tham số cấu hình <span class="text-blue">`DcmDslPeriodicTransmissionConRef`</span> cho phép liên kết giao thức *dùng để nhận periodic transmission request / truyền periodic transmission response* với giao thức dùng để *truyền các periodic transmission messages*.
- Có thể gán nhiều <span class="text-blue">`DcmTxPduId`</span> cho giao thức truyền dữ liệu định kỳ này.





<details markdown="block">
<summary><span class="text-blue"><code>DcmDslPeriodicTransmissionConRef</code></span></summary>

> <strong>Description</strong>: Reference to a periodic transmission connection which is used for the processing of periodic transmission events.  
{: .codeBlock }
</details>






<details markdown="block">
<summary><i>Tại sao có thể gán nhiều <span class="text-blue"><code>DcmTxPduId</code></span> cho giao thức truyền dữ liệu định kỳ?</i></summary>

> <strong>Mục đích</strong>: hỗ trợ khả năng truyền tải song song nhiều luồng dữ liệu UUDT cho dịch vụ 0x2A, giúp tách biệt hoàn toàn luồng dữ liệu định kỳ khỏi luồng phản hồi chẩn đoán chuẩn và tối ưu hóa hiệu suất truyền dẫn trên bus.  
>  
> <strong>1. Bản chất của thông điệp UUDT:</strong>  
> Các phản hồi định kỳ (ngoại trừ phản hồi tích cực ban đầu cho yêu cầu 0x2A) thường sử dụng định dạng **UUDT (Unacknowledged Unsegmented Data Transfer)**: là dạng truyền dữ liệu trực tiếp không phân đoạn và không cần xác nhận ở tầng cao, do đó mỗi loại dữ liệu định kỳ có thể cần một định danh PDU truyền riêng biệt để phân biệt trên bus truyền thông (ví dụ: các CAN ID khác nhau cho các dữ liệu khác nhau).
>  
> <strong>Quản lý nhiều Periodic DIDs cùng lúc:</strong>  
> Khi có nhiều dữ liệu định kỳ được kích hoạt, ECU có thể cần gửi chúng qua các kênh hoặc với các thuộc tính ưu tiên khác nhau trên mạng. Việc cho phép cấu hình nhiều <span class="text-blue"><code>DcmTxPduId</code></span> giúp Dcm linh hoạt trong việc ánh xạ các dữ liệu này tới các luồng truyền dẫn khác nhau mà không bị bó hẹp trong một kết nối duy nhất.  
>  
> <strong>3. Tách biệt với giao thức chẩn đoán chính:</strong>  
> Việc gán nhiều <span class="text-blue"><code>DcmTxPduId</code></span> cho giao thức này cho phép hệ thống duy trì nhiều luồng dữ liệu "broadcast" đồng thời mà không làm ảnh hưởng hoặc gây nghẽn luồng phản hồi vật lý chính của Tester.  
>  
> <strong>4. Thứ tự và Ưu tiên truyền dẫn:</strong>  
> Trong trường hợp có nhiều thông điệp **UUDT** được cấu hình, Dcm sẽ luôn sử dụng cùng một thứ tự truyền các định danh dữ liệu định kỳ cho mỗi Tester. Việc có các <span class="text-blue"><code>DcmTxPduId</code></span> riêng biệt giúp hệ thống xử lý được **tình trạng đảo ngược ưu tiên (priority inversion)** trong quá trình truyền tin, đảm bảo các dữ liệu quan trọng hơn có thể được gửi đi đúng lúc thông qua việc gán ID có ưu tiên cao hơn ở tầng dưới.
{: .codeBlock }
</details>






---

## Ràng buộc về chế độ giao tiếp (Communication Mode):

Việc truyền dữ liệu định kỳ **chỉ được phép diễn ra trong Full Communication Mode (COMM_FULL_COMMUNICATION)**.

Nếu một sự kiện truyền định kỳ xảy ra khi hệ thống không ở chế độ Full Communication, Dcm phải **hủy bỏ (discard)** sự kiện đó và **không được đưa vào hàng đợi** để truyền sau này.

Bản thân các sự kiện truyền dữ liệu định kỳ **không được phép kích hoạt** chế độ Full Communication Mode.