Mình nghĩ hướng bạn đang đi là **đúng hơn rất nhiều** so với đa số tài liệu System Design trên mạng.

Phần lớn tài liệu hiện nay dạy theo kiểu:

> Cache là gì?
> 
> Queue là gì?
> 
> Redis là gì?
> 
> Kafka là gì?

Nhưng khi đi làm hoặc phỏng vấn Senior thì người ta suy nghĩ hoàn toàn ngược lại.

Họ không nhìn theo **công nghệ**.

Họ nhìn theo **vấn đề (Problem Driven Design).**

Nghĩa là:

> Hệ thống đang gặp vấn đề gì?
> 
> Bottleneck ở đâu?
> 
> Công nghệ nào sinh ra để giải quyết đúng vấn đề đó?
> 
> Sau khi giải quyết xong thì vấn đề mới phát sinh là gì?

Đây mới chính là "bức tranh toàn cảnh".

---

# Mình sẽ viết tài liệu theo cấu trúc này

Đây là cấu trúc mình nghĩ đủ tổng quan để sau này học bất kỳ công nghệ nào cũng có thể đặt nó đúng vị trí.

---

# Chương 1. Triết lý chung của System Design

## Không tồn tại công nghệ hoàn hảo

Điều đầu tiên cần hiểu là:

> **Không có công nghệ nào sinh ra để giải quyết mọi vấn đề.**

Mỗi công nghệ chỉ được tạo ra để giải quyết **một hoặc một nhóm nhỏ vấn đề**.

Ví dụ

Redis

không sinh ra để thay Database.

Kafka

không sinh ra để thay Database.

CDN

không sinh ra để thay Backend.

Kubernetes

không sinh ra để tăng tốc chương trình.

Firewall

không sinh ra để chống nghẽn Database.

Mỗi công nghệ đều có:

- phạm vi hoạt động
    
- ưu điểm
    
- nhược điểm
    
- giới hạn
    
- chi phí
    
- tradeoff
    

Nếu sử dụng sai bài toán thì đôi khi còn làm hệ thống tệ hơn.

---

## Không có hệ thống vô hạn

Không tồn tại hệ thống nào:

- chịu vô hạn request
    
- chịu vô hạn user
    
- chịu vô hạn upload
    
- chịu vô hạn memory
    
- chịu vô hạn CPU
    

Mọi thành phần đều có giới hạn vật lý:

- CPU
    
- RAM
    
- SSD
    
- Network
    
- Disk IOPS
    
- File Descriptor
    
- Socket
    
- Thread
    
- Connection
    
- Power
    

Cho nên:

**System Design thực chất là nghệ thuật quản lý giới hạn.**

---

# Chương 2. Hệ thống thực chất đang làm gì?

Dù hệ thống lớn đến đâu.

Cuối cùng nó chỉ đang làm vài việc.

```text
User

↓

Gửi request

↓

Xử lý

↓

Đọc dữ liệu

↓

Tính toán

↓

Ghi dữ liệu

↓

Trả kết quả
```

Toàn bộ hàng trăm công nghệ hiện nay đều chỉ tối ưu một hoặc nhiều bước trong chuỗi này.

---

# Chương 3. Vì sao hệ thống bị sập?

Hệ thống chỉ sập khi

```text
Tốc độ sinh công việc

>

Tốc độ xử lý công việc
```

Đây là quy luật quan trọng nhất.

Ví dụ

Worker

xử lý

```text
100 jobs/s
```

Developer tạo

```text
1000 jobs/s
```

Thì

Queue tăng.

Không liên quan Queue.

Queue chỉ phản ánh hệ thống đang quá tải.

---

Ví dụ

Database

ghi

```text
5000 writes/s
```

API

gửi

```text
20000 writes/s
```

DB chết.

Không phải lỗi DB.

Mà đầu vào vượt khả năng xử lý.

---

Cho nên

