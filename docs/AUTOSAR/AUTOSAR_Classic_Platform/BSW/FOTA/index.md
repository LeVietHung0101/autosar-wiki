---
title: FOTA
parent: BSW
nav_order: 6
has_children: true
---

# Firmware Over-The-Air (FOTA)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Giới thiệu

{: .note }
FOTA giới thiệu một cơ chế chung để cập nhật phần mềm ECU trong khi hệ thống vẫn đang vận hành (runtime). Điểm khác biệt cốt lõi là trong khi phần mềm hiện tại vẫn đang thực thi và sẵn sàng về mặt chức năng (ví dụ: khi xe đang chạy), phần mềm mới sẽ được lập trình ở chế độ nền (background installation).

Các đặc điểm then chốt:
- **Tính ngắt quãng:** Quá trình cài đặt có thể bị tạm dừng và tiếp tục xuyên suốt nhiều chu kỳ lái xe khác nhau.
- **Xác thực:** Tính xác thực và toàn vẹn của phần mềm mới phải được kiểm tra trong giai đoạn cài đặt.
- **Kích hoạt an toàn:** Việc kích hoạt phần mềm mới (activation) luôn yêu cầu một chế độ ECU đặc biệt (như chế độ boot) và chỉ được thực hiện khi xe ở trạng thái an toàn (xe dừng hẳn, tắt máy, kéo phanh tay); tuyệt đối không kích hoạt khi xe đang chạy.
- **Khả năng Rollback:** Nếu phát hiện bất thường sau khi kích hoạt, ECU có khả năng tự quay lui (internal rollback) về phần mềm cũ vốn vẫn được lưu giữ bên trong ECU.

---

## Lý do phát triển FOTA

Sự gia tăng độ phức tạp của phần mềm và yêu cầu bảo mật khiến nhu cầu cập nhật hệ thống trở nên liên tục.

**Hạn chế của phương pháp cũ:** Trước đây, việc cập nhật qua Flash Bootloader yêu cầu xe phải ngừng hoạt động hoàn toàn (downtime), gây bất tiện khi phải đến gara và có nguy cơ làm "treo" ECU (bricked) nếu xảy ra lỗi trong quá trình cài đặt.

**Lợi ích của FOTA:** Cách tiếp cận mới giúp **giảm đáng kể thời gian dừng xe** vì phần lớn quá trình (cài đặt) diễn ra ngầm khi xe đang chạy. Ngoài ra, FOTA cho phép **cài đặt song song trên nhiều ECU** và kích hoạt tập trung, giúp tối ưu hóa việc cập nhật cho các chức năng phân tán.