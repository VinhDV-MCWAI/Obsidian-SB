
> **Chủ đề trung tâm**
> 
> _Máy tính không được tạo ra để tính toán trước, mà được tạo ra để xử lý thông tin._

---

# Mục tiêu của chương

Sau chương này bạn sẽ trả lời được:

- Thông tin là gì?
    
- Dữ liệu là gì?
    
- Vì sao con người phải lưu trữ dữ liệu?
    
- Tại sao việc lưu trữ ngày càng trở nên khó khăn?
    
- Vì sao từ nhu cầu lưu trữ lại sinh ra toán học, máy tính và toàn bộ Computer Science?
    

---

# 1. Khi chưa có máy tính

## Khoảng 100.000 năm trước

Con người chỉ có trí nhớ.

Muốn truyền đạt kiến thức chỉ có thể:

- nói
    
- quan sát
    
- bắt chước
    

Ví dụ:

> Loài thú này nguy hiểm.

Muốn truyền cho con cháu.

Chỉ có thể kể lại.

Nếu người kể chết...

Thông tin cũng chết.

Đây là vấn đề đầu tiên của nhân loại.

> **Thông tin không bền vững.**

---

# Vấn đề số 1

Con người nhớ không chính xác.

Ví dụ:

Ngày đầu

```
Có 5 con nai ở phía bắc.
```

Sau nhiều lần truyền miệng

```
Có rất nhiều nai.
```

Hoặc

```
Có khoảng 7 con.
```

Thông tin bị biến dạng.

Trong khoa học máy tính ngày nay, đây được gọi là:

> **Data Integrity (Tính toàn vẹn dữ liệu)**

Đây không phải khái niệm mới.

Con người đã đối mặt với nó từ hàng chục nghìn năm trước.

---

# Giải pháp đầu tiên

Con người bắt đầu ghi lại.

Ban đầu bằng:

- hình vẽ
    
- ký hiệu
    
- khắc đá
    

Ví dụ

Một con bò

↓

Vẽ hình con bò.

Một người chết

↓

Khắc biểu tượng.

Thông tin không còn phụ thuộc vào trí nhớ.

---

# Nhưng ngay lập tức xuất hiện vấn đề mới

Giả sử muốn ghi:

```
Có 1.235 con cừu.
```

Nếu mỗi con cừu đều phải vẽ một hình...

Việc ghi chép trở nên không thể.

Thông tin ngày càng lớn.

Biểu diễn trở thành vấn đề.

---

# Vấn đề số 2

Biểu diễn thông tin quá tốn công.

Con người cần:

- ít ký hiệu hơn
    
- ghi nhanh hơn
    
- đọc dễ hơn
    

---

# Giải pháp

Sinh ra chữ viết.

Ví dụ

Không cần vẽ cả con bò.

Chỉ cần một ký hiệu đại diện.

Đó là bước đầu tiên của khái niệm:

> **Representation (Biểu diễn thông tin)**

Đây là một khái niệm cốt lõi của Computer Science.

Một thực thể ngoài đời luôn được biểu diễn bằng một đại diện đơn giản hơn.

Ví dụ hiện đại:

```
Người thật
↓

Object User
↓

Binary
↓

Bit
```

Mọi tầng đều chỉ là biểu diễn của tầng phía trên.

---

# Thông tin và dữ liệu khác nhau thế nào?

Đây là chỗ rất nhiều tài liệu nói chưa rõ.

## Thông tin (Information)

Là ý nghĩa.

Ví dụ

```
Nhiệt độ ngoài trời là 37°C.
```

Người đọc hiểu:

> Hôm nay rất nóng.

Đó là thông tin.

---

## Dữ liệu (Data)

Là cách lưu trữ thông tin.

Ví dụ

```
37
```

hoặc

```
00100101
```

hoặc

```
0x25
```

Ba cách biểu diễn khác nhau.

Ý nghĩa vẫn là:

Điều quan trọng:

> **Thông tin không đổi. Dữ liệu có thể thay đổi cách biểu diễn.**

---

Ví dụ Backend

Trong Java

```java
int age = 20;
```

Trong MySQL

```sql
INT
```

Trong RAM

```
00010100
```

Ba tầng khác nhau.

Đều biểu diễn cùng một thông tin.

---

# Bài toán ngày càng lớn

Khoảng 5.000 năm trước.

Các quốc gia xuất hiện.

Họ phải lưu:

- dân số
    
- thuế
    
- mùa màng
    
- quân đội
    
- thương mại
    

Lượng dữ liệu tăng theo cấp số nhân.

Một người không thể nhớ hết.

Một quyển sách cũng không đủ.

---

# Đây là lúc xuất hiện nhu cầu mới

Không chỉ lưu.

Mà còn:

- tìm kiếm
    
- sửa
    
- thống kê
    
- cộng
    
- trừ
    

Ví dụ

Một kho có:

```
Lúa:

10.251 bao
```

Ngày mai nhập

```
+251
```

Ngày kia bán

```
-36
```

Nếu mỗi lần đều đếm lại từ đầu.

Chi phí quá lớn.

Con người cần một cách tính toán.

---

# Toán học bắt đầu trở thành công cụ xử lý dữ liệu

