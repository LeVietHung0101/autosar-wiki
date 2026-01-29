---
title: AUTOSAR
nav_order: 2
has_children: true
---

# AUTOSAR

<!-- <strong>Mục lục</strong>
1. TOC
{:toc} -->

---

## AUTOSAR là gì?

**AUTOSAR** (**AUT**omotive **O**pen **S**ystem **AR**chitecture) là một liên minh toàn cầu của các công ty hàng đầu trong ngành ô tô và phần mềm nhằm phát triển và thiết lập khung phần mềm tiêu chuẩn hóa cùng **open Electrical/Electronic system architecture** cho di chuyển thông minh.

Các công ty này gồm các nhà sản xuất ô tô (Original Equipment Manufacturer - OEM), nhà cung cấp (supplier), và các công ty từ ngành điện tử, bán dẫn và phần mềm.

---

### Phạm vi của AUTOSAR

Phạm vi của AUTOSAR bao gồm tất cả các lĩnh vực liên quan đến xe hơi, ngoại trừ lĩnh vực thông tin giải trí (infotainment).
- Các lĩnh vực thân xe (body), hệ thống truyền động (power train) và khung gầm (chassis).
- Tất cả các ứng dụng điều khiển xe đều được đề cập, đặc biệt là các chức năng phân tán (distributed functions), hỗ trợ cho xe kết nối và tự động hóa (ví dụ: thông qua các bus như CAN, Ethernet).

---

### Tại sao cần phát triển AUTOSAR?

Quy trình phát triển hệ thống dựa trên phần cứng ECU (Electronic Control Unit) cần chuyển sang dựa trên yêu cầu và chức năng. Điều này đòi hỏi kiến trúc mở, cùng các module phần mềm có khả năng mở rộng và trao đổi.

{: .note }
>Trong cách tiếp cận cũ, phát triển phần mềm ô tô bắt đầu từ ECU. Mỗi ECU được coi là một đơn vị độc lập, với phần mềm được viết riêng lẻ cho từng ECU cụ thể. Điều này dẫn đến:
>- Phần mềm phụ thuộc chặt chẽ vào phần cứng của ECU, dẫn đến khó tái sử dụng.
>- Khó tích hợp (integrate) khi hệ thống mở rộng (ví dụ: xe có hàng chục ECU kết nối qua mạng như CAN hoặc Ethernet).
>- Tăng chi phí và thời gian khi phải tùy chỉnh cho từng nhà cung cấp hoặc mẫu xe.

Vì đây là thách thức toàn ngành, nên từ năm 2003, nhiều công ty đã hợp tác trong AUTOSAR để phát triển tiêu chuẩn mở cho kiến trúc E/E. Ý tưởng cốt lõi là tái sử dụng các thành phần phần mềm (software components) để xử lý sự phức tạp ngày càng tăng trong tương lai (về mặt công nghệ và kinh tế).

{: .note }
Các chức năng (như điều khiển động cơ, thân xe, phanh ABS) được thiết kế dưới dạng thành phần phần mềm (Software Components - SWC) độc lập, có thể tái sử dụng và phân bổ linh hoạt qua nhiều ECU.


Kiến trúc hướng dịch vụ (service-oriented architecture - SOA) mới sẽ được hỗ trợ bởi AUTOSAR. Kỹ thuật này không chỉ tập trung tối ưu hóa từng linh kiện mà còn ở cấp độ toàn bộ hệ thống.

---

### Mục tiêu của AUTOSAR

AUTOSAR đặt ra mục tiêu sẽ trở thành **tiêu chuẩn toàn cầu** được thiết lập cho **phần mềm** và **cách tiếp cận phát triển**, cho phép **open E/E system architectures** phục vụ cho sự di chuyển thông minh trong tương lai, hỗ trợ mức độ tin cậy cao, đặc biệt là an toàn và bảo mật.

Mục tiêu chính là tiêu chuẩn hóa các chức năng hệ thống cơ bản và giao diện chức năng. Điều này cho phép các đối tác phát triển tích hợp, trao đổi và chuyển giao các chức năng trong mạng lưới ô tô, đồng thời cải thiện đáng kể việc cập nhật và nâng cấp phần mềm trong suốt vòng đời của xe.

