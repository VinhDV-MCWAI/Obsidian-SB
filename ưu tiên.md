
# Điều đầu tiên: Không tồn tại một kỹ sư biết tất cả

Đây là điều gần như chắc chắn.

Hãy thử liệt kê riêng Backend thôi.

- Computer Architecture
    
- Operating System
    
- Linux
    
- Network
    
- Database
    
- Distributed System
    
- Message Queue
    
- Cache
    
- Security
    
- Compiler
    
- Cloud
    
- Kubernetes
    
- Docker
    
- Monitoring
    
- Observability
    
- CI/CD
    
- Performance
    
- Testing
    
- DDD
    
- Event Driven
    
- System Design
    
- Algorithms
    
- Concurrency
    
- Storage Engine
    
- TCP internals
    
- Memory allocator
    
- JVM
    
- CLR
    
- Go Runtime
    
- Redis internals
    
- PostgreSQL internals
    
- Kafka internals
    
- Raft
    
- Paxos
    
- ...
    

Mỗi chủ đề này có thể học vài năm.

Ví dụ:

Một Database Engineer ở PostgreSQL thường chỉ nghiên cứu

- MVCC
    
- WAL
    
- Vacuum
    
- Query Planner
    
- Index
    

đã mất nhiều năm.

Một DevOps Senior có thể hiểu Kubernetes rất sâu.

Nhưng lại không giỏi Dynamic Programming.

Điều đó rất bình thường.

---

# Thực tế Big Tech cũng không yêu cầu điều đó

Đây là điều rất nhiều người hiểu sai.

Người ta nhìn JD rồi nghĩ:

> "Ôi trời phải biết hết."

Không.

Họ muốn **breadth** và **depth**.

Tức là

Có chiều rộng.

Có một vài chiều sâu.

Không phải chiều sâu ở mọi thứ.

Ví dụ.

Một Backend Senior ở Google.

Có thể

★★★★★

- Concurrency
    
- Distributed System
    
- Go Runtime
    

★★★★☆

- Linux
    
- TCP
    
- Memory
    

★★★☆☆

- Kubernetes
    

★★☆☆☆

- Frontend
    

★☆☆☆☆

- Machine Learning
    

Không ai chê họ.

---

# Big Tech tuyển "Engineer"

Điều này bạn nói rất đúng.

Họ tuyển người giải quyết vấn đề.

Không tuyển người thuộc API.

Đó là lý do họ hỏi

- Complexity
    
- Thread
    
- Memory
    
- Lock
    
- Deadlock
    
- Cache
    
- CPU
    
- OS
    

vì đây là thứ giúp giải quyết mọi framework.

---

# Nhưng cũng không phải ai trong Big Tech đều ở mức thần thánh

Đây là hiểu lầm phổ biến nhất.

Một công ty như Google có hơn một trăm nghìn nhân viên kỹ thuật.

Không phải tất cả đều viết Storage Engine.

Không phải tất cả đều viết Kubernetes.

Không phải tất cả đều viết Compiler.

Ví dụ.

Team Gmail.

Quan tâm nhiều

- Backend
    
- Distributed System
    
- Storage
    

Team Chrome

Quan tâm

- C++
    
- Rendering
    
- Browser
    

Team Android

Quan tâm

- Java
    
- Kotlin
    
- Runtime
    

Team TPU

Quan tâm

- Hardware
    

Không ai giỏi toàn bộ.

---

# Bạn nói một điều rất giống thực tế

Bạn viết

> đụng đến đâu thì tìm hiểu sâu đến đó

Đây thực ra là cách rất nhiều kỹ sư giỏi học.

Ví dụ.

Bạn gặp Deadlock.

Bạn mất 2 tuần.

Đọc

- Java Concurrency in Practice
    
- Linux Futex
    
- pthread
    
- JVM monitor
    

Đến lúc hiểu.

Sau đó nhiều năm không đọc nữa.

Nhưng kiến thức vẫn ở đó.

5 năm.

Bạn xử lý thêm

- Redis
    
- Kafka
    
