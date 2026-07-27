Đây là cách tiếp cận mà một Software Engineer hoặc System Engineer thường phân tích một Data Structure, không chỉ biết cách dùng mà còn hiểu **tại sao nó nhanh/chậm**.

---

# 1. Tree là gì?

Tree (cây) là một **cấu trúc dữ liệu phân cấp (hierarchical data structure)**.

Nó gồm các **Node** được kết nối bằng các **Edge**.

Ví dụ

```
        A
      / | \
     B  C  D
    / \
   E   F
```

Có các khái niệm

- Root
    
- Parent
    
- Child
    
- Leaf
    
- Height
    
- Depth
    
- Subtree
    

Khác với Graph:

- Tree không có cycle
    
- Có đúng một đường đi giữa hai node
    
- Với n node sẽ có n−1 edge
    

---

# 2. Tree lưu dữ liệu như thế nào trong bộ nhớ?

Đây là phần quan trọng nhất.

Khác với Array.

Array

```
[10][20][30][40]
```

Memory

```
0x100
+----+
|10  |
+----+
|20  |
+----+
|30  |
+----+
|40  |
+----+
```

Liên tiếp (Contiguous Memory)

---

Tree thì không.

Ví dụ

```
      50
     /  \
   30    70
  / \   / \
20 40 60 80
```

Memory có thể là

```
0x500
Node(50)

left -> 0x820
right -> 0x1A0

0x820
Node(30)

left -> 0x330
right -> 0x910

0x1A0
Node(70)
...
```

Có nghĩa là

Các node nằm rải rác khắp Heap.

Chúng chỉ biết nhau thông qua Pointer.

```
Node
+------------------+
value = 50
left  = address
right = address
+------------------+
```

Đây gọi là

**Pointer-based structure**

---

Không có tính liên tục trong bộ nhớ.

CPU Cache kém hơn Array rất nhiều.

---

# 3. Node thường gồm những gì?

Binary Tree

```
class Node {

    int value;

    Node left;

    Node right;
}
```

AVL

```
value

left

right

height
```

Red Black Tree

```
value

left

right

parent

color
```

B-tree

```
keys[]

children[]
```

---

# 4. Các Operation chính

Gần như Tree nào cũng có

- Search
    
- Insert
    
- Delete
    
- Traversal
    
- Find Min
    
- Find Max
    
- Successor
    
- Predecessor
    

---

Traversal gồm

Preorder

```
Root
Left
Right
```

Inorder

```
Left
Root
Right
```

Postorder

```
Left
Right
Root
```

Level Order

```
BFS
```

---

# 5. Big-O

Điều này phụ thuộc loại Tree.

## Binary Search Tree

|Operation|Average|Worst|
|---|---|---|
|Search|O(log n)|O(n)|
|Insert|O(log n)|O(n)|
|Delete|O(log n)|O(n)|
|Traversal|O(n)|O(n)|

---

Nếu cây bị lệch

```
1
 \
 2
  \
   3
    \
     4
```

Nó trở thành Linked List.

Search

```
O(n)
```

---

## AVL Tree

|Operation|Complexity|
|---|---|
|Search|O(log n)|
|Insert|O(log n)|
|Delete|O(log n)|

Luôn cân bằng.

---

## Red Black Tree

Tương tự

```
O(log n)
```

---

## B-tree

```
Search O(log n)

Insert O(log n)

Delete O(log n)
```

Nhưng log cơ số rất lớn.

Thực tế chỉ vài lần truy cập Disk.

---

# 6. Vì sao Search nhanh?

BST

```
          50
        /    \
      30      70
```

Search

```
60
```

50

↓

60 > 50

↓

đi sang phải

↓

70

↓

60 < 70

↓

đi sang trái

↓

60

Mỗi lần loại bỏ một nửa không gian tìm kiếm (với cây cân bằng).

```
O(log n)
```

Tương tự Binary Search trên mảng đã sắp xếp, nhưng không cần dịch chuyển nhiều phần tử khi chèn/xóa.

---

# 7. Ưu điểm

## Search nhanh

```
O(log n)
```

---

## Insert nhanh

Không cần shift.

Array

```
[1 2 3 4]

Insert đầu

↓

Shift toàn bộ
```

Tree

Chỉ cần nối Pointer.

---

## Delete nhanh

Không phải dịch dữ liệu.

---

## Dữ liệu luôn được sắp xếp (BST)

Inorder

```
20

30

40

50

60

70
```

Không cần sort lại sau mỗi lần chèn (nếu giữ được tính chất BST).

---

## Phù hợp dữ liệu phân cấp

Ví dụ

```
Folder

Organization

DOM HTML

Menu

Permission

Comment
```

---

# 8. Nhược điểm

## Không cache-friendly

Array

```
CPU đọc liên tục
```

Tree

```
Pointer Jump
```

CPU Cache Miss nhiều hơn.

---

## Tốn bộ nhớ

Node

```
Value

Left pointer

Right pointer
```

Nếu 64 bit

```
8 byte

8 byte

8 byte

≈24 byte (chưa tính padding/object header)
```

Trong Java còn có object header và alignment, nên chi phí mỗi node thường lớn hơn đáng kể.

---

## Pointer chasing

CPU phải

```
Load

↓

Jump

↓

Load

↓

Jump
```

Chậm hơn đọc liên tục trong Array.

---

## Cần cân bằng

BST thường

```
O(log n)
```

BST lệch

```
O(n)
```

Do đó các triển khai thực tế thường dùng cây tự cân bằng.

---

# 9. Trade-off với cấu trúc khác

