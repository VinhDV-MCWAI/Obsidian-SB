Nếu chương trước trả lời câu hỏi “làm sao máy tính lưu chữ”, thì chương này đi xuống tầng thấp hơn nữa:

> **Sau khi dữ liệu đã được mã hóa thành byte, nó được đặt ở đâu trên thiết bị lưu trữ, theo quy tắc nào, và hệ điều hành quản lý nó ra sao?**

Đây là chỗ rất nhiều người học phần mềm bỏ qua, nhưng với một kỹ sư hệ thống hoặc backend engineer, đây là nền cực kỳ quan trọng.

Vì nếu không hiểu tầng này, bạn sẽ rất khó hiểu:

- tại sao file mở chậm,
    
- tại sao database cần index,
    
- tại sao SSD khác HDD,
    
- tại sao file system có phân mảnh,
    
- tại sao một file tưởng nhỏ nhưng chiếm nhiều dung lượng,
    
- tại sao dữ liệu bị mất khi tắt máy nếu chưa ghi ra đĩa.
    

---

# 1. Từ RAM xuống lưu trữ lâu dài: cần một nơi không mất dữ liệu

RAM nhanh nhưng mất điện là mất dữ liệu.

Vì thế máy tính cần một lớp lưu trữ khác:

- giữ dữ liệu lâu dài,
    
- không phụ thuộc việc máy đang bật hay tắt,
    
- có thể lưu file, chương trình, ảnh, video, database, log.
    

Đó là lý do tồn tại:

- HDD
    
- SSD
    
- flash
    
- các thiết bị lưu trữ thứ cấp khác
    

RAM là nơi làm việc.

Storage là nơi giữ lâu dài.

Hai vai trò này khác nhau hoàn toàn.

---

# 2. File là gì?

Người dùng thường nghĩ file là một “vật” có sẵn.

Nhưng ở tầng hệ thống, file chỉ là:

> một cách tổ chức dữ liệu có tên, có metadata, và có vị trí lưu trên thiết bị.

File không phải là dữ liệu tự nhiên. Nó là một cấu trúc logic do hệ điều hành và file system tạo ra.

Một file thường có:

- tên
    
- kích thước
    
- quyền truy cập
    
- thời gian tạo/sửa
    
- vị trí lưu
    
- nội dung byte
    

Điểm cốt lõi là:

> file system biến một đống byte rời rạc thành đối tượng có ý nghĩa cho con người và phần mềm.

---

# 3. Vấn đề đầu tiên: dữ liệu trên đĩa không thể cứ “ném đại” là xong

Nếu chỉ ghi byte liên tiếp mà không có quy tắc, hệ thống sẽ gặp ngay hàng loạt vấn đề:

- làm sao biết file nào bắt đầu ở đâu
    
- làm sao biết file nào kết thúc ở đâu
    
- làm sao cấp phát chỗ trống
    
- làm sao xóa file
    
- làm sao tìm file nhanh
    
- làm sao chống hỏng dữ liệu khi mất điện
    
- làm sao quản lý nhiều file cùng lúc
    

Vì thế cần một lớp tổ chức nằm giữa dữ liệu thô và phần mềm.

Đó là **file system**.

---

# 4. File system là gì?

File system là cơ chế tổ chức dữ liệu trên thiết bị lưu trữ để hệ điều hành có thể:

- tạo file
    
- đọc file
    
- ghi file
    
- xóa file
    
- đổi tên file
    
- phân quyền file
    
- quản lý thư mục
    

Nó giống như một hệ thống quản lý kho.

Thiết bị lưu trữ chỉ là vật lý.

File system là cách ta biến thiết bị vật lý thành một không gian logic có cấu trúc.

Ví dụ các file system quen thuộc:

- NTFS
    
- ext4
    
- XFS
    
- APFS
    
- FAT32
    

Mỗi loại có quy tắc riêng, điểm mạnh riêng, và lịch sử phát triển riêng.

---

# 5. Sector: đơn vị vật lý cơ bản trên thiết bị lưu trữ

Thiết bị lưu trữ không ghi từng byte một cách tự do theo nghĩa phần mềm mong muốn.

Ở tầng vật lý, dữ liệu thường được tổ chức theo các đơn vị nhỏ gọi là **sector**.

Sector là một đơn vị đọc/ghi vật lý hoặc gần vật lý trên thiết bị lưu trữ.

Trước đây, sector thường có kích thước 512 byte.

Ngày nay, nhiều ổ dùng 4 KB sector vật lý hoặc 4K native.

Ý nghĩa của sector là:

- thiết bị đọc/ghi theo đơn vị đó
    
