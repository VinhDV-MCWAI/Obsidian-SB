---
chu_de: thong-bao-loi
ap_dung: [front-end]
muc_do: nen-tang
cap_nhat: 2026-10-26
---
# Thông báo và hiển thị lỗi

## Lựa chọn của tôi

- **Một** cơ chế thông báo (toast) dùng chung, **một vị trí** cho cả ứng dụng, bốn loại: thành công, thông tin, cảnh báo, lỗi.
- Lỗi **không tự tắt**; thành công tự tắt sau vài giây.
- Lỗi validate hiện **dưới đúng ô**, cuộn và focus ô lỗi đầu tiên; lỗi server 422 hiện cùng cách đó.
- Lỗi hệ thống / mất mạng: thông báo chung + mã tra cứu + nút thử lại; **không bao giờ mất dữ liệu đã nhập**.
- Câu thông báo lấy từ khoá ngôn ngữ, nói *chuyện gì* + *làm gì tiếp*.

## Loại thông báo

| Loại | Màu (gợi ý) | Biểu tượng | Tự tắt | Dùng khi |
|---|---|---|---|---|
| Thành công | Xanh lá | ✓ | 3–5 giây | Lưu, xoá, gửi xong |
| Thông tin | Xanh dương | i | 5 giây | Việc nền đã bắt đầu, có cập nhật mới |
| Cảnh báo | Vàng / cam | ! | Không tự tắt hoặc ≥ 8 giây | Thành công một phần, sắp hết hạn |
| Lỗi | Đỏ | ✕ | **Không tự tắt** (người dùng đóng) | Thao tác thất bại |

Màu không phải tín hiệu duy nhất (người mù màu) → luôn có biểu tượng + chữ. Dùng màu từ hệ thống thiết kế (token), không mã màu viết thẳng.

## Chọn kiểu hiển thị

| Tình huống | Kiểu | Vị trí |
|---|---|---|
| Kết quả một thao tác (lưu xong) | Toast | Góc trên phải (máy tính), trên giữa / dưới giữa (điện thoại) — **một vị trí cho cả app** |
| Lỗi từng ô nhập | Chữ dưới ô | Ngay dưới ô, viền ô đỏ |
| Nhiều lỗi trong form dài | Tóm tắt đầu form + chữ dưới từng ô | Đầu form, có liên kết tới ô |
| Lỗi cả trang (không tải được dữ liệu) | Khối lỗi trong vùng nội dung + nút "Thử lại" | Thay vùng dữ liệu |
| Mất mạng, bảo trì, hết phiên | Thanh cố định | Đầu trang |
| Cần người dùng quyết định | Dialog xác nhận | [[Dialog và hộp xác nhận]] |

## Thứ tự hiển thị lỗi khi gửi form

1. Kiểm phía front-end → có lỗi: hiện dưới từng ô, **cuộn và focus ô lỗi đầu tiên** (theo thứ tự trên màn hình), không gửi.
2. Gửi → server trả 422 → gắn từng lỗi vào đúng ô (cùng cách hiển thị như bước 1).
3. Server trả 409 / lỗi nghiệp vụ → toast lỗi hoặc thông báo đầu form, giữ nguyên dữ liệu.
4. 401 → thông báo hết phiên, chuyển trang đăng nhập (giữ đường dẫn quay lại; nếu được, giữ nháp).
5. 403 → "Bạn không có quyền …".
6. 5xx / mất mạng → toast lỗi chung + mã tra cứu (`trace_id`) + cho thử lại; **không mất dữ liệu đã nhập**.

## Nội dung thông báo

- [ ] Nói **chuyện gì** + **làm gì tiếp**: "Không lưu được vì mất kết nối. Kiểm tra mạng rồi thử lại."
- [ ] Ngắn, ngôn ngữ người dùng, không thuật ngữ ("Lỗi 500", "SQLSTATE", "null").
- [ ] Có tên đối tượng: "Đã xoá danh mục 'Tin tức'" thay vì "Xoá thành công".
- [ ] Lỗi ô nhập: nói giới hạn ("Tối đa 50 ký tự"), không chỉ "Không hợp lệ".
- [ ] Lấy từ khoá thông báo ([[Hằng số, enum, thông báo]]), không viết thẳng câu trong component.
- [ ] Không xếp chồng quá 3 toast; cùng thông báo lặp lại thì gộp.
- [ ] Thông báo cho trình đọc màn hình (`aria-live`: `polite` cho thành công, `assertive` cho lỗi).
