> Bản nháp thứ hai (2026-10-26). Đã chốt khác so với bản này: bộ chuẩn nằm trong **kho Obsidian** (không nằm trong repo Second Memory), tài liệu riêng dự án nằm ở máy, không commit. Xem [[Kế hoạch triển khai]]. Danh mục ở mục 4 vẫn là danh mục gốc.

# Đề xuất: Bộ chuẩn làm việc cá nhân (dùng lại cho mọi dự án)

Ngày: 2026-10-26 · Trạng thái: **đề xuất, chờ bạn duyệt** · Chưa tạo file nào trong repo.

---

## 1. Hiểu lại yêu cầu

| Bạn muốn | Không muốn |
|---|---|
| Một bộ **quy chuẩn + kinh nghiệm** độc lập công nghệ (ví dụ: email phải kiểm tra cú pháp, độ dài, trùng trong DB, tên miền có máy chủ thư…) để AI và bạn đối chiếu, khỏi sót | Sinh code dùng chung giữa back-end và front-end (rủi ro khi chuyển qua nhiều công nghệ) |
| Nạp **khi cần**, không nạp hết mỗi lần → tiết kiệm chi phí, không làm loãng ngữ cảnh | Bộ tài liệu phình to và bị đọc toàn bộ mỗi lần |
| AI **đề xuất bài học mới** sau mỗi việc; bạn duyệt rồi mới vào bộ chuẩn | AI tự sửa bộ chuẩn không qua bạn |
| Đặt trong **chính repo này**, copy một thư mục sang dự án công ty, **không commit ở đó**, không ảnh hưởng nhóm | Chia sẻ cho người khác |
| Bộ chuẩn đủ rộng: từ cách tiếp cận dự án, công cụ, git, code, validate, giao diện, log, test, tài liệu | Mẹo vặt trên mạng mà không kiểm chứng |
| **Liệt kê đầy đủ trước**, đi sâu từng phần sau | — |

---

## 2. Cách bộ chuẩn hoạt động

### 2.1 Luồng làm một việc (luồng chuẩn cho AI)

```
Bạn ra yêu cầu
  │
  ① Phân loại việc: nhỏ / vừa / lớn; thuộc chủ đề nào (validate? giao diện? API? git?)
  │     → nhỏ (sửa chữ, đổi màu): bỏ qua bộ chuẩn, làm luôn
  ② Đọc MỤC LỤC (một file ngắn) → chọn 1–3 file chuẩn liên quan
  ③ Đọc HỒ SƠ DỰ ÁN: công nghệ, mục nào tắt / thêm cho dự án này
  ④ Đọc code / tài liệu dự án đúng phần bị ảnh hưởng (không quét cả repo)
  ⑤ Lập kế hoạch: làm gì, ảnh hưởng chỗ nào, rủi ro, kiểm tra bằng gì
  │     → việc lớn: dừng, chờ bạn duyệt
  ⑥ Làm
  ⑦ Kiểm tra bằng CÔNG CỤ trước (lint, kiểu, test) — không tin lời AI
  ⑧ Đối chiếu checklist đã chọn ở ② → đánh dấu đã làm / không áp dụng / bỏ qua có lý do
  ⑨ Ghi minh chứng (lệnh đã chạy, kết quả)
  ⑩ Có bài học mới? → ghi vào HỘP ĐỀ XUẤT, không sửa bộ chuẩn
```

Điểm quan trọng: **bước ⑧ mới là nơi bộ chuẩn bù chỗ AI hay sót.** AI viết code xong phải đi qua từng dòng checklist và trả lời từng dòng, không được nói chung chung "đã validate đầy đủ".

### 2.2 Tiết kiệm chi phí như thế nào

