---
loai: ke-hoach
cap_nhat: 2026-10-26
---
# Kế hoạch triển khai Playbook

Danh mục gốc ~130 mục: [[Bản nháp 2 - Đề xuất bộ chuẩn]] (mục 4, nhóm A–O).

## Đã chốt

| Quyết định | Chốt |
|---|---|
| Bộ chuẩn ở đâu | Kho Obsidian, `Playbook/` |
| Cách viết | Không phụ thuộc công nghệ; mỗi chủ đề có "Lựa chọn của tôi" |
| Tài liệu riêng dự án | `.me/`, tạo khi bắt đầu dự án (lọc vừa đủ từ Playbook + bản đồ đường dẫn), loại bằng `.git/info/exclude`, không commit ở mọi dự án |
| Học từ công việc | AI ghi một dòng vào [[Ghi chú]]; mình cập nhật theo đợt (tháng / cuối dự án) |
| Dự án công ty | Chỉ học công nghệ, cách triển khai, giải pháp, sai lầm chung; không ghi gì về công ty / hệ thống / lỗ hổng |
| CV | Cuối dự án, lọc từ git / PR / `.me/` |
| Thứ tự tham khảo của AI | Dự án → `.me/` → Playbook → kiến thức model → internet |
| Định dạng | Markdown + phần đầu YAML |
| AI biết Playbook bằng cách nào | Kỹ năng cấp người dùng `~/.claude/skills/playbook/` ([[Khởi tạo dự án]]) |

## Trạng thái theo nhóm

| Nhóm | Nội dung | Trạng thái | File |
|---|---|---|---|
| A | Cách làm việc với AI | ✅ | `01 Cách làm việc/` |
| B | Tiếp cận dự án mới | ✅ | `02 Tiếp cận dự án mới/` |
| C | Công cụ kiểm tra tự động | ✅ | [[Công cụ kiểm tra tự động]] |
| D | Git và quy trình | ✅ | [[Git và quy trình]] |
| E | Thiết kế code | ✅ | [[Thiết kế code và chia lớp]], [[Hằng số, enum, thông báo]], [[Hợp đồng API giữa các phần]] |
| F | Nền tảng server | ✅ | Lỗi / giao dịch / phản hồi, đăng nhập, phân quyền, nhật ký, upload, thùng rác, việc nền, tìm kiếm, [[Hiệu năng, cache và dịch vụ ngoài]], cấu hình trong [[Môi trường phát triển và dọn dẹp]] |
| G | Validate theo loại trường | ✅ đủ dùng | 9 file. Thêm khi gặp: mã số thuế / giấy tờ, địa chỉ |
| H | Giao diện | ✅ | Dialog, thông báo, trạng thái / form / bảng, [[Bố cục, màu và truy cập dễ dàng]] |
| I | Log | ✅ | [[Log và theo dõi]] |
| J | Kiểm thử | ✅ | [[Chiến lược kiểm thử]] + [[Testing]] |
| K | Bảo mật | ✅ | [[Bảo mật cơ bản]] |
| L | Tài liệu dự án | ✅ | [[Tài liệu dự án]] |
| M | DB | ✅ | [[Dữ liệu và DB]] |
| N | Phát hành, vận hành | ✅ | [[Vận hành và sự cố]], [[Môi trường phát triển và dọn dẹp]], [[Phát hành và quay lui]] |
| O | Duy trì | ✅ | [[Duy trì Playbook]] |

Tất cả là **bản đầu** do Claude soạn từ kho Obsidian + Second Memory + kiến thức chung → cần mình đọc, sửa "Lựa chọn của tôi" cho đúng ý mình.

## Việc tiếp theo

| # | Việc | Ai | Ghi chú |
|---|---|---|---|
| 1 | Đọc, sửa "Lựa chọn của tôi" ở từng file (bắt đầu: [[Giao dịch, lỗi và phản hồi API]], [[Dialog và hộp xác nhận]], [[Ghi chú và cập nhật theo đợt]]) | Mình | Rồi commit + push kho Obsidian |
| 2 | ~~Cài kỹ năng `playbook`~~ — **đã cài 2026-10-26** ở `~/.claude/skills/playbook/SKILL.md` | — | Gỡ: xoá thư mục đó |
| 3 | Chạy thử "bắt đầu dự án" trên một dự án: tạo `.me/` + bản đồ, xem tốn bao nhiêu | Mình + Claude | Điều chỉnh mục lục / checklist theo kết quả |
| 4 | Thêm file khi thật sự gặp (mã số thuế, địa chỉ, CI / CD chi tiết…) | Qua [[Ghi chú]] → đợt cập nhật | Không viết trước khi cần |
| 5 | Đợt cập nhật đầu tiên sau ~1 tháng dùng | Mình khởi động | Từ [[Ghi chú]] |