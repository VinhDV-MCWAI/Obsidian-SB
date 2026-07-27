Tài liệu này tổng hợp lại toàn bộ phần kiến thức đã trao đổi về **Array** và **Linked List**, theo hướng:

- đúng bản chất kỹ thuật
- có chiều sâu đủ để dùng khi phỏng vấn Product Company
- mạch lạc, không nói nấp lửng
- đủ dễ hiểu cho người mới
- có đủ bối cảnh từ **cấu trúc dữ liệu → bộ nhớ → Big-O → CPU/cache → hiệu năng thực tế → trade-off**
- có phần thực hành / demo riêng bằng **PHP**
Tài liệu này không cố “làm đẹp” khái niệm. Mục tiêu là hiểu đúng, nói đúng, và dùng được. Sử dụng được kể cả cho người chưa có nền tảng và sử dụng lại lâu dài.

---

# 1. Linked List là gì?

Linked List là một **cấu trúc dữ liệu tuyến tính** gồm nhiều **node** nối với nhau bằng pointer (con trỏ) | reference (tham chiếu).

Mỗi node thường chứa

```
+---------+---------+
|  Data   |  Next   |
+---------+---------+
```

hoặc

```
+---------+---------+---------+
| Prev    | Data    | Next    |
+---------+---------+---------+
```

đối với Doubly Linked List.

Trong đó:
+ Data: là giá trị của phần tử đó
+ Prev hoặc next: là giá trị trỏ địa chỉ của node trước or sau nó

Điểm quan trọng:
+ Dữ liệu **không nằm liên tục trong bộ nhớ**.
+ Mỗi node có thể nằm ở địa chỉ bất kỳ đâu trong RAM.
+ Node hiện tại chỉ biết node kế tiếp thông qua con trỏ.

Ví dụ

```
Head
 |
 v

Address:1000
+-----+------+
| 10  | 8000 |
+-----+------+

Address:8000
+-----+------+
| 20  | 3500 |
+-----+------+

Address:3500
+-----+------+
| 30  | NULL |
+-----+------+
```

Node thứ hai hoàn toàn không cần nằm cạnh node đầu tiên.

---

# 2. Nó lưu dữ liệu trong bộ nhớ như thế nào?

Đây là điều cực kỳ quan trọng.

## [[Array]]

```
Address

1000
1004
1008
1012
1016
```

```
[10][20][30][40][50]
```

CPU biết:

```
address = base + index * sizeof(element)
```

nên truy cập O(1).

---

## Linked List

```
1000 -> 5030 -> 9212 -> 700 -> NULL
```

Mỗi node

```
Node

Data
Pointer
```

Pointer chứa địa chỉ node tiếp theo.

Ví dụ

```
1000

+------+------+
| 10   |5030  |
+------+------+

5030

+------+------+
|20    |9212  |
+------+------+
```

Linked List không có công thức nhảy trực tiếp tới node thứ `i`.
Muốn đến node thứ `i`, phải bắt đầu từ `head` và đi lần lượt từng node:

Ở đây, muốn đến node thứ 100:

```
head

↓

1 -> 2 -> 3 -> 4 -> 5 -> ...
```

phải đi từng node và đếm số cho tới 100.
Nó giống một chuỗi liên kết, không giống một dãy địa chỉ liên tiếp.

---

# 3. Các loại Linked List

## Singly Linked List

```
1 -> 2 -> 3 -> 4 -> NULL
```

Mỗi node chỉ có con trỏ tới node tiếp theo.

### Đặc điểm
- đơn giản
- ít overhead hơn doubly list
- đi một chiều
### Hạn chế
- không đi ngược lại được
- xóa node hiện tại thường cần biết node trước đó

---

## Doubly Linked List

```
NULL <-1<->2<->3<->4->NULL
```

Mỗi node có cả `prev` và `next`.

### Đặc điểm
- đi hai chiều
- thuận tiện cho thao tác xóa/chèn quanh node đã biết
- thường được dùng khi cần duyệt cả hai hướng

### Hạn chế
- tốn thêm bộ nhớ cho một con trỏ nữa
- thao tác cập nhật phức tạp hơn singly list

---

## Circular Linked List

