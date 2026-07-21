## Tối ưu hóa Chiến lược Unit Test trong Tháp Kiểm Thử

Trong mô hình kim tự tháp kiểm thử (Testing Pyramid), **Unit Test** chiếm tỷ trọng lớn nhất, đòi hỏi tốc độ thực thi cực nhanh, tính cô lập tuyệt đối và không phụ thuộc vào hạ tầng.

### 1. Hạn chế của phương pháp truyền thống (Integration Testing giả lập)

Trước đây, việc kiểm thử thường bị nhầm lẫn khi cố gắng thiết lập một môi trường vật lý riêng biệt (Database, API, WebSockets...). Quy trình này bộc lộ 3 nhược điểm lớn:

* **Xung đột dữ liệu:** Khi chạy test song song, các test case dùng chung môi trường sẽ ghi đè và làm sai lệch dữ liệu của nhau, buộc hệ thống phải chạy tuần tự (sequential).
* **Hiệu năng kém & Tốn chi phí:** Việc lặp đi lặp lại chu kỳ *Khởi tạo môi trường $\rightarrow$ Thực thi $\rightarrow$ Xóa dữ liệu (Cleanup)* làm tăng đáng kể thời gian chạy test và tiêu tốn tài nguyên hạ tầng.
* **Mất tính cô lập:** Việc liên kết với các hệ thống ngoại vi khiến bài test thất bại khi hạ tầng lỗi, dù logic nội tại của hàm hoàn toàn chính xác. Điều này đi ngược lại bản chất của Unit Test.

### 2. Giải pháp chuẩn mực: Cô lập hóa và Chạy song song trên RAM

Để đạt đúng bản chất của Unit Test, phạm vi kiểm thử phải được giới hạn nghiêm ngặt trong chính logic của hàm/hợp phần đó thông qua kỹ thuật **Mocking**:

* **Giả lập hạ tầng (In-Memory & Mocking):** * Thay thế Database vật lý bằng **Mock DB** hoặc **In-Memory Database**, chuyển đổi các Entity thành Object lưu trữ trực tiếp trên RAM.
* Giả lập (Mock) các phản hồi (Response) từ API/Dịch vụ bên thứ ba để kiểm thử mọi kịch bản ngoại lệ của UI hoặc Business Logic.


* **Thực thi song song (Parallel Execution):** Cô lập mỗi test case trong một luồng xử lý riêng biệt (Thread Process). Toàn bộ vòng đời từ khởi tạo, thực thi đến giải phóng tài nguyên đều diễn ra khép kín trong Thread đó.

### Kết quả đạt được

$$\text{Tốc độ tối đa} \quad \Vert \quad \text{Tiết kiệm tài nguyên} \quad \Vert \quad \text{Cô lập tuyệt đối}$$

Phương pháp này giúp hệ thống test có thể **chạy song song hàng nghìn test case cùng lúc**, kiểm soát hoàn toàn dữ liệu đầu vào/đầu ra và phản ánh chính xác chất lượng của mã nguồn.


### 3. Mock data
- Mock thực chất là kẻ mạo danh, chúng có thể là class, method, tham số,... nó bắt trước giống như đích đến mà unit test case (một logic service cần test) request đến nó để lấy kết quả. Mock này quy định kết quả trả về để kiểm tra logic xử lý của unit đó hoạt động ra sao trong các kịch bản kết quả khác nhau. 
VD: Yêu cầu test chức năng tạo 1 category thành công: unit test case call service create category với param name là X -> mock đóng giả repository category và thay đổi inject categoryService, trả về kết quả thành công -> hàm kiểm tra kết quả tạo thành công record có name là X phải không ? -> Đúng -> unit test case thành công

### 4. Lý do singleton khó làm unit test
- Khởi nguyên: Để tạo singleton, class đó bắt buộc tụ khởi tạo (private contructor) để không không ai có thể new object(...) từ bên ngoài. Làm vậy để chặt chẽ, có thể thằng dev nào đó ở nơi khác không biết sẽ khởi tạo ghi đè lại new object này, để sở hữu một variable static giữ ô nhớ duy nhất (logic của singleton). Do đó, contructor bị block không truyền dữ liệu mock vào được