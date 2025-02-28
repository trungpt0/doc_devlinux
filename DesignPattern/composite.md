# Composite

## Khái niệm

Composite Pattern là một mẫu thiết kế trong Structural Design Patterns. Nó được sử dụng để tổ chức các đối tượng vào một cấu trúc cây để thể hiện mối quan hệ "phần-tổng thể" (part-whole). Mẫu thiết kế này tạo ra một hệ thống phân cấp cho phép người dùng xử lý các đối tượng đơn lẻ và tổ hợp của chúng một cách thống nhất.

## Mục đích

Mục đích chính của Composite Pattern là đơn giản hóa quá trình làm việc với các cấu trúc phức tạp bằng cách cho phép client tương tác với các đối tượng đơn lẻ và tổ hợp theo cùng một cách. Điều này giúp giảm thiểu sự phức tạp khi quản lý và tương tác với cấu trúc cây, làm cho mã nguồn dễ bảo trì và mở rộng hơn.

## Ý tưởng chính của Pattern

Ý tưởng cốt lõi của Composite Pattern nằm ở việc cung cấp một interface chung cho cả hai loại đối tượng: đơn lẻ và tổ hợp. Interface này cho phép client tương tác với mỗi đối tượng một cách riêng rẻ hoặc nhóm các đối tượng lại với nhau như một tổng thể thống nhất mà không cần quan tâm đến đặc điểm nội tại của chúng. Kết quả là, client có thể them, xóa hoặc thay đổi các đối tượng trong cấu trúc cây một cách linh hoạt mà không cần viết lại code hoặc hiểu biết sâu sắc về cấu trúc nội bộ.

## Đặt vấn đề 

Khi sử dụng Composite Pattern bạn phải chắc chắn rằng mô hình ứng dụng của bạn có biểu hiện bằng sơ đồ cây.

Ví dụ trong việc lưu trữ trong máy tính có hai dạng chính: Folder và File. Một Folder thì có thể chứa nhiều Folder và File. Có thể một trong Folder chỉ chứa File và trong File thì chứa nội dụng.

![alt text](image/image18.png)

Giờ giả sử ta cần tìm tất cả File trong một Folder. Thử cách tiếp cận thông thường là ta sẽ mở từng Folder con ra và đếm xem co bao nhiêu File vào Folder tiếp theo đếm tiếp. Nhưng trong lập trình nó không hề đơn giản như việc bạn chỉ cần chạy một dòng for. Bạn phải biết trước loại File và Folder mà sẽ duyệt và mực đồ lòng vào nhau. Tất cả điều đó làm cho cách tiếp cận này trở nên khó khăn hơn.

## Giải pháp

Chúng ta sẽ sử chung Composite Pattern để thực hiện công việc với Folder và File bằng cách tạo một interrface chung với một phương thức count.

![alt text](image/image19.png)

Đối với File thì chỉ trả về cộng một, Đối với Folder thì nó sẽ duyệt từng item trong Folder đó, bắt từng item đếm sau cùng tới lượt nó tổng hợp lại và trả về tổng số của Folder. Nếu một các item là Folder thì sao? Thì nó sẽ bắt Folder con đó đi đếm các thành item nằm trong Folder con và trả về tổng số.

## Cấu trúc

![alt text](image/image20.png)

- Component: interface chung, mô ta các phương thức chung của thành phần trong cây.
- Leaf: Đây là thành phần cơ bản của cây, nó không có các node con.
- Composite: lưu trữ tập hợp các Leaf và cài đặt các phương thức của Component.

## Cách triển khai

### Xây dựng Component

```cpp
class Component {
protected:
    std::string name;

public:
    Component(const std::string& name) : name(name) {}

    virtual void add(std::shared_ptr<Component> component) {
        throw std::logic_error("Add operation is not supported.");
    }

    virtual void remove(std::shared_ptr<Component> component) {
        throw std::logic_error("Remove operation is not supported.");
    }

    virtual void display(int depth) const = 0;
    
    virtual ~Component() = default;
};
```

### Xây dựng Leaf