Ban đầu

```
1 + 1
```

Sau đó

```
1000 + 500
```

Sau đó

```
Tính thuế

Tính diện tích

Tính lãi

Tính thời gian
```

Toán học không chỉ để nghiên cứu.

Nó trở thành công cụ xử lý thông tin.

Điều này cực kỳ quan trọng.

Computer Science không sinh ra từ lập trình.

Nó sinh ra từ nhu cầu xử lý dữ liệu.

---

# Nhưng con người lại gặp giới hạn

Giả sử một đế chế có

```
5 triệu dân.
```

Muốn tính thuế.

Một người tính tay mất:

```
5 phút/người
```

Tổng thời gian:

```
25 triệu phút
≈ 47 năm làm việc liên tục
```

Đây là một ví dụ minh họa cho thấy tính toán thủ công không thể mở rộng theo quy mô dữ liệu.

Con người bắt đầu tự hỏi:

> Có thể tạo một cỗ máy để tính thay mình không?

---

# Đây là mầm mống của máy tính

Lưu ý.

Ban đầu.

Không ai nghĩ tới:

- Windows
    
- Linux
    
- CPU
    
- Internet
    

Họ chỉ muốn giải quyết một bài toán rất đơn giản.

> **Làm sao để tính nhanh hơn con người?**

Từ câu hỏi đó.

Hàng loạt công cụ ra đời.

- Bàn tính (abacus)
    
- Thước tính (slide rule)
    
- Máy tính cơ học của Pascal
    
- Máy sai phân của Charles Babbage
    

Những thiết bị này chưa phải là máy tính hiện đại, nhưng chúng đánh dấu sự chuyển dịch từ **con người tính toán** sang **máy hỗ trợ tính toán**.

---

# Góc nhìn của một kỹ sư

Một kỹ sư phần mềm không nên nhìn máy tính như:

```
CPU

RAM

SSD

GPU
```

Đó chỉ là các thành phần.

Thay vào đó, hãy nhìn toàn bộ hệ thống như một chuỗi xử lý thông tin:

```
Thế giới thực
        │
        ▼
Thông tin
        │
        ▼
Biểu diễn thành dữ liệu
        │
        ▼
Lưu trữ
        │
        ▼
Tìm kiếm
        │
        ▼
Tính toán
        │
        ▼
Truyền tải
        │
        ▼
Hiển thị cho con người
```

Toàn bộ Khoa học Máy tính là quá trình tối ưu chuỗi này theo bốn tiêu chí:

- Biểu diễn **chính xác** (accuracy).
    
- Lưu trữ **ít tốn tài nguyên** (space efficiency).
    
- Xử lý **nhanh** (time efficiency).
    
- Truyền tải **đáng tin cậy** (reliability).
    

Mỗi công nghệ bạn sẽ học trong các chương tiếp theo—từ hệ nhị phân, Boolean, transistor, CPU, RAM, hệ điều hành, cơ sở dữ liệu, Unicode, mạng đến điện toán đám mây—đều là một lời giải cho ít nhất một trong bốn tiêu chí này.

---

# Liên hệ với công việc Backend Engineer

Ngay cả một API đơn giản cũng đang thực hiện toàn bộ chuỗi xử lý thông tin:

```
Người dùng nhập:

"Tôi tên là Vinh"

        │
        ▼
Frontend biểu diễn thành JSON

{
  "name": "Vinh"
}

        │
        ▼
HTTP truyền dữ liệu

        │
        ▼
Backend parse JSON

        │
        ▼
Object trong bộ nhớ

User.name = "Vinh"

        │
        ▼
Mã hóa chuỗi bằng UTF-8

        │
        ▼
Ghi xuống trang dữ liệu (page) của cơ sở dữ liệu

        │
        ▼
Lưu trên SSD dưới dạng bit (0 và 1)

        │
        ▼
Khi cần, dữ liệu được đọc ngược qua các tầng và hiển thị lại thành:

"Tôi tên là Vinh"
```

Ở mỗi bước đều có những quyết định thiết kế: chọn bảng mã nào, kiểu dữ liệu nào, định dạng truyền nào, cấu trúc lưu trữ nào, chiến lược lập chỉ mục nào. Các chương tiếp theo sẽ lần lượt giải thích vì sao những lựa chọn đó tồn tại và khi nào nên áp dụng chúng.

---

# Tổng kết chương

Chương này chưa nói về máy tính.

Nó trả lời một câu hỏi quan trọng hơn:

> **Vì sao máy tính phải tồn tại?**

Câu trả lời là:

> **Để giải quyết bài toán biểu diễn, lưu trữ, xử lý và truyền tải thông tin khi quy mô dữ liệu vượt quá khả năng của con người.**

Đó là sợi chỉ xuyên suốt toàn bộ series. Ở chương tiếp theo, từ câu hỏi "Máy lưu thông tin bằng cách nào?", chúng ta sẽ bắt đầu đi vào nền tảng vật lý của máy tính: vì sao cuối cùng mọi dữ liệu đều được quy về hai trạng thái và tại sao **hệ nhị phân** trở thành lựa chọn thống trị thay vì hệ thập phân hay các hệ đếm khác.