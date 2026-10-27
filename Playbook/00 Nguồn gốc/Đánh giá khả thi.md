---
loai: danh-gia
cap_nhat: 2026-10-26
---
# Đánh giá khả thi và hiệu quả

> **Lỗi thời (2026-10-27):** đánh giá theo mô hình cũ (kỹ năng + mức 0–3, dòng "Playbook: …"). Mô hình hiện tại: khởi tạo một lần → quy tắc dự án — xem [[Playbook]]. Chỉ giữ làm lịch sử.

Đánh giá khách quan Playbook ở trạng thái 2026-10-26 (sau khi có [[Mẫu - Quy tắc dự án]] và [[Khởi tạo dự án]]). AI không đọc file này khi làm việc — chỉ để mình tham khảo khi quyết định giữ / sửa.

## Kết luận ngắn

| Mặt | Đánh giá |
|---|---|
| Khả thi | **Cao.** Không cần công cụ mới; một kỹ năng + Markdown. Chi phí mỗi việc: ~150 token khi không dùng, ~1–3 nghìn khi chạm chốt chặn, ~4–8 nghìn ở mức 2–3 |
| Hữu dụng | **Cao ở chốt chặn và "Lựa chọn của tôi"**, thấp ở phần kiến thức phổ biến (git, SOLID, đặt tên) mà model mạnh đã tự làm đúng |
| Tối ưu | **Tốt** nhờ mức 0–3 và đi thẳng tới file; điểm yếu còn lại là phụ thuộc AI tự gọi kỹ năng |
| Không bỏ sót | **Khá**, không tuyệt đối — chốt chặn phủ các chỗ sai đắt nhất; vẫn cần mình xem lại ở quyền, DB, tiền, phát hành |

## Theo loại dự án

| Loại | Khả thi | Hữu dụng | Tối ưu khi | Nguy cơ sót | Chặn bằng |
|---|---|---|---|---|---|
| **A. Mới** | Cao | **Cao nhất** — "Lựa chọn của tôi" thành quy tắc + code dùng chung ngay từ đầu, mọi việc sau nhất quán | Quy tắc đưa **vào dự án** (rules, lint, lớp dùng chung) → các phiên sau gần như không cần mở Playbook | Chuyển lựa chọn sang công nghệ cụ thể sai / thiếu; dựng quá nhiều thứ chưa dùng | Chỉ dựng phần nền tảng; phần "nên có" thêm khi gặp. Mình duyệt bộ quy tắc đầu tiên |
| **B. Cũ cần nâng cấp** | Trung bình – cao | Cao — bảng đối chiếu cho thấy chỗ lệch rõ ràng | Chỉ sửa mục có lợi lớn / công sức nhỏ; còn lại ghi nhận trong `.me/` | Sửa lan rộng, lẫn với việc tính năng; dự án công ty không được tự đổi quy ước | Mỗi mục nâng cấp là việc riêng, commit riêng; công ty → chỉ đề xuất |
| **C. Đã chuẩn** | Cao | Thấp – trung bình — chủ yếu chốt chặn + giải pháp tính năng | `.me/` vài dòng, "Playbook dùng cho" rõ → mức 0–1 cho hầu hết việc | Playbook lặp lại quy tắc sẵn có → tốn token vô ích; hoặc hai bộ quy tắc mâu thuẫn | Quy tắc dự án thắng; liệt kê rõ phần **không** dùng |
| **D. Nặng** | Trung bình | Cao **nếu** quét theo module | Bản đồ theo module, chỉ đào sâu module đang làm | Ảnh hưởng chéo module bị bỏ qua; quét quá rộng tốn token | Bước đánh giá ảnh hưởng ("ai đang gọi thứ mình sửa") bắt buộc với việc chạm module khác; tìm kiếm rộng giao cho tác tử phụ |

## Theo thành phần

| Thành phần | Giá trị | Giữ / sửa |
|---|---|---|
| Chốt chặn ([[Mẫu - Quy tắc dự án]]) | Cao — đúng chỗ model mạnh vẫn sai | Giữ; bổ sung khi gặp điểm mù mới |
| "Lựa chọn của tôi" | Cao — nhất quán giữa dự án | Giữ; **cần mình sửa** cho đúng ý |
| Bẫy trong từng file | Cao — kinh nghiệm thật | Giữ; nguồn chính để Playbook có giá trị hơn kiến thức model |
| Validate theo loại trường | Cao với form / API | Giữ |
| Giải pháp thường gặp | Cao khi làm đúng tính năng đó; bằng 0 lúc khác | Giữ — chỉ mở khi cần nên không tốn |
| Luồng làm việc, kế hoạch | Thấp với model mạnh | Giữ làm tham khảo; AI tự quyết |
| Git, thiết kế code, đặt tên | Thấp với model mạnh | Giữ cho mình học; AI ít khi cần mở |
| `.me/` | Cao với dự án dài | Giữ; ngắn theo loại |
| Ghi chú + đợt cập nhật | Phụ thuộc mình có chạy đợt hay không | Giữ — đây là đường để Playbook tốt dần |
| Thống kê CV cuối dự án | Cao, chi phí một lần | Giữ |

## Điều kiện để hiệu quả thật

1. Mình sửa "Lựa chọn của tôi" ở các file chính — trước đó AI áp lựa chọn do Claude soạn.
2. Để ý dòng "Playbook: …" đầu câu trả lời; thiếu ở việc chạm chốt chặn → nhắc.
3. Chạy đợt cập nhật đều — không thì ghi chú chất đống, Playbook đứng yên.
4. Vẫn tự xem lại kết quả ở quyền, DB, tiền, phát hành.

## Đã vá khi đánh giá

- Bảng chốt chặn thiếu: **gửi ra bên ngoài** (mail, webhook, API bên thứ ba), **việc nền / hẹn giờ**, **dữ liệu riêng tư lộ ra trang công khai / tìm kiếm / xuất file** → đã thêm.
- Thêm [[Khởi tạo dự án]]: tự đánh giá gói AI, công cụ, model, loại việc, cách prompt, phạm vi, điểm mù.
