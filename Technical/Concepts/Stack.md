Đây là cách một kỹ sư backend hoặc người phỏng vấn Senior thường nhìn về **Stack**. Không chỉ biết API (`push`, `pop`) mà phải hiểu mối liên hệ giữa **Data Structure → Memory Layout → Big-O → CPU Cache → Trade-off → Ứng dụng thực tế**.

---

# 1. Stack là gì?

Stack là một **Linear Data Structure (cấu trúc dữ liệu tuyến tính)** hoạt động theo nguyên tắc:

> **LIFO (Last In First Out)**  
> Phần tử vào sau sẽ ra trước.

Ví dụ:

```
Push 1
Push 2
Push 3

Stack

Top
 ↓
+---+
| 3 |
+---+
| 2 |
+---+
| 1 |
+---+

Pop -> 3
Pop -> 2
Pop -> 1
```

Có thể hình dung như:

- chồng sách
    
- chồng đĩa
    
- undo trong Word
    

---

# 2. Nó lưu dữ liệu như thế nào trong bộ nhớ?

Đây là phần nhiều người chỉ học thuộc mà không hiểu.

## Có 2 cách hiện thực phổ biến

### Cách 1: Array-based Stack

```
int stack[5]

Memory

Address

100
101
102
103
104

+---+
|10 |
+---+
|20 |
+---+
|30 |
+---+
|   |
+---+
|   |
+---+

top = 2
```

Đặc điểm:

- dữ liệu nằm liên tiếp trong RAM (contiguous memory)
    
- chỉ cần lưu:
    

```
top
```

để biết phần tử cuối.

Push:

```
top++

stack[top] = value
```

Pop

```
value = stack[top]

top--
```

Không cần dịch chuyển dữ liệu.

---

### Cách 2: Linked List Stack

```
Top
 ↓

+------+     +------+     +------+
| 30 | o---->|20 | o----->|10|null|
+------+     +------+     +------+
```

Mỗi node gồm

```
value

next pointer
```

Push

```
newNode->next = top

top = newNode
```

Pop

```
top = top->next
```

---

## So sánh memory

Array

```
[10][20][30][40][50]
```

Liên tiếp.

Linked List

```
100
Node

500
Node

900
Node
```

Rải rác trong RAM.

Điều này ảnh hưởng trực tiếp đến CPU Cache.

---

# 3. Các Operation chính

## Push

```
Push(5)

Top
↓

3
2
1

↓

5
3
2
1
```

Thêm vào đỉnh.

---

## Pop

```
5
3
2

↓

3
2
```

Lấy phần tử trên cùng.

---

## Peek / Top

```
5
3
2

peek()

=> 5
```

Không xóa.

---

## isEmpty

```
top == -1
```

hoặc

```
top == null
```

---

## Size

```
size
```

---

# 4. Big-O

|Operation|Array|Linked List|
|---|---|---|
|Push|O(1)*|O(1)|
|Pop|O(1)|O(1)|
|Peek|O(1)|O(1)|
|isEmpty|O(1)|O(1)|
|Size|O(1)|O(1)|

(*) nếu Array phải resize thì có một lần O(n), nhưng **amortized O(1)**.

---

# 5. Tại sao Push/Pop là O(1)?

Ví dụ

```
1
2
3
```

Push

```
4
```

chỉ cần

```
top++
```

Không cần duyệt.

Không cần dịch chuyển.

Không cần sort.

Do đó

```
constant time
```

---

# 6. Ưu điểm

## Rất đơn giản

Code ngắn.

Logic rõ.

---

## Push/Pop cực nhanh

Chỉ thao tác ở đỉnh.

---

## Cache-friendly (Array)

Do dữ liệu liên tiếp.

CPU Prefetch hiệu quả.

---

## Ít thao tác cấp phát

Array chỉ resize khi đầy.

---

## Rất phù hợp với recursive algorithm

Ví dụ

DFS

Backtracking

Expression Evaluation

Undo

Call Stack

---

# 7. Nhược điểm

## Chỉ truy cập được Top

Không thể

```
stack[100]
```

về mặt ADT (Abstract Data Type), dù một số hiện thực bằng mảng có thể truy cập chỉ số.

---

## Không tìm kiếm hiệu quả

Muốn tìm

```
value = 10
```

phải pop từng phần tử.

O(n)

---

## Không insert giữa

Không delete giữa.

---

## Array có thể phải resize

```
capacity = 8

Push thêm

↓

capacity = 16

copy toàn bộ
```

---

## Linked List tốn pointer

Ví dụ

```
int = 4 byte

pointer = 8 byte
```

Node

```
4 + 8 = 12 byte
```

thậm chí có thêm padding.

---

# 8. Trade-off với Data Structure khác

## Stack vs Queue

Stack

```
LIFO
```

Queue

```
FIFO
```

Ứng dụng khác nhau.

---

## Stack vs Array

Array

```
Random Access

O(1)
```

Stack

```
chỉ Top
```

Đổi lại Push/Pop rõ ràng và an toàn theo mô hình LIFO.

---

## Stack vs Linked List

Linked List

```
Insert/Delete nhiều vị trí
```

Stack

```
Chỉ đầu
```

Đơn giản hơn.

---

## Stack vs Vector (Dynamic Array)

Vector

```
push_back()

pop_back()
```

Thực chất chính là nền tảng để hiện thực Stack.

