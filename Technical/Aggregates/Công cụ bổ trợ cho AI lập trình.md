---
cap_nhat: 2026-10-27
---
# Công cụ bổ trợ cho AI lập trình

Dùng ở bước 6 "Đề xuất công cụ" của [[Khởi tạo dự án]]: AI so **dấu hiệu** của dự án với bảng dưới, chỉ đề xuất công cụ khớp dấu hiệu, kèm lý do, đánh đổi và nơi đặt tệp (commit vào repo, hay chỉ nằm trong thư mục dự án và loại trừ cục bộ như `.me/`). Mình duyệt rồi mới cài.

Đánh giá ngày 2026-10-27. Công cụ AI đổi rất nhanh: quá 6 tháng thì kiểm lại phiên bản, cách cài và đánh giá độc lập trước khi đề xuất. Con số quảng cáo của nhà làm công cụ không tính là đánh giá.

## Chọn nhanh

| Công cụ | Đề xuất khi | Không đề xuất khi |
|---|---|---|
| **Graphify**: sơ đồ quan hệ mã + tài liệu | Kho mã lớn (nhiều nghìn tệp, nhiều module / repo), repo lạ cần hiểu nhanh, việc sửa lan nhiều tệp | Dự án vừa và nhỏ (~1000 tệp trở xuống): AI tự tìm đã đủ nhanh |
| **Basic Memory**: kho ghi chú Markdown + tìm theo nghĩa cho AI | Kho ghi chú lớn nằm ngoài repo, không nhớ ghi chú nào ở đâu, cần dùng chung cho nhiều dự án / nhiều công cụ AI | Tài liệu nằm trong repo và có mục lục; công cụ AI đã có bộ nhớ + kỹ năng đọc tri thức cá nhân |
| **GitHub Spec Kit**: quy trình đặc tả có cổng | Dự án mới (loại A), làm nhóm cần khuôn chung, tính năng lớn yêu cầu còn mơ hồ, chưa có quy trình riêng | Việc nhỏ; dự án đã có quy trình đầy đủ; dự án công ty khi nhóm chưa đồng ý (tệp nằm trong repo) |
| **OpenSpec**: đặc tả sống + phần thay đổi | Dự án mới, hoặc dự án có sẵn mã nhưng **chưa có** tài liệu chức năng sống (loại A, B, D); muốn tài liệu luôn khớp mã | Dự án đã có tài liệu chức năng sống và quy trình riêng (thử thật: thành hai nguồn sự thật, xem mục OpenSpec); dự án công ty khi nhóm chưa đồng ý |
| Context7, DeepWiki | Không, xem mục cuối | — |

Spec Kit và OpenSpec **chọn một**, không dùng cả hai.

## Theo máy — kiểm trước khi đề xuất

| Điều kiện | Ảnh hưởng |
|---|---|
| **macOS** | Cài thẳng (uv, npm hoặc Homebrew); kho ghi chú và dự án cùng một hệ tệp → không có bẫy đồng bộ |
| **Windows + WSL**, kho ghi chú trên ổ Windows | Công cụ chạy trong WSL đọc kho qua `/mnt/c/…`: chậm hơn, việc tự phát hiện tệp vừa sửa có thể trượt → thử ngay sau khi cài |
| **Windows thuần** | Ưu tiên bản cài cho Windows (PowerShell / bản chạy độc lập) nếu công cụ có |
| **Máy công ty** | Hỏi chính sách trước: cài công cụ, công cụ nối thêm cho AI, gửi mã / tài liệu cho dịch vụ AI. Công cụ ghi tệp vào repo (Spec Kit, OpenSpec) cần nhóm đồng ý, nếu không thì chỉ để trong loại trừ cục bộ của git |
| Công cụ cài ở cấp máy (uv tool, npm -g, cấu hình người dùng của công cụ AI) | Ghi vào quy tắc dự án: đã cài gì, ở cấp nào, gỡ thế nào |

## Graphify

