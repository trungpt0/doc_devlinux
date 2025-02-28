# Adapter

## Giới thiệu

Adapter Pattern là một mẫu thiết kế thuộc loại cấu trúc (Structural Patterns). Nó giúp kết nối giữa hai interface không tương thích sao cho chúng có thể làm việc cùng nhau mà không cần sửa đổi. Để thực hiện điều này, Adapter Pattern sử dụng một lớp trung gian (gọi là adapter) để chuyển đổi interface của lớp này thành một interface khác.

## Mục đích

- Giúp kết nối giữa các đối tượng hoặc hệ thống với nhau mà không cần phải sửa đổi chúng, dựa trên nguyên tắc "kết nối chứ không là để sửa đổi".
- Tích hợp hoặc kết nối các hệ thống có sẵn, thư viện, modules có giao diện không tương thích.
- Cung cấp một giải pháp linh hoạt để mở rộng và tái sử dụng mã nguồn.

## Đặt vấn đề

Trong quá trình phát triển phần mềm, chúng ta thường gặp phải tình huống mà hai hệ thống hoặc thư viện có sẵn không thể nối tiếp với nhau trực tiếp bởi vì chúng có những interface khác biệt.

![alt text](image/image12.png)

- Muốn tích hợp một thư viện cũ vào ứng dụng hiện tại nhưng interface không tương thích.
- Muốn kết nối một hệ thống legacy với hệ thống mới nhưng hai bên có interface khác biệt.
Lúc này, ta cần một lớp trung gian đóng vai trò nối kết, chuyển đổi interface giữa chúng.

## Giải pháp

Giải pháp đề xuất ở đây là sử dụng một "Adapter" - một lớp trung gian để "dịch" hoặc "chuyển đổi" interface từ hệ thống này sang hệ thống kia, giúp chúng có thể làm việc chung mà không gặp vấn đề.

## Cấu trúc

Các thành phần trong Adapter Pattern:

![alt text](image/image13.png)

- Target: interface mà client sử dụng.
- Adapter: lớp trung gian, triển khai interface Target và gọi tới Adaptee.
- Adaptee: lớp cần được adapt để phù hợp với interface Target.
- Client: tương tác với Target interface.

## Triển khai Adapter Pattern

### Định nghĩa interface cần phục vụ

Đầu tiên, bạn xác định interface mới mà ứng dụng hoặc client sẽ sử dụng. Đây là interface mà lớp adapter sẽ triển khai.

```cpp
// NewInterface.h
class NewInterface {
public:
    virtual void newMethod() = 0;
    virtual ~NewInterface() = default;
};
```

### Tạo lớp cũ

Lớp cũ là đối tượng bạn muốn tích hợp vào hệ thống hiện tại, có thể có những phương thức mà hệ thống mới không hỗ trợ.

```cpp
// OldClass.h
class OldClass {
public:
    void oldMethod() {
        std::cout << "Old Method called!" << std::endl;
    }
};
```

### Tạo Lớp Adapter

Lớp adapter này sẽ triển khai NewInterface và "đóng gói" phương thức newMethod() của NewInterface để sử dụng phương thức oldMethod() từ OldClass.

```cpp
// Adapter.h
#include "NewInterface.h"
#include "OldClass.h"

class Adapter : public NewInterface {
private:
    OldClass* oldObject; // Tham chiếu tới đối tượng cũ

public:
    // Constructor nhận đối tượng của OldClass
    Adapter(OldClass* oldObject) : oldObject(oldObject) {}

    // Implement phương thức newMethod
    void newMethod() override {
        oldObject->oldMethod(); // Gọi phương thức cũ
    }

    ~Adapter() {
        delete oldObject; // Xóa đối tượng cũ khi không còn sử dụng
    }
};
```

### Sử dụng lớp Adapter

Giờ đây, bạn có thể sử dụng lớp Adapter trong ứng dụng mà không cần phải biết về chi tiết của lớp cũ. Client sẽ chỉ làm việc với NewInterface.

```cpp
// Client.cpp
#include <iostream>
#include "Adapter.h"
#include "OldClass.h"

int main() {
    // Tạo đối tượng của lớp cũ và đóng gói nó trong lớp Adapter
    OldClass* oldObject = new OldClass();
    NewInterface* target = new Adapter(oldObject);

    // Sử dụng phương thức mới mà không cần biết chi tiết bên trong
    target->newMethod();

    // Giải phóng bộ nhớ
    delete target;

    return 0;
}
```