Queue là một trong những Data Structure cơ bản và xuất hiện rất nhiều trong hệ thống backend, OS, networking, database và distributed systems. Nếu **Stack đại diện cho LIFO (Last In First Out)** thì **Queue đại diện cho FIFO (First In First Out)**.

---

# Queue là gì?

Queue (hàng đợi) là cấu trúc dữ liệu chỉ cho phép:

- thêm phần tử ở cuối (rear/tail)
    
- lấy phần tử ở đầu (front/head)
    

Giống như xếp hàng mua vé.

```
Front                           Rear

+----+----+----+----+
| A  | B  | C  | D  |
+----+----+----+----+

enqueue(E)

+----+----+----+----+----+
| A  | B  | C  | D  | E  |
+----+----+----+----+----+

dequeue()

+----+----+----+----+
| B  | C  | D  | E  |
+----+----+----+----+
```

Luật duy nhất:

> Ai vào trước thì ra trước.

---

# Queue lưu dữ liệu trong bộ nhớ như thế nào?

Queue không quy định cách lưu.

Nó chỉ quy định **hành vi (FIFO)**.

Có nhiều cách hiện thực.

---

## Cách 1: Array

Ví dụ:

```
Memory

Index

0    1    2    3    4
+----+----+----+----+----+
| A  | B  | C  |    |    |
+----+----+----+----+----+

front = 0
rear = 2
```

enqueue(D)

```
+----+----+----+----+----+
| A  | B  | C  | D  |    |
+----+----+----+----+----+

rear = 3
```

dequeue()

không copy dữ liệu

chỉ tăng front

```
front = 1

+----+----+----+----+----+
| A  | B  | C  | D  |    |
+----+----+----+----+----+
```

A vẫn còn trong RAM.

Chỉ là queue không còn sử dụng nữa.

---

## Vấn đề nếu dùng Array đơn giản

Sau nhiều dequeue

```
front = 9000
rear = 9999
capacity = 10000
```

Mặc dù còn rất nhiều ô trống phía trước

```
[ ][ ][ ][ ][ ]..........
          ^
front
```

rear lại tới cuối mảng.

enqueue sẽ báo đầy.

---

## Circular Queue

Đây là cách phổ biến.

```
capacity = 8

      rear
       ↓
+---+---+---+---+---+---+---+---+
| G | H |   |   | C | D | E | F |
+---+---+---+---+---+---+---+---+
            ↑
          front
```

rear quay vòng về đầu.

Không cần copy.

O(1).

---

## Cách 2: Linked List

```
Head

↓

A -> B -> C -> D

             ↑

            Tail
```

enqueue

```
Tail.next = newNode

Tail = newNode
```

dequeue

```
Head = Head.next
```

Không cần dịch chuyển dữ liệu.

---

# Queue gồm những operation gì?

## enqueue()

Thêm cuối.

```
enqueue(X)

A B C

↓

A B C X
```

---

## dequeue()

Lấy đầu.

```
A B C

↓

B C
```

---

## peek()

Xem đầu queue.

Không xóa.

```
peek()

A B C

return A
```

---

## isEmpty()

```
size == 0
```

---

## size()

```
return count
```

---

## clear()

Xóa toàn bộ.

---

# Big-O

## Queue dùng LinkedList

|Operation|Complexity|
|---|---|
|enqueue|O(1)|
|dequeue|O(1)|
|peek|O(1)|
|size|O(1)|
|clear|O(n) (hoặc O(1) nếu chỉ bỏ tham chiếu, tùy ngôn ngữ/GC)|

---

## Queue dùng Circular Array

|Operation|Complexity|
|---|---|
|enqueue|O(1) amortized|
|dequeue|O(1)|
|peek|O(1)|
|random access|O(1) nếu tự truy cập theo chỉ số nội bộ, nhưng **không phải** API chuẩn của Queue|
|resize|O(n)|

---

# Vì sao enqueue/dequeue là O(1)?

enqueue

```
rear++

arr[rear]=x
```

Hai thao tác.

Không phụ thuộc n.

