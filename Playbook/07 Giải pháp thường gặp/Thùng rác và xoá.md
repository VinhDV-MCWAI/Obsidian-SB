---
chu_de: thung-rac
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Thùng rác và xoá

## Lựa chọn của tôi

| Mức | Khi | Khôi phục |
|---|---|---|
| Xoá mềm (vào thùng rác) | Mặc định cho dữ liệu người dùng tạo | Được, trong thời hạn giữ (ví dụ 30 ngày) |
| Xoá vĩnh viễn | Người dùng chọn trong thùng rác, hoặc hết hạn giữ | Không |

- Xoá vào thùng rác: hỏi lại hoặc thông báo có nút **Hoàn tác** ([[Dialog và hộp xác nhận]]).
- Xoá vĩnh viễn / dọn sạch thùng rác: **luôn** hỏi lại.
- Mọi khôi phục / xoá vĩnh viễn ghi [[Nhật ký thay đổi]].

## Checklist

- [ ] Thùng rác hiện: tên, vị trí cũ, ngày xoá, ai xoá, **còn mấy ngày**; lọc theo tên.
- [ ] Khôi phục về đúng chỗ cũ; chỗ cũ đã bị xoá → tạo lại (cả các cấp cha).
- [ ] Tên đã có thứ khác dùng → đặt `tên (restored)`, `tên (restored 2)`, …
- [ ] Bản ghi đã xoá mềm **không** hiện ở danh sách, tìm kiếm, trang công khai, thống kê.
- [ ] Unique tính tới xoá mềm: tên của mục đã xoá dùng lại được ([[Dữ liệu và DB]]).
- [ ] Việc dọn định kỳ xoá hẳn mục quá hạn — xoá cả phần đi kèm (ảnh nhỏ, file trong kho).
- [ ] Khôi phục mục mà dữ liệu thật đã mất → 422 với câu rõ ("không còn giữ, không khôi phục được"); quá hạn → 404.
- [ ] Xoá thư mục / bản ghi cha: rõ hành vi với con (xoá kèm, chặn, chuyển).

## Thùng rác dựa vào lưu phiên bản của kho file

Kho file bật lưu phiên bản: xoá chỉ đặt "dấu đã xoá", khôi phục là gỡ dấu, xoá vĩnh viễn là xoá mọi phiên bản.

- [ ] Có luật vòng đời xoá phiên bản cũ sau thời hạn giữ — thiếu luật này thì file đã xoá **không bao giờ** giải phóng dung lượng.
- [ ] Phần đi kèm (ảnh nhỏ) không đặt dấu xoá, để thùng rác vẫn hiển thị được.
- [ ] **Sao lưu phải gồm cả phiên bản cũ.** Công cụ đồng bộ thường chỉ chép phiên bản mới nhất → sau khi khôi phục từ sao lưu, thùng rác vẫn hiện tên file nhưng bấm khôi phục sẽ lỗi.

## Bẫy

- Lấy "ngày xoá" từ cột "cập nhật lúc" — đúng chỉ khi dòng đã xoá không bao giờ bị sửa nữa. Nếu có thể bị sửa → thêm cột ngày xoá.
- Truy vấn quên lọc bản ghi đã xoá ở một chỗ (tìm kiếm, xuất file) → dữ liệu "đã xoá" vẫn lộ ra.