| Thứ | Lúc nào nạp | Kích thước ước tính |
|---|---|---|
| Mô tả ngắn của "kỹ năng" bộ chuẩn (để AI biết nó tồn tại) | Luôn luôn | ~100 token |
| Mục lục | Khi việc từ cỡ vừa trở lên | ~1.500 token |
| Hồ sơ dự án | Cùng lúc với mục lục | ~500–1.000 token |
| 1–3 file chuẩn | Chỉ file khớp chủ đề | ~1.000–2.000 token / file |
| Cả bộ chuẩn (khi đã đầy đủ) | **Không bao giờ** | có thể 50.000+ token |

Quy tắc giữ bộ chuẩn không phình:
- Mỗi file một chủ đề, **tối đa ~150 dòng**. Dài hơn thì tách.
- Mục lục chỉ có: tên chủ đề → file → *khi nào dùng* (một dòng).
- Mỗi dòng checklist một câu ngắn, bỏ phần giải thích dài sang mục "Vì sao" ở cuối file. AI chỉ cần đọc phần đầu.
- Định kỳ (ví dụ mỗi tháng) gộp / xoá dòng trùng.

### 2.3 Học từ kinh nghiệm (của bạn và của AI)

```
Làm xong một việc
  └─► AI ghi vào inbox/<ngày>-<chủ-đề>.md:
        - chuyện gì xảy ra, sai ở đâu, sửa ra sao
        - đề xuất thêm / sửa dòng nào trong file chuẩn nào
        - kiến thức mới (xu hướng, công cụ mới) kèm nguồn
  └─► Bạn đọc hộp đề xuất (khi rảnh) → đồng ý thì chép vào file chuẩn, không thì xoá
```

- "Tài liệu cũ và mới để đối chiếu": **git làm việc này sẵn** (xem lịch sử, so sánh từng lần sửa). Không cần giữ hai bản file.
- Thông tin riêng của công ty **không** đi vào bộ chuẩn: hộp đề xuất ở dự án công ty chỉ được ghi bài học *đã khái quát hoá* (bỏ tên khách hàng, tên bảng, URL nội bộ).

### 2.4 Đặt ở đâu, mang sang công ty thế nào

```
second_memory/                     ← repo cá nhân của bạn (nguồn gốc của bộ chuẩn)
└── playbook/                      ← bộ chuẩn (tên tạm, bạn có thể đổi)

Dự án công ty/
├── (code của công ty)
└── playbook/                      ← bản copy, KHÔNG commit
      git loại trừ bằng .git/info/exclude (chỉ máy bạn biết, không sửa .gitignore của nhóm)
```

- Một script cài đặt: copy thư mục, thêm dòng vào `.git/info/exclude`, đăng ký "kỹ năng" để AI biết gọi.
- Hồ sơ dự án công ty (`playbook/project.md`) chỉ nằm ở bản copy, không mang về repo cá nhân.
- Cập nhật hai chiều: bài học tốt từ công ty → bạn tự chép (đã khái quát) về `second_memory/playbook`.

### 2.5 Định dạng: Markdown hay JSON?

| | Markdown + đầu file ngắn (YAML) | JSON |
|---|---|---|
| Bạn viết, sửa, đọc trong Obsidian | Dễ | Khó (dấu ngoặc, không chú thích được) |
| AI đọc hiểu | Tốt | Tốt |
| Có giải thích "vì sao", ví dụ | Có | Gượng |
| Máy lọc / sinh mục lục tự động | Được (đọc phần đầu YAML) | Được |

**Khuyến nghị: Markdown**, mỗi file có phần đầu YAML ngắn:

```yaml
---
chu_de: validate-email
ap_dung: [back-end, front-end]      # nơi áp dụng
muc_do: nen-tang                    # nen-tang | nen-co | tuy-du-an
cap_nhat: 2026-10-26
---
```

Nếu sau này cần JSON (cho công cụ), sinh từ phần đầu này, không viết tay.

---

## 3. Ví dụ một file chuẩn: validate email

> Để bạn thấy hình dạng; nội dung đầy đủ làm ở giai đoạn sau.

