---
loai: nguon-goc
cap_nhat: 2026-10-26
---
# Ý tưởng, mong đợi và đắn đo

Ghi lại vì sao có Playbook, mình đã nghĩ gì, phân vân gì, và Claude đã làm gì — để sau này đọc lại hiểu được lý do của từng quyết định.

## Ý tưởng qua từng bước

| Bước | Mình nghĩ | Kết quả |
|---|---|---|
| 1 | Front-end và back-end dùng chung nhiều cấu hình (validate, enum, message, hằng số). Gom về một file JSON, lúc build sinh code cho từng công nghệ (FormRequest, zod), không tải JSON lúc chạy. Cùng với đó làm sẵn common: giao dịch, ngoại lệ, khung phản hồi, middleware. | Claude phân tích trên Second Memory → [[Bản nháp 1 - Dùng chung giữa FE và BE]] |
| 2 | Không hẳn vậy. Sinh code qua nhiều công nghệ thì rủi ro, không bền. Cái mình cần là **quy chuẩn và kinh nghiệm**: ví dụ email phải kiểm tra cú pháp, độ dài, trùng trong DB, tên miền có máy chủ thư… để khi nhờ AI thì nó không sót. | Claude đề xuất cấu trúc + danh mục ~130 mục → [[Bản nháp 2 - Đề xuất bộ chuẩn]] |
| 3 | Bộ chuẩn để trong **Obsidian**, không trong source. Tách rõ tri thức lâu dài với tài liệu riêng của từng dự án. Tài liệu dự án để ở máy, không commit, xoá dự án không tiếc. AI cần thống kê việc mình làm để viết CV. | Bộ Playbook bản đầu + [[Kế hoạch triển khai]] |
| 4 | Viết **chung, không phụ thuộc công nghệ**: mỗi chủ đề nói chuẩn và *lựa chọn của tôi* (ví dụ khung phản hồi API chứa gì; dialog có header cố định, footer chứa nút theo tính năng, body cuộn riêng) — không chép code, không nói riêng Laravel / Next.js. Chuẩn luôn đúng ở mọi dự án; khi bắt đầu dự án thì AI lọc ra phần vừa đủ. AI chỉ **ghi chú**, mình cập nhật **theo đợt** (rẻ hơn làm ở mỗi prompt). Dự án công ty: chỉ học công nghệ, cách làm, sai lầm chung — không ghi gì về công ty hay lỗ hổng. CV: làm khi kết thúc dự án, lọc từ lịch sử. | Viết lại Playbook: bỏ thư mục công nghệ, thêm "Lựa chọn của tôi", thêm `07 Giải pháp thường gặp/` rút từ Second Memory |

| 5 | Mình dùng prompt ngắn, việc chia nhỏ vài ngày, tự dọn tài liệu → model mạnh thường đủ. Cần: một file **đọc đầu tiên** để quyết có cần tri thức cá nhân không; **chốt chặn** ở chỗ AI hay sai dù model mạnh; lập kế hoạch để AI tự quyết; đề xuất thiết lập **theo loại dự án** (mới / cũ cần nâng cấp / đã chuẩn / nặng: quét trước rồi quyết). Không cần giai đoạn thử nghiệm. | [[Mẫu - Quy tắc dự án]], [[Khởi tạo dự án]]; kỹ năng đổi sang "chốt chặn luôn bắt buộc, còn lại mức 0–3" |

## Mong đợi

- Một nguồn **tri thức chuẩn** bù chỗ AI hay sót, nhất là khi ngữ cảnh dài bị loãng hoặc dùng model yếu.
- Nạp **khi cần**, không quét hết → không tốn chi phí, không làm loãng ngữ cảnh.
- **Độc lập công nghệ**: chuẩn chung một nơi, cách làm trên từng công nghệ một nơi khác.
- **Học hai chiều**: kinh nghiệm của mình + kiến thức mới, xu hướng mới AI tìm được. AI đề xuất, mình duyệt.
- Phủ **toàn bộ vòng đời**: tiếp cận tài liệu, môi trường, công cụ (lint, format, quét), git, cách viết code, validate, giao diện, log, test, tài liệu.
- Ưu tiên quy chuẩn phổ biến; dự án nào có đặc thù mới chỉnh cho phù hợp.
- Làm **từ đủ đến tốt**, không muốn thiếu.
- Dùng được ở công ty: copy sang dự án, không ảnh hưởng nhóm.

## Thắc mắc và câu trả lời

