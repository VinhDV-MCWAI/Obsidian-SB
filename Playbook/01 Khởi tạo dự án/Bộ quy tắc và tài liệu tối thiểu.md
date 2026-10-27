---
chu_de: bo-quy-tac-toi-thieu
ap_dung: [du-an-moi, du-an-cu]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Bộ quy tắc và tài liệu tối thiểu — đối chiếu tri thức với dự án

Bước 5 và 7 của [[Khởi tạo dự án]]: với **mọi** dự án, đi qua từng chủ đề dưới đây, xem dự án đã có gì, rồi quyết định lấy theo dự án hay mượn Playbook. Câu hỏi kiểm tra cuối: *một AI mới mở repo lần đầu có biết đọc gì, chạy lệnh gì, theo quy tắc nào, dựa vào đâu để kết luận việc đã xong không?*

## Lựa chọn của tôi

- **Dự án là nguồn chính.** Playbook chỉ lấp chỗ dự án thiếu; đặc thù của dự án không bị viết đè bằng sở thích cá nhân.
- **Sau khởi tạo dự án tự đủ.** Phần lấy từ Playbook được **viết lại vào bộ của dự án** (theo công nghệ, lệnh, đường dẫn thật), không để lại liên kết bắt AI mở Playbook trong lúc làm.
- **Mỗi chủ đề một file**, một quy tắc một nơi gốc: tài liệu giải thích, cấu hình công cụ thực thi, file hướng dẫn AI chỉ **trỏ đường**.
- Quy tắc nạp **đúng lúc cần**, không nạp hết mọi phiên.
- AI được đề xuất phương án tốt hơn cả hai bên, nhưng **nêu rõ** khác biệt và lý do; mình duyệt mới áp dụng.

## Chọn giữa dự án và Playbook

| So sánh | Lấy |
|---|---|
| Hai bên tương đồng | Dự án (đã viết sẵn, khỏi chép) |
| Cùng giá trị, khác cách làm | **Dự án** |
| Dự án chưa có | **Playbook**, viết lại cho công nghệ và lệnh thật của dự án |
| Có thể tốt hơn cả hai | Đề xuất kèm lý do, đánh đổi; dự án công ty chỉ đề xuất cho nhóm |
| Tài liệu dự án lệch code | Code thắng; đề xuất sửa tài liệu |

## Bốn quyết định cho mỗi chủ đề

| Quyết định | Khi | Ghi vào |
|---|---|---|
| **Theo dự án** | Dự án đã có quy ước / code mẫu | Đường dẫn trong bản đồ (không chép) |
| **Viết vào quy tắc** | Cần ở mọi phiên, ngắn: cỡ việc, cổng duyệt, tối thiểu khi viết code, git | Khối Quy tắc (nạp mọi phiên) |
| **Viết thành quy tắc theo chủ đề** | Cần khi việc chạm: chốt chặn (quyền, dữ liệu nhập, DB / xoá, tiền, file, phát hành, log nhạy cảm, gửi ra ngoài), giải pháp thường gặp mà dự án có, review từng mặt | Một file mỗi chủ đề trong bộ của dự án; khối Quy tắc có bảng "khi chạm → mở file nào". Chỉ giữ dòng hợp với dự án, viết lại theo công nghệ thật |
| **Bỏ** | Không liên quan tới dự án (tính năng dự án không có), hoặc máy đã kiểm | Không ghi; nêu lý do trong đề xuất |

Chưa chắc giữ dòng nào → giữ, đánh dấu "chưa kiểm chứng ở dự án này"; bỏ sau khi thực tế cho thấy không cần.

## Bộ chủ đề tối thiểu

