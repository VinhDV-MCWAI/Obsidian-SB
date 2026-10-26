---
chu_de: khi-nao-dung
ap_dung: [moi-viec]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Khi nào dùng Playbook — đọc đầu tiên

Mục đích: **không lãng phí** khi model đủ sức, nhưng **luôn có chốt chặn** ở những chỗ sai thì đắt. Playbook là nguồn tham khảo, không phải quy trình bắt buộc. Lập kế hoạch thế nào, đọc gì thêm — AI tự quyết theo việc; Playbook chỉ đưa chuẩn và chỗ hay sót.

## Bước 1 — việc có chạm chốt chặn không? (luôn kiểm, mất vài giây)

**Chốt chặn** = chỗ AI kể cả model mạnh vẫn hay sai, và sai thì hậu quả lớn. Việc chạm một trong các mục dưới → **bắt buộc** mở đúng checklist đó và đối chiếu trước khi báo xong, bất kể việc lớn hay nhỏ.

| Chạm vào | Checklist bắt buộc |
|---|---|
| Đăng nhập, phiên, quyền, ai được xem / sửa gì | [[Đăng nhập và phiên]], [[Phân quyền theo vai trò]], [[Bảo mật cơ bản]] |
| Dữ liệu người dùng nhập (form, request, tham số URL, import) | [[Validate - quy tắc chung]] + file loại trường liên quan |
| Đổi cấu trúc DB, migration, xoá dữ liệu, xoá hàng loạt | [[Dữ liệu và DB]], [[Thùng rác và xoá]] |
| Tiền, số lượng, tồn kho, thao tác đồng thời | [[Validate - Số và tiền]], [[Hiệu năng, cache và dịch vụ ngoài]] (phần đồng thời) |
| Upload / tải file | [[Upload file và kho file]], [[Validate - File và ảnh]] |
| Lỗi trả về, giao dịch, khung phản hồi API | [[Giao dịch, lỗi và phản hồi API]] |
| Phát hành, cấu hình production, biến môi trường, bí mật | [[Phát hành và quay lui]], [[Môi trường phát triển và dọn dẹp]] |
| Log có thể chứa dữ liệu cá nhân / bí mật | [[Log và theo dõi]] |
| Gửi ra bên ngoài: mail, tin nhắn, webhook, gọi API bên thứ ba | [[Hiệu năng, cache và dịch vụ ngoài]] (phần dịch vụ ngoài), [[Giao dịch, lỗi và phản hồi API]] (gửi sau commit) |
| Việc nền, hàng đợi, việc hẹn giờ | [[Việc nền và thông báo tức thì]] |
| Dữ liệu riêng tư có thể lộ ra trang công khai / tìm kiếm / xuất file | [[Phân quyền theo vai trò]] (quyền theo dữ liệu), [[Tìm kiếm]] |

Không chạm chốt chặn nào → sang bước 2.

## Bước 2 — có cần tham khảo thêm không?

| Tình huống | Quyết định |
|---|---|
| Việc nhỏ / rõ (sửa chữ, đổi giao diện nhỏ, sửa lỗi rõ nguyên nhân, việc lặp lại cách đã có trong dự án) | **Không dùng Playbook** |
| Dự án đã có quy ước cho đúng việc này (code mẫu sẵn, rules, `.me/`) | **Theo dự án**, không dùng Playbook |
| Việc thuộc chủ đề Playbook có **"Lựa chọn của tôi"** mà dự án chưa có quy ước (khung phản hồi, dialog, thông báo, mã lỗi…) | **Mở đúng một file** để giữ nhất quán |
| Model yếu / rẻ, hoặc phiên đã rất dài (ngữ cảnh loãng), hoặc việc nhiều phần | **Mở checklist chủ đề** dù không chạm chốt chặn |
| Chủ đề mới với mình / chưa từng làm trong dự án | Mở file "Giải pháp thường gặp" liên quan để biết bẫy |

## Bước 3 — mức đọc

| Mức | Đọc | Khi |
|---|---|---|
| 0 | Không đọc | Bước 1 không chạm + bước 2 "không dùng" |
| 1 | Một file chốt chặn / một file chủ đề, đi thẳng theo bảng trên | Phần lớn các việc vài ngày |
| 2 | [[Mục lục cho AI]] + 2–3 file | Việc nhiều phần hoặc chưa rõ thuộc chủ đề nào |
| 3 | Như mức 2 + `.me/` + đọc code liên quan trước khi lập kế hoạch | Việc lớn, đổi kiến trúc, dự án nặng chưa quen |

Không dò mục lục khi bảng ở bước 1–2 đã chỉ thẳng file.

## Model có hợp với việc không

AI **không tự đổi được model của phiên** — mình đổi. Vì vậy không làm một bước "đánh giá rồi đề xuất model" riêng cho mỗi việc (tốn thêm một lượt, và lượt đánh giá đã chạy trên model hiện tại rồi). Thay vào đó:

| Tình huống | AI làm |
|---|---|
| Model hiện tại hợp với việc | Làm luôn, không nói gì |
| Model hiện tại **thấp hơn** bậc đủ sức của việc (bậc thang trong [[Hướng dẫn sử dụng]] mục 2) | **Dừng trước khi làm**, một dòng: "Việc này cần ít nhất bậc <n> vì …" + prompt gợi ý để dán lại. Đề xuất **bậc thấp nhất đủ sức**, không mặc định mạnh nhất |
| Model hiện tại **cao hơn nhiều** so với cần (việc dài, máy móc) | Một dòng: "Bậc <n> làm được việc này" — vẫn làm nếu mình không đổi |
| Đang làm mà có dấu hiệu quá sức (sai lặp lại, tự mâu thuẫn, bỏ sót chốt chặn) | Nói thẳng, đề xuất nâng một bậc |
| Công cụ có tác tử phụ chọn được model | Có thể giao phần tìm kiếm rộng / việc máy móc cho tác tử phụ model rẻ hơn, phần quyết định giữ ở phiên chính |

Mình chọn model theo bảng loại việc trong `.me/Hồ sơ dự án.md` (hoặc [[Hướng dẫn sử dụng]] mục 3) **khi mở phiên** — nhanh và miễn phí hơn hỏi AI.

## Lập kế hoạch

- AI tự quyết có cần kế hoạch và viết dạng gì; [[Mẫu - Kế hoạch việc]] chỉ để tham khảo.
- **Chốt chặn bất biến:** việc đổi DB, quyền / đăng nhập, thêm thư viện, xoá / đổi tính năng, hợp đồng API, thao tác không quay lui được → **nêu kế hoạch và chờ mình duyệt** trước khi làm.
- Việc dài: AI ghi danh sách bước vào `.me/Tiến độ.md` (hoặc nơi mình chỉ định) để làm theo và tiếp tục ở phiên sau.

## Báo trong câu trả lời

Một dòng ở đầu: *"Playbook: mức 1 — Validate - Email (chốt chặn: dữ liệu người dùng)"* hoặc *"Playbook: không dùng — việc nhỏ"*. Mình thấy ngay AI đã chọn gì, sai thì sửa.