|Data Structure|Search|Insert|Delete|Memory|Cache|
|---|---|---|---|---|---|
|Array|O(n)|O(n)|O(n)|Rất tốt|Rất tốt|
|Sorted Array|O(log n)|O(n)|O(n)|Rất tốt|Rất tốt|
|Linked List|O(n)|O(1)*|O(1)*|Thấp nhưng nhiều pointer|Kém|
|HashMap|O(1)|O(1)|O(1)|Cao|Khá|
|BST|O(log n)|O(log n)|O(log n)|Cao|Kém|
|AVL|O(log n)|O(log n)|O(log n)|Cao hơn BST|Kém|
|B-tree|O(log n)|O(log n)|O(log n)|Tối ưu cho block|Tốt hơn BST trên lưu trữ ngoài|

* O(1) khi đã có con trỏ đến node cần thao tác.

---

# 10. Khi nào dùng trong hệ thống thực tế?

Tree xuất hiện rất nhiều.

## Database Index

B+

```
UserID

↓

B+Tree

↓

Page

↓

Record
```

Hầu hết RDBMS dùng B-tree/B+ tree cho index vì giảm số lần truy cập đĩa và hỗ trợ truy vấn theo khoảng.

---

## File System

```
/

home

user

document
```

---

## HTML DOM

```
html

body

div

button
```

---

## Permission

```
Company

Department

Team

Employee
```

---

## Compiler

AST

```
      +
    /   \
   *     3
 /   \
2     5
```

---

## AI

Decision Tree

---

## Router

Trie

---

## Auto Complete

Trie

---

# 11. Java hiện thực thế nào?

Không có lớp `Tree` tổng quát trong Java Standard Library.

Java cung cấp các cấu trúc dựa trên cây như:

- `TreeMap` (Red-Black Tree)
    
- `TreeSet` (Red-Black Tree)
    

Ví dụ

```java
TreeMap<Integer,String> map = new TreeMap<>();
```

Search

```
O(log n)
```

---

Node bên trong (đơn giản hóa)

```java
class Entry<K,V>{

    K key;

    V value;

    Entry left;

    Entry right;

    Entry parent;

    boolean color;
}
```

Đây là Red-Black Tree để đảm bảo chiều cao luôn ở mức O(log n).

---

# 12. PHP

PHP không có Tree trong Standard Library.

Có

```php
SplDoublyLinkedList

SplQueue

SplStack

SplHeap
```

Nếu cần Tree

- tự implement
    
- hoặc dùng package Composer
    

Ví dụ Node

```php
class Node{

    public $value;

    public $left;

    public $right;
}
```

---

# 13. Mối liên hệ giữa Data Structure → Memory → Big-O → Performance → Trade-off

Đây là tư duy rất quan trọng trong phỏng vấn hệ thống và hiệu năng.

```
Data Structure
        │
        ▼
Memory Layout
(Array liên tục hay Node rời rạc?)
        │
        ▼
CPU Access Pattern
(Cache hit hay Pointer chasing?)
        │
        ▼
Algorithm
(Cần duyệt tuần tự, chia đôi tìm kiếm hay băm?)
        │
        ▼
Big-O
(O(1), O(log n), O(n))
        │
        ▼
Performance thực tế
(Cache locality, branch prediction, cấp phát bộ nhớ, số lần truy cập RAM/đĩa)
        │
        ▼
Trade-off
(Tốc độ tìm kiếm ↔ chèn/xóa ↔ bộ nhớ ↔ tính có thứ tự ↔ khả năng mở rộng)
```

Một điểm cần nhớ là **Big-O không quyết định toàn bộ hiệu năng**. Ví dụ:

- Một `HashMap` có tra cứu trung bình `O(1)` nhưng không giữ thứ tự và có thể tốn nhiều bộ nhớ.
    
- Một `Sorted Array` có tìm kiếm `O(log n)` và chèn `O(n)`, nhưng do dữ liệu liên tục trong bộ nhớ nên trên tập dữ liệu vừa phải, nó có thể nhanh hơn một BST `O(log n)` nhờ tận dụng CPU cache.
    
- Một `Red-Black Tree` giữ được `O(log n)` ổn định và hỗ trợ duyệt theo thứ tự, nhưng phải đánh đổi bằng nhiều con trỏ hơn, thao tác cân bằng cây và khả năng tận dụng cache kém hơn mảng.
    

## Kết luận

Đối với backend và system design, cần phân biệt rõ các họ cây vì mục đích sử dụng khác nhau:

- **Binary Tree**: cấu trúc nền tảng để học.
    
- **Binary Search Tree (BST)**: hỗ trợ tìm kiếm theo khóa nhưng có thể suy biến.
    
- **AVL Tree**: cân bằng chặt, tối ưu tìm kiếm, chi phí chèn/xóa cao hơn.
    
- **Red-Black Tree**: cân bằng mềm, được dùng phổ biến trong thư viện chuẩn (Java `TreeMap`, `TreeSet`, C++ `std::map`, `std::set`).
    
- **B-tree/B+ Tree**: tiêu chuẩn cho chỉ mục cơ sở dữ liệu và hệ thống tệp nhờ tối ưu truy cập lưu trữ ngoài.
    
- **Trie**: tối ưu xử lý chuỗi, autocomplete, từ điển và định tuyến theo tiền tố.
    

Khi học, nên theo thứ tự: **Binary Tree → BST → AVL → Red-Black Tree → B/B+ Tree → Trie**. Thứ tự này giúp hiểu rõ từ nguyên lý cơ bản đến các cấu trúc được sử dụng trong hệ thống thực tế.