---

dequeue

```
front++
```

Không copy.

Không dịch chuyển.

---

Sai lầm phổ biến:

```
A B C D

↓

dequeue

B C D
```

Nhiều người nghĩ:

```
copy B
copy C
copy D
```

Thực tế queue chuẩn không làm vậy.

Chỉ tăng front.

---

# Ưu điểm

## 1. FIFO tự nhiên

Rất phù hợp cho:

- request
    
- event
    
- task
    
- packet
    

---

## 2. enqueue/dequeue O(1)

Không phải dịch chuyển dữ liệu.

---

## 3. Producer Consumer

```
Producer

↓

Queue

↓

Consumer
```

Đây là mô hình cực phổ biến.

---

## 4. Giảm coupling

Producer không cần biết consumer.

Consumer cũng không biết producer.

---

# Nhược điểm

## 1. Không truy cập ngẫu nhiên hiệu quả theo API

Không thể:

```
queue[300]
```

vì Queue không thiết kế cho random access.

---

## 2. Không tìm kiếm nhanh

```
contains(x)
```

phải duyệt.

O(n)

---

## 3. Không phù hợp khi cần insert giữa

```
A B C D

insert X

↓

A B X C D
```

Queue không hỗ trợ.

---

# Trade-off với Data Structure khác

|Data Structure|Điểm mạnh|Điểm yếu|
|---|---|---|
|Array|Random access O(1), cache locality tốt|Thêm/xóa đầu O(n)|
|Linked List|Chèn/xóa đầu/cuối O(1)|Cache locality kém, tốn bộ nhớ cho con trỏ|
|Stack|LIFO|Không FIFO|
|Queue|FIFO|Không random access|
|Deque|Thêm/xóa cả hai đầu O(1)|API phức tạp hơn Queue|
|Priority Queue|Lấy phần tử ưu tiên cao nhất|Không còn đảm bảo FIFO tuyệt đối|

---

# Queue được dùng ở đâu?

## Operating System

CPU Scheduler

```
Process

↓

Ready Queue

↓

CPU
```

---

## Printer

```
Document

↓

Print Queue

↓

Printer
```

---

## Web Server

```
HTTP Request

↓

Queue

↓

Worker
```

---

## RabbitMQ

```
Producer

↓

Queue

↓

Consumer
```

---

## Kafka

```
Producer

↓

Topic (log dạng hàng đợi tuần tự)

↓

Consumer
```

Kafka không phải Queue thuần túy (vì dữ liệu vẫn được giữ lại sau khi đọc), nhưng mô hình xử lý vẫn dựa trên thứ tự tuần tự.

---

## BFS

Breadth First Search

```
Queue

A

↓

B C

↓

D E F
```

BFS bắt buộc dùng Queue.

---

## Event Loop

JavaScript

```
Event Queue

↓

Event Loop

↓

Execute
```

---

# Các ngôn ngữ hiện thực Queue như thế nào?

Lưu ý: nhiều ngôn ngữ **không có lớp `Queue` cụ thể để lưu dữ liệu**, mà cung cấp interface và nhiều implementation khác nhau.

|Ngôn ngữ|Thư viện khuyến nghị|Hiện thực bên dưới|
|---|---|---|
|Java|`ArrayDeque`|Circular array|
|Java|`LinkedList`|Doubly Linked List|
|Java|`PriorityQueue`|Binary Heap (không FIFO)|
|C#|`Queue<T>`|Circular array|
|C++|`std::queue`|Adapter (mặc định dùng `std::deque`)|
|C++|`std::deque`|Khối bộ nhớ liên tiếp (segmented array)|
|Python|`collections.deque`|Danh sách liên kết các block (không phải linked list đơn thuần)|
|Go|Không có trong standard library|Thường dùng slice hoặc tự cài đặt ring buffer|
|Rust|`VecDeque`|Circular buffer|
|JavaScript|Không có Queue chuẩn|Thường dùng `Array` (không tối ưu với `shift()`) hoặc thư viện|
|PHP|`SplQueue`|Doubly Linked List (`SplDoublyLinkedList`)|

