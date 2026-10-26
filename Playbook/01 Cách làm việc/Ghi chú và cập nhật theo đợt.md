---
chu_de: ghi-chu-cap-nhat
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Ghi chú và cập nhật theo đợt

**Lựa chọn của tôi:** trong lúc làm việc, AI chỉ **ghi chú ngắn**, không tra cứu, không sửa Playbook. Mình cập nhật Playbook **theo đợt** từ các ghi chú đó. Lý do: tra cứu + tổng hợp + sửa tài liệu ở mỗi prompt tốn chi phí gấp nhiều lần làm một lần cho cả đợt, mà chất lượng không hơn.

## 1. Trong lúc làm: ghi một dòng

Khi gặp điều **học được** — công nghệ, cách triển khai, giải pháp, sai lầm hay gặp — AI thêm **một dòng** vào cuối `Hộp đề xuất/Ghi chú.md`:

```
- 2026-10-26 | validate | Kiểm trùng ở code mà thiếu unique index → hai request cùng lúc vẫn tạo trùng | 04 Validate/Validate - Email | thực tế
```

`ngày | chủ đề | điều học được (đã khái quát) | file Playbook liên quan | nguồn: thực tế / tài liệu chính thức / chưa kiểm chứng`

- Chỉ **thêm cuối file**, không đọc lại cả file, không mở file chuẩn để so.
- Không tra cứu internet ở bước này.
- Một dòng ≤ ~200 ký tự. Cần dài hơn → viết ngắn, chi tiết để đợt cập nhật.

## 2. Ghi vào đâu

| Điều gặp | Ví dụ | Ghi vào |
|---|---|---|
| Học được, áp dụng được ở dự án khác | Cách chia phần khi upload file lớn; bẫy khi cache cấu hình; sai lầm khi kiểm quyền | `Hộp đề xuất/Ghi chú.md` |
| Cách dự án **này** làm, đường dẫn, quy ước riêng | "Dự án dùng mã trạng thái dạng chuỗi"; "lệnh test là …" | `.me/` của dự án |
| Thông tin công ty / khách hàng | Tên, cấu trúc hệ thống, số liệu, lỗ hổng | **Không ghi ở đâu cả** |

Chuẩn chung luôn đúng ở mọi dự án. Khi bắt đầu một dự án, AI **lọc** Playbook ra phần vừa đủ cho dự án đó ([[Checklist tiếp cận dự án]]) — không phải phân loại lại chuẩn.

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

Cuối câu trả lời của một việc, nếu vừa thêm ghi chú **thực sự hữu ích** (không phải mọi lần), AI nói một dòng:

> "Đã thêm 2 ghi chú cho Playbook (upload file lớn, cache cấu hình). Nên cập nhật theo đợt và push kho Obsidian khi tiện."

Dự án cá nhân: nhắc thêm nếu tài liệu của chính dự án nên cập nhật và push.

## 5. Đợt cập nhật (mình khởi động — mỗi tháng, hoặc khi kết thúc dự án)

Câu lệnh gợi ý: *"Chạy đợt cập nhật Playbook từ Hộp đề xuất/Ghi chú.md."*

1. AI đọc `Ghi chú.md`, gom theo file đích.
2. Với mỗi file đích: đọc file, so với ghi chú. Trùng → bỏ. Mâu thuẫn → nêu ra cho mình chọn.
3. Chỉ khi cần (công cụ / chuẩn có thể đã đổi): tra **nguồn chính thức**, ghi nguồn + ngày.
4. Sửa file đích theo khung ở [[Duy trì Playbook]] (giữ ngắn; thêm vào checklist / bẫy / lựa chọn của tôi).
5. Xoá các dòng đã xử lý khỏi `Ghi chú.md`.
6. Mình xem thay đổi (`git diff`) trong kho Obsidian, sửa, commit, push.

Trước khi xoá một dự án: chạy đợt cập nhật nếu còn ghi chú từ dự án đó, và [[Thống kê công việc cho CV]].
