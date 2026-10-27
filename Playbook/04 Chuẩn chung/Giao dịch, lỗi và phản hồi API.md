---
chu_de: giao-dich-loi-phan-hoi
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Giao dịch, lỗi và phản hồi API

Ba thứ áp dụng cho **mọi API**, dựng chung một lần ngay từ đầu dự án. Vào dự án có sẵn: dự án đã chọn gì thì theo dự án, ghi đường dẫn vào bản đồ dự án (mục Thành phần dùng chung).

## 1. Khung phản hồi API

### Lựa chọn của tôi

Mọi API trả **cùng một khung**, do một lớp dùng chung bọc (controller / handler không tự dựng khung):

**Thành công**

```json
{
  "data": { "id": 12, "name": "Tin tức" },
  "meta": null,
  "error": null
}
```

**Danh sách có phân trang**

```json
{
  "data": [ { "id": 1 }, { "id": 2 } ],
  "meta": { "page": 2, "per_page": 20, "total": 134, "last_page": 7 },
  "error": null
}
```

**Lỗi**

```json
{
  "data": null,
  "meta": null,
  "error": {
    "status": 422,
    "code": "VALIDATION_FAILED",
    "message": "Dữ liệu không hợp lệ",
    "fields": {
      "email": [
        { "code": "validation.max", "message": "Email tối đa 254 ký tự", "params": { "max": 254 } }
      ]
    },
    "trace_id": "01JC9W4K6R…"
  }
}
```

| Trường | Ý nghĩa | Vì sao |
|---|---|---|
| `data` | Kết quả; `null` khi lỗi hoặc không có nội dung | Luôn có → client không phải kiểm tồn tại |
| `meta` | Phân trang / thông tin phụ; `null` nếu không có | Tách khỏi `data` → `data` của danh sách luôn là mảng |
| `error` | `null` khi thành công | Client chỉ cần `if (res.error)` |
| `error.status` | Trùng mã HTTP | Client / log đọc được kể cả khi mất mã HTTP (proxy, công cụ) |
| `error.code` | **Mã lỗi dạng chữ, ổn định** (`EMAIL_TAKEN`, `ORDER_ALREADY_PAID`) | Client rẽ nhánh theo mã, không so câu chữ |
| `error.message` | Câu cho người dùng, đã dịch theo ngôn ngữ của request | Client đơn giản hiện thẳng được |
| `error.fields` | Lỗi từng trường: **luôn** là object → mảng `{code, message, params}` | Một dạng duy nhất; client tự dịch lại từ `code` + `params` nếu muốn |
| `error.trace_id` | Trùng id request trong log | Người dùng báo mã → tìm ra log trong vài giây |

Chuẩn tham khảo nếu nhóm muốn chuẩn công nghiệp: RFC 9457 "Problem Details for HTTP APIs" — cùng ý tưởng (mã, tiêu đề, chi tiết, trường mở rộng).

### Checklist

- [ ] Một lớp dùng chung bọc mọi phản hồi (thành công lẫn lỗi), kể cả lỗi do framework ném ra (401, 404, 429, 500).
- [ ] Lỗi trường luôn cùng dạng — không lúc chuỗi, lúc mảng, lúc object.
- [ ] Danh sách luôn phân trang theo cùng một `meta`.
- [ ] Mã lỗi nằm trong một danh sách hằng số ([[Hằng số, enum, thông báo]]).
- [ ] Đặc tả API mô tả đúng khung thật, kể cả phản hồi lỗi ([[Hợp đồng API giữa các phần]]).
- [ ] Client dùng kiểu dữ liệu sinh từ đặc tả, không khai báo tay một khung khác.

## 2. Mã HTTP

### Lựa chọn của tôi

| Mã | Khi |
|---|---|
| 200 | Đọc, sửa thành công |
| 201 | Tạo mới thành công |
| 202 | Đã nhận, xử lý nền ([[Việc nền và thông báo tức thì]]) |
| 204 | Thành công không có nội dung — hoặc 200 với `data: null` nếu muốn khung luôn giống nhau (chọn một cho cả dự án) |
| 400 | Request sai dạng (JSON hỏng, thiếu header bắt buộc) |
| 401 | **Chưa** đăng nhập / phiên hết hạn |
| 403 | **Đã** đăng nhập nhưng không có quyền |
| 404 | Không tìm thấy (hoặc không được biết là có — xem bản ghi của người khác) |
| 409 | Xung đột: đã tồn tại, đã bị người khác sửa, trạng thái không cho phép |
| 419 | Thiếu / sai mã chống giả mạo (CSRF) — nếu dùng cookie |
| 422 | Dữ liệu nhập sai luật validate; vi phạm quy tắc nghiệp vụ |
| 429 | Quá giới hạn tần suất, kèm `Retry-After` |
| 500 | Lỗi hệ thống — **chỉ** khi server thật sự hỏng |
| 502 / 503 / 504 | Dịch vụ phía sau lỗi / bảo trì / hết giờ chờ |

