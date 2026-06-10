- Các vấn đề ?
	- Tránh lãng phí tài nguyên: Mỗi lần một service sử dụng một đối tượng sẽ khởi tạo đối tượng đó. Nếu nhiều service cùng dùng 1 đối tượng, sẽ khởi tạo nhiều lần đối tượng đó. Như vậy sẽ tạo ra nhiều bản sao, mỗi bản sao lại clone lại đối tượng gốc. Mặc dù các đối tượng khởi tạo đầu tiên và các đối tượng khởi tạo sau này là như nhau, khiến lãng phí tài nguyên để lưu trữ các đối tượng và lặp lại các xử lý như khởi tạo gây ảnh hưởng hiệu suất
	+ Thiếu nhất quán: Khi nhiều service cùng dùng chung 1 đối tượng. Ở 1 service nào đó khi sử dụng đối tượng, chúng có thể thực hiện ghi đè giá trị lên các propety của đối tượng đó. Khiến các service khác không cập nhật thay đổi này làm thiếu nhất quán dữ liệu xử lý ở các service.
	+ Khó quản lý trạng thái: Vì có nhiều bản sao đối tượng được tạo ra và sử dụng, khiến việc quản lý theo dõi trạng thái chung trở nên phức tạp
	
- Giải quyết được gì ?
	+ Tiết kiệm tài nguyên: Các đối tượng sử dụng chung được khởi tạo 1 lần duy nhất, tránh việc tạo nhiều đối tượng giống nhau -> tiết kiệm tài nguyên
	+ Tăng performment: Các đối tượng được khởi tạo 1 lần duy nhất và sử dụng cho mọi nơi. Tránh các sử lý lặp lại: Khởi tạo, đọc, ghi dữ liệu -> tăng hiệu suất
	+ Đảm bảo toàn vẹn và nhất quán: Tất cả các service đều truy cập cùng 1 đối tượng được khởi tạo lần đầu. Khi đối tượng thay đổi thì các service khác đều được cập nhật sự thay đổi
	+ Cung cấp điểm truy cập toàn cục
		
- Các vấn đề của singleton ?
	+ Vấn đề đa luồng (concurrency): Nếu hai hoặc nhiều luồng cùng lúc gọi getInstance() khi instance còn null. Hai or nhiều luồng có thể cùng nhau pass qua kiểm tra instance == null (khởi tạo 1 lần duy nhất lúc ban đầu) và tạo ra nhiều insctance khác nhau, phá vỡ mục đích sử dụng của singleton. Có thể sử dụng synchronized or double-checked locking để khác phục. Nhưng chúng làm code làm code phức tạp hơn và ảnh hưởng đến hiệu năng
		+ Cách xử lý :
			+ earger init: Tạo instance trước khi class được load (tạo ngay khi class đó được nạp vào xử lý) or trước khi luồng request
		Nhược: Các instance có thể tạo ra mà không dùng gây lãng phí, ko bắt lỗi được tại thời điểm khởi tạo
		-> Lazy init: Mỗi lần truy cập instance sẽ lock và unlock lại để kiểm tra
		Nhược: Mỗi lần gọi instance đều phải đồng bộ lock/unlock gây ảnh hưởng tới hiệu năng
		-> Một số phương thức dành riêng cho từng ngôn ngữ lập trình khác nhau
	+ Khả năng mở rộng: Môi khi thay đổi đối tượng xử lý sẽ cần sửa lại instance ở tất cả các nơi sử dụng. Ko như Depence Injection có thể thay đổi nhanh chóng bằng việc thay đổi implement inteface trong contruct()
	+ Đa luồng: Khi sử dụng không chia sẻ kết nối. Mỗi singleton xử lý trên mỗi process. Làm leak or race condition queue, cache
	+ Thiếu nhất quán: Singleton khởi tạo các thông tin gửi mail + method set property (thay đổi thông tin gửi mail) 2 phần khác nhau trong hệ thống. Khi 1 module xử lý gửi mail, thì đồng thời có 1 module chạy song song sử dụng method set property kia khiến thông tin gửi mail bị sai. => Gây lỗi khó hiểu và khó tìm


Sự mâu thuẫn của bạn nằm ở chỗ: Bạn đang nghĩ Object là một "thực thể" khép kín, chứa cả **Hành vi (Method)** và **Dữ liệu (Thuộc tính/Trạng thái)** của một đối tượng cụ thể (ví dụ: Object `User` thì có `id`, `name`). Bạn nghĩ `UserService` cũng giống như vậy.

Nhưng trong Backend, **`UserService` là một loại Object đặc biệt: Nó hoàn toàn KHÔNG CÓ THUỘC TÍNH DỮ LIỆU (Stateless).**

Hãy cùng bóc tách chính xác cái gì nằm trên Heap, cái gì nằm trên Stack để giải quyết triệt để mâu thuẫn này.

