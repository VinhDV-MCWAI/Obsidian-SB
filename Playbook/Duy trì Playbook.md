---
loai: duy-tri
cap_nhat: 2026-10-27
---
# Duy trì Playbook

## Khung một file chuẩn

```markdown
---
chu_de: <slug>
ap_dung: [back-end, front-end, moi-du-an, …]
muc_do: nen-tang | nen-co | tuy-du-an
cap_nhat: YYYY-MM-DD
---
# <Tên>

(1–2 dòng: dùng khi nào; liên kết kiến thức nền trong kho)

## Lựa chọn của tôi   ← quyết định đầy đủ, đọc là làm theo được
## Checklist          ← mỗi dòng kiểm được (có / không)
## Bẫy                ← sai lầm đã gặp
## Vì sao (tuỳ chọn)  ← giải thích dài, nguồn
```

## Quy tắc viết

- **Không phụ thuộc công nghệ.** Nói *cần đạt gì* và *lựa chọn của tôi*; không chép code, không nêu tên ngôn ngữ, framework, editor, công cụ AI hay thư viện — AI ở dự án tự chọn theo môi trường. Định dạng dữ liệu (ví dụ khung JSON trả về), chuẩn mở (HTTP, đặc tả API), sơ đồ bố cục, bảng quyết định — được.
- **Chỉ tài liệu.** Không đặt script, hook, cấu hình trong kho; mô tả khả năng cần có để AI tự tạo trong dự án ([[Công cụ và hook của dự án]]). Kiến thức sâu về một công nghệ → ghi chú trong `Technical/`, liên kết tới.
- Mỗi file **một chủ đề, ≤ ~150 dòng**. Dài hơn → tách, thêm dòng vào [[Mục lục cho AI]].
- Mỗi dòng checklist một câu, kiểm được.
- Kiến thức nền dài đã có trong `Technical/` → liên kết, không chép.
- Không thông tin công ty / khách hàng / hệ thống / lỗ hổng.
- Thêm file mới → thêm dòng vào mục lục (không có trong mục lục = AI không biết).

## Đợt cập nhật

Theo [[Ghi chú và cập nhật theo đợt]]: khối Đề xuất tri thức trong `.me/` của các dự án → gom theo file → so với file → (chỉ khi cần) tra nguồn chính thức → sửa → xoá đề xuất đã xử lý → mình xem diff, commit, push.

## Dọn định kỳ (cùng đợt cập nhật)

- [ ] Dòng checklist trùng giữa các file → giữ một nơi, nơi khác liên kết.
- [ ] Dòng nhiều dự án liền đánh ⏭ / ➖, hoặc bị ghi chú `danh-gia` "không giúp gì" → hạ mức "tuỳ dự án" hoặc xoá.
- [ ] Dòng lỗi thời (công cụ / chuẩn đã đổi) → sửa, ghi ngày.
- [ ] File không dự án nào đưa vào bảng "Tham khảo tri thức cá nhân" → xem lại có còn cần không.
- [ ] Đổi [[Mẫu - Quy tắc dự án]] hoặc [[Công cụ và hook của dự án]] → ghi trong commit để biết dự án nào cần cập nhật.

## Đo hiệu quả

Thước đo chính: **số dòng Bẫy / checklist có nguồn `thực tế`** vào Playbook sau mỗi dự án (đợt cập nhật báo số này). Sau vài dự án mà gần bằng 0 → Playbook chưa thành kinh nghiệm của mình, chỉ là kiến thức chung — xem lại cách ghi đề xuất.

## Lịch sử và commit

Kho Obsidian là repo git → `git log -p -- Playbook/<file>` xem file đã đổi thế nào. Không giữ bản cũ song song. Vì lịch sử nằm ở git nên commit message phải đọc được:

```
docs(playbook): <tóm tắt thay đổi>

- <file>: <đổi gì, vì sao>
Dự án nên cập nhật: <có / không — đổi mẫu quy tắc hay khả năng hook>
```

Ghi chú ngoài Playbook: `docs(technical): …`, `notes(<thư mục>): …`.
