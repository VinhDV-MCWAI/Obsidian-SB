
+ Kiểm thử chức năng
	+ Kiểm tra chức năng
		+ UI
			+ Danh sách không có dữ liệu
				+ Danh sách có dữ liệu
				+ Danh sách khi có cột chứa data quá dài
			+ URL Có ID và Filter
				+ ID không tồn tại (đặc biệt id =10000000000000000000000000000)
				+ ID không phải số nguyên (float: [1.1.1.1.111], chữ [abc], kí tự đặc biệt [??#])
				+ ID/ filter rỗng hoặc thiếu
				+ URL chứa các ký tự độc hại (ví dụ: ' OR 1=1 --, <script>alert(1)</script>)
			 + Pagination
				 + Kiểm tra phân trang (số trang hợp lệ)
				+ Kiểm tra phân trang (số trang không hợp lệ: ngoài số phân trang, ví dụ có 2 phân trang, sửa id phân trang là 3)
			+ Dropdown list
				+ Kiểm tra khi không có option
				+ Kiểm tra UI khi option name quá dài
			+ Button
				+ Click nhiều lần vào các button sort, search, phân trang
				+ Click nhiều lần vào các button khi đang loading
			+ Drag& Drop
				+ Kéo thả item vào vị trí khác trong danh sách
				+ Kéo item rồi thả ra ngoài vùng drop
				+ Kéo thả nhanh nhiều lần
			+ Bulk action
				+ Bulk update [status]
				+ Bulk delete với dữ liệu hợp lệ
				+ Bulk delete với dữ liệu không hợp lệ (data được liên kết với record ở màn hình khác)
			+ Field input - Text Fields
				+ Kiểm tra validate (tối thiểu, tối đa, vượt tối đa)  <br>(bỏ trống, nhập kí tự [space] đối với field bắt buộc)
				+ Những field không có validate, nhập vào input text > 30000 ký tự ( có thể vượt quá hơn 50000 ký tự )
				+ Nhập kí tự (latin, non-latin (full-width/ half-width), unicode, đặc biệt)  <br>latin: [abcABCx]  <br>non-latin: full-width: [アアア], half-width: [âđêô], [ｱｱｱｱ]  <br>unicode: [U+1F642] hoặc [U+4E2D]  <br>đặc biệt: 😀, <>/?
				+ Copy/Paste nội dung (hợp lệ và không hợp lệ)
				+ Nhập HTML/JavaScript Code
			+ Field input - Number Fields
				+ Nhập sai định dạng (ví dụ: field kiểu int thì nhập float)
				+ Nhập số full-width/ half-width  <br>full-width: ０１２３  <br>half-width: 0123
				+ Copy/Paste nội dung (hợp lệ và không hợp lệ)
				+ Nhập kí tự đặc biệt: 😀, <>/?
				+ Kiểm tra validate (tối thiểu, tối đa, vượt tối đa)  <br>(bỏ trống đối với field bắt buộc)
			+ Field input - Date Fields
				+ Nhập ngày không hợp lệ (ví dụ ngày 02/30/2025)
				+ Nhập ngày sai định dạng (ví dụ mm/dd/yyyy thì nhập dd/mm/yyyy)
				+ Chọn ngày trong quá khứ quá xa (ví dụ: 01/01/0001)
				+ Chọn ngày trong tương lai quá xa (ví dụ: 31/12/10000000000000000000000000)
				+ Chọn ngày start date lớn hơn finish date
			+ Field tìm kiếm
				+ Nhập chuỗi tìm kiếm quá dài
				+ Nhập kí tự (latin, non-latin (full-width/ half-width), unicode, đặc biệt)
					+ latin: [abcABCx]
					+ non-latin: full-width: [アアア], half-width: [âđêô], [ｱｱｱｱ]  
					+ unicode: [U+1F642] hoặc [U+4E2D]
					+ đặc biệt: 😀, <>/?
				+ Nhập SQL/Injection (ví dụ: ' OR 1=1 --)
				+ Nhập HTML/JS code
				+ Ký tự xuống dòng (\n), carriage return (\r)
			+ Permission
			+ Truy cập URL/màn hình khi không có quyền
			+ Thao tác CRUD khi không có quyền
			+ Thay đổi quyền trong lúc user đang thao tác
			+ Cố gắng view/write data của department khác
			+ Thực hiện CRUD khi hết session
			+ Thực hiện CRUD khi tài khoản user bị vô hiệu hóa
			+ Tương tác trên cùng 1 item (cập nhật đồng thời, không refresh trang, thực hiện bằng 2 user/ 2 tab)
			+ User A mở item và sửa, User B xóa cùng item
			+ User A và B đồng thời thêm cùng dữ liệu giống nhau cho item không trùng lặp  <br>VD: User A và User B cùng thêm dữ liệu giống nhau cho category
			+ Hai user cùng chỉnh sửa item
			+ User A xóa item, User B xóa cùng item
			+ User A xóa item, User B duplicate item
			+ Thực hiện thao tác với một item trong khi item đó đang được sử dụng để tạo dữ liệu ở màn hình khác  <br>(cập nhật đồng thời, không refresh trang, thực hiện bằng 2 user/ 2 tab)|Trong lúc tạo data ở màn A (có chứa item B), item B được edit ở màn C
			+ Trong lúc tạo data ở màn A (có chứa item B), item B được delete ở màn C
			+ Tạo data ở màn A (có chứa item B), thực hiện xóa item B màn C
			+ Import, Upload file
			+ Gửi file MIME type khác nhau.  <br>VD: quy định upload img loại .png nhưng upload .jpeg
			+ Các chức năng download, upload file có cơ chế nén hoặc giải nén thì kiểm tra hoạt động trên các hệ điều hành khác nhau
			+ Kiểm tra file có kích thước khác nhau
			+ Kiểm tra tên file và nội dung file: Tên dài hàng nghìn kí tự, alphabet (in thường, in hoa, latin, non-latin), số (thập phân, full-width, Ả Rập,..), kí tự đặc biệt, unicode (emoji, ký tự điều khiển, kí tự kết hợp, ngôn ngữ khác,..), khoảng trắng đầu và cuối file, BOM, trùng tên...
			+ Kiểm tra thêm số lượng file
			+ Kiểm tra thay đổi nội dung file với loại file  <br>VD: Dùng tool để thay đổi metadata của file như: đuôi file .exe thành .jpg
			+ Kiểm tra gửi file với kích thước cực lớn, check tốc độ upload/download, check hoạt động khác của hệ thống: gọi API, load page,... có bị ảnh hưởng gì không
			+ Kiểm tra đường dẫn/thư mục lưu trữ: Có đường dẫn cực dài, đường dẫn có dấu, upload nhiều lần với file cùng tên.
			+ Kiểm tra tên, nội dung, kích thước, thời gian download file trên nhiều hệ điều hành khác nhau
			+ Import file với mã hóa không chuẩn (ví dụ: UTF-8)
			+ Export CSV
		+ Không có dữ liệu
		+ Xuất file với dữ liệu lớn, kiểm tra số lượng file, số lượng record, kiểm tra data
		+ Kiễm tra mã hóa (ví dụ UTF-8)
			+ Export PDF
		+ Xuất PDF với dữ liệu rỗng hoặc ít dữ liệu
		+ Xuất PDF với dữ liệu lớn, kiểm tra số lượng file, số lượng record
		+ Kiểm tra định dạng trang, layout, margin, header, footer
			+ Get List không giới hạn
		+ Kiểm tra số lượng cực lớn dữ liệu test những chức năng get list không giới hạn
			+ Security -  Sử dụng công cụ bên ngoài thao tác dữ liệu
		+ Kiểm tra CRUD, access hệ thống bằng công cụ bên ngoài như: postman, jmeter, Burp suite,...
	+ Kiểm tra giao diện sử dụng
	+ Kiểm tra dữ liệu và tính toàn vẹn dữ liệu
	+ Kiểm tra bảo mật và kiểm soát truy cập
+ Kiểm thử phi chức năng
	+ Kiểm tra hiệu suất
	+ Kiểm tra độ chịu tải
	+ Kiểm tra tính ổn định của hệ thống
	+ Kiểm tra độ bảo mật
	+ Kiểm tra độ tương thích ở môi trường và thiết bị khác nhau
	+ Kiểm tra tính hữu dụng của phần mềm
	+ Kiểm tra sao lưu phục hồi dữ liệu