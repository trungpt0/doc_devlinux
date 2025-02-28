# Observer

## Khái niệm

Observer Pattern là một mẫu thiết kế hành vi (Behavioral Design Pattern). Nó được sử dụng để tạo một cơ chế quản lý mối quan hệ một-đến-nhiều giữa các đối tượng, sao cho khi trạng thái của một đối tượng thay đổi, tất cả đối tượng phụ thuộc (observers) sẽ được thông báo và cập nhật tự động.

Observer Pattern bao gồm hai loại đối tượng chính: 'Subject' (đối tượng quan sát) và 'Observer' (đối tượng được thông báo). 'Subject' duy trì một danh sách các 'Observer' quan tâm tới trạng thái của nó và thông báo cho tất cả 'Observers' khi có bất kỳ thay đổi nào về trạng thái.

## Mục đích

Mục đích của Observer Pattern là tạo điều kiện cho việc tạo ra các ứng dụng có cấu trúc linh hoạt, mở rộng được và dễ bảo trì bằng cách giảm thiểu sự phụ thuộc lẫn nhau giữa các đối tượng. Khi một 'Subject' thay đổi trạng thái, 'Observers' của nó sẽ tự động được thông báo và cập nhật mà không cần sự can thiệp trực tiếp.

## Cấu trúc

![alt text](image/image39.png)

**Các thành phần trong Observer Pattern:**

- Subject: đối tượng chủ thể cần theo dõi. Nó duy trì danh sách các Observer và thông báo cho chúng khi trạng thái thay đổi.
- Observer: đối tượng quan sát Subject. Nó đăng ký theo dõi Subject và cập nhật khi nhận được thông báo từ Subject.
- ConcreteSubject và ConcreteObserver: các cài đặt cụ thể.

## Cách triển khai

### Subject Interface

```cpp
class Observer;
class Message;

// Subject Interface
class Subject {
public:
    virtual void attach(Observer* o) = 0;
    virtual void detach(Observer* o) = 0;
    virtual void notifyUpdate(const Message& m) = 0;
    virtual ~Subject() = default;
};
```

### Observer Interface

```cpp
class Observer {
public:
    virtual void update(const Message& m) = 0;
    virtual ~Observer() = default;
};
```

### Concrete Subject

```cpp
class ConcreteSubject : public Subject {
private:
    std::vector<Observer*> observers;

public:
    void attach(Observer* o) override {
        observers.push_back(o);
    }

    void detach(Observer* o) override {
        observers.erase(std::remove(observers.begin(), observers.end(), o), observers.end());
    }

    void notifyUpdate(const Message& m) override {
        for (Observer* o : observers) {
            o->update(m);
        }
    }
};
```

### Concrete Observer

```cpp
class ConcreteObserver : public Observer {
private:
    std::string name;

public:
    explicit ConcreteObserver(const std::string& observerName) : name(observerName) {}

    void update(const Message& m) override {
        std::cout << name << " received message: " << m.getMessageContent() << std::endl;
    }
};
```

### Message Class

```cpp
class Message {
private:
    std::string messageContent;

public:
    explicit Message(const std::string& content) : messageContent(content) {}

    std::string getMessageContent() const {
        return messageContent;
    }
};
```

### Main

```cpp
int main() {
    // Create the Subject
    ConcreteSubject subject;

    // Create Observers
    ConcreteObserver observer1("Observer 1");
    ConcreteObserver observer2("Observer 2");

    // Attach Observers to the Subject
    subject.attach(&observer1);
    subject.attach(&observer2);

    // Notify Observers
    std::cout << "Sending first message..." << std::endl;
    subject.notifyUpdate(Message("First Message"));

    // Detach an Observer
    subject.detach(&observer1);

    std::cout << "Sending second message..." << std::endl;
    subject.notifyUpdate(Message("Second Message"));

    return 0;
}
```

## Ví dụ

Giả sử một hệ thống nhúng có một Temperature Sensor (Subject) gửi dữ liệu nhiệt độ đến nhiều Displays (Observers) để hiển thị.

```cpp
// Forward declaration
class Observer;

// Subject Interface
class Subject {
public:
    virtual void attach(Observer* o) = 0;
    virtual void detach(Observer* o) = 0;
    virtual void notifyUpdate(int temperature) = 0;
    virtual ~Subject() = default;
};

// Observer Interface
class Observer {
public:
    virtual void update(int temperature) = 0;
    virtual ~Observer() = default;
};

class TemperatureSensor : public Subject {
private:
    std::vector<Observer*> observers;
    int temperature;

public:
    void attach(Observer* o) override {
        observers.push_back(o);
    }

    void detach(Observer* o) override {
        observers.erase(std::remove(observers.begin(), observers.end(), o), observers.end());
    }

    void notifyUpdate(int temp) override {
        for (Observer* o : observers) {
            o->update(temp);
        }
    }

    void setTemperature(int temp) {
        temperature = temp;
        notifyUpdate(temperature); // Notify all observers
    }
};

class LCD : public Observer {
public:
    void update(int temperature) override {
        std::cout << "LCD Display: Current Temperature = " << temperature << "°C" << std::endl;
    }
};

class LED : public Observer {
public:
    void update(int temperature) override {
        std::cout << "LED Display: Current Temperature = " << temperature << "°C" << std::endl;
    }
};

int main() {
    // Create Subject (Temperature Sensor)
    TemperatureSensor tempSensor;

    // Create Observers (Displays)
    LCD lcdDisplay;
    LED ledDisplay;

    // Attach Observers to the Sensor
    tempSensor.attach(&lcdDisplay);
    tempSensor.attach(&ledDisplay);

    // Simulate temperature changes
    std::cout << "Temperature Update 1..." << std::endl;
    tempSensor.setTemperature(25); // Notify all observers

    std::cout << "Temperature Update 2..." << std::endl;
    tempSensor.setTemperature(30); // Notify all observers

    // Detach an observer
    tempSensor.detach(&ledDisplay);

    std::cout << "Temperature Update 3..." << std::endl;
    tempSensor.setTemperature(35); // Only LCD will get notified

    return 0;
}
```