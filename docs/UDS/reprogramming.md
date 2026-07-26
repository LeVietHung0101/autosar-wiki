<!-- 
## 17 Quy trình lập trình bộ nhớ máy chủ không mất dữ liệu (Non-volatile server memory programming process)

### 17.1 Thông tin chung (General information)

Subclause này định nghĩa một khuôn khổ (framework) cho việc tải xuống định hướng vật lý (physically oriented download) của một hoặc nhiều module phần mềm ứng dụng/dữ liệu (application software/data modules) vào bộ nhớ máy chủ không mất dữ liệu (non-volatile server memory). Chuỗi lập trình bộ nhớ máy chủ không mất dữ liệu (non-volatile server memory programming sequence) được định nghĩa giải quyết:

a) Các nhu cầu riêng của nhà sản xuất xe (vehicle manufacturer specific needs) khi thực hiện các bước nhất định trong quy trình lập trình (programming process), đồng thời tuân thủ các yêu cầu thực thi dịch vụ chung (general service execution requirements) được quy định trong tài liệu này và **ISO 14229-2** (chẳng hạn như thứ tự tuần tự của các dịch vụ (sequential order of services) và quản lý phiên (session management)),

b) Để hỗ trợ các mạng (networks) có nhiều nút kết nối (multiple nodes connected) tương tác với nhau bằng các tin nhắn giao tiếp thông thường (normal communication messages),

c) Sử dụng tiếp cận xe định hướng vật lý (physically oriented vehicle approach) (giao tiếp điểm-đến-điểm (point-to-point communication) — các máy chủ không hỗ trợ giao tiếp chẩn đoán chức năng (functional diagnostic communication)) hoặc tiếp cận xe định hướng chức năng (functionally oriented vehicle approach) (giao tiếp điểm-đến-điểm (point-to-point) và điểm-đến-nhiều-điểm (point-to-multiple communication) — các máy chủ hỗ trợ giao tiếp chẩn đoán chức năng (functional diagnostic communication)). Một phương tiện đơn lẻ chỉ được hỗ trợ một trong các phương pháp tiếp cận xe (vehicle approaches) nêu trên.

Chuỗi lập trình (programming sequence) được chia thành hai giai đoạn lập trình (programming phases). Tất cả các bước được phân loại dựa trên các loại sau:

* **Các bước chuẩn hóa (Standardized steps):** loại bước này là bắt buộc (mandatory). Client và server phải hoạt động đúng như quy định.
* **Các bước tùy chọn/khuyến nghị (Optional/recommended steps):** loại bước này là tùy chọn (optional). Các bước tùy chọn này yêu cầu sử dụng định danh dịch vụ chẩn đoán cụ thể (specific diagnostic service identifier) (như được mô tả trong bước) và chứa các khuyến nghị về cách thực hiện một thao tác. Khi chức năng được chỉ định được sử dụng, client và server phải hoạt động đúng như quy định.
* **Các bước riêng của nhà sản xuất xe (Vehicle manufacturer specific steps):** loại bước này là tùy chọn (optional). Việc sử dụng và nội dung (ví dụ: các định danh dịch vụ chẩn đoán (diagnostic service identifiers) được sử dụng) của các bước tùy chọn này tùy thuộc vào sự quyết định của nhà sản xuất xe (vehicle manufacturer) và phải tuân theo tài liệu này cũng như **ISO 14229-2**. -->