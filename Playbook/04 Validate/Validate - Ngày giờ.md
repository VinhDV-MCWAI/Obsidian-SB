---
chu_de: validate-ngay-gio
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Ngày giờ

Nền: [[Chương 17 — Unix time, timestamp, Y2K, năm 2038, leap year và leap second]].

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Định dạng gửi lên **một dạng chuẩn**: ISO 8601 (`2026-10-26`, `2026-10-26T08:30:00Z`) | FE hiển thị theo vùng (`26/10/2026`), gửi ISO |
| 2 | Ngày không tồn tại: `2025-02-30`, `2026-13-01`, 29/02 năm không nhuận | Dùng hàm parse chặt (strict) |
| 3 | Sai định dạng (dd/mm vs mm/dd) | |
| 4 | Quá xa quá khứ (`0001-01-01`) / tương lai (`10000-…`) → giới hạn hợp lý | Ngày sinh: không tương lai, không quá 150 năm |
| 5 | Khoảng ngày: bắt đầu ≤ kết thúc; độ dài khoảng tối đa (báo cáo không quá 1 năm) | |
| 6 | Ngày trong quá khứ / tương lai theo nghiệp vụ (hẹn lịch không được ở quá khứ) | So với giờ **server** |
| 7 | Múi giờ: lưu UTC, chuyển khi hiển thị; ngày thuần (không giờ) lưu kiểu `DATE`, không chuyển múi giờ | |
| 8 | "Hôm nay" theo múi giờ của ai (người dùng hay hệ thống) — ghi rõ | |
| 9 | Năm 2038 với timestamp 32-bit | Dùng kiểu 64-bit / kiểu thời gian có múi giờ của DB |
| 10 | Server đọc ngày theo **đúng một** định dạng đã thống nhất | Đọc nhầm ngày/tháng thành tháng/ngày: ngày > 12 báo lỗi 500, ngày ≤ 12 lọc sai lặng lẽ |

## Bẫy

- Ngày sinh lưu kiểu có giờ rồi chuyển múi giờ → lệch một ngày.
- Nhiều thư viện hiểu chuỗi ngày không có giờ là UTC, nhưng chuỗi định dạng khác là giờ địa phương → cùng một ngày ra hai thời điểm.
- Lọc ngày sai định dạng **không báo lỗi** khi ngày ≤ 12 — chỉ lộ ra khi gặp ngày 13 trở đi. Test với ngày > 12.
- Lọc "đến ngày 26" bằng `<= 2026-10-26 00:00` → mất dữ liệu cả ngày 26. Dùng `< 2026-10-27`.
- Giờ mùa hè (DST) ở thị trường nước ngoài: 1 giờ không tồn tại / lặp hai lần.
