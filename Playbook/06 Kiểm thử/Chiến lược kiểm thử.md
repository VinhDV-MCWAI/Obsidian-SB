---
chu_de: kiem-thu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Chiến lược kiểm thử

Kiến thức có sẵn trong kho — **dùng trực tiếp, không chép lại**:
- [[Testing]] — checklist trường hợp biên cho từng thành phần giao diện, quyền, đồng thời, file, export.
- [[Unit & integrate test case]] — mẫu test case và báo cáo (môi trường, OK / NG / chờ / còn lại).
- [[Unit Test Auto]] — cô lập, chạy song song, mock, vì sao Singleton khó test.

## Test theo rủi ro, không chạy theo phần trăm

Hỏi: "**cái gì hỏng thì đau nhất, và test rẻ nhất nào bắt được nó?**". Độ phủ chỉ để không âm thầm giảm.

| Mức | Chứng minh | Viết khi |
|---|---|---|
| Đơn vị | Một hàm / lớp đúng quy tắc | Nghiệp vụ, tính toán, xử lý chuỗi, hook |
| Tích hợp / API | Các phần chạy đúng với DB, cache thật | **Mỗi API**: đúng, 422, 401, 403, 404, 409 |
| Hợp đồng | FE và BE hiểu nhau | Mỗi lần đổi API |
| Component | Một component hiển thị / tương tác đúng | Component dùng chung, form phức tạp |
| Đầu cuối (E2E) | Một luồng người dùng thật | **Chỉ** luồng quan trọng (đăng nhập, thanh toán, luồng chính) |
| Hiệu năng | Đủ nhanh dưới tải | API nặng, trước phát hành |
| Bảo mật | Tấn công đã biết bị chặn | Đăng nhập, quyền, upload |
| Truy cập dễ dàng | Bàn phím, đọc màn hình, tương phản | Khi dự án yêu cầu |
| SEO | Thẻ meta, sitemap, tốc độ | Trang công khai |
| Giao diện bằng ảnh | Không vỡ giao diện | Hệ thống thiết kế lớn |

Nhiều đơn vị + tích hợp, ít E2E. Mức nào cần đi sâu (hiệu năng, SEO, a11y) → **hỏi mình** theo dự án.

## Bài học đã trả giá

- **Test không bao giờ chạm dữ liệu dev / chung.** DB test riêng, có test kiểm chính việc cô lập. Ngoại lệ E2E: tự tạo dữ liệu tiền tố `E2E `, tự dọn kể cả khi lỗi.
- **Kiểm thứ tự và danh tính, không chỉ "có kết quả".** "Khớp nhất lên đầu" cần hai dòng cạnh tranh và kiểm đúng thứ tự.
- **Cấu hình quan trọng phải test đúng cách production nạp nó** (cache cấu hình, image production, proxy).
- **Bỏ thay đổi đi thì test phải fail** — nếu không, test không chứng minh gì.

## Mỗi API tối thiểu

- [ ] Đúng: mã HTTP, khung phản hồi, dữ liệu.
- [ ] 422 cho từng nhóm luật validate quan trọng.
- [ ] 401 khi chưa đăng nhập; 403 khi không có quyền; truy cập bản ghi của người khác.
- [ ] 404 với id không tồn tại / sai dạng.
- [ ] Trường hợp biên của nghiệp vụ (trùng, trạng thái không cho phép, đồng thời nếu có).

## Kiểm thủ công giao diện

Theo [[Testing]] cho thành phần liên quan; ghi kết quả theo [[Unit & integrate test case]]; minh chứng theo [[Mẫu - Minh chứng]].
