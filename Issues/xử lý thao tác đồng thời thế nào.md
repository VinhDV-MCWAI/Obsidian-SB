**

- Đồng thời ở tầng Request (Web Server & PHP)
    

- Vấn đề: 1.000 người cùng nhấn "Đặt hàng" tại một giây.
    
- Nguyên nhân: Do phần cứng có hạn (CPU/RAM). PHP-FPM hoạt động theo mô hình blocking, mỗi request chiếm một worker.
    
- Hậu quả: Hết worker, các request sau phải chờ (queue). Nếu hàng chờ quá dài, Web Server sẽ trả về lỗi 504 Gateway Timeout hoặc 502 Bad Gateway.
    
- Giải pháp:
    

- Scale Up: Tăng RAM/CPU.
    
- Scale Out: Chạy nhiều Server (Load Balancing).
    
- Asynchronous: Chuyển sang dùng các nền tảng non-blocking như Swoole hoặc Laravel Octane. Nó cho phép một worker xử lý hàng ngàn request cùng lúc bằng cách không đợi các tác vụ I/O (như gửi email, đọc DB).
    

- Giới hạn: Càng nhiều server thì chi phí càng cao và việc đồng bộ hóa dữ liệu giữa các server càng phức tạp.
    

- Đồng thời ở tầng Thao tác dữ liệu (Database Race Condition)
    

- ví dụ: Hệ thống chỉ còn 1 sản phẩm, nhưng 2 người cùng mua một lúc. Cả hai cùng check stock > 0 và cùng trừ tồn kho. Kết quả: tồn kho thành -1.
    
- Các nguy cơ (Concurrency Phenomena):
    

- Dirty Read: Đọc dữ liệu chưa được commit (dữ liệu rác).
    
- Lost Update: Hai giao dịch cùng sửa một dòng, người sau ghi đè hoàn toàn người trước.
    

- Ý tưởng giải pháp:
    

- Pessimistic Locking (Khóa bi quan):
    

- Cách này dùng khi logic của bạn quá phức tạp, không đơn thuần là tăng/giảm một con số mà cần phải đọc dữ liệu ra, chạy qua nhiều hàm kiểm tra (Validation), gọi sang API bên thứ ba, rồi mới quyết định sửa record. Bạn sẽ chủ động khóa dòng đó lại, cấm tất cả các truy vấn khác đọc/ghi cho đến khi bạn xử lý xong. 
    
- Cách làm: "Tôi đang dùng dòng này, cấm ai đụng vào". Trong SQL dùng SELECT ... FOR UPDATE.
    
- Cơ chế: Câu lệnh sinh ra có đuôi `SELECT ... FOR UPDATE`. Khi Request A đang giữ khóa này, Request B tới chạy cùng câu lệnh đó sẽ phải **đứng chờ (Block)** cho đến khi Request A hoàn thành Commit hoặc Rollback. 
    
- Kết quả: Đảm bảo tuyệt đối không sai lệch.
    
- Giới hạn: Làm chậm hệ thống vì các request khác phải xếp hàng đợi khóa được giải phóng. Dễ gây Deadlock (A đợi B, B đợi A).
    

- Optimistic Locking (Khóa lạc quan):
    

- Cách này áp dụng cho các hệ thống có lượng truy vấn cực lớn (High Traffic) nhưng tỷ lệ xung đột thực tế trên cùng một record lại thấp (ví dụ: Chỉnh sửa bài viết, cập nhật hồ sơ cá nhân). Bạn không muốn hệ thống bị chậm vì phải đứng xếp hàng chờ đợi nhau giống như Khóa bi quan. 
    
- Cách làm: Bạn thêm một cột `version` hoặc `updated_at` vào bảng. 
    

- 1. Request A đọc record lên, thấy `version = 1`. 
    
- 2. Request B cũng đọc record đó lên, thấy `version = 1`.
    
- 3. Request A xử lý xong trước và thực hiện Update: `UPDATE users SET name = 'A', version = 2 WHERE id = 1 AND version = 1`. (Thành công, version lên 2). 
    
