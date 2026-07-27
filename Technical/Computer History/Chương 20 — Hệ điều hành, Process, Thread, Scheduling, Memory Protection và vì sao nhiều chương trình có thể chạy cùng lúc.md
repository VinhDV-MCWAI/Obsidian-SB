Ở chương trước, chúng ta đã đi hết hành trình của một chương trình:

> Source Code → Token → AST → Compiler / Interpreter → Binary → CPU thực thi.

Nhưng đến đây lại xuất hiện một câu hỏi mới.

Giả sử bạn viết xong một chương trình.

Máy tính có thể chạy nó.

Vậy...

- Ai nạp chương trình vào RAM?
    
- Ai cấp phát bộ nhớ?
    
- Ai quyết định chương trình nào được chạy trước?
    
- Ai ngăn chương trình A đọc dữ liệu của chương trình B?
    
- Ai quản lý chuột, bàn phím, màn hình, ổ cứng, mạng?
    
- Làm sao vừa nghe nhạc, vừa mở Chrome, vừa code trong VS Code, vừa tải file?
    

CPU chỉ biết thực thi lệnh.

Nó không biết:

- chương trình nào quan trọng hơn
    
- chương trình nào thuộc người dùng nào
    
- tiến trình nào đang treo
    
- ứng dụng nào được phép truy cập camera
    

Đó là lý do **Hệ điều hành (Operating System - OS)** ra đời.

Nếu CPU là "bộ não tính toán", thì hệ điều hành là **người quản lý toàn bộ thành phố máy tính**.

---

# 1. Cuộc sống trước khi có hệ điều hành

Những máy tính đầu tiên gần như không có hệ điều hành.

Muốn chạy chương trình phải:

- nạp chương trình bằng tay
    
- thiết lập địa chỉ bộ nhớ
    
- cấu hình thiết bị
    
- chạy xong mới nạp chương trình khác
    

Máy chỉ chạy được:

```
Một chương trình

↓

Kết thúc

↓

Chạy chương trình tiếp theo
```

CPU thường xuyên rảnh.

Thiết bị ngoại vi thường xuyên chờ.

Hiệu suất cực kỳ thấp.

---

# 2. Vấn đề đầu tiên

Giả sử có ba chương trình:

```
A
B
C
```

Nếu A đang đọc ổ cứng.

CPU phải làm gì?

Nếu không có hệ điều hành.

CPU sẽ...

```
Đứng chờ.
```

Trong hàng triệu chu kỳ clock.

Đó là sự lãng phí khổng lồ.

Người ta nhận ra:

> CPU quá đắt để chỉ ngồi chờ thiết bị.

---

# 3. Ý tưởng của hệ điều hành

Người ta nghĩ:

"Nếu A đang chờ ổ cứng..."

Tại sao không cho B chạy?

```
CPU

↓

A chờ Disk

↓

CPU chuyển sang B

↓

B chờ Network

↓

CPU chuyển sang C

↓

C chạy

↓

Disk xong

↓

Quay lại A
```

CPU gần như luôn bận.

Đây chính là tư tưởng đầu tiên của **đa nhiệm (multitasking).**

---

# 4. Hệ điều hành là gì?

Hệ điều hành là lớp phần mềm nằm giữa:

```
Application

↓

Operating System

↓

Hardware
```

Ứng dụng không nói chuyện trực tiếp với:

- CPU
    
- RAM
    
- SSD
    
- Card mạng
    
- GPU
    

Mà thông qua hệ điều hành.

OS đóng vai trò:

- quản lý tài nguyên
    
- phân phối tài nguyên
    
- bảo vệ tài nguyên
    
- điều phối tài nguyên
    

---

# 5. Tài nguyên (Resource)

Trong máy tính có rất nhiều tài nguyên:

- CPU
    
- RAM
    
- Disk
    
- Network
    
- USB
    
- GPU
    
- File
    
- Socket
    
- Printer
    

Tất cả đều hữu hạn.

OS phải quyết định:

"Ai được dùng?"

"Bao lâu?"

"Khi nào thu hồi?"

Đây là bài toán quản lý tài nguyên.

---

# 6. Process ra đời

Nếu chỉ có:

```
Program
```

thì chưa đủ.

Một file:

```
chrome.exe
```

có thể mở:

```
10 cửa sổ Chrome.
```

Đó không còn là một chương trình.

Đó là nhiều **Process**.

---

# 7. Program và Process khác nhau thế nào?

Program chỉ là:

```
File trên ổ cứng.
```

Ví dụ:

```
notepad.exe
```

Nó chưa chạy.

Process là:

```
Một chương trình đang chạy.
```

Có:

- RAM riêng
    
- Stack riêng
    
- Heap riêng
    
- Register riêng
    
- PID
    
- File đang mở
    
- Socket
    

Một Program có thể sinh ra hàng trăm Process.

---

# 8. Vì sao Process cần bộ nhớ riêng?

