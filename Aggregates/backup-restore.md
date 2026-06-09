
  

## Khái niệm Cơ bản

  

### Sao lưu Dữ liệu

  

Quá trình sao lưu dữ liệu là quá trình đóng gói các dữ liệu của môi trường tại một thời điểm nhất định, dữ liệu đó là những gì được lưu trữ và sử dụng cho môi trường tại thời điểm đó. Dữ liệu này có thể là:

  

- Cơ sở dữ liệu

- Tệp hoặc thư mục có cấu trúc được lưu trong các storage

- Tệp cấu hình hệ thống

  

Chúng được gom nhóm, đóng gói và lưu trữ lại ở nhiều nơi khác nhau tùy theo mục đích sử dụng. Phổ biến lưu dữ liệu này ở local, máy tính cá nhân, máy chủ, cloud,...

  

### Khôi phục Dữ liệu

  

Quá trình khôi phục dữ liệu là quá trình lấy dữ liệu đã được sao lưu và khôi phục lại môi trường. Quá trình này thường được thực hiện khi:

  

- Môi trường bị lỗi

- Dữ liệu bị mất

- Cần di chuyển sang môi trường mới

  

Quá trình thực hiện ngược lại so với sao lưu. Nếu các thay đổi liên quan đến cấu hình, bạn có thể hoặc không phải build lại môi trường, nhưng cần build lại để cập nhật cấu hình nếu thay đổi không áp dụng runtime.

  

#### Vấn đề Tính Toàn Vẹn Dữ liệu

  

Trong quá trình triển khai có thể sai khác dữ liệu trong quá trình build. Không thể sử dụng cách build mới môi trường và cho chúng hoạt động song song với môi trường cũ. Sau đó chuyển hướng người dùng đến môi trường mới đồng thời xóa môi trường cũ. Tuy nhiên, người dùng đang thao tác dữ liệu trên môi trường cũ, nên khi chuyển sang môi trường mới sẽ không có dữ liệu mới nhất, dẫn đến mất dữ liệu.

  

Do đó, khi diễn ra quá trình này cần proxy điều hướng traffic người dùng đến màn hình bảo trì để đảm bảo người dùng không thao tác gì thay đổi dữ liệu trong quá trình triển khai, nhằm đảm bảo tính toàn vẹn dữ liệu. Sau khi hoàn thiện, điều hướng người dùng trở lại hệ thống.

  

---

  

# Tài liệu Tiêu chuẩn: Chiến lược Sao lưu và Phục hồi Thảm họa

  

**Mức độ bảo mật:** Nội bộ (Internal)  

**Phạm vi áp dụng:** Môi trường Containerized (Docker/Docker Compose) của hệ thống Second Memory

  

**Mức độ bảo mật:** Nội bộ (Internal)

**Phạm vi áp dụng:** Môi trường Containerized (Docker/Docker Compose) của hệ thống Second Memory.

  

## 1. Tổng quan & Mục tiêu

  

Tài liệu này quy định các tiêu chuẩn kỹ thuật cấp cao và quy trình vận hành chuẩn (SOP) cho việc bảo toàn dữ liệu và phục hồi thảm họa **(Disaster Recovery - DR)**. Mục tiêu cốt lõi là đảm bảo:

  

- **Tính toàn vẹn** dữ liệu

- **Tính khả dụng** của hệ thống

- **Khả năng lưu trữ, truy xuất** tài liệu dài hạn mà không gặp rủi ro mất mát thông tin dưới bất kỳ hình thức nào

  

### KPIs - Chỉ số Đo lường Hiệu quả

  

Hệ thống tuân thủ các chỉ số sau theo chuẩn doanh nghiệp:

  

- **RPO (Recovery Point Objective):** < 24 giờ

  - Dữ liệu mất mát tối đa cho phép không vượt quá 24 giờ thao tác

- **RTO (Recovery Time Objective):** < 30 phút

  - Thời gian gián đoạn tối đa để khôi phục toàn bộ dịch vụ cốt lõi kể từ khi kích hoạt kịch bản DR

