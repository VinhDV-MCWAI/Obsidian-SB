---
loai: mau
dat_tai: "khối Quy tắc trong .me/ của dự án"
---
# Quy tắc làm việc: <tên dự án>

> Đề mục cho khối **Quy tắc** của dự án, AI tạo lúc [[Khởi tạo dự án]]. Khối này được **nạp ở mọi phiên** (qua điểm vào tự nạp của công cụ AI) nên giữ ≤ ~150 dòng: điền thông tin thật, bỏ mục không dùng, phần dài để ở bản đồ / tài liệu. Quy ước của nhóm (quy tắc cho AI, tài liệu trong repo) thắng khối này. Cập nhật: <YYYY-MM-DD>.

## Dự án

| | |
|---|---|
| Mục đích | <một câu> |
| Phần mình phụ trách | <module / vai trò> |
| Loại | <A mới / B cũ cần nâng cấp / C đã chuẩn / D nặng> |
| Công nghệ | <ngôn ngữ, framework, DB, phiên bản> |
| Môi trường | <hệ điều hành, editor, công cụ AI — những gì ảnh hưởng cách làm> |
| Không được đụng | <thư mục, dữ liệu, môi trường> |
| Kho tri thức trên máy này | <đường dẫn …/Playbook mình đưa lúc khởi tạo> |

## Lệnh

| Việc | Lệnh |
|---|---|
| Chạy local | <…> |
| Test | <…> |
| Định dạng / lint / kiểu | <…> |
| Kiểm tra tất cả trước khi báo xong | <…> |
| Review trước commit / kiểm tra lệch tài liệu | <lệnh hoặc câu gọi đã tạo> |

## Đọc gì, theo thứ tự

1. **Bản đồ** → mở đúng tài liệu và code của phần đang làm. Không quét cả repo; tìm rộng thì giao tác tử phụ (nếu có), chỉ lấy kết luận.
2. Tài liệu + code của dự án; quyết định, ghi chú dự án khi việc chạm tới.
3. Tri thức cá nhân — **chỉ** theo bảng "Tham khảo tri thức cá nhân" bên dưới.
4. Kiến thức của model → tài liệu chính thức trên internet (khi đụng thư viện / dịch vụ ngoài; ghi nguồn).

Mâu thuẫn: yêu cầu của mình lúc làm > quy ước nhóm > khối này > tri thức cá nhân > thói quen của AI.

## Cỡ việc

| Cỡ | Dấu hiệu | Làm |
|---|---|---|
| Nhỏ | Sửa chữ, đổi giá trị, lỗi rõ nguyên nhân trong 1 file | Làm luôn |
| Vừa | Một API / form / tính năng nhỏ, sửa nhiều file | Vài dòng kế hoạch trong câu trả lời rồi làm |
| Lớn | Nhiều lớp (DB + API + giao diện), hoặc chạm mục "Luôn hỏi trước" | Kế hoạch: làm gì, ảnh hưởng, rủi ro, kiểm bằng gì → **chờ duyệt**. Việc nhiều phiên → bước + tiến độ vào khối Tiến độ |

Không chắc cỡ nào → chọn cỡ lớn hơn. Yêu cầu mơ hồ ảnh hưởng kết quả → hỏi trước khi làm.

## Luôn hỏi trước

- Đổi cấu trúc DB, đăng nhập, phân quyền, hợp đồng API.
- Thêm thư viện, công cụ, dịch vụ ngoài.
- Xoá / đổi tên / chuyển tính năng, thư mục, dữ liệu không do mình tạo trong việc này.
- Thao tác không quay lui được hoặc ra bên ngoài: push, deploy, gửi mail, xoá dữ liệu.

## Khi viết code

