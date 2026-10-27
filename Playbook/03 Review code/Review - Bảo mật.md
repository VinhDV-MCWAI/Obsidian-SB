---
chu_de: review-bao-mat
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Bảo mật

Mặt ưu tiên số 1 trong [[Review code - quy trình]]. Mở khi diff chạm: API, form, quyền, đăng nhập, upload, truy vấn, đầu ra HTML, cấu hình, log, thư viện. Chuẩn đầy đủ: [[Bảo mật cơ bản]]; ở đây chỉ là câu hỏi khi đọc diff.

## Lựa chọn của tôi

- Kiểm theo **đường đi của dữ liệu không tin cậy**: vào từ đâu → qua validate nào → tới chỗ nguy hiểm nào (DB, HTML, lệnh hệ thống, file, URL gọi ra ngoài).
- Kiểm quyền ở **server và theo từng bản ghi**; giao diện ẩn nút không tính.
- Phát hiện bảo mật luôn kèm kịch bản tấn công cụ thể; không báo "có thể không an toàn" chung chung.

## Checklist

- [ ] Mỗi API / hành động mới hoặc bị sửa có kiểm xác thực và quyền ở server.
- [ ] Quyền kiểm trên **bản ghi cụ thể** (đổi id trên URL không truy cập được bản ghi người khác).
- [ ] Đầu vào mới có validate ở biên ([[Validate - quy tắc chung]]); chỉ nhận trường cho phép.
- [ ] Truy vấn dùng tham số, không ghép chuỗi.
- [ ] Đầu ra HTML được escape; HTML người dùng nhập được lọc theo danh sách cho phép.
- [ ] Form dùng cookie có chống giả mạo request.
- [ ] Không có bí mật, token, mật khẩu trong code, test, log, thông báo lỗi.
- [ ] Log / phản hồi không lộ dữ liệu cá nhân hoặc chi tiết nội bộ (stack trace, câu truy vấn).
- [ ] File tải lên: kiểm loại theo nội dung, giới hạn dung lượng, đổi tên khi lưu ([[Upload file và kho file]]).
- [ ] Thư viện mới / đổi phiên bản: có lý do, không có lỗ hổng đã công bố.
- [ ] Cấu hình (CORS, header, debug) không bị nới ra ngoài phạm vi việc.

Bẫy: xem Bẫy của [[Bảo mật cơ bản]]; thêm: bắt lỗi rồi trả nguyên thông báo kỹ thuật cho người dùng.
