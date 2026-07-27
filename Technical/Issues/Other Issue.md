- Cơ chế brute force bảo vệ: sử dụng limit access trong DB để chống brute force không hiệu quả. Nó tạo gánh nặng select, update DB liên tục, ko
hiệu quả với tấn công phân tán (Distributed Brute Force) từ nhiều IP khác nhau nhắm vào 1 account
-> Triển khai rate limiting đa tầng bằng redis: Level 1 (Targeted Defense): Giới hạn theo User ID + IP. Level 2 (Distributed Defense): Giới hạn theo User ID (Username) duy nhất
- Dư thừa token: gửi access và refresh token trong header mỗi requetst là dư thừa, rủi do bảo mật. Nên gửi chuyên biệt
- Thiếu cơ chế thu hồi đồng loạt của user trên nhiều thiết bị, thu hồi token trên 1 thiết bị
---------------------------------

  

Cloud & DevOps: Các công ty ưu tiên ứng viên biết AWS, Docker và tự động hóa CI/CD (thứ bạn đã làm trong dự án cá nhân).

  

AI Integration: Không chỉ là dùng AI để code, mà là biết tích hợp các mô hình AI/ML vào hệ thống (như cách bạn muốn đưa docs vào notebook).

  

Tôi sử dụng Python/FastAPI cho dự án này vì tính hiệu suất của nó. Nhờ tư duy hệ thống và sự hỗ trợ của AI, tôi đã triển khai nhanh chóng cấu trúc dữ liệu và logic nghiệp vụ. Tuy nhiên, thế mạnh cốt lõi của tôi vẫn là thiết kế hệ thống và tối ưu hóa backend, thứ mà tôi có thể áp dụng linh hoạt trên bất kỳ ngôn ngữ nào
  

Build proof: Hoàn thiện Second Memory, deploy demo, thêm README tốt. Contribute nhỏ open-source hoặc blog về một project.

  

hiểu giao dịch DB (transaction, concurrency, idempotent) và tối ưu hoá cơ sở dữ liệu (chỉ mục, truy vấn, nhất quán dữ liệu)

- Hỏi HR range salary để nhận định mức offer cty có thể chi phí, dựa vào để cân nhắc điều chỉnh hoặc bỏ qua, nhớ note lại thông tin trong hội thoại cần lưu ý và check chuẩn bị cho các plan tiếp theo

- Tìm cách check 1 số cv khác để tham khảo

- Check JD yêu cầu và bucket để biết rõ hơn về yêu cầu và mức offer

- Yêu cầu AI đóng làm người interview. Thực hiện lên plan test, các vòng phỏng vấn, interview đánh giá, chấm điểm, đưa ra QA, bài test, để đúc kết cải thiện dần.

- Train với kho phỏng vấn thực tế: trên github interview-laravel-questions, php-interview-vietnam, hoặc awesome-laravel-interview. Đọc các bài chia sẻ kinh nghiệm php/laravel trên internet page vible.asia, dev group,... interview-matrix

- Tìm và thực hiện thử các bài test: laravel-assessment, laravel-interview-test

  
  

- Thử nghiệm đánh giá CV: chọn một vài chỗ thì điểm gửi cv và check nhận phản hồi. Nếu có phản hồi, phản hồi nhanh, xử lý nhanh, tỉ lệ phản hồi cao thì ổn.

- Thử nghiệm đánh giá bucket: chọn một số chỗ thí điểm để thông báo mức offer mong muốn. Collect thông tin để phỏng đoán có ổn không dựa vào phản hồi, có dễ apply mức đó hay không ? có bị deal xuống hay không ? có tỉ lệ phản hồi và chấp thuận cao hay không ?

- Thử nghiệp interview thực tế: collect thông tin trong buổi đó, về thị trường đang yêu cầu ? mình bị miss ở đâu ? note câu hỏi, note câu bí, note chỗ bị đào sâu. có thể cải thiện nhanh bằng cách học QA hay cần demo thực tế, nếu demo thực tế yêu cầu hơi nhiều có thể điều chỉnh lại. Cải thiện ngay để fix cho lần tiếp theo với chỗ tiếp theo

- Nếu có điều kiện interview nhiều nhất có thể để: tăng phản xạ, cách diễn đạt, hiểu market, trai lì tự tin hơn, deal thỏa thuận ổn hơn.


*****

- CV trông khá đáng tin, không thổi phồng phóng đại quá mức gây khó khi phỏng vấn cũng không quá mờ nhạt tầm thường bị bỏ qua. Phản ánh chân thực, thực tế.

- Sửa lại cv một chút để phần dự án well để tránh hiểu lầm hỏi phần dữ liệu đồng bộ các sàn bên thứ 3

- Bỏ system design và db optimize trong mô tả tại hơi nguy hiểm tại nó có phạm vi cùng rộng, câu hỏi khó lường. có thể đổi lại thành backend architecture fundamentals hoặc Database & Backend Architecture