- không thể tùy tiện ghi nửa sector theo nghĩa vật lý
    
- hệ điều hành phải làm việc với các ràng buộc này
    

Đây là bước đầu tiên cho việc hiểu vì sao lưu trữ có chi phí và quy tắc.

---

# 6. Block: đơn vị file system dùng để quản lý dữ liệu

File system không nhất thiết quản lý dữ liệu theo sector trực tiếp.

Nó thường dùng **block**.

Block là đơn vị mà file system dùng để cấp phát và lưu dữ liệu.

Block có thể là:

- 1 KB
    
- 2 KB
    
- 4 KB
    
- hoặc lớn hơn
    

Tại sao không dùng sector luôn?

Vì file system cần một đơn vị quản lý phù hợp với:

- hiệu năng,
    
- cấp phát,
    
- metadata,
    
- cache,
    
- truy cập ngẫu nhiên.
    

Block là mức trừu tượng cao hơn sector.

Tức là:

- sector: gần phần cứng
    
- block: gần hệ điều hành/file system
    

---

# 7. Vì sao block không nên quá nhỏ?

Nếu block quá nhỏ, hệ thống sẽ phải quản lý quá nhiều đơn vị.

Hậu quả:

- metadata tăng
    
- overhead tăng
    
- truy cập phức tạp hơn
    
- phân mảnh quản lý tăng
    

Nếu block quá lớn:

- file nhỏ sẽ bị lãng phí chỗ
    
- mỗi file chiếm nhiều không gian hơn cần thiết
    

Đây là một bài toán đánh đổi rất kinh điển.

File system phải cân bằng giữa:

- hiệu năng
    
- dung lượng
    
- độ đơn giản
    
- mức lãng phí
    

Không có lựa chọn hoàn hảo tuyệt đối.

---

# 8. Page: đơn vị mà RAM và hệ điều hành thường dùng để quản lý bộ nhớ

Page là khái niệm gần với bộ nhớ hơn, nhưng rất quan trọng khi nối giữa RAM và storage.

Trong hệ điều hành, memory thường được quản lý theo page, ví dụ:

- 4 KB
    
- 8 KB
    
- hoặc kích thước khác tùy hệ thống
    

Page là đơn vị mà OS dùng để:

- cấp phát bộ nhớ ảo
    
- ánh xạ địa chỉ
    
- swap
    
- cache
    
- quản lý virtual memory
    

Nói cách khác:

- block là đơn vị quản lý lưu trữ
    
- page là đơn vị quản lý bộ nhớ
    

Hai thứ khác nhau, nhưng thường đều mang kích thước theo lũy thừa của 2 và có liên hệ chặt chẽ trong kiến trúc hệ thống.

---

# 9. Vì sao page và block thường cùng cỡ?

Không phải ngẫu nhiên mà nhiều hệ thống thấy block và page đều ở mức vài KB.

Lý do là:

- tối ưu truy cập
    
- cân bằng overhead
    
- thuận tiện cho cache
    
- phù hợp với cơ chế DMA, I/O buffer, paging
    
- giảm số lượng lần quản lý metadata
    

Khi kích thước gần nhau, hệ điều hành dễ chuyển dữ liệu giữa:

- RAM
    
- cache
    
- file system
    
- storage
    

Nếu lệch quá xa, hệ thống phải đệm và chuyển đổi nhiều hơn.

---

# 10. HDD là gì?

HDD là ổ đĩa cứng cơ học.

Nó lưu dữ liệu trên đĩa từ quay, đầu đọc cơ khí sẽ di chuyển để đọc/ghi.

## Ưu điểm

- dung lượng lớn
    
- giá thành tốt
    
- phù hợp lưu trữ nhiều dữ liệu
    

## Nhược điểm

- chậm hơn SSD
    
- độ trễ cao
    
- bị giới hạn bởi cơ khí
    
- dễ bị ảnh hưởng bởi va đập
    
- seek time lớn
    

HDD là đại diện điển hình của lưu trữ cơ khí trong lịch sử máy tính.

---

# 11. SSD là gì?

SSD là ổ lưu trữ trạng thái rắn.

Nó không có bộ phận cơ khí quay và đầu đọc di chuyển như HDD.

SSD dùng bộ nhớ flash để lưu dữ liệu.

## Ưu điểm

- nhanh hơn HDD nhiều trong truy cập ngẫu nhiên
    
- ít độ trễ
    
- bền hơn trước va chạm cơ học
    
- phù hợp hệ điều hành, database, ứng dụng hiện đại
    

## Nhược điểm

- có giới hạn ghi
    
- điều khiển phức tạp hơn ở tầng thấp
    
