---
loai: tong-quan
cap_nhat: 2026-10-27
---
# Playbook — tri thức cá nhân

Kinh nghiệm của riêng mình, viết thành chuẩn **không phụ thuộc ngôn ngữ, framework, editor hay công cụ AI**: mỗi chủ đề nói *cần đạt gì* và *lựa chọn của tôi*. Kho này chỉ có tài liệu và liên kết — không có code, script hay cấu hình để chép.

Đây là **nguồn tham khảo để khởi tạo**, không phải bộ để chép: ở mỗi dự án, AI đọc **đúng phần cần** trong kho, đối chiếu với môi trường và dự án thật, rồi **tự tạo** bộ quy tắc + tài liệu + công cụ cho riêng dự án. Sau đó dự án tự đủ dùng — làm việc như prompt bình thường; tri thức cá nhân chỉ được mở ở những chỗ dự án đã đánh dấu, hoặc khi mình yêu cầu.

## Phạm vi đọc — AI chỉ trích xuất phần cần

Kho Obsidian là kho tri thức chung của mình (có cả ghi chú cá nhân). AI ở dự án **đọc tại chỗ** theo đường dẫn trên máy, **không clone, không chép cả kho vào dự án**, và chỉ được mở:

| Được mở | Khi |
|---|---|
| `Playbook/` (trừ `00 Nguồn gốc/`, `Hộp đề xuất/`) | Khởi tạo; hằng ngày chỉ theo bảng tham khảo của dự án |
| File trong mục "Kiến thức nền" của [[Mục lục cho AI]] | Chỉ khi cần đào sâu đúng chủ đề đó |
| `Hộp đề xuất/` | Chỉ trong đợt cập nhật |

Mọi thư mục / file khác của kho (ghi chú cá nhân, công việc, đời sống, file rời ở gốc) — **không mở, không liệt kê, không tìm kiếm trong đó**. Tìm file thì đi qua [[Mục lục cho AI]], không quét cả kho.

## Đối chiếu tri thức với dự án

| Tình huống | Lấy |
|---|---|
| Hai bên tương đồng | Bên nào cũng được — ưu tiên cách viết sẵn của dự án (khỏi chép) |
| Cùng giá trị, khác cách làm | **Dự án** |
| Dự án chưa có | **Tri thức cá nhân**, viết lại cho công nghệ của dự án |
| Cả hai đều có thể tốt hơn | AI đề xuất phương án tối ưu kèm lý do; mình duyệt rồi mới thành quy tắc dự án |

Kết quả là quy tắc + tài liệu **của dự án**; từ đó AI làm theo quy tắc dự án, không đối chiếu lại mỗi lần.

## Mô hình

```
Kho tri thức (repo này — chỉ tài liệu, AI chỉ đọc phần trong phạm vi)
   │  khởi tạo — một lần mỗi dự án: AI đọc phần cần, kiểm môi trường, đối chiếu, hỏi, tự tạo
   ▼
Dự án/.me/  quy tắc · bản đồ · tài liệu · công cụ và hook riêng   ← dùng cho hầu hết mọi việc
   │  học được điều dùng lại được → một dòng đề xuất tri thức
   ▼
Đợt cập nhật (khi kết thúc dự án / khi mình yêu cầu) → sửa Playbook → commit kho tri thức
```

| Lớp | Ở đâu | Ai sửa |
|---|---|---|
| Tri thức cá nhân | Repo này, nằm sẵn trên máy; AI đọc tại chỗ | Mình, theo đợt |
| Tài liệu riêng của dự án | `.me/` và file cá nhân AI tạo trong dự án: nằm trong thư mục dự án như `node_modules`, không commit vào bất kỳ repo nào | AI tạo khi khởi tạo, cập nhật khi code đổi, mình xác nhận |
| Tài liệu của nhóm | Repo dự án | Theo quy trình nhóm — luôn thắng hai lớp trên |

