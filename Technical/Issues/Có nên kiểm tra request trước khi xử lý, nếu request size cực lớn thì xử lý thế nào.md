**

- Thay vì để request chạm đến phần app xử lý của hệ thống, thì nên chặn hoặc điều tiết ngay tại tầng web server or firewall. Cách thức thường được thực hiện là:
    

- Rate limit (nginx): thiết lập giới hạn số lượng request từ một ip trong 1 thời gian. Nếu vượt quá trả lỗi 429. Cơ chế dùng thuật toán leaky bucket or token bucket
    
- WAF của các dịch vụ cloud: như cloudflare or aws waf để lọc traffic ảo, botnet or payload quá lớn.
    
- Giới hạn dung lượng request trong cấu hình nginx để ngắt kết nối ngay khi request quá nặng.
    

- Khi request tới phần ứng dụng, thì có kiểm tra không ? kiểm tra như thế nào ? vd validate jwt trước khi verify (vì có fill 1 token dài vô tận gọi api => làm tắc nghẽn xử lý)

- Để kiểm tra tính hợp lệ của 1 request, ở tầng ứng dụng tạo thêm các lớp trung gian xử lý như middleware, chúng thực hiện các loại xác thực request như authen, author nếu không hợp lệ sẽ return để giải phóng tài nguyên, xử lý nhanh, tiết kiệm tài nguyên xử lý không cần thiết.
    
- Tiếp theo, tầng controller có lớp validate thực hiện verify payload để việc gửi dữ liệu và xử lý dữ liệu sau đó được an toàn. Đảm bảo request được kiểm tra kỹ lưỡng và đảm bảo an toàn trước khi thực hiện các logic phía sau.
    

**