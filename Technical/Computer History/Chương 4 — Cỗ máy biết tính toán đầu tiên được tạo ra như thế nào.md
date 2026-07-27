
# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Vì sao con người muốn chế tạo máy tính.
    
- Máy tính cơ học hoạt động ra sao.
    
- Punch Card giải quyết vấn đề gì.
    
- Relay là gì.
    
- Vì sao Relay có thể thực hiện Boolean.
    
- Vì sao Relay thất bại.
    

---

# Chúng ta đang đứng ở đâu?

Chương trước kết thúc bằng:

```
0

1

AND

OR

NOT
```

Nhưng tất cả vẫn chỉ nằm trên giấy.

Giống như bạn biết công thức:

```
F = ma
```

không có nghĩa bạn đã chế tạo được ô tô.

Boolean cũng vậy.

Người ta biết:

```
1 AND 1 = 1
```

Nhưng...

> **Làm sao để một cỗ máy thực sự làm được phép tính đó?**

---

# Vấn đề

Con người vẫn đang tính bằng tay.

Ví dụ.

Một ngân hàng có:

```
50.000 giao dịch.
```

Một kế toán cần cộng:

```
1234

+453

+998

...

```

Hàng nghìn phép cộng.

Không ai muốn làm việc này cả đời.

Sai một con số.

Toàn bộ sổ sách sai.

---

Thế giới cần:

- nhanh hơn
    
- chính xác hơn
    
- ít phụ thuộc vào con người hơn
    

---

# Giải pháp đầu tiên

Không phải điện.

Không phải transistor.

Mà là...

Máy cơ học.

---

# Máy cơ học

Thế kỷ XVII.

Các nhà khoa học tự hỏi.

> Nếu bánh răng có thể quay.

Liệu nó có thể cộng số không?

---

Ví dụ.

Một bánh răng có:

```
10 răng
```

Đại diện:

```
0

1

2

...

9
```

Quay thêm một bước.

↓

```
3

↓

4
```

Khi quay hết vòng.

↓

Bánh răng kế tiếp quay.

Giống hệt phép nhớ.

```
19

↓

20
```

---

Đây chính là phép cộng cơ học.

Không cần điện.

Chỉ dùng:

- bánh răng
    
- trục
    
- tay quay
    

---

# Điều thú vị

Máy cộng cơ học thực sự làm phép cộng.

Không hề "biết" số.

Nó chỉ quay.

Giống như CPU ngày nay.

CPU cũng không biết:

```
2 + 2
```

có nghĩa gì.

Nó chỉ chuyển trạng thái điện.

---

# Nhưng vấn đề xuất hiện

Máy cơ học rất lớn.

Ví dụ.

Muốn cộng:

```
123456789
```

Cần rất nhiều bánh răng.

Muốn nhân.

↓

Thêm hàng trăm bánh răng.

Muốn chia.

↓

Còn phức tạp hơn.

---

Ma sát.

Bụi.

Mòn bánh răng.

Sai số.

Tốc độ.

Đều trở thành vấn đề.

---

Con người nhận ra.

> Cơ khí không thể mở rộng mãi.

---

# Một vấn đề khác

Giả sử có một bảng lương.

```
Nguyễn Văn A

8.500.000

```

Làm sao đưa dữ liệu vào máy?

Ngày nay.

Ta có:

- bàn phím
    
- file
    
- API
    

Lúc đó.

Không có gì cả.

---

# Punch Card xuất hiện

Ban đầu.

Punch Card không dùng cho máy tính.

Nó dùng cho...

Máy dệt.

Joseph Jacquard phát minh ra loại thẻ giấy có đục lỗ để điều khiển hoa văn trên vải.

Ý tưởng rất đơn giản:

```
Có lỗ

↓

Thực hiện
```

```
Không có lỗ

↓

Không thực hiện
```

Đó gần như là Binary.

---

Sau này.

Herman Hollerith nhận ra.

Có thể dùng chính ý tưởng đó để lưu dữ liệu điều tra dân số.

Ví dụ.

```
Nam

↓

Có lỗ
```

```
Nữ

↓

Không lỗ
```

---

Tuổi.

Nghề.

Địa chỉ.

Đều được mã hóa bằng các lỗ.

---

# Điều quan trọng

Punch Card là một trong những thiết bị đầu tiên tách:

> **Dữ liệu**

ra khỏi

> **Máy**

Đây là ý tưởng cực kỳ lớn.

Ngày nay.

Bạn có:

```
program.exe
```

và

```
data.csv
```

Tách riêng.

Ý tưởng này đã xuất hiện từ hơn một thế kỷ trước.

---

# Nhưng Punch Card không biết tính

Nó chỉ lưu.

Muốn tính.

Vẫn cần máy.

---

# Relay xuất hiện

Relay ban đầu được phát minh cho hệ thống điện báo và điện thoại.

Nó là một công tắc điều khiển bằng điện.

Nếu có dòng điện đi qua cuộn dây:

↓

Nam châm điện hút.