**Lưu ý quan trọng về Java:**

- `Queue` là **interface**.
    
- `ArrayDeque` thường là lựa chọn mặc định nhờ hiệu năng tốt hơn `LinkedList` trong đa số trường hợp.
    
- Không nên dùng `ArrayList` làm Queue vì thao tác xóa đầu (`remove(0)`) có độ phức tạp **O(n)** do phải dịch chuyển toàn bộ phần tử còn lại.
    

---

# Mối liên hệ giữa cấu trúc dữ liệu → bộ nhớ → độ phức tạp → hiệu năng → trade-off

Đây là chuỗi tư duy quan trọng trong thiết kế hệ thống:

```
Yêu cầu bài toán
        │
        ▼
Chọn Data Structure
        │
        ▼
Cách tổ chức dữ liệu trong bộ nhớ
(Array, Linked List, Circular Buffer...)
        │
        ▼
Chi phí thao tác
(Big-O)
        │
        ▼
Hiệu năng thực tế
(Cache locality, số lần cấp phát bộ nhớ,
CPU cache miss, branch prediction...)
        │
        ▼
Trade-off
```

Ví dụ với Queue:

### Queue bằng Circular Array

```
Memory

+----+----+----+----+----+
| A  | B  | C  | D  | E  |
+----+----+----+----+----+
```

- Bộ nhớ gần nhau (contiguous).
    
- Cache locality tốt.
    
- Ít cấp phát bộ nhớ.
    
- `enqueue`/`dequeue` O(1).
    
- Đôi khi phải resize (O(n), nhưng hiếm nên chi phí trung bình vẫn O(1) amortized).
    

=> Phù hợp cho hầu hết ứng dụng tổng quát.

### Queue bằng Linked List

```
A --> B --> C --> D
```

- Mỗi node cấp phát riêng.
    
- Bộ nhớ phân tán.
    
- Cache locality kém hơn.
    
- Không cần resize.
    
- `enqueue`/`dequeue` luôn O(1).
    

=> Phù hợp khi kích thước thay đổi mạnh hoặc cần chèn/xóa node mà không muốn sao chép dữ liệu.

---

# Tổng kết

| Khía cạnh          | Queue                                                                                     |
| ------------------ | ----------------------------------------------------------------------------------------- |
| Nguyên tắc         | FIFO (First In, First Out)                                                                |
| Thao tác chính     | `enqueue`, `dequeue`, `peek`                                                              |
| Hiện thực phổ biến | Circular Array, Linked List                                                               |
| `enqueue`          | O(1) (amortized với mảng động)                                                            |
| `dequeue`          | O(1)                                                                                      |
| `peek`             | O(1)                                                                                      |
| `contains`         | O(n)                                                                                      |
| Ưu điểm            | FIFO tự nhiên, xử lý luồng công việc, producer-consumer, scheduler                        |
| Nhược điểm         | Không hỗ trợ tìm kiếm hay truy cập ngẫu nhiên theo API, không phù hợp cho chèn/xóa ở giữa |
| Ứng dụng           | BFS, scheduler, message queue, web server, event loop, networking, job processing         |













///////////////////////////////

## 1. Problems & Use Case (Vấn đề & Nhu cầu thực tế)

- **Bối cảnh & Khởi nguồn phát sinh:** Trong kiến trúc monolithic truyền thống hoặc các luồng xử lý đồng bộ (Synchronous), khi Client gửi một Request, Server phải thực hiện toàn bộ các tác vụ liên quan từ tính toán, ghi DB cho đến gọi Third-party service trước khi trả về Response. Nếu hệ thống gặp tình trạng traffic tăng đột biến (Spike load), Server sẽ cạn kiệt tài nguyên (CPU, RAM, Connection Pool) do phải giữ kết nối chờ xử lý. Nguyên nhân sâu xa là sự phụ thuộc chặt chẽ (Tight Coupling) về mặt thời gian và hiệu năng giữa luồng nhận Request và luồng xử lý nghiệp vụ, dẫn đến việc thắt nút cổ chai tại các tác vụ tốn tài nguyên hoặc có độ trễ cao (I/O bound, CPU bound). Ngoài ra, nếu không có queue hệ thống thường gặp các vấn đề:
	- Producer tạo công việc nhanh hơn khả năng xử lý của Consumer.
	- Các thành phần phụ thuộc trực tiếp vào nhau gây coupling cao.
	- Hệ thống không có cơ chế đệm (buffer) để hấp thụ tải.
	- Một service chậm hoặc tạm thời ngừng hoạt động có thể làm lan truyền lỗi sang các service khác.
	- Khó triển khai retry, rate limiting, backpressure và asynchronous processing.

