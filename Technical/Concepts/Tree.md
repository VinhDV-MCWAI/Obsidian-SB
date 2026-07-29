# TÀI LIỆU TOÀN DIỆN VÀ DUY NHẤT: CẤU TRÚC DỮ LIỆU CÂY (MASTER SYSTEM REFERENCE)
## CHƯƠNG 1: BẢN CHẤT BỘ NHỚ VÀ TƯƠNG TÁC PHẦN CỨNG

### 1.1. Phân bổ Bộ nhớ: Contiguous Block vs. Pointer-Based Structure

Khác với Mảng (Array) lưu trữ các phần tử nằm trên một khối bộ nhớ liên tục (Contiguous Memory Block), Cây (Tree) là một **cấu trúc dữ liệu liên kết dựa trên con trỏ (Pointer-Based Structure)**.

#### Mảng trong bộ nhớ RAM

Địa chỉ bộ nhớ tăng dần đều. Việc tính địa chỉ phần tử thứ $i$ thực hiện qua công thức:

$$\text{Address}(i) = \text{BaseAddress} + i \times \text{ElementSize}$$

Chi phí tính toán địa chỉ là $O(1)$.

```
Memory Address:  0x100     0x104     0x108     0x10C
Data Layout:    [ 10  ]   [ 20  ]   [ 30  ]   [ 40  ]
```

#### Cây trong bộ nhớ RAM

Các Node được cấp phát động (`malloc` / `new`) tại các địa chỉ ngẫu nhiên trên bộ nhớ Heap. Các Node không nằm cạnh nhau mà kết nối qua địa chỉ con trỏ.

```
0x1A0 (Node 50)              0x820 (Node 30)              0x400 (Node 70)
+-------------------+        +-------------------+        +-------------------+
| Value: 50         |        | Value: 30         |        | Value: 70         |
| Left : 0x820      | ---->  | Left : nullptr    |        | Left : nullptr    |
| Right: 0x400      | ──┐    | Right: nullptr    |        | Right: nullptr    |
+-------------------+   │    +-------------------+        +-------------------+
                        └──> (chuyển sang địa chỉ 0x400)
```

#### Mối liên hệ cấu trúc

- **Singly Linked List:** Cây suy biến trong đó mỗi Node có tối đa 1 con trỏ ($Out\text{-}Degree \le 1$).
    
- **Binary Tree:** Tối đa 2 con trỏ (`left`, `right`).
    
- **N-ary Tree:** Tập hợp mảng chứa nhiều con trỏ (`std::vector<Node*> children`).
    

### 1.2. Bản chất Disk I/O và Page-Based Storage

- **Disk I/O:** Là 1 thao tác truyền nhận dữ liệu vật lý giữa thiết bị lưu trữ (HDD/SSD) và RAM.
    
- **Đơn vị I/O:** Phần cứng ổ đĩa không đọc/ghi theo Byte hay từng Node nhỏ, mà đọc/ghi theo các khối cố định gọi là **Page / Block** (thường là $4\text{ KB}, 8\text{ KB}, 16\text{ KB}$).
    
- Thời gian 1 lần Disk I/O tốn $\approx 1 - 10\text{ ms}$ (chậm hơn RAM $10.000 - 100.000$ lần). Do đó, tiêu chuẩn tối ưu hóa cấu trúc dữ liệu trên đĩa là **giảm số lần phát lệnh Disk I/O**.
    

### 1.3. Cơ chế CPU: Cache Locality và Hiện tượng Pointer Chasing

Khi CPU xử lý dữ liệu, nó nạp dữ liệu từ RAM vào bộ nhớ đệm (L1/L2/L3 Cache) theo từng **Cache Line** ($64\text{ Bytes}$).

```
[ CPU Core ] <---> [ L1 Cache (64B Line) ] <---> [ L2/L3 Cache ] <---> [ Main Memory (RAM) ]
```

- **Trên Mảng:** Tận dụng tốt **Spatial Locality**. CPU Hardware Prefetcher tự động nạp các phần tử kế tiếp vào Cache. Tỷ lệ Cache Hit cao.
    
- **Trên Cây (RAM):** Bị hiện tượng **Pointer Chasing**. Các Node nằm rải rác trên Heap khiến con trỏ trỏ tới các vùng nhớ nằm ngoài Cache Line hiện tại. Xảy ra **Cache Miss**, CPU buộc phải tạm dừng đường ống lệnh (**Pipeline Stall**) $100 - 200$ chu kỳ xung nhịp để nạp dữ liệu từ RAM về Cache cho mỗi bước nhảy con trỏ.
    

### 1.4. Hao phí Bộ nhớ (Memory Overhead)

Trên kiến trúc $64\text{-bit}$, cấu trúc con trỏ tốn bộ nhớ hơn rất nhiều so với dữ liệu thực tế (Payload).

**Mô phỏng bộ nhớ 1 Node Cây Nhị Phân chứa giá trị `int` ($4\text{ Bytes}$) trên Java $64\text{-bit}$ JVM:**

|**Thành phần**|**Dung lượng (Bytes)**|
|---|---|
|Mark Word (Object Header)|$8\text{ Bytes}$|
|Klass Word (Object Header)|$8\text{ Bytes}$|
|Data Payload (`int value`)|$4\text{ Bytes}$|
|Padding (Để chia hết cho 8)|$4\text{ Bytes}$|
|Left Child Pointer|$8\text{ Bytes}$|
|Right Child Pointer|$8\text{ Bytes}$|
|**Tổng kích thước 1 Node trên RAM**|**$40\text{ Bytes}$**|

> **Nhận xét:** Để lưu trữ dữ liệu thực tế $4\text{ Bytes}$, hệ thống tốn $36\text{ Bytes}$ hào phí quản lý (Overhead gấp 9 lần Payload).

Điều kiện: _"Bảng dưới đây tính toán trên kiến trúc 64-bit khi TẮT Compressed OOPs (hoặc Heap > 32GB). Nếu bật Compressed OOPs (mặc định), tổng dung lượng Node giảm từ 40 Bytes xuống 24 Bytes."_
## CHƯƠNG 2: PHÂN LOẠI CẤU TRÚC CÂY

```
                          CẤU TRÚC DỮ LIỆU CÂY
                                    │
         ┌──────────────────────────┴──────────────────────────┐
         ▼                                                     ▼
 CÂY NGHIỆP VỤ (Domain Tree)                        CÂY THUẬT TOÁN (Algorithmic Tree)
 ───────────────────────────                        ────────────────────────────────
 • DOM, File System, Org Chart, JSON                • BST, AVL, Red-Black, B-Tree
 • Không có quy tắc so sánh giá trị                 • Tuân theo bất biến toán học (Invariant)
 • Vị trí Node do ứng dụng/người dùng quy định       • Thuật toán tự động định vị và sắp xếp
```