- **Làm gì:** phân tích mã ngay trên máy (không gửi đi), tài liệu / PDF / ảnh thì gửi cho mô hình AI. Tạo `graphify-out/`: sơ đồ HTML, báo cáo (nút trung tâm, cụm chức năng, liên kết bất ngờ), dữ liệu cho AI truy vấn. Có hook đẩy AI tra sơ đồ trước khi tìm / đọc tệp.
- **Cài:** cần Python ≥ 3.10 + uv. `uv tool install graphifyy` (hai chữ y; các gói tên gần giống không phải của họ) → `graphify install` → trong công cụ AI chạy `/graphify .`. Chỉ phân tích mã: `--code-only`. Cập nhật: `--update`, hoặc `graphify hook install` để tự dựng lại khi commit.
- **Đánh giá thật:** quảng cáo 71 lần là trường hợp tốt nhất trên kho mã khổng lồ; phân tích độc lập 7–49 lần; một buổi thử 10 câu hỏi trên dự án thật chỉ tiết kiệm 7–8% (câu trả lời tốt hơn); một người dùng báo không hơn Claude Code tự tìm, mỗi lần hỏi sơ đồ tốn ~2.000 token.
- **Bẫy:**
  - Sơ đồ cũ → AI trả lời sai. Bắt buộc có cách tự cập nhật.
  - Hook chen trước khi đọc tệp được báo là lỗi với bản Claude Code mới (một nguồn, chưa kiểm chứng): thử ngay sau khi cài.
  - `graphify-out/` đưa vào loại trừ git.
  - Cài global (`uv tool`): trái nguyên tắc "không cài global" của Playbook → ghi rõ ngoại lệ trong quy tắc dự án, hoặc chạy trong container dùng xong bỏ.

## Basic Memory

- **Làm gì:** ghi chú là tệp Markdown thường (mở được bằng Obsidian). Nó lập chỉ mục SQLite, tìm theo từ khoá + theo nghĩa, hiểu quan hệ `- [loại] nội dung` và `- quan hệ [[Ghi chú]]`. AI có thao tác tìm / đọc / ghi / sửa ghi chú, và gom ghi chú liên quan qua liên kết. AI chỉ tìm khi được dặn (quy tắc dự án) hoặc tự thấy cần; không phải trí nhớ tự động.
- **Cài:** cần uv. `uv tool install basic-memory --prerelease=allow` (thiếu cờ này uv có thể cài bản cũ) → `claude mcp add basic-memory -- uvx --prerelease=allow basic-memory mcp` → `basic-memory project add "<tên>" <thư mục>`. Tuỳ chọn: `bm install claude-code` (tóm tắt đầu phiên, lưu trước khi nén ngữ cảnh). Khởi động lại công cụ AI sau khi cài.
- **Bẫy:**
  - **Phạm vi đọc:** chỉ trỏ vào thư mục được phép (ví dụ `Playbook/`), **không trỏ cả kho Obsidian**, nếu không AI tìm được cả ghi chú cá nhân.
  - **Quyền ghi:** AI ghi thẳng được vào ghi chú → trái quy tắc "chỉ thêm một dòng vào Ghi chú.md". Dặn rõ trong quy tắc dự án: chỉ đọc.
  - **Tiếng Việt:** mô hình mặc định `bge-small-en-v1.5` là tiếng Anh → tìm theo nghĩa bằng tiếng Việt yếu. Đổi sang `paraphrase-multilingual-MiniLM-L12-v2` rồi `bm reindex --embeddings`; tài liệu không nêu tiếng Việt → phải thử vài câu hỏi thật.
  - **Kho trên ổ Windows, AI chạy trong WSL:** việc theo dõi tệp thay đổi qua ranh giới này có thể chậm hoặc không bắt được → sửa một ghi chú bằng Obsidian rồi tìm thử ngay sau khi cài.
  - Trùng chức năng với bộ nhớ có sẵn của công cụ AI và kỹ năng đọc tri thức cá nhân: kiểm cái đã có trước khi thêm.
