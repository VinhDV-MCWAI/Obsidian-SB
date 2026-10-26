---
loai: huong-dan
cap_nhat: 2026-10-26
---
# Hướng dẫn sử dụng — mình đang có gì, làm tốt nhất tới đâu

Dành cho mình (hoặc bất kỳ ai dùng Playbook): biết **đang ở đâu, có gì, làm được tới đâu, giới hạn ở đâu**, để mỗi việc chọn đúng cách làm — không lãng phí, không bỏ sót.

**Khi nào đọc:** lần đầu dùng; khi đổi gói AI / công cụ / máy; khi vào dự án mới; khi thấy kết quả AI kém đi.

**Nhờ AI làm thay:**

> "Dùng playbook: đọc Hướng dẫn sử dụng, hỏi tôi những câu ở mục 1 còn trống, rồi đề xuất cách làm việc cho tôi theo mục 2–5 (bảng ngắn)."

---

## 1. Mình đang có gì (điền, cập nhật khi đổi)

| Câu hỏi | Hiện tại | Ảnh hưởng tới |
|---|---|---|
| Gói AI đang dùng (miễn phí / trả phí cá nhân / gói cao / nhóm – doanh nghiệp / API trả theo token) | *chưa điền* | Model được dùng, giới hạn lượt, chi phí mỗi việc |
| Có giới hạn lượt theo giờ / tuần không? Hay hay chạm giới hạn? | *chưa điền* | Có nên dồn model mạnh cho việc khó không |
| Công cụ AI | Claude Code trong VS Code (WSL2, Linux) | Đọc / sửa repo, chạy lệnh, kỹ năng, bộ nhớ, tác tử phụ |
| Model hằng ngày / khi việc khó | *chưa điền* (phiên 2026-10-26 dùng model mạnh nhất) | Mức đọc Playbook ([[Khi nào dùng Playbook]]) |
| Kỹ năng / bộ nhớ đã cài | `playbook` (cấp người dùng); bộ nhớ theo từng dự án | AI tự biết Playbook và quy ước đã chốt |
| Môi trường | Windows + WSL2, Docker, VS Code | Lệnh chạy trong container; đường dẫn `/mnt/c/...` |
| Dự án đang làm và loại (A mới / B cũ cần nâng cấp / C đã chuẩn / D nặng) | Second Memory — loại C | [[Thiết lập theo loại dự án]] |
| Dự án công ty: chính sách dùng AI với source? | *chưa điền* | Được dùng tới đâu, có phải ẩn dữ liệu không |
| Việc chính hằng ngày (tính năng / sửa lỗi / review / tài liệu / vận hành) | *chưa điền* | Mục 3 |
| Mình mạnh / yếu ở đâu | *chưa điền* | AI bù phần yếu (ví dụ: yếu giao diện → bật checklist giao diện thường xuyên hơn) |

---

## 2. Làm được tốt tới đâu — theo gói và công cụ

| Tình trạng | Làm tốt | Giới hạn | Cách tối ưu |
|---|---|---|---|
| **Claude Code** (CLI / VS Code / desktop) | Đọc cả repo, sửa nhiều file, chạy test / lint, dùng kỹ năng + bộ nhớ, tác tử phụ tìm kiếm rộng | Ngữ cảnh có hạn → phiên dài loãng; chỉ thấy thứ nó đọc | Một phiên một việc; `.me/` để không khám phá lại; dọn ngữ cảnh giữa các việc không liên quan |
| **Chat** (không có Claude Code) | Giải thích, thiết kế, viết đoạn code nhỏ, review đoạn dán vào | Không thấy repo, không chạy được gì → không tự kiểm | Dán đúng phần liên quan + **dán checklist chốt chặn** cần dùng; tự chạy test |
| **Gói có giới hạn lượt** | Đủ cho việc hằng ngày nếu chọn model hợp lý | Hết lượt giữa việc | Model mạnh cho thiết kế / điều tra / chốt chặn; model nhanh cho sửa nhỏ, chạy lệnh, tài liệu |
| **API trả theo token** | Không giới hạn lượt | Chi phí tăng theo độ dài ngữ cảnh | Prompt ngắn, phiên ngắn, tận dụng cache; không dán cả file khi chỉ cần một hàm |
| **Model mạnh nhất** | Thiết kế, điều tra lỗi khó, việc nhiều phần, bảo mật | Đắt / chậm hơn | Chỉ khi bậc cân bằng không đủ (bậc thang bên dưới) |
| **Model cân bằng** | Phần lớn việc tính năng, sửa lỗi, review | Dễ sót chi tiết hơn ở việc dài | Bật checklist chủ đề (mức 1–2) |
| **Model nhanh** | Sửa nhỏ, đổi tên, viết tài liệu theo mẫu, tìm kiếm | Kém ở suy luận nhiều bước | Việc rõ, phạm vi hẹp; luôn kiểm bằng lệnh |