- Giống code xung quanh: đặt tên, cấu trúc, cách import, mật độ comment.
- Comment, tên, log, thông báo lỗi, commit message bằng tiếng Anh. Chữ người dùng thấy đi qua i18n / dictionary của dự án, không viết cứng.
- Chỉ sửa điều được yêu cầu; refactor hoặc lỗi thấy được → báo, tách commit.
- Trước khi sửa / xoá: tìm mọi chỗ đang dùng (hàm, route, key, class, biến môi trường). Xoá phải xoá hết, kể cả thứ chỉ phần bị xoá dùng. File mới phải được tham chiếu.
- Không để lại: import / biến / style / key thừa, code bị comment, lệnh in debug, test bị đánh dấu chạy riêng, TODO không lý do.
- Không nuốt lỗi, không bọc bắt lỗi rộng cho "chạy được"; kiểm quyền ở server, không chỉ giao diện.
- Trường hợp biên: rỗng, quá dài, trùng, đồng thời, quyền theo từng bản ghi.
- Kiểm lại hàm / file còn tồn tại trước khi dựa vào bộ nhớ hay phiên trước.
- <quy ước code chép từ tri thức cá nhân, viết lại cho dự án: khung phản hồi API, mã lỗi, thông báo, dialog, đặt tên…>

## Trước khi báo xong

- Chạy lệnh "kiểm tra tất cả" và test liên quan; chỉ nói một bước đã qua khi nó thật sự chạy trong phiên này, kèm kết quả. Liệt kê bước chưa chạy và lý do.
- Đã mở checklist nào (bảng tham khảo) → đối chiếu từng dòng theo [[Đối chiếu checklist và minh chứng]]; trong câu trả lời chỉ liệt kê ❌, ⏭ và ✅ về bảo mật / quyền / dữ liệu / tiền (kèm vị trí), còn lại gộp số đếm.
- Phát hiện / review: tách **đã xác minh** và **nghi vấn**; mỗi mục có vị trí, kịch bản lỗi cụ thể, hậu quả. Không thấy gì → nói rõ chỉ cho phạm vi đã xem, không khẳng định "không có lỗi".
- Giao diện: liệt kê trang / khu vực mình cần tự xem trên trình duyệt.
- Dọn dữ liệu thử, file tạm, tiến trình / container tạm.
- Tài liệu: theo mục "Tài liệu dự án" bên dưới.

## Git, test và minh chứng

- Branch: <quy ước nhóm, hoặc `<loại>/<mã-ticket>-<mô-tả>`>. Merge: <squash / rebase / merge commit>. Nhánh chính để so: <…>.
- Commit: <Conventional Commits tiếng Anh `type(scope): …`, thân nói vì sao>; một commit một mục đích; không dòng ghi công AI.
- Trước khi đưa danh sách `git add` hoặc commit message: chạy [[Review trước commit]] trên đúng các file đó. AI không tự commit / push.
- Test: <loại test, lệnh, khi nào bắt buộc>. Minh chứng: <nộp gì, cho ai, theo [[Mẫu - Minh chứng]] hay mẫu của nhóm>.
- Làm việc với bộ phận khác: <ai duyệt, ai test, kênh báo — vai trò, không cần tên>.

## Tài liệu dự án — giữ khớp với code

- Làm tính năng / sửa hành vi → sửa luôn tài liệu liên quan (tra bản đồ) trong cùng việc; báo đã sửa file nào. Code mới chưa có trong bản đồ → đề xuất thêm dòng.
- Kiểm tra lệch tài liệu ([[Công cụ và hook của dự án]]) báo tài liệu có thể cũ → nhắc mình **một dòng**, chưa sửa.
- Mình đồng ý → đọc thay đổi của đúng các file được báo, sửa tài liệu, cho mình xem → mình xác nhận → đặt mốc đồng bộ mới.

## Công cụ và hook đã tạo

Thiết lập lúc khởi tạo, riêng của dự án này. Không tự thêm / đổi quy ước hay hook trong lúc làm — chỉ khi mình yêu cầu; thấy nên đổi → đề xuất một dòng.