- PostgreSQL
    

Mỗi lần thêm một lớp.

Đó là cách kinh nghiệm được tích lũy.

Không phải đọc hết trước.

---

# Người Đức mà bạn nhắc tới

Tôi tin đây là điều rất gần với thực tế.

Ở Đức, Mỹ, Bắc Âu...

Có rất nhiều kỹ sư kiểu này.

Ví dụ.

Bug

Memory Leak.

Họ dành

3 ngày

chỉ để đọc source code.

Không ai nói

"Sao anh chậm vậy?"

Ngược lại.

Nếu sửa sai.

Hậu quả có thể hàng triệu USD.

Nên người ta ưu tiên đúng.

Không ưu tiên nhanh.

Đây là khác biệt giữa nhiều công ty sản phẩm trưởng thành và môi trường giao hàng gấp.

Tuy nhiên, điều đó **không có nghĩa** họ nhớ sẵn mọi chi tiết. Họ có nền tảng đủ tốt để biết **tìm ở đâu, đọc gì, và kiểm chứng ra sao** khi gặp vấn đề mới.

---

# Product có nhiều thời gian hơn outsource?

Thường là đúng.

Không phải lúc nào.

Nhưng nhìn chung.

Outsource

Deadline theo khách hàng.

Feature liên tục.

Product

Quan tâm

- maintainability
    
- scalability
    
- monitoring
    
- optimization
    

vì họ phải nuôi sản phẩm nhiều năm.

Do đó.

Engineer học được nhiều hơn.

---

# Bây giờ đến câu hỏi quan trọng nhất

> Mặt bằng chung của lập trình viên cùng cấp với tôi thế nào?

Bạn nói:

25 tuổi.

4 năm kinh nghiệm.

Backend.

Outsource.

Muốn vào Product.

Theo quan sát từ thị trường Việt Nam và quốc tế, bức tranh thường gần như sau (đây là mô hình khái quát chứ không phải số liệu đo đạc tuyệt đối):

|Nhóm|Tỷ lệ ước lượng|Đặc điểm|
|---|--:|---|
|Làm được theo framework, ít quan tâm nền tảng|~40–50%|Hoàn thành tính năng, phụ thuộc nhiều vào kinh nghiệm thực tế và tìm kiếm|
|Có nền tảng CS khá, biết phân tích và tối ưu|~30–40%|Hiểu cấu trúc dữ liệu, độ phức tạp, DB, thread ở mức sử dụng tốt|
|Rất mạnh về nền tảng và đào sâu kỹ thuật|~10–20%|Có thể giải thích cơ chế bên trong, thiết kế hệ thống, phân tích trade-off rõ ràng|
|Chuyên gia đầu ngành hoặc kỹ sư nổi bật|<1–2%|Thiết kế hệ thống lớn, đóng góp công nghệ lõi, nghiên cứu hoặc dẫn dắt kỹ thuật|

Điều quan trọng là: **đa số kỹ sư không ở nhóm cuối**, kể cả ở các công ty rất lớn.

---

# Tôi nghĩ bạn đang đánh giá sai một điểm

Bạn đang lấy **union của toàn bộ kiến thức ngành** rồi nghĩ:

> Một Middle phải biết hết.

Không.

Hãy tưởng tượng.

Có khoảng 30 lĩnh vực.

Middle mạnh thường:

- hiểu khá khoảng 15–20 lĩnh vực,
    
- đào sâu khoảng 4–8 lĩnh vực,
    
- rất sâu 1–3 lĩnh vực.
    

Senior có xu hướng mở rộng chiều sâu hoặc chiều rộng hơn, chứ cũng không "max" tất cả.

---

# Về tài chính

Bạn hỏi:

> Nếu học hết những thứ đó thì phải kiếm được rất nhiều tiền đúng không?

Không có quan hệ tuyến tính.

Thu nhập chịu ảnh hưởng bởi nhiều yếu tố:

- kỹ năng kỹ thuật,
    
- khả năng giải quyết vấn đề có giá trị,
    
- tiếng Anh,
    