---

## Stack vs Heap

Đây là hai khái niệm hoàn toàn khác.

Stack (Data Structure)

```
ADT
```

Heap

```
Priority Queue

hoặc

Heap Memory
```

Không liên quan.

---

# 9. Khi nào dùng trong hệ thống thực tế?

## Call Stack

```
main()

↓

login()

↓

validate()

↓

hashPassword()
```

CPU lưu

- return address
    
- local variable
    
- parameter
    

đều trong call stack.

---

## Undo / Redo

```
Type A

Type B

Type C
```

Undo

```
C

↓

B

↓

A
```

---

## Browser History (Back)

Back

```
Page3

↓

Page2

↓

Page1
```

(Thực tế thường dùng hai stack để hỗ trợ Back/Forward.)

---

## DFS

```
Graph

A

↓

B

↓

C
```

Stack giúp nhớ đường đi.

---

## Expression Evaluation

```
(1+2)*5
```

Compiler

Interpreter

Calculator

đều dùng Stack.

---

## Parsing

Compiler

Parser

AST

Recursive Descent

---

## Backtracking

Sudoku

Maze

N-Queens

---

# 10. Các ngôn ngữ hiện thực Stack như thế nào?

## Java

Không khuyến nghị dùng

```java
Stack<E>
```

vì là lớp cũ (legacy).

Hiện nay dùng

```java
Deque<Integer> stack = new ArrayDeque<>();
```

```
push()

pop()

peek()
```

`ArrayDeque` được hiện thực bằng **circular resizable array**, không phải linked list.

---

## C++

```cpp
std::stack<int>
```

Thực chất là adapter.

Mặc định dùng

```
deque
```

Có thể thay bằng

```
vector

list
```

---

## C#

```csharp
Stack<T>
```

Array-based.

---

## Python

Không có class Stack riêng.

Dùng

```python
list.append()

list.pop()
```

vì `list` là dynamic array.

Hoặc

```python
collections.deque
```

---

## JavaScript

```javascript
let stack = [];

stack.push();

stack.pop();
```

Array động.

---

## PHP

Có nhiều cách:

Array thông thường:

```php
$stack = [];

array_push($stack, 1);

array_pop($stack);
```

Hoặc dùng

```php
SplStack
```

thuộc **SPL (Standard PHP Library)**, tối ưu hơn cho mô hình stack.

---

## Go

```go
stack := []int{}

stack = append(stack, 1)

x := stack[len(stack)-1]

stack = stack[:len(stack)-1]
```

Slice động.

---

# 11. Mối liên hệ giữa Data Structure → Memory → Big-O → Hiệu năng → Trade-off

Đây là phần quan trọng nhất.

```
Stack
```

↓

**Quy tắc**

```
LIFO
```

↓

**Memory Layout**

Array

```
[1][2][3][4]
```

hoặc

Linked List

```
Node -> Node
```

↓

**Memory Access**

Chỉ truy cập Top.

↓

**Không cần dịch chuyển dữ liệu**

↓

```
Push

Pop
```

đều

```
O(1)
```

↓

Nếu Array

```
CPU Cache Hit cao
```

↓

Hiệu năng thực tế rất tốt.

↓

Trade-off

Không random access theo mô hình Stack.

Không insert giữa.

Không delete giữa.

Không search nhanh.

---

# 12. Các vấn đề cần lưu tâm khi sử dụng

- Tránh rò rỉ bộ nhớ vì phải lưu trữ quá nhiều thao tác, cần đặt giới hạn thao tác. vd: 50
- Khi sử dụng người dùng có thể liên tục undo | redo thao tác, từ đó gây ra chuyện phân nhánh lịch sử (lịch sử thao tác trước đó, lịch sử thao tác mới có tác động bởi undo | redo). Khi người dùng thực hiện undo | redo >= 2 lần, sau đó thực hiện thao tác mới. Thì cần refresh lại lịch sử để cập nhật lại toàn bộ lịch sử theo hướng mới nhất.
- Hệ thống nhiều người dùng cùng sửa trực tuyến: tham khảo thuật toán của gg thực hiện chuyện này để đồng bộ trạng thái
- Đây là một phương án hoặc coi nó như phương pháp để xử lý các vấn đề có trạng thái đó để tham khảo. Thực tế sẽ thực hiện thủ công, tự tùy chỉnh theo yêu cầu.

---
# Góc nhìn của một kỹ sư Backend

Khi phân tích một cấu trúc dữ liệu, nên luôn đi theo chuỗi tư duy sau:

```
Bài toán
    ↓
Quy tắc truy cập dữ liệu (LIFO/FIFO/Random/Priority)
    ↓
Cấu trúc dữ liệu (Stack, Queue, Hash Table, Tree...)
    ↓
Cách hiện thực (Array, Linked List, Deque...)
    ↓
Tổ chức bộ nhớ (liên tiếp hay phân tán)
    ↓
Độ phức tạp Big-O
    ↓
Đặc tính phần cứng (CPU cache, pointer chasing, allocation)
    ↓
Hiệu năng thực tế
    ↓
Trade-off và lý do lựa chọn trong hệ thống
```

Đó là cách phân tích mà các kỹ sư giàu kinh nghiệm và các buổi phỏng vấn backend ở mức Middle/Senior thường kỳ vọng, thay vì chỉ liệt kê các phép toán và độ phức tạp.