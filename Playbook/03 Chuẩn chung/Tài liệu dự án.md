---
chu_de: tai-lieu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Tài liệu dự án

Áp dụng cho tài liệu **của nhóm** (lớp 3, trong repo). Tài liệu riêng của mình: [[Thư mục tài liệu riêng của dự án]].

## Lựa chọn của tôi

- Mỗi tài liệu trả lời một câu hỏi; tài liệu chia theo cụm và liên kết qua lại.
- Một tính năng một file, có liên kết tới yêu cầu, quyết định, API, test.
- Quyết định lớn ghi thành một bản ghi quyết định riêng (không sửa, chỉ thay thế).
- Khi code đổi, đối chiếu danh sách file đã sửa với bảng "khi code đổi" bên dưới trước khi báo xong.
- Viết ngôn ngữ đơn giản, tên đầy đủ, bảng thay đoạn văn dài.

## Cấu trúc gợi ý (chia cụm, liên kết qua lại)

```
docs/
├── README.md            bản đồ tài liệu: tài liệu nào trả lời câu hỏi gì
├── requirements/        yêu cầu, tiêu chí chấp nhận
├── architecture/        tổng quan, sơ đồ, luồng chính
├── adr/                 quyết định kiến trúc (một quyết định một file, không sửa, chỉ thay thế)
├── features/            một tính năng một file: làm gì, API, màn hình, quyền, giới hạn
├── api/                 đặc tả API (sinh từ code nếu được)
├── runbooks/            vận hành: sao lưu, sự cố, dọn dẹp
├── incidents/           báo cáo sự cố
└── dev-guide.md         cài, chạy, test, sửa lỗi thường gặp
```

- [ ] Mỗi tài liệu trả lời **một** câu hỏi; trùng nội dung → giữ một nơi, nơi khác liên kết tới.
- [ ] Tài liệu tính năng liên kết tới: yêu cầu, ADR, API, test, runbook liên quan → kiểm tra ảnh hưởng nhanh.
- [ ] Ghi ngày cập nhật. Tài liệu cũ không còn đúng → ghi "lỗi thời, code thắng" hoặc chuyển vào lưu trữ.

Mẫu: [[Detail Design]] (thiết kế chi tiết màn hình / API).

## Khi code đổi, tài liệu nào phải đổi

| Thay đổi | Cập nhật |
|---|---|
| Thêm / đổi API | Đặc tả API (sinh lại), tài liệu tính năng |
| Đổi hành vi người dùng thấy | Tài liệu tính năng, ghi chú phát hành |
| Đổi cấu trúc DB | Sơ đồ DB, tài liệu tính năng |
| Đổi biến môi trường, cổng, lệnh chạy | `dev-guide`, `.env.example`, runbook |
| Quyết định thiết kế lớn | ADR mới |
| Xoá / đổi tên tính năng | Mọi chỗ nhắc tới (tìm theo tên) |
| Sự cố | Báo cáo sự cố + runbook nếu có cách xử lý mới |

Cách kiểm: trước khi báo xong, xem danh sách file đã sửa (`git diff --stat`) → đối chiếu bảng trên → tìm tên tính năng / route / biến trong `docs/`.

## Viết

- [ ] Ngôn ngữ đơn giản; tên đầy đủ thay vì mã / viết tắt; bảng thay cho đoạn văn dài.
- [ ] Bắt đầu bằng câu trả lời, chi tiết sau.
- [ ] Lệnh chạy được nguyên văn.
- [ ] Không chứa bí mật, dữ liệu thật của khách hàng.
