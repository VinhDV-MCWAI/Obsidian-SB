---
chu_de: tiep-can-du-an
ap_dung: [du-an-moi, moi-truong-moi]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Checklist tiếp cận dự án mới

**Lựa chọn của tôi:** mỗi dự án (pull code về, hoặc tự khởi tạo) bắt đầu bằng **một phiên riêng** để AI đọc dự án, đối chiếu với Playbook và tạo tài liệu riêng trong `.me/` — **vừa đủ** cho dự án đó, kèm đường dẫn tới các thành phần cần đọc. Không commit `.me/`. Các phiên sau chỉ đọc `.me/` thay vì khám phá lại từ đầu.

Thứ tự: **nghiệp vụ → tài liệu → môi trường → code → công cụ → quy ước → tạo `.me/`**.

Bắt đầu bằng [[Thiết lập theo loại dự án]]: loại dự án quyết định làm bao nhiêu bước dưới đây. Dự án đã chuẩn (loại C) thường chỉ cần bước 5–6; dự án nặng (loại D) làm theo từng module.

## 1. Nghiệp vụ và mục tiêu

- [ ] Dự án giải quyết vấn đề gì, cho ai? Người dùng chính?
- [ ] Mục tiêu đo được (thời hạn, số người dùng, hiệu năng)?
- [ ] Phạm vi của mình; ai giao việc, ai duyệt, ai kiểm thử (vai trò, không cần tên)?
- [ ] Thuật ngữ nghiệp vụ.
- [ ] Ràng buộc: pháp lý, dữ liệu cá nhân, ngân sách.

## 2. Gom tài liệu

- [ ] Yêu cầu, thiết kế, mock-up, đặc tả API, sơ đồ kiến trúc, sơ đồ DB.
- [ ] Quyết định kiến trúc đã có.
- [ ] Quy ước nhóm: coding convention, git flow, review, định nghĩa "xong".
- [ ] Nơi quản lý ticket, cách đặt tên.
- [ ] Mỗi tài liệu một dòng trong `.me/Hồ sơ dự án.md`: là gì, ở đâu, tin được tới đâu (tài liệu cũ → "code thắng").

## 3. Môi trường

- [ ] Các môi trường và mình được đụng cái nào.
- [ ] Cách chạy local: lệnh, container, dữ liệu mẫu, tài khoản thử.
- [ ] Bí mật lấy ở đâu (**không** lưu vào `.me/` hay Playbook).
- [ ] Phiên bản ngôn ngữ, runtime, DB.
- [ ] Chỗ vướng khi cài → `.me/Ghi chú dự án.md`; điều học được chung → [[Ghi chú]].

## 4. Đọc code (tổng quan, không đọc hết)

- [ ] Cấu trúc thư mục → `.me/Bản đồ.md`: thư mục → vai trò → đường dẫn.
- [ ] Đi theo **một request** từ đầu đến cuối: route → kiểm tra quyền → validate → xử lý → DB → phản hồi.
- [ ] Đi theo **một màn hình**: trang → thành phần → gọi API → hiển thị lỗi.
- [ ] Phần dùng chung đã có: giao dịch, ngoại lệ, khung phản hồi, validate, log, thông báo, dialog. Đối chiếu với chuẩn → ghi "dự án chọn gì" (chỉ ghi, chưa sửa).
- [ ] Test đang có: loại nào, chạy bằng gì, mất bao lâu.

## 5. Công cụ

- [ ] Formatter, linter, kiểm tra kiểu, test, hook, CI: có gì, chạy bằng lệnh gì.
- [ ] Extension editor nên cài ([[Công cụ kiểm tra tự động]]).
- [ ] Thiếu công cụ nền tảng → **đề xuất** cho nhóm, không tự thêm vào repo. Công cụ cá nhân (extension, cấu hình editor) → tự cài.

## 6. Lọc Playbook cho dự án (bước tạo `.me/`)

- [ ] Với mỗi chủ đề trong [[Mục lục cho AI]]: dự án đã có lựa chọn riêng chưa? Có → ghi **lựa chọn của dự án** (dự án thắng). Chưa → dùng lựa chọn của Playbook.
- [ ] Mục Playbook **tắt** cho dự án (có lý do) và mục **thêm** riêng.
- [ ] Chỉ ghi phần **vừa đủ**, ưu tiên đường dẫn tới code / tài liệu thật hơn là chép nội dung.

## 7. Tuần đầu

- [ ] Làm một việc nhỏ đi hết quy trình (branch → commit → PR → review → merge).
