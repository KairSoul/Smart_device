**1. THÔNG TIN ĐỀ TÀI**
  **TÊN ĐỀ TÀI:** Thiết bị đeo tay thông minh dự đoán biến đổi thời tiết và theo dõi sức khỏe người dùng


**1. GIỚI THIÊU TỔNG QUAN VÀ ĐẶT VẤN ĐỀ**

Sự biến đổi thất thường của thời tiết va môi trường sống hiện nay tác động trực tiếp đến hệ tim mạch cũng như hô hấp của con người. Mặc dù nhu cầu tự theo dõi sức khỏe
và cập nhật thời tiết tại nhà càng cao, các thiết bị và ứng dụng phổ biến trên thị trường vẫn còn một số hạn chế :

* **Sai lệch giữa dự báo diện rộng và môi trường thực tế:** Thông tin từ các đài khí tượng chỉ mang tính khu vực chung, không đo lường được nhiệt độ, độ ẩm và áp suất
  thực tế trong không gian kín hoặc vị trí làm việc của người dùng.
* **Đánh giá sức khỏe thiếu ngữ cảnh môi trường:** Các thiết bị đo nhịp tim và SpO<sub>2</sub> cá nhân chỉ đọc chỉ số cơ thể một cách độc lập. Việc tách rời thông số
  sinh học khỏi điều kiện nhiệt độ, độ ẩm xung quanh khiến người dùng không nhận biết được các hội chứng nguy hiểm như sốc nhiệt, mệt mỏi do tụt áp suất khí quyển.
* **Dự liệu phân mảnh, khó theo dõi:** Người dùng phải sử dụng nhiều thiết bị và ứng dụng rời rạc cho từng mục đích, thiếu một giao diện tập trung để thống kê và
  đối chiếu lịch sử sức khỏe cùng biến động thời tiết
* **Thiếu tính năng phân tích và dự báo thông minh:** Phần lớn thiết bị đo dân dụng chỉ hiển thị con số tức thời mà chưa có khả năng học máy để dự đoán xu hướng thời tiết
ngắn hạn hay cảnh báo sớm nguy cơ suy giảm sức khỏe.

Từ thực tế đó, dự án xây dựng một hệ thống IoT tích hợp giúp giám sát song song khí hậu và chỉ số sinh tồn của cơ thể trên cùng một nền tảng. Thiết bị phần cứng sử dụng
**ESP32-C3 mini** để thu thập dữ liệu nhiệt độ, độ ẩm, áp suất qua cảm biến **BME/BMP280** cùng nhịp tim, nồng độ oxy trong máu (SpO<sub>2</sub>) từ cảm biến **MAX30102**. Toàn
bộ thông số được đồng bộ thời gian thực được lưu trữ vào đám mây thông qua **MQTT** và dữ liệu sẽ được xử lí và được hiển qua giao diện.

**2. MỤC TIÊU CỦA ĐỀ TÀI**

**3. KIẾN TRÚC HỆ THỐNG VÀ SƠ ĐỒ KHỐI**
**3.1. Sơ đồ khối**
