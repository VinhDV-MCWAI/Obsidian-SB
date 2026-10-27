---
chu_de: review-xoa-them-rac
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Xoá đủ, thêm đủ, rác và phạm vi commit

Mặt số 7 trong [[Review code - quy trình]]. Mở cho **mọi** diff trước commit.

## Lựa chọn của tôi

- **Xoá phải xoá hết**: mỗi thứ bị xoá được tìm trong cả repo; còn tham chiếu là lỗi.
- **Thêm phải thêm đủ**: mỗi thứ mới phải được dùng / đăng ký / có ở mọi nơi cần.
- Một commit một mục đích; không lẫn file cá nhân, file môi trường, file build.

## Checklist — xoá

- [ ] Hàm, export, class, id, key, route, ảnh, file bị xoá → không còn tham chiếu trong code, test, tài liệu, cấu hình.
- [ ] Thứ chỉ phần bị xoá dùng (helper, style, key, thư viện) → xoá theo.
- [ ] Tài liệu nhắc tới thứ bị xoá / đổi tên đã sửa.

## Checklist — thêm

- [ ] Key đa ngôn ngữ mới có ở mọi ngôn ngữ.
- [ ] Style / asset / file mới được tham chiếu.
- [ ] Migration, cấu hình, route, quyền mới được đăng ký đúng chỗ.
- [ ] Tài liệu tính năng cập nhật theo bảng "khi code đổi" của [[Tài liệu dự án]].

## Checklist — rác

- [ ] Không có code bị comment, file `.bak`, file tạm.
- [ ] Không có TODO không lý do / không mã việc.
- [ ] Không có import / biến / export thừa.
- [ ] Không có dòng chỉ đổi định dạng ở file không liên quan.
- [ ] Không có test bị đánh dấu chạy riêng / bỏ qua.

## Checklist — phạm vi commit

- [ ] Không có file cá nhân, bí mật, file môi trường, dump DB, file build.
- [ ] Lockfile đổi → có thay đổi thư viện tương ứng và có lý do.
- [ ] Nhiều chủ đề trong một diff → đề xuất tách commit.

## Bẫy

- Xoá một component nhưng còn key đa ngôn ngữ và style của nó.
- Thêm file mới mà quên stage → commit build được ở máy mình, hỏng ở máy khác.
