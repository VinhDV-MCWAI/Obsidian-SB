---
chu_de: lenh-kiem-tra
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Lệnh kiểm tra và thời điểm chạy

Bước 7 của [[Quy trình một việc]]. Mọi kiểm tra có **một cách chạy xác định từ repo**, và mỗi thời điểm chạy đúng mức. Người, AI và CI dùng chung các lệnh này. Cần nhóm công cụ nào: [[Công cụ kiểm tra tự động]].

## Lựa chọn của tôi

- Mọi kiểm tra quan trọng có **lệnh chạy được từ repo**; không phải đoán gọi extension hay lệnh nào.
- **Editor cho phản hồi nhanh, dòng lệnh để tái lập, CI để xác nhận trước merge.** Bảng lỗi của editor không phải nguồn xác nhận duy nhất.
- Ba cấp lệnh: **nhanh** (phần đang làm), **đầy đủ** (trước khi báo xong / mở pull request), **CI** (trước merge).
- Hook chỉ **kích hoạt lệnh đã có**, không phải bộ kiểm tra mới.
- Mỗi việc kiểm **một lần**: phần đã qua (cùng nội dung, cùng cấu hình) thì bước sau bỏ qua; chỉ chạy test / kiểu cho phần bị ảnh hưởng.
- Chỉ nói một bước đã qua khi nó **thật sự chạy trong phiên này**, kèm kết quả; bước chưa chạy nói rõ lý do.

## Mỗi công cụ phải có

- [ ] Cấu hình nằm trong repo; phiên bản quản lý qua trình quản lý gói + lockfile (không dựa bản cài global).
- [ ] Một lệnh chuẩn, tên dễ nhớ; phạm vi kiểm và loại trừ rõ.
- [ ] Mã thoát có nghĩa: 0 khi đạt, khác 0 khi vi phạm.
- [ ] Hướng dẫn sửa và chạy lại trong tài liệu dev.
- [ ] Một lệnh gom "kiểm tra tất cả"; dự án nhiều phần → lệnh theo phần + lệnh gom ở gốc.

## Thời điểm

| Khi | Nên chạy | Không nên mặc định |
|---|---|---|
| Lưu file | Định dạng, chẩn đoán của editor | Cả bộ test |
| Trước commit (hook) | Định dạng / lint **file đã stage**, kiểm nhanh (< ~10 giây) | Toàn bộ test, AI review |
| Trước push (hook) | Test và kiểm kiểu phần bị ảnh hưởng | Coi là hàng rào tuyệt đối |
| Trước khi báo xong việc | "Kiểm tra tất cả" hoặc phần liên quan | Nói đã qua khi chưa chạy |
| Trước commit / pull request (người, AI) | [[Review code - quy trình]] | Gộp review vào lệnh kiểm tra |
| CI trên pull request | Kiểm tra đầy đủ, test, quét bảo mật, cổng chất lượng | Bỏ bước vì hook local đã chạy |

## Editor, bảng lỗi và terminal

- Bảng lỗi = chẩn đoán từ language server và extension; thường **chỉ file đang mở**, và cần thời gian quét ([[Công cụ và hook của dự án]], mục quét extension).
- Khung log của extension = nhật ký của extension, không phải danh sách lỗi.
- Terminal = kết quả lệnh + mã thoát; kiểm được cả repo.
- Muốn lỗi của lệnh hiện vào bảng lỗi → cơ chế task của editor với bộ đọc định dạng lỗi.

## Hook

- [ ] Một cơ chế quản lý hook cho cả dự án (không chồng nhiều cái); nhiều ngôn ngữ → một bộ điều phối hoặc script shell.
- [ ] Công cụ tự sửa file trong hook → bản đã sửa phải được stage lại.
- [ ] Đo thời gian hook; chậm → dời bước chậm sang trước push / CI.
- [ ] AI review không chạy trong hook (chậm, tốn quota, có cảnh báo sai).
- [ ] Hook tắt được (`--no-verify`) → điều kiện merge thực thi bằng CI + bảo vệ nhánh ([[Git và quy trình]]).
- [ ] Hook trước push chạy trên thư mục làm việc, không phải đúng commit được push → phần làm dở cất đi trước.

## Nợ cũ và ngoại lệ

- [ ] Dự án có sẵn nhiều cảnh báo → ghi baseline; mục tiêu **không thêm lỗi mới**, giảm baseline dần; baseline không lớn thêm.
- [ ] Phân biệt **lỗi mới** (phải sạch) với **nợ cũ** (ghi lại, không sửa lẫn vào việc).
- [ ] Loại trừ chỉ cho thư mục sinh ra / thư viện ngoài / build; không loại trừ cả mã nguồn hay cả bộ luật bảo mật vì nhiều cảnh báo.
- [ ] Mỗi ngoại lệ có lý do, phạm vi, và (nếu là nợ) thời điểm xem lại.

## Bẫy

- Lệnh "kiểm tra tất cả" không tự giới hạn vào file đổi; muốn vậy phải có script riêng.
- Hết gạch đỏ trong editor ≠ code đúng nghiệp vụ.
