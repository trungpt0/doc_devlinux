# Command

## Khái niệm

Command Pattern là một trong các mẫu thiết kế thuộc nhóm Behavioral Design Patterns giúp chuyển đổi một yêu cầu thành một đối tượng độc lập chứa đầy đủ thông tin về yêu cầu đó. Command Pattern cho phép đóng gói thông tin cần thiết để thực hiện một hành động hoặc kích hoạt một sự kiện vào trong một đối tượng đơn lẻ. Điều này bao gồm thông tin về phương thức nào được gọi, người gọi, và các tham số của phương thức.

## Mục đích

Mục đích chính của mẫu thiết kế này là tách rời người phát ra yêu cầu (sender) khỏi đối tượng thực hiện yêu cầu (receiver). Điều này giúp giảm sự phụ thuộc giữa các lớp gọi và lớp nhận, từ đó tăng tính mô-đun và khả năng mở rộng của ứng dụng.

## Cấu trúc

![alt text](image/image37.png)

- Command (Interface): đây là một interface hoặc abstract class định nghĩa phương thức execute() với mục đích là để tạo một giao diện chung cho tất cả các lệnh cụ thể.
- ConcreteCommand: là một lớp cụ thể thực hiện interface Command. Trong phương thức execute(), nó gọi phương thức tương ứng của Receiver.
- Invoker: lưu trữ một tham chiếu đến một đối tượng Command. Gọi phương thức execute() trên đối tượng Command để thực hiện yêu cầu.
- Receiver: biết cách thực hiện các hoạt động cần thiết để thực hiện yêu cầu và mỗi ConcreteCommand sẽ liên kết với một Receiver.
- Client: tạo ra một đối tượng ConcreteCommand và thiết lập receiver của nó và có thể giao Command cho Invoker để thực hiện.

## Cách triển khai

### Command interface

```cpp
class Command {
public:
    virtual ~Command = default;
    virtual void execute() const = 0;
};
```

### Concrete Command

```cpp
class ConcreteCommand : public Command {
private:
    Receiver* receiver; // Tham chiếu đến đối tượng thực hiện hành động

public:
    explicit ConcreteCommand(Receiver* receiver) : receiver(receiver) {}

    void execute() const override {
        receiver->action(); // Gọi phương thức thực thi của Receiver
    }
};

```

### Receiver

```cpp
class Receiver {
public:
    void action() const {
        std::cout << "Action performed by Receiver" << std::endl;
    }
};
```

### Invoker

```cpp
class Invoker {
private:
    Command* command; // Lưu trữ tham chiếu đến Command

public:
    void setCommand(Command* command) {
        this->command = command;
    }

    void executeCommand() const {
        if (command) {
            command->execute(); // Gọi phương thức execute() của Command
        }
    }
};
```

### Client

```cpp
int main() {
    // Tạo Receiver
    Receiver receiver;

    // Tạo Command với Receiver
    ConcreteCommand command(&receiver);

    // Tạo Invoker và gán Command
    Invoker invoker;
    invoker.setCommand(&command);

    // Thực thi lệnh qua Invoker
    invoker.executeCommand(); // Output: Action performed by Receiver

    return 0;
}
```

## Ví dụ

Giả sử chúng ta có một mảng các thiết bị (ví dụ: các bóng đèn), và chúng ta sẽ sử dụng Command Pattern để điều khiển việc bật/tắt các thiết bị này.

```cpp
class Command {
public:
    virtual ~Command() = default;
    virtual void execute(int index) const = 0; // Phương thức thực thi lệnh
};

class TurnOnDeviceCommand : public Command {
private:
    DeviceArray* deviceArray;

public:
    explicit TurnOnDeviceCommand(DeviceArray* deviceArray) : deviceArray(deviceArray) {}

    void execute(int index) const override {
        deviceArray->turnOnDevice(index); // Gọi phương thức bật thiết bị tại index
    }
};

class TurnOffDeviceCommand : public Command {
private:
    DeviceArray* deviceArray;

public:
    explicit TurnOffDeviceCommand(DeviceArray* deviceArray) : deviceArray(deviceArray) {}

    void execute(int index) const override {
        deviceArray->turnOffDevice(index); // Gọi phương thức tắt thiết bị tại index
    }
};

class DeviceArray {
private:
    std::vector<bool> devices; // Mảng thiết bị (true = bật, false = tắt)

public:
    DeviceArray(int size) : devices(size, false) {}

    void turnOnDevice(int index) {
        if (index >= 0 && index < devices.size()) {
            devices[index] = true;
            std::cout << "Device " << index << " turned ON!" << std::endl;
        }
    }

    void turnOffDevice(int index) {
        if (index >= 0 && index < devices.size()) {
            devices[index] = false;
            std::cout << "Device " << index << " turned OFF!" << std::endl;
        }
    }

    void showStatus() const {
        for (int i = 0; i < devices.size(); ++i) {
            std::cout << "Device " << i << ": " << (devices[i] ? "ON" : "OFF") << std::endl;
        }
    }
};

class Invoker {
private:
    Command* command; // Lưu trữ lệnh

public:
    void setCommand(Command* command) {
        this->command = command;
    }

    void executeCommand(int index) const {
        if (command) {
            command->execute(index); // Thực thi lệnh
        }
    }
};

int main() {
    // Tạo mảng 5 thiết bị
    DeviceArray deviceArray(5);

    // Tạo các lệnh bật/tắt thiết bị
    TurnOnDeviceCommand turnOnCommand(&deviceArray);
    TurnOffDeviceCommand turnOffCommand(&deviceArray);

    // Tạo Invoker
    Invoker invoker;

    // Bật thiết bị thứ 2 (index 1)
    invoker.setCommand(&turnOnCommand);
    invoker.executeCommand(1); // Output: Device 1 turned ON!

    // Tắt thiết bị thứ 2 (index 1)
    invoker.setCommand(&turnOffCommand);
    invoker.executeCommand(1); // Output: Device 1 turned OFF!

    // Bật thiết bị thứ 4 (index 3)
    invoker.setCommand(&turnOnCommand);
    invoker.executeCommand(3); // Output: Device 3 turned ON!

    // Hiển thị trạng thái các thiết bị
    deviceArray.showStatus();

    return 0;
}
```