- **Giữ đúng hai lớp tài liệu** (tài liệu dự án dùng hằng ngày; tri thức cá nhân chỉ khi khởi tạo / mình yêu cầu / bảng tham khảo của dự án): Basic Memory **không tự phân biệt** hai lớp. Nó chỉ có "dự án" (mỗi cái một thư mục) và AI chọn dự án nào để tìm. Cấu hình nằm ở cấp máy (`~/.basic-memory/`), dùng chung cho mọi repo trên máy đó. Muốn đúng hai lớp:
  1. Tạo **hai** dự án Basic Memory: một cho tài liệu của dự án đang làm (thư mục tài liệu trong repo hoặc `.me/`), một cho `Playbook/`. Không dự án nào trỏ cả kho.
  2. Đặt dự án tài liệu làm **mặc định**; khi đổi sang làm repo khác thì đổi mặc định theo.
  3. Ghi vào quy tắc dự án: chỉ tìm trong dự án tài liệu; dự án `Playbook` chỉ dùng khi mình yêu cầu hoặc theo bảng "Tham khảo tri thức cá nhân", và chỉ đọc. Ranh giới này do **quy tắc** giữ, công cụ không chặn hộ.
  4. Thử: hỏi một câu chỉ Playbook có câu trả lời → AI không được tìm sang Playbook nếu chưa được phép.

## GitHub Spec Kit

- **Làm gì:** "hiến pháp" dự án (`/speckit.constitution`) → mô tả yêu cầu (`specify`) → hỏi lại chỗ mơ hồ (`clarify`) → kế hoạch kỹ thuật (`plan`) → chia việc (`tasks`) → kiểm tra yêu cầu / kế hoạch / việc có khớp nhau không (`analyze`) → làm (`implement`), thêm `checklist`, `converge`. Hỗ trợ chính thức Claude Code; các lệnh là khuôn câu lệnh + script, AI làm theo tốt.
- **Cài:** Python ≥ 3.11 + uv. `uv tool install specify-cli` → dự án có sẵn: `specify init --here --integration claude` (commit hoặc sao lưu trước, lệnh có thể ghi đè).
- **Bẫy:**
  - Mỗi tính năng một thư mục đặc tả = **ảnh chụp lúc làm**, không tự thành tài liệu sống của hệ thống. Vẫn cần tài liệu chức năng riêng.
  - Bản trước đây tạo **nhánh git mới cho mỗi tính năng**: trái quy tắc một nhánh nếu dự án có → kiểm khi cài, tắt nếu được.
  - Nặng: nhiều Markdown, nhiều bước. Việc nhỏ thì bỏ qua quy trình.
  - Khuôn mẫu tiếng Anh; nội dung viết tiếng Việt được.
- **Đáng học kể cả khi không cài:** bước hỏi lại chỗ mơ hồ, bước kiểm yêu cầu ↔ kế hoạch ↔ việc có khớp nhau.

## OpenSpec