- [ ] Lỗi nghiệp vụ **không bao giờ** là 500 (500 làm giám sát báo động nhầm, client tưởng server hỏng).
- [ ] Phân biệt 401 / 403: client gặp 401 thì đi đăng nhập lại; gặp 403 mà tưởng 401 thì làm mới phiên vô ích.

## 3. Phân loại lỗi

### Lựa chọn của tôi

Một **lớp lỗi nghiệp vụ gốc** mang: mã HTTP, mã lỗi dạng chữ, khoá thông báo + tham số, có ghi log không. Mỗi lỗi nghiệp vụ là một lớp con nhỏ (hoặc một hằng số mã lỗi). Một **bộ xử lý lỗi tập trung** đổi mọi ngoại lệ thành khung ở mục 1.

| Loại | HTTP | Giao dịch | Log | Người dùng thấy |
|---|---|---|---|---|
| Dữ liệu nhập sai | 422 | Rollback | Không | Lỗi từng trường |
| Vi phạm quy tắc nghiệp vụ | 422 / 409 | Rollback | Info | Câu theo mã lỗi |
| Chưa đăng nhập | 401 | Rollback | Không | "Vui lòng đăng nhập lại" |
| Không có quyền | 403 | Rollback | Warning (đáng theo dõi) | Câu chung |
| Không tìm thấy | 404 | Rollback | Không | Câu chung — **không lộ tên bảng / lớp** |
| Quá giới hạn | 429 | — | Không | Thời gian chờ |
| Dịch vụ ngoài lỗi | 502 / 503 / 504 | Rollback + dọn phần đã ghi ra ngoài | Error | "Thử lại sau" |
| Lỗi hệ thống | 500 | Rollback | **Error, đầy đủ** | Câu chung + `trace_id`; **không** lộ SQL, đường dẫn, stack, tên máy |

### Bẫy

- Framework thường **bọc** ngoại lệ gốc (không tìm thấy, không có quyền) vào ngoại lệ HTTP chung trước khi tới bộ xử lý → mất mã / thông điệp gốc. Kiểm cả nguyên nhân gốc của ngoại lệ.
- Thông điệp mặc định của framework hay chứa tên lớp / bảng ("No query results for model …") → thay bằng câu chung.
- Lỗi nghiệp vụ ném bằng một loại ngoại lệ chung (logic error) → mặc định ra 500. Phải có lớp riêng.
- Môi trường dev bật debug → lỗi hiện chi tiết bên trong (tên máy, cổng). Không sao ở dev, nhưng **phải kiểm production tắt**.
- Gửi sai kiểu dữ liệu (mảng thay chuỗi) làm sập xử lý → 500. Validate kiểu trước mọi xử lý.

## 4. Giao dịch DB

### Lựa chọn của tôi

- Request **ghi** chạy trong một giao dịch mở ở lớp dùng chung (middleware / interceptor); request **đọc** không mở.
- Có ngoại lệ **hoặc** phản hồi ≥ 400 → rollback; còn lại → commit.
- Service báo thất bại bằng **ném lỗi**, không trả `false` / `null`.
- Một giao dịch ngoài cùng; service lồng nhau không tự mở thêm nếu không cần.

### Bẫy

| Tình huống | Sai | Đúng |
|---|---|---|
| Ghi nhật ký đăng nhập sai / nhật ký thay đổi | Lỗi → rollback → mất luôn dòng nhật ký | Route đó không bọc giao dịch, hoặc ghi qua kết nối riêng |
| Gửi mail, đẩy việc nền, phát sự kiện | Chạy trước commit: DB rollback mà mail đã đi, việc nền đọc dữ liệu chưa có | Chạy **sau commit** |
| Lưu file vào kho file rồi DB lỗi | File mồ côi | DB trước, xoá file sau khi DB lưu xong; hoặc dọn trong nhánh lỗi; hoặc việc dọn định kỳ |
| Nhập hàng loạt | Một giao dịch giữ khoá rất lâu | Chia lô, mỗi lô một giao dịch, chạy nền |
| Đọc rồi ghi dựa trên giá trị vừa đọc (tồn kho, số dư, thứ tự) | Hai request cùng đọc, cùng ghi → sai | Khoá dòng khi đọc, hoặc cập nhật nguyên tử, hoặc kiểm phiên bản (409 khi lệch) — xem [[Làm thế nào để xử lý request đồng thời mua hàng lớn hơn số hàng trong kho]] |

## 5. Phía client

- [ ] Một API client chung: đọc khung, ném lỗi có kiểu (mang `status`, `code`, `fields`, `trace_id`).
- [ ] Xử lý theo mã (401 → đăng nhập lại, 403, 422 gắn vào ô, 409, 5xx / mất mạng) đúng thứ tự ở [[Thông báo và hiển thị lỗi]].
- [ ] Không đọc thẳng các trường lẻ của phản hồi ở từng màn hình; luôn qua hàm dùng chung.
