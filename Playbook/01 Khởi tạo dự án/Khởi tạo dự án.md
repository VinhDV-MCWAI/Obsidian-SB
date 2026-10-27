---
chu_de: khoi-tao-du-an
ap_dung: [du-an-moi, du-an-cu, doi-cong-cu]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Khởi tạo dự án

AI đọc file này **một lần** khi bắt đầu, nhận lại, hoặc mở dự án trên máy mới. Kết quả: bộ quy tắc, checklist, tài liệu, lệnh / kỹ năng, hook **riêng của dự án**, viết từ dự án thật và chỉ mượn Playbook ở chỗ dự án còn thiếu. Sau bước này dự án **tự đủ**: mọi việc dùng bộ của dự án, không mở lại Playbook ([[Playbook]], nguyên tắc 1–2).

Sau: [[Quy trình một việc]] cho từng việc.

## `.me/` — bộ riêng của dự án trên máy này

- Mọi thứ khởi tạo tạo ra (`.me/`, điểm vào tự nạp, hook, lệnh) nằm **trong thư mục dự án**, như `node_modules`: mở bằng editor nào cũng thấy, nhưng **không bao giờ commit hay push** lên bất kỳ repo nào, kể cả repo riêng.
- Loại trừ bằng **cơ chế loại trừ cục bộ của git** (`.git/info/exclude`), không sửa file bỏ qua dùng chung của repo (đó cũng là một commit).
- **Có `.me/` = đã khởi tạo, không làm lại.** Chưa có (clone mới, máy mới, dự án mới đều như nhau) → AI hỏi **một câu** có khởi tạo không; mình đồng ý thì đưa đường dẫn kho trên máy đó. Mình từ chối → AI ghi nhớ cho riêng dự án đó, không hỏi lại. Không bao giờ tự khởi tạo.
- Cấp người dùng chỉ có kỹ năng `playbook` và một quy tắc ngắn "hỏi khi chưa có `.me/`" trong file hướng dẫn chung của công cụ AI. Máy mới phải cài hai thứ này trước.
- Khởi tạo **không commit, không push, không sửa code**.

## Đầu ra

AI tự đặt tên file và cách chia cho hợp công cụ AI và dự án; chỉ cần đủ các khối, khối nào không cần thì không tạo.

| Khối | Chứa gì | Theo |
|---|---|---|
| **Quy tắc** | Nạp **mọi phiên** → ≤ ~150 dòng: dự án tóm tắt, lệnh, cỡ việc + cổng duyệt, cách viết code tối thiểu, git, test, tài liệu, công cụ đã tạo, bảng "khi chạm → mở file nào" | [[Mẫu - Quy tắc dự án]] |
| **Quy tắc theo chủ đề** | Mỗi chủ đề dự án thiếu một file riêng, viết lại từ Playbook theo dự án: quy ước, checklist chốt chặn, review từng mặt…; nạp đúng lúc cần | [[Bộ quy tắc và tài liệu tối thiểu]] |
| **Bản đồ** | Tổng quan, tài liệu ↔ code, thành phần dùng chung, thuật ngữ, nghiệp vụ ẩn | [[Mẫu - Bản đồ dự án]] |
| **Tài liệu** | Chỉ phần dự án thiếu, và chỉ cho phần mình làm trước | [[Tài liệu dự án]] |
| **Quyết định, ghi chú dự án, tiến độ** | Quyết định đã chốt (ngày, vì sao); bẫy riêng của dự án; việc dài đang dở | — |
| **Đề xuất tri thức** | Một dòng mỗi điều học được dùng lại được | [[Ghi chú và cập nhật theo đợt]] |
| **Lệnh / kỹ năng và hook** | Điểm vào tự nạp, lệnh "làm một việc", lệnh review, git hook, quét extension, kiểm tra lệch tài liệu, lệnh đặc thù của dự án | [[Công cụ và hook của dự án]] |

## Các bước