- 4. Request B xử lý sau và thực hiện Update: `UPDATE users SET name = 'B', version = 2 WHERE id = 1 AND version = 1`. (Thất bại, vì lúc này version trong DB đã là 2, điều kiện `WHERE version = 1` không còn đúng nữa).
    
- Cách xử lý khi thất bại:** Trong code PHP, bạn bắt lấy ngoại lệ (Exception) khi update thất bại, sau đó cho hệ thống tự động tải lại dữ liệu mới (Retry) hoặc báo lỗi cho người dùng "Dữ liệu đã thay đổi bởi người khác, vui lòng thử lại". 
    

- Kết quả: Hiệu năng cực cao vì không ai phải đợi ai.
    
- Giới hạn: Nếu tỉ lệ xung đột quá cao (ví dụ săn sale 1 món đồ), nhiều request sẽ bị thất bại và phải thử lại (retry).
    

- Atomic Updates (SQL chuẩn, đơn giản hiệu quả, tận dụng tối đa sức mạnh DB):=
    

- Database Engine (như MySQL InnoDB) có cơ chế khóa hàng (Row-level locking) mặc định khi làm việc với lệnh `UPDATE`. Các câu lệnh sẽ được xếp hàng xử lý tuần tự ngay tại nhân DB, đảm bảo không bao giờ bị trừ sai. Nó thực hiện theo kiểu, run điều kiện where trước, lock hàng, cập nhật thành công, giải phóng khóa.
    
- Hiểu đúng khi xử lý đồng thời thay đổi dữ liệu (create, update, delete) chỉ cần đặt đủ điều kiện vẹn toàn dữ liệu thì sẽ không có vấn đề gì cả. Chỉ cần vấn đề vẹn toàn trong câu truy vấn đảm bảo thì việc xử lý đồng thời còn lại db tận dụng toàn bộ sức mạnh, vừa đúng đắn, đơn giản và hiệu quả
    
- VD 1: có 2 request cùng đặt hàng có số lượng là 1 trong kho, nếu không xử lý toàn vẹn dữ liệu như update stock = stock - 1 where id = … thì khi xử lý diễn ra cả 2 đều diễn ra, kết quả stock = -1 do bị trừ 2 lần, điều này không đúng vì stock không < 0. Giải pháp ở đây đơn giản là thêm điều kiện stock > 0. Xử lý nào xong trước thì thực hiện commit, xử lý sau stock <= 0 nó sẽ rollback dữ liệu, việc còn lại chỉ đơn giản là thông báo dữ liệu đã bị cập nhật bởi người khác
    
- VD 2: 1 người đăng ký tài khoản cần admin xác thực. Có 2 admin cùng xác thực cùng lúc, update status = true where user_id = 1. lúc này cả 2 request cùng thực hiện thành công, status vẫn là true. Nhưng logic tiếp theo thông báo cho user rằng họ được kích hoạt được gửi qua mail, người dùng nhận được 2 mail với cùng nội dung họ đã được xác thực. Việc này đơn giản giải quyết bằng sql toàn vẹn thêm điều kiện là where status = false. kết quả sẽ chỉ có 1 transaction thực hiện thành công.
    

- Xử lý hàng đợi: 
    

- Nếu record đó quá hot (ví dụ: Số lượng vé ca nhạc của một ca sĩ nổi tiếng, hàng ngàn người cùng tranh nhau 1 record), việc ép Database chịu tải bằng các lệnh Lock sẽ làm sập DB.
    
- Giải pháp: Bạn không cho Request chạm trực tiếp vào DB nữa. * Toàn bộ yêu cầu chỉnh sửa record đó sẽ được đẩy vào một **Message Queue (như Redis Queue hoặc RabbitMQ)**.
    
- Phía sau, bạn chỉ bật **đúng 1 Worker (một tiến trình PHP duy nhất)** để nhặt từng yêu cầu trong Queue ra xử lý tuần tự (FIFO - First In First Out). Vì chỉ có duy nhất 1 luồng xử lý ghi vào DB tại một thời điểm, bài toán xử lý đồng thời hoàn toàn bốc hơi.
    







**