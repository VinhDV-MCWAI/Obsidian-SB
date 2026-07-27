Nếu ở chương trước ta đã thấy rằng RAM chỉ là không gian lưu các dãy byte có địa chỉ, thì bước tiếp theo là câu hỏi quan trọng hơn:

> **Một dãy bit trong bộ nhớ thực sự có nghĩa là gì?**

Máy tính không tự biết.

Cùng một chuỗi bit có thể là:

- một số nguyên,
    
- một ký tự,
    
- một địa chỉ,
    
- một phần của ảnh,
    
- một phần của lệnh máy,
    
- hoặc một giá trị logic.
    

Nghĩa của nó phụ thuộc vào **quy ước diễn giải**.

Trong chương này, ta chỉ tập trung vào một trong những quy ước nền tảng nhất: **biểu diễn số**.

---

# 1. Vì sao phải biểu diễn số?

Con người muốn máy tính làm các việc như:

- cộng,
    
- trừ,
    
- đếm,
    
- so sánh,
    
- tính toán tài chính,
    
- tính thời gian,
    
- xử lý ID,
    
- làm việc với địa chỉ bộ nhớ,
    
- xử lý cảm biến.
    

Tất cả đều cần số.

Nhưng máy không đọc được “3”, “-7”, “1024” theo nghĩa trực giác của con người.

Máy chỉ hiểu trạng thái vật lý của bit:

- 0
    
- 1
    

Vì vậy, người ta phải tạo ra một quy ước để chuyển số sang bit và ngược lại.

Đó là phần nền của toàn bộ tính toán số học trong máy tính.

---

# 2. Số không dấu: unsigned

## Ý tưởng

Nếu chỉ cần biểu diễn các số không âm, cách làm đơn giản nhất là:

- bit càng nhiều thì biểu diễn được số càng lớn,
    
- toàn bộ tổ hợp bit đều dành cho giá trị dương và 0.
    

Đây là dạng **unsigned**.

Ví dụ với 3 bit:

- `000` = 0
    
- `001` = 1
    
- `010` = 2
    
- `011` = 3
    
- `100` = 4
    
- `101` = 5
    
- `110` = 6
    
- `111` = 7
    

Với `n` bit, số lượng giá trị biểu diễn được là:

> **2^n**

Và miền giá trị là:

> **0 đến 2^n - 1**

## Ưu điểm

- đơn giản
    
- trực quan
    
- dễ cộng trừ hơn ở mức logic cơ bản
    
- tận dụng toàn bộ miền giá trị cho số không âm
    

## Hạn chế

Không biểu diễn được số âm.

Điều đó không đủ cho rất nhiều bài toán thực tế.

---

# 3. Khi cần số âm: vấn đề xuất hiện

Con người không chỉ đếm “1, 2, 3”.

Ta còn cần:

- nhiệt độ âm
    
- lỗ vốn
    
- độ lệch
    
- hướng đi hai chiều
    
- trừ trong số học
    
- chênh lệch
    
- tọa độ
    
- vector
    
- trạng thái tăng/giảm
    

Vì vậy, biểu diễn số phải mở rộng để chứa cả âm và dương.

Đây là lúc các cách biểu diễn signed xuất hiện.

---

# 4. Những cách biểu diễn số âm ban đầu

Lịch sử từng thử nhiều cách:

## Sign-magnitude

- một bit để chỉ dấu
    
- các bit còn lại lưu độ lớn
    

Ví dụ:

- `0 101` = +5
    
- `1 101` = -5
    

### Vấn đề

Có hai cách biểu diễn cho số 0:

- +0
    
- -0
    

Điều này làm logic so sánh, cộng trừ, và thiết kế mạch phức tạp hơn.

---

## One’s complement

Số âm được tạo bằng cách đảo bit của số dương.

Ví dụ với 4 bit:

- +5 = `0101`
    
- -5 = `1010`
    

### Vấn đề

Cũng có hai số 0:

- `0000`
    
- `1111`
    

Không thuận tiện cho tính toán số học và thiết kế phần cứng.

---

# 5. Two’s complement: giải pháp thắng thế

Cuối cùng, hệ thống máy tính hiện đại gần như thống nhất dùng **two’s complement** để biểu diễn số nguyên có dấu.

Đây là một trong những ý tưởng quan trọng nhất trong toàn bộ biểu diễn số.

## Cách tạo số âm

Muốn lấy số âm của một số dương:

1. đảo bit
    
2. cộng thêm 1
    

Ví dụ với 4 bit:

- +5 = `0101`
    
- đảo bit → `1010`
    
- cộng 1 → `1011`
    

Vậy `1011` là -5.

## Miền giá trị

Với `n` bit, two’s complement biểu diễn được:

> **-2^(n-1) đến 2^(n-1) - 1**

Ví dụ với 8 bit:

- nhỏ nhất: -128
    
- lớn nhất: 127
    

## Vì sao two’s complement thắng?

Vì nó giải quyết được nhiều vấn đề cùng lúc:

- chỉ có một số 0
    
- phép cộng và trừ có thể dùng cùng mạch
    
- phần cứng đơn giản hơn
    
