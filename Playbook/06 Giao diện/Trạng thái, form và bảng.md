---
chu_de: trang-thai-form-bang
ap_dung: [front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Trạng thái, form và bảng dữ liệu

## Lựa chọn của tôi

- Mọi vùng dữ liệu có đủ **bốn trạng thái**: đang tải, rỗng, lỗi, có dữ liệu.
- Form: một thư viện form + một schema validate cho cả ứng dụng; nút gửi khoá khi đang gửi; lỗi giữ nguyên dữ liệu.
- Bảng: một thành phần bảng dùng chung; phân trang / sắp xếp / lọc ở server; trạng thái nằm trên URL.
- Thao tác hàng loạt luôn có xác nhận kèm số lượng.

## Bốn trạng thái của mọi vùng dữ liệu

| Trạng thái | Hiển thị |
|---|---|
| Đang tải | Khung xương (skeleton) giữ đúng bố cục; không nhảy trang |
| Rỗng | Câu giải thích + hành động ("Chưa có bài viết. [Tạo bài viết]"); rỗng do lọc → "Không có kết quả. [Xoá bộ lọc]" |
| Lỗi | Câu lỗi + "Thử lại" |
| Có dữ liệu | Bình thường; dữ liệu **quá dài** được cắt + tooltip / xuống dòng, không vỡ bố cục |

## Form

- [ ] Nhãn rõ cho mọi ô; dấu `*` cho ô bắt buộc (giải thích một lần).
- [ ] Gợi ý định dạng / giới hạn trước khi người dùng sai ("Tối đa 50 ký tự", bộ đếm ký tự).
- [ ] Kiểm khi rời ô (blur) và khi gửi; không báo lỗi khi người dùng chưa gõ xong.
- [ ] Nút gửi khoá khi đang gửi; **bấm nhiều lần chỉ gửi một lần**.
- [ ] Lỗi → giữ nguyên dữ liệu đã nhập.
- [ ] `Enter` gửi form (trừ ô nhiều dòng).
- [ ] Kiểu ô đúng (`type="email"`, `inputmode="numeric"`, `autocomplete`) — điện thoại hiện bàn phím phù hợp.
- [ ] Giá trị mặc định hợp lý; sửa → điền sẵn dữ liệu hiện tại.
- [ ] Có thay đổi chưa lưu → xác nhận khi rời ([[Dialog và hộp xác nhận]]).

## Bảng dữ liệu

- [ ] Phân trang, sắp xếp, lọc, tìm kiếm phía server; trạng thái giữ trên URL.
- [ ] Bấm sắp xếp / phân trang / tìm liên tục, kể cả khi đang tải → không lỗi, không hiển thị kết quả cũ đè kết quả mới.
- [ ] Cột chứa dữ liệu quá dài, không có dữ liệu, ký tự đặc biệt.
- [ ] Thao tác hàng loạt: chọn tất cả (trang này / mọi trang — nói rõ), xác nhận kèm số lượng, kết quả từng dòng nếu lỗi một phần.
- [ ] Kéo thả sắp xếp: thả ra ngoài, kéo nhanh nhiều lần, hai người cùng sắp xếp.
- [ ] Hàng có thao tác: nút hành động nhất quán vị trí (cuối hàng / menu ba chấm).
- [ ] Điện thoại: cuộn ngang hoặc chuyển thành thẻ.

## Bố cục trang

Xem [[Bố cục, màu và truy cập dễ dàng]].

## Đồng thời và quyền (kiểm khi thiết kế)

Lấy từ [[Testing]]: hai tab / hai người sửa cùng bản ghi; A sửa, B xoá; quyền đổi trong lúc đang thao tác; hết phiên giữa chừng; tài khoản bị khoá giữa chừng. Mỗi trường hợp phải có hành vi rõ (thông báo, tải lại, chặn).
