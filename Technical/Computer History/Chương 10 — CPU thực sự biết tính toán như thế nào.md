
> **Chủ đề trung tâm**
> 
> _Mọi phép toán phức tạp đều được xây dựng từ một số rất ít phép toán logic và số học cơ bản._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- CPU thực sự biết làm gì.
    
- Vì sao cộng là phép toán quan trọng nhất.
    
- Phép trừ được xây dựng như thế nào.
    
- Nhân và chia đến từ đâu.
    
- Shift là gì.
    
- Compare hoạt động ra sao.
    
- Vì sao thuật toán quan trọng hơn instruction.
    

---

# Kết thúc chương trước

Ta đã biết.

CPU liên tục:

```text
Fetch

↓

Decode

↓

Execute
```

Giả sử CPU vừa Decode xong.

Nó đọc được.

```text
ADD R1,R2
```

CPU biết cộng.

Điều này khá dễ hiểu.

Nhưng.

Nếu instruction là:

```text
MUL
```

CPU làm gì?

---

# Một câu hỏi thú vị

Giả sử.

Bạn hỏi một đứa trẻ.

```text
8 × 5
```

Có hai cách.

## Cách 1

Nó thuộc bảng cửu chương.

↓

Trả lời ngay.

---

## Cách 2

Nó không biết.

↓

Làm:

```text
8

+

8

+

8

+

8

+

8
```

↓

40

Đó chính là điều máy tính thời kỳ đầu từng làm.

---

# Vì sao cộng quan trọng nhất?

Hãy nhìn.

```text
5
+
7
```

↓

12

Đó là phép toán cơ bản nhất.

Một khi đã biết cộng.

Bạn có thể tạo ra rất nhiều thứ khác.

---

# Phép trừ

```text
9 - 4
```

Thực ra.

Có thể viết thành.

```text
9 + (-4)
```

Nhưng.

Máy tính không lưu:

```text
-4
```

Theo cách con người nghĩ.

Nó dùng:

**Two's Complement**

(Chúng ta sẽ học rất sâu ở chương biểu diễn số.)

Ý tưởng.

Trừ.

↓

Biến thành.

↓

Cộng.

Đây là một trong những thiết kế thông minh nhất của kiến trúc máy tính.

---

# Phép nhân

Giả sử.

```text
6 × 4
```

CPU nguyên thủy không có instruction:

```text
MUL
```

Nó làm.

```text
6

+

6

+

6

+

6
```

↓

24

Đó chính là thuật toán.

---

# Hãy thử với số lớn

```text
125 × 20
```

Không ai cộng.

Hai mươi lần.

Người ta nghĩ ra thuật toán nhanh hơn.

Ví dụ.

```text
125 × 20

=

125 × (2 × 10)

=
250 × 10

=
2500
```

Máy tính cũng vậy.

Thuật toán càng tốt.

↓

Tính càng nhanh.

---

# Shift xuất hiện

Một phát hiện rất thú vị.

Trong hệ nhị phân.

Nhân cho:

```text
2
```

chính là.

```text
Dịch trái 1 bit.
```

Ví dụ.

```text
00000101

=

5
```

Shift trái.

```text
00001010
```

↓

10

---

Lại Shift.

```text
00010100
```

↓

20

Không cần cộng.

Không cần nhân.

Chỉ cần.

```text
SHIFT
```

---

# Chia cũng vậy

Ví dụ.

```text
40
```

```text
00101000
```

Shift phải.

```text
00010100
```

↓

20

Lại Shift.

↓

10

Đó là chia cho:

```text
2
```

---

Đây là lý do.

Nhiều compiler sẽ tự động biến.

```c
x * 8
```

thành.

```text
SHIFT LEFT 3
```

vì:

```text
8

=

2³
```

Đây là một ví dụ về **tối ưu hóa ở mức compiler**. Ngày nay, nhiều CPU có bộ nhân phần cứng rất nhanh, nhưng phép dịch bit vẫn là một phép toán đơn giản và hiệu quả.

---

# Compare

CPU không biết.

```java
if(a>b)
```

CPU chỉ biết.

```text
COMPARE
```

Ví dụ.

```text
COMPARE

5

7
```

ALU không trả về.

```text
false
```

Nó chỉ cập nhật:

Các cờ (Flags).

Ví dụ.

```text
Zero Flag

Carry Flag

Overflow Flag

Negative Flag
```

Sau đó.

Instruction tiếp theo sẽ đọc.

Các cờ này.

Ví dụ.

```text
JUMP_IF_ZERO
```

Hoặc.

```text
JUMP_IF_GREATER
```

Điều này sẽ được dùng để xây dựng:

- if
    
- while
    
- switch
    
- for
    

---

# Có phải CPU biết sqrt?

Không hẳn.

Ngày xưa.

Không.

Muốn tính.

```text
√100
```

↓

Thuật toán.

Ngày nay.

Một số CPU có instruction chuyên biệt.

Nhưng.

Về bản chất.

Nó vẫn được xây dựng từ các mạch logic và số học bên dưới.

---

# Có phải CPU biết sin?

Không.

Thông thường.

Thư viện toán học.

↓

Thuật toán.

↓

Instruction.

↓

CPU.

Đó là lý do.

```java
Math.sin()
```

không phải instruction.

Nó là code.

---

# Một điều rất thú vị

CPU hiện đại.

Có thể có instruction.

```text
AES_ENCRYPT
```

Hoặc.

```text
SHA256
```

Nhưng.

Điều đó không có nghĩa.

CPU hiểu:

Mã hóa.

Nó chỉ có thêm các mạch chuyên dụng.

Giống như.

Máy tính bỏ túi.