```
1 ->2 ->3
^      |
|______|
```

Node cuối trỏ ngược về node đầu.

### Đặc điểm
- dùng cho các bài toán vòng lặp, luân phiên, round-robin
- node cuối không kết thúc bằng `NULL` mà quay về head

### Hạn chế
- dễ gây vòng lặp vô hạn nếu không quản lý điều kiện dừng cẩn thận


---

# 4. Các operation chính

Có thể chia thành

- Access
    
- Search
    
- Insert
    
- Delete
    

---

## Access by index

```
index = 4
```

phải

```
head

↓

1→2→3→4→5
```

Muốn lấy phần tử tại index `k`, Linked List phải duyệt từ `head` đến node thứ `k`. Vì nó không có công thức tính i như array nhảy trực tiếp đến index, nguyên nhân do cấu trúc array cho phép làm điều đó.

Thì Big-O

```
O(n)
```

---

## Search

```
find value = 100
```

```
head

↓

10→20→30→40→100
```

Tìm giá trị `x` trong Linked List cũng phải duyệt từng node cho đến khi gặp giá trị cần tìm hoặc hết danh sách.

Big-O

```
O(n)
```

### Lưu ý quan trọng

Vì có phương thức binary search, nó dựa trên danh sách được sắp xếp sẵn. Sau đó chia đôi danh sách để tìm khoảng giá trị. Nó tốt hơn search theo kiểu duyệt toàn bộ danh sách.
Nên có người hỏi: nếu dữ liệu đã được sắp xếp, có thể dùng binary search không?

**Giải đáp thắc mắc: Có thể dùng Binary Search trên Linked List đã sắp xếp để giảm độ phức tạp xuống $O(\log n)$ không?**

**Khẳng định:** **KHÔNG**.

Binary Search là một thuật toán, nhưng hiệu năng $O(\log n)$ của nó phụ thuộc tuyệt đối vào khả năng **Random Access $O(1)$** của cấu trúc dữ liệu bên dưới.

- Trong Array, việc tìm phần tử ở giữa (`mid = (left + right) / 2`) tốn chi phí $O(1)$.
    
- Trong Linked List, để truy cập được node ở giữa `mid`, bạn bắt buộc phải duyệt từ `Head` qua $n/2$ con trỏ, tốn chi phí $O(n)$.
    

Phương trình đệ quy tính độ phức tạp của Binary Search trên Linked List:

$$T(n) = T\left(\frac{n}{2}\right) + O(n)$$

Áp dụng định lý Master hoặc giải đệ quy:

$$T(n) = O(n) + O\left(\frac{n}{2}\right) + O\left(\frac{n}{4}\right) + \dots + O(1) = O(n)$$

**Kết luận:** Áp dụng Binary Search lên Linked List vẫn tốn độ phức tạp tổng là $O(n)$, đồng thời phát sinh chi phí gọi hàm/đệ quy. Do đó, Binary Search trên Linked List không mang lại hiệu quả cải thiện tốc độ so với Linear Search $O(n)$.

---

## Insert at head

Chèn ở đầu là thao tác rẻ nhất của Linked List.

```
head

↓

20→30
```

### Cách làm

```text

newNode.next = head

head = newNode

```
  
### Big-O
- **O(1)**
### Vì sao
Chỉ thay đổi con trỏ của node mới và cập nhật `head`.

ứng dụng insert

```
10
```

chỉ cần

```
10.next=head

head=10
```

---

## Insert at tail

#### Trường hợp 1: có tail pointer

Nếu danh sách giữ sẵn con trỏ đến node cuối (`tail`), ta chỉ cần:

```
tail.next=newNode

tail=newNode
```

```
O(1)
```

#### Trường hợp 2: không có tail pointer

Nếu không giữ `tail`, phải duyệt từ `head` tới node cuối.

```
O(n)
```

### Ý nghĩa của `tail`

`Tail` là một con trỏ đặc biệt được lưu trữ trong cấu trúc quản lý danh sách, luôn trỏ trực tiếp đến node cuối cùng của Linked List.

Nó giúp chèn cuối nhanh hơn vì không cần duyệt toàn bộ danh sách để tìm node cuối.

