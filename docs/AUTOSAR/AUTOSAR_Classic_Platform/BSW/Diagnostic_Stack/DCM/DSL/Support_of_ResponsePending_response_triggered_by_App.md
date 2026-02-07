---
title: Support of ResponsePending response triggered by the Application
parent: DSL
nav_order: 10
---

# Support of ResponsePending response triggered by the Application
*Hỗ trợ phản hồi ResponsePending do App kích hoạt.*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Mục đích và nhu cầu thực tế
Thông thường, **Dcm** tự động gửi phản hồi **NRC 0x78 (Response pending)** ngay trước khi hết hạn thời gian `P2ServerMax` hoặc `P2*ServerMax` để giữ kết nối. Tuy nhiên, trong một số trường hợp cụ thể (ví dụ: khi thực thi một routine), **App có thể cần yêu cầu gửi NRC 0x78 ngay lập tức** mà không cần đợi đến khi sắp timeout.

---

## 2. Cơ chế kích hoạt

Khi **Dcm** gọi một thao tác (operation) của **App** và nhận được mã trạng thái lỗi `DCM_E_FORCE_RCRRP`, **DSL** sẽ kích hoạt việc truyền negative response NRC 0x78, đồng thời **Dcm** sẽ **không gọi lại thao tác đó** cho đến khi việc truyền NRC 0x78 hoàn tất.

Sau khi có xác nhận đã truyền xong NRC 0x78, từ chu kỳ của <span class="text-orange">`Dcm_MainFunction()`</span>, **Dcm** sẽ gọi lại thao tác đó với tham số `OpStatus` = `DCM_FORCE_RCRRP_OK`.




<details markdown="block">
<summary><code>DCM_E_FORCE_RCRRP</code></summary>

> <strong>Description</strong>: Application requests the transmission of a response Response Pending (NRC 0x78).  
{: .codeBlock }
</details>


<details markdown="block">
<summary><code>OpStatus</code></summary>

> <strong>OpStatus (Operation Status)</strong> là tham số thuộc kiểu <code>Dcm_OpStatusType</code>, được sử dụng trong các hàm thực thi dịch vụ chẩn đoán để quản lý trạng thái của các tác vụ, đặc biệt là các tác vụ không đồng bộ (asynchronous) hoặc tốn nhiều thời gian xử. OpStatus có thể mang các giá trị sau:  
> DCM_INITIAL  
> DCM_PENDING  
> DCM_CANCEL  
> DCM_FORCE_RCRRP_OK  
> 
> Cơ chế:
> 1. <strong>Dcm</strong> gọi <strong>App</strong> với <code>OpStatus</code> = <code>DCM_INITIAL</code>.
> 2. Nếu <strong>App</strong> chưa xử lý xong, nó trả về <code>DCM_E_PENDING</code>.
> 3. <strong>Dcm</strong> tiếp tục gọi lại với <code>OpStatus</code> = <code>DCM_PENDING</code> cho đến khi nhận được kết quả cuối cùng (<code>E_OK</code> hoặc <code>E_NOT_OK</code>).
> 4. Nếu <strong>App</strong> trả về <code>DCM_E_FORCE_RCRRP</code>, <strong>Dcm</strong> sẽ gửi phản hồi NRC 0x78 ngay lập tức, tạm dừng gọi <strong>App</strong> cho đến khi gửi xong, rồi mới gọi lại với <code>OpStatus</code> = <code>DCM_FORCE_RCRRP_OK</code>.
{: .codeBlock }
</details>



---

## 3. Quản lý tài nguyên (Bộ đệm riêng)

Phản hồi NRC 0x78 được kích hoạt bởi **App** phải được gửi từ một **bộ đệm riêng biệt (separate buffer)**.

**Lý do:** Việc sử dụng bộ đệm tách biệt này là cực kỳ quan trọng để **tránh ghi đè dữ liệu** lên quá trình xử lý yêu cầu đang diễn ra.

**Ví dụ:** nếu **App** đã chuẩn bị một phần nội dung positive response trong diagnostic buffer nhưng cần thêm thời gian để hoàn tất, việc gửi NRC 0x78 từ diagnostic buffer sẽ làm mất các dữ liệu đã chuẩn bị đó.
