---
chu_de: review-test
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Test

Mặt số 8 trong [[Review code - quy trình]]. Mở khi diff đổi hành vi hoặc sửa / thêm test. Chiến lược đầy đủ: [[Chiến lược kiểm thử]].

## Lựa chọn của tôi

- Thay đổi hành vi quan trọng phải có test **chứng minh hành vi mới** và **chặn hồi quy**.
- Sửa lỗi → có test tái hiện lỗi đó.
- Chỉ nói test đã qua khi nó thật sự chạy lần này.

## Checklist

- [ ] Có test ở đúng mức (đơn vị / tích hợp / đầu-cuối) cho hành vi mới hoặc đã đổi.
- [ ] **Bỏ thay đổi đi thì test fail** (test thật sự kiểm thay đổi).
- [ ] Có ca biên, ca lỗi, ca không có quyền — không chỉ ca vui.
- [ ] Sửa lỗi có test tái hiện.
- [ ] Test độc lập: không phụ thuộc thứ tự, giờ hệ thống, mạng ngoài, dữ liệu dev.
- [ ] Tên test nói hành vi được kiểm.
- [ ] Không có test bị bỏ qua / chạy riêng còn sót.
- [ ] Đã chạy test liên quan; ghi lệnh và kết quả thật trong báo cáo.

Bẫy: xem Bẫy và "Bài học đã trả giá" của [[Chiến lược kiểm thử]].
