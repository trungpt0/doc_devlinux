# Array - Decision - Looping

Nội dung:
- Array trong C
- Decision trong C
- Looping trong C

## Section 1: Array trong C

### Mảng (Array) là gì?

Mảng là tập hợp các phần tử có cùng kiểu dữ liệu, mỗi phần tử được xác định bởi một chỉ mục (index) duy nhất.

#### Đặc điểm của mảng

- Index của mảng bắt đầu từ 0.
- Kích thước mảng được xác định khi khai báo.
- Mảng có nhiều chiều, phổ biến nhất là mảng 1 chiều và mảng 2 chiều.

#### Cách khai báo mảng

```c
int arr[11]; // Mảng chứa 11 phần tử, chỉ mục từ 0 đến 10
```

#### Lưu ý

- Truy xuất ngoài phạm vi mảng gây lỗi (buffer overflow).
- Mảng có thể được truyền vào hàm bằng con trỏ.
- Sử dụng sizeof(arr) / sizeof(arr[0]) để lấy số phần tử của mảng.

### Mảng Nhiều Chiều (Multidimensional Arrays) trong C

Mảng nhiều chiều là mảng có nhiều hơn một chỉ mục, giúp lưu trữ dữ liệu dạng bảng (ma trận). Mảng 2 chiều là dạng phổ biến nhất, có thể coi như một bảng có hàng (rows) và cột (columns).

#### Khai báo mảng 2 chiều

```c
int temp[4][3];  // Mảng có 4 hàng và 3 cột
```

#### Khởi tạo mảng 2 chiều

```c
int temp[4][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9},
    {10, 11, 12}
};
```

#### Truy xuất phần tử trong mảng 2 chiều

```c
#include <stdio.h>

int main() {
    int temp[4][3] = {
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9},
        {10, 11, 12}
    };

    // Duyệt mảng và in ra giá trị
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 3; j++) {
            printf("%d ", temp[i][j]);
        }
        printf("\n");
    }

    return 0;
}
```

#### Kết quả

```bash
1 2 3  
4 5 6  
7 8 9  
10 11 12  
```

## Section 2: Decision trong C

Câu lệnh điều kiện giúp thay đổi luồng chương trình bằng cách kiểm tra một điều kiện và thực hiện các hành động tương ứng.

### If - else

```c
if (condition) {
    // Thực hiện nếu điều kiện đúng (khác 0)
} else {
    // Thực hiện nếu điều kiện sai (bằng 0)
}
```

### Switch case

```c
switch (variable) {
    case value1:
        // Khối lệnh thực hiện nếu variable == value1
        break;
    case value2:
        // Khối lệnh thực hiện nếu variable == value2
        break;
    case value3:
        // Khối lệnh thực hiện nếu variable == value3
        break;
    default:
        // Khối lệnh thực hiện nếu không khớp với giá trị nào ở trên
}
```

### Looping trong C

Vòng lặp trong C giúp lặp lại một đoạn mã nhiều lần cho đến khi một điều kiện nào đó được thỏa mãn. Nó giúp giảm sự lặp lại trong code và làm chương trình hiệu quả hơn.

#### Vòng lặp for

```c
for (initialization; condition; increment/decrement) {
    // Code to execute
}
```

#### Vòng lặp while

```c
while (condition) {
    // Code to execute
}
```
#### Vòng lặp do while

```c
do {
    // Code to execute
} while (condition);
```