1. **Cây Nghiệp vụ (Domain-driven Hierarchy Tree):** HTML DOM, Org Chart, JSON. Vị trí Node do logic ứng dụng hoặc con người thiết lập thủ công bằng cách nối con trỏ / thêm vào mảng `children[]`.Ví dụ: DOM HTML, Sơ đồ tổ chức công ty (Org Chart), Thư mục tập tin (File System), Dữ liệu JSON/XML.
    
2. **Cây Thuật toán (Value-driven Algorithmic Tree):** BST, AVL, Red-Black, B-Tree, B+ Tree. Vị trí Node do thuật toán tự động xác định dựa trên Hàm so sánh (`<`, `>`, `==`) để bảo toàn Bất biến toán học (Invariant).
    

## CHƯƠNG 3: TRỤC TIẾN HÓA CỦA CÁC MÔ HÌNH CÂY

### GIAI ĐOẠN 1: Mảng / Linked List $\rightarrow$ Cây Tìm Kiếm Nhị Phân (BST)

#### 1. Vấn đề phát sinh

Khi tập dữ liệu lớn lên đến hàng triệu phần tử, thao tác tìm kiếm thông tin trên mảng chưa sắp xếp hoặc trên Danh sách liên kết (Linked List) tốn thời gian quá lâu ($O(N)$). Nếu dùng Mảng đã sắp xếp để tìm kiếm nhanh ($O(\log N)$), thì thao tác chèn/xóa một phần tử vào giữa mảng lại cực kỳ chậm ($O(N)$) do phải dời hàng loạt ô nhớ.

#### 2. Nguyên nhân & Hậu quả

- Mảng có cấu trúc khối bộ nhớ liên tục: Tìm kiếm nhanh nhờ chỉ số (Index), nhưng co giãn kích thước rất kém.
    
- Danh sách liên kết có cấu trúc linh hoạt nhờ con trỏ: Chèn/xóa $O(1)$, nhưng không thể nhảy truy cập ngẫu nhiên mà phải duyệt từ đầu đến cuối $O(N)$.
    

#### 3. Mô hình / Nghiên cứu mới

Sự kết hợp giữa **con trỏ linh hoạt** (Linked List) và **chiến lược chia để trị** (Binary Search) tạo ra **Binary Search Tree (BST)**.

#### 4. Cơ chế hoạt động & Giải quyết

Toàn bộ cây tuân thủ Bất biến toán học:

$$\forall x \in \text{LeftSubtree}(Node), x.value < Node.value$$

$$\forall y \in \text{RightSubtree}(Node), y.value > Node.value$$

- **Cơ chế tìm kiếm (Tại sao Search đạt $O(\log_2 N)$):**
    
    Khi tìm kiếm giá trị $K$, CPU so sánh $K$ với `Node.value`. Nếu $K < \text{value}$, rẽ sang `left`. Nếu $K > \text{value}$, rẽ sang `right`.
    
    Mỗi bước nhảy loại bỏ chính xác $50\%$ không gian tìm kiếm còn lại (trên cây cân bằng).
    

```
          50             (Tìm K = 60)
        /    \           1. 60 > 50 -> Rẽ phải tới 70
      30      70         2. 60 < 70 -> Rẽ trái tới 60
     /  \    /  \        3. 60 == 60 -> Tìm thấy (Tốn 3 bước thay vì N bước)
   20   40  60  80
```

#### 5. Hạn chế mới phát sinh

BST phụ thuộc hoàn toàn vào thứ tự dữ liệu đầu vào. Nếu chèn một dãy số tăng dần (`10 -> 20 -> 30 -> 40`), cây bị suy biến hoàn toàn thành **Singly Linked List**. Chiều cao cây $H = N - 1$. Độ phức tạp tìm kiếm, chèn, xóa bị tụt từ $O(\log_2 N)$ về $O(N)$.

#### 6. Ứng dụng thực tế

- **Công cụ sử dụng:** BST thuần túy **không bao giờ** được dùng trong các hệ thống Production thực tế vì rủi ro suy biến hiệu năng.
    
- **Thực hiện thủ công:** Chỉ viết khi làm bài tập giải thuật hoặc các ứng dụng nhỏ trong bộ nhớ RAM khi đảm bảo dữ liệu đầu vào hoàn toàn ngẫu nhiên.
    
- **Cách triển khai:** Khai báo struct chứa `value`, con trỏ `left`, `right`. Dùng đệ quy hoặc vòng lặp để duyệt.

### GIAI ĐOẠN 2: BST Suy biến $\rightarrow$ Cây Tự Cân Bằng trên RAM (AVL & Red-Black Tree)

#### 1. Vấn đề phát sinh

Làm sao giữ cho chiều cao cây luôn ở mức $H \approx \log_2 N$ trong mọi trường hợp (kể cả khi chèn dữ liệu đã sắp xếp) để đảm bảo độ phức tạp $O(\log_2 N)$?

#### 2. Nguyên nhân & Hậu quả

Thuật toán chèn của BST thụ động: chỉ nối node mới vào vị trí lá thỏa mãn điều kiện $Left < Node < Right$ mà không đánh giá lại độ lệch chiều cao giữa các nhánh.

#### 3. Mô hình / Nghiên cứu mới

Phát minh cơ chế **Tự cân bằng (Self-Balancing)** thông qua thao tác **Xoay Cây (Tree Rotation)**:

1. **AVL Tree:** Tự cân bằng nghiêm ngặt.
    
2. **Red-Black Tree:** Tự cân bằng tương đối (dùng màu sắc Đỏ/Đen).
    

#### 4. Cơ chế hoạt động & Giải quyết

- **Thao tác Xoay Cây (Tree Rotation):** Thay đổi mối quan hệ cha-con giữa các con trỏ để giảm chiều cao nhánh bị lệch mà không phá vỡ bất biến BST.
    

```
   Left Rotation (Xoay Trái tại Node A)
        A                   B
       / \                 / \
      t1  B     ===>      A   t3
         / \             / \
        t2  t3          t1  t2
```

- **AVL Tree:** Quản lý bằng chỉ số cân bằng **Balance Factor** tại MỌI node:
    
    $$\text{BalanceFactor} = \vert{}H_{left} - H_{right}\vert{} \le 1$$
    
    Nếu lệch quá 1, kích hoạt xoay ngay lập tức. Tối ưu cho hệ thống đọc nhiều (Read-heavy).
    
