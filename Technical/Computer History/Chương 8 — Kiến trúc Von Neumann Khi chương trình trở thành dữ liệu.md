
> **Chủ đề trung tâm**
> 
> _Cuộc cách mạng lớn nhất của máy tính không phải transistor hay CPU, mà là ý tưởng lưu chương trình trong bộ nhớ giống như dữ liệu._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Máy tính trước Von Neumann hoạt động như thế nào.
    
- Vì sao ENIAC lập trình rất khó.
    
- Ý tưởng Stored Program.
    
- CPU, Memory và Bus phối hợp ra sao.
    
- Vì sao gần như mọi máy tính hiện đại đều dựa trên kiến trúc Von Neumann.
    
- Von Neumann Bottleneck là gì.
    

---

# Kết thúc chương trước

Đến đây chúng ta đã có:

- Bit
    
- Boolean
    
- Transistor
    
- IC
    
- CPU
    

Nghe có vẻ máy tính đã hoàn chỉnh.

Nhưng thực tế.

Nó vẫn chưa biết chạy chương trình.

---

# Hãy quay lại ENIAC

Ở chương trước.

Ta biết ENIAC muốn đổi chương trình phải:

- tháo dây
    
- cắm dây
    
- đổi công tắc
    

Ví dụ.

Hôm nay.

Máy cộng.

↓

Ngày mai.

Muốn nhân.

↓

Kỹ sư phải tháo hàng trăm dây.

Điều này rất chậm.

---

# Vấn đề

Máy tính lúc đó không phân biệt:

```text
Chương trình
```

và

```text
Dữ liệu
```

Muốn thay đổi thuật toán.

↓

Phải thay đổi phần cứng.

Đây là giới hạn rất lớn.

---

# Một ý tưởng cực kỳ đơn giản

John von Neumann đặt câu hỏi.

> Nếu dữ liệu lưu trong bộ nhớ được...

Thì...

**Chương trình có thể lưu trong bộ nhớ không?**

Ngày nay nghe rất bình thường.

Nhưng lúc đó.

Đó là ý tưởng mang tính cách mạng.

---

# Stored Program

Ý tưởng.

Không cần:

```text
Đấu dây

↓

Đổi chương trình
```

Mà chỉ cần:

```text
Ghi chương trình vào bộ nhớ.
```

Ví dụ.

Muốn chạy:

```java
a = b + c;
```

Không cần sửa máy.

Chỉ cần thay đổi nội dung trong Memory.

---

Đây là nền tảng của:

- Windows
    
- Linux
    
- Android
    
- JVM
    
- Docker
    

Tất cả đều dựa trên ý tưởng này.

---

# Kiến trúc Von Neumann

Ông đề xuất máy tính chỉ cần 5 thành phần.

```text
          +----------------+
          |     Memory     |
          +----------------+
                 ▲
                 │
                 ▼
+-----------+    │     +-----------+
|   Input   |────┼────>|    CPU    |
+-----------+    │     +-----------+
                 │
                 ▼
          +----------------+
          |    Output      |
          +----------------+
```

Trong đó.

CPU gồm:

- ALU
    
- Control Unit
    
- Register
    

Chúng ta sẽ học chi tiết ở chương sau.

---

# Memory không chỉ chứa dữ liệu

Đây là điều quan trọng nhất.

Ví dụ.

Trong RAM.

Có thể tồn tại:

```text
Address      Nội dung

1000         15

1001         20

1002         ADD

1003         STORE

1004         PRINT
```

Điều thú vị.

CPU không biết đâu là:

- số
    
- chương trình
    

Nó chỉ đọc từng byte.

Việc byte đó được hiểu là:

- dữ liệu
    
- lệnh
    

phụ thuộc vào thời điểm CPU đọc.

---

# Đây là một cuộc cách mạng

Trước đây.

Muốn đổi chương trình.

↓

Đổi dây.

Bây giờ.

Muốn đổi chương trình.

↓

Ghi dữ liệu khác vào RAM.

Máy tính trở nên linh hoạt.

---

# Hãy so sánh

### Máy trước Von Neumann

```text
Thuật toán

↓

Đấu dây

↓

Máy chạy
```

---

### Máy Von Neumann

```text
Thuật toán

↓

File chương trình

↓

Memory

↓

CPU đọc

↓

Chạy
```

---

Đây là lý do.

Bạn chỉ cần:

```bash
java Main
```

Không cần mở máy.

Không cần hàn dây.

---

# CPU biết đọc từ đâu?

CPU có một thanh ghi đặc biệt.

Tên là:

```text
Program Counter (PC)
```

Ví dụ.

```text
PC = 1000
```

CPU đọc:

```text
Memory[1000]
```

Sau đó.

```text
PC++

↓

1001
```

Lại đọc.

```text
Memory[1001]
```

Lại tăng.

Cứ thế tiếp tục.

Đây chính là nền tảng của việc thực thi chương trình.

---

# Vai trò của Bus

Ở chương trước.

Ta đã biết Bus.

Bây giờ.

Nó bắt đầu có ý nghĩa.

