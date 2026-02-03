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

## Hỗ trợ dịch vụ ở OSI-Layer

**OSI-level 7 (Application Layer)**:
- Đây là layer cao nhất, xử lý logic ứng dụng.
- Dcm cung cấp dịch vụ từ **ISO 14229-1 (UDS)**, bao gồm đọc/ghi dữ liệu, kiểm soát session, security states,...
- Ngoài ra, Dcm hỗ trợ dịch vụ **OBD** từ $01 đến $0A (ví dụ: $01 đọc dữ liệu PID - Parameter ID như tốc độ xe, nhiệt độ động cơ). Với các dịch vụ này, chức năng OBD của Autosar có khả năng đáp ứng tất cả các quy định OBD dành cho xe hạng nhẹ trên toàn thế giới (California OBDII, EOBD, Japan OBD và tất cả các quy định khác).

**OSI-level 5 (Session Layer)**:
- Dcm xử lý phần không phụ thuộc mạng (the network-independent sections) của:
  - ISO 15765-3 (UDS trên CAN).
  - ISO 15765-4 (yêu cầu khí thải, session layer).
- Đây là layer quản lý communication session, như thời gian chờ phản hồi.
- Không phụ thuộc mạng nghĩa là Dcm không quan tâm đến cách dữ liệu truyền (CAN hay LIN), mà chỉ xử lý logic session.

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

## Phụ thuộc vào các module khác

Sau đây là các BSW module và SW-Cs mà Dcm cung cấp API cho và phụ thuộc:

<table class="hover-table">
  <thead>
    <tr>
      <th>Module</th>
      <th>Tương tác với Dcm</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Diagnostic Event Manager (Dem)</td>
      <td>Dcm sử dụng Dem để lưu trữ và truy vấn các DTC, và để lấy thông tin chẩn đoán liên quan đến khí thải.</td>
    </tr>
    <tr>
      <td>PDU Router (PduR)</td>
      <td>Dcm sử dụng PduR để giao tiếp với các module mạng (như CanTp, LinTp, FrTp,...) và để định tuyến các PDU chẩn đoán.</td>
    </tr>
    <tr>
      <td>Communication Manager (ComM)</td>
      <td>Dcm thông báo cho ComM trạng thái hoạt động (active/inactive) của diagnostic communication, đồng thời xử lý các communication mode do ComM yêu cầu (Full/Silent/No-Communication).<br>
          Dcm cung cấp các chức năng để enable/disable diagnostic communication nếu được yêu cầu từ ComM.
      </td>
    </tr>
    <tr>
      <td>SW-C và RTE</td>
      <td>Dcm sử dụng RTE để giao tiếp với các SW-Cs (ví dụ: để đọc/giải thích dữ liệu từ các DID hoặc để thực thi các routine).</td>
    </tr>
    <tr>
      <td>Basic Software Mode Manager (BswM)</td>
      <td>Dcm thông báo cho BswM rằng App đã được cập nhật nếu việc init Dcm là kết quả của việc nhảy từ bootloader. Dcm cũng chỉ ra cho BswM sự thay đổi communication mode.</td>
    </tr>
    <tr>
      <td>Crypto Service Manager (Csm)</td>
      <td>Csm cung cấp một loạt các thuật toán mật mã. Csm được sử dụng để tính toán xác thực.</td>
    </tr>
    <tr>
      <td>Key Manager (KeyM)</td>
      <td>KeyM hỗ trợ xử lý chứng chỉ và các API để thực hiện chẩn đoán được xác thực bằng chứng chỉ.</td>
    </tr>
    <tr>
      <td>IoHwAb (I/O Hardware Abstraction)</td>
      <td>Dcm sử dụng IoHwAb để truy cập phần cứng I/O (ví dụ: để kiểm soát các bộ truyền động (actuator) trong dịch vụ InputOutputControlByIdentifier 0x2F).</td>
    </tr>
    <tr>
      <td>Function Inhibition Manager (FIM)</td>
      <td>Dcm sử dụng FIM để kiểm soát việc cấm chức năng (function inhibition) khi có lỗi. Dcm thông báo FIM khi xóa DTC..</td>
    </tr>
    <tr>
      <td>Non-Volatile Memory Manager (NvM)</td>
      <td>Dcm sử dụng NvM để lưu trữ dữ liệu non-volatile, như security states, diagnostic sessions hoặc DTC. AUTOSAR không quy định chi tiết cách truy cập chúng, mà để cho OEM tự xử lý, nhằm linh hoạt cho các nền tảng phần cứng khác nhau.
      </td>
    </tr>
    <tr>
      <td>Development Error Tracer (DET)</td>
      <td>Dcm sử dụng Det để trace lỗi phát triển trong runtime.</td>
    </tr>
    <tr>
      <td>Scheduler Manager (SchM)</td>
      <td>Dcm sử dụng SchM để quản lý lịch trình và đồng bộ hóa.</td>
    </tr>
  </tbody>