> **Giải đáp thắc mắc: Tại sao các tài liệu viết Insert/Delete vị trí giữa có độ phức tạp $O(1)$, trong khi thực tế phải tìm kiếm vị trí tốn $O(n)$?**
> 
> Đây là điểm rất nhiều người học lẫn tài liệu tóm tắt hay gây hiểu lầm. Bắt buộc phải tách minh bạch thao tác này thành **2 pha độc lập**:
> 
> 1. **Pha 1: Tìm vị trí (Search / Traversal):** Tìm ra node đứng trước vị trí cần thao tác. Chi phí: **$O(n)$**.
>     
> 2. **Pha 2: Thực thi thay đổi con trỏ (Pointer Re-assignment):** Thay đổi các liên kết con trỏ giữa các node liên quan. Chi phí: **$O(1)$**.
>     

Plaintext

```
Chèn X vào giữa A và B (A -> B  =>  A -> X -> B):
Chi phí thao tác con trỏ thuần túy (Pha 2):
1. X.next = A.next  (O(1))
2. A.next = X       (O(1))
```

- **Trường hợp 1 (Chưa có con trỏ tới vị trí chèn/xóa):**
    
    $$\text{Tổng chi phí} = \text{Tìm vị trí } O(n) + \text{Sửa con trỏ } O(1) = O(n)$$
    
- **Trường hợp 2 (Đã giữ sẵn con trỏ/tham chiếu/iterator đến node cần chèn/xóa):**
    
    $$\text{Tổng chi phí} = \text{Sửa con trỏ } O(1) = O(1)$$
    

**Tại sao sách kinh điển và lý thuyết xếp thao tác này là $O(1)$?**

Lý do là trong thiết kế hệ thống thực tế (ví dụ: LRU Cache, Scheduler Queues, Memory Allocators), Linked List thường kết hợp với một cấu trúc dữ liệu khác (như Hash Map) để lưu trữ trực tiếp con trỏ tới các node. Khi đó, hệ thống bỏ qua được Pha 1 ($O(1)$ lookup) và tận dụng trọn vẹn ưu thế $O(1)$ của Pha 2.

So sánh với Array: Dù đã có sẵn index $k$ ($O(1)$ lookup), thao tác chèn/xóa trong Array **bắt buộc phải dịch chuyển $n-k$ phần tử** còn lại sang trái/phải trên bộ nhớ, dẫn đến chi phí biến đổi cấu trúc của Array luôn là $O(n)$.

---

## Insert middle

Giả sử

```
A -> B -> C
```

insert X

```
A -> X -> B -> C
```

chỉ đổi pointer

```
X.next=B

A.next=X
```

#### Về mặt thao tác con trỏ
Đổi pointer:

```
O(1)
```

#### Nhưng để chèn đúng vị trí
ta thường phải tìm node trước vị trí cần chèn, ví dụ `A`.

```
O(n)
```

nên tổng

```
O(n)
```

### Kết luận đúng

Insert ở giữa thường được hiểu là:
- **O(n)** nếu phải duyệt để tìm vị trí
- **O(1)** chỉ khi đã có sẵn node đứng trước vị trí cần chèn

Cần tách rõ hai phần:
1. **Tìm vị trí**: thường là `O(n)`
2. **Đổi con trỏ**: `O(1)`

Tổng hợp thực tế: **O(n)**
  
Đây là điểm mà nhiều giải thích viết quá vắn tắt khiến người đọc hiểu sai.

---

## Delete head

```
head=head.next
```

```
O(1)
```

---

## Delete middle

```
A->B->C
```

Muốn xóa `B`:

```
A.next=C
```

Nếu đã có pointer tới A, thao tác cập nhật là:

```
O(1)
```

Nếu phải tìm A trước, thì:

```
O(n)
```

#### Kết luận đúng

Delete ở giữa cũng phải tách:
1. **tìm node trước node cần xóa**: thường là `O(n)`
2. **nối lại con trỏ**: `O(1)`

Tổng hợp thực tế: **O(n)** nếu không có sẵn vị trí.

## Delete tail

