---
loai: mau
dat_tai: "khối Quy tắc trong .me/ của dự án"
---
# Quy tắc làm việc: <tên dự án>

> Đề mục cho khối **Quy tắc** của dự án, AI tạo lúc [[Khởi tạo dự án]]. Nạp **mọi phiên** → ≤ ~150 dòng. Mỗi `<…>` là chỗ AI **viết lại cho dự án thật** từ nơi gốc được dẫn, không chép nguyên văn Playbook; dự án đã có quy ước thì trỏ tới đó. Bỏ mục không dùng. Quy ước nhóm thắng khối này. Cập nhật: <YYYY-MM-DD>.

## Dự án

| | |
|---|---|
| Mục đích | <một câu> |
| Phần mình phụ trách | <module / vai trò> |
| Loại | <A mới / B cũ cần nâng cấp / C đã chuẩn / D nặng> |
| Công nghệ | <ngôn ngữ, framework, DB, phiên bản> |
| Môi trường | <hệ điều hành, editor, công cụ AI — những gì ảnh hưởng cách làm> |
| Không được đụng | <thư mục, dữ liệu, môi trường> |
| Kho tri thức trên máy này | <đường dẫn …/Playbook mình đưa lúc khởi tạo> — chỉ dùng cho đợt cập nhật và khi mình yêu cầu |

## Lệnh

| Việc | Lệnh |
|---|---|
| Chạy local | <…> |
| Kiểm nhanh / đầy đủ / như CI | <…> |
| Test (một file / tất cả) | <…> |
| Review (chỉ thời điểm git) | <lệnh review đã tạo> |
| Kiểm tra lệch tài liệu | <lệnh hoặc câu gọi> |

## Đọc gì, theo thứ tự

1. Quy ước / tài liệu của nhóm → **bản đồ** → đúng tài liệu và code của phần đang làm. Không quét cả repo; tìm rộng thì giao tác tử phụ.
2. Quy tắc theo chủ đề (bảng "Khi việc chạm" dưới); quyết định, ghi chú dự án khi việc chạm tới.
3. Kiến thức của model → tài liệu chính thức trên internet (ghi nguồn).

Không mở Playbook trong lúc làm việc; bộ này đã đủ. Mâu thuẫn: yêu cầu của mình lúc làm > quy ước nhóm > khối này > thói quen của AI.

## Cỡ việc và cổng duyệt

<Viết lại từ [[Quy trình một việc]] cho dự án: bảng cỡ việc (nhỏ / vừa / lớn và dấu hiệu), ba cổng duyệt, danh sách "Luôn hỏi trước", luồng riêng nếu nhóm có (ví dụ bảng việc, ticket).>

## Khi viết code

- **Ngôn ngữ** (luôn ghi rõ, không để trống): <code, tên, comment, log, thông báo lỗi cho dev, tên test, commit message — tiếng Anh; chữ người dùng thấy qua <cơ chế đa ngôn ngữ của dự án>; tài liệu — <ngôn ngữ của nhóm>>.
- <Chép rút gọn phần còn lại của mục "Tối thiểu mọi phiên" trong [[Quy ước viết code]], cộng quy ước riêng của dự án: khung phản hồi API, mã lỗi, thông báo, dialog, đặt tên.>

## Trước khi báo xong

- Chạy <lệnh kiểm tra> và test liên quan; chỉ nói đã qua khi thật sự chạy trong phiên này, kèm kết quả; bước chưa chạy nói lý do.
- Đối chiếu checklist đã mở theo [[Đối chiếu checklist và minh chứng]]; báo cáo theo mục "Báo cáo khi xong" của [[Quy trình một việc]].
- Phát hiện / điều tra: tách **đã xác minh** và **nghi vấn**, mỗi mục có vị trí + kịch bản + hậu quả; không thấy gì → nói rõ chỉ cho phạm vi đã xem.
- Dọn theo <quy tắc dọn của dự án / [[Môi trường phát triển và dọn dẹp]]>.

## Git, test và minh chứng

- Nhánh: <mô hình nhánh, nhánh làm việc, nhánh dài hạn được bảo vệ>. Merge: <…>.
- Commit: <quy ước, tiếng Anh>; <có / không> dòng ghi công AI; AI <không commit / được commit local qua …>; **không bao giờ** push thẳng / force push nhánh dài hạn ([[Git và quy trình]]).
- Trước commit: chạy lệnh review ([[Review code - quy trình]]); checklist từng mặt ở <thư mục review của dự án>, chỉ mở lúc này.
- Pull request: AI soạn mô tả theo <mẫu nhóm / [[Mẫu - Mô tả pull request]]>; mình mở.
- Test: <loại test, lệnh, khi nào bắt buộc>; test tốn kém / có tác dụng phụ → xin xác nhận ([[Kế hoạch test cho một việc]]). Minh chứng: <nộp gì, cho ai>.
- Làm việc với bộ phận khác: <ai duyệt, ai test, kênh báo — vai trò, không cần tên>.

## Tài liệu dự án — giữ khớp với code

- Đổi hành vi → sửa tài liệu liên quan (tra bản đồ) trong cùng việc; code mới chưa có trong bản đồ → đề xuất thêm dòng.
- Kiểm tra lệch báo tài liệu có thể cũ → nhắc mình **một dòng**, chưa sửa. Mình đồng ý → sửa, cho xem, xác nhận, đặt mốc mới ([[Công cụ và hook của dự án]]).

## Công cụ và hook đã tạo

Riêng của dự án này; không tự thêm / đổi — thấy nên đổi → đề xuất một dòng.

| Khả năng | Ở đâu | Chạy khi | Tắt một lần / gỡ |
|---|---|---|---|
| <điểm vào / review / git hook / quét extension / lệch tài liệu> | <đường dẫn> | <…> | <…> |

## Khi việc chạm — mở quy tắc theo chủ đề

Chỉ mở file khi việc chạm **đúng dòng**; đối chiếu từng dòng của file đó trước khi báo xong. File nằm trong bộ của dự án (`.me/…`) hoặc tài liệu của repo — viết lại từ Playbook lúc khởi tạo, không trỏ về Playbook.

| Khi việc chạm | Mở | Ghi chú |
|---|---|---|
| <dữ liệu người dùng nhập / DB, migration, xoá / upload / tiền / phát hành / log nhạy cảm / tính năng hay gặp mà dự án có…> | <`.me/<chủ đề>.md` hoặc tài liệu repo> | <khác Playbook ở đâu> |
| Thời điểm git | <lệnh review → checklist review từng mặt> | Không nạp lúc khác |

Gặp chủ đề bảng này chưa có → nói một dòng; mình yêu cầu thì tra Playbook (đường dẫn ở mục Dự án) và **thêm file + dòng** vào bộ này.

## Ghi chú học được

- Chỉ đúng ở dự án này → ghi chú dự án; quyết định đã chốt → quyết định.
- Dùng lại được ở dự án khác → **một dòng** cuối khối Đề xuất tri thức: `ngày | chủ đề | điều học được (đã khái quát) | file tri thức liên quan | nguồn: thực tế / tài liệu chính thức / chưa kiểm chứng`. Không tra cứu, không sửa Playbook trong lúc làm; không ghi tên công ty / khách hàng / hệ thống / lỗ hổng / code.

## Riêng của dự án

- Không commit: <file cá nhân, file sinh ra…>
- Quy ước riêng: <cấu trúc thư mục, chuỗi người dùng thấy nằm ở đâu, kích thước màn hình cần kiểm, khung phản hồi…>
- Cảnh báo công cụ đã biết là sai: <công cụ — lý do>
- Điểm mù / bẫy riêng: <…>
