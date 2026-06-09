- Các vấn đề ?
	- Tránh lãng phí tài nguyên: Mỗi lần một service sử dụng một đối tượng sẽ khởi tạo đối tượng đó. Nếu nhiều service cùng dùng 1 đối tượng, sẽ khởi tạo nhiều lần đối tượng đó. Như vậy sẽ tạo ra nhiều bản sao, mỗi bản sao lại clone lại đối tượng gốc. Mặc dù các đối tượng khởi tạo đầu tiên và các đối tượng khởi tạo sau này là như nhau, khiến lãng phí tài nguyên để lưu trữ các đối tượng và lặp lại các xử lý như khởi tạo gây ảnh hưởng hiệu suất
	+ Thiếu nhất quán: Khi nhiều service cùng dùng chung 1 đối tượng. Ở 1 service nào đó khi sử dụng đối tượng, chúng có thể thực hiện ghi đè giá trị lên các propety của đối tượng đó. Khiến các service khác không cập nhật thay đổi này làm thiếu nhất quán dữ liệu xử lý ở các service.
	+ Khó quản lý trạng thái: Vì có nhiều bản sao đối tượng được tạo ra và sử dụng, khiến việc quản lý theo dõi trạng thái chung trở nên phức tạp
	
- Giải quyết được gì ?
	+ Tiết kiệm tài nguyên: Các đối tượng sử dụng chung được khởi tạo 1 lần duy nhất, tránh việc tạo nhiều đối tượng giống nhau -> tiết kiệm tài nguyên
	+ Tăng performment: Các đối tượng được khởi tạo 1 lần duy nhất và sử dụng cho mọi nơi. Tránh các sử lý lặp lại: Khởi tạo, đọc, ghi dữ liệu -> tăng hiệu suất
	+ Đảm bảo toàn vẹn và nhất quán: Tất cả các service đều truy cập cùng 1 đối tượng được khởi tạo lần đầu. Khi đối tượng thay đổi thì các service khác đều được cập nhật sự thay đổi
	+ Cung cấp điểm truy cập toàn cục
		
- Các vấn đề của singleton ?
	+ Vấn đề đa luồng (concurrency): Nếu hai hoặc nhiều luồng cùng lúc gọi getInstance() khi instance còn null. Hai or nhiều luồng có thể cùng nhau pass qua kiểm tra instance == null (khởi tạo 1 lần duy nhất lúc ban đầu) và tạo ra nhiều insctance khác nhau, phá vỡ mục đích sử dụng của singleton. Có thể sử dụng synchronized or double-checked locking để khác phục. Nhưng chúng làm code làm code phức tạp hơn và ảnh hưởng đến hiệu năng
		+ Cách xử lý :
			+ earger init: Tạo instance trước khi class được load (tạo ngay khi class đó được nạp vào xử lý) or trước khi luồng request
		Nhược: Các instance có thể tạo ra mà không dùng gây lãng phí, ko bắt lỗi được tại thời điểm khởi tạo
		-> Lazy init: Mỗi lần truy cập instance sẽ lock và unlock lại để kiểm tra
		Nhược: Mỗi lần gọi instance đều phải đồng bộ lock/unlock gây ảnh hưởng tới hiệu năng
		-> Một số phương thức dành riêng cho từng ngôn ngữ lập trình khác nhau
	+ Khả năng mở rộng: Môi khi thay đổi đối tượng xử lý sẽ cần sửa lại instance ở tất cả các nơi sử dụng. Ko như Depence Injection có thể thay đổi nhanh chóng bằng việc thay đổi implement inteface trong contruct()
	+ Đa luồng: Khi sử dụng không chia sẻ kết nối. Mỗi singleton xử lý trên mỗi process. Làm leak or race condition queue, cache
	+ Thiếu nhất quán: Singleton khởi tạo các thông tin gửi mail + method set property (thay đổi thông tin gửi mail) 2 phần khác nhau trong hệ thống. Khi 1 module xử lý gửi mail, thì đồng thời có 1 module chạy song song sử dụng method set property kia khiến thông tin gửi mail bị sai. => Gây lỗi khó hiểu và khó tìm