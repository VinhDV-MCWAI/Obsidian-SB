---
loai: duy-tri
cap_nhat: 2026-10-26
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

- **Không phụ thuộc công nghệ.** Nói *cần đạt gì* và *lựa chọn của tôi*, không chép code, không nói riêng một framework. Định dạng dữ liệu (ví dụ khung JSON trả về), sơ đồ bố cục, bảng quyết định — được. Tên công cụ chỉ xuất hiện như "ví dụ".
- Mỗi file **một chủ đề, ≤ ~150 dòng**. Dài hơn → tách, thêm dòng vào [[Mục lục cho AI]].
- Mỗi dòng checklist một câu, kiểm được.
- Kiến thức nền dài đã có trong `Technical/` → liên kết, không chép.
- Không thông tin công ty / khách hàng / hệ thống / lỗ hổng.
- Thêm file mới → thêm dòng vào mục lục (không có trong mục lục = AI không biết).

## Đợt cập nhật

Theo [[Ghi chú và cập nhật theo đợt]]: ghi chú → gom theo file → so với file → (chỉ khi cần) tra nguồn chính thức → sửa → xoá ghi chú đã xử lý → mình xem diff, commit, push.

## Dọn định kỳ (cùng đợt cập nhật)

- [ ] Dòng checklist trùng giữa các file → giữ một nơi, nơi khác liên kết.
- [ ] Dòng nhiều dự án liền đánh ⏭ / ➖ → hạ mức "tuỳ dự án" hoặc xoá.
- [ ] Dòng lỗi thời (công cụ / chuẩn đã đổi) → sửa, ghi ngày.
- [ ] File AI không bao giờ mở → xem lại mô tả "khi nào" trong mục lục.

## Lịch sử

Kho Obsidian là repo git → `git log -p -- Playbook/<file>` xem file đã đổi thế nào. Không giữ bản cũ song song.
