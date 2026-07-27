

> **Chủ đề trung tâm**
> 
> _Máy tính không được thiết kế để hiểu số 0 và 1. Nó được thiết kế để phân biệt hai trạng thái vật lý ổn định._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Vì sao máy không thể lưu chữ như con người.
    
- Thông tin cuối cùng luôn phải được chuyển thành trạng thái vật lý.
    
- Vì sao nhị phân thắng các hệ đếm khác.
    
- Bit thực chất là gì.
    
- Byte từ đâu mà có.
    
- Máy hiện đại vẫn đang hoạt động dựa trên nguyên lý này.
    

---

# Chương trước chúng ta đã biết

Con người cần lưu thông tin.

Thông tin có thể là:

```
Tên

Tuổi

Ngày sinh

Tiền

Ảnh

Video

Âm thanh
```

Con người nhìn thấy

```
A
```

là hiểu.

Máy thì không.

Vậy câu hỏi là:

> **Máy nhìn thấy cái gì?**

---

# Máy không nhìn thấy chữ

Ví dụ

```
A
```

Đối với chúng ta

đó là chữ cái.

Đối với máy

không có khái niệm:

- A
    
- B
    
- C
    
- tiếng Việt
    
- tiếng Nhật
    

Máy chỉ có phần cứng.

Phần cứng chỉ cảm nhận được các hiện tượng vật lý.

Ví dụ

- có điện
    
- không điện
    

hoặc

- điện áp cao
    
- điện áp thấp
    

hoặc

- có từ trường
    
- không có từ trường
    

hoặc

- có điện tích
    
- không có điện tích
    

Máy không hiểu "A".

Máy chỉ đo trạng thái của vật chất.

---

# Vấn đề đầu tiên

Làm sao lưu được thông tin?

Giả sử muốn lưu

```
A
```

Con người có thể viết

```
A
```

lên giấy.

CPU thì không.

Nó cần một thứ có thể tồn tại lâu dài.

Ví dụ

```
Có điện
```

↓

```
1
```

Không điện

↓

```
0
```

Nhưng ngay lập tức xuất hiện câu hỏi.

---

# Tại sao phải dùng điện?

Thực ra...

Không nhất thiết.

Trong lịch sử người ta từng dùng:

- bánh răng
    
- lò xo
    
- relay
    
- từ trường
    
- điện
    
- ánh sáng
    
- điện tích
    

Ví dụ

Máy cơ học.

Một bánh răng quay sang trái.

↓

```
0
```

Quay sang phải.

↓

```
1
```

Hoặc

Nam châm

Có từ

↓

```
1
```

Không từ

↓

```
0
```

Điều quan trọng không phải là điện.

Điều quan trọng là:

> **Phải có các trạng thái có thể phân biệt rõ ràng.**

---

# Thông tin luôn phải có vật mang

Đây là nguyên lý rất quan trọng.

Thông tin không thể tồn tại một mình.

Nó luôn phải được lưu trên một môi trường vật lý.

Ví dụ

|Thông tin|Vật mang|
|---|---|
|Chữ viết|Giấy|
|Âm thanh|Sóng không khí|
|Video|Điện tích trên SSD|
|Ảnh|Các bit trong bộ nhớ|
|Database|Sector/Page trên ổ đĩa|

Đây gọi là:

> **Physical Representation (Biểu diễn vật lý của thông tin).**

Thông tin là khái niệm trừu tượng.

Dữ liệu là cách biểu diễn.

Và cuối cùng dữ liệu phải được ánh xạ xuống vật chất.

---

# Vậy dùng bao nhiêu trạng thái?

Đây là câu hỏi cực kỳ thú vị.

Giả sử tạo ra một máy có:

10 trạng thái.

Ví dụ

```
0

1

2

3

...

9
```

Mỗi trạng thái là một mức điện áp.

```
0V

1V

2V

...

9V
```

Nghe có vẻ rất hợp lý.

Một chữ số lưu được nhiều thông tin hơn.

Tại sao không làm?

---

# Vấn đề của thế giới thực

Điện không hoàn hảo.

Ví dụ

Muốn lưu

```
5V
```

Nhưng do nhiễu

điện áp trở thành

```
4.92V
```

hoặc

```
5.13V
```

CPU phải đoán.

Nếu đo sai.

Thông tin sai.

---

Nếu chỉ có

```
0V

5V
```

Khoảng cách rất lớn.

```
0.2V
```

vẫn là

```
0
```

```
4.8V
```

vẫn là

```
1
```

Sai số rất nhỏ.

Độ tin cậy rất cao.

---

# Đây là lý do nhị phân chiến thắng

Không phải vì toán học.

Không phải vì con người thích số 2.

Mà vì vật lý.

Hai trạng thái:

```
Có

Không
```

là hai trạng thái dễ chế tạo nhất.

Dễ đo nhất.

Ít lỗi nhất.

