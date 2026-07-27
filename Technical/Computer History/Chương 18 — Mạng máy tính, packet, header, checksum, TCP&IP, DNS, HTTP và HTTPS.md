Nếu các chương trước đã nói về cách máy tính lưu và xử lý thông tin bên trong một máy, thì chương này mở ra câu hỏi tiếp theo:

> **Làm thế nào để nhiều máy tính trao đổi thông tin với nhau một cách tin cậy?**

Đây là bước rất quan trọng trong lịch sử khoa học máy tính.

Khi một máy tính không còn đứng một mình mà phải kết nối với máy khác, toàn bộ bài toán thay đổi:

- dữ liệu phải đi qua dây, sóng, switch, router
    
- dữ liệu có thể bị chia nhỏ
    
- dữ liệu có thể mất gói
    
- dữ liệu có thể đến sai thứ tự
    
- dữ liệu có thể bị đọc nhầm
    
- dữ liệu có thể bị giả mạo
    
- dữ liệu phải được định tuyến đúng nơi
    

Mạng máy tính sinh ra để giải quyết chính những vấn đề này.

---

# 1. Vì sao phải có mạng?

Ban đầu, máy tính là một hệ thống đơn lẻ.

Nhưng rất sớm, con người đã cần:

- chia sẻ dữ liệu giữa các máy
    
- truy cập từ xa
    
- truyền file
    
- làm việc nhiều người dùng
    
- kết nối các hệ thống lớn
    
- chia tài nguyên tính toán
    

Nếu mỗi máy chỉ tự giữ dữ liệu của mình, hệ thống sẽ rất hạn chế.

Mạng biến các máy riêng lẻ thành một hệ thống liên thông.

Nó là nền của:

- Internet
    
- web
    
- API
    
- email
    
- chat
    
- cloud
    
- distributed systems
    
- microservices
    

---

# 2. Vấn đề cốt lõi của mạng: dữ liệu phải đi xa

Trong máy đơn lẻ, dữ liệu chỉ đi giữa CPU, RAM, storage.

Trong mạng, dữ liệu phải đi:

- qua NIC
    
- qua cáp hoặc sóng
    
- qua switch
    
- qua router
    
- qua nhiều thiết bị trung gian
    
- tới đúng máy đích
    

Mỗi chặng đều có thể sinh lỗi.

Vì vậy, mạng không chỉ là “truyền dữ liệu”.  
Nó là một hệ thống có cơ chế:

- đóng gói
    
- định tuyến
    
- kiểm tra lỗi
    
- xác nhận
    
- truyền lại
    
- kiểm soát luồng
    
- bảo mật
    

---

# 3. Packet là gì?

Để truyền dữ liệu trên mạng, người ta không gửi nguyên cả khối dữ liệu lớn một lần theo kiểu ngây thơ.

Dữ liệu được chia thành các phần nhỏ gọi là **packet** hoặc **frame** tùy tầng.

Packet là một đơn vị truyền thông mang theo:

- phần dữ liệu thật
    
- và thông tin điều khiển
    

Tại sao phải chia nhỏ?

Vì:

- đường truyền có giới hạn
    
- lỗi có thể xảy ra
    
- cần truyền lại từng phần nếu hỏng
    
- cần định tuyến hiệu quả
    
- cần chia tải giữa nhiều thiết bị
    

Packet là một giải pháp cực kỳ thực dụng.

---

# 4. Header là gì?

Một packet không chỉ có payload.

Nó thường có **header**.

Header là phần thông tin ở đầu gói tin, chứa các metadata cần thiết để mạng hoạt động.

Ví dụ header có thể chứa:

- địa chỉ nguồn
    
- địa chỉ đích
    
- loại giao thức
    
- số thứ tự
    
- TTL
    
- checksum
    
- cổng nguồn / cổng đích
    
- thông tin điều khiển khác
    

Nếu payload là nội dung thư, thì header giống như phong bì và thông tin gửi thư.

Không có header, mạng không biết:

- gói này từ đâu đến
    
- cần gửi đi đâu
    
- có bị hỏng không
    
- có phải ghép lại không
    
- có còn được phép chuyển tiếp không
    

---

# 5. Tại sao phải có nhiều tầng?