## 1. Cái "Object chứa logic" trên Heap thực chất là gì?

Khi bạn viết một Class trong code (ví dụ bằng Java, C#, hay TypeScript):

TypeScript

```
class UserService {
    findById(id: number) {
        // Logic tìm kiếm
        return "User " + id;
    }
}
```

Khi ứng dụng khởi chạy, Framework sẽ làm hành động: `const userServiceInstance = new UserService();` **đúng 1 lần duy nhất** và ném nó lên Heap.

### Vậy Object `userServiceInstance` trên Heap này chứa cái gì?

Nó **không chứa dữ liệu của bất kỳ User nào cả**. Bản chất của nó trên Heap lúc này chỉ là một **bảng chỉ mục chứa các con trỏ trỏ tới vùng mã máy (Code Segment) của các hàm**.

- Nó chỉ là một "tấm biển chỉ đường" nói với CPU rằng: _"Nếu ai đó muốn chạy hàm `findById`, hãy chạy đoạn mã máy ở địa chỉ vùng nhớ X này"_.
    
- Nó giống như một **Trạm thu phí** cố định trên đường. Bản thân trạm thu phí không chứa chiếc xe nào cả, nó chỉ chứa luật lệ và thiết bị để xử lý các chiếc xe đi qua.
    

## 2. Việc truyền giá trị và xử lý khác nhau diễn ra ở đâu? (Sự kỳ diệu của Stack)

Khi có 2 request đến cùng một lúc:

- **Request A:** Muốn `findById(5)`
    
- **Request B:** Muốn `findById(10)`
    

Cả hai request này **XỬ LÝ HOÀN TOÀN TRÊN STACK**, không liên quan gì đến Heap của `UserService`.

Hãy nhìn vào quy trình chạy trong CPU:

1. **Mỗi Request có một Thread (Luồng) riêng, sở hữu một Stack riêng biệt.**
    
    - Request A có **Stack A**.
        
    - Request B có **Stack B**.
        
2. **Khi Request A gọi hàm:**
    
    - Hệ thống tạo ra một **Stack Frame** cho hàm `findById` bên trong **Stack A**.
        
    - Giá trị `id = 5` được nạp vào Stack A.
        
    - CPU nhìn vào Object `UserService` trên Heap để biết logic của hàm `findById` là gì, rồi nó bê cái logic đó về **chạy trực tiếp trên dữ liệu `id = 5` của Stack A**.
        
3. **Cùng lúc đó, Request B gọi hàm:**
    
    - Hệ thống tạo ra một **Stack Frame** bên trong **Stack B**.
        
    - Giá trị `id = 10` được nạp vào Stack B.
        
    - CPU cũng nhìn vào Object `UserService` trên Heap đó, bê logic về **chạy trên dữ liệu `id = 10` của Stack B**.
        

> 💡 **Kết luận 1:** Bản chất Object `UserService` trên Heap chỉ là **nơi chứa bộ quy tắc (Logic)**. Còn dữ liệu đầu vào (`id=5`, `id=10`) và các biến tạm phát sinh trong quá trình tính toán đều nằm ở **Stack riêng biệt của từng request**. Vì Stack độc lập, nên hai request không bao giờ bị lẫn lộn dữ liệu với nhau, dù dùng chung một Object Service.

## 3. "Object kết quả trả về" được lưu trữ ở đâu?

Bạn thắc mắc: _"Mỗi request đưa vào một tham số riêng... kết quả cho mỗi object sau khi xử lý này chúng có các kết quả khác nhau."_ — **Điều này hoàn toàn đúng!**

Sau khi `UserService` chạy xong logic, nó sẽ tạo ra kết quả (ví dụ: trả về một Object `UserResult` chứa thông tin của User đó).

- Object `UserResult(id=5, name="An")` của Request A sẽ được khởi tạo mới và nạp vào **Heap (vùng nhớ ngắn hạn của Request A)**.
    
- Object `UserResult(id=10, name="Bình")` của Request B cũng được khởi tạo mới và nạp vào một ô nhớ khác trên **Heap**.
    

Khi Request A kết thúc, Response được trả về cho client, Stack A bị xóa sạch. Object `UserResult(id=5)` trên Heap không còn ai tham chiếu nữa, nó sẽ bị Garbage Collector quét sạch.

Trong khi đó, cái Object `UserService` ban đầu (chứa logic) **vẫn đứng im trên Heap dài hạn**, không hề bị ảnh hưởng hay thay đổi gì sau các request đó cả.

## Tóm lại bằng một hình ảnh ẩn dụ cho dễ hiểu nhất:

Hãy tưởng tượng `UserService` giống như một **Chiếc máy photocopy (Singleton trên Heap)**.

- Chiếc máy này chỉ có 1 cái duy nhất trong văn phòng (Ứng dụng). Nó chứa logic: _"Nếu đưa giấy vào, tôi sẽ in ra bản sao"_.
    
- **Request A** đến, mang theo một tờ giấy màu **Xanh** (`id=5`).
    
- **Request B** đến, mang theo một tờ giấy màu **Đỏ** (`id=10`).
    
- Cả hai người cùng xếp hàng dùng chung một chiếc máy photocopy đó.
    
- Kết quả (Result) trả ra: Người A cầm về tờ giấy Xanh được nhân bản, người B cầm về tờ giấy Đỏ được nhân bản.
    

Sau khi hai người cầm kết quả đi về (Request kết thúc), chiếc máy photocopy **vẫn đứng nguyên ở đó**, không hề biến đổi thành màu xanh hay màu đỏ. Nó sẵn sàng cho người tiếp theo.

Đó chính là lý do vì sao người ta sử dụng lại (tái sử dụng tham chiếu) Object Service thông qua Singleton: **Để không phải tốn tiền mua (khởi tạo) một chiếc máy photocopy mới cho mỗi một người bước vào văn phòng.**

Bạn đã giải tỏa được sự mâu thuẫn này chưa? Bạn có muốn nhìn trực tiếp vào một đoạn code để xem các biến nó nhảy vào Stack/Heap thế nào không?




Object Lifetime Management


-------------------------------------------
Ví dụ:

```php
class UserService
{
    private $logger;

    public function find($id)
    {
        return "User {$id}";
    }
}
```

---

## Có 3 thứ khác nhau

### 1. Source code trên disk

File:

```php
UserService.php
```

chứa:

```php
class UserService
{
    ...
}
```

Đây chỉ là text.

---

### 2. Code được load vào memory

Khi ứng dụng chạy:

```php
find()
```

được load vào RAM.

Bạn có thể hình dung:

```text
Memory

Code:
 └─ UserService::find()
```

Nó chỉ cần tồn tại một lần.

---

### 3. Object (instance)

Khi gọi:

```php
$service = new UserService();
```

lúc này mới sinh ra object.

```text
Heap

UserService Object
```

Object này chứa:

```text
logger reference
```

và các dữ liệu của instance.

---

## Điều tôi muốn nói lúc trước

Bạn đang suy luận như sau (nếu tôi hiểu đúng):

```text
Request A
 -> new UserService()

Request B
 -> new UserService()

Request C
 -> new UserService()
```

Bạn nghĩ:

```text
Mỗi lần new
=> copy toàn bộ logic find()
=> rất tốn
```

Đây là chỗ không chính xác.

---

Ví dụ:

```php
new UserService();
new UserService();
new UserService();
```

thì:

```text
Code của find()
```

vẫn chỉ có:

```text
1 bản
```

trong memory.

---

Cái được tạo thêm là:

```text
Object #1
Object #2
Object #3
```

chứ không phải:

```text
find() #1
find() #2
find() #3
```

---

## Hình dung bằng bản thiết kế nhà

Class giống như:

```text
Bản thiết kế căn nhà
```

---

Object giống như:

```text
Ngôi nhà được xây từ bản thiết kế đó
```

---

Bạn có thể xây:

```text
Nhà A
Nhà B
Nhà C
```

từ cùng một bản thiết kế.

---

Không ai phải vẽ lại:

```text
Bản thiết kế
```

cho mỗi căn nhà.

---

## Vì sao điều này quan trọng?

Vì lúc trước bạn có ý:

> Các request phải khởi tạo object để lấy logic dùng chung nên rất tốn tài nguyên.

Thực tế:

```php
new UserService()
```

thường không tốn vì:

- logic đã có sẵn
    
- code đã load sẵn
    

Nó chỉ tạo thêm một object nhỏ.

---

Thứ thực sự tốn thường là:

```php
new PDO(...)
```

```php
new Redis(...)
```

```php
new KafkaProducer(...)
```

vì chúng phải:

- mở connection
    
- cấp phát buffer
    
- tạo socket
    
- handshake
    

---

## Từ đó dẫn đến Singleton

Người ta không giữ lại object vì:

```text
Method find() quá đắt
```

mà thường vì:

```text
Object đó giữ tài nguyên đắt đỏ
```

Ví dụ:

```php
DatabaseConnection
RedisClient
HttpClient
```

---

Nói ngắn gọn:

- Class = bản thiết kế.
    
- Method = phần logic của bản thiết kế.
    
- Object = một instance được tạo từ bản thiết kế.
    
- Tạo nhiều object không đồng nghĩa với việc copy toàn bộ logic nhiều lần.
    
- Singleton chủ yếu hữu ích khi object giữ tài nguyên hoặc trạng thái dùng chung, không phải vì method của nó "nặng".
    

Đây là điểm cốt lõi khiến suy luận trước đó của bạn bị lệch hướng một chút. Bạn đang đánh đồng **"khởi tạo object"** với **"nạp lại logic của class"**, trong khi ở runtime chúng là hai việc rất khác nhau.