<table class="hover-table">
  <thead>
    <tr>
      <th>Mục tiêu</th>
      <th>Ý nghĩa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Chuẩn hóa</th>
      <td>Ngành công nghiệp ô tô bao gồm chuỗi cung ứng toàn cầu và nhiều sản phẩm công nghệ đa dạng. AUTOSAR hỗ trợ chuẩn hoá cách giao tiếp và hoạt động của các thành phần và hệ thống khác nhau này; từ đó giảm độ phức tạp trong phát triển và đảm bảo tính tương thích.</td>
    </tr>
    <tr>
      <td>Khả năng tái sử dụng và tối ưu chi phí</td>
      <td>Kiến trúc AUTOSAR thúc đẩy việc tái sử dụng phần mềm thông qua cấu trúc module. Điều này giúp giảm chi phí và thời gian phát triển, đồng thời nâng cao chất lượng phần mềm.</td>
    </tr>
    <tr>
      <td>An toàn và độ tin cậy</td>
      <td>Ô tô là sản phẩm có tính chất ảnh hưởng trực tiếp đến tính mạng. AUTOSAR tuân thủ các tiêu chuẩn an toàn nghiêm ngặt và góp phần nâng cao độ tin cậy của phần mềm.</td>
    </tr>
    <tr>
      <td>Khả năng mở rộng</td>
      <td>Khi các tính năng trên xe ngày càng được bổ sung và công nghệ liên tục phát triển, hệ thống phần mềm cần có khả năng mở rộng dễ dàng. AUTOSAR hỗ trợ khả năng mở rộng này và tạo điều kiện thuận lợi cho việc tích hợp các tính năng mới.</td>
    </tr>
    <tr>
      <td>Ứng dụng các công nghệ tương lai</td>
      <td>Việc triển khai các công nghệ ô tô trong tương lai, như lái xe tự động, xe điện và xe kết nối (connected car), đòi hỏi khả năng tích hợp phần mềm ở mức cao. AUTOSAR cung cấp một nền tảng giúp tích hợp hiệu quả các công nghệ mới này.</td>
    </tr>
  </tbody>
</table>


---

## Quan hệ đối tác AUTOSAR

---

### Tổng quan

AUTOSAR là một liên minh toàn cầu gồm các nhà sản xuất OEM, nhà cung cấp phụ tùng ô tô cấp 1 (Tier 1 automotive suppliers), các công ty điện tử ô tô, bán dẫn và phần mềm.
Họ cùng nhau phát triển và thiết lập tiêu chuẩn công nghiệp mở cho kiến trúc E/E ô tô, làm cơ sở hạ tầng cơ bản để quản lý chức năng ứng dụng và module phần mềm tiêu chuẩn.

Liên minh được quản lý bởi các đối tác cốt lõi (Core Partners), với tổ chức ảo phân tán, ra quyết định dựa trên sự nhất trí.
Nguồn lực đến từ Core Partners, Premium Partners và Development Partners trên toàn cầu, đòi hỏi quản lý dự án hiệu quả, báo cáo chính xác, đảm bảo chất lượng và giao tiếp minh bạch.

Các công ty quan tâm có thể đăng ký làm Premium Partners hoặc Associate Partners. Development Partners phù hợp cho đóng góp cụ thể vào phát triển công nghệ và thông số kỹ thuật AUTOSAR.

---

### Các cấp độ đối tác trong AUTOSAR

Liên minh AUTOSAR phân loại thành viên theo cấp độ để khuyến khích hợp tác đa dạng:
- **Core Members (Thành viên cốt lõi)**: Là những sáng lập viên chính, chịu trách nhiệm lãnh đạo và xây dựng nền tảng AUTOSAR từ năm 2003. Họ bao gồm các OEM lớn và nhà cung cấp hàng đầu.
- **Premium Members (Thành viên cao cấp)**: Tham gia phát triển công cụ (tool development), dịch vụ cung cấp, và tiêu chuẩn hóa phần mềm cho ECU (Electronic Control Unit). Họ hỗ trợ mở rộng ứng dụng AUTOSAR trong ngành ô tô.
- **Development Members (Thành viên phát triển)**: Tập trung vào đóng góp cụ thể cho công nghệ, như thông số kỹ thuật và module phần mềm.
- **Associate Members (Thành viên liên kết)**: Cấp độ cơ bản cho các bên quan tâm, không được đề cập chi tiết nhưng có thể đăng ký để tham gia.


