Nếu các chương trước đã cho thấy máy tính lưu số như thế nào, thì chương này mở ra một sự thật rất quan trọng:

> Máy tính ban đầu không “biết chữ”.

Máy chỉ biết bit, byte, và các quy ước diễn giải.

Vì vậy, câu hỏi không còn là “lưu số thế nào”, mà là:

> **Làm thế nào để lưu chữ, dấu câu, ký hiệu, ngôn ngữ, và toàn bộ văn bản của loài người bằng một hệ bit hữu hạn?**

Đây là một trong những bước ngoặt lớn nhất trong lịch sử khoa học máy tính.

---

# 1. Vì sao chữ viết là một vấn đề riêng?

Con người giao tiếp bằng văn bản từ rất lâu:

- chữ cái
    
- số
    
- dấu chấm, phẩy
    
- ký hiệu toán học
    
- tiếng Hy Lạp
    
- chữ Latin có dấu
    
- chữ Ả Rập
    
- chữ Hán
    
- chữ Nhật
    
- chữ Hàn
    
- biểu tượng cảm xúc
    

Mỗi hệ chữ có số lượng ký tự và quy ước khác nhau.

Máy tính không thể lưu chữ theo kiểu “cảm tính”. Nó phải biến ký tự thành số.

Đó là bài toán **mã hóa ký tự**.

---

# 2. Ý tưởng nền tảng: ký tự là một mã số

Nguyên lý rất đơn giản:

- mỗi ký tự được gán một số
    
- khi lưu trữ, số đó được biến thành bit
    
- khi hiển thị, số được đổi ngược thành ký tự
    

Ví dụ:

- `A` → một số nào đó
    
- `B` → một số khác
    
- `1` → một số khác nữa
    

Như vậy, ký tự không còn là chữ theo nghĩa trực tiếp nữa, mà là **một giá trị mã hóa**.

Đây là một bước lặp lại đúng tinh thần các chương trước:

> Máy không hiểu nghĩa. Máy chỉ hiểu biểu diễn.

---

# 3. ASCII: giải pháp đầu tiên phổ biến

## ASCII là gì?

ASCII là một bảng mã ký tự rất sớm và rất quan trọng trong lịch sử máy tính.

Nó được thiết kế để mã hóa các ký tự cơ bản cho tiếng Anh và các ký hiệu điều khiển.

ASCII dùng 7 bit, nên biểu diễn được:

> **2^7 = 128 ký tự**

## Nó gồm những gì?

- chữ cái Latin in hoa
    
- chữ cái Latin in thường
    
- số 0 đến 9
    
- dấu câu cơ bản
    
- ký tự điều khiển như xuống dòng, tab
    

ASCII giải quyết rất tốt nhu cầu ban đầu của máy tính thương mại và truyền thông kỹ thuật số.

---

# 4. Vì sao ASCII ra đời là một bước tiến lớn?

Trước ASCII, nhiều hệ thống có bảng mã riêng.

Điều này tạo ra vấn đề nghiêm trọng:

- cùng một byte nhưng hai máy hiểu khác nhau
    
- dữ liệu truyền qua lại bị sai
    
- lưu file rồi mở ở máy khác bị hỏng ký tự
    
- hệ thống không tương thích
    

ASCII tạo ra một chuẩn chung đủ đơn giản để:

- lưu văn bản
    
- truyền dữ liệu
    
- in ấn
    
- giao tiếp giữa các hệ thống
    

Đây là một chiến thắng quan trọng của chuẩn hóa.

---

# 5. Vì sao ASCII thất bại trong thế giới thật?

ASCII chỉ có 128 mã.

Điều đó đủ cho:

- tiếng Anh cơ bản
    
- một ít ký hiệu
    
- điều khiển thiết bị
    

Nhưng không đủ cho thế giới thực.

Các ngôn ngữ khác cần:

- dấu
    
- ký tự mở rộng
    
- chữ không Latin
    
