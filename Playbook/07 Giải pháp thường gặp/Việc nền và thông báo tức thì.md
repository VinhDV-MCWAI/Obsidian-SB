---
chu_de: viec-nen
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Việc nền và thông báo tức thì

Nền: [[Queue]], [[Worker]], [[Background Process]], [[Khi có nhiều xử lý đồng thời (gửi email, download, xử lý ảnh,...) bạn sẽ làm gì]].

## Lựa chọn của tôi

- Việc chạy lâu (upload lớn, nhập / xuất dữ liệu, gửi mail hàng loạt, sao lưu) **không làm trong request** — đẩy vào hàng đợi, trả lời ngay (202 + mã việc).
- Mỗi việc có **bản ghi trạng thái** (`queued → running → completed / failed`) giữ có hạn (ví dụ 1 ngày).
- Xong → **thông báo tức thì** tới đúng người qua kênh riêng (WebSocket), ở trang nào cũng nhận.
- Lỡ thông báo (tải lại trang, mất mạng) → khi kết nối lại, giao diện **hỏi trạng thái** các việc nó đang chờ.
- Một khung dùng chung: lớp việc nền cơ sở + bộ theo dõi trạng thái + bộ nghe thông báo gắn sẵn trong layout. Thêm việc mới chỉ viết phần xử lý.

## Checklist

- [ ] Thử lại có giới hạn (ví dụ 3 lần, chờ 5 s rồi 30 s).
- [ ] Lỗi mà thử lại cũng không sửa được (dữ liệu sai) → **lỗi ngay**, không thử lại, kèm câu cho người dùng.
- [ ] Hết lượt thử → vào danh sách việc lỗi (xem / chạy lại / bỏ được); người dùng chỉ thấy câu chung, chi tiết ở log.
- [ ] Mỗi người chỉ nghe được kênh của mình (server ký quyền nghe kênh; nghe kênh người khác → 403).
- [ ] Chỉ xem được trạng thái việc **của mình** (người khác / hết hạn → 404).
- [ ] Mở nhiều tab → tab nào cũng nhận.
- [ ] Việc đẩy **sau khi** giao dịch commit ([[Giao dịch, lỗi và phản hồi API]]).
- [ ] Việc chạy lặp lại an toàn (idempotent) — có thể bị chạy hai lần.
- [ ] Sửa code việc nền → khởi động lại worker (worker giữ code cũ trong bộ nhớ).
- [ ] Việc hẹn giờ (dọn dẹp, báo cáo) chạy bằng một bộ lập lịch, không cron rải rác.

## Theo dõi (lỗi im lặng là nguy hiểm nhất)

- [ ] Cảnh báo khi một việc **chờ quá lâu** trong hàng đợi (ví dụ > 5 phút) — worker dừng thì người dùng chỉ thấy "đang xử lý" mãi, không ai biết.
- [ ] Cảnh báo khi có việc lỗi mới.
- [ ] Biết hậu quả nếu hàng đợi dừng lâu (ví dụ phần upload chờ ghép bị kho xoá sau 24 giờ → file hỏng).

## Bẫy

- Gửi chi tiết lỗi (stack, SQL) ra màn hình qua thông báo.
- Lưu danh sách việc đang chờ ở bộ nhớ trang → tải lại là mất; lưu ở bộ nhớ của tab.
- Không có trang / nút thử việc nền → khó kiểm cả luồng; một nút "chạy việc thử" (và "việc thử lỗi") cho người có quyền giúp kiểm nhanh.
