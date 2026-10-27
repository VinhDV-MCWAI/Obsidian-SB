---
chu_de: review-bao-cao
ap_dung: [moi-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Review — Báo cáo phát hiện

Bước cuối của [[Review code - quy trình]]. Cùng một khung cho mọi người review và mọi công cụ AI, để kết quả so được với nhau.

## Lựa chọn của tôi

- Mỗi phát hiện quan trọng có **bằng chứng**; không có bằng chứng → "nghi vấn", ghi điều chưa kiểm được.
- Không bịa API, luồng chạy, quy tắc dự án hay kết quả test.
- Lỗi xếp theo mức; không trộn lỗi thật với sở thích.
- Không thấy lỗi → "không thấy lỗi cần sửa trong phạm vi đã review" + giới hạn; không khẳng định code không có bug.

## Trường của một phát hiện

| Trường | Yêu cầu |
|---|---|
| Mức | Nghiêm trọng / Cao / Vừa / Thấp |
| Vị trí | File và dòng |
| Vấn đề | Cụ thể, một câu |
| Bằng chứng | Đoạn code / điều kiện chứng minh |
| Kịch bản | Đầu vào / điều kiện / thao tác → kết quả sai |
| Hậu quả | Điều gì xảy ra nếu lỗi xảy ra |
| Đề xuất | Hướng sửa, không viết lại cả module |
| Độ chắc | Đã xác minh / nghi vấn, kèm giả định |

Ví dụ kém: "Hàm đăng nhập có thể có vấn đề bảo mật." Ví dụ đạt: "Nhánh X cấp phiên mà không kiểm tài khoản bị khoá; tài khoản khoá + mật khẩu đúng → đăng nhập được."

## Mẫu báo cáo

```
Phạm vi: <staged | nhánh so với <đích> | file> — N file

Phase 1 (máy)            | Trước | Sau sửa
<từng kiểm tra>          | FAIL  | PASS
Bảng lỗi editor (N file) | n     | 0

Đã sửa: vị trí — vấn đề — đã làm gì
Phải sửa (cần quyết): [mức · độ chắc] vị trí — vấn đề — kịch bản — đề xuất
Nghi vấn: [mức · nghi vấn] vị trí — vấn đề — điều chưa kiểm được
Có sẵn từ trước: …
Đã chạy: <lệnh> → <kết quả thật> · Chưa chạy: <bước> — lý do
Cần tự xem trên trình duyệt: …
Mặt đã review: … · Mặt bỏ qua: … — lý do
```

Chỉ khi "Phải sửa" trống và Phase 1 sạch: danh sách `git add` chính xác + commit message theo quy ước dự án.

## Checklist

- [ ] Phát hiện xếp theo mức, nặng trước.
- [ ] Tách "đã xác minh" và "nghi vấn"; tách lỗi mới và "có sẵn từ trước".
- [ ] Một bước chỉ ghi PASS khi thật sự chạy lần này.
- [ ] Không báo định dạng mà máy đã lo, không báo sở thích.
