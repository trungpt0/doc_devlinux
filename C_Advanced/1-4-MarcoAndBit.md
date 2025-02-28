# Macro and Bit Operations

Nội dung:
- C Preprocessor Overview
- Macro
- C Preprocessor Directives
- Bit Operations

## Section 1: C Preprocessor Overview

Tiền xử lý (Preprocessor) là một chương trình xử lý mã nguồn trước khi biên dịch. Nó sử dụng chỉ thị tiền xử lý (Preprocessor Directives) để hướng dẫn trình biên dịch thực hiện các thao tác trên mã nguồn.

Khi lập trình viên viết mã nguồn trong C/C++, mã nguồn này phải trải qua nhiều bước trước khi trở thành tệp thực thi. Quá trình này gồm các giai đoạn chính như sau:

1. Mã nguồn (.c hoặc .cpp)
- Lập trình viên viết mã và lưu vào tệp, ví dụ: program.c.

2. Tiền xử lý (.i - Tệp mã nguồn mở rộng)
- Bộ tiền xử lý (Preprocessor) xử lý các chỉ thị như #include, #define.
- Mở rộng macro, chèn nội dung của tệp tiêu đề (.h), loại bỏ chú thích.
- Tạo ra tệp mã nguồn mở rộng có phần mở rộng .i, ví dụ: program.i.

```bash
gcc -E program.c -o program.i   # Tiền xử lý
```

3. Biên dịch (.obj hoặc .o - Mã đối tượng)
- Trình biên dịch (Compiler) chuyển mã nguồn mở rộng (.i) thành mã máy nhưng chưa hoàn chỉnh.
- Kết quả là một tệp mã đối tượng (.obj trên Windows hoặc .o trên Linux/macOS).

```bash
gcc -c program.i -o program.o   # Biên dịch
```

4. Liên kết (.exe hoặc tệp thực thi)
- Trình liên kết (Linker) kết hợp tệp mã đối tượng (.obj hoặc .o) với các thư viện chuẩn (như stdio.h, math.h).
- Tạo ra tệp thực thi cuối cùng (.exe trên Windows, hoặc a.out trên Linux/macOS).

```bash
gcc program.o -o program.exe    # Liên kết
```

### Chức năng chính của tiền xử lý

#### Bao gồm tệp tiêu đề (Header Inclusion)

Sử dụng #include để chèn nội dung từ các tệp tiêu đề (.h).

```c
#include <stdio.h>  // Chèn thư viện nhập/xuất chuẩn
```

#### Mở rộng macro (Macro Expansion)

Định nghĩa macro để thay thế một chuỗi văn bản trong mã nguồn.

```c
#define PI 3.14
#define SQUARE(x) (x * x)  // Macro tính bình phương
```

#### Biên dịch có điều kiện (Conditional Compilation)

Dùng #ifdef, #ifndef, #if, #else, #endif để kiểm soát biên dịch.

```c
#ifdef DEBUG
printf("Debug mode enabled\n");
#endif
```

#### Chỉ thị chẩn đoán (Diagnostics Directives)

```c
#pragma warning(disable: 4996)  // Vô hiệu hóa cảnh báo
```

### Đặc điểm của chỉ thị tiền xử lý

- Tất cả đều bắt đầu bằng dấu #
- Không kết thúc bằng dấu ;
- Thực hiện trước khi quá trình biên dịch chính thức bắt đầu

## Section 2: Marco

### Định nghĩa

- Macro là một đoạn mã đã được đặt tên. Bất cứ khi nào tên được sử dụng, nó sẽ được thay thế bằng nội dung của macro
- Macro được định nghĩa bằng cách sử dụng lệnh tiền xử lý #define trong ngôn ngữ C.

Khi nào sử dụng macro ?
- Khi tạo hằng số biểu diễn số, chuỗi hoặc biểu thức.

Macro classification:
- Predefined macro
- User-defined macro

### Object-like Macros (Macro giống đối tượng)

- Được định nghĩa bằng #define mà không có tham số.
- Hoạt động như một hằng số hoặc một chuỗi văn bản thay thế.

```c
#include <stdio.h>

// Định nghĩa macro đơn giản
#define PI 3.14
#define MESSAGE "Hello, World!"

int main() {
    printf("Pi = %f\n", PI);
    printf("%s\n", MESSAGE);
    return 0;
}
```

### Multi-line Macros (Macro nhiều dòng)

- Dùng ký tự \ để tiếp tục dòng lệnh trên nhiều dòng.

```c
#include <stdio.h>

// Macro định nghĩa danh sách số
#define ELE 1, \
            2, \
            3

int main() {
    int arr[] = {ELE};
    for (int i = 0; i < 3; i++) {
        printf("%d ", arr[i]);
    }
    return 0;
}
```

