---
chu_de: review-chon-kiem-tra
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Chọn kiểm tra theo loại thay đổi và rủi ro

Dùng ở Phase 1 của [[Review code - quy trình]]: diff chạm loại nào thì chạy kiểm tra nào. Lệnh cụ thể nằm ở quy tắc của dự án ([[Lệnh kiểm tra và thời điểm chạy]]).

## Lựa chọn của tôi

- Local: kiểm **phần thay đổi** và phần bị ảnh hưởng; CI: kiểm **toàn bộ**.
- Bảng dưới chọn kiểm tra **bổ sung**, không bỏ kiểm tra cơ sở của dự án.
- Giảm việc thừa, không giảm kiểm tra quan trọng để nhanh hơn.
- Phân biệt **lỗi mới** (phải sạch) với **nợ cũ** — quy tắc baseline và ngoại lệ: [[Lệnh kiểm tra và thời điểm chạy]], mục Nợ cũ.

## Ma trận

| Diff chạm | Kiểm tra |
|---|---|
| Chỉ tài liệu | Định dạng, chính tả, link (nếu dự án có) |
| Code client (script, kiểu) | Định dạng, lint, kiểm kiểu, test liên quan |
| Template / trang | Kiểm riêng của framework giao diện + lint + kiểu |
| Style | Lint style, định dạng |
| Code server | Định dạng, lint, phân tích tĩnh, test liên quan |
| File test | Lint, kiểu, chạy test mới và test liên quan |
| Khai báo thư viện, lockfile | Nhất quán phụ thuộc, quét lỗ hổng thư viện |
| Cấu trúc DB, migration | Chạy migration + quay lui trên DB test, [[Review - Dữ liệu và migration]] |
| Hợp đồng API | Sinh lại đặc tả / kiểu, test tích hợp, [[Review - Hợp đồng và tương thích]] |
| Đăng nhập, quyền, upload, dữ liệu nhập | Test quyền, [[Review - Bảo mật]] |
| Container, CI, triển khai | Kiểm cú pháp cấu hình, build thử |

## Phạm vi ảnh hưởng

Xác định từ diff:

- [ ] File thêm / sửa / xoá / đổi tên.
- [ ] Module nào dùng hàm, API, kiểu bị đổi (đổi kiểu trả về → phân tích rộng hơn file đã sửa).
- [ ] Có đổi thư viện, migration, cấu hình chạy không.
- [ ] Đường đi dữ liệu và quyền có bị tác động không.
- [ ] Test nào xác nhận được hành vi bị đổi.

## Bẫy

- Chỉ phân tích file đã sửa khi đổi chữ ký hàm dùng ở nhiều module → bỏ sót lỗi nơi gọi.
