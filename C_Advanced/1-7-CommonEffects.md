# COMMON DEFECTS IN C​

## Section 1: Memory Management Error

1. Memory Leak: quên giải phóng cấp phát động khi cấp phát bộ nhớ
2. Dangling Pointers: là một con trỏ vẫn tồn tại nhưng không hợp lệ hoặc không trỏ đến vùng nhớ cần thiết. Điều này thường xảy ra khi vùng nhớ mà con trỏ trỏ tới đã bị giải phóng hoặc trỏ tới vùng nhớ không đáng tin cậy. Điều này có thể dẫn đến các hậu quả khó lường và thậm chí là crash chương trình nếu con trỏ đó được sử dụng một cách không an toàn.là một con trỏ vẫn tồn tại nhưng không hợp lệ hoặc không trỏ đến vùng nhớ cần thiết. Điều này thường xảy ra khi vùng nhớ mà con trỏ trỏ tới đã bị giải phóng hoặc trỏ tới vùng nhớ không đáng tin cậy. Điều này có thể dẫn đến các hậu quả khó lường và thậm chí là crash chương trình nếu con trỏ đó được sử dụng một cách không an toàn.
3. Buffer Overflows: xảy ra khi chương trình ghi dữ liệu vượt quá giới hạn của một mảng hoặc bộ đệm đã cấp phát.
4. Uninitialized Pointers: các con trỏ chưa được gán địa chỉ hợp lệ trước khi sử dụng.
5. Double Free: là lỗi xảy ra khi gọi free() nhiều lần trên cùng một con trỏ. Điều này có thể dẫn đến undefined behavior, gây lỗi segmentation fault, tham chiếu đến bộ nhớ không hợp lệ, hoặc làm hỏng bộ nhớ heap.

## Section 2: Pointer Misuse

1. Dereferencing Null or Invalid Pointers (Giải tham chiếu con trỏ NULL hoặc không hợp lệ) là lỗi xảy ra khi truy cập một con trỏ trỏ đến vùng nhớ không hợp lệ.
2. Pointer Arithmetic Errors Leading to Unintended Memory Access: Lỗi này xảy ra khi sử dụng toán tử số học trên con trỏ (pointer arithmetic) một cách sai sót, khiến con trỏ trỏ đến vùng nhớ không hợp lệ, gây lỗi segmentation fault, memory corruption, hoặc undefined behavior.
3. Casting Pointers Incorrectly, Especially in Low-Level Operations: Lỗi xảy ra khi ép kiểu con trỏ không đúng, dẫn đến việc truy cập bộ nhớ sai cách, gây undefined behavior, memory corruption, hoặc crash chương trình.

## Section 3: Undefined Behavior

1. Division by Zero (Chia cho 0)
2. Signed Integer Overflow – Tràn số nguyên có dấu
3. Using Variables Without Initializing Them – Sử dụng biến chưa khởi tạo
4. Modifying a Variable Multiple Times Between Sequence Points – Thay đổi biến nhiều lần giữa các điểm trình tự

## Section 4: Syntax Errors

Lỗi cú pháp là những lỗi xảy ra do vi phạm quy tắc của ngôn ngữ lập trình. Chúng thường được phát hiện trong quá trình biên dịch và khiến chương trình không thể chạy được.

### Off-by-One Errors (Lỗi lệch 1 đơn vị)

Lỗi Off-by-One xảy ra khi chỉ số vòng lặp hoặc điều kiện biên không được viết chính xác, dẫn đến lặp quá nhiều hoặc quá ít lần.

```c
#include <stdio.h>

int main() {
    int arr[5];
    for (int i = 0; i <= 5; i++) {  // ❌ Lỗi: truy cập arr[5] (ngoài phạm vi)
        arr[i] = i;
    }
    return 0;
}
```

### Incorrect Precedence (Lỗi độ ưu tiên toán tử)

Lỗi này xảy ra khi không hiểu rõ độ ưu tiên của toán tử, dẫn đến kết quả sai.

```c
#include <stdio.h>

int main() {
    int a = 1, b = 0, c = 1;
    if (a & b || c)  // ❌ Hiểu nhầm thành (a & (b || c))
        printf("True\n");
    return 0;
}
```

### Missing Semicolons or Braces (Thiếu dấu chấm phẩy hoặc dấu ngoặc)

```c
#include <stdio.h>

int main() {
    int x = 10
    printf("%d\n", x);  // ❌ Lỗi vì thiếu dấu `;` sau `int x = 10`
    return 0;
}
```

