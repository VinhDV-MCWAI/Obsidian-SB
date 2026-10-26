---
chu_de: luong-lam-viec
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Luồng làm một việc

Trước file này: [[Khi nào dùng Playbook]] (chốt chặn + mức đọc). File này mô tả luồng khi việc **cần** Playbook ở mức 2–3; mức 0–1 thì làm theo cách của AI / dự án, chỉ cộng thêm đối chiếu chốt chặn.

## 1. Phân loại cỡ việc

| Cỡ | Dấu hiệu | Đọc Playbook? | Kế hoạch | Chờ duyệt? |
|---|---|---|---|---|
| **Nhỏ** | Sửa chữ, đổi màu, đổi một giá trị, sửa lỗi rõ nguyên nhân trong 1 file | Không | Không | Không |
| **Vừa** | Một tính năng nhỏ, một API, một form, sửa lỗi nhiều file | Mục lục + 1–3 file | Vài dòng trong câu trả lời | Không, trừ khi đụng thứ ở cột "Lớn" |
| **Lớn** | Nhiều phần (API + giao diện + DB), đổi DB, đổi quyền / đăng nhập, thêm thư viện, xoá / đổi tính năng, đổi hợp đồng API | Mục lục + file liên quan | AI tự chọn dạng; [[Mẫu - Kế hoạch việc]] để tham khảo | **Có** |

Không chắc cỡ nào → chọn cỡ lớn hơn.

**Dự án đã có quy trình riêng đầy đủ** (CLAUDE.md, rules, sổ tay của nhóm): bỏ phần *cách làm việc* của Playbook (luồng, kế hoạch, git) — quy trình của dự án thay thế. Chỉ dùng **checklist theo chủ đề** (validate, giao diện, bảo mật, lỗi / phản hồi, giải pháp thường gặp) để bắt chỗ sót.

## 2. Mười bước (việc vừa và lớn)

| # | Bước | Làm gì | Ra gì |
|---|---|---|---|
| 1 | Hiểu yêu cầu | Đọc lại yêu cầu; liệt kê điều chưa rõ. Mơ hồ ảnh hưởng kết quả → hỏi trước | Danh sách câu hỏi (nếu có) |
| 2 | Chọn chuẩn | Đọc [[Mục lục cho AI]], mở file khớp chủ đề | Danh sách checklist sẽ đối chiếu |
| 3 | Đọc dự án | `.me/Hồ sơ dự án.md` (lựa chọn của dự án, mục bật/tắt), tài liệu nhóm liên quan | Biết quy ước riêng |
| 4 | Đọc code bị ảnh hưởng | Bắt đầu từ `.me/Bản đồ.md` (đường dẫn tới thành phần), rồi chỉ phần liên quan: tìm theo tên, đi theo luồng gọi. Không quét cả repo | Danh sách file sẽ sửa + nơi đang gọi chúng |
| 5 | Kế hoạch | Làm gì, ảnh hưởng gì, rủi ro, kiểm tra bằng gì, có cách đơn giản hơn không | Kế hoạch; việc lớn → dừng chờ duyệt |
| 6 | Làm | Theo quy ước của dự án trước, chuẩn chung sau. Mỗi thay đổi một mục đích | Code + test + tài liệu |
| 7 | Kiểm tra bằng công cụ | Lint, kiểm tra kiểu, test, build — chạy thật, đọc kết quả thật | Kết quả lệnh |
| 8 | Đối chiếu checklist | Trả lời từng dòng ([[Đối chiếu checklist và minh chứng]]) | Bảng đối chiếu |
| 9 | Dọn dẹp | Dữ liệu thử, file tạm, code chết, container tạm | Danh sách đã dọn |
| 10 | Ghi chú | Có điều học được → một dòng vào `Hộp đề xuất/Ghi chú.md`; điều riêng dự án → `.me/`. Không tra cứu, không sửa Playbook ([[Ghi chú và cập nhật theo đợt]]) | Một dòng (nếu có) |

## 3. Thứ tự ưu tiên khi các quy tắc mâu thuẫn

1. Yêu cầu rõ ràng của mình trong lúc làm.
2. Quy ước của nhóm / dự án (tài liệu trong repo, `CLAUDE.md` của dự án).
3. `.me/` — đặc thù dự án mình đã ghi.
4. Playbook (chuẩn chung).
5. Thói quen mặc định của AI.

Khi Playbook nói khác quy ước nhóm: **làm theo nhóm**, ghi khác biệt vào `.me/Hồ sơ dự án.md`.

## 4. Những thứ luôn phải hỏi trước

- Xoá, đổi tên, chuyển chỗ tính năng / thư mục / dữ liệu không do mình tạo trong việc này.
- Đổi cấu trúc DB, đăng nhập, phân quyền, hợp đồng API.
- Thêm thư viện, công cụ, dịch vụ ngoài.
- Thao tác không quay lui được hoặc ra bên ngoài: push, gửi mail, xoá volume, deploy.

## 5. Đánh giá ảnh hưởng (bước 4–5)

| Hỏi | Cách trả lời nhanh |
|---|---|
| Ai đang gọi thứ mình sửa? | Tìm theo tên hàm / route / biến môi trường trong cả repo |
| Dữ liệu cũ có còn đúng không? | Có cần migration dữ liệu, giá trị mặc định? |
| Giao diện nào hiển thị nó? | Tìm theo tên trường / endpoint ở front-end |
| Tài liệu nào mô tả nó? | Xem [[Tài liệu dự án]] mục "khi code đổi" |
| Test nào phủ nó? | Tìm test theo tên lớp / route |
| Quay lui thế nào nếu lỗi? | Revert commit được không? Migration có đường lui? |
