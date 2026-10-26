---
chu_de: phat-hanh
ap_dung: [moi-du-an]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Phát hành và quay lui

## Lựa chọn của tôi

- Phiên bản theo SemVer `vMAJOR.MINOR.PATCH`; mỗi lần phát hành có **tag** + **ghi chú phát hành**.
- CI chạy đúng lệnh "kiểm tra tất cả" như máy local, trên **code đã commit**, trong môi trường dùng xong bỏ.
- Mỗi lần phát hành **biết trước cách quay lui** (image / tag trước đó, migration có đường lui hoặc kiểu mở rộng → thu hẹp).
- Thay đổi rủi ro đi sau **cờ tính năng** (bật dần, tắt nhanh).

## Trước khi phát hành

- [ ] CI xanh; kiểm tra "file sinh ra không cũ" qua ([[Hợp đồng API giữa các phần]]).
- [ ] Dựng **từ đầu** đúng image / cấu hình production và chạy luồng chính (không dùng môi trường đang chạy sẵn) — [[Vận hành và sự cố]].
- [ ] Cấu hình quan trọng được test **đúng cách production nạp nó** (cache cấu hình, image production, proxy).
- [ ] Migration: chạy thử trên bản sao dữ liệu; thời gian chạy; có khoá bảng lâu không.
- [ ] Biến môi trường mới đã có ở mọi môi trường; `.env.example` đã cập nhật.
- [ ] Debug tắt; log không ghi dữ liệu nhạy cảm.
- [ ] Sao lưu ngay trước khi phát hành (có thay đổi dữ liệu).
- [ ] Ghi chú phát hành: thêm gì, sửa gì, **phá vỡ gì**, cách nâng cấp, cách quay lui.
- [ ] Nghiệm thu: tiêu chí chấp nhận đã kiểm, người duyệt xác nhận.

## Trong và sau khi phát hành

- [ ] Theo dõi tỉ lệ lỗi, thời gian phản hồi, hàng đợi trong 15–30 phút đầu.
- [ ] Kiểm tay luồng chính trên production (đăng nhập, thao tác quan trọng nhất).
- [ ] Lỗi nghiêm trọng → **quay lui trước, điều tra sau**.

## Quay lui

| Thay đổi | Cách quay lui |
|---|---|
| Chỉ code | Triển khai lại phiên bản trước |
| Có migration thêm (cột, bảng) | Code cũ vẫn chạy được với cấu trúc mới → chỉ quay code |
| Có migration xoá / đổi | Không quay được dễ → phải theo mở rộng → chuyển → thu hẹp qua nhiều lần phát hành ([[Dữ liệu và DB]]) |
| Tính năng sau cờ | Tắt cờ |
| Dữ liệu đã hỏng | Khôi phục từ sao lưu — đã diễn tập trước |

## Sửa gấp (hotfix)

- [ ] Nhánh từ `main`, sửa nhỏ nhất có thể, test phần bị đụng, merge về cả `main` và nhánh tích hợp ([[Git và quy trình]]).
- [ ] Vẫn có ghi chú phát hành và báo cáo sự cố nếu người dùng bị ảnh hưởng.

## Bẫy

- Kiểm tra trên môi trường đang chạy sẵn → lỗi chỉ lộ ra khi dựng lại từ đầu.
- Đổi đường dẫn / cổng mà quên kiểm tra sức khoẻ, proxy, cấu hình hạ tầng.
- Phát hành nhiều thay đổi lớn một lần → lỗi không biết do cái nào.
