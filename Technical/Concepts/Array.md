## Hiểu đúng từ bộ nhớ đến hiệu năng thực tế

Tài liệu này chọn đúng chuỗi tư duy cần thiết khi học cấu trúc dữ liệu:

> **Data Structure → Memory Layout → Operations → Time Complexity → CPU Behavior → Trade-off → Use Cases**

Đây là cách học đúng, vì độ phức tạp Big-O không tự nhiên xuất hiện. Nó là hệ quả của cách dữ liệu được tổ chức trong bộ nhớ và cách CPU xử lý dữ liệu đó.

Array là cấu trúc đầu tiên nên học thật sâu, vì rất nhiều cấu trúc dữ liệu khác либо xây dựng trực tiếp trên nó, либо tối ưu theo nó: dynamic array, stack, queue, heap, hash table buckets, adjacency array trong graph, buffer xử lý I/O, tensor trong AI. Nếu hiểu chắc array, bạn sẽ dễ hiểu những cấu trúc sau theo cùng một cơ chế.

---

# 1. Array là gì?

Array là một tập hợp các phần tử **cùng kiểu dữ liệu**, được lưu **liên tiếp trong bộ nhớ**. Đây là ý nền tảng của mọi phân tích tiếp theo.

Ví dụ:

```text
index:  0   1   2   3   4
value: 12  35  80  90  11
```

Ta truy cập bằng chỉ số:

```text
array[3] = 90
```

Điểm cốt lõi không phải là “có nhiều phần tử”, mà là:

- các phần tử nằm cạnh nhau,
    
- cùng kiểu,
    
- địa chỉ của phần tử có thể suy ra từ vị trí của nó.
    

---

# 2. Vì sao array lại được lưu liên tiếp?

Đây là câu hỏi rất quan trọng. Lý do không phải chỉ vì “tiết kiệm chỗ”, mà vì **tối ưu truy cập và tối ưu phần cứng**.

## 2.1 Lý do 1: Tính địa chỉ trực tiếp

Nếu các phần tử nằm liên tiếp, CPU có thể tính địa chỉ phần tử bằng công thức:

```text
address = base + index × sizeof(element)
```

Đây là lý do truy cập theo index nhanh và không cần duyệt qua các phần tử trước đó.

## 2.2 Lý do 2: Cache locality

CPU làm việc rất hiệu quả với dữ liệu nằm gần nhau. Khi một phần tử được đọc, phần tử kế cận thường cũng được nạp vào cache theo cache line. Tài liệu gốc có nhắc đúng đến cache line và spatial locality.

Vì vậy, lưu liên tiếp giúp:

- duyệt tuần tự nhanh hơn,
    
- giảm số lần chạm RAM,
    
- tận dụng cache tốt hơn,
    
- tăng hiệu năng thực tế.
    

## 2.3 Lý do 3: Ít metadata hơn

Array thường không cần một node riêng cho mỗi phần tử như linked list. Điều đó làm:

- bộ nhớ gọn hơn,
    
- truy cập ít overhead hơn,
    
- thiết kế đơn giản hơn.
    

## 2.4 Lý do 4: Hợp với xử lý vector/SIMD

Dữ liệu liên tiếp giúp CPU xử lý nhiều phần tử cùng lúc, đặc biệt trong tính toán số, đồ họa, AI, xử lý ảnh.

Kết luận:

> Array được lưu liên tiếp không phải vì ngẫu nhiên, mà vì đó là cách tổ chức dữ liệu hợp nhất giữa mô hình bộ nhớ, cache CPU và chi phí thao tác.

---

# 3. Cách array nằm trong bộ nhớ

Giả sử:

```c
int arr[5] = {10,20,30,40,50};
```

Nếu `int = 4 bytes` và địa chỉ đầu tiên là `1000`, thì:

```text
1000 -> 10
1004 -> 20
1008 -> 30
1012 -> 40
1016 -> 50
```

Công thức chung là:

```text
address = base + index × sizeof(element)
```

Trong đó:

- `base` là địa chỉ đầu mảng,
    
- `index` là vị trí phần tử,
    
- `sizeof(element)` là kích thước của mỗi phần tử.
    

Ví dụ:

```text
arr[3] = 1000 + 3 × 4 = 1012
```

Tại sao không phải “binary sang ký tự” như bạn đang nghĩ?

Chỗ này cần sửa lại.

- Máy tính lưu dữ liệu dưới dạng bit.
- Bộ nhớ được quản lý theo **byte**.
- Kiểu dữ liệu quyết định số byte cần dùng.
- Khi đọc `int`, CPU/compilers diễn giải 4 byte đó theo kiểu `int`.
- Khi đọc `char`, nó diễn giải 1 byte theo kiểu `char`.

Không có bước “so khớp binary với bảng ký tự” theo nghĩa tổng quát như bạn mô tả. Đó chỉ là một cách diễn giải quá xa.

Cách đúng là:

> Bộ nhớ lưu byte. Kiểu dữ liệu quyết định cách giải mã các byte đó thành giá trị.


## 3.1 Những thành phần nào tham gia vào tính địa chỉ?

Khi truy cập một phần tử, hệ thống cần:

1. **Base: địa chỉ gốc của mảng**
    
2. **Index: vị trí phần tử trong array**
    
3. **Sizeof: kích thước mỗi phần tử**
    
4. **cách diễn giải kiểu dữ liệu**
    

Từ đó, xác định được điểm đầu, cuối của bộ nhớ. Lấy được toàn bộ giá trị của phần tử đó được định nghĩa bằng byte. Đồng thời dựa vào kiểu dữ liệu của array đó quy định để chuyển đổi thành dạng mà con người có thể hiểu. Vì máy tính chỉ lưu giá trị nhị phân. Do đó, CPU không cần duyệt từ đầu tới phần tử cần tìm. Nó chỉ tính địa chỉ rồi đọc đúng vùng nhớ đó.

---

# 4. O(1) nghĩa là gì?

`O(1)` nghĩa là:

> chi phí thực hiện **không tăng theo kích thước đầu vào**.

Đây không có nghĩa là “chỉ xử lý 1 phần tử”. Nó có nghĩa là dù mảng có 10 phần tử hay 10 triệu phần tử, số bước để truy cập `arr[i]` vẫn gần như cố định.

Với truy cập index trong array:

- đọc `base`
    
- nhân `index × size`
    
- cộng địa chỉ
    
- đọc dữ liệu
    

Không có vòng lặp phụ thuộc vào n.

Do đó:

- truy cập theo index: **O(1)**
    
- cập nhật theo index: **O(1)**
    

## 4.1 Vì sao người ta quy định như vậy?

Vì Big-O không đo “thời gian thật tuyệt đối”, mà đo **cách chi phí tăng khi input tăng**.

Nó là một quy ước toán học để mô tả tốc độ tăng trưởng của chi phí.

- Nếu input tăng gấp 10 mà chi phí không đổi theo n → O(1)
    
- Nếu input tăng gấp 10 mà chi phí cũng tăng xấp xỉ gấp 10 → O(n)
    
- Nếu input tăng gấp 10 mà số bước chỉ tăng chậm theo log → O(log n)
    

---

# 5. O(n) và O(log n) là gì?

## 5.1 O(n)

`n` là kích thước input, thường là số phần tử của mảng hoặc danh sách.

O(n) nghĩa là:

- chi phí tăng tuyến tính theo số phần tử,
    
- mảng càng lớn, số bước càng nhiều.
    

Ví dụ:

- linear search
    
- insert/delete giữa array
    
- duyệt toàn bộ mảng
    

## 5.2 O(log n)

`log n` xuất hiện khi sau mỗi bước, không gian xử lý bị giảm theo **tỉ lệ cố định**, thường là **chia đôi**.