```markdown
---
chu_de: validate-email
ap_dung: [back-end, front-end]
muc_do: nen-tang
---
# Trường email

## Kiểm tra (theo thứ tự)
| # | Kiểm tra | FE | BE | Ghi chú |
|---|---|---|---|---|
| 1 | Chuẩn hoá: bỏ khoảng trắng đầu/cuối, chữ thường phần tên miền | ✔ | ✔ | Làm trước mọi kiểm tra |
| 2 | Bắt buộc / không bắt buộc | ✔ | ✔ | Chuỗi toàn khoảng trắng = rỗng |
| 3 | Cú pháp (RFC 5322 rút gọn) | ✔ | ✔ | Không tự viết regex dài |
| 4 | Độ dài tối thiểu / tối đa (tối đa chuẩn 254) | ✔ | ✔ | Một hằng số, không viết thẳng số |
| 5 | Ký tự lạ: full-width, emoji, Unicode giả dạng | ✔ | ✔ | Theo checklist nhập liệu |
| 6 | Trùng trong DB: so sánh **không phân biệt hoa thường**; có tính bản ghi đã xoá mềm không? | — | ✔ | Có index unique ở DB, không chỉ kiểm tra ở code |
| 7 | Tên miền có máy chủ thư (MX/DNS) | — | ✔ | Có công tắc bật/tắt; thông báo riêng "kiểm tra lỗi gõ tên miền" |
| 8 | Tên miền email dùng một lần (mailinator…) | — | tuỳ dự án | |
| 9 | Email đã xác minh chưa (gửi link) | — | tuỳ dự án | |
| 10 | Thông báo lỗi: mỗi loại một mã riêng, không gộp | ✔ | ✔ | |

## Bẫy hay gặp
- Kiểm tra trùng ở code nhưng thiếu unique index → hai request cùng lúc vẫn tạo trùng.
- Đăng nhập so sánh phân biệt hoa thường nhưng đăng ký thì không → người dùng không đăng nhập được.
- Thông báo "email đã tồn tại" ở trang đăng ký công khai → lộ ai có tài khoản (cân nhắc thông báo chung).

## Vì sao
(giải thích dài, nguồn, bài học — AI không cần đọc khi đã nắm phần trên)
```

---

## 4. Danh mục đầy đủ (cần làm dần)

Cột **Mức**: 🟥 nền tảng (mọi dự án) · 🟧 nên có · 🟨 tuỳ dự án.
Cột **Nguồn có sẵn**: chỗ đã có tài liệu để rút ra — 🗂 kho Obsidian · 🏠 repo Second Memory · — chưa có.

### A. Cách làm việc với AI (dùng cho mọi việc)
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| A1 | Phân loại việc: nhỏ / vừa / lớn, khi nào phải dừng chờ duyệt | 🟥 | 🏠 `docs/plan/01-goals-and-workflow.md` |
| A2 | Mục lục + quy tắc chọn file chuẩn cần đọc | 🟥 | — |
| A3 | Mẫu kế hoạch: mục tiêu, phạm vi, ảnh hưởng, rủi ro, cách kiểm tra, chi phí | 🟥 | 🏠 handbook |
| A4 | Đánh giá ảnh hưởng: từ file sửa → chức năng → tài liệu → test liên quan | 🟥 | — |
| A5 | Đối chiếu checklist sau khi làm (trả lời từng dòng) | 🟥 | — |
| A6 | Minh chứng: lệnh, kết quả, ảnh chụp, đã dọn gì | 🟥 | 🏠 `.claude/rules/cleanup.md` |
| A7 | Ghi bài học vào hộp đề xuất, cách duyệt | 🟥 | 🏠 bộ nhớ AI hiện tại |
| A8 | Chọn mức model / độ sâu theo cỡ việc (tiết kiệm chi phí) | 🟧 | — |
| A9 | Khi nào tìm kiếm trên mạng, cách ghi nguồn | 🟧 | — |

