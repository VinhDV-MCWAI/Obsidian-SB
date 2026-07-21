**- Trước sự cố:
    

- Thực hiện hàng ngày vào thời điểm ít người dùng hoạt động nhất.
    
- Replication (Bản sao thời gian thực): Chạy mô hình Master-Slave. Mọi dữ liệu ghi vào Master sẽ lập tức tự động đồng bộ sang Slave. Nếu Master sập, ta lập tức chuyển Slave lên làm Master (Thời gian gián đoạn gần như bằng 0)
    
- Kịch bản Quy trình Xử lý khi xảy ra mất dữ liệu:Nếu Interviewer hỏi: "Sáng ra đi làm, khách hàng kêu mất dữ liệu đơn hàng đêm qua, bạn xử lý thế nào?", hãy trả lời theo quy trình 3 bước:
    

- Bước 1: Cô lập và Xác định phạm vi (Dựa vào Log): Em sẽ ngay lập tức truy cập vào hệ thống Log (Sentry/ELK) để tìm các Exception xảy ra vào đêm qua. Xác định xem lỗi do code bug làm gãy Transaction, do sập phần cứng, hay do có lệnh Delete nhầm từ con người.
    
- Bước 2: Sửa lỗi (Fix Bug/Cấu hình): Nếu do Bug làm dữ liệu ghi vào DB bị thiếu, em sẽ tiến hành vá code ngay lập tức (đảm bảo bọc bằng Transaction) để chặn không cho các request mới tiếp tục làm lỗi dữ liệu.
    
- Bước 3: Khắc phục và Bù đắp dữ liệu (Dựa vào Backup & Log):
    

- Nếu mất trên diện rộng: Em sẽ dùng bản Backup gần nhất để khôi phục lại.
    
- Nếu chỉ mất một vài bản ghi: Em sẽ dựa vào thông tin Payload (dữ liệu thô) được lưu trong Log hoặc dữ liệu từ các bên thứ 3 (như Webhook của cổng thanh toán) để viết script chạy bù (Replay/Migration) nạp lại các đơn hàng bị thiếu vào DB cho khách.
    

- Trong sự cố: Sử dụng transaction bọc các xử lý thay đổi dữ liệu. Chỉ khi mọi thứ hoàn thành sẽ có commit thì dữ liệu mới thay đổi.
    
- Sau sự cố: kiểm tra log, monitor hệ thống tại thời điểm xảy ra lỗi để tiến hành điều tra nguyên nhân, phân tích và đưa ra giải pháp khắc phục. Log chuẩn phải bao gồm: Timestamp (thời gian chính xác), Context (User nào làm, Payload request gửi lên là gì, ID của bản ghi bị tác động), và Stack Trace (lỗi xảy ra ở dòng code nào).**