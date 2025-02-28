# DATA STRUCTURES AND ALGORITHMS

## Section 1: Data structures

- Cấu trúc dữ liệu là một bộ lưu trữ được sử dụng để xử lý, truy xuất, lưu trữ dữ liệu và tổ chức dữ liệu. Đây là một cách sắp xếp dữ liệu trên máy tính để có thể truy cập và cập nhật dữ liệu một cách hiệu quả (Lists, arrays, stacks, queues or trees...).

- Cấu trúc dữ liệu tuyến tính : Cấu trúc dữ liệu trong đó các phần tử dữ liệu được sắp xếp tuần tự hoặc tuyến tính, trong đó mỗi phần tử được gắn với các phần tử liền kề trước và sau nó, được gọi là cấu trúc dữ liệu tuyến tính. Ví dụ: Mảng, Ngăn xếp, Hàng đợi, Danh sách liên kết, v.v.

- Cấu trúc dữ liệu phi tuyến tính: Các cấu trúc dữ liệu mà các phần tử dữ liệu không được đặt tuần tự hoặc tuyến tính được gọi là cấu trúc dữ liệu phi tuyến tính. Trong cấu trúc dữ liệu phi tuyến tính, chúng ta không thể duyệt tất cả các phần tử chỉ trong một lần chạy. Ví dụ: Cây và Đồ thị.

- Cấu trúc dữ liệu tĩnh: Cấu trúc dữ liệu tĩnh có kích thước bộ nhớ cố định. Dễ dàng truy cập các phần tử trong cấu trúc dữ liệu tĩnh. Ví dụ : mảng.

- Cấu trúc dữ liệu động: Trong cấu trúc dữ liệu động, kích thước không cố định. Nó có thể được cập nhật ngẫu nhiên trong thời gian chạy, điều này có thể được coi là hiệu quả liên quan đến độ phức tạp của bộ nhớ (không gian) của mã. Ví dụ : Hàng đợi, Ngăn xếp, v.v.

### Array

- Mảng là một cấu trúc dữ liệu tuyến tính lưu trữ các phần tử cùng kiểu dữ liệu trong bộ nhớ liên tục.
- Mỗi phần tử có một chỉ mục (index) duy nhất để truy xuất.

Ưu Điểm Của Mảng:
- Truy cập ngẫu nhiên (O(1))
- Tận dụng bộ nhớ đệm (Cache Friendly)
- Là nền tảng để xây dựng các cấu trúc dữ liệu khác

Nhược Điểm Của Mảng:
- Khó chèn/xóa giữa mảng
- Kích thước cố định (Với mảng tĩnh)

### String

- Chuỗi (String) là một dãy ký tự dùng để biểu diễn văn bản.
- Trong C, chuỗi là mảng ký tự kết thúc bởi ký tự NULL ('\0').

Đặc Điểm Của Chuỗi:
1. Tập Ký Tự Nhỏ:
- Chỉ gồm một số lượng ký tự hạn chế.
- Bảng ASCII có 256 ký tự.
- Chữ cái thường tiếng Anh chỉ có 26 ký tự ('a' → 'z').

2. Tối Ưu Hóa Khi Xử Lý:
- Việc sắp xếp, đếm tần suất ký tự nhanh hơn do tập ký tự nhỏ.
- Nhiều bài toán xử lý chuỗi có thể tận dụng đặc điểm này để tối ưu.

### Stack

Stack (Ngăn xếp) là một cấu trúc dữ liệu tuyến tính (Linear Data Structure) tuân theo nguyên tắc LIFO (Last In, First Out).

Các Phép Toán Cơ Bản Trên Stack:

1. Push (Thêm vào stack): ``` int push(stack s, void *item); ```
2. Pop (Lấy phần tử ra): ``` int push(stack s, void *item); ```
3. Kiểm tra stack rỗng: ``` int IsEmpty(stack s); ```
4. Lấy phần tử trên cùng nhưng không xóa: ``` void *Top(stack s); ```

Ứng Dụng Quan Trọng Của Stack:
✅ Duyệt cây (Tree Traversal) – Sử dụng DFS (Depth First Search).
✅ Chuyển đổi & tính toán biểu thức – Infix → Postfix, dùng thuật toán Shunting Yard.
✅ Backtracking (Quay lui) – Giải mê cung, Sudoku, DFS.
✅ Undo/Redo trong trình soạn thảo – Lưu trạng thái trước đó.
✅ Duyệt trình duyệt (Browser Navigation) – Back & Forward.

### Heap

### Linked List

### Tree

### Binary Tree

### Graph

## Section 2: Algorithms

### Sort

#### Insertion Sort

#### Bubble Sort

#### Selection Sort

#### Quick Sort

#### Sorting Comparision

### Searching

#### Sequential Search

#### Binary Search