- ký hiệu chuyên ngành
    
- ngôn ngữ châu Á
    
- biểu tượng đặc biệt
    

Ví dụ:

- tiếng Việt cần dấu
    
- tiếng Pháp cần é, è, à, ç
    
- tiếng Đức cần ü, ö, ß
    
- tiếng Nga cần chữ Cyrillic
    
- tiếng Hy Lạp cần ký tự riêng
    
- tiếng Nhật, Trung, Hàn cần bộ ký tự hoàn toàn khác
    

ASCII không thể bao phủ toàn bộ.

Vấn đề bắt đầu từ đây.

---

# 6. Giai đoạn mở rộng: các bảng mã 8-bit và hỗn loạn tương thích

Để khắc phục hạn chế của ASCII, nhiều hệ thống dùng thêm 1 byte nữa, tức 8 bit, để mở rộng số lượng ký tự lên 256.

Nghe có vẻ là giải pháp ổn.

Nhưng thực tế sinh ra một vấn đề mới:

> Mỗi vùng, mỗi hãng, mỗi hệ điều hành lại tự định nghĩa phần mở rộng khác nhau.

Kết quả là:

- cùng một byte ở máy A là một ký tự
    
- nhưng ở máy B lại là ký tự khác
    
- file mở sai bảng mã sẽ thành mojibake
    
- văn bản bị rác ký tự
    

Đây là thời kỳ hỗn loạn mã hóa.

Mỗi nơi một chuẩn, nhưng không có chuẩn toàn cầu thật sự.

---

# 7. Unicode: ý tưởng thống nhất toàn bộ văn bản của thế giới

Unicode ra đời để giải quyết tận gốc vấn đề:

> Một hệ thống mã hóa duy nhất cho tất cả ký tự của tất cả ngôn ngữ.

Điểm quan trọng của Unicode là:

- không còn giới hạn ở 128 hay 256 ký tự
    
- mỗi ký tự có một mã số chuẩn gọi là **code point**
    
- bao phủ hầu như toàn bộ hệ chữ và ký hiệu của loài người
    

Ví dụ:

- `A`
    
- `á`
    
- `Ж`
    
- `你`
    
- `あ`
    
- `한`
    
- `😀`
    

đều có thể được gán một code point riêng.

Unicode không chỉ là “bảng mã lớn hơn”.

Nó là nền tảng để thống nhất ngôn ngữ số trên toàn cầu.

---

# 8. Code point là gì?

Code point là mã số của ký tự trong Unicode.

Ví dụ:

- `A` có một code point
    
- `你` có một code point khác
    
- `😀` có một code point khác nữa
    

Điều rất quan trọng là:

> Unicode định nghĩa ký tự là gì và mã số của nó là gì.

Nhưng Unicode **không trực tiếp quy định cách lưu bit trong bộ nhớ** theo một kiểu duy nhất.

Đây là chỗ nhiều người nhầm.

Unicode là “hệ mã chuẩn”.

Còn cách biến code point thành byte thực tế lại do các họ mã hóa UTF đảm nhận.

---

# 9. UTF là gì?

UTF là nhóm các cách mã hóa Unicode thành byte.

Phổ biến nhất là:

- UTF-8
    
- UTF-16
    
- UTF-32
    

Chúng đều phục vụ cùng một mục tiêu:

> lưu và truyền Unicode bằng chuỗi byte

Nhưng mỗi cách có đánh đổi riêng.

---

# 10. UTF-32: đơn giản nhưng tốn bộ nhớ

UTF-32 thường dùng 4 byte cho một code point.

## Ưu điểm

- rất đơn giản
    
- truy cập trực tiếp
    
- dễ tính vị trí nếu mọi ký tự có kích thước cố định
    

## Nhược điểm

- rất tốn dung lượng
    
- lãng phí cho văn bản chủ yếu là ASCII hoặc ký tự ngắn
    