Mạng quá phức tạp nếu mọi thứ nằm trong một lớp duy nhất.

Vì thế người ta chia thành các tầng trừu tượng.

Ý tưởng này rất giống toàn bộ khoa học máy tính:

- bit thành byte
    
- byte thành ký tự
    
- byte thành file
    
- file thành database
    
- packet thành mạng
    

Mỗi tầng chỉ quan tâm một phần vấn đề.

Điều này giúp hệ thống:

- dễ thiết kế
    
- dễ thay thế
    
- dễ chuẩn hóa
    
- dễ mở rộng
    

---

# 6. Mô hình TCP/IP

Trong thực tế Internet, TCP/IP là bộ giao thức nền tảng cực kỳ quan trọng.

Nó không phải chỉ là một giao thức đơn lẻ, mà là một họ giao thức.

Ý tưởng tổng quát là chia trách nhiệm thành các lớp:

- lớp liên kết
    
- lớp mạng
    
- lớp vận chuyển
    
- lớp ứng dụng
    

Mỗi lớp giải quyết một loại vấn đề riêng.

Ví dụ:

- lớp mạng lo định tuyến
    
- lớp vận chuyển lo giao tiếp đầu cuối và độ tin cậy
    
- lớp ứng dụng lo giao thức của chương trình cụ thể
    

Mô hình này là một trong những thành công lớn nhất của kỹ thuật mạng.

---

# 7. IP là gì?

IP là giao thức lớp mạng.

Nó phụ trách:

- đánh địa chỉ máy
    
- định tuyến gói tin
    
- chuyển gói qua nhiều mạng con
    
- đảm bảo packet có thể tìm đường tới đích
    

IP không cam kết mọi gói sẽ tới đúng thứ tự hoặc không mất.

Nó chủ yếu lo chuyện:

> gói này phải đi tới đâu?

Đây là tư duy rất quan trọng:

- IP lo “đi đường”
    
- còn các lớp trên mới lo “có tới đủ, đúng và nguyên vẹn không”
    

---

# 8. TCP là gì?

TCP là giao thức lớp vận chuyển chú trọng độ tin cậy.

Nó giải quyết các vấn đề mà mạng IP không tự giải quyết:

- chia dữ liệu thành nhiều segment
    
- đánh số thứ tự
    
- ghép lại đúng thứ tự
    
- phát hiện gói mất
    
- truyền lại khi cần
    
- kiểm soát luồng
    
- điều chỉnh tắc nghẽn
    

Nói ngắn gọn:

> TCP biến một đường truyền không tin cậy thành một kênh đáng tin cậy hơn cho ứng dụng.

Đây là lý do nhiều hệ thống ứng dụng vẫn thích TCP khi cần tính đúng đắn.

---

# 9. UDP là gì?

UDP là một giao thức vận chuyển khác, đơn giản hơn TCP.

Nó không mang nhiều cơ chế đảm bảo như TCP.

Ưu điểm:

- nhẹ
    
- nhanh
    
- ít overhead
    

Nhược điểm:

- không đảm bảo giao hàng
    
- không đảm bảo thứ tự
    
- không tự truyền lại như TCP
    

UDP phù hợp với các tình huống:

- độ trễ thấp hơn độ tin cậy tuyệt đối
    
- audio/video realtime
    
- game
    
- một số kiểu telemetry hoặc protocol tự xử lý riêng
    

---

# 10. Vì sao mạng cần checksum?

Trong quá trình truyền, bit có thể bị lỗi do:

- nhiễu
    
- suy hao
    
- thiết bị lỗi
    
- đường truyền không ổn định
    
- va chạm mức vật lý
    

Checksum là một giá trị kiểm tra giúp phát hiện lỗi trong dữ liệu.

Ý tưởng rất đơn giản:

- bên gửi tính một giá trị kiểm tra
    
- bên nhận tính lại
    
- nếu không khớp, dữ liệu có thể đã bị hỏng
    

Checksum không luôn sửa được lỗi, nhưng giúp phát hiện lỗi.

Đây là một lớp bảo vệ rất quan trọng trong network stack.

---

# 11. Packet loss, reorder và duplication

Khi truyền dữ liệu, nhiều vấn đề có thể xảy ra:

- packet bị mất
    
