---
chu_de: khoi-tao-du-an
ap_dung: [du-an-moi, du-an-cu, doi-cong-cu]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Khởi tạo dự án — từ tri thức cá nhân ra bộ tài liệu riêng của dự án

AI đọc file này **một lần** khi bắt đầu (hoặc nhận lại) một dự án. Kho tri thức chỉ là **nguồn tham khảo để khởi tạo**: AI đọc, kiểm môi trường và dự án thật, hỏi mình phần còn thiếu, rồi **tự tạo** bộ tài liệu, quy tắc và công cụ của dự án theo các đề mục dưới đây. Sau bước này mọi việc hằng ngày dùng tài liệu dự án; tri thức cá nhân chỉ được mở theo bảng "Tham khảo tri thức cá nhân" trong quy tắc của dự án.

## Câu lệnh

Mở dự án, rồi:

> *Tri thức cá nhân ở `<đường dẫn>/Playbook`. Đọc `02 Tiếp cận dự án mới/Khởi tạo dự án.md` và khởi tạo cho dự án này. Chưa sửa code.*

AI đọc kho **tại chỗ** theo đường dẫn, không clone, không chép cả kho vào dự án, và chỉ mở file trong **phạm vi đọc** ở [[Playbook]] (đi qua [[Mục lục cho AI]]). Phần ngoài phạm vi của kho — không mở, không tìm kiếm.

## Bộ tài liệu riêng: chỉ trong thư mục dự án này, trên máy này

Kết quả khởi tạo (`.me/` và mọi file AI tạo cho mình: điểm vào tự nạp, hook, lệnh) giống `node_modules`: **nằm trong thư mục dự án**, mở bằng editor / công cụ nào cũng thấy, nhưng **không bao giờ commit hay push** lên bất kỳ repo nào, kể cả repo riêng tư. Khác `node_modules` ở chỗ đây là tài liệu sửa được, không phải gói cài.

- **Loại trừ cục bộ của git** (ví dụ `.git/info/exclude`), không sửa file bỏ qua dùng chung của repo (việc đó cũng là một commit).
- **Clone về là chưa có:** dự án mới, dự án cũ, máy mới hay máy cài lại đều như nhau: chưa có `.me/` = chưa khởi tạo. Có `.me/` = dự án này trên máy này đã khởi tạo, không làm lại.
- **Chỉ khi mình yêu cầu:** mình dán câu lệnh khởi tạo kèm đường dẫn kho tri thức **trên máy đó**. AI ghi đường dẫn này vào quy tắc của dự án. Mình không yêu cầu → AI không tự khởi tạo, không nhắc.
- Không cài gì ở cấp máy / người dùng. Dự án khác, máy khác không bị ảnh hưởng.

## Đầu ra — các khối thông tin dự án phải có

AI tự quyết tên file và cách chia cho hợp công cụ và dự án; chỉ cần đủ các khối, đặt trong thư mục cá nhân `.me/` ở gốc dự án, **không commit** vào bất kỳ repo nào (loại trừ cục bộ của git).

| Khối | Chứa gì | Đề mục theo |
|---|---|---|
| **Quy tắc** | Dự án tóm tắt, lệnh, cách đọc, cỡ việc, điều luôn hỏi, cách viết code, trước khi báo xong, git / test / minh chứng, tài liệu, bảng tham khảo tri thức, riêng của dự án. **Được nạp mọi phiên** → ngắn (≤ ~150 dòng) | [[Mẫu - Quy tắc dự án]] |
| **Bản đồ** | Tổng quan, tài liệu ↔ code, tài liệu nguồn, thành phần dùng chung, thuật ngữ, nghiệp vụ ẩn | [[Mẫu - Bản đồ dự án]] |
| **Tài liệu** | Spec, thiết kế chi tiết, nghiệp vụ ẩn — **chỉ** khi dự án thiếu, và chỉ cho phần mình làm trước | [[Tài liệu dự án]], [[Detail Design]] |
| **Quyết định** | Quyết định đã chốt: ngày, vì sao | — |
| **Ghi chú dự án** | Chỗ vướng, bẫy, lỗi hay mắc chỉ đúng ở dự án này | — |
| **Tiến độ** | Việc dài đang dở, để phiên sau làm tiếp | — |
| **Đề xuất tri thức** | Điều học được dùng lại được, chờ đợt cập nhật | [[Ghi chú và cập nhật theo đợt]] |
| **Công cụ và hook** | Điểm vào tự nạp, kiểm tra sau khi sửa, review trước commit, git hook, kiểm tra lệch tài liệu | [[Công cụ và hook của dự án]] |

Khối nào không cần thì không tạo.

## Các bước

### 1. Kiểm tra môi trường (tự kiểm trước, hỏi sau)

AI tự kiểm, chỉ hỏi phần không kiểm được:

- [ ] Hệ điều hành, shell; công cụ dòng lệnh đã có (git, trình quản lý gói, container, công cụ kiểm tra).
- [ ] **Công cụ AI đang dùng** và cơ chế nó hỗ trợ: file quy tắc tự nạp, hook, lệnh / kỹ năng tự định nghĩa, tác tử phụ, bộ nhớ.
- [ ] **Editor** và plugin đã cài (đọc được thì đọc, không thì hỏi); dự án có danh sách plugin đề xuất không.
- [ ] Ngôn ngữ, framework, phiên bản, DB của dự án (từ file cấu hình).
- [ ] Dự án đã có gì cho AI: file quy tắc, kỹ năng, hook của nhóm.
- [ ] **Máy**: cá nhân hay công ty; hệ điều hành thật (macOS, Windows, Windows + WSL: kho ghi chú và dự án nằm phía nào); chính sách công ty về cài công cụ, công cụ nối thêm cho AI, gửi mã / tài liệu cho dịch vụ AI.

### 2. Quét dự án và phân loại (chỉ cấu trúc, không đọc code)

Cây thư mục 2–3 cấp, file cấu hình (build / lint / CI / container), README, file quy tắc cho AI, thư mục tài liệu, số file, commit gần đây. Dự án lớn: giao từng module cho tác tử phụ (nếu có), mỗi module trả vài dòng.

| Loại | Dấu hiệu | Tri thức cá nhân dùng tới đâu | Quy mô tài liệu |
|---|---|---|---|
| **A. Mới** | Mình khởi tạo / repo gần trống | Nguồn chính: "Lựa chọn của tôi" thành quy tắc, công cụ, lớp dùng chung — phần là code thì commit vào dự án | Quy tắc đủ; bản đồ lớn dần theo tính năng |
| **B. Cũ cần nâng cấp** | Thiếu quy ước / lint / test / tài liệu, hoặc quy ước lệch nhau | So rồi **đề xuất** (chủ đề → dự án đang làm → tri thức chọn → sửa / giữ / ghi khác biệt). Dự án công ty: chỉ đề xuất cho nhóm | Quy tắc + bản đồ + tài liệu cho phần thiếu |
| **C. Đã chuẩn** | Có quy tắc cho AI / sổ tay, lint + test + CI, tài liệu cập nhật | Gần như không: chỉ chốt chặn dự án chưa phủ | Quy tắc rất ngắn; bản đồ trỏ tới tài liệu của dự án |
| **D. Nặng** | Nhiều module / repo / công nghệ — kết hợp A, B hoặc C | Như loại đi kèm, **chỉ cho module mình làm** | Bản đồ theo module; module khác một dòng, đào sâu khi chạm |

Báo loại + lý do + kết quả bước 1, **chờ xác nhận**.

### 3. Hỏi mình (≤ 6 câu, chỉ câu chưa tự trả lời được)

- Mục tiêu dự án, phần mình phụ trách, ai giao / duyệt / kiểm thử (vai trò).
- Dự án công ty hay cá nhân; chính sách dùng AI với source.
- Quy trình nhóm nếu repo không nói: branch, commit message, review / merge, ticket, cách test và minh chứng nộp cho ai.
- Gói / giới hạn của công cụ AI nếu ảnh hưởng cách làm.
- Tài liệu ngoài repo: spec, thiết kế, ticket, sơ đồ DB — ở đâu, tin được tới đâu.

### 4. Đọc dự án

Thứ tự **nghiệp vụ → tài liệu → môi trường → code → công cụ → quy ước**:

- [ ] Nghiệp vụ: vấn đề, người dùng, thuật ngữ, ràng buộc (dữ liệu cá nhân, pháp lý).
- [ ] Tài liệu có sẵn: là gì, ở đâu, tin được tới đâu (cũ → "code thắng").
- [ ] Môi trường: cách chạy local, môi trường được đụng, bí mật lấy ở đâu (**không** ghi bí mật vào `.me/`).
- [ ] Code: đi theo **một request** (route → quyền → validate → xử lý → DB → phản hồi) và **một màn hình** (trang → thành phần → API → hiển thị lỗi); phần dùng chung đã có.
- [ ] Công cụ: định dạng, lint, kiểu, test, hook, CI — lệnh nào, mất bao lâu.

### 5. Đối chiếu tri thức với dự án — mỗi chủ đề một quyết định

Đi qua [[Mục lục cho AI]]; chủ đề nào liên quan tới dự án thì mở đúng file đó (chỉ "Lựa chọn của tôi" + đề mục checklist, không đọc hết), so với cách dự án đang làm:

| So sánh | Lấy |
|---|---|
| Hai bên tương đồng | Bên nào cũng được — ưu tiên cách đã có trong dự án |
| Cùng giá trị, khác cách làm | **Dự án** |
| Dự án chưa có | **Tri thức cá nhân**, viết lại cho công nghệ của dự án |
| AI thấy phương án tốt hơn cả hai | Đề xuất kèm lý do và đánh đổi ở bước 7; mình duyệt mới áp dụng. Dự án công ty: không tự đổi quy ước nhóm, chỉ đề xuất |

