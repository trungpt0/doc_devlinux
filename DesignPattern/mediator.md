# Mediator

## Khái niệm

Mediator Pattern là một mẫu thiết kế thuộc nhóm Behavioral Patterns. Nó giúp giảm sự phức tạp trong giao tiếp giữa nhiều đối tượng hoặc lớp bằng cách cung cấp một đối tượng trung gian, gọi là 'mediator'. Điều này giúp các đối tượng không giao tiếp trực tiếp với nhau, mà thông qua mediator, từ đó giảm thiểu sự phụ thuộc lẫn nhau và làm cho mã nguồn dễ bảo trì hơn.

## Mục Đích

Mục đích chính của Mediator Pattern là tạo ra một kênh giao tiếp trung tâm để các đối tượng có thể giao tiếp mà không cần biết về sự tồn tại của nhau. Điều này tạo điều kiện cho việc mở rộng và bảo trì hệ thống trở nên dễ dàng hơn.

## Cấu trúc

![alt text](image/image41.png)

- Mediator: định nghĩa interface để giao tiếp với các Colleague.
- Colleague: giao tiếp với mediator thay vì giao tiếp trực tiếp với các Colleague khác.
- ConcreteMediator: triển khai Mediator để điều phối các Colleague.
- ConcreteColleague: các lớp cụ thể tương tác với mediator.

## Cách triển khai

### Interface Mediator

```cpp
class Colleague; // Forward declaration

class Mediator {
public:
    virtual void registerColleague(Colleague* colleague) = 0;
    virtual void sendMessage(const std::string& message, Colleague* originator) = 0;
    virtual ~Mediator() = default;
};
```

### Class Abstract Colleague

```cpp
class Colleague {
protected:
    Mediator* mediator;

public:
    Colleague(Mediator* mediator) : mediator(mediator) {}
    virtual void receiveMessage(const std::string& message) = 0;
    virtual ~Colleague() = default;
};
```

### Class ConcreteMediator

```cpp
class ConcreteMediator : public Mediator {
private:
    std::vector<Colleague*> colleagues;

public:
    void registerColleague(Colleague* colleague) override {
        colleagues.push_back(colleague);
    }

    void sendMessage(const std::string& message, Colleague* originator) override {
        for (Colleague* colleague : colleagues) {
            // Không gửi thông điệp lại chính người gửi
            if (colleague != originator) {
                colleague->receiveMessage(message);
            }
        }
    }
};
```

### Class ConcreteColleague1 và ConcreteColleague2

```cpp
class ConcreteColleague1 : public Colleague {
public:
    ConcreteColleague1(Mediator* mediator) : Colleague(mediator) {}

    void receiveMessage(const std::string& message) override {
        std::cout << "ConcreteColleague1 received: " << message << std::endl;
    }

    void sendMessage(const std::string& message) {
        mediator->sendMessage(message, this);
    }
};

class ConcreteColleague2 : public Colleague {
public:
    ConcreteColleague2(Mediator* mediator) : Colleague(mediator) {}

    void receiveMessage(const std::string& message) override {
        std::cout << "ConcreteColleague2 received: " << message << std::endl;
    }

    void sendMessage(const std::string& message) {
        mediator->sendMessage(message, this);
    }
};
```

### Main

```cpp
int main() {
    // Tạo Mediator
    ConcreteMediator mediator;

    // Tạo các Colleague
    ConcreteColleague1 colleague1(&mediator);
    ConcreteColleague2 colleague2(&mediator);

    // Đăng ký Colleague với Mediator
    mediator.registerColleague(&colleague1);
    mediator.registerColleague(&colleague2);

    // Gửi và nhận tin nhắn
    std::cout << "Colleague1 sends a message:" << std::endl;
    colleague1.sendMessage("Hello from Colleague1");

    std::cout << "\nColleague2 sends a message:" << std::endl;
    colleague2.sendMessage("Hello from Colleague2");

    return 0;
}
```

## Ví dụ