## Section 5: Improper Use of Standard Library Functions

Lỗi này xảy ra khi lập trình viên sử dụng hàm thư viện chuẩn của C mà không kiểm tra lỗi hoặc sử dụng các hàm không an toàn, gây ra lỗi bộ nhớ hoặc bảo mật.

### Không kiểm tra giá trị trả về của hàm quan trọng

Các hàm như malloc(), fopen(), scanf() có thể thất bại, nhưng nếu không kiểm tra giá trị trả về, chương trình có thể gặp lỗi nghiêm trọng.

```c
#include <stdio.h>

int main() {
    FILE *file = fopen("test.txt", "r"); // ❌ Không kiểm tra lỗi
    // Nếu test.txt không tồn tại, code này sẽ gặp lỗi
    return 0;
}
```

### Sử dụng các hàm không an toàn (gets, strcpy, sprintf, …)

Một số hàm như gets(), strcpy(), sprintf() không có giới hạn kích thước, dễ gây tràn bộ đệm.

```c
#include <stdio.h>

int main() {
    char str[10];
    gets(str); // ❌ Không kiểm soát kích thước đầu vào
    printf("%s\n", str);
    return 0;
}
```

## Section 6: Concurrency Issues

Lập trình đa luồng mang lại hiệu suất cao nhưng cũng dễ gặp lỗi như race condition (điều kiện tranh chấp), deadlock (kẹt tài nguyên), và thread-local storage sai cách. Những lỗi này có thể dẫn đến kết quả không xác định, crash hoặc treo chương trình.

### Race Condition

Race condition xảy ra khi nhiều luồng (threads) cùng truy cập và thay đổi dữ liệu chung mà không có cơ chế đồng bộ hóa. Điều này dẫn đến dữ liệu bị ghi đè không mong muốn.

```c
#include <pthread.h>
#include <stdio.h>

int counter = 0;

void* increment(void* arg) {
    for (int i = 0; i < 1000; i++) {
        counter++;  // ❌ Truy cập dữ liệu chung không được bảo vệ
    }
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, increment, NULL);
    pthread_create(&t2, NULL, increment, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    printf("Counter: %d\n", counter); // Kết quả không chính xác
    return 0;
}
```

### Deadlock

Deadlock xảy ra khi hai hoặc nhiều luồng cùng giữ một khóa (lock/mutex) và đợi nhau mở khóa, dẫn đến chương trình bị treo.

```c
#include <pthread.h>
#include <stdio.h>

pthread_mutex_t lock1, lock2;

void* thread1(void* arg) {
    pthread_mutex_lock(&lock1);
    pthread_mutex_lock(&lock2);  // ❌ Chờ lock2 trong khi thread2 đang giữ
    printf("Thread 1 running\n");
    pthread_mutex_unlock(&lock2);
    pthread_mutex_unlock(&lock1);
    return NULL;
}

void* thread2(void* arg) {
    pthread_mutex_lock(&lock2);
    pthread_mutex_lock(&lock1);  // ❌ Chờ lock1 trong khi thread1 đang giữ
    printf("Thread 2 running\n");
    pthread_mutex_unlock(&lock1);
    pthread_mutex_unlock(&lock2);
    return NULL;
}

int main() {
    pthread_t t1, t2;
    pthread_mutex_init(&lock1, NULL);
    pthread_mutex_init(&lock2, NULL);
    pthread_create(&t1, NULL, thread1, NULL);
    pthread_create(&t2, NULL, thread2, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    pthread_mutex_destroy(&lock1);
    pthread_mutex_destroy(&lock2);
    return 0;
}
```

### Improper Use of Thread-Local Storage

Mỗi luồng có bộ nhớ riêng, nhưng nếu sử dụng sai có thể gây lỗi bộ nhớ chung bị ghi đè hoặc truy cập không hợp lệ.

```c
#include <pthread.h>
#include <stdio.h>

char* message;

void* thread_func(void* arg) {
    message = "Hello from thread";
    return NULL;
}

int main() {
    pthread_t thread;
    pthread_create(&thread, NULL, thread_func, NULL);
    pthread_join(thread, NULL);
    printf("%s\n", message);  // ❌ Có thể truy cập vào bộ nhớ đã bị ghi đè
    return 0;
}
```

## Section 7: Error Handling Omissions