Sau đó mỗi chủ đề ghi theo **một** trong bốn cách:

| Quyết định | Khi | Ghi vào |
|---|---|---|
| **Theo dự án** | Dự án đã có quy ước / code mẫu cho chủ đề này | Đường dẫn trong bản đồ (không chép) |
| **Chép vào quy tắc** | Lựa chọn của tôi cần nhất quán mà dự án chưa có (khung phản hồi, mã lỗi, thông báo, dialog, đặt tên, git…) | Quy tắc — chỉ phần áp dụng, viết lại cho công nghệ của dự án |
| **Tham khảo khi chạm** | Checklist dài, chỉ cần khi việc chạm tới: chốt chặn (quyền, dữ liệu nhập, DB, tiền, file, phát hành, log nhạy cảm, gửi ra ngoài) và "Giải pháp thường gặp" | Bảng "Tham khảo tri thức cá nhân": file + **phạm vi đọc** |
| **Bỏ** | Kiến thức phổ biến, máy đã kiểm (lint, test, kiểu), hoặc không liên quan | Không ghi |

AI được đánh giá và điều chỉnh cho tối ưu với dự án, nhưng mỗi điều chỉnh khác cả dự án lẫn tri thức phải **nêu rõ** trong đề xuất, không lặng lẽ thay. Chưa chắc nên chép hay không → "tham khảo khi chạm". Đợt cập nhật sau bỏ dòng nào thực tế không giúp gì ([[Ghi chú và cập nhật theo đợt]]).

### 6. Đề xuất công cụ

Từ kết quả bước 1 và [[Công cụ kiểm tra tự động]], [[Công cụ và hook của dự án]]:

- Plugin editor / công cụ dòng lệnh nên cài **cho mình** — đúng editor, đúng ngôn ngữ, chỉ cái còn thiếu.
- Công cụ nên **đề xuất cho nhóm** (không tự thêm vào repo).
- Công cụ bổ trợ cho AI (hiểu kho mã lớn, kho ghi chú, quy trình đặc tả): so dấu hiệu của dự án **và điều kiện của máy** (bước 1) với [[Công cụ bổ trợ cho AI lập trình]], chỉ đề xuất cái khớp, kèm đánh đổi và nơi đặt tệp. Không có công cụ nào khớp → nói rõ "không cần thêm" và lý do, không bỏ qua im lặng.
- Khả năng nào trong [[Công cụ và hook của dự án]] sẽ tạo, hiện thực bằng cơ chế gì của công cụ AI hiện tại.
- Điểm mù riêng của dự án — chỗ dễ sai mà tri thức chưa nói.

### 7. Đề xuất → mình chỉnh → AI tạo

Một bảng ngắn: loại, các khối sẽ tạo, chủ đề theo bốn quyết định, công cụ và hook, quy trình git / test / minh chứng, điểm mù. Mình chỉnh xong AI mới:

1. Tạo **quy tắc** theo đề mục của [[Mẫu - Quy tắc dự án]] — điền thông tin thật, bỏ mục không dùng.
2. Tạo **bản đồ** theo [[Mẫu - Bản đồ dự án]].
3. Tạo tài liệu còn thiếu cho phần mình làm trước; phần khác tạo khi việc chạm tới.
4. Tạo công cụ và hook đã chốt; thử chạy từng cái một lần, báo kết quả thật.
5. Đặt **mốc đồng bộ tài liệu**.
6. Kiểm `git status`: không hiện file cá nhân nào.

## Sau khởi tạo

Quy tắc, tài liệu, công cụ và hook là **của riêng dự án này**, không ảnh hưởng dự án khác. Hằng ngày AI làm theo quy tắc dự án như prompt bình thường; không đối chiếu lại tri thức, không tự thêm / đổi quy ước hay hook. Thấy nên đổi → đề xuất một dòng, chờ mình yêu cầu.

## Làm lại — chỉ khi mình yêu cầu

Những lúc nên yêu cầu:

- Đổi công cụ AI hoặc editor → bước 1 + 6, sửa quy tắc và hook. (Clone sang máy khác không phải "làm lại": đó là một lần khởi tạo mới.)
- Dự án đổi công nghệ / cấu trúc lớn, hoặc nhận phần việc khác hẳn → bước 2, 4, 5 cho phần thay đổi.
- Tri thức cá nhân có thay đổi đáng áp dụng → bảo AI so quy tắc dự án với file tương ứng và đề xuất.

Pull code mới **không** phải lý do làm lại — kiểm tra lệch tài liệu báo phần cần sửa.

## Kết thúc dự án

1. Đợt cập nhật từ đề xuất tri thức ([[Ghi chú và cập nhật theo đợt]]).
2. Đợt CV cuối ([[Thống kê công việc cho CV]]).
3. Gỡ git hook đã thêm, rồi xoá `.me/` và các file cá nhân đã tạo (liệt kê trong quy tắc).
