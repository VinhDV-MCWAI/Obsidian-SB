---
loai: muc-luc
cap_nhat: 2026-10-27
---
# Mục lục cho AI

Danh sách **duy nhất** các file AI được mở. Dùng khi khởi tạo (chọn chủ đề, viết lại vào bộ của dự án), khi mình yêu cầu tham khảo một chủ đề, và trong đợt cập nhật. Việc hằng ngày không đọc file này: dự án đã tự đủ. Cột "Phạm vi đọc" = phần cần đọc để viết lại cho dự án. Đường dẫn tính từ `Playbook/`.

**Phạm vi:** chỉ mở file có trong mục lục, 1–3 file mỗi lần, đúng "Phạm vi đọc". Phần còn lại của kho Obsidian (ghi chú cá nhân, công việc, đời sống, file ở gốc kho, `00 Nguồn gốc/`) — không mở, không liệt kê, không tìm kiếm. File cần mà không có ở đây → hỏi mình.

## 01 Khởi tạo dự án — một lần mỗi dự án

| File | Mở khi | Phạm vi đọc |
|---|---|---|
| `01 Khởi tạo dự án/Khởi tạo dự án.md` | Bắt đầu / nhận lại dự án, dự án trên máy mới, đổi công cụ AI hoặc editor | Toàn bộ |
| `01 Khởi tạo dự án/Bộ quy tắc và tài liệu tối thiểu.md` | Khởi tạo: đối chiếu từng chủ đề, chọn theo dự án / tri thức | Toàn bộ |
| `01 Khởi tạo dự án/Công cụ và hook của dự án.md` | Tạo / sửa lệnh, hook, quét bằng extension, kiểm tra lệch tài liệu | Phần đang làm |

## 02 Quy trình một việc — theo thứ tự

| File | Mở khi | Phạm vi đọc |
|---|---|---|
| `02 Quy trình một việc/Quy trình một việc.md` | Khởi tạo (viết lại cỡ việc, cổng duyệt vào quy tắc dự án); việc lớn | Toàn bộ |
| `02 Quy trình một việc/Nhận và làm rõ yêu cầu.md` | Việc vừa / lớn, yêu cầu mơ hồ | Checklist |
| `02 Quy trình một việc/Thiết kế trước khi làm.md` | Việc cần use case, luồng, ERD, giao diện, API | Phần loại thiết kế cần |
| `02 Quy trình một việc/Kế hoạch test cho một việc.md` | Lập kế hoạch test, xin xác nhận phạm vi | Toàn bộ (ngắn) |
| `02 Quy trình một việc/Đối chiếu checklist và minh chứng.md` | Trước khi báo "xong" một việc có mở checklist | Toàn bộ (ngắn) |
| `02 Quy trình một việc/Git và quy trình.md` | Thời điểm git: nhánh, commit, pull request, merge | Phần cần |
| `02 Quy trình một việc/Phát hành và quay lui.md` | Phát hành, tag, quay lui, sửa gấp | Checklist |
| `02 Quy trình một việc/Vận hành và sự cố.md` | Kiểm tra sức khoẻ, cảnh báo, sao lưu, sự cố | Checklist |

## 03 Review code — chỉ ở thời điểm git

| File | Mở khi | Phạm vi đọc |
|---|---|---|
| `03 Review code/Review code - quy trình.md` | Trước commit, trước / khi review pull request | Toàn bộ |
| `03 Review code/Review - <mặt>.md`: `Chọn kiểm tra theo rủi ro`, `Bảo mật`, `Đúng nghiệp vụ và trường hợp biên`, `Hợp đồng và tương thích`, `Dữ liệu và migration`, `Vận hành, log và lỗi`, `Thiết kế và quy ước`, `Xoá, thêm và rác`, `Test`, `Báo cáo phát hiện` | Diff chạm mặt đó | Checklist + Bẫy |

## 04 Chuẩn chung

| Chủ đề | File | Phạm vi đọc |
|---|---|---|
| Chia lớp, phạm vi dùng chung, SOLID | `04 Chuẩn chung/Thiết kế code và chia lớp.md` | Lựa chọn + bảng lớp |
| Đặt tên, thứ tự trong file, hàm, comment, mức quy tắc | `04 Chuẩn chung/Quy ước viết code.md` | Phần cần |
| Hằng số, enum, thông báo, đa ngôn ngữ | `04 Chuẩn chung/Hằng số, enum, thông báo.md` | Checklist |
| Khung phản hồi API, mã HTTP, lỗi, giao dịch | `04 Chuẩn chung/Giao dịch, lỗi và phản hồi API.md` | Checklist + Bẫy |
| Kiểu / enum dùng chung server–client, sinh từ đặc tả | `04 Chuẩn chung/Hợp đồng API giữa các phần.md` | Checklist |
| Log, mã tra cứu | `04 Chuẩn chung/Log và theo dõi.md` | Checklist |
| Bảo mật chung | `04 Chuẩn chung/Bảo mật cơ bản.md` | Checklist |
| Bảng, ràng buộc, migration, truy vấn, sao lưu | `04 Chuẩn chung/Dữ liệu và DB.md` | Checklist |
| Nhóm công cụ kiểm tra, extension, bảng quét theo công nghệ | `04 Chuẩn chung/Công cụ kiểm tra tự động.md` | Phần cần |
| Lệnh kiểm tra, ba cấp, thời điểm chạy, hook, nợ cũ | `04 Chuẩn chung/Lệnh kiểm tra và thời điểm chạy.md` | Phần cần |
| Các mức test, bài học, tối thiểu mỗi API | `04 Chuẩn chung/Chiến lược kiểm thử.md` | Toàn bộ (ngắn) |
| Tài liệu nhóm, "code đổi thì tài liệu nào đổi" | `04 Chuẩn chung/Tài liệu dự án.md` | Bảng |
| Container dev, biến môi trường, dọn dẹp | `04 Chuẩn chung/Môi trường phát triển và dọn dẹp.md` | Checklist |

