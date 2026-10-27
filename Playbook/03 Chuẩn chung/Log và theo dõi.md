---
chu_de: log
ap_dung: [back-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Log và theo dõi

Mục tiêu: khi có sự cố, từ một **mã tra cứu** người dùng báo, tìm ra toàn bộ đường đi của request trong vài phút (hệ thống gom log tập trung).

## Lựa chọn của tôi

- Log **có cấu trúc (JSON)**, mỗi dòng có `trace_id`, `event` (mã sự kiện dạng `<cụm>.<hành động>`), người thực hiện, bản ghi liên quan.
- Id request sinh ở lớp dùng chung, trả về trong header và trong `error.trace_id` ([[Giao dịch, lỗi và phản hồi API]]).
- Mã sự kiện nằm trong một danh sách hằng số như mã lỗi.
- Lọc tự động các khoá nhạy cảm trước khi ghi.
- Lỗi chi tiết chỉ ở log; người dùng chỉ thấy câu chung + mã tra cứu.

## Mức log

| Mức | Khi | Ví dụ |
|---|---|---|
| `debug` | Chỉ để gỡ lỗi; tắt ở production | Giá trị trung gian |
| `info` | Sự kiện nghiệp vụ bình thường đáng ghi | Tạo đơn hàng, người dùng đăng nhập, job xong |
| `warning` | Bất thường nhưng hệ thống tự xử được | Thử lại lần 2, bị từ chối quyền, dữ liệu lạ từ bên thứ ba |
| `error` | Một thao tác thất bại cần người xem | Gọi thanh toán lỗi, job hết số lần thử |
| `critical` | Hệ thống / chức năng chính hỏng | Mất kết nối DB, hàng đợi dừng |

## Log có cấu trúc (JSON) — trường bắt buộc

| Trường | Ý nghĩa |
|---|---|
| `timestamp` | UTC, ISO 8601 |
| `level` | Như bảng trên |
| `event` | **Mã sự kiện** dạng chữ ổn định: `order.created`, `payment.failed`, `auth.login_failed` |
| `message` | Câu ngắn cho người đọc |
| `trace_id` | Id request (sinh ở middleware, trả về header `X-Request-Id` và trong lỗi API) |
| `user_id` | Người thực hiện (id, không email) |
| `module` | Cụm tính năng |
| `entity`, `entity_id` | Bản ghi liên quan |
| `duration_ms` | Với gọi ngoài, job, truy vấn chậm |
| `error.class`, `error.code` | Với lỗi |

`event` là thứ giúp lọc: đặt theo `<cụm>.<hành động>[_<kết quả>]`, liệt kê trong một file hằng số như mã lỗi.

## Mỗi lớp log gì

| Lớp | Log |
|---|---|
| Middleware | Một dòng mỗi request: method, route, mã HTTP, thời gian, trace_id (access log) |
| Service | Sự kiện nghiệp vụ quan trọng (`info`), quyết định bất thường (`warning`) |
| Gọi dịch vụ ngoài | Đích, thời gian, mã kết quả, số lần thử; **không** log body chứa dữ liệu nhạy cảm |
| Job | Bắt đầu, xong, thất bại, số lần thử |
| Bộ xử lý lỗi | Mọi 5xx với stack (`error`); 4xx thường không log (trừ 403 → `warning`) |

## Không bao giờ log

Mật khẩu, token, cookie phiên, khoá API, số thẻ, dữ liệu cá nhân đầy đủ (che: `a***@gmail.com`), body upload. Cấu hình **lọc tự động** các khoá tên `password`, `token`, `secret`, `authorization`.

## Truy vấn mẫu (CloudWatch Logs Insights)

```
fields @timestamp, level, event, message
| filter trace_id = "01JC..."
| sort @timestamp asc
```

```
filter level = "error" | stats count() by event | sort count() desc
```

## Cảnh báo (khi có production)

- Tỉ lệ 5xx vượt ngưỡng trong 5 phút.
- Thời gian phản hồi p95 vượt ngưỡng.
- Hàng đợi tồn quá lâu (job không chạy — lỗi im lặng, người dùng chỉ thấy "đang xử lý" mãi).
- Không có log nào trong X phút (dịch vụ chết).