↓

Tiếp điểm đóng.

↓

Dòng điện đi tiếp.

Nếu mất điện:

↓

Lò xo kéo về.

↓

Tiếp điểm mở.

---

# Relay và Boolean

Đây là khoảnh khắc rất thú vị.

Relay chỉ có hai trạng thái.

```
Đóng
```

hoặc

```
Mở
```

Ta có thể quy ước.

```
Đóng

↓

1
```

```
Mở

↓

0
```

Lúc này.

Boolean trên giấy.

Đã trở thành thiết bị thật.

---

Ví dụ AND

Hai relay mắc nối tiếp.

```
Nguồn

↓

Relay A

↓

Relay B

↓

Bóng đèn
```

Chỉ khi:

```
A đóng

và

B đóng
```

Đèn mới sáng.

Đó là:

```
AND
```

---

OR

Hai relay mắc song song.

Chỉ cần một relay đóng.

↓

Có điện.

↓

Đèn sáng.

---

NOT

Relay thường đóng (Normally Closed - NC).

Có điện vào cuộn dây.

↓

Tiếp điểm mở.

↓

Đảo trạng thái.

---

Lần đầu tiên trong lịch sử.

Con người có thể tạo ra một cỗ máy.

Không chỉ lưu dữ liệu.

Mà còn:

- quyết định
    
- so sánh
    
- tính toán
    

Tất cả đều nhờ công tắc.

---

# Máy tính Relay

Các máy tính đầu tiên như **Harvard Mark I** sử dụng hàng nghìn relay.

Mỗi phép tính là hàng loạt relay đóng và mở.

Nếu cộng hai số.

Có thể cần hàng trăm relay hoạt động nối tiếp.

---

# Vấn đề mới

Relay có một nhược điểm chí mạng.

Nó là...

Máy cơ khí.

Bên trong vẫn có:

- lò xo
    
- tiếp điểm
    
- lõi sắt
    
- nam châm
    

Mỗi lần chuyển trạng thái.

Relay phải...

Di chuyển.

---

Điều này dẫn tới ba vấn đề lớn.

## 1. Chậm

Một relay mất khoảng vài mili giây để đóng hoặc mở.

Trong khi CPU hiện đại thực hiện hàng tỷ chu kỳ mỗi giây.

Khoảng cách này lên tới hàng triệu đến hàng tỷ lần.

---

## 2. Mòn

Relay có tiếp điểm kim loại.

Đóng.

Mở.

Đóng.

Mở.

Sau hàng triệu lần.

Tiếp điểm mòn.

Máy hỏng.

---

## 3. Ồn và tốn điện

Một phòng đầy relay sẽ phát ra tiếng "tách tách" liên tục.

Mỗi relay cũng cần năng lượng để kích hoạt cuộn dây.

Hàng nghìn relay đồng thời khiến hệ thống nóng, tiêu tốn điện và khó bảo trì.

---

# Góc nhìn của Backend Engineer

Tại sao chương này vẫn quan trọng với lập trình viên?

Vì nhiều khái niệm bạn dùng hằng ngày đã xuất hiện từ giai đoạn này.

Ví dụ:

- **Dữ liệu tách khỏi chương trình** → ngày nay là file cấu hình, database, object storage.
    
- **Đầu vào được mã hóa theo quy ước** → ngày nay là JSON, Protocol Buffers, Avro.
    
- **Logic được xây từ các công tắc đơn giản** → ngày nay là CPU thực thi các lệnh máy.
    
- **Mọi quyết định đều quy về các trạng thái nhị phân** → từ `if` trong Java đến mạch logic trong bộ xử lý.
    

Nhìn ở góc độ hệ thống, Backend Engineer đang làm việc ở tầng rất cao, nhưng cuối cùng mọi thao tác vẫn phải được dịch xuống những nguyên lý vật lý đã hình thành từ thời relay.

---

# Tổng kết chương

Chương này đánh dấu bước chuyển từ **ý tưởng** sang **máy móc**.

- Chương 2 trả lời: thông tin được biểu diễn bằng hai trạng thái.
    
- Chương 3 trả lời: hai trạng thái có thể xử lý bằng đại số Boolean.
    
- Chương 4 trả lời: relay là linh kiện đầu tiên biến Boolean thành một cỗ máy có thể tính toán.
    

Nhưng relay vẫn là một **công tắc cơ khí**. Nó quá chậm, quá lớn và không thể đáp ứng nhu cầu tính toán ngày càng tăng.

Điều đó dẫn đến cuộc cách mạng tiếp theo.

> **Nếu bỏ hoàn toàn các bộ phận chuyển động cơ khí, liệu có thể tạo ra một công tắc chỉ bằng điện tử?**

Đó sẽ là nội dung của **Chương 5 – Đèn điện tử (Vacuum Tube): bước đầu tiên của máy tính điện tử**, nơi máy tính lần đầu tiên thoát khỏi giới hạn của cơ khí để tiến vào kỷ nguyên điện tử.