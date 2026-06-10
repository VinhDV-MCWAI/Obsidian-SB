

## 5. Dependency Injection (DI) hoạt động và thay thế Singleton thế nào?

Để giải quyết các nhược điểm "Chết cứng" (Tight Coupling) và "Khó Test" của Singleton truyền thống, các framework hiện đại ngày nay sử dụng **Dependency Injection (DI) Container**.

### Cách Singleton truyền thống làm (Tight Coupling)

Trong code của bạn, ở bất kỳ Controller nào, bạn cũng gọi trực tiếp:

JavaScript

```
// Bạn gọi đích danh tên Class Singleton, tự vào RAM lấy
DatabaseConnection.getInstance().query("SELECT...");
```

Nếu ngày mai bạn muốn đổi sang dùng `PostgresConnection` thay cho `MysqlConnection`, bạn phải lội vào từng file Controller để sửa lại tên Class. Lúc viết Unit Test, bạn không thể "nhét" một Database giả (Mock Database) vào đây được vì code đã chỉ định đích danh Class thật.

### Cách Dependency Injection (DI) vận hành

DI Container đóng vai trò như một **"Chợ đầu mối quản lý Object"** của Server. Khi Server vừa khởi động (Application Start), bạn ra lệnh cho DI Container:

_"Hãy tạo cho tôi đúng 1 Instance duy nhất của Class DatabaseConnection và giữ trong kho của bạn. Khi tôi đăng ký scope là `Singleton`, hãy giữ nó sống suốt vòng đời ứng dụng"_.

Lúc này, các Controller của bạn sẽ viết theo kiểu "Đăng ký nhu cầu" chứ không tự đi lấy:

TypeScript

```
// Controller chỉ nói: "Tôi cần một thứ có thể query dữ liệu, đưa cho tôi"
class UserController {
    constructor(private db: IDatabase) {} // Nhận vào qua hàm khởi tạo (Injection)

    hàm_xử_lý_request() {
        this.db.query("SELECT...");
    }
}
```

- **Khi Request đến:** DI Container sẽ nhìn vào `UserController`, thấy nó cần một `IDatabase`. DI Container lục trong kho của nó, thấy có instance `DatabaseConnection` (được tạo dạng Singleton từ lúc start server) $\rightarrow$ Nó tự động "bơm" (Inject) instance đó vào Controller này.
    
- **Tại sao DI thay thế được Singleton truyền thống?**
    
    - Về mặt **vật lý trên RAM**: Nó vẫn hoạt động y hệt Singleton (Chỉ có duy nhất 1 instance tồn tại suốt vòng đời Server để mọi request dùng chung, không bị tạo lại).
        
    - Về mặt **kiến trúc code**: Class `UserController` hoàn toàn không biết và không quan tâm `db` lấy từ đâu ra, nó không bị trói buộc vào tên Class cụ thể nào cả. Khi bạn viết Unit Test, bạn chỉ cần tự tạo một object database giả (`MockDatabase`) rồi truyền tay vào `new UserController(mockDb)`, việc kiểm thử trở nên cực kỳ dễ dàng và độc lập.

------------------------------

# KIẾN TRÚC BỘ NHỚ VÀ CƠ CHẾ VẬN HÀNH CỦA SERVER

## (Từ Tầng Vật Lý Đến Design Pattern Singleton & Dependency Injection)

## 1. Bản Chất Của Source Code Và Quá Trình Khởi Động Ứng Dụng (Application Startup)

Trước khi ứng dụng chạy, mã nguồn của bạn thực chất chỉ là các tệp văn bản (Text files) tĩnh được lưu trữ trên ổ đĩa cứng (HDD/SSD).

Khi bạn thực thi lệnh khởi động Server (ví dụ: `npm start`, `dotnet run`, `mvn spring-boot:run`), Hệ điều hành (OS) sẽ cấp phát một Tiến trình (Process) và tiến hành nạp chương trình từ Ổ đĩa vào **RAM**. Quá trình này được xử lý tùy theo loại ngôn ngữ:

- **Ngôn ngữ biên dịch (Compiled - C++, Go, Rust):** Mã nguồn đã được dịch sẵn thành mã máy (Machine code) dưới dạng nhị phân, OS chỉ việc nạp trực tiếp vào RAM để thực thi.
    