Giả sử:

Chrome

đọc password.

Calculator

đọc password.

Nếu dùng chung RAM.

Điều gì xảy ra?

Calculator có thể đọc:

```
Cookie

Password

Token
```

của Chrome.

Đó là thảm họa bảo mật.

Nên mỗi Process phải có:

```
Virtual Address Space
```

riêng.

---

# 9. Virtual Memory

Ở chương 11 chúng ta đã học:

RAM có địa chỉ.

Nhưng Process không nhìn thấy RAM thật.

Nó nhìn thấy:

```
Virtual Memory
```

Mỗi Process đều nghĩ rằng:

"Mình sở hữu một không gian địa chỉ riêng."

Thực tế.

OS ánh xạ:

```
Virtual Address

↓

Physical Address
```

thông qua MMU (Memory Management Unit).

Đây là một trong những phát minh quan trọng nhất của hệ điều hành hiện đại.

---

# 10. Memory Protection

Nếu không có Memory Protection.

```
Program A
```

có thể:

```
Write

↓

RAM

↓

Program B
```

Hoặc:

```
Delete

↓

OS
```

Máy sẽ sập.

OS dùng:

- MMU
    
- Page Table
    
- Permission
    

để ngăn điều đó.

Đây là lý do:

Một ứng dụng crash.

Không kéo sập cả hệ điều hành.

---

# 11. Process Context

Một Process đang chạy gồm:

- Program Counter
    
- Register
    
- Stack
    
- Heap
    
- Open File
    
- Socket
    
- Signal
    
- Environment Variable
    

Đó gọi là:

```
Execution Context
```

OS phải lưu toàn bộ thông tin này khi chuyển sang Process khác.

---

# 12. Context Switch

Giả sử:

```
Chrome

↓

VSCode

↓

Spotify
```

CPU chỉ có:

```
1 Core
```

Làm sao ba ứng dụng cùng chạy?

OS làm:

```
Save Context

↓

Load Context

↓

Save

↓

Load

↓

Save

↓

Load
```

rất nhanh.

Đó gọi là:

```
Context Switch
```

CPU thực chất chỉ chạy:

```
Một Thread

Một thời điểm.
```

Nhưng chuyển đổi đủ nhanh để con người có cảm giác mọi thứ diễn ra đồng thời.

---

# 13. Thread ra đời

Sau vài năm.

Người ta lại gặp vấn đề.

Một Process:

```
Chrome
```

vừa cần:

- render UI
    
- tải hình
    
- tải JavaScript
    
- phát video
    
- đọc ổ cứng
    

Nếu tất cả đều nằm trong một luồng.

Chỉ cần:

```
Download File
```

chậm.

Toàn bộ cửa sổ sẽ bị treo.

---

# 14. Thread là gì?

Thread là đơn vị thực thi nhỏ nhất được hệ điều hành lập lịch.

Một Process có thể có:

```
1 Thread

↓

2 Thread

↓

10 Thread

↓

1000 Thread
```

Các Thread:

- dùng chung Code
    
- dùng chung Heap
    
- dùng chung File
    
- dùng chung Socket
    

Nhưng có:

- Stack riêng
    
- Register riêng
    
- Program Counter riêng
    

---

# 15. Vì sao Thread nhanh hơn Process?

Tạo Process mới cần:

- cấp không gian địa chỉ
    
- page table
    
- nhiều cấu trúc quản lý
    

Trong khi Thread chỉ cần:

- stack
    
- context thực thi
    

Do đó:

Context Switch giữa Thread trong cùng Process thường nhẹ hơn giữa hai Process khác nhau.

---

# 16. Scheduler

Nếu có:

```
1000 Thread
```

CPU chỉ có:

```
8 Core
```

Ai quyết định:

Thread nào chạy?

Đó là:

```
Scheduler
```

Scheduler là thành phần của hệ điều hành chịu trách nhiệm phân phối thời gian CPU.

Nó cố gắng cân bằng:

- tính công bằng
    
- độ ưu tiên
    
- độ phản hồi
    
- hiệu suất tổng thể
    

Đây là một bài toán rất phức tạp và là lĩnh vực nghiên cứu riêng trong hệ điều hành.

---

# 17. Time Slice

Scheduler thường không để một Thread chạy mãi.

Nó cấp cho mỗi Thread một khoảng thời gian rất ngắn gọi là **time slice** (hay time quantum).

Ví dụ:

```
Thread A → 5 ms

↓

Thread B → 5 ms

↓

Thread C → 5 ms
```

Sau khi hết lượt, CPU chuyển sang Thread khác.

Nhờ tốc độ chuyển đổi rất nhanh, người dùng cảm thấy nhiều chương trình chạy đồng thời.

---

# 18. CPU nhiều nhân thay đổi điều gì?

Trước đây:

```
1 CPU

↓

1 Core
```

Ngày nay:

```
CPU

↓

8 Core

↓

16 Core

↓

32 Core
```

Lúc này:

Nhiều Thread thực sự có thể chạy song song trên các nhân khác nhau.

Đây là sự khác biệt giữa:

- **Concurrency**: nhiều công việc cùng được quản lý và xen kẽ thực thi.
    
- **Parallelism**: nhiều công việc thực sự chạy cùng lúc trên các nhân khác nhau.
    

Hai khái niệm này thường bị nhầm lẫn nhưng không giống nhau.

---

# 19. System Call

Ứng dụng không được phép trực tiếp:

- đọc ổ cứng
    
- tạo Process
    
- mở Socket
    
- ghi File
    
- cấp phát vùng nhớ đặc quyền
    

Thay vào đó, ứng dụng yêu cầu hệ điều hành thông qua **System Call**.

Ví dụ:

```
Application

↓

read()

↓

Operating System

↓

SSD
```

System Call giống như quầy tiếp nhận yêu cầu của chính quyền.

Ứng dụng không tự ý sử dụng tài nguyên, mà phải xin phép hệ điều hành.

---

# 20. User Mode và Kernel Mode

Để bảo vệ hệ thống, CPU hoạt động ở hai chế độ chính:

## User Mode

Ứng dụng bình thường chạy ở đây.

Quyền hạn bị giới hạn.

Không thể trực tiếp:

- điều khiển thiết bị
    
- truy cập vùng nhớ đặc quyền
    
- thực hiện nhiều lệnh nhạy cảm
    

## Kernel Mode

Đây là chế độ của hệ điều hành.

Có quyền truy cập gần như toàn bộ phần cứng.

Khi một ứng dụng gọi System Call, CPU sẽ chuyển từ User Mode sang Kernel Mode để hệ điều hành thực hiện công việc thay mặt ứng dụng.

Đây là nền tảng của cơ chế bảo mật hiện đại.

---

# 21. Deadlock – Khi các chương trình chờ nhau mãi mãi

Khi có nhiều Thread hoặc Process cùng chia sẻ tài nguyên, một vấn đề mới xuất hiện.

Ví dụ:

- Thread A giữ khóa của Database và chờ File.
    
- Thread B giữ File và chờ Database.
    

Cả hai đều chờ nhau.

Không ai tiếp tục được.

Đó gọi là **Deadlock**.

Deadlock không phải lỗi của CPU hay hệ điều hành.

Nó là lỗi trong cách thiết kế và phối hợp tài nguyên của chương trình.

Đây là một chủ đề rất quan trọng mà chúng ta sẽ nghiên cứu sâu hơn trong phần về lập trình đồng thời.

---

# 22. Vì sao chương này quan trọng với Backend Engineer?

Hầu hết các ứng dụng backend hiện đại đều chạy trên hệ điều hành đa nhiệm.

Khi bạn viết:

- Web Server
    
- REST API
    
- Message Queue
    
- Background Job
    
- Worker
    
- Cron Job
    

bạn đang làm việc trực tiếp với các khái niệm:

- Process
    
- Thread
    
- Scheduler
    
- Virtual Memory
    
- File Descriptor
    
- Socket
    
- System Call
    

Hiểu các khái niệm này giúp giải thích nhiều hiện tượng thực tế như:

- Vì sao CPU 100% nhưng ứng dụng vẫn chậm.
    
- Vì sao tạo quá nhiều Thread lại phản tác dụng.
    
- Vì sao Memory Leak làm hệ thống ngày càng tiêu tốn RAM.
    
- Vì sao Context Switch quá nhiều làm giảm hiệu năng.
    
- Vì sao phải giới hạn số kết nối đồng thời.
    

---

# 23. Kết luận của chương

Ở chương trước, chúng ta đã học cách biến mã nguồn thành chương trình có thể thực thi.

Nhưng một chương trình chỉ thật sự hữu ích khi có một "người quản lý" điều phối nó cùng hàng trăm chương trình khác.

Hệ điều hành chính là người quản lý đó.

Nó tạo ra các khái niệm như:

- Process
    
- Thread
    
- Virtual Memory
    
- Scheduler
    
- Memory Protection
    
- System Call
    

để biến một chiếc máy tính từ chỗ chỉ chạy được **một chương trình tại một thời điểm** thành một hệ thống có thể phục vụ hàng triệu tác vụ mỗi ngày.

Đây cũng là nền tảng để bước sang chương cuối của series, nơi mọi kiến thức trước đó hội tụ:

> **Cloud Computing, Virtualization, Container, Docker, Kubernetes, GPU, AI và kiến trúc hạ tầng hiện đại**.

Bạn sẽ thấy rằng Cloud hay Docker không phải là những công nghệ xuất hiện từ hư không, mà là kết quả tất yếu của gần một thế kỷ tiến hóa trong cách con người biểu diễn, lưu trữ, tính toán, bảo vệ và phân phối thông tin.