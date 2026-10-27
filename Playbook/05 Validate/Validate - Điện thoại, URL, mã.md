---
chu_de: validate-dien-thoai-url-ma
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Validate — Số điện thoại, URL, slug / mã

Đọc cùng [[Validate - quy tắc chung]].

## Số điện thoại

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Chuẩn hoá: bỏ khoảng trắng, `-`, `.`, `()`; số full-width → half-width | Lưu một dạng |
| 2 | **Lưu dạng quốc tế E.164** (`+84912345678`), hiển thị theo vùng | Dùng thư viện số điện thoại đã kiểm chứng, không tự viết regex |
| 3 | Độ dài hợp lệ theo quốc gia (E.164 tối đa 15 chữ số) | |
| 4 | Đầu số `0` trong nước ↔ mã quốc gia: quy đổi rõ | `0912…` = `+84912…` |
| 5 | Trùng (nếu là định danh): so trên dạng đã chuẩn hoá | |
| 6 | Xác minh bằng mã OTP nếu dùng để đăng nhập / khôi phục | Giới hạn tần suất gửi |

## URL

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Chỉ cho `http` / `https` | Chặn `javascript:`, `data:`, `file:` |
| 2 | Có tên miền hợp lệ; độ dài tối đa (~2.000) | |
| 3 | Hiển thị: escape; link ra ngoài có `rel="noopener noreferrer"` | |
| 4 | **Server tự gọi tới URL người dùng nhập** (xem trước link, webhook, tải ảnh từ URL) → chặn địa chỉ nội bộ (`localhost`, `127.0.0.1`, `10.*`, `192.168.*`, `169.254.169.254`…), kiểm cả sau chuyển hướng | Chống SSRF |
| 5 | Chuẩn hoá trước khi so trùng (chữ thường tên miền, bỏ `/` cuối nếu nghiệp vụ cho phép) | |

## Slug / mã do người nhập hoặc tự sinh

| # | Kiểm tra | Ghi chú |
|---|---|---|
| 1 | Ký tự cho phép: `a-z 0-9 -` (slug), `A-Z 0-9 _ -` (mã) | |
| 2 | Tự sinh từ tên: bỏ dấu tiếng Việt (`đ` → `d`), chữ thường, khoảng trắng → `-`, gộp `-` liên tiếp | |
| 3 | Trùng → thêm hậu tố (`-2`, `-3`) khi tự sinh; báo lỗi khi người dùng nhập | Unique ở DB, theo đúng phạm vi |
| 4 | Không trùng từ dành riêng của route (`new`, `edit`, `admin`, `api`) | |
| 5 | Đổi slug của trang công khai → giữ slug cũ chuyển hướng (301), không làm gãy link đã chia sẻ | |
| 6 | Độ dài tối đa | |

## Bẫy

- Lưu số điện thoại nhiều dạng (`0912…`, `+84 912…`, `84912…`) → kiểm trùng không bắt được.
- Cho người dùng nhập URL ảnh rồi server tải về mà không chặn địa chỉ nội bộ → đọc được dịch vụ nội bộ.
- Đổi slug làm gãy link cũ trên mạng xã hội / tìm kiếm.
