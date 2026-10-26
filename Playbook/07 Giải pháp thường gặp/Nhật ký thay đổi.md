---
chu_de: nhat-ky-thay-doi
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Nhật ký thay đổi (audit log)

Ai đã thêm, sửa, xoá gì, lúc nào — khác với log kỹ thuật ([[Log và theo dõi]]): nhật ký thay đổi là **dữ liệu nghiệp vụ**, người dùng xem được.

## Lựa chọn của tôi

- **Một bảng chung** cho mọi loại dữ liệu: loại bản ghi, mã bản ghi, việc, giá trị cũ, giá trị mới, người làm, IP, thời điểm.
- Ghi tự động ở tầng dữ liệu (sự kiện thêm / sửa / xoá của model), không gọi tay ở từng chỗ.
- **Chỉ đọc**: không API nào sửa / xoá dòng nhật ký.

## Ghi gì

| Việc | Nội dung |
|---|---|
| Thêm | Giá trị mới |
| Sửa | **Chỉ các ô thay đổi**: cũ → mới |
| Xoá | Ảnh chụp bản ghi trước khi xoá |
| Khôi phục / xoá vĩnh viễn | Vị trí sau khôi phục / ảnh chụp trước khi xoá hẳn |
| Đăng nhập, đăng xuất | Người, IP |
| Đăng nhập sai | **Chỉ tên đã gõ**, không mã tài khoản (không lộ tài khoản có tồn tại) |
| Thay đổi do công cụ / tự động | Đánh dấu nguồn (ví dụ `via: importer`) |

Không bao giờ ghi: mật khẩu, token, mã ghi nhớ đăng nhập.

## Hiển thị

- [ ] Màn hình nhật ký: mới nhất trước, phân trang, lọc theo loại / người / khoảng ngày; bấm để xem từng ô cũ → mới.
- [ ] Dưới form sửa của mỗi loại dữ liệu: lịch sử của đúng bản ghi đó.
- [ ] Trang tổng quan: vài hoạt động gần nhất.

## Bẫy

- Ghi nhật ký trong cùng giao dịch với thao tác lỗi → rollback mất luôn nhật ký (nhất là đăng nhập sai). Xem [[Giao dịch, lỗi và phản hồi API]].
- Lọc theo ngày đọc sai định dạng ngày/tháng → lỗi chỉ lộ ra với ngày > 12 ([[Validate - Ngày giờ]]).
- Bảng lớn nhanh → có index theo (loại, mã bản ghi) và thời điểm; có chính sách giữ / lưu trữ.
- Bỏ một tính năng: dòng nhật ký cũ vẫn còn → giao diện phải hiển thị được loại dữ liệu không còn tồn tại.
