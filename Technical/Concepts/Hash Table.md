Đây là một trong những cấu trúc dữ liệu quan trọng nhất trong lập trình. Nếu chỉ nhớ một điều về Hash Table thì hãy nhớ:

> **Hash Table đánh đổi bộ nhớ để lấy tốc độ truy cập gần như O(1).**

Nó là nền tảng của rất nhiều thành phần trong hệ điều hành, database, cache, compiler, web framework...

---

# 1. Hash Table là gì?

Hash Table là một **cấu trúc dữ liệu ánh xạ (mapping)**.

Nó lưu dữ liệu dưới dạng

```
key -> value
```

Ví dụ

```
"apple" -> 100
"user:1001" -> User Object
"token" -> JWT
```

Khác với Array

```
Array

0 -> A
1 -> B
2 -> C
```

Hash Table không dùng index mà dùng **key**.

Ví dụ

```
phoneBook

"John" -> 0909...
"Mary" -> 0933...
"David" -> 0988...
```

---

# 2. Nó lưu dữ liệu trong bộ nhớ như thế nào?

Đây là phần quan trọng nhất.

Hash Table **không lưu theo thứ tự**.

Nó gồm:

```
Array + Hash Function
```

Ví dụ

```
capacity = 8

Memory

0
1
2
3
4
5
6
7
```

Đây chỉ là một Array bình thường.

---

Khi insert

```
put("apple",100)
```

Hash Table sẽ gọi

```
hash("apple")
```

Ví dụ

```
hash("apple")

=

128312312
```

Sau đó lấy

```
128312312 % 8

=

0
```

Thì dữ liệu sẽ nằm ở

```
index 0
```

```
0 -> ("apple",100)
1
2
3
4
5
6
7
```

---

Nếu insert

```
put("banana",300)
```

```
hash("banana")

=

982398712
```

```
982398712 % 8

=

5
```

```
0 -> apple
1
2
3
4
5 -> banana
6
7
```

Nghĩa là

> Key được chuyển thành index bằng Hash Function.

---

# 3. Hash Function là gì?

Hash Function

```
Key

↓

Hash

↓

Array Index
```

Ví dụ

```
hash("apple")

↓

213123123

↓

213123123 % 16

↓

3
```

Nó biến mọi kiểu dữ liệu

```
String

Integer

UUID

Object
```

thành

```
Integer
```

rồi map sang Array.

---

# 4. Collision là gì?

Không thể tránh được.

Ví dụ

```
capacity = 8
```

```
hash("apple")

=

16

16 % 8 = 0
```

```
hash("orange")

=

24

24 % 8 = 0
```

Cả hai đều vào

```
index 0
```

Đây gọi là

```
Collision
```

---

Có hai cách xử lý phổ biến.

---

## Cách 1: Chaining

```
0

↓

apple

↓

orange

↓

grape
```

Mỗi bucket là một Linked List hoặc Tree.

Java dùng cách này.

---

## Cách 2: Open Addressing

Nếu chỗ đó đầy

```
0 occupied

↓

1

↓

2

↓

3
```

Hash Table sẽ tìm ô trống tiếp theo.

Ví dụ

```
Linear probing

Quadratic probing

Double hashing
```

Python dùng biến thể của Open Addressing.

---

# 5. Các Operation chính

```
put(key,value)

get(key)

remove(key)

containsKey(key)

containsValue()

size()

clear()
```

---

# 6. Big-O

Giả sử hash tốt.

|Operation|Average|Worst|
|---|---|---|
|Insert|O(1)|O(n)|
|Search|O(1)|O(n)|
|Delete|O(1)|O(n)|

---

Tại sao worst lại O(n)?

Ví dụ

```
0

↓

A

↓

B

↓

C

↓

D

↓

E
```

Nếu mọi key đều collision.

Muốn tìm

```
E
```

Phải duyệt hết.

---

# 7. Resize (Rehash)

Ví dụ

```
capacity

=

8
```

Đã dùng

```
7 ô
```

Load factor

```
7 / 8

=

87%
```

Hash Table sẽ resize.

```
8

↓

16
```

Sau đó

**rehash toàn bộ dữ liệu.**

```
apple

↓

hash lại

↓

index mới
```

Do capacity đổi.

---

Big-O

```
Resize

=

O(n)
```

Nhưng không xảy ra thường xuyên.

Nên average vẫn O(1).

---

# 8. Load Factor

```
Load Factor

=

size / capacity
```

Ví dụ

```
capacity = 16

size = 12
```

```
12 / 16

=

0.75
```

Java mặc định

```
0.75
```

Đây là điểm cân bằng giữa:

- ít collision
    
- ít lãng phí bộ nhớ
    

---

# 9. Ưu điểm

## Rất nhanh

```
Search

Insert

Delete

≈ O(1)
```

---

Không cần sort.

---

Không cần dịch chuyển phần tử.

---

Lookup cực nhanh.

---

Phù hợp dữ liệu lớn.

---

# 10. Nhược điểm

## Tốn RAM

Vì phải cấp phát

```
Bucket Array
```

thường lớn hơn số phần tử.

---

Không giữ thứ tự.

```
Insert

A

B

C
```

Có thể iterate

```
C

A

B
```

---

Collision.

Hash kém sẽ làm hiệu năng giảm mạnh.

---

Resize tốn chi phí.

---

# 11. Trade-off với cấu trúc khác

