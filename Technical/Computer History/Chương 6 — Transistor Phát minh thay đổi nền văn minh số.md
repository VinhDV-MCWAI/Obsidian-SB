

> **Chủ đề trung tâm**
> 
> _Máy tính chỉ thực sự bước vào kỷ nguyên hiện đại khi công tắc điện tử trở nên đủ nhỏ, đủ rẻ, đủ bền để có thể sản xuất hàng triệu chiếc._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Vì sao Vacuum Tube thất bại.
    
- Transistor là gì.
    
- Vì sao transistor nhỏ hơn hàng nghìn lần.
    
- Vì sao transistor tiêu thụ ít điện.
    
- MOSFET là gì.
    
- Vì sao CPU hiện đại chứa hàng chục tỷ transistor.
    
- Vì sao định luật Moore từng đúng trong nhiều thập kỷ.
    

---

# Chúng ta đang đứng ở đâu?

Cuối chương trước.

Máy tính đã dùng Vacuum Tube.

Nó nhanh.

Nó tính toán tốt.

Nhưng...

Nó có kích thước như một căn phòng.

---

ENIAC.

Khoảng:

- 17.000 Vacuum Tube
    
- 30 tấn
    
- tiêu thụ khoảng 150 kW
    

Giả sử muốn tăng tốc gấp 100 lần.

Có thể chỉ cần thêm nhiều bóng đèn hơn.

Nhưng.

Điều gì sẽ xảy ra?

Máy sẽ:

- lớn gấp nhiều lần
    
- nóng hơn
    
- tốn điện hơn
    
- hỏng nhiều hơn
    

Đây là ngõ cụt.

---

# Vấn đề

Con người không cần một máy tính lớn hơn.

Họ cần:

- nhỏ hơn
    
- rẻ hơn
    
- nhanh hơn
    
- đáng tin cậy hơn
    

Nói cách khác.

Họ cần một loại công tắc mới.

---

# Bell Labs

Năm 1947.

Tại Bell Labs.

Ba nhà khoa học:

- John Bardeen
    
- Walter Brattain
    
- William Shockley
    

đã tạo ra transistor đầu tiên.

Lúc đó.

Họ không nghĩ mình vừa mở đầu cuộc cách mạng số.

Mục tiêu ban đầu chỉ là:

> **Tìm một linh kiện thay thế Vacuum Tube.**

---

# Điều gì đặc biệt ở transistor?

Hãy nhớ Relay.

```id="5tpg7i"
Có điện

↓

Nam châm hút

↓

Tiếp điểm đóng
```

Vacuum Tube.

```id="dbd2hs"
Có điện

↓

Electron bay trong chân không
```

Transistor.

```id="7qmt5l"
Có điện

↓

Điều khiển dòng điện

↓

Không cần chân không

Không cần nung nóng

Không cần bộ phận chuyển động
```

---

# Một công tắc bằng chất rắn

Đây là ý tưởng thiên tài.

Thay vì dùng:

- lò xo
    
- nam châm
    
- bóng thủy tinh
    

Người ta dùng...

Một miếng vật liệu bán dẫn.

Thường là:

```id="iwd2pj"
Silicon
```

Silicon không chỉ dẫn điện.

Không chỉ cách điện.

Nó nằm ở giữa.

Đó là lý do gọi là:

> **Semiconductor (chất bán dẫn).**

---

# Chất bán dẫn là gì?

Đây là điểm nhiều người hiểu sai.

Không phải:

> "Nó dẫn điện một nửa."

Mà là:

> **Khả năng dẫn điện của nó có thể được điều khiển.**

Ví dụ.

Đồng.

↓

Luôn dẫn điện.

Nhựa.

↓

Luôn cách điện.

Silicon.

↓

Có thể chuyển đổi giữa hai trạng thái.

Đó chính là điều ta cần để xây dựng công tắc.

---

# Transistor hoạt động như thế nào?

Không đi sâu vào cơ học lượng tử.

Hãy hình dung.

Transistor giống như một van nước.

```text
Nguồn nước
     │
     ▼
   [Van]
     │
     ▼
 Đầu ra
```

Van nhỏ.

↓

Điều khiển dòng nước lớn.

---

Transistor cũng vậy.

Một tín hiệu rất nhỏ.

↓

Điều khiển một dòng điện lớn hơn.

```text
Tín hiệu điều khiển
         │
         ▼
     Transistor
         │
         ▼
    Dòng điện chính
```

Nó vừa là:

- công tắc
    
- bộ khuếch đại
    

---

# Boolean quay trở lại

Chúng ta đã gặp điều này ba lần.

Relay.

```id="fd4i1x"
Đóng

Mở
```

Vacuum Tube.

```id="ghpl7w"
Có dòng

Không dòng
```

Transistor.

```id="bhg78b"
Dẫn

Không dẫn
```

Boolean vẫn không thay đổi.

Đó là sức mạnh của việc phân tầng trong kỹ thuật.

---

# Tại sao transistor tốt hơn?

## 1. Rất nhỏ

Một Vacuum Tube.

↓

Vài centimet.

Một transistor.

↓

Ban đầu vài milimet.

Ngày nay.

Chỉ vài nanomet.

Nhờ đó.

Một CPU có thể chứa:

```text
Hàng chục tỷ transistor.
```

---

## 2. Ít tốn điện

Vacuum Tube phải nung nóng.

Transistor thì không.

Điện năng giảm rất mạnh.

Đây là lý do laptop có thể chạy bằng pin.

Nếu vẫn dùng Vacuum Tube.

Điều đó gần như bất khả thi.

---

## 3. Ít sinh nhiệt

Ít điện hơn.

↓