- **Red-Black Tree:** Gán màu Đỏ/Đen và duy trì quy tắc (không có 2 node Đỏ liền kề, cùng số node Đen trên mọi đường đi từ Root tới lá). Chiều cao tối đa $\le 2 \log_2(N + 1)$. Xoay ít hơn AVL, tối ưu cho cả Read và Write.
    

#### 5. Hạn chế trên Ổ đĩa (HDD/SSD)

AVL và Red-Black Tree hoạt động hoàn hảo trên **RAM**. Tuy nhiên, khi tập dữ liệu cực lớn, mà RAM đặc điểm dung lượng lưu trữ giới hạn và sử dụng tạm thời. Do đó, phải **lưu trữ dưới Ổ ĐĨA (HDD/SSD)**, cấu trúc cây nhị phân thất bại hoàn toàn.

- **Lý do:** Đĩa đọc/ghi theo từng **Page ($4\text{ KB} - 16\text{ KB}$)**. Cây nhị phân chỉ chứa 1 Key/Node ($24 - 40\text{ Bytes}$), khiến $99.9\%$ dung lượng của một Page đĩa bị lãng phí.
    
- Với $100$ triệu phần tử, chiều cao cây Red-Black $\approx 27$ tầng. Tìm 1 phần tử mất $27$ lần đọc đĩa ngẫu nhiên (Disk I/O). Tốc độ đọc đĩa $10\text{ ms}$/lần $\rightarrow 27 \times 10\text{ ms} = 270\text{ ms}$/truy vấn (Quá chậm).
    

#### 6. Ứng dụng thực tế

Được đóng gói tự động trong thư viện chuẩn:

- C++ STL: `std::map`, `std::set`
    
- Java: `java.util.TreeMap`, `java.util.TreeSet`
    
- Linux Kernel: Completely Fair Scheduler (CFS), Virtual Memory Areas (VMA).
    

### GIAI ĐOẠN 3: Cây Nhị phân thất bại trên Đĩa $\rightarrow$ B-Tree

#### 1. Vấn đề phát sinh

Làm sao thiết kế cấu trúc cây lưu trữ trên đĩa vật lý sao cho nén chiều cao cây xuống mức tối đa ($3 - 4$ tầng) để giảm số lần đọc đĩa (Disk I/O)?

#### 2. Nguyên nhân & Hậu quả

Hệ thống lưu trữ ngoài (HDD/SSD) có độ trễ truy xuất chậm hơn RAM hàng chục ngàn lần. Việc dùng cây nhị phân làm phát sinh quá nhiều lượt đọc đĩa ngẫu nhiên do chiều cao cây lớn.

#### 3. Mô hình / Nghiên cứu mới

Mô hình Cây tìm kiếm đa nhánh: **B-Tree (Balanced Tree for External Storage)**.

#### 4. Cơ chế hoạt động & Giải thích chi tiết

```
                     [ 20 | 50 | 80 ]               <- Root Node (1 Page đĩa = 1 Disk Read)
                   /      |      \      \
       [ 5 | 10 ]    [ 30 | 40 ]  [ 60 | 70 ]       <- Child Nodes (1 Page đĩa)
```

- **Key trong Node là gì?** Key là giá trị trường chỉ mục dùng để so sánh và định tuyến (ví dụ: `ID = 50`, `Timestamp = 1710000000`).
    
- **Mục đích gom nhiều Key vào 1 Node:**
    
    1. **Không làm giảm tổng dung lượng dữ liệu**, nhưng giúp 1 Node chứa vừa khớp kích thước 1 Page đĩa ($16\text{ KB}$).
        
    2. Chỉ tốn **1 Disk I/O** để nạp toàn bộ hàng trăm Key trong Node đó vào RAM.
        
    3. CPU dùng thuật toán Binary Search trên RAM (tốc độ nanosecond) để so sánh hàng trăm Key cùng lúc, từ đó xác định chính xác Node con tiếp theo.
        
    4. Nâng cao **Hệ số phân nhánh (Fan-out $M$)** từ 2 lên 100 hoặc 1000.
        
- **Hiệu năng Disk I/O với Fan-out $M = 100$:**
    
    - Tầng 1 (Root): $1\text{ Page} \rightarrow 100\text{ Keys}$
        
    - Tầng 2: $100\text{ Pages} \rightarrow 10.000\text{ Keys}$
        
    - Tầng 3: $10.000\text{ Pages} \rightarrow 1.000.000\text{ Keys}$
        
    - Tầng 4: $1.000.000\text{ Pages} \rightarrow 100.000.000\text{ Keys}$
        
- **Kết quả:** Tìm kiếm trong $100$ triệu phần tử chỉ tốn tối đa **$4$ lần đọc đĩa (Disk I/O)**.
    

#### 5. Hạn chế mới phát sinh

1. B-Tree lưu Dữ liệu thực tế (Payload) ở **tất cả các tầng** (Root, Internal, Leaf). Vì Payload chiếm không diện tích trong Page $16\text{ KB}$, số lượng Key dẫn đường trong 1 Page bị giảm xuống $\rightarrow$ Giảm Fan-out $M$.
    
2. **Range Query (Tìm theo khoảng) kém:** Khi thực hiện câu lệnh `WHERE age BETWEEN 20 AND 80`, B-Tree phải duyệt đệ quy đi ngược lên Node cha rồi hạ xuống Node con nhiều lần, gây ra các lượt đọc đĩa ngẫu nhiên lặp lại.
    

#### 6. Ứng dụng thực tế

Được dùng trong các Hệ thống quản lý tập tin (File Systems) như NTFS, HFS+, EXT3/EXT4.

### GIAI ĐOẠN 4: Range Query thất bại trên B-Tree $\rightarrow$ B+ Tree

#### 1. Vấn đề phát sinh

Tối ưu hóa B-Tree cho hệ quản trị cơ sở dữ liệu quan hệ (RDBMS), nơi các truy vấn quét theo khoảng (**Range Scan**) diễn ra với tần suất cao.

#### 2. Nguyên nhân & Hậu quả

B-Tree không có liên kết ngang giữa các phần tử ở nút lá. Việc di chuyển giữa các phần tử kế tiếp bắt buộc phải duyệt qua nút cha làm tăng Disk I/O.

#### 3. Mô hình / Nghiên cứu mới

Phát minh biến thể chuyên dụng cho Cơ sở dữ liệu: **B+ Tree**.

