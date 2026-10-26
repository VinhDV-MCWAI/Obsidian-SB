---
chu_de: git
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Git và quy trình

Quy ước của nhóm luôn thắng. Dưới đây là mặc định khi nhóm chưa có quy ước.

## Lựa chọn của tôi

- Nhánh sống ngắn, một việc một nhánh, tên `<loại>/<mã-ticket>-<mô-tả-ngắn>`.
- Conventional Commits; mỗi commit một mục đích, build được; thân commit nói **vì sao**.
- PR nhỏ (< ~400 dòng), tự review trước, CI xanh trước khi nhờ người duyệt.
- Squash khi merge việc vào nhánh tích hợp; merge commit khi phát hành.
- Không bao giờ dùng lệnh đụng cả thư mục làm việc khi có thay đổi chưa commit không phải của mình.

## Branch

| Branch | Dùng cho | Tách từ → merge về | Sống |
|---|---|---|---|
| `main` | Code đã phát hành | — | Mãi mãi, chỉ merge qua PR |
| `develop` | Gom việc đã xong | `main` | Mãi mãi |
| `feature/<mã-ticket>-<mô-tả-ngắn>` | Một tính năng | `develop` → `develop` | Giờ – vài ngày |
| `fix/<mã>-<mô-tả>` | Sửa lỗi | `develop` → `develop` | Giờ |
| `hotfix/<mã>-<mô-tả>` | Sửa gấp production | `main` → `main` **và** `develop` | Giờ |
| `refactor/…`, `chore/…`, `docs/…` | Đúng tên | `develop` → `develop` | Ngắn |

Tên: chữ thường, gạch ngang, không dấu, ≤ ~50 ký tự. Ví dụ `feature/SHOP-123-checkout-coupon`. Branch sống càng ngắn càng ít xung đột.

## Commit (Conventional Commits)

```
<loại>(<phạm vi>): <tóm tắt mệnh lệnh, ≤ 72 ký tự>

<thân: VÌ SAO, không phải làm gì>
<chân: Refs: SHOP-123 / BREAKING CHANGE: …>
```

| Loại | Khi |
|---|---|
| `feat` | Hành vi mới |
| `fix` | Sửa lỗi |
| `refactor` | Đổi code, không đổi hành vi |
| `perf` | Nhanh hơn |
| `test`, `docs` | Chỉ test / tài liệu |
| `build`, `ci`, `chore` | Build, CI, linh tinh |

- [ ] Mỗi commit **một mục đích**; commit nào cũng build được.
- [ ] Sửa lỗi phát hiện trong lúc refactor → commit riêng, ghi rõ.
- [ ] Không commit: bí mật, `.env`, dump DB, file build, file cá nhân (`.me/`).
- [ ] Không có dòng ghi công AI nếu nhóm không muốn.
- [ ] Phá tương thích: `feat(api)!:` + `BREAKING CHANGE:`.

## Pull request

- [ ] Nhỏ (dưới ~400 dòng thay đổi). Lớn hơn → tách.
- [ ] Mô tả: làm gì, **vì sao**, đã test thế nào, rủi ro, cách quay lui, ảnh chụp (giao diện).
- [ ] Liên kết ticket; gán người duyệt và người phụ trách theo quy ước nhóm.
- [ ] **Tự review một lượt** trước khi gửi: đọc diff như người lạ.
- [ ] CI xanh trước khi nhờ review.
- [ ] Trả lời mọi góp ý: sửa, hoặc giải thích vì sao không.

## Review code người khác

| Mặt | Hỏi |
|---|---|
| Đúng | Đạt yêu cầu? Trường hợp biên, rỗng, lỗi? |
| Thiết kế | Đúng lớp? Lặp code? Có cách đơn giản hơn? |
| Hợp đồng | Đổi API đã cập nhật nơi dùng, tài liệu? Còn tương thích? |
| Dữ liệu | Migration quay lui được? Index? N+1? |
| Bảo mật | Kiểm quyền mỗi API? Đầu vào được kiểm? Không lộ bí mật trong log? |
| Test | Đúng mức? Bỏ thay đổi thì test fail? |
| Vận hành | Log có ích? Thông báo lỗi rõ? Cấu hình không viết thẳng? |

Góp ý vào code, không vào người. Góp ý không bắt buộc ghi `nit:`. Không bắt bẻ thứ linter đã lo.

## Merge

| Cách | Khi | Ghi chú |
|---|---|---|
| Squash | Mặc định cho feature/fix | Lịch sử `develop` gọn, một commit mỗi việc |
| Rebase + merge | Nhóm muốn giữ từng commit | Chỉ rebase branch của **mình**, chưa ai dùng |
| Merge commit | `develop` → `main`, hotfix | Giữ dấu vết phát hành |

## Xung đột

1. Hiểu **cả hai phía** (`git log -p <branch> -- <file>`).
2. File sinh tự động (lockfile, file kiểu dữ liệu sinh ra): không sửa tay — lấy một bên rồi sinh lại.
3. Chạy test phần bị đụng.
4. Xung đột về **hành vi** → hỏi người kia, không tự chọn.

## Lệnh nguy hiểm

Không dùng khi chưa chắc, nhất là khi có thay đổi chưa commit: `reset --hard`, `clean -fd`, `checkout -- .`, `push --force` (dùng `--force-with-lease` trên branch của mình), `rebase` branch người khác đang dùng, `commit --amend` commit đã push.

## Tag và phát hành

SemVer `vMAJOR.MINOR.PATCH`; ghi chú phát hành: thêm gì, sửa gì, phá vỡ gì, cách nâng cấp.
