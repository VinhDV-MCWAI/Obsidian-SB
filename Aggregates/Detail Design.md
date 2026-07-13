+ UI
	+ No
	+ Component Name: Tên component trên màn hình như : input username,.. hoặc tên button
	+ UI Positon / prerequisite : 
		+ Truy cập url nào ? Giải thích nó thuộc admin dashboard hay client, thuộc bộ phận nào ? 
		+ Có đăng nhập không ? dùng account nào ? có quyền gì ?
		+ Truy cập vào menu nào ? vd: click dropdown xxxx > chọn option aaaa > click button rrrr > hiển thị modal tttt > ....
		+ URL sau cùng
	+ Component:
		+ Text
		+ Link
		+ Input
		+ Checkbox
		+ Button
		+ Radio Button
		+ Image
		+ File
		+ Drag & Drop
		+ Combobox
		+ Icon
		+ Accordion
		+ Drop Down List
	+ Type
		+ Number
		+ Bool
		+ String
		+ None
		+ JSON
	+ Format
		+ Date: YYYY/mm/dd
		+ Number: 000.000.000
		+ Email
		+ Phone: 000-000-0000
		+ URL
	+ Required
		+ Yes
		+ No
		+ None
	+ Default value
	+ Description
		+ Mô tả ý nghĩa, nội dung, nó có tác dụng gì ?
		+ Readonly/desabled/hidden: theo điều kiện gì ?
		+ Rule logic thao tác như thế nào ? action ra sao
		+ Phân quyền ai được action: write, read
	+ Validation
		+ Điều kiện
		+ Message or popup thông báo, vị trí, nội dung, màu sắc,...
	+ Event
		+ Click:
			 + Component: Có hiện tượng gì, thay đổi nhưng gì
			 + Gọi API nào ?
			 + Mở model, chuyển trang đến đâu, mở tab mới là gì ?
		- Change:
			 +...
		+ Drag&drop
	+ API
		+ Tên api
		- Link: Điều hướng đến mô tả chi tiết api đó.
		- Ý nghĩa:
		- Response
	- Note
- API
	+ No
	+ API Name: Tên API (Ví dụ: `Create User`, `Get Product List`,...)
	+ Method / Protocol:
		- GET
		- POST 
		- PUT
		- PATCH 
		- DELETE
	+ Endpoint URL: Đường dẫn API (Ví dụ: `/api/v1/users/{id}`)
	- Giải thích cụ thể về môi trường/phân hệ (Admin, Client, Partner,...)
	+ Authentication & Authorization (Xác thực & Phân quyền):
		+ Yêu cầu Token: Yes / No
		- Loại Token: Bearer JWT / API Key / Session ID
	- Quyền truy cập (RBAC/Scopes): Role nào được gọi? (Ví dụ: `admin`, `manager:write`, `user:read`)
	+ Request Headers:
		- Content-Type (`application/json`, `multipart/form-data`, `application/x-www-form-urlencoded`)
		- Authorization
		- Custom Headers (nếu có)
	+ Request Parameters (Dữ liệu đầu vào)
		- Position (Vị trí đặt param):
		    - Path / URL Parameter (Ví dụ: `{id}`)
		    - Query Parameter (Ví dụ: `?page=1&limit=10`)
		    - Body Parameter (Payload gửi trong body)
		    - Form Data (Dùng khi upload file)
		- **Field Name:** Tên biến/trường dữ liệu đầu vào.
		- Type:
		    - String
		    - Number (Integer / Float)
		    - Boolean
		    - Array
		    - Object / JSON
		    - File (Binary)
		    - None
		- Format:
		    - Date/DateTime: `YYYY-MM-DD` / `YYYY-MM-DD HH:mm:ss` / `Timestamp`
		    - Email
		    - Phone
		    - UUID / ObjectId
		    - Regex cụ thể
		- Required:
		    - Yes
		    - No
		    - Conditional (Bắt buộc theo điều kiện logic nào?)
		- Default value: Giá trị mặc định nếu client không truyền lên.
		- Description: Mô tả ý nghĩa của trường dữ liệu, khoảng giá trị cho phép (Min/Max).
		+ Business Logic & Processing (Luồng xử lý nghiệp vụ)
			- Step-by-step Workflow: Các bước xử lý tuần tự của API từ khi nhận request đến khi trả response.
			- Database Interactions (Tương tác DB):
				- Table bị ảnh hưởng: Tên các bảng dữ liệu liên quan.
			    - Hành động CRUD: Select / Insert / Update / Delete.
		    - Logic truy vấn đặc biệt: Điều kiện Join, cách tính toán dữ liệu, các hàm trigger, DB Transaction (Commit/Rollback).
			- Background Jobs / Queues: Có đẩy tác vụ vào hàng đợi xử lý ngầm không? (Ví dụ: Gửi mail, push notification, sync data).
			- Third-party / External Services: Có gọi đến API của bên thứ 3 hoặc Microservice khác không? (Tên dịch vụ, mục đích).
		+ Validation & Error Handling (Kiểm tra dữ liệu & Xử lý lỗi)
			- Điều kiện validate:
				- Validate định dạng/kiểu dữ liệu (Data type, Format check).
				- Validate nghiệp vụ (Business validation): Kiểm tra trùng lặp (Duplicate check), kiểm tra tồn tại (Existence check), kiểm tra số dư, kiểm tra trạng thái hợp lệ,...
			- **HTTP Status Code tương ứng:** 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 409 (Conflict), 422 (Unprocessable Entity).
			- Error Response System:
				- Error Code nội bộ (Ví dụ: `USER_NOT_FOUND`, `INVALID_PASSWORD`).
			    - Error Message trả về cho UI hiển thị (Đa ngôn ngữ nếu có).
			    - Vị trí/Trường bị lỗi (`field_error`).
		+ Response Data (Dữ liệu đầu ra)
			- Success HTTP Status: 200 (OK), 201 (Created), 202 (Accepted), 204 (No Content).
			- Response Type: JSON / XML / File Stream / HTML.
			- Response Body Structure (Cấu trúc dữ liệu thành công):
			    - Field Name
			    - Type (String, Number, Bool, Array, Object)
			    - Format
			    - Description (Mô tả ý nghĩa trường trả về)
		+ Performance & Security Note (Lưu ý hiệu năng & Bảo mật)
			- **Data Caching:** Có lưu cache không? (Redis/Memcached), thời gian hết hạn (TTL) bao lâu? Cơ chế clear cache khi data thay đổi.
			- **Rate Limiting:** Giới hạn tối đa bao nhiêu request / giây / IP hoặc / User.
			- **Data Encryption:** Các trường dữ liệu nhạy cảm nào cần mã hóa khi lưu trữ hoặc khi truyền tải (Ví dụ: Password, Card Number).
			- **Pagination / Sorting / Filtering:** Cơ chế phân trang (Offset-based hoặc Cursor-based), các trường hỗ trợ sắp xếp và lọc.