- giao tiếp,
    
- viết tài liệu,
    
- thiết kế hệ thống,
    
- đàm phán,
    
- mức độ ảnh hưởng tới sản phẩm,
    
- quy mô và thị trường của công ty.
    

Có người rất giỏi kỹ thuật nhưng làm ở công ty nhỏ hoặc thị trường lương thấp nên thu nhập không cao. Ngược lại, một kỹ sư khá ở công ty trả lương theo thị trường Mỹ có thể có thu nhập vượt trội.

Kiến thức là điều kiện rất quan trọng, nhưng không phải điều kiện duy nhất.

---

# Với tình hình của bạn

Từ những gì tôi biết trong cuộc trò chuyện này:

- 25 tuổi.
    
- Khoảng 4 năm kinh nghiệm Backend.
    
- Chủ yếu làm outsourcing.
    
- Đã bắt đầu quan tâm đến CS Fundamentals, Database, System Design và tư duy kỹ thuật thay vì chỉ framework.
    
- Muốn chuyển sang product
    

Tôi không nghĩ mục tiêu là quá tham vọng nếu bạn chuẩn bị tốt cho phỏng vấn. Điều tôi **không khuyến nghị** là cố học hết toàn bộ danh sách CS Fundamentals trong một tháng. Điều đó vừa không thực tế, vừa không phản ánh cách các kỹ sư giỏi phát triển.

Nếu tôi phải xây dựng chiến lược trong **1 tháng**, tôi sẽ ưu tiên những chủ đề có xác suất xuất hiện cao và mang lại giá trị lớn:

1. Data Structures & Algorithms cơ bản và phân tích độ phức tạp.
    
2. Thread, Process, Memory, Deadlock, Synchronization.
    
3. Database (Index, Transaction, Isolation, Lock, Query Plan).
    
4. System Design mức Middle (cache, load balancer, message queue, scaling, trade-off).
    
5. Linux, Network và HTTP ở mức phục vụ backend.
    
6. Ôn lại toàn bộ dự án bạn từng làm để có thể giải thích các quyết định kỹ thuật và bài học rút ra.
    


Tôi đang tìm hiểu về data structure của array, hãy giúp tôi trả lời các câu hỏi:

nó là gì ? Nó lưu dữ liệu như thế nào trong bộ nhớ?
Các operation chính là gì?
Độ phức tạp Big-O của từng operation?
Ưu điểm và nhược điểm là gì?
Trade-off so với Data Structure khác?
Khi nào nên sử dụng trong hệ thống thực tế?
Các thư viện/ngôn ngữ hiện thực nó như thế nào? (ví dụ Java ArrayList, php v.v.)
mối liên hệ giữa cấu trúc dữ liệu → cách tổ chức trong bộ nhớ → độ phức tạp → hiệu năng → trade-off

Đó là chiến lược có tỷ lệ lợi ích trên thời gian rất cao.

---

Điều cuối cùng tôi muốn nói là thế này.

Tôi không nghĩ câu hỏi lớn nhất của bạn là **"mình có đủ giỏi không?"**.

Tôi nghĩ câu hỏi thực sự là:

> **"Tiêu chuẩn mình đang tự đặt ra có phản ánh đúng tiêu chuẩn của ngành không?"**

Theo đánh giá của tôi, **bạn đang đặt tiêu chuẩn cao hơn mức mà đa số kỹ sư Middle thực tế đạt được**. Điều đó tốt nếu nó trở thành định hướng dài hạn. Nhưng nếu bạn dùng chính tiêu chuẩn đó để đánh giá bản thân trong một khoảng thời gian chỉ một tháng, bạn rất dễ kết luận sai rằng mình "chưa đủ", trong khi thực tế mục tiêu trước mắt chỉ là chứng minh rằng bạn có nền tảng tốt, biết suy nghĩ như một kỹ sư, và có thể tiếp tục phát triển nhanh khi vào môi trường product. Những phẩm chất đó thường có giá trị hơn việc cố gắng biết mọi chủ đề ở mức chuyên gia.