#### 4. Cơ chế hoạt động & Giải quyết

```
                          [  50  ]                       <- Internal Node (Chỉ chứa Key chỉ mục)
                         /        \
               [ 20 | 30 ]        [ 60 | 70 ]           <- Internal Node
              /     |     \      /     |     \
  [10|15|20] <-> [22|25|30] <-> [55|60] <-> [65|70|80]   <- Leaf Nodes (Data thực tế + Linked List)
```

1. **Tách biệt Dẫn đường và Dữ liệu:**
    
    - **Internal Nodes:** CHỈ chứa Key chỉ mục dẫn đường, không chứa Payload. Nhờ đó, 1 Page $16\text{ KB}$ chứa được nhiều Key hơn gấp nhiều lần $\rightarrow$ Fan-out lớn hơn $\rightarrow$ **B+ Tree phẳng hơn (thấp hơn) B-Tree**.
        
    - **Leaf Nodes:** Chứa toàn bộ dữ liệu thực tế (Payload).
        
2. **Danh sách liên kết đôi (Doubly Linked List):**
    
    - Tất cả các Leaf Node được nối với nhau theo hàng ngang bằng danh sách liên kết đôi.
        
    - **Xử lý Range Scan:** Để tìm giá trị từ `20` đến `60`, B+ Tree dùng cây định vị phần tử `20` ở Leaf Node (mất $3$ Disk I/O), sau đó **duyệt tuần tự theo con trỏ ngang** sang phải để lấy tiếp các phần tử cho đến `60` mà không quay lại Root.
        


#### 5. So sánh bản chất cấu trúc: B-Tree vs. B+Tree

|**Đặc tính**|**B-Tree**|**B+Tree**|
|---|---|---|
|**Vị trí Data Payload**|Nằm ở **tất cả** các node (Internal Nodes và Leaf Nodes).|**Chỉ nằm ở Leaf Nodes**. Internal Nodes chỉ chứa **Key** và **Pointer** (địa chỉ con).|
|**Sức chứa của 1 Page/Block**|**Thấp**: Do chứa cả payload nên 1 node chứa được ít Key hơn.|**Rất cao**: Internal node chỉ chứa Key + Pointer, dẫn đến Fan-out (hệ số phân nhánh) lớn.|
|**Chiều cao cây (Height)**|Thường cao hơn B+Tree với cùng một lượng dữ liệu.|Thấp hơn B-Tree đáng kể (thường chỉ 3 - 4 tầng cho hàng triệu bản ghi).|
|**Liên kết giữa các Node lá**|Không có.|Có (thường là Doubly-Linked List liên kết các Page lá kế tiếp).|

#### 6. Chu trình xử lý chi tiết (Execution Flow) của từng loại

Giả sử hệ điều hành đọc/ghi theo Page (ví dụ: 8KB hoặc 16KB). Mỗi Node trong cây tương ứng đúng **1 Page** dữ liệu trên đĩa cứng.

Bài toán: **Truy vấn khoảng (Range Query)** `WHERE id BETWEEN 15 AND 45`.

##### A. Chu trình với B-Tree

```
                [ 30 (data) ]           <-- Internal Node (Root)
               /            \
    [ 10 (data)  20 (data) ]  [ 40 (data)  50 (data) ]  <-- Leaf Nodes
```

1. **Tìm điểm bắt đầu ($id = 15$):**
    
    - Đọc Root Page: So sánh $15 < 30$, theo con trỏ trái xuống Node con. (1 I/O)
        
    - Đọc Internal Page: Tìm trong node chứa $10$ và $20$. Phát hiện $15$ nằm giữa $10$ và $20$.
        
2. **Thu thập dữ liệu trong khoảng [15, 45]:**
    
    - Do payload nằm rải rác ở mọi node:
        
        - Muốn lấy bản ghi $20$: Lấy ngay tại node hiện tại.
            
        - Muốn lấy bản ghi $30$: Phải **truy vết ngược lên Node cha (Root)** để lấy payload của key 30.
            
        - Muốn lấy bản ghi $40$: Từ Root lại phải đi xuống nhánh con bên phải để load Node chứa $40$.
            
3. **Hệ quả I/O:** Luồng truy vấn phải di chuyển lên-xuống theo chiều dọc giữa các tầng của cây nhiều lần (In-order Traversal). Điều này gây ra hiện tượng **Random I/O** liên tục để load các Page cha/con vào Memory Buffer Pool.
    

##### B. Chu trình với B+Tree (Cách RDBMS như InnoDB/PostgreSQL vận hành)

```
                     [ 30 | 60 ]               <-- Internal Node (Root) - Chỉ chứa Key & Pointer
                    /     |     \
          [ 10 | 20 ]  [ 30 | 40 ]  [ 60 | 70 ] <-- Internal Nodes
               /           |           \
     [10|12|15] <---> [20|25|28] <---> [30|35|45] <---> [60|70|80]  <-- Leaf Nodes (Chứa ALL Data Payloads)
```

1. **Bước 1: Tìm điểm bắt đầu ($id = 15$) - Xử lý theo chiều dọc (Index Search)**
    
    - CPU nạp Root Page: So sánh $15 < 30 \rightarrow$ Chọn nhánh 1. (1 I/O)
        
    - CPU nạp Internal Page tiếp theo: So sánh $15$ nằm trong khoảng $10-20 \rightarrow$ Đẩy xuống Leaf Page chứa $15$. (1 I/O)
        
    - Nạp Leaf Page chứa $15$: Dùng Binary Search trong Page để định vị chính xác vị trí $15$.
        
    - _Kết thúc giai đoạn định vị:_ Bạn đã đứng đúng ở Node lá đầu tiên chứa mốc $15$. Chi phí: $H$ lần I/O ($H$ là chiều cao cây, thường là 3-4).
        
2. **Bước 2: Quét khoảng [15, 45] - Xử lý theo chiều ngang (Sequential Scan via Linked List)**
    
    - Đọc tuyến tính các bản ghi trong Leaf Page hiện tại từ vị trí $15$ cho đến hết Page ($15, 25, 28...$).
        
    - Khi chạm cuối Leaf Page:
        
        - **KHÔNG** đọc ngược lên Node cha.
            
        - **KHÔNG** chạy lại thuật toán tìm kiếm từ Root.
            
        - Thuật toán lấy trực tiếp địa chỉ **Next Page Pointer** nằm ở cuối cấu trúc Leaf Page đó.
            
    - CPU ra lệnh I/O nạp thẳng Leaf Page tiếp theo vào RAM via Prefetching/Sequential Read.
        
    - Đọc tiếp các bản ghi ($30, 35, 45$). Khi thấy $id = 45$, dừng tiến trình.
        

