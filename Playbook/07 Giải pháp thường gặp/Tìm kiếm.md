---
chu_de: tim-kiem
ap_dung: [back-end, front-end]
muc_do: nen-co
cap_nhat: 2026-10-26
---
# Tìm kiếm

Nền: [[Search]].

## Lựa chọn của tôi

- Bắt đầu bằng **tìm toàn văn của DB** (full-text + so độ giống chữ). Chỉ thêm search engine riêng khi DB thật sự không đủ (dữ liệu rất lớn, cần gợi ý phức tạp) — mỗi hệ thống thêm là một thứ phải đồng bộ, sao lưu, giám sát.
- Hai bước: **chính xác trước**, **gần đúng** chỉ khi bước chính xác không ra gì — và nói rõ cho người dùng ("Không có kết quả khớp — đang hiện kết quả gần giống").
- Một cách tìm dùng chung cho mọi ô tìm kiếm (trang quản trị, trang công khai), khác nhau ở **phạm vi dữ liệu được thấy**.

## Cách tìm

| Bước | Chi tiết |
|---|---|
| Kiểm từ khoá | Dài 2–100 ký tự, có ít nhất một chữ hoặc số (`!!` → 422; giao diện nhắc, không gọi API) |
| Tách từ | Chỉ giữ chữ và số |
| Chuẩn hoá | **Bỏ dấu cả hai phía** (gõ không dấu vẫn ra), không phân biệt hoa thường |
| Chính xác | Toàn văn; từ cuối hiểu là đầu từ (gõ dở vẫn ra) |
| Gần đúng | So độ giống chữ (trigram) — gõ sai một hai chữ vẫn ra |
| Xếp hạng | Khớp tiêu đề / tên > tóm tắt > nội dung; tên ngắn trước khi bằng điểm |
| Giới hạn | Số kết quả mỗi nhóm, độ dài đoạn trích |

## Checklist

- [ ] Kết quả **tôn trọng mọi bộ lọc quyền**: riêng tư, nằm dưới mục riêng tư, thùng rác, đang tải lên dở, thư mục bị khoá theo phòng ban, không có quyền xem loại đó → không bao giờ ra.
- [ ] Index phù hợp (cột tìm kiếm tự tính + index toàn văn; index trigram chỉ trên dòng chưa xoá).
- [ ] Ô tìm: tự tìm sau khi ngừng gõ ~0,35 s; gõ tiếp → **huỷ lần tìm cũ**, chỉ hiện kết quả mới nhất; Enter → trang kết quả đầy đủ; Esc / bấm ra ngoài → đóng.
- [ ] Danh sách gợi ý dưới ô và trang kết quả dùng **cùng một nguồn** → luôn giống nhau.
- [ ] Từ khoá giữ trên URL; tô đậm từ khớp bằng cùng cách so như server.
- [ ] Không có JavaScript → ô vẫn là form thường.
- [ ] Ký tự đặc biệt của truy vấn (`%`, `_`) được escape ([[Validate - Tham số danh sách]]).

## Bẫy

- Tìm "có kết quả" là chưa đủ để test: cần hai dòng cạnh tranh và kiểm **đúng thứ tự** xếp hạng.
- Chuẩn hoá dấu ở server nhưng tô đậm ở client không chuẩn hoá → khớp mà không tô.
- Chạy tìm gần đúng song song với chính xác → kết quả rác lẫn vào kết quả đúng.
