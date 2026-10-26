---
chu_de: tiet-kiem-chi-phi
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Tiết kiệm chi phí AI

**Lựa chọn của tôi:** chất lượng đứng đầu, nhưng mỗi token phải có lý do. Việc đắt (tra cứu, tổng hợp, cập nhật tri thức, thống kê CV) làm **theo đợt**, không làm ở mỗi prompt.

## Nguyên tắc

1. **Nạp theo nhu cầu:** mục lục → 1–3 file. Không đọc cả Playbook, không quét cả repo.
2. **Thứ tự tham khảo:** (1) code và tài liệu của chính dự án → (2) `.me/` → (3) Playbook → (4) kiến thức của model → (5) internet. Chỉ đi bước sau khi bước trước không đủ.
3. **Máy kiểm thứ máy kiểm được:** lint, kiểu, test, quét bí mật rẻ và chính xác hơn AI đọc code ([[Công cụ kiểm tra tự động]]).
4. **Hỏi sớm thay vì làm lại.** Một câu hỏi rõ rẻ hơn làm sai một việc lớn.
5. **Kế hoạch trước cho việc lớn:** duyệt kế hoạch rẻ hơn duyệt code sai hướng.
6. **Ghi chú, không cập nhật ngay:** [[Ghi chú và cập nhật theo đợt]].
7. **Không trừu tượng hoá sớm.**

## Ước lượng

| Thứ | Token (ước) |
|---|---|
| Mô tả kỹ năng Playbook (luôn có) | ~100 |
| Mục lục | ~1.500 |
| Một file chuẩn | ~1.000–2.000 |
| `.me/Hồ sơ dự án.md` | ~500–1.000 |
| Một dòng ghi chú | ~50 |
| Cả Playbook | 50.000+ — **không bao giờ đọc hết** |

## Chọn mức theo việc

| Việc | Mức |
|---|---|
| Sửa nhỏ, chạy lệnh có sẵn | Model nhanh, không đọc Playbook |
| Tính năng vừa có checklist rõ | Model trung bình + checklist (checklist bù phần còn thiếu) |
| Thiết kế, đổi DB, bảo mật, điều tra sự cố | Cân bằng + kế hoạch; mạnh nhất chỉ khi phức tạp / rủi ro rất cao hoặc bậc dưới làm chưa tốt |
| Tìm kiếm rộng nhiều file | Tác tử tìm kiếm riêng, chỉ trả kết luận |
| Đợt cập nhật Playbook, tổng hợp CV | Một phiên riêng, khi rảnh |

## Dấu hiệu đang lãng phí

- AI đọc lại file vừa đọc, hoặc đọc cả thư mục để tìm một hàm.
- Hỏi lại điều đã có trong `.me/` → bổ sung `.me/`.
- Tra internet cho điều Playbook / dự án đã trả lời.
- Ngữ cảnh quá dài → ghi tiến độ vào `.me/Tiến độ.md`, mở phiên mới.
