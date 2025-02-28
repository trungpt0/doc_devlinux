# Decorator 

## Khái niệm

Decorator Pattern là một mẫu thiết kế thuộc nhóm Structural Design Patterns. Pattern này cho phép chúng ta "trang trí" thêm hành vi cho đối tượng mà không cần thay đổi cấu trúc nội tại của chúng, hỗ trợ mở rộng chức năng mà vẫn tuân thủ nguyên tắc đóng mở (Open/Closed Principle).

Ý tưởng: Bằng cách sử dụng thành phần (composition), Decorator Pattern thêm "vỏ bọc" cho đối tượng cơ bản, cung cấp hành vi thêm vào và có thể thay đổi tại runtime.

## Đặt vấn đề

Trong hệ thống IoT, giả sử bạn có một lớp Notifier chuyên gửi tín hiệu cảnh báo khi hệ thống có vấn đề qua email. Khi người dùng muốn thêm tính năng thông báo qua SMS, Zalo hay Facebook thì việc tiếp tục tạo thêm và kế thừa từ lớp Notifier ban đầu dường như là một giải pháp đơn giản.

![alt text](image/image14.png)

Tuy nhiên, khi nhu cầu thông báo đa dạng và phức tạp hơn, việc quản lý số lượng lớn các lớp con trở nên khó khăn và không hiệu quả. Đặc biệt là khi người dùng cần kết hợp nhiều hình thức thông báo cùng một lúc, cấu trúc mã nguồn có thể trở nên cồng kềnh và khó bảo trì.

![alt text](image/image15.png)

Đây là lúc mà Decorator Pattern trở nên quan trọng và thiết thực. Pattern này cho phép chúng ta "trang trí" các đối tượng với các chức năng mới mà không cần phải thay đổi cấu trúc nội tại của chúng, mang lại sự linh hoạt và dễ dàng mở rộng mà không làm ảnh hưởng đến các thành phần khác trong hệ thống.

## Giải pháp

Để giải quyết vấn đề mở rộng chức năng một cách hiệu quả, Decorator Pattern cung cấp một giải pháp linh hoạt. Thay vì tạo ra một loạt các lớp con, mỗi lớp với một chức năng cụ thể, chúng ta có thể sử dụng mô hình "trang trí" này để bổ sung chức năng mới.

Xét về trường hợp thêm chức năng SMS, Decorator Pattern cho phép chúng ta "bọc" đối tượng Notifier ban đầu trong một lớp NotifierDecorator, sau đó thêm một lớp SMSDecorator bổ sung chức năng gửi SMS. SMSDecorator sẽ không thay thế lớp Notifier gốc mà là mở rộng chức năng của nó. Khi phương thức send() được gọi trên SMSDecorator, nó sẽ thực hiện cả hành động gửi email thông qua Notifier gốc cùng với việc gửi tin nhắn SMS mới thêm vào được.

![alt text](image/image16.png)

Mô hình này không chỉ đơn giản hóa quá trình quản lý mã nguồn bằng cách giảm thiểu số lượng lớp cần phải xử lý, mà còn cung cấp sự linh hoạt để dễ dàng thêm hoặc bớt các "vỏ bọc" mà không ảnh hưởng tới hệ thống hiện có.

## Cấu Trúc Decorator Pattern

![alt text](image/image17.png)

- **Component**: Đây là interface chung cho tất cả các đối tượng, cả cơ bản và trang trí. Nó quy định các phương thức chung cần có.
- **ConcreteComponent**: Đây là lớp triển khai interface Component. Nó định nghĩa một đối tượng cơ bản có thể có chức năng được "trang trí" bởi Decorators.
- **Decorator**: Lớp trung gian này giữ một tham chiếu đến một đối tượng Component và cung cấp giao diện tương tự như Component. Mục đích của nó là để kế thừa từ lớp Component và mở rộng chức năng của nó.
- **ConcreteDecorator**: Những lớp này thực hiện việc trang trí cụ thể. Mỗi ConcreteDecorator thêm các chức năng hoặc trách nhiệm mới cho Component mà nó trang trí.

Các thành phần này tương tác với nhau như sau: ConcreteComponent là đối tượng cơ bản được trang trí. Decorator chứa một tham chiếu đến Component và định nghĩa giao diện phù hợp với Component. ConcreteDecorator thực hiện các phương thức của Decorator và thêm chức năng mới. Khi một phương thức trong Decorator được gọi, nó chuyển tiếp yêu cầu đến đối tượng Component mà nó trang trí, có thể thực hiện thêm các hành động trước hoặc sau khi chuyển tiếp yêu cầu.

## Ví dụ

```cpp
class Notifier {
public:
    virtual void send(const std::string& message) = 0;
    virtual ~Notifier() = default;
};

class EmailNotifier : public Notifier {
public:
    void send(const std::string& message) override {
        std::cout << "Sending email: " << message << std::endl;
    }
};

class NotifierDecorator : public Notifier {
protected:
    Notifier* wrappedNotifier;

public:
    NotifierDecorator(Notifier* notifier) : wrappedNotifier(notifier) {}
    virtual void send(const std::string& message) override {
        wrappedNotifier->send(message);
    }
};

// Thêm chức năng gửi SMS vào thông báo
class SMSDecorator : public NotifierDecorator {
public:
    SMSDecorator(Notifier* notifier) : NotifierDecorator(notifier) {}

    void send(const std::string& message) override {
        NotifierDecorator::send(message); // Gửi email
        std::cout << "Sending SMS: " << message << std::endl; // Thêm chức năng gửi SMS
    }
};

// Thêm chức năng gửi thông báo qua Facebook
class FacebookDecorator : public NotifierDecorator {
public:
    FacebookDecorator(Notifier* notifier) : NotifierDecorator(notifier) {}

    void send(const std::string& message) override {
        NotifierDecorator::send(message); // Gửi thông báo trước đó
        std::cout << "Sending Facebook Notification: " << message << std::endl; // Thêm chức năng gửi Facebook
    }
};

int main() {
    // Tạo đối tượng cơ bản EmailNotifier
    Notifier* notifier = new EmailNotifier();

    // Thêm chức năng gửi SMS
    notifier = new SMSDecorator(notifier);

    // Thêm chức năng gửi thông báo qua Facebook
    notifier = new FacebookDecorator(notifier);

    // Gửi thông báo với tất cả các chức năng
    notifier->send("Hello, World!");

    // Dọn dẹp bộ nhớ
    delete notifier;

    return 0;
}
```