Binary search là ví dụ chuẩn.

Cách suy ra:

```text
n → n/2 → n/4 → n/8 → ... → 1
```

Sau `k` bước:

```text
n / 2^k = 1
=> n = 2^k
=> k = log2(n)
```

Vì vậy binary search là **O(log n)**.

---

# 6. Binary search: vì sao là O(log n)?

Binary search chỉ dùng khi dữ liệu đã được sắp xếp.

Ví dụ tìm `9` trong dãy `1 -> 10`:

1. lấy phần tử giữa, ví dụ `5`
    
2. so sánh `9` với `5`
    
3. vì `9 > 5`, bỏ nửa trái
    
4. còn `6..10`
    
5. lấy giữa mới, ví dụ `8`
    
6. vì `9 > 8`, bỏ nửa trái
    
7. còn `9..10`
     
8. Lúc này không thể chia đổi được nữa, do con số bị dư và phần tử các bên < 2. Nên thực hiện duyệt mảng và so sánh trực tiếp để tìm số cần tìm
    
9. so sánh trực tiếp và tìm thấy `9`
    

Điểm cốt lõi:

> Mỗi bước chỉ giữ lại một nửa không gian tìm kiếm.

Đây là lý do số bước tăng rất chậm khi n tăng.

Không phải vì “đệ quy” làm nó log.  
Nó là log vì **mỗi bước loại bỏ một nửa**. Đệ quy chỉ là một cách cài đặt.

---

# 7. Các thao tác trên array

## 7.1 Update

```cpp
arr[2] = 100;
```

Nếu đã biết index, chỉ cần ghi đè tại địa chỉ tương ứng.

- không cần dịch phần tử khác
    
- không cần duyệt toàn bộ mảng
    

→ **O(1)**

## 7.2 Insert

Ví dụ:

```text
1 2 3 4 5
```

Chèn `X` vào giữa:

```text
1 2 X 3 4 5
```

Để giữ đúng thứ tự và tính liên tiếp, các phần tử phía sau phải dời sang phải.

Cơ chế thực tế:

1. tìm vị trí chèn
    
2. dịch các phần tử phía sau sang phải 1 ô
    
3. ghi phần tử mới vào vị trí trống
    

Nếu chèn ở đầu hoặc giữa, có thể phải dời gần như toàn bộ phần tử.

→ **O(n)**

## 7.3 Delete

Ví dụ:

```text
1 2 3 4 5
```

Xóa `2`:

1. xóa phần tử tại vị trí đó
    
2. dời các phần tử phía sau sang trái để lấp chỗ trống
    

→ **O(n)**

## 7.4 Có cần quan tâm điều này không?

Có, nếu bạn làm backend.

Không phải vì bạn phải nhớ từng công thức máy móc, mà vì đây là lý do chọn cấu trúc dữ liệu.

Ví dụ trong backend:

- list kết quả từ DB
    
- danh sách cache
    
- buffer xử lý request
    
- batch dữ liệu gửi đi
    
- tập item chỉ duyệt tuần tự
    

Nếu thao tác chủ yếu là đọc và duyệt, array rất hợp.

Nếu thao tác chủ yếu là chèn/xóa giữa nhiều, array có thể không phù hợp.

---

# 8. Dynamic Array là gì?

Dynamic array là array có thể tự tăng dung lượng khi đầy. Ví dụ phổ biến như `ArrayList`, `List<T>`, `vector`.

Cơ chế:

1. có `capacity` ban đầu
    
2. khi đủ chỗ, cấp phát vùng nhớ mới lớn hơn
    
3. copy toàn bộ phần tử cũ sang vùng mới
    
4. giải phóng vùng cũ
    
5. tiếp tục thêm phần tử
    

Đây là lý do:

- phần lớn lần `append/push_back` rất nhanh,
    
- nhưng thỉnh thoảng sẽ có lần rất đắt do resize.
    

Vì vậy, chi phí trung bình là:

