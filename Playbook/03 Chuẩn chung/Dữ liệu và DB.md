---
chu_de: db
ap_dung: [back-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Dữ liệu và DB

Nền: [[Chương 16 — Database, index, B-tree, buffer pool và vì sao không thể chỉ lưu file thẳng]], [[Migrate Schema]], [[Migrate Data]], [[backup-restore]].

## Lựa chọn của tôi

- Ràng buộc quan trọng (unique, khoá ngoại, not null) đặt **ở DB**, không chỉ ở code.
- Thời gian lưu UTC; tiền lưu số nguyên đơn vị nhỏ nhất hoặc số thập phân cố định.
- Xoá mềm cho dữ liệu người dùng tạo; xoá thật có xác nhận và ghi nhật ký ([[Thùng rác và xoá]]).
- Migration lặp lại an toàn, có đường lui; đổi bảng lớn theo kiểu mở rộng → chuyển → thu hẹp.
- Test chạy trên DB riêng; dữ liệu thử có tiền tố để dọn.

## Đặt tên

- [ ] Bảng: `snake_case`, số nhiều (hoặc theo quy ước dự án — thống nhất một kiểu).
- [ ] Khoá ngoại: `<bảng số ít>_id`. Bảng nối: `<a>_<b>` theo thứ tự chữ cái.
- [ ] Thời gian: `created_at`, `updated_at`, `deleted_at` (UTC).
- [ ] Boolean: `is_…`, `has_…`. Index: `<bảng>_<cột>_index`, unique `<bảng>_<cột>_unique`.

## Ràng buộc ở DB (không chỉ ở code)

- [ ] Unique bằng **index unique**, không chỉ kiểm tra ở validate (hai request cùng lúc vẫn lọt).
- [ ] Unique không phân biệt hoa thường → index trên `lower(col)` (hoặc cột `citext`).
- [ ] Có xoá mềm → unique phải tính tới `deleted_at` (index một phần `WHERE deleted_at IS NULL`) hoặc chấp nhận không tạo lại được.
- [ ] Khoá ngoại có hành vi rõ: `restrict` (mặc định an toàn), `cascade` (chỉ khi con không có nghĩa nếu thiếu cha), `set null`.
- [ ] `NOT NULL` + giá trị mặc định cho cột bắt buộc.
- [ ] Tiền: số nguyên đơn vị nhỏ nhất hoặc `DECIMAL`, **không** `float` ([[Chương 13 — Floating Point, IEEE 754 và vì sao 0.1 + 0.2 != 0.3]]).

## Migration

- [ ] Mỗi migration chạy được lặp lại an toàn trên DB mới.
- [ ] Có đường lui (`down`) hoặc ghi rõ vì sao không.
- [ ] Đổi trên bảng lớn / đang chạy: **mở rộng → chuyển dữ liệu → thu hẹp** qua nhiều lần phát hành (thêm cột mới, ghi cả hai, chuyển, xoá cột cũ sau).
- [ ] Không sửa migration đã chạy ở môi trường chung; tạo migration mới.
- [ ] Migration dữ liệu tách khỏi migration cấu trúc.
- [ ] Không chạy rollback trên DB dev / chung có dữ liệu thật khi chưa hỏi — thử trên DB test.

## Truy vấn

- [ ] Index cho cột lọc, sắp xếp, khoá ngoại.
- [ ] Không N+1: nạp quan hệ trước (eager load); bật cảnh báo N+1 ở dev nếu framework có.
- [ ] Danh sách luôn phân trang; giới hạn `per_page` tối đa.
- [ ] Truy vấn chậm: xem kế hoạch (`EXPLAIN ANALYZE`) trước khi đoán.
- [ ] Không `SELECT *` ở truy vấn nóng.

## Sao lưu

- [ ] Có sao lưu tự động **và đã thử khôi phục** (sao lưu chưa thử = chưa có).
- [ ] Sao lưu gồm cả file (kho file), không chỉ DB — và cả phiên bản cũ nếu "thùng rác" dựa vào phiên bản.
- [ ] So sánh sau khi khôi phục: số dòng + mã băm nội dung.

## Dữ liệu thử

- [ ] Đặt tiền tố dễ nhận (`TMP `, `E2E `) để dọn.
- [ ] Test chạy trên DB riêng, không bao giờ trên DB dev / chung.
