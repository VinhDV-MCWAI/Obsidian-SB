---
chu_de: git
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Git và quy trình — nhánh, commit, pull request, merge

Bước 10 của [[Quy trình một việc]]. Quy ước của nhóm luôn thắng; dưới đây là mặc định khi nhóm chưa có.

Trước: [[Review code - quy trình]] · Sau: [[Phát hành và quay lui]].

## Lựa chọn của tôi

- Nhánh việc sống ngắn, một việc một nhánh, tách từ nhánh tích hợp.
- Nhánh dài hạn (chính, tích hợp, môi trường) **chỉ nhận thay đổi qua pull request**: không push thẳng, không force push.
- Conventional Commits **tiếng Anh** (cả tiêu đề, kể cả commit chỉ đổi tài liệu); mỗi commit một mục đích, build được; thân commit nói **vì sao** (nhóm cho phép thân tiếng khác thì theo nhóm).
- Pull request nhỏ (< ~400 dòng), tự review trước, CI xanh trước khi nhờ duyệt.
- **AI không commit, không push, không mở / merge pull request** khi quy tắc dự án chưa cho; AI soạn sẵn để mình duyệt và tự chạy.
- Không dùng lệnh đụng cả thư mục làm việc khi có thay đổi chưa commit không phải của mình.

## Nhánh

Chọn **một** mô hình cho dự án:

| Mô hình | Nhánh dài hạn | Dùng khi |
|---|---|---|
| Tích hợp + chính | `develop` (gom việc xong) → `main` (đã phát hành) | Phát hành theo đợt |
| Theo môi trường | `dev` → `stg` → `prod` / `main`, mỗi nhánh triển khai tới một môi trường | Nhóm triển khai theo nhánh |

| Nhánh việc | Tách từ → về | Sống |
|---|---|---|
| `feature/<mã>-<mô-tả>` | Nhánh tích hợp (`develop` / `dev`) | Giờ – vài ngày |
| `fix/<mã>-<mô-tả>` | Nhánh tích hợp | Giờ |
| `refactor/…`, `chore/…`, `docs/…`, `test/…` | Nhánh tích hợp | Ngắn |
| `hotfix/<mã>-<mô-tả>` | Nhánh production → production, rồi đưa về `stg` / tích hợp | Giờ ([[Phát hành và quay lui]]) |

- Tên: chữ thường, gạch ngang, không dấu, ≤ ~50 ký tự (`feature/SHOP-123-checkout-coupon`). Không có ticket → `<loại>/<mô-tả>`. Nhóm gom theo đợt / epic → thêm một cấp `feature/<epic>/<mã>-<mô-tả>`, chỉ khi nhóm dùng.
- Thay đổi đi **một chiều** dev → stg → prod. Không tách nhánh việc từ `stg` / `prod` (trừ hotfix); không merge `stg` / `prod` ngược vào nhánh việc.

## Bảo vệ nhánh

Cấu hình trên nơi lưu repo cho mọi nhánh dài hạn:

- [ ] Cấm push thẳng, force push, xoá nhánh.
- [ ] Bắt buộc pull request, ≥ 1 người duyệt, không tự duyệt.
- [ ] Bắt buộc CI xanh; nhánh cập nhật với nhánh đích trước khi merge.
- [ ] Commit mới sau khi duyệt → duyệt cũ bị huỷ.
- [ ] Chỉ người được giao mới merge vào production.

Nơi lưu không hỗ trợ → quy tắc dự án + hook trước push chặn push thẳng / force push ([[Công cụ và hook của dự án]] #4). Hook tắt được, chỉ là lớp phụ.

## AI và git

| AI được | AI không, trừ khi quy tắc dự án / mình cho phép rõ |
|---|---|
| Xem trạng thái, diff, log, blame | Commit |
| Tạo nhánh việc local khi được giao | Push; mở / merge / đóng pull request |
| Chạy review trước khi đưa danh sách file / commit message | Sửa lịch sử, đổi cấu hình git, bỏ qua hook |
| Soạn danh sách `git add`, commit message, mô tả pull request | **Không bao giờ**: force push, push thẳng nhánh dài hạn |

Dự án cho AI commit local → quy tắc dự án ghi: qua lệnh nào, chỉ file của việc đó, review trước, không push.

## Commit

```
<loại>(<phạm vi>): <tóm tắt mệnh lệnh, ≤ 72 ký tự>

<thân: VÌ SAO, không phải làm gì>
<chân: Refs: SHOP-123 / BREAKING CHANGE: …>
```

| Loại | Khi |
|---|---|
| `feat` / `fix` | Hành vi mới / sửa lỗi |
| `refactor` / `perf` | Đổi code không đổi hành vi / nhanh hơn |
| `test`, `docs` | Chỉ test / tài liệu |
| `build`, `ci`, `chore` | Build, CI, linh tinh |

- [ ] Mỗi commit một mục đích, build được; lỗi thấy trong lúc refactor → commit riêng.
- [ ] Không commit bí mật, `.env`, dump DB, file build, file cá nhân (`.me/`).
- [ ] Không dòng ghi công AI nếu nhóm không muốn.
- [ ] Phá tương thích: `feat(api)!:` + `BREAKING CHANGE:`.

## Pull request

- [ ] Nhỏ; lớn hơn ~400 dòng → tách, hoặc nói vì sao.
- [ ] Mô tả theo [[Mẫu - Mô tả pull request]]: làm gì, vì sao, đã kiểm thế nào, rủi ro, quay lui, ảnh chụp.
- [ ] Tự review một lượt (đọc diff như người lạ); CI xanh trước khi nhờ duyệt.
- [ ] Trả lời mọi góp ý: sửa, hoặc giải thích. Góp ý vào code, không vào người; duyệt khi chỉ còn góp ý nhỏ; không bắt bẻ thứ linter đã lo.

## Merge và xung đột

| Cách | Khi |
|---|---|
| Squash | Mặc định cho nhánh việc → nhánh tích hợp |
| Rebase + merge | Nhóm muốn giữ từng commit; chỉ rebase nhánh **của mình** chưa ai dùng |
| Merge commit | Giữa các nhánh dài hạn, hotfix — giữ dấu vết phát hành |

Xung đột: hiểu **cả hai phía** (`git log -p <nhánh> -- <file>`); file sinh tự động (lockfile, kiểu sinh từ đặc tả) không sửa tay — lấy một bên rồi sinh lại; chạy test phần bị đụng; xung đột về **hành vi** → hỏi người kia.

## Lệnh nguy hiểm

Không dùng khi chưa chắc, nhất là khi có thay đổi chưa commit: `reset --hard`, `clean -fd`, `checkout -- .`, `restore .`, `stash` (khi có thay đổi của người khác), `push --force` (cần thì `--force-with-lease` trên nhánh của mình), rebase nhánh người khác đang dùng, `commit --amend` commit đã push.
