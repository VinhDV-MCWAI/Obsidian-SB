**

+ Bài toán "Đồng bộ dữ liệu trên nhiều server" (Data Synchronization) xuất hiện khi hệ thống của bạn phát triển lên quy mô lớn, cần chạy nhiều cụm server (Cluster) ở các vùng địa lý khác nhau để tăng tốc độ truy cập hoặc để dự phòng thảm họa (High Availability / Disaster Recovery).

Khi có nhiều xử lý đồng thời thay đổi dữ liệu trên nhiều server khác nhau, thách thức lớn nhất là đảm bảo Tính nhất quán của dữ liệu (Data Consistency) — làm sao để Server A sửa dữ liệu thì Server B, C cũng thấy dữ liệu mới ngay lập tức mà không bị xung đột.

Dưới đây là các mô hình và giải pháp thực tế được phân loại theo từng tầng kiến trúc hệ thống:

## 1. Đồng bộ ở tầng Database (Phổ biến và Triệt để nhất)

Thay vì cố gắng viết code PHP để gọi API cập nhật từng server, người ta sẽ đẩy trách nhiệm đồng bộ xuống cho tầng Database Engine xử lý thông qua các mô hình cấu trúc sau:

### Mô hình Master - Slave (Replication)

- Cơ chế: Bạn có 1 Server Master (chuyên nhận các lệnh Ghi/Sửa/Xóa dữ liệu) và nhiều Server Slave (chỉ nhận lệnh Đọc dữ liệu). Khi dữ liệu được ghi vào Master, Master sẽ tự động đồng bộ (thường là bất đồng bộ) xuống các Slave qua file Log (Binlog trong MySQL).
    
- Ưu điểm: Tách biệt tải Đọc/Ghi, cấu hình cực kỳ đơn giản.
    
- Nhược điểm: Có một khoảng trễ nhỏ (Replication Lag). Nếu khách hàng vừa bấm mua (ghi vào Master) rồi tải lại trang ngay lập tức (đọc từ Slave chưa kịp đồng bộ), họ sẽ thấy dữ liệu cũ.
    

### Mô hình Master - Master (Multi-Master Replication)

- Cơ chế: Cả Server A và Server B đều có quyền Đọc và Ghi. Dữ liệu ghi vào A sẽ tự đồng bộ sang B và ngược lại.
    
- Tính thời sự & Nguy cơ: Mô hình này rất dễ bị Xung đột dữ liệu (Write Conflict). Ví dụ: Cùng một mili giây, Server A cập nhật tên User thành "Nguyễn Văn A", Server B cập nhật thành "Nguyễn Văn B". Khi hai server đồng bộ cho nhau, DB sẽ không biết giữ lại giá trị nào.
    
- Giải pháp xử lý xung đột: Thường dựa vào luật Last-Write-Wins (giữ lại dữ liệu của server có Timestamp muộn hơn) hoặc phải cấu hình cơ chế tự động tăng ID lệch nhau (Server A tăng ID lẻ, Server B tăng ID chẵn).
    

## 2. Đồng bộ ở tầng Ứng dụng: Mô hình Event-Driven (Kiến trúc hướng sự kiện)

Nếu các server của bạn không dùng chung một loại Database (ví dụ: Server 1 dùng MySQL để lưu thông tin, Server 2 dùng Elasticsearch để phục vụ tìm kiếm, Server 3 dùng Redis để làm Cache), bạn bắt buộc phải đồng bộ ở tầng ứng dụng.

Giải pháp tối ưu nhất lúc này là sử dụng Message Broker (Kafka, RabbitMQ, Amazon SQS).

### Cơ chế hoạt động (Publish/Subscribe):

1. Khi một xử lý thay đổi dữ liệu diễn ra tại Server 1, thay vì gọi API trực tiếp sang Server 2 và 3, Server 1 sẽ bắn một "Sự kiện" (Event) vào Message Broker (Ví dụ: Sự kiện UserUpdated kèm theo thông tin mới).
    
2. Server 2 và Server 3 đã đăng ký (Subscribe) lắng nghe Sự kiện này từ trước. Hệ thống Message Broker sẽ tự động phân phối bản sao của sự kiện đó đến cả 2 server.
    
3. Server 2 và 3 nhận được sự kiện sẽ tự cập nhật vào Database nội bộ của riêng mình.
    

Tại sao cách này tốt? Nó giúp các server hoàn toàn độc lập (Decoupling). Nếu Server 2 đột ngột bị sập, Message Broker sẽ giữ hộ sự kiện đó trong hàng đợi. Khi Server 2 sống lại, nó sẽ nhặt lại sự kiện để xử lý tiếp mà không sợ bị mất dữ liệu.

## 3. Khái niệm cốt lõi cần nhớ: Định lý CAP (CAP Theorem)

Khi đi phỏng vấn về vấn đề đồng bộ nhiều server, bạn bắt buộc phải biết thuật ngữ này để chứng minh tư duy kiến trúc của mình. Định lý CAP chỉ ra rằng, trong một hệ thống phân tán, bạn chỉ có thể chọn tối đa 2 trong 3 yếu tố sau:

1. C (Consistency): Tính nhất quán — Mọi server đều thấy dữ liệu giống hệt nhau tại cùng một thời điểm.
    
2. A (Availability): Tính sẵn sàng — Mọi request gửi lên đều nhận được phản hồi (dù server đó dữ liệu cũ hay mới).
    
3. P (Partition Tolerance): Tính chịu lỗi mạng — Hệ thống vẫn chạy được ngay cả khi kết nối giữa các server bị đứt gãy.
    

- Lựa chọn thực tế: Trong môi trường Internet, mạng chắc chắn sẽ có lúc bị lỗi (bắt buộc phải có P). Vì vậy bạn chỉ được chọn CP hoặc AP:
    

- Nếu chọn CP (Ưu tiên chính xác): Khi đồng bộ giữa 2 server bị lỗi, hệ thống sẽ khóa lại cấm người dùng sửa dữ liệu để tránh sai lệch (Ứng dụng cho Ngân hàng, Ví điện tử).
    
- Nếu chọn AP (Ưu tiên tốc độ): Hệ thống chấp nhận cho người dùng sửa dữ liệu trên Server A, còn Server B từ từ cập nhật sau (Chấp nhận dữ liệu không đồng nhất trong vài giây - Ứng dụng cho Facebook Like, Đăng bài viết, Comment).
    

  

+ Conflict Resolution: Áp dụng timestamp hoặc kiểm tra phiên bản (versioning) để xác định dữ liệu chính xác.

  

#### - Hệ thống gặp vấn đề khi upload file lớn xử lý thế nào ?

+ Tăng giới hạn PHP: Điều chỉnh upload_max_filesize và post_max_size trong php.ini.

+ Chunked Upload: Chia file thành nhiều phần nhỏ và ghép lại sau khi tải lên.

+ Xử lý không đồng bộ: Dùng AJAX hoặc WebSocket để cập nhật trạng thái tải.

**