- **Tính nhất quán nguyên tử (Atomic Consistency):**

  - Một điểm khôi phục (Restore Point) phải là sự khớp nối hoàn hảo về thời gian giữa:

    - Cơ sở dữ liệu (PostgreSQL)

    - Hệ thống tệp (MinIO)

    - Cấu hình môi trường (`.env`)

  

## 2. Kiến trúc Hệ thống DR

  

Kiến trúc sao lưu được phân tách thành **3 phân hệ độc lập** nhằm giảm thiểu rủi ro điểm lỗi đơn lẻ *(Single Point of Failure)*:

  

### Phân hệ 1: Dữ liệu (Stateful Tier)

  

Bao gồm:

- **PostgreSQL** (Relational Data)

- **MinIO** (Object Storage)

  

Đây là nơi chứa giá trị cốt lõi của hệ thống.

  

### Phân hệ 2: Điều phối (Orchestration Tier)

  

Bao gồm:

- Các kịch bản tự động hóa: `backup.sh`, `restore.sh`

  

Đóng vai trò Controller, xử lý logic:

- Nén dữ liệu

- Mã hóa dữ liệu

- Định tuyến luồng dữ liệu

  

### Phân hệ 3: Phân tán (Sovereignty Storage Tier)

  

- Tích hợp **Rclone** làm cầu nối luân chuyển bản sao lưu lên môi trường Multi-Cloud

- Hỗ trợ các cloud storage: Google Drive, OneDrive, AWS S3

- Dự phòng rủi ro vật lý tại máy chủ cục bộ

  

## 3. Quy trình Sao lưu Tự động (Automated Backup Lifecycle)

  

Tiến trình sao lưu được kích hoạt định kỳ *(Cronjob)* và thực thi qua kịch bản `backup/backup.sh` theo luồng **4 bước**:

  

### Bước 1: Trích xuất Dữ liệu Định tuyến (Database Snapshot)

  

Sử dụng `pg_dump` với định dạng **Custom format (`-Fc`)**:

- Định dạng nhị phân tối ưu nhất của PostgreSQL

- Tích hợp sẵn nén nội bộ

- Cho phép khôi phục cực nhanh thông qua công cụ `pg_restore`

  

### Bước 2: Đồng bộ Dữ liệu Phi cấu trúc (Media Mirroring)

  

Kích hoạt `mc mirror` *(MinIO Client)*:

- Quét và đồng bộ delta changes (chỉ copy các file thay đổi) ra vùng nhớ đệm tại Host

- Đảm bảo giữ nguyên Metadata

  

### Bước 3: Đóng gói Định danh (Versioning & Archiving)

  

Gộp tất cả các thành phần thành một khối (Archive) duy nhất:

- SQL Dump

- Thư mục MinIO

- Tệp `.env`

  

Định dạng đặt tên: `system_backup_YYYYMMDD_HHMMSS.tar.gz`

  

### Bước 4: Phân phối & Vòng đời (Distribution & Retention)

  

Thực hiện các tác vụ sau:

  

- **Đẩy bản sao lưu** lên các node Cloud định sẵn trong biến `RCLONE_REMOTES`

- **Xóa bản sao lưu cũ** trên Cloud: Tự động xóa các bản sao lưu vượt quá ngưỡng **3 ngày** (tuỳ chỉnh qua tham số `--min-age`)

- **Xóa thư mục cục bộ** (`backups/`): Xóa toàn bộ ngay sau khi hoàn tất việc tải lên Cloud để tối ưu tài nguyên lưu trữ *(Zero-Local Footprint)*

  

### Chạy Backup

  

**Backup thủ công hoặc qua Cronjob:**

  

```bash

bash backup/backup.sh

```

  

## 4. Quy trình Phục hồi & Vận hành Cắt lớp (Restore & Cutover SOP)

  

:::danger CẢNH BÁO QUAN TRỌNG

