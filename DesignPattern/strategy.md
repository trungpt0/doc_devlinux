# Strategy

## Khái niệm

Strategy Design Pattern là một trong những mẫu thiết kế thuộc nhóm Behavioral Design Patterns. Nó được sử dụng để định nghĩa một họ thuật toán, đóng gói từng thuật toán thành các lớp riêng biệt, và cho phép chúng có thể hoán đổi cho nhau. Điều này giúp tăng cường tính mô-đun và tái sử dụng của mã, bởi vì nó tách rời việc triển khai của các thuật toán từ các lớp sử dụng chúng.

## Mục đích

Mục đích của Strategy Pattern là cung cấp một cách để định cấu hình một lớp với một trong nhiều hành vi, hoặc thay đổi hành vi tại thời điểm chạy. Điều này giúp loại bỏ các câu lệnh điều kiện trong mã và thay thế chúng bằng việc chọn đối tượng chiến lược phù hợp. Cách tiếp cận này giúp mã nguồn dễ dàng mở rộng và bảo trì hơn.

## Cấu trúc

![alt text](image/image36.png)

- Context là lớp môi trường, nó chứa một tham chiếu đến một đối tượng Strategy. Context có thể thay đổi chiến lược tại thời điểm chạy (runtime).
- Strategy là một interface hoặc lớp trừu tượng định nghĩa một nhóm các thuật toán liên quan hoặc hành vi cụ thể. Trong mô hình chiến lược, các thuật toán này đều được đóng gói và hoán đổi cho nhau một cách linh hoạt.
- ConcreteStrategyA, ConcreteStrategyB, và ConcreteStrategyC là các lớp triển khai cụ thể của interface Strategy. Mỗi một ConcreteStrategy triển khai một biến thể của một thuật toán hoặc một hành vi.

## Cách triển khai

### Strategy Interface

```java
public interface Strategy {
    void executeStrategy();
}
```

### Concrete Strategy Classes

```java
public class ConcreteStrategyA implements Strategy {
    @Override
    public void executeStrategy() {
        System.out.println("Executing Strategy A");
    }
}

public class ConcreteStrategyB implements Strategy {
    @Override
    public void executeStrategy() {
        System.out.println("Executing Strategy B");
    }
}

public class ConcreteStrategyC implements Strategy {
    @Override
    public void executeStrategy() {
        System.out.println("Executing Strategy C");
    }
}
```

### Context

```java
public class Context {
    private Strategy strategy;

    public Context(Strategy strategy) {
        this.strategy = strategy;
    }

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public void executeStrategy() {
        strategy.executeStrategy();
    }
}
```

### Sử dụng Pattern

```java
public class StrategyPatternDemo {
    public static void main(String[] args) {
        Context context = new Context(new ConcreteStrategyA());

        // The context is using ConcreteStrategyA.
        context.executeStrategy(); // Executing Strategy A

        // Change strategy to ConcreteStrategyB
        context.setStrategy(new ConcreteStrategyB());

        // Now the context is using ConcreteStrategyB.
        context.executeStrategy(); // Executing Strategy B
    }
}
```

## Ví dụ

Sau đây là ví dụ điều khiển LED trên hệ thống nhúng sử dụng Strategy Pattern.

```cpp
class LEDStrategy {
public:
    virtual ~LEDStrategy() = default;
    virtual void blink() const = 0; // Phương thức nhấp nháy LED
};

class FastBlinkStrategy : public LEDStrategy {
public:
    void blink() const override {
        std::cout << "Fast blinking LED: ON-OFF every 100ms" << std::endl;
    }
};

class SlowBlinkStrategy : public LEDStrategy {
public:
    void blink() const override {
        std::cout << "Slow blinking LED: ON-OFF every 500ms" << std::endl;
    }
};

class SingleBlinkStrategy : public LEDStrategy {
public:
    void blink() const override {
        std::cout << "Single blink: ON for 1s, then OFF" << std::endl;
    }
};

class LEDContext {
private:
    LEDStrategy* strategy;

public:
    LEDContext(LEDStrategy* initialStrategy) : strategy(initialStrategy) {}

    void setStrategy(LEDStrategy* newStrategy) {
        strategy = newStrategy;
    }

    void executeBlink() const {
        if (strategy) {
            strategy->blink();
        }
    }
};

int main() {
    // Khởi tạo các chiến lược
    FastBlinkStrategy fastBlink;
    SlowBlinkStrategy slowBlink;
    SingleBlinkStrategy singleBlink;

    // Tạo Context với chiến lược ban đầu
    LEDContext ledContext(&fastBlink);

    // Sử dụng chiến lược Fast Blink
    ledContext.executeBlink(); // Output: Fast blinking LED: ON-OFF every 100ms

    // Chuyển sang chiến lược Slow Blink
    ledContext.setStrategy(&slowBlink);
    ledContext.executeBlink(); // Output: Slow blinking LED: ON-OFF every 500ms

    // Chuyển sang chiến lược Single Blink
    ledContext.setStrategy(&singleBlink);
    ledContext.executeBlink(); // Output: Single blink: ON for 1s, then OFF

    return 0;
}
```