Ví dụ.

CPU muốn đọc.

```text
Memory[100]
```

Quy trình.

```text
CPU

↓

Address Bus

↓

RAM

↓

Data Bus

↓

CPU
```

Nếu muốn ghi.

Quy trình tương tự.

Chỉ khác tín hiệu điều khiển.

---

# Một ví dụ thực tế

Giả sử.

Bạn viết.

```java
int a = 5;
```

Compiler.

↓

Machine Code.

↓

Load vào RAM.

CPU.

↓

Đọc từng instruction.

↓

Thực hiện.

Bạn không nhìn thấy.

Nhưng mọi chương trình đều đi qua quy trình này.

---

# Một điểm rất thú vị

Máy tính không chạy:

```java
if(a>b)
```

CPU cũng không chạy:

```java
System.out.println()
```

CPU chỉ đọc.

```text
11001010

00110100

01010110
```

Đó là Machine Code.

Java.

Python.

Go.

Rust.

Cuối cùng đều biến thành dạng này.

---

# Von Neumann Bottleneck

Đến đây.

Lại xuất hiện một vấn đề mới.

CPU.

↓

Rất nhanh.

RAM.

↓

Chậm hơn.

Muốn tính.

CPU luôn phải:

```text
RAM

↓

CPU

↓

RAM

↓

CPU
```

Dữ liệu phải đi qua cùng một đường truyền.

Đó gọi là:

> **Von Neumann Bottleneck**

Nói cách khác.

CPU thường không bận tính toán.

Mà bận...

**Chờ dữ liệu.**

Đây là nguyên nhân của:

- Cache
    
- Prefetch
    
- Pipeline
    
- Branch Prediction
    
- NUMA
    
- HBM
    

Tất cả sẽ xuất hiện trong các chương sau.

---

# Góc nhìn của Backend Engineer

Kiến trúc Von Neumann không chỉ là kiến thức lịch sử.

Nó ảnh hưởng trực tiếp đến cách bạn viết phần mềm.

Ví dụ.

Bạn viết.

```java
for (...) {
    sum += array[i];
}
```

Nếu dữ liệu nằm liên tục trong RAM.

↓

CPU đọc rất nhanh.

---

Nếu dữ liệu phân tán.

↓

CPU phải chờ RAM nhiều hơn.

↓

Chương trình chậm.

Đó là lý do:

- Array thường truy cập nhanh hơn Linked List.
    
- Cache locality rất quan trọng.
    
- Database ưu tiên đọc theo Page thay vì từng byte.
    
- CPU cache ảnh hưởng mạnh đến hiệu năng.
    

Những tối ưu này đều bắt nguồn từ việc CPU và bộ nhớ là hai thành phần tách biệt, phải giao tiếp qua bus.

---

# Một góc nhìn sâu hơn

Đây là một trong những ý tưởng đẹp nhất của Computer Science.

> **Chương trình cũng chỉ là dữ liệu.**

Một file:

```text
hello.exe
```

Không khác bản chất so với:

```text
photo.jpg
```

Hay:

```text
report.pdf
```

Tất cả đều là các dãy bit.

Điều khác biệt duy nhất là:

**Ai đọc chúng và đọc theo quy tắc nào.**

- CPU đọc `.exe` như các lệnh.
    
- Trình xem ảnh đọc `.jpg` theo chuẩn JPEG.
    
- Trình đọc PDF đọc `.pdf` theo chuẩn PDF.
    

Đây là ví dụ điển hình của nguyên lý **"data + interpretation"** trong khoa học máy tính.

---

# Tổng kết chương

Von Neumann không phát minh ra transistor.

Không phát minh CPU.

Ông đưa ra một ý tưởng quan trọng hơn.

> **Lưu chương trình trong bộ nhớ giống như dữ liệu.**

Ý tưởng này biến máy tính từ một cỗ máy chuyên dụng thành một **máy tính đa dụng**, có thể chạy vô số chương trình khác nhau mà không cần thay đổi phần cứng.

Đến ngày nay, phần lớn máy tính cá nhân, máy chủ và điện thoại vẫn dựa trên mô hình này, dù đã có nhiều cải tiến.

---

# Chuẩn bị cho chương tiếp theo

Đến đây, chúng ta đã biết:

- Chương trình nằm trong bộ nhớ.
    
- CPU đọc chương trình từ bộ nhớ.
    
- Program Counter xác định lệnh tiếp theo.
    

Nhưng vẫn còn một câu hỏi quan trọng:

> **CPU đọc một chuỗi bit như thế nào để biết đó là lệnh cộng, lệnh nhảy hay lệnh đọc bộ nhớ?**

Đó sẽ là nội dung của **Chương 9 – Chu kỳ Fetch → Decode → Execute**, nơi chúng ta đi vào "nhịp tim" của CPU: mỗi chu kỳ xung nhịp, CPU lấy lệnh, giải mã và thực thi ra sao. Hiểu chương này sẽ giúp bạn thấy rõ điều gì thực sự xảy ra khi bạn nhấn nút **Run** trong IDE.