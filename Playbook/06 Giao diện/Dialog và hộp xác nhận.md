---
chu_de: dialog-xac-nhan
ap_dung: [front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Dialog và hộp xác nhận

## Lựa chọn của tôi

- **Một** thành phần dialog dùng chung cho cả ứng dụng. Tính năng chỉ truyền vào: tiêu đề, mô tả ngắn, nội dung, danh sách nút, cỡ.
- **Header cố định:** tiêu đề + mô tả ngắn + nút X. **Body cuộn riêng.** **Footer cố định** chứa nút hành động do từng tính năng quyết định.
- Bốn cỡ: nhỏ (xác nhận), vừa (form ngắn), lớn (form dài / xem trước), toàn màn hình (điện thoại hoặc nội dung rất lớn).
- Đóng khi có thay đổi chưa lưu → luôn hỏi lại. Thao tác không hoàn tác được → luôn có hộp xác nhận.
- Một thành phần hộp xác nhận dùng chung (tiêu đề câu hỏi, hệ quả, nút ghi rõ hành động).

## Cấu trúc dialog dùng chung

```
┌──────────────────────────────────────────┐
│ Tiêu đề                              [X] │  HEADER — cố định, không cuộn
│ Mô tả ngắn (tuỳ chọn, 1 dòng)            │
├──────────────────────────────────────────┤
│                                          │
│  Nội dung theo tính năng                 │  BODY — cuộn riêng khi dài
│                                          │
├──────────────────────────────────────────┤
│                    [Huỷ]  [Hành động chính] │  FOOTER — cố định, nút theo tính năng
└──────────────────────────────────────────┘
```

- [ ] Một component dialog chung; tính năng chỉ truyền tiêu đề, mô tả, nội dung, danh sách nút.
- [ ] Header: tiêu đề nói **việc gì** ("Sửa quản trị viên"), nút X luôn có (trừ dialog bắt buộc chọn).
- [ ] Body cuộn riêng; header và footer luôn thấy; chiều cao tối đa ~85% màn hình.
- [ ] Footer: nút phụ (Huỷ) trái, nút chính phải (hoặc theo hệ thống thiết kế của dự án — thống nhất một kiểu). Hành động nguy hiểm: nút màu đỏ.
- [ ] Nút chính khoá + hiện đang xử lý khi đang gửi; không đóng dialog khi đang gửi.
- [ ] Lỗi validate hiện trong body tại ô lỗi; lỗi hệ thống hiện ở đầu body hoặc thông báo, **không** đóng dialog, không mất dữ liệu đã nhập.
- [ ] Bàn phím: `Esc` đóng (qua kiểm thay đổi chưa lưu), `Tab` không thoát ra ngoài dialog, focus vào ô đầu tiên khi mở, trả focus về nút mở khi đóng.
- [ ] Điện thoại: dialog thành toàn màn hình hoặc kéo từ dưới lên.
- [ ] Không mở dialog chồng dialog (quá 2 lớp) — chuyển thành trang riêng.

## Khi nào cần hộp xác nhận

| Thao tác | Xác nhận? | Nội dung |
|---|---|---|
| Xoá vĩnh viễn, thao tác không hoàn tác được | **Bắt buộc** | Nói rõ mất gì; việc rất nguy hiểm → bắt gõ tên để xác nhận |
| Xoá vào thùng rác (khôi phục được) | Không bắt buộc — thay bằng thông báo có nút "Hoàn tác" | |
| Đóng dialog / rời trang khi **có thay đổi chưa lưu** | **Bắt buộc** | "Bỏ thay đổi?" — [Ở lại] [Bỏ thay đổi] |
| Thao tác hàng loạt | **Bắt buộc** | Nêu số lượng: "Xoá 23 bài viết?" |
| Ảnh hưởng người khác (khoá tài khoản, đổi quyền, gửi thư hàng loạt) | **Bắt buộc** | Nêu ai bị ảnh hưởng |
| Thanh toán, gửi đi bên ngoài | **Bắt buộc** | Tóm tắt trước khi gửi |
| Lưu bình thường, đổi bộ lọc, sắp xếp | Không | |

Hộp xác nhận:
- [ ] Tiêu đề là câu hỏi cụ thể ("Xoá danh mục 'Tin tức'?"), không "Bạn có chắc không?".
- [ ] Nút ghi rõ hành động ("Xoá", "Bỏ thay đổi"), không "OK" / "Có".
- [ ] Focus mặc định ở nút **an toàn** (Huỷ) với thao tác nguy hiểm.
- [ ] Nêu hệ quả: "Bài viết trong danh mục sẽ chuyển sang 'Chưa phân loại'".

## Kiểm "thay đổi chưa lưu"

- [ ] So dữ liệu hiện tại với dữ liệu lúc mở (trạng thái "dirty" của thư viện form), không chỉ "đã gõ phím".
- [ ] Áp dụng cho: nút X, `Esc`, bấm ra ngoài dialog, chuyển trang trong app, đóng tab (`beforeunload`).
- [ ] Lưu xong → hết trạng thái chưa lưu.