| Thắc mắc | Trả lời |
|---|---|
| Có nên sinh code dùng chung FE/BE từ JSON? | Chỉ đáng cho dữ liệu thuần (enum, giới hạn, kiểu dữ liệu API) và khi có một back-end làm nguồn. Luật cần DB / mạng, luật nhiều trường thì viết tay. Với nhiều công nghệ: viết chuẩn thành tài liệu bền hơn sinh code. Chi tiết trong bản nháp 1. |
| JSON hay Markdown cho bộ chuẩn? | Markdown + vài dòng YAML ở đầu file. Dễ viết trong Obsidian, AI đọc tốt, khi cần có thể sinh JSON từ phần đầu. |
| Bộ chuẩn phình to, tốn tài nguyên? | Có mục lục; mỗi file một chủ đề, tối đa ~150 dòng; AI chỉ mở 1–3 file. Xem [[Mẫu - Quy tắc dự án]], [[Duy trì Playbook]]. |
| Tài liệu cũ và mới để đối chiếu? | Kho Obsidian là repo git → lịch sử từng lần sửa đã có, không cần giữ hai bản. |
| Đóng project / phiên Claude thì còn lịch sử không? | **Còn.** Mỗi phiên Claude Code được lưu ở `~/.claude/projects/<thư mục dự án>/<mã phiên>.jsonl` (trong WSL), mở lại được từ danh sách phiên cũ của tiện ích VS Code hoặc `claude --resume`. Bộ nhớ của Claude cho dự án nằm ở `~/.claude/projects/.../memory/`. **Mất:** thư mục tạm (`/tmp/...scratchpad`) khi máy khởi động lại — vì vậy mọi tài liệu đã được chép vào kho này. Lịch sử phiên chỉ nằm trên máy này; đổi máy thì không mang theo. |
| Có thể dùng thông tin từ Second Memory để bổ sung? | Có — các file chuẩn đã rút từ sổ tay quy trình, quy tắc, sự cố của Second Memory (đã khái quát hoá). Xem cột "Nguồn" trong [[Kế hoạch triển khai]]. |

## Đắn đo

| Đắn đo | Cân nhắc | Chốt |
|---|---|---|
| Chi phí khi AI đọc nhiều tài liệu | Đọc mục lục ~1.500 token + 1–3 file ~1.000–2.000 token mỗi file, rẻ hơn rất nhiều so với sửa lỗi bị sót | Việc nhỏ bỏ qua Playbook; việc vừa/lớn mới đọc |
| Dựng common code vs prompt thường | Common code cho thứ lặp ở mọi API và sai thì khó thấy (giao dịch, lỗi, khung phản hồi). Phần phức tạp, dùng một lần: prompt + kiểm tra tay + test | Playbook ghi *cần có gì*, không ép một bộ code cố định |
| AI tự sửa bộ chuẩn | Nhanh nhưng dễ trôi, dễ phình, có thể đưa thông tin công ty vào | AI chỉ ghi một dòng vào [[Ghi chú]]; mình cập nhật theo đợt |
| Tra cứu / cập nhật tri thức mỗi prompt | Kết quả mới hơn nhưng tốn gấp nhiều lần | Theo đợt, từ ghi chú |
| Ghi nhật ký CV sau mỗi việc | Đủ dữ liệu nhưng tốn token mỗi việc; lịch sử git đã có | Làm một lần khi kết thúc dự án |
| Tài liệu nói thẳng một công nghệ | Dễ áp dụng ngay nhưng hết dùng khi đổi công nghệ | Viết chung + "lựa chọn của tôi"; cách làm trên công nghệ cụ thể để AI tự suy ra từ dự án |
| Mẹo trên mạng | Hay không kiểm chứng, không hợp bối cảnh | Chỉ vào chuẩn khi đã dùng thật hoặc có nguồn chính thức |
| Ghi tài liệu dự án vào repo công ty | Làm nhiễu nhóm, lộ cách làm cá nhân | Để trong `.me/`, loại bằng `.git/info/exclude` |

## Claude đã làm gì (2026-10-26)

1. Đọc code Second Memory: middleware giao dịch, bọc phản hồi, bộ xử lý ngoại lệ, FormRequest, enum và giới hạn chép tay ở front-end, file OpenAPI. Viết bản nháp 1.
2. Đọc kho Obsidian (bỏ qua thư mục đời sống cá nhân). Thấy sẵn có checklist kiểm thử, mẫu thiết kế chi tiết, mẫu test case, ghi chú bảo mật. Viết bản nháp 2 với danh mục ~130 mục.
3. Tạo thư mục `Playbook/` này: lớp cách làm việc, tiếp cận dự án, chuẩn chung nền tảng, validate, giao diện, kiểm thử, công nghệ, mẫu. Không sửa note cũ nào, không commit.
4. Viết lại theo bước 4: bỏ `07 Công nghệ/`; thêm "Lựa chọn của tôi" vào các file chuẩn; đổi cách ghi bài học thành ghi chú + đợt cập nhật; CV làm cuối dự án; tạo `.me/` khi bắt đầu dự án kèm bản đồ đường dẫn. Đọc thêm tài liệu tính năng, sự cố, runbook của Second Memory → 10 file giải pháp chung (đăng nhập, phân quyền, nhật ký, upload, thùng rác, việc nền, tìm kiếm, hợp đồng API, vận hành, môi trường dev).

## Còn mở

Xem mục "Việc tiếp theo" trong [[Kế hoạch triển khai]].