- **amortized O(1)** cho thêm cuối,
    
- nhưng có những lần cụ thể là **O(n)** do copy lại toàn bộ.
    

---

# 9. Vì sao array có ưu điểm mạnh?

Truy cập nhanh, cache friendly, ít overhead, tiết kiệm RAM và SIMD.

## 9.1 Đọc nhiều hơn ghi

Array rất hợp khi:

- dữ liệu chủ yếu được đọc,
    
- ít thay đổi cấu trúc,
    
- thao tác chính là truy cập theo index và duyệt tuần tự.
    

Lý do:

- truy cập theo index là O(1),
    
- duyệt tuần tự tận dụng cache tốt,
    
- chi phí thấp hơn các cấu trúc có node hoặc con trỏ.
    

### Ví dụ thực tế

- danh sách sản phẩm từ API để hiển thị
    
- kết quả query DB rồi xử lý tuần tự
    
- danh sách ID cần gửi sang service khác
    
- buffer dữ liệu đọc từ file hoặc network
    

## 9.2 Duyệt tuần tự rất tốt

Array có các phần tử liên tiếp trong bộ nhớ. Khi đọc phần tử hiện tại, CPU dễ nạp luôn các phần tử kế tiếp vào cache.

### Ví dụ thực tế

- quét log
    
- xử lý ảnh
    
- tính tổng, map, filter, reduce
    
- parse dữ liệu batch
    

## 9.3 Ít overhead

Không phải mỗi phần tử là một node rời có con trỏ như linked list.

### Ví dụ thực tế

- mảng số liệu lớn
    
- buffer nhị phân
    
- tensor
    
- ma trận
    

## 9.4 Tiết kiệm bộ nhớ hơn nhiều cấu trúc có metadata

Với cùng số phần tử, array nguyên thủy thường ít tốn bộ nhớ hơn các cấu trúc có node, pointer, hash metadata.

### Ví dụ thực tế

- lưu số lượng lớn giá trị số
    
- lưu pixel ảnh
    
- lưu dữ liệu tính toán trong bộ nhớ tạm
    

## 9.5 Tối ưu cho SIMD/vectorization

Dữ liệu liên tiếp giúp CPU xử lý nhiều phần tử cùng lúc.

### Ví dụ thực tế

- xử lý ảnh
    
- machine learning
    
- tính toán khoa học
    
- nén/giải nén dữ liệu
    

---

# 10. Vì sao array có nhược điểm rõ?

Tài liệu gốc cũng nêu đúng các nhược điểm: insert/delete chậm, resize tốn, cần vùng nhớ liên tục.

## 10.1 Insert/Delete chậm

Vì phải dời phần tử.

### Ví dụ thực tế

- thêm một item ở đầu danh sách lớn
    
- xóa nhiều phần tử giữa mảng
    
- chèn liên tục trong editor buffer nếu không có cấu trúc phù hợp
    

## 10.2 Resize tốn chi phí

Vì phải cấp phát vùng mới và copy toàn bộ dữ liệu.

### Ví dụ thực tế

- danh sách tăng trưởng liên tục và không dự đoán được
    
- bộ đệm lớn vượt capacity nhiều lần
    

## 10.3 Cần vùng nhớ liên tục

Đây là giới hạn quan trọng của mảng vật lý.

### Ý nghĩa thực tế

- hệ điều hành phải tìm một vùng liên tục đủ lớn
    
- càng lớn càng khó cấp phát liên tục
    
- đây là một trong các lý do array không phải giải pháp cho mọi bài toán lớn
    

---

# 11. Trade-off cốt lõi

Tài liệu gốc đã diễn đạt đúng phần so sánh giữa array, linked list, hash table và tree.

- **Array**: đổi chèn/xóa để lấy truy cập nhanh và locality tốt
    
- **Linked List**: đổi locality để lấy chèn/xóa nhanh tại node đã biết
    
