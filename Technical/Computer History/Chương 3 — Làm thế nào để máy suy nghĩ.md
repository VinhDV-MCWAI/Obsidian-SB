

> **Chủ đề trung tâm**
> 
> _Máy không biết suy nghĩ. Máy chỉ thực hiện các phép toán logic trên các trạng thái nhị phân._

---

# Mục tiêu chương

Sau chương này bạn sẽ hiểu:

- Vì sao chỉ có 0 và 1 vẫn đủ để xử lý mọi chương trình.
    
- Logic là gì.
    
- Đại số Boolean ra đời để giải quyết vấn đề gì.
    
- Các phép AND, OR, NOT, XOR thực chất là gì.
    
- Tại sao `if`, `while`, SQL `WHERE`, firewall, CPU và transistor đều dựa trên cùng một nền tảng toán học.
    

---

# Kết thúc chương trước

Chúng ta đã biết:

Máy chỉ có

```
0

1
```

Hay nói chính xác hơn

```
Có điện

Không điện
```

Nhưng chỉ lưu được dữ liệu là chưa đủ.

Con người cần máy thực hiện những yêu cầu như:

```
Nếu nhiệt độ > 35
→ Bật quạt
```

hoặc

```
Nếu còn hàng
→ Cho phép đặt mua
```

hoặc

```
Nếu username và password đúng
→ Đăng nhập
```

Máy phải biết đưa ra quyết định.

---

# Vấn đề

Máy không hiểu tiếng người.

Ví dụ

```
Nếu trời mưa thì mang ô.
```

Con người hiểu ngay.

Máy thì không.

Nó cần một thứ hoàn toàn khác.

Một hệ thống có thể mô tả:

- đúng
    
- sai
    

một cách tuyệt đối.

---

# Quan sát của George Boole

Vào giữa thế kỷ XIX, nhà toán học **George Boole** nhận ra một điều thú vị.

Nhiều lập luận của con người không phụ thuộc vào số học.

Ví dụ

```
Nếu:

A đúng

và

B đúng

↓

Kết quả đúng.
```

Hoặc

```
Nếu:

A sai

↓

Phủ định của A là đúng.
```

Ông đặt câu hỏi:

> **Có thể biến logic thành toán học không?**

---

# Đại số Boolean ra đời

Boole xây dựng một hệ toán học hoàn toàn mới.

Khác với toán thông thường.

Toán phổ thông xử lý:

```
1

2

3

4
```

Boolean chỉ có hai giá trị:

```
True

False
```

Sau này máy tính biểu diễn thành

```
1

0
```

Đây là bước kết nối đầu tiên giữa **toán học** và **điện tử**.

---

# Phép toán đầu tiên — NOT

Giả sử

```
Có điện

↓

1
```

NOT đơn giản là đảo trạng thái.

|Input|NOT|
|---|---|
|0|1|
|1|0|

Ví dụ

```
Đăng nhập?

Có

↓

NOT

↓

Không
```

---

Trong lập trình

```java
if (!isLogin)
```

chính là

```
NOT isLogin
```

---

# Phép toán thứ hai — AND

AND có nghĩa là

> **Tất cả điều kiện đều đúng.**

Ví dụ

```
Có tài khoản

VÀ

Có mật khẩu
```

↓

Được đăng nhập.

---

Bảng chân trị

|A|B|A AND B|
|---|---|---|
|0|0|0|
|0|1|0|
|1|0|0|
|1|1|1|

Chỉ có một trường hợp kết quả là 1.

---

Ví dụ Backend

```java
if(userExist && passwordCorrect)
```

Đó chính là phép AND.

---

SQL

```sql
WHERE age > 18
AND status='ACTIVE'
```

Cũng là AND.

---

Firewall

```
Đúng IP

AND

Đúng Port
```

↓

Cho phép.

---

# Phép toán thứ ba — OR

OR nghĩa là

> **Chỉ cần một điều kiện đúng.**

Bảng chân trị

|A|B|A OR B|
|---|---|---|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|1|

---

Ví dụ

```
Thanh toán

bằng

Visa

hoặc

Master
```

Chỉ cần một đúng.

---

Trong Java

```java
if(isAdmin || isOwner)
```

---

SQL

```sql
WHERE
country='VN'
OR country='JP'
```

---

# Phép toán thứ tư — XOR

Đây là phép nhiều người mới học thấy lạ.

XOR nghĩa là

> **Đúng khi hai giá trị khác nhau.**

|A|B|A XOR B|
|---|---|---|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

---

Ví dụ

Hai công tắc cầu thang.

Bật một công tắc.

↓

Đèn sáng.

Bật tiếp công tắc còn lại.

↓

Đèn tắt.

Đó chính là XOR.

---

Ngày nay XOR được dùng rất nhiều trong:

- mã hóa
    
- RAID
    
