---
chu_de: review-du-lieu
ap_dung: [back-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Dữ liệu và migration

Mặt số 4 trong [[Review code - quy trình]]. Mở khi diff chạm: migration, cấu trúc bảng, truy vấn mới, xoá dữ liệu, dữ liệu mẫu. Chuẩn đầy đủ: [[Dữ liệu và DB]], [[Thùng rác và xoá]].

## Lựa chọn của tôi

- Migration phải **quay lui được** và chạy được trên dữ liệu thật đang có (không chỉ DB trống).
- Truy vấn mới: nghĩ tới số dòng khi dữ liệu lớn, không chỉ dữ liệu thử.
- Ghi nhiều bảng trong một nghiệp vụ → một giao dịch.

## Checklist

- [ ] Migration có bước quay lui; quay lui không làm mất dữ liệu ngoài ý muốn.
- [ ] Thêm cột bắt buộc vào bảng đã có dữ liệu → có giá trị mặc định hoặc bước điền dữ liệu.
- [ ] Đổi tên / xoá cột: mọi nơi đọc / ghi đã sửa; client cũ không còn dùng.
- [ ] Cột dùng để lọc / sắp xếp / nối bảng có index.
- [ ] Không truy vấn lặp trong vòng lặp (N+1); nạp sẵn quan hệ khi cần.
- [ ] Danh sách có phân trang / giới hạn.
- [ ] Ràng buộc (khoá ngoại, duy nhất, không rỗng) ở DB, không chỉ ở code.
- [ ] Ghi nhiều bước trong giao dịch; gửi ra ngoài (mail, hàng đợi) sau khi giao dịch xong.
- [ ] Xoá theo quy ước của dự án (xoá mềm / thùng rác) và dọn cả file / dữ liệu đi kèm.

## Bẫy

- Migration chạy được ở máy (DB trống) nhưng lỗi trên dữ liệu thật (trùng, rỗng).
- Xoá bản ghi bằng truy vấn trực tiếp → file / object đi kèm còn lại.
- Gửi thông báo trong giao dịch → giao dịch quay lui nhưng thông báo đã đi.
