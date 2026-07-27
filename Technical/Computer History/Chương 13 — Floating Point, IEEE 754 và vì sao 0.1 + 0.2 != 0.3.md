Nếu ở chương trước ta đã thấy số nguyên bị giới hạn bởi số bit, thì với số thực, vấn đề còn khó hơn.

Con người muốn máy tính tính được:

- 0.1
    
- 3.14
    
- -12.5
    
- 1/3
    
- 0.000000123
    
- 10^20
    

Nhưng máy tính không thể lưu mọi số thực theo cách “đúng tuyệt đối” như trong toán học thuần túy.

Lý do cốt lõi là:

> Máy tính có bộ nhớ hữu hạn, còn tập số thực là vô hạn.

Vì vậy, máy buộc phải dùng một cách xấp xỉ.

Đó chính là **floating point**.

---

# 1. Vì sao số thực khó hơn số nguyên?

Số nguyên có thể biểu diễn trực tiếp bằng bit theo kiểu đếm.

Ví dụ:

- 0
    
- 1
    
- 2
    
- 3
    
- 4
    

Đây là các giá trị rời rạc, hữu hạn trong một miền bit nhất định.

Nhưng số thực thì khác.

Giữa 0 và 1 có vô số số:

- 0.1
    
- 0.01
    
- 0.001
    
- 0.333333...
    
- 0.314159...
    
- 0.0000000001
    

Không thể có đủ bit để lưu chính xác mọi số thực.

Do đó, máy tính phải chọn:

- lưu gần đúng,
    
- trong phạm vi hữu hạn,
    
- với một độ chính xác nhất định.
    

Đây là nền tảng của số dấu chấm động.

---

# 2. Ý tưởng của floating point

Floating point hoạt động giống cách viết số khoa học.

Con người thường viết:

> 6.02 × 10^23

thay vì viết cả chuỗi số rất dài.

Máy tính cũng làm tương tự, nhưng với hệ nhị phân:

> **số = dấu × trị số × 2^mũ**

Tức là một số dấu chấm động thường được tách thành ba phần:

- **sign**: dấu dương hay âm
    
- **exponent**: số mũ
    
- **fraction / mantissa / significand**: phần trị số chính
    

Ý tưởng này giúp biểu diễn được cả số rất nhỏ lẫn số rất lớn.

---

# 3. Tại sao phải tách thành sign, exponent, fraction?

Nếu chỉ lưu một chuỗi bit thô, ta sẽ không thể cân bằng giữa:

- độ lớn của số,
    
- và độ chính xác của phần thập phân.
    

Ví dụ:

- số rất lớn cần nhiều bit cho phần mũ
    
- số rất nhỏ cần khả năng dịch vị trí dấu chấm
    
- số cần nhiều chữ số chính xác cần nhiều bit cho phần trị
    

Floating point giải quyết bằng cách chia tài nguyên bit thành các phần có vai trò khác nhau.

Điều này là một thỏa hiệp kỹ thuật rất điển hình:

> Không thể có tất cả cùng lúc: vừa cực lớn, vừa cực nhỏ, vừa cực chính xác, vừa cực rẻ.

---

# 4. IEEE 754 là gì?

Để các máy tính khác nhau hiểu số thực giống nhau, người ta cần một chuẩn chung.

Chuẩn đó là **IEEE 754**.

Đây là chuẩn quy định cách biểu diễn số dấu chấm động trong phần cứng và phần mềm.

Nhờ có chuẩn này, cùng một giá trị số thực sẽ được diễn giải thống nhất hơn giữa các hệ thống.

IEEE 754 đặc tả nhiều thứ, trong đó quan trọng nhất là:

- cách chia bit cho sign / exponent / fraction
    
- cách biểu diễn số 0
    
- số vô cùng
    
- NaN
    
- số rất nhỏ bất thường
    
- quy tắc làm tròn
    

---

# 5. Cấu trúc của floating point

Một số floating point thường gồm:

- 1 bit dấu
    
- vài bit exponent
    
- vài bit fraction
    

Ví dụ với 32-bit float thường gặp:

- 1 bit sign
    
- 8 bit exponent
    
- 23 bit fraction
    

Với 64-bit double thường gặp:

- 1 bit sign
    
- 11 bit exponent
    
- 52 bit fraction
    

Số bit dành cho exponent và fraction càng nhiều thì số càng biểu diễn được phạm vi rộng hơn hoặc chính xác hơn.

Nhưng không thể tăng vô hạn cả hai.

Đây lại là một bài toán đánh đổi.

---

# 6. Cách số floating point được hiểu

Ta có thể hình dung số floating point theo công thức khái quát:

> **(-1)^sign × 1.fraction × 2^(exponent - bias)**

Trong đó:

- `sign` quyết định số dương hay âm
    
- `fraction` lưu phần trị
    
- `bias` là giá trị điều chỉnh để exponent có thể lưu cả số âm lẫn dương
    

Điểm quan trọng là dấu chấm nhị phân không cố định ở một chỗ.

Nó “trôi” theo exponent, nên mới gọi là floating point.

---

# 7. Vì sao `0.1` không biểu diễn chính xác?