### B. Tiếp cận dự án mới
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| B1 | Thu thập: yêu cầu ban đầu, mục tiêu, thiết kế, công nghệ, người liên quan | 🟥 | — |
| B2 | Hồ sơ dự án (một trang): công nghệ, quy ước riêng, mục bật/tắt | 🟥 | — |
| B3 | Bản đồ dự án: thư mục → chức năng → tài liệu; luồng chính | 🟥 | 🏠 `CLAUDE.md` các tầng |
| B4 | Kiểm tra môi trường: chạy được chưa, lệnh nào, dữ liệu mẫu | 🟥 | 🏠 `docs/dev-guide.md` |
| B5 | Đề xuất công cụ còn thiếu (xem nhóm C) | 🟧 | — |
| B6 | Bảng thuật ngữ nghiệp vụ | 🟧 | — |

### C. Công cụ và kiểm tra tự động (để máy báo lỗi, bớt phụ thuộc AI)
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| C1 | Tiện ích editor theo công nghệ (PHP, TS, Python, Docker, YAML…) | 🟥 | — |
| C2 | Định dạng code tự động (Prettier, Pint, ruff…) | 🟥 | 🏠 |
| C3 | Lint + kiểm tra kiểu (ESLint, PHPStan, mypy, tsc) | 🟥 | 🏠 |
| C4 | Hook trước commit: định dạng, lint, chặn file bí mật | 🟥 | 🏠 (commit-msg hook) |
| C5 | Quét bí mật lộ trong code (gitleaks…) | 🟥 | — |
| C6 | Quét thư viện có lỗ hổng (npm audit, composer audit…) | 🟥 | — |
| C7 | Phân tích tĩnh bảo mật (Semgrep…) | 🟧 | — |
| C8 | Quét virus file người dùng tải lên (ClamAV) | 🟨 | — |
| C9 | Phát hiện code trùng, hàm quá dài, quá phức tạp | 🟧 | — |
| C10 | Phát hiện số / chuỗi viết thẳng (chống hardcode) | 🟧 | — |
| C11 | Kiểm tra quy tắc import giữa các lớp (deptrac, eslint-plugin-boundaries) | 🟨 | — |

### D. Git và quy trình nhóm
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| D1 | Đặt tên nhánh | 🟥 | — |
| D2 | Commit message (Conventional Commits), độ lớn một commit | 🟥 | 🏠 |
| D3 | Pull request: mô tả, checklist, ảnh, người duyệt, gán người | 🟥 | — |
| D4 | Cách merge (squash / rebase / merge), xử lý xung đột | 🟥 | — |
| D5 | Review code: tự review trước khi gửi, checklist review | 🟥 | — |
| D6 | Tag, phiên bản, ghi chú phát hành | 🟧 | 🏠 (v3.0.0) |
| D7 | Sửa gấp trên môi trường thật (hotfix) | 🟧 | — |
| D8 | Những lệnh git không được dùng | 🟥 | 🏠 `parallel-lanes.md` |

### E. Thiết kế code
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| E1 | Chia lớp: route → middleware → controller → validate → service → repository → model → định dạng đầu ra | 🟥 | 🏠 laravel-api |
| E2 | Trách nhiệm từng lớp: được làm gì, không được làm gì | 🟥 | — |
| E3 | SOLID áp dụng thực tế (không trừu tượng hoá thứ dùng một lần) | 🟥 | 🗂 App & Request Life-Cycle (DI, Singleton) |
| E4 | Đặt tên: biến, hàm, lớp, file, bảng, cột, route | 🟥 | — |
| E5 | Hằng số, enum, mã thông báo — không viết thẳng giá trị | 🟥 | 🏠 |
| E6 | Helper / hàm dùng chung: khi nào tạo, đặt ở đâu, phạm vi | 🟥 | — |
| E7 | Quy ước route / URL API | 🟥 | 🗂 Detail Design (phần API) |
| E8 | Comment: khi nào viết, viết "vì sao" không viết "làm gì" | 🟥 | — |
| E9 | Kiểu dữ liệu đồng bộ giữa hai bên (từ mô tả API) | 🟧 | 🏠 OpenAPI |
| E10 | Phạm vi code: toàn hệ thống / cụm / một tính năng | 🟧 | — |