Quá trình phục hồi yêu cầu thời gian gián đoạn dịch vụ *(Downtime Window)*. Tuyệt đối **KHÔNG** chạy song song hệ thống cũ và hệ thống đang restore để tránh phân mảnh và ghi đè dữ liệu *(Data Corruption)*. Việc khởi động container phải tuân thủ nghiêm ngặt theo trình tự **4 giai đoạn** dưới đây.

:::

  

### Giai đoạn 1: Cách ly & Thiết lập Trạng thái Bảo trì (Maintenance Mode)

  

- Chuyển hướng toàn bộ lưu lượng truy cập *(Traffic)* thông qua Nginx/Reverse Proxy đến trang trạng thái "Hệ thống đang bảo trì"

- Tắt toàn bộ các container ứng dụng *(Stateless)* hiện tại để chặn mọi Transaction mới

  

### Giai đoạn 2: Tái thiết lập Phân hệ Lưu trữ (Stateful Provisioning)

  

Chỉ khởi động các dịch vụ lõi lưu trữ để chuẩn bị nhận dữ liệu:

  

```bash

cd docker

docker compose up -d ml-postgres ml-minio

```

  

:::note Lưu ý

Chờ trạng thái health-check của các container này đạt `healthy` trước khi qua Giai đoạn 3.

:::

  

### Giai đoạn 3: Thực thi Phục hồi & Đồng nhất (Data Ingestion)

  

Chạy kịch bản khôi phục tự động:

  

```bash

bash backup/restore.sh

```

  

Các bước thực hiện:

- Hệ thống tải bản Snapshot mới nhất từ Cloud

- Giải nén bản backup

- Sử dụng công cụ `pg_restore` để nạp dữ liệu nhị phân vào PostgreSQL

- Đồng bộ MinIO

- Áp dụng tệp `.env` từ bản Snapshot

  

### Giai đoạn 4: Khởi động Ứng dụng & Hủy cách ly (Application Boot & Cutover)

  

Khởi động phần còn lại của hệ thống dựa trên dữ liệu đã được đảm bảo tính toàn vẹn:

  

```bash

docker compose up -d ml-php ml-reverb ml-queue ml-nextjs ml-nextjs-docs

```

  

Các bước kiểm tra:

- Kiểm tra logs để xác nhận không có lỗi

- Tắt chế độ bảo trì trên Nginx

- Khôi phục luồng truy cập bình thường cho người dùng

- Toàn bộ thư mục tạm khôi phục (`restore_tmp/`) sẽ được tự động xóa sạch

  

## 5. Tiêu chuẩn Vận hành Nâng cao (Best Practices)

  

### Bảo mật Thông tin Nhạy cảm (Credential Security)

  

Tệp `.env` chứa các Secret Keys. Khi cấu hình hệ thống Cloud qua Rclone:

  

- Phải sử dụng xác thực **OAuth2** hoặc **Token phân quyền hạn chế** *(Least Privilege)*

- Khuyến nghị bật tính năng **Mã hóa tại chỗ** *(Encryption at Rest)* trên phía Cloud Provider

  

### Cô lập Dữ liệu Tạm (Zero-Local Footprint)

  

Hệ thống được thiết kế để không để lại dấu vết dữ liệu tại máy chủ cục bộ:

  

- Mọi tệp tin trung gian và bản sao lưu vừa tạo/tải về phải được kịch bản bash tự động xóa sạch *(purge)*

- Sử dụng cờ `trap` nhắm vào toàn bộ thư mục làm việc (`backups/` hoặc `restore_tmp/`) ở cuối kịch bản

- Tự động xóa ngay cả khi quy trình thất bại

  

### Cơ chế Cảnh báo (Alerting)

  

Tích hợp Webhook *(Slack/Telegram)* vào kịch bản Bash:

  

- Báo cáo trạng thái `SUCCESS` hoặc `FAILED` sau mỗi chu kỳ Cronjob

- Đảm bảo đội ngũ kỹ thuật có khả năng phản ứng ngay lập tức *(Proactive Monitoring)*

  

:::info Ghi chú

Cơ chế cảnh báo có thể được triển khai sau trong giai đoạn tối ưu hóa hệ thống.

:::