Đây là câu hỏi kinh điển.

Trong hệ thập phân, 0.1 là một số rất đơn giản.

Nhưng trong hệ nhị phân, 0.1 lại là một phân số vô hạn tuần hoàn.

Tương tự như:

- 1/3 trong hệ thập phân là 0.333333...
    
- 1/10 trong hệ nhị phân cũng không có dạng kết thúc hữu hạn dễ dàng
    

Vì vậy, khi máy tính lưu `0.1`, nó chỉ lưu được **một giá trị gần đúng nhất có thể** trong số bit giới hạn.

Do đó:

- `0.1` không được lưu chính xác tuyệt đối
    
- `0.2` cũng vậy
    
- cộng hai giá trị gần đúng lại sẽ ra một giá trị gần đúng khác
    

Kết quả cuối cùng có thể là:

> `0.1 + 0.2 != 0.3`

Không phải vì máy tính sai toán.

Mà vì máy tính đang làm toán trên biểu diễn gần đúng.

---

# 8. Sai số làm tròn là gì?

Khi một số thực không thể biểu diễn chính xác, hệ thống phải làm tròn.

Đây là **rounding error**.

Sai số này thường nhỏ, nhưng có thể tích lũy.

Ví dụ:

- cộng rất nhiều số nhỏ
    
- lặp lại phép tính hàng triệu lần
    
- tính lãi suất
    
- tính tỷ lệ
    
- tính tổng tiền
    
- mô phỏng vật lý
    
- xử lý tín hiệu
    

Nếu sai số làm tròn tích lũy, kết quả cuối cùng có thể lệch đáng kể.

Đây là lý do floating point rất mạnh, nhưng cũng rất nguy hiểm nếu dùng sai.

---

# 9. Precision và range: hai thứ không giống nhau

Khi làm việc với số thực, có hai khái niệm rất hay bị nhầm:

## Range

Miền giá trị mà số có thể biểu diễn.

Ví dụ: từ rất nhỏ đến rất lớn.

## Precision

Độ chính xác chi tiết trong khoảng giá trị đó.

Ví dụ: lưu được bao nhiêu chữ số có nghĩa.

Một kiểu floating point có thể:

- biểu diễn được số rất lớn
    
- nhưng không giữ được nhiều chữ số thập phân chính xác
    

Hoặc ngược lại.

Đây là lý do không nên nhìn “float lớn hay nhỏ” một cách mơ hồ. Phải biết nó cần:

- phạm vi,
    
- hay độ chính xác,
    
- hay cả hai.
    

---

# 10. Zero, subnormal, infinity, NaN

IEEE 754 không chỉ có số bình thường.

Nó còn có các giá trị đặc biệt.

## Zero

Có thể có +0 và -0.

Điều này nghe kỳ lạ, nhưng hữu ích trong một số phép toán và quy ước toán học.

## Infinity

Khi một kết quả vượt quá giới hạn biểu diễn, hệ thống có thể tạo:

- +∞
    
- -∞
    

## NaN

NaN là viết tắt của **Not a Number**.

Nó xuất hiện khi kết quả không xác định hoặc không hợp lệ, ví dụ:

- 0/0
    
- sqrt(-1) trong miền số thực thường
    
- các phép toán lỗi khác
    

Các giá trị đặc biệt này giúp máy tính không chỉ “im lặng sai”, mà có thể gắn cờ trạng thái lỗi toán học.

---

# 11. Vì sao floating point không phù hợp cho tiền tệ?

Đây là ứng dụng thực tế cực kỳ quan trọng.

Tiền tệ đòi hỏi:

- chính xác tuyệt đối theo đơn vị nhỏ nhất quy định
    
- cộng trừ ổn định
    
- so sánh chính xác
    
- không được trôi sai số
    

Floating point không phù hợp để lưu tiền nếu cần độ chính xác tuyệt đối.

Ví dụ:

- 0.1 VND không có nghĩa thực tế
    
- nhưng với đơn vị lớn hơn hoặc quy đổi phức tạp, sai số tích lũy là vấn đề
    

Thay vào đó, hệ thống tiền tệ thường dùng:

- số nguyên theo đơn vị nhỏ nhất
    
- hoặc decimal fixed-point / kiểu số thập phân chính xác hơn
    

Đây là lý do backend engineer phải rất cẩn thận.

---

# 12. Fixed-point và floating-point

## Fixed-point

Dấu chấm nằm ở vị trí cố định.

Ví dụ:

- lưu 12345 nhưng hiểu là 123.45
    

Ưu điểm:

- dễ kiểm soát
    
- chính xác hơn cho tiền tệ
    
- không có sai số nhị phân kiểu floating-point theo cách thường gặp
    

Nhược điểm:

- kém linh hoạt hơn
    
- phạm vi hạn chế hơn
    
- cần quy ước rõ ràng về số chữ số thập phân
    

## Floating-point

Dấu chấm “trôi” theo exponent.

Ưu điểm:

- biểu diễn được dải rất rộng
    
- thuận tiện cho khoa học, kỹ thuật, đồ họa, tính toán số học lớn
    

Nhược điểm:

- sai số làm tròn
    
