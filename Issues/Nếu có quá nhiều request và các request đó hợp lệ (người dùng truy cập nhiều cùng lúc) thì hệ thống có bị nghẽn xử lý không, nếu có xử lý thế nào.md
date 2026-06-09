**

- Thông thường sẽ xử lý đồng bộ : nhận request và đưa vào xử lý a-z và trả kết quả về. Nếu trường hợp có số lượng lớn request đồng thời thì hệ thống sẽ xử lý tất cả các request với cùng tiến trình như vậy cùng lúc. Việc đó làm quá tải xử lý khi tài nguyên xử lý có hạn
    
- Phương án xử lý là chuyển dự án qua xử lý bất đồng bộ. Nó thực hiện kiểu như sau: khi có request hợp lệ (pass qua waf, proxy, đơn giản là một lúc có nhiều người dùng). Push hết request đó vào queue một hệ thống trung gian như redis, rabbitMQ -> phản hồi ngay về mã 202, rằng tôi đã nhận request rồi hãy đợi xử lý xong -> phía sau các worker nhận các task đưa vào xử lý, số lượng xử lý được tính toán và điều chỉnh theo khả năng của hệ thống tại thời điểm đó
    
- Nhờ đó quá trình không bị dừng toàn bộ đột ngột, chúng xử lý linh hoạt theo khả năng hệ thống, tránh làm nghẽn hệ thống và làm nó dừng đột ngột
    

**