- kiểm tra lỗi
    
- CRC
    
- AES
    
- thao tác bit (bit manipulation)
    

---

# Điều thú vị

Boole không hề nghĩ đến transistor.

Ông chỉ nghiên cứu logic.

Khoảng gần 100 năm sau.

Claude Shannon phát hiện ra:

> Hai trạng thái của Boolean giống hệt hai trạng thái của công tắc điện.

```
True

↓

Có điện

↓

1
```

```
False

↓

Không điện

↓

0
```

Đó là một trong những khoảnh khắc quan trọng nhất lịch sử khoa học máy tính.

Một công trình toán học trừu tượng bỗng trở thành nền tảng để xây dựng máy tính điện tử.

---

# Từ toán học đến phần cứng

Hãy tưởng tượng hai công tắc mắc nối tiếp.

```
Nguồn

↓

[Công tắc A]

↓

[Công tắc B]

↓

Bóng đèn
```

Đèn chỉ sáng khi

A đóng

và

B đóng.

Đó chính là

```
AND
```

---

Nếu mắc song song

```
Nguồn

↓

A ----

      |

      |--- Đèn

      |

B ----
```

Chỉ cần một nhánh có điện.

Đó chính là

```
OR
```

---

Nếu thêm mạch đảo.

Ta có

```
NOT
```

Không cần CPU.

Không cần phần mềm.

Chỉ bằng công tắc.

---

# Mạch logic (Logic Gate)

Khi Boolean được hiện thực bằng linh kiện điện tử, chúng được gọi là **cổng logic**.

Những cổng cơ bản gồm:

- NOT
    
- AND
    
- OR
    
- XOR
    

Ngoài ra còn có:

- NAND
    
- NOR
    
- XNOR
    

Điều đáng chú ý là:

> **Chỉ với các cổng logic này, người ta có thể xây dựng mọi mạch số hiện đại.**

CPU không "suy nghĩ".

CPU chỉ truyền tín hiệu qua hàng tỷ cổng logic.

---

# Tại sao chỉ vài phép toán lại tạo được cả máy tính?

Đây là câu hỏi rất đáng suy ngẫm.

Nếu bạn nhìn một chương trình Java.

```java
if(age > 18){
    ...
}
```

Có vẻ rất cao cấp.

Nhưng cuối cùng.

Compiler sẽ biến thành rất nhiều phép:

- so sánh bit
    
- AND
    
- OR
    
- NOT
    
- cộng
    
- dịch bit
    
- nhảy lệnh
    

CPU không biết Java.

CPU cũng không biết SQL.

CPU chỉ biết logic.

---

# Góc nhìn của Backend Engineer

Bạn sử dụng Boolean hàng ngày mà có thể không để ý.

Ví dụ:

```java
if(user != null
   && user.isActive()
   && !user.isLocked())
```

Đây không chỉ là cú pháp Java.

Đó là biểu diễn trực tiếp của đại số Boolean.

---

Trong SQL:

```sql
WHERE
status='ACTIVE'
AND deleted=false
OR isAdmin=true
```

Query Optimizer sẽ phân tích biểu thức Boolean để quyết định cách thực thi.

---

Trong Elasticsearch:

```
must

should

must_not
```

Thực chất tương ứng với:

- AND
    
- OR
    
- NOT
    

---

Trong Kubernetes:

Node Selector, Affinity, Taints/Tolerations cũng dựa trên các điều kiện logic.

Trong Firewall:

```
IF
Source IP
AND
Destination Port
AND
Protocol
THEN Allow
```

Đó vẫn là Boolean.

---

# Tổng kết chương

George Boole không phát minh ra máy tính.

Ông phát minh ra **một ngôn ngữ toán học để mô tả logic**.

Claude Shannon sau đó chứng minh rằng ngôn ngữ này có thể được hiện thực bằng công tắc điện.

Đó là cây cầu nối giữa **toán học** và **phần cứng**.

Kể từ thời điểm đó, mọi hệ thống số đều có thể được xây dựng từ các cổng logic.

---

# Chuẩn bị cho chương 4

Đến đây chúng ta đã có hai mảnh ghép:

- Chương 2 trả lời: **Máy lưu thông tin bằng hai trạng thái vật lý.**
    
- Chương 3 trả lời: **Máy xử lý thông tin bằng logic toán học.**
    

Nhưng vẫn còn thiếu một câu hỏi quan trọng:

> **Nếu đã có logic và có hai trạng thái, người ta dùng linh kiện vật lý nào để hiện thực chúng?**

Đó là nội dung của **Chương 4 – Từ máy cơ học đến relay: những cỗ máy tính đầu tiên**, nơi chúng ta sẽ bắt đầu hành trình từ bánh răng, thẻ đục lỗ, relay điện từ và những thiết bị đặt nền móng cho máy tính hiện đại.