### F. Nền tảng chung phía server
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| F1 | Giao dịch DB: request ghi mở, đọc không mở; trường hợp ngoại lệ | 🟥 | 🏠 |
| F2 | Phân loại lỗi → mã HTTP → rollback → log → thông báo | 🟥 | 🏠 |
| F3 | Bảng mã HTTP dùng khi nào (200, 201, 204, 400, 401, 403, 404, 409, 422, 429, 500, 502, 503) | 🟥 | — |
| F4 | Khung phản hồi API (thành công, lỗi, phân trang) | 🟥 | 🏠 |
| F5 | Định dạng thông báo, đa ngôn ngữ | 🟥 | 🏠 |
| F6 | Cấu hình, biến môi trường, bí mật | 🟥 | — |
| F7 | Phân trang, sắp xếp, lọc | 🟥 | 🏠 |
| F8 | Đăng nhập, phiên, phân quyền | 🟥 | 🗂 authen_&_author, 🏠 |
| F9 | Giới hạn tần suất, chống dò mật khẩu nhiều tầng | 🟥 | 🗂 Other Issue |
| F10 | Nhật ký kiểm tra (ai đổi gì) | 🟧 | 🏠 |
| F11 | Việc chạy nền, hàng đợi, thử lại | 🟧 | 🗂 Queue, Worker, Background Process |
| F12 | Tải file lên: giới hạn, loại file, lưu trữ, dọn file mồ côi | 🟧 | 🗂 minio, 🏠 |
| F13 | Cache: khi nào, xoá cache khi nào | 🟧 | 🗂 Redis |
| F14 | Ngày giờ, múi giờ, tiền tệ, số | 🟥 | 🗂 Chương 13, 17 (số thực, thời gian) |
| F15 | Xử lý đồng thời: hai người sửa cùng lúc, mua quá số hàng, chống gửi trùng | 🟧 | 🗂 Issues (đồng thời, kho hàng) |
| F16 | Gọi dịch vụ bên ngoài: hết giờ chờ, thử lại, kết quả lạ | 🟧 | 🗂 Issues (API bên thứ ba) |
| F17 | Request quá lớn, lưu lượng tăng đột biến | 🟨 | 🗂 Issues |

### G. Validate theo loại trường (mỗi loại một file, như ví dụ email)
| # | Loại trường | Mức | Nguồn có sẵn |
|---|---|---|---|
| G0 | Quy tắc chung cho mọi trường: chuẩn hoá trước, FE để tiện, BE là cổng cuối, thông báo theo mã | 🟥 | 🗂 Testing |
| G1 | Email | 🟥 | 🏠 |
| G2 | Mật khẩu (độ dài, độ mạnh, nhập lại, khác mật khẩu cũ, mật khẩu đã lộ) | 🟥 | 🏠 |
| G3 | Chữ ngắn: tên, tiêu đề, mã | 🟥 | 🗂 Testing |
| G4 | Chữ dài, nội dung HTML / Markdown (chống chèn script) | 🟥 | 🗂 Testing |
| G5 | Số nguyên, số thực, tiền | 🟥 | 🗂 Testing, Chương 12–13 |
| G6 | Ngày, giờ, khoảng ngày | 🟥 | 🗂 Testing, 🏠 `DateRange` |
| G7 | Số điện thoại | 🟧 | — |
| G8 | URL | 🟧 | — |
| G9 | Lựa chọn (enum, dropdown, nhiều lựa chọn) | 🟥 | 🗂 Testing |
| G10 | Khoá ngoại / ID trên URL (không tồn tại, không phải số, số cực lớn, chuỗi độc hại) | 🟥 | 🗂 Testing |
| G11 | File, ảnh (dung lượng, loại thật, kích thước, virus) | 🟧 | 🗂 minio |
| G12 | Mảng, danh sách ID (trùng, quá nhiều phần tử) | 🟧 | 🏠 |
| G13 | Tham số tìm kiếm, phân trang, sắp xếp | 🟥 | 🗂 Testing |
| G14 | Slug, mã định danh do người nhập | 🟧 | 🏠 |
| G15 | Số giấy tờ, mã số thuế, địa chỉ | 🟨 | — |