##### 7. Tại sao B+Tree tối ưu vượt trội trên đĩa cứng (Disk I/O)?

1. **Hệ số phân nhánh (Fan-out) cực cao:**
    
    - Giả sử 1 Page = 16KB. Key = 8 bytes, Pointer = 8 bytes.
        
    - Internal Node của **B+Tree** chỉ chứa Key + Pointer (16 bytes/cặp) $\rightarrow$ Một Page chứa được $\approx 1000$ keys.
        
    - Cây B+Tree 3 tầng có thể quản lý: $1000 \times 1000 \times 1000 = 1,000,000,000$ (1 tỷ) bản ghi.
        
    - Toàn bộ các tầng chỉ mục (Internal Nodes) có dung lượng rất nhỏ, RDBMS có thể **cache hoàn toàn 100% trên RAM (Buffer Pool)**. Nhờ đó, Bước 1 (định vị điểm bắt đầu) gần như tốn **0 chi phí Disk I/O**.
        
2. **Tối ưu hóa Disk Read-Ahead (Prefetching):**
    
    - Đĩa cứng (HDD) hay SSD đều đọc dữ liệu hiệu quả nhất khi đọc các khối liên tục (Sequential Read).
        
    - Con trỏ Linked list giữa các Leaf Node cho phép Storage Engine đưa ra tín hiệu cho Operating System thực hiện **Read-Ahead**: Khi ứng dụng đang đọc Leaf Page $N$, hệ điều hành tự động nạp trước Leaf Page $N+1$ và $N+2$ vào RAM.
        
3. **Loại bỏ hoàn toàn chi phí di chuyển con trỏ đĩa theo chiều dọc khi quét khoảng:**
    
    - B-Tree bắt buộc phải duyệt cây theo kiểu In-order Traversal (Lên cha - Xuống con - Lên cha).
        
    - B+Tree chuyển bài toán duyệt cây phức tạp thành bài toán **Đọc chuỗi liên kết đơn giản trên RAM/Disk** sau khi đã định vị xong Node lá đầu tiên.

#### 8. Tổng kết lại mô hình chuẩn xác

- Hiện tại, B-Tree tối ưu cho việc tìm kiếm đích xác 1 bản ghi (Point Lookup) khi Key và Payload đi liền nhau trên cả RAM lẫn File System. B+ Tree tối ưu vượt trội cho các Hệ quản trị cơ sở dữ liệu quan hệ (RDBMS) nhờ khả năng Range Scan và giữ tầng chỉ mục siêu nhỏ trên RAM.

- **Cả B-Tree và B+Tree:** Mỗi node đều gồm các bộ `(Key, Pointer)`.
    
- **B-Tree:**
    
    - Giữ Payload ở mọi Node $\rightarrow$ Fan-out thấp $\rightarrow$ Cây cao $\rightarrow$ Tốn nhiều I/O khi chạy trên Disk.
        
    - Phù hợp cho: Cấu trúc dữ liệu In-Memory (RAM), hoặc File System cần truy vấn trực tiếp thuộc tính file mà không phải theo Page.
        
- **B+Tree:**
    
    - Đẩy toàn bộ Payload xuống Node lá, giải phóng các Node trên chỉ làm nhiệm vụ điều hướng (Routing) $\rightarrow$ Fan-out cực cao $\rightarrow$ Cây rất lùn.
        
    - Giúp giữ toàn bộ tầng chỉ mục trên RAM $\rightarrow$ Tối ưu hóa Disk I/O xuống mức tối thiểu cho **Point Query**.
        
    - Bổ sung **Doubly-Linked List giữa các Leaf Node** $\rightarrow$ Giải quyết triệt để bài toán **Range Query** bằng Sequential Read thay vì Random I/O.

### CHUYÊN SÂU: DATABASE INDEXING VÀ CƠ CHẾ CHÈN PRIMARY KEY

#### Thao tác Chèn Primary Key vào B+ Tree Leaf Page

```
1. KHÓA TĂNG DẦN (Auto-increment / TSID):
   Page 1 [ 1 | 2 | 3 | 4 ] (Đầy)  --->  Cấp phát Page 2 mới [ 5 | ... ]
   (Ghi nối tiếp ở cuối, Zero Page Splitting, Tỷ lệ lấp đầy Page ~100%)

2. KHÓA NGẪU NHIÊN (UUIDv4):
   Page 1 [ 10 | 25 | 40 | 85 ] (Đầy)
   Chèn ID = 30 vào giữa  --->  BẮT BUỘC TÁCH PAGE (Page Splitting)
   Page 1 [ 10 | 25 ]  <--->  Page 2 mới [ 30 | 40 | 85 ]
   (Tốn Disk I/O, Phân mảnh đĩa, Tỷ lệ lấp đầy Page giảm xuống ~50%)
```

- **Khóa tăng dần (Auto-increment / TSID):**
    
    Dữ liệu mới luôn lớn hơn dữ liệu cũ. Thao tác chèn luôn nối tiếp vào cuối Page lá bên phải. Khi Page đầy, hệ thống chỉ cấp phát Page mới nối vào cuối. Tỷ lệ lấp đầy Page đạt $\approx 100\%$, zero phân mảnh đĩa, I/O tối thiểu.
    
- **Khóa ngẫu nhiên (UUIDv4):**
    
    Dữ liệu mới có giá trị ngẫu nhiên rơi vào giữa các Page hiện có. Khi chèn vào Page đã đầy, hệ thống bắt buộc thực hiện **Tách Page (Page Splitting)**: cấp phát Page mới, cắt $50\%$ dữ liệu sang Page mới, cập nhật con trỏ Node cha và con trỏ liên kết đôi.
    
    - _Hậu quả:_ Gây ra xung đợt I/O (I/O Storms), phân mảnh đĩa, và lãng phí $50\%$ bộ nhớ mỗi Page.
        
- **Quy tắc Leftmost Prefix trên Composite Index:**
    
    Nếu tạo Index `(A, B)`, B+ Tree sắp xếp theo `A` trước, rồi mới tới `B`. Do đó, câu lệnh `WHERE B = 10` không thể tận dụng Index này.
    

#### Ứng dụng thực tế

B+ Tree là cấu trúc Index chuẩn trong MySQL (InnoDB Engine), PostgreSQL, SQLite.

### GIAI ĐOẠN 5: Xử lý Chuỗi Ký tự $\rightarrow$ Trie (Prefix Tree)

