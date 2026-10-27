---
chu_de: cong-cu-hook-du-an
ap_dung: [du-an-moi, du-an-cu, doi-cong-cu]
muc_do: nen-tang
cap_nhat: 2026-10-27
---
# Công cụ và hook của dự án

Danh sách **khả năng** mỗi dự án nên có sau [[Khởi tạo dự án]]. File này chỉ nói *cần đạt gì*; AI ở dự án tự hiện thực (script, hook, lệnh, kỹ năng, cấu hình) theo công cụ AI, editor, hệ điều hành và ngôn ngữ kiểm được lúc khởi tạo. Mọi lệnh, kỹ năng, hook **chỉ đọc file của dự án** (quy tắc, checklist, tài liệu), không đọc Playbook. Mọi thứ nằm trong thư mục dự án, loại trừ cục bộ khỏi git, không cài global. Thiết lập **một lần**; sau đó chỉ đổi khi mình yêu cầu. Yêu cầu chung với mọi hook và lệnh kiểm tra: [[Lệnh kiểm tra và thời điểm chạy]].

## Bảng khả năng

| # | Khả năng | Để làm gì | Công cụ AI không hỗ trợ thì |
|---|---|---|---|
| 1 | **Điểm vào tự nạp** | Mỗi phiên AI tự đọc quy tắc của dự án | Mình dán "đọc `.me/` …" đầu phiên |
| 2 | **Kiểm tra sau mỗi lần AI sửa file** | Định dạng (chỉ khi file vốn sạch), lint file đó, quét dòng thêm; lỗi trả về AI sửa ngay | AI chạy lệnh kiểm tra trước khi báo xong |
| 3 | **Lệnh review** | Quy trình review ([[Review code - quy trình]]) viết lại cho dự án, chạy **chỉ ở thời điểm git**; nạp checklist review từng mặt của dự án | Mình dán "review theo quy tắc review của dự án" |
| 4 | **Git hook trước commit / push** | Trước commit: lint / định dạng **đúng file đã stage**, kiểm dấu đã quét extension; lỗi in file + dòng + cách sửa và chặn commit. Trước push: test / kiểu phần bị ảnh hưởng; chặn push thẳng nhánh dài hạn nếu nơi lưu repo không bảo vệ | — (hook của git) |
| 5 | **Kiểm tra lệch tài liệu** | Đầu phiên: code mới có làm tài liệu nào cũ đi không | Mình nói "kiểm tra tài liệu" |
| 6 | **Quét bằng extension của editor trước commit** | Bắt lỗi mà lệnh lint của dự án không có | AI chạy lệnh tương ứng; mình tự xem bảng lỗi |
| 7 | **Lệnh / kỹ năng "làm một việc"** | Dẫn một việc qua các bước của [[Quy trình một việc]] theo quy tắc dự án: nhận → làm rõ → phương án / thiết kế (dừng ở cổng duyệt) → kế hoạch test → làm → kiểm tra → đối chiếu → báo cáo; nối với bảng việc / ticket nếu dự án có | Mình dán "làm theo quy trình một việc của dự án" |
| 8 | **Lệnh đặc thù** | Việc lặp lại riêng của dự án (chạy kiểm tra qua hàng chờ, sinh lại kiểu từ đặc tả, dọn dữ liệu thử…) — AI đề xuất khi thấy việc lặp | Ghi lệnh tay vào quy tắc |

Không cần đủ cả tám: dự án không có công cụ định dạng / lint thì bỏ #2; nhóm / repo đã có lệnh tương đương (#3, #7, #8) hoặc hook (#4) thì dùng cái có sẵn, chỉ **nối thêm** phần thiếu.

## Yêu cầu chung

- **Mình duyệt trước khi tạo**: sẽ tạo gì, ở đâu, chạy khi nào.
- Chỉ dùng cấu hình của dự án; công cụ không có cấu hình trong dự án → bỏ bước đó, in SKIP.
- Hook **không bao giờ chặn phiên AI vì chính nó lỗi**: lỗi của hook → cảnh báo, cho đi tiếp. Không có gì để báo → không in gì.
- Có cách tắt một lần và cách gỡ hẳn; ghi cả hai cùng nơi đặt, lệnh chạy tay vào quy tắc dự án.

## Git hook: kiểm hook có sẵn trước khi tạo

1. Thư mục hook thật: `git rev-parse --git-path hooks` (đúng cả với worktree).
2. `git config --show-origin core.hooksPath`: có giá trị → hook ở thư mục mặc định bị bỏ qua. Cấu hình global → **báo mình**, không sửa.
3. Nhóm có trình quản lý hook (cấu hình / thư mục hook được track) → **nối thêm** bước của mình sau bước của nhóm bằng cách gọi script trong `.me/`; không sửa file được track.
4. Thư mục hook có file không phải mẫu (`*.sample`) → đọc, nối thêm, không ghi đè.
5. Chưa có gì → tạo hook trong thư mục hook của repo này, gọi script trong `.me/`.
6. Thử cả hai đường: có lỗi thì bị chặn, sạch thì qua.

