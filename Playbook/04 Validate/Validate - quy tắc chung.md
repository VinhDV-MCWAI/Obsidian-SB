---
chu_de: validate-chung
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Validate — quy tắc chung (đọc cùng file theo loại trường)

## Nguyên tắc

1. **Back-end là cổng cuối.** Front-end kiểm để người dùng sửa nhanh; back-end kiểm lại **toàn bộ**, kể cả khi gọi bằng Postman / curl.
2. **Chuẩn hoá trước, kiểm sau**: bỏ khoảng trắng đầu/cuối, chuẩn hoá Unicode (NFC), đổi số full-width → half-width nếu nghiệp vụ cho phép, chữ thường với email / mã.
3. **Chỉ nhận trường cho phép**; trường lạ bỏ qua hoặc báo lỗi, không gán thẳng vào model.
4. **Mỗi lỗi một mã thông báo riêng**, không gộp ("không hợp lệ" chung chung).
5. **Giới hạn ở một nơi** ([[Hằng số, enum, thông báo]]); giới hạn của DB (độ dài cột) ≥ giới hạn validate.
6. Kiểm cần DB / mạng (trùng, tồn tại, MX) chạy **sau** kiểm rẻ (bắt buộc, cú pháp, độ dài).

## Phân công front-end / back-end

| Loại kiểm | FE | BE |
|---|---|---|
| Bắt buộc, kiểu, độ dài, định dạng, nằm trong danh sách | ✔ | ✔ |
| So sánh nhiều trường (nhập lại mật khẩu, từ ngày ≤ đến ngày) | ✔ | ✔ |
| Trùng / tồn tại trong DB | Tuỳ (gọi API kiểm trước, chỉ để tiện) | ✔ bắt buộc |
| Kiểm qua dịch vụ ngoài (MX, mã số thuế) | — | ✔ |
| Quyền với giá trị (id bản ghi có thuộc người này không) | — | ✔ |

## Checklist cho **mọi** trường

- [ ] Bắt buộc hay không? Chuỗi chỉ có khoảng trắng tính là rỗng.
- [ ] `null`, chuỗi rỗng, thiếu trường — ba trường hợp khác nhau, xử lý rõ.
- [ ] Kiểu đúng (chuỗi / số / mảng / boolean) — gửi sai kiểu (mảng thay chuỗi) không được làm sập server (500).
- [ ] Độ dài / giá trị tối thiểu, tối đa, **vượt tối đa** (biên: max, max+1).
- [ ] Ký tự: latin, có dấu, full-width (`アアア`, `０１２３`), half-width (`ｱｱｱ`), emoji (`😀`), ký tự điều khiển, xuống dòng (`\n`, `\r`), ký tự kết hợp.
- [ ] Chuỗi độc hại: `' OR 1=1 --`, `<script>alert(1)</script>` → không lỗi, không thực thi khi hiển thị.
- [ ] Copy / paste (kèm khoảng trắng, xuống dòng ẩn).
- [ ] Trường không có giới hạn: thử > 30.000–50.000 ký tự → phải có giới hạn ở đâu đó.
- [ ] Thông báo lỗi: đúng trường, tên hiển thị, nói cách sửa.

Nguồn: [[Testing]].

## Theo loại trường

[[Validate - Email]] · [[Validate - Mật khẩu]] · [[Validate - Chữ]] · [[Validate - Số và tiền]] · [[Validate - Ngày giờ]] · [[Validate - Lựa chọn, ID, danh sách]] · [[Validate - File và ảnh]] · [[Validate - Tham số danh sách]] · [[Validate - Điện thoại, URL, mã]]

Chưa có file (thêm khi gặp): mã số thuế / giấy tờ, địa chỉ.
