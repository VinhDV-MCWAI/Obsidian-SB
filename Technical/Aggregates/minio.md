# MinIO - Object Storage Setup Guide

  

## Khởi tạo & Cấu hình

  

MinIO là object storage tương thích S3, được sử dụng để quản lý toàn bộ media (hình ảnh, video, tài liệu) trong hệ thống. Việc khởi tạo và cấu hình được thực hiện ngay khi build môi trường.

  

Khi sử dụng MinIO để quản lý dữ liệu media, bước đầu tiên là khởi tạo, thực hiện khi build env:

  

### Cách Scan và Clear

  

MinIO thực hiện quét định kỳ (mặc định 24 giờ) trước khi xóa dữ liệu. Do đó:

- Các file đã hết hạn nhưng chưa đến chu trình quét sẽ vẫn tồn tại

- Với phiên bản MaxIO mới, quét diễn ra liên tục nhưng tùy theo mức độ ưu tiên, số lượng object và tải hệ thống

- Thời gian xóa thực tế tùy thuộc vào hiệu suất hệ thống

  

### Tạo các Bucket

  

Tạo 2 bucket để lưu trữ dữ liệu:

  

#### media-official

Chứa dữ liệu riêng tư như thông tin cá nhân, tài liệu nhạy cảm, v.v.

  

**Cấu hình:**

- Đánh dấu versioning để backup

- Setting rule: cho phép tồn tại file trong 30 ngày để rollback, sau 30 ngày → hard delete

- Setting rule để dọn delete marker dư thừa

- Setting rule để dọn các multipart upload thừa chưa hoàn tất (thời gian 24H)

- Setting rule Expire Non-current Versions: chỉ giữ lại 3-5 phiên bản gần nhất, xóa vĩnh viễn các phiên bản cũ

- Nếu muốn khôi phục, có thể show version trên UI MinIO hoặc dùng lệnh

  

#### media-temp

Chứa dữ liệu tạm thời với clear tự động theo ngày.

  

**Cấu hình:**

- Bucket lưu trữ tạm thời với lifecycle độc lập, xóa dữ liệu tự động mỗi ngày

- Thường vài tiếng sẽ quét object có modified_time > 1 ngày → xóa object

- Không versioning để tiết kiệm chi phí lưu trữ (không tạo delete marker)

- Tự động clear dữ liệu mà không tồn rác

- Setting rule để dọn các multipart upload thừa chưa hoàn tất (thời gian 24H)

  

### Format Lưu trữ Media

  

Lưu trữ media sẽ theo format: `{workspace}/{year}/{month}/{uuid}.{extension}`

  

**Ví dụ:**

- `media-official/2026/01/16/abc.jpg`

- Upload media temp sẽ theo format: `bucket/{uuid}.{extension}`

  

### Các Lưu ý Quan trọng

  

**Cấu trúc thư mục và Prefix:**

- MinIO sử dụng dấu `/` để mô phỏng cấu trúc thư mục

- Nếu dồn quá nhiều đối tượng vào 1 prefix duy nhất sẽ gây áp lực truy vấn list và head

- Khuyến nghị: giữ < 10.000 đối tượng/prefix

- Có thể chia thành nhiều prefix theo năm/tháng/ngày hoặc theo hash của object ID

  

**Tiering và Lifecycle:**

- Setting lifecycle tự động move media xuống tier lưu trữ thấp hơn

- Dành cho media ít được sử dụng, lâu không sử dụng, hoặc tần xuất truy cập ít

- Di chuyển từ SSD → HDD hoặc cloud rẻ để tối ưu chi phí lưu trữ

- Toàn bộ giao tiếp với dữ liệu đều thông qua giao thức HTTP(S) RESTful

  

### Multipart Upload

  

Khi upload file nặng:

- File được chia thành nhiều part nhỏ để upload

- Khác với upload file nhẹ upload one-shot 1 lần duy nhất

- **Quy trình:**

  1. Khởi tạo object (khu vực lưu trữ)

  2. Upload các part vào khu vực đó

  3. Request confirm kết thúc (complete upload)

  4. Storage sẽ tự check, merge các part thành object và đánh dấu hoàn thành/thất bại

  

**Mặc định:**

- Mọi multipart upload bị hủy (không hoàn tất) sẽ tự động bị xóa sau 24H

- Tần xuất quét xóa mặc định là 6H

- Nếu không thay đổi thiết lập thì việc này tự động diễn ra

  

### Backup và Soft Delete

  

Cơ chế backup object xóa mềm:

- Cho phép khôi phục dữ liệu khi cần trong khoảng thời gian ngắn

- Không cần thiết phải lưu trữ dài hạn (ảnh hưởng dung lượng và hiệu xuất)

- **Setting rule:** Tự động xóa vĩnh viễn object sau X ngày nếu không khôi phục

- Trước đó X ngày cho phép rollback

- Đảm bảo quản lý, lưu trữ dữ liệu tối ưu

  

### Versioning

  

Mục đích: bảo vệ dữ liệu và đảm bảo tính toàn vẹn của dữ liệu

  

#### Tác dụng

  

1. **Chống ghi đè/xóa nhầm:**

   - Nếu không có version, dữ liệu cũ sẽ bị xóa vĩnh viễn

   - Với versioning, dữ liệu cũ vẫn được giữ lại

  

2. **Chống ransomware:**

   - Nếu hệ thống bị mã hóa, chỉ dữ liệu phiên bản mới bị ảnh hưởng

   - Các phiên bản cũ vẫn an toàn, có thể khôi phục được

  

#### Cơ chế đánh dấu Version

  

- Mỗi khi object thay đổi, tạo version mới và đánh dấu làm phiên bản hiện tại

- Khi xóa, chèn delete marker lên trên cùng (soft delete)

- Object không được xóa hoàn toàn cho đến khi chỉ định rõ phiên bản

  

#### Cơ Chế Backup

  

Có 3 loại:

  

1. **Đồng bộ hóa các thay đổi:**

   - Sao chép dữ liệu các phiên bản mới nhất của object

   - Mỗi object chỉ lấy một phiên bản mới nhất

  

2. **Khôi phục theo thời gian:**

   - Sao chép tất cả dữ liệu bao gồm tất cả các phiên bản của từng object

   - Vì mỗi version có timestamp tạo, hệ thống cho phép khôi phục dữ liệu tại thời điểm trong quá khứ

   - Lấy dữ liệu có timestamp tạo ≤ thời điểm yêu cầu khôi phục

  

