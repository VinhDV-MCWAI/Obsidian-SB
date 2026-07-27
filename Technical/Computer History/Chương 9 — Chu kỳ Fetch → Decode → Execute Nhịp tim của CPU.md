
> **Chủ đề trung tâm**
> 
> _CPU không "chạy chương trình". CPU chỉ lặp đi lặp lại một quy trình đơn giản: lấy lệnh, giải mã và thực thi. Mọi phần mềm trên thế giới đều được xây dựng từ vòng lặp này._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Instruction là gì.
    
- Opcode là gì.
    
- Register là gì.
    
- Program Counter hoạt động như thế nào.
    
- Chu kỳ Fetch → Decode → Execute.
    
- Clock (xung nhịp) thực chất là gì.
    
- Tại sao CPU có thể chạy hàng tỷ lệnh mỗi giây.
    

---

# Chúng ta đang đứng ở đâu?

Cuối chương trước.

Ta biết:

- Chương trình nằm trong RAM.
    
- CPU đọc chương trình.
    
- Program Counter (PC) chỉ vị trí lệnh tiếp theo.
    

Nhưng...

CPU nhìn thấy gì?

---

# Một chương trình trong mắt CPU

Bạn viết:

```java
int c = a + b;
```

Compiler tạo Machine Code.

Ví dụ minh họa:

```text
LOAD R1, [100]

LOAD R2, [104]

ADD R1, R2

STORE [108], R1
```

CPU không biết:

- Java
    
- C++
    
- Python
    

CPU chỉ biết các **Instruction (lệnh máy)**.

---

# Instruction là gì?

Instruction giống như một câu mệnh lệnh rất ngắn.

Ví dụ:

```text
ADD
```

Nghĩa là:

> Cộng.

Hoặc

```text
LOAD
```

Nghĩa là:

Đọc dữ liệu từ Memory.

Hoặc

```text
STORE
```

Ghi dữ liệu vào Memory.

---

Một CPU hiện đại chỉ có vài chục đến vài trăm loại instruction.

Ví dụ:

- ADD
    
- SUB
    
- MUL
    
- DIV
    
- MOV
    
- LOAD
    
- STORE
    
- JMP
    
- CALL
    
- RET
    

Mọi chương trình cuối cùng đều được tạo thành từ các lệnh này.

---

# Nhưng CPU không đọc chữ

CPU không nhìn thấy:

```text
ADD
```

CPU nhìn thấy.

Ví dụ.

```text
10001001
```

Đó gọi là:

**Opcode**

Opcode nghĩa là:

> Mã số của lệnh.

Ví dụ minh họa:

|Opcode|Ý nghĩa|
|---|---|
|0001|ADD|
|0010|SUB|
|0011|LOAD|
|0100|STORE|

Lưu ý: Đây chỉ là ví dụ để dễ hiểu. Mỗi kiến trúc CPU (x86, ARM, RISC-V...) có tập lệnh và cách mã hóa opcode riêng.

---

# Register là gì?

Đây là nơi rất nhiều người nhầm.

Họ nghĩ CPU tính toán trực tiếp trên RAM.

Không.

CPU rất ít khi làm việc trực tiếp với RAM.

CPU có một vùng nhớ cực nhỏ.

Gọi là:

**Register**

Ví dụ.

```text
R1

R2

R3

R4
```

Register:

- rất nhỏ
    
- rất đắt
    
- cực kỳ nhanh
    

Có thể coi Register là "bàn làm việc" của CPU.

RAM giống như nhà kho.

CPU sẽ:

Nhà kho.

↓

Lấy vật.

↓

Đặt lên bàn.

↓

Làm việc.

↓

Mang trở lại kho.

---

# Ví dụ

Muốn tính:

```text
5 + 7
```

CPU không làm:

```text
RAM

↓

RAM

↓

RAM
```

CPU làm:

```text
RAM

↓

Register

↓

ALU

↓

Register

↓

RAM
```

Đó là quy trình chuẩn.

---

# ALU xuất hiện

ALU.

**Arithmetic Logic Unit**

Là nơi thực hiện:

- cộng
    
- trừ
    
- AND
    
- OR
    
- XOR
    
- SHIFT
    
- COMPARE
    

Lưu ý.

ALU không biết Java.

Không biết SQL.

Không biết API.

Nó chỉ biết các phép toán cơ bản.

---

# Hãy chạy một phép cộng

Ví dụ.

```text
5 + 7
```

Memory.

```text
100

↓

5
```

```text
104

↓

7
```

CPU bắt đầu.

---

## Bước 1

Fetch.

Đọc instruction.

```text
LOAD R1,100
```

---

## Bước 2

Decode.

Hiểu rằng.

Đây là:

LOAD.

---

## Bước 3

Execute.

Đọc:

```text
Memory[100]
```

↓

Đưa vào.

```text
R1
```

---

Tiếp tục.

```text
LOAD R2,104
```

↓

```text
R2 = 7
```

---

Tiếp tục.

```text
ADD R1,R2
```

ALU nhận.

```text
5

+

7
```

↓

```text
12
```

↓

Ghi lại.

```text
R1
```

---

Tiếp tục.

```text
STORE 108,R1
```

↓

```text
Memory[108]=12
```

Hoàn thành.

---

# Chu kỳ Fetch → Decode → Execute

Mọi instruction đều đi qua ba bước.

```text
Fetch

↓

Decode

↓

Execute
```

Sau đó.

Lại quay về.

```text
Fetch

↓

Decode

↓

Execute
```

Lặp mãi.

CPU thực chất là một vòng lặp vô tận.

---

# Clock là gì?

Đây là chỗ nhiều người hiểu sai.

Clock không phải:

Đồng hồ xem giờ.

Clock là:

**Xung nhịp đồng bộ.**

Hãy tưởng tượng một dây chuyền sản xuất.

Mỗi tiếng chuông:

```text
Ting!
```

Mọi công nhân.

↓

Làm bước tiếp theo.

CPU cũng vậy.

Mỗi xung Clock.

↓

CPU tiến thêm một bước.

---

Ví dụ.

```text
Clock

↓

3 GHz
```

Nghĩa là.

Khoảng:

```text
3 tỷ xung mỗi giây.
```

Không có nghĩa:

3 tỷ phép tính.

Một instruction có thể cần:

- 1 chu kỳ
    
- 2 chu kỳ
    
- 5 chu kỳ
    
- hàng chục chu kỳ
    

Thậm chí nếu phải truy cập RAM hoặc SSD, thời gian còn lâu hơn rất nhiều.

---

# Program Counter hoạt động liên tục

Giả sử.

```text
PC =1000
```

CPU đọc.

```text
Instruction[1000]
```

Sau khi xong.

↓

```text
PC++

↓

1001
```

Đọc tiếp.

Nếu gặp.

```text
JMP 5000
```

Thì.

```text
PC

↓

5000
```

Đó là cách.

- if
    
- for
    
- while
    
- function
    

được thực hiện.

Không có "vòng lặp" đặc biệt.

Chỉ có Program Counter thay đổi địa chỉ đọc.

---

# Hàm (Function) hoạt động như thế nào?

Giả sử:

```java
sum(a, b);
```

CPU không hiểu "hàm".

Compiler sẽ tạo:

```text
CALL 500
```

CPU:

- lưu địa chỉ hiện tại
    
- đổi PC sang 500
    
- chạy các instruction của hàm
    

Khi gặp:

```text
RET
```

CPU lấy lại địa chỉ đã lưu.

Tiếp tục chương trình.

Sau này chúng ta sẽ gặp **Stack**, nơi lưu địa chỉ quay về và các biến cục bộ.

---

# Điều thú vị

CPU chưa từng đọc:

```java
for(...)
```

Compiler sẽ biến thành:

```text
COMPARE

↓

JUMP

↓

ADD

↓

JUMP

↓

COMPARE
```

Đó là lý do.

CPU không cần biết ngôn ngữ lập trình.

---

# Một góc nhìn sâu hơn

Bạn có để ý không?

CPU chỉ biết khoảng vài chục hoặc vài trăm instruction.

Nhưng.

Thế giới có:

- Java
    
- Go
    
- Rust
    
- Python
    
- PHP
    
- Kotlin
    
- C#
    
- JavaScript
    

Làm sao được?

Bởi vì.

Compiler.

Interpreter.

JIT.

Đều có cùng nhiệm vụ.

↓

Dịch.

↓

Instruction.

CPU chỉ có một "ngôn ngữ mẹ đẻ".

Đó là **Instruction Set Architecture (ISA)**.

Ví dụ:

- x86-64
    
- ARM64
    
- RISC-V
    

Ngôn ngữ lập trình chỉ là các "ngôn ngữ bậc cao" được dịch xuống ISA của CPU.

---

# Góc nhìn của Backend Engineer

Có rất nhiều hiện tượng bạn gặp hàng ngày bắt nguồn từ chương này.

## 1. Hàm nhỏ không phải lúc nào cũng nhanh hơn

Mỗi lần gọi hàm.

↓

CALL.

↓

RET.

↓

Lưu và khôi phục Register.

Đó là chi phí thật.

Compiler hiện đại có thể **inline** các hàm nhỏ để loại bỏ chi phí này.

---

## 2. Branch Prediction

Nếu CPU gặp:

```java
if(...)
```

Nó phải quyết định.

Đi đâu?

CPU hiện đại thường đoán trước nhánh sẽ chạy.

Nếu đoán sai.

↓

Pipeline bị xóa.

↓

Hiệu năng giảm.

Đây là lý do dữ liệu có tính ngẫu nhiên cao đôi khi chạy chậm hơn dữ liệu có quy luật.

---

## 3. Register nhanh hơn RAM rất nhiều

Khi compiler tối ưu.

Nó cố giữ biến trong Register.

Thay vì liên tục đọc RAM.

Đó là lý do:

```java
int sum=0;
```

có thể không bao giờ được ghi xuống RAM trong suốt quá trình tính toán.

---

# Tổng kết chương

CPU không "hiểu" chương trình.

CPU chỉ lặp đi lặp lại ba bước:

```text
Fetch

↓

Decode

↓

Execute
```

Đây là "nhịp tim" của mọi máy tính hiện đại.

Mọi thứ từ:

- trình duyệt
    
- game
    
- database
    
- JVM
    
- Docker
    
- AI
    

đều được thực hiện bằng hàng tỷ vòng lặp Fetch → Decode → Execute mỗi giây.

---

# Chuẩn bị cho chương tiếp theo

Đến đây, chúng ta đã hiểu **CPU vận hành như thế nào**.

Nhưng vẫn còn một câu hỏi rất lớn:

> **CPU có thể thực hiện hàng trăm loại instruction, nhưng bản chất nó có thực sự biết nhân, chia, căn bậc hai hay số thực không?**

Câu trả lời là **không hoàn toàn**.

Chương tiếp theo sẽ quay lại một ý tưởng rất thú vị:

> **CPU nguyên thủy gần như chỉ biết cộng, so sánh và dịch bit. Những phép toán phức tạp hơn được xây dựng từ các phép toán đơn giản này như thế nào?**

Đây sẽ là chương kết nối giữa **kiến trúc CPU**, **thuật toán** và **toán học**, đồng thời giải thích vì sao việc hiểu bit, dịch bit và phép cộng lại quan trọng đến vậy trong khoa học máy tính.