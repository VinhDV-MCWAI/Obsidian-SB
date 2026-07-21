**

- Có. Mặc dù internet có các cơ chế lọc bảo vệ. Nhưng các request attack lập ra tuân thủ các quy tắc này và nó pass qua các cơ chế này. hệ thống tự bảo vệ, về cơ chế thực hiện có thể cấu hình webserver nginx về đặt giới hạn dung lượng các thành phần trong request hoặc sử dụng waf của các cloud. Ví dụ 1 request hợp lệ, chúng tạo ra 1 đoạn token dung lượng 20MB, phía ứng dụng sẽ cần cấp phát bộ nhớ để chỉ lưu thứ này và cần thêm các phần khác như cpu, disk để lưu trữ, xử lý. Nếu có 5 request như vậy đồng thời thì sẽ mất 100MB chỉ để lưu trữ token trong thời gian tạm thời, nó đủ làm nghẽn hệ thống có giới hạn ram tương tự, gây ra hàng loạt lỗi khác.
    

**