- logic so sánh thuận tiện hơn
    
- tận dụng hiệu quả số bit
    

Đây là một ví dụ điển hình của kỹ thuật máy tính:

> Giải pháp tốt không phải là giải pháp “đẹp nhất về mặt trực giác”, mà là giải pháp cân bằng nhất giữa biểu diễn, phần cứng và tính toán.

---

# 6. Cộng trừ trong máy tính thực sự diễn ra thế nào?

CPU không “biết” cộng theo kiểu con người học ở tiểu học.

CPU thực hiện cộng bằng mạch số học trên bit.

## Cộng unsigned

Ví dụ:

```
  0101
+ 0011
= 1000
```

Đây là cộng nhị phân cơ bản.

## Cộng signed trong two’s complement

Điểm hay của two’s complement là:

> CPU vẫn dùng cùng một cơ chế cộng bit như trên.

Ví dụ:

- +5 = `0101`
    
- -3 = `1101`
    

Cộng lại:

```
  0101
+ 1101
= 0010
```

Kết quả là 2.

Tức là cùng một mạch cộng có thể xử lý cả số âm và số dương, miễn là ta diễn giải đúng bit.

Đây là lý do two’s complement cực kỳ quan trọng trong kiến trúc máy tính.

---

# 7. Overflow: khi kết quả vượt quá miền biểu diễn

## Định nghĩa

Overflow xảy ra khi kết quả toán học thật sự **không thể biểu diễn** bằng số bit hiện có.

Ví dụ với 8 bit unsigned:

- lớn nhất là 255
    

Nếu cộng thêm 1:

- 255 + 1 = 256
    

Nhưng 256 không còn vừa trong 8 bit.

Kết quả bị tràn.

## Hiện tượng xảy ra

Khi vượt giới hạn, bit “rơi ra ngoài” phạm vi lưu trữ.

Máy không tự nhiên dừng lại và cảnh báo như con người mong muốn. Tùy ngôn ngữ, hệ điều hành, CPU và kiểu dữ liệu, kết quả có thể:

- bị cắt cụt,
    
- quấn vòng,
    
- hoặc tạo lỗi runtime.
    

## Tại sao nguy hiểm?

Overflow thường gây ra lỗi rất nặng:

- sai tính toán tiền tệ,
    
- sai số lượng,
    
- sai timestamp,
    
- sai kích thước buffer,
    
- sai logic bảo mật,
    
- lỗi phân bổ bộ nhớ.
    

Trong kỹ thuật hệ thống, overflow là một trong những nguồn lỗi kinh điển.

---

# 8. Underflow: khi giá trị nhỏ hơn giới hạn

Underflow là tình huống ngược lại:

> giá trị nhỏ hơn miền có thể biểu diễn.

Với số nguyên signed, nếu đi dưới ngưỡng nhỏ nhất thì sẽ tràn theo cách tương ứng.

Trong số thực, underflow còn có nghĩa riêng khác: giá trị quá nhỏ đến mức hệ thống không biểu diễn chính xác được nữa và bị làm tròn về 0 hoặc giá trị cực nhỏ gần 0.

Trong chương này, ta chủ yếu hiểu underflow ở mức nguyên lý:

- vượt giới hạn dưới,
    
- mất đúng giá trị,
    
- kết quả sai so với toán học thực.
    

---

# 9. Vì sao máy tính không dùng số vô hạn?

Nghe có vẻ hiển nhiên, nhưng đây là câu hỏi nền tảng.

Lý do là:

- phần cứng hữu hạn,
    
- bộ nhớ hữu hạn,
    
- thanh ghi hữu hạn,
    
- bus hữu hạn,
    
- dây dẫn hữu hạn,
    
- thời gian tính hữu hạn.
    

Máy tính là hệ thống vật lý, nên mọi thứ đều phải hữu hạn.

Vì vậy, biểu diễn số luôn có biên.

Và cứ khi nào có biên, sẽ có:

- giới hạn trên,
    
- giới hạn dưới,
    
- và lỗi khi vượt qua giới hạn đó.
    

Đây là nguồn gốc của rất nhiều bug thực tế.

---

# 10. Biểu diễn số trong CPU gắn với độ rộng bit

Số bit quyết định trực tiếp miền giá trị.

Ví dụ:

## 8 bit unsigned

- 0 → 255
    

## 16 bit unsigned

- 0 → 65.535
    

## 32 bit unsigned

- 0 → 4.294.967.295
    

## 64 bit unsigned

- 0 → rất lớn
    

Tương tự với signed thì miền sẽ lệch sang âm.

Điều này giải thích vì sao chọn kiểu dữ liệu không thể tùy ý.

Chọn sai kiểu có thể làm:

- lãng phí bộ nhớ,
    
- tràn số,
    
- hoặc sai logic.
    

---

# 11. Số nguyên trong phần mềm không chỉ là “số”

Trong phần mềm thực tế, số nguyên thường đại diện cho:

- ID
    
- số lượng
    
- độ dài
    
- trạng thái
    
- mã lỗi
    
- timestamp
    
- offset
    
- chỉ số mảng
    
