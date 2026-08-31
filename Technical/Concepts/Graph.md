# TÀI LIỆU CẤU TRÚC DỮ LIỆU ĐỒ THỊ (GRAPH DATA STRUCTURE)

**Mốc thời gian cập nhật & kiểm chứng:** Tháng 07/2026

## Phần 1: Nguồn gốc Bài toán & Tiến trình Phát triển

Để hiểu bản chất của Graph, cần đi từ sự thất bại của các cấu trúc dữ liệu tuyến tính và phân cấp khi giải quyết các bài toán kết nối trong thực tế.

```
Array (Mảng 1D) ──► Matrix (Mảng 2D) ──► Hash Table ──► Tree (Cây) ──► Graph (Đồ thị)
  [Tuyến tính]      [Ma trận cố định]   [Tra cứu Key]   [Phân cấp]     [Mạng lưới]
```

### 1. Sự hạn chế của Array (Mảng 1D)

- **Bản chất:** Lưu trữ danh sách phần tử liên tiếp nhau trong bộ nhớ.
    
- **Hạn chế:** Array chỉ biểu diễn dữ liệu theo thứ tự trước - sau (tuyến tính). Trong thực tế, mối quan hệ giữa các thực thể không nằm trên một đường thẳng. Một người có thể có nhiều người bạn, một chuyến bay có thể nối đến nhiều thành phố. Array không thể biểu diễn trực tiếp các mối quan hệ đa chiều này nếu không dùng các chỉ số trung gian phức tạp.
    

### 2. Sự thất bại của Matrix (Mảng 2D) khi dữ liệu thưa

- **Bản chất:** Dùng bảng 2 chiều kích thước $V \times V$ ($V$ là số lượng thực thể) để biểu diễn kết nối.
    
- **Hạn chế:** Mối quan hệ trong thế giới thực có tính chất **thưa (sparse)**.
    
    - _Ví dụ:_ Một mạng xã hội có 10,000 người dùng. Nếu dùng Ma trận 2D, hệ thống tạo $10,000 \times 10,000 = 100,000,000$ ô nhớ. Nhưng trung bình một người chỉ có 100 người bạn. Số ô chứa giá trị kết nối thực tế chỉ là $10,000 \times 100 = 1,000,000$ ô.
        
    - $99\%$ dung lượng bộ nhớ bị lãng phí để lưu trữ các giá trị $0$ (không có kết nối).
        

### 3. Hạn chế của Hash Table (Bảng băm)

- **Bản chất:** Lưu cặp Key-Value, cho tốc độ truy xuất $O(1)$.
    
- **Hạn chế:** Hash Table hoàn toàn **không có khái niệm về cấu trúc không gian (Topology-unaware)**. Hash Table biết $A$ có tồn tại hay không, nhưng không thể trả lời các câu hỏi về chuỗi liên kết nhiều chặng như: _"Tìm đường đi từ A đến C thông qua B"_ hoặc _"Tìm khoảng cách ngắn nhất giữa hai điểm"_.
    

### 4. Ràng buộc khắt khe của Tree (Cây)

- **Bản chất:** Biểu diễn cấu trúc phân cấp (Cha - Con).
    
- **Hạn chế:** Cây đặt ra 3 quy tắc cố định:
    
    1. Mỗi nút con chỉ có duy nhất **1 nút cha (Single Parent)**.
        
    2. Không được phép có **chu trình (Acyclic)** - tức là không có đường đi vòng lặp lại nút cũ.
        
    3. Số lượng cạnh luôn cố định bằng số nút trừ 1 ($E = V - 1$).
        
- **Thực tế:** Nhiều hệ thống vi phạm cả 3 quy tắc này. Một lớp trong lập trình có thể triển khai nhiều Interface (Đa cha); một luồng công việc có thể quay lại bước cũ (Có chu trình); mạng giao thông có số lượng đường nối bất kỳ.
    

### 5. Sự ra đời của Graph

Graph ra đời như một mô hình tổng quát nhất để giải quyết toàn bộ các hạn chế trên. Graph đóng gói các mối quan hệ phi tuyến tính, phi phân cấp, cho phép một nút có nhiều liên kết, có hoặc không có hướng, có hoặc không có chu trình.

