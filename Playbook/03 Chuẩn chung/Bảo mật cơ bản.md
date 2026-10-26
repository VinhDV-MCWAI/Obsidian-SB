---
chu_de: bao-mat
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Bảo mật cơ bản

Rút gọn từ OWASP Top 10 + kinh nghiệm. Đào sâu: [[authen_&_author]], [[CSRF]], [[Đăng nhập và phiên]], [[Phân quyền theo vai trò]].

## Lựa chọn của tôi

- Server là nơi kiểm thật (xác thực, quyền, validate); giao diện chỉ ẩn những gì server sẽ từ chối.
- Dùng phần bảo mật **có sẵn, đã được kiểm chứng** của nền tảng (mã hoá, phiên, băm mật khẩu) thay vì tự viết.
- Mọi lỗi đăng nhập dùng **một** thông báo chung; không lộ tài khoản có tồn tại.
- Mỗi dịch vụ một tài khoản với quyền tối thiểu; bí mật không bao giờ vào code / log / tài liệu.

## Mỗi API

- [ ] Kiểm **xác thực** (ai) và **quyền** (được làm gì) ở server — giao diện ẩn nút chỉ là tiện, không phải bảo vệ.
- [ ] Kiểm quyền trên **bản ghi cụ thể** (người A không sửa được đơn của người B bằng cách đổi id trên URL).
- [ ] Mọi đầu vào được validate ([[Validate - quy tắc chung]]); chỉ nhận trường cho phép (chống gán hàng loạt).
- [ ] Truy vấn có tham số, không ghép chuỗi SQL.
- [ ] Đầu ra HTML được escape; nội dung HTML người dùng nhập được lọc theo danh sách cho phép.
- [ ] Giới hạn kích thước request, số phần tử mảng, độ sâu JSON.

## Đăng nhập và phiên

- [ ] Mật khẩu băm bằng thuật toán chậm (bcrypt / argon2), không bao giờ mã hoá hai chiều.
- [ ] Giới hạn tần suất **nhiều tầng**: theo tài khoản + IP và theo tài khoản riêng (chống dò từ nhiều IP), dùng Redis thay vì DB (xem [[Other Issue]]).
- [ ] Thông báo đăng nhập sai chung chung (không nói "email không tồn tại").
- [ ] Cookie phiên: `HttpOnly`, `Secure`, `SameSite`; đổi id phiên sau khi đăng nhập.
- [ ] Thu hồi được phiên / token trên một thiết bị và tất cả thiết bị; đổi mật khẩu → thu hồi phiên khác.
- [ ] Có CSRF cho cookie-based auth.

## Cấu hình

- [ ] CORS chỉ cho nguồn cần thiết, không `*` với cookie.
- [ ] Header: `Content-Security-Policy`, `X-Content-Type-Options`, `Strict-Transport-Security`, `Referrer-Policy`, `X-Frame-Options` / `frame-ancestors`.
- [ ] Tắt debug ở production; không lộ phiên bản server.

## Bí mật

- [ ] Không commit `.env`, khoá, token; có quét bí mật trước commit ([[Công cụ kiểm tra tự động]]).
- [ ] Mỗi dịch vụ một tài khoản với quyền tối thiểu (ví dụ tài khoản kho file chỉ có quyền trên bucket của nó).
- [ ] Bí mật lộ → **xoay vòng ngay**, xoá khỏi lịch sử git không đủ.

## File tải lên

- [ ] Kiểm loại file bằng nội dung (magic bytes), không tin đuôi file / `Content-Type`.
- [ ] Giới hạn dung lượng ở cả proxy và ứng dụng.
- [ ] Đổi tên khi lưu; lưu ngoài thư mục chạy được; tải xuống qua URL ký có hạn.
- [ ] Quét virus nếu người khác sẽ tải về (ClamAV).

## Dữ liệu cá nhân

- [ ] Chỉ thu cái cần; che khi hiển thị / log; xoá được khi người dùng yêu cầu.
- [ ] Sao lưu cũng chứa dữ liệu cá nhân → mã hoá, giới hạn người truy cập.

## Thư viện

- [ ] Quét lỗ hổng thư viện định kỳ (`npm audit`, `composer audit`, `pip-audit`, Dependabot / Renovate).
- [ ] Thêm thư viện phải có lý do; ưu tiên thư viện được duy trì.
