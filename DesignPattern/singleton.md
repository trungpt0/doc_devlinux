# Singletin

## Giới thiệu

Singleton là một Design Pattern thuộc nhóm Creational Pattern. Nó đảm bảo chỉ duy nhất một thể hiện của một lớp được tạo ra trong suốt chương trình.

## Đặt vấn đề

Trong nhiều trường hợp, cần đảm bảo chỉ có một thể hiện của một lớp. Ví dụ trong hệ thống lưu trữ dữ liệu từ cảm biến bằng Database thì chỉ nên có một đối tượng SensorManager để quản lý và thu thập dữ liệu từ các SensorNode và SensorManager sẽ đưa dữ liệu lên Database.

Nếu tạo nhiều đối tượng SensorManager có thể dẫn đến:
- Dữ liệu bị trùng lặp
- Xung đột tài nguyên
- Khó kiểm soát

![alt text](image/image7.png)

## Giải quyết

Singleton giải quyết bằng cách đảm bảo chỉ tạo duy nhất một thể hiện trong toàn bộ chương trình.

![alt text](image/image8.png)

Giải thích:
- Lớp Database được triển khai Singleton
- Chỉ có duy nhất một đối tượng SensorGateway (SensorManager) trong hệ thống
- Quản lý tất cả Sensor Node một cách tập trung
- Tránh được các vấn đề như dữ liệu trùng lặp, xung đột tài nguyên, khó kiểm soát

Với cách triển khai này, chỉ có một đối tượng SensorManager duy nhất được tạo ra, và đối tượng này có thể được truy cập từ bất kỳ nơi nào trong chương trình.

## Cấu trúc

Singleton Pattern có cấu trúc đơn giản, bao gồm các thành phần sau:

![alt text](image/image9.png)

* Lớp Singleton: Lớp này chứa các phương thức và biến cần thiết để triển khai Singleton Pattern.
* Phương thức khởi tạo private: Phương thức này chỉ có thể được gọi từ bên trong lớp.
* Biến static private: Biến này giữ đối tượng của lớp.
* Phương thức static public để trả về đối tượng của lớp: Phương thức này trả về đối tượng của lớp.

## Cách triển khai

Sử dụng một biến static private để lưu trữ instance của class.

```java
public class Singleton {
    private static Singleton instance;

    private Singleton() {
        // Constructor is private to prevent direct instantiation
    }

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

Cách triển khai này đảm bảo rằng chỉ có một instance của Singleton được tạo ra. Khi một đối tượng Singleton được yêu cầu, phương thức getInstance() sẽ kiểm tra xem instance đã tồn tại hay chưa. Nếu chưa, phương thức sẽ tạo ra một instance mới. Nếu đã tồn tại, phương thức sẽ trả về instance hiện tại.

Một cách triển khai khác của Singleton Pattern là sử dụng một biến static final private.

```java
public final class Singleton {

    private static final Singleton instance = new Singleton();

    private Singleton() {
        // Constructor is private to prevent direct instantiation
    }

    public static Singleton getInstance() {
        return instance;
    }
}
```

Cách triển khai này tương tự như cách triển khai đầu tiên, nhưng nó sử dụng một biến static final private thay vì một biến static private. Cách triển khai này có một số ưu điểm như sau:

- Sử dụng biến static final private sẽ ngăn chặn việc thay đổi giá trị của biến instance.
- Cấu trúc code sẽ gọn gàng hơn.

## Ví dụ

Dưới đây là một ví dụ minh họa cách sử dụng Singleton Pattern để tạo một đối tượng DatabaseConnection.

```cpp
#include <iostream>
#include <string>

// Singleton class for Sensor Gateway
class SensorGateway {
private:
    // Static instance of SensorGateway
    static SensorGateway* instance;

    // Private constructor to prevent instantiation
    SensorGateway() {
        initialized = false;
        connectedSensors = 0;
    }
    
    // Private destructor
    ~SensorGateway() {}

