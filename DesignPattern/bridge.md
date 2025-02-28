# Bridge

## Khái niệm

Bridge Pattern là một mẫu thiết kế thuộc loại cấu trúc (Structural Pattern). Nó giúp tách rời abstraction (lớp trừu tượng) và implementation (lớp thực thi) ra khỏi nhau, giúp cả hai có thể thay đổi và phát triển một cách độc lập, không làm ảnh hưởng đến nhau.

## Mục đích

- Tách biệt abstraction khỏi implementation để cả hai có thể thay đổi mà không ảnh hưởng đến nhau.
- Khắc phục vấn đề kết hợp giữa lớp cha và lớp con, giúp giảm sự phức tạp trong thiết kế và giúp mở rộng mã nguồn một cách linh hoạt hơn.
- Cung cấp khả năng thay thế và tái sử dụng implementation mà không cần sửa đổi code của lớp trừu tượng.

## Ý tưởng chính của Pattern

Ý tưởng chính của Bridge Pattern là sử dụng "cầu nối" giữa abstraction và implementation. Thay vì một lớp trừu tượng kiểm soát và mở rộng từ một lớp cụ thể (lớp thực thi), mẫu thiết kế này đề xuất việc tạo ra một interface (cầu nối) giữa chúng. Khi cần mở rộng chức năng, bạn có thể thêm vào phía abstraction mà không ảnh hưởng đến phía implementation và ngược lại.

## Đặt vấn đề

Hãy tưởng tượng bạn có một lớp Vehicle với hai subclass là BicycleBike và MotorBike. Bây giờ, bạn muốn mở rộng tính năng bằng cách thêm màu sắc cho mỗi loại phương tiện, và bạn tạo ra hai thuộc tính là Red và Blue. Với cách tiếp cận này, bạn sẽ phải tạo ra tổng cộng bốn lớp con như BlueBicycleBike và RedMotorBike.

![alt text](image/image21.png)

Khi bạn muốn thêm một loại phương tiện hoặc một màu sắc mới, bạn sẽ cần tạo thêm nhiều lớp con, làm cho hệ thống trở nên phức tạp và khó kiểm soát.

## Giải pháp

Vấn đề ở đây là chúng ta đang cố gắng tích hợp quá nhiều tính năng vào một lớp trừu tượng, trong khi mỗi tính năng đều có tiềm năng phát triển theo hướng riêng biệt.

![alt text](image/image22.png)

Bridge Pattern giải quyết vấn đề này bằng cách tách biệt lớp trừu tượng (abstraction) và các đối tượng thực thi (implementation), sau đó kết nối chúng lại với nhau thông qua một "cầu" (bridge). Trong ví dụ này, một phương tiện sẽ có một thuộc tính màu sắc, tạo ra mối quan hệ "has-a" (có một) thay vì "is-a" (là một).

## Cấu trúc

![alt text](image/image23.png)

- The Abstraction (Abstraction): Lớp cơ sở cung cấp mức độ trừu tượng cho việc quản lý các công việc do các đối tượng cụ thể thực hiện (Implementation).
- The Implementation (Implementor): Giao diện định nghĩa các tác vụ cần thiết cho lớp trừu tượng. Đây thường là một giao diện xác định các tác vụ cần thiết cho lớp trừu tượng.
- Concrete Implementations (ConcreteImplementor): Các lớp này chứa logic cụ thể và thực hiện các tác vụ được định nghĩa bởi Implementor.
- Refined Abstractions (RefinedAbstraction): Các lớp con của Abstraction thực hiện và mở rộng các phương thức được xác định trong lớp Abstraction.

## Cách triển khai

### Bước 1: Xác định Abstraction và Implementation

```cpp
// Abstraction
class Color {
public:
    virtual void applyColor() const = 0;
    virtual ~Color() = default;
};

class Shape {
protected:
    Color* color;

public:
    Shape(Color* color) : color(color) {}
    virtual void draw() const = 0;
    virtual ~Shape() = default;
};
```

### Bước 2: Mở rộng Abstraction

```cpp
// Refined Abstraction: Circle
class Circle : public Shape {
public:
    Circle(std::shared_ptr<Color> color) : Shape(color) {}
    
    void draw() const override {
        std::cout << "Draw Circle in ";
        color->applyColor();
    }
};

// Refined Abstraction: Square
class Square : public Shape {
public:
    Square(std::shared_ptr<Color> color) : Shape(color) {}
    
    void draw() const override {
        std::cout << "Draw Square in ";
        color->applyColor();
    }
};
```

### Bước 3: Cung cấp các Implementations cụ thể

```cpp
// Implementation: Example Colors
class Red : public Color {
public:
    void applyColor() const override {
        std::cout << "Red" << std::endl;
    }
};

class Blue : public Color {
public:
    void applyColor() const override {
        std::cout << "Blue" << std::endl;
    }
};
```

### Bước 4: Sử dụng Bridge Pattern

```cpp
// Main function for testing
int main() {
    auto red = std::make_shared<Red>();
    auto blue = std::make_shared<Blue>();

    std::shared_ptr<Shape> circle = std::make_shared<Circle>(red);
    std::shared_ptr<Shape> square = std::make_shared<Square>(blue);

    circle->draw(); // Output: Draw Circle in Red
    square->draw(); // Output: Draw Square in Blue

    return 0;
}
```