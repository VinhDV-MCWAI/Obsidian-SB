---
chu_de: validate-lua-chon-id
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Lựa chọn, ID, danh sách

## Lựa chọn (enum, dropdown, radio, checkbox)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Giá trị nằm trong tập cho phép (enum) ở **server** | Người dùng sửa HTML gửi giá trị lạ |
| 2 | Lựa chọn phụ thuộc (tỉnh → huyện): con thuộc đúng cha | |
| 3 | Dropdown không có option nào → giao diện ra sao | [[Testing]] |
| 4 | Option tên quá dài | |
| 5 | Lựa chọn đã ngừng dùng: bản ghi cũ vẫn hiển thị được, bản ghi mới không chọn được | |

## ID (trên URL, trong body, khoá ngoại)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Không phải số / UUID sai dạng (`abc`, `1.1`, `??#`) → 404 hoặc 422, **không 500** | |
| 2 | Số cực lớn (`10000000000000000000000`) → không tràn, không 500 | |
| 3 | Không tồn tại → 404 với câu chung (không lộ tên bảng) | |
| 4 | Đã xoá mềm → coi như không tồn tại (trừ màn thùng rác) | |
| 5 | **Thuộc quyền** người dùng (cùng tổ chức / phòng ban / chủ sở hữu) | Đổi id trên URL để xem dữ liệu người khác — lỗi bảo mật phổ biến nhất |
| 6 | Khoá ngoại tồn tại **và** đang hoạt động (không gán vào danh mục đã khoá) | |
| 7 | Không tự tham chiếu / vòng lặp (danh mục cha là chính nó hoặc con của nó) | |
| 8 | Chuỗi độc hại trên URL (`' OR 1=1 --`, `<script>`) | |

## Danh sách (mảng id, nhiều lựa chọn, thao tác hàng loạt)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Là mảng; mỗi phần tử đúng kiểu | |
| 2 | Số phần tử tối thiểu / tối đa (chống gửi 100.000 id) | |
| 3 | Trùng phần tử → loại hoặc báo lỗi | |
| 4 | Từng id: tồn tại + thuộc quyền (kiểm **một truy vấn**, không N+1) | |
| 5 | Hàng loạt: một phần lỗi thì sao? Tất cả hoặc không (giao dịch) hay báo từng dòng — ghi rõ | |
| 6 | Xoá hàng loạt bản ghi đang được nơi khác dùng → chặn / báo rõ bản nào | |