| Chủ đề | Nội dung | Playbook | Nạp khi |
|---|---|---|---|
| Hướng dẫn chung cho AI | Dự án tóm tắt, lệnh chuẩn, đọc gì theo thứ tự, liên kết các file dưới | [[Mẫu - Quy tắc dự án]] | Mọi phiên (ngắn) |
| Quy trình làm việc | Cỡ việc, cổng duyệt, "Luôn hỏi trước", luồng sửa lỗi / sửa gấp / refactor | [[Quy trình một việc]] | Cỡ việc + cổng: mọi phiên; chi tiết khi việc vừa / lớn |
| Làm rõ và thiết kế | Cách hỏi, tiêu chí chấp nhận, loại thiết kế cần | [[Nhận và làm rõ yêu cầu]], [[Thiết kế trước khi làm]] | Việc vừa / lớn |
| Kiến trúc | Module, ranh giới, chiều phụ thuộc, luồng một request / một màn hình | [[Thiết kế code và chia lớp]] | Khi chạm module |
| Quy ước code | Đặt tên, thứ tự file, hàm, lỗi, comment | [[Quy ước viết code]] | Khi viết code (theo đường dẫn file nếu công cụ AI hỗ trợ) |
| Lệnh kiểm tra | Nhanh / đầy đủ / CI, hook | [[Lệnh kiểm tra và thời điểm chạy]] | Trước khi báo xong |
| Công cụ quét | Bảng quét và review theo công nghệ | [[Công cụ kiểm tra tự động]] | Lúc review |
| Test | Mức test, dữ liệu test, test nào phải xin xác nhận | [[Chiến lược kiểm thử]], [[Kế hoạch test cho một việc]] | Khi lập kế hoạch / viết test |
| Review code | Quy trình + một file checklist cho mỗi mặt, viết lại theo công nghệ của dự án | [[Review code - quy trình]] và `03 Review code/` | **Chỉ thời điểm git**, qua lệnh review |
| Checklist chốt chặn | Validate, giao diện, giải pháp thường gặp — chỉ phần dự án có | `05 Validate/` → `07 Giải pháp thường gặp/` | Khi việc chạm |
| Git | Nhánh, bảo vệ nhánh, AI được làm gì, commit, pull request | [[Git và quy trình]] | Thời điểm git |
| Bảo mật | Checklist theo miền của dự án | [[Bảo mật cơ bản]] | Khi chạm chốt chặn |
| Tài liệu | Cấu trúc, bảng "code đổi thì tài liệu nào đổi" | [[Tài liệu dự án]] | Khi đổi hành vi |
| Môi trường, dọn dẹp | Lệnh chung, biến môi trường, dữ liệu thử, dọn | [[Môi trường phát triển và dọn dẹp]] | Trước khi báo xong |
| CI | Điều kiện bắt buộc trước merge | [[Lệnh kiểm tra và thời điểm chạy]] | — (máy chạy) |

## Đối chiếu từng chủ đề

| Dự án có | Làm |
|---|---|
| Đủ, còn đúng | Ghi đường dẫn vào bản đồ; không tạo gì |
| Có nhưng gom nhiều chủ đề vào một file | Dự án nhóm: đề xuất tách. Dự án mình quyết: tách sau khi mình duyệt |
| Có nhưng thiếu dòng | Bổ sung vào **file riêng** của chủ đề đó; ghi khác biệt |
| Chưa có | Tạo từ Playbook, viết lại cho công nghệ và lệnh thật |

| Loại dự án | Quy tắc / tài liệu mới đặt ở |
|---|---|
| Mình quyết repo (cá nhân, loại A) | Trong repo, thư mục tài liệu kỹ thuật, mỗi chủ đề một file |
| Nhóm / công ty, hoặc chỉ mình dùng | Trong `.me/`, mỗi chủ đề một file; chỉ **đề xuất** cho nhóm |

Khi quy tắc va nhau: (1) đúng, bảo mật, nghiệp vụ, tương thích → (2) kiến trúc và quy ước của dự án → (3) quy tắc máy thực thi → (4) gợi ý tối ưu của reviewer / AI. Quy tắc bảo mật quan trọng phải được thực thi bằng validate, test hoặc công cụ, không chỉ nằm trong tài liệu.

## Checklist

- [ ] Mỗi chủ đề tối thiểu có một quyết định (kèm lý do khi bỏ).
- [ ] Không file nào gom nhiều chủ đề; quy tắc chỉ dùng ở thời điểm git không nằm trong phần nạp mọi phiên.
- [ ] Hướng dẫn cho AI chỉ liên kết tới tài liệu / cấu hình **của dự án**, không chép lại.
- [ ] Không quy tắc, lệnh, hook nào của dự án đọc file trong Playbook (đường dẫn kho chỉ được ghi để dùng cho đợt cập nhật).
- [ ] Mọi chỗ khác Playbook mà không theo dự án đều được nêu trong đề xuất.
- [ ] Trả lời được câu hỏi kiểm tra cuối ở đầu file.

## Bẫy

- Chép nguyên Playbook vào dự án → quy tắc trái cách framework / nhóm đang làm, AI áp sai.
- Gom review, kiểm tra, quy ước vào một file nạp mọi phiên → tốn ngữ cảnh, AI áp sai lúc.
- File hướng dẫn AI ghi một đằng, cấu hình công cụ một nẻo.
