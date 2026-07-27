
> **Chủ đề trung tâm**
> 
> _Để máy tính nhanh hơn, con người phải loại bỏ mọi bộ phận cơ khí khỏi quá trình tính toán._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Vì sao relay là ngõ cụt của máy tính.
    
- Đèn điện tử (Vacuum Tube) là gì.
    
- Vì sao nó nhanh hơn relay hàng nghìn lần.
    
- ENIAC hoạt động như thế nào.
    
- Vì sao đèn điện tử cũng thất bại.
    
- Vì sao transistor là bước tiến tất yếu.
    

---

# Kết thúc chương trước

Chúng ta đã có:

```
Boolean
        ↓
Relay
        ↓
Máy biết tính toán
```

Nghe có vẻ hoàn hảo.

Nhưng rồi thế giới bước vào Thế chiến II.

---

# Nhu cầu mới

Trong chiến tranh, con người cần tính toán rất nhiều.

Ví dụ:

- đường bay
    
- quỹ đạo pháo
    
- radar
    
- mã hóa
    
- giải mã
    
- bảng đạn đạo
    

Một phép tính trước đây mất vài giờ.

Giờ phải hoàn thành trong vài giây.

Nếu chậm.

Có thể mất cả trận chiến.

---

# Relay trở thành nút thắt

Hãy nhớ relay hoạt động thế nào.

```
Có điện

↓

Nam châm hút

↓

Lõi sắt di chuyển

↓

Tiếp điểm chạm

↓

Có điện
```

Có một điều rất quan trọng.

Relay luôn phải...

**Di chuyển.**

Dù chỉ vài milimet.

Nó vẫn cần thời gian.

Ví dụ.

Một relay cần khoảng:

```
5 ms
```

để đóng.

Một giây.

Chỉ đóng mở khoảng:

```
200 lần
```

---

# Hãy thử tưởng tượng

Giả sử muốn cộng hai số.

Cần:

```
200 relay
```

hoạt động liên tiếp.

Mỗi relay:

```
5 ms
```

↓

Một phép tính có thể mất gần một giây.

Ngày nay.

CPU thực hiện hàng tỷ phép tính trong khoảng thời gian đó.

Khoảng cách gần như không thể tưởng tượng.

---

# Vấn đề không nằm ở toán học

Boolean vẫn đúng.

Logic vẫn đúng.

Thuật toán vẫn đúng.

Vấn đề nằm ở...

**Vật lý.**

Cơ khí không thể chuyển động nhanh vô hạn.

Đây là giới hạn đầu tiên của Computer Engineering.

---

# Ý tưởng mới

Một số nhà khoa học đặt câu hỏi.

> Có thể tạo một công tắc mà không cần bất kỳ bộ phận nào chuyển động không?

Nếu được.

Máy sẽ nhanh hơn rất nhiều.

---

# Đèn điện tử (Vacuum Tube)

Đèn điện tử ban đầu không được tạo ra để làm máy tính.

Nó được dùng trong:

- radio
    
- khuếch đại tín hiệu
    
- truyền thông
    

Sau đó người ta phát hiện.

Có thể dùng nó như một...

**Công tắc điện tử.**

---

# Vacuum Tube hoạt động như thế nào?

Không đi quá sâu vật lý.

Hãy tưởng tượng.

Bên trong là một ống thủy tinh gần như hút hết không khí.

Có ba thành phần chính.

```
Cathode

↓

Grid

↓

Anode
```

Cathode được nung nóng.

↓

Giải phóng electron.

Electron muốn bay sang Anode.

Nhưng.

Ở giữa có Grid.

Grid giống như...

Một cánh cổng.

Nếu Grid cho phép.

↓

Electron đi qua.

↓

Có dòng điện.

Nếu Grid chặn.

↓

Electron không qua.

↓

Không có dòng điện.

---

# Điều quan trọng

Không có:

- bánh răng
    
- lò xo
    
- nam châm
    
- tiếp điểm
    

Không có gì phải di chuyển.

Chỉ có electron.

Electron gần như di chuyển với tốc độ cực lớn.

Đây là lý do.

Vacuum Tube nhanh hơn relay rất nhiều.

---

# Một lần nữa xuất hiện Binary

Relay.

```
Mở

Đóng
```

Vacuum Tube.

```
Có dòng điện

Không có dòng điện
```

Lại là hai trạng thái.

Boolean vẫn hoạt động.

Không cần thay đổi toán học.

Chỉ thay đổi phần cứng.

Đây là một bài học rất lớn trong Computer Science.

> **Một tầng trừu tượng tốt có thể tồn tại qua nhiều thế hệ công nghệ.**

Boolean không hề thay đổi.

Chỉ phần cứng thay đổi.

Điều này sau này cũng xảy ra với:

- TCP/IP chạy trên cáp đồng, cáp quang, Wi-Fi.
    
- SQL chạy trên HDD, SSD, NVMe.
    
- Java chạy trên x86 và ARM.
    

Các lớp bên trên được giữ ổn định, trong khi lớp bên dưới liên tục được cải tiến.

---

# ENIAC

Năm 1945.

ENIAC ra đời.

Đây là một trong những máy tính điện tử đa dụng đầu tiên.

Thông số của ENIAC cho thấy quy mô khổng lồ của công nghệ thời đó:

- khoảng **17.000** đèn điện tử
    
