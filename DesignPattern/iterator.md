# Iterator

## Khái niệm

Iterator Pattern là một mẫu thiết kế hành vi (Behavioral Design Pattern). Nó cho phép bạn duyệt qua các phần tử của một bộ sưu tập (collection) mà không cần quan tâm tới cấu trúc cụ thể của nó, như danh sách (list), ngăn xếp (stack), cây (tree), v.v. Iterator tạo ra một giao diện thống nhất cho việc truy cập và duyệt qua các phần tử, giúp tách rời cách các bộ sưu tập được cấu trúc từ logic duyệt qua chúng.

## Mục đích

Mục đích chính của Iterator Pattern là giảm sự phụ thuộc trực tiếp giữa các algorithim duyệt và cấu trúc dữ liệu của các bộ sưu tập. Điều này giúp tăng tính mô-đun và khả năng tái sử dụng code, đồng thời cung cấp một cách tiếp cận thống nhất cho việc duyệt dữ liệu.

## Cấu trúc

![alt text](image/image40.png)

- Iterator : là một interface hoặc abstract class khai báo các hoạt động cần thiết để duyệt qua các phần tử.
- Concrete Iterators : cài đặt các phương thức của Iterator, giữ index khi duyệt qua các phần tử.
- Iterable Collection : là một interface tạo ra một hoặc nhiều phương thức cho để lấy interators tương thích với Collection.
- Concrete Collections : cài đặt các phương thức Iterable Collection, nó cái đặt interface tạo Iterator trả về một Concrete Iterators thích hợp.
- Client : Đối tượng sử dụng Iterator Pattern.

## Ví dụ

Ví dụ trong hệ thống nhúng đọc dữ liệu từ một số cảm biến nhiệt độ qua I2C. Iterator Pattern được sử dụng để duyệt qua dữ liệu cảm biến mà không cần biết chi tiết cách lưu trữ.

### Iterator

```cpp
class Iterator {
public:
    virtual bool hasNext() = 0;  // Kiểm tra nếu còn phần tử
    virtual int next() = 0;     // Lấy phần tử tiếp theo
    virtual ~Iterator() = default;
};
```

### Concrete Iterators

```cpp
class SensorIterator : public Iterator {
private:
    int* data;
    int size;
    int index;

public:
    SensorIterator(int* dataArray, int dataSize) : data(dataArray), size(dataSize), index(0) {}

    bool hasNext() override {
        return index < size;
    }

    int next() override {
        return hasNext() ? data[index++] : -1; // Trả về -1 nếu hết phần tử
    }
};
```

### Iterable Collection

```cpp
class IterableCollection {
public:
    virtual Iterator* createIterator() = 0; // Tạo một iterator
    virtual ~IterableCollection() = default;
};
```

### Concrete Collections

```cpp
class SensorCollection : public IterableCollection {
private:
    static const int MAX_SENSORS = 10;
    int sensorData[MAX_SENSORS];
    int count;

public:
    SensorCollection() : count(0) {}

    void addSensorData(int value) {
        if (count < MAX_SENSORS) {
            sensorData[count++] = value;
        }
    }

    Iterator* createIterator() override {
        return new SensorIterator(sensorData, count);
    }
};
```

### Client

```cpp
// Hàm giả lập đọc nhiệt độ từ cảm biến qua I2C
int readTemperatureFromSensor(int sensorId) {
    return 20 + sensorId * 2; // Trả về nhiệt độ giả lập
}

int main() {
    // Tạo tập hợp dữ liệu cảm biến
    SensorCollection sensors;

    // Giả lập đọc dữ liệu từ 5 cảm biến
    for (int i = 0; i < 5; ++i) {
        sensors.addSensorData(readTemperatureFromSensor(i));
    }

    // Tạo Iterator
    Iterator* sensorIterator = sensors.createIterator();

    // Duyệt qua các phần tử
    std::cout << "Dữ liệu nhiệt độ cảm biến:" << std::endl;
    while (sensorIterator->hasNext()) {
        std::cout << "Nhiệt độ: " << sensorIterator->next() << "°C" << std::endl;
    }

    delete sensorIterator;

    return 0;
}
```