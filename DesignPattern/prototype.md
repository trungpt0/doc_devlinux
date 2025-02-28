# Prototype

## Giới thiệu

Prototype là một Creational Design Pattern cho phép sao chép các đối tượng hiện có thay vì khởi tạo chúng từ đầu. 

Cụ thể, Prototype Pattern định nghĩa một kiểu đối tượng (Prototype) có khả năng tự nhân bản bằng cách tạo ra một bản sao độc lập với đối tượng gốc.

Mục đích của Pattern này là tạo ra các đối tượng mới bằng cách clone từ đối tượng hiện có thay vì khởi tạo, tiết kiệm chi phí tạo mới đối tượng, đặc biệt là các đối tượng phức tạp. Ngoài ra, nó che giấu logic khởi tạo và cung cấp khả năng tạo các đối tượng tương tự một cách hiệu quả.

## Đặt vấn đề

Giả sử bạn có một mảng cảm biến với các cấu hình giống nhau và muốn sao chép cấu hình để áp dụng cho từng cảm biến trong mảng. Tuy nhiên, thường có nhiều cảm biến chỉ khác biệt ở một vài thuộc tính nhỏ. Ví dụ như cảm biến độ ẩm, nhiệt độ thì tại các vị trí lắp đặt khác nhau hoặc tần suất đọc dữ liệu khác nhau thì chúng sẽ khác nhau mặc dù cùng các chức năng như đọc độ ẩm, nhiệt độ.

Nếu phải khởi tạo hoàn toàn từ đầu các cảm biến này thì rất tốn kém và lãng phí tài nguyên. Vì thế Prototype Pattern ra đời nhằm giải quyết bài toán này bằng cách tạo ra các đối tượng tương tự một cách hiệu quả hơn, bằng cách tận dụng lại những đối tượng đã khởi tạo từ trước.

## Giải quyết

![alt text](image/image10.png)

Giải thích:
- Định nghĩa một interface Prototype chung cho các đối tượng nhân vật có thể clone.
- Các lớp nhân vật cụ thể (Concrete Prototype) sẽ triển khai interface này và cung cấp hiện thực cho phương thức clone(). Phương thức clone() sẽ sao chép giá trị các trường dữ liệu của đối tượng sang một đối tượng mới.
- Tạo một đối tượng nhân vật ban đầu với quá trình khởi tạo đầy đủ.
- Khi cần tạo nhân vật mới tương tự, client sẽ gọi phương thức clone() trên đối tượng ban đầu để tạo ra bản sao. Sau đó có thể thay đổi các thuộc tính cần thiết trên đối tượng mới.

Như vậy, Prototype Pattern cho phép tạo ra các đối tượng nhân vật mới một cách nhanh chóng và hiệu quả hơn so với khởi tạo lại từ đầu.

## Cấu trúc
Để hiểu rõ cách tổ chức và hoạt động của Prototype Pattern, chúng ta cùng phân tích kỹ hơn cấu trúc của Pattern này

![alt text](image/image11.png)

Các thành phần chính trong Prototype Pattern bao gồm:
- Prototype: định nghĩa một interface chung, khai báo phương thức clone() cho việc sao chép. Đây là giao diện mà Client sẽ tương tác để tạo ra các đối tượng
- ConcretePrototype: các lớp cụ thể triển khai interface Prototype. Chúng cung cấp hiện thực cho phương thức clone() để sao chép chính bản thân mình, tạo ra một bản sao độc lập.
- Client: tương tác với các đối tượng thông qua interface Prototype, không phụ thuộc vào các lớp cụ thể. Client khởi tạo một ConcretePrototype ban đầu với đầy đủ các bước. Sau đó, nó sẽ sử dụng đối tượng này như một Prototype để nhân bản thành các đối tượng mới thay vì phải khởi tạo lại từ đầu.

## Ví dụ

Giả sử bạn đang phát triển một hệ thống giám sát môi trường với các loại cảm biến. Trong hệ thống này mỗi cảm biến sẽ gồm vị trí lắp đặt, tần suất đọc dữ liệu, ngưỡng cảnh báo.

```cpp
class Sensor {
public:
    virtual std::shared_ptr<Sensor> clone() const = 0;
    virtual void configure(const std::string& location, int samplingRate, double threshold) = 0;
    virtual void printDetails() const = 0;
    virtual ~Sensor() = default;
};
```

Tiếp theo, bạn tạo một lớp cụ thể EnvironmentalSensor kế thừa từ giao diện Sensor và triển khai các phương thức configure và printDetails:

```cpp
// Concrete Prototype: Environmental Sensor
class EnvironmentalSensor : public Sensor {
private:
    std::string type;         // Loại cảm biến: Nhiệt độ, Độ ẩm, Ánh sáng
    std::string location;     // Vị trí lắp đặt
    int samplingRate;         // Tần suất đọc dữ liệu (Hz)
    double threshold;         // Ngưỡng cảnh báo

public:
    EnvironmentalSensor(const std::string& sensorType) : type(sensorType), samplingRate(0), threshold(0.0) {}

    // Implement clone method
    std::shared_ptr<Sensor> clone() const override {
        return std::make_shared<EnvironmentalSensor>(*this);
    }

    // Configure sensor
    void configure(const std::string& location, int samplingRate, double threshold) override {
        this->location = location;
        this->samplingRate = samplingRate;
        this->threshold = threshold;
    }

    // Print sensor details
    void printDetails() const override {
        std::cout << "Loại cảm biến: " << type << "\n";
        std::cout << "Vị trí: " << location << "\n";
        std::cout << "Tần suất đọc dữ liệu: " << samplingRate << " Hz\n";
        std::cout << "Ngưỡng cảnh báo: " << threshold << "\n";
    }
};
```

Sau đó, ta có thể sử dụng Mẫu thiết kế Prototype để sao chép các cảm biến mà không cần tạo mới chúng từ đầu:

```cpp
// Main function
int main() {
    // Create prototypes for sensors
    std::shared_ptr<Sensor> temperatureSensor = std::make_shared<EnvironmentalSensor>("Nhiệt độ");
    std::shared_ptr<Sensor> humiditySensor = std::make_shared<EnvironmentalSensor>("Độ ẩm");
    std::shared_ptr<Sensor> lightSensor = std::make_shared<EnvironmentalSensor>("Ánh sáng");

    // Clone and configure sensors
    auto tempSensorRoom1 = temperatureSensor->clone();
    tempSensorRoom1->configure("Phòng 1", 2, 25.5);

    auto tempSensorRoom2 = temperatureSensor->clone();
    tempSensorRoom2->configure("Phòng 2", 1, 22.0);

    auto humiditySensorRoom1 = humiditySensor->clone();
    humiditySensorRoom1->configure("Phòng 1", 5, 60.0);

    auto lightSensorHallway = lightSensor->clone();
    lightSensorHallway->configure("Hành lang", 10, 300.0);

    // Print sensor details
    std::cout << "Thông tin cảm biến 1:\n";
    tempSensorRoom1->printDetails();

    std::cout << "\nThông tin cảm biến 2:\n";
    tempSensorRoom2->printDetails();

    std::cout << "\nThông tin cảm biến 3:\n";
    humiditySensorRoom1->printDetails();

    std::cout << "\nThông tin cảm biến 4:\n";
    lightSensorHallway->printDetails();

    return 0;
}
```