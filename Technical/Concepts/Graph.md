Đây là một trong những cấu trúc dữ liệu quan trọng nhất của Computer Science. Nếu Array là mô hình **danh sách**, Tree là mô hình **phân cấp**, thì **Graph** là mô hình **mạng lưới (network)**.

Nó được dùng ở khắp nơi: mạng xã hội, Google Maps, Internet, Dependency Injection, Build System, Kubernetes, Git...

---

# 1. Graph là gì?

Graph là tập hợp gồm:

- **Vertex (Node)**: đỉnh
    
- **Edge**: cạnh kết nối giữa hai đỉnh
    

Ví dụ:

```
A ----- B
|       |
|       |
C ----- D
```

Vertex:

```
A B C D
```

Edge

```
(A,B)
(A,C)
(B,D)
(C,D)
```

Graph chỉ mô tả **mối quan hệ**, không quan tâm dữ liệu nằm theo thứ tự như Array.

---

Ví dụ Facebook

```
Vinh ----- Nam
  |
  |
Trang
```

Node

```
User
```

Edge

```
Friend
```

---

Ví dụ Google Maps

```
HCM ------ Bien Hoa
  \
   \
    Vung Tau
```

Node

```
City
```

Edge

```
Road
```

---

# 2. Các loại Graph

## Undirected Graph

```
A ----- B
```

Đi được hai chiều.

---

## Directed Graph

```
A -----> B
```

Chỉ đi một chiều.

---

Ví dụ

Twitter Follow

```
A follows B
```

không có nghĩa

```
B follows A
```

---

## Weighted Graph

```
A ----10km---- B
```

Edge có trọng số.

---

## Unweighted Graph

```
A -------- B
```

Mọi cạnh đều giống nhau.

---

## Cyclic Graph

Có vòng

```
A
| \
|  \
C---B
```

---

## Acyclic Graph

Không có vòng

Ví dụ

Dependency

```
API

↓

Service

↓

Repository

↓

Database
```

---

# 3. Graph lưu trong bộ nhớ như thế nào?

Có hai cách phổ biến.

---

# Cách 1. Adjacency Matrix

Ma trận N × N.

Ví dụ

```
A B C

A 0 1 1
B 1 0 0
C 1 0 0
```

Nếu

```
Matrix[A][B]=1
```

nghĩa là

```
A nối với B
```

---

Trong RAM

```
[0][1][1]
[1][0][0]
[1][0][0]
```

thực chất là

```
Array<Array<int>>
```

---

Bộ nhớ

```
O(V²)
```

---

Nếu graph có

```
10000 node
```

thì cần

```
10000²

=100 triệu ô
```

rất tốn RAM.

---

# Cách 2. Adjacency List

Mỗi node lưu danh sách hàng xóm.

Ví dụ

```
A -> B,C

B -> A

C -> A
```

Trong RAM

```
HashMap<Node,List<Node>>
```

hoặc

```
Array<List<int>>
```

Ví dụ

```
0 -> [1,2]

1 -> [0]

2 -> [0]
```

---

Bộ nhớ

```
O(V+E)
```

Đây là cách phổ biến nhất.

---

# Ví dụ Memory

```
A

↓

[B,C,D]
```

```
A
```

chỉ giữ pointer

```
↓

List

↓

pointer

↓

B

↓

pointer

↓

C

↓

pointer

↓

D
```

---

# 4. Các Operation chính

## Add Vertex

```
add(A)
```

---

## Remove Vertex

```
remove(A)
```

---

## Add Edge

```
A -> B
```

---

## Remove Edge

```
A x B
```

---

## Check Connection

```
A

↓

B ?
```

---

## Traverse

DFS

BFS

---

## Find Path

```
A

↓

...

↓

B
```

---

## Detect Cycle

Có vòng không.

---

## Topological Sort

Cho DAG.

---

## Shortest Path

Ví dụ Google Maps.

---

# 5. Big-O

## Với Adjacency Matrix

|Operation|Big O|
|---|---|
|Add Edge|O(1)|
|Remove Edge|O(1)|
|Check Edge|O(1)|
|Traverse|O(V²)|
|Memory|O(V²)|

---

## Với Adjacency List

|Operation|Big O|
|---|---|
|Add Edge|O(1)|
|Remove Edge|O(degree)|
|Check Edge|O(degree) _(hoặc O(1) nếu danh sách kề dùng HashSet thay vì List)_|
|Traverse|O(V+E)|
|Memory|O(V+E)|

---

# DFS

```
A

↓

B

↓

D

↑

C
```

Đi sâu trước.

Stack.

```
O(V+E)
```

---

# BFS

```
A

↓

B C D

↓

...
```

Đi theo từng tầng.

Queue.

```
O(V+E)
```

---

# 6. Ưu điểm

Có thể biểu diễn gần như mọi quan hệ.

Ví dụ

```
Friend

Road

Dependency

Package

Call graph

Knowledge graph

Blockchain

Internet
```

đều là Graph.

---

Không bị giới hạn phân cấp như Tree.

---

Có nhiều thuật toán mạnh.

Ví dụ

```
Dijkstra

A*

Prim

Kruskal

Tarjan

Kosaraju

Floyd-Warshall

Bellman-Ford
```

---

# 7. Nhược điểm

Graph rất khó hơn Array.

---

Traversal phức tạp.

---

Khó debug.

---

Thuật toán rất nhiều.

---

