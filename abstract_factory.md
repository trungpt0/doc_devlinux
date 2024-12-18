# Abstract Factory

## Giới thiệu

Abstract Factory là một Design Pattern thuộc nhóm Creational Pattern. Khác với Factory Method là một Pattern giúp tạo ra các đối tượng của một lớp. Tuy nhiên, trong nhiều trường hợp, các đối tượng có quan hệ với nhau và được nhóm thành các họ. Lúc này, chúng ta cần Abstract Factory. Abstract Factory cung cấp một interface để tạo ra các họ đối tượng liên quan với nhau một cách linh hoạt.

## Đặt vấn đề

Giả sử bạn đang phát triển một hệ thống điều khiển đa nền tảng sử dụng các vi điều khiển như STM32 và ESP32. Hệ thống này cần quản lý GPIO và UART để tương tác với các cảm biến và module ngoại vi. Tuy nhiên, mỗi loại vi điều khiển có cách triển khai khác nhau như:

* STM32 sử dụng thư viện HAL.
* ESP32 sử dụng thư viện ESP-IDF.

Khi thay đổi phần cứng chúng ta cần một cách để dễ dàng tạo ra và thay đổi mà không ảnh hưởng đến code hiện tại.

## Giải quyết

Để giải quyết vấn đề này, Abstract Factory được sử dụng như sau:

* Tạo interface trừu tượng định nghĩa các phương thức cơ bản cho GPIO và UART.
* Tạo Concrete Factory cho từng vi điều khiển, mỗi factory chịu trách nhiệm triển khai các phương thức phù hợp với phần cứng cụ thể.
* Client code sử dụng Abstract Factory để làm việc với GPIO và UART mà không cần biết cụ thể đang sử dụng vi điều khiển nào.

## Cấu trúc

![alt text](image/image4.png)

* PlatformFactory (Abstract Factory): Định nghĩa các phương thức trừu tượng createGPIO() và createUART() để tạo các đối tượng GPIO và UART.
* STM32_Factory và ESP32_Factory (Concrete Factory): Cụ thể hóa PlatformFactoryInterface để tạo ra các đối tượng GPIO và UART tương ứng cho từng nền tảng, STM32_Factory tạo ra STM32_GPIO và STM32_UART và ESP32_Factory tạo ra ESP32_GPIO và ESP32_UART.
* STM32_GPIO và ESP32_GPIO (Concrete Product): Cụ thể hóa các chức năng GPIO như init(pin, mode), write(pin, value), và read(pin) cho từng nền tảng.
* STM32_UART và ESP32_UART (Concrete Product): Cụ thể hóa các chức năng UART như init(baudrate), write(data), và read(buffer, length) cho từng nền tảng.

## Cách triển khai

### 1. Định nghĩa Abstract Factory (PlatformFactory)

```c
typedef struct {
    GPIOInterface* (*createGPIO)();
    UARTInterface* (*createUART)();
} PlatformFactoryInterface;
```

### 2. Định nghĩa Concrete Factory (STM32_Factory và ESP32_Factory) từ PlatformFactory

STM32_Factory:

```c
GPIOInterface* STM32_createGPIO() {
    return &STM32_GPIO;
}

UARTInterface* STM32_createUART() {
    return &STM32_UART;
}

PlatformFactoryInterface STM32_Factory = {
    .createGPIO = STM32_GPIO,
    .createUART = STM32_UART
};
```

ESP32_Factory:

```c
GPIOInterface* ESP32_createGPIO() {
    return &STM32_GPIO;
}

UARTInterface* ESP32_createUART() {
    return &ESP32_UART;
}

PlatformFactoryInterface ESP32_Factory = {
    .createGPIO = ESP32_GPIO,
    .createUART = ESP32_UART
};
```

### Định nghĩa Concrete Product

STM32_GPIO:

```c
void init(int pin, int mode) {
    wiringPiSetup();
    pinMode(pin, mode);
}

void write(int pin, int value) {
    digitalWrite(pin, value);
}

int read(int pin) {
    return digitalRead(pin);
}

GPIOInterface STM32_GPIO = {
    .init = STM32_GPIO_Init,
    .write = STM32_GPIO_Write,
    .read = STM32_GPIO_Read
};
```

STM32_UART:

```c
int uart_fd;

void STM32_UART_Init(int baudrate) {
    uart_fd = serialOpen("/dev/serial0", baudrate);
    if (uart_fd < 0) {
        perror("UART Init Failed");
    }
}

void STM32_UART_Write(const char* data) {
    serialPuts(uart_fd, data);
}

void STM32_UART_Read(char* buffer, int length) {
    for (int i = 0; i < length; i++) {
        buffer[i] = serialGetchar(uart_fd);
    }
}

UARTInterface RaspberryPi_UART = {
    .init = STM32_UART_Init,
    .write = STM32_UART_Write,
    .read = STM32_UART_Read
};
```

ESP32_GPIO:

```c
void init(int pin, int mode) {
    wiringPiSetup();
    pinMode(pin, mode);
}

void write(int pin, int value) {
    digitalWrite(pin, value);
}

int read(int pin) {
    return digitalRead(pin);
}

GPIOInterface ESP32_GPIO = {
    .init = ESP32_GPIO_Init,
    .write = ESP32_GPIO_Write,
    .read = ESP32_GPIO_Read
};
```

ESP32_UART:

```c
int uart_fd;

void ESP32_UART_Init(int baudrate) {
    uart_fd = serialOpen("/dev/serial0", baudrate);
    if (uart_fd < 0) {
        perror("UART Init Failed");
    }
}

void ESP32_UART_Write(const char* data) {
    serialPuts(uart_fd, data);
}

void ESP32_UART_Read(char* buffer, int length) {
    for (int i = 0; i < length; i++) {
        buffer[i] = serialGetchar(uart_fd);
    }
}

UARTInterface RaspberryPi_UART = {
    .init = ESP32_UART_Init,
    .write = ESP32_UART_Write,
    .read = ESP32_UART_Read
};
```

### Ví dụ

```c
#include <stdio.h>

// Chọn factory dựa trên nền tảng
PlatformFactoryInterface* factory;

#if defined(STM32)
    factory = &STM32_Factory;
#elif defined(ESP32)
    factory = &ESP32_Factory;
#else
    #error "No platform defined!"
#endif

int main() {
    // Tạo đối tượng GPIO và UART
    GPIOInterface* gpio = factory->createGPIO();
    UARTInterface* uart = factory->createUART();

    // Sử dụng GPIO
    gpio->init(7, 1);  // Khởi tạo GPIO pin 7 (OUTPUT)
    gpio->write(7, 1); // Ghi giá trị 1 vào GPIO pin 7

    // Sử dụng UART
    uart->init(9600);  // Khởi tạo UART với baudrate 9600
    uart->write("Hello!"); // Gửi dữ liệu qua UART

    return 0;
}
```