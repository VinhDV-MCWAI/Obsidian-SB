---
chu_de: thiet-ke-code
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Thiết kế code và chia lớp

Kiến trúc: code nằm ở lớp nào, phụ thuộc theo chiều nào, khi nào tạo phần dùng chung. Cách viết từng dòng (tên, hàm, comment): [[Quy ước viết code]]. Dự án / framework đã có kiến trúc → theo dự án.

## Lựa chọn của tôi

- Server chia lớp: **validate ở biên → xử lý request mỏng → nghiệp vụ → truy cập dữ liệu → định dạng đầu ra**, cùng các lớp cắt ngang dùng chung (xác thực, quyền, giao dịch, bọc phản hồi, mã request).
- Client chia: **trang → thành phần tính năng → thành phần dùng chung**; mọi gọi API qua một client chung; dữ liệu server qua **một** lớp cache với khoá truy vấn đặt ở một nơi.
- Chỉ tạo lớp chung / interface khi có **≥ 2 chỗ dùng thật**.
- Cấu hình chỉ đọc qua **một lớp cấu hình**; code không đọc thẳng biến môi trường (để cache / kiểm cấu hình được).
- Mọi chuỗi người dùng thấy đi qua lớp đa ngôn ngữ, kể cả khi dự án chỉ có một ngôn ngữ.

## Các lớp phía server

```
Route → Lớp cắt ngang → Validate → Xử lý request → Nghiệp vụ → Truy cập dữ liệu → DB
                                         └──► Định dạng đầu ra ──► Bọc phản hồi
Việc nền: hàng đợi / sự kiện / bộ nghe
```

| Lớp | Được làm | Không được làm |
|---|---|---|
| Route | Khai báo đường dẫn, phương thức, lớp cắt ngang, tên | Logic |
| Lớp cắt ngang | Xác thực, quyền, giao dịch, bọc phản hồi, giới hạn tần suất, mã request | Nghiệp vụ của một tính năng |
| Validate | Kiểm + chuẩn hoá đầu vào, thông báo lỗi | Ghi DB, gọi dịch vụ ngoài (trừ kiểm tồn tại) |
| Xử lý request | Nhận request đã kiểm, gọi **một** hàm nghiệp vụ, trả kết quả | Truy vấn DB, nghiệp vụ, bắt lỗi rồi nuốt |
| Nghiệp vụ | Quy tắc nghiệp vụ, điều phối nhiều nguồn dữ liệu, ném lỗi nghiệp vụ | Biết về HTTP (request, mã HTTP, cookie) |
| Truy cập dữ liệu | Truy vấn, tái dùng câu truy vấn | Quy tắc nghiệp vụ |
| Mô hình dữ liệu | Quan hệ, kiểu cột, phạm vi truy vấn đơn giản | Nghiệp vụ dài |
| Định dạng đầu ra | Chọn trường trả về, ẩn trường nhạy cảm | Truy vấn thêm (gây N+1) |
| Interface | Khi có **≥ 2** cài đặt thật hoặc cần thay khi test (dịch vụ ngoài) | Tạo cho mọi lớp "cho chuẩn" |

## Phía giao diện

| Lớp | Vai trò |
|---|---|
| Trang / route | Ghép thành phần, lấy tham số URL |
| Thành phần tính năng | Giao diện + trạng thái của một tính năng |
| Thành phần dùng chung | Nút, dialog, bảng, ô nhập — không biết nghiệp vụ |
| Lớp gọi API | Gọi API, cache, xử lý lỗi chung ([[Giao dịch, lỗi và phản hồi API]] mục client) |
| Schema validate | Luật form |
| Kiểu dữ liệu | Sinh từ đặc tả API, không khai báo tay trùng ([[Hợp đồng API giữa các phần]]) |

## Phạm vi code dùng chung

| Phạm vi | Đặt ở | Quy tắc import |
|---|---|---|
| Chỉ một hàm / một module | Tại chỗ | — |
| Một cụm tính năng | Thư mục của cụm | Chỉ import phần dùng chung hoặc chính cụm |
| Toàn hệ thống, **cùng khái niệm, cùng nghĩa** | Thư mục dùng chung / hạ tầng | Không import code của tính năng |
| Nghiệp vụ đặc thù | Trong miền nghiệp vụ tương ứng | — |
| Giống công thức nhưng **khác nghĩa** | **Không gộp** | — |

## SOLID — áp dụng thực tế

| Nguyên tắc | Hiểu đơn giản | Dấu hiệu vi phạm |
|---|---|---|
| Một trách nhiệm | Một lớp một lý do để sửa | Lớp nghiệp vụ 2.000 dòng làm mọi thứ |
| Mở / đóng | Thêm loại mới bằng thêm lớp, không sửa chuỗi `if` | `switch` theo loại lặp ở nhiều nơi |
| Thay thế Liskov | Lớp con dùng thay lớp cha không vỡ | Lớp con ném "không hỗ trợ" |
| Tách interface | Interface nhỏ theo nhu cầu | Cài đặt phải viết hàm rỗng |
| Đảo phụ thuộc | Phụ thuộc thứ được tiêm vào, không tự tạo dịch vụ | Không test được vì gọi thẳng dịch vụ thật |

Nền: [[App & Request Life-Cycle]] (tiêm phụ thuộc thay Singleton).

## Bẫy

- Ép mọi nghiệp vụ qua một lớp riêng vì tài liệu nói vậy, trái cách framework đang làm.
- AI "dời ra dùng chung cho dễ dùng lại" trái ranh giới của dự án.
- Gộp hai hàm giống công thức nhưng khác nghĩa → sửa một chỗ hỏng chỗ kia.
