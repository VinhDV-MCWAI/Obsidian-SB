---
loai: tong-quan
cap_nhat: 2026-10-26
---
# Playbook — bộ chuẩn làm việc cá nhân

Bắt đầu đọc ở đây. Bộ này là **kinh nghiệm của riêng mình**, viết thành quy chuẩn **không phụ thuộc công nghệ**: mỗi chủ đề nói *cần đạt gì* và *lựa chọn của tôi là gì*, không chép code hay nói riêng một framework. Dùng cho mọi dự án; không chia sẻ, không commit vào source của dự án.

## Ba lớp tài liệu

| Lớp | Nằm ở đâu | Sống bao lâu | Chứa gì | Ai sửa |
|---|---|---|---|---|
| **1. Tri thức chuẩn** | Kho Obsidian, `Playbook/` | Mãi mãi | Chuẩn, checklist, lựa chọn của tôi, giải pháp, bẫy — đã khái quát hoá | **Mình**, theo đợt, từ [[Ghi chú]] |
| **2. Tài liệu riêng dự án** | `.me/` trong thư mục dự án, loại bằng `.git/info/exclude` | Bằng tuổi dự án; xoá thoải mái | Lọc **vừa đủ** từ Playbook cho dự án + lựa chọn của dự án + bản đồ đường dẫn tới thành phần | AI tạo khi bắt đầu dự án, cập nhật khi code đổi |
| **3. Tài liệu của nhóm** | Repo dự án | Theo dự án | Theo quy ước nhóm | Theo quy trình nhóm |

Không commit lớp 2 ở bất kỳ dự án nào (công ty hay cá nhân).

**Người mới dùng / đổi gói AI / đổi công cụ:** đọc [[Hướng dẫn sử dụng]] trước — biết mình đang có gì, làm tốt tới đâu, prompt và cô lập phạm vi thế nào. Đánh giá khách quan: [[Đánh giá khả thi]].

## Bắt đầu sử dụng — câu lệnh mẫu

Kỹ năng `playbook` đã cài ở `~/.claude/skills/playbook/` ([[Kết nối Playbook với AI]]). AI đọc [[Khi nào dùng Playbook]] trước, tự quyết có cần tham khảo không (mức 0–3), nhưng **luôn** đối chiếu checklist khi việc chạm chốt chặn (quyền, dữ liệu nhập, DB, tiền, file, lỗi API, phát hành, log nhạy cảm). Đầu câu trả lời có một dòng "Playbook: …" để mình biết AI đã chọn gì.

| Khi | Dán |
|---|---|
| Lần đầu mở / nhận lại một dự án (**model đủ sức**: thường cân bằng, quét bằng tác tử phụ nhanh — xem bậc thang trong [[Hướng dẫn sử dụng]]) | *Dùng playbook: xác định loại dự án theo Thiết lập theo loại dự án (chỉ quét cấu trúc), báo loại + lý do. Sau khi tôi xác nhận: hỏi tôi các câu cần thiết, đề xuất cách đóng gói tài liệu và cách làm việc với dự án này theo mục 'Bước cuối'. Chưa sửa gì.* |
| Mỗi việc | Prompt ngắn như bình thường. Việc dài: *"ghi danh sách bước rồi làm theo"*. |
| Pull code mới sau một thời gian | *Cập nhật `.me/Bản đồ.md` theo các commit mới.* |
| Mỗi tháng | *Chạy đợt cập nhật Playbook từ Hộp đề xuất/Ghi chú.md.* → xem diff, commit, push kho Obsidian |
| Kết thúc dự án | *Tổng hợp CV cuối dự án theo Thống kê công việc cho CV.* → rồi mới xoá `.me/` |

Nhớ: Playbook là **bản đầu** — gặp chỗ không đúng ý thì sửa thẳng "Lựa chọn của tôi" trong Obsidian.

## Cách dùng

| Khi | Làm | File |
|---|---|---|
| Bắt đầu dự án (pull code / khởi tạo) | Một phiên riêng: AI đọc dự án, đối chiếu Playbook, tạo `.me/` | [[Checklist tiếp cận dự án]] |
| Mỗi việc | Nhỏ: làm luôn. Vừa / lớn: `.me/` → mục lục → 1–3 file → kế hoạch → làm → đối chiếu checklist | [[Luồng làm một việc]] |
| Gặp điều học được | AI thêm **một dòng** vào [[Ghi chú]] — không tra cứu, không sửa Playbook | [[Ghi chú và cập nhật theo đợt]] |
| Mỗi tháng / kết thúc dự án | Đợt cập nhật Playbook từ ghi chú; mình xem diff, commit, push | [[Ghi chú và cập nhật theo đợt]] |
| Kết thúc dự án | Tổng hợp CV từ lịch sử git / PR / `.me/` | [[Thống kê công việc cho CV]] |

Thứ tự tham khảo của AI: **dự án → `.me/` → Playbook → kiến thức model → internet**. Việc đắt (tra cứu, tổng hợp, cập nhật tri thức, CV) làm theo đợt ([[Tiết kiệm chi phí AI]]).

Ở dự án công ty, chỉ học **công nghệ, cách triển khai, giải pháp, sai lầm chung** — không ghi gì về công ty, hệ thống hay lỗ hổng của họ.

## Bản đồ thư mục

| Thư mục | Nội dung |
|---|---|
| `00 Nguồn gốc/` | Vì sao có bộ này, đắn đo, bản nháp, [[Kế hoạch triển khai]] |
| `01 Cách làm việc/` | Luồng làm việc với AI, đối chiếu, ghi chú theo đợt, CV, tiết kiệm chi phí |
| `02 Tiếp cận dự án mới/` | Checklist vào dự án, thư mục `.me/`, kết nối với AI |
| `03 Chuẩn chung/` | Git, thiết kế code, lỗi + giao dịch + phản hồi, hằng số, log, bảo mật, DB, công cụ, tài liệu |
| `04 Validate/` | Mỗi loại trường dữ liệu một file |
| `05 Giao diện/` | Dialog, xác nhận, thông báo, hiển thị lỗi, trạng thái, form, bảng |
| `06 Kiểm thử/` | Chiến lược, nối tới [[Testing]] và [[Unit & integrate test case]] |
| `07 Giải pháp thường gặp/` | Đăng nhập, phân quyền, nhật ký thay đổi, upload, thùng rác, việc nền, tìm kiếm, hợp đồng API, vận hành, môi trường dev |
| `08 Mẫu/` | Hồ sơ dự án, kế hoạch, minh chứng, tổng hợp CV |
| `Hộp đề xuất/` | [[Ghi chú]] chờ đợt cập nhật |

Cách viết và giữ bộ này gọn: [[Duy trì Playbook]].