- packet đến sai thứ tự
    
- packet bị lặp
    
- packet đến trễ
    

Ứng dụng bình thường không thể giả định mạng hoàn hảo.

Vì thế giao thức phải thiết kế để chịu được các bất thường này.

TCP dùng sequence number và cơ chế xác nhận để giải quyết.

Đây là lý do các hệ thống network rất khác với việc gọi hàm nội bộ trong một chương trình.

---

# 12. DNS là gì?

Khi người dùng gõ tên miền như `example.com`, máy tính không trực tiếp hiểu tên đó để định tuyến.

Máy cần IP.

DNS là hệ thống phân giải tên miền thành địa chỉ mạng.

Nó giải quyết vấn đề:

> con người nhớ tên dễ hơn nhớ số IP.

Ví dụ:

- `google.com` thay cho một địa chỉ IP cụ thể
    
- `api.company.com` thay cho địa chỉ máy chủ
    

DNS là một trong những hệ thống nền tảng nhất của Internet.

---

# 13. Vì sao DNS cần thiết?

Nếu không có DNS, người dùng phải nhớ IP.

Điều này là không thực tế vì:

- IP có thể thay đổi
    
- hệ thống có thể mở rộng
    
- dịch vụ có thể phân tải
    
- máy chủ có thể nằm sau load balancer
    
- một tên miền có thể trỏ tới nhiều máy
    

DNS giúp tách:

- tên có ý nghĩa cho người
    
- địa chỉ có ý nghĩa cho máy
    

Đây là một lớp trừu tượng rất đẹp.

---

# 14. DNS không chỉ là “tra IP”

DNS còn liên quan tới:

- TTL
    
- cache
    
- record type
    
- độ trễ phân giải
    
- tính sẵn sàng
    
- an toàn tên miền
    

Nếu DNS hỏng, rất nhiều dịch vụ sẽ lỗi dây chuyền dù server thật vẫn đang chạy.

Đây là một ví dụ cho thấy hạ tầng nền quan trọng không kém ứng dụng.

---

# 15. HTTP là gì?

HTTP là giao thức lớp ứng dụng rất phổ biến của web.

Nó quy định cách client và server trao đổi:

- request
    
- response
    
- header
    
- status code
    
- method
    
- body
    

HTTP là nền của:

- web browser
    
- REST API
    
- many service-to-service calls
    
- webhooks
    
- reverse proxy
    
- load balancer
    

Nói đơn giản:

> HTTP là ngôn ngữ giao tiếp phổ biến nhất giữa ứng dụng web và server.

---

# 16. Tư duy request/response

HTTP dùng mô hình:

- client gửi request
    
- server xử lý
    
- server trả response
    

Trong request thường có:

- method
    
- path
    
- header
    
- body
    

Trong response thường có:

- status code
    
- header
    
- body
    

Mô hình này rất thành công vì:

- đơn giản
    
- rõ ràng
    
- dễ mở rộng
    
- dễ debug
    
- dễ cache
    
- dễ proxy
    

---

# 17. Các method HTTP

Một số method phổ biến:

- `GET`
    
- `POST`
    
- `PUT`
    
- `PATCH`
    
- `DELETE`
    

Mỗi method có ý nghĩa nghiệp vụ khác nhau.

Ví dụ:

- `GET`: lấy dữ liệu
    
- `POST`: tạo dữ liệu mới hoặc thực hiện hành động
    
- `PUT`: thay thế tài nguyên
    
- `PATCH`: cập nhật một phần
    
- `DELETE`: xóa tài nguyên
    

Nếu dùng sai method, API sẽ khó hiểu và khó chuẩn hóa.

---

# 18. Status code là gì?

Status code phản ánh kết quả xử lý của server.

Ví dụ:

- `200` thành công
    
- `201` tạo mới thành công
    
- `400` request lỗi
    
- `401` chưa xác thực
    
- `403` không có quyền
    
- `404` không tìm thấy
    
- `500` lỗi server
    

Status code giúp client hiểu nhanh kết quả.

Nó là một phần rất quan trọng trong thiết kế API.

---

# 19. HTTPS là gì?

HTTPS là HTTP chạy trên nền mã hóa bảo mật TLS.

Mục tiêu:

- mã hóa dữ liệu
    