- giá thành trên mỗi GB thường cao hơn HDD trong nhiều ngữ cảnh
    

SSD đã thay đổi mạnh cách ta thiết kế hệ thống.

---

# 12. Tại sao SSD nhanh hơn HDD?

Vì SSD không cần:

- quay đĩa
    
- tìm vị trí cơ khí
    
- chờ đầu đọc dịch chuyển
    

HDD bị giới hạn bởi cơ khí.

SSD chủ yếu bị giới hạn bởi điện tử và điều khiển flash.

Điều này làm giảm rất nhiều độ trễ.

Với workloads hiện đại:

- database
    
- web server
    
- hệ điều hành
    
- cache
    
- build system
    

SSD thường đem lại cải thiện lớn hơn rất nhiều so với HDD.

---

# 13. Nhưng SSD không đơn giản chỉ là “HDD nhanh hơn”

Đây là điểm nhiều người hiểu sai.

SSD có cơ chế vật lý hoàn toàn khác:

- flash memory không ghi xóa như RAM
    
- dữ liệu thường phải xóa theo block lớn hơn mới ghi lại được
    
- có wear leveling
    
- có garbage collection
    
- có controller quản lý phức tạp
    

Nghĩa là SSD nhanh hơn, nhưng kiến trúc của nó hoàn toàn khác HDD.

Nếu chỉ nhìn từ bề mặt “đều là nơi lưu file” thì sẽ bỏ qua bản chất kỹ thuật.

---

# 14. Vấn đề của flash memory: không ghi đè tự do như RAM

Flash không hoạt động như RAM.

Trong nhiều trường hợp, flash:

- không thể ghi đè từng byte tùy ý như RAM
    
- phải xóa theo đơn vị lớn
    
- có giới hạn số lần ghi/xóa
    
- cần điều khiển phân bố write để tránh hỏng sớm
    

Đây là lý do SSD cần controller thông minh.

Nó phải làm các việc như:

- mapping logic address sang physical location
    
- wear leveling
    
- bad block management
    
- garbage collection
    
- cache nội bộ
    

Đây là tầng rất quan trọng, nhưng người dùng thường không thấy.

---

# 15. File system làm gì bên trên storage?

File system phải biến storage thô thành hệ thống logic.

Nó quản lý:

- file
    
- thư mục
    
- quyền truy cập
    
- metadata
    
- vị trí block
    
- free space
    
- consistency
    

Một file có thể không nằm ở một chỗ liên tục.

Nó có thể bị chia thành nhiều block trên đĩa.

File system phải giữ bảng ánh xạ để biết:

- block nào thuộc file nào
    
- block nào còn trống
    
- block nào đang hỏng
    
- file nào nằm trong thư mục nào
    

---

# 16. Fragmentation: khi file bị chia vụn

Khi dữ liệu được tạo, sửa, xóa liên tục, không gian trống trên đĩa sẽ không còn liên tiếp hoàn hảo.

Kết quả là file có thể bị lưu rải rác.

Đó là **fragmentation**.

## Hậu quả

- truy cập chậm hơn, nhất là trên HDD
    
- file lớn có thể bị đọc từ nhiều vị trí
    
- metadata phức tạp hơn
    
- hiệu năng giảm
    

Trên SSD, fragmentation vật lý ít gây ảnh hưởng hơn HDD, nhưng vẫn có tác động đến cấu trúc logic và cache.

---

# 17. Metadata: dữ liệu về dữ liệu

Khi ta nói đến file system, một phần quan trọng không phải là nội dung file, mà là **metadata**.

Metadata bao gồm:

- tên file
    
- kích thước
    
- quyền
    
- thời gian
    
- vị trí block
    
- owner
    
- trạng thái
    

Nói đơn giản:

> nội dung file là dữ liệu  
> còn metadata là thông tin mô tả dữ liệu đó

Trong hệ thống hiện đại, metadata thường rất quan trọng, vì tìm file, sắp xếp file, kiểm tra quyền truy cập đều dựa vào nó.

---

# 18. Tại sao không lưu file như một dải byte đơn giản?

Có thể làm vậy trong một hệ thống cực kỳ đơn giản, nhưng sẽ không đủ cho hệ điều hành hiện đại.

Vì ta cần:

- tên gọi
    
- thư mục lồng nhau
    
- truy cập đồng thời
    
- phân quyền
    
- nhật ký thay đổi
    
- khôi phục lỗi
    
- ánh xạ block
    
- chống mất dữ liệu
    

File system là câu trả lời cho tất cả các vấn đề đó.

---