|Data Structure|Search|Insert|Delete|Memory|Ordered|
|---|---|---|---|---|---|
|Array|O(n)|O(1) cuối mảng, O(n) giữa mảng|O(n)|Thấp|Có|
|Linked List|O(n)|O(1) nếu đã có node|O(1) nếu đã có node|Cao|Có|
|Binary Search Tree|O(log n)|O(log n)|O(log n)|TB|Có|
|Balanced Tree|O(log n)|O(log n)|O(log n)|TB|Có|
|Hash Table|O(1)|O(1)|O(1)|Cao|Không|

---

# 12. Khi nào nên dùng?

## Cache

```
Redis

Local Cache

Guava Cache

Caffeine
```

Lookup theo key.

---

Dictionary

```
username

↓

User
```

---

Session

```
SessionID

↓

Session
```

---

API Token

```
Token

↓

Permission
```

---

Config

```
"db.host"

↓

localhost
```

---

Compiler

```
Variable Name

↓

Memory Address
```

---

Database

Hash Index.

---

# 13. Java hiện thực Hash Table như thế nào?

Java có

```
HashMap
```

Bên trong

```
Node[]
```

Mỗi Node

```
hash

key

value

next
```

```
Bucket

↓

Node

↓

Node

↓

Node
```

Từ Java 8

Nếu collision quá nhiều

```
Linked List

↓

Red Black Tree
```

giúp từ

```
O(n)

↓

O(log n)
```

---

# 14. PHP

PHP Array

thực chất là

```
Ordered Hash Table
```

Không phải Array truyền thống.

Nó vừa

```
Hash Table

+

Linked List
```

để giữ thứ tự insert.

Do đó

```
$arr = [
    "a"=>1,
    "b"=>2
];
```

lookup vẫn O(1) trung bình.

---

# 15. Python

```
dict
```

là Hash Table.

Python dùng

```
Open Addressing
```

không dùng Linked List.

---

# 16. C#

```
Dictionary<TKey,TValue>
```

Hash Table.

---

# 17. Go

```
map[K]V
```

Hash Table.

Go chia bucket và tối ưu cho CPU cache.

---

# 18. JavaScript

```
Map
```

đa số engine hiện đại dùng Hash Table (hoặc các biến thể tối ưu).

Object cũng được tối ưu để hỗ trợ truy cập theo thuộc tính với hiệu năng gần tương tự trong nhiều trường hợp.

---

# 19. Mối liên hệ giữa cấu trúc dữ liệu → bộ nhớ → Big-O → hiệu năng → trade-off

Đây là tư duy mà một backend engineer hoặc system engineer cần nắm.

```
                    Data Structure
                          │
                          ▼
              Cách tổ chức dữ liệu trong bộ nhớ
                          │
                          ▼
             Chi phí của từng thao tác (Big-O)
                          │
                          ▼
        Hiệu năng thực tế (CPU cache, RAM, branch prediction,
           cấp phát bộ nhớ, số lần truy cập bộ nhớ...)
                          │
                          ▼
                 Trade-off của cấu trúc dữ liệu
```

Áp dụng với Hash Table:

|Khía cạnh|Hash Table|
|---|---|
|Tổ chức bộ nhớ|Một mảng bucket, mỗi bucket chứa phần tử hoặc chuỗi phần tử (chaining) hay tìm ô trống khác (open addressing).|
|Chi phí thao tác|Trung bình O(1), nhưng phụ thuộc chất lượng hàm băm, load factor và cách xử lý collision.|
|Hiệu năng thực tế|Thường rất nhanh khi số collision thấp. Tuy nhiên, resize và collision nhiều sẽ làm tăng độ trễ. Open addressing có locality tốt hơn, còn chaining chịu thêm chi phí con trỏ và cấp phát động.|
|Trade-off|Đánh đổi bộ nhớ dư thừa và chi phí resize để đạt tốc độ tra cứu rất cao; đổi lại không hỗ trợ thứ tự hay truy vấn theo khoảng giá trị.|

---

# 20. Góc nhìn của System Design

Khi thiết kế hệ thống, câu hỏi không chỉ là **"Big-O là bao nhiêu?"** mà còn là:

- Dữ liệu có cần giữ thứ tự không?
    
- Có cần truy vấn theo khoảng (`BETWEEN`, `>=`, `<=`) không?
    
- Kích thước dữ liệu có thay đổi liên tục không?
    
- Có chấp nhận dùng thêm RAM để giảm độ trễ không?
    
- Mẫu truy cập chủ yếu là đọc (`read-heavy`) hay ghi (`write-heavy`)?
    
- Có cần tính ổn định của thứ tự duyệt (`iteration order`) không?
    

Ví dụ:

- **Cache, session, cấu hình, bảng ánh xạ ID → Object**: Hash Table là lựa chọn rất phù hợp vì ưu tiên tra cứu nhanh.
    
- **Leaderboard, sổ địa chỉ cần sắp xếp, index B-Tree trong database**: Cấu trúc cây cân bằng phù hợp hơn vì hỗ trợ dữ liệu có thứ tự và truy vấn theo khoảng với chi phí `O(log n)`.
    

Đây là lý do trong thực tế không có cấu trúc dữ liệu nào "tốt nhất"; mỗi cấu trúc tối ưu cho một tập yêu cầu và chấp nhận những đánh đổi khác nhau.