- Nguyên nhân sâu xa:
	- Tốc độ sinh dữ liệu và tốc độ xử lý dữ liệu không đồng đều.
	- Các hệ thống phân tán luôn tồn tại độ trễ mạng, lỗi tạm thời và giới hạn tài nguyên.
	- Producer và Consumer có vòng đời, năng lực xử lý và yêu cầu mở rộng khác nhau.
    
- **Mục đích cốt lõi:** Giải pháp này sinh ra để chuyển đổi mô hình tương tác từ đồng bộ sang bất đồng bộ (Asynchronous processing) và làm phẳng biểu đồ tải (Load Leveling). Tách Producer và Consumer khỏi nhau. Cho phép giải phóng luồng xử lý của Server ngay lập tức, Làm bộ đệm giữa tốc độ ghi và tốc độ xử lý. Hỗ trợ xử lý bất đồng bộ. Hấp thụ traffic spike. Tăng khả năng chịu lỗi của hệ thống. Cân bằng tải công việc giữa nhiều [[Worker]].
    

## 2. Definition & Implementation (Bản chất & Cách hoạt động)

- **Nó là gì?** Queue (Hàng đợi) về mặt cấu trúc dữ liệu là một danh sách trừu tượng hoạt động theo nguyên lý **FIFO (First In, First Out)** - phần tử vào trước sẽ được xử lý trước. Trong kiến trúc hệ thống, Queue đóng vai trò là một Message Broker hoặc Vùng đệm trung gian (Buffer) lưu trữ tạm thời các thông điệp dữ liệu giữa bên gửi và bên nhận theo thứ tự để consumer xử lý sau. Nó không xử lý công việc, nó chỉ nhận lưu và phân phối dữ liệu. Việc xử lý thuộc về consumer or [[Worker]]. Sẽ có nhiều vấn đề cần xử lý và không toàn vẹn khi có dự định quản lý tính toán queue thủ công như: lock, concurrency, retry,... hầu hết trong các ứng dụng công nghệ hỗ trợ các việc này rồi. Khi sử dụng chỉ cầnL định nghĩa mỗi job sẽ thực hiện action gì, đẩy job vào queue (tùy theo quy tắc từng công nghệ), chạy [[Worker]] or cái gì đó khác.
    
- **Dùng cho việc gì?** * Xử lý tác vụ nền (Background Jobs): Gửi email marketing, render video, xuất báo cáo Excel nặng.
    
    - Tránh nghẽn mạch hệ thống (Throttling / Rate Limiting): Đệm các request mua hàng trong sự kiện Flash Sale.
        
    - Đồng bộ hóa dữ liệu bất đồng bộ: Đẩy log hệ thống về trung tâm xử lý, đồng bộ dữ liệu từ DB sang Elasticsearch.
        
    - Giao tiếp giữa các Microservices: Truyền thông tin sự kiện (Event-driven) mà không cần các dịch vụ phải gọi trực tiếp (HTTP/gRPC) lẫn nhau.
        