- bitmask
    
- quyền truy cập
    

Nghĩa là một số nguyên không chỉ là “một con số”.

Nó là một dạng mã hóa ngữ nghĩa.

Ví dụ:

- `0` có thể là success
    
- `1` có thể là error
    
- `404` là not found
    
- `200` là OK
    
- `-1` có thể là sentinel value
    
- `0xffffffff` có thể là một giá trị đặc biệt
    

Do đó, hiểu biểu diễn số cũng là hiểu cách hệ thống “nghĩ” bằng dữ liệu.

---

# 12. So sánh signed và unsigned: sai chỗ là lỗi lớn

Trong thực tế lập trình, signed/unsigned là nguồn bug khá kinh điển.

Ví dụ:

- một biến đếm âm không nên dùng unsigned
    
- một chỉ số mảng âm không tồn tại
    
- so sánh giữa signed và unsigned có thể dẫn tới kết quả bất ngờ
    
- trừ hai giá trị unsigned có thể gây quấn vòng nếu không cẩn thận
    

Nói cách khác:

> Một giá trị số chỉ an toàn khi miền giá trị của nó khớp với ý nghĩa nghiệp vụ.

Nếu không khớp, bug sẽ xuất hiện ở rìa hệ thống, nơi rất khó dò.

---

# 13. Two’s complement và bit cao nhất

Trong two’s complement:

- bit cao nhất không chỉ là “một bit bình thường”
    
- nó mang ý nghĩa dấu trong cách diễn giải số có dấu
    

Với cùng một chuỗi bit, cách hiểu signed và unsigned có thể khác hoàn toàn.

Ví dụ 8 bit:

- `11111111`
    
    - unsigned = 255
        
    - signed = -1
        

Cùng một bit pattern, hai ý nghĩa khác nhau.

Đây là lý do khi debug hoặc đọc binary, bạn phải biết:

- đang nhìn dưới góc signed hay unsigned,
    
- dữ liệu này được parse theo kiểu nào,
    
- code path nào đã tạo ra nó.
    

---

# 14. Vì sao chương này quan trọng trước khi đi sang floating point?

Số nguyên là nền tảng.

Nếu chưa hiểu:

- bit
    
- byte
    
- signed
    
- unsigned
    
- overflow
    
- two’s complement
    

thì sang số thực sẽ rất dễ bị rối.

Vì floating point còn phức tạp hơn nhiều:

- mantissa
    
- exponent
    
- dấu
    
- chuẩn IEEE 754
    
- lỗi làm tròn
    
- sai số biểu diễn
    

Nói cách khác:

> Chương này là bước đệm bắt buộc để hiểu vì sao `0.1 + 0.2 != 0.3` trong máy tính.

---

# 15. Ứng dụng trực tiếp trong thực tế backend

Đây là phần bạn cần liên hệ trực tiếp với công việc.

## 1. Chọn kiểu số phù hợp trong database

Ví dụ:

- `TINYINT`
    
- `SMALLINT`
    
- `INT`
    
- `BIGINT`
    

Không nên chọn theo thói quen. Phải chọn theo miền giá trị thực tế.

Nếu ID có thể tăng rất lớn, dùng `INT` là rủi ro.

Nếu chỉ là trạng thái, dùng số lớn là lãng phí.

## 2. Kiểm soát overflow

Các phép tính trong hệ thống tiền tệ, thống kê, quota, traffic, hoặc billing phải kiểm soát giới hạn.

## 3. Tránh sai kiểu signed/unsigned giữa các tầng

Lỗi có thể xảy ra khi:

- DB lưu một kiểu
    
- backend parse sang kiểu khác
    
- frontend hiển thị theo kiểu khác
    
- API gateway xử lý khác
    

## 4. Hiểu giới hạn timestamp

Rất nhiều vấn đề thời gian thực chất là vấn đề biểu diễn số nguyên có giới hạn.

## 5. Hiểu phép trừ và chênh lệch

Các giá trị như:

- số bản ghi còn lại
    
- số giây còn lại
    
- số tiền chênh lệch
    
- số lượt vượt quota
    

đều có thể lỗi nếu chọn kiểu sai.

---

# 16. Kết luận của chương

Máy tính không làm việc với số theo kiểu “tự nhiên” như con người.

Máy tính chỉ làm việc với:

- bit,
    
- quy ước diễn giải,
    
- miền giá trị hữu hạn,
    
- và logic số học cụ thể.
    

Từ đó sinh ra:

- unsigned
    
- signed
    
- two’s complement
    
- overflow
    
- underflow
    

Đây không chỉ là kiến thức nền.

Đây là thứ quyết định hệ thống của bạn có đúng hay không ở tầng thấp nhất.

Ở chương tiếp theo, ta sẽ đi sâu vào nơi các số bắt đầu trở nên khó hơn rất nhiều:

> **số thực, dấu chấm động, IEEE 754, và vì sao phép tính tưởng như đơn giản lại sinh sai số trong máy tính**.

Nếu cần, tôi có thể viết tiếp ngay **Chương 13 — Floating Point và IEEE 754** theo đúng mạch này.