| Khả năng | Ở đâu | Chạy khi | Tắt một lần / gỡ |
|---|---|---|---|
| <điểm vào tự nạp / kiểm tra sau khi sửa / review / git hook / lệch tài liệu> | <đường dẫn> | <…> | <…> |

## Tham khảo tri thức cá nhân

Đường dẫn: dòng "Kho tri thức trên máy này" ở mục Dự án (không thấy → hỏi mình). Chỉ mở khi việc chạm **đúng dòng**, chỉ đọc **phạm vi** ghi ở cột cuối. Không có trong bảng → không tự mở. Mình yêu cầu tham khảo chủ đề chưa có → tra `Mục lục cho AI.md`, mở đúng file, rồi đề xuất thêm dòng vào bảng này. Ngoài `Playbook/` và file mục lục liệt kê → không mở, không tìm kiếm. Khi có mở, đầu câu trả lời ghi một dòng `Tri thức: <file> (<lý do>)`.

| Khi việc chạm | File (từ `Playbook/`) | Phạm vi đọc |
|---|---|---|
| Đăng nhập, phiên, quyền | `07 Giải pháp thường gặp/Đăng nhập và phiên.md`, `07 Giải pháp thường gặp/Phân quyền theo vai trò.md`, `03 Chuẩn chung/Bảo mật cơ bản.md` | Checklist + Bẫy |
| Dữ liệu người dùng nhập | `04 Validate/Validate - quy tắc chung.md` + file theo loại trường | Checklist |
| Đổi DB, migration, xoá dữ liệu | `03 Chuẩn chung/Dữ liệu và DB.md`, `07 Giải pháp thường gặp/Thùng rác và xoá.md` | Checklist |
| Tiền, số lượng, đồng thời | `04 Validate/Validate - Số và tiền.md`, `07 Giải pháp thường gặp/Hiệu năng, cache và dịch vụ ngoài.md` | Checklist, phần đồng thời |
| Upload / tải file | `07 Giải pháp thường gặp/Upload file và kho file.md`, `04 Validate/Validate - File và ảnh.md` | Checklist |
| Lỗi trả về, giao dịch, gửi ra ngoài sau commit | `03 Chuẩn chung/Giao dịch, lỗi và phản hồi API.md` | Checklist |
| Phát hành, cấu hình production, bí mật | `07 Giải pháp thường gặp/Phát hành và quay lui.md` | Checklist |
| Log có dữ liệu cá nhân / bí mật | `03 Chuẩn chung/Log và theo dõi.md` | Checklist |
| Việc nền, hàng đợi, hẹn giờ | `07 Giải pháp thường gặp/Việc nền và thông báo tức thì.md` | Bẫy |
| <chủ đề mới với mình trong dự án này> | <file "Giải pháp thường gặp" tương ứng> | Bẫy |

Bỏ dòng nào dự án đã có quy ước riêng (đã ghi ở bản đồ / mục "Khi viết code").

## Ghi chú học được

- Chỉ đúng ở dự án này → ghi chú dự án. Quyết định đã chốt → quyết định.
- Dùng lại được ở dự án khác → **một dòng** cuối khối Đề xuất tri thức: `ngày | chủ đề | điều học được (đã khái quát) | file tri thức liên quan | nguồn`. Dòng tham khảo trên không giúp gì, hoặc thiếu chủ đề cần có → chủ đề `danh-gia`.
- Không tra cứu, không sửa tri thức cá nhân trong lúc làm. Không ghi tên công ty / khách hàng / hệ thống / lỗ hổng / code nguyên văn.

## Riêng của dự án

- Không commit: <file cá nhân, file sinh ra…>
- Quy ước riêng: <cấu trúc thư mục, chuỗi nằm ở đâu, kích thước màn hình, khung phản hồi…>
- Cảnh báo công cụ đã biết là sai: <công cụ — lý do>
- Điểm mù / bẫy riêng: <…>