### Function-like Macros (Macro giống hàm)

```c
#include <stdio.h>

// Macro tìm giá trị nhỏ nhất
#define min(a, b) (((a) < (b)) ? (a) : (b))

int main() {
    int x = 10, y = 20;
    printf("Min: %d\n", min(x, y));
    return 0;
}
```

### Un-define and Re-define Macro

- Dùng để thay đổi giá trị macro trong từng phần của chương trình.
- Tránh xung đột khi macro được định nghĩa từ tệp tiêu đề khác.

```c

#include <stdio.h>

// Định nghĩa macro TRUE
#define TRUE 0

// Kiểm tra nếu TRUE đã được định nghĩa
#ifdef TRUE
#undef TRUE  // Hủy định nghĩa cũ
#define TRUE 1  // Định nghĩa lại giá trị mới
#endif

int main() {
    printf("TRUE = %d\n", TRUE); // Kết quả: 1
    return 0;
}
```

## Section 3: C Preprocessor Directives

Chỉ thị #include được sử dụng để chèn tệp tiêu đề (header file) vào mã nguồn trước khi biên dịch. Nó giúp tái sử dụng mã và tổ chức chương trình hiệu quả hơn.

Cú pháp:

```c
#include <filename.h>   // Dùng cho thư viện hệ thống
#include "filename.h"   // Dùng cho tệp do người dùng định nghĩa
```

1. #include <filename> – Thư viện hệ thống
- Tìm tệp tiêu đề trong thư mục hệ thống tiêu chuẩn (ví dụ: /usr/include/ trên Linux).
- ùng cho thư viện chuẩn của C như stdio.h, math.h, stdlib.h,...

2. #include "filename" – Tệp tiêu đề do người dùng định nghĩa
- Trình biên dịch tìm tệp trong thư mục hiện tại trước, nếu không thấy thì tìm trong thư viện hệ thống.
- Dùng để tổ chức mã nguồn bằng cách tách các phần thành tệp .h riêng.

### Biên dịch có điều kiện trong C (Conditional Compilation)

Biên dịch có điều kiện cho phép chèn hoặc loại bỏ mã nguồn tùy theo điều kiện cụ thể, giúp chương trình linh hoạt hơn khi chạy trên các nền tảng khác nhau hoặc trong các chế độ debug/test.

#### #if - Kiểm tra điều kiện
- Nếu biểu thức trong #if là true, khối mã sẽ được biên dịch.
- Nếu biểu thức là false, chương trình sẽ kiểm tra #elif, #else hoặc bỏ qua đoạn mã đó.

```c
#include <stdio.h>

#define VERSION 2

#if VERSION == 1
    #define MESSAGE "Version 1"
#elif VERSION == 2
    #define MESSAGE "Version 2"
#else
    #define MESSAGE "Unknown Version"
#endif

int main() {
    printf("%s\n", MESSAGE);  // In ra: Version 2
    return 0;
}
```

#### #ifdef - Kiểm tra nếu macro đã được định nghĩa

- Nếu macro đã được định nghĩa, khối mã sẽ được biên dịch.

```c
#include <stdio.h>

#define DEBUG  // Định nghĩa macro DEBUG

#ifdef DEBUG
    #define LOG printf("Debugging Mode Enabled\n");
#endif

int main() {
    LOG  // In ra "Debugging Mode Enabled"
    return 0;
}
```

#### #ifndef - Kiểm tra nếu macro chưa được định nghĩa

- Nếu macro chưa được định nghĩa, khối mã sẽ được biên dịch.

```c
#include <stdio.h>

// Không định nghĩa DEBUG
#ifndef DEBUG
    #define LOG printf("Debugging Mode Disabled\n");
#endif

int main() {
    LOG  // In ra "Debugging Mode Disabled"
    return 0;
}
```

#### #else - Xử lý khi điều kiện trước đó sai

- Khi #if, #ifdef, hoặc #ifndef sai, #else sẽ được thực thi.

```c
#include <stdio.h>

#define MODE 0

#if MODE == 1
    #define MESSAGE "Mode 1 Activated"
#else
    #define MESSAGE "Default Mode"
#endif

int main() {
    printf("%s\n", MESSAGE);  // In ra: Default Mode
    return 0;
}
```

#### #elif - Kiểm tra điều kiện khác nếu #if sai

- Cho phép kiểm tra nhiều điều kiện liên tiếp.

```c
#include <stdio.h>

#define LEVEL 2

#if LEVEL == 1
    #define MESSAGE "Beginner Mode"
#elif LEVEL == 2
    #define MESSAGE "Intermediate Mode"
#elif LEVEL == 3
    #define MESSAGE "Advanced Mode"
#else
    #define MESSAGE "Unknown Mode"
#endif

int main() {
    printf("%s\n", MESSAGE);  // In ra: Intermediate Mode
    return 0;
}
```