#### 1. Vấn đề phát sinh

Khi tìm kiếm một từ trong từ điển $N$ phần tử, nếu dùng Red-Black Tree, độ phức tạp so sánh chuỗi độ dài $K$ là $O(K \cdot \log_2 N)$. Khi $N$ rất lớn, việc so sánh toàn bộ chuỗi ký tự tại mỗi Node tốn nhiều chi phí CPU.

#### 2. Mô hình & Cơ chế hoạt động

**Trie (Prefix Tree):** Chia nhỏ chuỗi thành từng ký tự đơn lẻ dọc theo đường đi từ Root. Các từ có chung tiền tố sẽ **dùng chung chuỗi Node ban đầu**.

```
               Root
             /      \
           'c'      'd'
           /          \
         'a'          'o'
         / \           \
       't' 'r'         'g'
        *   /           *
          't'
           *   (Lưu trữ các từ: "cat", "cart", "dog". Dấu * đánh dấu isEndOfWord = true)
```

#### 3. Cơ chế Tìm kiếm Chính xác vs. Gợi ý (Autocomplete)

- **Ký tự đánh dấu:** Mỗi Node chứa một cờ boolean `isEndOfWord` để xác định tại Node đó có tạo thành một từ hoàn chỉnh hay không.
    
- **Tìm kiếm chính xác (Exact Search):** Duyệt từng ký tự của chuỗi cần tìm. Khi duyệt hết chuỗi, kiểm tra `isEndOfWord == true`. Độ phức tạp: $O(K)$ (**hoàn toàn độc lập với số lượng từ $N$ trong hệ thống**).
    
- **Gợi ý / Tìm kiếm tiền tố (Autocomplete / Prefix Search):**
    
    Duyệt qua các Node tương ứng với tiền tố nhập vào (ví dụ nhập `"ca"` $\rightarrow$ duyệt tới Node `'a'`). Từ Node `'a'`, chạy giải thuật DFS/BFS thu thập toàn bộ các nhánh con cháu có `isEndOfWord == true` (`"cat"`, `"cart"`).
    

#### 4. Ứng dụng thực tế

Chức năng Gợi ý từ khóa (Autocomplete), Tìm kiếm tiền tố IP (Longest Prefix Match trong Router), Từ điển T9.

## CHƯƠNG 4: THAO TÁC XÓA VÀ THUẬT TOÁN DUYỆT CÂY

### 4.1. Thuật toán Xóa (Delete Operation) trên BST

Xóa một Node trên BST được chia thành 3 trường hợp:

- **Trường hợp 1: Node cần xóa là Node lá (0 con)**
    
    - _Giải pháp:_ Giải phóng bộ nhớ Node và gán con trỏ của Node cha trỏ tới nó thành `nullptr`.
        
- **Trường hợp 2: Node cần xóa có đúng 1 Node con**
    
    - _Giải pháp:_ Nối con trỏ của Node cha trực tiếp tới Node con của Node bị xóa, sau đó giải phóng Node bị xóa.
        
- **Trường hợp 3: Node cần xóa có đầy đủ 2 Node con**
    
    - _Giải pháp:_
        
        1. Tìm **In-order Successor** (Node nhỏ nhất ở cây con bên phải) hoặc **In-order Predecessor** (Node lớn nhất ở cây con bên trái).
            
        2. Ghi đè giá trị của In-order Successor vào Node cần xóa.
            
        3. Tiến hành xóa In-order Successor (Lúc này Node Successor rơi vào Trường hợp 1 hoặc Trường hợp 2).
            

### 4.2. Các Giải thuật Duyệt Cây (Tree Traversals)

```
        F
      /   \
     B     G
    / \     \
   A   D     I
```

#### A. Depth-First Search (DFS) - Duyệt theo chiều sâu (Dùng Call Stack / Đệ quy)

- **Pre-order (`Root -> Left -> Right`):** `F -> B -> A -> D -> G -> I`
    
    - _Ứng dụng:_ Copy toàn bộ cấu trúc cây, Serialize cây ra file.
        
- **In-order (`Left -> Root -> Right`):** `A -> B -> D -> F -> G -> I`
    
    - _Ứng dụng:_ Trích xuất toàn bộ dữ liệu trong BST theo thứ tự tăng dần.
        
- **Post-order (`Left -> Right -> Root`):** `A -> D -> B -> I -> G -> F`
    
    - _Ứng dụng:_ Giải phóng bộ nhớ cây (xóa từ lá lên gốc), tính toán cây biểu thức (AST).
        

#### B. Breadth-First Search (BFS / Level-order) - Duyệt theo chiều rộng (Dùng Queue)

- **Level-order:** `F -> B -> G -> A -> D -> I`
    
    - _Ứng dụng:_ Duyệt cây theo từng tầng từ trên xuống dưới, tìm đường đi ngắn nhất trên cây.
        

## CHƯƠNG 5: MA TRẬN TỔNG HỢP VÀ CÂY QUYẾT ĐỊNH (SYSTEM DESIGN GUIDE)

### 5.1. Bảng So sánh Tổng hợp Độ phức tạp và Tính chất Phần cứng

|**Cấu trúc dữ liệu**|**Search (Avg)**|**Search (Worst)**|**Insert (Avg)**|**Delete (Avg)**|**Tận dụng CPU Cache**|**Mức độ tiêu tốn Memory**|
|---|---|---|---|---|---|---|
|**Unsorted Array**|$O(N)$|$O(N)$|$O(1)$|$O(N)$|**Cực tốt**|Rất thấp (Không Overhead)|
|**Sorted Array**|$O(\log_2 N)$|$O(\log_2 N)$|$O(N)$|$O(N)$|**Cực tốt**|Rất thấp|
|**Linked List**|$O(N)$|$O(N)$|$O(1)^*$|$O(1)^*$|**Kém**|Trung bình (1 Pointer/Node)|
|**Hash Table**|$O(1)$|$O(N)$|$O(1)$|$O(1)$|**Khá**|Cao (Load Factor & Buckets)|
|**BST (Cây nhị phân)**|$O(\log_2 N)$|$O(N)$|$O(\log_2 N)$|$O(\log_2 N)$|**Kém**|Cao (2 Pointers + Overhead)|
|**Red-Black Tree**|$O(\log_2 N)$|$O(\log_2 N)$|$O(\log_2 N)$|$O(\log_2 N)$|**Kém**|Cao (2 Pointers + Parent + Color)|
|**B+ Tree**|$O(\log_M N)$|$O(\log_M N)$|$O(\log_M N)$|$O(\log_M N)$|**Tốt trên Page**|Tối ưu theo Block đĩa|
|**Trie**|$O(K)$|$O(K)$|$O(K)$|$O(K)$|**Kém**|Rất cao (Tùy thuộc Alphabet)|

