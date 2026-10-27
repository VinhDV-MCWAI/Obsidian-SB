---
chu_de: review-van-hanh
ap_dung: [back-end, front-end]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Vận hành, log và lỗi

Mặt số 5 trong [[Review code - quy trình]]. Mở khi diff có: xử lý lỗi, log, cấu hình, việc nền, gọi dịch vụ ngoài, thông báo lỗi cho người dùng. Chuẩn: [[Giao dịch, lỗi và phản hồi API]], [[Log và theo dõi]], [[Hiệu năng, cache và dịch vụ ngoài]].

## Lựa chọn của tôi

- Lỗi hoặc được **xử lý thật**, hoặc **ném lên** cho lớp xử lý chung; không nuốt.
- Log để **đọc lúc sự cố**: có ngữ cảnh (mã bản ghi, mã request), không có bí mật / dữ liệu cá nhân.
- Cấu hình đọc qua lớp cấu hình của dự án, không viết thẳng giá trị.

## Checklist

- [ ] Không có `catch` rỗng hoặc bắt lỗi rộng chỉ để "chạy được".
- [ ] Lỗi nghiệp vụ dùng cơ chế chung của dự án (loại ngoại lệ, mã lỗi, khung phản hồi).
- [ ] Thông báo lỗi cho người dùng rõ, qua đa ngôn ngữ, không lộ chi tiết kỹ thuật.
- [ ] Log có mức đúng (lỗi / cảnh báo / thông tin), có ngữ cảnh, không ghi dữ liệu nhạy cảm.
- [ ] Không còn lệnh in debug.
- [ ] Gọi dịch vụ ngoài có thời hạn chờ, thử lại có giới hạn, xử lý khi dịch vụ chết.
- [ ] Việc nền chạy lại được an toàn (không làm hai lần một việc).
- [ ] Không có URL, cổng, khoá, đường dẫn viết thẳng trong code.
- [ ] Hiệu năng: không tải cả bảng, không vòng lặp gọi mạng, có cache nếu đã có quy ước.

## Bẫy

- Thử lại vô hạn khi dịch vụ ngoài chết → nghẽn hàng đợi.
- Lỗi bị bắt ở tầng dưới rồi trả "thành công" lên trên.