Tên model đổi theo thời gian — dùng ba nhóm "mạnh nhất / cân bằng / nhanh" và tra tên hiện tại trong công cụ.

### Nguyên tắc: model **thấp nhất đủ sức**, nâng bậc khi có dấu hiệu

| Bậc | Nhóm | Dùng cho |
|---|---|---|
| 1 | Nhanh | Quét cấu trúc, tìm kiếm, tóm tắt file / module, việc máy móc, tài liệu theo mẫu, sửa nhỏ đã rõ |
| 2 | Cân bằng — **mặc định** | Tính năng, sửa lỗi, review, tổng hợp + đề xuất khi tiếp cận dự án, chốt chặn thông thường |
| 3 | Mạnh nhất — **chỉ khi cần** | Thiết kế / đổi kiến trúc nhiều phần; lỗi khó mà bậc 2 đã thử không ra; rủi ro rất cao (bảo mật, tiền, dữ liệu không khôi phục được); bậc 2 cho kết quả yếu |

**Nâng bậc khi:** sai lặp lại, giải thích tự mâu thuẫn, bỏ sót dòng chốt chặn, đi vòng không tiến. **Hạ bậc khi:** phần còn lại là việc máy móc.

**Tiếp cận dự án nặng không cần model mạnh nhất:** tác tử phụ bậc 1 quét từng module → bản tóm tắt ngắn; phiên chính bậc 2 tổng hợp, phân loại, hỏi, đề xuất; bậc 3 chỉ khi kiến trúc rối hoặc có quyết định lớn.

### Ai chọn model — sự thật cần biết

- AI **không tự đổi model của phiên** đang chạy; mình đổi (trong Claude Code: lệnh chọn model, hoặc mở phiên mới).
- Đổi model **giữa phiên** làm mất phần ngữ cảnh đã được lưu đệm → lượt kế tiếp đắt hơn. Nên chọn model **khi mở phiên**, theo loại việc.
- Hỏi AI "nên dùng model nào" trước mỗi việc = thêm một lượt trả lời trên model hiện tại → thường đắt hơn tự chọn theo bảng.
- Cách tối ưu: **mình chọn theo bảng** (mục 3 hoặc `.me/Hồ sơ dự án.md`); AI chỉ lên tiếng khi lệch rõ (model yếu gặp việc chốt chặn khó → dừng và đề xuất chuyển; model mạnh làm việc máy móc dài → gợi ý một dòng) — xem [[Khi nào dùng Playbook]].
- Claude Code có tác tử phụ chọn được model → phiên chính (model mạnh) có thể giao phần tìm kiếm / việc máy móc cho model rẻ hơn mà mình không phải đổi tay.
- Bước **khởi đầu dự án** cũng theo bậc thang ở trên: quét bằng bậc 1, tổng hợp + đề xuất bằng bậc 2; chỉ lên bậc 3 khi dự án thật sự phức tạp.

---

## 3. Theo loại việc — chọn gì, prompt thế nào

**Khung prompt ngắn** (đủ cho hầu hết việc):

> *Mục tiêu:* … · *Phạm vi:* chỉ `<thư mục / module>` · *Không:* … (đổi DB, thêm thư viện, sửa ngoài phạm vi) · *Xong khi:* … (test / lệnh / màn hình chạy được)

| Việc | Model | Playbook | Phạm vi | Thêm vào prompt | Kiểm bằng |
|---|---|---|---|---|---|
| Sửa lỗi rõ nguyên nhân | Nhanh / cân bằng | 0 (trừ chốt chặn) | 1–2 file | Lỗi + vị trí | Test tái hiện lỗi rồi pass |
| Lỗi chưa rõ nguyên nhân | Cân bằng → mạnh nếu thử một lượt chưa ra | 0–1 | Đọc rộng, **sửa hẹp** | "Tìm nguyên nhân gốc trước, báo giả thuyết, chưa sửa" | Bằng chứng (log, test) cho nguyên nhân |
| Tính năng một phần (một API / một form) | Cân bằng | 1 | Một module | Dữ liệu vào / ra, quyền | Test + đối chiếu chốt chặn |
| Tính năng nhiều phần (DB + API + giao diện) | Cân bằng; mạnh nếu đổi kiến trúc / rủi ro cao | 2 | Một tính năng xuyên lớp | "Ghi danh sách bước, chờ duyệt phần DB / API" | Từng bước một commit + test |
| Refactor | Cân bằng / mạnh | 0–1 | Đúng phần refactor | "Không đổi hành vi; lỗi thấy được thì báo, commit riêng" | Test cũ pass, không sửa test |
| Review code | Cân bằng; mạnh cho phần bảo mật / tiền | 1 (checklist review trong [[Git và quy trình]]) | Diff | "Chỉ báo lỗi thật, xếp theo mức nặng" | Mình đọc lại các lỗi nặng |
| Viết / dọn tài liệu | Nhanh / cân bằng | 0–1 ([[Tài liệu dự án]]) | Thư mục tài liệu | Người đọc là ai | Đọc lại một lượt |
| Tiếp cận dự án | Cân bằng (quét: tác tử phụ nhanh) | [[Thiết lập theo loại dự án]] | Chỉ quét cấu trúc | "Chưa sửa gì" | Mình xác nhận loại + đề xuất |
| Sự cố / vận hành | Cân bằng; mạnh nếu nguyên nhân khó / mất dữ liệu | 1 ([[Vận hành và sự cố]]) | Đọc rộng, thao tác hẹp | "Giữ bằng chứng trước khi bật lại; hỏi trước thao tác không quay lui" | Hệ thống chạy lại + nguyên nhân gốc |