<figure>
  <img
    src="{{ site.baseurl }}\assets\images\TypeOfAutosarPartnership.png"
    alt="Các loại thành viên Autosar"
  />
  <figcaption>Các loại thành viên Autosar</figcaption>
</figure>

---

### Vai trò của các bên trong hệ sinh thái AUTOSAR

**Original Equipment Manufacturer (OEM)**: Là các hãng sản xuất xe như Volkswagen, BMW, Toyota. Họ chịu trách nhiệm tích hợp và tối ưu toàn bộ hệ thống xe, bao gồm mua sắm linh kiện từ nhà cung cấp (Supplier). OEM sử dụng AUTOSAR để chuẩn hóa phần mềm, dễ dàng tích hợp ECU từ nhiều nguồn, giảm phụ thuộc và chi phí chuyển đổi.

**Tier 1 supplier**: Cung cấp trực tiếp sản phẩm cho OEM, chủ yếu là ECU – đơn vị điều khiển điện tử. Trước AUTOSAR, phần mềm ECU thiếu chuẩn hóa, dẫn đến khó khăn khi OEM chuyển nhà cung cấp hoặc ngược lại. AUTOSAR giúp Tier 1 như BOSCH hay Continental phát triển ECU với kiến trúc phần mềm thống nhất, dễ trao đổi và tái sử dụng.

**Tier 2 supplier**:Cung cấp trực tiếp cho Tier 1, tập trung vào thành phần cốt lõi của ECU như MCU (Microcontroller), phần mềm tầng thấp (chủ yếu MCAL – Microcontroller Abstraction Layer), hoặc phần cứng hỗ trợ (wireless, crypto). Họ hỗ trợ Tier 1 sản xuất ECU, gián tiếp đóng góp vào tiêu chuẩn AUTOSAR qua chuỗi cung ứng.

---

### Danh sách các Core Members

Core Members là những thành viên chính đã sáng lập AUTOSAR, bao gồm cả OEM và nhà cung cấp:

- **BMW Group**: OEM, tập trung sản xuất xe hơi cao cấp.
- **BOSCH**: Nhà cung cấp Tier 1, chuyên về điện tử ô tô, cảm biến và ECU.
- **Continental**: Nhà cung cấp Tier 1, cung cấp hệ thống phanh, khung gầm và điện tử.
- **Daimler**: OEM (nay là Mercedes-Benz Group), sản xuất xe hơi và xe tải.
- **Ford**: OEM, sản xuất xe hơi và xe tải toàn cầu.
- **General Motors**: OEM, sản xuất xe hơi đa dạng.
- **PSA Group (nay là phần của Stellantis)**: OEM, chuyên xe hơi châu Âu.
- **Toyota**: OEM, dẫn đầu về xe hybrid và điện.
- **VOLKSWAGEN**: OEM, tập đoàn lớn với nhiều thương hiệu xe.

Những công ty này đại diện cho sự kết hợp giữa OEM (sản xuất xe) và Tier 1 (cung cấp linh kiện), tạo nền tảng cho sự hợp tác toàn ngành.

---

## Lịch sử phát triển

AUTOSAR được thành lập vào năm 2003 bởi một nhóm gồm 10 công ty sáng lập (bao gồm các ông lớn như BMW, Bosch, Continental, Daimler, Ford, General Motors, PSA Group, Siemens VDO, Toyota và Volkswagen). Ý tưởng ban đầu là hợp nhất chuyên môn từ ngành ô tô để định nghĩa một tiêu chuẩn kiến trúc hệ thống mở, nhằm giải quyết vấn đề thiếu đồng nhất trong phần mềm ô tô – lúc đó mỗi nhà sản xuất thường phát triển hệ thống riêng lẻ, dẫn đến chi phí cao và khó tích hợp.

Giai đoạn đầu (2003-2006): Tập trung vào việc định nghĩa các tiêu chuẩn cơ bản cho phần mềm ô tô.
Mở rộng (2007-nay): AUTOSAR đã phát triển thành hai nền tảng chính (Classic Platform và Adaptive Platform), với hơn 300 đối tác toàn cầu. Đến nay, nó hỗ trợ các xu hướng như xe tự lái (autonomous driving), kết nối (connectivity) và điện khí hóa (electrification). Các bản cập nhật định kỳ (releases) được phát hành để thích ứng với công nghệ mới, ví dụ như tích hợp AI và bảo mật mạng.

