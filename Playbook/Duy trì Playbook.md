---
loai: duy-tri
cap_nhat: 2026-10-27
---
# Duy trì Playbook

Cách viết và giữ kho gọn. Quy trình đợt cập nhật: [[Ghi chú và cập nhật theo đợt]].

## Khung một file

```markdown
---
chu_de: <slug>
ap_dung: [back-end, front-end, moi-du-an, moi-viec, …]
muc_do: nen-tang | nen-co | tuy-du-an
cap_nhat: YYYY-MM-DD
---
# <Tên>

(1–2 dòng: dùng khi nào; file trong 02 thêm dòng "Trước / Sau" để nối luồng)

## Lựa chọn của tôi   ← quyết định, đọc là làm theo được
## Checklist          ← mỗi dòng kiểm được (có / không)
## Bẫy                ← sai lầm đã gặp
```

File checklist thuần (Validate, Review từng mặt) có thể chỉ có bảng kiểm tra + Bẫy.

## Quy tắc viết

- **Không phụ thuộc công nghệ.** Nói *cần đạt gì*, không nêu tên ngôn ngữ, framework, editor, công cụ AI, thư viện hay dịch vụ cụ thể; AI ở dự án tự chọn theo môi trường. Được dùng: chuẩn mở (HTTP, SQL, ISO 8601, SemVer, Conventional Commits), định dạng dữ liệu, thuật toán (bcrypt / argon2), lệnh git, sơ đồ dạng chữ.
- **Một quy tắc một nơi gốc.** Thấy viết lại ở file thứ hai → giữ ở file đúng chủ đề nhất, nơi khác chỉ một dòng liên kết. Bảng "nơi gốc" ở cuối file này.
- **Chỉ tài liệu.** Không đặt script, hook, cấu hình trong kho; mô tả khả năng cần có để AI tự tạo trong dự án ([[Công cụ và hook của dự án]]).
- Mỗi file **một chủ đề, ≤ ~150 dòng**; dài hơn → tách, thêm dòng vào [[Mục lục cho AI]] (không có trong mục lục = AI không biết).
- Mỗi dòng checklist một câu, kiểm được. Kiến thức nền dài đã có trong `Technical/` → liên kết, không chép.
- Không thông tin công ty / khách hàng / hệ thống / lỗ hổng.

## Nơi gốc của các quy tắc hay bị viết lại

| Quy tắc | Nơi gốc |
|---|---|
| Tham khảo, dự án thắng; thứ tự ưu tiên | [[Playbook]] |
| Phạm vi đọc của AI | [[Mục lục cho AI]] |
| `.me/` như `node_modules`, không commit; có rồi thì không khởi tạo lại | [[Khởi tạo dự án]] |
| Cách chọn giữa dự án và tri thức; bộ chủ đề tối thiểu | [[Bộ quy tắc và tài liệu tối thiểu]] |
| Hook riêng của dự án, quét bằng extension, kiểm tra lệch tài liệu | [[Công cụ và hook của dự án]] |
| Yêu cầu chung với hook, ba cấp lệnh, editor vs dòng lệnh vs CI, nợ cũ | [[Lệnh kiểm tra và thời điểm chạy]] |
| Cỡ việc, cổng duyệt, "Luôn hỏi trước", báo cáo khi xong | [[Quy trình một việc]] |
| Định nghĩa "xong", ký hiệu ✅ ➖ ⏭ ❌ | [[Đối chiếu checklist và minh chứng]] |
| Nhánh, bảo vệ nhánh, AI được làm gì với git | [[Git và quy trình]] |
| Ngôn ngữ trong code, comment, log, commit; chữ người dùng thấy | [[Quy ước viết code]] |
| Cô lập dữ liệu test | [[Chiến lược kiểm thử]] |
| Tiền tố `TMP ` / `E2E `, dọn dẹp | [[Môi trường phát triển và dọn dẹp]] |
| Sửa gấp production | [[Phát hành và quay lui]] |

## Dọn định kỳ (cùng đợt cập nhật)

- [ ] Dòng trùng giữa các file → giữ ở nơi gốc, nơi khác liên kết.
- [ ] Dòng nhiều dự án liền bỏ lúc khởi tạo, hoặc bị ghi chú `danh-gia` "không giúp gì" → hạ mức `muc_do` (ví dụ `tuy-du-an`) hoặc chuyển xuống Bẫy; **không xoá** chỉ vì ít dự án dùng — Playbook là bản chuẩn đầy đủ, dự án tự lọc lúc khởi tạo. Chỉ xoá khi sai hoặc lỗi thời.
- [ ] Dòng lỗi thời (công cụ / chuẩn đã đổi) → sửa, ghi ngày.
- [ ] File lâu không dự án nào dùng lúc khởi tạo → xem lại còn đúng không (giữ nếu còn đúng).
- [ ] Tên công cụ cụ thể lọt vào → viết lại thành nhóm công cụ.

## Đo hiệu quả

Playbook gồm hai phần, đều giữ: **kiến thức chuẩn** (có thể trùng điều AI đã biết — giữ để làm bản chuẩn thống nhất) và **kinh nghiệm thật** (Bẫy, lựa chọn đã trả giá). Thước đo của đợt cập nhật: **số dòng có nguồn `thực tế`** được thêm. Sau vài dự án mà gần bằng 0 → xem lại cách ghi đề xuất trong lúc làm, không phải lý do bỏ kiến thức chuẩn.

## Lịch sử và commit

Kho Obsidian là repo git → `git log --follow -p -- "Playbook/<thư mục>/<file>"` xem file đã đổi thế nào. Không giữ bản cũ song song.

```
docs(playbook): <tóm tắt thay đổi>

- <file>: <đổi gì, vì sao>
Dự án nên cập nhật: <có / không — đổi mẫu quy tắc, quy trình hay khả năng hook>
```

Ghi chú ngoài Playbook: `docs(technical): …`, `notes(<thư mục>): …`.
