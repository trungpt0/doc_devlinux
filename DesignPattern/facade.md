# Facade

## Khái niệm

Facade Pattern là một mẫu thiết kế thuộc nhóm Structural Design Patterns. Nó cung cấp một giao diện đơn giản để tương tác với một hệ thống phức tạp, giúp che giấu sự phức tạp và các chi tiết kỹ thuật không cần thiết khỏi người dùng.

## Mục đích

Mẫu thiết kế này hữu ích khi cần cung cấp một giao diện đơn giản cho các hệ thống lớn và phức tạp, giúp người dùng dễ dàng tương tác mà không cần hiểu sâu về chi tiết bên trong.

## Đặt vấn đề

Hãy tưởng tượng bạn đang phát triển một hệ thống điều khiển tự động cho một nhà thông minh. Ban đầu, hệ thống chỉ cần quản lý các chức năng cơ bản như đo nhiệt độ, độ ẩm và điều khiển quạt hoặc máy sưởi. Bạn sử dụng các thành phần như TemperatureSensor, HumiditySensor, và FanController để thực hiện các tác vụ này.

![alt text](image/image28.png)

Khi nhu cầu mở rộng, hệ thống cần tích hợp thêm nhiều chức năng phức tạp hơn như quản lý ánh sáng tự động, điều khiển hệ thống tưới nước, giám sát CO₂, và gửi cảnh báo qua tin nhắn hoặc email. Điều này dẫn đến việc bổ sung thêm các thành phần mới như LightController, IrrigationSystem, CO2Monitor, và NotificationService.

- LightController để điều chỉnh ánh sáng dựa trên thời gian và điều kiện môi trường.
- IrrigationSystem để tự động tưới nước dựa trên độ ẩm đất.
- CO2Monitor để giám sát và duy trì mức CO₂ trong nhà kính.
- NotificationService để gửi cảnh báo khi các thông số vượt ngưỡng.

![alt text](image/image29.png)

Với sự phát triển này, hệ thống trở nên phức tạp và khó sử dụng. Người quản lý nhà kính phải điều khiển từng thành phần một cách riêng lẻ để thực hiện các nhiệm vụ như điều chỉnh nhiệt độ, bật/tắt đèn, hoặc kiểm tra trạng thái các cảm biến. Việc này không chỉ làm tăng khả năng xảy ra lỗi mà còn làm giảm hiệu quả vận hành, đặc biệt khi xử lý tình huống khẩn cấp.

Ban đầu, giao diện người dùng chỉ cần thực hiện các tác vụ cơ bản với một vài thành phần. Khi hệ thống mở rộng, người dùng phải học cách sử dụng và tương tác với nhiều thành phần hơn, mỗi thành phần lại có giao diện và quy trình hoạt động khác nhau.

Quy trình vận hành trở nên phức tạp hơn khi phải thực hiện nhiều bước: giám sát các thông số, bật/tắt thiết bị, điều chỉnh hệ thống tưới hoặc ánh sáng, và gửi thông báo. Từng bước yêu cầu tương tác với các thành phần riêng lẻ, làm tăng độ phức tạp trong vận hành và bảo trì. Điều này cũng đặt ra gánh nặng trong việc đào tạo người dùng và đảm bảo hoạt động liên tục của hệ thống.

Tóm lại, sự phức tạp tăng lên không chỉ ảnh hưởng đến hiệu quả vận hành mà còn gây khó khăn cho việc bảo trì và nâng cấp hệ thống. Đây chính là thách thức mà Facade Pattern có thể giải quyết, giúp đơn giản hóa quy trình bằng cách cung cấp một giao diện thống nhất, giảm tải cho người dùng và cải thiện hiệu quả quản lý nhà kính thông minh.

## Giải pháp

Để giải quyết các thách thức trong hệ thống nhà thông minh, chúng ta có thể sử dụng Facade Pattern. Mô hình này tạo ra một giao diện đơn giản, giúp quản lý dễ dàng các hệ thống phức tạp như điều khiển nhiệt độ, ánh sáng, tưới nước và giám sát môi trường, nâng cao hiệu quả vận hành và trải nghiệm người dùng.Cách thức áp dụng Facade Pattern:

- **Tạo Lớp Facade**: Chúng ta xây dựng một lớp SmarthomeFacade, hoạt động như một trung tâm điều phối, liên kết các thành phần như cảm biến và bộ điều khiển.
- **Tích Hợp Dịch Vụ**: lớp SmarthomeFacade sẽ kết nối với các dịch vụ như TemperatureSensor, HumiditySensor, FanController, LightController, IrrigationSystem, CO2Monitor, và NotificationService. Lớp này che giấu các chi tiết kỹ thuật, cung cấp một giao diện đơn giản để thực hiện các chức năng phức tạp.
- **Đơn Giản Hóa Giao Diện Người Dùng**: người dùng chỉ cần tương tác với lớp GreenhouseFacade mà không phải điều khiển từng thành phần riêng lẻ. Ví dụ, để kích hoạt chế độ tự động quản lý nhà kính, người dùng chỉ cần gọi một phương thức trên GreenhouseFacade. Hệ thống sẽ tự động thực hiện các tác vụ như kiểm tra nhiệt độ, bật/tắt quạt, điều chỉnh ánh sáng, hoặc gửi thông báo khi có cảnh báo.

