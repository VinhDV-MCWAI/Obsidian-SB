---
chu_de: validate-file
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Validate — File và ảnh

Nền: [[minio]]. Giải pháp đầy đủ: [[Upload file và kho file]].

| # | Kiểm tra | FE | BE | Ghi chú |
|---|---|---|---|---|
| 1 | Dung lượng tối đa — ở **cả** proxy, máy chủ ứng dụng, code | ✔ | ✔ | Ba nơi khác nhau → lỗi khó hiểu (413 từ proxy, không qua khung phản hồi) |
| 2 | Loại file theo **nội dung** (magic bytes / MIME thật), không theo đuôi | gợi ý | ✔ | `.exe` đổi tên `.jpg` |
| 3 | Đuôi file nằm trong danh sách cho phép | ✔ | ✔ | |
| 4 | Ảnh: kích thước pixel tối đa (chống "bom giải nén"), đọc được bằng thư viện ảnh | — | ✔ | |
| 5 | Tên file: rất dài, có dấu, emoji, ký tự điều khiển, `../`, khoảng trắng đầu/cuối, trùng tên | — | ✔ | Lưu bằng tên sinh ra; giữ tên gốc trong DB |
| 6 | Số file mỗi lần tối đa | ✔ | ✔ | |
| 7 | File rỗng (0 byte) | ✔ | ✔ | |
| 8 | Quét virus nếu người khác tải về | — | tuỳ | công cụ quét virus |
| 9 | Xoá metadata nhạy cảm của ảnh (EXIF vị trí) | — | tuỳ | |
| 10 | File lớn: tải nhiều phần, tiếp tục được, có tiến độ | ✔ | ✔ | |
| 11 | Import CSV/Excel: mã hoá (UTF-8, BOM, Shift-JIS), dòng trống, cột thiếu, số dòng tối đa | — | ✔ | Báo lỗi theo dòng |
| 12 | DB lỗi sau khi lưu file → không để file mồ côi | — | ✔ | [[Giao dịch, lỗi và phản hồi API]] |

## Bẫy

- Kiểm `Content-Type` do trình duyệt gửi → giả được.
- SVG chứa script → hiển thị trực tiếp là XSS. Lọc hoặc phục vụ với `Content-Disposition: attachment`.
- Tải file lớn chặn luồng xử lý / ảnh hưởng API khác → kiểm cùng lúc.
- Nén / giải nén khác nhau theo hệ điều hành (tên file tiếng Việt trong ZIP).
