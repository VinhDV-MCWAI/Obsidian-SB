- **Bản chất Heap (bộ nhớ tự do):** Là một vùng nhớ lớn, không có cấu trúc định sẵn. Dữ liệu có thể nằm rải rác ở bất kỳ đâu.
    
- **Đặc điểm:** Tốc độ truy cập chậm hơn Stack vì CPU phải mất công tìm kiếm địa chỉ thông qua tham chiếu. Tuy nhiên, dung lượng của nó rất lớn (phụ thuộc vào tổng RAM của máy). Việc dọn dẹp trên Heap do lập trình viên (C/C++) hoặc do bộ dọn rác tự động (**Garbage Collector - GC** trong Java, C#, JavaScript, Go...) đảm nhận.
    
- **Dùng làm gì:** Lưu trữ tất cả các **Object (đối tượng)** phức tạp được khởi tạo qua từ khóa `new` hoặc các cấu trúc dữ liệu động (Array, Object, Class Instance).