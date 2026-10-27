---
chu_de: log
ap_dung: [back-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Log và theo dõi

Mục tiêu: từ một **mã tra cứu** người dùng báo, tìm ra toàn bộ đường đi của request trong vài phút. Cảnh báo dựa trên log: [[Vận hành và sự cố]].

## Lựa chọn của tôi

- Log **có cấu trúc (JSON)**, mỗi dòng có `trace_id`, `event`, người thực hiện, bản ghi liên quan; gom về một nơi tra cứu tập trung.
- Mã request sinh ở lớp dùng chung, trả về trong header và trong `error.trace_id` ([[Giao dịch, lỗi và phản hồi API]]).
- Mã sự kiện nằm trong một danh sách hằng số như mã lỗi.
- Lọc tự động khoá nhạy cảm trước khi ghi. Lỗi chi tiết chỉ ở log; người dùng thấy câu chung + mã tra cứu.

## Mức log

| Mức | Khi | Ví dụ |
|---|---|---|
| `debug` | Chỉ để gỡ lỗi; tắt ở production | Giá trị trung gian |
| `info` | Sự kiện nghiệp vụ đáng ghi | Tạo đơn, đăng nhập, việc nền xong |
| `warning` | Bất thường nhưng tự xử được | Thử lại lần 2, bị từ chối quyền, dữ liệu lạ từ bên thứ ba |
| `error` | Một thao tác thất bại cần người xem | Gọi thanh toán lỗi, việc nền hết lượt thử |
| `critical` | Hệ thống / chức năng chính hỏng | Mất kết nối DB, hàng đợi dừng |

## Trường bắt buộc

| Trường | Ý nghĩa |
|---|---|
| `timestamp` | UTC, ISO 8601 |
| `level` | Như bảng trên |
| `event` | Mã sự kiện ổn định `<cụm>.<hành động>[_<kết quả>]`: `order.created`, `auth.login_failed` |
| `message` | Câu ngắn cho người đọc |
| `trace_id` | Mã request |
| `user_id` | Người thực hiện (id, không email) |
| `module`, `entity`, `entity_id` | Cụm tính năng, bản ghi liên quan |
| `duration_ms` | Gọi ngoài, việc nền, truy vấn chậm |
| `error.class`, `error.code` | Với lỗi |

## Mỗi lớp log gì

| Lớp | Log |
|---|---|
| Lớp cắt ngang | Một dòng mỗi request: phương thức, route, mã HTTP, thời gian, `trace_id` |
| Nghiệp vụ | Sự kiện quan trọng (`info`), quyết định bất thường (`warning`) |
| Gọi dịch vụ ngoài | Đích, thời gian, mã kết quả, số lần thử; **không** log body nhạy cảm |
| Việc nền | Bắt đầu, xong, thất bại, số lần thử |
| Bộ xử lý lỗi | Mọi 5xx kèm stack (`error`); 4xx thường không log, trừ 403 (`warning`) |

## Không bao giờ log

Mật khẩu, token, cookie phiên, khoá API, số thẻ, dữ liệu cá nhân đầy đủ (che: `a***@gmail.com`), body upload. Lọc tự động các khoá tên `password`, `token`, `secret`, `authorization`.

## Truy vấn cần làm được

- Mọi dòng của một `trace_id`, theo thời gian tăng dần.
- Đếm lỗi theo `event` trong khoảng thời gian, nhiều nhất trước.
- Mọi hành động của một `user_id` / trên một `entity_id`.

Ghi các truy vấn mẫu bằng cú pháp của công cụ log thật vào runbook của dự án.

## Bẫy

- Ghi cả request vào log → lộ mật khẩu / token.
- `event` viết tự do mỗi chỗ một kiểu → không lọc / đếm được.
- Log lỗi ở cả tầng dưới và bộ xử lý chung → một lỗi thành nhiều dòng.
