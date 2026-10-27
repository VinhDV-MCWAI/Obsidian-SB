---
chu_de: phat-hanh
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Phát hành và quay lui

Bước 11 của [[Quy trình một việc]]: đưa thay đổi đã merge tới người dùng, luôn biết cách quay lui.

Trước: [[Git và quy trình]] · Sau: [[Vận hành và sự cố]].

## Lựa chọn của tôi

- Phiên bản SemVer `vMAJOR.MINOR.PATCH`; mỗi lần phát hành có **tag** + **ghi chú phát hành** (thêm gì, sửa gì, phá vỡ gì, cách nâng cấp, cách quay lui).
- CI chạy đúng lệnh "kiểm tra tất cả" như local, trên **code đã commit**, trong môi trường dùng xong bỏ.
- Mỗi lần phát hành **biết trước cách quay lui**.
- Thay đổi rủi ro đi sau **cờ tính năng** (bật dần, tắt nhanh). Không gộp nhiều thay đổi lớn vào một lần.

## Trước khi phát hành

- [ ] CI xanh; kiểm "file sinh ra không cũ" qua ([[Hợp đồng API giữa các phần]]).
- [ ] Dựng **từ đầu** đúng image / cấu hình production và chạy luồng chính — không kiểm trên môi trường đang chạy sẵn.
- [ ] Cấu hình quan trọng được test **đúng cách production nạp nó** (cache cấu hình, image production, proxy).
- [ ] Migration: chạy thử trên bản sao dữ liệu; đo thời gian; có khoá bảng lâu không.
- [ ] Biến môi trường mới có ở mọi môi trường; file mẫu môi trường đã cập nhật.
- [ ] Debug tắt; log không ghi dữ liệu nhạy cảm.
- [ ] Đổi đường dẫn / cổng → đã sửa kiểm tra sức khoẻ, proxy, cấu hình hạ tầng ([[Vận hành và sự cố]]).
- [ ] Sao lưu ngay trước khi phát hành (khi có thay đổi dữ liệu).
- [ ] Nghiệm thu: tiêu chí chấp nhận đã kiểm, người duyệt xác nhận.

## Trong và sau khi phát hành

- [ ] Theo dõi tỉ lệ lỗi, thời gian phản hồi, hàng đợi trong 15–30 phút đầu.
- [ ] Kiểm tay luồng chính trên production.
- [ ] Lỗi nghiêm trọng → **quay lui trước, điều tra sau**.

## Quay lui

| Thay đổi | Cách quay lui |
|---|---|
| Chỉ code | Triển khai lại phiên bản trước |
| Migration thêm (cột, bảng) | Code cũ vẫn chạy với cấu trúc mới → chỉ quay code |
| Migration xoá / đổi | Khó quay → phải theo mở rộng → chuyển → thu hẹp qua nhiều lần phát hành ([[Dữ liệu và DB]]) |
| Tính năng sau cờ | Tắt cờ |
| Dữ liệu đã hỏng | Khôi phục từ sao lưu — đã diễn tập trước |

## Sửa gấp production

- [ ] Phạm vi nhỏ nhất; nhánh `hotfix/…` tách từ nhánh production ([[Git và quy trình]]).
- [ ] Có cách quay lui **trước** khi sửa; test phần bị đụng.
- [ ] Merge về production, rồi đưa ngược về các nhánh dài hạn còn lại.
- [ ] Vẫn có ghi chú phát hành; người dùng bị ảnh hưởng → báo cáo sự cố ([[Vận hành và sự cố]]).

## Bẫy

- Kiểm trên môi trường đang chạy sẵn → lỗi chỉ lộ ra khi dựng lại từ đầu.
- Phát hành nhiều thay đổi lớn một lần → lỗi không biết do cái nào.
- Migration chạy nhanh trên dữ liệu thử, khoá bảng hàng phút trên dữ liệu thật.
