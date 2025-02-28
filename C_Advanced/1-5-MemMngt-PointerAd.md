# Memory Management and Pointer Advanced

## Section 1: Build Process Overview

### Preprocessing (Tiền xử lý)

- Mở rộng macro: Xử lý các #define.
- Bao gồm file: Thay thế #include bằng nội dung thực tế của file.
- Dịch có điều kiện: Xử lý #if, #ifdef, #else, #endif.
- Output: File preprocessed (.i) – mã nguồn đã xử lý tất cả macro & #include.

### Compilation (Biên dịch)

- Chuyển C code → Assembly code (.s).
- Kiểm tra cú pháp, tối ưu hóa code.
- Output: Object file (.o hoặc .obj) – mã máy cấp thấp của từng file .c.

### Assembly (Dịch Assembly)

- Chuyển Assembly (.s) → Mã máy (.o).
- Sử dụng Assembler để dịch từng file riêng lẻ.
- Output: Object file (.o hoặc .obj) – chứa mã máy chưa liên kết.

### Linking (Liên kết)

- Kết hợp các object files (.o) và thư viện để tạo chương trình hoàn chỉnh.
- Liên kết tĩnh (Static linking): Nhúng thư viện trực tiếp vào file chạy.
- Liên kết động (Dynamic linking): Chỉ trỏ đến thư viện động (.so, .dll) trong runtime.
- Sắp xếp dữ liệu, mã lệnh vào vị trí nhớ thích hợp.
- Output: Executable file (a.out, program, myprogram.exe).

### Execution (Chạy chương trình)

- Hệ điều hành tải chương trình vào RAM.
- Cấp phát tài nguyên & khởi chạy chương trình.
- Output: Chương trình chạy trên hệ thống.

## Section 2: Memory Layout

### Text Segment (Code Segment)

Chứa:
- Mã lệnh chương trình (instructions).
- Bộ nhớ chỉ đọc (Read-Only), không thể thay đổi trong runtime.

### Data Segment (Dữ liệu tĩnh – Biến toàn cục & tĩnh)

Chia làm hai phần:
- Initialized Data Segment (Đã khởi tạo): Chứa biến toàn cục hoặc static có giá trị khởi tạo.
- Uninitialized Data Segment (BSS - Block Started by Symbol): Chứa biến toàn cục hoặc static chưa khởi tạo (mặc định = 0).

### Heap Segment (Vùng nhớ cấp phát động)

Chứa:
- Bộ nhớ cấp phát bằng malloc(), calloc(), realloc().
- Tăng từ dưới lên.

### Stack Segment (Ngăn xếp – Biến cục bộ & Gọi hàm đệ quy)

Chứa:
- Biến cục bộ (Local Variables).
- Con trỏ hàm trả về (Return Address).
- Các tham số truyền vào hàm.
- Giảm từ trên xuống.

### Registers (Thanh ghi CPU)

Chứa:
- Thanh ghi dùng để xử lý nhanh dữ liệu.
- Chứa con trỏ Instruction Pointer (IP) – trỏ đến lệnh tiếp theo cần thực thi.

## Section 3: Variable and Memory Location in C

### Variable Properties

- Storage Class – Phạm vi lưu trữ (auto, static, extern, register)
- Storage Area – Nằm trong .text, .data, .bss, heap, stack
- Default Initial Value – Giá trị mặc định (0, rác, hoặc do lập trình viên khởi tạo)
- Lifetime – Tồn tại trong bao lâu (toàn cục, trong phạm vi hàm, v.v.)
- Scope – Phạm vi truy cập (local, global, file scope)

### Memory Sections for Variables

| Memory Section | Chứa gì? | Ví dụ biến |
|----------------|----------|------------|
| .text	| Mã lệnh chương trình (code) | void func() { ... } |
| .rodata | Dữ liệu chỉ đọc (const) | const char *msg = "Hello"; |
| .data | Biến toàn cục/tĩnh đã khởi tạo (!= 0) | int global = 10; |
| .bss | Biến toàn cục/tĩnh chưa khởi tạo (= 0) | static int x; (= 0 mặc định) |
| COMMON | Biến chưa khởi tạo (một số compiler hợp nhất vào .bss) | int commonVar; |
| Stack | Biến cục bộ (Local Variables) | int local_var; (trong hàm) |
| Heap | Cấp phát động (malloc/free) | int *ptr = malloc(4); |

## Section 4:

## Section 5: Pointer Variable

### Tổng quan về con trỏ trong C

Con trỏ (pointer) là một biến lưu địa chỉ của một biến khác trong bộ nhớ.