Lịch sử này phản ánh sự chuyển dịch từ hệ thống nhúng truyền thống sang kiến trúc linh hoạt hơn cho ô tô thông minh.

---

### Classic AUTOSAR và Adaptive AUTOSAR

Mặc dù cả Classic AUTOSAR và Adaptive AUTOSAR đều là một phần của tiêu chuẩn AUTOSAR, nhưng mục đích và triết lý thiết kế của chúng lại khác nhau đáng kể. Hai nền tảng này được phát triển để đáp ứng nhu cầu đa dạng của ngành công nghiệp ô tô.


{: .note }
> **Classic AUTOSAR** là nền tảng dành cho các ECU truyền thống, được thiết kế đặc biệt cho hoạt động thời gian thực trong các ECU có tài nguyên hạn chế. Nó tập trung vào việc cung cấp độ tin cậy cao và hiệu suất thời gian thực có thể dự đoán được.
>
> **Adaptive AUTOSAR** là nền tảng linh hoạt và có khả năng mở rộng hơn, được thiết kế cho tính toán hiệu năng cao và hoạt động trên nhiều hệ điều hành khác nhau. Nó chủ yếu được thiết kế để đáp ứng nhu cầu của các công nghệ ô tô hiện đại, bao gồm Hệ thống hỗ trợ lái xe nâng cao (Advanced Driver Assistance Systems - ADAS), hệ thống thông tin giải trí và công nghệ xe kết nối.

<table class="hover-table">
  <thead>
    <tr>
      <th></th>
      <th>Classic AUTOSAR</th>
      <th>Adaptive AUTOSAR</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cấu trúc</th>
      <td>Đơn giản, tĩnh. Software Components được tích hợp (integrated) theo cách định sẵn.</td>
      <td>Module hoá, động. Cung cấp tính linh hoạt trong các môi trường tính toán hiệu năng cao.</td>
    </tr>
    <tr>
      <th>Thành phần</th>
      <td>Bao gồm Basic Software (BSW), Runtime Environment (RTE) và Application Software Components (ASW).</td>
      <td>Dựa trên kiến trúc hướng dịch vụ (Service-oriented architecture - SOA) và sử dụng các mô hình phát triển phần mềm hiện đại như microservices.</td>
    </tr>
    <tr>
      <th>Mục đích</th>
      <td>Đáp ứng các yêu cầu an toàn chức năng (Functional Safety - FuSa); phù hợp với các chức năng ô tô truyền thống.</td>
      <td>Xử lý dữ liệu, kết nối nâng cao và thích ứng nhanh với các yêu cầu công nghệ thay đổi liên tục.</td>
    </tr>
    <tr>
      <th>Ứng dụng</th>
      <td>Thực hiện các chức năng điều khiển thiết yếu của xe như điều khiển động cơ, hệ thống phanh điện tử và túi khí.</td>
      <td>Hỗ trợ các tính năng phức tạp và động như lái xe tự động, hệ thống infotainment nâng cao và cập nhật phần mềm trực tuyến (Over-the-Air - OTA).</td>
    </tr>
  </tbody>
</table>

---
<!-- 
# Các khái niệm trong bài viết

- Open Electrical/Electronic system architecture
- OEM - Original Equipment Manufacturer
- Supplier Tier 1/2
- CAN
- Ethernet
- ECU - Electronic Control Unit
- SWC - Software Components
- Service-Oriented Architecture - SOA
- MCU - Microcontroller
- MCAL – Microcontroller Abstraction Layer
- Classic AUTOSAR
- Adaptive AUTOSAR
- ADAS - Advanced Driver Assistance Systems
- BSW - Basic Software
- RTE - Runtime Environment
- ASW - Application Software Components
- Microservices
- FuSa - Functional Safety
- OTA - Over-the-Air



[AUTOSAR](https://www.autosar.org/)

[AUTOSAR_EXP_Introduction_Part1](https://www.autosar.org/fileadmin/user_upload/AUTOSAR_Introduction_PDF/AUTOSAR_EXP_Introduction_Part1.pdf)
-->