Không cài gì global: quy tắc, hook, lệnh đều nằm trong thư mục của từng dự án trên máy đó, không commit (mỗi lần clone về thì khởi tạo lại, xem [[Khởi tạo dự án]]), mỗi dự án tuỳ chỉnh riêng, không ảnh hưởng dự án khác. Thiết lập **một lần lúc khởi tạo**; sau đó chỉ đổi khi mình yêu cầu — AI không tự thêm / đổi quy ước hay hook trong lúc làm việc.

## Ba thời điểm

| Khi | Mình làm | AI đọc |
|---|---|---|
| **Khởi tạo** (dự án mới / dự án cũ trên máy mới / nhận lại / đổi công cụ) | Dán câu lệnh bên dưới, kèm đường dẫn kho trên máy | [[Khởi tạo dự án]] → lọc theo [[Mục lục cho AI]] |
| **Hằng ngày** | Prompt như bình thường | Quy tắc của dự án (tự nạp) → bản đồ → đúng file; tri thức cá nhân chỉ theo bảng tham khảo trong quy tắc, hoặc khi mình yêu cầu |
| **Định kỳ / khi yêu cầu** | Ra lệnh | [[Ghi chú và cập nhật theo đợt]], [[Thống kê công việc cho CV]] |

## Câu lệnh

| Khi | Dán |
|---|---|
| Khởi tạo | *Tri thức cá nhân ở `<đường dẫn>/Playbook`. Đọc `02 Tiếp cận dự án mới/Khởi tạo dự án.md` và khởi tạo cho dự án này. Chưa sửa code.* |
| Được báo tài liệu lệch | *Cập nhật tài liệu theo báo cáo lệch.* (hoặc bỏ qua) |
| Muốn tự kiểm tài liệu | *Kiểm tra tài liệu.* |
| Kết thúc dự án / khi muốn | *Chạy đợt cập nhật tri thức theo `<đường dẫn>/Playbook/01 Cách làm việc/Ghi chú và cập nhật theo đợt.md`.* → xem diff, commit kho tri thức |
| Mỗi tháng / trước khi rời dự án | *Tổng hợp CV theo `<đường dẫn>/Playbook/01 Cách làm việc/Thống kê công việc cho CV.md`.* |
| Tri thức có thay đổi đáng áp dụng | *So quy tắc và hook của dự án với `<đường dẫn>/Playbook/<file>`, đề xuất phần cần sửa.* |
| Cần tham khảo chủ đề chưa có trong bảng | *Tham khảo tri thức cá nhân về `<chủ đề>`.* → AI tra mục lục, mở đúng file, đề xuất thêm dòng vào bảng tham khảo |
| Đổi quy tắc / hook | *Sửa quy tắc / hook của dự án: …* |

## Bản đồ thư mục

| Thư mục | Nội dung | Ai đọc, khi nào |
|---|---|---|
| `00 Nguồn gốc/` | Vì sao có bộ này, bản nháp, đắn đo | Lưu trữ — AI không đọc |
| `01 Cách làm việc/` | Review trước commit, đối chiếu checklist, đợt cập nhật, CV | Khi làm việc tương ứng; định kỳ |
| `02 Tiếp cận dự án mới/` | [[Khởi tạo dự án]], [[Công cụ và hook của dự án]] | Khởi tạo |
| `03 Chuẩn chung/` → `07 Giải pháp thường gặp/` | Chuẩn, checklist, lựa chọn của tôi theo chủ đề | Khởi tạo (lọc); hằng ngày chỉ qua bảng tham khảo của dự án |
| `08 Mẫu/` | Đề mục: [[Mẫu - Quy tắc dự án]], [[Mẫu - Bản đồ dự án]], kế hoạch, minh chứng, CV | Khởi tạo, khi cần mẫu |
| `Hộp đề xuất/` | [[Ghi chú]] ghi tay chờ đợt cập nhật | Đợt cập nhật |

Ở dự án công ty chỉ học **công nghệ, cách triển khai, giải pháp, sai lầm chung** — không ghi gì về công ty, hệ thống hay lỗ hổng của họ.

Cách viết và giữ bộ này gọn: [[Duy trì Playbook]]. Playbook là bản đầu — gặp chỗ không đúng ý thì sửa thẳng "Lựa chọn của tôi".
