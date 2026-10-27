---
chu_de: validate-tham-so-danh-sach
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Tham số danh sách (tìm kiếm, phân trang, sắp xếp, lọc)

## Phân trang

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | `page` là số nguyên ≥ 1; sai → về 1 hoặc 422 (thống nhất một) | |
| 2 | `page` vượt số trang → danh sách rỗng + meta đúng, không lỗi | [[Testing]] |
| 3 | `per_page` có tối đa (ví dụ 100) và danh sách giá trị cho phép | Chống `per_page=1000000` |
| 4 | Không có API "lấy tất cả" không giới hạn; nếu bắt buộc thì thử với dữ liệu cực lớn | |

## Sắp xếp

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | `sort_by` trong **danh sách cột cho phép** — không đưa thẳng vào SQL | SQL injection qua `ORDER BY` |
| 2 | `sort_order` chỉ `asc` / `desc` | |
| 3 | Có khoá phụ ổn định (id) để phân trang không lặp / sót | |

## Tìm kiếm

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Trim; độ dài tối đa; chuỗi rỗng = không lọc | |
| 2 | Ký tự đặc biệt của `LIKE` (`%`, `_`) được escape | Tìm "50%" |
| 3 | Unicode, có dấu / không dấu, full-width, emoji, xuống dòng | |
| 4 | Chuỗi độc hại không gây lỗi, không thực thi khi hiển thị lại | |
| 5 | Hiệu năng: có index phù hợp (full-text / trigram) | |
| 6 | Bấm tìm nhiều lần liên tục → chống gọi trùng (debounce, huỷ request cũ) | |

## Lọc

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Mỗi bộ lọc validate theo loại trường của nó (ngày, enum, id) | |
| 2 | Khoảng ngày: từ ≤ đến | [[Validate - Ngày giờ]] |
| 3 | Bộ lọc theo id: kiểm quyền (không lọc ra dữ liệu phòng ban khác) | |
| 4 | Tham số lạ bỏ qua, không lỗi | |
| 5 | Trạng thái lọc giữ trên URL (chia sẻ được, quay lại được) | Giao diện |
