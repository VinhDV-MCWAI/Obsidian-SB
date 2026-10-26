---
chu_de: thiet-lap-theo-loai
ap_dung: [du-an-moi, du-an-cu]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Thiết lập theo loại dự án

Làm **một lần khi bắt đầu** (hoặc khi nhận lại dự án sau thời gian dài). Mục tiêu: các phiên sau đọc ít nhất mà vẫn đúng. Bước chi tiết: [[Checklist tiếp cận dự án]].

## Bước 0 — xác định loại (AI quét nhanh trước khi đề xuất)

Quét **cấu trúc, không đọc code**: cây thư mục 2–3 cấp, file cấu hình (package / build / lint / CI / docker), README, `CLAUDE.md` / rules, thư mục tài liệu, số file, số commit gần đây.

| Loại | Dấu hiệu |
|---|---|
| **A. Dự án mới** | Mình khởi tạo / repo gần trống |
| **B. Dự án cũ cần nâng cấp** | Có code nhưng thiếu quy ước, thiếu lint / test / tài liệu, hoặc quy ước lệch nhau |
| **C. Dự án đã chuẩn** | Có rules / CLAUDE.md / sổ tay, lint + test + CI đầy đủ, tài liệu cập nhật |
| **D. Dự án nặng** | Rất nhiều module / nhiều repo / nhiều công nghệ — kết hợp với A, B hoặc C |

AI báo loại + lý do, mình xác nhận rồi mới làm tiếp.

## A. Dự án mới — Playbook là nguồn chính

| Tạo | Từ đâu | Commit? |
|---|---|---|
| Quy tắc cho AI của dự án (`CLAUDE.md`, rules) | "Lựa chọn của tôi" trong các file chuẩn, chỉ phần áp dụng cho công nghệ đã chọn | Dự án cá nhân: có. Dự án công ty: theo nhóm |
| Lint, format, kiểm tra kiểu, test, hook, quét bí mật, lệnh "kiểm tra tất cả" | [[Công cụ kiểm tra tự động]] | Có |
| Lớp dùng chung: khung phản hồi, bộ xử lý lỗi, giao dịch, log có trace_id, dialog / thông báo dùng chung | [[Giao dịch, lỗi và phản hồi API]], [[Log và theo dõi]], `05 Giao diện/` | Có (là code) |
| Hợp đồng API → kiểu dữ liệu sinh tự động | [[Hợp đồng API giữa các phần]] | Có |
| `.me/Hồ sơ dự án.md` (ngắn) | [[Mẫu - Hồ sơ dự án]] | Không |

Sau bước này, quy tắc nằm **trong dự án** → các phiên sau đọc rules của dự án, gần như không cần mở Playbook, trừ chốt chặn ([[Khi nào dùng Playbook]]).

## B. Dự án cũ cần nâng cấp — đối chiếu rồi đề xuất

1. AI so dự án với các file chuẩn **nền tảng**: lỗi / phản hồi, validate, bảo mật, DB, công cụ, log.
2. Ra bảng: chủ đề → dự án đang làm gì → Playbook chọn gì → **nên sửa / giữ nguyên / ghi nhận khác biệt** → lợi / hại / công sức.
3. Mình chọn mục nào sửa. Dự án công ty: chỉ **đề xuất** cho nhóm, không tự đổi quy ước chung.
4. `.me/Hồ sơ dự án.md` ghi lựa chọn của dự án (kể cả chỗ khác Playbook mà giữ nguyên) + `.me/Bản đồ.md` cho phần chưa có tài liệu.

## C. Dự án đã chuẩn — chỉ lấy phần dự án còn thiếu