**Cú Pháp:**

```c
int *ptr;  // Một con trỏ trỏ đến một biến kiểu int
```

**Các Toán Tử Liên Quan Đến Con Trỏ:**

| Toán tử | Chức năng | Ví dụ |
|-|-|-|
| & (Address-of) | Lấy địa chỉ của một biến | ptr = &var; |
| * (Dereferencing) | Truy xuất giá trị tại địa chỉ của con trỏ | value = *ptr; |

**Con Trỏ Có Kích Thước Bao Nhiêu?**
- Kích thước của một con trỏ phụ thuộc vào kiến trúc và nền tảng hệ thống, không phụ thuộc vào kiểu dữ liệu mà nó trỏ đến.
- Tất cả các con trỏ trên một hệ thống thường có cùng kích thước vì chúng chỉ lưu địa chỉ bộ nhớ.

**Kích Thước Con Trỏ Trên Các Hệ Thống Khác Nhau:**
- Hệ thống 32-bit: Con trỏ thường có kích thước 4 byte (32 bit) vì không gian địa chỉ tối đa là 2³² (4GB).
- Hệ thống 64-bit: Con trỏ thường có kích thước 8 byte (64 bit) vì Vì không gian địa chỉ tối đa là 2⁶⁴ (16 exabyte).

### Các Loại Con Trỏ Nâng Cao Trong C

#### Con Trỏ Trỏ Đến Con Trỏ (Pointer to Pointer)

Là một con trỏ lưu địa chỉ của một con trỏ khác.

```c
int a = 10;
int *ptr = &a;  // Con trỏ ptr trỏ đến a
int **ptr2 = &ptr;  // Con trỏ ptr2 trỏ đến ptr
```

#### Con Trỏ Trỏ Đến Mảng (Pointer to an Array)

Một con trỏ trỏ đến mảng, cụ thể là phần tử đầu tiên của mảng.

```c
int arr[5] = {1, 2, 3, 4, 5};
int (*arr_ptr)[5] = &arr;  // arr_ptr trỏ đến mảng gồm 5 số nguyên
```

#### Con Trỏ Hàm (Pointer to a Function)

Một con trỏ lưu địa chỉ của một hàm.

```c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int main() {
    int (*func_ptr)(int, int) = &add;  // Con trỏ hàm trỏ đến hàm add
    printf("Sum: %d\n", func_ptr(3, 4));  // Gọi hàm thông qua con trỏ
    return 0;
}
```

### Cấp Phát Bộ Nhớ Động Trong C Với Con Trỏ

#### malloc() – Cấp phát bộ nhớ động

Cấp phát một vùng nhớ có kích thước cố định nhưng không khởi tạo giá trị.

Cú pháp:

```c
ptr = (cast_type*)malloc(byte_size);
```

Ví dụ:
```c
int *ptr = (int*)malloc(5 * sizeof(int));  // Cấp phát bộ nhớ cho 5 số nguyên
```

#### calloc() – Cấp phát và khởi tạo về 0

Cấp phát bộ nhớ giống malloc(), nhưng khởi tạo tất cả giá trị về 0.

Cú pháp:

```c
ptr = (cast_type*)calloc(n, element_size);
```

Ví dụ:
```c
int *ptr = (int*)calloc(5, sizeof(int));  // Cấp phát bộ nhớ cho 5 số nguyên, tất cả đều là 0
```

#### realloc() – Thay đổi kích thước bộ nhớ

Thay đổi kích thước vùng nhớ đã cấp phát trước đó mà không mất dữ liệu cũ.

Cú pháp:

```c
ptr = (cast_type*)realloc(ptr, new_size);
```

Ví dụ:
```c
ptr = (int*)realloc(ptr, 10 * sizeof(int));  // Mở rộng bộ nhớ lên 10 số nguyên
```

#### free() – Giải phóng bộ nhớ

Trả lại bộ nhớ đã cấp phát để tránh rò rỉ bộ nhớ (memory leak).

Cú pháp:

```c
free(ptr);
```

Ví dụ:
```c
free(ptr);  // Giải phóng bộ nhớ đã cấp phát
ptr = NULL;  // Tránh con trỏ trỏ đến vùng nhớ không hợp lệ
```

### Toán Tử Trên Con Trỏ Trong C (Pointer Arithmetic)

#### Tăng/Giảm Con Trỏ (Increment/Decrement)

Dùng để duyệt mảng hoặc truy cập địa chỉ bộ nhớ kế tiếp.

Cú pháp:

```c
ptr++;  // Dịch con trỏ đến phần tử kế tiếp
ptr--;  // Dịch con trỏ đến phần tử trước đó
```

Ví dụ:
```c
int arr[] = {10, 20, 30, 40};
int *ptr = arr;  

printf("%d\n", *ptr);  // In 10
ptr++;  
printf("%d\n", *ptr);  // In 20
ptr--;  
printf("%d\n", *ptr);  // In 10
```

#### Phép Toán Trên Con Trỏ

Phép cộng, trừ con trỏ giúp truy xuất dữ liệu nhanh hơn.

Cú pháp:

```c
ptr + n;  // Dịch con trỏ lên n phần tử
ptr - n;  // Dịch con trỏ xuống n phần tử
```

Ví dụ:
```c
int arr[] = {10, 20, 30, 40};
int *ptr = arr;  

printf("%d\n", *(ptr + 2));  // In 30 (vì ptr + 2 trỏ đến phần tử thứ 3)
```

#### So Sánh Con Trỏ (Pointer Comparisons)

So sánh hai con trỏ xem chúng có trỏ cùng một địa chỉ không.

```c
if (ptr1 == ptr2)  // Kiểm tra hai con trỏ có trỏ đến cùng địa chỉ không
```

### Mảng Đa Chiều và Con Trỏ trong C (Multidimensional Arrays & Pointers)

Con trỏ trỏ đến mảng 2D:

```c
int (*ptr)[4];  // Con trỏ trỏ đến mảng gồm 4 phần tử (1 hàng)
ptr = arr;      // Trỏ đến hàng đầu tiên của mảng
```

Con trỏ trỏ đến mảng 3D:

```c
int (*ptr3D)[3][4] = arr3D;  // Con trỏ trỏ đến một mảng 2D (3 hàng, 4 cột)
```

### Con Trỏ Trỏ Đến Struct (Pointer to Structure)

- Con trỏ trỏ đến struct giúp thao tác với các đối tượng struct thông qua địa chỉ bộ nhớ thay vì bản sao.
- Điều này hữu ích khi cấp phát động, truyền struct vào hàm, hoặc quản lý dữ liệu động.

```c
#include <stdio.h>

struct MyStruct {
    int id;
    float value;
};

int main() {
    struct MyStruct obj;  // Khai báo một biến struct
    struct MyStruct *ptr = &obj;  // Con trỏ trỏ đến struct

    ptr->id = 10;      // Tương đương với (*ptr).id = 10;
    ptr->value = 20.5; // Tương đương với (*ptr).value = 20.5;

    printf("ID: %d, Value: %.2f\n", ptr->id, ptr->value);

    return 0;
}
```

Cấp phát động:

```c
#include <stdio.h>
#include <stdlib.h>

struct MyStruct {
    int id;
    float value;
};

int main() {
    struct MyStruct *ptr = (struct MyStruct*)malloc(sizeof(struct MyStruct));  // Cấp phát động

    if (ptr == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }

    ptr->id = 42;
    ptr->value = 99.9;

    printf("ID: %d, Value: %.2f\n", ptr->id, ptr->value);

    free(ptr);  // Giải phóng bộ nhớ tránh memory leak
    return 0;
}
```

### Con Trỏ Hàm và Callback trong C

- Con trỏ hàm (Function Pointer) là một biến lưu địa chỉ của một hàm.
- Có thể sử dụng để gọi hàm một cách gián tiếp.
- Callback là kỹ thuật truyền con trỏ hàm vào một hàm khác để thực hiện hành động tùy chỉnh.

Khai báo và sử dụng con trỏ hàm:

```c
#include <stdio.h>

// Định nghĩa một hàm bình thường
void sayHello() {
    printf("Hello, World!\n");
}

int main() {
    void (*funcPtr)(); // Khai báo con trỏ hàm
    funcPtr = sayHello; // Gán địa chỉ hàm vào con trỏ

    funcPtr(); // Gọi hàm gián tiếp
    return 0;
}
```

Con trỏ hàm với tham số:

```c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int main() {
    int (*operation)(int, int); // Khai báo con trỏ hàm
    operation = add;  // Gán địa chỉ hàm add

    printf("Result: %d\n", operation(5, 10)); // Gọi hàm thông qua con trỏ
    return 0;
}
```

Callback Function (Truyền Con Trỏ Hàm vào Hàm Khác):