- **Làm gì:** đặc tả sống ở `openspec/specs/`; mỗi thay đổi một thư mục `openspec/changes/<tên>/` (đề xuất, **phần khác biệt** của đặc tả: thêm / sửa / bỏ yêu cầu, thiết kế, việc). Xong thì `archive` gộp phần khác biệt vào đặc tả sống. Lệnh Claude Code: `/opsx:explore`, `/opsx:propose <tên>`, `/opsx:apply`, `/opsx:archive`. Yêu cầu viết `### Requirement:` + `#### Scenario:` dạng **WHEN / THEN**.
- **Cài:** `npm install -g @fission-ai/openspec@latest` (Node ≥ 20.19) hoặc bản chạy độc lập không cần Node → `openspec init`.
- **Khác Spec Kit:** tài liệu là thứ dẫn đường và **luôn là hiện trạng mới nhất**; nhẹ hơn, không có cổng cứng.
- **Bẫy:** thay đổi không đi qua `propose` (sửa vội, người khác sửa) làm đặc tả lệch → vẫn cần kiểm tra lệch tài liệu ([[Công cụ và hook của dự án]]). Tệp nằm trong repo.
- **Thử thật** (2026-10-27, bản 1.15.0, một đề xuất trên dự án đã có tài liệu chức năng đầy đủ):
  - `openspec init --tools claude --language vi`: tài liệu viết tiếng Việt được, đề mục và từ khoá SHALL / WHEN / THEN giữ tiếng Anh; `openspec validate --strict` kiểm được định dạng. Tạo 6 kỹ năng + 6 lệnh trong thư mục của công cụ AI (tốn thêm ngữ cảnh mỗi phiên).
  - Tốt: mỗi yêu cầu có tình huống kiểm được (= test case); mỗi việc ghi cách kiểm; tách rõ "vì sao / cái gì" với "làm thế nào"; bắt ghi điều **không** làm; bắt đọc mã trước khi viết nên lộ ra chỗ thiếu thật.
  - Vướng: dự án đã có tài liệu sống thì `openspec/specs/` trống → **hai nguồn sự thật** cho tới khi chuyển hết tài liệu cũ sang định dạng của nó. Danh sách việc và bước "archive" trùng với bảng công việc riêng của dự án. Quy trình của nó muốn hỏi chủ dự án **trước** khi viết, trong khi đề xuất cần duyệt lại muốn đưa phương án ra **sau**. Một đề xuất ra 4 tệp, khoảng 250 dòng.
  - Kết luận: hợp nhất khi dùng **từ đầu dự án** (tài liệu sống sinh ra từ đặc tả ngay). Dự án đã có tài liệu và quy trình riêng → mượn ý (tình huống KHI / THÌ, cách kiểm cho từng việc, "không làm") thay vì cài.

## Không đề xuất (đã đánh giá)

| Công cụ | Vì sao | Thay bằng |
|---|---|---|
| **Context7**: đưa tài liệu thư viện đúng phiên bản cho AI | Bài đo 89 bài (Marginlab, 4/2026, Codex): 70,8% → 71,9%, nằm trong sai số; AI chỉ gọi 6/89 lần dù được dặn; mỗi phiên tốn thêm ngữ cảnh | Quy tắc dự án: thư viện có phiên bản mới hơn mốc kiến thức của model → tra tài liệu chính thức đúng phiên bản trước khi viết |
| **DeepWiki / deepwiki-open**: tự sinh wiki từ kho mã | Wiki dài, vẫn phải tự đọc và lọc; chỉ nói được "mã làm gì", không nói được vì sao; bản gốc không mở mã, bản mở chưa kiểm tra bảo mật | Hỏi AI trực tiếp về luồng / logic; câu trả lời quý thì ghi vào tài liệu dự án |

## Nguồn

- Graphify: [GitHub](https://github.com/safishamsi/graphify), [issue #985](https://github.com/Graphify-Labs/graphify/issues/985), [Towards AI](https://pub.towardsai.net/beyond-the-71x-benchmark-knowledge-graphs-for-coding-agents-graphify-and-rivals-compared-c835177a3b55), [RoboRhythms](https://www.roborhythms.com/graphify-review/)
- Basic Memory: [GitHub](https://github.com/basicmachines-co/basic-memory), [tìm theo nghĩa](https://github.com/basicmachines-co/basic-memory/blob/main/docs/semantic-search.md), [tài liệu](https://docs.basicmemory.com/start-here/what-is-basic-memory)
- Spec Kit: [GitHub](https://github.com/github/spec-kit), [cài đặt](https://github.com/github/spec-kit/blob/main/docs/installation.md)
- OpenSpec: [GitHub](https://github.com/Fission-AI/OpenSpec)
- So sánh quy trình đặc tả: [CodeMySpec](https://codemyspec.com/blog/best-spec-driven-development-tools), [SSOJet](https://ssojet.com/blog/best-spec-driven-development-tools)
- Context7: [Marginlab](https://marginlab.ai/blog/do-mcps-improve-coding-agents-part-1)
