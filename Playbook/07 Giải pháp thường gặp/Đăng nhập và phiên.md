---
chu_de: dang-nhap-phien
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Đăng nhập và phiên

Nền: [[authen_&_author]], [[CSRF]], [[Session tạo ra như thế nào, làm sao sever biết session nào, cookie tạo ra như thế nào, làm sao session xác đinh cookie, làm sao server biết session đó của user nào]].

## Lựa chọn của tôi

| Loại client | Cách đăng nhập |
|---|---|
| Web cùng tên miền (trang quản trị, trang người dùng) | **Phiên phía server + cookie** (`HttpOnly`, `Secure`, `SameSite`) + mã chống giả mạo (CSRF) |
| App điện thoại, công cụ dòng lệnh, máy gọi máy | **Token API** có phạm vi quyền và hạn dùng, thu hồi được |
| Bên thứ ba đăng nhập thay người dùng | OAuth — quyết định riêng |

Dùng phần đăng nhập **có sẵn của nền tảng**. Không tự viết bộ ký / giải mã token.

## Vì sao phiên server hơn token tự viết cho web (bài học đã trả giá)

| Mặt | Token tự viết (truy cập ngắn + làm mới) | Phiên server + cookie |
|---|---|---|
| Mã hoá | Tự viết, dễ sai thứ tự kiểm tra | Có sẵn, đã kiểm chứng |
| Lộ kho phiên (cache) | Token thô làm khoá → đọc được kho là có token dùng ngay | Kho chỉ có mã phiên; tạo cookie hợp lệ còn cần khoá bí mật |
| Khoá / xoá tài khoản | Vẫn làm mới được token tới hết hạn | Bị chặn ngay request kế tiếp |
| Đổi quyền | Phải đăng nhập lại | Áp dụng ngay request kế tiếp |
| Nhiều tab | Các tab tranh nhau làm mới token, cần khoá | Dùng chung cookie; đồng bộ đăng nhập / đăng xuất giữa tab bằng kênh trình duyệt |
| Đăng xuất | Hỏng khi token đã hết hạn | Luôn thành công |
| Chi phí mỗi request | Kiểm chữ ký | Đọc kho phiên + 1–2 truy vấn theo khoá chính — chậm hơn chút, đổi lại thu hồi được ngay |

## Checklist

- [ ] Sai tên, sai mật khẩu, tài khoản bị khoá → **cùng một** lỗi 401, cùng một câu.
- [ ] Khoá tạm khi sai nhiều lần bằng **hai bộ đếm**: theo *tên đăng nhập + IP* (ví dụ 5 lần → khoá tên đó trên IP đó 15 phút, đăng nhập đúng thì xoá) và theo *IP* (ví dụ 30 lần với bất kỳ tên nào → khoá IP, chặn thử hàng loạt tên). Đếm trong cache, không trong DB. Không khoá vĩnh viễn (bị dùng để khoá người khác).
- [ ] Đổi mã phiên sau khi đăng nhập.
- [ ] Mỗi thiết bị một phiên; màn hình **thiết bị đang đăng nhập** xem và đăng xuất từ xa.
- [ ] "Ghi nhớ đăng nhập" riêng từng thiết bị, có hạn (ví dụ 30 ngày), thu hồi được từ màn hình thiết bị.
- [ ] Đổi mật khẩu → đăng xuất mọi phiên khác.
- [ ] Mỗi request kiểm lại tài khoản còn hoạt động và quyền hiện tại (thu hồi tức thì). Cache thông tin này chỉ khi tải DB thật sự cao, và phải xoá cache khi tài khoản bị sửa / khoá.
- [ ] Ghi nhật ký: đăng nhập, đăng xuất, đăng nhập sai (chỉ tên đã gõ, không mã tài khoản) — [[Nhật ký thay đổi]].
- [ ] Production: cookie `Secure` (bắt buộc với HTTPS); cân nhắc mã hoá nội dung phiên.
- [ ] Tài khoản dùng chung nhiều người → không biết ai làm gì. Mỗi người một tài khoản + vai trò.
- [ ] Client: 401 → về trang đăng nhập (giữ đường dẫn quay lại); 403 → "không có quyền", **không** đi làm mới phiên.

## Bẫy

- Trả 401 cho "thiếu quyền" → client đi làm mới token vô ích, lặp vô tận.
- Đọc khoá bí mật trực tiếp từ biến môi trường trong code → hỏng khi bật cache cấu hình.
- Giao diện tự hẹn giờ làm mới token → nhiều tab tranh nhau, đăng xuất ngẫu nhiên.