### H. Giao diện và trải nghiệm
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| H1 | Bố cục trang chung: header, menu, vùng nội dung, breadcrumb | 🟥 | — |
| H2 | Dialog chung: header cố định (tiêu đề, mô tả ngắn, nút X), body cuộn riêng, footer chứa nút thao tác | 🟥 | — |
| H3 | Khi nào cần hộp xác nhận: xoá, thao tác không hoàn tác được, đóng khi có thay đổi chưa lưu, thao tác hàng loạt | 🟥 | — |
| H4 | Thông báo: loại (thành công, thông tin, cảnh báo, lỗi), màu, vị trí, thời gian tự tắt, nội dung | 🟥 | — |
| H5 | Hiển thị lỗi validate: dưới ô nhập, màu, thứ tự, cuộn và focus ô lỗi đầu tiên | 🟥 | 🗂 Detail Design (Validation) |
| H6 | Hiển thị lỗi hệ thống, mất mạng, hết phiên | 🟥 | — |
| H7 | Trạng thái: đang tải, không có dữ liệu, lỗi, dữ liệu quá dài | 🟥 | 🗂 Testing |
| H8 | Form: khoá nút khi đang gửi, giữ dữ liệu khi lỗi, nút mặc định | 🟥 | 🗂 Testing (bấm nhiều lần) |
| H9 | Bảng dữ liệu: phân trang, sắp xếp, lọc, thao tác hàng loạt, cột dài | 🟥 | 🗂 Testing |
| H10 | Màu sắc, khoảng cách, chữ (design token), chế độ tối | 🟧 | — |
| H11 | Truy cập dễ dàng (bàn phím, đọc màn hình, tương phản) | 🟧 | — |
| H12 | Hiển thị trên điện thoại | 🟧 | — |
| H13 | Mẫu mô tả từng thành phần màn hình | 🟥 | 🗂 Detail Design (UI) |

### I. Log và theo dõi hệ thống
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| I1 | Mức log (debug, info, warning, error, critical) dùng khi nào | 🟥 | — |
| I2 | Log có cấu trúc: các trường bắt buộc (thời gian, id request, người dùng, chức năng, hành động, kết quả) | 🟥 | — |
| I3 | Mỗi lớp code log gì (controller, service, job, gọi ngoài) | 🟥 | — |
| I4 | Không bao giờ log: mật khẩu, token, dữ liệu cá nhân | 🟥 | — |
| I5 | Mã sự kiện để lọc nhanh trên CloudWatch / Grafana / ELK | 🟧 | — |
| I6 | Câu truy vấn log mẫu theo tình huống (một request, một người dùng, một lỗi) | 🟧 | — |
| I7 | Số đo và cảnh báo (tỉ lệ lỗi, thời gian phản hồi) | 🟨 | — |

