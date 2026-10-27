---
chu_de: cong-cu-hook-du-an
ap_dung: [du-an-moi, du-an-cu, doi-cong-cu]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Công cụ và hook của dự án

Danh sách **khả năng** mà mỗi dự án nên có sau khi khởi tạo. File này chỉ nói *cần đạt gì*; AI ở dự án tự hiện thực (script, hook, lệnh, kỹ năng, cấu hình) theo **công cụ AI, editor, hệ điều hành và ngôn ngữ của dự án** mà nó kiểm được lúc [[Khởi tạo dự án]]. Mọi thứ đặt **trong phạm vi dự án**, không cài global, không commit vào repo nhóm (dùng cơ chế loại trừ cục bộ của git, ví dụ `.git/info/exclude`). Mỗi dự án một bộ riêng, không ảnh hưởng dự án khác.

Thiết lập **một lần lúc khởi tạo** để làm việc chuẩn chỉ từ đầu; sau đó hook tự chạy, AI không tạo lại hay đổi hook trong lúc làm việc — chỉ khi mình yêu cầu.

## Bảng khả năng

| # | Khả năng | Để làm gì | Công cụ AI không hỗ trợ cơ chế tương ứng thì |
|---|---|---|---|
| 1 | **Điểm vào tự nạp** | Mỗi phiên AI tự đọc quy tắc của dự án mà mình không phải nhắc | Mình dán một câu "đọc `.me/` …" đầu phiên |
| 2 | **Kiểm tra sau mỗi lần AI sửa file** | Định dạng (chỉ khi file vốn đã sạch), lint file đó, quét dòng thêm; lỗi trả ngược cho AI sửa ngay trong lượt | AI chạy lệnh kiểm tra trước khi báo xong |
| 3 | **Review trước commit** | Lệnh / kỹ năng chạy [[Review trước commit]] | Mình dán "review theo `Review trước commit`" |
| 4 | **Git hook trước commit / push** | Trước commit: kiểm nhanh phần đã stage; commit message đúng quy ước; trước push: test / kiểu / thừa cho phần bị ảnh hưởng | — (hook của git, không phụ thuộc công cụ AI) |
| 5 | **Kiểm tra lệch tài liệu** | Đầu phiên: code mới có làm tài liệu nào cũ đi không (chi tiết bên dưới) | Mình nói "kiểm tra tài liệu" |

Không phải dự án nào cũng cần đủ: dự án không có công cụ định dạng / lint thì bỏ #2; nhóm đã có hook thì #4 chỉ **nối thêm** sau hook của nhóm, không thay.

## Yêu cầu chung

- **Mình duyệt trước khi tạo**: AI liệt kê sẽ tạo gì, ở đâu, chạy khi nào.
- Chỉ đọc cấu hình của dự án; công cụ không có cấu hình trong dự án → bỏ bước đó và nói SKIP, không áp cấu hình riêng.
- **Mỗi việc kiểm một lần**: nhớ kết quả theo nội dung file + cấu hình công cụ; bước sau không kiểm lại phần đã qua.
- Kiểm theo **phần thay đổi**: dòng thêm, file thay đổi, phần (package / module) bị ảnh hưởng và phần phụ thuộc vào nó.
- Hook **không bao giờ chặn phiên AI** vì chính nó lỗi: lỗi của hook → in cảnh báo, cho đi tiếp.
- Hook không có gì để báo → **không in gì** (không tốn ngữ cảnh).
- Có cách tắt một lần (biến môi trường, `--no-verify`) và cách gỡ hẳn; ghi cả hai vào quy tắc dự án.
- Ghi vào quy tắc dự án: đã tạo gì, ở đâu, lệnh chạy tay.

## Kiểm tra lệch tài liệu

**Mục tiêu:** tài liệu dự án luôn khớp code mà không tốn công đọc lại cả dự án.

1. **Bản đồ** (`.me/` — mục "Tài liệu ↔ code" theo [[Mẫu - Bản đồ dự án]]): mỗi dòng nối tài liệu với thư mục / file code.
2. **Mốc đồng bộ**: lưu commit của nhánh chính và của HEAD tại lần cuối tài liệu được xác nhận là khớp, cùng tên nhánh chính để so (thường là nhánh chính trên remote).
3. **Đầu phiên** (hoặc khi mình yêu cầu): fetch nhánh chính (không hỏi mật khẩu, có thời hạn; lỗi mạng → dùng bản local và nói vậy) → lấy các commit trên nhánh chính **và** nhánh hiện tại chưa nằm trong mốc → danh sách file đổi (kể cả file bị xoá) → đối chiếu bản đồ.
4. **Báo** (chỉ khi có): số commit; tài liệu cần xem lại ← file code đã đổi; file code đổi mà bản đồ chưa có. Bỏ qua file trong `.me/`, lockfile.
5. **Mình đồng ý** → AI đọc thay đổi của đúng các file được báo, sửa tài liệu, cho mình xem → mình xác nhận → **đặt mốc mới**. Không đồng ý → giữ mốc cũ, lần sau báo lại.

Trong lúc làm tính năng, AI sửa tài liệu liên quan luôn trong việc đó; mốc chỉ dời khi mình xác nhận.

## Làm lại / cập nhật — chỉ khi mình yêu cầu

- Đổi công cụ AI hoặc editor → hiện thực lại #1, #2, #3, #5 theo cơ chế mới.
- Khả năng trong file này đổi (sau đợt cập nhật tri thức) → bảo AI ở dự án so quy tắc + hook hiện có với file này và đề xuất phần cần sửa.
- Kết thúc dự án → gỡ git hook (#4) trước, rồi xoá các file đã tạo.