Có nút.

```text
√
```

Không có nghĩa.

Nó hiểu.

Toán học.

---

# Thuật toán quan trọng hơn instruction

Giả sử.

Bạn cần tính.

```text
2^100
```

Có hai cách.

---

## Cách 1

Nhân.

99 lần.

---

## Cách 2

Exponentiation by Squaring.

↓

Nhanh hơn rất nhiều.

CPU giống nhau.

Instruction giống nhau.

Thuật toán khác nhau.

↓

Hiệu năng khác nhau.

Đây là lý do.

Computer Science.

Quan tâm:

- Data Structure
    
- Algorithm
    

hơn là.

Instruction.

---

# Một ví dụ Backend

Giả sử.

Bạn cần tìm.

Một user.

Trong:

```text
10 triệu record
```

CPU.

Giống nhau.

Instruction.

Giống nhau.

---

## Thuật toán 1

Linear Search.

↓

10 triệu lần Compare.

---

## Thuật toán 2

B-tree Index.

↓

Khoảng 20–30 lần Compare.

---

CPU không hề thay đổi.

Điều thay đổi là:

Thuật toán.

Đó là lý do.

Backend Engineer học:

- B-tree
    
- Hash
    
- Trie
    
- Skip List
    

chứ không học.

Instruction.

---

# Một góc nhìn rất sâu

Hãy nhìn.

Con người.

```text
if

for

while

class

object

REST

JSON
```

↓

Compiler.

↓

Assembly.

↓

Instruction.

↓

ALU.

↓

Logic Gate.

↓

Transistor.

↓

Electron.

↓

Vật lý.

Đây là một chuỗi trừu tượng (abstraction stack).

Mỗi tầng chỉ cần hiểu tầng ngay bên dưới, không cần biết toàn bộ hệ thống hoạt động như thế nào. Nhờ vậy, hàng triệu kỹ sư có thể cùng xây dựng các hệ thống cực kỳ phức tạp mà không phải ai cũng là chuyên gia vật lý bán dẫn.

---

# Một ví dụ hoàn chỉnh

Bạn viết.

```java
int c = a * 8 + b;
```

Quá trình thực sự.

```text
Java

↓

Bytecode

↓

Machine Code

↓

LOAD

↓

SHIFT LEFT 3

↓

LOAD

↓

ADD

↓

STORE
```

Và cuối cùng.

Hàng tỷ transistor.

↓

Đóng.

↓

Mở.

↓

Đóng.

↓

Mở.

↓

Hoàn thành.

Từ góc nhìn của CPU, không tồn tại khái niệm "biến", "hàm" hay "đối tượng". Chỉ có dữ liệu trong bộ nhớ, các thanh ghi và các instruction.

---

# Góc nhìn của Backend Engineer

Có ba bài học rất quan trọng.

## 1. Thuật toán thắng phần cứng

Một thuật toán:

```text
O(n²)
```

chạy trên CPU mạnh.

Vẫn có thể chậm hơn.

```text
O(n log n)
```

chạy trên CPU yếu.

Đó là lý do các công ty công nghệ đánh giá rất cao tư duy thuật toán.

---

## 2. Compiler thông minh hơn bạn nghĩ

Nhiều tối ưu như:

- constant folding
    
- strength reduction (`* 8` → `<< 3` khi phù hợp)
    
- loop unrolling
    
- inlining
    

được compiler thực hiện tự động.

Bạn không nên tối ưu vi mô (micro-optimization) nếu chưa đo đạc hiệu năng.

---

## 3. Đừng thần thánh hóa instruction

Việc CPU có thêm instruction mới không tự động làm ứng dụng nhanh hơn.

Hiệu năng thực tế phụ thuộc vào:

- thuật toán.
    
- truy cập bộ nhớ.
    
- cache.
    
- I/O.
    
- compiler.
    
- dữ liệu đầu vào.
    

Trong nhiều hệ thống backend, thời gian chờ database hoặc mạng thường lớn hơn rất nhiều so với thời gian CPU thực hiện phép cộng hay phép nhân.

---

# Tổng kết chương

CPU không phải một cỗ máy biết mọi phép toán.

Nó chỉ có một tập các phép toán cơ bản, và từ đó xây dựng nên các khả năng phức tạp hơn.

Đây là một nguyên lý xuyên suốt của khoa học máy tính:

> **Những hệ thống rất phức tạp thường được xây dựng từ một số lượng rất nhỏ các thành phần đơn giản, được kết hợp theo những cách thông minh.**

---

# Chuẩn bị cho phần tiếp theo

Đến đây, chúng ta đã hiểu cách **CPU xử lý thông tin**.

Nhưng chúng ta vẫn đang giả định rằng CPU đã có sẵn các con số để tính toán.

Câu hỏi tiếp theo là:

> **Một số nguyên như `-42`, hay một số thực như `3.14159`, được lưu trong bộ nhớ dưới dạng bit như thế nào?**

Đây là nơi chúng ta chuyển sang phần **Biểu diễn dữ liệu trong bộ nhớ**:

1. Bit, Byte, Word và địa chỉ bộ nhớ.
    
2. Unsigned Integer.
    
3. Signed Integer và Two's Complement.
    
4. Overflow và Underflow.
    
5. Floating Point (IEEE 754).
    

Phần này sẽ giải thích những hiện tượng mà hầu như mọi lập trình viên đều gặp, như:

- Vì sao `int` có giới hạn.
    
- Vì sao `0.1 + 0.2 != 0.3`.
    
- Vì sao xảy ra integer overflow.
    
- Vì sao cần chọn `INT`, `BIGINT` hay `DECIMAL` trong cơ sở dữ liệu.