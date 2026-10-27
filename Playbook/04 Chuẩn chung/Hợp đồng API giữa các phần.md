---
chu_de: hop-dong-api
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Hợp đồng API giữa các phần

Server và client (web, trang công khai, app) phải hiểu nhau về: đường dẫn, dữ liệu gửi, dữ liệu nhận, lỗi, enum, giới hạn. Chép tay hai bên là nguồn lệch lớn nhất.

## Lựa chọn của tôi

- **Một nguồn sự thật cho hợp đồng**, các bên còn lại **sinh** ra từ đó; file sinh ra không sửa tay.
- Một server làm nguồn (thường gặp): server sinh **đặc tả API** (theo chuẩn mở) từ chính code → client sinh kiểu dữ liệu từ đặc tả. Nhiều nhóm / nhiều server: viết đặc tả trước rồi sinh cho mọi bên.
- Kiểm tra tự động **"file sinh ra không cũ"**: chạy lại bước sinh, có khác biệt → báo lỗi.
- Đặc tả phải mô tả đúng **khung thật** ([[Giao dịch, lỗi và phản hồi API]]), gồm cả phản hồi lỗi và mã lỗi mà công cụ sinh không tự thấy (403, 404, 429).
- **Không** sinh code dùng chung cho mọi luật validate qua nhiều công nghệ — chỉ dữ liệu thuần (kiểu, enum, giới hạn đơn giản). Luật cần DB / mạng / nhiều trường viết tay ở bên cần nó.

## Các mức (chọn theo dự án)

| Mức | Dùng chung | Chi phí | Khi |
|---|---|---|---|
| 1 | Kiểu dữ liệu request / response | Thấp (công cụ có sẵn) | **Mọi dự án có client riêng** |
| 2 | + Enum, giới hạn đơn giản | Thấp – vừa | Nhiều form, enum hay đổi |
| 3 | + Schema validate phía client sinh từ đặc tả, mở rộng thêm luật riêng | Vừa | > ~10 form |
| 4 | Ngôn ngữ luật validate tự định nghĩa cho mọi bên | Rất cao, bảo trì mãi | **Không nên** |

## Checklist

- [ ] Đổi API → chạy bước sinh, sửa **mọi** client trong cùng một thay đổi.
- [ ] Response trong test được so với đặc tả (bắt lệch hợp đồng sớm).
- [ ] Phá tương thích → đánh dấu rõ (phiên bản API / ghi chú phát hành).
- [ ] Giá trị buộc phải chép tay (không sinh được) → ghi nguồn bên cạnh + test so sánh hai bên.
- [ ] Xung đột git ở file sinh ra → không sửa tay, lấy một bên rồi sinh lại.

## Bẫy

- Ghi chú "tự đồng bộ" trên code thực ra chép tay → mọi người tin là đồng bộ.
- Kiểu lỗi khai báo tay ở client khác hẳn khung thật, không ai dùng → người sau hiểu sai.
- Công cụ sinh không đọc được một số luật (ví dụ độ dài tối thiểu viết bằng đối tượng luật riêng) → đặc tả thiếu; kiểm lại phần quan trọng.
