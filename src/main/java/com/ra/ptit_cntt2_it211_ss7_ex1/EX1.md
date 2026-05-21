# Phân Tích Hệ Thống Ghi Log Hiệu Năng Thủ Công

## 1. Các nguyên tắc thiết kế bị vi phạm

Việc chèn mã nguồn tính toán thời gian thực thi bằng `System.currentTimeMillis()` thủ công vào từng phương thức đã vi phạm nghiêm trọng các nguyên tắc thiết kế phần mềm cốt lõi sau:

*   **Nguyên tắc đơn nhiệm - Single Responsibility Principle (SRP):**
    *   *Hệ quả:* Lớp `TransactionService` chỉ nên chịu trách nhiệm xử lý các logic nghiệp vụ giao dịch ngân hàng. Việc bắt nó phải gánh thêm trách nhiệm đo lường hiệu năng và ghi log đã làm sai lệch vai trò của lớp này.
*   **Nguyên tắc Đóng/Mở - Open/Closed Principle (OCP):**
    *   *Hệ quả:* Khi muốn thay đổi định dạng log, đổi đơn vị đo (từ ms sang ns), hoặc ngừng đo hiệu năng, chúng ta buộc phải vào từng phương thức để chỉnh sửa/xóa bỏ code. Code không đóng với việc sửa đổi và không mở với việc mở rộng.
*   **Don't Repeat Yourself (DRY):**
    *   *Hệ quả:* Đoạn mã lấy thời gian đầu, thời gian cuối và trừ đi nhau bị lặp lại (Code Duplication) ở khắp mọi nơi.

## 2. Vấn đề về cấu trúc Code

*   **Code Tangling (Đan xen code):** Logic nghiệp vụ giao dịch ngân hàng bị trộn lẫn với logic hạ tầng (đo hiệu năng), khiến code trở nên rối rắm, khó đọc và khó hiểu bản chất nghiệp vụ chính.
*   **Code Scattering (Phân tán code):** Logic đo hiệu năng không nằm tập trung ở một nơi mà bị phân tán, rải rác ở khắp các phương thức, các tầng dịch vụ khác nhau, gây cực kỳ khó khăn cho việc bảo trì hoặc nâng cấp hệ thống.