#### Singly Linked List
Muốn xóa node cuối, thường phải tìm node đứng trước nó.
- **O(n)**
#### Doubly Linked List
Nếu đã có `tail` và node có `prev`, có thể thao tác rất nhanh hơn.

- thường có thể đạt **O(1)** cho việc xóa cuối trong điều kiện đủ thông tin


---

# 5. Big-O

|Operation|Singly|Doubly|
|---|---|---|
|Access index|O(n)|O(n)|
|Search|O(n)|O(n)|
|Insert head|O(1)|O(1)|
|Insert tail (có tail)|O(1)|O(1)|
|Insert middle (đã có node trước)|O(1)|O(1)|
|Delete head|O(1)|O(1)|
|Delete tail|O(n)|O(1) nếu có tail và prev|
|Delete middle (đã có node)|O(1)|O(1)|
### Ghi chú quan trọng
Các giá trị `O(1)` ở trên là theo điều kiện đã có sẵn node / vị trí cần thao tác.  
Nếu phải tìm vị trí trước, độ phức tạp tổng thể thường trở thành `O(n)`.


---

# 6. Vì sao lại có Big-O như vậy?

Đây là phần quan trọng nhất.

## Vì sao Access O(n)?

Array

```
base+index
```

Linked List

```
head

↓

1→2→3→4→5→6→7
```

Vì không có chỉ số để nhảy thẳng tới vị trí mong muốn.
Không biết node số 6 ở đâu.

Muốn tới node 6, phải đi từng pointer.

---

## Vì sao Insert O(1)?

Bản chất của linked list là **đổi liên kết**, không phải **dịch toàn bộ phần tử**.

```
A→B
```

thành

```
A→X→B
```

chỉ sửa

```
2 pointer
```

không cần copy dữ liệu.

### Nhưng phải nhấn mạnh lại

Điều đó chỉ đúng khi đã biết vị trí cần sửa.  

Nếu không biết vị trí, phải tìm trước, và việc tìm là `O(n)`.

---

## Vì sao Delete O(1)?

```
A→B→C
```

thành

```
A→C
```

đổi pointer.

Tương tự như insert. Nếu không biết vị trí, phải tìm trước, và việc tìm là `O(n)`.

---

# 7. ## GÓC NHÌN PHẦN CỨNG (CPU, BỘ NHỚ VÀ CACHE LOCALITY)

Đây là lý do quan trọng nhất giải thích tại sao trong thực tế, Linked List có hiệu năng thực thi chậm hơn Array rất nhiều, dù xét về mặt lý thuyết Big-O một số thao tác chèn/xóa của chúng bằng hoặc tốt hơn.

Plaintext

```
KIẾN TRÚC CACHE LINE TRÊN CPU (Thường có dung lượng 64 Bytes)

Array trong bộ nhớ (Liên tục):
[ E0 ][ E1 ][ E2 ][ E3 ][ E4 ][ E5 ][ E6 ][ E7 ]
|<------------- Nạp 1 lần vào Cache Line (64B) ------------->|
==> Đọc E0 -> CPU tự động load luôn E1..E7 vào L1/L2 Cache (Cache Hit cực cao).

Linked List trong bộ nhớ (Rải rác):
RAM:  [ Node 1 (0x1000) ] ........ [ Node 2 (0x9000) ] ........ [ Node 3 (0x0350) ]
       |                            |                            |
       +--- Pointer Chasing ------->+--- Pointer Chasing ------->+
==> Mỗi lần đọc 1 Node phải gọi RAM bus, nạp Cache Line chứa dữ liệu thừa không dùng.
==> Hiện tượng Cache Miss liên tục (Pointer Chasing Issue).
```

### 7.1. Spatial Locality (Tính định vị không gian) và CPU Cache Line

Khi CPU cần đọc một dữ liệu từ RAM, nó không đọc đơn lẻ từng byte hay từng biến mà sẽ nạp cả một đường bộ nhớ gọi là **Cache Line** (thường là 64 Bytes) vào CPU Cache (L1, L2, L3).

- **Array:** Do các phần tử xếp liên tục, khi bạn truy cập `arr[0]`, CPU sẽ nạp luôn `arr[1]` đến `arr[15]` (với kiểu `int` 4 bytes) vào Cache. Các thao tác duyệt sau đó diễn ra hoàn toàn trên L1 Cache với độ trễ cực thấp (~1-2 ns).
    