Trong hệ thống nhúng, bạn có thể sử dụng một mảng đơn giản để đại diện cho các đối tượng hoặc trạng thái của các thiết bị, và Mediator sẽ giao tiếp với chúng. Hãy xem một ví dụ cụ thể hơn, giả sử bạn đang phát triển một hệ thống điều khiển các thiết bị nhúng (như LED, cảm biến, nút nhấn) trong một mảng.

```cpp
class Device; // Forward declaration

class Mediator {
public:
    virtual void registerDevice(Device* device) = 0;
    virtual void sendMessage(const std::string& message, Device* originator) = 0;
    virtual ~Mediator() = default;
};

class Device {
protected:
    Mediator* mediator;
    std::string name;

public:
    Device(Mediator* mediator, const std::string& name) : mediator(mediator), name(name) {}
    virtual void receiveMessage(const std::string& message) = 0;
    const std::string& getName() const { return name; }
    virtual ~Device() = default;
};

class ConcreteMediator : public Mediator {
private:
    static const int MAX_DEVICES = 3;
    Device* devices[MAX_DEVICES];
    int deviceCount = 0;

public:
    void registerDevice(Device* device) override {
        if (deviceCount < MAX_DEVICES) {
            devices[deviceCount++] = device;
        }
    }

    void sendMessage(const std::string& message, Device* originator) override {
        for (int i = 0; i < deviceCount; ++i) {
            if (devices[i] != originator) {
                devices[i]->receiveMessage(message);
            }
        }
    }
};

class ConcreteDevice1 : public Device {
public:
    ConcreteDevice1(Mediator* mediator, const std::string& name) : Device(mediator, name) {}

    void receiveMessage(const std::string& message) override {
        std::cout << getName() << " received: " << message << std::endl;
    }

    void sendMessage(const std::string& message) {
        mediator->sendMessage(message, this);
    }
};

class ConcreteDevice2 : public Device {
public:
    ConcreteDevice2(Mediator* mediator, const std::string& name) : Device(mediator, name) {}

    void receiveMessage(const std::string& message) override {
        std::cout << getName() << " received: " << message << std::endl;
    }

    void sendMessage(const std::string& message) {
        mediator->sendMessage(message, this);
    }
};

class ConcreteDevice3 : public Device {
public:
    ConcreteDevice3(Mediator* mediator, const std::string& name) : Device(mediator, name) {}

    void receiveMessage(const std::string& message) override {
        std::cout << getName() << " received: " << message << std::endl;
    }

    void sendMessage(const std::string& message) {
        mediator->sendMessage(message, this);
    }
};

int main() {
    ConcreteMediator mediator;

    ConcreteDevice1 device1(&mediator, "Device1");
    ConcreteDevice2 device2(&mediator, "Device2");
    ConcreteDevice3 device3(&mediator, "Device3");

    mediator.registerDevice(&device1);
    mediator.registerDevice(&device2);
    mediator.registerDevice(&device3);

    std::cout << "Device1 sends a message:" << std::endl;
    device1.sendMessage("Hello from Device1");

    std::cout << "\nDevice2 sends a message:" << std::endl;
    device2.sendMessage("Hello from Device2");

    std::cout << "\nDevice3 sends a message:" << std::endl;
    device3.sendMessage("Hello from Device3");

    return 0;
}
```

- Mảng Devices: Trong ví dụ này, mảng devices[] trong ConcreteMediator được dùng để lưu trữ tối đa 3 thiết bị (Device). Đây là một mảng đơn giản trong hệ thống nhúng mà không cần phải sử dụng cấu trúc dữ liệu phức tạp. Mảng này giữ các đối tượng Device và quản lý việc giao tiếp giữa chúng thông qua Mediator.

- Colleague là Thiết Bị: Các đối tượng như ConcreteDevice1, ConcreteDevice2, ConcreteDevice3 đại diện cho các thiết bị hoặc các bộ phận trong hệ thống nhúng (ví dụ: LED, cảm biến, nút nhấn, hoặc các mô-đun điều khiển khác).

- Gửi và Nhận Thông Điệp: Mỗi Device có thể gửi thông điệp tới các thiết bị khác thông qua Mediator. Mediator đảm nhiệm việc chuyển tiếp thông điệp giữa các thiết bị mà không cần thiết bị nào phải biết về các thiết bị khác.