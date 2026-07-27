Nếu chương trước trả lời câu hỏi dữ liệu được đặt lên đĩa như thế nào, thì chương này đi xa hơn một tầng:

> **Tại sao chỉ lưu file là chưa đủ, và tại sao con người phải tạo ra database?**

Đây là bước tiến rất tự nhiên trong lịch sử khoa học máy tính.

Ban đầu, người ta có:

- byte
    
- file
    
- thư mục
    
- storage
    

Nhưng khi dữ liệu tăng lên, bài toán không còn là “lưu được” nữa, mà là:

- tìm nhanh,
    
- sửa an toàn,
    
- truy vấn linh hoạt,
    
- chịu tải lớn,
    
- giữ nhất quán,
    
- và không làm hệ thống chết khi dữ liệu quá nhiều.
    

Database sinh ra từ chính các nhu cầu đó.

---

# 1. Vấn đề của file thuần túy

Lưu dữ liệu vào file nghe có vẻ đơn giản.

Ví dụ:

- một file JSON
    
- một file CSV
    
- một file text
    
- một file log
    

Cách này ổn khi dữ liệu nhỏ và truy cập đơn giản.

Nhưng ngay khi hệ thống có yêu cầu thực tế hơn, file thuần túy bắt đầu lộ giới hạn:

- tìm một bản ghi trong hàng triệu bản ghi rất chậm
    
- sửa đồng thời dễ gây xung đột
    
- xóa và cập nhật dễ sinh lỗi
    
- cấu trúc dữ liệu lớn khó index
    
- truy vấn theo nhiều điều kiện trở nên cực kỳ tốn kém
    
- khó đảm bảo transaction và consistency
    

Nói ngắn gọn:

> file là nơi chứa dữ liệu, nhưng chưa phải là hệ thống quản lý dữ liệu.

Database xuất hiện để giải quyết chính khoảng trống đó.

---

# 2. Database là gì?

Database là một hệ thống lưu trữ và truy vấn dữ liệu có tổ chức.

Nó không chỉ lưu byte.

Nó còn cung cấp:

- mô hình dữ liệu
    
- ngôn ngữ truy vấn
    
- index
    
- transaction
    
- concurrency control
    
- recovery
    
- permission
    
- replication trong nhiều hệ thống hiện đại
    

Điểm mấu chốt là:

> database là lớp trừu tượng cao hơn file system, được thiết kế để truy cập và quản lý dữ liệu theo nghiệp vụ, không chỉ theo byte.

---

# 3. Tại sao không để ứng dụng tự xử lý file?

Về lý thuyết, ứng dụng có thể tự viết logic lưu file riêng.

Nhưng sẽ nhanh chóng gặp các vấn đề:

- tự phải viết cơ chế tìm kiếm
    
- tự phải xử lý khóa đồng thời
    
- tự phải đảm bảo dữ liệu không hỏng khi crash
    
- tự phải quản lý index
    
- tự phải xử lý backup và recovery
    
- tự phải giải quyết consistency
    

Khi mỗi ứng dụng tự làm điều đó, code sẽ:

- dài hơn
    
- dễ lỗi hơn
    
- khó bảo trì hơn
    
- khó tối ưu hơn
    
- khó mở rộng hơn
    

Database tồn tại vì nó chuẩn hóa phần khó nhất của việc lưu dữ liệu.

---

# 4. Mô hình tư duy của database

Hãy hình dung database như một thư viện cực lớn.

Nếu chỉ có một đống sách đặt bừa:

- muốn tìm một cuốn sẽ rất lâu
    
- muốn thêm sách mới khó
    
- muốn xóa sách lỗi dễ nhầm
    
- muốn chia quyền truy cập gần như bất khả thi
    

Database giải quyết bằng cách:

- tổ chức dữ liệu theo bảng hoặc cấu trúc khác
    
- tạo chỉ mục tra cứu
    
- quản lý việc ghi đọc
    
- đảm bảo tính nhất quán
    
- cho phép nhiều người dùng cùng lúc
    

Đó là lý do database không chỉ là “nơi lưu dữ liệu”, mà là một hệ thống tổ chức dữ liệu.

---

# 5. Bản ghi, bảng và hàng

Trong mô hình quan hệ phổ biến, dữ liệu được tổ chức thành:

- bảng
    
- hàng
    
- cột
    