- **Ngôn ngữ thông dịch hoặc chạy trên máy ảo (Interpreted/VM - Java, C#, Node.js, PHP):** Runtime/VM của ngôn ngữ sẽ parse, biên dịch JIT (Just-In-Time) hoặc chuyển đổi source code thành Bytecode/Mã máy mà môi trường đó hiểu được, sau đó mới nạp vào bộ nhớ.
    

### Phân vùng bộ nhớ của một Tiến trình (Process Memory Layout)

Khi đã nằm trên RAM, bộ nhớ của một Process được chia thành nhiều phân vùng logic, trong đó có 3 phân vùng cốt lõi:

1. **Code Segment (Text Segment):** Vùng nhớ **chỉ đọc (Read-only)**. Nơi lưu trữ toàn bộ mã máy, các hàm (Methods), cấu trúc các Class, Logic xử lý, các Middleware, Route và Controller của hệ thống. Vùng này được nạp **cố định 1 lần duy nhất** lúc khởi động ứng dụng.
    
2. **Heap Segment:** Vùng nhớ tự do, dùng để lưu trữ tất cả các Đối tượng (Objects) được khởi tạo động trong quá trình app vận hành.
    
3. **Stack Segment:** Vùng nhớ ngăn xếp, dùng để quản lý các luồng thực thi (Threads). Khi app khởi động, hệ thống sẽ chạy hàm khởi tạo chính (Main Thread) và cấp phát một **Main Stack**.
    

> **Đính chính quan trọng:** Logic code và cấu trúc hệ thống **KHÔNG** đợi đến khi có Request mới nạp vào RAM, cũng không nạp riêng cho từng Thread. Chúng đã nằm cố định và sẵn sàng tại **Code Segment** ngay từ giai đoạn Khởi động ứng dụng (Application Startup).

## 2. Mô Hình Vòng Đời: Vòng Đời Ứng Dụng vs. Vòng Đời Request

Hệ thống **KHÔNG** tắt đi bật lại sau mỗi Request. Nếu mỗi Request đến lại phải start lại hệ thống, load lại cấu hình, connect lại DB... thì Server sẽ sập ngay lập tức vì chi phí khởi động hệ thống cực kỳ lớn. Để tối ưu hóa hiệu năng, Server hoạt động dựa trên sự phân tách tuyệt đối giữa hai tầng vòng đời:

### A. Vòng đời Ứng dụng (Application Lifecycle)

- **Thời điểm:** Chạy duy nhất một lần khi bật Server.  (ví dụ: `npm start`, `dotnet run`, `mvn spring-boot:run`)
    
- **Hành vi:** Đọc cấu hình, nạp Code Segment, tạo các Object quản trị hệ thống dài hạn trên Heap.
    
- **Trạng thái:** Sau khi khởi động, Server rơi vào trạng thái "Lắng nghe" (Listening) tại một cổng mạng (Port). Nó tồn tại vĩnh viễn cho đến khi bị tắt tiến trình (Stop Process).
    

### B. Vòng đời Request (Request Lifecycle)

- **Thời điểm:** Phát sinh bất cứ khi nào có User gửi tín hiệu đến Server.
    
- **Hành vi:** Trong mô hình đa luồng (Multi-threading / Async), mỗi Request đến sẽ được đảm nhận bởi một Luồng xử lý riêng. Hệ điều hành cấp phát cho Luồng này một **Thread Stack (Stack riêng)**.
    
- **Cơ chế cô lập:** Các biến tạm, tham số truyền vào hàm (ví dụ: `userId = 5`) sẽ nằm trên Stack của Request đó. Vì Stack là độc lập, Request A không thể can thiệp vào bộ nhớ của Request B, đảm bảo tính **An toàn luồng (Thread Safety)**.
    
- **Kết thúc:** Khi Response được trả về cho Client, **Thread Stack của Request đó bị xóa sạch 100% ngay lập tức**. Các Object dữ liệu tạm thời do Request đó sinh ra trên Heap (nếu không còn ai tham chiếu tới) sẽ trở thành "Rác" và đợi Bộ gom rác (Garbage Collector - GC) đến dọn dẹp.
    

## 3. Bản Chất Của Việc "Khởi Tạo Object" Trên RAM

Khi bạn viết câu lệnh `const service = new UserService()`, hệ thống sẽ thực hiện các bước sau dưới tầng vật lý:

1. **Cấp phát trên Heap:** Xin Hệ điều hành cấp một vùng trống trên vùng nhớ **Heap**. Vùng trống này **chỉ dùng để lưu trữ các thuộc tính (Properties/State)** và các thông tin định danh (Metadata) của Object đó.
    
2. **Liên kết với Code Segment:** Hệ thống **không sao chép** các hàm/phương thức (Methods) của Class vào Heap. Thay vào đó, nó tạo một con trỏ (Pointer/Virtual Method Table) trỏ ngược về vùng nhớ **Code Segment** – nơi chứa logic code cố định của Class `UserService`.
    
3. **Tham chiếu trên Stack:** Trả về một **Địa chỉ ô nhớ** của Heap. Biến `service` thực chất nằm ở vùng nhớ **Stack** của luồng hiện tại, giữ vai trò là con trỏ chứa địa chỉ ô nhớ đó.
    

> **Đính chính 2:** Khi khởi tạo một Object, bộ nhớ Heap của Object đó không chứa mã nguồn hay logic của các hàm. Nó chỉ chứa dữ liệu/thuộc tính và địa chỉ trỏ về vùng mã máy cố định. Do đó, việc nhân bản nhiều Object thực chất là nhân bản các ô chứa thuộc tính, chứ không phải nhân bản logic code.
### Thảm họa tài nguyên khi KHÔNG quản lý vòng đời Object (Non-Singleton)

Nếu bạn đặt lệnh `const service = new UserService()` hoặc `const db = new DatabaseConnection()` **bên trong** hàm xử lý Request (Controller):

- **Áp lực lên Heap:** 10.000 Requests/giây đồng nghĩa với việc 10.000 Object được tạo mới liên tục trên Heap. Dù logic chạy giống hệt nhau, việc liên tục xin cấp phát vùng trống cho các thuộc tính vẫn ngốn một lượng RAM lớn.
    
- **Nghẽn CPU vì Garbage Collector:** Khi 10.000 Requests kết thúc, các Object này mất tham chiếu. Bộ gom rác (GC) phải hoạt động hết công suất để quét và giải phóng Heap. Quá trình này làm bùng nổ CPU (CPU Spike) và có thể gây đứng hình ứng dụng tạm thời (Stop-The-World).
    
- **Thảm họa kết nối Database:** Với các Object kết nối vật lý, việc `new` và hủy liên tục bắt buộc hệ thống phải thực hiện lại quá trình bắt tay TCP (TCP Handshake) và xác thực tài khoản (mất khoảng ~100ms/lần). Database Server sẽ nhanh chóng sụp đổ do cạn kiệt tài nguyên xử lý kết nối.

## 4. Giải Pháp Singleton Pattern: Định Nghĩa Và Cơ Chế Vận Hành

### Định nghĩa bản chất

**Singleton** là một mẫu thiết kế (Design Pattern) đảm bảo một Class chỉ có **duy nhất một Instance (đối tượng)** tồn tại trong suốt vòng đời của ứng dụng, và cung cấp một điểm truy cập toàn cục tới đối tượng đó.

### Cơ chế vận hành vật lý trên RAM

Thay vì khởi tạo ở tầng Request, một Object Singleton được khởi tạo ở tầng **Vòng đời Ứng dụng** (lúc Start App hoặc trong lần gọi đầu tiên) và được gán vào một biến Tĩnh/Toàn cục (`static` / `global`).

- **Khi Request 1 đến:** Gọi hàm lấy instance, hệ thống cấp địa chỉ của Object Singleton duy nhất trên Heap (ví dụ: `0xABC`). Request 1 thực thi logic dựa trên địa chỉ này.
    
- **Khi Request 1 kết thúc:** Thread Stack của Request 1 bị hủy, nhưng vì Object Singleton tại `0xABC` vẫn đang được tham chiếu bởi biến static của hệ thống, **Garbage Collector không bao giờ được phép xóa nó**.
    
- **Khi Request 2 đến:** Hệ thống tiếp tục trả về chính xác địa chỉ `0xABC`. Request 2 dùng chung Object đó mà không tốn một byte RAM khởi tạo nào.
    

> **Đính chính 3 (Về Kết Nối Database):** Singleton và Connection Pool là hai khái niệm bổ trợ nhau chứ không phải là một. Singleton chỉ đảm bảo có **1 instance** của đối tượng quản lý database. Còn việc mở sẵn 10 kết nối giữ nguyên trên RAM là do logic của **Connection Pool** (nằm bên trong Object Singleton đó) đảm nhiệm. Singleton bọc Connection Pool để các request có một "trạm điều phối kết nối" duy nhất.

## 5. Mặt Trái Của Singleton: Khi Nào Nó Trở Thành "Chất Độc"?

Dù tối ưu hiệu năng rất tốt, Singleton có hai nhược điểm chí mạng nếu lạm dụng:

### Nhược điểm 1: Xung đột dữ liệu liên phiên (Stateful Issue)

Singleton biến dữ liệu lưu trữ bên trong nó thành dữ liệu "Toàn cục" (Global State). Do đó, nó **chỉ được phép sử dụng cho các Dịch vụ không lưu trạng thái (Stateless Service)** – tức là các Class chỉ chứa hàm xử lý logic, không chứa biến lưu dữ liệu riêng của người dùng (ví dụ: _Logger, Database Connection Pool, Bcrypt Hashing, PaymentService_). Các method ko quan tâm ai gọi (function common), nhận dữ liệu từ param or không, xử lý logic và return về or không. Không giữ lại giá trị dữ liệu gì. 

> **Ví dụ về thảm họa Stateful:**
> 
> Cơ chế lưu lại dữ liệu của lần chạy trước, mà lần sau hoặc luồng khác chạy bị ảnh hưởng thì là statefull. Hầu hết đều đến từ property, reference, khai báo và sử dụng variable global or không reset mỗi lần sử dụng.
> 
> Nếu bạn dùng Singleton cho `CartService` (Giỏ hàng) và khai báo một thuộc tính lưu trạng thái `this.items = []`:
> 
> 1. User A đến, thêm món hàng `"Điện thoại"` $\rightarrow$ Singleton cập nhật `this.items = ["Điện thoại"]`.
>     
> 2. User B (từ một máy tính khác) truy cập giỏ hàng của họ. Vì dùng chung một Instance Singleton, User B gọi hàm lấy giỏ hàng và sẽ nhìn thấy ngay chiếc `"Điện thoại"` của User A. Dữ liệu của các User bị lẫn lộn vào nhau.
>     

### Nhược điểm 2: Khó viết Unit Test (Code lỗi dây chuyền)

Do Singleton sống suốt vòng đời ứng dụng và giữ nguyên trạng thái, các ca kiểm thử (Unit Test) sẽ bị phụ thuộc lẫn nhau. Nếu _Test Case 1_ chạy và làm thay đổi một biến bên trong Singleton, thì _Test Case 2_ chạy sau đó sẽ bị sai lệch kết quả do dính phải "dữ liệu rác" mà _Test Case 1_ để lại. Việc cô lập môi trường test trở nên cực kỳ phức tạp vì Singleton không tự reset sau mỗi phiên. Đây là trường hợp singleton cho statefull. Singleton kết hợp với tài nguyên bên ngoài như DB, file, API bên khác. Vì phải call đích danh service.getInstance() khiến khó nhét mock dữ liệu vào để test

## 6. Sự Tiến Hóa Với Dependency Injection (DI) Container

Để giải quyết triệt để nhược điểm **"Chết cứng code" (Tight Coupling)** và **"Khó Test"** của Singleton truyền thống mà vẫn giữ được ưu điểm tối ưu RAM, các framework hiện đại ngày nay sử dụng **Dependency Injection (DI) Container**.

### So sánh kiến trúc thiết kế: Singleton Truyền Thống vs Dependency Injection

|**Tiêu chí**|**Singleton Truyền Thống**|**Dependency Injection (DI) Container**|
|---|---|---|
|**Cách lấy Object**|Gọi đích danh Class thông qua hàm static: `DatabaseConnection.getInstance()`|Nhận Object từ ngoài truyền vào thông qua Constructor (Hàm khởi tạo).|
|**Độ phụ thuộc (Coupling)**|**Tight Coupling (Kết dính chặt):** Controller bị trói buộc cứng vào một tên Class cụ thể. Nếu đổi DB, phải sửa toàn bộ các file Controller.|**Loose Coupling (Kết dính lỏng):** Controller chỉ làm việc thông qua Interface trừu tượng. Không quan tâm Class cụ thể phía sau là gì.|
|**Khả năng Unit Test**|**Cực kỳ khó:** Không thể thay thế (Mock) một Database giả lập vào cấu trúc kiểm thử.|**Cực kỳ dễ:** Chỉ cần khởi tạo thủ công `new UserController(new MockDatabase())` để test độc lập.|
|**Bản chất vật lý trên RAM**|Tạo ra 1 instance duy nhất trên Heap.|**Y hệt:** DI Container quản lý và duy trì duy nhất 1 instance trên Heap khi đăng ký scope là `Singleton`.|

### Luồng vận hành của DI Container trong thực tế

1. **Giai đoạn Khởi động (Application Start):** Bạn đăng ký cấu hình với DI Container: _"Hãy tạo cho tôi đúng 1 Instance duy nhất của Class `DatabaseConnection` (gắn với Interface `IDatabase`) và giữ nó ở dạng Singleton trong kho"_.
    
2. **Khai báo nhu cầu:** Các Controller được viết theo phong cách "đòi hỏi" chứ không tự đi tìm:
    
    TypeScript
    
    ```
    class UserController {
        // Nhận vào một thực thể có cấu trúc IDatabase thông qua Constructor
        constructor(private db: IDatabase) {} 
    
        handleRequest() {
            this.db.query("SELECT...");
        }
    }
    ```
    
3. **Bơm phụ thuộc (Injection):** Khi có Request đến, DI Container kiểm tra `UserController`, thấy nó cần một `IDatabase`. Nó lập tức lục trong "kho" của nó, lấy địa chỉ của Instance `DatabaseConnection` duy nhất (đã tạo từ lúc start server) và **tự động "bơm" (Inject)** vào Controller này.
    

## 7. Tổng Kết Thực Tế Ngành Công Nghiệp (Stateless Service Architecture)

Trong các Framework hiện đại ngày nay (_Spring Boot, ASP.NET Core, NestJS_), hệ thống tối ưu hóa hiệu năng bằng cách chia thế giới Object làm 2 loại rõ rệt:

### Loại 1: Các Class chứa "Logic xử lý" (Controller, Service, Repository)

- **Đặc điểm:** Chỉ chứa các hàm/phương thức để xử lý luồng đi của dữ liệu, **không chứa biến lưu trạng thái riêng của User (Stateless)**.
    
- **Cơ chế quản lý:** Được DI Container khởi tạo theo dạng **Singleton duy nhất 1 lần lúc bật Server**. 1 triệu hay 10 triệu Requests đổ vào đều dùng chung các Instance này. Nhờ vậy, RAM của Server cực kỳ ổn định, không bị trồi sụt liên tục.
    

### Loại 2: Các Class chứa "Dữ liệu" (Model, Entity, DTO, Request Body)

- **Đặc điểm:** Chứa dữ liệu định danh, thông tin giỏ hàng, thông tin tài khoản của riêng từng User (ví dụ: `UserInfo(id: 5, name: "Sơn")`).
    
- **Cơ chế quản lý:** Bắt buộc phải **khởi tạo riêng biệt (Transient/Scoped) trên Heap cho từng Request**. Ngay khi Request kết thúc và trả về kết quả cho User, Garbage Collector sẽ quét sạch các Object này để giải phóng RAM và bảo mật dữ liệu.
    

## 💡 Hình Ảnh Ẩn Dụ: Mô Hình Nhà Hàng Server

Để dễ hình dung và ghi nhớ lâu dài, hãy tưởng tượng Server của bạn là một **Nhà hàng**:

- **Giai đoạn khởi động (Start App):** Bạn xây dựng Bếp ga (`Database Connection`) và thuê Đầu bếp (`Stateless Service`). Họ đến nhà hàng, đứng sẵn vào vị trí trong bếp và ngồi trên RAM chờ đợi.
    
- **Khi Request 1 đến (User đặt món):** Người phục vụ mang vào một **Tờ hóa đơn giấy** (`Object chứa dữ liệu Request`). Đầu bếp (`Singleton`) đọc tờ giấy đó, nấu món ăn, rồi gửi món ăn đi (`Response`).
    
- **Khi Request 1 kết thúc:** Tờ hóa đơn giấy bị xé bỏ và vứt vào sọt rác (`Garbage Collector xóa bộ nhớ tạm`). Tuy nhiên, **Đầu bếp và Bếp ga vẫn đứng nguyên ở đó**, không ai đuổi việc họ cả.
    
- **Khi Request 2 đến:** Chính Đầu bếp đó lại tiếp tục tiếp nhận tờ hóa đơn mới và nấu ăn. Họ không bị "thuê lại từ đầu" (`Không khởi tạo lại`).
    

> **Nhà hàng chỉ giải tán và giải phóng toàn bộ mặt bằng (Xóa sạch RAM) khi chủ nhà hàng tuyên bố Đóng cửa thương hiệu (Tắt tiến trình Server Application).**