![alt text](image/image30.png)

Sơ đồ minh họa cách SmarthomeFacade kết nối và điều phối các thành phần sẽ giúp đơn giản hóa quy trình vận hành nhà kính thông minh. Facade Pattern không chỉ giảm thiểu sự phức tạp mà còn đảm bảo hệ thống hoạt động ổn định, nâng cao trải nghiệm và hiệu quả quản lý.

## Cấu trúc

![alt text](image/image31.png)

Các Thành Phần:
- Facade: Một lớp duy nhất đóng vai trò là giao diện chính cho hệ thống con phức tạp. Facade biết chức năng nào của hệ thống con cần được kích hoạt để xử lý yêu cầu.
- Hệ thống con (Subsystems): Các lớp cấu thành hệ thống, mỗi lớp cung cấp chức năng đặc biệt. Chúng có thể làm việc độc lập hoặc tương tác với nhau.

Tổ Chức và Tương Tác:
- Facade cung cấp một giao diện đơn giản đến một hoặc nhiều hệ thống con phức tạp. Khi một yêu cầu đến từ client, Facade sẽ xác định xem cần kích hoạt chức năng nào từ hệ thống con để xử lý yêu cầu đó.
- Hệ thống con không biết về sự tồn tại của Facade; chúng xử lý các nhiệm vụ được giao mà không cần biết liệu yêu cầu đến từ Facade hay trực tiếp từ client.
- Facade có thể chọn một hoặc nhiều chức năng từ mỗi hệ thống con để hoàn thành yêu cầu, giúp giảm sự phức tạp và tương tác trực tiếp mà client cần thực hiện với hệ thống.

## Triển khai

```cpp
#include <iostream>
#include <string>

// Subsystem 1: Temperature Sensor
class TemperatureSensor {
public:
    float getTemperature() {
        return 25.5;
    }
};

// Subsystem 2: Humidity Sensor
class HumiditySensor {
public:
    float getHumidity() {
        return 60.0; // Giả lập độ ẩm
    }
};

// Subsystem 3: Fan Controller
class FanController {
public:
    void turnOn() {
        std::cout << "Fan is turned ON.\n";
    }

    void turnOff() {
        std::cout << "Fan is turned OFF.\n";
    }
};

// Subsystem 4: Light Controller
class LightController {
public:
    void turnOn() {
        std::cout << "Lights are turned ON.\n";
    }

    void turnOff() {
        std::cout << "Lights are turned OFF.\n";
    }
};

// Subsystem 5: Irrigation System
class IrrigationSystem {
public:
    void startIrrigation() {
        std::cout << "Irrigation system started.\n";
    }

    void stopIrrigation() {
        std::cout << "Irrigation system stopped.\n";
    }
};

// Subsystem 6: Notification Service
class NotificationService {
public:
    void sendAlert(const std::string& message) {
        std::cout << "ALERT: " << message << "\n";
    }
};

// Facade Class: SmarthomeFacade
class SmarthomeFacade {
private:
    TemperatureSensor temperatureSensor;
    HumiditySensor humiditySensor;
    FanController fanController;
    LightController lightController;
    IrrigationSystem irrigationSystem;
    NotificationService notificationService;

public:
    void monitorAndControl() {
        float temp = temperatureSensor.getTemperature();
        float humidity = humiditySensor.getHumidity();

        std::cout << "Current Temperature: " << temp << "°C\n";
        std::cout << "Current Humidity: " << humidity << "%\n";

        // Điều khiển quạt dựa trên nhiệt độ
        if (temp > 30.0) {
            fanController.turnOn();
        } else {
            fanController.turnOff();
        }

        // Điều chỉnh ánh sáng dựa trên điều kiện (giả lập ban ngày)
        bool isDaytime = true; // Giả lập trạng thái
        if (isDaytime) {
            lightController.turnOn();
        } else {
            lightController.turnOff();
        }

        // Điều khiển tưới nước dựa trên độ ẩm
        if (humidity < 50.0) {
            irrigationSystem.startIrrigation();
        } else {
            irrigationSystem.stopIrrigation();
        }

        // Gửi cảnh báo nếu vượt ngưỡng
        if (temp > 40.0) {
            notificationService.sendAlert("Temperature too high!");
        }

        if (humidity < 30.0) {
            notificationService.sendAlert("Humidity too low!");
        }
    }
};

// Main Function
int main() {
    SmarthomeFacade smarthome;

    std::cout << "Starting greenhouse monitoring and control...\n";
    smarthome.monitorAndControl();

    return 0;
}
```