Đó là điều quan trọng nhất.

---

# Có phải chỉ tồn tại hai trạng thái?

Không.

Thực tế có rất nhiều hệ thống nhiều mức.

Ví dụ:

- Bộ nhớ flash lưu nhiều mức điện tích (MLC, TLC, QLC).
    
- Một số hệ thống truyền thông dùng nhiều mức điện áp.
    
- Analog computer xử lý tín hiệu liên tục.
    

Nhưng các hệ thống nhiều mức luôn phải đánh đổi:

- khó sản xuất hơn
    
- dễ nhiễu hơn
    
- mạch đọc phức tạp hơn
    
- khó mở rộng hơn
    

Khi công nghệ bán dẫn phát triển, hai trạng thái vẫn là lựa chọn tối ưu cho logic số.

---

# Từ trạng thái vật lý đến Bit

Bây giờ người ta đặt tên cho hai trạng thái này.

```
Có điện

↓

1

Không điện

↓

0
```

Mỗi trạng thái được gọi là

```
Binary Digit
```

Rút gọn thành

```
Bit
```

Bit là đơn vị nhỏ nhất của thông tin trong máy tính.

Một bit chỉ có hai khả năng.

```
0
```

hoặc

```
1
```

Không có giá trị ở giữa trong logic số.

---

# Một Bit lưu được gì?

Rất ít.

Ví dụ

```
Đèn bật?

Có

Không
```

Một bit là đủ.

```
1
```

↓

Có

```
0
```

↓

Không

---

Nếu muốn lưu

```
Màu sắc
```

Có bốn màu.

```
Đỏ

Xanh

Vàng

Đen
```

Một bit không đủ.

Ta cần hai bit.

```
00

01

10

11
```

Đủ bốn trạng thái.

---

# Hai bit, ba bit, tám bit...

Đây là quy luật tổng quát.

Nếu có **n bit**, số trạng thái biểu diễn được là:

[  
2^n  
]

Ví dụ:

|Số bit|Số trạng thái|
|---|--:|
|1|2|
|2|4|
|3|8|
|4|16|
|8|256|
|16|65.536|
|32|4.294.967.296|
|64|18.446.744.073.709.551.616|

Đây là công thức quan trọng đầu tiên trong toàn bộ Computer Science.

Nó sẽ xuất hiện ở mọi nơi:

- địa chỉ bộ nhớ
    
- màu sắc
    
- mã ký tự
    
- IPv4
    
- UUID
    
- khóa mã hóa
    
- kiểu dữ liệu số nguyên
    

---

# Byte xuất hiện như thế nào?

Những máy tính đầu tiên không thống nhất kích thước dữ liệu.

Có máy dùng:

- 6 bit
    
- 7 bit
    
- 9 bit
    
- 12 bit
    

Điều này gây khó khăn khi thiết kế phần cứng và phần mềm.

Dần dần, 8 bit trở thành lựa chọn phổ biến vì cân bằng giữa khả năng biểu diễn và chi phí phần cứng.

Người ta gọi nhóm 8 bit là:

```
Byte
```

Một byte biểu diễn được:

[  
2^8 = 256  
]

trạng thái.

Con số này sau này đủ để chứa một ký tự ASCII mở rộng, một giá trị màu, hoặc một số nguyên nhỏ.

---

# Góc nhìn của một kỹ sư

Khi bạn khai báo:

```java
boolean active = true;
```

Bạn nghĩ rằng máy lưu:

```
true
```

Thực tế, nó sẽ được biểu diễn thành một hoặc nhiều bit theo cách mà ngôn ngữ, trình biên dịch và CPU quy định.

Khi bạn lưu:

```sql
TINYINT
```

Hay

```sql
BOOLEAN
```

Hoặc

```json
true
```

Cuối cùng, tất cả đều phải được chuyển thành các bit và được lưu dưới dạng trạng thái vật lý trên RAM hoặc thiết bị lưu trữ.

Điều này dẫn đến một nguyên tắc xuyên suốt của Computer Science:

> **Mọi kiểu dữ liệu, mọi cấu trúc dữ liệu và mọi chương trình cuối cùng đều được biểu diễn bằng bit.**

---

# Chuẩn bị cho chương tiếp theo

Đến đây, chúng ta đã trả lời được:

- Máy không hiểu chữ.
    
- Máy chỉ phân biệt được trạng thái vật lý.
    
- Hai trạng thái là lựa chọn đáng tin cậy nhất.
    
- Thông tin được biểu diễn bằng bit.
    

Nhưng vẫn còn một câu hỏi rất lớn:

> **Nếu chỉ có 0 và 1, làm sao máy có thể cộng, trừ, so sánh, ra quyết định và chạy chương trình?**

Đó là điểm khởi đầu của **Chương 3 – Đại số Boolean và Logic số**, nơi toán học gặp phần cứng để tạo nên nền tảng của mọi CPU hiện đại.