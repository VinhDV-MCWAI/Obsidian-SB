# Redis từ nền tảng đến chuyên sâu

## Tài liệu học tập, phân tích kiến trúc và ứng dụng thực tế cho Backend Software Engineer

> **Đối tượng:** Backend Developer từ Junior/Middle muốn hiểu Redis ở mức có thể thiết kế, sử dụng, đo đạc, debug và đánh giá trade-off trong hệ thống thực tế.  
> **Stack định hướng:** PHP, JavaScript/Node.js, Redis, PostgreSQL, Web API.  
> **Mục tiêu:** Không chỉ biết Redis làm gì, mà hiểu **tại sao nó nhanh, khi nào nó chậm, tại sao cần một data structure cụ thể, giới hạn ở đâu, lỗi nào có thể xảy ra và khi nào không nên dùng Redis.**

---

# 1. Redis thực chất là gì?

Redis thường được giới thiệu bằng câu:

> Redis là một in-memory key-value database.

Câu này đúng nhưng **không đủ**.

Cách hiểu chính xác hơn:

> Redis là một **data structure server**: server cung cấp các cấu trúc dữ liệu nguyên bản như String, Hash, List, Set, Sorted Set, Stream... và các operation được thực thi phía server.

Redis hiện hỗ trợ nhiều nhóm dữ liệu từ String, Hash, List, Set, Sorted Set, Streams đến JSON, Geospatial, Probabilistic, Time Series và Vector Sets. Redis Open Source 8.x đã phát triển xa hơn rất nhiều so với hình ảnh cũ về một “key-value cache”. ([Redis](https://redis.io/docs/latest/develop/data-types/?utm_source=chatgpt.com "Redis data types | Docs"))

Ở tầng rất đơn giản:

```text
Application
    |
    | command
    v
+-------------------+
|       Redis       |
|                   |
| key -> value      |
|                   |
| key -> structure  |
|                   |
+-------------------+
```

Ví dụ:

```text
user:1001 -> "Vinh"
```

là String.

Nhưng:

```text
user:1001
    name    -> "Vinh"
    age     -> 30
    role    -> "admin"
```

có thể được biểu diễn bằng Hash.

Một leaderboard:

```text
leaderboard
    Alice -> 900
    Bob   -> 850
    John  -> 700
```

lại phù hợp với Sorted Set.

Một event stream:

```text
orders:events
    1000-0 -> {order_id: 1001, event: created}
    1001-0 -> {order_id: 1002, event: paid}
    1002-0 -> {order_id: 1001, event: shipped}
```

phù hợp với Stream.

Đây chính là tư tưởng quan trọng nhất cần ghi nhớ:

> Redis không chỉ lưu dữ liệu. Redis cung cấp **cấu trúc dữ liệu + operation + execution model** để giải quyết một lớp vấn đề.

---

# 2. Redis ra đời để giải quyết vấn đề gì?

## 2.1. Bài toán của database truyền thống

Giả sử backend có:

```text
HTTP Request
    |
    v
PHP Application
    |
    v
PostgreSQL
```

Một request:

```sql
SELECT *
FROM users
WHERE id = 1001;
```

PostgreSQL phải thực hiện nhiều công việc:

```text
SQL parsing
    ↓
planning
    ↓
index lookup / table access
    ↓
visibility / MVCC checks
    ↓
buffer/cache lookup
    ↓
disk hoặc memory
    ↓
result serialization
    ↓
network
```

PostgreSQL được thiết kế để giải quyết bài toán rất lớn:

- durability;
    
- ACID;
    
- complex query;
    
- joins;
    
- indexing;
    
- transactions;
    
- concurrency;
    
- data integrity;
    
- relational data model.
    

Vì vậy, PostgreSQL không phải là một công cụ “chậm”.

Nó chỉ đang giải quyết một bài toán **khác**.

Nếu một request phải đọc:

```text
user profile
feature flag
permission
session
rate-limit counter
configuration
```

và mỗi lần đều phải đi qua database thì có thể xuất hiện:

```text
Application
     |
     +----> PostgreSQL
     +----> PostgreSQL
     +----> PostgreSQL
     +----> PostgreSQL
     +----> PostgreSQL
```

Đây là nơi Redis trở nên hữu ích.

---

# 3. Ý tưởng cốt lõi: đưa dữ liệu gần application hơn

Một mô hình cache điển hình:

```text
Client
   |
   v
PHP Application
   |
   +------ Redis ------+
   |                   |
   | cache hit         |
   |                   |
   +------ PostgreSQL  |
```

Khi có Redis:

```text
Request
   |
   v
Application
   |
   v
Redis GET
   |
   +---- hit ----> return
   |
   +---- miss
          |
          v
      PostgreSQL
          |
          v
        Redis SET
          |
          v
        return
```

Ta giảm được:

- database workload;
    
- disk-related work;
    
- query planning;
    
- complex database execution;
    
- database connection pressure;
    
- network round-trip đến PostgreSQL.
    

Nhưng điều quan trọng:

> Redis không làm cho PostgreSQL nhanh hơn. Redis **tránh việc phải hỏi PostgreSQL trong một số trường hợp**.

Đây là sự khác biệt rất quan trọng.

---

# 4. Vì sao Redis nhanh?

Không nên giải thích:

> Redis nhanh vì dùng RAM.

Đó mới chỉ là một phần.

Có nhiều yếu tố.

## 4.1. In-memory

RAM có latency thấp hơn storage.

Nhưng:

```text
RAM ≠ CPU register ≠ L1 cache ≠ L2 cache ≠ SSD ≠ HDD
```

Ngay cả khi PostgreSQL đã cache data trong RAM, Redis vẫn có thể nhanh hơn trong một workload đơn giản vì Redis tránh được nhiều processing.

---

# 5. Redis command model

Redis sử dụng mô hình:

```text
Client
   |
   | command
   v
Redis Server
   |
   | execute
   v
reply
```

Ví dụ:

```text
SET user:1001 Vinh
```

client gửi command tới Redis.

Redis xử lý rồi trả:

```text
OK
```

Lệnh:

```text
GET user:1001
```

trả:

```text
Vinh
```

Giao tiếp này sử dụng RESP — Redis Serialization Protocol. Client gửi command và arguments dưới dạng protocol structure; server trả về các kiểu dữ liệu được xác định bởi RESP. Redis dùng TCP hoặc stream-oriented socket; port mặc định là 6379. RESP được thiết kế để đơn giản và nhanh parse. ([Redis](https://redis.io/docs/latest/develop/reference/protocol-spec/ "Redis serialization protocol specification | Docs"))

---

# 6. Một request Redis thực sự phải trả chi phí gì?

Giả sử PHP gọi:

```php
$value = $redis->get('user:1001');
```

Không có chuyện:

```text
PHP -> RAM trực tiếp
```

Mà gần đúng:

```text
PHP process
    |
    | function call
    v
Redis PHP client
    |
    | serialize command
    v
TCP socket
    |
    | network
    v
Redis
    |
    | parse RESP
    |
    | lookup key
    |
    | execute command
    v
reply
    |
    | TCP
    v
PHP
```

Do đó:

> Redis nhanh không có nghĩa là network latency bằng 0.

Và đây là một lý do **pipelining** quan trọng.

---

# 7. Round trip latency

Giả sử cần:

```text
GET A
GET B
GET C
GET D
```

Cách naive:

```text
GET A -> response
GET B -> response
GET C -> response
GET D -> response
```

Có thể có 4 round-trip.

Trong khi pipeline:

```text
GET A
GET B
GET C
GET D
---------------->
<----------------
responses
```

giảm số lần phải chờ network.

Điểm quan trọng:

> Pipeline không làm command có complexity tốt hơn.

Nếu 4 lệnh vốn là:

```text
O(1)
O(1)
O(1)
O(1)
```

thì pipeline vẫn là tổng workload tương đương.

Nó chủ yếu giảm **communication latency và request/response waiting**.

---

# 8. Redis data model

Mental model:

```text
Redis Database
    |
    +-- key
    |    |
    |    +-- type
    |    |
    |    +-- value
    |
    +-- key
    |
    +-- key
```

Một key có kiểu.

Ví dụ:

```text
user:1001 -> String
```

không thể dùng:

```text
HGET user:1001 name
```

nếu key đó thực sự là String.

Redis sẽ trả:

```text
WRONGTYPE
```

Đây là tư duy quan trọng:

> Redis key không chỉ có "value". Nó có **data type**, và command phải tương thích với type đó.

---

# 9. Redis String

String là cấu trúc đơn giản nhất.

```text
key -> bytes
```

Ví dụ:

```text
user:1001 -> "Vinh"
```

hoặc:

```text
session:abc -> serialized JSON
```

Redis String thực tế là một chuỗi byte, không nhất thiết phải là text. Nó có thể chứa text, serialized object hoặc binary data. SET và GET là O(1); nhiều operation khác có complexity phụ thuộc vào kích thước dữ liệu. ([Redis](https://redis.io/docs/latest/develop/data-types/strings/ "Redis Strings | Docs"))

---

# 10. String dùng để làm gì?

## 10.1. Cache

```text
user:1001 -> {"id":1001,"name":"Vinh"}
```

Application:

```php
$data = $redis->get('user:1001');

if ($data === false) {
    $user = loadFromPostgres(1001);

    $redis->setex(
        'user:1001',
        300,
        json_encode($user)
    );
}
```

---

# 11. Cache JSON bằng String

Một pattern phổ biến:

```text
user:1001
    |
    +--> '{"id":1001,"name":"Vinh","age":30}'
```

Ưu điểm:

- đơn giản;
    
- dễ serialize;
    
- dễ cache nguyên object;
    
- ít thao tác Redis.
    

Nhược điểm:

Nếu chỉ muốn đổi:

```text
age
```

thì application thường phải:

```text
GET whole object
    ↓
decode JSON
    ↓
modify
    ↓
encode
    ↓
SET whole object
```

Nó có thể lớn hơn cần thiết.

---

# 12. String làm counter

Một đặc tính rất quan trọng:

```text
INCR counter
```

Ví dụ:

```text
page:view
```

ban đầu:

```text
100
```

chạy:

```text
INCR page:view
```

thành:

```text
101
```

`INCR` là atomic operation và có complexity O(1). Redis documentation cũng mô tả nó như một atomic counter. ([Redis](https://redis.io/docs/latest/develop/data-types/strings/ "Redis Strings | Docs"))

Điều này giải quyết:

```text
read
modify
write
```

một cách nguy hiểm.

---

# 13. Vì sao `GET -> +1 -> SET` nguy hiểm?

Giả sử:

```text
counter = 100
```

Hai request đồng thời:

```text
A: GET -> 100
B: GET -> 100

A: +1 -> 101
B: +1 -> 101

A: SET -> 101
B: SET -> 101
```

Expected:

```text
102
```

Actual:

```text
101
```

Đây là lost update.

Dùng:

```text
INCR counter
```

thay vì:

```text
GET + SET
```

giúp operation increment được Redis thực hiện atomic trong execution của server.

---

# 14. SET NX

Một pattern quan trọng:

```text
SET key value NX
```

Ý nghĩa:

> Chỉ set nếu key chưa tồn tại.

Ví dụ:

```text
SET lock:order:1001 token NX EX 30
```

Có thể dùng làm primitive cho distributed lock.

Nhưng:

> `SET NX` không tự biến ứng dụng thành một distributed locking system hoàn chỉnh.

Phải tiếp tục xét:

- ownership;
    
- expiration;
    
- token;
    
- release;
    
- crash;
    
- clock;
    
- replication;
    
- failover;
    
- fencing.
    

---

# 15. Redis Hash

Hash:

```text
key
 |
 +-- field -> value
 +-- field -> value
 +-- field -> value
```

Ví dụ:

```text
user:1001
    name -> Vinh
    age  -> 30
    role -> backend
```

Thay vì:

```text
user:1001 -> JSON
```

ta có:

```text
HSET user:1001 name Vinh
HSET user:1001 age 30
HSET user:1001 role backend
```

---

# 16. Khi nào Hash phù hợp?

Hash phù hợp khi:

- dữ liệu có nhiều field;
    
- thường truy cập riêng từng field;
    
- cần update một field;
    
- không cần query relational như PostgreSQL.
    

Ví dụ:

```text
HINCRBY user:1001 login_count 1
```

không cần lấy toàn bộ object.

---

# 17. Hash không phải PostgreSQL row

Đây là điểm cần tránh hiểu sai.

PostgreSQL:

```text
users
----------------
id
name
age
role
```

có:

- schema;
    
- constraints;
    
- transaction;
    
- index;
    
- relational semantics.
    

Redis Hash:

```text
user:1001
    name
    age
    role
```

không thay thế toàn bộ relational database.

Redis Hash nên được xem như:

> một data structure phục vụ một access pattern cụ thể.

---

# 18. Redis List

List là sequence có thứ tự:

```text
[ A, B, C, D ]
```

Có thể push từ hai đầu:

```text
LPUSH
RPUSH
```

và pop:

```text
LPOP
RPOP
```

Điều này làm List rất phù hợp với:

- queue đơn giản;
    
- stack;
    
- recent items;
    
- task processing đơn giản.
    

---

# 19. Queue bằng List

Producer:

```text
LPUSH jobs job1
```

Consumer:

```text
BRPOP jobs
```

Mô hình:

```text
Producer
   |
   v
Redis List
   |
   v
Worker
```

`BRPOP` có thể block cho đến khi item xuất hiện.

---

# 20. Nhưng List queue có giới hạn

List queue đơn giản có vấn đề:

```text
Worker nhận job
       |
       v
Worker crash
```

Nếu job đã bị pop ra khỏi list thì job có thể mất.

Đây là lý do Redis Streams trở nên quan trọng khi yêu cầu reliability cao hơn.

---

# 21. Redis Set

Set:

```text
{ A, B, C, D }
```

Các phần tử unique.

Ví dụ:

```text
online_users
```

có:

```text
1001
1002
1005
```

Nếu:

```text
SADD online_users 1001
```

nhiều lần, phần tử vẫn chỉ tồn tại một lần.

---

# 22. Set giải quyết bài toán membership

Ví dụ:

```text
user:1001:roles
```

:

```text
admin
editor
viewer
```

Có thể kiểm tra:

```text
SISMEMBER user:1001:roles admin
```

Ý nghĩa:

> Có phần tử này trong tập hợp hay không?

Đây là một pattern rất khác database query.

---

# 23. Set và authorization

Ví dụ:

```text
role:admin:permissions
```

```text
read_user
write_user
delete_user
```

Request:

```text
SISMEMBER role:admin:permissions write_user
```

Có thể rất nhanh.

Nhưng cần cân nhắc:

- stale permission cache;
    
- invalidation;
    
- consistency;
    
- deploy permission change.
    

---

# 24. Sorted Set

Sorted Set là:

```text
member + score
```

Ví dụ:

```text
Alice -> 100
Bob   -> 90
John  -> 80
```

Redis duy trì thứ tự theo score.

Đây là cấu trúc cực kỳ quan trọng.

---

# 25. Use case: leaderboard

```text
ZADD leaderboard 100 Alice
ZADD leaderboard 90 Bob
ZADD leaderboard 80 John
```

Sau đó:

```text
ZREVRANGE leaderboard 0 9
```

lấy top 10.

Không cần tự:

```text
load all
sort in PHP
```

Redis thực hiện operation trong server-side data structure.

---

# 26. Sorted Set còn dùng làm gì?

- leaderboard;
    
- ranking;
    
- scheduled tasks;
    
- priority queue pattern;
    
- time ordered items;
    
- delayed jobs;
    
- score-based lookup.
    

Ví dụ delayed task:

```text
scheduled_jobs
    jobA -> 1690000000
    jobB -> 1690000020
    jobC -> 1690000100
```

Worker tìm những job có score:

```text
<= current timestamp
```

---

# 27. Redis Stream

Stream là một trong những cấu trúc quan trọng nhất nếu mục tiêu là:

> background processing + realtime event processing.

Mental model:

```text
Stream
 |
 +-- entry ID
 |      |
 |      +-- field=value
 |
 +-- entry
 |
 +-- entry
```

Ví dụ:

```text
orders:events

1710000000000-0
    order_id=1001
    event=created

1710000000001-0
    order_id=1001
    event=paid
```

---

# 28. Stream khác Pub/Sub

Đây là phần phải hiểu rất rõ.

## Pub/Sub

```text
Publisher
    |
    v
Redis channel
    |
    +----> Subscriber A
    +----> Subscriber B
    +----> Subscriber C
```

Nếu subscriber không online tại thời điểm message được publish:

```text
message mất
```

Redis Pub/Sub có semantics **at-most-once**: một message được gửi tới subscriber đang nhận; nếu subscriber không xử lý hoặc mất connection thì message không được Redis lưu để giao lại. ([Redis](https://redis.io/docs/latest/develop/pubsub/ "Redis Pub/sub | Docs"))

Do đó:

> Pub/Sub phù hợp với transient notification/event fan-out, không phải durable job queue.

---

# 29. Stream khác ở điểm nào?

Stream lưu event:

```text
Producer
    |
    v
Redis Stream
    |
    +-- Consumer
    |
    +-- Consumer
```

Message có ID.

Consumer group có:

```text
last delivered ID
```

và pending entries.

Khi consumer đọc message:

```text
Stream
   |
   v
Consumer
   |
   v
processing
```

Redis có thể theo dõi entry chưa được acknowledge.

Sau khi xử lý thành công:

```text
XACK
```

entry được acknowledge.

Consumer group còn cho phép consumer khác claim message pending khi consumer cũ chết hoặc không còn xử lý được. ([Redis](https://redis.io/resources/how-to-build-apps-using-redis-streams.pdf?utm_source=chatgpt.com "TUTORIAL
How to Build Apps using Redis Streams
Ros"))

---

# 30. Stream consumer group

Giả sử:

```text
orders:events
```

Có:

```text
group: order-workers
```

và:

```text
worker-1
worker-2
worker-3
```

Producer:

```text
XADD orders:events * order_id 1001 event created
```

Redis lưu entry.

Consumer group phân phối message:

```text
message A -> worker-1
message B -> worker-2
message C -> worker-3
```

Không phải:

```text
A -> tất cả worker
```

Đây là điểm phân biệt với Pub/Sub.

---

# 31. Pending Entries List

Giả sử:

```text
worker-1 nhận Job A
```

nhưng:

```text
worker-1 crash
```

chưa `XACK`.

Message nằm trong trạng thái pending.

Một worker khác có thể phát hiện:

```text
Job A idle quá lâu
```

và claim nó.

Các command quan trọng:

```text
XPENDING
XCLAIM
XAUTOCLAIM
XACK
```

Mục tiêu của chúng là giúp xây dựng cơ chế:

```text
at-least-once processing
```

nhưng không nên hiểu nó là:

> Redis tự động đảm bảo exactly-once processing.

Không.

---

# 32. Exactly-once là một vấn đề khác

Ví dụ worker:

```text
1. đọc message
2. gửi email
3. XACK
```

Nếu:

```text
1. đọc
2. gửi email thành công
3. process crash
4. chưa XACK
```

message sẽ có thể được xử lý lại.

Kết quả:

```text
email gửi 2 lần
```

Redis không thể tự biết:

> email lần đầu đã thành công.

Do đó consumer phải thiết kế **idempotency**.

Ví dụ:

```text
event_id = 12345
```

Redis hoặc PostgreSQL lưu:

```text
processed:event:12345
```

Nếu thấy đã xử lý:

```text
skip
```

---

# 33. Đây là tư duy System Engineer

Không hỏi:

> Redis có bảo đảm message không bị xử lý hai lần?

Phải hỏi:

```text
Delivery guarantee là gì?
Processing guarantee là gì?
Side effect guarantee là gì?
```

Có thể có:

```text
delivery = at-least-once
processing = potentially multiple
business effect = exactly-once-like
```

nhờ idempotency.

---

# 34. Redis Pub/Sub dùng cho realtime

Ví dụ:

```text
User A gửi comment
     |
     v
PHP API
     |
     v
PostgreSQL
     |
     v
Redis PUBLISH
     |
     v
Realtime service
     |
     v
WebSocket
     |
     v
Browser
```

Đây là một kiến trúc hợp lý.

Redis không nhất thiết trực tiếp nói chuyện với browser.

Nó có thể nằm giữa backend services.

---

# 35. WebSocket thực sự nằm ở đâu?

Redis không thay thế WebSocket.

WebSocket giải quyết:

```text
Browser <---- persistent connection ----> server
```

Redis Pub/Sub hoặc Streams giải quyết:

```text
Backend service <---- event distribution ----> backend service
```

Ví dụ:

```text
             PostgreSQL
                  |
                  v
              PHP API
                  |
                  v
              Redis
             /     \
            /       \
      realtime-1   realtime-2
          |            |
       WS users     WS users
```

Redis đóng vai trò **event distribution / coordination layer**.

---

# 36. Background processing

Đây là bài toán rất phù hợp để thực nghiệm.

Ví dụ user đăng ký:

```text
POST /register
```

Không nên luôn làm:

```text
save user
send email
generate report
resize image
notify admin
```

trong cùng HTTP request.

Có thể:

```text
HTTP Request
    |
    v
PostgreSQL
    |
    v
enqueue job
    |
    v
HTTP response
```

Worker xử lý:

```text
Worker
    |
    +-- send email
    +-- generate report
    +-- image processing
```

---

# 37. Background job architecture

Một mô hình:

```text
                    +----------------+
                    |   PostgreSQL   |
                    +----------------+
                           ^
                           |
                    transaction
                           |
                           v
+--------+          +-------------+
| Client | -------> | PHP API     |
+--------+          +-------------+
                           |
                           v
                    +-------------+
                    | Redis Stream |
                    +-------------+
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
         Worker 1      Worker 2      Worker 3
```

---

# 38. Vấn đề lớn: database transaction và queue

Có một race condition kinh điển.

Code:

```text
BEGIN

INSERT order

COMMIT

XADD queue
```

Giữa:

```text
COMMIT
```

và:

```text
XADD
```

process crash.

Database có order nhưng queue không có event.

Ngược lại:

```text
XADD
INSERT order
```

queue có event nhưng DB transaction có thể rollback.

Đây là lý do các hệ thống lớn thường nghiên cứu:

> Transactional Outbox Pattern.

---

# 39. Transactional Outbox

PostgreSQL:

```text
orders
outbox_events
```

Trong cùng transaction:

```sql
BEGIN;

INSERT INTO orders (...);

INSERT INTO outbox_events (...);

COMMIT;
```

Sau đó worker:

```text
PostgreSQL outbox
      |
      v
Redis Stream
      |
      v
Consumers
```

Điều này làm business write và event creation cùng transaction database.

Sau đó event dispatcher có thể retry.

---

# 40. Redis không phải lúc nào cũng là queue tốt nhất

Nếu yêu cầu rất cao:

- enormous event stream;
    
- long retention;
    
- replay lịch sử;
    
- independent consumers;
    
- partitioning;
    
- large durable log;
    
- analytical event pipelines;
    

thì Kafka hoặc hệ thống log tương tự có thể phù hợp hơn.

Redis Streams tốt cho rất nhiều workload, đặc biệt:

- application event processing;
    
- internal queues;
    
- notifications;
    
- moderate event streams;
    
- realtime workflows.
    

Nhưng không nên biến thành:

> “mọi message system đều phải dùng Redis”.

---

# 41. TTL

TTL:

```text
SET session:123 abc EX 3600
```

nghĩa:

```text
key sống tối đa khoảng 3600 giây
```

Use case:

- session;
    
- cache;
    
- temporary token;
    
- rate limit;
    
- lock;
    
- short-lived state.
    

TTL là một đặc tính quan trọng vì cache thường cần lifecycle tự nhiên.

---

# 42. Expiration không phải garbage collector đơn giản

Một key hết hạn không nhất thiết có nghĩa:

```text
đúng timestamp = process ngay lập tức delete object
```

Redis sử dụng expiration mechanisms để quản lý expired keys.

Tư duy cần nhớ:

> Expiration là policy của data lifecycle, không phải lịch scheduler chính xác từng millisecond.

Nếu bạn cần:

```text
"đúng 10:00:00 phải chạy job"
```

thì không nên chỉ dựa vào TTL như một scheduler chính xác.

---

# 43. Eviction

TTL và eviction khác nhau.

TTL:

> Key được đặt expiration time.

Eviction:

> Redis chủ động loại dữ liệu ra khi memory policy yêu cầu.

Ví dụ `maxmemory`:

```text
Redis memory = 1 GB
maxmemory = 1 GB
```

Nếu tiếp tục insert, Redis phải theo policy:

- reject writes;
    
- hoặc evict keys theo policy.
    

Redis có nhiều eviction policies, chẳng hạn LRU/LFU theo toàn bộ keyspace hoặc chỉ key có TTL, ngoài random/no-eviction tùy cấu hình. ([Redis](https://redis.io/wp-content/uploads/2021/12/caching-at-scale-with-redis-updated-2021-12-04.pdf?utm_source=chatgpt.com "Caching at Scale"))

---

# 44. Cache stampede

Giả sử:

```text
popular:user
```

có TTL 60s.

Có 1000 request cùng lúc.

Key hết hạn:

```text
1000 requests
      |
      v
1000 cache miss
      |
      v
1000 PostgreSQL queries
```

Database bị đập mạnh.

Đây gọi là:

> Cache stampede / thundering herd.

---

# 45. Cách xử lý stampede

Một số chiến lược:

## Lock

```text
first request
     |
     +---- obtains lock
     |
     v
load DB
     |
     v
populate cache
```

request khác:

```text
lock exists
    |
    +--> wait/retry
```

## Probabilistic early refresh

Refresh cache trước expiration.

## Background refresh

Một worker chủ động cập nhật cache.

## Request coalescing

Gộp nhiều request đang cùng chờ một resource.

---

# 46. Cache penetration

Client hỏi:

```text
user:999999999
```

nhiều lần.

DB không có user.

Nếu không cache negative result:

```text
request
  ↓
Redis MISS
  ↓
PostgreSQL MISS
  ↓
request tiếp
```

Có thể cache:

```text
user:999999999 -> NULL marker
```

với TTL ngắn.

---

# 47. Cache avalanche

Nếu toàn bộ key được tạo:

```text
TTL = 3600
```

cùng một thời điểm:

```text
10:00:00
```

thì:

```text
11:00:00
```

chúng có thể expire đồng loạt.

Cách giảm:

```text
TTL = base + random_jitter
```

Ví dụ:

```text
3600 + random(0..300)
```

Không phải tất cả expire cùng lúc.

---

# 48. Memory là tài nguyên quan trọng nhất của Redis

Nếu PostgreSQL thường được suy nghĩ:

```text
data -> disk + buffer pool
```

thì Redis cần suy nghĩ:

```text
data
+ key memory
+ value memory
+ object metadata
+ data structure overhead
+ allocator overhead
+ fragmentation
+ replication buffers
+ client buffers
+ persistence overhead
```

Do đó:

```text
100 bytes JSON
```

không có nghĩa:

```text
Redis sử dụng đúng 100 bytes
```

Memory overhead có thể rất đáng kể.

Redis có các công cụ như:

```text
MEMORY USAGE
MEMORY STATS
INFO memory
```

để quan sát footprint.

Redis documentation cũng nhấn mạnh vấn đề memory optimization vì data representation và overhead ảnh hưởng đáng kể đến memory footprint. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/ "Memory optimization | Docs"))

---

# 49. Big Key

Một key:

```text
orders:user:1001
```

chứa:

```text
10 million elements
```

có thể trở thành Big Key.

Không chỉ tốn RAM.

Có thể ảnh hưởng:

- latency;
    
- replication;
    
- serialization;
    
- network;
    
- eviction;
    
- backup;
    
- failover;
    
- operational management.
    

Ví dụ:

```text
LRANGE giant_list 0 -1
```

có thể trả hàng triệu phần tử.

Vấn đề không nằm ở “Redis chậm”.

Vấn đề là:

> workload đang yêu cầu Redis tạo và truyền một lượng dữ liệu quá lớn trong một operation.

---

# 50. Complexity

Phải đọc command theo complexity.

Ví dụ:

```text
GET -> O(1)
SET -> O(1)
INCR -> O(1)
```

nhưng:

```text
LRANGE -> phụ thuộc số element trả về
SMEMBERS -> O(N)
ZRANGE -> phụ thuộc range size
```

Cho nên:

```text
Redis command = O(1)
```

là cách nói cực kỳ nguy hiểm.

Phải đọc **complexity của từng command và input size**.

Redis documentation công bố complexity cho từng command; ví dụ String GET/SET là O(1), MGET/MSET là O(N) theo số key, còn các operation khác phụ thuộc độ dài dữ liệu hoặc số phần tử. ([Redis](https://redis.io/docs/latest/develop/data-types/strings/ "Redis Strings | Docs"))

---

# 51. Single-threaded misconception

Một cách giải thích cũ rất phổ biến:

> Redis single-threaded.

Nói như vậy ngày nay không còn đủ chính xác.

Điều quan trọng hơn:

> Phần command execution của Redis có semantics tuần tự trên một execution path chính, nhưng Redis hiện đại có nhiều hoạt động/background subsystem và có thể tận dụng nhiều thread cho một số nhiệm vụ.

Đừng xây mental model:

```text
Redis = một thread làm mọi thứ
```

mà nên hiểu:

```text
             Redis Process
                   |
        +----------+-----------+
        |                      |
 command execution      background activities
        |
 sequential semantics
```

Điểm quan trọng đối với application:

> Một command nặng có thể làm các command khác phải chờ.

Đây mới là vấn đề performance thực tế.

---

# 52. Vì sao command O(N) có thể nguy hiểm?

Giả sử:

```text
SMEMBERS huge_set
```

N = 10 triệu.

Redis phải thực hiện work lớn.

Trong lúc đó:

```text
GET user:123
```

cũng có thể bị delay.

Kết quả:

```text
P99 latency tăng
```

Đây là một trong những nguyên nhân Redis có thể:

```text
CPU không cao
```

nhưng:

```text
latency vẫn xấu
```

hoặc ngược lại tùy workload.

Redis cung cấp các công cụ chẩn đoán latency để phân tích latency events, command behavior và các nguồn gây delay. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/latency/ "Diagnosing latency issues | Docs"))

---

# 53. Network thường là bottleneck bị bỏ quên

Giả sử:

```text
Redis command execution = 10 µs
network round trip = 500 µs
```

Nếu application gọi:

```text
100 commands
```

tuần tự:

```text
100 * network waiting
```

thì Redis rất nhanh nhưng hệ thống vẫn chậm.

Do đó cần xem:

```text
total latency
=
network
+
serialization
+
Redis execution
+
application processing
```

Không được chỉ benchmark Redis command.

---

# 54. Pipelining

Pipelining:

```text
Client
 |
 +-- command 1
 +-- command 2
 +-- command 3
 +-- command 4
 |
 v
Redis
```

Redis nhận nhiều command mà client không cần chờ từng reply.

Mục tiêu:

> giảm round trips.

Không phải:

> biến O(N) thành O(1).

---

# 55. Transaction

Redis có:

```text
MULTI
EXEC
DISCARD
WATCH
```

Ví dụ:

```text
MULTI
INCR counter
INCR counter
EXEC
```

Các command trong transaction được execute theo thứ tự và không bị command của client khác chen vào giữa execution của transaction.

Nhưng phải phân biệt:

> Redis transaction không phải SQL transaction.

Nó không phải cơ chế ACID tương đương PostgreSQL.

---

# 56. WATCH

Giả sử cần:

```text
read
check
write
```

Có race condition.

`WATCH` hỗ trợ optimistic concurrency.

Mental model:

```text
WATCH key
    |
GET key
    |
validate
    |
MULTI
write
EXEC
```

Nếu key thay đổi trước `EXEC`, transaction có thể bị abort.

---

# 57. Lua / Redis Functions

Một vấn đề phổ biến:

```text
GET
check
SET
```

giữa các bước có race condition.

Một cách là dùng Lua:

```text
EVAL script
```

Script được execute phía server và Redis đảm bảo script execution là atomic; trong thời gian script chạy, các hoạt động server khác bị block. ([Redis](https://redis.io/docs/latest/develop/programmability/eval-intro/ "Scripting with Lua | Docs"))

Ví dụ:

```lua
local current = redis.call("GET", KEYS[1])

if current == false then
    redis.call("SET", KEYS[1], ARGV[1])
    return 1
end

return 0
```

Toàn bộ logic:

```text
GET
IF
SET
```

được đưa vào một execution unit trên Redis.

---

# 58. Lua có mặt trái

Vì script block server execution path:

```text
script
    |
    +---- 10 ms
```

có thể ảnh hưởng:

```text
GET
SET
INCR
...
```

Do đó:

> Lua không phải “free performance”.

Một script hợp lý:

```text
small
deterministic
bounded
```

Một script nguy hiểm:

```text
loop million times
SCAN huge dataset
complex computation
```

Redis documentation nhấn mạnh rằng script execution atomic nhưng toàn bộ server activities bị block trong thời gian script chạy. ([Redis](https://redis.io/docs/latest/develop/programmability/eval-intro/ "Scripting with Lua | Docs"))

---

# 59. Redis Functions

Redis Functions là cách mở rộng server-side programmable logic theo hướng server-managed hơn Lua EVAL scripts.

Khác biệt quan trọng:

- `EVAL` scripts thường được application quản lý;
    
- script cache không phải persistent database;
    
- Redis Functions cung cấp cơ chế server-side function management.
    

Với hệ thống production lớn, programmable logic cần được versioning/deployment một cách có chủ ý.

---

# 60. Serialization

PHP object:

```php
$user = [
    'id' => 1001,
    'name' => 'Vinh'
];
```

Redis không hiểu native PHP array.

Client/application phải serialize:

```text
PHP array
    ↓
JSON / MessagePack / PHP serialization
    ↓
bytes
    ↓
Redis
```

Ngược lại:

```text
Redis bytes
    ↓
deserialize
    ↓
PHP value
```

---

# 61. JSON hay PHP serialization?

JSON:

Ưu điểm:

- language independent;
    
- debug dễ;
    
- Node.js đọc được;
    
- format phổ biến.
    

Nhược:

- verbose;
    
- type limitations;
    
- serialization/deserialization CPU.
    

PHP serialize:

Ưu:

- giữ nhiều PHP semantics.
    

Nhược:

- PHP-specific;
    
- khó interoperate;
    
- cần cẩn thận với version/object semantics.
    

Nếu hệ thống có:

```text
PHP + JavaScript + worker khác
```

thường nên ưu tiên format có tính interoperability cao.

---

# 62. Key naming

Key nên có namespace:

```text
user:1001
session:abc
product:1001
rate_limit:user:1001
lock:order:1001
queue:email
```

Không nên:

```text
1001
```

vì namespace mơ hồ.

Naming convention là một phần architecture chứ không chỉ style.

---

# 63. Versioning key

Khi schema cache thay đổi:

```text
user:v1:1001
```

thành:

```text
user:v2:1001
```

giúp migration dễ.

Thay vì phụ thuộc hoàn toàn vào:

```text
DEL all old keys
```

có thể tạo namespace mới rồi để TTL xử lý dữ liệu cũ.

---

# 64. Cache-aside

Đây là pattern phổ biến nhất:

```text
Application
    |
    v
Redis GET
    |
    +---- hit ---> return
    |
    +---- miss
            |
            v
       PostgreSQL
            |
            v
          Redis
            |
            v
          return
```

Ưu:

- đơn giản;
    
- app chủ động;
    
- Redis không phải source of truth.
    

Nhược:

- cache invalidation;
    
- stampede;
    
- stale data.
    

---

# 65. Write-through

```text
Application
    |
    +--> Cache
    |
    +--> DB
```

hoặc abstraction:

```text
write cache
    ↓
cache writes DB
```

Ưu:

- cache thường đầy đủ hơn.
    

Nhược:

- coupling;
    
- write latency;
    
- failure complexity.
    

---

# 66. Write-behind

```text
Application
    |
    v
Redis
    |
    v
async writer
    |
    v
PostgreSQL
```

Ưu:

- latency thấp.
    

Nhược:

- durability;
    
- ordering;
    
- data loss;
    
- recovery.
    

Không nên dùng chỉ vì “nhanh”.

---

# 67. Redis cache có phải source of truth không?

Trong kiến trúc PostgreSQL + Redis cache thông thường:

```text
PostgreSQL = source of truth
Redis       = derived state / cache
```

Nếu Redis mất:

```text
cache empty
```

application vẫn có thể:

```text
load PostgreSQL
rebuild cache
```

Nếu Redis đang chứa dữ liệu business duy nhất thì:

> Redis không còn chỉ là cache.

Lúc đó phải đánh giá persistence, replication, recovery và consistency một cách hoàn toàn khác.

---

# 68. Persistence

Redis có nhiều persistence mechanisms, trong đó hai cơ chế kinh điển là:

```text
RDB
AOF
```

RDB tạo snapshot dataset.

AOF ghi lại các write operations để có thể replay.

Redis hiện có tài liệu riêng cho persistence vì durability là một trade-off quan trọng; Redis không nên mặc định được xem là “tất cả dữ liệu mất khi restart” cũng như không nên được xem là “durable ngang PostgreSQL”. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/ "Redis persistence | Docs"))

---

# 69. RDB

Mental model:

```text
Redis memory
      |
      | snapshot
      v
     RDB
      |
      v
   disk file
```

Ưu:

- compact;
    
- thuận tiện backup;
    
- recovery có thể nhanh.
    

Nhược:

- snapshot có khoảng thời gian;
    
- crash giữa snapshots có thể làm mất writes chưa snapshot.
    

RDB phù hợp khi muốn:

- backup;
    
- periodic snapshots;
    
- relatively efficient restart.
    

---

# 70. AOF

Mental model:

```text
SET A 1
INCR B
DEL C
```

được append vào log.

Sau restart:

```text
replay
```

AOF thường có durability behavior tùy `appendfsync` policy.

Trade-off:

```text
more fsync
   =
more durability
   +
more I/O
```

và ngược lại.

---

# 71. Persistence không đồng nghĩa database mạnh hơn PostgreSQL

PostgreSQL có:

- WAL;
    
- MVCC;
    
- isolation levels;
    
- constraints;
    
- foreign keys;
    
- recovery;
    
- relational transactions.
    

Redis persistence giải quyết:

> làm thế nào để Redis có thể khôi phục dataset.

Nó không biến Redis thành PostgreSQL.

---

# 72. Replication

Redis replication:

```text
Primary
   |
   +---- Replica
   |
   +---- Replica
```

Replica có bản copy dataset.

Mục đích:

- read scaling;
    
- redundancy;
    
- failover support;
    
- backup-like topology.
    

Redis replication là asynchronous replication trong mô hình cơ bản. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/replication/ "Redis replication | Docs"))

---

# 73. Asynchronous replication có consequence

Giả sử:

```text
Primary
    |
    | write A
    |
    v
return OK
```

nhưng replica chưa nhận A.

Primary chết:

```text
Replica promoted
```

A có thể chưa tồn tại.

Do đó:

> “write thành công ở Redis” không đồng nghĩa tuyệt đối với “write đã tồn tại trên mọi replica”.

Đây là trade-off latency vs durability/availability.

---

# 74. Sentinel

Sentinel giải quyết high availability cho non-clustered Redis topology.

Mental model:

```text
             Sentinels
             /   |   \
            /    |    \
        Primary  Replica Replica
```

Sentinels theo dõi nodes và có thể tham gia quyết định failover.

Sentinel không phải sharding.

Nó giải quyết:

```text
"Primary chết thì ai được promote?"
```

không giải quyết:

```text
"Dataset quá lớn, làm sao chia data?"
```

Đó là Cluster.

Redis Sentinel được thiết kế để monitor, notify và automatic failover trong Redis deployment không cần sharding. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/ "High availability with Redis Sentinel | Docs"))

---

# 75. Redis Cluster

Cluster giải quyết:

```text
data quá lớn
traffic quá lớn
```

bằng sharding.

Mental model:

```text
           Redis Cluster
                 |
     +-----------+-----------+
     |           |           |
  Master A    Master B    Master C
     |           |           |
 Replica A    Replica B    Replica C
```

Redis Cluster phân phối keyspace bằng hash slots.

---

# 76. Hash slots

Redis Cluster có:

```text
16384 slots
```

Một key được map:

```text
key
 ↓
CRC16
 ↓
slot
 ↓
node
```

Ví dụ:

```text
user:1001
   ↓
slot 500
   ↓
Master A
```

Một key không được random giữa các node.

---

# 77. Hash tags

Một kỹ thuật:

```text
{user:1001}:profile
{user:1001}:permissions
{user:1001}:sessions
```

Phần:

```text
{user:1001}
```

được dùng để force các key có cùng hash slot.

Điều này rất quan trọng nếu cần multi-key operation trong cluster.

---

# 78. Tại sao cross-slot là vấn đề?

Giả sử:

```text
MGET user:1 user:2
```

Nếu:

```text
user:1 -> node A
user:2 -> node B
```

cluster không thể xử lý như một local multi-key operation thông thường.

Vì:

```text
transaction
Lua
MULTI-key command
```

thường cần các key liên quan nằm cùng slot.

Do đó key design trong Redis Cluster là architectural concern.

---

# 79. Redis Cluster và PHP

Nếu dùng cluster-aware client:

```text
PHP
 |
 +----> node A
 |
 +----> node B
 |
 +----> node C
```

client phải hiểu:

```text
MOVED
ASK
slot mapping
```

Không nên tự viết logic cluster routing nếu client library đã hỗ trợ.

Ở đây xuất hiện nguyên tắc:

> Việc nào nên tự code, việc nào framework/client đã giải quyết?

---

# 80. Cái backend developer nên tự làm

Nên tự thiết kế:

```text
cache key
TTL
data structure
invalidation strategy
retry strategy
idempotency
queue semantics
failure handling
capacity estimate
monitoring metrics
```

---

# 81. Cái client/library/server nên hỗ trợ

Không nên tự viết từ đầu:

```text
RESP parser
TCP Redis protocol
cluster slot routing
connection handling
reconnect
serialization primitives
```

trừ khi:

- học;
    
- research;
    
- viết infrastructure;
    
- cần custom client.
    

Trong application thực tế:

```text
PHP
  |
phpredis / Predis / client tương ứng
  |
Redis
```

Node.js:

```text
Node.js
  |
node-redis / client tương ứng
  |
Redis
```

Nên dùng client đã được kiểm chứng.

---

# 82. Connection management

Redis nhanh nhưng mỗi request:

```text
connect
authenticate
execute
close
```

là lãng phí.

Application nên thường sử dụng:

```text
persistent connection
```

hoặc:

```text
connection pool
```

tùy runtime/framework.

PHP-FPM có lifecycle khác Node.js.

Ví dụ:

```text
Node.js process
    |
    +---- long-lived Redis connection
```

Trong khi PHP:

```text
PHP-FPM worker
    |
    +---- Redis connection
```

cách quản lý lifecycle cần phù hợp với worker model.

---

# 83. Redis trong PHP web request

Ví dụ pseudo-code:

```php
function getUser(int $id): array
{
    $key = "user:$id";

    $cached = $redis->get($key);

    if ($cached !== false) {
        return json_decode($cached, true);
    }

    $user = $postgres->findUser($id);

    if ($user === null) {
        $redis->setex(
            "user:$id",
            60,
            '{"not_found":true}'
        );

        throw new RuntimeException("Not found");
    }

    $redis->setex(
        $key,
        300,
        json_encode($user)
    );

    return $user;
}
```

Đây là **cache-aside**.

Nhưng production phải tiếp tục xử lý:

- serialization error;
    
- Redis unavailable;
    
- PostgreSQL unavailable;
    
- stampede;
    
- stale cache;
    
- negative caching;
    
- timeout.
    

---

# 84. Redis failure có nên làm request fail?

Đây là câu hỏi architecture.

Nếu Redis là:

```text
cache
```

thì thông thường:

```text
Redis DOWN
    |
    v
fallback PostgreSQL
```

có thể hợp lý.

Nếu Redis là:

```text
authorization state
```

hoặc:

```text
critical queue
```

thì không thể đơn giản:

```text
Redis DOWN -> ignore
```

Cần xác định:

> Redis đang giữ loại state nào?

---

# 85. Timeout

Không nên để:

```text
Redis call
```

block request vô hạn.

Cần giới hạn:

```text
connect timeout
read timeout
operation timeout
```

Một cache failure không nên biến thành:

```text
entire API thread/process pool blocked
```

đây là một phần của resilience.

---

# 86. Retry không phải luôn tốt

Ví dụ Redis chậm.

Application:

```text
request
  |
  +-- Redis timeout
  |
  +-- retry
  |
  +-- retry
  |
  +-- retry
```

1000 request có thể tạo:

```text
3000 Redis requests
```

Redis càng quá tải.

Đây là:

> retry storm.

Retry cần:

```text
bounded
backoff
jitter
max attempts
```

---

# 87. Rate limiting

Redis rất phù hợp với counter + expiration.

Ví dụ:

```text
rate:user:1001 -> 27
TTL -> 1 second
```

Mỗi request:

```text
INCR rate:user:1001
```

nếu:

```text
count > 100
```

thì reject.

Nhưng:

```text
INCR
EXPIRE
```

cần thiết kế cẩn thận vì hai operation vẫn là hai command.

Có thể dùng atomic server-side logic để đảm bảo:

```text
increment + expiration setup
```

đúng như policy.

---

# 88. Distributed lock

Mô hình cơ bản:

```text
SET lock:key random-token NX PX 30000
```

Nếu thành công:

```text
lock acquired
```

Release phải kiểm tra ownership:

```text
GET lock:key
```

so sánh token:

```text
token == my_token
```

rồi mới delete.

Không nên:

```text
DEL lock:key
```

mà không kiểm tra ownership.

Nếu process A hết thời gian lock:

```text
lock expired
```

B lấy lock.

A vẫn chạy và sau đó:

```text
DEL lock:key
```

sẽ vô tình xóa lock của B.

Token owner giúp tránh lỗi này.

---

# 89. Nhưng distributed lock còn sâu hơn

Cần suy nghĩ:

```text
lock expiration
network partition
GC pause
process pause
clock behavior
failover
replication lag
fencing token
```

Một distributed lock không thể chỉ đánh giá bằng:

```text
SET NX
```

Đặc biệt nếu critical correctness phụ thuộc vào lock.

Trong bài toán database correctness, PostgreSQL row lock/advisory lock hoặc transactional constraints đôi khi phù hợp hơn Redis lock.

---

# 90. Keyspace notifications

Redis có thể phát notification khi key event xảy ra.

Ví dụ:

```text
key expired
```

Một service có thể nhận:

```text
expired event
```

Nhưng cần tránh suy nghĩ:

> key expiration notification = durable job scheduler.

Notification không nên là nơi duy nhất lưu business event quan trọng.

Nếu event mất:

```text
business correctness
```

không nên phụ thuộc hoàn toàn vào nó.

---

# 91. Observability

Production Redis cần theo dõi:

```text
memory_used
memory_peak
connected_clients
ops/sec
command latency
blocked clients
evictions
expired keys
keyspace hits
keyspace misses
replication lag
rejected connections
network throughput
CPU
fragmentation
```

Đặc biệt:

```text
P50
P95
P99
P99.9
```

quan trọng hơn:

```text
average
```

---

# 92. Cache hit rate

Một metric cơ bản:

```text
hit rate
=
hits / (hits + misses)
```

Ví dụ:

```text
hits   = 900,000
misses = 100,000

hit rate = 90%
```

Nhưng:

> Hit rate cao không tự động có nghĩa hệ thống tốt.

Ví dụ:

```text
99.9% hit rate
```

nhưng những request miss lại là request cực kỳ nặng và gây DB overload.

Phải xem:

```text
hit rate
+
latency
+
DB load
+
memory
```

---

# 93. Hot key

Giả sử:

```text
homepage:config
```

được đọc:

```text
1,000,000 requests/sec
```

Một key đơn có thể trở thành hot key.

Dù GET O(1):

```text
1 key
→ enormous traffic
```

có thể trở thành bottleneck.

Scale horizontally không giải quyết tự động vì tất cả request vẫn có thể route đến node chứa key đó.

---

# 94. Hot key mitigation

Các hướng:

```text
local cache
client-side caching
replicated/sharded copies
key duplication
application-level fan-out
```

Nhưng duplicate data tạo:

```text
invalidation complexity
```

Một cách khác là Redis client-side caching.

Redis hỗ trợ server-assisted client-side caching bằng tracking; server có thể gửi invalidation khi tracked key thay đổi, giúp giảm network traffic và request tới Redis. ([Redis](https://redis.io/docs/latest/develop/clients/client-side-caching/?utm_source=chatgpt.com "Client-side caching introduction | Docs"))

---

# 95. Local cache + Redis

Kiến trúc:

```text
Application
   |
   +-- L1 local memory
   |
   +-- L2 Redis
   |
   +-- L3 PostgreSQL
```

Ví dụ:

```text
GET user:1001
       |
       v
PHP local cache?
       |
       +-- hit -> return
       |
       v
Redis?
       |
       +-- hit -> local cache -> return
       |
       v
PostgreSQL
```

Latency:

```text
L1 < Redis < PostgreSQL
```

nhưng consistency khó hơn.

Đây là trade-off quan trọng.

---

# 96. Cache invalidation

Có ba chiến lược lớn:

## TTL

```text
cache tự hết hạn
```

đơn giản nhưng stale.

## Explicit invalidation

```text
update DB
    |
    v
DEL cache
```

fresh hơn nhưng phải đảm bảo mọi write path đều invalidate.

## Event-driven invalidation

```text
DB change
   |
   v
event
   |
   v
invalidate cache
```

mạnh hơn nhưng architecture phức tạp hơn.

---

# 97. Cache consistency

Không có:

```text
"Redis cache luôn đúng"
```

một cách tự nhiên.

Có những mô hình:

```text
strong consistency
eventual consistency
stale-while-revalidate
best effort
```

Business requirement phải quyết định.

Ví dụ avatar:

```text
stale 30s
```

có thể chấp nhận.

Balance:

```text
stale 30s
```

không thể chấp nhận tương tự.

---

# 98. Redis + PostgreSQL: phân chia trách nhiệm

Một kiến trúc hợp lý:

```text
PostgreSQL
    |
    +-- durable source of truth
    +-- relational consistency
    +-- complex queries
    +-- constraints
    +-- transactions

Redis
    |
    +-- cache
    +-- ephemeral state
    +-- counters
    +-- rate limiting
    +-- locks
    +-- queues
    +-- streams
    +-- realtime event distribution
```

Không nên:

```text
PostgreSQL = slow
Redis = fast
=> replace PostgreSQL
```

Đó là cách hiểu sai.

---

# 99. Redis và PostgreSQL có thể bổ trợ nhau

Ví dụ order system:

```text
POST /orders
      |
      v
PHP
      |
      +---- PostgreSQL transaction
      |
      +---- Outbox event
                    |
                    v
             Event dispatcher
                    |
                    v
              Redis Stream
                    |
          +---------+---------+
          |                   |
          v                   v
     Email worker       Realtime worker
                              |
                              v
                          WebSocket
```

Đây là architecture có thể mở rộng tốt hơn việc bắt PHP request làm mọi việc.

---

# 100. Background processing: thiết kế một prototype thực tế

## Mục tiêu

Làm:

```text
POST /orders
```

không chờ:

```text
send email
notify user
```

---

## Component

```text
PHP API
PostgreSQL
Redis
PHP Worker
WebSocket/Node.js realtime service
Browser
```

---

# 101. Request flow

```text
Browser
   |
POST /orders
   |
   v
PHP API
   |
   +---- PostgreSQL
   |       create order
   |
   +---- enqueue event
           |
           v
       Redis Stream
           |
           v
        Worker
        /     \
       /       \
 email service realtime service
                  |
                  v
               WebSocket
                  |
                  v
                Browser
```

---

# 102. Worker pseudo-code

```php
while (true) {
    $messages = $redis->xReadGroup(
        'order-workers',
        $consumerName,
        ['orders:events' => '>'],
        10,
        5000
    );

    foreach ($messages as $message) {
        try {
            processEvent($message);

            $redis->xAck(
                'orders:events',
                'order-workers',
                [$message['id']]
            );
        } catch (Throwable $e) {
            // Do not ACK.
            // Message remains pending.
        }
    }
}
```

Ý nghĩa:

```text
XREADGROUP
     |
     v
receive
     |
     v
process
     |
     +-- success --> XACK
     |
     +-- failure --> remain pending
```

Consumer groups của Streams theo dõi pending messages và cho phép xử lý recovery/claim khi consumer gặp sự cố. ([Redis](https://redis.io/resources/how-to-build-apps-using-redis-streams.pdf?utm_source=chatgpt.com "TUTORIAL
How to Build Apps using Redis Streams
Ros"))

---

# 103. Recovery worker

Một worker recovery có thể tìm:

```text
pending messages
idle > threshold
```

sau đó:

```text
XAUTOCLAIM
```

để đưa message sang consumer mới.

Architecture:

```text
Normal worker
     |
     +-- process success
     |
     +-- ACK

Recovery worker
     |
     +-- inspect pending
     |
     +-- claim stale entries
     |
     +-- reprocess
```

---

# 104. Idempotency

Ví dụ event:

```json
{
  "event_id": "evt-10001",
  "type": "order_paid",
  "order_id": 1001
}
```

Worker:

```text
if already_processed(event_id):
    ACK
    continue

process business effect

mark event processed

ACK
```

Nhưng nếu:

```text
process success
mark processed chưa thành công
crash
```

thì vẫn có duplicate.

Do đó idempotency mechanism cũng phải được thiết kế atomic hoặc dựa vào database constraint.

Một lựa chọn mạnh:

```sql
CREATE TABLE processed_events (
    event_id TEXT PRIMARY KEY
);
```

Worker transaction:

```sql
BEGIN;

INSERT INTO processed_events(event_id)
VALUES ('evt-10001')
ON CONFLICT DO NOTHING;

-- nếu insert thành công
-- thực hiện business update

COMMIT;
```

Tùy business semantics, đây có thể giúp xây dựng hiệu ứng exactly-once ở tầng business.

---

# 105. Realtime notification prototype

## Ví dụ

User đặt hàng:

```text
order created
```

Browser cần nhận:

```text
"Order #1001 created"
```

Architecture:

```text
PHP API
   |
   v
PostgreSQL
   |
   v
Redis Pub/Sub
   |
   v
Node.js realtime server
   |
   v
WebSocket
   |
   v
Browser
```

---

# 106. Vì sao Pub/Sub hợp lý ở đây?

Nếu notification chỉ có ý nghĩa:

```text
ngay lúc này
```

và nếu user disconnected:

```text
không cần replay notification cũ
```

Pub/Sub rất phù hợp.

Nếu yêu cầu:

> user reconnect phải nhận toàn bộ notification chưa đọc

thì Pub/Sub không phù hợp làm nguồn duy nhất.

Khi đó:

```text
Redis Streams
```

hoặc:

```text
PostgreSQL notification table
```

có thể phù hợp hơn.

---

# 107. Redis Pub/Sub mental model

```text
PUBLISH notification:user:1001 payload

              |
              v

       +--------------+
       | Redis channel|
       +--------------+
         /    |     \
        /     |      \
       v      v       v
     WS1     WS2     WS3
```

Message không trở thành một persistent queue.

Do đó:

```text
subscriber offline
      |
      v
miss message
```

---

# 108. Redis Streams realtime model

Nếu cần replay:

```text
Redis Stream
    |
    +-- realtime consumer
    |
    +-- notification consumer
    |
    +-- analytics consumer
```

Có thể giữ event để consumer đọc theo lifecycle của stream.

Đây là điểm Redis Streams mạnh hơn Pub/Sub.

---

# 109. Nhưng Stream retention cũng cần thiết kế

Stream không phải:

> log vô hạn miễn phí.

Cần có policy:

```text
XTRIM
max length
min-idle-time
retention window
```

Câu hỏi:

```text
Giữ event bao lâu?
```

là business/architecture question.

Không có retention policy:

```text
Stream
  |
  +-- 1 million
  +-- 10 million
  +-- 100 million
  +-- ...
```

memory tăng.

---

# 110. Memory capacity planning

Giả sử:

```text
average item = 500 bytes
```

và:

```text
10 million items
```

raw payload:

```text
~5 GB
```

Nhưng Redis memory không chỉ là payload.

Có:

```text
key
object metadata
allocation overhead
data structure overhead
replication
fragmentation
client buffers
```

Do đó không được lấy:

```text
5 GB
```

rồi kết luận:

> Redis cần máy 5 GB.

Phải benchmark realistic dataset và xem:

```text
MEMORY USAGE
INFO memory
allocator fragmentation
```

---

# 111. Fragmentation

Memory allocator có thể cấp phát:

```text
10 KB
```

nhưng thực tế process không nhất thiết sử dụng đúng:

```text
10 KB
```

theo cách application nghĩ.

Có thể xảy ra:

```text
used_memory
    <
RSS
```

do allocator fragmentation hoặc allocator behavior.

Do đó:

```text
RSS
```

và:

```text
used_memory
```

không phải cùng một metric.

---

# 112. Eviction policy chọn dựa trên semantics

Ví dụ cache hoàn toàn:

```text
allkeys-lru
```

có thể hợp lý.

Nếu chỉ muốn evict keys có TTL:

```text
volatile-...
```

có thể phù hợp.

Nếu data loss không chấp nhận được:

```text
noeviction
```

có thể được cân nhắc, nhưng writes sẽ fail khi memory limit bị đạt.

Không có policy:

```text
best
```

cho mọi hệ thống.

---

# 113. Persistence + cache

Nếu Redis chỉ là cache:

```text
PostgreSQL
   |
   v
Redis
```

thì mất Redis:

```text
cache miss
```

không nhất thiết là disaster.

Trong trường hợp này:

```text
aggressive durability
```

có thể không đáng giá bằng:

```text
latency
simplicity
memory capacity
```

Ngược lại, nếu Redis chứa queue hoặc critical state:

```text
durability requirement
```

tăng lên đáng kể.

---

# 114. Security

Redis không nên public trực tiếp ra Internet.

Architecture:

```text
Internet
   |
   v
Load Balancer
   |
   v
Application network
   |
   v
Redis private network
```

Cần:

- authentication;
    
- ACL;
    
- TLS nếu cần;
    
- network segmentation;
    
- firewall/security groups;
    
- principle of least privilege.
    

---

# 115. ACL

Không phải application nào cũng cần:

```text
CONFIG
FLUSHALL
DEBUG
```

Production client có thể chỉ cần:

```text
GET
SET
DEL
EXPIRE
...
```

Least privilege giảm blast radius.

---

# 116. FLUSHALL là command nguy hiểm

Đặc biệt production.

Không nên:

```text
clear Redis
```

để “fix cache”.

Nếu Redis chứa queue:

```text
FLUSHALL
```

có thể phá toàn bộ workload.

Operational command phải được coi là production-impacting action.

---

# 117. Namespace giữa cache và queue

Không nên trộn:

```text
user:1001
queue:emails
lock:orders
session:abc
stream:events
```

mà không quy ước.

Ví dụ:

```text
cache:user:1001
session:abc
lock:order:1001
rate:user:1001
stream:orders
```

để dễ:

- monitoring;
    
- debugging;
    
- migration;
    
- ACL;
    
- key analysis.
    

---

# 118. Không nên dùng Redis cho mọi thứ

Redis không phù hợp nếu:

```text
data chủ yếu relational
data rất lớn và cần durability mạnh
complex queries
joins
constraints
historical analytics
large durable event log
```

PostgreSQL thường phù hợp hơn.

Redis phù hợp khi:

```text
access pattern rõ
latency quan trọng
data structure phù hợp
data có lifecycle rõ
state có thể ephemeral/derived
```

---

# 119. Không nên lưu toàn bộ database vào Redis

Anti-pattern:

```text
PostgreSQL = source
Redis = copy toàn bộ database
```

Nếu:

```text
10 TB database
```

thì Redis copy toàn bộ có thể vô cùng đắt.

Cache phải dựa trên access pattern.

Ví dụ:

```text
10 million users
```

nhưng chỉ:

```text
top 100k active users
```

được đọc thường xuyên.

Cache subset mới có ý nghĩa.

---

# 120. Data locality

Một insight quan trọng:

> Redis performance phụ thuộc rất lớn vào việc dữ liệu và operation có nằm gần nhau hay không.

Ví dụ:

```text
ZADD
ZRANGE
```

được thực hiện trong Redis.

Nếu thay bằng:

```text
GET all
  ↓
PHP
  ↓
sort
```

thì ta đã di chuyển computation qua network/application.

Do đó:

> Chọn đúng data structure chính là chọn nơi computation xảy ra.

---

# 121. Đây là lý do Redis là “data structure server”

Ví dụ requirement:

> lấy top 10 user theo điểm.

Không nên tư duy:

```text
database rows
→ PHP array
→ usort()
```

mà hỏi:

```text
Data structure nào biểu diễn bài toán này?
```

Đáp án:

```text
Sorted Set
```

Operation:

```text
ZREVRANGE
```

---

# 122. Chọn data type theo access pattern

## String

Khi:

```text
one key -> one opaque value
```

Ví dụ:

```text
cache
counter
token
serialized JSON
```

## Hash

Khi:

```text
one key -> many fields
```

và muốn field-level operations.

## List

Khi:

```text
ordered sequence
push/pop
```

## Set

Khi:

```text
unique membership
set operations
```

## Sorted Set

Khi:

```text
unique members + score + ordering
```

## Stream

Khi:

```text
append-only events
consumer groups
ack
replay/recovery
```

---

# 123. Một bảng mental model

|Bài toán|Data structure|
|---|---|
|Cache object|String / Hash / JSON|
|Counter|String|
|Session|String / Hash|
|Tags|Set|
|Online users|Set|
|Leaderboard|Sorted Set|
|Ranking|Sorted Set|
|Priority|Sorted Set|
|Simple queue|List|
|Durable-ish internal event queue|Stream|
|Event processing|Stream|
|Realtime transient fan-out|Pub/Sub|
|Rate limit|String + TTL|
|Temporary lock|String + NX + TTL|
|Geo lookup|Geospatial|
|Approximate cardinality|HyperLogLog|
|Bitmap flags|Bitmap|

Redis documentation itself treats these as different trade-offs rather than interchangeable structures. ([Redis](https://redis.io/docs/latest/develop/data-types/compare-data-types/?utm_source=chatgpt.com "Compare data types | Docs"))

---

# 124. Benchmark: đừng đo Redis theo kiểu sai

Không nên chỉ benchmark:

```text
SET 1 million times
```

rồi kết luận:

> Redis nhanh.

Nên benchmark:

```text
payload size
command
concurrency
network distance
pipelining
hit/miss
key distribution
memory size
hot key
big key
replication
persistence
```

---

# 125. Benchmark nên trả lời câu hỏi gì?

Ví dụ:

### Experiment 1

```text
GET 100k times
```

so với:

```text
PostgreSQL SELECT 100k times
```

Mục tiêu:

> đo latency.

### Experiment 2

```text
100 GET sequential
```

vs:

```text
100 GET pipeline
```

Mục tiêu:

> đo network round-trip overhead.

### Experiment 3

```text
1 KB value
10 KB
100 KB
1 MB
```

Mục tiêu:

> xem payload ảnh hưởng latency/throughput thế nào.

### Experiment 4

```text
1 key
100k keys
10m keys
```

Mục tiêu:

> memory + lookup + eviction.

---

# 126. Benchmark queue

So sánh:

```text
List
vs
Streams
```

test:

```text
1 producer
1 worker

1 producer
10 workers

10 producers
10 workers
```

Đo:

```text
throughput
latency
pending entries
recovery
duplicate processing
```

---

# 127. Benchmark Pub/Sub

Test:

```text
1 publisher
1 subscriber

1 publisher
100 subscribers

10 publishers
100 subscribers
```

Đo:

```text
message propagation latency
CPU
network
subscriber disconnect
```

Sau đó:

```text
disconnect subscriber
publish 100 messages
reconnect
```

và kiểm chứng:

> message có được replay không?

Bạn sẽ trực tiếp thấy sự khác nhau giữa:

```text
Pub/Sub
```

và:

```text
Streams
```

---

# 128. Prototype project đề xuất

Một project rất phù hợp với stack của bạn:

```text
redis-lab/
```

```text
docker-compose
  |
  +-- postgres
  +-- redis
  +-- php-api
  +-- php-worker
  +-- node-realtime
  +-- browser-client
```

Features:

```text
1. Product/user API
2. PostgreSQL persistence
3. Redis cache
4. Rate limiting
5. Background email job
6. Redis Stream
7. Worker retry
8. Idempotency
9. Pub/Sub realtime notification
10. WebSocket
11. Metrics
12. Benchmark
```

---

# 129. Lab 1 — Redis cơ bản

Học:

```text
SET
GET
DEL
EXISTS
TTL
EXPIRE
INCR
DECR
MGET
MSET
```

Tự kiểm chứng:

```text
SET foo bar
GET foo
TTL foo
EXPIRE foo 10
TTL foo
```

Mục tiêu:

> hiểu lifecycle của key.

---

# 130. Lab 2 — Data structures

Thử:

```text
String
Hash
List
Set
Sorted Set
Stream
```

Mỗi structure phải trả lời:

```text
insert?
read?
update?
delete?
ordering?
uniqueness?
complexity?
memory?
```

Không học command như từ vựng.

---

# 131. Lab 3 — Cache-aside

Build:

```text
GET /users/:id
```

Flow:

```text
Redis GET
    |
    +-- HIT
    |
    +-- MISS
          |
          v
      PostgreSQL
          |
          v
        Redis
```

Log:

```text
cache_hit
cache_miss
db_query_time
redis_latency
```

---

# 132. Lab 4 — Stampede

Tạo:

```text
100 concurrent requests
```

cho cùng một key hết hạn.

Không có lock.

Đếm:

```text
DB queries
```

Sau đó thêm lock.

So sánh:

```text
DB load
response latency
```

Bạn sẽ thấy cache invalidation không chỉ là chuyện TTL.

---

# 133. Lab 5 — Rate limit

Implement:

```text
100 requests / second / user
```

Test:

```text
INCR
TTL
atomic script
```

Sau đó test concurrency.

Mục tiêu:

> thấy difference giữa correctness và chỉ “code chạy được”.

---

# 134. Lab 6 — Background job

Implement:

```text
POST /orders
```

sau đó:

```text
Redis Stream
```

worker:

```text
XREADGROUP
XACK
XPENDING
XAUTOCLAIM
```

Tự gây crash worker:

```text
kill -9 worker
```

quan sát pending messages.

Sau đó start worker khác.

---

# 135. Lab 7 — Idempotency

Worker:

```text
process order
```

cố tình:

```text
process success
crash before XACK
```

Sau đó worker mới xử lý lại.

Kiểm tra:

```text
business effect
```

có bị lặp không.

Đây là một experiment cực kỳ quan trọng.

---

# 136. Lab 8 — Realtime

Build:

```text
Browser
   |
 WebSocket
   |
Node.js
   |
Redis Pub/Sub
   |
PHP API
```

POST:

```text
/order
```

Browser nhận:

```text
order_created
```

Sau đó:

```text
disconnect browser
publish event
reconnect
```

quan sát message bị mất.

Tiếp tục chuyển sang Stream và thiết kế replay.

---

# 137. Lab 9 — Redis latency

Test:

```text
redis-cli --latency
```

và benchmark:

```text
redis-benchmark
```

Nhưng không dừng ở benchmark built-in.

Test workload giống production:

```text
PHP
   |
TCP
   |
Redis
```

vì application performance chịu ảnh hưởng bởi:

```text
client
network
serialization
Redis
```

---

# 138. Lab 10 — Big Key

Tạo:

```text
large list
large set
large hash
```

Đo:

```text
MEMORY USAGE
```

và thử:

```text
LRANGE
SMEMBERS
HGETALL
```

Sau đó quan sát:

```text
latency
CPU
network
```

---

# 139. Lab 11 — Hot Key

Tạo:

```text
hot:key
```

cho toàn bộ traffic.

Sau đó:

```text
10k request/sec
```

quan sát:

```text
CPU
latency
connection
```

Tiếp tục thử:

```text
local cache
```

và so sánh.

---

# 140. Lab 12 — Fail Redis

Giả lập:

```text
Redis unavailable
```

Kiểm tra ứng dụng:

```text
API có timeout không?
PHP worker có retry vô hạn không?
PostgreSQL có bị overload không?
User nhận HTTP 500 hay fallback?
```

Đây mới là production thinking.

---

# 141. Lab 13 — Fail worker

```text
worker process
     |
     v
consume message
     |
     v
kill -9
```

Quan sát:

```text
pending
idle time
claim
retry
```

Mục tiêu:

> hiểu failure recovery bằng thực nghiệm thay vì ghi nhớ command.

---

# 142. Lab 14 — Redis restart

Test:

```text
SET keys
restart Redis
```

sau đó so sánh:

```text
RDB
AOF
no persistence
```

Mục tiêu:

> hiểu durability bằng experiment.

---

# 143. Lab 15 — Replication lag

Architecture:

```text
Primary
   |
Replica
```

Tạo write workload.

Quan sát:

```text
replication offset
lag
read behavior
```

Sau đó kill primary.

Mục tiêu:

> hiểu asynchronous replication và failover thay vì chỉ biết sơ đồ.

---

# 144. Lab 16 — Cluster

Khi đã hiểu standalone:

```text
3 master
3 replica
```

Thử:

```text
key routing
MOVED
hash slots
hash tags
multi-key operation
```

Đây là giai đoạn advanced, không nên học ngay ngày đầu.

---

# 145. Thứ tự học nên là

## Phase 1 — Mental model

Nắm:

```text
Redis là gì?
client/server
key/value
data structures
command
TTL
```

## Phase 2 — Core data structures

```text
String
Hash
List
Set
Sorted Set
Stream
```

## Phase 3 — Performance

```text
complexity
network
pipeline
payload
memory
big key
hot key
latency
```

## Phase 4 — Consistency/concurrency

```text
atomic command
MULTI/EXEC
WATCH
Lua
idempotency
lock
```

## Phase 5 — Application patterns

```text
cache
rate limit
session
queue
event
realtime
```

## Phase 6 — Reliability

```text
RDB
AOF
replication
Sentinel
Cluster
failover
recovery
```

## Phase 7 — Production

```text
monitoring
capacity
memory
security
timeout
retry
backpressure
```

---

# 146. Cách suy nghĩ của Software Engineer

Khi gặp requirement:

> "Cần Redis."

Không nên hỏi ngay:

```text
Dùng SET hay HASH?
```

Phải hỏi:

```text
Dữ liệu này là gì?
Ai là source of truth?
Access pattern là gì?
Read/write ratio?
Latency requirement?
Durability requirement?
Consistency requirement?
Data size?
Retention?
Concurrency?
Failure semantics?
Scaling?
```

Sau đó mới chọn Redis.

---

# 147. Một decision framework thực tế

Requirement:

> Cần giảm tải PostgreSQL.

Hỏi:

```text
Dữ liệu có thể stale không?
```

Nếu yes:

```text
cache
```

---

Requirement:

> Cần đếm request.

```text
counter
```

→ String + INCR.

---

Requirement:

> Cần kiểm tra user thuộc group.

```text
membership
```

→ Set.

---

Requirement:

> Top 100 user.

```text
score + order
```

→ Sorted Set.

---

Requirement:

> Worker cần xử lý event có retry.

```text
persistent event processing
```

→ Streams.

---

Requirement:

> Push event realtime cho subscriber đang online.

```text
transient fan-out
```

→ Pub/Sub.

---

Requirement:

> Business event phải lưu lâu dài, replay nhiều consumer độc lập.

Cần đánh giá:

```text
Streams
Kafka
PostgreSQL outbox
```

không mặc định chọn Redis.

---

# 148. Redis nhanh không có nghĩa mọi Redis operation nhanh

Mental model sai:

```text
Redis = fast
```

Mental model đúng:

```text
Redis
 |
 +-- O(1) operation
 +-- O(log N)
 +-- O(N)
 +-- O(N*M)
 +-- large payload
 +-- network heavy
 +-- serialization heavy
 +-- Lua heavy
 +-- memory pressure
```

Một command Redis chậm không có nghĩa:

> Redis chậm.

Có thể:

```text
operation complexity sai
data size sai
access pattern sai
network sai
client usage sai
memory pressure sai
```

---

# 149. Redis performance equation

Có thể tư duy gần đúng:

```text
Application latency
=
client overhead
+
serialization
+
network latency
+
Redis queueing
+
Redis execution
+
response transfer
+
deserialization
```

Ở scale lớn:

```text
queueing
```

rất quan trọng.

Nếu Redis server bận:

```text
command A
   ↓
long execution
   ↓
command B waits
```

P99 có thể tăng mạnh dù average vẫn đẹp.

---

# 150. Redis throughput không phải latency

Một hệ thống có thể:

```text
high throughput
```

nhưng:

```text
high tail latency
```

Ví dụ:

```text
100k ops/sec
```

không cho biết:

```text
P99 = ?
```

Khi thiết kế backend production:

> luôn quan tâm latency distribution, không chỉ ops/sec.

---

# 151. Backpressure

Giả sử:

```text
producer = 10,000 jobs/sec
worker = 2,000 jobs/sec
```

Queue growth:

```text
+8,000 jobs/sec
```

Redis không “giải quyết” vấn đề này.

Redis chỉ giữ backlog.

Sau một thời gian:

```text
queue -> memory exhaustion
```

Do đó queue cần monitoring:

```text
queue depth
consumer throughput
processing latency
oldest message age
failure rate
```

---

# 152. Một công thức tư duy rất quan trọng

Nếu:

```text
arrival_rate > processing_rate
```

thì:

```text
backlog tăng
```

Redis không thể thay thế capacity planning.

Cần:

```text
scale workers
optimize processing
reduce producer rate
apply backpressure
```

---

# 153. Queue depth chưa đủ

Ví dụ:

```text
queue depth = 100
```

có thể tốt.

Nhưng:

```text
oldest message age = 10 minutes
```

lại rất xấu.

Ngược lại:

```text
queue depth = 10000
```

nhưng:

```text
worker throughput = 20000/s
```

có thể xử lý nhanh.

Do đó monitoring nên nhìn:

```text
depth
+
age
+
throughput
+
failure
```

---

# 154. Error handling cho worker

Không nên:

```php
catch(Throwable $e) {
    $redis->xAck(...);
}
```

nếu ACK nghĩa là:

> đã xử lý thành công.

Nếu ACK mọi thứ:

```text
failed message
→ ACK
→ mất message
```

Ngược lại, không ACK mãi cũng gây:

```text
infinite retry
```

Cần policy:

```text
retry count
backoff
dead-letter
manual intervention
```

---

# 155. Dead-letter pattern

Ví dụ:

```text
Stream
   |
   v
Worker
   |
   +-- success -> ACK
   |
   +-- fail -> retry
               |
               +-- retry 1
               +-- retry 2
               +-- retry 3
               |
               v
          dead-letter
```

Dead-letter có thể là:

```text
Redis Stream
```

riêng:

```text
orders:failed
```

hoặc PostgreSQL.

---

# 156. Redis không tự giải quyết business retry semantics

Redis có primitives.

Application phải quyết định:

```text
retry bao nhiêu lần?
retry sau bao lâu?
error nào retry?
error nào permanent?
```

Ví dụ:

```text
400 Bad Request
```

thường không nên retry vô hạn.

Trong khi:

```text
network timeout
```

có thể retry.

---

# 157. Redis Streams và ordering

Stream có ID tăng theo thứ tự append.

Nhưng application phải cẩn thận:

```text
message A
message B
```

nếu phân phối cho:

```text
worker-1
worker-2
```

thì processing completion order có thể:

```text
B hoàn thành
A hoàn thành
```

Do đó:

> Stream ordering ≠ global business processing ordering.

Nếu business yêu cầu:

```text
order 1001: created
order 1001: paid
order 1001: shipped
```

phải thiết kế concurrency model phù hợp.

---

# 158. Một cách giải quyết ordering

Partition theo entity:

```text
order:1001
order:1002
```

hoặc dùng stream/key grouping phù hợp để các event cần ordering được xử lý bởi cùng logical consumer sequence.

Hoặc:

```text
PostgreSQL state transition
```

enforce:

```text
created -> paid -> shipped
```

Đây là nguyên tắc quan trọng:

> Database/business invariant thường nên là lớp cuối bảo vệ correctness.

---

# 159. Redis và PostgreSQL nên cùng bảo vệ correctness

Ví dụ:

```text
Redis
    |
    | fast path
    v
Application
    |
    v
PostgreSQL constraint
```

Nếu race condition xảy ra ở cache:

```text
DB constraint
```

vẫn bảo vệ invariant.

Đây là architecture mạnh hơn việc đặt toàn bộ correctness vào Redis.

---

# 160. Những lỗi tư duy Redis phổ biến

## Sai 1

> Redis nhanh nên mọi thứ đưa vào Redis.

Sai vì:

```text
memory cost
durability
consistency
operational cost
```

---

## Sai 2

> Redis là database.

Không sai hoàn toàn, nhưng incomplete.

Redis là data structure server với nhiều database-like capability; cách thiết kế phải dựa vào workload.

---

## Sai 3

> Redis single-threaded nên một command luôn nhanh.

Sai.

```text
O(N)
big key
large payload
Lua
network
```

đều có thể làm latency tăng.

---

## Sai 4

> Pub/Sub là queue.

Sai.

Pub/Sub phù hợp với transient fan-out; semantics cơ bản là at-most-once. ([Redis](https://redis.io/docs/latest/develop/pubsub/ "Redis Pub/sub | Docs"))

---

## Sai 5

> Stream đảm bảo exactly-once.

Sai.

Streams hỗ trợ tracking pending/ack/recovery, nhưng business side effect vẫn có thể bị xử lý lại.

---

## Sai 6

> TTL là scheduler.

Sai.

TTL là expiration mechanism.

---

## Sai 7

> Redis lock giải quyết mọi race condition.

Sai.

Có những race condition nên giải quyết bằng:

```text
PostgreSQL transaction
row lock
unique constraint
idempotency key
```

---

## Sai 8

> AOF = PostgreSQL WAL.

Sai về semantics và durability model.

---

# 161. Một architecture thực tế cho stack của bạn

```text
                         Internet
                            |
                            v
                     Load Balancer
                            |
                    +-------+-------+
                    |               |
                    v               v
                PHP API 1       PHP API 2
                    |               |
                    +-------+-------+
                            |
             +--------------+--------------+
             |                             |
             v                             v
         PostgreSQL                    Redis
             |                       /    |    \
             |                      /     |     \
             |                  Cache   Stream Pub/Sub
             |                            |       |
             |                            |       v
             |                            |   Node.js
             |                            |      |
             |                            |   WebSocket
             |                            |      |
             v                            v      v
        durable data                  Workers -> Browser
```

Đây là một architecture có tính thực tế cao cho hệ thống web business.

---

# 162. Một request cụ thể

User:

```text
POST /orders
```

PHP:

```text
validate request
    ↓
PostgreSQL transaction
    ↓
create order
    ↓
create outbox event
    ↓
commit
```

HTTP trả:

```text
201 Created
```

Background dispatcher:

```text
outbox
    ↓
Redis Stream
```

Worker:

```text
Stream
    ↓
order worker
    ↓
process
    ↓
XACK
```

Realtime worker:

```text
order event
    ↓
Redis Pub/Sub
    ↓
Node.js
    ↓
WebSocket
    ↓
Browser
```

Đây là cách Redis thực sự trở thành infrastructure chứ không chỉ là “cache”.

---

# 163. Phân loại state khi thiết kế

Mỗi dữ liệu nên được đặt vào một trong các nhóm:

```text
1. Source of truth
2. Derived state
3. Cache
4. Ephemeral coordination state
5. Queue/event
6. Realtime transient event
```

Ví dụ:

```text
order record
    -> source of truth

user profile cache
    -> derived state

lock
    -> ephemeral coordination

email job
    -> queue/event

WebSocket notification
    -> transient event
```

Từ classification này mới quyết định Redis có phù hợp không.

---

# 164. Một checklist thiết kế Redis production

Trước khi thêm Redis vào feature, cần trả lời:

```text
[ ] Redis đang giữ loại state nào?
[ ] Source of truth ở đâu?
[ ] Nếu Redis mất thì sao?
[ ] TTL bao nhiêu?
[ ] Memory requirement bao nhiêu?
[ ] Key size bao nhiêu?
[ ] Value size bao nhiêu?
[ ] Expected QPS?
[ ] Read/write ratio?
[ ] P99 latency?
[ ] Command complexity?
[ ] Hot key?
[ ] Big key?
[ ] Cache stampede?
[ ] Eviction policy?
[ ] Persistence?
[ ] Replication?
[ ] Failover?
[ ] Retry?
[ ] Idempotency?
[ ] Ordering?
[ ] Backpressure?
[ ] Monitoring?
[ ] Security?
```

Nếu chưa trả lời được những câu này, feature chưa nên được coi là production-ready.

---

# 165. Mức độ kiến thức cần đạt

## Level 1 — User

Biết:

```text
GET
SET
DEL
TTL
```

---

## Level 2 — Backend Developer

Biết:

```text
String
Hash
List
Set
Sorted Set
Stream
Pub/Sub
cache-aside
rate limit
```

---

## Level 3 — Middle Backend

Hiểu:

```text
TTL
eviction
pipeline
atomicity
Lua
transaction
lock
cache invalidation
stampede
idempotency
worker
retry
```

---

## Level 4 — Senior / System Engineer

Hiểu:

```text
memory model
latency
network
replication
failover
Sentinel
Cluster
hash slots
hot key
big key
capacity planning
failure modes
consistency
observability
```

---

## Level 5 — Infrastructure / Distributed Systems

Đi sâu:

```text
Redis internals
event loop
data structure implementation
allocator
RDB
AOF
replication protocol
cluster protocol
failover semantics
distributed coordination
network partition
consistency model
tail latency
benchmark methodology
```

---

# 166. Tại sao nên học Redis theo data structure trước framework?

Ví dụ framework có:

```php
Cache::remember(...)
```

Nếu chỉ học framework:

```text
"Redis cache rất dễ dùng"
```

Nhưng không hiểu:

```text
TTL
serialization
network
stampede
eviction
failure
```

thì không thể debug production.

Ngược lại, nếu hiểu:

```text
SET
GET
EXPIRE
```

thì framework abstraction chỉ là:

```text
wrapper
```

---

# 167. Tại sao nên học Redis bằng thực nghiệm?

Một số kiến thức rất khó học bằng đọc:

```text
single-thread blocking
hot key
big key
network latency
cache stampede
replication lag
pending entries
duplicate processing
memory overhead
eviction
```

Tạo lab nhỏ:

```text
1000 requests
10000 requests
100000 requests
```

và đo:

```text
P50
P95
P99
throughput
CPU
memory
```

sẽ giúp hình thành intuition rất nhanh.

---

# 168. Thứ tự nghiên cứu chuyên sâu đề xuất

## Giai đoạn A

```text
Redis architecture
RESP
key/value
command execution
```

## Giai đoạn B

```text
String
Hash
List
Set
Sorted Set
```

## Giai đoạn C

```text
Stream
Pub/Sub
consumer groups
ack
pending
recovery
```

## Giai đoạn D

```text
TTL
expiration
eviction
memory
```

## Giai đoạn E

```text
transactions
WATCH
Lua
Functions
```

## Giai đoạn F

```text
cache patterns
stampede
penetration
avalanche
hot key
```

## Giai đoạn G

```text
persistence
RDB
AOF
replication
```

## Giai đoạn H

```text
Sentinel
Cluster
hash slots
failover
```

## Giai đoạn I

```text
production
observability
capacity planning
chaos testing
```

---

# 169. Những nguồn kiến thức nên đọc sâu sau tài liệu này

Tài liệu này nên được coi là **mental model + engineering guide**, không phải thay thế Redis Command Reference.

Các tài liệu chính thức nên dùng để đào sâu:

- Redis Data Types và Compare Data Types. ([Redis](https://redis.io/docs/latest/develop/data-types/?utm_source=chatgpt.com "Redis data types | Docs"))
    
- Redis Strings và command complexity. ([Redis](https://redis.io/docs/latest/develop/data-types/strings/ "Redis Strings | Docs"))
    
- Redis Streams và Consumer Groups. ([Redis](https://redis.io/resources/how-to-build-apps-using-redis-streams.pdf?utm_source=chatgpt.com "TUTORIAL
    How to Build Apps using Redis Streams
    Ros"))
    
- Redis Pub/Sub và delivery semantics. ([Redis](https://redis.io/docs/latest/develop/pubsub/ "Redis Pub/sub | Docs"))
    
- Redis Transactions / optimistic concurrency.
    
- Lua scripting / Redis Functions. ([Redis](https://redis.io/docs/latest/develop/programmability/eval-intro/ "Scripting with Lua | Docs"))
    
- Persistence. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/persistence/ "Redis persistence | Docs"))
    
- Replication. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/replication/ "Redis replication | Docs"))
    
- Sentinel. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/sentinel/ "High availability with Redis Sentinel | Docs"))
    
- Redis Cluster. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/scaling/ "Scale with Redis Cluster | Docs"))
    
- Memory optimization. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/ "Memory optimization | Docs"))
    
- Latency diagnosis. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/latency/ "Diagnosing latency issues | Docs"))
    

Tại thời điểm xây dựng tài liệu này, Redis Open Source 8.2.x là một mốc thực tế cần lưu ý; release notes hiện có Redis Open Source 8.2.7, được phát hành tháng 6/2026. Redis 8.2 cũng bổ sung các cải tiến liên quan đến Streams, cluster monitoring, bitmap và performance/memory. ([Redis](https://redis.io/docs/latest/operate/oss_and_stack/stack-with-enterprise/release-notes/redisce/redisos-8.2-release-notes/?utm_source=chatgpt.com "Redis Open Source 8.2 release notes | Docs"))

---

# 170. Kết luận cốt lõi

Nếu chỉ giữ lại một mental model duy nhất, hãy giữ:

```text
Redis
=
Data Structures
+
Server-side Operations
+
Fast In-memory Access
+
Network Service
+
Expiration / Eviction
+
Optional Persistence
+
Replication / HA
+
Programmability
+
Event / Streaming Capabilities
```

Và khi thiết kế backend:

```text
Requirement
    ↓
State thuộc loại gì?
    ↓
Access pattern?
    ↓
Consistency?
    ↓
Durability?
    ↓
Concurrency?
    ↓
Data structure?
    ↓
Command complexity?
    ↓
Memory?
    ↓
Network?
    ↓
Failure?
    ↓
Scale?
    ↓
Monitoring?
```

Redis không nên được học như:

```text
SET
GET
HGET
LPUSH
ZADD
XADD
PUBLISH
```

để rồi ghi nhớ command.

Nó nên được học như:

```text
problem
   ↓
data model
   ↓
data structure
   ↓
operation
   ↓
complexity
   ↓
execution
   ↓
network
   ↓
memory
   ↓
concurrency
   ↓
failure
   ↓
scaling
```

Khi tư duy được theo chuỗi này, bạn có thể nhìn một yêu cầu backend và tự quyết định:

```text
PostgreSQL?
Redis Cache?
Redis Hash?
Redis Set?
Sorted Set?
List?
Stream?
Pub/Sub?
Local cache?
Database transaction?
Outbox?
Worker?
WebSocket?
Kafka?
```

thay vì chọn Redis chỉ vì:

> “Redis nhanh.”

Đó mới là mức hiểu Redis có giá trị lâu dài đối với Software Engineer/System Engineer.