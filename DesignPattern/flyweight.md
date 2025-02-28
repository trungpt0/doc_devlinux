# Flyweight 

## Định nghĩa

Flyweight Pattern là một mẫu thiết kế cấu trúc (Structural Design Pattern). Nó sử dụng chia sẻ để hỗ trợ một lượng lớn đối tượng nhỏ mà không làm giảm hiệu suất. Mẫu này giúp giảm thiểu việc sử dụng bộ nhớ bằng cách chia sẻ càng nhiều trạng thái giữa các đối tượng càng tốt.

## Mục Đích

Mẫu thiết kế này hữu ích khi cần tạo ra một số lượng lớn đối tượng nhỏ trong một hệ thống, nơi mà việc sử dụng bộ nhớ và tài nguyên là một vấn đề cần được xem xét kỹ lưỡng.

## Đặt vấn đề

Hãy tưởng tượng bạn đang phát triển một hệ thống nhúng cho một thiết bị IoT, chẳng hạn như một trạm cảm biến giám sát môi trường với hàng ngàn cảm biến, mỗi cảm biến thu thập dữ liệu từ các khu vực khác nhau (nhiệt độ, độ ẩm, mức độ ánh sáng). Các cảm biến này cần kết nối và gửi dữ liệu tới một trung tâm xử lý để phân tích và hiển thị kết quả.

Ban đầu, mỗi cảm biến trong hệ thống được xử lý như một thực thể độc lập, với bộ nhớ và tài nguyên xử lý riêng biệt cho mỗi cảm biến, bao gồm các thuộc tính như vị trí, ID, dữ liệu cảm biến và trạng thái. Khi số lượng cảm biến ngày càng tăng lên, vấn đề về bộ nhớ và hiệu suất xử lý trở nên rõ rệt, đặc biệt khi hệ thống cần duy trì và quản lý hàng ngàn cảm biến hoạt động đồng thời.

Mỗi cảm biến có thể có các thuộc tính chung như loại cảm biến, giao thức truyền thông, và các phương thức xử lý dữ liệu. Tuy nhiên, việc duy trì mỗi cảm biến như một đối tượng riêng biệt có thể gây lãng phí tài nguyên, ảnh hưởng đến hiệu suất và tốc độ phản hồi của hệ thống.

Đây chính là lúc Flyweight Pattern có thể được áp dụng trong hệ thống nhúng. Mẫu thiết kế này giúp tối ưu hóa bộ nhớ và tài nguyên xử lý bằng cách chia sẻ các thuộc tính chung giữa các cảm biến tương tự. Các cảm biến có cùng loại, cùng giao thức truyền thông có thể chia sẻ các đối tượng chung, giảm bớt sự trùng lặp và tối thiểu hóa bộ nhớ cần thiết. Điều này không chỉ giúp cải thiện hiệu suất hệ thống mà còn giảm thiểu việc tiêu tốn tài nguyên, từ đó nâng cao trải nghiệm người dùng và đáp ứng được yêu cầu của môi trường nhúng, nơi tài nguyên hạn chế.

![alt text](image/image32.png)

**Sơ đồ:**

- Flyweight: Lớp này chứa trạng thái bên trong (intrinsic) không đổi.
- ConcreteFlyweight: Lớp con của Flyweight, cài đặt trạng thái bên trong.
- UnsharedConcreteFlyweight: Không phải lúc nào cũng cần thiết, dùng cho trạng thái bên ngoài (extrinsic) có thể thay đổi.
- FlyweightFactory: Tạo và quản lý các đối tượng Flyweight, đảm bảo rằng flyweights được chia sẻ một cách hiệu quả.
- Client: Sử dụng các đối tượng Flyweight thông qua FlyweightFactory.

## Ví dụ áp dụng Flyweight Pattern

Flyweight Pattern giúp tối ưu hóa bộ nhớ và hiệu suất trong các ứng dụng nhúng, nơi mà tài nguyên phần cứng hạn chế. Dưới đây là ví dụ sử dụng Flyweight Pattern để quản lý một hệ thống giám sát với nhiều cảm biến cùng loại (như cảm biến nhiệt độ hoặc áp suất) kết nối qua I2C.

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Flyweight: Đối tượng SensorType chứa thông tin chung
typedef struct {
    char type[20];
    char unit[10];
    int precision;
} SensorType;

// Flyweight Factory: Quản lý và tái sử dụng các SensorType
typedef struct {
    SensorType *sensorTypes[10];
    int count;
} SensorFactory;

SensorFactory factory = {.count = 0};

SensorType *getSensorType(const char *type, const char *unit, int precision) {
    for (int i = 0; i < factory.count; i++) {
        if (strcmp(factory.sensorTypes[i]->type, type) == 0) {
            return factory.sensorTypes[i];
        }
    }
    SensorType *newSensorType = (SensorType *)malloc(sizeof(SensorType));
    strcpy(newSensorType->type, type);
    strcpy(newSensorType->unit, unit);
    newSensorType->precision = precision;
    factory.sensorTypes[factory.count++] = newSensorType;
    return newSensorType;
}

// Client: Đối tượng Sensor với thuộc tính riêng
typedef struct {
    int id;
    int address;
    SensorType *sensorType;
} Sensor;

Sensor *createSensor(int id, int address, SensorType *sensorType) {
    Sensor *sensor = (Sensor *)malloc(sizeof(Sensor));
    sensor->id = id;
    sensor->address = address;
    sensor->sensorType = sensorType;
    return sensor;
}

void displaySensor(Sensor *sensor) {
    printf("Sensor ID: %d, Address: 0x%02X, Type: %s, Unit: %s, Precision: %d bits\n",
           sensor->id, sensor->address, sensor->sensorType->type,
           sensor->sensorType->unit, sensor->sensorType->precision);
}

// Main: Sử dụng Flyweight
int main() {
    SensorType *tempSensorType = getSensorType("Temperature", "Celsius", 12);
    SensorType *pressureSensorType = getSensorType("Pressure", "Pascal", 16);

    Sensor *sensor1 = createSensor(1, 0x48, tempSensorType);
    Sensor *sensor2 = createSensor(2, 0x49, tempSensorType);
    Sensor *sensor3 = createSensor(3, 0x50, pressureSensorType);

    displaySensor(sensor1);
    displaySensor(sensor2);
    displaySensor(sensor3);

    // Cleanup
    free(sensor1);
    free(sensor2);
    free(sensor3);
    for (int i = 0; i < factory.count; i++) {
        free(factory.sensorTypes[i]);
    }

    return 0;
}
```