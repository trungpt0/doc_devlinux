# Function

## Section 1: Hàm là gì?

- Một hàm là một đoạn chương trình độc lập thực hiện một nhiệm vụ cụ thể, được xác định rõ
- Các hàm thường được sử dụng như các từ viết tắt cho một loạt các hướng dẫn sẽ được thực hiện nhiều lần
- Các hàm dễ viết và dễ hiểu
- Việc gỡ lỗi chương trình trở nên dễ dàng hơn vì cấu trúc của chương trình rõ ràng hơn, do dạng mô-đun của nó
- Các chương trình chứa các hàm cũng dễ bảo trì hơn, vì các sửa đổi, nếu cần, sẽ được giới hạn ở một số hàm nhất định trong chương trình

Cú pháp:

```c
type_specifier function_name(arguments)
{
    body of the function
}
```

- type_specifier chỉ định kiểu dữ liệu của giá trị mà hàm sẽ trả về.
- Một tên hàm hợp lệ cần được gán để xác định hàm.
- Các đối số xuất hiện trong dấu ngoặc đơn cũng được gọi là tham số hình thức.

## Section 2: Tham số của hàm

Hàm xử lý dữ liệu thông qua các đối số. Dữ liệu được truyền từ hàm main() đến hàm squarer()

```c
#include <stdio.h>

// Function to calculate square
squarer(int x) {
    int j;
    j = x * x;
    return j;
}

int main() {
    for (int i = 1; i <= 10; i++) {
        printf("Square of %d is %d\n", i, squarer(i));
    }
    return 0;
}
```

Trả về từ hàm:

```c
squarer(int x) {
    int j;
    j = x * x;
    return j;
}
```

- Nó chuyển quyền điều khiển từ hàm trở lại chương trình gọi ngay lập tức.
- Bất cứ thứ gì nằm trong dấu ngoặc đơn theo sau lệnh return, đều được trả về dưới dạng giá trị cho chương trình gọi

### Invoking a function

- Dấu chấm phẩy được sử dụng ở cuối câu lệnh khi một hàm được gọi, nhưng không phải
sau định nghĩa hàm
- Dấu ngoặc đơn là bắt buộc sau tên hàm, bất kể hàm có đối số hay không
- Chỉ một giá trị có thể được trả về bởi một hàm
- Chương trình có thể có nhiều hơn một hàm
- Hàm gọi một hàm khác được gọi là hàm/thủ tục gọi
- Hàm được gọi được gọi là hàm/thủ tục được gọi

### Function Prototype

Function prototype là một khai báo của hàm mà chưa có phần thân. Nó giúp trình biên dịch biết được tên, kiểu trả về và danh sách tham số của hàm trước khi thực sự sử dụng nó.

```c
int foo(int arg1, char arg2);  // function prototype
```

Function prototype vs. function definition:
- Prototype: Khai báo hàm mà không có phần thân
- Definition: Định nghĩa hàm có phần thân

```c
// Prototype (khai báo)
int foo(int arg1, char arg2);

// Definition (định nghĩa)
int foo(int arg1, char arg2) {
    return arg1 + arg2;
}
```

### Variable

Biến cục bộ (Local Variables):
- Được khai báo bên trong một hàm
- Được tạo khi nhập vào một khối và bị hủy khi thoát khỏi khối

Tham số chính thức (Formal Parameters):
- Được khai báo trong định nghĩa của hàm dưới dạng tham số
- Hoạt động giống như bất kỳ biến cục bộ nào bên trong một hàm

Biến toàn cục (Global Variables)
- Được khai báo bên ngoài tất cả các hàm
- Giữ giá trị trong suốt quá trình thực thi chương trình

### Function scope

- Phạm vi toàn cục có nghĩa là chức năng có thể nhìn thấy hoặc sử dụng trong nhiều đơn vị dịch (objects).
- Phạm vi nội bộ có nghĩa là chức năng chỉ được sử dụng
trong một đơn vị dịch (objects).

### Passing Argument by Value

Khi truyền tham số theo giá trị (pass by value), một bản sao của giá trị được truyền vào hàm, nghĩa là bất kỳ thay đổi nào đối với tham số trong hàm không ảnh hưởng đến giá trị ban đầu bên ngoài hàm. 

```c
#include <stdio.h>

// Hàm truyền tham số theo giá trị
void modifyValue(int x) {
    x = x + 10;  // Chỉ thay đổi giá trị của biến x trong hàm
    printf("Inside function: x = %d\n", x);
}

int main() {
    int num = 5;
    printf("Before function call: num = %d\n", num);
    modifyValue(num);  // Truyền giá trị của num vào hàm
    printf("After function call: num = %d\n", num);  // Không thay đổi
    return 0;
}
```

### Passing Argument by Reference

