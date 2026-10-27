---
chu_de: quy-trinh-mot-viec
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Quy trình một việc — từ yêu cầu tới bàn giao

Xương sống cho mọi việc: thứ tự các bước, đầu ra của từng bước, và **chỗ phải dừng chờ duyệt**. Mỗi bước có file riêng; file này chỉ nối chúng lại. Nhóm đã có quy trình → theo nhóm. Lúc [[Khởi tạo dự án]], phần cỡ việc, cổng duyệt và "Luôn hỏi trước" được viết lại vào quy tắc của dự án.

## Lựa chọn của tôi

- Không sửa code khi chưa **nói lại được yêu cầu** và chưa có **tiêu chí "xong"** kiểm được.
- **Cỡ việc quyết định số bước**: nhỏ làm luôn; vừa có kế hoạch vài dòng; lớn có đề xuất + thiết kế + kế hoạch test và chờ duyệt.
- **Ba cổng duyệt**, AI không tự vượt: (1) phương án / thiết kế, (2) test tốn kém, (3) commit / pull request.
- Mỗi bước để lại **đầu ra kiểm được**, không chỉ "đã làm".
- Máy kiểm trước, người / AI đọc sau.
- Đang làm thấy việc lớn hơn dự kiến, hoặc phạm vi đổi → **dừng, báo, nâng cỡ**.

## Các bước

| # | Bước | Đầu ra | Chờ duyệt | File |
|---|---|---|---|---|
| 1 | Nhận yêu cầu | Yêu cầu nói lại 1–3 câu + cỡ | — | [[Nhận và làm rõ yêu cầu]] |
| 2 | Làm rõ | Câu hỏi, giả định, tiêu chí chấp nhận | Khi còn câu hỏi ảnh hưởng kết quả | [[Nhận và làm rõ yêu cầu]] |
| 3 | Phương án | Bảng phương án, một đề xuất | Lớn, hoặc chạm "Luôn hỏi trước" (cổng 1) | [[Nhận và làm rõ yêu cầu]] |
| 4 | Thiết kế | Use case, luồng, ERD, giao diện, API — chỉ loại cần | Lớn (cổng 1) | [[Thiết kế trước khi làm]] |
| 5 | Kế hoạch test | Tiêu chí ↔ test, hồi quy, test tốn kém | Test tốn kém / có tác dụng phụ (cổng 2) | [[Kế hoạch test cho một việc]] |
| 6 | Làm | Code + test + tài liệu, theo quy tắc dự án và chuẩn 04–07 | — | Quy tắc dự án |
| 7 | Kiểm tra | Kết quả lệnh thật; chạy thật trên dev | — | [[Lệnh kiểm tra và thời điểm chạy]] |
| 8 | Đối chiếu | Bảng ✅ ➖ ⏭ ❌, minh chứng, định nghĩa "xong" | — | [[Đối chiếu checklist và minh chứng]] |
| 9 | Review | Báo cáo phát hiện (máy → extension → đọc diff theo mặt) | — | [[Review code - quy trình]] |
| 10 | Commit / pull request | Danh sách file, commit message, mô tả pull request | Luôn (cổng 3) | [[Git và quy trình]] |
| 11 | Phát hành | Ghi chú phát hành, cách quay lui | Theo quy trình nhóm | [[Phát hành và quay lui]] |
| 12 | Dọn, ghi chú, báo cáo | Đã dọn gì; đề xuất tri thức; báo cáo | — | [[Môi trường phát triển và dọn dẹp]], [[Ghi chú và cập nhật theo đợt]] |

## Cỡ việc

| Cỡ | Dấu hiệu | Bước |
|---|---|---|
| Nhỏ | Sửa chữ, đổi giá trị, lỗi rõ nguyên nhân trong 1 file | 1 → 6 → 7 → (9–10 khi commit) → 12 |
| Vừa | Một API / form / tính năng nhỏ, nhiều file | 1–3 và 5 gói trong vài dòng ở câu trả lời → 6–12 |
| Lớn | Nhiều lớp (DB + API + giao diện), chạm "Luôn hỏi trước", nhiều phiên | Đủ các bước; 1–5 viết theo [[Mẫu - Kế hoạch việc]], **chờ duyệt** rồi mới sửa code; việc nhiều phiên ghi tiến độ |

Không chắc → chọn cỡ lớn hơn.

## Luôn hỏi trước

- Đổi cấu trúc DB, đăng nhập, phân quyền, hợp đồng API.
- Thêm thư viện, công cụ, dịch vụ ngoài.
- Xoá / đổi tên / chuyển tính năng, thư mục, dữ liệu không do mình tạo trong việc này.
- Thao tác không quay lui được hoặc ra bên ngoài: commit / push (khi quy tắc dự án chưa cho), deploy, gửi mail, xoá dữ liệu.

## Luồng khác

| Luồng | Khác ở đâu |
|---|---|
| Sửa lỗi | Bước 2 = **tái hiện**: các bước, mong đợi, thực tế, môi trường, dữ liệu. Test fail trước → sửa → pass. Tìm nguyên nhân gốc và chỗ khác cùng mẫu lỗi |
| Sửa gấp production | [[Phát hành và quay lui]], mục sửa gấp |
| Refactor | Không đổi hành vi: có test bảo vệ trước; không trộn tính năng; lỗi gặp được → commit riêng |
| Nâng thư viện / công cụ | Đọc phần phá vỡ trong ghi chú phát hành; nâng một thứ một lần; kiểm tra đầy đủ; luôn qua cổng 1 |
| Chỉ tài liệu | 1 → 6 → kiểm định dạng / liên kết → 10 |
| Điều tra, trả lời câu hỏi | Không sửa code; trả lời kèm vị trí và bằng chứng; việc cần làm → đề xuất riêng |
| Review pull request người khác | Chỉ báo, không sửa ([[Review code - quy trình]]) |
| Sự cố | [[Vận hành và sự cố]] |

## Báo cáo khi xong

| Mục | Nội dung |
|---|---|
| Đã làm | Theo từng tiêu chí chấp nhận: đạt / chưa |
| Đã kiểm | Lệnh thật + kết quả; phần chưa chạy và vì sao |
| Bạn cần tự xem | Trang / khu vực, kích thước màn hình, ngôn ngữ |
| Rủi ro còn lại | Nghi vấn, giả định chưa xác nhận |
| Đã dọn | Dữ liệu thử, file, container |
| Việc tiếp | Lỗi / phần mở rộng thấy được nhưng ngoài phạm vi |

## Bẫy

- Làm ngay theo câu chữ → đúng chữ, sai ý. Hỏi một câu rẻ hơn làm lại.
- Hỏi điều tự tra được → tốn thời gian người giao việc.
- Thiết kế đủ bộ cho việc nhỏ → chậm mà không thêm giá trị.
- Phạm vi phình dần mà không báo → diff to, khó review, khó quay lui.
- Báo "xong" khi chưa chạy kiểm tra, hoặc chỉ dựa vào bảng lỗi của editor.