```cpp
class Leaf : public Component {
public:
    Leaf(const std::string& name) : Component(name) {}

    void add(std::shared_ptr<Component> component) override {}
    void remove(std::shared_ptr<Component> component) override {}
    void display(int depth) const override {
        std::cout << std::string(depth, '-') << name << std::endl;
    }
};
```

### Xây dựng Composite

```cpp
class Composite : public Component {
private:
    std::vector<std::shared_ptr<Component>> children;

public:
    Composite(const std::string& name) : Component(name) {}

    void add(std::shared_ptr<Component> component) override {
        children.push_back(component);
    }

    void remove(std::shared_ptr<Component> component) override {
        children.erase(
            std::remove(children.begin(), children.end(), component), 
            children.end()
        );
    }

    void display(int depth) const override {
        std::cout << std::string(depth, '-') << name << std::endl;
        for (const auto& component : children) {
            component->display(depth + 2);
        }
    }
};
```

### Sử dụng Composite

```cpp
int main() {
    std::shared_ptr<Component> root = std::make_shared<Composite>("Root");

    std::shared_ptr<Component> leaf1 = std::make_shared<Leaf>("Leaf1");
    std::shared_ptr<Component> leaf2 = std::make_shared<Leaf>("Leaf2");
    std::shared_ptr<Component> childComposite = std::make_shared<Composite>("Child Composite");

    root->add(leaf1);
    root->add(leaf2);
    root->add(childComposite);

    childComposite->add(std::make_shared<Leaf>("Leaf3"));

    root->display(1);

    return 0;
}
```

## Ví dụ

Dưới đây là một ví dụ sử dụng Composite Pattern trong lĩnh vực Linux Embedded, cụ thể là quản lý hệ thống file nhúng (file system). Trong Linux, một hệ thống file có thể bao gồm cả thư mục (directories) và tệp (files), trong đó các thư mục có thể chứa tệp hoặc các thư mục con.

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <memory>
#include <algorithm>

// Giao diện chung cho File và Directory
class FileSystemComponent {
protected:
    std::string name;

public:
    FileSystemComponent(const std::string& name) : name(name) {}
    virtual ~FileSystemComponent() = default;

    virtual void add(std::shared_ptr<FileSystemComponent> component) {
        throw std::logic_error("Operation not supported.");
    }

    virtual void remove(std::shared_ptr<FileSystemComponent> component) {
        throw std::logic_error("Operation not supported.");
    }

    virtual void display(int depth) const = 0;
};

// Leaf: Đại diện cho File
class File : public FileSystemComponent {
public:
    File(const std::string& name) : FileSystemComponent(name) {}

    void display(int depth) const override {
        std::cout << std::string(depth, '-') << "File: " << name << std::endl;
    }
};

// Composite: Đại diện cho Directory
class Directory : public FileSystemComponent {
private:
    std::vector<std::shared_ptr<FileSystemComponent>> children;

public:
    Directory(const std::string& name) : FileSystemComponent(name) {}

    void add(std::shared_ptr<FileSystemComponent> component) override {
        children.push_back(component);
    }

    void remove(std::shared_ptr<FileSystemComponent> component) override {
        children.erase(std::remove(children.begin(), children.end(), component), children.end());
    }

    void display(int depth) const override {
        std::cout << std::string(depth, '-') << "Directory: " << name << std::endl;
        for (const auto& child : children) {
            child->display(depth + 2);
        }
    }
};

int main() {
    // Tạo thư mục gốc
    auto root = std::make_shared<Directory>("root");

    // Thêm các file vào thư mục gốc
    root->add(std::make_shared<File>("file1.txt"));
    root->add(std::make_shared<File>("file2.txt"));

    // Tạo thư mục con
    auto subDir = std::make_shared<Directory>("subdir");
    subDir->add(std::make_shared<File>("file3.txt"));
    subDir->add(std::make_shared<File>("file4.txt"));

    // Thêm thư mục con vào thư mục gốc
    root->add(subDir);

    // Hiển thị cấu trúc hệ thống file
    root->display(1);

    return 0;
}

```