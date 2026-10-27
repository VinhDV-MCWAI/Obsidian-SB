---
chu_de: ghi-chu-cap-nhat
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Ghi chú và cập nhật theo đợt

**Lựa chọn của tôi:** trong lúc làm việc, AI chỉ **ghi chú một dòng** trong dự án, không tra cứu, không sửa tri thức cá nhân. Mình cập nhật tri thức **theo đợt** (khi kết thúc dự án, hoặc khi mình yêu cầu — ví dụ ghi chú đã nhiều) từ các ghi chú đó. Lý do: tra cứu + tổng hợp + sửa tài liệu ở mỗi prompt tốn gấp nhiều lần làm một lần cho cả đợt, mà chất lượng không hơn.

## 1. Trong lúc làm: ghi một dòng

Gặp điều **học được** — công nghệ, cách triển khai, giải pháp, sai lầm hay gặp — AI thêm **một dòng** vào cuối khối **Đề xuất tri thức** trong `.me/` của dự án:

```
- 2026-10-26 | validate | Kiểm trùng ở code mà thiếu unique index → hai request cùng lúc vẫn tạo trùng | 04 Validate/Validate - Email | thực tế
```

`ngày | chủ đề | điều học được (đã khái quát) | file tri thức liên quan | nguồn: thực tế / tài liệu chính thức / chưa kiểm chứng`

- Chỉ **thêm cuối file**, không đọc lại cả file, không mở tri thức cá nhân để so.
- Không tra cứu internet ở bước này. Một dòng ≤ ~200 ký tự.
- Chủ đề `danh-gia`: dòng trong bảng "Tham khảo tri thức cá nhân" của dự án **không giúp gì** (model đã tự làm đúng, hoặc checklist không bắt được gì) hoặc **thiếu** (sai ở chỗ tri thức chưa nói). Đây là dữ liệu để cắt / bổ sung tri thức dựa trên thực tế, không dựa trên cảm giác.

Ghi tay khi không ở trong dự án nào → [[Ghi chú]] trong `Hộp đề xuất/`, cùng định dạng.

## 2. Ghi vào đâu

| Điều gặp | Ví dụ | Ghi vào |
|---|---|---|
| Học được, áp dụng được ở dự án khác | Cách chia phần khi upload file lớn; bẫy khi cache cấu hình; sai lầm khi kiểm quyền | Đề xuất tri thức |
| Cách dự án **này** làm, bẫy riêng, lỗi hay mắc | "Dự án dùng mã trạng thái dạng chuỗi"; "lệnh test là …" | Ghi chú dự án (quy tắc thì sửa khối Quy tắc) |
| Thông tin công ty / khách hàng | Tên, cấu trúc hệ thống, số liệu, lỗ hổng | **Không ghi ở đâu cả** |

## 3. Khái quát hoá — bắt buộc, nhất là dự án công ty

Mục đích là học **công nghệ, cách triển khai, giải pháp, sai lầm chung** — không phải ghi lại công ty đó làm gì.

Không bao giờ ghi:
- Tên công ty, khách hàng, sản phẩm, người, dự án.
- Cấu trúc hệ thống, tên bảng / cột / máy chủ / URL / mã nội bộ.
- **Lỗ hổng bảo mật hay điểm yếu của hệ thống công ty** — kể cả đã sửa. Chỉ ghi dạng chung: "kiểm quyền theo từng bản ghi khi id nằm trên URL", không ghi ở đâu từng thiếu.
- Code nguyên văn, cấu hình, khoá, tài khoản.
- Số liệu kinh doanh.

Kiểm nhanh: "Người ngoài đọc dòng này có đoán ra công ty / hệ thống nào không?" — có thì viết lại.

## 4. Báo khi có điều đáng cập nhật

Cuối câu trả lời của một việc, nếu vừa thêm ghi chú **thực sự hữu ích** (không phải mọi lần), AI nói một dòng: *"Đã thêm 2 đề xuất tri thức (upload file lớn, cache cấu hình)."*

## 5. Đợt cập nhật (mình khởi động — khi kết thúc dự án, hoặc khi mình yêu cầu)

Câu lệnh (trong dự án): *"Chạy đợt cập nhật tri thức theo `<đường dẫn>/Playbook/01 Cách làm việc/Ghi chú và cập nhật theo đợt.md`."* Nhiều dự án: chạy trong kho tri thức và đưa đường dẫn các file đề xuất.

1. AI đọc các dòng đề xuất (và [[Ghi chú]] nếu có), gom theo file đích.
2. Với mỗi file đích: đọc file, so với đề xuất. Trùng → bỏ. Mâu thuẫn → nêu ra cho mình chọn.
3. Dòng `danh-gia`: một mục bị đánh giá "không giúp gì" ở **từ hai dự án** → đề xuất hạ xuống "Bẫy" hoặc xoá; mục "thiếu" → thêm vào checklist / bẫy.
4. Chỉ khi cần (công cụ / chuẩn có thể đã đổi): tra **nguồn chính thức**, ghi nguồn + ngày.
5. Sửa file đích theo khung ở [[Duy trì Playbook]] (giữ ngắn).
6. Xoá các dòng đã xử lý khỏi file đề xuất.
7. Báo số đo của đợt: số dòng đề xuất đã xử lý, bao nhiêu dòng nguồn `thực tế` đã vào Playbook, file nào được thêm Bẫy / checklist, mục nào bị hạ hoặc xoá.
8. Mình xem `git diff` trong kho tri thức, sửa, commit (theo quy ước commit ở [[Duy trì Playbook]]), push. Dự án nào nên nhận thay đổi → mình bảo AI ở dự án đó so quy tắc / hook với file đã đổi và đề xuất.