UTF-32 hiếm khi là lựa chọn tối ưu cho lưu trữ thông thường.

---

# 11. UTF-16: trung gian giữa độ đơn giản và tiết kiệm

UTF-16 thường dùng 2 byte cho phần lớn ký tự thông dụng, và nhiều hơn cho ký tự đặc biệt.

## Ưu điểm

- tiết kiệm hơn UTF-32
    
- phù hợp với nhiều hệ thống
    

## Nhược điểm

- không phải mọi ký tự đều cố định 2 byte
    
- xử lý độ dài chuỗi, cắt chuỗi, duyệt ký tự phức tạp hơn
    

UTF-16 từng rất phổ biến trong nhiều hệ sinh thái.

---

# 12. UTF-8: giải pháp thắng thế trên Internet

UTF-8 là mã hóa có độ dài biến thiên.

## Ý tưởng

- ký tự ASCII dùng 1 byte
    
- ký tự ngoài ASCII dùng nhiều byte hơn, từ 2 đến 4 byte
    

## Ưu điểm lớn

- tương thích ngược với ASCII
    
- tiết kiệm cho văn bản tiếng Anh
    
- đủ mạnh để mã hóa toàn bộ Unicode
    
- phù hợp cho web, API, file text, hệ thống phân tán
    

Chính vì vậy, UTF-8 trở thành chuẩn thực tế rất phổ biến trên Internet.

---

# 13. Vì sao UTF-8 thắng?

UTF-8 thắng vì nó giải quyết được đồng thời nhiều mục tiêu:

1. **Tương thích với ASCII**
    
    - Văn bản ASCII cũ vẫn đọc được như cũ.
        
2. **Tiết kiệm bộ nhớ**
    
    - Văn bản tiếng Anh cơ bản không bị tốn quá nhiều.
        
3. **Đủ bao phủ Unicode**
    
    - Có thể biểu diễn toàn bộ ký tự hiện đại.
        
4. **Dễ truyền qua hệ thống mạng**
    
    - Là chuỗi byte rõ ràng, linh hoạt, ít mơ hồ.
        

Đây là kiểu giải pháp rất điển hình trong lịch sử kỹ thuật:

> không cần tối ưu cho một trường hợp duy nhất, mà tối ưu cho tính tương thích và tổng quát.

---

# 14. Vấn đề khi lưu văn bản: không chỉ là mã hóa

Lưu chữ không chỉ là “byte nào tương ứng ký tự nào”.

Còn nhiều lớp vấn đề khác:

- encoding
    
- decoding
    
- collation
    
- normalization
    
- grapheme cluster
    
- font rendering
    
- line break
    
- locale
    

Tức là từ ký tự đến văn bản hiển thị, còn rất nhiều tầng trung gian.

Máy tính không lưu “chữ đẹp” hay “chữ đúng nghĩa”.  
Nó chỉ lưu byte.  
Mọi thứ còn lại là quy ước và xử lý.

---

# 15. Tại sao cùng một chuỗi byte có thể hiển thị sai?

Nếu một chuỗi byte được:

- mã hóa bằng UTF-8
    
- nhưng lại được đọc như ISO-8859-1
    
- hoặc Windows-1252
    
- hoặc một bảng mã cũ khác
    

thì ký tự sẽ bị lỗi.

Đây là hiện tượng thường gọi là **mojibake**.

Nói đơn giản:

> dữ liệu không hỏng, nhưng cách diễn giải sai.

Điều này rất giống các chương trước:

- cùng bit
    
- nhưng diễn giải khác
    
- thì nghĩa khác hoàn toàn
    

---

# 16. Unicode không đồng nghĩa với “một ký tự = một byte”

Đây là hiểu lầm rất phổ biến.

Thực tế:

- một ký tự Unicode có thể chiếm 1, 2, 3 hoặc 4 byte trong UTF-8
    
- có ký tự nhìn như một đơn vị nhưng thực ra là tổ hợp nhiều code point
    