Ít nhiệt hơn.

↓

Máy ổn định hơn.

↓

Tuổi thọ cao hơn.

---

## 4. Bền

Không có:

- dây tóc
    
- chân không
    
- bóng thủy tinh
    

Nên transistor bền hơn rất nhiều.

---

## 5. Dễ sản xuất hàng loạt

Đây mới là cuộc cách mạng thật sự.

Vacuum Tube.

↓

Lắp từng bóng.

Transistor.

↓

In hàng triệu cái lên cùng một tấm silicon.

Đó là điều chưa từng có.

---

# MOSFET

Transistor đầu tiên chưa phải loại dùng ngày nay.

CPU hiện đại chủ yếu sử dụng:

> **MOSFET**
> 
> _Metal-Oxide-Semiconductor Field-Effect Transistor._

Không cần nhớ tên đầy đủ ngay.

Điều quan trọng là hiểu.

MOSFET giúp transistor:

- nhỏ hơn
    
- tiêu thụ điện ít hơn
    
- đóng mở nhanh hơn
    
- dễ tích hợp với số lượng cực lớn
    

Ngày nay, gần như mọi CPU, RAM và chip logic đều được xây dựng chủ yếu từ MOSFET.

---

# Một CPU thực chất là gì?

Đây là điều rất nhiều lập trình viên bất ngờ.

Bạn nhìn thấy:

```java
if(a>b)
```

Compiler.

↓

Assembly.

↓

Machine Code.

↓

CPU.

CPU không nhìn thấy Java.

CPU chỉ thấy...

Hàng tỷ transistor.

Mỗi transistor.

Chỉ biết:

```text
ON

OFF
```

Khi hàng tỷ transistor phối hợp với nhau.

Chúng tạo thành:

- cổng logic
    
- bộ cộng
    
- thanh ghi
    
- ALU
    
- cache
    
- bộ điều khiển
    

Cuối cùng.

Bạn có một CPU.

Một CPU không phải là "một linh kiện thông minh".

Nó là **một hệ thống khổng lồ gồm hàng tỷ công tắc cực nhỏ**.

---

# Định luật Moore

Năm 1965.

Gordon Moore quan sát rằng.

Số transistor trên một chip có xu hướng tăng gấp đôi sau khoảng 18–24 tháng, trong khi chi phí trên mỗi transistor giảm.

Điều này không phải là định luật vật lý.

Đó là một quan sát về tốc độ phát triển của ngành bán dẫn.

Trong nhiều thập kỷ.

Quan sát này gần đúng.

Kết quả là.

Máy tính ngày càng:

- nhanh hơn
    
- nhỏ hơn
    
- rẻ hơn
    

Mà người dùng gần như không phải làm gì.

---

# Nhưng Moore's Law không kéo dài mãi

Khi transistor chỉ còn vài nanomet.

Các vấn đề vật lý xuất hiện.

Ví dụ.

- electron rò rỉ
    
- tản nhiệt
    
- hiệu ứng lượng tử
    
- chi phí sản xuất tăng mạnh
    

Do đó, tốc độ thu nhỏ transistor hiện nay đã chậm hơn đáng kể so với giai đoạn trước.

Ngành công nghiệp chuyển sang tối ưu theo nhiều hướng khác:

- nhiều nhân CPU
    
- cache lớn hơn
    
- GPU
    
- chip chuyên dụng (NPU, TPU)
    
- kiến trúc mới
    

---

# Góc nhìn của Backend Engineer

Tại sao bạn cần biết transistor?

Vì rất nhiều quyết định phần mềm cuối cùng đều chịu ảnh hưởng bởi phần cứng.

Ví dụ:

Bạn tối ưu một thuật toán từ:

```text
O(n²)

↓

O(n log n)
```

Kết quả cuối cùng là:

CPU phải kích hoạt ít transistor hơn.

---

Bạn giảm số lần truy cập RAM.

↓

CPU ít phải chờ dữ liệu hơn.

↓

Hàng tỷ transistor trong CPU được sử dụng hiệu quả hơn.

---

Bạn dùng SIMD, AVX hoặc GPU.

↓

Bạn đang khai thác những cách tổ chức transistor khác nhau để xử lý dữ liệu song song.

Nói cách khác.

Tối ưu phần mềm không chỉ là chuyện của code.

Nó luôn có tác động trực tiếp đến cách phần cứng hoạt động.

---

# Tổng kết chương

Transistor không thay đổi toán học.

Không thay đổi Boolean.

Không thay đổi Binary.

Nó thay đổi **khả năng hiện thực hóa** những ý tưởng đó ở quy mô chưa từng có.

Đó là lý do transistor được xem là nền móng của toàn bộ ngành công nghiệp điện tử hiện đại.

---

# Chuẩn bị cho chương tiếp theo

Đến đây chúng ta đã có:

- Binary → cách biểu diễn thông tin.
    
- Boolean → cách xử lý logic.
    
- Transistor → công tắc điện tử thực hiện logic.
    

Nhưng vẫn còn một vấn đề lớn.

Giả sử bạn có **10 triệu transistor**.

Làm sao nối từng chiếc bằng dây?

Nếu mỗi transistor là một linh kiện riêng.

Bạn sẽ có một "mớ dây" khổng lồ không thể lắp ráp hay bảo trì.

Đó là lý do chương tiếp theo sẽ nói về phát minh đưa máy tính từ hàng nghìn linh kiện rời rạc sang **Integrated Circuit (IC)**, nơi hàng triệu rồi hàng tỷ transistor được tích hợp trên **một con chip duy nhất**. Đây chính là nền tảng trực tiếp của CPU, RAM và các bộ vi xử lý hiện đại.