3. **Khôi phục theo từng object:**

   - Khôi phục dữ liệu cho từng object riêng lẻ

   - Xử lý các sự cố nhỏ chỉ ảnh hưởng object đó, không ảnh hưởng object khác

  

#### Lưu ý về Dọn dẹp Version

  

Versioning rất tuyệt vời nhưng gây ra vấn đề khi object thay đổi nhiều lần:

- Sao chép object ra số lượng tương ứng

- Gây áp lực dung lượng lưu trữ và hiệu xuất sử dụng

  

**Setting rule dọn dẹp:**

- Tự động xóa các version cũ (không phải version hiện tại) sau 30 ngày

- Chỉ dữ lại 5 version gần nhất (setting phạm vi bucket, áp dụng cho tất cả object)

- Tự động xóa các delete marker thừa trong khoảng thời gian nhất định

  

#### Delete Marker và Xóa Đối tượng

  

Khi bucket bật versioning:

- Mỗi khi xóa object chỉ là soft delete

- Tạo delete marker để đánh dấu object đó

- Client không nhìn thấy object đã xóa, nhưng vẫn lưu trữ ở disk

  

**Chức năng:**

- Cho phép khôi phục dữ liệu khi nhầm lẫn xóa

- Xóa object bằng cách xóa delete marker (current version)

  

**Vấn đề:**

- Object và delete marker tồn tại độc lập

- Khi xóa object thật vĩnh viễn, delete marker vẫn tồn tại (trở thành rác)

- Delete marker không đánh dấu cho object nào

  

**Giải pháp:**

- Setting rule để xóa vĩnh viễn delete marker

- Vì bật versioning để khôi phục, nên setting rule như thùng rác

- Tự động xóa vĩnh viễn object sau X ngày nếu không khôi phục (30 ngày là hợp lý)

- Đảm bảo quản lý, lưu trữ dữ liệu tối ưu

  

### IAM/Policy Configuration

  

Setting IAM/policy để có quyền thay đổi dữ liệu: upload, read-only, temp-only

  

**Nguyên tắc:**

- Không dùng root access key cho app

- Giảm rủi ro nhầm lẫn, tăng bảo mật

  

#### Các bước cấu hình

  

1. **Tạo file policy.json:**

   ```json

   {

     "Version": "2012-10-17",

     "Statement": [

       {

         "Effect": "Allow",

         "Action": [

           "s3:GetObject",

           "s3:PutObject",

           "s3:DeleteObject"

         ],

         "Resource": [

           "arn:aws:s3:::media-temp/*",

           "arn:aws:s3:::media-official/*"

         ]

       }

     ]

   }

   ```

  

2. **Apply policy:** `mc policy add`

  

3. **Tạo user và apply policy:** `mc admin user add`

   - Tạo access key và secret key

  

4. **Cấu hình .env:**

   ```bash

   AWS_ACCESS_KEY_ID=backend-user

   AWS_SECRET_ACCESS_KEY=strong-backend-password

   AWS_DEFAULT_REGION=us-east-1

   AWS_BUCKET=media

   AWS_ENDPOINT=http://minio:9000

   AWS_USE_PATH_STYLE_ENDPOINT=true

   ```

  

5. **Cấu hình trong file `config/filesystem.php`:**

   ```php

   'disks' => [

       's3' => [

           'driver' => 's3',

           'key' => env('AWS_ACCESS_KEY_ID'),

           'secret' => env('AWS_SECRET_ACCESS_KEY'),

           'region' => env('AWS_DEFAULT_REGION'),

           'bucket' => env('AWS_BUCKET'),

           'url' => env('AWS_URL'),

           'endpoint' => env('AWS_ENDPOINT'),

           'use_path_style_endpoint' => env('AWS_USE_PATH_STYLE_ENDPOINT', false),

       ],

   ],

   ```

  

6. **Kiểm tra đường dẫn:**

   - Kiểm tra file `.env` có tồn tại: `/home/vinhdv/projects/my_life_management/laravel-api/.env`

   - Tìm và thay thế giá trị access key, secret key

   - Nếu không có, kiểm tra `.env.example`

   - Kiểm tra lại luồng xử lý back-end, refactor để sử dụng key này thao tác dữ liệu qua MinIO

  

### Tiering

  

Tiering là tính năng tự động di chuyển dữ liệu giữa các tier lưu trữ khác nhau:

- **Ví dụ:** SSD → HDD hoặc cloud rẻ

- **Mục đích:** Tối ưu chi phí lưu trữ, truy vấn

  

**Hiện tại:** Bỏ qua, mount 1 disk, chạy MinIO docker single-node

  

**Tương lai - Chia các tier khác:**

- **Pool SSD (hot):** Dữ liệu hot

- **Pool HDD (warm):** Dữ liệu sau 90 ngày không sờ vào

- **Remote S3 (cold):** Dữ liệu sau 1 năm không sờ vào

  

### Future Roadmap

  

- Backup & Disaster Recovery

- Monitoring & Alert

  

### Hard Limit

  

- Cần hard limit cho các bucket để tránh spam upload làm đầy dung lượng

- Đã được thiết lập khi build container storage

- **FE:** Thông báo lỗi trực quan cho user

- **Monitor:** Thiết lập gửi thông báo và cảnh báo dung lượng lưu trữ (tương lai)

  

## Phân loại và Cách thức Xử lý từng Loại Dữ liệu

  

### Thao tác với Dữ liệu trong Store (MinIO)

  

**Sử dụng key/secret:**

- Thường dùng ở back-end

- Xử lý các thao tác: delete, rename, move, list, copy

- Yêu cầu bảo mật cao, xử lý không quá nặng

  

**Sử dụng presigned URL:**

- Thường dùng ở FE

- Xử lý các thao tác: download, upload

- Yêu cầu bảo mật thấp, xử lý nặng ở front-end

- Sử dụng tài nguyên FE, giảm tải back-end

- Tối ưu hiệu năng

  

### Presigned URL

  

Là URL tạm thời, có thời gian tồn tại, sau đó tự động hết hạn.

  

**Ứng dụng:**

- Cho thành phần truy cập/thao tác mà không cần đăng nhập hay secret key

- FE thao tác trực tiếp với store

