---
chu_de: hieu-nang-cache-dich-vu-ngoai
ap_dung: [back-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Hiệu năng, cache, đồng thời và dịch vụ ngoài

Nền: [[Redis]], [[Các Vấn Đề Chung Khi Nói Về Hệ Thống Có Triệu User]], [[xử lý thao tác đồng thời thế nào]], [[Bạn sẽ xử lý như thế nào khi một API bên thứ ba trả về kết quả không mong muốn]], [[Hệ thống có lượng truy cập tăng đột biến xử lý thể nào]].

## Lựa chọn của tôi

- **Đo trước, tối ưu sau.** Không thêm cache / hàng đợi / search engine khi chưa có số đo cho thấy cần.
- Cache chỉ cho dữ liệu **đọc nhiều, đổi ít**, có thời hạn, và có chỗ **xoá cache rõ ràng** khi dữ liệu đổi.
- Gọi dịch vụ ngoài luôn có **thời gian chờ tối đa**, thử lại có giới hạn, và xử lý được kết quả lạ.
- Thao tác đọc rồi ghi trên cùng dữ liệu (tồn kho, số dư, thứ tự) luôn chống ghi đè đồng thời.

## Hiệu năng

- [ ] Danh sách luôn phân trang; giới hạn số dòng mỗi trang.
- [ ] Không truy vấn lặp theo từng dòng (N+1); nạp quan hệ trước.
- [ ] Index cho cột lọc / sắp xếp / khoá ngoại; xem kế hoạch truy vấn khi chậm ([[Dữ liệu và DB]]).
- [ ] Việc lâu chuyển sang việc nền ([[Việc nền và thông báo tức thì]]).
- [ ] Ảnh / file tĩnh phục vụ qua kho file / CDN, không qua ứng dụng.
- [ ] Đo bằng công cụ tải (k6, JMeter…) trước khi phát hành API nặng; ghi kết quả.

## Cache

- [ ] Khoá cache có tiền tố theo cụm + phiên bản (`article:v2:{id}`) → đổi cấu trúc chỉ cần tăng phiên bản.
- [ ] Mọi chỗ ghi dữ liệu liên quan đều xoá / cập nhật cache — liệt kê các chỗ đó trong code.
- [ ] Dữ liệu phân quyền / tài khoản: cache rất ngắn hoặc không cache (khoá tài khoản phải có hiệu lực ngay) — [[Đăng nhập và phiên]].
- [ ] Cache hết hạn cùng lúc → nhiều request cùng tính lại → khoá tính lại hoặc thời hạn lệch ngẫu nhiên.
- [ ] Cache dừng → hệ thống chậm đi chứ không sập (nếu được thiết kế vậy); biết phần nào phụ thuộc cứng vào cache (phiên đăng nhập, hàng đợi).

## Đồng thời

- [ ] Hai người sửa cùng bản ghi → kiểm phiên bản (cột version / thời điểm cập nhật), lệch → 409 + gợi ý tải lại.
- [ ] Trừ tồn kho / số dư: cập nhật nguyên tử có điều kiện (`… WHERE stock >= n`) hoặc khoá dòng.
- [ ] Bấm gửi hai lần / mạng gửi lại → chống trùng (khoá chống gửi trùng phía client + mã idempotency phía server cho thao tác quan trọng).
- [ ] Unique ở DB cho thứ không được trùng — không dựa vào kiểm tra ở code.

## Gọi dịch vụ ngoài

- [ ] Thời gian chờ kết nối + đọc (ví dụ 3–10 giây), không để mặc định vô hạn.
- [ ] Thử lại chỉ với lỗi tạm thời (mạng, 5xx, 429), chờ tăng dần + ngẫu nhiên, tối đa vài lần. Không thử lại lỗi 4xx.
- [ ] Kiểm dạng dữ liệu trả về như kiểm đầu vào người dùng — không tin dịch vụ ngoài.
- [ ] Dịch vụ ngoài lỗi → trả 502 / 503 với câu chung; nếu được thì hệ thống chạy tiếp với tính năng giảm bớt.
- [ ] Gọi lâu / không cần ngay → chuyển sang việc nền.
- [ ] Log: đích, thời gian, mã kết quả, số lần thử; **không** log khoá API, dữ liệu cá nhân.
- [ ] Khoá API ở cấu hình / kho bí mật; quyền tối thiểu.
- [ ] Test bằng giả lập dịch vụ ngoài; có test cho hết giờ chờ và kết quả lạ.

## Bẫy

- Cache thông tin quyền lâu → người bị khoá vẫn dùng được trong thời gian cache.
- Thử lại mọi lỗi kể cả 4xx → nhân lỗi, có thể bị dịch vụ ngoài chặn.
- Gọi dịch vụ ngoài trong giao dịch DB → giữ khoá DB trong lúc chờ mạng.
