---
chu_de: validate-email
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Email

| # | Kiểm tra | FE | BE | Ghi chú |
|---|---|---|---|---|
| 1 | Chuẩn hoá: bỏ khoảng trắng đầu/cuối; chữ thường (ít nhất phần tên miền; thường là cả chuỗi) | ✔ | ✔ | Trước mọi kiểm tra; lưu dạng đã chuẩn hoá |
| 2 | Bắt buộc | ✔ | ✔ | |
| 3 | Cú pháp: dùng hàm có sẵn của framework (RFC), không tự viết regex dài | ✔ | ✔ | Có dấu cách bên trong, hai `@`, thiếu tên miền → lỗi |
| 4 | Độ dài tối đa 254 (phần trước `@` ≤ 64), hoặc giới hạn nghiệp vụ — **một hằng số** | ✔ | ✔ | Cột DB ≥ giới hạn |
| 5 | Ký tự lạ: full-width, emoji, Unicode giả dạng (`а` Cyrillic) | ✔ | ✔ | Có chấp nhận email quốc tế (IDN) không? Quyết định rõ |
| 6 | Trùng trong DB: **không phân biệt hoa thường**; có tính bản ghi đã xoá mềm không | — | ✔ | **Bắt buộc có unique index** (trên `lower(email)`), không chỉ kiểm ở code |
| 7 | Sửa: bỏ qua chính bản ghi đang sửa khi kiểm trùng | — | ✔ | |
| 8 | Tên miền có máy chủ thư (MX / DNS) | — | ✔ | Có công tắc bật/tắt theo môi trường; thông báo riêng: "Tên miền email không nhận thư — kiểm tra lỗi gõ" |
| 9 | Chặn email dùng một lần (mailinator…) | — | tuỳ dự án | Danh sách cập nhật được |
| 10 | Xác minh sở hữu (gửi link / mã) | — | tuỳ dự án | Đăng ký công khai thì gần như bắt buộc |
| 11 | Đổi email: xác minh email mới trước khi thay; thông báo về email cũ | — | tuỳ dự án | |
| 12 | Thông báo: mỗi loại lỗi một mã (cú pháp / quá dài / đã dùng / tên miền không nhận thư) | ✔ | ✔ | |

## Bẫy

- Kiểm trùng ở code nhưng thiếu unique index → hai request cùng lúc tạo trùng.
- Đăng ký lưu chữ hoa, đăng nhập so chữ thường (hoặc ngược lại) → không đăng nhập được.
- Báo "email đã tồn tại" ở trang đăng ký / quên mật khẩu công khai → lộ ai có tài khoản. Cân nhắc câu chung ("Nếu email hợp lệ, bạn sẽ nhận thư").
- Gộp kiểm cú pháp và kiểm MX vào một luật / một thông báo → người dùng không biết là gõ sai tên miền. Tách thành luật riêng với thông báo riêng.
- Kiểm MX trong test / CI không có mạng → test chập chờn. Tắt bằng config ở test.
- Không giới hạn tần suất gửi thư xác minh → bị dùng để spam.