- ngăn nghe lén
    
- ngăn sửa đổi giữa đường
    
- xác thực server
    
- tăng độ tin cậy khi truyền dữ liệu
    

Nếu HTTP là giao tiếp văn bản công khai, thì HTTPS là giao tiếp có mã hóa và xác thực.

---

# 20. Vì sao phải dùng HTTPS?

Nếu chỉ dùng HTTP thuần:

- người trung gian có thể đọc dữ liệu
    
- có thể sửa dữ liệu
    
- có thể đánh cắp token
    
- có thể giả mạo server
    

HTTPS giải quyết các rủi ro này ở mức giao thức.

Trong hệ thống hiện đại, HTTPS gần như là bắt buộc.

---

# 21. TLS làm gì?

TLS cung cấp các tính chất:

- confidentialiy: giữ bí mật dữ liệu
    
- integrity: chống sửa dữ liệu
    
- authenticity: xác thực bên gửi hoặc server
    

Nó dùng:

- chứng chỉ
    
- khóa công khai
    
- khóa phiên
    
- cơ chế bắt tay
    
- mã hóa đối xứng sau khi bắt tay
    

Đây là lớp bảo mật nền tảng của Internet hiện đại.

---

# 22. Không phải mọi lỗi mạng đều giống nhau

Khi một request thất bại, nguyên nhân có thể là:

- DNS không phân giải được
    
- kết nối TCP thất bại
    
- TLS handshake lỗi
    
- server timeout
    
- proxy chặn
    
- packet loss
    
- backend lỗi
    
- authentication fail
    

Backend engineer cần biết tầng nào đang hỏng.

Nếu không, debug sẽ rất khó.

---

# 23. Nút thắt hiệu năng trong mạng

Hiệu năng mạng phụ thuộc vào:

- latency
    
- bandwidth
    
- packet loss
    
- congestion
    
- handshake
    
- DNS lookup
    
- số lần round-trip
    
- kích thước payload
    
- keep-alive
    
- cache
    

Một request chậm không phải lúc nào cũng do server xử lý chậm.

Có thể chậm vì:

- DNS
    
- TLS
    
- mạng trung gian
    
- chờ TCP
    
- tải payload lớn
    
- backend gọi tiếp dịch vụ khác
    

Hiểu mạng giúp bạn không đổ lỗi sai chỗ.

---

# 24. Backend engineer cần nhớ gì từ chương này?

## 1. Mạng không đáng tin tuyệt đối

Phải luôn giả định packet có thể mất, chậm hoặc sai thứ tự.

## 2. Header rất quan trọng

Nó chứa metadata để gói dữ liệu đi đúng và được xử lý đúng.

## 3. DNS là tầng nền của truy cập dịch vụ

Nó không chỉ “đổi tên thành IP”.

## 4. HTTP là giao thức ứng dụng cốt lõi

Phải hiểu method, status code, header, body.

## 5. HTTPS là bắt buộc trong hệ thống thật

Bảo mật không phải tùy chọn.

## 6. Phân biệt rõ lỗi mạng và lỗi ứng dụng

Debug tốt là phải xác định lỗi ở tầng nào.

---

# 25. Kết luận của chương

Nếu các chương trước nói về cách máy lưu và tính trong một máy, thì chương này nói về cách nhiều máy nói chuyện với nhau.

Mạng máy tính là nền của toàn bộ Internet và hệ thống phân tán hiện đại.

Từ packet, header, checksum, TCP/IP, DNS đến HTTP và HTTPS, ta thấy một tư duy xuyên suốt:

> biến sự hỗn loạn của truyền thông vật lý thành một hệ thống giao tiếp có thể tin cậy, chuẩn hóa và mở rộng.

Đó là lý do mọi backend engineer đều phải hiểu mạng ở mức nền tảng.

Chương tiếp theo sẽ đi lên một tầng trừu tượng hơn, nơi con người bắt đầu nói chuyện với máy bằng chính từ khóa và cú pháp của mình:

> **ngôn ngữ lập trình, compiler, interpreter, AST, runtime và vì sao code có thể chạy được**.

Nếu cần, tôi sẽ viết tiếp **Chương 19 — Programming languages, compiler, interpreter, AST, runtime** theo cùng mạch này.