## Phần 2: Các Khái niệm Cốt lõi & Phân loại Đồ thị

Đồ thị được ký hiệu toán học là $G = (V, E)$.

- **$V$ (Vertex / Node):** Tập hợp các nút (đại diện cho đối tượng: User, Thành phố, Server, File code).
    
- **$E$ (Edge):** Tập hợp các cạnh (đại diện cho mối quan hệ: Bạn bè, Đường đi, Kết nối mạng, Dependency).
    

```
Vô hướng (Undirected)         Có hướng (Directed)           Có trọng số (Weighted)
    A ─── B                       A ───► B                      A ───(10km)───► B
(Đi được 2 chiều)             (Chỉ đi từ A sang B)          (Có chi phí/khoảng cách)
```

### 1. Đồ thị Vô hướng (Undirected Graph) vs Có hướng (Directed Graph)

- **Vô hướng:** Cạnh kết nối giữa $A$ và $B$ không phân biệt chiều ($A - B$ đồng nghĩa với $B - A$).
    
    - _Ứng dụng:_ Mạng bạn bè trên Facebook (Khi $A$ là bạn $B$, $B$ tự động là bạn $A$).
        
- **Có hướng:** Cạnh $A \rightarrow B$ chỉ cho phép đi từ $A$ đến $B$.
    
    - _Ứng dụng:_ Hệ thống Follow trên X/Twitter ($A$ follow $B$ không bắt buộc $B$ follow $A$), luồng chuyển tiền ngân hàng.
        

### 2. Đồ thị Không trọng số (Unweighted) vs Có trọng số (Weighted)

- **Không trọng số:** Mọi cạnh có vai trò và chi phí ngang nhau.
    
- **Có trọng số:** Mỗi cạnh được gán một giá trị số (Weight) đại diện cho khoảng cách (km), độ trễ mạng (ms), hoặc chi phí tiền tệ.
    
    - _Ứng dụng:_ Bản đồ Google Maps (nút là giao lộ, cạnh là đoạn đường kèm độ dài/thời gian di chuyển).
        

### 3. Đồ thị Có chu trình (Cyclic) vs Không chu trình (Acyclic)

- **Chu trình (Cycle):** Tồn tại một đường đi bắt đầu từ nút $A$, qua một số nút khác và quay trở lại đúng nút $A$.
    
- **DAG (Directed Acyclic Graph - Đồ thị có hướng không chu trình):** Đồ thị có hướng và không chứa bất kỳ chu trình nào.
    
    - _Ứng dụng cốt lõi:_ Quản lý tiến trình build code (Maven, Gradle), Lịch sử Commit của Git, Hệ thống Dependency Injection trong Spring/NestJS.
        

## Phần 3: Biểu diễn Đồ thị trong Bộ nhớ RAM & Thực tế Phần cứng

Việc lựa chọn cách cấu trúc bộ nhớ quyết định trực tiếp đến hiệu năng và mức độ tiêu tốn tài nguyên của ứng dụng.

```
                      CÁCH LƯU TRỮ TRONG RAM
                               │
         ┌─────────────────────┴─────────────────────┐
         ▼                                           ▼
Adjacency Matrix                            Adjacency List
(Ma trận nút liên kết - Mảng 2D)            (Danh sách nút liên kết - Dynamic Array/Hash)
• Bộ nhớ: O(V²)                             • Bộ nhớ: O(V + E)
• Tốc độ tra cạnh: O(1)                     • Tốc độ tra cạnh: O(degree)
```

### 1. Adjacency Matrix (Ma trận nút liên kết)

Sử dụng mảng 2 chiều kích thước $V \times V$. Ô `matrix[i][j] = 1` nếu có cạnh từ nút `i` đến nút `j`, ngược lại bằng `0`.

#### Bảng độ phức tạp & Giải thích công thức:

- **Bộ nhớ: $O(V^2)$**
    
    - _Giải thích:_ Bất kể đồ thị có bao nhiêu cạnh, ma trận luôn phải khởi tạo đủ $V$ hàng và $V$ cột.
        
    - _Tính toán cụ thể:_ Với $V = 50,000$ nút, số ô nhớ cần dùng là $50,000 \times 50,000 = 2,500,000,000$ ô. Nếu mỗi ô dùng 1 byte, hệ thống tốn $\approx 2.5 \text{ GB RAM}$ chỉ để lưu khung ma trận.
        
- **Tra cứu cạnh (Check Edge $A \rightarrow B$): $O(1)$**
    
    - _Giải thích:_ Lấy trực tiếp giá trị tại chỉ số `matrix[A][B]`, không cần qua vòng lặp.
        
- **Duyệt nút liên kết: $O(V)$**
    
    - _Giải thích:_ Để biết nút $A$ nối với những nút nào, phải duyệt qua toàn bộ $V$ cột trên hàng $A$.
        

### 2. Adjacency List (Danh sách liên kết)

Mỗi nút giữ một danh sách (ArrayList, LinkedList hoặc HashSet) chứa các nút liên kết trực tiếp với nó.

Java

```
// Biểu diễn Adjacency List phổ biến trong Java
Map<Integer, List<Integer>> graph = new HashMap<>();
graph.put(0, Arrays.asList(1, 2)); // Nút 0 nối với 1 và 2
graph.put(1, Arrays.asList(2));    // Nút 1 nối với 2
```

#### Bảng độ phức tạp & Giải thích công thức:

- **Bộ nhớ: $O(V + E)$**
    
    - _Giải thích:_ $V$ đại diện cho mảng/hashmap chứa $V$ nút. $E$ đại diện cho tổng số phần tử nằm trong các danh sách con cộng lại (mỗi cạnh được lưu đúng 1 lần nếu có hướng, 2 lần nếu vô hướng).
        
    - _Tính toán cụ thể:_ Với $V = 50,000$ nút và $E = 100,000$ cạnh. Số ô nhớ cần lưu là $50,000 + 100,000 = 150,000$ ô. So với 2.5 tỷ ô của Ma trận liên kết, Danh sách liên kết tiết kiệm bộ nhớ gấp hơn 16,000 lần.
        
- **Tra cứu cạnh (Check Edge $A \rightarrow B$): $O(\text{degree}(A))$**
    
    - _Giải thích:_ `degree(A)` là số lượng nút liên kết của $A$. Hệ thống phải duyệt qua danh sách con của $A$ để tìm $B$. Nếu danh sách con dùng `HashSet`, độ phức tạp giảm xuống $O(1)$.
        
- **Duyệt toàn bộ đồ thị: $O(V + E)$**
    
    - _Giải thích:_ Duyệt qua $V$ nút và đi qua tổng cộng $E$ cạnh.
        

### 3. Thực tế Phần cứng (Production Hardware Reality)

Trong các tài liệu lý thuyết, Adjacency List bằng `HashMap<Node, List<Node>>` được coi là tối ưu. Tuy nhiên, ở cấp độ Kỹ sư Hệ thống, cách triển khai này gặp phải vấn đề về phần cứng CPU.

#### Hiện tượng Pointer Chasing & CPU Cache Miss

- **Bản chất phần cứng:** CPU không đọc dữ liệu từng byte lẻ từ RAM. Mỗi khi truy nhập RAM, CPU nạp một khối dữ liệu liên kết dung lượng **64 Bytes** gọi là **Cache Line** vào tầng bộ nhớ đệm (L1/L2/L3 Cache).
    
    - Tốc độ đọc L1 Cache: $\approx 1 \text{ nanosecond}$.
        
    - Tốc độ đọc RAM (DRAM): $\approx 60 - 100 \text{ nanoseconds}$ (chậm hơn $\approx 100$ lần).
        
- **Vấn đề của Hash Table / Con trỏ:**
    
    Khi dùng `HashMap` hoặc `LinkedList`, mỗi Node là một Object nằm ở các địa chỉ ngẫu nhiên, phân tán trên bộ nhớ Heap. Việc duyệt từ Node này sang Node khác buộc CPU phải lần theo các con trỏ (Pointer Chasing) nhảy qua các địa chỉ RAM khác nhau. Kết quả: Khối 64 bytes nạp vào Cache không chứa dữ liệu của Node tiếp theo $\rightarrow$ **CPU Cache Miss**. CPU phải dừng chờ nạp dữ liệu từ RAM, làm giảm hiệu năng thực tế.
    

