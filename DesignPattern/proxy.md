# Proxy Pattern

## Khái niệm

Proxy Pattern là một mẫu thiết kế cấu trúc (Structural Pattern), giúp điều khiển việc truy cập đến một đối tượng khác. Mẫu này tạo ra một đối tượng đại diện (proxy) để thay thế cho đối tượng thật (real object). Proxy có thể kiểm soát các hành động được thực hiện đối với đối tượng thật, như kiểm tra quyền truy cập, ghi lại lịch sử truy cập, hoặc thậm chí trì hoãn việc tạo đối tượng thật cho đến khi cần thiết.

## Mục đích

Mẫu thiết kế này rất hữu ích để kiểm soát hoặc mở rộng chức năng của một đối tượng mà không cần phải sửa đổi mã nguồn gốc của nó. Nó thường được dùng trong việc quản lý tài nguyên, cải thiện bảo mật và tăng hiệu suất.

## Đặt vấn đề

Tưởng tượng bạn đang xây dựng một hệ thống quản lý và giám sát các thiết bị IoT trong một nhà máy sản xuất. Ban đầu, hệ thống chỉ bao gồm các chức năng cơ bản như thu thập dữ liệu từ các thiết bị (sensors), giám sát trạng thái các máy móc, và cảnh báo khi có sự cố.

![alt text](image/image24.png)

Khi hệ thống phát triển, bạn muốn thêm vào các tính năng như kiểm soát quyền truy cập, tải dữ liệu ngoại tuyến, giám sát và báo cáo. Điều này dẫn đến việc phải phát triển thêm nhiều lớp và dịch vụ mới, làm tăng độ phức tạp của hệ thống.

## Khó Khăn và Vấn Đề

- Hiệu Suất: Tải tài liệu lớn hoặc từ nguồn ngoại tuyến có thể làm chậm hệ thống, đặc biệt khi nhiều người dùng cùng truy cập.
- Bảo Mật: Kiểm soát quyền truy cập và bảo vệ thông tin nhạy cảm trở nên khó khăn và phức tạp.
- Quản Lý Tài Nguyên: Theo dõi và giám sát việc sử dụng tài liệu đòi hỏi cơ chế phức tạp và tốn kém tài nguyên hệ thống.

![alt text](image/image25.png)

Khi không sử dụng Proxy Pattern, mỗi tương tác với hệ thống trở nên chậm chạp và không an toàn. Việc xử lý trực tiếp mọi yêu cầu cũng làm tăng khả năng quá tải hệ thống và gặp phải các vấn đề bảo mật.

## Giải pháp

Để tối ưu hóa hệ thống thư viện số đang ngày càng phức tạp và đa năng, việc áp dụng Proxy Pattern là một giải pháp hữu hiệu. Proxy Pattern giúp kiểm soát tương tác với hệ thống, nâng cao hiệu suất, và tăng cường bảo mật. Dưới đây là cách thức triển khai Proxy Pattern:

- Tạo Proxy Classes: Các lớp proxy như AccessControlProxy được thiết kế để kiểm soát và quản lý quyền truy cập đến các tài nguyên. Các lớp này hoạt động như trung gian, xử lý các tác vụ phức tạp và nhạy cảm.
- Cải Thiện Hiệu Suất và Bảo Mật: Các lớp Proxy có thể cache dữ liệu, thực hiện xác thực, và giám sát quyền truy cập. Điều này giúp giảm thiểu tải không cần thiết và tăng tốc độ xử lý, đồng thời bảo vệ thông tin nhạy cảm.
- Đơn Giản Hóa Quy Trình: Việc sử dụng Proxy giúp giảm độ phức tạp trong việc quản lý các chức năng của hệ thống, tạo điều kiện thuận lợi cho việc mở rộng và bảo trì.

![alt text](image/image26.png)

## Cấu trúc

![alt text](image/image26.png)

**Sơ đồ:**

- Subject: Đây là interface mà cả RealSubject và Proxy đều triển khai. Nó định nghĩa phương thức Request() cần được thực thi.
- RealSubject: Lớp thực sự thực hiện logic của phương thức Request(). Đây là lớp mà Proxy sẽ đại diện hoặc "ủy quyền".
- Proxy: Lớp này duy trì một tham chiếu đến đối tượng RealSubject và cũng triển khai interface Subject. Nó có thể kiểm soát hoặc bổ sung hành vi trước hoặc sau khi chuyển yêu cầu đến RealSubject.
- Client: Lớp này sử dụng đối tượng Subject, không biết rằng nó thực sự đang tương tác với Proxy của RealSubject.

**Tổ chức và Tương tác:**

Trong Proxy Pattern, Client tương tác với đối tượng thông qua interface Subject, cho phép sử dụng Proxy thay thế cho RealSubject.
Proxy nhận yêu cầu từ Client và có thể thực hiện một số tác vụ như truy cập kiểm soát, caching, hoặc lazy initialization trước hoặc sau khi chuyển yêu cầu đến RealSubject.
Nếu Proxy quyết định chuyển tiếp yêu cầu, nó gọi phương thức Request() của đối tượng RealSubject.
Sự sắp xếp này cho phép thêm lớp trung gian giữa Client và RealSubject mà không làm thay đổi hợp đồng interface, đảm bảo sự linh hoạt và khả năng mở rộng của code.

## Ví dụ

Dưới đây là một ví dụ sử dụng Proxy Pattern trong C++ với ngữ cảnh mảng embedded để minh họa việc kiểm soát truy cập vào dữ liệu cảm biến (sensor data). Proxy Pattern được sử dụng để trì hoãn việc khởi tạo cảm biến thực (RealSensor) cho đến khi cần thiết.

```cpp
#include <iostream>
#include <string>
#include <vector>

// Interface for Sensor
class Sensor {
public:
    virtual void readData() = 0; // Abstract method to read data
    virtual ~Sensor() = default;
};

// RealSensor class
class RealSensor : public Sensor {
private:
    int sensorID;
    float data;

public:
    RealSensor(int id) : sensorID(id), data(0.0) {
        std::cout << "Initializing Sensor ID: " << sensorID << std::endl;
    }

    void readData() override {
        // Simulate reading sensor data
        data = 25.0 + (rand() % 100) / 10.0; // Random data
        std::cout << "Sensor ID: " << sensorID << " Data: " << data << std::endl;
    }
};

// ProxySensor class
class ProxySensor : public Sensor {
private:
    int sensorID;
    RealSensor* realSensor;

public:
    ProxySensor(int id) : sensorID(id), realSensor(nullptr) {}

    void readData() override {
        if (!realSensor) {
            realSensor = new RealSensor(sensorID);
        } else {
            std::cout << "Sensor ID: " << sensorID << " is already initialized." << std::endl;
        }
        realSensor->readData();
    }

    ~ProxySensor() {
        delete realSensor;
    }
};

// Main function
int main() {
    // Create an array of ProxySensors
    std::vector<ProxySensor*> sensors;
    for (int i = 1; i <= 3; ++i) {
        sensors.push_back(new ProxySensor(i));
    }

    // Access sensors
    std::cout << "Accessing sensor 1:" << std::endl;
    sensors[0]->readData();
    std::cout << "\nAccessing sensor 1 again:" << std::endl;
    sensors[0]->readData();

    std::cout << "\nAccessing sensor 2:" << std::endl;
    sensors[1]->readData();

    // Clean up
    for (auto sensor : sensors) {
        delete sensor;
    }

    return 0;
}
```