# 19. Journaling: chống mất dữ liệu khi hỏng giữa chừng

Nhiều file system hiện đại dùng journaling.

Ý tưởng là:

> trước khi thực sự sửa dữ liệu, hệ thống ghi lại “kế hoạch sửa” vào một vùng nhật ký.

Nếu mất điện hoặc crash giữa chừng, hệ thống có thể đọc journal để khôi phục trạng thái nhất quán.

Đây là cơ chế cực kỳ quan trọng để tránh corruption.

Nó giúp file system không rơi vào trạng thái:

- nửa chừng sửa xong
    
- metadata đã đổi nhưng data chưa kịp ghi
    
- hoặc ngược lại
    

---

# 20. Cache và buffer: cầu nối giữa RAM và storage

Khi ứng dụng đọc file, hệ điều hành thường không đọc thẳng từng byte từ đĩa lên ứng dụng.

Nó dùng các lớp trung gian:

- page cache
    
- buffer cache
    
- read-ahead
    
- write-back
    

Mục tiêu là:

- giảm số lần truy cập chậm xuống storage
    
- gom nhiều thao tác nhỏ thành thao tác lớn
    
- tận dụng locality
    

Đây là một điểm rất quan trọng trong hiệu năng hệ thống.

Nhiều người tưởng ứng dụng đọc file là đọc ngay từ đĩa, nhưng thực tế thường đã qua cache.

---

# 21. Vì sao database rất quan tâm tới file system?

Database không sống bên ngoài file system. Nó dựa trên file system và storage.

Vì vậy database phải chú ý tới:

- page size
    
- block alignment
    
- fsync
    
- flush
    
- I/O pattern
    
- journaling
    
- latency
    
- durability
    

Một số chiến lược database thậm chí thiết kế riêng để tránh bị file system làm chậm hoặc khó dự đoán.

Đây là lý do backend engineer cần hiểu tầng lưu trữ, không chỉ biết viết query.

---

# 22. Vấn đề hiệu năng: không phải cứ nhanh là xong

Hiệu năng storage phụ thuộc vào:

- kiểu thiết bị: HDD hay SSD
    
- kiểu truy cập: tuần tự hay ngẫu nhiên
    
- kích thước block/page
    
- cache
    
- file system
    
- workload thực tế
    
- mức đồng thời
    
- pattern đọc/ghi
    

Ví dụ:

- HDD hợp với đọc tuần tự lớn
    
- SSD hợp với truy cập ngẫu nhiên nhiều
    
- small random write có thể làm SSD stress hơn tưởng tượng
    
- metadata-heavy workloads khác hoàn toàn data-heavy workloads
    

Nghĩa là phải hiểu tải thực tế, không thể chỉ nghe thông số quảng cáo.

---

# 23. Backend engineer cần nhớ gì từ chương này?

## 1. File không phải là một khái niệm mơ hồ

Nó là dữ liệu có metadata và được file system quản lý trên storage.

## 2. Storage không giống RAM

Storage chậm hơn, bền hơn, và có quy tắc ghi khác.

## 3. Block, sector, page là các mức trừu tượng khác nhau

Không nên lẫn lộn chúng.

## 4. SSD và HDD có bản chất khác nhau

Không chỉ là tốc độ.

## 5. Cache và journal ảnh hưởng trực tiếp đến dữ liệu thật

Bạn không thể xem nhẹ flush, fsync, hoặc durability.

## 6. Thiết kế hệ thống phải tính đến I/O pattern

Đọc ghi thế nào quan trọng không kém dữ liệu là gì.

---

# 24. Kết luận của chương

Từ RAM ta đi xuống storage, và từ storage ta đi tới file system.

Đây là bước chuyển từ:

- dữ liệu đang sống
    
- sang dữ liệu được giữ lâu dài
    

Nếu không hiểu tầng này, bạn sẽ chỉ thấy file là file, đĩa là đĩa, SSD là SSD.

Nếu hiểu tầng này, bạn sẽ thấy một hệ thống rất rõ:

- byte được mã hóa từ chương trước
    
- được gom thành block
    
- được file system quản lý
    
- được thiết bị vật lý lưu giữ
    
- được hệ điều hành cache và đồng bộ
    
- được ứng dụng đọc ghi theo quy luật
    

Đó là nền để sang chương tiếp theo:

> **database: vì sao không chỉ lưu file thẳng, tại sao cần index, B-tree, buffer pool, và vì sao hệ thống dữ liệu phải tổ chức sâu hơn file system rất nhiều**.

Nếu cần, tôi sẽ viết tiếp **Chương 16 — Database, index, B-tree, buffer pool** theo cùng mạch này.