- Chỉ dùng cho 1 action duy nhất

- 1 object hoặc 1 part number, 1 method

- Không thể: list, đọc object khác, upload part/object khác

  

**Phù hợp với:**

- One-shot: upload, download, delete, rename, move với file nhỏ

- Multipart upload, download

  

**TTL (Time To Live):**

- Không quá nguy hiểm, có thể set TTL rộng hơn

- Upload part nặng chờ lâu: ~10 phút

- Upload file nhỏ: ~5-10 giây

  

#### File Upload Nhỏ

  

- Không cần reconnect hoặc retry

  

#### One-shot Operations

  

- Rename, move, delete (không cần cân nhắc dung lượng file)

- TTL ngắn (~5-10 giây)

  

#### Operations Cần Tính Toán

  

- Upload, streaming, multipart upload, download

- Cần tính toán: dung lượng file, tốc độ mạng, thời gian xử lý

- Để tính toán thời gian tồn tại presigned URL

  

#### Streaming

  

```

Client Request → BE xử lý, hỗ trợ range header → Store MinIO lấy → Response Client

```

  

**Yêu cầu:** Bắt buộc hỗ trợ HTTP range

  

### Vấn đề Upload

  

#### Vì sao PHP/Laravel Không Phù hợp

  

- Không phù hợp cho xử lý file, stream

- Dễ bottleneck, dễ lỗi 429, chậm

  

#### Nguyên tắc

  

**Laravel/PHP (API):**

- Chỉ đóng vai trò xác thực

- Một số chức năng one-shot

- Xử lý lưu trữ, quản lý dữ liệu metadata

  

**Công việc khác (dùng cơ chế khác):**

- Xử lý file, stream, multipart upload

- Virus scan, backup, cleanup

- Sử dụng: batch, queue, worker, cron

  

#### Lifecycle AbortIncompleteMultipartUpload

  

- Dùng cho bucket temp ở MinIO

- Thực hiện clean up các part không được commit complete tự động

- Thời gian xóa: 24H

- Không cấu thành object hoàn chỉnh

  

#### Direct Upload

  

- Upload file trực tiếp từ client → MinIO qua presigned URL

- Sử dụng tài nguyên của client để xử lý file

- **RAM:** Giữ 1 vài part rất nhỏ cùng thời điểm

- **CPU:** Tiêu thụ ít, xử lý nhanh

- **Disk:** Gần như không dùng

  

**Không cần kiểm tra tài nguyên client:**

- RAM thấp → upload chậm

- CPU không đáng kể

- Mạng yếu → timeout, thực hiện retry

  

#### Crash/Interrupt Handling

  

- Nếu browser crash → multipart bị bỏ dở

- Lifecycle sẽ clean các part thừa không được commit complete

  

#### Part Storage

  

- Các part upload lên MinIO được lưu trữ tạm thời

- Sau khi commit complete sẽ merge thành object hoàn chỉnh

  

### Nguyên lý Upload File Nặng và Multipart

  

**Quy trình:**

1. File chia nhỏ thành nhiều part

2. Mỗi part upload lên MinIO độc lập

3. MinIO không biết khi nào upload hoàn thành

4. Mục đích: cho phép retry/replace part, tránh object thiếu part

  

**Nếu không complete:**

- Object không tồn tại

- Part được lưu tạm thời

- Sẽ bị cleanup sau 1 thời gian (lifecycle)

  

**CompleteMultipartUpload:**

- Multipart upload xong → gửi request CompleteMultipartUpload

- Payload: uploadId, part (partNumber + etag tương ứng)

- MinIO: check hợp lệ payload, ghép part theo thứ tự, tạo object hoàn chỉnh, xóa dữ liệu tạm

  

**Vai trò của client:** Người xác nhận, commit confirm hoàn thành upload

  

### Hình thức Upload

  

Có 2 hình thức upload qua presigned URL:

1. **Multipart upload:** Upload part (phần nhỏ của file được chia nhỏ)

2. **Single upload:** Upload 1 file nhỏ

  

### Bản Chất Upload File

  

- Đính kèm luồng byte (binary stream) bên trong request

- Gửi qua HTTP

  

**File nhỏ:**

- Số byte ít, thời gian xử lý ngắn

- Không bị timeout

- Tốc độ truyền tải mạnh khiến xử lý rất nhanh

- Fail thì retry dễ chịu hơn

  

**File lớn:**

- Request rất nặng

- Browser gửi stream, OS + TCP stack từng phần nhỏ

- Request xử lý quá lâu → bị timeout

- Thời gian lâu → user có thể reload, off, sleep

- Fail dễ, retry chịu khó

- Upload 1 lần không tận dụng hết băng thông

- Khó control kết quả, UX tệ

  

## Công thức Ước lượng và Tính toán

  

### Tính toán Tốc độ Upload

  

Công thức hiển thị cho user:

```

percent = (loaded / total) * 100

```

  

Trong đó:

- **loaded:** Tổng số byte các part đã xong + số byte đang tải của các part hiện tại

- **total:** Tổng dung lượng file ban đầu (đơn vị: byte)

  

### Công thức Tính Time Retry

  

```

t_delay = t_base * 2^n + random(0, 100)

```

  

Trong đó:

- **t_base:** Thời gian chờ cơ bản (ví dụ: 1000ms)

- **n:** Số lần thử lại thất bại (0, 1, 2, 3)

- **random(0, 100):** Độ ngẫu nhiên, tránh nhiều máy/request thử lại cùng lúc

- **Giới hạn:** Nếu n ≥ 3, dừng quá trình và báo lỗi

  

### Công thức Tính Số lượng Part Song song

  

| HTTP | Số Part Song song |

|------|------------------|

| HTTP/1.1 | ≤ 6 |

| HTTP/2 | 8 – 16 |

| HTTP/3 | 16 – 32 |

  

### Công thức Tính toán Part Size

  

| File Size | Part Size |

|-----------|-----------|

| < 100 MB | Không multipart, dùng single upload |

| 100 MB - 500 MB | max(16 MB, ceil(FileSize / 10_000)) |

| 500 MB - 10 GB | max(32 MB, ceil(FileSize / 10_000)) |

| 10 – 100 GB | max(64 MB, ceil(FileSize / 10_000)) |

| > 100 GB | max(128 MB, ceil(FileSize / 10_000)) |

  

**Ghi chú:**