- **Sử dụng thế nào?** Luồng hoạt động tiêu chuẩn gồm 3 thành phần chính tương tác qua các bước sau:
    
    1. **Producer (Bên gửi):** Nhận request từ Client, đóng gói dữ liệu thành một Message (JSON, Protobuf...) và đẩy (Publish) vào Queue. Producer ngay lập tức trả về phản hồi thành công cho Client mà không đợi tác vụ hoàn thành.
        
    2. **Queue (Hàng đợi trung gian):** Tiếp nhận, phân loại và lưu trữ Message an toàn (trên RAM hoặc Disk tùy cấu hình).
        
    3. **Consumer (Bên nhận):** Các [[Worker]] process chủ động kéo (Pull) hoặc được phân phối (Push) Message từ Queue về. Consumer xử lý tuần tự hoặc song song các Message này. Sau khi xử lý xong, Consumer gửi tín hiệu xác nhận (Acknowledge - ACK) để Queue xóa bỏ Message khỏi hệ thống.
        

## 3. Evaluation & Trade-offs (Ưu nhược điểm & Sự đánh đổi)

- **Ưu điểm (Advantages):**
    
    - **Loosely Coupled:** Các dịch vụ không cần biết sự tồn tại của nhau, chỉ cần giao tiếp qua cấu trúc Message chuẩn hóa.
        
    - **Scalability:** Dễ dàng scale up/scale out số lượng [[Worker]] (Consumer) để tăng tốc độ xử lý khi hàng đợi bị dồn ứ.
        
    - **Resiliency (Tính đàn hồi):** Nếu Consumer bị sập, Message vẫn nằm an toàn trong Queue và sẽ được xử lý lại khi Consumer hoạt động trở lại.
        
- **Nhược điểm (Disadvantages):**
    
    - **Tăng độ phức tạp kiến trúc:** Đòi hỏi phải vận hành, giám sát thêm một hệ thống trung gian (Broker). Phát sinh các bài toán như trùng lặp message (Duplicate), mất message, hoặc lệch thứ tự xử lý.
        
    - **Mất tính trực quan khi Debug:** Luồng dữ liệu bị chia cắt khiến việc tracking log end-to-end qua các dịch vụ trở nên khó khăn hơn (cần áp dụng Distributed Tracing).
        
- **Khi nào NÊN dùng? (When to use):**
    
    - Tác vụ không cần trả về kết quả ngay lập tức cho User (ví dụ: tạo hóa đơn PDF sau khi thanh toán thành công).
        
    - Hệ thống có các đỉnh lưu lượng (Traffic peaks) diễn ra trong thời gian ngắn và có thể dự đoán trước.
        
    - Cần tích hợp với các hệ thống bên thứ ba có tốc độ phản hồi chậm hoặc không ổn định.
        
- **Khi nào KHÔNG NÊN dùng? (When NOT to use):**
    
    - Các luồng xử lý yêu cầu kết quả tức thì (Real-time / Synchronous) để hiển thị lên giao diện (ví dụ: Xác thực tài khoản đăng nhập, truy vấn số dư tài khoản ngân hàng hiện tại).
        
    - Hệ thống quá đơn giản, lượng truy cập thấp; việc áp dụng Queue chỉ làm tăng chi phí vận hành vô ích.
        
- **Sự đánh đổi (Trade-offs):**
    
    - **Đổi tính nhất quán tức thì (Strong Consistency) lấy tính nhất quán sau cùng (Eventual Consistency):** Hệ thống chấp nhận việc dữ liệu chưa được cập nhật ngay lập tức trên toàn bộ các view, nhưng đảm bảo mọi tác vụ đều sẽ được thực thi hoàn tất vào một thời điểm trong tương lai gần.
        
    - **Đổi độ trễ (Latency) lấy tính ổn định (Throughput):** Thời gian hoàn thành toàn bộ tác vụ của một request cá lẻ có thể lâu hơn (do mất thời gian xếp hàng), nhưng tổng thể hệ thống chịu được lượng request lớn hơn gấp nhiều lần mà không bị sập.
        

## 4. Variants & Alternatives (Biến thể & Giải pháp thay thế)