**Mọi bottleneck đều xuất phát từ mất cân bằng giữa Input và Processing Capacity.**

---

# Chương 4. Mô hình chung của một hệ thống lớn

Một hệ thống lớn có thể biểu diễn như sau

```text
Internet

↓

DNS

↓

CDN

↓

Firewall

↓

WAF

↓

Load Balancer

↓

Rate Limiter

↓

API Gateway

↓

Authentication

↓

Application

↓

Cache

↓

Queue

↓

Workers

↓

Database

↓

Object Storage

↓

Search

↓

Analytics

↓

Monitoring
```

Đây chỉ là mô hình logic.

Không phải hệ thống nào cũng cần đầy đủ.

---

# Chương 5. Vai trò của từng tầng

Đây là phần mình nghĩ quan trọng nhất.

Không học theo công nghệ.

Mà học theo:

## Thành phần này bảo vệ cái gì?

Ví dụ

---

### CDN

Giải quyết

- giảm tải Origin
    
- giảm latency
    

Không giải quyết

- Database
    
- Business Logic
    

Bảo vệ

Origin Server

Giới hạn

không cache được dữ liệu động.

---

### Firewall

Giải quyết

- truy cập trái phép
    

Bảo vệ

Toàn bộ hệ thống.

Giới hạn

Không hiểu business.

---

### WAF

Giải quyết

- SQL Injection
    
- XSS
    

Giới hạn

Không biết logic nghiệp vụ.

---

### Load Balancer

Giải quyết

- phân phối request
    

Bảo vệ

Application Server

Giới hạn

Không sinh thêm CPU.

---

### Rate Limiter

Giải quyết

- spam
    
- DDOS mức ứng dụng
    
- bug client
    

Bảo vệ

API

Queue

DB

Giới hạn

Không tăng throughput.

---

### Cache

Giải quyết

Giảm số lần đọc Database.

Bảo vệ

Database.

Giới hạn

Không giúp Write.

Có cache invalidation.

---

### Queue

Giải quyết

Làm mượt lưu lượng.

Bảo vệ

Worker

Database

Giới hạn

Không xử lý nhanh hơn.

---

### Worker

Giải quyết

Tách xử lý nặng.

Giới hạn

CPU vẫn hữu hạn.

---

### Database

Giải quyết

Lưu dữ liệu.

Giới hạn

Khó scale Write.

---

### Monitoring

Giải quyết

Phát hiện lỗi.

Không sửa lỗi.

---

# Chương 6. Mỗi thành phần đều có giới hạn

Đây là phần rất nhiều tài liệu không nói.

Ví dụ

Queue

giới hạn

- RAM
    
- Disk
    
- Queue Length
    
- Throughput
    
- Consumer
    

Redis

giới hạn

- RAM
    

Database

giới hạn

- Connection
    
- IOPS
    
- Lock
    
- CPU
    

Worker

giới hạn

- CPU
    
- Thread
    
- Memory
    

Load Balancer

giới hạn

- Connection
    

CDN

giới hạn

- chỉ cache static
    

Không có gì vô hạn.

---

# Chương 7. Vậy ai bảo vệ các thành phần đó?

Đây là chỗ rất hay.

Ví dụ

Database

được bảo vệ bởi

- Cache
    
- Queue
    
- Read Replica
    
- Connection Pool
    
- Rate Limit
    

---

Queue

được bảo vệ bởi

- Rate Limit
    
- Producer Validation
    
- Max Queue Length
    
- Dead Letter Queue
    
- Monitoring
    

---

Worker

được bảo vệ bởi

- Autoscaling
    
- Queue
    
- Circuit Breaker
    

---

Redis

được bảo vệ bởi

- TTL
    
- Eviction Policy
    
- Memory Limit
    

---

Không có thành phần nào đứng một mình.

---

# Chương 8. Không có kỹ thuật nào miễn phí

Mỗi công nghệ đều đánh đổi.