Lập trình không chỉ là viết code chạy đúng, mà còn phải đảm bảo nó xử lý được các trường hợp lỗi để tránh crash hoặc hành vi không mong muốn. Dưới đây là các lỗi phổ biến và cách khắc phục.

### Không kiểm tra lỗi từ hàm hệ thống hoặc thư viện

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *ptr = malloc(10 * sizeof(int));  // ❌ Không kiểm tra malloc()
    ptr[0] = 42;  // Nếu malloc() thất bại, truy cập này sẽ gây segmentation fault
    free(ptr);
    return 0;
}
```

### Bỏ qua các trường hợp biên hoặc đầu vào không hợp lệ

Ví dụ lỗi: Không kiểm tra đầu vào của scanf()

```c
#include <stdio.h>

int main() {
    int num;
    printf("Enter a number: ");
    scanf("%d", &num);  // ❌ Không kiểm tra giá trị trả về
    printf("You entered: %d\n", num);
    return 0;
}
```

Cách sửa: Kiểm tra giá trị trả về của scanf()

```c
if (scanf("%d", &num) != 1) {
    fprintf(stderr, "Invalid input! Please enter a number.\n");
    return 1;
}
```

## Section 8: File I/O Errors

Làm việc với file trong C có thể gây ra nhiều lỗi nếu không kiểm tra kỹ giá trị trả về hoặc quản lý tài nguyên không đúng cách. Dưới đây là các lỗi phổ biến và cách khắc phục.

### Không kiểm tra giá trị trả về của thao tác file

Ví dụ lỗi: Không kiểm tra giá trị trả về của fopen():

```c
#include <stdio.h>

int main() {
    FILE *file = fopen("data.txt", "r");  // ❌ Không kiểm tra fopen()
    char buffer[100];
    fgets(buffer, sizeof(buffer), file);  // Nếu fopen() thất bại, fgets() sẽ gây lỗi
    fclose(file);
    return 0;
}
```

Cách sửa: Kiểm tra giá trị trả về của fopen()

```c
FILE *file = fopen("data.txt", "r");
if (!file) {
    perror("Error opening file");  // In thông báo lỗi
    return 1;
}
char buffer[100];
if (fgets(buffer, sizeof(buffer), file) == NULL) {
    printf("File is empty or read error occurred\n");
}
fclose(file);
```

### Xử lý không đúng con trỏ file hoặc descriptor

Ví dụ lỗi: Sử dụng con trỏ file sau khi đóng

```c
FILE *file = fopen("data.txt", "r");
fclose(file);
char buffer[100];
fgets(buffer, sizeof(buffer), file);  // ❌ Đọc file đã đóng
```

Cách sửa: Không sử dụng file sau khi đóng, nếu cần mở lại, hãy dùng fopen().

```c
file = fopen("data.txt", "r");  // Mở lại file nếu cần
```

### Rò rỉ tài nguyên khi quên đóng file

Ví dụ lỗi: Không gọi fclose() sau khi mở file

```c
void processFile() {
    FILE *file = fopen("data.txt", "r");  
    if (!file) return;  // ❌ Không đóng file nếu fopen() thành công
    // Xử lý file...
}
```

Cách sửa: Luôn gọi fclose() trước khi thoát khỏi hàm

```c
void processFile() {
    FILE *file = fopen("data.txt", "r");
    if (!file) return;
    
    // Xử lý file...
    
    fclose(file);  // ✅ Đóng file sau khi xử lý
}
```

## Section 9: #define Macros Errors

Sử dụng #define để tạo macro giúp tối ưu mã nguồn, nhưng nếu không cẩn thận có thể gây ra hành vi không mong muốn hoặc lỗi khó phát hiện. Dưới đây là một số lỗi phổ biến và cách sửa.

### Không đặt dấu ngoặc cho tham số trong function-like macro

```c
#define SQUARE(x) x * x  // ❌ Thiếu dấu ngoặc

int result = SQUARE(3 + 1); // Kết quả mong muốn: 16, nhưng thực tế là 3 + 1 * 3 + 1 = 7
```

Cách sửa: Thêm dấu ngoặc () để đảm bảo toán tử hoạt động đúng

### Thêm dấu ; vào macro

Ví dụ lỗi:

```c
#define PRINT_HELLO printf("Hello World!\n");  // ❌ Macro kết thúc bằng dấu `;`

int main() {
    if (1)
        PRINT_HELLO  // Lỗi: Dấu `;` thừa gây ra lỗi cú pháp
    else
        printf("Hi\n");
}
```