---
chu_de: cong-cu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Công cụ kiểm tra tự động

Nguyên tắc: **thứ gì máy kiểm được thì để máy kiểm** — rẻ hơn AI đọc code, chính xác hơn, lần nào cũng cho cùng kết quả. File này nói **cần nhóm công cụ nào và để làm gì**. Lệnh, ba cấp, thời điểm chạy, hook: [[Lệnh kiểm tra và thời điểm chạy]]. Quét bằng extension trước commit: [[Công cụ và hook của dự án]]. Công cụ cụ thể do AI chọn lúc [[Khởi tạo dự án]] theo hệ sinh thái của dự án và những gì máy đã cài.

## Lựa chọn của tôi

- Mọi dự án có đủ **nhóm nền tảng** dưới đây và **một lệnh "kiểm tra tất cả"** (CI chạy đúng lệnh đó).
- Công cụ chỉ áp quy tắc **khi dự án có cấu hình cho nó** — không áp style cá nhân lên code của nhóm.
- Công cụ cá nhân (extension, script riêng) → tự cài, không thêm vào file được track. Nhóm nền tảng còn thiếu → **đề xuất** cho nhóm, kèm lợi / chi phí.
- **Mỗi luật một công cụ chịu trách nhiệm**; review không lặp việc máy.

## Nhóm công cụ

| Nhóm | Bắt gì | Mức |
|---|---|---|
| Định dạng + cấu hình editor chung | Khoảng trắng, xuống dòng, thứ tự import, mã hoá, thụt lề | Nền tảng |
| Lint | Lỗi hay gặp, quy ước, code chết, import thừa | Nền tảng |
| Kiểm tra kiểu | Sai kiểu, null, thiếu trường | Nền tảng |
| Test | Hành vi ([[Chiến lược kiểm thử]]) | Nền tảng |
| Hook trước commit / push | Chạy nhanh các nhóm trên cho phần thay đổi | Nền tảng |
| Quét bí mật | Khoá, token, mật khẩu lọt vào code | Nền tảng |
| Lỗ hổng thư viện | Thư viện có lỗ hổng đã công bố | Nền tảng |
| Phân tích tĩnh bảo mật | Chèn câu lệnh, XSS, dữ liệu không tin cậy tới chỗ nguy hiểm | Nên có |
| Chất lượng code | Trùng lặp, hàm dài, quá phức tạp | Nên có |
| Chính tả | Tên, chuỗi, comment | Nên có |
| File / export / thư viện thừa | Thứ không còn ai dùng | Nên có |
| Ranh giới lớp | Lớp gọi sai lớp | Tuỳ dự án |
| Quét image / hạ tầng; quét virus file tải lên | Lỗ hổng container, cấu hình sai; mã độc | Tuỳ dự án |
| Truy cập dễ dàng, hiệu năng trang, SEO | A11y, tốc độ, thẻ meta | Tuỳ dự án |

Không cài công cụ chỉ vì phổ biến; mỗi cái phải có việc rõ. Language server cho gợi ý / chẩn đoán trong editor nhưng không thay phân tích tĩnh chạy bằng lệnh; review (người / AI) lo logic, rủi ro, ngữ cảnh — [[Review code - quy trình]].

## Bảng quét và review theo công nghệ của dự án

Lúc khởi tạo (bước 6), AI lập bảng này và ghi vào quy tắc theo chủ đề của dự án:

| Phần (công nghệ) | Định dạng | Lint | Kiểu | Test | Bí mật | Thư viện | Bảo mật tĩnh | Extension editor | Lệnh thay extension | Mặt review cần người / AI |
|---|---|---|---|---|---|---|---|---|---|---|
| <server — ngôn ngữ, framework> | | | | | | | | | | |
| <giao diện — framework> | | | | | | | | | | |
| <DB, migration> | | | | | | | | | | |
| <container, CI, hạ tầng> | | | | | | | | | | |
| <tài liệu> | | | | | | | | | | |

1. Liệt kê các phần theo công nghệ **thật** (file cấu hình, lockfile, CI), không đoán.
2. Mỗi ô: lệnh đã có trong dự án, extension đã cài trên máy, hoặc **thiếu**; ghi thời gian chạy từng lệnh.
3. Ô thiếu ở nhóm nền tảng → đề xuất: công cụ, bắt được gì, chi phí, đặt ở repo (cho nhóm) hay máy mình.
4. **Chống trùng**: hai công cụ cùng quản một luật → tắt ở một bên. Cột review chỉ ghi điều máy không bắt được (nghiệp vụ, quyền theo bản ghi, tương thích, dữ liệu).
5. **Chống sai công nghệ**: checklist review của dự án viết theo công nghệ từng phần; lệnh review chọn checklist theo đường dẫn file trong diff.
6. Thời gian chạy quyết định chỗ đặt: lưu file, trước commit, trước push hay CI.

## Extension của editor

AI kiểm editor và extension đã cài, đề xuất phần còn thiếu theo nhóm: hiện lỗi của linter / kiểm kiểu **theo đúng cấu hình dự án**; hiện lỗi ngay trên dòng; phân tích bảo mật và chất lượng; chính tả; lịch sử git theo dòng; ngôn ngữ / framework chính.

| Thiết lập | Lưu ở |
|---|---|
| Giao diện, phím tắt, extension cá nhân | Cài đặt người dùng + đồng bộ của editor |
| Extension nên có cho repo | File gợi ý extension trong repo |
| Định dạng, luật lint, quy ước | Cấu hình công cụ + tài liệu trong repo |
| Phiên bản runtime | File phiên bản, lockfile, container |

- Dự án đã có danh sách extension đề xuất → dùng theo đó. Editor cho tách bộ extension theo loại dự án → dùng.
- Đồng bộ bằng repo trước, đồng bộ editor sau; đồng bộ editor không phủ môi trường từ xa (WSL, SSH, container).

## Thứ tự dựng cho dự án mới

1. Định dạng + cấu hình editor chung.
2. Lint + kiểm tra kiểu ở mức vừa, nâng dần.
3. Test chạy được bằng một lệnh.
4. Hook trước commit (chỉ bước nhanh); bước chậm để trước push / CI.
5. Lệnh "kiểm tra tất cả".
6. CI chạy đúng lệnh đó + quét bí mật + quét thư viện.
7. Kiểm "file sinh ra không cũ" nếu có sinh code ([[Hợp đồng API giữa các phần]]).

## Khi vào dự án có sẵn

- [ ] Lệnh nào kiểm tra gì → ghi vào quy tắc dự án (mục Lệnh).
- [ ] Lỗi linter có sẵn rất nhiều → không sửa hàng loạt; chỉ đảm bảo phần mình sửa sạch (nợ cũ: [[Lệnh kiểm tra và thời điểm chạy]]).
- [ ] Thiếu nhóm nền tảng → đề xuất, kèm lợi / chi phí.

## Bẫy

- Mỗi máy cài một bản công cụ khác → kết quả khác nhau (dùng lockfile / container).
- Hai công cụ chồng luật (định dạng và lint cùng quản khoảng trắng) → cãi nhau mãi.
