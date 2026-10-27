---
chu_de: validate-mat-khau
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Mật khẩu

| # | Kiểm tra | FE | BE | Ghi chú |
|---|---|---|---|---|
| 1 | **Không** trim, không chuẩn hoá chữ hoa/thường | ✔ | ✔ | Khoảng trắng là một phần mật khẩu |
| 2 | Độ dài tối thiểu (khuyến nghị ≥ 8, NIST: ≥ 8 cho người dùng, ≥ 15 nếu không có lớp xác thực thứ hai) | ✔ | ✔ | Hằng số; người dùng và admin có thể khác nhau — ghi rõ |
| 3 | Độ dài tối đa (64–128) | ✔ | ✔ | bcrypt chỉ dùng 72 byte đầu → chặn hoặc băm trước |
| 4 | Cho phép mọi ký tự, kể cả Unicode, emoji | ✔ | ✔ | Đừng cấm ký tự đặc biệt |
| 5 | Nhập lại khớp (`password_confirmation`) | ✔ | ✔ | |
| 6 | Đổi mật khẩu: nhập đúng mật khẩu hiện tại; mới khác cũ | — | ✔ | |
| 7 | Mật khẩu phổ biến / đã lộ (danh sách, Have I Been Pwned k-anonymity) | — | nên có | Tốt hơn luật "phải có chữ hoa, số, ký tự đặc biệt" |
| 8 | Không chứa email / tên đăng nhập | ✔ | ✔ | |
| 9 | Đồng hồ độ mạnh | tuỳ | — | Gợi ý, không chặn |
| 10 | Băm bằng bcrypt / argon2; không log, không trả về trong API | — | ✔ | [[Bảo mật cơ bản]] |
| 11 | Đổi / đặt lại xong → thu hồi phiên khác, gửi thông báo | — | ✔ | |
| 12 | Giới hạn tần suất đăng nhập / đặt lại | — | ✔ | |

## Bẫy

- Giới hạn tối thiểu ở front-end và back-end khác nhau (6 và 8) → người dùng qua được form nhưng bị server từ chối.
- Ô mật khẩu chặn dán → người dùng trình quản lý mật khẩu khó dùng. Cho phép dán.
- Thông báo "mật khẩu sai" vs "email không tồn tại" → lộ tài khoản. Dùng một câu chung.
- Link đặt lại không hết hạn / dùng lại được nhiều lần.
