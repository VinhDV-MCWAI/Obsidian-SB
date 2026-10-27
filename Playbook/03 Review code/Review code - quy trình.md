---
chu_de: review-code-quy-trinh
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review code — quy trình

Bước 9 của [[Quy trình một việc]]. Dùng **chỉ ở thời điểm git**: trước commit, trước khi mở / cập nhật pull request, khi review pull request của người khác. Không dùng cho "kiểm tra trước khi báo xong" (đó là lệnh kiểm tra của dự án — [[Lệnh kiểm tra và thời điểm chạy]]).

Trước: [[Đối chiếu checklist và minh chứng]] · Sau: [[Git và quy trình]].

## Lựa chọn của tôi

- **Máy kiểm trước, người / AI đọc sau.** Lỗi máy bắt được (định dạng, lint, kiểu, test) phải sạch trước khi đọc diff; review không lặp lại việc của máy.
- Review theo **diff và rủi ro**, không đọc cả repo; mở thêm file khi cần ngữ cảnh để kết luận.
- Mỗi mặt review là **một file checklist riêng** (bảng dưới); chỉ mở file của mặt mà diff chạm tới.
- Chỉ đưa danh sách `git add` + commit message khi không còn mục "phải sửa"; AI được commit hay không theo [[Git và quy trình]] (mục AI và git).
- Review không chạy trong git hook ([[Lệnh kiểm tra và thời điểm chạy]], mục Hook).

## Khi nào chạy

| Thời điểm | Phạm vi | Mức |
|---|---|---|
| Trước commit (việc nhỏ / vừa) | Phần đã stage | Phase 1 + các mặt bắt buộc |
| Trước commit (việc lớn, chạm quyền / tiền / dữ liệu) | Phần đã stage | Phase 1 + mọi mặt diff chạm |
| Trước khi mở / cập nhật pull request | Cả nhánh so với nhánh đích (fetch trước) | Đầy đủ |
| Review pull request người khác | Diff của pull request | Đầy đủ, chỉ báo — không sửa |

Không có gì stage → lấy thay đổi chưa commit và nói rõ. Ghi phạm vi + số file ở đầu báo cáo.

## Các bước

1. **Nạp quy tắc dự án** liên quan tới phần thay đổi (quy ước code, kiến trúc, bảo mật, lệnh kiểm tra).
2. **Xác định diff chính xác**: file thêm, sửa, xoá, đổi tên, cấu hình, thư viện. Kiểm `git diff --check` (khoảng trắng, dấu xung đột).
3. **Phase 1 — máy**: chọn kiểm tra theo [[Review - Chọn kiểm tra theo rủi ro]]; dùng lại kết quả đã chạy cho đúng nội dung đó, chỉ chạy phần chưa chạy. Quét bằng extension của editor theo [[Công cụ và hook của dự án]]. Sửa lỗi **trong phạm vi diff**; lỗi có sẵn từ trước → ghi "có sẵn từ trước", không sửa.
4. **Quét dòng thêm**: chữ không phải tiếng Anh trong code, lệnh in debug, test bị đánh dấu chạy riêng, bí mật.
5. **Phase 2 — đọc diff**: từng hunk; mỗi thay đổi hành vi trả lời "trước làm gì, giờ làm gì, luồng nào có thể hỏng". Mở checklist của các mặt bị chạm, theo thứ tự ưu tiên:

| # | Mặt | File |
|---|---|---|
| 1 | Bảo mật, lộ dữ liệu | [[Review - Bảo mật]] |
| 2 | Đúng nghiệp vụ, trường hợp biên | [[Review - Đúng nghiệp vụ và trường hợp biên]] |
| 3 | Hợp đồng, mapping, tương thích | [[Review - Hợp đồng và tương thích]] |
| 4 | Dữ liệu, migration | [[Review - Dữ liệu và migration]] |
| 5 | Xử lý lỗi, log, vận hành | [[Review - Vận hành, log và lỗi]] |
| 6 | Thiết kế, quy ước, dễ đọc | [[Review - Thiết kế và quy ước]] |
| 7 | Xoá đủ, thêm đủ, rác, phạm vi commit | [[Review - Xoá, thêm và rác]] |
| 8 | Test | [[Review - Test]] |

6. **Xác minh** mỗi phát hiện (chạy lệnh, test, tìm, đọc thêm). Chưa đủ bằng chứng → "nghi vấn".
7. **Sửa** mục rõ ràng; **hỏi trước** khi đổi hành vi, xoá thứ có thể còn dùng ngoài repo, hoặc có nhiều cách sửa hợp lý. Sửa xong → Phase 1 lại; file đã stage vừa sửa → nhắc stage lại.
8. **Giao diện**: diff chạm giao diện → liệt kê trang + khu vực mình cần tự xem, ở từng ngôn ngữ và kích thước màn hình của dự án.
9. **Báo cáo** theo [[Review - Báo cáo phát hiện]].

## Checklist

- [ ] Phạm vi review ghi rõ ở đầu báo cáo.
- [ ] Phase 1 sạch trước Phase 2.
- [ ] Chỉ mở checklist của mặt diff chạm; mặt bỏ qua có lý do.
- [ ] Mỗi phát hiện có vị trí, kịch bản, hậu quả, mức, độ chắc.
- [ ] Không sửa ngoài diff; không viết lại code theo sở thích.
- [ ] Chỉ đưa `git add` + commit message khi "phải sửa" trống.

## Bẫy

- Hết gạch đỏ trong editor ≠ code đúng nghiệp vụ.
- AI review mỗi lần một khác — "không thấy lỗi" ≠ "không có lỗi".
- Review lỗi đã lọt ra sau merge → thêm dòng vào checklist / test tương ứng (rule chỉ thêm khi có lý do).
