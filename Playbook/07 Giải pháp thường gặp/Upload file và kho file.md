---
chu_de: upload-file
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Upload file và kho file

Nền: [[minio]]. Validate từng file: [[Validate - File và ảnh]].

## Lựa chọn của tôi

- File đi **thẳng từ trình duyệt vào kho file** (kho lưu trữ đối tượng) bằng **link ký có hạn** do server cấp; server không chuyển tiếp dữ liệu file.
- Luồng: **xin link → gửi file → báo xong → server kiểm → xử lý nền** (ảnh nhỏ, đọc thông tin).
- File lớn **chia phần**; nhỏ gửi một lần. Ví dụ ngưỡng: ≤ 100 MB một lần; 100–500 MB phần 16 MB; > 500 MB phần 32 MB.
- Kho file không cho đọc công khai; tải về / xem trước bằng link ký ngắn hạn (vài phút; video dài thì dài hơn).
- Bản ghi trong DB ở trạng thái "đang tải lên" cho tới khi báo xong và kiểm đạt.

## Checklist gửi file

- [ ] Lỗi mạng / kho trả 5xx khi gửi một phần → thử lại tối đa 3 lần, chờ tăng dần (1 s, 2 s) + **ngẫu nhiên nhỏ** để các phần không thử cùng lúc.
- [ ] Link hết hạn (mạng chậm) → xin link mới cho phần đó rồi thử lại.
- [ ] Lỗi khác / hết lượt thử → dừng mọi phần, **giữ phần đã gửi** để tiếp tục sau.
- [ ] Tiếp tục: chỉ gửi phần còn thiếu. Kiểm đúng **cùng một file** (tên + dung lượng + thời điểm sửa file) — trộn phần của hai file ra file hỏng.
- [ ] Huỷ → gọi server huỷ, kho xoá các phần đã gửi.
- [ ] Bỏ dở (đóng trình duyệt) → việc dọn định kỳ xoá upload treo quá X giờ (ví dụ 24 giờ, tính lại mỗi lần tiếp tục).
- [ ] Chỉ người bắt đầu upload mới xin link / tiếp tục / huỷ được (người khác → 404).

## Checklist khi báo xong (server kiểm)

- [ ] File đã thật sự có trong kho; dung lượng thật = dung lượng đã khai (lệch → xoá file, 422).
- [ ] Đủ mọi phần; ghép được.
- [ ] Loại file theo nội dung; chặn file chạy được kể cả khi đổi tên.
- [ ] Tên trùng trong cùng thư mục (không phân biệt hoa thường, chỉ tính bản ghi chưa xoá) → 422.

## Xử lý nền sau upload

- [ ] Đọc kích thước ảnh, thời lượng video / nhạc, tạo ảnh nhỏ — trong việc nền, không trong request.
- [ ] Công cụ xử lý media: **giới hạn thời gian** (ví dụ 60 s), chỉ cho đọc định dạng thông thường (file giả dạng danh sách phát có thể trỏ tới file khác trên máy).
- [ ] Ảnh quá lớn (ví dụ > 50 triệu điểm ảnh) → bỏ qua tạo ảnh nhỏ để không tràn bộ nhớ.
- [ ] Không đọc được → upload vẫn thành công, chỉ thiếu ảnh nhỏ; ghi log lý do.
- [ ] Lệnh bù cho file cũ chạy lặp lại an toàn (bỏ qua file đã xử lý, kể cả đã thử mà không đọc được).

## Xem trước

- [ ] Danh sách loại được xem trước (ảnh, PDF, video, nhạc, chữ thuần ≤ vài MB); còn lại chỉ tải về.
- [ ] File chữ hiển thị dạng **chữ thuần**, không hiểu HTML. HTML / SVG / XML không xem trước (XSS).

## Bẫy

- Xoá file trong kho trước khi DB lưu xong → DB lỗi thì mất file. Xoá **sau** khi DB lưu.
- Giới hạn dung lượng chỉ đặt ở một tầng → lỗi 413 khó hiểu từ proxy.
- Ghép phần thất bại (mã kiểm phần sai) mà vẫn thử lại → lặp vô ích. Lỗi kiểu này **không thử lại**, báo người dùng ngay.
- Thêm công cụ xử lý media làm image nặng gần gấp đôi → cân nhắc tách worker riêng.
