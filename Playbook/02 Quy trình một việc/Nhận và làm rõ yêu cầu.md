---
chu_de: lam-ro-yeu-cau
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Nhận và làm rõ yêu cầu

Bước 1–3 của [[Quy trình một việc]]: hiểu đúng, đủ, sâu trước khi làm; hỏi lại khi chưa chắc; đưa phương án tối ưu để người giao việc dễ quyết.

Sau: [[Thiết kế trước khi làm]] (việc cần thiết kế) hoặc [[Kế hoạch test cho một việc]].

## Lựa chọn của tôi

- Đọc **nguyên văn** yêu cầu và mọi thứ đính kèm trước; nói lại bằng 1–3 câu: ai, cần gì, để làm gì.
- **Tự trả lời trước, hỏi sau**: câu nào tra được từ code, tài liệu, ticket, lịch sử git → tự tra. Chỉ hỏi điều chỉ người giao việc biết: ý định, ưu tiên, nghiệp vụ, đánh đổi chấp nhận được.
- **Hỏi gom một lần**, ≤ ~5 câu, mỗi câu kèm **đề xuất mặc định**, để người trả lời chỉ cần đồng ý hoặc sửa.
- Điều không ảnh hưởng kết quả → không hỏi, ghi thành **giả định**.
- Mở rộng vấn đề để thấy hết ảnh hưởng, nhưng **làm đúng phạm vi đã chốt**; phần ngoài phạm vi → đề xuất thành việc riêng.
- Kết thúc bằng **tiêu chí chấp nhận** kiểm được — đầu vào của thiết kế và kế hoạch test.

## 1. Nói lại yêu cầu

| Mục | Trả lời |
|---|---|
| Ai | Vai trò nào dùng; ai giao, ai duyệt, ai kiểm thử |
| Cần gì | Hành vi mong muốn, nhìn thấy được |
| Để làm gì | Vấn đề thật đằng sau (hỏi "vì sao" một lần) — đôi khi có cách khác rẻ hơn |
| Khi nào xong | Tiêu chí chấp nhận, hạn |
| Cỡ | Nhỏ / Vừa / Lớn ([[Quy trình một việc]]) |

## 2. Mở rộng — đi qua từng mặt

Mặt nào việc chạm tới thì ghi: đã biết (nguồn) / cần hỏi / giả định.

| Mặt | Câu hỏi |
|---|---|
| Người dùng, quyền | Ai được làm, ai không; quyền theo từng bản ghi; chưa đăng nhập thì sao |
| Dữ liệu | Trường nào, bắt buộc, giới hạn, trùng; xoá thì sao; dữ liệu đang có có phải chuyển không |
| Luồng | Luồng chính, luồng phụ, luồng lỗi; trạng thái và chuyển trạng thái |
| Trường hợp biên | Rỗng, quá dài, quá nhiều, đồng thời, bấm hai lần, mạng lỗi giữa chừng |
| Giao diện | Trang nào; điện thoại; ngôn ngữ; trạng thái tải / rỗng / lỗi / không có quyền |
| Tích hợp | API khác, dịch vụ ngoài, việc nền, thông báo, email |
| Phi chức năng | Hiệu năng, bảo mật, log, truy cập dễ dàng, SEO |
| Ảnh hưởng | Tính năng khác dùng chung phần này; hợp đồng API; tài liệu; test có sẵn |
| Vận hành | Cấu hình, biến môi trường, migration, phát hành, quay lui |
| Ngoài phạm vi | Điều cố ý không làm lần này |

## 3. Hỏi lại

```
1. <câu hỏi ngắn>
   Vì sao cần: <ảnh hưởng tới kết quả thế nào>
   Đề xuất: <phương án mặc định> — khác thì bạn nói
```

| Hỏi khi | Không hỏi khi |
|---|---|
| Hai cách hiểu cho ra kết quả khác nhau | Tra được trong code / tài liệu |
| Chạm "Luôn hỏi trước" (DB, quyền, thư viện, xoá, ra bên ngoài) | Chi tiết trình bày đã có quy ước |
| Thiếu dữ kiện nghiệp vụ | Quyết định dễ đổi lại sau → làm theo mặc định, ghi giả định |
| Làm sai thì tốn kém | Chỉ để chắc ăn |

Câu trả lời làm đổi cỡ việc → báo lại cỡ trước khi làm tiếp.

## 4. Đưa phương án

| Phương án | Làm gì | Lợi | Hại / rủi ro | Công sức | Cần duyệt |
|---|---|---|---|---|---|
| A (đề xuất) | | | | | |
| B | | | | | |

- Một phương án khi chỉ có một cách hợp lý; 2–3 khi có đánh đổi thật. Không bày phương án cho có.
- So theo **mục tiêu của dự án** (đúng, bảo mật, dễ bảo trì, nhanh ra, chi phí), không theo sở thích.
- Cột "Cần duyệt": thêm thư viện, đổi DB, đổi quyền, đổi hợp đồng API, xoá thứ đang có.
- Chọn một, nói vì sao trong một câu.

## 5. Tiêu chí chấp nhận

Mỗi tiêu chí một câu kiểm được:

```
Cho  <ngữ cảnh: vai trò, dữ liệu có sẵn>
Khi  <hành động>
Thì  <kết quả nhìn thấy / kiểm được>
```

Ít nhất một tiêu chí cho luồng lỗi hoặc không có quyền. Việc lớn: ghi vào [[Mẫu - Kế hoạch việc]].

## Checklist

- [ ] Đã đọc nguyên văn yêu cầu và đính kèm; nói lại được trong 1–3 câu.
- [ ] Đã tự tra code / tài liệu trước khi hỏi.
- [ ] Đã đi qua bảng mở rộng; mặt nào chạm có ghi chú.
- [ ] Câu hỏi gom một lần, mỗi câu có đề xuất mặc định.
- [ ] Giả định ghi rõ, tách khỏi điều đã chốt.
- [ ] Có tiêu chí chấp nhận, gồm luồng lỗi.
- [ ] Phạm vi "không làm" ghi rõ.

## Bẫy

- "Thêm nút X" nhưng thật ra cần giải quyết vấn đề Y → hỏi "để làm gì" một lần.
- Hỏi từng câu qua nhiều lượt → chậm, người giao việc mệt.
- Giả định im lặng → làm xong mới lộ hiểu sai.
- Mở rộng thành làm luôn phần ngoài phạm vi → diff to, khó review.