### J. Kiểm thử
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| J1 | Tháp kiểm thử: unit / tích hợp / đầu cuối — thứ gì test ở tầng nào | 🟥 | 🗂 Unit Test Auto |
| J2 | Công cụ theo công nghệ | 🟥 | 🏠 |
| J3 | Mẫu test case (điều kiện, các bước, kết quả, phân loại) | 🟥 | 🗂 Unit & integrate test case |
| J4 | Mẫu báo cáo minh chứng (môi trường, OK/NG/chờ/còn lại) | 🟥 | 🗂 Unit & integrate test case |
| J5 | Checklist trường hợp biên theo thành phần giao diện | 🟥 | 🗂 Testing |
| J6 | Dữ liệu test: đặt tên, dọn dẹp, cô lập | 🟥 | 🏠 `TMP ` / `E2E ` |
| J7 | Test API (mã lỗi, quyền, dữ liệu sai) | 🟥 | — |
| J8 | Hiệu năng / tải | 🟨 | 🏠 `perf/` |
| J9 | SEO | 🟨 | — |
| J10 | So sánh giao diện bằng ảnh, kiểm tra truy cập dễ dàng | 🟨 | — |
| J11 | Kiểm thử bảo mật cơ bản | 🟧 | 🗂 Testing (chuỗi độc hại) |

### K. Bảo mật
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| K1 | Checklist OWASP Top 10 rút gọn | 🟥 | — |
| K2 | CSRF, CORS, header bảo mật, cookie | 🟥 | 🗂 CSRF |
| K3 | Phiên, token, thu hồi trên nhiều thiết bị | 🟥 | 🗂 Other Issue |
| K4 | Bí mật: lưu ở đâu, xoay vòng, không commit | 🟥 | 🏠 |
| K5 | Dữ liệu cá nhân: ẩn khi hiển thị, xoá khi yêu cầu | 🟧 | — |

### L. Tài liệu dự án
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| L1 | Cấu trúc tài liệu theo cụm, có liên kết qua lại | 🟥 | 🏠 `docs/` |
| L2 | Mẫu tài liệu một tính năng | 🟥 | 🏠 `docs/features/` |
| L3 | Mẫu thiết kế chi tiết (màn hình, API) | 🟥 | 🗂 Detail Design |
| L4 | Ghi quyết định kiến trúc (ADR) | 🟧 | 🏠 `docs/adr/` |
| L5 | Khi code đổi thì tài liệu nào phải đổi (dò theo thay đổi git) | 🟥 | — |
| L6 | Nhật ký thay đổi | 🟧 | 🏠 PROGRESS |

### M. Dữ liệu và DB
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| M1 | Đặt tên bảng, cột, index, khoá | 🟥 | — |
| M2 | Migration: lặp lại an toàn, không phá dữ liệu, có đường lui | 🟥 | 🗂 Migrate Schema / Data |
| M3 | Xoá mềm, khoá ngoại, ràng buộc unique ở DB | 🟥 | 🏠 |
| M4 | Index, truy vấn N+1, xem kế hoạch truy vấn | 🟧 | 🗂 Chương 16, 🏠 `perf/` |
| M5 | Sao lưu, khôi phục, chuyển DB | 🟧 | 🗂 backup-restore, Làm sao migrate db |

### N. Phát hành và vận hành
| # | Mục | Mức | Nguồn có sẵn |
|---|---|---|---|
| N1 | Các môi trường (local, dev, staging, production) và khác biệt | 🟧 | 🗂 Unit & integrate test case |
| N2 | CI: các cổng kiểm tra, thứ tự | 🟧 | 🏠 `ci/` |
| N3 | Triển khai, quay lui khi lỗi | 🟨 | 🏠 `infra/` |
| N4 | Kiểm tra sức khoẻ, giám sát | 🟨 | 🏠 |

### O. Duy trì bộ chuẩn
| # | Mục | Mức |
|---|---|---|
| O1 | Cách viết một file chuẩn (khung, độ dài, phần đầu YAML) | 🟥 |
| O2 | Cách duyệt hộp đề xuất | 🟥 |
| O3 | Dọn định kỳ: gộp, xoá trùng, đánh dấu lỗi thời | 🟧 |
| O4 | Cài vào dự án mới, gỡ ra | 🟥 |

**Tổng: khoảng 130 mục.** Không viết hết một lần — xem lộ trình.