Mỗi hàng là một thực thể dữ liệu.

Ví dụ:

- user
    
- order
    
- product
    
- invoice
    
- log event
    

Mỗi cột là một thuộc tính:

- id
    
- name
    
- age
    
- created_at
    
- status
    

Mô hình này có ưu điểm:

- dễ hiểu
    
- dễ truy vấn
    
- dễ chuẩn hóa
    
- phù hợp với nghiệp vụ business
    

Tuy nhiên, để bảng hoạt động hiệu quả khi dữ liệu lớn, database cần nhiều cơ chế phía dưới.

---

# 6. Vì sao truy vấn nhanh là vấn đề khó?

Giả sử bạn có một bảng 100 triệu dòng.

Nếu không có index, muốn tìm một dòng theo `email` có thể phải:

- quét từng dòng từ đầu đến cuối
    

Đó là **full table scan**.

Với dữ liệu lớn, full scan thường rất đắt.

Database không thể mỗi lần đều đọc hết bảng.

Do đó người ta tạo ra **index**.

---

# 7. Index là gì?

Index là cấu trúc phụ giúp tra cứu dữ liệu nhanh hơn.

Nó giống như mục lục trong sách.

Thay vì mở từng trang để tìm một từ, ta nhìn mục lục trước, rồi nhảy thẳng tới đúng trang.

Trong database cũng vậy:

- bảng là dữ liệu chính
    
- index là cấu trúc phụ để định vị nhanh
    

Index làm giảm đáng kể chi phí tra cứu, đặc biệt khi truy vấn theo:

- khóa chính
    
- khóa phụ
    
- cột tìm kiếm thường xuyên
    
- cột sắp xếp hoặc join
    

Nhưng index không miễn phí.

Nó tốn:

- dung lượng
    
- chi phí ghi
    
- chi phí cập nhật
    
- chi phí đồng bộ
    

Đây là đánh đổi rất quan trọng.

---

# 8. Vì sao index không thể chỉ là một danh sách phẳng?

Nếu index chỉ là một danh sách tuyến tính, tìm kiếm vẫn chậm.

Ta cần một cấu trúc có thể:

- duyệt nhanh
    
- chèn/xóa tương đối hiệu quả
    
- giữ dữ liệu có thứ tự
    
- tối ưu cho I/O đĩa
    

Đây là lý do các cấu trúc cây cân bằng xuất hiện.

Trong database, cấu trúc rất phổ biến là **B-tree** hoặc biến thể của nó.

---

# 9. B-tree là gì?

B-tree là một cấu trúc cây được thiết kế để tối ưu cho hệ thống lưu trữ cấp block/page.

Không giống cây nhị phân đơn giản, B-tree có:

- nhiều nhánh
    
- node chứa nhiều khóa
    
- chiều cao thấp
    
- truy cập ít lần hơn khi đi xuống cây
    

Điểm cực kỳ quan trọng là:

> B-tree sinh ra không chỉ để đẹp về mặt thuật toán, mà để giảm số lần đọc đĩa.

Đây là khác biệt lớn.

Trên bộ nhớ, một phép toán log n là tốt.

Nhưng trên storage, mỗi lần nhảy tới một node có thể là một lần I/O đắt đỏ.

Vì thế B-tree được thiết kế để giảm số tầng cần đi qua.

---

# 10. Vì sao B-tree phù hợp với database?

Database làm việc với dữ liệu rất lớn, phần lớn nằm trên đĩa hoặc SSD.

Khi truy vấn:

- không phải chỉ tính số phép so sánh
    
- mà là số lần chạm page / block / I/O
    

B-tree tối ưu điều này bằng cách:

- tăng độ phân nhánh
    
- giảm chiều cao cây
    
- đưa nhiều key vào cùng một node
    
- tận dụng locality
    

Kết quả là:

- tìm kiếm nhanh
    
- chèn xóa chấp nhận được
    
- phù hợp với dữ liệu trên storage
    

Đây là lý do B-tree trở thành nền tảng cho nhiều index trong database.

---

# 11. B-tree khác gì binary tree?

Binary tree mỗi node thường chỉ có tối đa 2 con.

B-tree có thể có nhiều con hơn rất nhiều.

Điều này giúp:

- giảm chiều cao
    
- giảm số lần truy cập page
    
- phù hợp với block size của storage
    

