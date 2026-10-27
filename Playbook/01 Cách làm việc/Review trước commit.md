---
chu_de: review-truoc-commit
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review trước commit

**Lựa chọn của tôi:** lỗi máy bắt được thì để máy bắt và sửa trước; AI chỉ dùng cho phần máy không hiểu (logic, mapping, quy tắc, convention). AI **không** tự commit / push — chỉ đưa danh sách file cần `git add` và commit message.

Lúc [[Khởi tạo dự án]], AI biến quy trình này thành một lệnh / kỹ năng của **dự án** theo cơ chế của công cụ AI đang dùng ([[Công cụ và hook của dự án]]).

## Phạm vi

| Yêu cầu | Diff được review |
|---|---|
| Mặc định | Phần đã stage; không có gì stage → thay đổi chưa commit, nói rõ |
| Cả nhánh | Từ điểm tách với nhánh chính (fetch trước) |
| Vài file | Đúng các file đó |

Ghi phạm vi + số file ở đầu báo cáo. Có chế độ "chỉ báo, không sửa".

## Phase 1 — máy kiểm, rồi sửa

1. Chạy song song: **lệnh kiểm tra của dự án** trên các file thay đổi ([[Công cụ kiểm tra tự động]]) và **bảng lỗi của editor** (mở từng file thay đổi nếu plugin chỉ phân tích file đang mở).
2. Quét **dòng được thêm**: chữ không phải tiếng Anh trong code, lệnh in debug, test bị đánh dấu chạy riêng, dấu xung đột merge, bí mật.
3. Sửa mọi lỗi và cảnh báo **trong phạm vi diff**:
   - Định dạng: chỉ format file thay đổi; lệnh format đổi nhiều dòng ngoài diff (file vốn chưa format) → không format cả file, hỏi.
   - Import / biến / export / file thừa: xoá, hoặc nối vào chỗ dùng.
   - Chữ người dùng thấy viết cứng → chuyển vào i18n / dictionary của dự án; comment → tiếng Anh.
   - Từ đúng mà công cụ chính tả báo sai → thêm vào từ điển của dự án.
   - Lỗi có sẵn từ trước (không do diff) → không sửa, ghi "có sẵn từ trước".
4. Chạy lại đến khi sạch; file đã stage mà vừa sửa → nhắc stage lại.

## Phase 2 — đọc diff như reviewer, rồi sửa

Đọc **từng hunk**, mở file đầy đủ khi cần ngữ cảnh (nơi gọi, nơi được gọi, schema, validate, test). Mỗi thay đổi hành vi: trước làm gì, giờ làm gì, luồng nào có thể hỏng.

Ưu tiên: bảo mật / lộ dữ liệu → sai chức năng, nghiệp vụ → hồi quy, tương thích → xử lý lỗi, trường hợp biên → hiệu năng → convention → thiếu test. Không báo lỗi định dạng mà máy đã lo; không coi sở thích là lỗi.

| Mặt | Kiểm |
|---|---|
| Logic | Điều kiện, giá trị biên, rỗng / null, thiếu chờ bất đồng bộ, lỗi không được bắt, listener không gỡ, tranh chấp đồng thời, so sánh lỏng / chặt |
| An toàn | Truy vấn dùng tham số (không nối chuỗi), escape đầu ra, chống giả mạo request ở form, validate ở server, kiểm quyền theo bản ghi |
| Mapping | Tên trường khớp form ↔ request ↔ validate ↔ DB / API ↔ phản hồi ↔ hiển thị; key i18n ↔ dictionary; route ↔ link; selector ↔ markup; cấu hình / biến môi trường ↔ nơi đọc |
| Xoá phải xoá hết | Mỗi thứ bị xoá (hàm, export, class, id, key, route, ảnh, file) → tìm toàn repo; còn tham chiếu là lỗi. Thứ chỉ phần bị xoá dùng → xoá theo |
| Thêm phải thêm đủ | Key i18n mới có ở mọi ngôn ngữ; style mới được dùng; file / asset mới được tham chiếu; migration / cấu hình mới được đăng ký |
| Quy tắc & convention | Theo quy tắc của dự án; code mới giống code xung quanh |
| Rác | Code bị comment, TODO không lý do, giá trị viết cứng (URL, khoá, cổng), comment mô tả hành vi cũ, dòng chỉ đổi định dạng ở file không liên quan |
| Phạm vi commit | Không lẫn file cá nhân, file môi trường, file build; lockfile đổi mà không nói về thư viện; nhiều chủ đề → đề xuất tách |

Mỗi phát hiện: vị trí — vấn đề — **kịch bản cụ thể** (đầu vào / điều kiện → kết quả sai) — hậu quả — cách sửa — mức (nghiêm trọng / cao / vừa / thấp) — độ chắc. **Xác minh trước khi báo** (chạy lệnh, test, tìm, đọc thêm); chưa đủ bằng chứng → "nghi vấn" kèm điều chưa kiểm được.

Sửa ngay mục phải sửa rõ ràng. Hỏi trước khi: đổi hành vi, xoá thứ có thể còn dùng ngoài repo, có nhiều cách sửa hợp lý. Sửa xong → chạy lại phase 1. Diff có logic phức tạp hoặc rủi ro cao (quyền, tiền, dữ liệu) → đề xuất thêm một lượt review sâu nếu công cụ AI có.

## Giao diện

Diff chạm giao diện → liệt kê trang + khu vực mình cần tự xem trên trình duyệt, ở từng ngôn ngữ và kích thước màn hình của dự án. Không tự khẳng định giao diện đúng khi chưa xem.

## Báo cáo

```
Phạm vi: <staged | nhánh so với base | file> — N file

Phase 1 (máy)             | Trước | Sau sửa
<từng bước kiểm tra>      | FAIL  | PASS
Bảng lỗi editor (N file)  | n lỗi | 0

Đã sửa: vị trí — vấn đề — đã làm gì
Phải sửa (cần mình quyết): [mức · độ chắc] vị trí — vấn đề — kịch bản — đề xuất
Nghi vấn: [mức · nghi vấn] vị trí — vấn đề — điều chưa kiểm được
Có sẵn từ trước: …
Đã chạy: <lệnh> → <kết quả thật> · Chưa chạy: <bước> — lý do
Cần tự kiểm trên trình duyệt: …
```

Không thấy lỗi → "không thấy lỗi cần sửa trong phạm vi đã review", kèm giới hạn; không khẳng định code không có bug. Chỉ ghi một bước là PASS khi nó thật sự chạy lần này.

Chỉ khi "phải sửa" trống và phase 1 sạch: đưa danh sách `git add` chính xác + commit message theo quy ước của dự án.

## Giới hạn

- Hook chạy được bỏ qua (`--no-verify`); AI review mỗi lần một khác — "không thấy lỗi" ≠ "không có lỗi".
- Kiểm trước push thường chạy trên thư mục làm việc, không đúng commit được push → phần làm dở thì cất đi trước.