#### #endif - Kết thúc khối biên dịch có điều kiện

- Mọi #if, #ifdef, #ifndef, #elif, #else phải có #endif để kết thúc.

#### #define và #undef - Định nghĩa và hủy macro

- #define giúp khai báo macro, còn #undef giúp xóa macro đã định nghĩa.

```c
#include <stdio.h>

#define FEATURE 1

#ifdef FEATURE
    #undef FEATURE  // Hủy macro FEATURE
#endif

#ifndef FEATURE
    #define MESSAGE "Feature Disabled"
#endif

int main() {
    printf("%s\n", MESSAGE);  // In ra: Feature Disabled
    return 0;
}
```

### Chỉ thị #error và #warning trong C Preprocessor

#### #error - Báo lỗi nghiêm trọng và dừng biên dịch

- #error sẽ khiến trình biên dịch dừng ngay lập tức và hiển thị thông báo lỗi tùy chỉnh.
- Dùng để phát hiện lỗi cấu hình, tương thích nền tảng, hoặc giá trị macro không hợp lệ.

```c
#ifdef __vax__
    #error "Won't work on VAX system. See comments at get_last_object."
#endif
```

#### #warning - Báo cảnh báo nhưng vẫn tiếp tục biên dịch

- #warning sẽ chỉ hiển thị cảnh báo, nhưng chương trình vẫn tiếp tục biên dịch.
- Dùng để thông báo thư viện lỗi thời, cấu hình không khuyến nghị, hoặc tính năng deprecated.

```c
#ifdef _WIN32
    #warning "This program may not run correctly on Windows."
#endif
```

| Chỉ thị | Mô tả | Ảnh hưởng |
|---------|-------|-----------|
| #error | Báo lỗi nghiêm trọng, ngăn biên dịch tiếp tục | Dừng biên dịch ngay lập tức|
| #warning | Cảnh báo nhưng không ngăn quá trình biên dịch | Tiếp tục biên dịch nhưng có cảnh báo |

Ứng dụng:
- Kiểm tra nền tảng hỗ trợ (Windows, Linux, ARM, x86).
- Chặn cấu hình sai (buffer size, version).
- Báo thư viện lỗi thời (old_header.h).
- Cảnh báo tính năng không khuyến nghị (deprecated API).

### Toán tử ba ngôi ?: trong C

Cú pháp:

```c
condition ? expression_2 : expression_3;
```

Ứng dụng trong macro:

```c
#define min(X, Y) ((X) < (Y) ? (X) : (Y))

int main() {
    int x = min(5, 8); // x = (5 < 8) ? 5 : 8 ==> x = 5
    int y = min(15, 10); // y = (15 < 10) ? 15 : 10 ==> y = 10

    printf("x = %d, y = %d\n", x, y); // Output: x = 5, y = 10
    return 0;
}
```

### Toán tử tiền xử lý (Preprocessor Operators) trong C

#### Toán tử xâu hóa (Stringizing Operator #)

- Dùng để chuyển tham số của macro thành chuỗi.

Cú pháp:

```c
#define STRINGIFY(x) #x
```

Ví dụ:

```c
#include <stdio.h>

#define STRINGIFY(x) #x

int main() {
    printf("%s\n", STRINGIFY(Hello, World!));  // Output: Hello, World!
    return 0;
}
```

#### Toán tử ghép token (Token Pasting Operator ##)

- Ghép hai token lại thành một token hợp lệ.

Cú pháp:

```c
#define CONCAT(a, b) a##b
```

Ví dụ:

```c
#include <stdio.h>

#define CONCAT(a, b) a##b

int main() {
    int xy = 100;
    printf("%d\n", CONCAT(x, y)); // Output: 100 (biến xy được gọi)
    return 0;
}
```

## Section 4: BIT OPERATIONS

### Bật (Set) một bit

- Đặt bit tại vị trí position thành 1.

```c
number |= (1 << position);
```

### Tắt (Clear) một bit

- Đặt bit tại vị trí position thành 0.

```c
number &= ~(1 << position);
```

### Đọc (Read) một bit

- Kiểm tra giá trị của bit tại position (0 hoặc 1).

```c
bit = (number >> position) & 1;
```

### Đảo (Toggle) một bit

- Chuyển 1 → 0 hoặc 0 → 1.

```c
number ^= (1 << position);
```

### Gán bit với giá trị cụ thể

- Đặt bit tại vị trí position thành value (0 hoặc 1).

```c
number = (number & ~(1 << position)) | (value << position);
```