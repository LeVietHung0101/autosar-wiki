---
title: Send a positive response message without corresponding request
parent: DSD
nav_order: 6
---

# Send a positive response message without corresponding request
*Gửi một thông điệp phản hồi tích cực mà không có yêu cầu tương ứng*

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Có hai dịch vụ UDS cho phép một request duy nhất kích hoạt nhiều response (định kỳ hoặc theo sự kiện), và các response sau được ECU gửi tự động mà không cần request mới.

Đây là cơ chế "subscription" kiểu chẩn đoán: client "subscribe" một lần → ECU gửi dữ liệu nhiều lần theo thời gian hoặc sự kiện. Tuân thủ ISO 14229-1 (phần 7.3 về periodic/event-triggered). Ví dụ: Trong xe, client subscribe dữ liệu tốc độ động cơ → ECU gửi định kỳ mà không cần hỏi lại, giúp theo dõi liên tục mà không làm nghẽn bus.

Service 0x2A:
Client yêu cầu truyền định kỳ (periodic) dữ liệu từ server bằng một hoặc nhiều periodicDataIdentifiers - PID.
Type 2: Sử dụng UUDT trên DcmTxPduId riêng (kênh truyền riêng).
Ví dụ: Client gửi 0x2A + list PID (như PID tốc độ, nhiệt độ) → ECU gửi định kỳ dữ liệu đó (ví dụ: mỗi 100ms). Type 2 dùng kênh riêng để tránh xung đột với giao tiếp chẩn đoán bình thường. Hữu ích cho logging dữ liệu liên tục trong kiểm tra xe hoặc phát triển.

Service 0x86:
Client yêu cầu server bắt đầu/dừng gửi response khi có event cụ thể (ví dụ: lỗi DTC mới, thay đổi trạng thái).
Type 1: Sử dụng USDT trên DcmTxPduId bình thường (kênh chẩn đoán chuẩn).
Type 2: Sử dụng USDT trên DcmTxPduId riêng.
Ví dụ: Client gửi 0x86 để subscribe "khi có DTC mới" → ECU tự gửi response (như 0x86 + dữ liệu lỗi) khi event xảy ra. Type 1 dùng kênh chung (tiết kiệm tài nguyên), Type 2 dùng kênh riêng (tránh ảnh hưởng giao tiếp khác). Rất quan trọng trong chẩn đoán sự kiện (event-driven), như báo lỗi túi khí ngay lập tức.

UUDT (Unacknowledged Unsegmented Data Transfer)
Là phương thức truyền dữ liệu trong giao thức UDS (Unified Diagnostic Services) mà không yêu cầu xác nhận từ bên nhận và không cần phân đoạn dữ liệu, nghĩa là dữ liệu nhỏ gọn chỉ gửi trong một khung (frame) duy nhất, thường dùng cho các thông điệp định kỳ hoặc tự phát để giảm tải hệ thống.

USDT (Unacknowledged Segmented Data Transfer)
Là phương thức truyền dữ liệu không yêu cầu xác nhận từ bên nhận nhưng hỗ trợ phân đoạn dữ liệu lớn thành nhiều khung để truyền, thường áp dụng cho thông điệp phức tạp hoặc event-driven.

Type 1:
Các response tự phát phải đồng bộ với "normal outgoing messages" (phản hồi chẩn đoán thông thường), và normal messages có ưu tiên cao hơn.
DSL quản lý hàng đợi truyền (transmission queue) với ưu tiên: Normal diagnostic responses (như 0x10, 0x22) ưu tiên cao hơn unsolicited responses. Nếu bus bận, unsolicited response chờ, tránh mất thông tin quan trọng. Ví dụ: Nếu đang trả lời yêu cầu đọc dữ liệu, thì event-triggered response phải chờ đến lượt.
Xử lý chủ yếu bởi DSL (quản lý truyền, đồng bộ, ưu tiên, TxPduId). Nhưng DSD cũng hỗ trợ generate response không request (tạo nội dung response).