Ví dụ

Cache

Được

- nhanh
    

Mất

- đồng bộ dữ liệu
    

---

Queue

Được

- chống spike
    

Mất

- tăng latency
    

---

Sharding

Được

- scale write
    

Mất

- query phức tạp
    

---

Replication

Được

- HA
    

Mất

- replication delay
    

---

Batch

Được

- throughput
    

Mất

- latency
    

---

Compression

Được

- giảm bandwidth
    

Mất

- CPU
    

---

Không tồn tại công nghệ chỉ có ưu điểm.

---

# Chương 9. Cách chọn giải pháp

Đây là điều Senior luôn làm.

Không phải

"Tôi thích Redis."

Mà

```text
Xác định vấn đề

↓

Xác định bottleneck

↓

Xác định mục tiêu

↓

Liệt kê các giải pháp

↓

So sánh tradeoff

↓

Chọn giải pháp phù hợp nhất
```

Ví dụ

DB Read cao.

Có thể dùng

- Cache
    
- Read Replica
    
- Materialized View
    

Không phải lúc nào Redis cũng là đáp án.

---

# Chương 10. Quy luật quan trọng nhất

Theo mình đây nên là chương cuối.

Mọi công nghệ đều tuân theo quy luật:

```text
Input

↓

Protection Layer

↓

Processing

↓

Storage

↓

Monitoring

↓

Feedback
```

Mỗi tầng

- giảm tải tầng sau
    
- bảo vệ tầng sau
    
- phát hiện lỗi tầng sau
    
- cô lập lỗi tầng sau
    

Đây là triết lý của mọi hệ thống lớn.

---

# Điều mình muốn bổ sung để tài liệu đạt "độ sâu"

Có một điểm mà hầu như rất ít sách System Design viết rõ, nhưng theo mình đây mới là "sợi chỉ đỏ" xuyên suốt toàn bộ tài liệu.

Thay vì học:

> Cache → Queue → Kafka → Redis → Sharding

Hãy học theo vòng đời của **một request**.

Ví dụ:

```text
User Click

↓

Internet

↓

DNS

↓

CDN

↓

Firewall

↓

WAF

↓

Rate Limiter

↓

Load Balancer

↓

API Gateway

↓

Authentication

↓

Business Logic

↓

Cache?

↓

Queue?

↓

Worker?

↓

Database?

↓

Storage?

↓

Monitoring

↓

Response
```

Tại **mỗi bước**, hãy luôn trả lời cùng một bộ câu hỏi:

1. Thành phần này tồn tại để giải quyết vấn đề gì?
    
2. Nếu bỏ nó đi thì hệ thống sẽ gặp rủi ro gì?
    
3. Giới hạn của nó là gì?
    
4. Điều gì sẽ xảy ra khi nó quá tải hoặc hỏng?
    
5. Thành phần nào đang bảo vệ nó?
    
6. Nó đang bảo vệ thành phần nào phía sau?
    
7. Khi nào nên dùng và khi nào không nên dùng?
    
8. Trade-off khi thêm nó vào hệ thống là gì?
    
9. Có giải pháp thay thế nào không?
    

Nếu bạn duy trì cùng một bộ câu hỏi cho **mọi thành phần**, thì sau khi học xong bạn sẽ không còn nhớ rời rạc "Redis để cache", "Kafka để queue", "Nginx để reverse proxy". Thay vào đó, bạn sẽ nhìn thấy **một bức tranh thống nhất**: mọi công nghệ đều chỉ là những mắt xích trong chuỗi xử lý một request, mỗi mắt xích sinh ra để giải quyết một loại bottleneck, đồng thời lại tạo ra những giới hạn và đánh đổi mới cần được các lớp khác bảo vệ. Theo mình, đây là cách tư duy gần với kiến trúc sư hệ thống nhất và cũng là nền tảng rất vững để nghiên cứu sâu từng công nghệ sau này.