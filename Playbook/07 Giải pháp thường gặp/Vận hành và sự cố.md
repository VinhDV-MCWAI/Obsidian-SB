---
chu_de: van-hanh-su-co
ap_dung: [moi-du-an]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Vận hành và sự cố

Nền: [[backup-restore]], [[Hệ thống xử lý bị lỗi mất dữ liệu thì xử lý thế nào]].

## Lựa chọn của tôi

- Mỗi dịch vụ có **kiểm tra sức khoẻ** gọi một đường dẫn thật, ổn định.
- Cảnh báo **một lần khi vấn đề mới xuất hiện** và một lần khi hết — không báo lặp mỗi phút.
- Sao lưu tự động **và diễn tập khôi phục** định kỳ.
- Mỗi sự cố có báo cáo: người dùng thấy gì, dòng thời gian, nguyên nhân (hỏi "tại sao" 5 lần), cái tốt / chưa tốt / may mắn, việc theo dõi.

## Kiểm tra sức khoẻ

- [ ] Gọi đường dẫn **chắc chắn tồn tại** (trang đăng nhập, `/health`), không gọi `/` hay trang có thể đổi.
- [ ] **Đổi route / chuyển trang → sửa luôn kiểm tra sức khoẻ.** Bài học: chuyển trang quản trị sang đường dẫn mới, kiểm tra sức khoẻ vẫn gọi đường dẫn cũ (404) → dịch vụ "không khoẻ" → cổng vào chờ dịch vụ đó nên không bật → cả trang không vào được. Không ai thấy vì container cũ vẫn giữ trạng thái "khoẻ" từ trước.
- [ ] Chuỗi phụ thuộc "chỉ bật khi X khoẻ": biết rằng một dịch vụ phụ không khoẻ có thể chặn cả hệ thống.
- [ ] Kiểm tra trước khi phát hành phải **tạo lại môi trường từ đầu**, không dùng container / máy đang chạy sẵn.

## Cảnh báo tối thiểu

| Cảnh báo | Vì sao |
|---|---|
| Dịch vụ dừng / không khoẻ / không tồn tại | Hiển nhiên |
| Việc nền chờ quá lâu | Worker dừng là lỗi **im lặng** ([[Việc nền và thông báo tức thì]]) |
| Việc nền lỗi mới | |
| Tỉ lệ 5xx, thời gian phản hồi vượt ngưỡng | Khi có production |
| Không có log trong X phút | Dịch vụ chết hẳn |
| Bộ canh cảnh báo tự nó không chạy | Biết lúc nào **không ai canh** |

Tắt dịch vụ có chủ đích → tạm tắt cảnh báo trước để không báo nhầm.

## Sao lưu và diễn tập

- [ ] Sao lưu DB **và** kho file (gồm phiên bản cũ nếu thùng rác dựa vào phiên bản — [[Thùng rác và xoá]]).
- [ ] Diễn tập khôi phục vào **DB / kho tạm**, không đụng dữ liệu đang dùng.
- [ ] So sau khôi phục: số dòng **và** mã băm nội dung từng bảng; file so từng byte.
- [ ] Chạy ứng dụng thử trên bản khôi phục.
- [ ] Ghi thời gian sao lưu / khôi phục thực tế.
- [ ] Tài khoản ứng dụng chỉ có quyền trên kho chính → kho tạm cần tài khoản riêng khi diễn tập.

## Diễn tập sự cố (mỗi quý hoặc trước phát hành lớn)

| Tình huống | Kiểm gì |
|---|---|
| Mất dữ liệu → khôi phục | Thời gian, dữ liệu mất, phần nào không có trong sao lưu |
| Hàng đợi dừng | Người dùng thấy gì, có cảnh báo không, dừng bao lâu thì hỏng dữ liệu |
| Cache / kho phiên dừng | Phần nào vẫn chạy, phần nào lỗi, lỗi có lộ chi tiết bên trong không |

## Khi có sự cố

1. **Giữ bằng chứng trước** (trạng thái, log, cấu hình) rồi mới bật lại.
2. Khắc phục tạm để người dùng dùng được.
3. Tìm nguyên nhân gốc: đưa giả thuyết → kiểm bằng dữ liệu (ví dụ "7/7 dịch vụ hỏng đều có điểm chung X, 3/3 dịch vụ còn chạy không có").
4. Sửa dứt điểm + thêm kiểm tra để lần sau tự phát hiện.
5. Viết báo cáo; thêm cách xử lý vào runbook; ghi một dòng [[Ghi chú]] nếu là bài học chung.

## Bẫy

- Môi trường dev phát triển trên container: gắn **từng file lẻ** từ máy vào container dễ hỏng sau khi máy khởi động lại — gắn cả thư mục hoặc chép file vào image.
- Debug bật → lỗi lộ tên máy, cổng nội bộ. Kiểm production tắt.
- Sửa lỗi bằng tay nhiều lần ("bật lại là chạy") mà không tìm nguyên nhân → lần sau lại gặp.