- có dấu ghép, emoji ghép, và biến thể trình bày phức tạp
    

Ví dụ:

- một chữ cái có dấu có thể được biểu diễn theo nhiều cách khác nhau
    
- một emoji có thể là chuỗi nhiều code point kết hợp
    

Vì thế, xử lý chuỗi không bao giờ đơn giản như đếm ký tự bằng số byte.

---

# 17. Normalization: cùng một chữ nhưng có thể viết theo nhiều cách

Một văn bản có thể trông giống nhau trên màn hình, nhưng nội tại byte lại khác.

Ví dụ:

- một ký tự có dấu có thể là một code point đơn
    
- hoặc là ký tự gốc + dấu kết hợp
    

Nếu hệ thống không chuẩn hóa, sẽ phát sinh lỗi:

- so sánh chuỗi sai
    
- tìm kiếm không ra
    
- trùng dữ liệu giả
    
- hash khác nhau dù nhìn giống nhau
    

Đây là lý do văn bản không chỉ là vấn đề hiển thị. Nó là vấn đề logic dữ liệu.

---

# 18. Unicode trong thực tế backend

Đây là phần liên hệ trực tiếp với công việc.

## 1. Database phải thống nhất encoding

Nếu DB, driver và ứng dụng dùng khác chuẩn, dữ liệu sẽ lỗi.

## 2. API phải xác định rõ charset

Khi nhận và trả text, phải biết chuỗi đang là UTF-8 hay gì khác.

## 3. Search và sort phụ thuộc collation

Cùng một chuỗi, cách sắp xếp và so sánh có thể khác nhau theo ngôn ngữ và quy ước.

## 4. Không được giả định “length” là số ký tự

`length`, `byte length`, và số ký tự người dùng nhìn thấy là ba chuyện khác nhau.

## 5. Dùng emoji, tiếng Việt, và ký tự quốc tế cần kiểm thử thật

Không nên test chỉ với chữ ASCII.

---

# 19. Liên hệ với cấu hình database hiện đại

Đây là chỗ rất thực dụng.

Khi bạn thấy các cấu hình như:

- `utf8mb4`
    
- `utf8mb4_unicode_ci`
    
- `collation`
    
- `charset`
    

thì đó không phải chi tiết trang trí.

Nó là quyết định nền tảng về:

- dữ liệu có lưu đúng không
    
- có lưu được emoji không
    
- có so sánh chuẩn không
    
- có tìm kiếm đúng không
    
- có gặp lỗi encoding khi tích hợp không
    

Đặc biệt, nhiều hệ thống lỗi vì tưởng rằng “utf8” là đủ, nhưng trong một số nền tảng cũ, đó chưa chắc là UTF-8 đầy đủ theo nghĩa hỗ trợ mọi ký tự 4 byte.

Vì thế, lựa chọn encoding và collation phải được xem là một phần của thiết kế dữ liệu, không phải cấu hình phụ.

---

# 20. Kết luận của chương

ASCII là giải pháp cho một thế giới nhỏ.

Unicode là giải pháp cho một thế giới toàn cầu.

UTF là cách biến chuẩn đó thành byte để máy tính lưu và truyền.

Từ đây, ta rút ra một nguyên lý rất quan trọng:

> Càng tiến gần đến dữ liệu thực tế của con người, vấn đề càng không còn là tính toán đơn thuần, mà là chuẩn hóa biểu diễn và diễn giải.

Đây chính là lý do làm việc với văn bản trong hệ thống phần mềm tưởng đơn giản nhưng lại rất dễ lỗi.

Ở chương tiếp theo, ta sẽ đi sang lớp lưu trữ nền tảng hơn nữa:

> **hệ thống file, sector, block, page, file system và cách dữ liệu được đặt lên thiết bị lưu trữ**.

Nếu cần, tôi sẽ viết tiếp **Chương 15 — File System, sector, block, page, SSD/HDD** theo cùng mạch này.