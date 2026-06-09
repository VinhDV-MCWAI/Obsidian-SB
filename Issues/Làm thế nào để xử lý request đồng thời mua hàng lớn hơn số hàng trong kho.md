**- Sẽ phân cấp giải pháp dựa trên quy mô traffic của hệ thống
    

- Với **Traffic thông thường**, sẽ sử dụng **Atomic Update trực tiếp ở DB** kèm điều kiện đủ `WHERE stock >= quantity`. Cơ chế khóa hàng của DB sẽ đảm bảo không bao giờ bị bán lố (Overselling).
    
- Giải pháp 2: Sử dụng Redis làm "Hàng rào" giảm tải cho DB (Dành cho Traffic lớn)  sẽ đưa **Redis** lên làm hàng rào phía trước. Tồn kho được lưu trên Redis và trừ bằng lệnh nguyên tử (hoặc Lua Script). Chỉ những request trừ kho thành công trên Redis mới được đẩy xuống DB để tạo đơn hàng, nhằm bảo vệ DB khỏi bị sập do nghẽn I/O
    

- Chuẩn bị: Khi bắt đầu mở bán, bạn lấy số lượng tồn kho từ DB ném vào Redis với một cái Key (Ví dụ: product:1:stock = 100).
    
- Khi có request mua hàng: Code PHP sẽ không đụng vào DB nữa, mà dùng lệnh nguyên tử của Redis là DECRBY (Giảm giá trị đi X) hoặc chạy một đoạn Lua Script trên Redis để kiểm tra và trừ kho.
    
- *Kết quả:** * Redis xử lý bằng RAM nên có thể chịu được hàng trăm ngàn request/giây. * Những người đến muộn khi kho trên Redis đã về `0` sẽ bị từ chối ngay lập tức ở tầng Redis. * Chỉ có những request trừ kho **thành công trên Redis** mới được phép đi tiếp xuống Database để ghi nhận đơn hàng (Lúc này DB chỉ phải xử lý đúng số lượng request bằng với số hàng thực tế, hoàn toàn thảnh thơi). 
    

- Giải pháp 3: Mô hình Giữ chỗ trước - Thanh toán sau (Reservation Pattern)để tối ưu trải nghiệm người dùng, đảm bảo người giữ được hàng có đủ thời gian thực hiện cổng thanh toán mà không làm ảnh hưởng đến lượng tồn kho thực tế. Trong thực tế (như các hệ thống bán vé máy bay, vé xem phim), việc trừ kho ngay khi bấm nút "Mua" đôi khi tạo ra trải nghiệm xấu nếu user thanh toán thất bại. Người ta thường chia làm 2 giai đoạn: **Hold (Giữ hàng)** và **Confirm (Xác nhận mua)**. 
    

- **Bước 1 (Giữ hàng):** Khi user bấm "Mua", hệ thống kiểm tra kho (bằng giải pháp 1 hoặc 2) và chuyển số lượng đó vào một bảng tạm gọi là `inventory_reservations` kèm theo một thời gian hết hạn (ví dụ: giữ ghế trong 10 phút). Lúc này kho thực tế đã bị trừ đi. 2
    
- **Bước 2 (Thanh toán):** * Nếu user thanh toán thành công trong 10 phút -> Chuyển trạng thái reservation thành "Hoàn tất", tạo đơn hàng chính thức. * Nếu quá 10 phút user không thanh toán -> Một tiến trình chạy ngầm (Cronjob/Queue) sẽ tự động xóa dòng reservation đó và **cộng trả lại số lượng** vào kho cho người khác mua.**