
- Có rất nhiều db khác nhau đang tồn tại, các kỹ sư nhận thấy: dữ liệu là cốt lõi, nhưng vật chứa (db) lại thường xuyên thay đổi do nhu cầu mở rộng, chi phí, tiến bộ công nghệ,... họ cần một cách tiếp cận để có thể chuyển đổi dữ liệu một cách dễ dàng. Nhằm giải phóng dữ liệu khỏi sự lệ thuộc bởi hạ tầng, giúp linh hoạt và bền vững trước sự thay đổi công nghệ. Luồng suy nghĩ của họ diễn ra như sau:
	- Trừu tượng hóa: Thay vì viết code riêng cho từng DB, họ tạo ra một lớp ngôn ngữ chung (thường là DSL - Domain Specific Language) để định nghĩa cấu trúc dữ liệu. Ngôn ngữ này dễ đọc, dễ viết và độc lập với từng loại DB.
	- Định nghĩa trạng thái (State Definition): Thay vì nhìn DB như một thực thể tĩnh, họ coi nó là một chuỗi các phiên bản (Version 1 -> Version 2).
	- Chia để trị (Separation of Concerns): Họ tách biệt cấu trúc (schema, khung xương), dữ liệu (thịt da) và cơ chế vận hành (engine, bộ máy xử lý) để xử lý riêng biệt.


- Chi tiết các thành phần migrate:
	- [[Migrate schema]]: Đây là việc di chuyển "bản vẽ thiết kế" của database
	- [[Migrate Data]]
	- [[Migrate engine]]

- Tuy nhiên, đối với mỗi công nghệ được phát triển riêng bởi các nền tảng tri thức khác nhau, nên triết lý vận hành khác nhau do đó có nhiều thành phần không tương đồng. Vậy quy trình xử lý riêng thường theo quy trình 

	- Phân tích mức độ tương thích: Xác định xem database nguồn và đích có bao nhiêu phần trăm giống nhau (cú pháp, kiểu dữ liệu, tính năng hỗ trợ). Có các tool như AWS Schema Conversion Tool (SCT),Ora2Pg để report độ tương thích 
    
	- Viết script chuyển đổi (Migration Script): Dựa trên báo cáo từ SCT, viết các script để chuyển đổi các đối tượng không tương thích. 
    
	- Kiểm thử (Testing): Thực hiện kiểm thử kỹ lưỡng trên môi trường staging để đảm bảo dữ liệu được chuyển đổi chính xác và ứng dụng hoạt động bình thường. 
    
	- Migration dữ liệu (Data Migration): Thực hiện chuyển đổi dữ liệu, có thể thực hiện theo các phương pháp khác nhau như: 
		- ETL (Extract, Transform, Load): Sử dụng các công cụ ETL để trích xuất dữ liệu, chuyển đổi và nạp vào database đích. 
		- Migration thủ công (Manual Migration): Viết script để chuyển đổi dữ liệu. 
		- Dịch vụ Cloud (Cloud Migration Services): Sử dụng các dịch vụ migration được cung cấp bởi các nhà cung cấp cloud như AWS Database Migration Service (DMS), Azure Database Migration Service, Google Cloud Database Migration Service. 

- Kiểm tra dữ liệu (Data Validation): Kiểm tra dữ liệu sau khi migrate để đảm bảo tính toàn vẹn và chính xác. 

- Cắt switch (Cutover): Cập nhật ứng dụng để sử dụng database đích. 

- Giám sát (Monitoring): Giám sát hiệu suất của database mới và khắc phục các sự cố phát sinh.