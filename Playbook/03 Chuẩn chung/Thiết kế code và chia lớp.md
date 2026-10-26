---
chu_de: thiet-ke-code
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Thiết kế code và chia lớp

## Lựa chọn của tôi

- Server chia lớp: **validate ở biên → xử lý mỏng nhận request → nghiệp vụ → truy cập dữ liệu → định dạng đầu ra**, cùng các lớp cắt ngang dùng chung (xác thực, quyền, giao dịch, bọc phản hồi, id request).
- Client chia: **trang → thành phần tính năng → thành phần dùng chung**; mọi gọi API qua một client chung; trạng thái dữ liệu server qua một thư viện / lớp cache duy nhất với khoá truy vấn đặt ở một nơi.
- Chỉ tạo lớp chung / interface khi có **≥ 2 chỗ dùng thật**.
- Cấu hình chỉ đọc qua **một lớp cấu hình**; code không đọc thẳng biến môi trường (để cache / kiểm tra cấu hình được).
- Mọi chuỗi người dùng thấy đi qua lớp đa ngôn ngữ, kể cả khi dự án chỉ có một ngôn ngữ.

## Các lớp phía server

```
Route → Middleware → Validate (Request) → Controller → Service → Repository → Model / DB
                                               └──► Định dạng đầu ra (Resource / DTO) ──► Bọc phản hồi
Việc chạy nền: Job / Event / Listener
```

| Lớp | Được làm | Không được làm |
|---|---|---|
| Route | Khai báo URL, method, middleware, tên | Logic |
| Middleware | Việc cắt ngang mọi request: xác thực, quyền, giao dịch, bọc phản hồi, giới hạn tần suất, id request | Nghiệp vụ của một tính năng |
| Validate | Kiểm tra + chuẩn hoá đầu vào, thông báo lỗi | Ghi DB, gọi dịch vụ ngoài (trừ kiểm tra tồn tại) |
| Controller | Nhận request đã kiểm, gọi **một** service, trả kết quả | Truy vấn DB, nghiệp vụ, `try/catch` nuốt lỗi |
| Service | Quy tắc nghiệp vụ, điều phối nhiều repository, ném ngoại lệ nghiệp vụ | Biết về HTTP (request, mã HTTP, cookie) |
| Repository | Truy vấn DB, tái dùng câu truy vấn | Quy tắc nghiệp vụ |
| Model | Quan hệ, kiểu cột, phạm vi truy vấn đơn giản | Nghiệp vụ dài |
| Resource / DTO | Định dạng đầu ra, ẩn trường nhạy cảm | Truy vấn thêm (gây N+1) |
| Interface | Khi có **≥ 2** cách cài đặt thật hoặc cần thay khi test (dịch vụ ngoài) | Tạo cho mọi lớp "cho chuẩn" |

## Phía giao diện

| Lớp | Vai trò |
|---|---|
| Trang / route | Ghép component, lấy tham số URL |
| Component tính năng | Giao diện + trạng thái của một tính năng |
| Component dùng chung | Nút, dialog, bảng, ô nhập — không biết nghiệp vụ |
| Hook / service gọi API | Gọi API, cache, xử lý lỗi chung |
| Schema validate | Luật form |
| Kiểu dữ liệu | Sinh từ đặc tả API nếu có, không khai báo tay trùng |

## Phạm vi code dùng chung

| Phạm vi | Đặt ở | Quy tắc import |
|---|---|---|
| Toàn hệ thống | `shared/`, `common/`, `Support/` | Không import code của tính năng |
| Cụm tính năng | `features/<cụm>/`, `Services/<Cụm>/` | Chỉ import `shared` hoặc chính cụm |
| Một tính năng | file riêng | — |

Tạo helper / lớp chung khi có **ít nhất hai** chỗ dùng thật. Một chỗ → để tại chỗ.

## SOLID — áp dụng thực tế

| Nguyên tắc | Hiểu đơn giản | Dấu hiệu vi phạm |
|---|---|---|
| Một trách nhiệm | Một lớp một lý do để sửa | Service tên `UserService` dài 2.000 dòng làm mọi thứ |
| Mở / đóng | Thêm loại mới bằng cách thêm lớp, không sửa chuỗi `if` | `switch` theo loại lặp ở nhiều nơi |
| Thay thế Liskov | Lớp con dùng thay lớp cha không vỡ | Lớp con ném "không hỗ trợ" |
| Tách interface | Interface nhỏ theo nhu cầu | Cài đặt phải viết hàm rỗng |
| Đảo phụ thuộc | Phụ thuộc vào thứ được tiêm vào (DI), không tự `new` dịch vụ | Không test được vì gọi thẳng dịch vụ thật |

Nền: [[App & Request Life-Cycle]] (DI thay Singleton).

## Đặt tên

- [ ] Tên nói **làm gì / là gì**; hàm là động từ (`calculateTotal`), boolean là câu hỏi (`isActive`, `hasPermission`).
- [ ] Không viết tắt khó hiểu; thống nhất một từ cho một khái niệm (không `user` chỗ này `member` chỗ kia).
- [ ] Theo quy ước của ngôn ngữ (camelCase, snake_case, PascalCase).
- [ ] Route REST: danh từ số nhiều, `GET /users`, `GET /users/{id}`, `POST /users`, `PUT/PATCH /users/{id}`, `DELETE /users/{id}`; thao tác đặc biệt `POST /users/{id}/activate`. Nhóm theo quy ước sẵn có của dự án nếu khác.

## Viết hàm

- [ ] Hàm nhỏ, một việc; trả về sớm thay vì `if` lồng nhau.
- [ ] Kiểm tra đầu vào ở biên (request, job, gọi ngoài), bên trong tin dữ liệu đã kiểm.
- [ ] Không nuốt lỗi: `catch` thì phải xử lý thật, ghi log, hoặc ném lại.
- [ ] Không có giá trị viết thẳng ([[Hằng số, enum, thông báo]]).
- [ ] Không code chết, không code comment lại, không file `.bak` — git đã giữ lịch sử.
- [ ] Đơn giản hơn khéo léo.

## Comment

- Viết **vì sao** (lý do, ràng buộc, bẫy), không viết **làm gì** (code đã nói).
- Bắt buộc comment: cách làm lạ có lý do, sửa tạm (kèm điều kiện gỡ), ràng buộc từ bên ngoài, công thức nghiệp vụ.
- Docblock cho hàm công khai khi kiểu dữ liệu chưa tự nói đủ.
- `TODO` phải có mã ticket hoặc tên, không để `TODO` mồ côi.

## Đổi thứ nơi khác đang gọi

Đổi route, tên hàm công khai, biến môi trường, dạng dữ liệu: tìm mọi chỗ dùng trong cả repo, sửa cùng một thay đổi, ghi vào mô tả PR.