</table>

---

## Submodule

Dcm chia thành 3 submodule để xử lý từng khía cạnh cụ thể của quy trình chẩn đoán:
Dcm được thiết kế theo kiến trúc phân tầng để xử lý các lớp chức năng khác nhau trong giao thức chẩn đoán. Cấu trúc này tuân thủ OSI model (lớp 5-7: Session, Presentation, Application), với 3 submodule:
- Diagnostic Session Layer (DSL): **quản lý** trạng thái luồng dữ liệu chẩn đoán.
- Diagnostic Service Dispatcher (DSD): **phân phối** yêu cầu chẩn đoán.
- Diagnostic Service Processing (DSP): **xử lý** dịch vụ cụ thể

<figure>
  <img
    src="{{ site.baseurl }}\assets\images\DCM\SubmodulesOfDcm.png"
    alt="Khả năng tương tác giữa các submodule trong Dcm"
  />
  <figcaption>Khả năng tương tác giữa các submodule trong Dcm</figcaption>
</figure>

### Diagnostic Session Layer (DSL)

DSL quản lý luồng dữ liệu chẩn đoán (data flow) giữa request và response. Nó giám sát timing giao thức (ví dụ: P2/P2* - thời gian chờ phản hồi, S3 - thời gian session timeout), đảm bảo tuân thủ tiêu chuẩn như ISO 14229.

DSL cũng quản lý trạng thái chẩn đoán:
- **Diagnostic Session**:
  - 0x01 - Default Session.
  - 0x02 - Programming Session.
  - 0x03 - Extended Diagnostic Session.
  - 0x04 - Safety System Diagnostic Session.
- **Security state**: Locked/Unlocked, sử dụng seed-key hoặc chứng chỉ để ngăn truy cập trái phép.

DSL giúp hệ thống an toàn, tránh gián đoạn khi xe chạy (on-board diagnostics).

Ví dụ: DSL không cho phép flash trong Default Session. Nếu timing sai (quá chậm), DSL gửi NRC 0x78 (Request Correctly Received - Response Pending).

{: .note }
DSL như "người gác cổng" – nó kiểm tra xem request có hợp lệ trong session/security-level hiện tại không? Đồng thời đảm bảo đúng timing với tester.

### Diagnostic Service Dispatcher (DSD)

DSD phân phối dòng dữ liệu chẩn đoán (diagnostic data stream). Cụ thể:
- Nhận yêu cầu mới từ mạng (qua PduR), phân tích và chuyển tiếp đến bộ xử lý dữ liệu (thường là DSP).
- Gửi phản hồi qua mạng khi được kích hoạt bởi DSP (sau khi xử lý xong).

DSD giúp tách biệt logic xử lý (DSP) khỏi giao tiếp mạng, khiến Dcm trở nên linh hoạt. Trong hệ thống đa ECU, DSD hỗ trợ định tuyến yêu cầu đến ECU đúng.

Ví dụ: Yêu cầu đến với SID (Service Identifier) như 0x22 (ReadDataByIdentifier), DSD chuyển đến DSP để xử lý, rồi nhận phản hồi từ DSP để gửi lại.

{: .note }
DSD như "người điều phối" – nó không xử lý nội dung dịch vụ mà chỉ định tuyến.

### Diagnostic Service Processing (DSP)

DSP xử lý các dịch vụ chẩn đoán (services) và subservice (sub-functions).

Ví dụ: Với dịch vụ 0x2F (InputOutputControl), DSP gọi SW-C qua RTE để kiểm soát actuator như đèn pha. Nếu không hỗ trợ, DSP tạo NRC như 0x12 (Subfunction Not Supported). DSP xử lý đa dạng dịch vụ UDS/OBD

{: .note }
DSP là "bộ não" xử lý logic dịch vụ – nó kiểm tra tính hỗ trợ của dịch vụ (supported?), điều kiện thực thi (allowed in current session?), và thực hiện hành động.