## Quét bằng extension của editor trước commit

Nhiều extension **chỉ quét file đang mở**, quét **chậm** và **không tự báo cho AI**: bảng lỗi trống chưa có nghĩa là sạch.

1. **Mở** từng file code sắp commit (loại extension có quét) bằng lệnh mở file của editor.
2. **Chờ** đủ lâu cho extension **chậm nhất**. Đọc ngay sau khi mở → rỗng dù có lỗi (đã thử thật). Extension lint / kiểu thường vài giây; extension phân tích chất lượng có lần cần 10–15 giây.
   - **Đo lúc khởi tạo** trên đúng máy và editor: mở một file có lỗi chắc chắn cho **từng** extension, đọc ngay (rỗng), chờ rồi đọc lại tới khi đủ. Mặc định = số giây của extension chậm nhất + dư vài giây, cộng ~1 giây mỗi file thêm, có giới hạn trên và biến môi trường để chỉnh. Xoá file thử.
   - Gói "mở + chờ" thành một script để AI không quên bước chờ.
3. **Đọc mọi nơi extension báo**: bảng lỗi (qua tích hợp editor của công cụ AI, từng file), gạch chân trong file, khung / log riêng của extension. Đọc **hai lần**; lần hai có thêm mục → chờ tiếp.
4. **Không đọc được** (không có tích hợp editor, editor không chạy) hoặc nghi chưa quét → chạy lệnh tương ứng trên đúng các file đó (lint, kiểu, chính tả, quét chất lượng). Lint dòng lệnh thường không bắt lỗi kiểu → chạy cả kiểm kiểu. Extension không có lệnh tương ứng → nhờ mình xem. Ghi vào quy tắc dự án lệnh nào thay extension nào.
5. **Gom vào một bảng** rồi sửa trước khi commit: file:dòng, nguồn, mã luật, mức, nội dung, cách xử lý. Danh sách dài → một file trong thư mục trạng thái của `.me/`, xoá khi sạch.

| Loại báo | Xử lý |
|---|---|
| Lỗi, cảnh báo | Sửa |
| Cảnh báo **sai** (làm theo sẽ hỏng code) | Không làm theo; tắt **đúng dòng đó** bằng chú thích của extension kèm mã luật + lý do; không tắt luật cho cả dự án |
| Thông tin (chính tả…) do thay đổi của mình | Sửa; từ đúng → thêm vào từ điển dự án |
| Gợi ý | Chỉ sửa khi nằm trong diff và khớp quy ước |
| Có sẵn từ trước, ngoài diff | Báo mình; không tự sửa; sửa thì tách commit |

6. Sửa xong → quét lại từ bước 1, chạy lint / test phần đã sửa. Sạch → **đánh dấu đã quét** theo mã băm nội dung các file (lưu trong `.me/`).
7. Git hook (#4) kiểm dấu: commit do AI chạy mà file đã stage chưa được quét ở **đúng nội dung đó** → chặn, in danh sách file và các bước trên. Commit mình tự chạy → không đòi dấu.

## Kiểm tra lệch tài liệu

Mục tiêu: tài liệu dự án khớp code mà không phải đọc lại cả dự án.

1. **Bản đồ** (mục "Tài liệu ↔ code" của [[Mẫu - Bản đồ dự án]]) nối tài liệu với thư mục / file code.
2. **Mốc đồng bộ**: commit của nhánh chính và HEAD tại lần cuối tài liệu được xác nhận khớp, cùng tên nhánh chính để so.
3. **Đầu phiên**: fetch nhánh chính (không hỏi mật khẩu, có thời hạn; lỗi mạng → dùng bản local, nói vậy) → commit chưa nằm trong mốc → file đổi (kể cả bị xoá) → đối chiếu bản đồ.
4. **Báo** (chỉ khi có): số commit; tài liệu cần xem lại ← file code đã đổi; file code đổi mà bản đồ chưa có. Bỏ qua `.me/`, lockfile.
5. Mình đồng ý → AI đọc thay đổi của đúng các file được báo, sửa tài liệu, cho xem → mình xác nhận → **đặt mốc mới**. Không đồng ý → giữ mốc, lần sau báo lại.

Trong lúc làm tính năng, AI sửa tài liệu liên quan luôn trong việc đó; mốc chỉ dời khi mình xác nhận.

## Làm lại — chỉ khi mình yêu cầu

- Đổi công cụ AI hoặc editor → hiện thực lại #1, #2, #3, #5, #6, #7, #8.
- File này đổi sau đợt cập nhật → AI so hook hiện có với file này và đề xuất.
- Kết thúc dự án → gỡ git hook trước, rồi xoá các file đã tạo.
