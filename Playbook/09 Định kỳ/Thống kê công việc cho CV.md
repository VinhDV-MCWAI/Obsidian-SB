---
chu_de: thong-ke-cv
ap_dung: [dinh-ky, ket-thuc-du-an]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Thống kê công việc cho CV và portfolio

**Lựa chọn của tôi:** làm **theo đợt — mỗi tháng và trước khi rời dự án**, bằng cách lọc lịch sử những gì mình đã làm. Không ghi nhật ký sau mỗi việc — tốn chi phí mà lịch sử đã có sẵn. Không đợi kết thúc dự án: rời công ty là mất quyền truy cập git / ticket.

Kết quả lưu trong kho tri thức: `Job/Lịch sử công việc/<dự án đã khái quát>.md` — mỗi đợt **thêm** vào cuối (khoảng thời gian, các gạch đầu dòng), đợt sau chỉ lọc từ ngày đợt trước.

## Nguồn lịch sử (rẻ trước, đắt sau)

| Nguồn | Lấy gì | Cách lọc rẻ |
|---|---|---|
| Lịch sử git | Commit của mình: loại, phạm vi, tóm tắt | `git log --author="<tên>" --no-merges --pretty="%ad %s" --date=short` |
| Pull request, ticket | Tiêu đề, mô tả, thời gian | Danh sách PR / ticket mình phụ trách |
| `.me/` của dự án | Quy tắc, quyết định, ghi chú dự án, bản đồ | Đọc thẳng (ngắn) |
| Lịch sử phiên của công cụ AI (nếu công cụ lưu lại) | Việc lớn, vấn đề khó đã giải | **Không đọc nguyên văn** (rất lớn). Chỉ dùng khi git chưa đủ: lọc bằng lệnh tìm theo từ khoá, đọc các đoạn trúng |

## Các bước

1. AI lấy danh sách commit của mình **từ ngày đợt trước** (`--since`), gom theo chủ đề (tính năng, sửa lỗi, tối ưu, hạ tầng, tài liệu…).
2. Mỗi nhóm: việc gì, vai trò của mình, công nghệ / cách làm, kết quả đo được (nếu có trong commit / PR / tài liệu), khó khăn đã giải.
3. Thêm vào `Job/Lịch sử công việc/<dự án>.md`; khi kết thúc dự án, gom lại theo [[Mẫu - Tổng hợp CV cuối dự án]].
4. Viết 3–5 gạch đầu dòng dạng **làm gì – bằng gì – kết quả**.
5. Mình chỉnh rồi chép vào CV / portfolio trong `Job/` (ví dụ [[Portfolio Architect]]).
6. Đợt cuối (rời dự án) xong mới xoá `.me/` / dự án.

## Lưu ý

- Ưu tiên **số đo được** (thời gian, dung lượng, số người dùng, % lỗi giảm). Không có số → phạm vi (bao nhiêu màn hình, API, bảng).
- Viết ở mức chung: không tên khách hàng, không số liệu bí mật.
- Việc làm chung: ghi rõ phần của mình.