---

## 5. Cấu trúc thư mục đề xuất

```
playbook/
├── README.md               cách dùng, cách cài sang dự án khác
├── INDEX.md                mục lục: chủ đề → file → khi nào dùng (luôn ngắn)
├── workflow/               nhóm A: phân loại, kế hoạch, đối chiếu, minh chứng, bài học
├── onboarding/             nhóm B: tiếp cận dự án, mẫu hồ sơ dự án
├── standards/              nhóm C–F, H–N: mỗi chủ đề một file
├── validate/               nhóm G: mỗi loại trường một file
├── testing/                nhóm J: checklist + mẫu
├── templates/              mẫu: kế hoạch, thiết kế chi tiết, test case, minh chứng, PR, ADR, tài liệu tính năng
├── stacks/                 áp chuẩn chung vào từng công nghệ: laravel.md, nextjs.md, … (thêm khi gặp)
├── inbox/                  bài học AI đề xuất, chờ bạn duyệt
├── skill/SKILL.md          "kỹ năng" để Claude Code biết bộ chuẩn tồn tại và gọi khi cần (tiếng Anh)
└── install.sh              copy sang dự án, thêm vào .git/info/exclude, đăng ký kỹ năng
```

Tách `standards/` (chung, mọi công nghệ) với `stacks/` (cách làm trên Laravel, Next.js…) chính là chỗ giữ bộ chuẩn **bền qua nhiều công nghệ**: đổi công nghệ chỉ thêm một file `stacks/`, chuẩn chung không đổi.

---

## 6. Lộ trình

| Giai đoạn | Làm gì | Kết quả để kiểm tra |
|---|---|---|
| 0 | Bạn duyệt đề xuất này, trả lời các câu hỏi ở mục 7 | — |
| 1 | Khung thư mục, `INDEX.md`, nhóm A (cách làm việc), kỹ năng, script cài | Thử một việc nhỏ trong repo này: AI có gọi đúng file, tốn bao nhiêu token |
| 2 | Các mục 🟥 của nhóm D, E, F, I (git, thiết kế code, nền tảng server, log) | Đối chiếu với code Second Memory → danh sách chỗ lệch |
| 3 | Nhóm G (validate) + J (kiểm thử), rút từ `Testing.md`, `Detail Design.md` trong kho Obsidian | Dùng cho một form có thật |
| 4 | Nhóm H (giao diện) + mẫu tài liệu | Đối chiếu với màn hình admin |
| 5 | `stacks/laravel.md`, `stacks/nextjs.md`; các mục 🟧 còn lại | — |
| 6 | Cài thử vào một dự án công ty; chạy vòng bài học | Bài học đầu tiên trong hộp đề xuất |
| Sau đó | Các mục 🟨 khi dự án cần | — |

Mỗi giai đoạn kết thúc bằng một lần bạn đọc và sửa — vì đây là **kinh nghiệm của bạn**, AI chỉ soạn nháp và tổng hợp.

---

## 7. Cần bạn quyết định

1. **Vị trí và tên:** thư mục `playbook/` ở gốc repo Second Memory — được không, hay bạn muốn tên khác?
2. **Commit vào repo Second Memory?** Repo này bạn đẩy lên GitHub. Nếu repo công khai, bộ chuẩn cũng công khai. Lựa chọn: (a) commit bình thường, (b) loại trừ khỏi git của repo này, chỉ để trên máy (mất nếu hỏng máy), (c) tách thành repo riêng tư.
3. **Định dạng:** Markdown + phần đầu YAML (khuyến nghị), hay bạn vẫn muốn JSON?
4. **Ngôn ngữ:** nội dung tiếng Việt, riêng file kỹ năng cho AI bằng tiếng Anh (giống quy ước repo hiện tại) — đồng ý không?
5. **Bắt đầu từ giai đoạn 1** hay bạn muốn sửa danh mục ở mục 4 trước?