- **Linked List:** Các node nằm rải rác trong Heap. Nạp Node A vào Cache Line chỉ thu được dữ liệu của Node A và các dữ liệu rác không liên quan nằm cạnh ô nhớ của A. Khi chuyển sang Node B, CPU lại nhận thấy dữ liệu không có trong Cache (**Cache Miss**) và phải chờ đợi để nạp dữ liệu từ RAM chính với độ trễ cao (~50-100 ns).
    

### 7.2. Hiện tượng Pointer Chasing và CPU Hardware Prefetcher

- Bộ vi xử lý hiện đại tích hợp bộ đoán trước dữ liệu (**Hardware Prefetcher**). Nếu CPU thấy chương trình đang đọc bộ nhớ theo chuỗi tuyến tính tăng dần (như duyệt Array), Prefetcher sẽ chủ động nạp trước các ô nhớ tiếp theo vào Cache trước khi lệnh chạy tới.
    
- Với Linked List, địa chỉ của node tiếp theo phụ thuộc hoàn toàn vào giá trị con trỏ nằm _bên trong_ node hiện tại. CPU không thể biết địa chỉ node tiếp theo để nạp trước cho tới khi đọc xong dữ liệu con trỏ của node hiện tại. Chuỗi phụ thuộc này gọi là **Pointer Chasing**, làm vô hiệu hóa hoàn toàn CPU Hardware Prefetcher và bắt CPU phải rơi vào các chu kỳ chờ (Stall Cycles).
    

### 7.3. Chi phí ẩn về bộ nhớ (Memory Overhead & Fragmentation)

Xét trên hệ thống 64-bit:

- Một con trỏ (Pointer) có kích thước **8 bytes**.
    
- Giả sử lưu trữ một số nguyên `int` (**4 bytes**).
    

#### Bài toán chi phí bộ nhớ:

1. **Array `int`:** Mỗi phần tử tốn đúng **4 bytes**.
    
2. **Singly Linked List:**
    
    - `Data` (4 bytes) + `Next Pointer` (8 bytes) = 12 bytes.
        
    - Do cơ chế căn chỉnh bộ nhớ (Memory Alignment / Data Padding) của CPU 64-bit, kích thước thực tế bị đẩy lên **16 bytes** cho 1 node.
        
    - **Overhead = 300%** (Dùng 16 bytes bộ nhớ chỉ để lưu 4 bytes dữ liệu hữu ích).
        
3. **Doubly Linked List:**
    
    - `Prev` (8 bytes) + `Data` (4 bytes) + `Padding` (4 bytes) + `Next` (8 bytes) = **24 bytes**.
        
    - **Overhead = 500%**.
        

Ngoài ra, việc liên tục cấp phát (`malloc`/`new`) các node nhỏ rời rạc trên Heap dẫn đến tình trạng **Memory Fragmentation** (Phân mảnh bộ nhớ) và tạo áp lực rất lớn lên bộ dọn rác (Garbage Collector) trong các ngôn ngữ quản lý bộ nhớ tự động như Java, C#, Go.


---

# 8. Ưu điểm

## Không cần contiguous memory

Không cần block RAM lớn.

---

## Insert/Delete nhanh

Không cần shift.

---

## Kích thước linh hoạt

Allocate từng node.

---

## Không invalid toàn bộ khi resize

Array resize thường copy.

Linked List không cần.

---

# 9. Nhược điểm

## Không random access

```
O(n)
```

---

## Cache locality kém

Cache miss nhiều.

---

## Tốn bộ nhớ

Ví dụ

```
int

4 bytes
```

Linked List

```
data

4 bytes

pointer

8 bytes
```

Node

```
12~16 bytes
```

Doubly

```
20~24 bytes
```

Overhead rất lớn.

---

## Fragmentation

Node rải rác trong heap.

---

# 10. Trade-off với Data Structure khác

