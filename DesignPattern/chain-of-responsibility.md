# Chain of Responsibility

## Khái niệm

Chain of Responsibility là một mẫu thiết kế trong lập trình giúp xử lý các yêu cầu (request) theo một chuỗi (chain), nơi mỗi đối tượng trong chuỗi có thể xử lý hoặc chuyển tiếp yêu cầu cho đối tượng tiếp theo trong chuỗi. Mẫu này giúp giảm sự phụ thuộc giữa các đối tượng bằng cách tách rời người gửi yêu cầu và người nhận yêu cầu.

## Mục đích

Mẫu thiết kế này giúp loại bỏ sự cứng nhắc trong việc chỉ định chính xác đối tượng xử lý một yêu cầu cụ thể. Nó giúp phân tán trách nhiệm xử lý và giảm sự phụ thuộc lẫn nhau giữa các đối tượng.

## Cấu trúc

![alt text](image/image42.png)

- Handler: Định nghĩa 1 interface để xử lý các yêu cầu.
- ConcreteHandler: Implement phương thức từ handler.
- Client: Tạo ra các yêu cầu và yêu cầu đó sẽ được gửi đến các đối tượng tiếp nhận.

## Cách triển khai

### Interface Handler

```cpp
class Handler {
public:
    virtual void handleRequest(const std::string& request) = 0;
    virtual void setNext(Handler* nextHandler) = 0;
    virtual ~Handler() = default;
};
```

### Concrete Handlers

```cpp
// ConcreteHandler1
class ConcreteHandler1 : public Handler {
private:
    Handler* next;

public:
    ConcreteHandler1() : next(nullptr) {}

    void handleRequest(const std::string& request) override {
        if (request == "Handler1") {
            std::cout << "ConcreteHandler1 đã xử lý yêu cầu." << std::endl;
        } else if (next != nullptr) {
            next->handleRequest(request);
        }
    }

    void setNext(Handler* nextHandler) override {
        next = nextHandler;
    }
};

// ConcreteHandler2
class ConcreteHandler2 : public Handler {
private:
    Handler* next;

public:
    ConcreteHandler2() : next(nullptr) {}

    void handleRequest(const std::string& request) override {
        if (request == "Handler2") {
            std::cout << "ConcreteHandler2 đã xử lý yêu cầu." << std::endl;
        } else if (next != nullptr) {
            next->handleRequest(request);
        }
    }

    void setNext(Handler* nextHandler) override {
        next = nextHandler;
    }
};
```

### Client

```cpp
class Client {
private:
    Handler* handler;

public:
    Client(Handler* handler) : handler(handler) {}

    void makeRequest(const std::string& request) {
        handler->handleRequest(request);
    }
};
```

### Main

```cpp
int main() {
    // Tạo các handler
    Handler* handler1 = new ConcreteHandler1();
    Handler* handler2 = new ConcreteHandler2();

    // Thiết lập chuỗi trách nhiệm
    handler1->setNext(handler2);

    // Tạo client và thực hiện yêu cầu
    Client client(handler1);
    client.makeRequest("Handler1");
    client.makeRequest("Handler2");
    client.makeRequest("Unknown"); // Sẽ không được xử lý bởi bất kỳ handler nào

    // Dọn dẹp bộ nhớ
    delete handler1;
    delete handler2;

    return 0;
}
```

## Ví dụ

Trong môi trường embedded systems, bạn có thể sử dụng mẫu Chain of Responsibility để xử lý các tín hiệu, lệnh điều khiển, hoặc sự kiện mà không cần phải xác định rõ đối tượng nào sẽ xử lý mỗi tín hiệu. Ví dụ dưới đây sẽ sử dụng mẫu thiết kế này để xử lý các sự kiện từ các cảm biến trong một hệ thống nhúng, ví dụ như nhận tín hiệu từ cảm biến nhiệt độ, cảm biến ánh sáng và cảm biến chuyển động.

```cpp
#include <iostream>
#include <string>

// Interface Handler
class Handler {
public:
    virtual void handleEvent(const std::string& event) = 0;
    virtual void setNext(Handler* nextHandler) = 0;
    virtual ~Handler() = default;
};

// Cảm biến nhiệt độ
class TemperatureSensorHandler : public Handler {
private:
    Handler* next;

public:
    TemperatureSensorHandler() : next(nullptr) {}

    void handleEvent(const std::string& event) override {
        if (event == "Temperature") {
            std::cout << "Temperature Sensor: Xử lý tín hiệu nhiệt độ." << std::endl;
        } else if (next != nullptr) {
            next->handleEvent(event);
        }
    }

    void setNext(Handler* nextHandler) override {
        next = nextHandler;
    }
};

// Cảm biến ánh sáng
class LightSensorHandler : public Handler {
private:
    Handler* next;

public:
    LightSensorHandler() : next(nullptr) {}

    void handleEvent(const std::string& event) override {
        if (event == "Light") {
            std::cout << "Light Sensor: Xử lý tín hiệu ánh sáng." << std::endl;
        } else if (next != nullptr) {
            next->handleEvent(event);
        }
    }

    void setNext(Handler* nextHandler) override {
        next = nextHandler;
    }
};

// Cảm biến chuyển động
class MotionSensorHandler : public Handler {
private:
    Handler* next;

public:
    MotionSensorHandler() : next(nullptr) {}

    void handleEvent(const std::string& event) override {
        if (event == "Motion") {
            std::cout << "Motion Sensor: Xử lý tín hiệu chuyển động." << std::endl;
        } else if (next != nullptr) {
            next->handleEvent(event);
        }
    }

    void setNext(Handler* nextHandler) override {
        next = nextHandler;
    }
};

// Main function
int main() {
    // Tạo các cảm biến
    Handler* tempSensor = new TemperatureSensorHandler();
    Handler* lightSensor = new LightSensorHandler();
    Handler* motionSensor = new MotionSensorHandler();

    // Thiết lập chuỗi trách nhiệm
    tempSensor->setNext(lightSensor);
    lightSensor->setNext(motionSensor);

    // Mô phỏng nhận sự kiện
    std::cout << "Nhận sự kiện: Temperature" << std::endl;
    tempSensor->handleEvent("Temperature");

    std::cout << "Nhận sự kiện: Light" << std::endl;
    tempSensor->handleEvent("Light");

    std::cout << "Nhận sự kiện: Motion" << std::endl;
    tempSensor->handleEvent("Motion");

    // Dọn dẹp bộ nhớ
    delete tempSensor;
    delete lightSensor;
    delete motionSensor;

    return 0;
}
```