1. AI liệt kê chủ đề dự án **đã có quy tắc** → Playbook **không dùng** cho các chủ đề đó.
2. Chủ đề dự án **chưa có** mà Playbook có → ghi vào `.me/Hồ sơ dự án.md`, mục "Playbook dùng cho": thường chỉ còn chốt chặn (validate theo loại trường, bảo mật) và "Giải pháp thường gặp" khi làm tính năng tương ứng.
3. Không tạo `.me/Bản đồ.md` nếu tài liệu của dự án đã có bản đồ — chỉ ghi đường dẫn tới tài liệu đó.

Ví dụ: Second Memory có `CLAUDE.md`, rules, sổ tay, lệnh `make verify` → loại C. Playbook chỉ dùng cho checklist validate / bảo mật / giải pháp khi làm tính năng; không dùng phần cách làm việc, git, thiết kế code.

## D. Dự án nặng — quét trước, quyết sau

1. Quét cấu trúc (bước 0) **theo từng module**, không đọc code — giao cho tác tử phụ **model nhanh** (mỗi module trả về tóm tắt vài dòng); phiên chính (bậc cân bằng) chỉ đọc các tóm tắt. Không dùng model mạnh nhất để quét.
2. Chọn module mình sẽ làm → chỉ tiếp cận sâu module đó (đi theo một request, một màn hình).
3. `.me/Bản đồ.md` theo module: module → đường dẫn → tài liệu → người phụ trách (vai trò).
4. Module khác: ghi một dòng, đào sâu khi có việc chạm tới.
5. Rồi áp A / B / C cho phần mình làm.

## Mức `.me/` theo loại

| Loại | `.me/` |
|---|---|
| A | Hồ sơ ngắn (quy tắc đã vào dự án) |
| B | Hồ sơ + bản đồ phần thiếu tài liệu |
| C | Hồ sơ rất ngắn: "Playbook dùng cho" + đường dẫn tới tài liệu của dự án |
| D | Hồ sơ + bản đồ theo module |

## Bước cuối — hỏi mình và đề xuất cách làm việc với dự án này

Sau khi mình xác nhận loại, AI (model **đủ sức** — thường bậc cân bằng; xem bậc thang trong [[Hướng dẫn sử dụng]] mục 2) đọc [[Hướng dẫn sử dụng]] mục 1–5 rồi:

1. **Hỏi** những gì chưa biết (gói AI, model hay dùng, chính sách AI của công ty, phần mình phụ trách, điểm mạnh / yếu) — chỉ hỏi câu ảnh hưởng tới đề xuất, tối đa ~5 câu.
2. **Đề xuất** (bảng ngắn, có lý do):
   - Đóng gói tài liệu: `.me/` gồm file nào, ghi gì, phần nào chỉ để đường dẫn tới tài liệu dự án.
   - Playbook dùng cho chủ đề nào / không dùng cho chủ đề nào.
   - Model theo loại việc của dự án này; prompt mẫu cho 3–5 loại việc hay gặp.
   - Phạm vi cô lập thường dùng (theo module nào).
   - Extension / công cụ nên cài (cho mình) và nên đề xuất cho nhóm (lint, test, quét…).
   - Điểm mù riêng của dự án (chỗ dễ sai mà Playbook chưa nói).
3. Mình **chỉnh** đề xuất → AI ghi bản đã chốt vào `.me/Hồ sơ dự án.md` (mục "Cách làm việc với dự án này").

**Làm lại** (cùng bậc model như lần đầu) khi: đổi gói AI / công cụ, dự án đổi công nghệ hoặc cấu trúc lớn, nhận phần việc mới khác hẳn, hoặc thấy kết quả AI kém đi. Không làm lại mỗi lần pull code — lúc đó chỉ cập nhật bản đồ.

## Câu lệnh mở đầu

> "Dùng playbook: xác định loại dự án theo Thiết lập theo loại dự án (chỉ quét cấu trúc), báo loại + lý do. Sau khi tôi xác nhận: hỏi tôi các câu cần thiết, đề xuất cách đóng gói tài liệu và cách làm việc với dự án này theo mục 'Bước cuối'. Chưa sửa gì."