|Data Structure|Access|Insert Middle|Delete|Memory Locality|
|---|---|---|---|---|
|Array|O(1)|O(n)|O(n)|Rất tốt|
|Dynamic Array|O(1)|O(n)|O(n)|Tốt|
|Linked List|O(n)|O(1)*|O(1)*|Kém|
|Hash Table|O(1) average|-|-|Trung bình|
|Tree|O(log n)|O(log n)|O(log n)|Trung bình|

(*) Khi đã có con trỏ tới vị trí cần thao tác.

---

# 11. Khi nào nên dùng?

Linked List từng được dùng nhiều hơn trước đây. Ngày nay, trong các hệ thống hiệu năng cao, nó được dùng có chọn lọc.

Phù hợp khi:

- Cần chèn/xóa thường xuyên tại các vị trí đã biết (ví dụ đã giữ iterator hoặc con trỏ đến node).
    
- Kích thước thay đổi liên tục và không cần truy cập theo chỉ số.
    
- Là thành phần bên trong các cấu trúc dữ liệu khác (danh sách kề của đồ thị, một số triển khai LRU Cache, free list của memory allocator).
    

Không phù hợp khi:

- Cần truy cập ngẫu nhiên (`list[i]`).
    
- Cần duyệt dữ liệu tốc độ cao.
    
- Dữ liệu lớn và quan tâm hiệu năng CPU cache.
    
- Thao tác chủ yếu là đọc.
    

Trong nhiều trường hợp thực tế, **Dynamic Array (`ArrayList`, `std::vector`, `List<T>`...) thường nhanh hơn Linked List**, ngay cả khi lý thuyết Big-O của thao tác chèn/xóa có vẻ bất lợi, do tận dụng tốt cache CPU và giảm số lần cấp phát bộ nhớ.

---

# 12. Các ngôn ngữ hiện thực như thế nào?

|Ngôn ngữ|Cấu trúc|Ghi chú|
|---|---|---|
|C|`struct Node*`|Tự quản lý bộ nhớ bằng `malloc/free`.|
|C++|`std::list`|Doubly Linked List. Ngoài ra có `std::forward_list` là Singly Linked List.|
|Java|`java.util.LinkedList`|Doubly Linked List, triển khai `List`, `Deque`, `Queue`.|
|C#|`LinkedList<T>`|Doubly Linked List.|
|Python|Không có Linked List chuẩn|`list` là Dynamic Array; `collections.deque` dùng cấu trúc block-linked chứ không phải Linked List thuần.|
|JavaScript|Không có Linked List chuẩn|`Array` là Dynamic Array; nếu cần phải tự cài đặt.|
|Go|`container/list`|Doubly Linked List.|
|PHP|`SplDoublyLinkedList`|Doubly Linked List trong thư viện SPL.|
|Rust|`std::collections::LinkedList`|Doubly Linked List, nhưng tài liệu chính thức cũng khuyến nghị ưu tiên `Vec` trong đa số trường hợp.|

---

# 13. Mối liên hệ giữa cấu trúc dữ liệu → bộ nhớ → Big-O → hiệu năng → trade-off

Đây là chuỗi tư duy quan trọng nhất:

```text
Data Structure
        │
        ▼
Cách tổ chức dữ liệu trong bộ nhớ
(Array: liên tục │ Linked List: rời rạc)
        │
        ▼
Chi phí của từng thao tác
(Access, Insert, Delete...)
        │
        ▼
Big-O
(O(1), O(n), O(log n)...)
        │
        ▼
Hành vi phần cứng
(Cache locality, Pointer chasing,
Cache miss, Branch prediction)
        │
        ▼
Hiệu năng thực tế
(Latency, Throughput, Memory usage)
        │
        ▼
Trade-off
(Chọn cấu trúc phù hợp với bài toán)
```

Một kỹ sư giỏi không dừng ở việc nhớ bảng Big-O. Họ hiểu rằng **Big-O chỉ mô tả tốc độ tăng trưởng thuật toán**, còn hiệu năng thực tế còn phụ thuộc mạnh vào cách dữ liệu được bố trí trong bộ nhớ và cách CPU truy cập chúng. Đó là lý do **Array/Dynamic Array thường là lựa chọn mặc định**, còn **Linked List chỉ nên dùng khi đặc tính chèn/xóa bằng con trỏ thực sự mang lại lợi ích cho bài toán.**