### 1. Kiểm môi trường (tự kiểm trước, hỏi sau)

- [ ] Hệ điều hành thật (macOS, Windows, Windows + WSL: kho ghi chú và dự án nằm phía nào), shell, công cụ dòng lệnh đã có.
- [ ] Máy cá nhân hay công ty; chính sách về cài công cụ và gửi mã / tài liệu cho dịch vụ AI.
- [ ] Công cụ AI đang dùng và cơ chế nó có: file quy tắc tự nạp, hook, lệnh / kỹ năng, tác tử phụ, bộ nhớ, đọc bảng lỗi của editor.
- [ ] Editor và extension đã cài; dự án có danh sách extension đề xuất không.
- [ ] Ngôn ngữ, framework, phiên bản, DB (từ file cấu hình).
- [ ] Dự án đã có gì cho AI: file quy tắc, kỹ năng, hook của nhóm; git hook đang chạy ([[Công cụ và hook của dự án]], mục kiểm hook có sẵn).

### 2. Quét cấu trúc và phân loại (không đọc code)

Cây thư mục 2–3 cấp, file cấu hình (build, lint, CI, container), README, thư mục tài liệu, số file, commit gần đây. Dự án lớn: giao từng module cho tác tử phụ, mỗi module trả vài dòng.

| Loại | Dấu hiệu | Playbook dùng tới đâu |
|---|---|---|
| **A. Mới** | Repo gần trống, mình quyết | Nguồn chính: "Lựa chọn của tôi" thành quy tắc, công cụ, lớp dùng chung — phần là code commit vào dự án |
| **B. Cũ cần nâng cấp** | Thiếu quy ước / lint / test / tài liệu, hoặc lệch nhau | So rồi **đề xuất**; dự án công ty chỉ đề xuất cho nhóm |
| **C. Đã chuẩn** | Có quy tắc cho AI, lint + test + CI, tài liệu cập nhật | Gần như không: chỉ chốt chặn dự án chưa phủ |
| **D. Nặng** | Nhiều module / repo / công nghệ | Như loại đi kèm, **chỉ cho phần mình làm** |

Báo loại + lý do + kết quả bước 1, **chờ xác nhận**.

### 3. Hỏi (≤ 6 câu, chỉ câu chưa tự trả lời được)

Mục tiêu dự án, phần mình phụ trách, ai giao / duyệt / kiểm thử; công ty hay cá nhân, chính sách dùng AI; quy trình nhóm mà repo không nói (nhánh, commit, review, ticket, minh chứng); giới hạn của công cụ AI; tài liệu ngoài repo ở đâu, tin được tới đâu.

### 4. Đọc dự án — nghiệp vụ → tài liệu → môi trường → code → công cụ

- [ ] Nghiệp vụ: vấn đề, người dùng, thuật ngữ, ràng buộc (dữ liệu cá nhân, pháp lý).
- [ ] Tài liệu có sẵn: ở đâu, tin được tới đâu (cũ → "code thắng").
- [ ] Môi trường: cách chạy local, môi trường được đụng, bí mật lấy ở đâu (không ghi bí mật vào `.me/`).
- [ ] Code: đi theo **một request** (route → quyền → validate → xử lý → DB → phản hồi) và **một màn hình** (trang → thành phần → API → hiển thị lỗi); phần dùng chung đã có.
- [ ] Công cụ: định dạng, lint, kiểu, test, hook, CI — lệnh nào, mất bao lâu.

### 5. Đối chiếu — mỗi chủ đề một quyết định

Theo [[Bộ quy tắc và tài liệu tối thiểu]]: đi qua bộ chủ đề tối thiểu, rồi các chủ đề trong [[Mục lục cho AI]] liên quan tới dự án; mỗi chủ đề ghi **theo dự án / chép vào quy tắc / tham khảo khi chạm / bỏ**.

### 6. Đề xuất công cụ