## 05 Validate, 06 Giao diện, 07 Giải pháp thường gặp — khi việc chạm

| Chủ đề | File | Phạm vi đọc |
|---|---|---|
| Form / request có dữ liệu người dùng | `05 Validate/Validate - quy tắc chung.md` **+** file theo loại: `Email`, `Mật khẩu`, `Chữ`, `Số và tiền`, `Ngày giờ`, `Lựa chọn, ID, danh sách`, `File và ảnh`, `Tham số danh sách`, `Điện thoại, URL, mã` | Checklist |
| Khung trang, màu, token, truy cập dễ dàng | `06 Giao diện/Bố cục, màu và truy cập dễ dàng.md` | Checklist |
| Dialog, xác nhận | `06 Giao diện/Dialog và hộp xác nhận.md` | Checklist |
| Thông báo, hiển thị lỗi | `06 Giao diện/Thông báo và hiển thị lỗi.md` | Checklist |
| Trạng thái tải / rỗng / lỗi, form, bảng | `06 Giao diện/Trạng thái, form và bảng.md` | Checklist |
| Đăng nhập, phiên, khoá khi sai nhiều lần | `07 Giải pháp thường gặp/Đăng nhập và phiên.md` | Checklist + Bẫy |
| Vai trò, quyền | `07 Giải pháp thường gặp/Phân quyền theo vai trò.md` | Checklist + Bẫy |
| Lịch sử ai sửa gì | `07 Giải pháp thường gặp/Nhật ký thay đổi.md` | Checklist |
| Upload, kho file, xem trước | `07 Giải pháp thường gặp/Upload file và kho file.md` | Checklist |
| Xoá mềm, thùng rác, khôi phục | `07 Giải pháp thường gặp/Thùng rác và xoá.md` | Checklist |
| Việc chạy lâu, hàng đợi, thông báo tức thì | `07 Giải pháp thường gặp/Việc nền và thông báo tức thì.md` | Checklist + Bẫy |
| Ô tìm kiếm | `07 Giải pháp thường gặp/Tìm kiếm.md` | Checklist |
| Chậm, cache, đồng thời, dịch vụ ngoài | `07 Giải pháp thường gặp/Hiệu năng, cache và dịch vụ ngoài.md` | Phần cần |

## 08 Mẫu, 09 Định kỳ

| File | Mở khi |
|---|---|
| `08 Mẫu/Mẫu - Quy tắc dự án.md`, `08 Mẫu/Mẫu - Bản đồ dự án.md` | Khởi tạo |
| `08 Mẫu/Mẫu - Kế hoạch việc.md`, `08 Mẫu/Mẫu - Kế hoạch test.md`, `08 Mẫu/Mẫu - Minh chứng.md`, `08 Mẫu/Mẫu - Mô tả pull request.md` | Việc lớn, bước tương ứng |
| `09 Định kỳ/Ghi chú và cập nhật theo đợt.md` | Chỉ khi mình yêu cầu đợt cập nhật |
| `09 Định kỳ/Thống kê công việc cho CV.md`, `08 Mẫu/Mẫu - Tổng hợp CV cuối dự án.md` | Chỉ khi mình yêu cầu |

## Kiến thức nền trong kho (ngoài Playbook — chỉ khi cần đào sâu đúng chủ đề)

| Chủ đề | Ghi chú |
|---|---|
| Checklist kiểm thử từng thành phần | `Testing/Aggregates/Testing.md` |
| Mẫu thiết kế chi tiết màn hình / API | `Technical/Aggregates/Detail Design.md` |
| Mẫu test case, báo cáo | `Technical/Concepts/Unit & integrate test case.md` |
| Đăng nhập, cache, kho file, sao lưu, tìm kiếm | `Technical/Aggregates/` |
| Đồng thời, tải lớn, API bên thứ ba | `Technical/Issues/` |
| Công cụ bổ trợ cho AI: hiểu kho mã lớn, kho ghi chú, quy trình đặc tả | `Technical/Aggregates/Công cụ bổ trợ cho AI lập trình.md` |