- **Các biến thể (Variants):**
    
    - **Priority Queue:** Hàng đợi có độ ưu tiên, các message quan trọng được đưa lên đầu để Consumer xử lý trước bất kể thời gian vào.
        
    - **Delay / Scheduled Queue:** Giữ message lại trong Queue và chỉ phân phối cho Consumer sau một khoảng thời gian thiết lập trước (ví dụ: Hủy đơn hàng nếu không thanh toán sau 15 phút).
        
    - **Publish/Subscribe (Pub/Sub):** Biến thể một message gửi đi có thể được copy và phân phối đồng thời đến nhiều Queue/Consumer khác nhau thông qua các Topic/Exchange (ví dụ: RabbitMQ Exchange, Apache Kafka).
        
    - **Dead Letter Queue (DLQ):** Hàng đợi đặc biệt chứa các message bị lỗi cấu trúc hoặc Consumer xử lý thất bại quá số lần quy định, phục vụ mục đích debug và cô lập lỗi.
        
- **Giải pháp thay thế (Alternatives):**
    
    - **Database Polling:** Lưu tác vụ vào một bảng trong DB với trạng thái `PENDING`, dùng một Cron job quét định kỳ để xử lý. _Khác biệt:_ Hiệu năng thấp, gây khóa bảng (Locking) và không tối ưu cho hệ thống high-throughput.
        
    - **gRPC / Webhook (Direct HTTP Calls):** Gọi trực tiếp giữa các service theo mô hình Non-blocking I/O. _Khác biệt:_ Vẫn duy trì kết nối trực tiếp, nếu service nhận bị sập thì dữ liệu request có nguy cơ bị mất hoàn toàn nếu service gửi không triển khai cơ chế retry phức tạp.
        

## 5. Limits & Scalability (Giới hạn vật lý & Khả năng mở rộng)

- **Giới hạn kỹ thuật (Limits):**
    
    - **Disk/Memory Overload:** Nếu tốc độ Producer đẩy vào nhanh hơn tốc độ Consumer xử lý trong một thời gian dài, Queue sẽ bị đầy bộ nhớ. Tùy cấu hình, Broker sẽ bắt đầu từ chối message mới (Drop) hoặc bị sập nguồn (Crash).
        
    - **Network Bandwidth:** Băng thông mạng tại node chứa Message Broker là điểm nghẽn vật lý tối thượng khi kích thước message quá lớn hoặc số lượng kết nối từ [[Worker]] quá dày đặc.
        
- **Ngưỡng chịu tải (Scale):**
    
    - **Số lượng User / Requests:** * Các giải pháp In-memory / Light-weight (như Redis Pub/Sub, BullMQ): Đạt ngưỡng từ **10,000 đến 100,000 ops/sec** trên một single node nhờ tối ưu hóa RAM.
        
        - Các Enterprise Broker chuyên dụng (như RabbitMQ): Xử lý ổn định từ **20,000 đến 50,000 msgs/sec** tùy thuộc vào chế độ cấu hình lưu dữ liệu (Persistent vs Transient).
            
        - Hệ thống phân tán dạng Log-structured (như Apache Kafka, Pulsar): Có thể scale out theo chiều ngang (Horizontal) bằng Partition để đạt ngưỡng **hàng triệu đến hàng chục triệu msgs/sec** đồng thời.
            
    - **Dữ liệu & Tốc độ Đọc/Ghi (Read/Write rate):** Phụ thuộc chặt chẽ vào cấu hình Disk I/O. Nếu bật chế độ Acknowledge và Write-to-disk (Persistent) để chống mất dữ liệu khi mất điện, tốc độ ghi sẽ bị giới hạn bởi tốc độ của ổ cứng (SSD/NVMe). Nếu chạy thuần trên RAM, tốc độ xử lý tiệm cận tốc độ bus của phần cứng.
        
    - **Tốc độ Download/Upload:** Thường khuyến nghị kích thước của một Message tối ưu dưới **1MB** (tốt nhất là vài KB). Message quá lớn (như đính kèm file binary trực tiếp vào queue) sẽ làm cạn kiệt băng thông card mạng của Broker vô cùng nhanh chóng; thay vào đó, kiến trúc chuẩn là lưu file vào Object Storage (S3) và chỉ truyền Link URL qua Queue.