- 128 MB là chuẩn hiệu xuất

- Nếu kết quả > 128 MB, lấy giá trị đó để không vượt giới hạn 10.000 part

  

### Công thức Tính TTL cho Presigned URL

  

| File Size | TTL Presign |

|-----------|------------|

| ≤ 100 MB | 300 giây |

| 100 MB – 10 GB | 60 giây |

| 10 GB – 100 GB | 60 – 120 giây |

| > 100 GB | ≤ 120 giây |

  

### Lưu ý

  

- **16/32/64/128 MB:** Kích thước part tối thiểu cho từng loại file size, đảm bảo tốc độ và ổn định

- **FileSize:** Dung lượng file (đơn vị: MB)

- **10.000:** Số lượng tối đa part 1 object (do MinIO và S3 quy định)

- **Multipart upload:** Cho file > 100 MB

  - Mỗi part > 5 MB (trừ part cuối)

  - Tối đa 5GB/part

  - Part size: 16/32/64 MB

  - Tối đa 10.000 part/file

  - File size tối đa: 5 TB

  

### Ví dụ Tính toán

  

**Ví dụ 1: 500 MB**

```

Công thức: max(16MB, ceil(500 / 10_000))

Kết quả: ~0.05 MB < 16 MB (tiêu chuẩn)

Lấy: 16 MB

Số part: 31,25 part

```

  

**Ví dụ 2: 100 GB (102.400 MB)**

```

Công thức: max(64MB, ceil(102.400 / 10.000))

Kết quả: ~10 MB < 64 MB (tiêu chuẩn)

Lấy: 64 MB

Số part: 1.600 part

```

  

### Quy trình Upload - Flow Client

  

**Bước 1:** Client có thể sử dụng 1 request test để đánh giá tốc độ xử lý, sau đó chọn giải pháp phù hợp

  

**Bước 2:** Tính toán thời gian upload dự kiến để set TTL cho presigned URL

- Ví dụ: 100 MB/s, file 10 GB → 100 giây → set TTL 120 giây

  
  

### Flow Upload File Nhẹ

  

#### MinIO

- Tạo secret key để cho phép hệ thống kết nối với bảo mật

- Tạo presignUrl tạm thời sau này

  

#### Client

- **Validate:** Tên, loại file, dung lượng file (min/max)

- Kiểm tra nếu file size < 100 MB thì tiếp tục

- Call API để setup presign URL upload lên S3 tạm thời (cho phép bất kỳ put object)

- Payload request chứa thông tin file để chỉ định path lưu trữ (UUID làm tên object, lưu vào bucket temp)

  

#### Back-end

- Nhận request validate

- Tạo presign URL upload lên bucket temp (set TTL = 5 phút)

- Return response chứa presign URL cho client

  

#### Client (tiếp)

- Nhận response: nếu thất bại → thông báo lỗi cho user

- Nếu thành công → call presign URL để upload file lên MinIO (payload chứa file dạng blob)

  

#### MinIO (tiếp)

- Nhận request upload file (payload chứa file dạng blob)

- Upload file lên MinIO

- Return response cho client

  

#### Client (tiếp)

- Nhận response: nếu thất bại → thông báo lỗi cho user

- Nếu thành công → call API để xử lý dữ liệu

  

#### Back-end (tiếp)

- Validate thông tin

- Store thông tin

- Move file mới upload từ bucket temp vào bucket chính

- Tìm file trong bucket temp theo thông tin từ payload

- Cập nhật thông tin file vào database

- Return kết quả cho client

  

#### Client (tiếp)

- Nếu thất bại → thông báo lỗi cho user

- Nếu thành công → hiển thị thông báo kết quả upload

- Hiển thị progress, pause, resume, cancel, retry

  

### Flow Upload File Nặng

  

#### MinIO

- Tạo secret key

- Tạo presignUrl tạm thời

- Setup lifecycle AbortIncompleteMultipartUpload ở bucket temp

- Thực hiện clean part đã CreateMultipartUpload khi lỗi hoặc chưa commit complete (sau 24H)

  

#### Client

- **Validate:** Tên, loại file, dung lượng file (min/max)

- Kiểm tra nếu file size > 100 MB thì tiếp tục

- Call API với payload: file name, file size, file type (không gửi payload part)

  

#### Back-end

- Verify request, check quyền bằng middleware

- Validate các thông tin request

- Lấy thông tin file từ payload

- Tính toán:

  - Số lượng part song song (theo công thức, mặc định 6)

  - Số lượng part (theo công thức part size)

  - Presigned URL cho mỗi part (theo công thức TTL)

- Generate UUID làm tên object (chỉ định tên lưu trữ)

- Call CreateMultipartUpload MinIO với object ID = UUID đã generate

  - Tạo uploadID

  - Tạo khoảng trống upload trong bucket temp (chứa các part file)

  - Dùng cho upload part, resume, complete, abort

- Return response chứa:

  - UploadID, objectID, partSize, partNumber

  - TTL, presignedUrl của từng part

- **Lưu ý:** Browser giới hạn số request đồng thời

  - Nếu part > giới hạn → chia thành nhiều đợt

  - Nếu part phía sau có TTL ngắn hơn thời gian upload part trước → TTL hết hạn, không upload được

  - Giải pháp: Các part chia thành đợt, mỗi đợt = giới hạn request đồng thời

  - Mỗi luồng xong trước → lấy presigned mới → upload part mới

- **Điều quan trọng:**

  - Mỗi part = 1 request độc lập

  - Không thể dùng 1 presigned URL cho nhiều part

  - Không thể tạo nhiều presigned URL cho 1 part

  - Tránh 1 URL bị leak gây ghi vô hạn dữ liệu

  - Browser giới hạn 6-15 connection/domain

  - Để ~6-8 connection song song (tránh throttle, tăng latency)

  - Tránh mạng kém, presignUrl TTL ngắn hết hạn trước khi xử lý hết

- Response thông tin upload part cho client

  

#### Client (tiếp)

- Nhận response: nếu lỗi → thông báo cho user, nếu thành công → tiếp tục

- Cắt file thành các part (byte-range)

  - Mỗi part kích thước bằng nhau (trừ part cuối)

  - Logic cắt part lấy thông tin từ response (dùng Blob.slice ở web)

- Tạo metadata các part:

  - Part number, part size, etag

  - Presigned URL, số lần upload fail

