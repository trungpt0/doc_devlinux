# State

## Khái niệm

State Pattern là một mẫu thiết kế hành vi cho phép một đối tượng thay đổi hành vi của mình khi trạng thái nội bộ thay đổi. Mẫu này coi trạng thái là một đối tượng độc lập và đối tượng chính có thể thay đổi hành vi của mình bằng cách thay đổi trạng thái hiện tại của nó. Điều này được thực hiện mà không cần sửa đổi mã nguồn của đối tượng, giúp cho việc quản lý các thay đổi về trạng thái trở nên dễ dàng và linh hoạt.

State Pattern bao gồm đối tượng 'Context' (môi trường hoặc ngữ cảnh sử dụng) và một tập hợp các đối tượng 'State' (trạng thái). 'Context' giữ một tham chiếu tới một 'State' hiện tại và có thể thay đổi tham chiếu này để chuyển đổi giữa các trạng thái khác nhau. Các 'State' biết cách xử lý các yêu cầu từ 'Context', và mỗi 'State' cung cấp hành vi cụ thể phù hợp với trạng thái của 'Context'.

## Mục đích

Mục đích của State Pattern là giúp quản lý và cơ cấu lại mã nguồn liên quan đến các quyết định điều khiển dựa trên trạng thái, bằng cách tách biệt hành vi liên quan đến trạng thái cụ thể ra khỏi 'Context'. Điều này giúp giảm sự phức tạp và tăng tính mô-đun của mã nguồn, cũng như dễ dàng thêm mới hoặc sửa đổi các trạng thái mà không ảnh hưởng đến 'Context'.

## Cấu trúc

![alt text](image/image38.png)

- Context là lớp môi trường chứa một thể hiện của các trạng thái khác nhau (State).
- State là lớp trừu tượng hoặc interface định nghĩa phương thức handle() mà mỗi trạng thái cụ thể (ConcreteStateA, ConcreteStateB) sẽ triển khai.
- ConcreteStateA và ConcreteStateB là các lớp cụ thể triển khai các hành vi khác nhau tương ứng với từng trạng thái của Context.

## Cách triển khai

### State Interface

```cpp
class State {
public:
    virtual ~State() {}
    virtual void handleRequest() = 0;  // Phương thức mà các ConcreteState sẽ thực thi
};
```

### Concrete State Classes

```cpp
class ConcreteStateA : public State {
public:
    void handleRequest() override {
        std::cout << "Handling request by ConcreteStateA" << std::endl;
    }
};

class ConcreteStateB : public State {
public:
    void handleRequest() override {
        std::cout << "Handling request by ConcreteStateB" << std::endl;
    }
};
```

### Context

```cpp
class Context {
private:
    State* state;  // Trạng thái hiện tại

public:
    Context(State* initialState) : state(initialState) {}

    void setState(State* newState) {
        state = newState;
    }

    void request() {
        state->handleRequest();  // Gọi phương thức xử lý theo trạng thái hiện tại
    }
};
```

### Main

```cpp
int main() {
    // Tạo các trạng thái
    ConcreteStateA stateA;
    ConcreteStateB stateB;

    // Tạo context với trạng thái ban đầu là ConcreteStateA
    Context context(&stateA);

    // Context đang ở ConcreteStateA
    context.request();  // Output: Handling request by ConcreteStateA

    // Chuyển trạng thái sang ConcreteStateB
    context.setState(&stateB);

    // Context đang ở ConcreteStateB
    context.request();  // Output: Handling request by ConcreteStateB

    return 0;
}
```

## Ví dụ

Để minh họa việc sử dụng State Pattern trong một hệ thống nhúng (embedded system), giả sử chúng ta đang phát triển một bộ điều khiển cho một thiết bị máy in. Máy in có thể ở các trạng thái khác nhau như "Idle", "Printing", và "Out of Paper". Chúng ta sẽ sử dụng State Pattern để thay đổi hành vi của máy in khi trạng thái thay đổi.

Trong ví dụ này:

State sẽ là interface định nghĩa các hành động mà máy in có thể thực hiện.
ConcreteState là các trạng thái cụ thể mà máy in có thể ở, như "Idle", "Printing", và "Out of Paper".
Context là lớp quản lý trạng thái của máy in và điều khiển các hành động của máy in dựa trên trạng thái hiện tại.

```cpp
class PrinterState {
public:
    virtual ~PrinterState() {}
    virtual void handleRequest() = 0;
};

class IdleState : public PrinterState {
public:
    void handleRequest() override {
        std::cout << "Printer is idle. Waiting for a print job." << std::endl;
    }
};

class PrintingState : public PrinterState {
public:
    void handleRequest() override {
        std::cout << "Printer is printing the document." << std::endl;
    }
};

class OutOfPaperState : public PrinterState {
public:
    void handleRequest() override {
        std::cout << "Printer is out of paper. Please load paper." << std::endl;
    }
};

class Printer {
private:
    PrinterState* state; // Trạng thái hiện tại của máy in

public:
    Printer(PrinterState* initialState) : state(initialState) {}

    void setState(PrinterState* newState) {
        state = newState; // Thay đổi trạng thái của máy in
    }

    void request() {
        state->handleRequest();  // Gọi hành động tương ứng với trạng thái hiện tại
    }
};

int main() {
    // Tạo các trạng thái
    IdleState idle;
    PrintingState printing;
    OutOfPaperState outOfPaper;

    // Tạo máy in với trạng thái ban đầu là Idle
    Printer printer(&idle);

    // Máy in đang ở chế độ Idle
    printer.request();  // Output: Printer is idle. Waiting for a print job.

    // Chuyển trạng thái sang Printing
    printer.setState(&printing);
    printer.request();  // Output: Printer is printing the document.

    // Chuyển trạng thái sang OutOfPaper
    printer.setState(&outOfPaper);
    printer.request();  // Output: Printer is out of paper. Please load paper.

    return 0;
}
```