> **(*):** Đạt $O(1)$ khi đã có sẵn con trỏ trỏ tới vị trí cần thao tác. $K$ là độ dài chuỗi ký tự.

### 5.2. Cây Quyết định Chọn Cấu trúc Dữ liệu

```
                                MỤC TIÊU THIẾT KẾ
                                        │
        ┌───────────────────────────────┼───────────────────────────────┐
        ▼                               ▼                               ▼
 Tốc độ Tra cứu Tuyệt đối        Cần Tìm kiếm theo Khoảng        Dữ liệu lớn lưu trữ trên đĩa
 (Point Lookup: Key = Val)       (Range Scan: WHERE age > 18)    (Database Index / File System)
        │                               │                               │
        ▼                               ▼                               ▼
   HASH TABLE                       RED-BLACK TREE                   B+ TREE
 (HashMap, Redis)                (TreeMap, std::map)             (MySQL InnoDB, PostgreSQL)
```

## CHƯƠNG 6: MÔ MÌNH TRIỂN KHAI TRÊN CÁC NGÔN NGỮ

|**Ngôn ngữ**|**Thư viện có sẵn (Tự động)**|**Cách triển khai thủ công**|
|---|---|---|
|**C++**|`std::map`, `std::set` (Red-Black Tree)|Khai báo `struct Node { T val; Node *left, *right; }`. Quản lý con trỏ hoặc dùng `std::unique_ptr`.|
|**Java**|`java.util.TreeMap`, `TreeSet` (Red-Black Tree)|Khai báo `class Node { T val; Node left, right; }`. JVM Garbage Collector tự động giải phóng bộ nhớ.|
|**PHP / Scripting**|Không có Tree trong Stdlib. (Mảng PHP là Ordered Hash Table).|Khai báo `class Node { public $value; public $left; public $right; }` hoặc dùng mảng con trỏ.|


## CHƯƠNG 7: `ARCHITECTURE DEEP-DIVE: INTERNAL STORAGE ENGINE & B+ TREE MECHANICS`.
### 1. Bản chất và Tác dụng của từng Thành phần trong Cây

Một mô hình cây lưu trữ đĩa (B-Tree / B+ Tree) được chia thành 4 thành phần cấu trúc bắt buộc. Không thành phần nào có thể bị loại bỏ mà không làm sụp đổ mô hình.

```
+-----------------------------------------------------------------------+
| [ Root Node ]     : Node đầu vào (Point of Entry), lưu trên RAM/Disk   |
| [ Internal Node ] : Node dẫn đường (Router), chứa Keys + Con trỏ child |
| [ Leaf Node ]     : Node tầng cuối, chứa Keys + Payload (+ Linked List)|
| [ Key ]           : Giá trị điểm mốc so sánh (Boundary Marker)        |
| [ Payload ]       : Dữ liệu bản ghi thực tế (Actual Table Row / Data)  |
+-----------------------------------------------------------------------+
```

|**Thành phần**|**Bản chất kỹ thuật**|**Tác dụng trong cấu trúc**|**Nếu loại bỏ thành phần này?**|
|---|---|---|---|
|**Root Node**|Node ở đỉnh cao nhất, là điểm khởi đầu duy nhất của cây.|Cung cấp địa chỉ Page đĩa cố định để CPU/Storage Engine nạp vào bộ nhớ đầu tiên khi bắt đầu truy vấn.|Không có điểm bắt đầu. CPU không biết nạp Page nào đầu tiên trên đĩa.|
|**Internal Node**|Các Node nằm giữa Root và Leaf.|Đóng vai trò là **Bộ định tuyến (Router)**. Chứa danh sách các Key mốc và con trỏ chỉ xuống các Page đĩa cấp dưới để chia không gian tìm kiếm thành $M$ nhánh.|Cây mất tính năng phân nhánh, suy biến thành 1 danh sách phẳng hoặc 1 Node khổng lồ. Mất khả năng giảm chiều cao $O(\log_M N)$.|
|**Leaf Node**|Các Node nằm ở tầng cuối cùng của cây.|Vùng lưu trữ đích. Nơi chứa dữ liệu thực tế (Payload) hoặc con trỏ trỏ tới bản ghi dữ liệu.|Hệ thống chỉ có chỉ mục dẫn đường mà không lưu được dữ liệu người dùng.|
|**Key**|Giá trị của trường dữ liệu làm chỉ mục (ví dụ: `id = 50`).|Làm ranh giới so sánh ($K_i \le x < K_{i+1}$) để CPU đưa ra quyết định rẽ trái hay rẽ phải.|CPU không có cơ sở toán học để so sánh và định tuyến tới Page tiếp theo.|
|**Payload**|Toàn bộ thông tin thực tế của bản ghi (`name`, `email`, `created_at`...).|Cung cấp giá trị thực tế mà câu lệnh `SELECT` yêu cầu lấy ra.|Cơ sở dữ liệu chỉ biết khóa tồn tại hay không chứ không lấy được thông tin chi tiết của bản ghi.|

## 2. Luồng thực thi chi tiết (Luồng di chuyển của CPU & Storage Engine)

Sự khác biệt cốt lõi về luồng thực thi giữa B-Tree và B+ Tree nằm ở **điểm dừng của thao tác tìm kiếm**:

```
B-TREE: Dừng ngay khi thấy Key ở BẤT KỲ tầng nào
[ Root (20, 50) ] ────(Gặp Key 50? Dừng & Lấy Payload ngay)
       │
[ Internal/Leaf ]

B+ TREE: BẮT BUỘC đi từ Root xuống tận Leaf Node
[ Root (20, 50) ] ────(Gặp Key 50? Chỉ dùng làm mốc rẽ)
       │
[ Internal (30, 50) ]
       │
[ Leaf Node ] ────────(Điểm dừng duy nhất, lấy Payload tại đây)
```

### Luồng tìm kiếm Điểm (Point Lookup: `WHERE id = 50`)

1. **Khởi tạo:** CPU đọc địa chỉ Root Page từ bộ nhớ hệ thống, nạp Root Page (16 KB) từ đĩa vào RAM.
    
2. **So sánh tại RAM:** CPU dùng Binary Search trên các Key trong Root Page để xác định khoảng chứa giá trị `50`.
    
