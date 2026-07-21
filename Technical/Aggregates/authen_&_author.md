# Tài Liệu Chi Tiết Hệ Thống Authentication & Authorization

  

## MỤC LỤC

  

1. [Tổng Quan Kiến Trúc](#1-tổng-quan-kiến-trúc)

2. [Công Nghệ & Thành Phần](#2-công-nghệ--thành-phần)

3. [Luồng Authentication](#3-luồng-authentication)

4. [Luồng Authorization](#4-luồng-authorization)

5. [Cơ Chế Quản Lý Token](#5-cơ-chế-quản-lý-token)

6. [Multi-Tab & Multi-Device](#6-multi-tab--multi-device)

7. [Bảo Mật & Xử Lý Lỗi](#7-bảo-mật--xử-lý-lỗi)

8. [Đánh Giá & Khuyến Nghị](#8-đánh-giá--khuyến-nghị)

  

---

  

## 1. TỔNG QUAN KIẾN TRÚC

  

### 1.1. Mô Hình Tổng Thể

  

Hệ thống sử dụng kiến trúc **JWT-based Authentication + Cookie-based Token Storage** kết hợp với **Redis Cache** cho Authorization.

  

```

┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐

│   Next.js FE    │◄───────►│   Laravel API    │◄───────►│  Redis Cache    │

│  (TypeScript)   │  HTTPS  │   (PHP 8.x)      │         │  + PostgreSQL   │

└─────────────────┘         └──────────────────┘         └─────────────────┘

      │                              │

      │                              ├─ Authentication: JWT

      │                              ├─ Authorization: Permission Cache

      │                              └─ Token Storage: Cookie + DB + Redis

      │

      └─ Multi-tab sync: BroadcastChannel API

```

  

### 1.2. Đặc Điểm Chính

  

- **Stateless JWT** cho access token (5 phút TTL)

- **Stateful refresh token** lưu trong database (3 ngày TTL)

- **HttpOnly cookies** để lưu trữ token an toàn

- **Redis cache** cho permission để tối ưu hiệu năng

- **BroadcastChannel** để đồng bộ multi-tab ở frontend

- **Lock mechanism** để tránh race condition khi refresh token

  

---

  

## 2. CÔNG NGHỆ & THÀNH PHẦN

  

### 2.1. Backend (Laravel API)

  

#### Cấu Trúc Token

  

**JWT Payload Structure:**

```json

{

  "id": "123",           // User ID

  "type": "admin",       // User type

  "iat": 1712345678,     // Issued at timestamp

  "exp": 1712346278      // Expiration timestamp (iat + TTL)

}

```

  

**Token TTL:**

- Access Token: `300 giây` (5 phút)

- Refresh Token: `259200 giây` (3 ngày)

  

#### Storage Layer

  

**1. Redis Cache (Access Token + Permission)**

  

Cấu trúc key:

```

# Access token tracking

admin:{user_id}:{access_token}

  └─ hash: { last_access_at: timestamp }

  └─ TTL: 300s (tự động xóa)

  

# Permission cache (shared cho nhiều access token của cùng user)

admin:{user_id}:admin_permission

  └─ hash: {

       "GET": ["api/admin/users", "api/admin/files", ...],

       "POST": ["api/admin/users", ...],

       "PUT": [...],

       "DELETE": [...]

     }

  └─ TTL: TTL của access token mới nhất (tự động gia hạn)

```

  

**Ưu điểm cấu trúc này:**

- Lưu trữ token hợp lệ trong cache: Đảm bảo việc truy vấn quyền hạn theo thời gian thực, không ảnh hưởng request truy vấn trực tiếp DB và xử lý các logic khác, tốc độ đọc ghi nhanh, dữ liệu có sẵn từ trước hạn chế xử lý dữ liệu lại khiến nó hữu ích cho việc tiết kiệm tài nguyên và thời gian xử lý request. Kiểm soát chặt chẽ việc quản lý truy cập hợp lệ hay không. Ở quy mô ứng dụng có người dùng lớn, chỉ lưu trữ truy cập hợp lệ và loại bỏ các request còn lại (không hợp lệ) tiết kiệm tài nguyên hơn rất nhiều so với lưu trữ black-list, khi có số lượng người dùng lớn + thực hiện thao tác đăng nhập, refresh token liên tục.

- Nhiều access token của cùng user dùng chung 1 permission cache → nhằm chia sẽ tài nguyên permission, vì cùng user nên cùng permission, tránh duplicate, tiết kiệm bộ nhớ. Quản lý quyền tập trung, xử lý phân quyền đồng loạt dễ dàng. Cho phép một tài khoản có thể sử dụng trên nhiều thiết bị, mỗi thiết bị có cơ chế hệ thống như login, logout, refresh riêng rẽ mà không ảnh hưởng đến các hoạt động của các thiết bị khác có cùng tài khoản.

- Nhiều token cùng user, đảm bảo quản lý tập trung, nhanh chóng chặt chẽ.

- TTL của permission tự động sync với token mới nhất → không cần quản lý riêng. Đảm bảo token cuối cùng có permission sử dụng và cả 2 cùng hết hạn, cả 2 tự động xóa khi hết hạn để tiết kiệm bộ nhớ, vì sự tồn tại của 1 trong 2 chúng không có giá trị lại tốn thêm dung lượng lưu trữ.

- Access token tự xóa khi hết hạn → tự động dọn dẹp. Access token cuối cùng clear thì parent không có child cũng sẽ tự xóa -> tiết kiệm tài nguyên, loại bỏ tài nguyên không sử dụng.

- Mỗi route trong permission đều là route duy nhất, tránh trùng lặp.

- Phân tách route duy nhất không trùng lặp vào mỗi method tương ứng, khiến việc search quyền truy cập theo route chỉ tập trung trong tệp dữ liệu của 1 method thôi, thay vì search all route, vì vậy việc search trở lên nhanh chóng.

  

**2. PostgreSQL Database (Refresh Token)**

  

Bảng `token_mst`:

```sql

CREATE TABLE token_mst (

  id            SERIAL PRIMARY KEY,

  token_hash    VARCHAR NOT NULL,      -- MD5 hash của refresh token

  account_id    INTEGER NOT NULL,      -- User ID

  device_name   VARCHAR,               -- User agent string

  ip_address    VARCHAR,               -- IP address

  expired_at    TIMESTAMP,             -- Thời điểm hết hạn

  created_at    TIMESTAMP,

  updated_at    TIMESTAMP

);

```

  

**Lý do lưu trong DB:**

- Logic thông thường access token sẽ tự động refresh trước khi hết hạn, hầu hết các request đều không sử dụng refresh token, refresh token chỉ sử dụng cho việc refresh → không phù hợp với cache

- Refresh token có TTL dài (3 ngày) → không phù hợp với cache

- Cần tracking device/IP để audit log

- Cần revoke từng device cụ thể

- Hash token trước khi lưu → bảo mật khi DB bị xâm nhập, sẽ cần phải giải mã token mới sử dụng được.

  

**3. Permission View (admin_permission_view)**

  

View SQL tổng hợp permission từ nhiều bảng:

```sql

CREATE VIEW admin_permission_view AS

SELECT

  am.id         AS admin_mst_id,

  rm.id         AS role_mst_id,

  rm.name       AS role_name,

  CASE

    WHEN am2.type = 0 THEN 'GET'

    WHEN am2.type = 1 THEN 'POST'

    WHEN am2.type = 2 THEN 'PUT'

    WHEN am2.type = 3 THEN 'PATCH'

    WHEN am2.type = 4 THEN 'DELETE'

  END AS type,

  am2.name      AS api_name,

  am2.path      AS path,

  fm.name       AS feature_name,

  fm.group_name AS feature_group

FROM

  admin_mst am

  INNER JOIN admin_role_mst arm ON arm.admin_mst_id = am.id

  INNER JOIN role_mst rm ON rm.id = arm.role_mst_id

  INNER JOIN api_role_mst arm2 ON arm2.role_mst_id = rm.id

  INNER JOIN api_mst am2 ON am2.id = arm2.api_mst_id

  INNER JOIN feature_mst fm ON fm.id = am2.feature_mst_id

WHERE

  am.status = 1

  AND am.is_active = TRUE

  AND rm.is_active = TRUE

  AND am2.is_active = TRUE

  AND fm.status = 1;

```

  

**Mô hình RBAC:**

```

admin_mst (Users)

    ↓ (nhiều-nhiều)

role_mst (Roles)

    ↓ (nhiều-nhiều)

api_mst (API Endpoints)

    ↓

feature_mst (Features/Modules)

```

  

#### Quyền hạn được phân cấp theo mô hình:

  

Người dùng → Vai trò (Admin, Manager, Editor...) → Tính năng (Quản lý bài viết, Quản lý tài khoản...) → Hành động cụ thể (Xem, Thêm, Sửa, Xóa).

  

### 2.2. Frontend (Next.js)

  

#### Auth Lock Mechanism

  

Để tránh race condition khi nhiều request cùng lúc nhận status code 401 (unauthorized chưa đăng nhập hoặc hết hạn token) và cùng gọi refresh token, hệ thống sử dụng **localStorage-based lock** với random backoff:

  

```typescript

// Lock structure in localStorage

{

  id: "abc123xyz",           // Random lock ID

  expires: 1712345678900     // Lock expiration timestamp (5s TTL)

}

```

  

**Quy trình acquire lock:**

1. Kiểm tra lock hiện tại có còn hợp lệ không

2. Nếu không, tạo lock mới với random ID

3. Chờ random delay 10-50ms (giải quyết race condition)

4. Verify xem lock ID có phải của mình không

5. Nếu đúng → acquire thành công

  

**Ưu điểm:**

- Đơn giản, không cần WebSocket/SharedWorker

- Random backoff giải quyết collision hiệu quả

- Timeout tự động (5s) → không bị deadlock

- Cross-tab synchronization nhờ localStorage events

  

#### Multi-Tab Synchronization

  

Sử dụng **BroadcastChannel API** để đồng bộ trạng thái giữa các tab:

  

**Các loại event broadcast:**

```typescript

{

  type: "LOGIN_SUCCESS",

  payload: {

    user: User,

    expiresAt: number

  }

}

  

{

  type: "REFRESH_SUCCESS",

  payload: {

    expiresAt: number

  }

}

  

{

  type: "LOGOUT",

  payload: {

    reason: "manual" | "token_refresh_failed" | "session_expired"

  }

}

  

{

  type: "FORCE_REFRESH"

}

```

  

#### Auto-Refresh Timer

  

**Nguyên tắc thiết kế:**

- Timer **không phải leader**, chỉ là trigger kiểm tra

- Timer chạy **một lần** trước khi access token hết hạn 30s

- Timer **không share giữa các tab** (mỗi tab tự quản lý)

- Timer **reset** khi nhận broadcast `REFRESH_SUCCESS` hoặc `LOGIN_SUCCESS`

  

**Luồng hoạt động:**

1. Mỗi tab tự tính toán `timeUntilRefresh = (expiresAt * 1000 - now) - 30000`

2. Set setTimeout với thời gian tính được

3. Khi timer trigger:

   - Kiểm tra có refresh lock không?

   - Nếu không → acquire lock và refresh

   - Nếu có → chờ kết quả broadcast

4. Khi nhận broadcast refresh success:

   - Clear timer cũ

   - Tạo timer mới với expiresAt mới

  

**Ưu điểm thiết kế này:**

- Leader chết không ảnh hưởng (tab khác vẫn có timer riêng)

- 401 error luôn là fallback cuối → không bao giờ kẹt

- Tránh polling → tiết kiệm tài nguyên

- Logic rõ ràng, dễ debug

  

---

  

## 3. LUỒNG AUTHENTICATION

  

### 3.1. Login Flow

  

**Frontend:**

```

User nhập credentials

    ↓

POST /api/admin/credential/login

    {

      user_name: string,

      password: string

    }

```

  

**Backend Processing:**

  

**Bước 1: Validate Credentials**

```

1. Verify request method, validate request body tránh sql injection

2. Query user từ admin_mst WHERE user_name = ?

3. Nếu không tồn tại → 401 Unauthorized

4. Nếu limit_access >= 5 → 403 Forbidden (khóa tài khoản để tránh trường hợp dò mật khẩu)

5. Convert password qua hash và so sánh với pass trong db đã được hash. Việc hash pass, sau đó mới lưu db để tránh trường hợp dò rỉ dữ liệu DB attacker lấy được pass gốc.

   - Nếu không khớp:

     * limit_access += 1

     * save() và throw 401

   - Nếu khớp:

     * limit_access = 0

     * save() và tiếp tục

```

  

**Bước 2: Generate Tokens**

```php

// Cùng iat để đảm bảo TTL chính xác

$iat = time();

  

// Access token

$accessPayload = [

  'id' => $userId,

  'type' => 'admin',

  'iat' => $iat, // Thời điểm hiện tại + exp => tính toán thời gian hết hạn trong tương lai + verify token chính xác

  'exp' => $iat + 300  // 5 phút ngắn để bảo mật do nó sử dụng với tần xuất dày đặc, bù lại chúng sẽ được refresh thường xuyên

];

$accessToken = JWT::encode($accessPayload, ACCESS_SECRET);

  

// Refresh token

$refreshPayload = [

  'id' => $userId,

  'type' => 'admin',

  'iat' => $iat, // Tương tự

  'exp' => $iat + 259200  // 3 ngày, nó được sử dụng ít hơn nên thời gian sống dài hơn

];

$refreshToken = JWT::encode($refreshPayload, REFRESH_SECRET);

```

  

**Bước 3: Store Access Token & Permission**

```

1. Access token có đặc điểm đó là sử dụng tần xuất cao, thời gian tồn tại ngắn để bảo mật, ngoài ra còn yêu cầu cần lưu trữ để dễ dàng quản lý thu hồi. Nhưng chúng chỉ có tác dụng trong thời điếm sử dụng thôi, không cần lưu trữ dài hạn vì mục đích của chúng ban đầu được thiết kế cho nhiệm vụ trở thành key truy cập trong một thời điểm ngắn. Do đó sẽ lựa chọn lưu trữ cache thay vì DB, lưu trữ cache sẽ đáp ứng tất cả các yêu cầu trên.

  

2. Về mô hình lưu trữ có 2 loại black|white list.

  - Black list lưu trữ các token không hợp lệ như khi logout, refresh, thu hồi,.. lúc này token vẫn còn hạn sử dụng nhưng các action trên muốn vô hiệu hóa nó, nó có nhiệm vụ lưu trữ các token này để tránh trường hợp ai đó sẽ sử dụng lại các token không hợp lệ này trước khi chúng hết hạn.

  

  - White list: Thì ngược lại chúng lưu trữ danh sách các token hợp lệ. Khi có các động thái như login, refresh chúng sẽ thêm các token hợp lệ vào danh sách. Khi các hoạt động như logout, refresh token, thu hồi

  logic sẽ thực hiện loại bỏ các token không lệ này ra khỏi danh sách.

  

3. Từ mô hình lưu trữ trên, có thể thấy trong môi trường hoạt động thực tế sẽ bảo gồm nhiều hoạt động login, logout, refresh, thu hồi,... đến từ tất cả user trên cả hệ thống.

  - Trường hợp 1: Hệ thống có ít người dùng, tần xuất truy cập ít, không liên tục.

    - Black list sẽ lưu trữ tối thiểu dữ liệu. Ngược lại, white list sẽ lưu tất cả giá trị hợp lệ => black list trường hợp này hiệu quả hơn

  

  - Trường hợp 2: Hệ thống có lượng lớn người dùng, tần xuất truy cập cao, hoạt động liên tục.

    - Black list sẽ liên tục lưu trữ lượng lớn dữ liệu từ các hoạt động refresh, logout, thu hồi. Vì các thao tác này tăng lên nhanh chóng tỉ lệ thuận với tần xuất truy cập và số lượng người dùng. Điều đó cũng đồng nghĩa với việc ra tăng các token không hợp lệ còn thời hạn sử dụng, lúc này black list cần lưu trữ chúng để quản lý. Để tối ưu sẽ lưu trữ chúng trong cache để tốc độ truy xuất nhanh và lưu trữ chúng tạm thời. Thiết lập tính năng tự động xóa dữ liệu của cache, thời hạn sử dụng của dữ liệu đó còn lại cũng chính là thời gian dữ liệu đó được xóa khỏi cache (ttl)

    - White list lúc này sẽ lưu trữ dữ liệu tương ứng với số lượng token đang hoạt động. Cho dù có nhiều hoạt động refresh, logout, thu hồi,... thì số lượng token đang hoạt động vẫn sẽ giữ nguyên, không thay đổi. Do nó chỉ lưu trữ token hợp lệ, white list sẽ lưu trữ tối thiểu dữ liệu, hiệu quả hơn trong trường hợp này.

  

  => Như vậy, sẽ lựa chọn white list. Vì định hướng là một hệ thống hoạt động ổn định đáng tin cậy, có thể mở rộng xử lý cho nhiều trường hợp.

  

4. Hiện tại, hệ thống đang định hướng logic sẽ thực hiện phần quyền linh động, các quyền hạn đó sẽ được lưu trữ trong DB và dễ dàng để thay đổi. Phần quyền sẽ thực hiện kiểm tra đơn giản đó là người dùng đó có quyền thực hiện chức năng đó hay không, mỗi chức năng tương ứng với 1 action api, đồng nghĩa với việc hệ thống sẽ quản lý quyền truy cập của user đối với từng api.

  

  - Đối với phần nội dung này, mục tiêu sẽ là làm thế nào để sử dụng hiệu quả nhất điều đó. Ở đây cần có các truy vấn DB để lấy thông tin, nhưng đây là chức năng có tần xuất sử dụng dụng cao, sử dụng liên tục. Với cách thông thường mỗi lần request xác thực sẽ đều cần phải truy vấn db, điều đó ảnh hưởng đến hiệu năng xử lý. Tôi đã tạo ra một view để gom các truy vấn lại, nó sẽ làm giảm thiểu truy vấn db khi thực hiện truy vấn giữa nhiều table trong db, vì chúng đã gom data thành một table ảo trước đó rồi, thời gian truy vấn sẽ nhanh hơn.

  

  - Tiếp theo, vấn đề trên chỉ xử lý được vấn đề tốc độ truy vấn. Còn về tấn xuất sử dụng, mỗi request đều sẽ qua hàng loạt các xử lý logic và gọi DB truy vấn phần view đó. Nhận thấy dữ liệu đó là một dữ liệu thường xuyên sử dụng với tần xuất cao và chúng hiếm khi thay đổi, bởi vì việc điều chỉnh quyền hạn không xảy ra thường xuyên. Nên tôi quyết định sử dụng cache để lưu trữ dữ liệu đó lại.

  

  - Tiếp theo, tôi sẽ lưu trữ nó trong cache nhưng nhìn mớ dữ liệu chúng rất hỗn độn, tôi cần sắp xếp nó lại. Tôi sẽ filter tất cả route và loại bỏ các route bị duplicate trong kết quả truy vấn để mỗi route trong list là duy nhất. Các route duplicate không cần thiết trong này, nó làm tăng dung lượng lưu trữ mà thôi. Nhưng số lượng route cũng rất nhiều, mỗi lần request tương ứng với một request route và chúng sẽ đi so khớp tất cả route trong list, điều đó làm tăng đáng kể tài nguyên xử lý. Tôi cần gom nhóm chúng để việc truy vấn hiệu quả hơn, nhận thấy hệ thống có các loại request được chia theo method, mỗi method sẽ quản lý danh sách chứa các route của method đó. Tất cả chúng đểu là duy nhất, method khác biệt duy nhất, mỗi route trong mỗi list đều là duy nhất. Khi sử dụng cache, tôi truy vấn theo method sau đó tìm route trong list method đó. Lúc đó list route sẽ ít đi rất nhiều, việc so khớp cũng sẽ nhanh hơn.

  

  - Nhưng theo hướng này đồng nghĩa với việc đánh đổi merories -> lantecy. Khi phân quyền thay đổi cần tìm kiếm và cập nhật lại cache tương ứng.

  

5. Tiếp theo là phần gắn kết chúng lại với nhau. Một tài khoản có thể được sử dụng để đăng nhập trên nhiều thiết bị khác nhau, như vậy ở mỗi thiết bị cần có cơ chế quản lý riêng biệt để tránh xung đột, khi thực hiện các hành động login, logout, refresh, thu hồi trên mỗi thiết bị. Như vậy tôi sẽ để mỗi thiết bị sử dụng mỗi tài khoản tương ứng với 1 token riêng biệt để các chức năng đề cập được hoạt động riêng biệt. Vậy là tôi có nhiều truy cập (token) khác nhau cùng chung một tài khoản. Và tất cả chúng đều dùng chung permission, không việc gì phải tạo ra nhiều permission giống như nhau cho nhiều token có cùng account. Tôi sẽ gom nhóm chúng lại theo user id. Như vậy tất cả token cùng account bên trong đều có thể dùng chúng permission được chia sẻ.

  

6. Vấn đề dọn dẹp dữ liệu. Đương nhiên chúng có thời hạn sử dụng, nhưng chúng có thời gian sử dụng khác nhau, do có thể sử dụng trên nhiều thiết bị khác nhau và ở bất kỳ thời điểm nào. Để tối ưu thời gian sử dụng permission chung, tôi sẽ thiết lập cơ chế mỗi khi có token mới trong group user tương ứng được tạo. Permission sẽ cập nhật thời gian tồn tại của mình bằng thời gian tồn tại của token mới nhất được thêm vào. Điều này đảm bảo permission được sử dụng đến khi token cuối cùng của group user đó còn tồn tại. Cả 2 tồn tại và cùng tự động xóa khi hết hạn. không cần thiết phải lưu trữ lại cache permission khi không có user nào sử dụng nó. Đương nhiên, cần kiểm tra sự tồn tại của chúng trước khi tạo, nếu đã có rồi thì sử dụng, không cần thiết tạo mới và sử dụng thứ đã có sẵn.

  

7. Nó thường đính kèm trong mỗi request để xác thực, tần xuất sử dụng dụng cao + ít khi thay đổi trong phiên làm việc. Do đó, giữ cho nó nhỏ gọn nhất có thể.

  

8. Với mô hình này có thể dễ dang quản lý và thực hiện nhiều kiểu thu hồi truy cập như: theo thiết bị or token (mỗi thiết bị 1 token), theo cụm địa lý (địa lý đó có các ip, user agent, ...), theo thời gian (thu hồi sau 1 khoảng thời gian nhất định), theo account (1 tài khoản có thể đăng nhập nhiều thiết bị) ...  Do đó, trong các phương pháp lưu trữ cần triển khai lưu trữ thêm các thông tin khác như user id, ip thiết bị, ...

```

  

```

IF NOT EXISTS Redis key "admin:{user_id}:admin_permission"

THEN

  1. Query permission từ admin_permission_view

  2. Group by HTTP method:

     {

       "GET": [unique paths],

       "POST": [unique paths],

       ...

     }

  3. HSET admin:{user_id}:admin_permission method paths_json

  4. EXPIRE = 300s

END IF

  

// Tracking access token

HSET admin:{user_id}:{access_token} "last_access_at" NOW()

EXPIRE admin:{user_id}:{access_token} 300

  

// Extend parent permission cache TTL to match newest token

EXPIRE admin:{user_id}:admin_permission TTL(admin:{user_id}:{access_token})

```

  

**Bước 4: Store Refresh Token**

```

  1. Refresh token: Có kiến trúc tương tự access token, nhiệm vụ của nó để làm mới access token hết hạn nên nó có tần xuất sử dụng thấp. Để làm mới token, chúng cần có thời gian tồn tại lâu hơn access token. Từ các đặc điểm tần xuất sử dụng thấp và thời gian tồn tại lâu dài nên chúng sẽ được lưu trữ trong db.

```

  

```sql

INSERT INTO token_mst (

  token_hash,   -- MD5($refreshToken) việc mã hóa token khi lưu nhắm tránh trường hợp dò rỉ dữ liệu và attacker dùng nó để truy cập tài khoản thông qua token

  account_id,

  device_name,  -- User-Agent header: Để quản lý các thiết bị đã đăng nhập

  ip_address,   -- Request IP: Để quản lý các IP đã đăng nhập

  expired_at    -- NOW() + 3 days

) VALUES (?, ?, ?, ?, ?);

```

  

**Bước 5: Set Cookies**

  

```

  1. Access và refresh token chúng là nhiệm vụ các key xác thực quyền truy cập, chúng là thành phần quan trọng. khi gửi và truyền dữ liệu qua lại cần đảm bảo chúng không bị đánh cắp, chỉ có cookie là phù hợp vì chúng có các đặc tính bảo mật cần thiết. Cookie chỉ được gửi khi có cùng domain, không bị js truy cập, cookie được gửi đính kèm ở route được chỉ định trước, được gửi thông qua giao thức bảo mật HTTPS, không bị mất khi reload page, close browser, chỉ hết hạn khi hết hạn hoặc response server yêu cầu xóa. Điều mà body request, local storage, session storage không có được. Các cookie này sẽ được server thiết lập và gắn vào response header. Phía Client sẽ không cho phép thao tác với nó, đảm bảo quyền kiểm soát. Cookie cũng có tính năng chia sẽ thông tin giữa các tab có dùng domain, điều này có nghĩa cho phép chia sẻ thông tin xác thực giữa các tab, đảm bảo trải nghiệm người dùng tốt hơn.

```

  

```js

Set-Cookie: access_token={token};

            Max-Age=300;

            Path=/api/admin; // Các route này chứa route cần quyền truy cập. Điều này quy định các route cần quyền truy cập sẽ tự động đính kèm cookie access_token này trong request

            HttpOnly; // Không thể truy cập cookie này thông qua client-side script

            Secure; // Chỉ gửi cookie qua HTTPS

            SameSite=Strict // Chỉ gửi cookie trong cùng domain

  

Set-Cookie: refresh_token={token};

            Max-Age=259200;

            Path=/api/admin/credential/trust; // Các route này chứa route refresh và logout. Điều này quy định chỉ refresh và logout mới có thể tự đính kèm sử dụng cookie này

            HttpOnly; // Không thể truy cập cookie này thông qua client-side script

            Secure; // Chỉ gửi cookie qua HTTPS

            SameSite=Strict // Chỉ gửi cookie trong cùng domain

```

  

**Response:**

```json

{

  "data": {

    "user": {...},

    "expires_at": 1712346278  // Absolute timestamp

  },

  "error": {

    "status": false

  }

}

```

  

**Frontend After Login:**

```

1. Dispatch SET_AUTHENTICATED action

2. Lưu user + expiresAt vào state

3. Broadcast LOGIN_SUCCESS event

4. Schedule refresh timer (expiresAt - 30s). Cần cơ chế làm mới token trước khi nó hết hạn để tránh user bị logout đột ngột, user cần login lại. Trải nghiệm sử dụng kém do bị gián đoạn. Buffer là 30s trước khi hết hạn.

5. Các tab khác nhận broadcast:

   - Update state

   - Schedule timer riêng

```

  

### 3.2. Request Validation Flow (Middleware)

  

**Mọi request đến `/api/admin/*` (trừ route login, refresh) đều qua AdminMiddleware:**

  

```

1. Extract access_token từ cookie

  

2. IF NOT exists → 401 Unauthorized

  

3. Decode & Verify JWT:

   - Verify signature với ACCESS_SECRET

   - Check expiration (exp < now?)

   - Check TTL calculation (exp - iat == 300s?)

   → Nếu fail → 401 Unauthorized

  

4. Extract payload: { id, type }

  

5. Verify token type:

   IF type !== 'admin' → 401 Unauthorized

  

6. Check token trong Redis:

   IF NOT EXISTS admin:{id}:{access_token} → 401 Unauthorized

  

7. Authorization (xem mục 4)

  

8. Set request attributes:

   - current_admin_id = id

   - user resolver = { id, type }

  

9. Continue to controller

```

  

### 3.3. Refresh Token Flow

  

**Trigger điều kiện:**

- Frontend nhận 401 từ bất kỳ API nào (trừ login/refresh)

- Auto-refresh timer hết hạn

  

**Frontend Processing:**

  

```

1. Request gọi API nhận 401

2. Interceptor catch error:

   IF request api != '/refresh' AND != '/login'

   THEN

     IF authLock.isLocked()

     THEN

       Wait for lock release (max 5s)

       Retry original request

     ELSE

       IF authLock.acquire()

       THEN

         Call POST /api/admin/credential/trust/refresh-token

         ON SUCCESS:

           - authLock.release()

           - Retry original request (access_token tự động cập nhật qua cookie)

         ON ERROR:

           - authLock.release()

           - Broadcast LOGOUT

           - Redirect to login

       ELSE

         Wait for other tab to finish

         Retry original request

       END IF

     END IF

   END IF

```

  

**Backend Processing:**

  

```

1. Extract refresh_token từ cookie

  

2. Verify refresh token:

   - Decode với REFRESH_SECRET

   - Check signature, exp, TTL calculation

   → Fail → 401 Unauthorized

  

3. Extract payload: { id, type }

  

4. Verify token trong DB:

   tokenHash = MD5(refresh_token)

   token = SELECT * FROM token_mst

           WHERE token_hash = tokenHash

           AND account_id = id

   IF NOT found → 401 Unauthorized

  

5. DELETE token từ DB (one-time use)

  

6. Generate tokens mới (flow giống login)

  

7. Store access token & refresh permission cache

  

8. INSERT refresh token mới vào DB

  

9. Set cookies mới

  

10. Response expires_at

```

  

**Frontend After Refresh:**

```

1. AuthProvider nhận expires_at mới

2. Dispatch SET_EXPIRES_AT

3. Broadcast REFRESH_SUCCESS

4. Clear timer cũ

5. Schedule timer mới với expires_at mới

6. Các tab khác:

   - Nhận broadcast

   - Update expiresAt

   - Reset timer riêng

```

  

### 3.4. Logout Flow

  

**Frontend:**

```

1. User click logout HOẶC refresh token fail

2. Call POST /api/admin/credential/trust/logout

```

  

**Backend:**

```

1. Extract access_token từ cookie

2. Extract refresh_token từ cookie

  

3. Revoke access token:

   DEL Redis key admin:{user_id}:{access_token}

  

4. Revoke refresh token:

   tokenHash = MD5(refresh_token)

   DELETE FROM token_mst

   WHERE token_hash = tokenHash

   AND account_id = user_id

  

5. Clear cookies (Max-Age=-1):

   Set-Cookie: access_token=; Max-Age=-1; Path=/api/admin

   Set-Cookie: refresh_token=; Max-Age=-1; Path=/api/admin/credential/trust

  

6. Response success

```

  

**Frontend After Logout:**

```

1. Dispatch LOGOUT action

2. Clear state (user, expiresAt, timer)

3. Broadcast LOGOUT event

4. Redirect to login

5. Các tab khác:

   - Nhận broadcast

   - Clear state

   - Redirect to login

```

  

---

  

## 4. LUỒNG AUTHORIZATION

  

### 4.1. Permission Check Logic (AdminMiddleware)

  

```

1. Request đã qua authentication (có user_id)

  

2. Extract current route info:

   method = strtoupper($request->method())         // GET, POST, ...

   currentRoute = trim($request->route()->uri(), '/') // "api/admin/users"

  

3. Get permission từ Redis:

   permissionKey = "admin:{user_id}:admin_permission"

   pathsJson = Redis::hget(permissionKey, method)

   IF pathsJson == null → 404 Not Found

   allowedRoutes = json_decode(pathsJson)

  

4. Check permission:

   IF currentRoute NOT IN allowedRoutes → 403 Forbidden

  

5. Allow request to continue

```

  

### 4.2. Permission Cache Management

  

**Khi nào permission được tạo/cập nhật?**

  

**Tạo mới (on-demand):**

```

Trigger: Login hoặc Refresh token

Condition: IF NOT EXISTS admin:{user_id}:admin_permission

  

Process:

  1. Query admin_permission_view WHERE admin_mst_id = user_id

  2. Distinct + Group by method

  3. HSET vào Redis

  4. EXPIRE = TTL của access token mới nhất

```

  

**Gia hạn TTL:**

```

Trigger: Mỗi lần login/refresh tạo access token mới

  

Process:

  IF EXISTS admin:{user_id}:admin_permission

  THEN

    EXPIRE admin:{user_id}:admin_permission TTL(admin:{user_id}:{new_access_token})

  END IF

Effect:

  - Permission cache tồn tại bằng token mới nhất

  - Tất cả token đều hết hạn → permission tự xóa

```

  

**Xóa cache (invalidation):**

```

Trigger: Khi admin update role/permission của user

  

Process:

  1. Get danh sách user_id bị ảnh hưởng

  2. FOR EACH user_id:

       DEL admin:{user_id}:admin_permission

       DEL admin:{user_id}:{access_token_patterns}

  3. User sẽ nhận 401 ở request tiếp theo

  4. Frontend auto refresh token

  5. Backend tạo lại permission cache mới

```

  

### 4.3. Multi-Device Permission Handling

  

**Đặc điểm:**

- Mỗi device có access token riêng

- Tất cả devices của cùng user **chia sẻ** 1 permission cache

- Permission cache có TTL = TTL của token mới nhất

  

**Ví dụ:**

```

User login từ:

  - Device A (8:00 AM) → token expires 8:05 AM

  - Device B (8:02 AM) → token expires 8:07 AM

  - Device C (8:03 AM) → token expires 8:08 AM

  

Redis state:

  admin:123:admin_permission → TTL = 8:08 AM (theo device C)

  admin:123:{token_A} → TTL = 8:05 AM

  admin:123:{token_B} → TTL = 8:07 AM

  admin:123:{token_C} → TTL = 8:08 AM

  

Timeline:

  8:05 AM → token_A tự xóa (device A nhận 401 và auto refresh)

  8:07 AM → token_B tự xóa (device B nhận 401 và auto refresh)

  8:08 AM → token_C tự xóa (device C nhận 401 và auto refresh)

           → Permission cache cũng tự xóa (nếu không có token mới)

```

  

**Ưu điểm:**

- Tiết kiệm memory (không duplicate permission cho mỗi device)

- Tự động cleanup khi không còn device active

- Permission luôn sync giữa devices

  

**Nhược điểm & Giải pháp:**

```

Vấn đề: Update permission không đồng bộ ngay lập tức

  - Device A đang dùng → có permission cache cũ

  - Admin revoke permission

  - Device A vẫn access được trong tối đa 5 phút (until access token expire)

  

Giải pháp:

  1. Manual invalidation:

     - Admin update permission → backend DELETE cache key

     - Device A nhận 401 ngay lập tức → auto refresh → load permission mới

  

  2. Critical permission:

     - Không cache trong Redis

     - Check trực tiếp từ DB mỗi request

     - Trade-off: latency cao hơn, nhưng real-time

```

  

---

  

## 5. CƠ CHẾ QUẢN LÝ TOKEN

  

### 5.1. Token Lifecycle

  

```

┌─────────────────────────────────────────────────────────────┐

│                    ACCESS TOKEN LIFECYCLE                    │

├─────────────────────────────────────────────────────────────┤

│ Birth:    Login/Refresh → JWT encode → Set cookie → Redis   │

│ Life:     300s (5 min) → Auto validate every request        │

│ Death:    TTL expired → Redis auto delete                   │

│           OR Manual revoke (logout/update permission)        │

└─────────────────────────────────────────────────────────────┘

  

┌─────────────────────────────────────────────────────────────┐

│                   REFRESH TOKEN LIFECYCLE                    │

├─────────────────────────────────────────────────────────────┤

│ Birth:    Login/Refresh → JWT encode → Hash → DB → Cookie   │

│ Life:     259200s (3 days) → Passive (only on refresh)      │

│ Death:    One-time use (DELETE on refresh)                  │

│           OR Manual revoke (logout)                          │

│           OR Cron job cleanup (expired_at < NOW)            │

└─────────────────────────────────────────────────────────────┘

```

  

### 5.2. Token Rotation Strategy

  

**Access Token:**

- Không rotate (mỗi 5 phút tự expire và refresh mới)

- Mỗi request không tạo token mới (tránh overhead)

  

**Refresh Token:**

- **Rotation mỗi lần refresh** (security best practice)

- Flow:

  1. Client gửi refresh_token_old

  2. Server verify và DELETE refresh_token_old từ DB

  3. Server generate refresh_token_new

  4. Server INSERT refresh_token_new vào DB

  5. Server response + set cookie refresh_token_new

  6. Client dùng refresh_token_new cho lần refresh sau

  

**Lý do rotation:**

- Refresh token bị leak → chỉ dùng được 1 lần

- Phát hiện token reuse → revoke toàn bộ session

- Giảm impact khi refresh token bị đánh cắp

  

### 5.3. Token Storage Security

  

**Cookie Configuration:**

```

access_token:

  - HttpOnly: true      → JS không đọc được (chống XSS)

  - Secure: true        → Chỉ gửi qua HTTPS

  - SameSite: Strict    → Chống CSRF

  - Path: /api/admin    → Chỉ gửi cho API admin

  - Max-Age: 300        → Auto expire

  

refresh_token:

  - HttpOnly: true

  - Secure: true

  - SameSite: Strict

  - Path: /api/admin/credential/trust  → Chỉ gửi cho refresh endpoint

  - Max-Age: 259200

```

  

**Database Storage:**

```

Không lưu plain text refresh token:

  - Hash: MD5(refresh_token)

  - Deterministic: cùng token → cùng hash

  - Không reverse: hash → token

  - Trade-off: MD5 không mạnh, nhưng đủ cho token random

```

  

**Tại sao không dùng bcrypt/argon2 cho refresh token?**

- Refresh token đã random (256-bit entropy)

- Không cần slow hash (không có brute-force risk)

- MD5 đủ nhanh để lookup

- Mục tiêu: obscure token trong DB, không phải prevent brute-force

  

### 5.4. Token Revocation

  

**Case 1: Logout (user-triggered)**

```

1. User click logout

2. DELETE access token từ Redis

3. DELETE refresh token từ DB

4. Clear cookies

5. Broadcast LOGOUT → all tabs logout

```

  

**Case 2: Session expired (timeout)**

```

1. Access token hết hạn → Redis tự xóa

2. Frontend nhận 401 → call refresh

3. Refresh token hết hạn → DB có record nhưng expired_at < now

4. Backend return 401 → Frontend logout

```

  

**Case 3: Manual revoke (admin action)**

```

Scenario: Admin phát hiện user gian lận

  

Backend:

  1. Get user_id cần revoke

  2. Redis: KEYS admin:{user_id}:* → get all keys

  3. Redis: DEL all keys → revoke tất cả access tokens

  4. DB: DELETE FROM token_mst WHERE account_id = user_id

     → revoke tất cả refresh tokens

  

Effect:

  - Tất cả devices của user nhận 401 ngay lập tức

  - Không thể refresh (refresh token đã xóa)

  - User bị force logout toàn bộ sessions

```

  

**Case 4: Permission update (role change)**

```

Scenario: Admin thay đổi role/permission của user

  

Backend:

  1. Update role/permission tables

  2. Get affected user_ids

  3. FOR EACH user_id:

       DEL admin:{user_id}:admin_permission

       # Không xóa access token → user vẫn authenticated

  4. User request tiếp theo:

     - Access token valid → qua authentication

     - Permission cache không tồn tại → 401

     - Frontend auto refresh → reload permission

```

  

### 5.5. Concurrent Session Management

  

**Giới hạn số lượng login đồng thời:**

  

**Hiện tại:** Không giới hạn (user có thể login nhiều thiết bị)

  

**Nếu muốn giới hạn:**

```php

// Frontend request login

public function login(Request $request) {

    $user = authenticate($credentials);

    // Count active tokens

    $activeTokens = TokenMst::where('account_id', $user->id)

                            ->where('expired_at', '>', now())

                            ->count();

    if ($activeTokens >= 5) {

        throw new AuthorizationException('errors.E0611'); // Max 5 devices

    }

    // Continue login...

}

```

  

**Clean up old sessions:**

```php

// Cron job chạy hàng ngày

public function cleanupExpiredTokens() {

    TokenMst::where('expired_at', '<', now())->delete();

}

```

  

---

  

## 6. MULTI-TAB & MULTI-DEVICE

  

### 6.1. Multi-Tab Synchronization (Cùng Browser)

  

**Công nghệ: BroadcastChannel API**

  

**Đặc điểm:**

- Chỉ hoạt động trong cùng origin (same protocol, domain, port)

- Lightweight, built-in browser API

- Không cần server/WebSocket

- Auto cleanup khi tab đóng

  

**Use cases:**

  

**UC1: Login ở tab A → Tab B auto login**

```

Tab A:

  1. User login

  2. AuthProvider dispatch SET_AUTHENTICATED

  3. Broadcast { type: "LOGIN_SUCCESS", payload: { user, expiresAt } }

  

Tab B (đang ở login page):

  1. Receive message

  2. Dispatch SET_AUTHENTICATED

  3. Redirect to dashboard

  4. Schedule refresh timer

```

  

**UC2: Logout ở tab A → Tab B auto logout**

```

Tab A:

  1. User logout

  2. Call API logout

  3. Dispatch LOGOUT

  4. Broadcast { type: "LOGOUT", payload: { reason: "manual" } }

  5. Redirect to login

  

Tab B (đang ở dashboard):

  1. Receive message

  2. Dispatch LOGOUT

  3. Clear timer

  4. Redirect to login

```

  

**UC3: Refresh token ở tab A → Tab B không cần refresh**

```

Timeline:

  8:00:00 - Both tabs schedule timer for 8:04:30 (30s before 8:05:00)

  

  8:04:30 - Tab A timer triggers:

              - Acquire lock in localStorage

              - Call refresh API

              - Get new expiresAt = 8:10:00

              - Broadcast REFRESH_SUCCESS

  

  8:04:30 - Tab B timer triggers (almost same time):

              - Try acquire lock → FAIL (Tab A đang hold)

              - Wait for broadcast

  

  8:04:32 - Tab A finish refresh:

              - Release lock

              - Broadcast { type: "REFRESH_SUCCESS", payload: { expiresAt: 8:10:00 } }

  

  8:04:32 - Tab B receive broadcast:

              - Update expiresAt to 8:10:00

              - Clear old timer

              - Schedule new timer for 8:09:30

```

  

**UC4: Tab A crash/close → Tab B không bị ảnh hưởng**

```

Tab A:

  1. Schedule timer for 8:04:30

  2. Crash at 8:02:00

  

Tab B:

  1. Schedule timer for 8:04:30

  2. Timer still runs normally

  3. At 8:04:30 → check lock → not found → acquire lock → refresh

  

Timeline:

  8:02:00 - Tab A crash (timer destroyed)

  8:04:30 - Tab B timer triggers → acquire lock → refresh → broadcast

  8:04:32 - All other tabs (if any) receive broadcast and update

  

Kết luận: Không cần "leader election" cố định

          Mỗi tab tự quản lý, lock mechanism giải quyết conflict

```

  

### 6.2. Multi-Device (Khác Browser/Máy)

  

**Đặc điểm:**

- Mỗi device có access token riêng

- Mỗi device có refresh token riêng (tracking trong DB)

- Chia sẻ permission cache (tiết kiệm Redis memory)

- Không đồng bộ state (không BroadcastChannel)

  

**Timeline độc lập:**

```

Device A (Desktop Chrome):

  8:00 - Login → token_A expires 8:05

  8:04 - Refresh → token_A' expires 8:09

  8:08 - Refresh → token_A'' expires 8:13

  

Device B (Mobile Safari):

  8:02 - Login → token_B expires 8:07

  8:06 - Refresh → token_B' expires 8:11

  8:10 - Logout → token_B' revoked

  

Device C (Laptop Firefox):

  8:05 - Login → token_C expires 8:10

  8:09 - Idle (không refresh) → token_C expires

  8:11 - User quay lại → nhận 401 → auto refresh → OK

```

  

**Permission cache sharing:**

```

Redis state (user_id = 123):

  

  admin:123:admin_permission → TTL theo token mới nhất

    { "GET": [...], "POST": [...] }

  

  admin:123:{token_A''} → TTL 8:13 (Desktop)

  admin:123:{token_B'} → Đã xóa (Logout)

  admin:123:{token_C'} → TTL 8:16 (Laptop refresh mới)

  

→ Permission cache TTL = 8:16 (theo token_C')

→ Device A và C dùng chung permission

→ Device B đã logout → không còn token

```

  

**Revoke single device:**

```php

// Admin revoke device cụ thể

public function revokeDevice(int $userId, string $deviceName) {

    // Xóa refresh token của device

    TokenMst::where('account_id', $userId)

            ->where('device_name', 'LIKE', "%$deviceName%")

            ->delete();

    // Access token vẫn valid trong tối đa 5 phút

    // Khi user refresh → refresh token không còn → 401 → logout

}

```

  

---

  

## 7. BẢO MẬT & XỬ LÝ LỖI

  

### 7.1. Bảo Mật Layers

  

**Layer 1: Transport Security**

```

- HTTPS only (TLS 1.2+)

- Cookie Secure flag = true

- HSTS header (production)

```

  

**Layer 2: [[CSRF]] Protection**

```

- SameSite=Strict cookie

- Origin header validation

- No custom headers needed (cookie auto-sent)

```

  

**Layer 3: XSS Protection**

```

- HttpOnly cookie → JS không đọc được

- Content-Security-Policy header

- Input sanitization (Laravel validator)

```

  

**Layer 4: Token Security**

```

Access Token:

  - Short TTL (5 min) → giảm thời gian tấn công

  - Signature verification mỗi request

  - Lưu trong Redis → có thể revoke

  

Refresh Token:

  - Hash trước khi lưu DB

  - One-time use (rotation)

  - Device/IP tracking

  - Manual revoke capability

```

  

**Layer 5: Rate Limiting**

```

// Laravel middleware (nên thêm)

Route::middleware('throttle:5,1')->group(function () {

    Route::post('/login');      // 5 lần/phút

    Route::post('/refresh');    // 5 lần/phút

});

  

// Redis-based distributed rate limit

```

  

**Layer 6: Brute-Force Protection**

```

Login attempt tracking:

  - limit_access counter trong admin_mst

  - Tăng mỗi lần sai password

  - Lock account khi >= 5 lần

  - Admin manual unlock hoặc auto unlock sau X giờ

```

  

### 7.2. Error Handling Strategy

  

**Backend Error Codes:**

```

E0401 - Unauthorized (invalid credentials/token)

E0600 - Invalid JWT format

E0601 - Invalid JWT header

E0603 - Invalid JWT payload

E0604 - JWT payload schema mismatch

E0605 - Invalid JWT signature

E0606 - JWT signature verification failed

E0607 - JWT token expired

E0608 - Invalid token type

E0609 - Token not found in cache/DB

E0610 - Account locked (too many failed attempts)

E0611 - Max concurrent sessions reached

```

  

**Frontend Error Handling:**

```typescript

// Interceptor flow

try {

  response = await axios.get('/api/admin/users');

} catch (error) {

  if (error.response?.status === 401) {

    // Case 1: Refresh endpoint itself failed

    if (isRefreshEndpoint(error.config)) {

      → Logout immediately

      → Broadcast LOGOUT

      → Redirect to login

    }

    // Case 2: Other endpoints

    if (authLock.isLocked()) {

      → Wait for lock release (max 5s)

      → Retry request

    } else {

      → Acquire lock

      → Call refresh

      → ON SUCCESS: Retry request

      → ON FAIL: Logout

    }

  }

  if (error.response?.status === 403) {

    → Show "Permission denied" message

    → Don't retry

  }

  if (error.response?.status >= 500) {

    → Show "Server error" message

    → Có thể retry với exponential backoff

  }

}

```

  

**Auto-Refresh Error Handling:**

```typescript

// AuthProvider

const performRefresh = async (isRetry = false) => {

  try {

    const data = await authService.refreshToken();

    retryCountRef.current = 0;

    broadcast({ type: "REFRESH_SUCCESS", payload: { expiresAt: data.expires_at } });

  } catch (error) {

    if (!isRetry && retryCountRef.current < 3) {

      // Retry after 1s (network glitch)

      retryCountRef.current++;

      setTimeout(() => performRefresh(true), 1000);

    } else {

      // Max retries reached → logout

      dispatch({ type: "SET_ERROR", payload: { code: "TOKEN_REFRESH_FAILED" } });

      await performLogout("token_refresh_failed");

    }

  }

};

```

  

### 7.3. Security Best Practices Implemented

  

✅ **Implemented:**

- JWT với signature verification

- HttpOnly + Secure + SameSite cookies

- Refresh token rotation

- Hash refresh token trong DB

- Access token short TTL (5 min)

- Brute-force protection (limit_access)

- Lock mechanism tránh race condition

- Permission cache với TTL auto-cleanup

- Device/IP tracking

- Manual token revocation

  

⚠️ **Cần cải thiện (xem mục 8):**

- Rate limiting chưa có

- CORS configuration cần review

- Refresh token TTL nên rút ngắn (7 ngày → 3 ngày ✓ đã fix)

- Không có session fingerprinting

- Không có token reuse detection

- Logging/audit trail chưa đầy đủ

  

---

  

## 8. ĐÁNH GIÁ & KHUYẾN NGHỊ

  

### 8.1. Điểm Mạnh Của Hệ Thống Hiện Tại

  

#### ✅ **Kiến Trúc Token Hợp Lý**

- Short-lived access token (5 min) giảm rủi ro bảo mật

- Long-lived refresh token (3 ngày) cân bằng UX

- Rotation strategy đúng chuẩn OAuth2

- Cookie-based storage an toàn hơn localStorage

  

#### ✅ **Authorization Performance Cao**

- Permission cache ở Redis → latency thấp

- Share permission giữa nhiều tokens → tiết kiệm memory

- TTL tự động sync → không cần manual cleanup

- View SQL tối ưu query permission

  

#### ✅ **Multi-Tab/Device Experience Tốt**

- BroadcastChannel đồng bộ mượt

- Lock mechanism tránh race condition hiệu quả

- Auto-refresh transparent với user

- Mỗi device độc lập → không ảnh hưởng lẫn nhau

  

#### ✅ **Security Fundamentals Vững**

- HTTPS + HttpOnly + Secure + SameSite

- No XSS risk (token trong cookie)

- CSRF protection (SameSite=Strict)

- Brute-force protection (limit_access)

  

### 8.2. Điểm Yếu & Rủi Ro

  

#### ⚠️ **Thiếu Rate Limiting**

  

**Vấn đề:**

- Login/refresh endpoints không có rate limit

- Attacker có thể spam refresh để DDoS

- Brute-force vẫn khả thi (5 lần/account, nhưng unlimited accounts)

  

**Giải pháp:**

```php

// Laravel throttle middleware

Route::middleware('throttle:login')->post('/login');

Route::middleware('throttle:refresh')->post('/refresh');

  

// config/cache.php

'stores' => [

    'redis' => [

        'driver' => 'redis',

        'connection' => 'cache',

        'lock_connection' => 'default',

    ],

],

  

// RouteServiceProvider

RateLimiter::for('login', function (Request $request) {

    return Limit::perMinute(5)->by($request->ip());

});

  

RateLimiter::for('refresh', function (Request $request) {

    return Limit::perMinute(10)->by($request->cookie('refresh_token'));

});

```

  

#### ⚠️ **Token Reuse Detection Chưa Có**

  

**Vấn đề:**

- Refresh token bị đánh cắp → attacker và victim cùng dùng

- Hệ thống không phát hiện được reuse

- Chỉ biết khi victim refresh → token cũ đã bị delete

  

**Giải pháp:**

```php

// Khi refresh token

public function refreshToken(Request $request) {

    $refreshToken = $request->cookie('refresh_token');

    $tokenHash = md5($refreshToken);

    $token = TokenMst::where('token_hash', $tokenHash)->first();

    if (!$token) {

        // Token không tồn tại → có thể đã được dùng → REUSE DETECTED

        // Revoke tất cả tokens của user này

        $userId = JWT::decode($refreshToken)['id'];

        TokenMst::where('account_id', $userId)->delete();

        Redis::del("admin:$userId:*");

        // Log security event

        Log::warning('Token reuse detected', ['user_id' => $userId]);

        throw new AuthorizationException('Token reuse detected. All sessions revoked.');

    }

    // Continue normal refresh...

}

```

  

#### ⚠️ **Session Fingerprinting Thiếu**

  

**Vấn đề:**

- Token bị đánh cắp → attacker dùng từ device/IP khác

- Không detect được anomaly

  

**Giải pháp:**

```php

// Khi tạo token

$fingerprint = hash('sha256', json_encode([

    'user_agent' => $request->header('User-Agent'),

    'ip' => $request->ip(),

    'accept_language' => $request->header('Accept-Language'),

]));

  

TokenMst::create([

    'token_hash' => md5($refreshToken),

    'fingerprint' => $fingerprint,

    // ...

]);

  

// Khi refresh

$currentFingerprint = hash('sha256', ...);

if ($token->fingerprint !== $currentFingerprint) {

    // Fingerprint khác → suspicious

    Log::warning('Fingerprint mismatch', [

        'user_id' => $userId,

        'expected' => $token->fingerprint,

        'actual' => $currentFingerprint,

    ]);

    // Option 1: Reject

    throw new AuthorizationException('Invalid session');

    // Option 2: Require re-login

    // Option 3: Send email notification + allow

}

```

  

⚠️ **Lưu ý:** Fingerprinting không perfect (user đổi IP, browser update), chỉ dùng làm signal phụ.

  

#### ⚠️ **Audit Logging Chưa Đầy Đủ**

  

**Vấn đề:**

- Không track login history

- Không log failed attempts

- Không track token usage

- Khó forensics khi bị tấn công

  

**Giải pháp:**

```php

// Migration: create admin_login_history table

Schema::create('admin_login_history', function (Blueprint $table) {

    $table->id();

    $table->unsignedInteger('admin_mst_id');

    $table->string('event_type'); // login, logout, refresh, failed_login

    $table->string('ip_address');

    $table->string('user_agent');

    $table->string('country')->nullable();

    $table->boolean('success');

    $table->string('failure_reason')->nullable();

    $table->timestamp('created_at');

});

  

// Log events

Log::channel('auth')->info('Login success', [

    'user_id' => $user->id,

    'ip' => $request->ip(),

    'user_agent' => $request->header('User-Agent'),

]);

  

// UI: Show user "Active sessions" với option "Logout other devices"

```

  

#### ⚠️ **Permission Cache Invalidation Không Real-time**

  

**Vấn đề:**

- Admin update permission → cache không xóa ngay

- User vẫn có quyền cũ trong tối đa 5 phút (until token expire)

- Không phù hợp với critical permission

  

**Giải pháp hiện tại (đã có):**

```php

// Khi update permission

public function updatePermission(int $roleId) {

    // Update DB

    DB::table('api_role_mst')->where('role_mst_id', $roleId)->update(...);

    // Get affected users

    $userIds = DB::table('admin_role_mst')

                 ->where('role_mst_id', $roleId)

                 ->pluck('admin_mst_id');

    // Invalidate cache

    foreach ($userIds as $userId) {

        Redis::del("admin:$userId:admin_permission");

        // Optional: Xóa tất cả access tokens của user

        $keys = Redis::keys("admin:$userId:*");

        foreach ($keys as $key) {

            Redis::del($key);

        }

    }

}

```

  

**Giải pháp nâng cao:**

```php

// Real-time permission check cho critical endpoints

class CriticalPermissionMiddleware {

    public function handle(Request $request, Closure $next) {

        $userId = $request->attributes->get('current_admin_id');

        // Luôn query DB, không dùng cache

        $hasPermission = DB::table('admin_permission_view')

            ->where('admin_mst_id', $userId)

            ->where('type', $request->method())

            ->where('path', $request->route()->uri())

            ->exists();

        if (!$hasPermission) {

            throw new AuthorizationException('Permission denied');

        }

        return $next($request);

    }

}

  

// Apply cho critical routes

Route::middleware(['admin', 'critical_permission'])->group(function () {

    Route::delete('/users/{id}');

    Route::post('/permissions/update');

});

```

  

#### ⚠️ **Concurrent Session Limit Chưa Có**

  

**Vấn đề:**

- User có thể login unlimited devices

- Account sharing dễ dàng

- Khó control license

  

**Giải pháp:** (Xem mục 5.5)

  

### 8.3. Khuyến Nghị Roadmap

  

#### 🔴 **Ưu tiên cao (Security critical)**

  

1. **Implement rate limiting cho login/refresh**

   - Effort: 1 day

   - Impact: Prevent DDoS, brute-force

  

2. **Add audit logging cho auth events**

   - Effort: 2 days

   - Impact: Forensics, compliance

  

3. **Token reuse detection**

   - Effort: 1 day

   - Impact: Detect stolen tokens

  

#### 🟡 **Ưu tiên trung (UX improvement)**

  

4. **Session management UI**

   - Show active devices/locations

   - "Logout other devices" button

   - Effort: 3 days

   - Impact: User control, transparency

  

5. **Concurrent session limit**

   - Configurable max devices

   - Effort: 1 day

   - Impact: License control

  

#### 🟢 **Ưu tiên thấp (Optional enhancement)**

  

6. **Session fingerprinting**

   - Effort: 2 days

   - Impact: Anomaly detection (có false positives)

  

7. **Push notification khi login mới**

   - Email/SMS

   - Effort: 3 days

   - Impact: User awareness

  

8. **Refresh token TTL tự động rút ngắn**

   - Idle 7 ngày → TTL giảm còn 1 ngày

   - Effort: 2 days

   - Impact: Balance UX & security

  

### 8.4. So Sánh Với Industry Standards

  

#### **OAuth 2.0 / OpenID Connect**

  

| Aspect | Hệ thống hiện tại | OAuth 2.0 standard | Assessment |

|--------|-------------------|-------------------|------------|

| Token format | JWT | JWT/Opaque | ✅ Đúng |

| Access TTL | 5 min | 10-60 min | ✅ Tốt (ngắn hơn) |

| Refresh TTL | 3 ngày | 7-90 ngày | ✅ Hợp lý |

| Token rotation | ✅ Có | Recommended | ✅ Đúng |

| Revocation | Manual | RFC 7009 | ⚠️ Thiếu endpoint `/revoke` |

| PKCE | ❌ Không | SPA required | ❌ Không cần (server-side cookie) |

| Scope | ❌ Không | Recommended | ⚠️ Nên thêm (thay vì permission list) |

  

**Lưu ý:** PKCE không cần vì:

- Token lưu trong HttpOnly cookie (không phải localStorage)

- Backend render cookie (không phải SPA redirect flow)

- No authorization code flow (direct password grant)

  

#### **OWASP Recommendations**

  

| OWASP Guideline | Implemented? | Notes |

|-----------------|--------------|-------|

| Use HTTPS | ✅ Yes | Production enforced |

| HttpOnly cookies | ✅ Yes | |

| Secure flag | ✅ Yes | Production only |

| SameSite | ✅ Yes | Strict mode |

| Short access token | ✅ Yes | 5 min |

| Refresh rotation | ✅ Yes | |

| Rate limiting | ❌ No | **Cần thêm** |

| Account lockout | ✅ Yes | limit_access >= 5 |

| Audit logging | ⚠️ Partial | **Cần mở rộng** |

| Token reuse detection | ❌ No | **Cần thêm** |

  

---

  

## 9. KẾT LUẬN

  

### 9.1. Tổng Kết

  

Hệ thống Authentication & Authorization hiện tại được xây dựng trên nền tảng **vững chắc** với kiến trúc JWT chuẩn, permission cache hiệu quả, và multi-device/multi-tab experience mượt mà.

  

**Điểm nổi bật:**

- **Bảo mật cơ bản tốt:** HTTPS, HttpOnly cookies, token rotation, brute-force protection

- **Hiệu năng cao:** Redis cache, view SQL tối ưu, auto-cleanup

- **UX tốt:** Auto-refresh transparent, multi-tab sync, 401 fallback luôn hoạt động

- **Scalable:** Stateless architecture, Redis distributed cache

  

**Điểm cần cải thiện:**

- **Rate limiting** (critical security gap)

- **Audit logging** (forensics, compliance)

- **Token reuse detection** (stolen token protection)

- **Session management UI** (user control)

  

### 9.2. Mức Độ Sẵn Sàng Production

  

**✅ Sẵn sàng cho:**

- Internal admin systems (team < 50 users)

- Low-to-medium traffic applications

- Non-critical data

  

**⚠️ Cần bổ sung trước khi deploy cho:**

- Public-facing applications

- High-traffic systems (>1000 concurrent users)

- Financial/healthcare data (high compliance)

- Multi-tenant SaaS

  

**🔴 Critical actions before production:**

1. Add rate limiting (Laravel throttle)

2. Implement audit logging (login history table)

3. Review CORS configuration

4. Load testing (Redis cache capacity, concurrent refresh)

5. Security penetration testing

6. Disaster recovery plan (Redis failover, DB backup)

  

### 9.3. Maintenance Notes

  

**Daily:**

- Monitor rate limit violations

- Review failed login attempts

- Check Redis memory usage

  

**Weekly:**

- Audit login history anomalies

- Review active sessions count

- Clean up expired tokens (cron job)

  

**Monthly:**

- Rotate JWT secrets (access + refresh)

- Review permission cache hit rate

- Security patch updates

  

**Quarterly:**

- Penetration testing

- Performance benchmarking

- Documentation update

  

---

  

## PHỤ LỤC

  

### A. Cấu Hình Đề Xuất

  

#### Laravel `.env`

```env

# JWT Secrets (rotate quarterly)

ACCESS_TOKEN_SECRET=random_256_bit_string

REFRESH_TOKEN_SECRET=random_256_bit_string_different

  

# Token TTL

MAX_ACCESS_TTL=300          # 5 minutes

MAX_REFRESH_TTL=259200      # 3 days

  

# Security

LIMIT_ACCESS_FAIL=5         # Lock after 5 failed attempts

SESSION_DOMAIN=.yourdomain.com

SESSION_SECURE_COOKIE=true  # Production only

  

# Redis

REDIS_HOST=127.0.0.1

REDIS_PASSWORD=null

REDIS_PORT=6379

REDIS_DB=0                  # Auth cache

REDIS_CACHE_DB=1            # App cache

```

  

#### Next.js `.env`

```env

NEXT_PUBLIC_API_BASE_URL=https://api.yourdomain.com

NEXT_PUBLIC_AUTH_CHANNEL=auth_sync_v1

  

# Auto-refresh config

NEXT_PUBLIC_REFRESH_BEFORE_EXPIRY=30000  # 30s before expire

NEXT_PUBLIC_MAX_RETRIES=3

NEXT_PUBLIC_RETRY_DELAY=1000

```

  

### B. Monitoring Metrics

  

**Key metrics to track:**

```

# Authentication

- auth.login.success_rate

- auth.login.failure_rate_by_reason

- auth.refresh.success_rate

- auth.refresh.latency_p95

- auth.logout.count

  

# Authorization

- authz.permission_cache_hit_rate

- authz.permission_cache_miss_count

- authz.403_errors_by_endpoint

  

# Tokens

- tokens.active_access_count

- tokens.active_refresh_count

- tokens.expired_cleanup_count

  

# Redis

- redis.memory_usage_bytes

- redis.key_count_by_pattern

- redis.evicted_keys_count

  

# Security

- security.account_lockouts_count

- security.suspicious_login_attempts

- security.token_reuse_detected

```

  

### C. Troubleshooting Guides

  

**Issue: User nhận 401 liên tục**

```

1. Check access token trong cookie:

   - DevTools → Application → Cookies

   - Verify access_token exists và chưa expire (Max-Age > 0)

  

2. Check Redis:

   - redis-cli

   - EXISTS admin:{user_id}:{access_token}

   - TTL admin:{user_id}:{access_token}

  

3. Check refresh token:

   - psql -d database

   - SELECT * FROM token_mst WHERE account_id = ?;

   - Verify expired_at > NOW()

  

4. Check permission cache:

   - HGETALL admin:{user_id}:admin_permission

   - Verify method và path tồn tại

  

5. Check logs:

   - Laravel: storage/logs/laravel.log

   - Search for user_id/JWT errors

```

  

**Issue: Multi-tab không sync**

```

1. Check BroadcastChannel support:

   - Chrome/Firefox: OK

   - Safari iOS < 15.4: NOT supported → fallback localStorage events

  

2. Check origin:

   - http://localhost:3000 ≠ https://localhost:3000

   - Different origins → BroadcastChannel không hoạt động

  

3. Check console errors:

   - DevTools → Console

   - Search for "BroadcastChannel"

```

  

**Issue: Redis memory cao**

```

1. Count keys:

   redis-cli --scan --pattern "admin:*" | wc -l

  

2. Check TTL:

   redis-cli --scan --pattern "admin:*" | xargs -L1 redis-cli TTL

3. Verify auto-cleanup:

   - Đợi TTL expire

   - Hoặc manual: redis-cli FLUSHDB (DEV only!)

  

4. Review config:

   - maxmemory-policy = allkeys-lru

   - maxmemory = 2gb

```

  

---

  

**Tài liệu này phản ánh chính xác hệ thống tại thời điểm 2026-04-06.**