- nặng khoảng **30 tấn**
    
- chiếm diện tích một căn phòng lớn
    
- tiêu thụ khoảng **150 kW** điện
    

Đổi lại.

Nó nhanh hơn các máy relay nhiều bậc.

Những bài toán trước đây cần hàng giờ có thể hoàn thành trong vài chục giây hoặc vài phút.

---

# Nhưng ENIAC vẫn chưa giống máy tính hiện đại

Đây là điều nhiều người hiểu nhầm.

Ngày nay.

Muốn đổi chương trình.

Bạn chỉ cần:

```java
Run
```

ENIAC thì không.

Muốn đổi thuật toán.

Kỹ sư phải:

- tháo dây
    
- cắm dây mới
    
- đổi công tắc
    
- cấu hình lại toàn bộ máy
    

Lập trình khi đó giống...

Lắp ráp phần cứng.

Chứ chưa phải viết code.

---

# Đèn điện tử giải quyết được gì?

So với relay.

Vacuum Tube:

- nhanh hơn rất nhiều
    
- không bị mòn tiếp điểm
    
- không phát ra tiếng kêu cơ khí
    
- cho phép xây dựng máy tính quy mô lớn hơn
    

Nhưng.

Nó lại tạo ra những vấn đề hoàn toàn mới.

---

# Vấn đề số 1 — Nóng

Cathode phải được nung nóng liên tục.

Hàng nghìn bóng đèn.

↓

Sinh nhiệt rất lớn.

Phòng máy cần hệ thống làm mát.

---

# Vấn đề số 2 — Tốn điện

Một bóng đèn tiêu thụ không nhiều.

Nhưng.

17.000 bóng.

↓

Tiêu thụ lượng điện khổng lồ.

Chi phí vận hành rất cao.

---

# Vấn đề số 3 — Hay hỏng

Đèn điện tử có tuổi thọ hữu hạn.

Trong hàng chục nghìn bóng.

Mỗi ngày gần như luôn có bóng bị hỏng.

Kỹ sư phải liên tục kiểm tra và thay thế.

Độ tin cậy trở thành bài toán lớn.

---

# Vấn đề số 4 — Quá lớn

Một bóng đèn có kích thước vài centimet.

Muốn tăng tốc.

↓

Thêm bóng.

↓

Máy càng lớn.

Không thể đặt một triệu bóng trong một căn phòng một cách hiệu quả.

---

# Một bài học kỹ thuật

Đây là lần đầu tiên lịch sử máy tính xuất hiện một quy luật quen thuộc.

Một công nghệ mới giải quyết vấn đề cũ.

Nhưng đồng thời sinh ra vấn đề mới.

|Thế hệ|Giải quyết|Tạo ra|
|---|---|---|
|Relay|Tự động hóa logic|Quá chậm|
|Vacuum Tube|Tăng tốc xử lý|Nóng, tốn điện, kích thước lớn|

Đây là một quy luật sẽ lặp lại trong toàn bộ series.

Không có công nghệ nào hoàn hảo.

Chỉ có công nghệ phù hợp với giới hạn của thời đại.

---

# Góc nhìn của Backend Engineer

Tại sao bạn cần biết điều này?

Vì nhiều nguyên tắc kiến trúc phần mềm hiện đại phản ánh đúng quy luật của phần cứng.

Ví dụ:

Một hệ thống cache.

Giải quyết:

- giảm truy vấn database.
    

Nhưng tạo ra:

- dữ liệu cũ (stale data)
    
- cache invalidation
    
- tăng độ phức tạp.
    

Microservices.

Giải quyết:

- chia nhỏ hệ thống.
    

Nhưng tạo ra:

- giao tiếp mạng
    
- distributed transaction
    
- quan sát hệ thống khó hơn.
    

Giống hệt Vacuum Tube.

Mỗi giải pháp đều mang theo chi phí.

Một kỹ sư giỏi không hỏi:

> "Công nghệ nào tốt nhất?"

Mà hỏi:

> "Công nghệ này giải quyết vấn đề gì, và đổi lại mình phải trả giá bằng điều gì?"

Đó chính là tư duy **trade-off**.

---

# Tổng kết chương

Relay thất bại vì bị giới hạn bởi cơ khí.

Vacuum Tube thay thế relay bằng một công tắc điện tử, giúp máy tính tăng tốc vượt bậc và mở ra kỷ nguyên máy tính điện tử.

Tuy nhiên, hàng chục nghìn bóng đèn điện tử khiến máy:

- quá nóng
    
- quá tốn điện
    
- quá lớn
    
- khó bảo trì
    

Thế giới lại đứng trước một câu hỏi mới:

> **Có thể tạo ra một công tắc điện tử nhỏ hơn, bền hơn, ít tiêu thụ điện hơn và có thể sản xuất với số lượng hàng triệu hay không?**

Đó là tiền đề cho phát minh quan trọng nhất trong lịch sử phần cứng hiện đại:

**Transistor**. Nhiều nhà sử học công nghệ coi đây là phát minh có tác động lớn nhất đến sự phát triển của máy tính trong thế kỷ XX. Chương tiếp theo sẽ giải thích vì sao chỉ một linh kiện nhỏ bé lại có thể thay đổi toàn bộ nền công nghiệp điện tử và mở đường cho CPU hiện đại.