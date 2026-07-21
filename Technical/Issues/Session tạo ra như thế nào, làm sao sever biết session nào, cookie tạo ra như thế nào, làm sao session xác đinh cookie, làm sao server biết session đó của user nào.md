**

- HTTP là stateless, nên serve muốn xác định request nào của ai, nó tạo ra một thứ là session. Session được tạo ra bởi function của ngôn ngữ lập trình, công nghệ tương ứng của nó, cái này có thể cấu hình tùy chỉnh. Nó tạo ra một file với id duy nhất. Khi kết hợp với cookie, nó sẽ lấy session id làm tiền đề để tạo cookie và trả về trình duyệt, mỗi lần request trình duyệt sẽ tự động đính kèm cookie, ở phía server dựa vào mã id định danh duy nhất sẽ biết được request đang gửi cho session nào. Session chỉ định phiên làm việc, nó sẽ không biết người đó là ai, dev phải định nghĩa thêm vào nội dung của chúng như user id, khi xác mình request nó sẽ đọc nội dung để biết request này có token hợp lệ đang được gửi bởi user nào.
    

