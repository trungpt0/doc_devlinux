# Visitor

## Khái niệm

Visitor Pattern là một mẫu thiết kế thuộc nhóm Behavioral Design Patterns. Nó cho phép một hoặc nhiều đối tượng 'Visitor' định nghĩa một tập hợp các phép toán để áp dụng lên một tập các đối tượng 'Element' mà không làm thay đổi mã nguồn của những đối tượng này. Các 'Element' có một phương thức 'accept' mà nó nhận một 'Visitor' làm tham số. 'Visitor' này sau đó được áp dụng cho đối tượng 'Element'.

## Mục Đích

Mục đích chính của Visitor Pattern là cho phép thêm các hoạt động mới vào một tập hợp các lớp đối tượng mà không cần sửa đổi mã nguồn của chúng. Điều này giúp mã nguồn trở nên dễ dàng mở rộng hơn và giảm sự phụ thuộc giữa các chức năng và cấu trúc dữ liệu của đối tượng.

## Cấu trúc

![alt text](image/image43.png)

- Visitor là một interface hoặc abstract class chứa một loạt các phương thức visit(), mỗi phương thức cho một loại element khác nhau.
- ConcreteVisitor1 và ConcreteVisitor2 là các lớp cụ thể triển khai interface Visitor, cung cấp cách thực hiện cụ thể cho mỗi phương thức visit().
- Element là một interface hoặc abstract class chứa phương thức accept(), trong đó tham số là một đối tượng Visitor.
- ConcreteElementA và ConcreteElementB là các lớp cụ thể triển khai Element, mỗi lớp cung cấp triển khai cụ thể cho phương thức accept(), thường là gọi phương thức visit() của Visitor và truyền chính nó như một đối số.

## Cách triển khai

### Interface Visitor

```cpp
class DeviceVisitor {
public:
    virtual void visitSensor(const class Sensor& sensor) = 0;
    virtual void visitMotor(const class Motor& motor) = 0;
    virtual void visitLED(const class LED& led) = 0;
    virtual ~DeviceVisitor() = default;
};
```

### Interface Element

```cpp
class Device {
public:
    virtual void accept(DeviceVisitor& visitor) const = 0;
    virtual ~Device() = default;
};
```

### Concrete Visitor Classes

```cpp
class InitializeVisitor : public DeviceVisitor {
public:
    void visitSensor(const Sensor& sensor) override {
        std::cout << "Initializing Sensor...\n";
    }
    void visitMotor(const Motor& motor) override {
        std::cout << "Initializing Motor...\n";
    }
    void visitLED(const LED& led) override {
        std::cout << "Initializing LED...\n";
    }
};

class StatusVisitor : public DeviceVisitor {
public:
    void visitSensor(const Sensor& sensor) override {
        std::cout << "Reading Sensor status...\n";
    }
    void visitMotor(const Motor& motor) override {
        std::cout << "Checking Motor status...\n";
    }
    void visitLED(const LED& led) override {
        std::cout << "Checking LED status...\n";
    }
};
```

### Concrete Element Classes

```cpp
class Sensor : public Device {
public:
    void accept(DeviceVisitor& visitor) const override {
        visitor.visitSensor(*this);
    }
};

class Motor : public Device {
public:
    void accept(DeviceVisitor& visitor) const override {
        visitor.visitMotor(*this);
    }
};

class LED : public Device {
public:
    void accept(DeviceVisitor& visitor) const override {
        visitor.visitLED(*this);
    }
};
```

### Client Code

```cpp
int main() {
    // Tạo các thiết bị
    std::vector<std::unique_ptr<Device>> devices;
    devices.push_back(std::make_unique<Sensor>());
    devices.push_back(std::make_unique<Motor>());
    devices.push_back(std::make_unique<LED>());

    // Tạo các visitor
    InitializeVisitor initializer;
    StatusVisitor statusChecker;

    // Khởi tạo các thiết bị
    std::cout << "Initializing devices:\n";
    for (const auto& device : devices) {
        device->accept(initializer);
    }

    // Kiểm tra trạng thái các thiết bị
    std::cout << "\nChecking device statuses:\n";
    for (const auto& device : devices) {
        device->accept(statusChecker);
    }

    return 0;
}
```