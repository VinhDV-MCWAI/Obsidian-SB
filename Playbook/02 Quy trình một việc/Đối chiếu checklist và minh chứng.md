---
chu_de: doi-chieu-minh-chung
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Đối chiếu checklist và minh chứng

Bước 8 của [[Quy trình một việc]]: bù chỗ AI hay sót. **Không được** viết chung chung "đã validate đầy đủ", "đã test kỹ".

Trước: [[Lệnh kiểm tra và thời điểm chạy]] · Sau: [[Review code - quy trình]] (khi commit).

## Cách đối chiếu

Mỗi checklist đã mở trong việc này (quy tắc theo chủ đề của dự án mà việc chạm tới), đi từng dòng, trả lời một trong bốn:

| Ký hiệu | Nghĩa | Phải kèm |
|---|---|---|
| ✅ | Đã làm | Ở đâu (file:dòng, tên test, lệnh) — không chỉ ra được → không phải ✅ |
| ➖ | Không áp dụng | Một câu vì sao |
| ⏭ | Bỏ qua có chủ đích | Lý do + ai đồng ý (mình, hoặc quy tắc dự án đã tắt) |
| ❌ | Chưa làm / không làm được | Nói rõ, đề xuất việc tiếp |

```
Validate - Email
✅ 1 chuẩn hoá: bước chuẩn hoá của request tạo người dùng
✅ 6 trùng: unique index trên lower(email) + luật kiểm trùng
⏭ 8 kiểm tra MX: quy tắc dự án tắt (môi trường nội bộ không có DNS ngoài)
➖ 9 email dùng một lần: chỉ quản trị viên tạo tài khoản
```

**Báo cáo gọn:** xét đủ mọi dòng, nhưng trong câu trả lời chỉ liệt kê mọi ❌ và ⏭ (kèm lý do), các ✅ thuộc **bảo mật, quyền, dữ liệu, tiền** (kèm vị trí); còn lại gộp một dòng "12 ✅, 3 ➖". Mình nói "đối chiếu đầy đủ" thì liệt kê hết.

## Minh chứng

| Loại | Nội dung |
|---|---|
| Lệnh đã chạy | Đúng lệnh, kết quả thật (số pass / fail, lỗi lint). Không có lệnh = không có minh chứng |
| Chạy thật | Giao diện: đã mở trang, làm thao tác chính, ảnh chụp nếu cần |
| Phần chưa kiểm | Nói thẳng: "chưa chạy đầu cuối vì…" |
| Đã dọn | Dữ liệu thử, file tạm, container tạm |

Mẫu đầy đủ: [[Mẫu - Minh chứng]].

## Định nghĩa "xong"

- [ ] Đạt mọi tiêu chí chấp nhận, đã cho thấy chạy thật.
- [ ] Có test đúng mức; bỏ thay đổi đi thì test fail.
- [ ] Lệnh kiểm tra của dự án đều qua (định dạng, lint, kiểu, test) trong phiên này.
- [ ] Checklist đã mở được đối chiếu; không còn ❌ chưa được mình chấp nhận.
- [ ] Đổi API → đã cập nhật nơi dùng và đặc tả; đổi DB → migration quay lui được hoặc có kế hoạch.
- [ ] Tài liệu liên quan đã sửa ([[Tài liệu dự án]]).
- [ ] Đã dọn ([[Môi trường phát triển và dọn dẹp]]).
- [ ] Có điều học được → đã ghi một dòng đề xuất tri thức.