```
Dữ liệu phân tán (Object/Pointer) ──► Nhảy địa chỉ RAM ngẫu nhiên ──► Cache Miss ──► CPU chờ DRAM
Dữ liệu liên tục (Contiguous Array) ──► Nạp 1 Cache Line (64B) ──► Cache Hit ──► CPU xử lý tức thì
```

#### Giải pháp Tối ưu: Compressed Sparse Row (CSR)

Đối với các hệ thống cần xử lý đồ thị lớn ở tốc độ cao (Game Engine, Routing Switch, Compiler, AI Graph Engine), dữ liệu được phẳng hóa (flatten) thành **các mảng 1D liên tục trên bộ nhớ** để tận dụng tối đa CPU Cache Line.

Một cấu trúc CSR tiêu chuẩn nén toàn bộ đồ thị vào 2 mảng:

1. `offsets` (kích thước $V + 1$): Lưu vị trí bắt đầu danh sách nút liên kết trực tiếp của từng nút.
    
2. `edges` (kích thước $E$): Gom toàn bộ các nút liên kết trực tiếp nằm liên tiếp nhau trên một mảng duy nhất.
    

```
Ví dụ Đồ thị có hướng:
Nút 0 liên kết với: [1, 2]
Nút 1 liên kết với: [2]
Nút 2 không liên kết với ai.

Chuyển thành CSR:
offsets = [0, 2, 3, 3]  // Nút 0 bắt đầu từ index 0; Nút 1 từ index 2; Nút 2 từ index 3
edges   = [1, 2, 2]     // Mảng liên tục chứa toàn bộ các nút liên kết trực tiếp
```

#### Giải mã mảng `edges` và Chỉ số (Index) thực tế

Mảng `edges` gom toàn bộ danh sách nút liên kết của các Node vào một mảng duy nhất.

Hãy nhìn vào chỉ số (Index) vật lý trong RAM của mảng `edges = [1, 2, 2]`:

| **Chỉ số trong mảng edges (Index)** | **0**  | **1**  | **2**  |
| ----------------------------------- | ------ | ------ | ------ |
| **Giá trị lưu trong ô nhớ**         | `1`    | `2`    | `2`    |
| **Thuộc về Node nào?**              | Node 0 | Node 0 | Node 1 |

- **Danh sách nút liên kết của Node 0** gồm 2 phần tử: `1` và `2`. Hai phần tử này chiếm **index 0** và **index 1** trong mảng `edges`.
    
- **Danh sách nút liên kết của Node 1** gồm 1 phần tử: `2`. Do index 0 và index 1 đã bị Node 0 chiếm mất, phần tử của Node 1 buộc phải nằm ở vị trí tiếp theo là **index 2**.
    

> **Tại sao Node 1 không thể ở index 1?**
> 
> Nếu Node 1 bắt đầu từ index 1, giá trị `2` của Node 1 sẽ ghi đè lên vị trí `edges[1]` (vốn đang lưu nút liên kết `2` của Node 0).

#### Bản chất và Công thức của mảng `offsets`

Mảng `offsets` sinh ra để giải quyết câu hỏi: **"Danh sách nút liên kết của Node $i$ bắt đầu từ index nào và kết thúc ở index nào trong mảng `edges`?"**

### Công thức khởi tạo `offsets`:

1. **Kích thước mảng `offsets`:** Luôn là $V + 1$ (với $V$ là tổng số Node). Trong ví dụ có 3 Node ($V=3$), mảng `offsets` có $3 + 1 = 4$ phần tử.
    
2. **`offsets[0] = 0`** (Luôn bằng 0 vì Node 0 luôn bắt đầu ở đầu mảng `edges`).
    
3. **`offsets[i] = offsets[i - 1] + (số lượng cạnh của Node i - 1)`**
    
