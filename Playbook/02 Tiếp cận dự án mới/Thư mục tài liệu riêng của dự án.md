---
chu_de: thu-muc-me
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Thư mục tài liệu riêng của dự án (`.me/`)

**Lựa chọn của tôi:** tài liệu của mình cho một dự án nằm trong thư mục dự án (AI đọc được ngay), **không commit** — cả dự án công ty lẫn dự án cá nhân. Xoá dự án là xoá luôn mà không mất gì: điều học được đã ở [[Ghi chú]] / Playbook, thành tích lấy lại được từ lịch sử git ([[Thống kê công việc cho CV]]).

## Cấu trúc (vừa đủ — file nào không cần thì không tạo)

```
<dự án>/.me/
├── Hồ sơ dự án.md     mục đích, công nghệ, lệnh, lựa chọn của dự án so với Playbook, mục bật/tắt
├── Bản đồ.md          thành phần → đường dẫn trong code / tài liệu; luồng chính
├── Thuật ngữ.md       (nếu nghiệp vụ nhiều thuật ngữ)
├── Quyết định.md      quyết định đã chốt trong lúc làm (ngày, vì sao)
├── Ghi chú dự án.md   điều chỉ đúng ở dự án này: chỗ vướng, cách dự án làm
├── Tiến độ.md         việc đang dở, để phiên AI sau làm tiếp
└── tam/               nháp, kết quả lệnh — xoá thoải mái
```

Mẫu: [[Mẫu - Hồ sơ dự án]].

## `Bản đồ.md` — đường dẫn thay vì chép

| Thành phần | Đường dẫn | Ghi chú |
|---|---|---|
| Xử lý lỗi chung | `<đường dẫn tới file>` | Dự án trả 500 cho lỗi nghiệp vụ — khác Playbook |
| Khung phản hồi | `<đường dẫn>` | |
| Dialog dùng chung | `<đường dẫn>` | |
| Luồng đăng nhập | `<đường dẫn route>` → `<đường dẫn xử lý>` | |

AI đọc bản đồ trước, rồi mở đúng file — không phải tìm lại mỗi phiên. Code đổi chỗ → sửa một dòng bản đồ.

## Loại khỏi git mà không đụng file của nhóm

```bash
echo ".me/" >> .git/info/exclude
```

- `.git/info/exclude` chỉ có trên máy mình, không bao giờ commit → nhóm không thấy gì.
- **Không** thêm vào `.gitignore` của dự án.
- Kiểm: `git status` không hiện `.me/`.

## Cập nhật `.me/` khi code đổi

Sau khi pull code mới, hoặc khi bắt đầu phiên sau một thời gian dài: *"So `.me/Bản đồ.md` với code hiện tại (`git log` từ lần cập nhật trước), sửa đường dẫn đã đổi."* Rẻ hơn tạo lại từ đầu.

## Kết thúc dự án

1. Chạy đợt cập nhật nếu còn ghi chú ([[Ghi chú và cập nhật theo đợt]]).
2. Tổng hợp CV ([[Thống kê công việc cho CV]]).
3. Xoá `.me/` (hoặc cả dự án).