---

## 4. Cô lập phạm vi tới đâu

| Mức | Khi | Cách |
|---|---|---|
| Một file / hàm | Sửa nhỏ | Nêu đường dẫn trong prompt |
| Một module | Phần lớn việc | "Chỉ sửa trong `<thư mục>`" |
| Một tính năng xuyên lớp | Tính năng nhiều phần | Danh sách bước; mỗi bước một commit |
| Cả repo | Đổi tên dùng khắp nơi, nâng cấp thư viện | Tìm mọi chỗ dùng trước; làm trên nhánh / worktree riêng |

Quy tắc:
- **Một phiên một việc.** Việc mới không liên quan → phiên mới (hoặc xoá ngữ cảnh).
- Việc dài nhiều phiên → danh sách bước + tiến độ trong `.me/Tiến độ.md`.
- Tìm kiếm rộng nhiều file → để tác tử phụ tìm, chỉ trả kết luận (giữ ngữ cảnh chính sạch).
- Thử nghiệm rủi ro → nhánh / worktree riêng, xong mới gộp.

**Dấu hiệu ngữ cảnh đã loãng:** AI quên quy tắc vừa nói, đọc lại file vừa đọc, lặp lại đề xuất cũ, trả lời chung chung → ghi tiến độ, mở phiên mới.

---

## 5. Điểm mù — AI hay bỏ sót (kể cả model mạnh)

| Điểm mù | Chặn bằng |
|---|---|
| Nói "đã test / đã chạy" mà chưa chạy | Yêu cầu lệnh + kết quả thật ([[Đối chiếu checklist và minh chứng]]) |
| Sửa một chỗ, quên nơi khác đang gọi | "Tìm mọi chỗ dùng trước khi sửa" ([[Luồng làm một việc]] mục 5) |
| Quên cập nhật đặc tả API, kiểu dữ liệu client, tài liệu | Bảng "khi code đổi" trong [[Tài liệu dự án]] |
| Trường hợp biên: rỗng, quá dài, đồng thời, quyền theo bản ghi | Checklist chốt chặn |
| Kiểm quyền ở giao diện, quên server | [[Phân quyền theo vai trò]] |
| Tự mở rộng phạm vi (refactor thêm, "tiện thì sửa") | "Không" trong prompt; refactor tách commit |
| Nuốt lỗi, bọc try / catch rộng để "chạy được" | [[Giao dịch, lỗi và phản hồi API]] |
| Kiến thức cũ: phiên bản thư viện, API đã đổi | Kiểm tài liệu chính thức khi đụng thư viện / dịch vụ ngoài |
| Quá tin bộ nhớ / phiên trước | Kiểm lại file, hàm còn tồn tại trước khi dùng |
| Quên dọn dữ liệu thử, file tạm | [[Môi trường phát triển và dọn dẹp]] |

Gặp điểm mù **mới** → một dòng [[Ghi chú]], chủ đề `diem-mu`. Đợt cập nhật đưa vào bảng này.

---

## 6. Biết AI đang làm gì

- Đầu câu trả lời có dòng **"Playbook: …"** → kỹ năng đã chạy và đã chọn mức. **Không có dòng đó** ở việc chạm chốt chặn → kỹ năng không chạy → nhắc "Dùng playbook:".
- Dòng ✅ trong đối chiếu phải có vị trí kiểm được.
- Có lệnh kiểm tra + kết quả thật trước khi báo xong.

## 7. Giới hạn của Playbook (nói thẳng)

- **Không thay được việc mình xem lại** ở chốt chặn (quyền, DB, tiền, phát hành).
- Không đảm bảo AI luôn tự gọi kỹ năng — dòng "Playbook: …" là cách phát hiện.
- "Lựa chọn của tôi" là bản đầu — chỉ đáng tin sau khi mình sửa theo kinh nghiệm thật.
- Không chứa nghiệp vụ của dự án — cái đó ở `.me/` và tài liệu dự án.
- Đánh giá đầy đủ: [[Đánh giá khả thi]].
