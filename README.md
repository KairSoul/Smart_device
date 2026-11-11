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

```mermaid
graph TD
    subgraph EDGE["LOP THIET BI BIEN"]
        A["Cam bien MAX30102<br>(Doc BPM, SpO2, HRV)"] -->|I2C| B["ESP32-C3 Mini<br>(Xu ly & Dong goi JSON)"]
        B -->|Dieu khien| C["Ngoai vi<br>(LED / Buzzer canh bao)"]
    end

    B -->|Wi-Fi / MQTT Protocol| D

    subgraph CLOUD["LOP TRUNG TAM & CLOUD"]
        D["MQTT Broker<br>(HiveMQ / EMQX)"] --> E["Node-RED Server<br>(Xu ly luong & Database)"]
        E --> F["AI Engine<br>(Machine Learning du doan)"]
    end

    F -->|Ket qua du doan| G

    subgraph APP["LOP NGUOI DUNG"]
        G["Node-RED Dashboard<br>(Bieu do tren PC / Mobile)"]
    end

    style EDGE fill:#f9f,stroke:#333,stroke-width:2px
    style CLOUD fill:#bbf,stroke:#333,stroke-width:2px
    style APP fill:#bfb,stroke:#333,stroke-width:2px

**3.2. Phần cứng**

| Linh kiện | Vai trò |
| :--- | :--- |
| **ESP32-C3 SuperMini** | Vi điều khiển trung tâm, kết nối Wi-Fi |
| **Module cảm biến BME280** | Đo nhiệt độ, độ ẩm tương đối và áp suất khí quyển (giám sát vi khí hậu cục bộ) |
| **Module cảm biến MAX30102** | Thu thập nhịp tim (**BPM**), **SpO₂** và đo biến thiên nhịp tim (**HRV**) |
| **Màn hình OLED 0.96" SSD1306 (I2C)** *(Tùy chọn)* | Hiển thị thông số thời tiết và sức khỏe trực tiếp tại chỗ |
| **Đèn LED báo trạng thái** | Chỉ thị trạng thái kết nối Wi-Fi và hoạt động của hệ thống |
| **Dịch vụ thông báo đám mây (Telegram Bot / Webhook)** | Gửi cảnh báo khẩn cấp qua Internet đến người thân khi thời tiết hoặc chỉ số sức khỏe nguy hiểm |
| **Nút bấm (Push Button)** | Thao tác nhanh / Gửi tín hiệu test thủ công |
| **Pin Li-ion + mạch sạc (TP4056)** | Cung cấp nguồn di động cho thiết bị hoạt động liên tục |

**3.3. Lưu đồ thuật toán**

```mermaid
graph TD
    subgraph EDGE["1. Thu thập & Tiền xử lý tại biên (Edge)"]
        A["Cảm biến MAX30102<br>(Đọc tín hiệu quang học PPG)"] -->|Giao tiếp I2C| B["ESP32-C3 Mini<br>(Lọc nhiễu & Tính BPM, SpO2, HRV)"]
    end

    B -->|Đóng gói JSON & Publish qua Wi-Fi| C["MQTT Broker<br>(Cloud Broker)"]

    subgraph CLOUD["2. Xử lý trung tâm & Đám mây (Cloud Server)"]
        C -->|Subscribe Topic| D["Node-RED Server<br>(Nhận dữ liệu & Lưu trữ Database)"]
        D --> E["AI Engine<br>(Machine Learning phân tích đa biến)"]
    end

    E -->|Phát hiện chỉ số nguy hiểm| F["Hệ thống cảnh báo tự động"]

    subgraph APP["3. Thông báo & Trực quan hóa (Presentation)"]
        F --> G["Node-RED Dashboard<br>(Biểu đồ thời gian thực trên PC / Mobile)"]
        F -->|Gửi yêu cầu HTTP POST| H["Dịch vụ thông báo thứ 3<br>(Telegram Bot / Webhook / Email tới Người thân)"]
    end

    style EDGE fill:#f9f,stroke:#333,stroke-width:2px
    style CLOUD fill:#bbf,stroke:#333,stroke-width:2px
    style APP fill:#bfb,stroke:#333,stroke-width:2px


