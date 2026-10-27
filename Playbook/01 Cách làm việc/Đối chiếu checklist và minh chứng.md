---
chu_de: doi-chieu-minh-chung
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Đối chiếu checklist và minh chứng

Đây là bước bù chỗ AI hay sót. **Không được** viết chung chung "đã validate đầy đủ", "đã test kỹ".

## Cách đối chiếu

Với mỗi checklist đã mở trong việc này (bảng "Tham khảo tri thức cá nhân" của dự án), đi từng dòng và trả lời một trong bốn:

| Ký hiệu | Nghĩa | Phải kèm |
|---|---|---|
| ✅ | Đã làm | Ở đâu (file:dòng, tên test) |
| ➖ | Không áp dụng | Một câu vì sao |
| ⏭ | Bỏ qua có chủ đích | Lý do + ai đồng ý (mình, hoặc quy tắc dự án đã tắt mục này) |
| ❌ | Chưa làm / không làm được | Nói rõ, đề xuất việc tiếp |

Ví dụ ngắn:

```
Validate - Email
✅ 1 chuẩn hoá: bước chuẩn hoá của request tạo người dùng (bỏ khoảng trắng, chữ thường)
✅ 4 độ dài tối đa: hằng số EMAIL_MAX, test "email quá dài"
✅ 6 trùng: unique index trên lower(email) + luật kiểm trùng
⏭ 8 kiểm tra MX: quy tắc dự án tắt (môi trường nội bộ không có DNS ngoài)
➖ 9 email dùng một lần: chỉ quản trị viên tạo tài khoản
```

### Báo cáo gọn (tiết kiệm token)

AI **xét đủ mọi dòng**, nhưng trong câu trả lời chỉ **liệt kê**:
- mọi dòng ❌ và ⏭ (kèm lý do);
- dòng ✅ thuộc **bảo mật, quyền, dữ liệu, tiền** — kèm vị trí (đây là chỗ đánh dấu ✅ cho có là nguy hiểm nhất);
- còn lại gộp một dòng: "12 dòng ✅, 3 dòng ➖".

Mình muốn xem đủ → nói "đối chiếu đầy đủ".

**✅ phải có vị trí kiểm được** (file, tên test, lệnh). Không chỉ ra được ở đâu → không phải ✅.

## Minh chứng cần có

| Loại | Nội dung |
|---|---|
| Lệnh đã chạy | Đúng lệnh, kết quả thật (số test pass/fail, lỗi lint). Không có lệnh = không có minh chứng |
| Chạy thật | Với giao diện: đã mở trang, làm thao tác chính, ảnh chụp nếu cần |
| Phần chưa kiểm | Nói thẳng: "chưa chạy e2e vì…" |
| Đã dọn | Dữ liệu thử, file tạm, container tạm |

Mẫu đầy đủ: [[Mẫu - Minh chứng]].

## Định nghĩa "xong"

- [ ] Đạt điều yêu cầu, đã cho thấy chạy thật.
- [ ] Có test đúng mức; bỏ thay đổi đi thì test phải fail.
- [ ] Công cụ kiểm tra của dự án đều qua (lint, kiểu, test).
- [ ] Đổi API → đã cập nhật nơi dùng và tài liệu API.
- [ ] Migration quay lui được hoặc có kế hoạch.
- [ ] Tài liệu liên quan đã sửa.
- [ ] Đã dọn dẹp.
- [ ] Có điều học được → đã ghi một dòng đề xuất tri thức.