- Tạo worker xử lý upload part song song

  - Giới hạn = giới hạn request đồng thời (6 part song song)

- **Với mỗi part:**

  - Kiểm tra có presigned URL không

  - Nếu có → upload

  - Nếu không → call API lấy presigned URL

    - Lỗi → thông báo cho user

    - Thành công → xử lý tiếp

  - Gửi request presigned URL upload part (URL: `http...&partNumber=x&uploadId=abc123`)

  - Đính kèm part file vào body request

  - Đính kèm content-type vào header (nếu không → MinIO reject)

  - **Nhận response:**

    - Thành công → đánh dấu kết quả, cập nhật etag từ header response

    - Lỗi mạng/timeout/5xx:

      - Nếu fail ≤ 2 lần → cập nhật số fail, thử lại (theo công thức delay)

      - Nếu fail ≥ 3 lần → dừng, thông báo lỗi cho user

      - Nếu 403 → thông báo lỗi, kết thúc

  - Sử dụng sliding window: 1 slot xong → đánh dấu done → lấy part tiếp theo từ queue → đẩy vào slot trống

- Khi upload tất cả part xong:

  - Tạo payload: object ID, upload ID, part number, part size, etag

  - Call API với payload này

- Hiển thị progress bar (dùng công thức tính tốc độ upload)

- Hiển thị progress, pause, resume, cancel, retry

  

#### Back-end (tiếp)

- Validate payload gửi lên

- Call CompleteMultipartUpload MinIO (không cần presigned URL)

  - Payload = payload từ client

  - Commit tất cả part thành 1 file hoàn chỉnh

- Call service scan virus file vừa upload

  - Nếu bị nhiễm → thông báo lỗi, xóa file (tương lai xử lý)

- Return kết quả cho client

  

#### Client (tiếp)

- Nhận response từ back-end

- Nếu lỗi → thông báo lỗi

- Nếu thành công → thông báo đã sẵn sàng submit upload

- Khi user submit → call API gửi thông tin metadata file

  

#### Back-end (final)

- Xử lý dữ liệu, store thông tin

- Lưu trạng thái file = processing

- Kích hoạt job move file từ bucket temp vào bucket chính (official)

- Return kết quả: room ID để user theo dõi

  - Format: `uuid_userId_mediaId` (user có thể upload nhiều file khác)

  

#### Worker Execute Job

- Kiểm tra object trong bucket temp có tồn tại không

- Nếu có → move object từ bucket temp → official

  - Storage tự chia thành nhiều part nếu file lớn (nội bộ, không can thiệp)

  - Hoàn tất → return kết quả

- **Retry:** 5 lần, fail = kết thúc

- Nếu thành công → delete object cũ (tránh rác, lỗi)

- Cập nhật trạng thái file = complete hoặc fail

- Reverb broadcast event kết quả đến room ID tương ứng

  

#### Client (final)

- Nhận response thông báo quá trình đỂ diễn ra

- Lấy room ID từ response

- Call WSS đăng ký join room để nhận thông báo kết quả file

- Call API list với media ID vừa upload, check status

  - Nếu khác processing → thông báo kết quả, call WSS rời room

  - (Vì file nhỏ, job xử lý nhanh tức thì, WSS kết nối chậm → miss thông báo)

- Nếu không → đợi job broadcast event

  - Job broadcast → Reverb receive → send to client (room subscriber)

  - → Hiển thị thông báo kết quả → call WSS rời room

  

#### Laravel Job (định kỳ)

- Clear record có upload status = inprogress

- Update time > 24H so với hiện tại

- Tránh rác dữ liệu (storage có lifecycle tự động clear)

  

### Lưu ý Upload Dở Dang

  

- User reload, close tab/browser, sleep, turn off

- Multipart không hoàn thành

- Part không được merge thành file hoàn chỉnh

- Lifecycle MinIO sẽ dọn dẹp các part dư thừa

  

### Kiểm tra Web Server

  

Kiểm tra web server (nginx, apache, caddy) dùng HTTP version bao nhiêu:

- Chọn giải pháp xử lý request đồng thời (theo công thức)

- Cân nhắc khả năng xử lý: client, browser, thiết bị, đường truyền

  
  

## Ý Nghĩa & Nguyên Lý Hoạt động Công Nghệ

  

### Băng thông

  

Năng lực xử lý tối đa của đường truyền (không dành riêng cho 1 request, tổng tài nguyên traffic)

  

**Ví dụ:**

- 100 Mbps (100 mega bit per second) ≈ 12.5 MB

- Công thức: Thời gian = Dung lượng request / Tốc độ

- 10 Mb dung lượng, 10 Mbps tốc độ → 1 giây

  

**Chia sẻ băng thông:**

- 2 request song song → cảm giác chia đều

- Request xong trước giải phóng, còn dư cho request đang xử lý

- Request cũ vẫn chiếm phần lớn, request mới từ từ (không chia ngay)

- Dần dần chúng cân bằng

  

### Tốc độ Mạng

  

Tốc độ thực tế của đường truyền

  

### Đơn vị Đo lường File Size

  

- 1 Byte (B) = 8 Bits

- 1 KB (Kilobyte) = 1.024 Bytes

- 1 MB (Megabyte) = 1.024 KB

- 1 GB (Gigabyte) = 1.024 MB

- 1 TB (Terabyte) = 1.024 GB

- 1 PB (Petabyte) = 1.024 TB

- **Chuyển đổi:** MB (megabyte) = Mb (megabit) × 8

  

### Tốc độ Mạng Thực tế

  

- 4G: 20-100 Mbps

- 5G: 187-393 Mbps

- 1 MB (megabyte) = 8 Mb (megabit)

  

### Khái niệm Cơ bản

  

- **Job:** Công việc được lập trình sẵn để thực hiện logic

- **Queue:** Hàng đợi thứ tự, ưu tiên, delay thực hiện job

- **Worker:** Thực hiện công việc, luôn chạy sẵn

  - Thực hiện các công việc trong queue

  - Thực hiện đồng thời hoặc retry

  - Báo cáo kết quả, phát event

- **Reverb:** Broadcast event kết quả theo room ID

- **Web Worker:** Script chạy background

  - Độc lập và song song với UI thread

  - Không ảnh hưởng hiệu suất giao diện

  - Hạn chế: không truy cập DOM

  - Đa luồng qua nhận/gửi tin nhắn

  