    bool initialized;
    int connectedSensors;

public:
    // Static method to get the Singleton instance
    static SensorGateway* getInstance() {
        if (instance == nullptr) {
            instance = new SensorGateway();
        }
        return instance;
    }

    // Initialize the Sensor Gateway
    void initialize() {
        if (!initialized) {
            std::cout << "Initializing Sensor Gateway...\n";
            initialized = true;
        } else {
            std::cout << "Sensor Gateway is already initialized.\n";
        }
    }

    // Connect a Sensor Node
    void connectSensor() {
        if (initialized) {
            connectedSensors++;
            std::cout << "Sensor Node connected. Total sensors: " << connectedSensors << "\n";
        } else {
            std::cout << "Sensor Gateway is not initialized. Cannot connect sensor.\n";
        }
    }

    // Disconnect a Sensor Node
    void disconnectSensor() {
        if (initialized && connectedSensors > 0) {
            connectedSensors--;
            std::cout << "Sensor Node disconnected. Total sensors: " << connectedSensors << "\n";
        } else {
            std::cout << "No sensors to disconnect or gateway not initialized.\n";
        }
    }
};

// Define and initialize static instance to nullptr
SensorGateway* SensorGateway::instance = nullptr;

// Main function
int main() {
    // Get the singleton instance
    SensorGateway* gateway = SensorGateway::getInstance();

    // Initialize the gateway
    gateway->initialize();

    // Connect sensor nodes
    gateway->connectSensor();
    gateway->connectSensor();

    // Try to get the instance again and disconnect a sensor
    SensorGateway* anotherGateway = SensorGateway::getInstance();
    anotherGateway->disconnectSensor();

    return 0;
}
```

Giải thích:
- Static Instance: Biến static SensorGateway* instance đảm bảo chỉ tồn tại một thể hiện duy nhất của lớp SensorGateway.
- Private Constructor: Hàm dựng SensorGateway() được đặt ở chế độ private để ngăn chặn việc tạo thể hiện mới từ bên ngoài.
- Usage: Mọi truy cập tới SensorGateway đều thông qua getInstance().

## So sánh
Singleton Pattern có thể được so sánh với một số Design Pattern tương tự, chẳng hạn như:
- Factory Pattern: Factory Pattern cung cấp một cách để tạo các đối tượng của lớp một cách linh hoạt. Tuy nhiên, Factory Pattern không đảm bảo rằng chỉ có một đối tượng của lớp được tạo ra.
- Prototype Pattern: Prototype Pattern cung cấp một cách để tạo các bản sao của đối tượng. Prototype Pattern cũng có thể được sử dụng để tạo một đối tượng duy nhất của lớp. Tuy nhiên, Prototype Pattern có thể phức tạp hơn Singleton Pattern.

## So sánh
Khi áp dụng Singleton Pattern, cần lưu ý một số điểm sau:
- Singleton Pattern có thể làm giảm tính linh hoạt của ứng dụng. Ví dụ, nếu bạn cần tạo ra nhiều instance của một class, bạn sẽ cần phải thay đổi code để xóa phương thức getInstance().
- Singleton Pattern có thể gây ra vấn đề khi test. Ví dụ, nếu bạn đang test một class sử dụng Singleton Pattern, bạn sẽ cần tạo ra một instance giả của class đó.

## Kết luận

Singleton Pattern là một Design Pattern hữu ích trong những trường hợp cần đảm bảo rằng chỉ có một thể hiện duy nhất của một lớp được tạo ra. Tuy nhiên, cần lưu ý những điểm hạn chế của Singleton Pattern khi áp dụng.

Dưới đây là một số hướng dẫn sử dụng Singleton Pattern:
- Nên sử dụng Singleton Pattern khi cần đảm bảo rằng chỉ có một thể hiện duy nhất của một lớp được tạo ra.
- Tránh sử dụng Singleton Pattern khi không cần thiết.
- Hạn chế sử dụng Singleton trong các hệ thống lớn hoặc phức tạp.