Nếu database dùng binary tree thuần túy cho dữ liệu lớn trên đĩa, số lần I/O sẽ quá nhiều.

B-tree là câu trả lời của hệ thống lưu trữ đối với bài toán tìm kiếm trên dữ liệu lớn.

---

# 12. Clustered index và non-clustered index

Trong nhiều hệ thống database, có nhiều kiểu index khác nhau.

## Clustered index

Dữ liệu chính được sắp theo cấu trúc index hoặc gắn rất chặt với nó.

## Non-clustered index

Index chỉ là cấu trúc phụ trỏ tới vị trí dữ liệu.

Điểm khác biệt này ảnh hưởng rất mạnh tới:

- tốc độ query
    
- tốc độ insert/update/delete
    
- cách lưu dữ liệu
    
- thiết kế schema
    

Không hiểu index là không hiểu vì sao có query nhanh hoặc chậm bất thường.

---

# 13. Index không phải lúc nào cũng tốt

Đây là chỗ rất nhiều người mới học hiểu sai.

Thêm index không có nghĩa là hệ thống sẽ nhanh hơn toàn diện.

Vì index cũng tạo chi phí:

- insert chậm hơn
    
- update chậm hơn
    
- delete chậm hơn
    
- tốn thêm storage
    
- tăng công bảo trì
    
- có thể làm planner chọn sai đường
    

Nói cách khác:

> index là đánh đổi giữa tốc độ đọc và chi phí ghi.

Nếu ứng dụng chủ yếu ghi nhiều, thêm quá nhiều index có thể làm hệ thống chậm đi.

---

# 14. Buffer pool là gì?

Database không đọc trực tiếp từng lần truy vấn từ đĩa nếu có thể tránh.

Nó dùng một vùng bộ nhớ trung gian gọi là **buffer pool** hoặc cache tương đương.

Mục tiêu:

- giữ các page hay dùng trong RAM
    
- giảm I/O xuống storage
    
- tăng tốc truy vấn
    
- tận dụng locality
    

Đây là một nguyên lý cực kỳ quan trọng:

> nhiều truy vấn nhanh không phải vì storage nhanh, mà vì dữ liệu đã nằm sẵn trong RAM cache của database hoặc hệ điều hành.

Buffer pool là một trong các thành phần quan trọng nhất quyết định hiệu năng database.

---

# 15. Page trong database

Database thường không làm việc theo từng byte, mà theo page.

Page là đơn vị dữ liệu mà database đọc/ghi cùng một lúc.

Vì sao?

Vì truy cập storage theo từng byte sẽ vô cùng tốn kém.

Đọc/ghi theo page giúp:

- giảm số lần I/O
    
- khớp với block size của storage
    
- tối ưu cache
    
- tối ưu transaction log
    
- tối ưu recovery
    

Một page có thể chứa:

- nhiều row
    
- một phần row lớn
    
- hoặc node của B-tree
    

Do đó, page là cầu nối cực kỳ quan trọng giữa logical data và physical storage.

---

# 16. Transaction: làm sao không hỏng dữ liệu khi đang sửa?

Khi nhiều thao tác xảy ra cùng lúc, database phải đảm bảo dữ liệu không rơi vào trạng thái nửa chừng.

Ví dụ:

- trừ tiền tài khoản A
    
- cộng tiền tài khoản B
    

Nếu mới trừ ở A mà chưa cộng ở B rồi hệ thống crash, dữ liệu sẽ sai.

Để tránh điều đó, database dùng **transaction**.

Transaction đảm bảo nhóm thao tác được xem như một đơn vị logic.

Nếu thành công thì cùng thành công.

Nếu thất bại thì có thể rollback.

Đây là một trong những phát minh quan trọng nhất của hệ thống dữ liệu.

---

# 17. ACID là gì?

Nhiều database quan hệ nói về ACID.

## Atomicity

Hoặc làm hết, hoặc không làm gì.

## Consistency

Dữ liệu vẫn tuân theo ràng buộc sau transaction.

## Isolation

Các transaction đồng thời không làm nhau hỏng logic.

## Durability

Một khi đã commit, dữ liệu phải bền vững sau crash.

Đây là bốn trụ cột của database giao dịch.

Nếu không có chúng, rất nhiều hệ thống tài chính, đơn hàng, kho vận sẽ không thể hoạt động tin cậy.

---

