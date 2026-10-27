---
chu_de: review-dung-nghiep-vu
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Đúng nghiệp vụ và trường hợp biên

Mặt số 2 trong [[Review code - quy trình]]. Mở cho mọi diff đổi hành vi (không chỉ đổi tài liệu / định dạng).

## Lựa chọn của tôi

- Đối chiếu với **tiêu chí chấp nhận** của việc (ticket, tài liệu tính năng), không với điều mình đoán.
- Mỗi thay đổi hành vi: ghi **trước làm gì → giờ làm gì → luồng nào có thể hỏng** (nơi gọi, nơi được gọi).
- Thiếu tiêu chí chấp nhận → hỏi, không tự đặt ra.

## Checklist

- [ ] Làm đúng và đủ tiêu chí chấp nhận; không làm thêm thứ không được yêu cầu.
- [ ] Điều kiện và giá trị biên: `<` / `<=`, đầu / cuối danh sách, 0, âm, tối đa.
- [ ] Rỗng / null / thiếu trường / danh sách rỗng được xử lý.
- [ ] Dữ liệu quá dài, trùng, sai định dạng.
- [ ] So sánh lỏng / chặt đúng ý (kiểu khác nhau, chuỗi số).
- [ ] Bất đồng bộ: có chờ kết quả, có bắt lỗi, không chạy hai lần.
- [ ] Đồng thời: hai request cùng lúc không làm sai dữ liệu (khoá, giao dịch, ràng buộc duy nhất).
- [ ] Listener / timer / kết nối được gỡ / đóng.
- [ ] Ngày giờ, múi giờ, tiền, làm tròn theo chuẩn của dự án ([[Validate - Ngày giờ]], [[Validate - Số và tiền]]).
- [ ] Mọi nơi gọi hàm / API bị đổi vẫn đúng với hành vi mới.

## Bẫy

- Sửa đúng ca chính nhưng hỏng ca phụ dùng chung hàm (tìm mọi nơi gọi).
- Trả về sớm bỏ qua bước dọn dẹp / ghi log / giải phóng khoá.
- Test chỉ có ca vui; trường hợp biên chưa được thử thật.