- khó dùng cho tiền tệ và các nghiệp vụ đòi hỏi tuyệt đối chính xác
    

---

# 13. Vì sao CPU và phần cứng lại hỗ trợ floating point?

Vì các hệ thống thực tế cần:

- đồ họa
    
- khoa học dữ liệu
    
- mô phỏng
    
- machine learning
    
- tín hiệu số
    
- tính toán kỹ thuật
    
- đo lường vật lý
    

Những miền này cần số rất lớn, rất nhỏ, và thay đổi linh hoạt.

Nếu chỉ dùng số nguyên, hệ thống sẽ bất tiện và không đủ khả năng biểu diễn.

Do đó, phần cứng tạo ra các đơn vị và lệnh hỗ trợ floating point.

Đây là bước tiến từ:

- cộng/trừ nguyên thủy
    
- sang mô hình số học phức tạp hơn
    
- nhưng vẫn nằm trong giới hạn phần cứng hữu hạn
    

---

# 14. Vì sao so sánh số thực rất dễ sai?

Khi hai số đều là giá trị gần đúng, so sánh trực tiếp có thể thất bại.

Ví dụ:

- sau nhiều phép tính, giá trị thực tế có thể là 0.30000000000000004
    
- trong khi bạn kỳ vọng 0.3
    

Nếu so sánh bằng `==`, kết quả có thể sai theo mặt nghiệp vụ.

Cách đúng thường là so sánh theo sai số cho phép, gọi là **epsilon**.

Tức là:

> coi hai số bằng nhau nếu chênh lệch đủ nhỏ

Điều này phản ánh đúng bản chất của floating point: xấp xỉ, không tuyệt đối.

---

# 15. Những lỗi thực tế do floating point gây ra

Trong hệ thống thực, floating point dễ gây lỗi ở:

- tính tiền
    
- chia tỉ lệ
    
- cộng dồn số liệu
    
- dashboard
    
- báo cáo
    
- thống kê
    
- mô phỏng
    
- thuật toán học máy
    
- tính khoảng cách, góc, tọa độ
    

Nếu không hiểu bản chất, bạn sẽ gặp các triệu chứng như:

- tổng không khớp
    
- kiểm tra bằng nhau thất bại
    
- sort hoặc grouping lệch
    
- giá trị hiển thị lạ
    
- sai số tích lũy qua nhiều bước xử lý
    

---

# 16. Tại sao chương này nối trực tiếp với database và API?

Vì rất nhiều dữ liệu thực tế đi qua đường này:

- DB lưu số
    
- API serialize số
    
- frontend parse số
    
- backend tính toán số
    
- báo cáo tổng hợp số
    

Nếu chỉ một tầng dùng float còn tầng khác kỳ vọng decimal hoặc integer, kết quả có thể lệch.

Đây là lý do khi thiết kế hệ thống, cần xác định rõ:

- dữ liệu là số nguyên hay số thực
    
- có yêu cầu chính xác tuyệt đối không
    
- có được phép sai số xấp xỉ không
    
- đơn vị lưu trữ là gì
    
- cách serialize/deserialize ra sao
    

---

# 17. Backend engineer cần nhớ gì từ chương này?

## 1. Không dùng floating point cho tiền nếu không hiểu rõ hậu quả

Tiền là miền cần chính xác. Nếu dùng float, phải hiểu rõ sai số và giới hạn.

## 2. Không so sánh số thực bằng `==` một cách mù quáng

Nên so sánh bằng ngưỡng sai số khi cần.

## 3. Chọn kiểu dữ liệu theo miền nghiệp vụ

Số đo, tỷ lệ, tiền, khoảng cách, thống kê đều có yêu cầu khác nhau.

## 4. Hiểu rằng số trong máy là biểu diễn gần đúng

Khi nhìn kết quả lạ, phải nghĩ tới cách lưu chứ không chỉ nghĩ tới logic code.

## 5. Khi thiết kế hệ thống, phải xác định unit rất rõ

Ví dụ:

- VND hay xu
    
- giây hay mili-giây
    
- MB hay byte
    
- phần trăm hay tỷ lệ thập phân
    

Thiếu unit là nguồn lỗi lớn.

---

# 18. Kết luận của chương

Floating point là cách máy tính đánh đổi giữa:

- phạm vi biểu diễn rộng,
    
- tính hiệu quả,
    
- và độ chính xác tuyệt đối.
    

Nó không sai. Nó chỉ là một mô hình xấp xỉ có quy tắc.

Hiểu floating point là hiểu một trong những ranh giới quan trọng nhất của tính toán số học trong máy tính.

Từ đây, ta đã đi qua:

- thông tin
    
- bit và byte
    
- CPU
    
- RAM
    
- số nguyên
    
- số thực
    

Chương tiếp theo tự nhiên sẽ là nơi máy tính bắt đầu xử lý thứ gần với con người nhất nhưng cũng gây rối nhiều nhất:

> **ký tự, văn bản, ASCII, Unicode và UTF-8**.

Nếu cần, tôi sẽ viết tiếp **Chương 14 — ASCII, Unicode, UTF-8** theo cùng mạch này.