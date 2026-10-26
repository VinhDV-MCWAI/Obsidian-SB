---
chu_de: phan-quyen
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Phân quyền theo vai trò

## Lựa chọn của tôi

- **Mỗi API là một quyền**, tên dạng `<chức năng>.<việc>` (`article.list`, `article.store`, `media.delete`) — trùng tên route để không phải duy trì hai danh sách.
- Quyền gom vào **vai trò**; vai trò gán cho người dùng; một người nhiều vai trò → quyền là phần gộp.
- Vai trò và gán quyền lưu **trong DB** → thêm / sửa vai trò không phải sửa code.
- Hai vai trò có sẵn: **Chủ** (luôn toàn quyền, kể cả API mới thêm) và **Chỉ xem** (mọi quyền đọc).
- **Server kiểm thật** (403). Giao diện chỉ ẩn những gì server sẽ từ chối.

## Checklist

- [ ] Một lớp dùng chung kiểm quyền cho mọi API theo tên route; API mới **mặc định cần quyền** (không quên gắn).
- [ ] Vài API luôn được phép với người đã đăng nhập (xem thông tin của chính mình) — liệt kê rõ.
- [ ] Vai trò có sẵn không đổi tên, không xoá được; không đổi quyền của vai trò Chủ.
- [ ] Luôn còn **ít nhất một** Chủ đang hoạt động; không tự khoá / tự bỏ vai trò Chủ của mình.
- [ ] Không xoá vai trò đang có người giữ (hoặc hỏi chuyển sang vai trò khác).
- [ ] Tên vai trò không trùng (không phân biệt hoa thường); quyền gửi lên phải có thật.
- [ ] Đổi vai trò / quyền → có hiệu lực ở **request kế tiếp** (không phải đăng nhập lại).
- [ ] Mọi thay đổi vai trò ghi nhật ký kèm danh sách quyền trước / sau.
- [ ] Phân quyền theo **dữ liệu** (phòng ban, chủ sở hữu, thư mục khoá) là lớp thứ hai, kiểm trong truy vấn — không chỉ theo API.

## Giao diện

- [ ] API "tôi là ai" trả kèm danh sách quyền hiện có → giao diện ẩn menu / nút.
- [ ] Menu chỉ hiện mục có quyền xem; nhóm menu rỗng thì ẩn cả nhóm.
- [ ] Nút Thêm / Sửa / Xoá / chọn nhiều chỉ hiện khi có quyền tương ứng.
- [ ] Form vai trò: **ma trận quyền** — mỗi hàng một chức năng, cột Xem / Thêm / Sửa / Xoá / Khác; tick cả hàng.
- [ ] Quyền đổi trong lúc đang dùng → giao diện tự cập nhật (lần gọi kế tiếp / khi quay lại tab).

## Bẫy

- Chỉ ẩn nút ở giao diện, quên kiểm ở server → gọi thẳng API vẫn được.
- Kiểm quyền theo API nhưng quên quyền theo dữ liệu → người A xem được bản ghi của phòng ban B bằng cách đổi id.
- Tìm kiếm / xuất file / thống kê trả cả dữ liệu người dùng không được xem → mọi đường đọc dữ liệu phải qua cùng bộ lọc quyền.
