---
chu_de: bao-mat
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Bảo mật cơ bản

Rút gọn từ OWASP Top 10 + kinh nghiệm. Câu hỏi khi đọc diff: [[Review - Bảo mật]]. Đào sâu: [[authen_&_author]], [[CSRF]].

## Lựa chọn của tôi

- Server là nơi kiểm thật (xác thực, quyền, validate); giao diện chỉ ẩn những gì server sẽ từ chối.
- Dùng phần bảo mật **có sẵn, đã được kiểm chứng** của nền tảng (mã hoá, phiên, băm mật khẩu), không tự viết.
- Mỗi dịch vụ một tài khoản với quyền tối thiểu; bí mật không bao giờ vào code / log / tài liệu.
- Quy tắc bảo mật quan trọng được thực thi bằng validate, test hoặc công cụ, không chỉ nằm trong tài liệu.

## Mỗi API

- [ ] Kiểm **xác thực** (ai) và **quyền** (được làm gì) ở server.
- [ ] Kiểm quyền trên **bản ghi cụ thể** (đổi id trên URL không đọc / sửa được bản ghi người khác) — [[Phân quyền theo vai trò]].
- [ ] Mọi đầu vào được validate ([[Validate - quy tắc chung]]); chỉ nhận trường cho phép (chống gán hàng loạt).
- [ ] Truy vấn có tham số, không ghép chuỗi SQL.
- [ ] Đầu ra HTML được escape; HTML người dùng nhập được lọc theo danh sách cho phép.
- [ ] Giới hạn kích thước request, số phần tử mảng, độ sâu JSON; giới hạn tần suất cho thao tác đắt / nhạy cảm.
- [ ] Server tự gọi URL do người dùng nhập → chặn địa chỉ nội bộ ([[Validate - Điện thoại, URL, mã]]).

## Theo miền (checklist đầy đủ ở file riêng)

| Miền | File |
|---|---|
| Đăng nhập, phiên, khoá khi sai nhiều lần, CSRF, cookie | [[Đăng nhập và phiên]], [[Validate - Mật khẩu]] |
| Vai trò, quyền theo dữ liệu | [[Phân quyền theo vai trò]] |
| File tải lên | [[Upload file và kho file]], [[Validate - File và ảnh]] |
| Lỗi trả về không lộ chi tiết | [[Giao dịch, lỗi và phản hồi API]] |
| Log không chứa bí mật / dữ liệu cá nhân | [[Log và theo dõi]] |

## Cấu hình

- [ ] CORS chỉ cho nguồn cần thiết, không `*` với cookie.
- [ ] Header: `Content-Security-Policy`, `X-Content-Type-Options`, `Strict-Transport-Security`, `Referrer-Policy`, `frame-ancestors`.
- [ ] Debug tắt ở production; không lộ phiên bản server.

## Bí mật

- [ ] Không commit `.env`, khoá, token; quét bí mật trước commit và trên CI ([[Công cụ kiểm tra tự động]]).
- [ ] Mỗi dịch vụ một tài khoản quyền tối thiểu (ví dụ tài khoản kho file chỉ có quyền trên kho của nó).
- [ ] Bí mật lộ → **xoay vòng ngay**; xoá khỏi lịch sử git không đủ.

## Dữ liệu cá nhân

- [ ] Chỉ thu cái cần; che khi hiển thị / log; xoá được khi người dùng yêu cầu.
- [ ] Sao lưu cũng chứa dữ liệu cá nhân → mã hoá, giới hạn người truy cập.

## Thư viện

- [ ] Quét lỗ hổng thư viện định kỳ và trên CI; bật đề xuất cập nhật tự động nếu nơi lưu repo có.
- [ ] Thêm thư viện phải có lý do; ưu tiên thư viện còn được duy trì.

## Bẫy

- Chỉ kiểm "đã đăng nhập", quên "được làm việc này với bản ghi này".
- Tìm kiếm / xuất file / thống kê đi đường khác, bỏ qua bộ lọc quyền.
- Thêm trường vào đối tượng trả về → vô tình lộ trường nhạy cảm.
