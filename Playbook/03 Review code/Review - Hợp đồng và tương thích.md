---
chu_de: review-hop-dong
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Hợp đồng và tương thích

Mặt số 3 trong [[Review code - quy trình]]. Mở khi diff đổi: API (route, tham số, dạng phản hồi), sự kiện / hàng đợi, cấu hình, biến môi trường, key đa ngôn ngữ, tên trường form / DB. Chuẩn: [[Hợp đồng API giữa các phần]], [[Giao dịch, lỗi và phản hồi API]].

## Lựa chọn của tôi

- Đổi thứ nơi khác đang gọi → tìm **mọi chỗ dùng trong cả repo**, sửa trong cùng thay đổi, ghi vào mô tả commit / pull request.
- File sinh ra từ đặc tả (kiểu dữ liệu, client API) được **sinh lại**, không sửa tay.
- Phá tương thích chỉ khi có quyết định; đánh dấu rõ trong commit.

## Checklist — mapping

- [ ] Tên trường khớp suốt chuỗi: form ↔ request ↔ validate ↔ DB / API ↔ phản hồi ↔ hiển thị.
- [ ] Key đa ngôn ngữ mới có trong từ điển của **mọi** ngôn ngữ.
- [ ] Route ↔ link / điều hướng; selector ↔ markup.
- [ ] Cấu hình / biến môi trường mới ↔ nơi đọc ↔ file mẫu môi trường ↔ tài liệu chạy local.

## Checklist — tương thích

- [ ] Đặc tả API và kiểu dữ liệu phía client đã sinh lại, không còn khác biệt.
- [ ] Client cũ (bản đã phát hành, ứng dụng khác) vẫn chạy: không xoá / đổi nghĩa trường đang dùng.
- [ ] Mã HTTP, khung phản hồi, mã lỗi theo chuẩn của dự án.
- [ ] Thay đổi phá tương thích có đánh dấu và hướng dẫn nâng cấp.

## Bẫy

- Đổi tên trường ở server, client vẫn đọc tên cũ → hiển thị rỗng, không báo lỗi.
- Thêm key đa ngôn ngữ ở một ngôn ngữ → ngôn ngữ kia hiện mã key.
- Thêm biến môi trường mà không thêm vào file mẫu → máy khác không chạy được.
