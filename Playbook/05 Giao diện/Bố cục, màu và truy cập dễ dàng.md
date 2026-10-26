---
chu_de: bo-cuc-mau-a11y
ap_dung: [front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Bố cục, màu và truy cập dễ dàng

## Lựa chọn của tôi

- **Một khung trang chung:** header (logo, tìm kiếm, người dùng), menu bên, vùng nội dung có breadcrumb + **tiêu đề trang + nút hành động chính ở cùng vị trí mọi trang**.
- **Design token** cho màu, khoảng cách, cỡ chữ, bo góc, đổ bóng — thành phần chỉ dùng token, không mã màu / số viết thẳng.
- Màu theo **vai trò**: chính, phụ, thành công, cảnh báo, lỗi, thông tin, chữ, nền, viền — mỗi vai trò có bản sáng và tối.
- Đạt mức **WCAG 2.2 AA** cho phần người dùng chính dùng.
- Thiết kế cho điện thoại trước nếu người dùng chủ yếu dùng điện thoại; trang quản trị thì máy tính trước nhưng không vỡ trên điện thoại.

## Bố cục

- [ ] Thứ tự đọc: tiêu đề → bộ lọc / công cụ → nội dung → phân trang.
- [ ] Nút hành động chính: một nút nổi bật mỗi màn hình; hành động nguy hiểm màu lỗi, không đặt cạnh nút chính dễ bấm nhầm.
- [ ] Khoảng cách theo thang cố định (4 / 8 / 12 / 16 / 24 / 32…).
- [ ] Nội dung dài: chiều rộng dòng chữ vừa đọc (~60–80 ký tự); bảng rộng cuộn ngang trong khung, không làm cả trang cuộn ngang.
- [ ] Điểm ngắt màn hình thống nhất; kiểm ở ~360 px, ~768 px, ~1280 px.
- [ ] Chế độ tối (nếu có): mọi màu qua token, kiểm cả hai chế độ.

## Truy cập dễ dàng

- [ ] Mọi ô nhập có nhãn; lỗi gắn với ô (đọc màn hình đọc được).
- [ ] Nút chỉ có biểu tượng có tên cho đọc màn hình.
- [ ] Làm được mọi thao tác bằng bàn phím; thấy rõ vị trí focus; thứ tự Tab hợp lý.
- [ ] Độ tương phản chữ ≥ 4.5:1 (chữ lớn ≥ 3:1); không dùng màu làm tín hiệu duy nhất.
- [ ] Vùng bấm trên điện thoại đủ lớn (~44 px).
- [ ] Ảnh có mô tả thay thế (ảnh trang trí để trống).
- [ ] Chuyển động có thể tắt theo cài đặt "giảm chuyển động" của hệ điều hành.
- [ ] Kiểm tự động bằng công cụ (axe / Lighthouse) + một lượt dùng thử chỉ bằng bàn phím.

## Bẫy

- Mỗi màn hình đặt nút chính một chỗ → người dùng phải tìm.
- Mã màu viết thẳng trong thành phần → chế độ tối / đổi thương hiệu phải sửa từng chỗ.
- Tắt viền focus cho đẹp → người dùng bàn phím không biết đang ở đâu.
