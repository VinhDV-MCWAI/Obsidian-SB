---
chu_de: van-hanh-su-co
ap_dung: [moi-du-an]
muc_do: nen-co
cap_nhat: 2026-10-27
---
# Vận hành và sự cố

Sau phát hành: giữ hệ thống chạy, biết sớm khi hỏng, xử lý sự cố có bài bản. Nền: [[backup-restore]], [[Hệ thống xử lý bị lỗi mất dữ liệu thì xử lý thế nào]].

Trước: [[Phát hành và quay lui]].

## Lựa chọn của tôi

- Mỗi dịch vụ có **kiểm tra sức khoẻ** gọi một đường dẫn thật, ổn định.
- Cảnh báo **một lần khi vấn đề mới xuất hiện** và một lần khi hết — không báo lặp mỗi phút.
- Sao lưu tự động **và diễn tập khôi phục** định kỳ (sao lưu chưa thử = chưa có).
- Mỗi sự cố có báo cáo: người dùng thấy gì, dòng thời gian, nguyên nhân gốc, việc theo dõi.

## Kiểm tra sức khoẻ

- [ ] Gọi đường dẫn **chắc chắn tồn tại** (`/health`, trang đăng nhập), không gọi `/` hay trang có thể đổi.
- [ ] **Đổi route / chuyển trang → sửa luôn kiểm tra sức khoẻ.** Bài học: chuyển trang sang đường dẫn mới, kiểm tra sức khoẻ vẫn gọi đường dẫn cũ (404) → dịch vụ "không khoẻ" → cổng vào chờ dịch vụ đó nên không bật → cả trang không vào được; không ai thấy vì container cũ vẫn giữ trạng thái "khoẻ".
- [ ] Chuỗi "chỉ bật khi X khoẻ": một dịch vụ phụ không khoẻ có thể chặn cả hệ thống.

## Cảnh báo tối thiểu

| Cảnh báo | Vì sao |
|---|---|
| Dịch vụ dừng / không khoẻ | Hiển nhiên |
| Việc nền chờ quá lâu; việc nền lỗi mới | Worker dừng là lỗi **im lặng** ([[Việc nền và thông báo tức thì]]) |
| Tỉ lệ 5xx, thời gian phản hồi p95 vượt ngưỡng | Khi có production |
| Không có log trong X phút | Dịch vụ chết hẳn |
| Bộ canh cảnh báo tự nó không chạy | Biết lúc nào không ai canh |

Tắt dịch vụ có chủ đích → tạm tắt cảnh báo trước. Log để tra: [[Log và theo dõi]].

## Sao lưu và diễn tập

- [ ] Sao lưu DB **và** kho file (gồm phiên bản cũ nếu thùng rác dựa vào phiên bản — [[Thùng rác và xoá]]).
- [ ] Diễn tập khôi phục vào **DB / kho tạm** (tài khoản riêng), không đụng dữ liệu đang dùng.
- [ ] So sau khôi phục: số dòng **và** mã băm nội dung từng bảng; file so từng byte; chạy ứng dụng thử trên bản khôi phục.
- [ ] Ghi thời gian sao lưu / khôi phục thực tế.

| Diễn tập (mỗi quý / trước phát hành lớn) | Kiểm gì |
|---|---|
| Mất dữ liệu → khôi phục | Thời gian, dữ liệu mất, phần nào không có trong sao lưu |
| Hàng đợi dừng | Người dùng thấy gì, có cảnh báo không, dừng bao lâu thì hỏng dữ liệu |
| Cache / kho phiên dừng | Phần nào vẫn chạy, lỗi có lộ chi tiết bên trong không |

## Khi có sự cố

1. **Giữ bằng chứng trước** (trạng thái, log, cấu hình) rồi mới bật lại.
2. Khắc phục tạm để người dùng dùng được (quay lui nếu do phát hành — [[Phát hành và quay lui]]).
3. Tìm nguyên nhân gốc: đưa giả thuyết → kiểm bằng dữ liệu ("7/7 dịch vụ hỏng có điểm chung X, 3/3 còn chạy thì không").
4. Sửa dứt điểm + thêm kiểm tra để lần sau tự phát hiện.
5. Báo cáo sự cố (dòng thời gian, nguyên nhân hỏi "tại sao" 5 lần, tốt / chưa tốt / may mắn, việc theo dõi); thêm cách xử lý vào runbook; ghi một dòng đề xuất tri thức nếu là bài học chung.

## Bẫy

- Debug bật ở production → lỗi lộ tên máy, cổng nội bộ.
- Sửa bằng tay nhiều lần ("bật lại là chạy") mà không tìm nguyên nhân → lần sau lại gặp.