- **Hash Table**: đổi thêm bộ nhớ để lấy tra cứu theo khóa nhanh
    
- **Tree**: đổi tốc độ truy cập trực tiếp để giữ thứ tự và truy vấn theo khoảng
    

Locality ở đây là **tính cục bộ của việc truy cập bộ nhớ**. Nói chính xác hơn, CPU có xu hướng truy cập dữ liệu **gần nhau về mặt vị trí** hoặc **lặp lại trong một khoảng thời gian ngắn**. Đây là lý do nhiều cấu trúc dữ liệu, đặc biệt là array, chạy nhanh trong thực tế. Với linked list dữ liệu nằm rải rác chúng phải nhảy sang vùng nhớ nhiều nơi hơn nên chậm hơn.

Câu cần nhớ:

> Không có cấu trúc dữ liệu nào tốt nhất cho mọi tình huống. Chỉ có cấu trúc dữ liệu phù hợp nhất với một nhóm thao tác cụ thể.

---

# 12. Khi nào nên dùng array trong backend?

Tài liệu gốc đã nêu đúng các use case chính.

Nên dùng array/dynamic array khi:

- đọc nhiều hơn ghi
    
- cần truy cập theo index
    
- cần duyệt tuần tự nhanh
    
- kích thước dữ liệu tương đối ổn định hoặc có thể dự đoán
    

### Ví dụ thực tế

- danh sách kết quả từ một query
    
- buffer xử lý file/network
    
- dữ liệu batch
    
- danh sách record để sort/filter/map
    
- cache cục bộ dạng list
    

Không nên ưu tiên array khi:

- chèn/xóa đầu hoặc giữa diễn ra thường xuyên
    
- kích thước tăng giảm liên tục
    
- thao tác tại vị trí bất kỳ là nhu cầu chính
    

---

# 13. Cách tự tính Big-O cho mọi cấu trúc dữ liệu

Đây là phần nền tảng để bạn tự xử lý các DS và algorithm khác.

Khi nhìn một thao tác, hãy hỏi:

## Bước 1: Có duyệt qua nhiều phần tử không?

Nếu có → thường tăng theo n.

## Bước 2: Có dời/copy dữ liệu không?

Nếu có → thường cũng tăng theo n.

## Bước 3: Có giảm một nửa không gian xử lý sau mỗi bước không?

Nếu có → thường là log n.

## Bước 4: Có truy cập trực tiếp theo index/key không?

Nếu có → thường là O(1) trung bình hoặc lý tưởng.

## Quy tắc nhớ nhanh

- Không phụ thuộc vào n → **O(1)**
    
- Đi qua từng phần tử → **O(n)**
    
- Mỗi bước loại bỏ một nửa → **O(log n)**
    

---

# 14. Kết luận

Array là cấu trúc nền tảng vì nó cho bạn một nguyên lý cốt lõi:

> **Cách dữ liệu được tổ chức trong bộ nhớ quyết định chi phí thao tác. Chi phí đó quyết định Big-O. Big-O kết hợp với cache, memory layout và behavior của CPU quyết định hiệu năng thực tế.**

Nếu hiểu chắc array, bạn sẽ hiểu tốt hơn:

- dynamic array
    
- stack
    
- queue
    
- hash table
    
- heap
    
- tree
    
- graph
    
- buffer
    
- và cách chọn data structure trong backend/system design
    

## Ghi nhớ ngắn nhất

- Array được lưu liên tiếp để tính địa chỉ trực tiếp và tận dụng cache.
    
- Access/update theo index là O(1).
    
- Search tuyến tính là O(n).
    
- Binary search là O(log n) vì mỗi bước loại bỏ một nửa.
    
- Insert/delete giữa chậm vì phải dời dữ liệu.
    
- Dynamic array thêm tiện lợi nhưng có lúc phải copy toàn bộ.
    
- Chọn cấu trúc dữ liệu là chọn trade-off, không phải chọn “cái nhanh nhất” theo cảm tính.
    