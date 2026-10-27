---
chu_de: kiem-thu
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Chiến lược kiểm thử

Chọn mức test và quy tắc dữ liệu test cho cả dự án. Lập kế hoạch test cho một việc cụ thể và xin xác nhận phạm vi: [[Kế hoạch test cho một việc]]. Kiến thức có sẵn trong kho, dùng trực tiếp:
- [[Testing]] — trường hợp biên cho từng thành phần giao diện, quyền, đồng thời, file, export.
- [[Unit & integrate test case]] — mẫu test case và báo cáo.
- [[Unit Test Auto]] — cô lập, chạy song song, mock.

## Lựa chọn của tôi

- Test **theo rủi ro**, không theo phần trăm: "cái gì hỏng thì đau nhất, và test rẻ nhất nào bắt được nó?". Độ phủ chỉ để không âm thầm giảm.
- Nhiều đơn vị + tích hợp, ít đầu cuối. Mức nào cần đi sâu (hiệu năng, SEO, truy cập dễ dàng) → hỏi mình theo dự án.
- Sửa lỗi → có test tái hiện lỗi đó.

## Các mức

| Mức | Chứng minh | Viết khi |
|---|---|---|
| Đơn vị | Một hàm / lớp đúng quy tắc | Nghiệp vụ, tính toán, xử lý chuỗi |
| Tích hợp / API | Các phần chạy đúng với DB, cache thật | **Mỗi API** (mục dưới) |
| Hợp đồng | Client và server hiểu nhau | Mỗi lần đổi API |
| Thành phần giao diện | Một thành phần hiển thị / tương tác đúng | Thành phần dùng chung, form phức tạp |
| Đầu cuối | Một luồng người dùng thật | **Chỉ** luồng quan trọng (đăng nhập, thanh toán, luồng chính) |
| Hiệu năng | Đủ nhanh dưới tải | API nặng, trước phát hành |
| Bảo mật | Tấn công đã biết bị chặn | Đăng nhập, quyền, upload |
| Truy cập dễ dàng, SEO, giao diện bằng ảnh | Bàn phím, đọc màn hình; thẻ meta; không vỡ giao diện | Khi dự án yêu cầu |

## Mỗi API tối thiểu

- [ ] Đúng: mã HTTP, khung phản hồi, dữ liệu.
- [ ] 422 cho từng nhóm luật validate quan trọng.
- [ ] 401 khi chưa đăng nhập; 403 khi không có quyền; truy cập bản ghi của người khác.
- [ ] 404 với id không tồn tại / sai dạng.
- [ ] Trường hợp biên của nghiệp vụ (trùng, trạng thái không cho phép, đồng thời nếu có).

## Dữ liệu và cô lập

- [ ] Test **không bao giờ** chạm dữ liệu dev / dùng chung: DB, cache, kho file test riêng; có test kiểm chính việc cô lập.
- [ ] Đầu cuối buộc chạy trên môi trường đang chạy → tự tạo dữ liệu tiền tố `E2E `, tự dọn kể cả khi lỗi.
- [ ] Không dùng dữ liệu thật của người dùng / khách hàng.
- [ ] Dịch vụ ngoài giả lập trong test đơn vị / tích hợp; gọi thật chỉ khi đã xác nhận.
- [ ] Thời gian, số ngẫu nhiên, múi giờ cố định; test không phụ thuộc thứ tự chạy.

## Bài học đã trả giá

- **Kiểm thứ tự và danh tính, không chỉ "có kết quả".** "Khớp nhất lên đầu" cần hai dòng cạnh tranh và kiểm đúng thứ tự.
- **Cấu hình quan trọng phải test đúng cách production nạp nó** (cache cấu hình, image production, proxy).
- **Bỏ thay đổi đi thì test phải fail** — nếu không, test không chứng minh gì.
- Ngày tháng: test với ngày > 12 (đọc nhầm ngày / tháng chỉ lộ ra khi đó).

## Kiểm thủ công giao diện

Theo [[Testing]] cho thành phần liên quan; ghi kết quả theo [[Unit & integrate test case]]; minh chứng theo [[Mẫu - Minh chứng]].

## Bẫy

- Test gọi đúng hàm nhưng không kiểm kết quả → luôn xanh.
- Mock quá nhiều → test xanh mà tích hợp thật hỏng.
- Sửa test cho khớp code sai thay vì sửa code.
