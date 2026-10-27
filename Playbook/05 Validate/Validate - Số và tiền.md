---
chu_de: validate-so
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — Số và tiền

Nền: [[Chương 12 — Biểu diễn số unsigned, signed, two’s complement, overflow và underflow]], [[Chương 13 — Floating Point, IEEE 754 và vì sao 0.1 + 0.2 != 0.3]].

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Kiểu: số nguyên hay thập phân; nhập `1.5` vào ô số nguyên → lỗi rõ | |
| 2 | Min / max nghiệp vụ **và** giới hạn kiểu DB (int 32-bit ≤ 2.147.483.647) | Số quá lớn không được làm 500 |
| 3 | Số âm, 0 có hợp lệ không | Số lượng, giá thường > 0 |
| 4 | Số chữ số thập phân tối đa | Tiền VND: 0; USD: 2 |
| 5 | Full-width `０１２３` → đổi hoặc báo lỗi rõ | |
| 6 | Dấu phân cách: `1,000.50` vs `1.000,50` theo vùng — server nhận một dạng chuẩn | FE định dạng hiển thị, gửi số thuần |
| 7 | `NaN`, `Infinity`, `1e308`, chuỗi rỗng, khoảng trắng | |
| 8 | Ký tự đặc biệt, emoji | |

## Tiền

- [ ] Lưu số nguyên theo đơn vị nhỏ nhất (xu) hoặc `DECIMAL(p,s)`; **không** `float`.
- [ ] Tính toán bằng kiểu / thư viện số thập phân chính xác của ngôn ngữ, không bằng số thực.
- [ ] Quy tắc làm tròn ghi rõ (làm tròn ở bước nào, kiểu làm tròn).
- [ ] Lưu kèm mã tiền tệ nếu có nhiều loại.
- [ ] Giá / tổng tiền **tính lại ở server**, không tin số front-end gửi.

## Bẫy

- Nhiều client / bộ đọc JSON dùng số thực 64-bit → mất chính xác trên 2^53. Id / số lớn gửi dạng chuỗi.
- `0.1 + 0.2 != 0.3` → so sánh tiền bằng số thực sai.
- Ô nhập số của trình duyệt vẫn cho gõ `e`, `-`, `+` → kiểm lại ở server.
