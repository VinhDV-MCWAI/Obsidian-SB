 - **Bản chất Stack (bộ nhớ ngăn xếp):** Hoạt động theo cơ chế **LIFO (Last In, First Out)** - Vào sau ra trước. Nó giống như một chồng đĩa, chiếc đĩa nào đặt vào sau cùng sẽ được lấy ra đầu tiên.
    
- **Đặc điểm:** Tốc độ cực nhanh, dung lượng nhỏ (bị giới hạn cứng bởi hệ điều hành). Do kích thước được xác định rõ ràng ngay khi biên dịch, việc cấp phát và giải phóng vùng nhớ diễn ra tự động thông qua con trỏ CPU.
    
- **Dùng làm gì:** * Lưu trữ các **biến nguyên thủy** (primitive types như `int`, `float`, `boolean`).
    
    - Lưu trữ **địa chỉ tham chiếu (reference)** trỏ tới vùng nhớ trên Heap.
        
    - Lưu trữ **Stack Frame**: Mỗi khi một hàm (method) được gọi, một khung bộ nhớ (frame) sẽ được đẩy vào Stack để chứa các tham số truyền vào và biến cục bộ của hàm đó. Khi hàm chạy xong, frame này lập tức bị hủy.