4. Phần tử cuối cùng `offsets[V]` lưu **tổng số lượng cạnh của toàn đồ thị**.
    

### Áp dụng tính toán từng bước:

- `offsets[0] = 0` _(Node 0 bắt đầu từ index 0)_
    
- `offsets[1] = offsets[0] + (số cạnh Node 0) = 0 + 2 = 2` _(Node 1 bắt đầu từ index 2)_
    
- `offsets[2] = offsets[1] + (số cạnh Node 1) = 2 + 1 = 3` _(Node 2 bắt đầu từ index 3)_
    
- `offsets[3] = offsets[2] + (số cạnh Node 2) = 3 + 0 = 3` _(Chỉ số chặn cuối - tổng số cạnh = 3)_
    

Kết quả: `offsets = [0, 2, 3, 3]`

#### Công thức TRUY XUẤT dữ liệu từ CSR

Khi chương trình muốn lấy tất cả các nút liên kết của **Node $i$**, thuật toán sẽ đọc mảng `edges` trong khoảng:

$$\text{Từ Index } \mathbf{offsets[i]} \quad \text{đến trước Index } \mathbf{offsets[i+1]}$$

Plaintext

```
Node i ──► đọc edges[ offsets[i] ... offsets[i+1] - 1 ]
```

Áp dụng lại vào mảng `offsets = [0, 2, 3, 3]` và `edges = [1, 2, 2]`:

- **Muốn lấy danh sách nút liên kết của Node 0 ($i = 0$):**
    
    - Đọc từ `offsets[0]` ($= 0$) đến trước `offsets[1]` ($= 2$).
        
    - Lấy `edges[0]` và `edges[1]` $\rightarrow$ Kết quả: **`[1, 2]`**.
        
- **Muốn lấy danh sách nút liên kết của Node 1 ($i = 1$):**
    
    - Đọc từ `offsets[1]` ($= 2$) đến trước `offsets[2]` ($= 3$).
        
    - Lấy `edges[2]` $\rightarrow$ Kết quả: **`[2]`**.
        
- **Muốn lấy danh sách nút liên kết của Node 2 ($i = 2$):**
    
    - Đọc từ `offsets[2]` ($= 3$) đến trước `offsets[3]` ($= 3$).
        
    - Khoảng đọc rỗng ($3$ đến $3$) $\rightarrow$ Kết quả: **`[]`** (Node 2 không có nút liên kết nào).
    
- **Ưu điểm:** Loại bỏ hoàn toàn con trỏ. Dữ liệu mảng `edges` nằm liên tiếp trên RAM, nạp vào CPU Cache cho tốc độ đọc và duyệt cực nhanh.
    
- **Đánh đổi (Trade-off):** Mảng liên tục khó thay đổi kích thước. CSR tối ưu tuyệt đối cho tác vụ **Đọc / Duyệt (Read-Heavy)** nhưng không phù hợp cho đồ thị thêm/xóa cạnh liên tục (Write-Heavy).
    

## Phần 4: Thuật toán Cốt lõi & Ứng dụng Bắt buộc

```
                      THUẬT TOÁN ĐỒ THỊ CƠ BẢN
                               │
         ┌─────────────────────┼─────────────────────┐
         ▼                     ▼                     ▼
        BFS                   DFS             Topological Sort
 (Breadth-First)          (Depth-First)        (Sắp xếp phụ thuộc)
 • Dùng Queue          • Dùng Stack/Recursion  • Áp dụng trên DAG
 • Tìm đường ngắn nhất • Phát hiện chu trình   • Xác định thứ tự
   (Không trọng số)      (Cycle Detection)       thực thi
```

### 1. BFS (Breadth-First Search - Duyệt theo chiều rộng)

- **Nguyên lý:** Duyệt theo từng lớp (Level-by-level). Thăm tất cả các nút liên kết trực tiếp (cách 1 chặng), sau đó mới chuyển sang các nút cách 2 chặng.
    
- **Cấu trúc dữ liệu sử dụng:** **Queue (Hàng đợi - FIFO)**.
    
- **Độ phức tạp thời gian:** $O(V + E)$.
    
