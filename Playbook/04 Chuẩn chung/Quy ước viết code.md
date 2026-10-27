---
chu_de: quy-uoc-viet-code
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Quy ước viết code

Cách viết từng file: đặt tên, thứ tự, hàm, lỗi, comment, cách viết một quy tắc. Kiến trúc và chỗ đặt code dùng chung: [[Thiết kế code và chia lớp]]. Dự án đã có quy ước → theo dự án; thiếu chủ đề nào ở bảng cuối → tạo lúc [[Khởi tạo dự án]].

## Lựa chọn của tôi

- Định dạng để máy lo; tài liệu chỉ ghi điều máy không kiểm được.
- Code mới **giống code xung quanh**: đặt tên, cấu trúc, import, mật độ comment.
- Quy tắc viết bằng từ khoá mức độ: **PHẢI / KHÔNG ĐƯỢC** (bắt buộc), **NÊN** (mặc định, ngoại lệ có giải thích), **CÓ THỂ** (tuỳ chọn).
- Bắt đầu với ít quy tắc ảnh hưởng tới đúng, bảo mật, bảo trì; thêm khi thực tế cần.
- Đơn giản hơn khéo léo.

## Tối thiểu mọi phiên (chép rút gọn vào quy tắc dự án)

- Chỉ sửa điều được yêu cầu; refactor hoặc lỗi thấy được → báo, tách commit.
- Trước khi sửa / xoá: tìm mọi chỗ đang dùng (hàm, route, key, lớp, biến môi trường). Xoá phải xoá hết, kể cả thứ chỉ phần bị xoá dùng; file mới phải được tham chiếu.
- Không để lại: import / biến / style / key thừa, code bị comment, lệnh in debug, test bị đánh dấu chạy riêng, TODO không mã việc, file `.bak`.
- Không nuốt lỗi; không bắt lỗi rộng cho "chạy được"; kiểm quyền ở server.
- Không viết thẳng giá trị có ý nghĩa nghiệp vụ ([[Hằng số, enum, thông báo]]).
- Trường hợp biên: rỗng, quá dài, trùng, đồng thời, quyền theo từng bản ghi.
- Kiểm lại hàm / file còn tồn tại trước khi dựa vào trí nhớ hay phiên trước.

## Đặt tên

- [ ] Tên nói **làm gì / là gì**; hàm là động từ (`calculateTotal`), boolean là câu hỏi (`isActive`, `hasPermission`).
- [ ] Không viết tắt khó hiểu; một khái niệm một từ trong cả dự án.
- [ ] Theo quy ước của ngôn ngữ (camelCase, snake_case, PascalCase).
- [ ] Route REST: danh từ số nhiều (`GET /users`, `GET /users/{id}`, `POST /users`, `PATCH /users/{id}`, `DELETE /users/{id}`); thao tác đặc biệt `POST /users/{id}/activate`.

## Thứ tự trong một file (mặc định)

1. Import: thư viện ngoài → alias nội bộ → tương đối.
2. Hằng số / cấu hình ở phạm vi nhỏ nhất phù hợp.
3. Kiểu, schema — gần nơi dùng; ra module riêng khi là hợp đồng dùng chung.
4. Phần xuất ra (hàm / lớp / thành phần chính).
5. Hàm phụ riêng — gần nơi dùng.

Chuẩn hoá **theo loại file** (module, thành phần, xử lý request, test); theo quy ước sẵn có của framework.

## Hàm và lỗi

- [ ] Hàm nhỏ, một việc; trả về sớm thay vì `if` lồng nhau.
- [ ] Kiểm đầu vào ở biên (request, việc nền, gọi ngoài); bên trong tin dữ liệu đã kiểm.
- [ ] Bắt lỗi thì phải xử lý thật, ghi log, hoặc ném lại ([[Giao dịch, lỗi và phản hồi API]]).
- [ ] Hằng số chỉ đặt cho giá trị có ý nghĩa nghiệp vụ, dùng lặp lại, hoặc cần đổi ở một nơi; không cho phép tính hiển nhiên dùng một lần.

## Comment

- Viết **vì sao** (lý do, ràng buộc, bẫy), không viết **làm gì**.
- Bắt buộc: cách làm lạ có lý do, sửa tạm (kèm điều kiện gỡ), ràng buộc từ bên ngoài, công thức nghiệp vụ.
- Mô tả hàm công khai khi kiểu dữ liệu chưa tự nói đủ. `TODO` phải có mã việc.

## Đổi thứ nơi khác đang gọi

Đổi route, tên hàm công khai, biến môi trường, dạng dữ liệu: tìm mọi chỗ dùng trong cả repo, sửa trong cùng thay đổi, ghi vào mô tả pull request ([[Review - Hợp đồng và tương thích]]).

## Chủ đề quy ước dự án phải có

| Chủ đề | Định nghĩa |
|---|---|
| Đặt tên | File, biến, hàm, lớp, enum, hằng số |
| Cấu trúc file | Thứ tự trên, theo loại file |
| Ranh giới module | Khi nào tạo module, dùng chung, trừu tượng |
| Kiểu | An toàn kiểu, hạn chế kiểu "bất kỳ" và ép kiểu không căn cứ |
| Hàm | Một trách nhiệm, độ dài, tham số, tác dụng phụ |
| Xử lý lỗi | Loại lỗi, nơi xử lý, log, lỗi trả cho client |
| Validate | Tại ranh giới tin cậy |
| Comment | Ngôn ngữ, khi nào cần, cách ghi TODO |
| Thư viện | Khi nào được thêm, cách đánh giá |
| Test | Đặt tên, dữ liệu mẫu, test hồi quy |
| Bảo mật | Xác thực, quyền, bí mật, upload, dữ liệu nhạy cảm |
| Refactor | Giữ nguyên hành vi, giới hạn phạm vi |

## Bẫy

- Viết cả trăm quy tắc ngày đầu → không ai đọc, không ai kiểm.
- Cùng một quy tắc chép ở nhiều nơi rồi lệch nhau → một nguồn chuẩn, nơi khác liên kết.
- Báo lỗi định dạng mà công cụ định dạng đã lo → nhiễu.