3. **Phân nhánh (Branching):**
    
    - **B-Tree:** Nếu Key `50` nằm ngay tại Root Page, CPU đọc luôn Payload đi kèm tại Root Page đó và **kết thúc truy vấn ngay lập tức**. Nếu không có, đọc con trỏ $P_i$ trỏ đến địa chỉ Page con tiếp theo trên đĩa.
        
    - **B+ Tree:** Kể cả khi Key `50` xuất hiện ở Root Page, CPU **không dừng lại** mà chỉ dùng `50` làm mốc rẽ, đọc con trỏ $P_i$ trỏ xuống Internal Page cấp dưới.
        
4. **Lặp lại:** CPU tiếp tục nạp Internal Page tiếp theo vào RAM, so sánh và rẽ nhánh.
    
5. **Kết thúc (Tại Leaf Node):**
    
    - Trong **B+ Tree**, thuật toán luôn kết thúc tại Leaf Node. CPU giải mã Payload tại Leaf Node và trả kết quả cho ứng dụng.
        

## 3. Bản chất bài toán Range Query: Sự thất bại của B-Tree và Giải pháp B+ Tree

Hiểu biết của bạn về cơ chế Range Query trên B-Tree là **hoàn toàn chính xác**.

### B-Tree xử lý Range Query (`WHERE age BETWEEN 20 AND 80`)

Do B-Tree lưu Payload ở tất cả các tầng và các Leaf Node **không có liên kết ngang**, B-Tree bắt buộc phải thực hiện giải thuật **Duyệt Trung Thứ (In-order Traversal)** đệ quy qua cây:

```
                  [ Node Cha (Internal): 50 ]
                         /          \
                        /            \
    [ Node Lá Con: 20 | 30 ]        [ Node Lá Con: 60 | 80 ]
```

- **Bước 1:** Tìm Key `20` ở Node Lá trái.
    
- **Bước 2:** Đọc `30` ở Node Lá trái.
    
- **Bước 3:** Đã hết Node Lá trái. Để tìm phần tử tiếp theo, CPU **bắt buộc phải truy ngược (Backtrack) lên Node Cha** để đọc Key `50`.
    
- **Bước 4:** Từ Node Cha, CPU rẽ xuống Node Lá phải để đọc tiếp `60` và `80`.
    

> **Hậu quả:** Thao tác "leo ngược lên cha rồi hạ xuống con" buộc CPU phải phát lệnh đọc lại các Page đĩa nằm ở tầng trên. Dữ liệu trên đĩa bị đọc ngẫu nhiên (Random Disk I/O) lặp đi lặp lại nhiều lần, làm trễ hệ thống.

### B+ Tree giải quyết triệt để bằng Doubly Linked List

B+ Tree thêm một danh sách liên kết đôi trỏ ngang giữa tất cả các Page ở tầng Lá (Leaf Pages).

```
Tầng Internal:        [ 50 ]
                       /  \
Tầng Leaf:    [20 | 30] <----> [50 | 60 | 80]
```

#### Luồng xử lý Range Query trên B+ Tree:

1. **Định vị điểm đầu ($O(\log_M N)$):** CPU đi từ Root xuống tầng Leaf để tìm phần tử đầu tiên thỏa mãn điều kiện (`id = 20`). Mất $3 - 4$ lần Disk I/O.
    
2. **Quét ngang ($O(K)$):** Sau khi tìm thấy Page chứa `20`, CPU **không quay lại Node cha nữa**. Nó chỉ việc đi theo con trỏ `next` của danh sách liên kết đôi để nạp tiếp Page lá kế tiếp vào RAM.
    
3. **Kết thúc:** CPU ngừng đọc khi gặp giá trị vượt quá `80`.
    

#### Tác dụng khi Cập nhật (Update / Insert / Delete) trên Leaf Node B+ Tree:

- Khi thêm/xóa 1 bản ghi ở Leaf Page, nếu Page chưa đầy, CPU chỉ cần ghi trực tiếp vào Page đó trên đĩa.
    
- Nếu Leaf Page bị đầy, thao tác **Tách Page (Page Split)** diễn ra:
    
    1. Cấp phát Page lá mới trên đĩa.
        
    2. Chuyển $50\%$ dữ liệu sang Page mới.
        
    3. Cập nhật lại 2 con trỏ `next` và `prev` của các Leaf Page liền kề.
        
    4. Đẩy 1 Key chỉ mục duy nhất lên Node cha.
        

Chi phí cập nhật con trỏ danh sách liên kết đôi là $O(1)$ thao tác trên bộ nhớ RAM trước khi Flush Page xuống đĩa.

## 4. Payload là gì và Vai trò trong Hệ thống

### Định nghĩa Payload

**Payload** là toàn bộ tập hợp dữ liệu hữu ích thực tế của bản ghi (Record Data) mà ứng dụng người dùng cần lưu trữ và truy xuất, khác với các dữ liệu quản lý cấu trúc (Key, Pointer, Page Header).

### Payload chứa gì trong Cơ sở dữ liệu?

Cấu trúc Payload phụ thuộc vào loại Index được định nghĩa trong Storage Engine (như MySQL InnoDB):

#### 1. Clustered Index (Chỉ mục Cụm - Thường là Primary Key Index)

Payload chính là **toàn bộ các cột dữ liệu của một dòng trong Bảng (Table Row)**.

```
+-----------------------------------------------------------------------------------------+
| KEY (Primary Key) | PAYLOAD (Dữ liệu thực tế của toàn bộ Row)                           |
+-------------------+---------------------------------------------------------------------+
| id = 1001         | name = "Nguyen Van A", email = "a@gmail.com", balance = 5000.00 ... |
+-----------------------------------------------------------------------------------------+
```

#### 2. Secondary Index (Chỉ mục Phụ - Ví dụ Index trên cột `email`)

Payload **không chứa toàn bộ Row**, mà chứa **giá trị của Primary Key**.

```
+-----------------------------------------------------------------------------------------+
| KEY (Secondary)   | PAYLOAD (Chỉ chứa Primary Key để tra cứu ngược)                     |
+-------------------+---------------------------------------------------------------------+
| email = a@gmail   | id = 1001                                                           |
+-----------------------------------------------------------------------------------------+
```

### Tác dụng của Payload

Không có Payload, cây chỉ đóng vai trò là một **Tập hợp tồn tại (Existence Set)** - tức là chỉ kiểm tra được giá trị $X$ có nằm trong hệ thống hay không. Payload chính là mục đích cuối cùng của mọi thao tác lưu trữ CSDLN: lưu giữ và trả về thông tin chi tiết cho ứng dụng.