## Streaming (Phát trực tuyến)

  

### Nguyên Tắc Cơ bản

  

Khi stream không gửi yêu cầu nhận toàn bộ file:

- File lớn, chặn băng thông, chậm xử lý, tốn dung lượng

- Ảnh hưởng trực tiếp stream realtime

- **Giải pháp:** Video encode → chia thành segment nhỏ (10s/segment)

- Trình phát tải dần từng đoạn

  

### Lựa Chọn Chất Lượng Video

  

1. Độ phân giải nguyên bản của video

2. Bitrate, FPS, codec...

  

### Adaptive Bitrate

  

Tự động điều chỉnh chất lượng dựa trên:

- Tốc độ mạng

- CPU, GPU

- Điều kiện mạng

  

### CDN Caching Video

  

Cache video trên CDN để giảm tải server

  

### FFmpeg Transcoding

  

Framework multimedia mã nguồn mở, xử lý:

- Decode, encode, transcode

- Mux, demux, stream, filter

- Play audio/video

- Hầu hết định dạng dữ liệu

  

**Ví dụ:**

- mov → mp4, video → mp3

- Nén dung lượng, giảm kích thước

- Cắt/ghép video, thêm hiệu ứng, watermark

- Trích xuất âm thanh

- Live streaming, đổi đuôi

  

Sử dụng FFmpeg trực tiếp khó và rủi ro → dùng công cụ chuyên dụng

  

#### Vai trò trong Live Stream

  

1. **Nén dữ liệu:** Mã hóa video (H.264, H.265)

2. **Chuyển mã:** Video gốc → nhiều bản (chất lượng khác nhau)

3. **Chia đoạn (Segment):** Cắt thành đoạn nhỏ (10s/segment)

4. **Tạo file Manifest:**

   - HLS: `.m3u8` (danh sách `.ts` segment + playlist)

   - DASH: `.mpd` (danh sách `.m4s` segment + playlist)

   - "Bản đồ" để trình phát biết lấy đoạn video nào tiếp theo

  

### HLS (m3u8, ts) & DASH (mpd)

  

**Trước đây:**

- Client tải toàn bộ file video

- Trình phát phát

- Lãng phí băng thông, tốc độ, dung lượng

- Ảnh hưởng stream realtime

  

**HLS (Apple) & DASH (MPEG):**

- Giao thức phân phối video

- Điều khiển segment nhỏ của video

- Thay đổi chất lượng khác nhau sẵn

- Mạng yếu → trình phát giảm chất lượng tự động

- Kết quả: Người dùng load/xem nhanh, cảm giác liên tục

- Không cần tải toàn bộ 1 lần

  

### Player

  

Trình phát video, các chức năng điều khiển trực quan:

- Action serve qua HTTP

- Đọc file manifest, tải segment qua HLS/DASH

- Tự xử lý:

  - Adaptive bitrate switching

  - Buffering strategy

  - Segment fetching

  - Fallback network

  

### VOD (Video On Demand)

  

Công nghệ cho phép user xem nội dung video lưu trữ sẵn bất cứ lúc nào:

- Thay vì tuân theo lịch phát sóng cố định

- User điều khiển video theo ý muốn

  

### File Container

  

File có đuôi mp4, mp3, webm... là hộp chứa dữ liệu:

- Browser không quan tâm đuôi file

- Quan tâm codec bên trong được hỗ trợ hay không

- Vấn đề: mỗi browser hỗ trợ codec khác nhau

- OS/hardware decode khác nhau

- CPU/GPU/RAM thiết bị khác nhau

- Tốc độ mạng khác nhau

  

### Video.js

  

Thư viện JavaScript phát video:

- Hỗ trợ nhiều định dạng (HLS, DASH)

- Tính năng:

  - Adaptive bitrate switching

  - Buffering strategy

  - Segment fetching

  - Fallback network

- Player thuần, không transcoding

- Sử dụng ở front-end

  

### Nginx Configuration

  

- **client_max_body_size:** Giới hạn body size (path `/upload`)

- **proxy_buffering, proxy_request_buffering:** Tắt buffering body (path `/stream`)

  

### Các Hướng Triển Khai

  

#### 1. Tự Host

  

Phức tạp, mất nhiều thời gian cân nhắc, nghiên cứu và triển khai.

  

**Giai đoạn xử lý:**

- User upload file video (ví dụ mp4) lên bucket

  

**Giai đoạn Transcoding:**

- Tự viết worker (Node.js, Python, Go) lắng nghe event

- Khi có file video mới upload → sử dụng FFmpeg

- Chuyển đổi `.mp4` → định dạng HLS (`.m3u8` + `.ts`) / DASH (`.mpd` + `.m4s`)

- Đẩy file đã xử lý lại vào MinIO

- Sửa lại path để truy cập stream video

  

**Giai đoạn phát (stream):**

- Web server hoặc static website hosting MinIO serve `.m3u8` + `.ts`

- Client: dùng video.js, hls.js → phát

  

#### 2. Cloud Services/Platforms

  

Không có chi phí bảo trì, tiện và nhanh

  

#### 3. Media Server

  

Ant Media, Wowza, Red5 → dính bản quyền, khó custom sâu

- Cấu hình lấy file từ MinIO

- Tự động convert và stream

  

#### 4. Webserver + VOD

  

nginx-vod-module → Transcoding on-the-fly

- nginx tự động convert video thành định dạng stream

- Hỗ trợ HLS hoặc DASH

- Không cần lưu trữ file đã convert

- Tốc độ chậm hơn so với convert lưu trước

  

**Lựa chọn khuyến nghị:** Sử dụng **nginx-vod-module**

  

### Triển Khai Chi Tiết (Self-hosted)

  

Phức tạp, tốn thời gian, rủi do nhiều → không hiệu quả nếu không chuyên sâu

  

**User request stream:**

- Browser gửi request với range tương ứng

  

**Transcode:**

- Chạy job FFmpeg (lúc đó hoặc trước)

- Convert codec: Video → H.264, Audio → AAC

- Tạo nhiều bitrate: 240p, 360p, 720p, 1080p

- Chia segment: 2–6 giây/segment

  

**Output:**

- **HLS:** `index.m3u8`, `chunk_000.ts`

- **DASH:** `manifest.mpd`, `segment_001.m4s`

  

**Lưu vào MinIO:** Chỉ storage, không quan tâm định dạng

  

