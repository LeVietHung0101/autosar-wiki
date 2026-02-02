---
title: DCM
parent: Diagnostic Stack
nav_order: 2
has_children: true
---

# Diagnostic Communication Manager (DCM)

<details markdown="block">
  <summary>Mục lục</summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Tổng quan

{: .note }
**Diagnostic Communication Manager (Dcm)** là module BSW trong AUTOSAR Classic, cung cấp API chung cho các dịch vụ chẩn đoán.
DCM được sử dụng bởi các công cụ chẩn đoán bên ngoài (external diagnostic tools) để đọc dữ liệu, chẩn đoán lỗi, hoặc cập nhật phần mềm mà không cần biết chi tiết về mạng lưới nội bộ xe.

DCM được sử dụng trong các quá trình sau:
- **Phát triển (development)**: Giai đoạn phát triển phần mềm và hệ thống, nơi kỹ sư sử dụng công cụ để kiểm tra và debug ECU trong lab.
- **Sản xuất (manufacturing)**: Giai đoạn lắp ráp và kiểm tra tại nhà máy, như cài đặt phần mềm ban đầu hoặc kiểm tra chất lượng.
- **Bảo dưỡng/Dịch vụ (service)**: Sửa chữa tại gara, như đọc mã lỗi động cơ hoặc cập nhật firmware.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\Overview_of_the_communication_between_the_external_diagnostic_tools_and_the_onboard_AUTOSAR_Application.png"
    alt="Overview of the communication between the external diagnostic tools and the On-board AUTOSAR Application"
  />
  <figcaption>Overview of the communication between the external diagnostic tools and the On-board AUTOSAR Application</figcaption>
</figure>

---

## Chức năng chính

**Đảm bảo luồng dữ liệu chẩn đoán**: Dcm đảm bảo dữ liệu từ công cụ chẩn đoán "chảy" đúng cách qua hệ thống, bao gồm nhận yêu cầu, xử lý, và trả lời.

**Quản lý trạng thái**:
- **Diagnostic sessions**: Các phiên chẩn đoán (default session, programming session,...). Mỗi session cho phép các dịch vụ khác nhau, ví dụ programming session cho phép flash ECU.
- **Security states**: Trạng thái bảo mật, để ngăn chặn truy cập trái phép, như yêu cầu khóa/mở khóa bằng seed-key.

**Kiểm tra yêu cầu**:
- Dịch vụ có được hỗ trợ hay không (ví dụ: xe có hỗ trợ đọc dữ liệu cảm biến không?).
- Dịch vụ có phù hợp với trạng thái hiện tại (diagnostic sessions / security states) hay không (ví dụ: không cho phép flash firmware trong default session để tránh rủi ro).

---

## Submodule

Để thực hiện hiệu quả các chức năng trên, Dcm chia thành 3 submodule:
- Diagnostic Session Layer (DSL)
- Diagnostic Service Dispatcher (DSP)
- Diagnostic Service Processing (DSD)

---

## Hỗ trợ dịch vụ ở OSI-Layer

**OSI-level 7 (Application Layer)**:
- Dcm cung cấp dịch vụ từ ISO 14229-1 (UDS), bao gồm đọc/ghi dữ liệu, kiểm soát session, security states,...
- Đây là layer cao nhất, xử lý logic ứng dụng.

**OSI-level 5 (Session Layer)**:
- Dcm xử lý phần không phụ thuộc mạng (the network-independent sections) của:
  - ISO 15765-3 (UDS trên CAN).
  - ISO 15765-4 (yêu cầu khí thải, session layer).
- Đây là layer quản lý communication session, như thời gian chờ phản hồi.
- Không phụ thuộc mạng nghĩa là Dcm không quan tâm đến cách dữ liệu truyền (CAN hay LIN), mà chỉ xử lý logic session.

**Hỗ trợ OBD (On-Board Diagnostics)**:
- Dcm hỗ trợ dịch vụ OBD từ $01 đến $0A (ví dụ: $01 đọc dữ liệu PID - Parameter ID như tốc độ xe, nhiệt độ động cơ).

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\diagnostic_osi_model.png"
    alt="Diagnostic protocols and OSI-Layers"
  />
  <figcaption>Diagnostic protocols and OSI-Layers</figcaption>
</figure>

---

## Vị trí trong AUTOSAR:

- Nằm ở Service Layer (Communication Services).
- Không phụ thuộc mạng: Giao tiếp qua PduR (PDU Router) để nhận/gửi thông điệp.
- Tương tác: Nhận thông điệp từ PduR, xử lý nội bộ, lấy dữ liệu từ BSW/SW-C qua RTE, gửi phản hồi qua PduR.

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\Position_of_the_Dcm_module_in_AUTOSAR_Architecture.png"
    alt="Position of the Dcm module in AUTOSAR Architecture"
  />
  <figcaption>Position of the Dcm module in AUTOSAR Architecture</figcaption>
</figure>

---




























