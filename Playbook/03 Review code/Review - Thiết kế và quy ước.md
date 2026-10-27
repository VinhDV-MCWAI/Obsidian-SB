---
chu_de: review-thiet-ke
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Thiết kế, quy ước và dễ đọc

Mặt số 6 trong [[Review code - quy trình]]. Mở cho diff thêm / sửa code (không cần cho diff chỉ đổi tài liệu). Chuẩn: [[Thiết kế code và chia lớp]], [[Quy ước viết code]], [[Hằng số, enum, thông báo]].

## Lựa chọn của tôi

- Chỉ báo khi **vi phạm quy tắc có ghi** của dự án hoặc tạo **rủi ro thật**; không báo theo sở thích.
- Code mới phải **giống code xung quanh** (đặt tên, cấu trúc, import, mật độ comment).
- Quy tắc va nhau → thứ tự ưu tiên ở [[Bộ quy tắc và tài liệu tối thiểu]].

## Checklist

- [ ] Code nằm đúng lớp (validate ở biên, nghiệp vụ ở lớp nghiệp vụ, truy vấn ở lớp dữ liệu, xử lý request mỏng).
- [ ] Không lặp code đã có sẵn ở phần dùng chung; không tạo lớp chung khi mới một chỗ dùng.
- [ ] Gộp code trùng chỉ khi **cùng ý nghĩa**, không chỉ giống công thức.
- [ ] Có cách đơn giản hơn mà vẫn đủ yêu cầu → đề xuất.
- [ ] Tên nói làm gì / là gì; một khái niệm một từ trong cả dự án.
- [ ] Không có giá trị "ma thuật": dùng hằng số / enum / cấu hình / key thông báo.
- [ ] Comment nói **vì sao**; không có comment mô tả hành vi cũ.
- [ ] Chữ người dùng thấy qua đa ngôn ngữ; comment, tên, log bằng tiếng Anh (hoặc theo quy ước dự án).
- [ ] Refactor không lẫn đổi hành vi; sửa lỗi thấy được tách commit.

Bẫy: xem Bẫy của [[Thiết kế code và chia lớp]] và [[Quy ước viết code]].
