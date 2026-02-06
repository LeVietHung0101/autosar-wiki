---
title: Manage authentication state
parent: DSL
nav_order: 13
---

# Manage authentication state
*Quản lý trạng thái xác thực*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## 1. Tổng quan và Quản lý theo kết nối
- **Dcm** cung cấp các phương tiện cho việc **chẩn đoán được xác thực (authenticated diagnostics)**.
- **DSL** có trách nhiệm cung cấp và quản lý trạng thái xác thực cho **từng kết nối chẩn đoán (diagnostic connection)** riêng biệt. 
- Trạng thái (state) này được khởi tạo khi hệ thống bắt đầu và sẽ tự động quay về trạng thái chưa xác thực nếu kết nối không hoạt động trong một khoảng thời gian.
- **Dcm** thể hiện trạng thái xác thực thông qua `ModeDeclarationGroupPrototype` `DcmAuthentication_<ConnectionName>`. Nhóm này chỉ được phép thay đổi bởi **Dcm**; các **App** thay đổi trạng thái này sẽ không làm ảnh hưởng đến trạng thái xác thực nội bộ của **Dcm**.

---

## 2. Các trạng thái xác thực
**Dcm** hỗ trợ hai trạng thái xác thực cơ bản cho mỗi kết nối:
- **deauthenticated (chưa xác thực):** Trạng thái mặc định khi khởi tạo.
- **authenticated (đã xác thực):** Chỉ đạt được sau khi Tester thực hiện thành công quy trình xác thực.

Trạng thái **authenticated** có thể được lưu trữ (persist) qua các chu kỳ power-on/power off nếu có mode rule tương ứng được cấu hình.

Việc chuyển đổi giữa các trạng thái xác thực được điều khiển bởi cả hai submodule **DSL** và **DSP**.
- **DSL** chịu trách nhiệm chuyển đổi từ **authenticated** sang **deauthenticated** (Fallback).
- **DSP** chịu trách nhiệm xử lý các thay đổi chuyển đổi được kích hoạt từ phía client bởi các diagnostic services.

<details markdown="block">
<summary><code>DcmModeRule</code></summary>

> <strong>Description</strong>: Container này chứa cấu hình của một mode rule. Mode rule này xác định một biểu thức logic với `DcmModeConditions` hoặc các `DcmModeRules` khác làm đối số (argument). Tất cả các đối số được xử lý bằng toán tử được định nghĩa bởi `DcmLogicalOperator`, ví dụ: Argument_A **AND** Argument_B **AND** Argument_C.  
{: .codeBlock }
</details>

---

## 3. Khởi tạo trạng thái
- **Khởi tạo thông thường:** Nếu `DcmDspAuthenticationPersistStateModeRuleRef` không được cấu hình, hoặc mode rule (referenced bởi `DcmDspAuthenticationPersistStateModeRuleRef`) trả về giá trị *false*, **Dcm** phải đặt tất cả trạng thái xác thực về **deauthenticated** trong <span class="text-orange">`Dcm_Init()`</span>.
- **Khởi tạo trạng thái xác thực được lưu trữ:** Nếu mode rule trả về *true*, **Dcm** phải khôi phục trạng thái xác thực đã lưu trước đó bằng <span class="text-orange">`Dcm_Init()`</span>, bao gồm cả thông tin về **role** và **white list** trên mỗi kết nối.


<details markdown="block">
<summary><code>DcmDspAuthenticationPersistStateModeRuleRef</code></summary>

> <strong>Description</strong>: References đến một mode rule xác định xem trạng thái xác thực có được lưu trữ (persisted) trong non-volatile memory hay không.  
{: .codeBlock }
</details>

---

## 4. Cơ chế quay lại trạng thái chưa xác thực (Fallback)
DSL chịu trách nhiệm đưa kết nối từ **authenticated** về lại **deauthenticated** trong các trường hợp sau:
- **Idle connection:** Khi **Dcm** đang ở default session và không nhận được diagnostic request hợp lệ nào trong khoảng thời gian `DcmDspAuthenticationDefaultSessionTimeOut` kể từ lần xác nhận truyền (<span class="text-orange">`Dcm_TpTxConfirmation()`</span>) cuối cùng.
- **S3Server timeout:** Nếu **Dcm** đang ở non-default session và S3Server timeout.

Khi thực hiện fallback, **Dcm** sẽ **xóa tất cả thông tin xác thực đã lưu trữ** trên kết nối đó, hủy bỏ **role** và **white list** hiện tại, sử dụng deauthentication role được cấu hình trong `DcmDspAuthenticationDeauthenticatedRole`.

---

## 5. API ghi đè vai trò (Dcm_SetDeauthenticatedRole)
**Dcm** cung cấp API `Dcm_SetDeauthenticatedRole()` để **App** có thể chủ động thiết lập vai trò (**role**). Trong một số trường hợp, việc này sẽ hiệu quả hơn việc thực hiện các diagnostic services với quá trình phân tích chứng chỉ (certificate parsing) có thể tốn nhiều thời gian.

Một số lưu ý:
- **Chỉ thiết lập deauthenticated role bởi SWC khi ở deauthenticated**: **Dcm** chỉ xử lý lệnh gọi `Dcm_SetDeauthenticatedRole()` nếu kết nối đang ở trạng thái **deauthenticated**.
- **Thời gian tồn tại (Lifetime) của deauthenticated role bởi SWC**: Một deauthenticated role được thiết lập bởi `Dcm_SetDeauthenticatedRole()` sẽ bi loại bỏ khi kết nối đó chuyển sang **authenticated**.
- **Không có sự duy trì deauthenticated roles bởi SWC**: Khi ECU power-on, ECU luôn sử dụng **deauthenticated** được cấu hình trong `DcmDspAuthenticationDeauthenticatedRole`.


<!-- - API này chỉ có hiệu lực khi kết nối đang ở trạng thái **deauthenticated**.
- Vai trò được thiết lập qua API này sẽ bị hủy bỏ ngay khi kết nối chuyển sang trạng thái **authenticated** và không được lưu trữ qua các chu kỳ khởi động. -->