Memory có thể lớn.

---

Cycle gây nhiều vấn đề.

Ví dụ

Dependency Injection

```
A

↓

B

↓

C

↓

A
```

sẽ crash.

---

# 8. Trade-off

## So với Array

Array

```
Index

↓

Value
```

Graph

```
Node

↓

Relationship
```

Array nhanh hơn.

Graph linh hoạt hơn.

---

## So với Linked List

Linked List

```
1 chiều
```

Graph

```
N chiều
```

---

## So với Tree

Tree

```
1 parent
```

Graph

```
n parent
```

Tree không có cycle.

Graph có.

---

## So với HashMap

HashMap

```
Key

↓

Value
```

Graph

```
Node

↓

Neighbor
```

HashMap không mô hình hóa quan hệ nhiều-nhiều; Graph thì có.

---

# 9. Khi nào dùng trong thực tế?

## Google Maps

Node

```
Intersection
```

Edge

```
Road
```

---

## Facebook

Node

```
User
```

Edge

```
Friend
```

---

## Git

Commit

```
o

↓

o

↓

o
```

Merge

```
o

↙ ↘

o   o

 \ /

  o
```

Commit graph thực chất là DAG.

---

## Maven / Gradle

Dependency Graph.

---

## Kubernetes

Resource Dependency.

---

## Compiler

AST (Abstract Syntax Tree) là Tree, nhưng Call Graph, Control Flow Graph (CFG) và Data Flow Graph (DFG) đều là Graph.

---

## Network

```
Router

↓

Switch

↓

Server
```

Internet chính là một graph rất lớn.

---

# 10. Java/PHP hiện thực như thế nào?

Không có `Graph` trong thư viện chuẩn của Java hay PHP. Thông thường, lập trình viên hiện thực bằng các cấu trúc cơ bản.

Ví dụ Java:

```java
Map<Integer, List<Integer>> graph = new HashMap<>();

graph.put(1, new ArrayList<>());
graph.put(2, new ArrayList<>());

graph.get(1).add(2);
graph.get(2).add(1);
```

Nếu cần kiểm tra cạnh nhanh:

```java
Map<Integer, Set<Integer>> graph = new HashMap<>();

graph.get(1).add(2);

graph.get(1).contains(2);
```

Lúc này việc kiểm tra cạnh trung bình là `O(1)` thay vì `O(degree)`.

Ví dụ PHP:

```php
$graph = [];

$graph[1] = [2, 3];
$graph[2] = [1];
$graph[3] = [1];
```

Hoặc:

```php
$graph = [
    1 => [
        2 => true,
        3 => true,
    ],
];
```

để kiểm tra kết nối nhanh bằng `isset($graph[1][2])`.

Các thư viện chuyên dụng:

- Java: JGraphT
    
- Java: Guava Graph
    
- C++: Boost Graph Library (BGL)
    
- Python: NetworkX
    
- JavaScript/TypeScript: graphlib
    

---

# 11. Mối liên hệ giữa cấu trúc dữ liệu → bộ nhớ → Big-O → hiệu năng → trade-off

Đây là tư duy quan trọng nhất khi học Data Structure.

```text
Bài toán
      │
      ▼
Chọn Graph
      │
      ▼
Chọn cách biểu diễn
(Matrix hoặc List)
      │
      ▼
Cách tổ chức dữ liệu trong RAM
(Array liên tục hay các danh sách liên kết/HashMap)
      │
      ▼
Chi phí truy cập bộ nhớ
(Cache locality, số lần dereference pointer, dung lượng RAM)
      │
      ▼
Độ phức tạp Big-O
(Add Edge, Check Edge, Traverse...)
      │
      ▼
Hiệu năng thực tế
(CPU, cache, RAM, khả năng mở rộng)
      │
      ▼
Trade-off
```

Ví dụ:

|Tiêu chí|Adjacency Matrix|Adjacency List|
|---|---|---|
|Tổ chức bộ nhớ|Mảng 2 chiều liên tục|Danh sách kề (List/Set) cho mỗi đỉnh|
|Bộ nhớ|`O(V²)`|`O(V+E)`|
|Kiểm tra cạnh|`O(1)`|`O(degree)` hoặc `O(1)` với `HashSet`|
|Duyệt graph|`O(V²)`|`O(V+E)`|
|Cache locality|Tốt|Kém hơn do nhiều con trỏ|
|Phù hợp|Dense graph (rất nhiều cạnh)|Sparse graph (ít cạnh)|

## Kết luận

Graph không phải là một cấu trúc dữ liệu đơn lẻ mà là **một mô hình biểu diễn quan hệ**. Hiệu năng của Graph phụ thuộc chủ yếu vào **cách biểu diễn trong bộ nhớ**:

- **Adjacency Matrix**: đánh đổi nhiều bộ nhớ để có truy cập cạnh rất nhanh (`O(1)`).
    
- **Adjacency List**: tiết kiệm bộ nhớ và tối ưu cho phần lớn bài toán thực tế, nhưng việc kiểm tra hoặc xóa cạnh có thể chậm hơn.
    

Đó là lý do trong các hệ thống backend, cơ sở dữ liệu đồ thị, mạng máy tính, công cụ build, Git và các bài toán đường đi, **Adjacency List** là cách biểu diễn được sử dụng phổ biến nhất vì đa số graph trong thực tế là **sparse graph** (số cạnh nhỏ hơn rất nhiều so với `V²`).