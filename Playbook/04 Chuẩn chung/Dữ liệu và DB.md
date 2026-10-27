---
chu_de: db
ap_dung: [back-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Dữ liệu và DB

Thiết kế bảng cho một việc (ERD, bảng cột): [[Thiết kế trước khi làm]]. Câu hỏi khi đọc diff: [[Review - Dữ liệu và migration]]. Nền: [[Chương 16 — Database, index, B-tree, buffer pool và vì sao không thể chỉ lưu file thẳng]], [[Migrate Schema]], [[Migrate Data]].

## Lựa chọn của tôi

- Ràng buộc quan trọng (unique, khoá ngoại, not null) đặt **ở DB**, không chỉ ở code.
- Thời gian lưu UTC; tiền lưu số nguyên đơn vị nhỏ nhất hoặc số thập phân cố định.
- Xoá mềm cho dữ liệu người dùng tạo; xoá hẳn có xác nhận và ghi nhật ký ([[Thùng rác và xoá]]).
- Migration chạy lặp lại an toàn, có đường lui; đổi bảng lớn theo **mở rộng → chuyển → thu hẹp**.

## Đặt tên

- [ ] Bảng `snake_case`, số nhiều (hoặc theo quy ước dự án — thống nhất một kiểu).
- [ ] Khoá ngoại `<bảng số ít>_id`; bảng nối `<a>_<b>` theo thứ tự chữ cái.
- [ ] Thời gian `created_at`, `updated_at`, `deleted_at` (UTC); boolean `is_…`, `has_…`.
- [ ] Index `<bảng>_<cột>_index`, unique `<bảng>_<cột>_unique`.

## Ràng buộc

- [ ] Unique bằng **index unique** (kiểm ở validate thôi thì hai request cùng lúc vẫn lọt).
- [ ] Unique không phân biệt hoa thường → index trên `lower(col)` hoặc kiểu chữ không phân biệt hoa thường.
- [ ] Có xoá mềm → unique tính tới `deleted_at` (index một phần `WHERE deleted_at IS NULL`).
- [ ] Khoá ngoại có hành vi rõ: `restrict` (mặc định an toàn), `cascade` (chỉ khi con vô nghĩa nếu thiếu cha), `set null`.
- [ ] `NOT NULL` + mặc định cho cột bắt buộc.
- [ ] Tiền: số nguyên đơn vị nhỏ nhất hoặc `DECIMAL`, **không** số thực ([[Validate - Số và tiền]]).

## Migration

- [ ] Chạy được lặp lại an toàn trên DB mới **và** trên dữ liệu thật đang có.
- [ ] Có đường lui hoặc ghi rõ vì sao không.
- [ ] Bảng lớn / đang chạy: thêm cột mới → ghi cả hai → chuyển dữ liệu → xoá cột cũ, qua nhiều lần phát hành.
- [ ] Không sửa migration đã chạy ở môi trường chung; tạo migration mới.
- [ ] Migration dữ liệu tách khỏi migration cấu trúc.
- [ ] Không chạy quay lui trên DB dev / chung có dữ liệu khi chưa hỏi — thử trên DB test.

## Truy vấn

- [ ] Index cho cột lọc, sắp xếp, khoá ngoại.
- [ ] Không N+1: nạp quan hệ trước; bật cảnh báo N+1 ở dev nếu framework có.
- [ ] Danh sách luôn phân trang, giới hạn số dòng mỗi trang.
- [ ] Truy vấn chậm: xem kế hoạch truy vấn (`EXPLAIN ANALYZE`) trước khi đoán.
- [ ] Không lấy mọi cột ở truy vấn chạy nhiều.
- [ ] Ghi nhiều bảng trong một nghiệp vụ → một giao dịch ([[Giao dịch, lỗi và phản hồi API]]).

Sao lưu và diễn tập khôi phục: [[Vận hành và sự cố]]. Dữ liệu test: [[Chiến lược kiểm thử]].
