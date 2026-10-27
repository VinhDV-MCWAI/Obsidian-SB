---
chu_de: cong-cu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Công cụ kiểm tra tự động

Nguyên tắc: **thứ gì máy kiểm được thì để máy kiểm.** Rẻ hơn AI đọc code, chính xác hơn, lần nào cũng cho cùng kết quả, và báo ngay trong editor.

File này chỉ nói **cần nhóm công cụ nào và để làm gì**. Công cụ cụ thể do AI chọn lúc khởi tạo, theo ngôn ngữ / hệ sinh thái của dự án, editor và những gì máy đã cài ([[Khởi tạo dự án]], bước kiểm tra môi trường).

## Lựa chọn của tôi

- Mọi dự án có đủ **bảy nhóm nền tảng** dưới đây và **một lệnh "kiểm tra tất cả"** chạy trước khi báo xong (CI chạy đúng lệnh đó).
- Công cụ chỉ áp quy tắc **khi dự án có cấu hình cho nó** — không áp style cá nhân lên code của nhóm.
- Công cụ cá nhân (plugin editor, script kiểm tra riêng) → tự cài, không thêm vào file được track của nhóm. Công cụ nền tảng còn thiếu → **đề xuất** cho nhóm.

## Nhóm công cụ

| Nhóm | Bắt gì | Mức |
|---|---|---|
| Định dạng | Khoảng trắng, xuống dòng, thứ tự import | Nền tảng |
| Lint | Lỗi hay gặp, quy ước, code chết, import thừa | Nền tảng |
| Kiểm tra kiểu | Sai kiểu, null, thiếu trường | Nền tảng |
| Test | Hành vi ([[Chiến lược kiểm thử]]) | Nền tảng |
| Hook trước commit / push | Chạy nhanh các nhóm trên trên phần thay đổi | Nền tảng |
| Quét bí mật | Khoá, token, mật khẩu lọt vào code | Nền tảng |
| Lỗ hổng thư viện | Thư viện có lỗ hổng đã công bố | Nền tảng |
| Phân tích tĩnh bảo mật | Chèn câu lệnh, XSS, dữ liệu không tin cậy đi tới chỗ nguy hiểm | Nên có |
| Chất lượng code | Trùng lặp, hàm dài, quá phức tạp | Nên có |
| Chính tả | Sai chính tả trong tên, chuỗi, comment | Nên có |
| File / export / thư viện thừa | Thứ không còn ai dùng | Nên có |
| Ranh giới lớp | Lớp gọi sai lớp | Tuỳ dự án |
| Quét image / hạ tầng | Lỗ hổng container, cấu hình hạ tầng sai | Tuỳ dự án |
| Quét virus file tải lên | Mã độc | Tuỳ dự án |
| Truy cập dễ dàng, hiệu năng trang, SEO | A11y, tốc độ, thẻ meta | Tuỳ dự án |

## Plugin / extension của editor

AI kiểm editor đang dùng và plugin đã cài, rồi đề xuất phần còn thiếu theo nhóm:

| Nhóm | Để làm gì |
|---|---|
| Hiện lỗi của linter / kiểm tra kiểu ngay trong editor | Lỗi hiện **giống hệt** khi chạy lệnh, theo đúng cấu hình của dự án |
| Hiện lỗi ngay trên dòng | Không phải mở bảng lỗi mới thấy |
| Phân tích bảo mật và chất lượng code | Bắt lỗi mà linter thường bỏ qua |
| Chính tả | Tên biến, chuỗi, comment |
| Lịch sử git theo dòng | Biết ai sửa, vì sao |
| Ngôn ngữ / framework chính của dự án | Gợi ý, đi tới định nghĩa, kiểm cú pháp |

- Dự án đã có danh sách plugin đề xuất → dùng theo đó.
- Nhiều plugin chỉ phân tích **file đang mở** → bảng lỗi trống không có nghĩa là sạch. Muốn thấy lỗi cả dự án: bật chế độ phân tích toàn dự án nếu plugin có, hoặc chạy lệnh qua cơ chế task của editor.
- Editor cho phép tách bộ plugin theo loại dự án → dùng, để mỗi dự án chỉ nạp thứ cần.

## Thứ tự dựng cho dự án mới

1. Định dạng + cấu hình editor chung → hết tranh cãi định dạng.
2. Lint + kiểm tra kiểu ở mức vừa, nâng dần.
3. Test chạy được bằng một lệnh.
4. Hook trước commit (chỉ bước nhanh, < 10 giây); bước chậm (test, kiểu cả dự án) để trước push.
5. Một lệnh "kiểm tra tất cả" — chạy trước khi báo xong.
6. CI chạy đúng lệnh đó + quét bí mật + quét thư viện.
7. Kiểm "file sinh ra không cũ" nếu có sinh code (đặc tả API → kiểu dữ liệu).

## Khi vào dự án có sẵn

- [ ] Lệnh nào kiểm tra gì? Ghi vào quy tắc của dự án (mục Lệnh).
- [ ] Lỗi linter có sẵn rất nhiều → không sửa hàng loạt (lẫn với việc của mình); chỉ đảm bảo phần mình sửa sạch.
- [ ] Thiếu nhóm nền tảng → đề xuất, kèm lợi / chi phí.
- [ ] Kiểm tra mỗi việc **một lần**: phần đã qua ở bước trước (cùng nội dung, cùng cấu hình) thì bước sau bỏ qua; chỉ chạy test / kiểu cho phần bị ảnh hưởng.