# 18. Concurrency: nhiều người cùng truy cập một lúc

Khi hệ thống có nhiều request đồng thời, database phải giải quyết:

- ai được đọc
    
- ai được ghi
    
- ai đang giữ lock
    
- ai phải chờ
    
- dữ liệu nhìn thấy là phiên bản nào
    

Đây là bài toán concurrency control.

Nếu làm không tốt, sẽ xuất hiện:

- lost update
    
- dirty read
    
- phantom read
    
- non-repeatable read
    
- race condition
    

Database phải cân bằng giữa:

- tính đúng
    
- tính đồng thời
    
- hiệu năng
    

Đây là lý do hệ thống dữ liệu là một lĩnh vực vừa toán học vừa hệ điều hành vừa kỹ thuật phần cứng.

---

# 19. WAL và recovery

Khi database ghi dữ liệu, nó không thể chỉ sửa trực tiếp lên page rồi hy vọng mọi thứ ổn.

Nó cần cơ chế phục hồi khi crash.

Một cơ chế rất phổ biến là **Write-Ahead Logging**.

Ý tưởng là:

> ghi log trước, sửa dữ liệu sau

Nếu hệ thống chết giữa chừng, database có thể đọc log để:

- redo thao tác đã cam kết
    
- undo thao tác chưa hoàn tất
    

Đây là nền của durability và crash recovery.

---

# 20. Tại sao database không chỉ là file + code?

Vì file + code tự viết rất khó đạt được:

- truy vấn nhanh
    
- index tốt
    
- transaction chuẩn
    
- recovery đáng tin
    
- concurrency an toàn
    
- tối ưu cho storage thật
    

Database là kết quả của nhiều thập kỷ tối ưu hóa cho một bài toán vô cùng khó:

> lưu dữ liệu lớn, truy vấn nhanh, sửa an toàn, và không sập khi hệ thống hỏng giữa chừng.

---

# 21. Database hiện đại đã trở thành hạ tầng, không còn là công cụ phụ

Ngày nay, database không còn là “chỗ chứa data” đơn giản.

Nó là nền của:

- hệ thống thương mại điện tử
    
- thanh toán
    
- CRM
    
- ERP
    
- logging
    
- analytics
    
- search
    
- recommendation
    
- microservices
    

Backend engineer làm việc với database hằng ngày, nhưng nếu chỉ biết viết query mà không hiểu bên dưới, sẽ rất khó tối ưu hệ thống thật.

---

# 22. Backend engineer cần nhớ gì từ chương này?

## 1. File không đủ cho hệ thống lớn

Database tồn tại vì file thuần túy không đủ mạnh cho truy vấn, transaction và concurrency.

## 2. Index là cấu trúc đánh đổi

Nó giúp đọc nhanh nhưng làm ghi tốn kém hơn.

## 3. B-tree là cốt lõi của nhiều index

Nó tối ưu cho storage cấp page/block.

## 4. Buffer pool quyết định rất nhiều hiệu năng

Không phải mọi truy vấn nhanh đều là do đĩa nhanh.

## 5. Transaction là điều bắt buộc cho dữ liệu quan trọng

Nhất là tiền, đơn hàng, trạng thái nghiệp vụ.

## 6. ACID không phải khẩu hiệu

Nó là cơ chế để dữ liệu không bị hỏng khi hệ thống phức tạp.

---

# 23. Kết luận của chương

Từ storage và file system, ta đi lên database.

Nếu file system trả lời câu hỏi “byte nằm ở đâu trên đĩa”, thì database trả lời câu hỏi:

> “Dữ liệu nghiệp vụ được tổ chức, tìm kiếm, cập nhật và bảo vệ như thế nào để hệ thống có thể vận hành ở quy mô thật?”

Đây là một bước nhảy rất lớn trong lịch sử máy tính.

Nó biến lưu trữ từ thao tác kỹ thuật thành một hệ thống dữ liệu hoàn chỉnh.

Từ đây, chương tiếp theo sẽ đi sang một vấn đề thời gian và lịch sử rất thực tế, nơi rất nhiều hệ thống từng gặp sự cố:

> **Unix time, timestamp, Y2K, năm 2038 và giới hạn của biểu diễn thời gian trong máy tính**.

Nếu cần, tôi có thể viết tiếp **Chương 17 — Unix time, timestamp, Y2K, 2038 problem** theo cùng mạch này.