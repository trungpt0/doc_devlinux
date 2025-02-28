# Template Method

## Khái niệm

Template Pattern là một mẫu thiết kế thuộc loại mẫu thiết kế hành vi (Behavioral Design Pattern). Mẫu này hoạt động bằng cách xác định khung sườn của một thuật toán trong một phương thức, hoãn một số bước cho các lớp con. Template Pattern cho phép lớp con có thể thay đổi hoặc mở rộng các bước cụ thể của thuật toán mà không thay đổi cấu trúc tổng thể của thuật toán.

## Mục đích

Mục đích của Template Pattern là tạo ra một cấu trúc thuật toán trong một phương thức, hoãn một số bước lại cho các lớp con. Mẫu này giúp tái sử dụng mã nguồn và tránh sự trùng lặp, đồng thời cung cấp một cách để các lớp con có thể mở rộng một số phần cụ thể của thuật toán mà không làm thay đổi cấu trúc thuật toán chính.

## Đặt vấn đề

Trong nhiều ứng dụng nhúng (embedded), các module xử lý thường chia sẻ những quy trình chung, nhưng cũng có các bước cần được điều chỉnh theo yêu cầu cụ thể của từng chức năng hoặc thiết bị. Điều này dễ dẫn đến việc lặp lại mã nguồn hoặc khó tích hợp, làm tăng độ phức tạp trong thiết kế và bảo trì hệ thống. Ví dụ, trong ứng dụng điều khiển cảm biến, các loại cảm biến khác nhau như nhiệt độ, áp suất, và gia tốc kế có thể tuân theo một quy trình chung để thu thập và xử lý dữ liệu, nhưng mỗi loại lại cần các bước đặc biệt như hiệu chỉnh (calibration), lọc tín hiệu, hoặc xử lý dữ liệu thô tùy thuộc vào đặc tính riêng của nó.

![alt text](image/image33.png)

## Giải pháp

Template Method Pattern giải quyết vấn đề trên bằng cách xác định khung của một thuật toán trong một phương thức, chừa lại các bước cụ thể để được ghi đè trong các lớp con. Điều này cho phép các lớp con mở rộng các bước cụ thể mà không cần thay đổi cấu trúc của thuật toán.

Việc áp dụng Template Method Pattern giúp giảm bớt sự trùng lặp mã nguồn và tăng tính tái sử dụng. Nó cũng giúp tập trung quản lý quy trình xử lý, đồng thời cung cấp khuôn mẫu cho các phần tuỳ chỉnh, làm cho mã nguồn dễ hiểu và bảo trì hơn.

Mặc dù Template Method Pattern giúp giảm sự lặp code và tăng tính mô-đun, nhưng nó cũng có thể dẫn đến một cấu trúc lớp phức tạp hơn và ít linh hoạt hơn do cơ chế kế thừa.

![alt text](image/image34.png)

- SensorControl định nghĩa phương thức Handler() gồm các phương thức TempuratureSensor(), HumiditySensor(), LightSensor() và được định nghĩa là trừu tượng trong lớp này để các lớp con cần phải cung cấp cài đặt cụ thể cho chúng.
- SpecificHandler xử lý từng cảm biến trong sơ đồ vấn đề. Lớp này kế thừa từ SensorControl và cung cấp các triển khai cụ thể cho các phương thức TempuratureSensor(), HumiditySensor(), LightSensor() xử lý cho từng loại cảm biến.

## Cấu trúc của Template Method Pattern

![alt text](image/image35.png)

- AbstractClass là một lớp trừu tượng chứa phương thức templateMethod(). Phương thức này thường được định nghĩa là final để ngăn chặn việc ghi đè, và nó gọi các phương thức trừu tượng (hoặc hook methods) khác như primitiveOperation1() và primitiveOperation2(), mà các lớp con sẽ cung cấp định nghĩa cụ thể.
- ConcreteClass là lớp cụ thể kế thừa từ AbstractClass và triển khai các phương thức trừu tượng như primitiveOperation1() và primitiveOperation2().

## Triển khai

Dưới đây là cách áp dụng Template Method Pattern trong lĩnh vực embedded để tạo khung xử lý chung cho các loại cảm biến.

### Abstract Class (Lớp trừu tượng)

Lớp trừu tượng định nghĩa khung xử lý (template method). Các bước xử lý chung được triển khai trong lớp này, trong khi các bước cần tùy chỉnh được để trống để các lớp con triển khai.

```cpp
// Abstract class
class Sensor {
public:
    // Template method
    void process() {
        initialize();
        calibrate();
        readData();
        filterData();
        processData();
    }

protected:
    // Steps that can be overridden by subclasses
    virtual void initialize() {
        cout << "Initializing sensor..." << endl;
    }
    virtual void calibrate() = 0; // Must be implemented by subclasses
    virtual void readData() = 0; // Must be implemented by subclasses
    virtual void filterData() {
        cout << "Default signal filtering..." << endl;
    }
    virtual void processData() {
        cout << "Processing sensor data..." << endl;
    }
};
```

### Concrete Classes (Lớp cụ thể)

```cpp
class TemperatureSensor : public Sensor {
protected:
    void calibrate() override {
        cout << "Calibrating temperature sensor..." << endl;
    }
    void readData() override {
        cout << "Reading temperature data..." << endl;
    }
    void filterData() override {
        cout << "Applying temperature-specific signal filtering..." << endl;
    }
};

class PressureSensor : public Sensor {
protected:
    void calibrate() override {
        cout << "Calibrating pressure sensor..." << endl;
    }
    void readData() override {
        cout << "Reading pressure data..." << endl;
    }
};
```

### Sử dụng Template Method Pattern

```cpp
int main() {
    cout << "Processing Temperature Sensor:" << endl;
    Sensor* tempSensor = new TemperatureSensor();
    tempSensor->process();
    delete tempSensor;

    cout << "\nProcessing Pressure Sensor:" << endl;
    Sensor* pressureSensor = new PressureSensor();
    pressureSensor->process();
    delete pressureSensor;

    return 0;
}
```