- **Bài toán giải quyết:** Tìm đường đi ngắn nhất (số chặng ít nhất) trên đồ thị **không có trọng số**.
    
    - _Ví dụ:_ Tìm danh sách "Bạn chung" hoặc gợi ý kết bạn $N$ chặng trên mạng xã hội.
        

### 2. DFS (Depth-First Search - Duyệt theo chiều sâu)

- **Nguyên lý:** Đi sâu nhất có thể theo một nhánh cho đến khi gặp ngõ tắp, sau đó quay lui (Backtrack) lại nút gần nhất để khám phá nhánh khác.
    
- **Cấu trúc dữ liệu sử dụng:** **Stack (Năn xếp - LIFO)** hoặc hàm Đệ quy (Recursion Call Stack).
    
- **Độ phức tạp thời gian:** $O(V + E)$.
    
- **Bài toán giải quyết:**
    
    - **Phát hiện chu trình (Cycle Detection):** Kiểm tra xem hệ thống có bị lặp vô tận hay không.
        
    - **Tìm đường đi trong mê cung:** Khám phá toàn bộ cấu trúc đồ thị.
        

### 3. Topological Sort (Sắp xếp Topo)

- **Bản chất:** Áp dụng trên đồ thị **DAG**. Thuật toán biến đổi đồ thị thành một danh sách tuyến tính các nút sao cho: Nếu có cạnh $A \rightarrow B$, thì $A$ luôn luôn đứng trước $B$ trong danh sách kết quả.
    
- **Cách hoạt động (Thuật toán Kahn - Dựa trên BFS):**
    
    1. Tính **In-degree** (số lượng cạnh hướng vào) của tất cả các nút.
        
    2. Đưa tất cả các nút có $\text{In-degree} = 0$ vào Queue.
        
    3. Rút từng nút ra khỏi Queue, giảm In-degree của các nút liên kết với nó đi 1.
        
    4. Nếu nút liên kết trực tiếp có $\text{In-degree}$ giảm về 0, tiếp tục đưa vào Queue.
        
    5. Nếu số phần tử lấy ra bằng $V$, thu được thứ tự Topo. Nếu nhỏ hơn $V$, đồ thị có chu trình (bị lỗi lặp phụ thuộc).
        
- **Bài toán giải quyết:** Xác định thứ tự biên dịch dự án phần mềm, thứ tự chạy các Job trong Data Pipeline.
    

## Phần 5: Ứng dụng Thực tế trong Hệ thống Backend

Dưới đây là cách Graph giải quyết trực tiếp các bài toán thiết kế hệ thống.

### 1. Hệ thống Quản lý Mã nguồn (Git)

Mô hình lưu trữ của Git bản chất là một đồ thị **DAG (Directed Acyclic Graph)**.

- **Node:** Mỗi Object Commit (chứa Hash, Author, Tree State).
    
- **Edge:** Con trỏ từ Commit con hướng về Commit cha (Parent Commit).
    
- **Ứng dụng:** Khi thực hiện `git merge`, Git dùng thuật toán đồ thị để tìm nút cha chung gần nhất (**Lowest Common Ancestor - LCA**) của 2 nhánh, từ đó tính toán các khác biệt (Diff) để tự động hợp nhất code.
    

```
Commit A ◄─── Commit B ◄─── Commit C (Main Branch)
                  ▲
                  └─── Commit D (Feature Branch)
(Commit B là LCA - Lowest Common Ancestor dùng để Merge)
```

### 2. Dependency Injection (DI Frameworks)

Các Framework Backend như Spring Boot (Java), NestJS (TypeScript), hay Wire (Go) xây dựng cây phụ thuộc giữa các Service dưới dạng Graph.

- **Node:** Các Class / Bean / Component.
    
- **Edge:** Phụ thuộc (Ví dụ: `UserController` cần `UserService` $\rightarrow$ Cạnh `UserController` $\rightarrow$ `UserService`).
    