- [ ] Lập **bảng quét và review theo công nghệ** ([[Công cụ kiểm tra tự động]]): phần nào quét bằng gì, ô nào thiếu.
- [ ] Extension / công cụ dòng lệnh nên cài **cho mình** — chỉ cái còn thiếu, đúng editor và ngôn ngữ.
- [ ] Công cụ nên **đề xuất cho nhóm** (không tự thêm vào repo).
- [ ] Công cụ bổ trợ cho AI: so dấu hiệu của dự án và điều kiện máy với [[Công cụ bổ trợ cho AI lập trình]]; không có cái nào khớp → nói rõ "không cần thêm" và lý do.
- [ ] Khả năng nào trong [[Công cụ và hook của dự án]] sẽ tạo, bằng cơ chế gì của công cụ AI hiện tại. Lệnh review luôn có, trừ khi nhóm đã có quy trình tương đương.
- [ ] Điểm mù riêng của dự án — chỗ dễ sai mà Playbook chưa nói.

### 7. Đề xuất → mình chỉnh → AI tạo

Một bảng ngắn: loại dự án, các khối sẽ tạo, quyết định từng chủ đề, công cụ và hook, quy trình git / test / minh chứng, điểm mù. Mình chỉnh xong AI mới:

1. Tạo **quy tắc** theo [[Mẫu - Quy tắc dự án]] (thông tin thật, bỏ mục không dùng) và **quy tắc theo chủ đề**, mỗi chủ đề một file.
2. Tạo **bản đồ** theo [[Mẫu - Bản đồ dự án]].
3. Tạo tài liệu còn thiếu cho phần làm trước.
4. Tạo công cụ và hook đã chốt; chạy thử từng cái, báo kết quả thật.
5. Đặt **mốc đồng bộ tài liệu**.
6. `git status` không hiện file cá nhân nào.
7. **Kiểm tự đủ**: tìm trong bộ vừa tạo mọi đường dẫn tới Playbook — chỉ được còn dòng ghi đường dẫn kho (dùng cho đợt cập nhật). Còn chỗ khác → viết lại nội dung đó vào bộ của dự án.

## Sau khởi tạo

Hằng ngày AI làm theo bộ của dự án; không mở Playbook, không tự thêm / đổi quy ước hay hook. Thấy nên đổi → đề xuất một dòng, chờ mình yêu cầu. Gặp chủ đề bộ riêng chưa có → nói một dòng; mình yêu cầu thì tra Playbook và **bổ sung vào bộ của dự án**.

| Làm lại khi mình yêu cầu | Bước |
|---|---|
| Đổi công cụ AI hoặc editor | 1, 6; sửa quy tắc và hook |
| Dự án đổi công nghệ / cấu trúc lớn, nhận phần việc khác hẳn | 2, 4, 5 cho phần thay đổi |
| Playbook có thay đổi đáng áp dụng | So quy tắc dự án với file đã đổi, đề xuất |
| Xoá bộ cũ và khởi tạo lại toàn bộ | Sao lưu bộ cũ ra ngoài dự án → gỡ hook → xoá → làm lại từ bước 1. **Mang sang bộ mới**: ghi chú dự án, quyết định, tiến độ, đề xuất tri thức chưa xử lý, mốc đồng bộ tài liệu (nếu còn báo lệch chưa xử lý — đặt mốc mới sẽ làm mất thông báo). Dùng lại script / hook cũ đã chạy đúng, chỉ sửa phần lệch với Playbook mới |

Pull code mới **không** phải lý do làm lại — kiểm tra lệch tài liệu báo phần cần sửa.

## Kết thúc dự án

1. Đợt cập nhật tri thức ([[Ghi chú và cập nhật theo đợt]]) và đợt CV cuối ([[Thống kê công việc cho CV]]).
2. Gỡ git hook đã thêm, rồi xoá `.me/` và các file cá nhân đã tạo (liệt kê trong quy tắc dự án).
