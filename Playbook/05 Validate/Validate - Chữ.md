---
chu_de: validate-chu
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Chữ (tên, tiêu đề, mô tả, nội dung)

## Chữ ngắn (tên, tiêu đề, mã)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Trim; gộp nhiều khoảng trắng liền nhau nếu nghiệp vụ muốn | Tên người: giữ nguyên dấu |
| 2 | Bắt buộc — chuỗi toàn khoảng trắng là rỗng | |
| 3 | Độ dài min / max theo **ký tự**, không theo byte | Dùng hàm đếm ký tự Unicode của ngôn ngữ; tiếng Việt 1 ký tự có thể 2–3 byte |
| 4 | Ký tự cho phép (mã: `[A-Z0-9_-]`; tên: chữ, dấu, khoảng trắng, `'`, `-`) | Đừng cấm dấu tiếng Việt / tên nước ngoài |
| 5 | Chuẩn hoá Unicode NFC | `ệ` có hai cách mã hoá → so trùng sai |
| 6 | Trùng (tên danh mục, mã): không phân biệt hoa thường, theo phạm vi (trong cùng cha / cùng phòng ban) | Unique index theo đúng phạm vi |
| 7 | Không có ký tự điều khiển, xuống dòng | |

## Chữ dài (mô tả, ghi chú)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Luôn có giới hạn tối đa (kể cả "không giới hạn" trong yêu cầu) | Chống request khổng lồ |
| 2 | Giữ xuống dòng; hiển thị bằng escape + giữ xuống dòng, không chèn HTML | |
| 3 | Đếm ký tự hiển thị ở FE khớp cách đếm của BE (emoji = 1 hay 2?) | |

## Nội dung HTML / Markdown (trình soạn thảo)

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Lọc HTML theo **danh sách cho phép** ở server bằng thư viện lọc đã được kiểm chứng | Không dùng danh sách cấm, không tự viết regex |
| 2 | Chặn `javascript:` trong link, `on*=` thuộc tính, `<script>`, `<iframe>` không cho phép | |
| 3 | Giới hạn dung lượng, số ảnh nhúng | |
| 4 | Ảnh nhúng base64 → chuyển thành file hoặc chặn | Bloat DB |
| 5 | Lưu bản gốc + bản đã lọc (nếu cần sửa lại sau) | |

## Bẫy

- Kiểm độ dài theo byte → tên tiếng Việt bị cắt ngắn bất ngờ.
- Cắt chuỗi giữa ký tự nhiều byte / emoji → ký tự hỏng.
- Tìm kiếm không chuẩn hoá dấu → tìm "Hà Nội" không ra "Hà Nội" (khác mã hoá).
