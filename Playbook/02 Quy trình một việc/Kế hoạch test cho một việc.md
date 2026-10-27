---
chu_de: ke-hoach-test
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Kế hoạch test cho một việc

Bước 5 của [[Quy trình một việc]]: chọn test nào, phạm vi tới đâu, cái nào **phải xin xác nhận trước khi chạy**. Mức test, dữ liệu và cô lập: [[Chiến lược kiểm thử]]; mẫu: [[Mẫu - Kế hoạch test]].

Trước: [[Thiết kế trước khi làm]] · Sau: làm theo quy tắc dự án → [[Lệnh kiểm tra và thời điểm chạy]] → [[Đối chiếu checklist và minh chứng]].

## Lựa chọn của tôi

- Lập kế hoạch test **trước khi viết code** cho việc vừa / lớn; đầu vào là tiêu chí chấp nhận ([[Nhận và làm rõ yêu cầu]]).
- Mỗi tiêu chí chấp nhận có ít nhất một test: tự động nếu được, thủ công có ghi bước nếu không.
- Test rẻ, không tác dụng phụ (đơn vị, tích hợp trên DB test riêng, lint, kiểu) → **chạy luôn**.
- Test tốn kém hoặc có tác dụng phụ (đầu cuối qua trình duyệt, hiệu năng, chạm môi trường / dữ liệu dùng chung, gọi dịch vụ thật, bộ hồi quy rất chậm) → **cân nhắc phạm vi, xin xác nhận, rồi mới chạy**.
- Báo kết quả thật: lệnh, số pass / fail, phần chưa chạy và lý do.

## Các loại test

| Loại | Chứng minh | Chạy ở đâu | Xin xác nhận |
|---|---|---|---|
| Đơn vị | Một hàm / lớp đúng quy tắc | Máy dev / container, không DB thật | Không |
| Tích hợp | Các phần chạy đúng với DB / cache / hàng đợi thật | DB test riêng | Không |
| API / hợp đồng | Mã HTTP, khung phản hồi, quyền; hai phía hiểu nhau | DB test riêng | Không |
| Component giao diện | Hiển thị / tương tác của một thành phần | Môi trường giả lập trình duyệt | Không |
| Giao diện thủ công | Bố cục, chữ, điện thoại, ngôn ngữ — mắt người | Trình duyệt trên dev | Liệt kê trang để mình xem |
| Đầu cuối tự động | Một luồng người dùng thật qua trình duyệt | Môi trường đang chạy | **Có** |
| Hồi quy | Luồng cũ không hỏng | Bộ test có sẵn | Khi bộ đầy đủ chậm |
| Hiệu năng / tải | Đủ nhanh dưới tải | Môi trường riêng | **Có** |
| Bảo mật | Tấn công đã biết bị chặn | Test tự động, công cụ quét | Quét chủ động vào hệ thống đang chạy → **có** |

"Test tự động" = mọi loại chạy bằng lệnh, không cần người, chạy lại được trên CI.

## Cân nhắc phạm vi

1. **Tiêu chí → mức test**: mỗi tiêu chí chọn mức rẻ nhất bắt được lỗi.
2. **Ảnh hưởng → hồi quy**: phần nào dùng thứ bị đổi ([[Review - Chọn kiểm tra theo rủi ro]]) → test có sẵn nào phải chạy lại.
3. **Rủi ro**: quyền, tiền, dữ liệu, xoá → test bắt buộc kể cả việc nhỏ.
4. **Chi phí**: thời gian chạy, dữ liệu cần tạo, môi trường cần bật, tác dụng phụ (email, dịch vụ ngoài, dữ liệu dùng chung).
5. **Chọn** cho từng dòng: bắt buộc / nên / bỏ (lý do + rủi ro còn lại).

## Xin xác nhận

```
Kế hoạch test: <tên việc>
Chạy luôn:      <loại — N test mới, lệnh>
Cần xác nhận:   <test — thời gian, dữ liệu tạo ra, tác dụng phụ, cách dọn>
Bạn tự xem:     <trang / khu vực — kích thước màn hình, ngôn ngữ>
Không test:     <cái gì — vì sao, rủi ro còn lại>
```

Có trả lời mới chạy phần cần xác nhận. Phạm vi đổi giữa chừng → hỏi lại.

Dữ liệu và môi trường test: theo mục "Dữ liệu và cô lập" của [[Chiến lược kiểm thử]]; dữ liệu thử tạo tay và dọn dẹp: [[Môi trường phát triển và dọn dẹp]].

## Checklist

- [ ] Mỗi tiêu chí chấp nhận ↔ ít nhất một test.
- [ ] Có test luồng lỗi: dữ liệu sai, chưa đăng nhập, không có quyền, không tồn tại, xung đột.
- [ ] Bỏ thay đổi đi thì test mới phải fail.
- [ ] Test tốn kém / có tác dụng phụ chỉ chạy sau khi được xác nhận.
- [ ] Kết quả ghi theo [[Mẫu - Minh chứng]]: lệnh, số pass / fail, phần chưa chạy.

## Bẫy

- Viết test sau khi code xong, nhìn code mà viết → test xác nhận lỗi thay vì bắt lỗi.
- Chỉ test luồng đúng.
- Chạy đầu cuối trên DB dev rồi quên dọn.
- Nói "test pass" khi chưa chạy trong phiên này.
