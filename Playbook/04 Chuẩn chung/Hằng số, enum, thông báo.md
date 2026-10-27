---
chu_de: hang-so-enum-thong-bao
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Hằng số, enum, thông báo — chống viết thẳng giá trị

## Lựa chọn của tôi

- Giá trị có ý nghĩa nghiệp vụ có **một nơi định nghĩa duy nhất**: enum (tập cố định), hằng số theo cụm tính năng (giới hạn), cấu hình (khác theo môi trường), khoá thông báo (câu chữ).
- Server trả **mã + tham số**; câu chữ nằm trong file ngôn ngữ.
- Client lấy enum / giới hạn từ đặc tả API hoặc sinh tự động; buộc phải chép tay thì ghi nguồn bên cạnh và có test so sánh ([[Hợp đồng API giữa các phần]]).

## Quy tắc

- [ ] Không viết thẳng số / chuỗi có ý nghĩa nghiệp vụ trong logic (`'max:30'`, `status == 2`, `'admin'`). Dùng enum, hằng số, config, khoá thông báo.
- [ ] **Một giá trị một nơi định nghĩa.** Hai hằng số cùng ý nghĩa khác giá trị (ví dụ `MAX_EMAIL = 254` và `EMAIL_MAX = 30`) là lỗi.
- [ ] Giá trị thay đổi theo môi trường → config / biến môi trường, không phải hằng số.
- [ ] Hằng số đặt theo phạm vi dùng, không gom hết vào một file chung của cả dự án.

## Chọn chỗ đặt

| Thứ | Đặt ở | Ví dụ |
|---|---|---|
| Tập giá trị cố định | Enum | Trạng thái đơn hàng, giới tính, vai trò hệ thống |
| Giới hạn nghiệp vụ | Hằng số theo cụm | `USER_NAME_MAX = 50` trong nhóm hằng số của cụm người dùng |
| Khác nhau theo môi trường | Config + biến môi trường | URL dịch vụ, bật/tắt kiểm tra MX |
| Câu hiển thị | Khoá thông báo + file ngôn ngữ | `validation.required`, `user.email_taken` |
| Bí mật | Kho bí mật / biến môi trường | **Không bao giờ** trong code |

## Enum

- [ ] Lưu DB bằng giá trị ổn định (số hoặc chuỗi ngắn), **không** bằng nhãn hiển thị.
- [ ] Nhãn hiển thị lấy qua khoá ngôn ngữ (`enum.order_status.paid`).
- [ ] Thêm giá trị mới: kiểm mọi `switch`/`match` (nên có nhánh mặc định báo lỗi), giao diện, dữ liệu cũ.
- [ ] Không xoá / đổi số của giá trị đã có dữ liệu.

## Thông báo và đa ngôn ngữ

- [ ] Server trả **mã + tham số**, không chỉ câu chữ: `{ code: "validation.max", params: { max: 50 } }`.
- [ ] Câu chữ nằm trong file ngôn ngữ; một mã một câu.
- [ ] Câu có tên trường: dùng tên hiển thị (`Email`), không tên cột (`email_address`).
- [ ] Không ghép chuỗi để tạo câu (khó dịch) — dùng tham số.
- [ ] Viết thông báo: nói **chuyện gì** + **làm gì tiếp** ("Email đã được dùng. Hãy đăng nhập hoặc dùng email khác."). Không đổ lỗi người dùng, không dùng thuật ngữ kỹ thuật.

## Bẫy

- Hai hằng số cùng ý nghĩa khác giá trị (giới hạn email 254 ở một nơi, 30 ở nơi khác) → form cho qua, server từ chối.
- Giá trị viết thẳng trong luật validate (`max:30`) trong khi đã có hằng số cùng tên.
- Enum phía client ghi chú "tự đồng bộ" nhưng thực ra chép tay → thêm giá trị ở server, giao diện hiện trống.

## Công cụ bắt viết thẳng giá trị

Luật "số ma thuật" của linter (cấu hình nhẹ), phân tích tĩnh với luật tự viết, review ([[Công cụ kiểm tra tự động]]). Lưu ý: khai báo hằng chỉ chặn gán lại biến, không làm đối tượng bên trong bất biến.
