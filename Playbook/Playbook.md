---
loai: tong-quan
cap_nhat: 2026-10-27
---
# Playbook — tri thức cá nhân về quy trình phát triển phần mềm

Kinh nghiệm của riêng mình, viết thành chuẩn **không phụ thuộc ngôn ngữ, framework, editor hay công cụ AI**. Mỗi chủ đề nói *cần đạt gì* và *lựa chọn của tôi*. Kho chỉ có tài liệu và liên kết, không có code, script hay cấu hình để chép.

## Năm nguyên tắc

1. **Tham khảo, không phải luật.** Mỗi dự án có đặc thù riêng. Làm ở dự án nào thì dùng **tài liệu, quy tắc, công cụ của dự án đó**; Playbook chỉ để đối chiếu lúc khởi tạo và khi việc chạm chốt chặn. Thứ tự: yêu cầu của mình lúc làm > quy ước nhóm / repo > quy tắc riêng `.me/` của dự án > Playbook > thói quen của AI.
2. **Khởi tạo một lần, rồi dự án tự đủ.** AI đọc đúng phần cần, đối chiếu với dự án thật, rồi **tự viết** quy tắc, checklist, tài liệu, lệnh / kỹ năng, hook cho riêng dự án ([[Khởi tạo dự án]]). Sau đó làm việc chỉ với bộ của dự án; **không quy tắc, lệnh hay hook nào của dự án đọc lại Playbook**. Playbook là bản chuẩn đầy đủ; không phải mọi thứ trong đó hợp với mọi dự án, nên dự án chỉ giữ phần hợp, viết lại theo đặc thù của nó. Cách chọn: [[Bộ quy tắc và tài liệu tối thiểu]].
3. **Riêng từng dự án.** Mọi thứ khởi tạo nằm trong `.me/` và file cá nhân của chính dự án đó: không commit, không cài global, không ảnh hưởng dự án khác.
4. **Chỉ mở Playbook khi**: khởi tạo, đợt cập nhật, hoặc mình yêu cầu tham khảo. Khi mở: chỉ file có trong [[Mục lục cho AI]], đúng phạm vi.
5. **Cập nhật theo đợt.** Trong lúc làm chỉ ghi một dòng đề xuất; mình sửa Playbook khi kết thúc dự án, định kỳ, hoặc khi yêu cầu ([[Ghi chú và cập nhật theo đợt]]).

## Luồng

```
 Khởi tạo dự án (01) ── một lần mỗi dự án trên mỗi máy
        │  quy tắc · bản đồ · tài liệu · lệnh · hook riêng của dự án
        ▼
 Mỗi việc (02):  nhận → làm rõ → phương án / thiết kế → kế hoạch test
                 → làm (theo chuẩn 04–07) → kiểm tra → đối chiếu
                 → review (03) → commit / pull request → phát hành → vận hành
        │  học được điều dùng lại được → một dòng đề xuất tri thức
        ▼
 Định kỳ / kết thúc dự án (09): đợt cập nhật Playbook · thống kê CV
```

| Thư mục | Nội dung | AI mở khi |
|---|---|---|
| `01 Khởi tạo dự án/` | [[Khởi tạo dự án]], [[Bộ quy tắc và tài liệu tối thiểu]], [[Công cụ và hook của dự án]] | Khởi tạo |
| `02 Quy trình một việc/` | [[Quy trình một việc]] (xương sống) và từng bước | Khởi tạo → quy trình, lệnh / kỹ năng "làm một việc" của dự án |
| `03 Review code/` | [[Review code - quy trình]] + một checklist mỗi mặt | Khởi tạo → lệnh review + checklist review của dự án |
| `04 Chuẩn chung/` | Thiết kế, quy ước, lỗi / API, log, bảo mật, DB, công cụ, test, tài liệu, môi trường | Khởi tạo → quy tắc theo chủ đề của dự án |
| `05 Validate/` → `07 Giải pháp thường gặp/` | Checklist theo loại dữ liệu, giao diện, tính năng hay gặp | Khởi tạo → checklist chốt chặn của dự án (chỉ phần dự án có) |
| `08 Mẫu/` | Đề mục cho quy tắc, bản đồ, kế hoạch, minh chứng, pull request, CV | Khi cần mẫu |
| `09 Định kỳ/` | Đợt cập nhật tri thức, thống kê CV | Khi mình yêu cầu |
| `00 Nguồn gốc/`, `Hộp đề xuất/` | Bản nháp lưu trữ; ghi chú chờ cập nhật | AI không đọc (`Hộp đề xuất/` chỉ trong đợt cập nhật) |

## Ba lớp tài liệu

| Lớp | Ở đâu | Ai sửa | Dùng khi |
|---|---|---|---|
| Tài liệu của nhóm | Repo dự án | Theo quy trình nhóm | Luôn thắng |
| Tài liệu riêng của dự án | `.me/` trong thư mục dự án, như `node_modules`: không commit vào bất kỳ repo nào | AI tạo lúc khởi tạo, cập nhật khi code đổi; mình xác nhận | Hầu hết mọi việc |
| Tri thức cá nhân (bản chuẩn) | Kho này, AI đọc tại chỗ | Mình, theo đợt | Khởi tạo; đợt cập nhật; khi mình yêu cầu |

## Câu lệnh

| Khi | Dán |
|---|---|
| Khởi tạo | *Tri thức cá nhân ở `<đường dẫn>/Playbook`. Đọc `01 Khởi tạo dự án/Khởi tạo dự án.md` và khởi tạo cho dự án này. Chưa sửa code.* |
| Dự án gặp chủ đề mà bộ riêng chưa có | *Tham khảo tri thức cá nhân về `<chủ đề>` và bổ sung vào bộ của dự án.* |
| Tri thức đổi, muốn áp vào dự án | *So quy tắc và hook của dự án với `<đường dẫn>/Playbook/<file>`, đề xuất phần cần sửa.* |
| Được báo tài liệu dự án lệch | *Cập nhật tài liệu theo báo cáo lệch.* (hoặc bỏ qua) |
| Kết thúc dự án / định kỳ | *Chạy đợt cập nhật tri thức theo `<đường dẫn>/Playbook/09 Định kỳ/Ghi chú và cập nhật theo đợt.md`.* |
| Mỗi tháng / trước khi rời dự án | *Tổng hợp CV theo `<đường dẫn>/Playbook/09 Định kỳ/Thống kê công việc cho CV.md`.* |

Ở dự án công ty chỉ học **công nghệ, cách làm, giải pháp, sai lầm chung**: không ghi gì về công ty, hệ thống hay lỗ hổng của họ. Cách viết và giữ kho gọn: [[Duy trì Playbook]].
