---
chu_de: moi-truong-dev
ap_dung: [moi-du-an]
muc_do: nen-co
cap_nhat: 2026-10-27
---
# Môi trường phát triển và dọn dẹp

Nơi gốc của quy tắc dữ liệu thử và dọn dẹp (bước cuối của [[Quy trình một việc]]). Cô lập dữ liệu test: [[Chiến lược kiểm thử]].

## Lựa chọn của tôi

- Mọi thứ chạy trong **container**; máy chỉ cài những gì editor cần (để extension hoạt động). Không chạy test / migration / cài thư viện trên máy.
- **Một file lệnh chung** bọc các lệnh hay dùng: dựng, bật, tắt, test, lint, "kiểm tra tất cả", làm mới dữ liệu dev. `help` liệt kê lệnh.
- Script (sinh env, build, DB, sao lưu) **chạy lặp lại an toàn**.
- Test dùng DB / cache **riêng**, có test kiểm chính việc cô lập.
- **Một lockfile** cho cả repo nhiều gói.
- Hai lệnh kiểm: "kiểm nhanh trong môi trường dev (gồm thay đổi chưa commit)" và "kiểm đúng như CI (code đã commit, môi trường dùng xong bỏ)".

## Cấu hình và biến môi trường

- [ ] Có file mẫu (`.env.example`) liệt kê **mọi** biến, giá trị giả, chú thích; cập nhật cùng lúc khi thêm biến.
- [ ] Ứng dụng **kiểm cấu hình khi khởi động**: thiếu / sai biến bắt buộc → dừng ngay với thông báo rõ, không chạy với giá trị rỗng.
- [ ] Code chỉ đọc cấu hình qua lớp cấu hình, không đọc thẳng biến môi trường ([[Thiết kế code và chia lớp]]).
- [ ] Giá trị mặc định an toàn (debug tắt, cookie bảo mật bật) — môi trường dev mới bật nới lỏng.
- [ ] Bí mật không nằm trong file mẫu, log, tài liệu, image; production lấy từ kho bí mật.
- [ ] Script sinh `.env` cho dev chạy lặp lại an toàn, không ghi đè giá trị đã có.

## Dọn dẹp sau mỗi việc

Việc chưa xong khi còn rác.

- [ ] Bản ghi tạo tay khi thử đặt tiền tố (`TMP `, tài khoản `tmp_…`); bản ghi của test đầu cuối đặt `E2E ` và tự xoá kể cả khi lỗi.
- [ ] Xoá bản ghi thử qua **chức năng của ứng dụng** (để phần đi kèm như file trong kho cũng bị xoá), không xoá thẳng trong DB.
- [ ] Container dùng một lần chạy kiểu tự xoá khi xong.
- [ ] File tạm, kết quả lệnh để ngoài repo.
- [ ] Code chết, code comment lại, file `.bak`, lệnh in debug → xoá.
- [ ] Tài liệu nhắc thứ đã xoá / đổi tên → sửa.
- [ ] Ghi trong ghi chú việc: đã dọn gì, cố ý để lại gì.

## Không bao giờ khi chưa hỏi

- Xoá volume dữ liệu, làm mới DB dev, rollback migration trên DB dev / chung.
- Xoá dữ liệu không do mình tạo trong việc này, dữ liệu mẫu, file thật của người dùng.

## Bẫy

- Gắn **từng file lẻ** từ máy vào container → dễ hỏng sau khi máy khởi động lại; gắn cả thư mục hoặc chép file vào image.
- Test chạy trên DB dev → mất dữ liệu đang thử bằng tay.
- Dọn "rác container" bằng lệnh xoá cả dữ liệu (volume).