```c
#include <stdio.h>

// Hàm callback
void greetVietnamese() { printf("Xin chào!\n"); }
void greetEnglish() { printf("Hello!\n"); }

// Hàm nhận con trỏ hàm làm tham số
void executeGreeting(void (*greet)()) {
    greet();
}

int main() {
    executeGreeting(greetVietnamese); // Truyền hàm vào hàm khác
    executeGreeting(greetEnglish);
    return 0;
}
```

### Con trỏ nâng cao

Con Trỏ void (Void Pointer):
- void * là con trỏ có thể trỏ đến bất kỳ kiểu dữ liệu nào.
- Sử dụng trong các hàm tổng quát (generic), chẳng hạn như malloc(), qsort().

```c
#include <stdio.h>

void printValue(void *ptr, char type) {
    if (type == 'i') printf("Integer: %d\n", *(int*)ptr);
    else if (type == 'f') printf("Float: %.2f\n", *(float*)ptr);
}

int main() {
    int a = 10;
    float b = 5.5;
    void *ptr;

    ptr = &a;
    printValue(ptr, 'i');

    ptr = &b;
    printValue(ptr, 'f');

    return 0;
}
```

Con Trỏ Trỏ Đến Hằng Số (Pointer to Constant):
- Không thể thay đổi giá trị của biến mà nó trỏ đến.
- Được sử dụng khi cần bảo vệ dữ liệu không bị sửa đổi.

```c
#include <stdio.h>

int main() {
    const int value = 100;
    const int *ptr = &value; // Con trỏ trỏ đến hằng số

    printf("Value: %d\n", *ptr);

    // *ptr = 200; // ❌ Lỗi: không thể thay đổi giá trị của biến
    return 0;
}
```

Con Trỏ Hằng (Constant Pointer):
- Con trỏ không thể trỏ đến địa chỉ khác, nhưng có thể thay đổi giá trị tại địa chỉ đó.
- Dùng khi cần giữ cố định địa chỉ vùng nhớ.

```c
#include <stdio.h>

int main() {
    int a = 10, b = 20;
    int *const ptr = &a; // Con trỏ hằng, phải gán địa chỉ khi khai báo

    printf("Value: %d\n", *ptr);

    *ptr = 50; // ✅ Hợp lệ: có thể thay đổi giá trị tại địa chỉ ptr trỏ đến
    // ptr = &b; // ❌ Lỗi: không thể thay đổi địa chỉ

    printf("Updated Value: %d\n", *ptr);
    return 0;
}
```

### Lỗi Thường Gặp & Cách Phòng Tránh Khi Dùng Con Trỏ (Common Pitfalls & Best Practices)

Con Trỏ Treo (Dangling Pointers):
- Con trỏ trỏ đến vùng nhớ đã bị giải phóng, có thể gây crash hoặc hành vi không xác định.
- Cách phòng tránh: Sau khi free(), gán con trỏ về NULL.

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr = (int*)malloc(sizeof(int));
    *ptr = 10;

    free(ptr); // Giải phóng bộ nhớ
    ptr = NULL; // Ngăn lỗi con trỏ treo

    return 0;
}
```

Rò Rỉ Bộ Nhớ (Memory Leaks):
- Không giải phóng bộ nhớ cấp phát động, dẫn đến tiêu tốn tài nguyên.
- Dùng free() đúng cách, kiểm tra con trỏ trước khi sử dụng.

Khởi Tạo Con Trỏ (Pointer Initialization):
- Dùng con trỏ chưa khởi tạo dẫn đến lỗi segmentation fault.
- Luôn khởi tạo con trỏ ngay khi khai báo.

```c
int *ptr;
*ptr = 20; // ❌ Lỗi: ptr chưa trỏ đến vùng nhớ hợp lệ

int value = 20;
int *ptr = &value; // ✅ Trỏ đến biến hợp lệ trước khi sử dụng
```

Best Practices:
- Gán NULL cho con trỏ sau khi free().
- Giải phóng bộ nhớ đúng cách để tránh rò rỉ.
- Luôn khởi tạo con trỏ trước khi sử dụng.
- Sử dụng công cụ như valgrind để kiểm tra lỗi con trỏ.

### Ép Kiểu Con Trỏ (Pointer Casting) Trong C

Pointer casting cho phép chuyển đổi một con trỏ từ kiểu dữ liệu này sang kiểu dữ liệu khác. Dùng khi làm việc với void*, thao tác bộ nhớ, hoặc giao tiếp với phần cứng.

```c
#include <stdio.h>
void display(void *ptr) {
    int *int_ptr = (int*)ptr;  // Ép kiểu từ void* sang int*
    printf("Value: %d\n", *int_ptr);
}

int main() {
    int num = 42;
    display(&num);
    return 0;
}
```