- Pass by reference có nghĩa là truyền tham chiếu đến biến thay vì truyền một bản sao.
- Khi tham số được truyền bằng tham chiếu, bất kỳ thay đổi nào trong hàm cũng ảnh hưởng trực tiếp đến biến gốc.
- Trong C, tham chiếu được thực hiện bằng con trỏ (*).
- Trong C++, có thể sử dụng cả con trỏ (*) hoặc tham chiếu (&).

```c
#include <stdio.h>

// Hàm thay đổi giá trị của biến thông qua con trỏ
void modifyValue(int *x) {
    *x = *x + 10;  // Thay đổi giá trị gốc
}

int main() {
    int num = 5;
    printf("Before function call: num = %d\n", num);
    modifyValue(&num);  // Truyền địa chỉ của num
    printf("After function call: num = %d\n", num);  // num đã thay đổi
    return 0;
}
```

## Section 3: Function Keyword

Trong ngôn ngữ lập trình C, một hàm tĩnh (static function) là một hàm có phạm vi sử dụng giới
hạn trong file mà nó được khai báo. Điều này có nghĩa là hàm tĩnh không thể được truy cập hoặc
sử dụng bởi các hàm hoặc mã trong các file khác, ngay cả khi chúng là một phần của cùng một
chương trình.

### Static

- Một hàm tĩnh trong C là một hàm có phạm vi giới hạn trong tệp đối tượng của nó. Điều này có
nghĩa là hàm tĩnh chỉ hiển thị trong tệp đối tượng của nó
- Các hàm tĩnh là các hàm chỉ hiển thị với các hàm khác trong cùng một tệp (chính xác hơn là
cùng một đơn vị dịch).

```c
static void staticFunc(void) {
    printf("Inside the static function staticFunc() ");
}
```

### Inline

- Hàm nội tuyến (inline function) trong ngôn ngữ lập trình C/C++ là một hàm được khai báo với từ khóa inline. Khi bạn sử dụng từ khóa này, bạn đang đề nghị trình biên dịch chèn toàn bộ mã của hàm vào vị trí mà hàm được gọi, thay vì thực hiện một lời gọi hàm thông thường.
- Điều này có thể giúp giảm thời gian thực thi chương trình, đặc biệt là đối với các hàm nhỏ và được gọi thường xuyên, vì nó loại bỏ chi phí của việc gọi hàm (như lưu trữ địa chỉ trả về, sao chép tham số, và chuyển điều khiển).

```c
inline int foo()
{
    return 2;
}
```

| Inline | Normal function |
|--------|-----------------|
| Tốc độ: Từ khóa Inline khuyến khích trình biên dịch xây dựng một hàm để code nơi nó được sử dụng (bên trong hàm khác). | Tốc độ: Hàm được đặt ở các phân đoạn bộ nhớ khác nhau, cần phải nhảy để di chuyển từ vị trí gọi hiện tại đến vị trí hàm |
| Kích thước: sẽ được sao chép cho mỗi lần gọi | Kích thước: Chỉ có một vị trí, duy nhất và nhỏ gọn. |

## Section 4: Function-like macro

Function-like macros trong C là các macro được định nghĩa để hoạt động giống như các hàm. Chúng được khai báo bằng cách sử dụng chỉ thị #define, với một cặp dấu ngoặc đơn ngay sau tên macro. Điều này cho phép bạn định nghĩa các hàm ngắn gọn mà không cần phải viết mã hàm đầy đủ

Syntax:

```c
#define Macro_Function_name(param0, param1,...) (expression)
```

Example:

```c
#include <stdio.h>

#define SQUARE(x) ((x) * (x))

int main() {
    int num = 5;
    printf("Binh phuong cua %d la %d\n", num, SQUARE(num));
    return 0;
}
```

## Section 5: Recursion

Recursion trong C là một kỹ thuật mà một hàm tự gọi lại chính nó để giải quyết một vấn đề. Điều này giúp đơn giản hóa các vấn đề phức tạp bằng cách chia chúng thành các vấn đề nhỏ hơn, dễ quản lý hơn.

Ưu điểm: Đệ quy có thể làm cho mã nguồn trở nên thanh lịch và dễ hiểu hơn, đặc biệt là đối với các vấn đề như duyệt cây và tính giai thừa.
Nhược điểm: Các giải pháp đệ quy có thể kém hiệu quả hơn và sử dụng nhiều bộ nhớ hơn so với các giải pháp lặp do chi phí của nhiều lần gọi hàm.

## Section 6: Variable Argument List

Trong ngôn ngữ lập trình C, bạn có thể sử dụng danh sách đối số biến (variable argument list) để tạo các hàm có thể nhận một số lượng đối số không xác định. Điều này được thực hiện bằng cách sử dụng thư viện stdarg.h và các macro liên quan như va_list, va_start, va_arg, và va_end.