- **Vấn đề giải quyết:** Khi khởi động ứng dụng, Framework chạy thuật toán **DFS** hoặc **Topological Sort**:
    
    - Nếu phát hiện chu trình ($A \rightarrow B \rightarrow C \rightarrow A$), Framework lập tức ngắt tiến trình và quăng lỗi `Circular Dependency Error`.
        
    - Nếu không có chu trình, thứ tự khởi tạo đối tượng sẽ được sắp xếp chuẩn xác từ gốc lên nút.
        

### 3. Cơ sở dữ liệu Quan hệ (RDBMS) vs Cơ sở dữ liệu Đồ thị (Graph DB)

|**Tiêu chí**|**Relational Database (MySQL, PostgreSQL)**|**Graph Database (Neo4j, Memgraph)**|
|---|---|---|
|**Biểu diễn quan hệ**|Dùng bảng trung gian (Junction Table) & Khóa ngoại (Foreign Key).|Lưu trực tiếp con trỏ kết nối giữa các Node (**Index-Free Adjacency**).|
|**Truy vấn đa chặng (Multi-hop)**|Sử dụng nhiều câu lệnh `JOIN`. Chi phí tăng theo **cấp số nhân** dựa trên kích thước bảng.|Duyệt trực tiếp theo con trỏ. Chi phí chỉ phụ thuộc vào **số lượng liên kết cục bộ**, không phụ thuộc tổng dung lượng DB.|
|**Trường hợp sử dụng**|Dữ liệu dạng bảng ngăn nắp, giao dịch tài chính ACID (Banking, E-commerce orders).|Mạng xã hội, Hệ thống phát hiện gian lận (Fraud Detection), Hệ thống gợi ý (Recommendation System).|

## Phần 6: Hệ sinh thái Công cụ & Lựa chọn Thực thi (Mốc 2026)

Đối với Kỹ sư Backend, không cần thiết phải tự viết lại toàn bộ các thuật toán đồ thị phức tạp từ đầu cho mọi bài toán, ngoại trừ các trường hợp làm việc ở tầng Core Engine.

```
                      LỰA CHỌN CÔNG NGHỆ GRAPH
                               │
     ┌─────────────────────────┼─────────────────────────┐
     ▼                         ▼                         ▼
Tự cài đặt Code          Thư viện Chuyên dụng       Graph Database
(In-Memory Code)         (Dedicated Libraries)    (Database Engine)
• Java: Map/List         • C++: Boost.Graph       • Neo4j (Cypher Query)
• Go: Struct/Slice       • Python: NetworkX       • Memgraph (In-memory)
• Phù hợp: Logic         • Java: JGraphT          • Phù hợp: Dữ liệu quan
  nghiệp vụ nhỏ/vừa      • Phù hợp: Tính toán       hệ phức tạp, quy mô lớn
                           thuật toán sâu
```

### Bảng định hướng quyết định cho Kỹ sư Backend:

| **Yêu cầu bài toán**                                                                                            | **Giải pháp kỹ thuật khuyến nghị**                                                    | **Lý do lựa chọn**                                                                                       |
| --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| Kiểm tra phân quyền truy cập (RBAC / ABAC), Phân cấp menu danh mục.                                             | **Tự viết cấu trúc bằng ngôn ngữ đang dùng** (`Map<ID, List<ID>>`).                   | Đơn giản, không phát sinh chi phí hạ tầng, dữ liệu nằm gọn trong bộ nhớ RAM của Service.                 |
| Biên dịch quy trình, Tính toán đường đi ngắn nhất nội bộ, Xử lý bài toán tối ưu.                                | **Sử dụng thư viện chuẩn** (Java: `JGraphT`, C++: `Boost.Graph`, Python: `NetworkX`). | Thư viện đã tối ưu hóa cấu trúc dữ liệu và xử lý chính xác các trường hợp biên (Edge cases).             |
| Hệ thống phát hiện gian lận tín dụng (Tìm nhóm tài khoản chuyển tiền vòng tròn), Mạng xã hội quy mô triệu user. | **Sử dụng Graph Database** (Neo4j, Memgraph).                                         | Hỗ trợ ngôn ngữ truy vấn đồ thị (Cypher/GQL), tối ưu hóa việc lưu trữ và truy vấn đa chặng trên đĩa/RAM. |