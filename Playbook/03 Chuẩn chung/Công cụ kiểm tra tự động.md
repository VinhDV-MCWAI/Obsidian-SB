---
chu_de: cong-cu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Công cụ kiểm tra tự động

Nguyên tắc: **thứ gì máy kiểm được thì để máy kiểm.** Rẻ hơn AI đọc code, chính xác hơn, và báo ngay trong editor (tab Problems).

## Lựa chọn của tôi

Mọi dự án có đủ **bảy nhóm nền tảng**: định dạng, lint, kiểm tra kiểu, test, hook trước commit, quét bí mật, quét lỗ hổng thư viện — và **một lệnh "kiểm tra tất cả"** chạy trước khi báo xong (CI chạy đúng lệnh đó). Tên công cụ cụ thể chọn theo công nghệ của dự án; cột "ví dụ" dưới đây chỉ để tham khảo.

## Nhóm công cụ

| Nhóm | Bắt gì | Ví dụ theo công nghệ | Mức |
|---|---|---|---|
| Formatter | Định dạng | Prettier (JS/TS), Pint / PHP-CS-Fixer (PHP), ruff format / black (Python), gofmt | Nền tảng |
| Linter | Lỗi hay gặp, quy ước | ESLint, PHPStan / Larastan, ruff, golangci-lint | Nền tảng |
| Kiểm tra kiểu | Sai kiểu | `tsc --noEmit`, PHPStan level cao, mypy / pyright | Nền tảng |
| Test | Hành vi | PHPUnit / Pest, Vitest / Jest, pytest, Playwright | Nền tảng |
| Hook trước commit | Chạy nhanh các thứ trên trước khi commit | husky + lint-staged, pre-commit, lefthook | Nền tảng |
| Quét bí mật | Khoá, token lọt vào code | gitleaks, trufflehog | Nền tảng |
| Lỗ hổng thư viện | Thư viện có CVE | `npm/pnpm audit`, `composer audit`, `pip-audit`, Dependabot, Renovate, Trivy | Nền tảng |
| Phân tích tĩnh bảo mật | SQL injection, XSS… | Semgrep, CodeQL, SonarQube | Nên có |
| Chất lượng code | Trùng, hàm dài, phức tạp | SonarQube / SonarLint, jscpd, phpmd | Nên có |
| Ranh giới import | Lớp gọi sai lớp | eslint-plugin-boundaries, deptrac, import-linter | Tuỳ dự án |
| Quét image / hạ tầng | Lỗ hổng container, cấu hình sai | Trivy, hadolint (Dockerfile), tflint, checkov | Tuỳ dự án |
| Quét virus file tải lên | Mã độc | ClamAV | Tuỳ dự án |
| Truy cập dễ dàng | A11y | axe (Playwright), Lighthouse | Tuỳ dự án |
| Hiệu năng / SEO | Tốc độ, thẻ meta | Lighthouse CI, k6 | Tuỳ dự án |

## Extension editor (VS Code) gợi ý

| Công nghệ | Extension |
|---|---|
| Chung | EditorConfig, ESLint, Prettier, GitLens, Error Lens (hiện lỗi ngay trên dòng), Code Spell Checker, SonarLint, Docker, YAML, dotenv |
| Theo công nghệ của dự án | Extension chính thức của ngôn ngữ và framework + extension của đúng linter / formatter / kiểm tra kiểu mà dự án dùng (để lỗi hiện trong tab Problems giống hệt khi chạy lệnh) |
| DB | Một client SQL |

Dự án có `.vscode/extensions.json` → dùng theo đó. Không có → cài cho mình, **đề xuất** cho nhóm.

## Thứ tự dựng cho dự án mới

1. Formatter + `.editorconfig` → hết tranh cãi định dạng.
2. Linter + kiểm tra kiểu ở mức vừa, nâng dần.
3. Test chạy được bằng một lệnh.
4. Hook trước commit (chỉ các bước nhanh, < 10 giây).
5. Một lệnh "kiểm tra tất cả" (`make verify`, `pnpm check`) — chạy trước khi báo xong.
6. CI chạy đúng lệnh đó + quét bí mật + quét thư viện.
7. Kiểm tra "file sinh ra không cũ" nếu có sinh code (đặc tả API → kiểu dữ liệu).

## Khi vào dự án có sẵn

- [ ] Lệnh nào kiểm tra gì? Ghi vào `.me/Hồ sơ dự án.md`.
- [ ] Lỗi linter có sẵn rất nhiều → không sửa hàng loạt (lẫn với việc của mình); chỉ đảm bảo phần mình sửa sạch.
- [ ] Thiếu công cụ nền tảng → đề xuất, kèm lợi / chi phí.