**Client chọn protocol:**

- Safari/iOS → HLS native

- Chrome/Firefox → hls.js

- Smart TV/Android → DASH/ExoPlayer

  

### Progressive MP4 Streaming

  

Dùng HTML5 video với URL MinIO:

- File ~7GB

- Cơ chế: Progressive MP4 Streaming via HTTP Range Requests

- Triển khai cực đơn giản

  

### Bảng So Sánh

  

| Tiêu chí | Progressive MP4 | nginx-vod |

|---------|-----------------|-----------|

| Protocol hỗ trợ | MP4 via Range | DASH, HLS, HDS, MSS |

| Adaptive Bitrate | Không | Có, multi-bitrate tự động |

| Độ trễ | Thấp (low latency) | Cao hơn (segmenting) |

| Lưu trữ | Hiệu quả (1 file) | Overhead segment, cache tốt |

| Seek chính xác | Cao, byte-level | Tốt, phụ thuộc segment |

| Tính năng nâng cao | Cơ bản | Track selection, DRM, AES |

| Phức tạp triển khai | Thấp | Cao |

| Hiệu suất | Cao (single stream) | Tối ưu (~26MB/s) |

| Use case lý tưởng | VOD đơn giản | Adaptive, multi-device |

  

### Lựa Chọn Triển Khai

  

**Khuyến nghị:** MinIO (storage) + nginx-vod-module (streaming server) + Video.js (client)

  

**Quy trình:**

1. nginx-vod đọc file từ MinIO (HTTP)

2. Convert on-the-fly → HLS/DASH

3. Video.js phát trên browser

  

**Luồng Xử Lý Nghiệp Vụ:**

  

**Step 1:** Client upload file lên MinIO via presigned URL

- Lưu dạng fragmented MP4 hoặc Fast start (nginx-vod đọc nhanh không tải toàn bộ RAM)

  

**Step 2:** BE lưu metadata video vào database

  

**Step 3:** Yêu cầu phát video

- Client click play

- FE không call trực tiếp MinIO

- Call URL đặc biệt tới nginx-vod: `http://nginx-vod/vod/video.mp4/playlist.m3u8`

  

**Step 4:** Xử lý tại nginx-vod

- **Ánh xạ:** Mapped mode, xác định vị trí file trong MinIO

- **Lấy dữ liệu:** Kết nối MinIO qua HTTP/S3, đọc byte cần thiết

- **Đóng gói:** Cắt `.mp4` → segment, tạo file danh sách phát trong RAM

- Return luồng video qua HLS/DASH cho client

  

**Step 5:** Client phát via video.js

  

**Actions:**

- Play: Range từ 0-

- Pause: Tự ngắt TCP

- Seek/Next/Back: Range tương ứng

- Resume: Range tiếp

- Zoom: CSS/player

- Chất lượng: Player (HLS/DASH)

- Âm lượng: Browser

- Thời gian: Browser

- Speed: Browser

  

**Step 6:** Lưu tiến độ xem

- Event timeUpdate video mỗi 5s

- Web worker lưu thời điểm hiện tại + video ID vào indexDB

- Tránh block UI

- Reload: kiểm tra indexDB, set thời gian từ lưu

- Chỉ chạy khi user xem, còn không → clear

  

### Cách Thức Triển Khai Chi Tiết

  

**Mapped mode:**

- Tạo file JSON mô tả vị trị MinIO

- nginx-vod đọc biết lấy dữ liệu ở đâu

- Link MinIO gốc không lộ

  

**Header & CORS:**

- FE và webserver có thể ở domain khác

- Cấu hình CORS để trình phát đọc segment

  

**Phân quyền:**

- BE tạo presigned URL/token

- nginx kiểm tra trước đóng gói

- Đảm bảo bảo mật

  

**Băng thông:**

- nginx-vod hỗ trợ adaptive bitrate

- Nhiều bản chất lượng → tự động chuyển đổi theo tốc độ mạng

  

**CPU:**

- Đóng gói tốn CPU nginx

- Nhiều user → cache segment đã cắt

  

**Truy cập MinIO:**

- Cấu hình nginx-vod thêm header xác thực (access/secret key)

- Không cần presigned URL

- Hoặc: Tạo presigned URL → quản lý refresh

  

**Lưu trữ dữ liệu xử lý:**

- Đọc lượng nhỏ MinIO vào buffer (RAM)

- Đóng gói HLS/DASH

- Đẩy qua HTTP cho user

- Không lưu lại

  

**Xử lý file lớn:**

- Yêu cầu byte cụ thể từ MinIO

- 100MB hay 100GB không khác nhiều

- File tối ưu (moov atom đầu) → RAM cực nhẹ, nhanh

- Chỉ đóng gói (không giải mã/nén) → CPU ít

  

**Xử lý nhiều request:**

- nginx-vod: non-blocking model

- Hàng ngàn kết nối đồng thời

- Mỗi active connection: RAM cho buffer/metadata

- Quá nhiều → OOM

- Giải pháp: nginx proxy cache + nginx-vod

  

### Move File Nặng Giữa Bucket

  

Khi cần move file từ bucket này qua bucket khác:

- Server-side Multipart Copy + Parallel Threads + batch job

- Thêm column đánh dấu trạng thái

- Cập nhật status = processing

- Thông báo user: "File của bạn đang được xử lý hệ thống. Chúng tôi sẽ thông báo khi file sẵn sàng."

- Tắt modal/dialog cho user tiếp tục

- Job chạy ngầm, khi hoàn tất → update status = upload completed

- Tận dụng sức mạnh đa luồng, phần cứng MinIO

- **Retry strategy:**

  - Fail → retry theo thời gian (exponential backoff)

  - Tối đa 5 lần retry

  - Quá ngưỡng → update fail

  

### Nhu Cầu và Công Nghệ Tương ứng

  

**Nhu cầu 1:** Truy cập dữ liệu từ nhiều máy → Internet

  

**Nhu cầu 2:** Cập nhật thông tin, sự phổ biến → Trình duyệt → HTTP gửi/nhận dữ liệu

  

**Nhu cầu 3:** Tương tác dữ liệu (validate form trước request) → JavaScript

  

**Nhu cầu 4:** Thao tác dữ liệu mượt mà (không reload page) → AJAX

  