- Đào sâu hơn

  *Backend deep dive*

    - các thành phần của laravel, lifecycle, kiến trúc,

  *System Design*

  - transaction optimization

  - indexing strategy

  - deadlock handling

  - consistency

  - cache invalidation

  - eventual consistency

  - distributed lock

  - horizontal scaling

  - observability

  - tracing

  - bottleneck analysis

  - scaling

  - DB internals

  - auth model

  - testing philosophy

  - concurrency

  - Docker/network/runtime

  - Linux

  - CQRS có không?

  - Audit log design?

  - testing strategy

  - coverage mindset

  - what to test

  - mock/fake/stub

  - integration boundary

  - flaky test handling

  - RBAC hay ABAC?

  - permission inheritance?

  - deny precedence?

  - multi-device logout?

  - session invalidation?

  - domain-driven design

  - Modular monolith

  - microservice

  - redis: persistence, eviction policy, memory issue, race condition, distributed lock, pub/sub, queue retry, delayed jobs

  - Xử lý race condition: locking, optimistic/pessimistic, revervation, timeout, retry, deadlock posibility

  
  

  *Product*

  - Notification system for 1 million users ? làm gì ?

  - Production CPU tăng 100%. Anh làm gì đầu tiên?

  - DB lock kéo dài. Cách identify?

  - Một queue job bị retry vô hạn. Handle sao?

  *Behavioral*

  - Đã từng gây sự cố chưa ? nó là gì ?

    * dùng AI set full quyền kêu nó test api, trong khi api dưới local đang connect env stg chưa sửa lại. Kết quả dữ liệu stg thay đổi

    * Sử dụng tính năng đồng bộ dự liệu của gg kém, tức xóa đi để đồng bộ lại -> dữ liệu dự án bị đồng bộ theo, may khôi phục lại được, đợt đó team 30 mem

    * Điều tra tính năng theo dõi hàng đợi khi đặt hàng thì gửi mail về số lượng người dùng thay đổi bất thường. Deploy nó nên prod để ghi nhận thông tin gửi về mail để sau dễ điều tra. Nhưng kết quả rạng sáng chủ nhật có lượng lớn người dùng đặt hàng, kết quả có vô số mail được gửi, gây ảnh hưởng đến túi tiền của khách vì server smtp mail khách trả tiền thuê ngoài. Sáng lơ mơ gọi điện thoại chạy nên fix gấp

    * Sử dụng jmeter test performance giả lập request user spam làm sập mấy môi trường test. khách sử dụng aws tuần đó chi chí độn lên hơn 2k đô

  *Nghiên cứu QA thêm*

  - Xử lý request refresh token ở front-end và backend như thế nào ? bầu leader, đặt time bắt đầu xử lý ngẫu nhiên, unique token, redis lock,

  - queue gửi mail retry liên tục tạo ra nhiều email, làm sao tránh được duplicate effect ? unique mail or event or payment id, retry-safe, idempotency

  - nghiên cứu thêm transaction outbox, after commit dispatch, queue after DB commit

  - Trường hợp lưu cache phân quyền và white list trong redis, nếu nó bị down trong một thời gian thì sao ? xử lý sao ? duplicate white list qua db nhưng có cost, tính đồng bộ, độ trễ, db overload cả khi không sử dụng,...

  - Nếu system có lượng lớn hàng triệu user, người dùng sử dụng rất nhiều thiết bị, permission update liên tục thì cache invalidation sớm xảy ra, hỏi scale model hiện tại như thế nào ?

  -

  
  

- đọc https://github.com/donnemartin/system-design-primer

  

- self

  + How much experience you have

  + What your technical background is

  + What positions you are interviewing for

  + Which companies you are interviewing with

  + Luck

  

- company engineering large in the world

  

- Định hình, xác định bối cảnh hệ thống

  + Ai sử dụng ? sử dụng như thế nào ? (hệ thống, team, địa lý,..)

  + Giới hạn hiện tại của hệ thống: phần cứng (db, cache, ram, cpu, tốc độ mạng,...). lượng tài nguyên sử dụng thông thường, lúc cao điểm, cùng với lượng tài nguyên đó hệ thống hiện tại đang xử lý các thành phần riêng biệt là bao nhiêu như số lượng request xử lý đồng thời, tỉ lệ đọc|ghi dữ liệu

  + Môi trường và công nghệ hệ thống đang sử dụng

  + Yêu cầu mong đợi input và output của việc này đối với hệ thống

  

- Định hình, xác định các giải pháp (công nghệ, thiết kế, thuật toán,...)

  + Vấn đề hoặc nhu cầu xã hội/business

  + Nó là gì ? sinh ra khi nào ? dùng cho việc gì ? dùng như thế nào ? ưu nhược điểm (thời gian triển khai, độ phức tạp, rủi do) ? khi nào sử dụng hoặc không ? đánh đổi gì ? giới hạn thế nào ? các biến thể nếu có ? tỉ lệ đánh đổi ? giải pháp thay thế

  + Vị trí và cách chúng vận hành trong hệ thống

  + Cost: Yêu cầu phần cứng, lượng tài nguyên nó tiêu thụ, nhân lực quản lý

  + Giới hạn của nó khi giải quyết vấn đề, lượng người dùng, lượng dữ liệu, tỉ lệ đọc|ghi or down|upload, request xử lý đồng thời trong khoảng bao nhiêu là tốt nhất.

  + Evolution: Vấn đề khi scale, Failure & Bottleneck (điểm chết).

  
  

- Vấn đề đối với db khi scale lớn, các yêu tố cần chú ý là:

  + Query đọc bao nhiêu rows or số row phải đọc là bao nhiêu

  + Có hit index không

  + Selectivity

  + Data size mỗi row

  + RAM

  + SSD hay HDD

  + Read/write ratio

  + Concurrent requests

  + Query pattern

  + Cardinality

  + Hot/cold data

  + latency

  

- Cách kiểm tra: Xác định query, chạy nó, theo dõi tiến trình, phân tích chiến lược thực thi. Đánh giá các chỉ số. Tối ưu chiến lược thực thi lại.