**Nhu cầu 5:** Gửi/nhận dữ liệu 2 chiều kịp thời, thời gian thực → WebSocket

  

**Nhu cầu 6:** Thao tác dữ liệu thời gian thực, độ trễ thấp → WebSocket + HTTP/2, WebRTC, WebTransport

  

### Quá Trình Phát Triển Công Nghệ

  

Khi công nghệ mới chứng minh ưu việt:

- Trình duyệt, ngôn ngữ, hệ sinh thái cố gắng tích hợp/hỗ trợ

- Quá trình 2 hướng:

  1. Các lớp lõi tích hợp trong phiên bản cập nhật

  2. Hệ sinh thái (cộng đồng, doanh nghiệp) phát triển thư viện, framework, công cụ

  

### WebSocket - Độ phức tạp Mở Rộng

  

WebSocket phức tạp để scale vì:

- 2 hướng mở rộng:

  1. **Chiều dọc:** Mở rộng cấu hình xử lý (tốn kém)

  2. **Chiều ngang:** Độ phức tạp tăng

- Bản chất WebSocket: kết nối liên tục, chỉ biết bản thân + client

- Khi mở rộng cần:

  - Phương thức mở rộng mà không mất kết nối

  - Chia sẻ dữ liệu đồng thời nhiều client

  - Thành phần trung gian: quản lý, điều phối, định tuyến, cân bằng tải, lưu trữ, xử lý

  

## WebSocket - Chi Tiết

  

### Định Nghĩa

  

Application-layer protocol, chạy trên TCP (không thay thế TCP):

- Giao thức truyền tải dữ liệu

- Thiết lập kênh liên lạc 2 chiều

- Duy trì liên tục giữa browser và server qua 1 kết nối TCP duy nhất

- Khác HTTP: client hỏi → server trả lời

- WebSocket: cả 2 chủ động gửi bất kỳ lúc nào (sau kết nối)

  

### Các Thành Phần

  

**Client:**

- Socket ID duy nhất

- Lưu trữ room ID trong memory (xác định gửi/nhận/rời room)

- Có thể đăng ký nhiều WebSocket server khác

- Khi gửi: chọn server ID, nội dung cần room ID, message content

  

**Room:**

- Không gian chung cho nhiều client

- Client theo dõi message của nhau

- Thiết lập kết nối mới → tạo đường dẫn → kết nối liên tục

- Tin nhắn → gửi tới client trong room (trừ sender)

- Khác HTTP: không cần địa chỉ IP

- WebSocket server tự clear room khi không có client

  

**WebSocket Server:**

- Quản lý danh sách room và socket ID (RAM)

- Client join room → lưu socket ID vào room

- Client rời room → xóa socket ID từ room

  

### Hệ Thống Phân Luồng

  

- **Public room:** Tất cả user nhận thông báo

- **Private room:** Chỉ user trong room

- **Presence room:** Private + biết ai đang connect (online)

  

### Hệ Thống Xác Thực

  

**Reverb:**

- Cơ chế verify token (authorization header)

- Sẵn có, không cần thêm

  

**Back-end:**

- Xác thực user qua token (auth hoặc cookie)

- Xác thực thành công → kiểm tra quyền room

- Hợp lệ → generate token từ secret key (reverb_secret = 1)

- Trả về token, ngược lại → lỗi

  

**Client:**

- FE cài thư viện: laravel-echo hoặc pusher-js

- Thiết lập đường dẫn WSS → Reverb

- Reverb verify token (client có truy cập WSS không)

- Secret key khớp với BE xác thực

- BE xác thực → tạo token (secret giống Reverb)

- Client dùng token → xác thực WSS kết nối

  

### Cách Thức Hoạt động

  

1. **Handshake:** Client gửi HTTP GET đặc biệt → server (yêu cầu WebSocket)

2. **Open connection:** Server đồng ý → kết nối thiết lập (HTTP → WebSocket)

3. **Data transfer:** Cả 2 gửi dữ liệu theo frame (nhẹ, không header rườm rà)

4. **Close:** 1 trong 2 đóng bất kỳ lúc nào

  

### Flow WebSocket Phức tạp

  

**Step 1:** Client cần theo dõi thông báo upload file nặng

- Gửi request server WebSocket khởi tạo kết nối

  

**Step 2:** Nginx proxy request → WebSocket server

  

**Step 3:** Reverb xác nhận yêu cầu

- Hợp lệ → trả kết nối thành công

- (Như ở sảnh khách sạn, ai cũng vào nhưng chưa xác thực phòng)

  

**Step 4:** Client (via laravel-echo)

- HTTP POST → Laravel xác thực channel

- Param: channel cần kết nối

- Channel format: `admin_id + channel_name` (upload file nặng)

- Request tự đính cookie (access token) → login thành công

  

**Step 5:** Nginx proxy → Laravel

  

**Step 6:** Laravel xác thực

- adminMiddleware kiểm tra cookie hợp lệ

- broadcast middleware: lấy admin_id param → so với admin_id từ JWT cookie

- Khớp → tạo chữ ký mã hóa (secret key = Reverb secret)

- Response JSON: `{"auth": "a1b2c3d4e5..."}`

  

**Step 7:** Client (via laravel-echo)

- Dùng chữ ký để xác thực channel kết nối

- Đóng gói → WSS frame

  

**Step 8:** Reverb xác nhận

- Giải mã chữ ký

- Hợp lệ → kết nối thành công, ngược lại → lỗi

  

**Step 9:** Laravel job

- Khi job hoàn tất → lưu admin_id (từ cookie/token lúc request)

- Tạo channel format → mã hóa JSON → push Redis channel

  

**Step 10:** Reverb (Redis subscriber)

- Đăng ký Redis channel trước

- Tin nhắn mới → đọc JSON payload

- Tìm channel name đang tồn tại

- Gửi tin nhắn tới socket ID trong channel

  

**Step 11:** Client

- Nhận tin nhắn → hiển thị thông báo kết quả job

- Close connection hoặc xóa room

  

**Step 12:** Laravel job (định kỳ)

- Clear record: upload status = inprogress

- Update time > 24H (hiện tại)

- Tránh rác dữ liệu (storage có lifecycle auto clear)

  

### Lưu ý WebSocket

  

**Nếu client rời trong xử lý:**

- Quay lại → phải tải dữ liệu mới

- Không cần message sync (không chatapp)

- Chat app → cần đồng bộ message