> Bản nháp đầu tiên (2026-10-26), viết khi còn hiểu yêu cầu là "sinh code dùng chung giữa back-end và front-end". Bạn đã đổi hướng sang bộ quy chuẩn độc lập công nghệ, xem [[Ý tưởng, mong đợi và đắn đo]]. Giữ lại vì phần phân tích giao dịch, lỗi, khung phản hồi và chi phí / lợi ích vẫn dùng được.

# Bộ công cụ dùng chung giữa front-end và back-end

Ngày viết: 2026-10-26 · Phạm vi: kiến thức tham khảo, dùng cho Second Memory và các dự án sau. Tài liệu này **không** yêu cầu triển khai gì.

---

## 1. Trả lời nhanh

| Câu hỏi | Trả lời ngắn |
|---|---|
| Có nên gom cấu hình dùng chung (hằng số, enum, thông báo lỗi, ngôn ngữ, giới hạn validate) về **một nguồn** rồi sinh code cho từng bên? | **Có**, với những thứ là *dữ liệu thuần*: enum, con số giới hạn, mã lỗi + câu thông báo, khung phản hồi API, kiểu dữ liệu của request/response. |
| Có nên định nghĩa *toàn bộ luật validate* bằng JSON rồi sinh ra FormRequest và zod? | **Chỉ phần đơn giản** (bắt buộc, kiểu, dài tối thiểu/tối đa, định dạng email, nằm trong danh sách). Luật so sánh nhiều trường, luật cần gọi DB / mạng thì **viết tay** ở bên cần nó. Cố nhét hết vào JSON là tự xây một ngôn ngữ lập trình nhỏ — tốn công hơn lợi. |
| Nguồn chung nên là file JSON riêng hay lấy từ code back-end? | Với dự án có **một** back-end (như Second Memory): **lấy back-end làm nguồn** — back-end sinh ra file mô tả API (OpenAPI), front-end sinh code từ đó. Dự án này đã đi được nửa đường. File JSON trung lập chỉ đáng khi có nhiều back-end khác ngôn ngữ, hoặc app di động dùng chung. |
| Có tải thẳng file JSON khi chạy không? | **Không.** Đúng như bạn nghĩ: lúc build sinh ra code thật (PHP enum, TypeScript enum, schema zod), có kiểm tra trong CI rằng code sinh ra không cũ. Chỉ phần được phép mới được sinh sang front-end (không lộ giới hạn nội bộ, khoá bí mật). |
| Lỗi, giao dịch, khung phản hồi có nên làm chung? | **Có, và đáng làm sớm nhất** — vì nó áp dụng cho *mọi* API. Second Memory đã có cả ba (chi tiết ở mục 5, 6). |
| Tự dựng bộ chung hay để AI viết theo prompt từng lần? | Dựng chung cho thứ **lặp lại ở mọi màn hình / mọi API và sai thì khó thấy** (giao dịch, lỗi, khung phản hồi, enum). Còn lại để prompt + kiểm tra tay rẻ hơn. Chi tiết mục 9. |

---

## 2. Hiện trạng trong Second Memory

### Đã dùng chung (tốt)

| Thứ | Nằm ở đâu | Cách hoạt động |
|---|---|---|
| Khung phản hồi `{ data, error: { status, code, messages } }` | `laravel-api/app/Traits/ApiResponse.php`, middleware `api.response` (`GenerateResponseMiddleware`) | Controller trả gì cũng được bọc vào khung chung; lỗi do bộ xử lý ngoại lệ trong `bootstrap/app.php` bọc. |
| Mô tả API → kiểu TypeScript | `make openapi`: Scramble đọc code Laravel → `openapi.json` → `pnpm gen:api` sinh `openapi.d.ts` cho cả hai front-end | `make openapi-check` báo lỗi nếu file sinh ra bị cũ. **Đây chính là mô hình "một nguồn, sinh code, không tải JSON lúc chạy".** |
| Giao dịch DB tự động | middleware `db.transaction` (`TransactionMiddleware`) | GET/HEAD không mở giao dịch; POST/PUT/DELETE mở, phản hồi ≥ 400 hoặc có ngoại lệ thì rollback, còn lại commit. |
| Bản đồ ngoại lệ → mã HTTP | `bootstrap/app.php` | Lỗi validate → 422 kèm lỗi từng trường; chưa đăng nhập → 401; không có quyền → 403; không tìm thấy → 404; quá giới hạn → 429; còn lại → 500 và giấu chi tiết. |

### Đang chép tay giữa hai bên (chỗ có thể lệch)

| Thứ | Back-end | Front-end | Rủi ro thấy được |
|---|---|---|---|
| Enum (trạng thái, giới tính, vai trò…) | `app/Enums/*.php` | `nextjs-fe/src/shared/enums/enums.ts` — ghi chú "Auto-synced" nhưng thực ra chép tay | Thêm một giá trị ở back-end, quên front-end → màn hình hiện trống. |
| Con số giới hạn | `CommonVal`, `EndUserConst`, và cả số viết thẳng `'max:30'` trong `StoreAdminMstRequest` | `validation-rules.ts`: `EMAIL_MAX: 30`, `PASSWORD_MIN: 6`… kèm ghi chú "khớp với back-end" | Hai nơi cùng tên "giới hạn email": `MAX_EMAIL = 254` và `EMAIL_MAX = 30`. Đổi một chỗ, chỗ kia không biết. |
| Câu thông báo lỗi | `app/Constants/Messages.php` (mã `E0001`…) + `lang/` | `nextjs-fe/messages/en.json` | Cùng một lỗi, hai câu chữ khác nhau. |
| Kiểu lỗi trả về | khung `{ data, error }` | `ApiErrorResponse` trong `shared/types/api.ts` mô tả một khung khác (`success`, `message`, `status_code`) và không nơi nào dùng | Kiểu thừa, dễ làm người sau hiểu sai. |

### Phát hiện quan trọng

`openapi.json` **đã chứa sẵn phần luật đơn giản**. Ví dụ, mục tạo quản trị viên (`StoreAdminMstRequest`) đã có: trường bắt buộc, `email` dạng email, `maxLength: 30`, `user_name` tối đa 50, `gender` / `status` là enum. Nghĩa là phần "validate dùng chung" bạn muốn **có thể sinh tự động từ thứ đang có**, không cần thêm file JSON thứ ba.

Chỗ còn thiếu: một số luật Scramble không đọc được (ví dụ độ dài tối thiểu của mật khẩu ra `minLength: 0`) — phải bổ sung hoặc chấp nhận viết tay ở front-end.

---

## 3. Nguồn cấu hình chung và sinh code

### Ba cách tổ chức

| Cách | Nguồn là gì | Hợp khi | Lợi | Hại |
|---|---|---|---|---|
| **A. Back-end làm nguồn** (code-first) | Code Laravel (Enum, FormRequest) → OpenAPI → front-end sinh code | Một back-end, nhóm nhỏ | Không thêm file nào; back-end là nơi luật thật sự chạy nên không bao giờ "nguồn nói một đằng, server làm một nẻo" | Front-end phải chờ back-end viết xong mới có kiểu; phụ thuộc khả năng đọc code của công cụ (Scramble) |
| **B. File đặc tả làm nguồn** (contract-first) | File OpenAPI / JSON Schema viết tay → sinh code cho cả hai bên | Nhiều nhóm, nhiều back-end, hoặc thiết kế API trước khi code | Hai bên làm song song; trung lập ngôn ngữ | Phải giữ file đặc tả và code back-end khớp nhau — một nguồn lệch mới |
| **C. Gói JSON chung tự định nghĩa** | `shared/config/*.json` (enum, hằng số, thông báo) → script sinh PHP + TS | Có thứ không thuộc API: hằng số giao diện, chuỗi ngôn ngữ, app di động | Kiểm soát hoàn toàn định dạng | Tự viết và bảo trì script sinh code; cần quy ước rõ |

**Khuyến nghị:** A làm xương sống, thêm C *chỉ* cho phần không đi qua API (ví dụ chuỗi ngôn ngữ dùng chung). Không chọn B cho dự án một người.

### Cách hoạt động của "sinh code lúc build"

```
nguồn (code back-end hoặc JSON chung)
   │  make gen  (chạy trong container, lặp lại bao nhiêu lần cũng ra cùng kết quả)
   ├─► back-end:  app/Enums/Generated/*.php, lang/*.php
   └─► front-end: src/shared/generated/enums.ts, messages.ts, schemas.ts (zod)
   │
CI: chạy lại make gen → git diff phải rỗng, nếu không là có người sửa nguồn mà quên sinh lại
```

Quy tắc:

1. File sinh ra có dòng đầu "ĐỪNG SỬA TAY — sinh từ …". Được commit vào git (để đọc code, review dễ) hoặc không commit (gọn hơn) — chọn một và giữ nhất quán. Dự án này đang commit.
2. Mỗi mục trong nguồn có **phạm vi xuất**: `both`, `backend`, `frontend`. Script chỉ sinh sang bên được phép → front-end không bao giờ nhìn thấy hằng số nội bộ.
3. Không tải JSON lúc chạy: tránh lộ dữ liệu, có kiểm tra kiểu, IDE gợi ý được.

### Ví dụ định dạng nguồn (cách C)

```json
{
  "enums": {
    "AdminStatus": {
      "export": "both",
      "values": { "INACTIVE": 0, "ACTIVE": 1, "WAITING": 2, "SUSPENDED": 3 },
      "labels": "enum.admin_status"
    }
  },
  "limits": {
    "ADMIN_EMAIL_MAX": { "export": "both", "value": 30 },
    "LOGIN_LOCK_MINUTES": { "export": "backend", "value": 15 }
  }
}
```

Sinh ra PHP `enum AdminStatus: int { case INACTIVE = 0; … }` và TS `export const AdminStatus = { INACTIVE: 0, … } as const`. `LOGIN_LOCK_MINUTES` chỉ có ở PHP.

### Thông báo và ngôn ngữ

- Dùng **mã thông báo** làm khoá chung (`validation.required`, `admin.email_taken`), không dùng câu chữ.
- Back-end trả **mã + tham số** trong lỗi (`{ code: "validation.max", params: { max: 30 } }`), front-end tự dịch theo ngôn ngữ người dùng. Khi đó cả hai bên chỉ cần chung *danh sách mã*, mỗi bên tự giữ câu chữ hoặc dùng chung một file ngôn ngữ.
- Lợi: đổi câu chữ một chỗ; front-end hiện được thông báo cùng ngôn ngữ dù lỗi đến từ server.
- Hại: đổi khung lỗi là đổi hợp đồng API → mọi màn hình đọc lỗi phải sửa theo.

---

## 4. Validate dùng chung: phần chung và phần riêng

Lấy đúng ví dụ của bạn: **tạo quản trị viên**.

| Luật | Chung / Riêng | Ai kiểm tra | Lý do |
|---|---|---|---|
| `name`, `email`, `password` bắt buộc | Chung | Cả hai | Đơn giản, không cần dữ liệu ngoài |
| `email` đúng cú pháp, tối đa 30 ký tự | Chung | Cả hai | Như trên |
| Mật khẩu 6–100 ký tự | Chung | Cả hai | Như trên |
| `status` nằm trong `AdminStatus` | Chung | Cả hai | Sinh từ enum |
| Nhập lại mật khẩu khớp | Chung *nhưng viết tay* | Cả hai | So sánh hai trường — đưa vào JSON thì phải tự chế cú pháp |
| Email chưa có ai dùng (`unique`) | Riêng back-end | Back-end | Cần DB. Front-end có thể gọi API kiểm tra trước nhưng **back-end vẫn là cổng cuối** |
| Tên miền email có máy chủ thư (`EmailDomainExists`) | Riêng back-end | Back-end | Cần mạng (DNS). Đã có trong `app/Rules/EmailDomainExists.php` |
| Ẩn/hiện trường theo vai trò | Riêng front-end | Front-end | Chỉ là giao diện |

### Cách sinh

- **Back-end:** phần chung có thể là một mảng luật dùng lại: `AdminRules::base()` trả `['email' => ['required','email:rfc','max:'.AdminConst::EMAIL_MAX]]`, rồi `StoreAdminMstRequest` ghép thêm `unique`, `EmailDomainExists`. Không cần sinh code cho phía back-end nếu back-end đã là nguồn.
- **Front-end:** sinh schema zod từ `openapi.json` (các công cụ có sẵn: `orval`, `openapi-zod-client`, `@hey-api/openapi-ts` có plugin zod). Rồi mở rộng:

```ts
import { StoreAdminMstRequestSchema } from '@/shared/generated/schemas'; // sinh ra
export const createAdminForm = StoreAdminMstRequestSchema
  .extend({ password_confirmation: z.string() })
  .refine(v => v.password === v.password_confirmation, { path: ['password_confirmation'], message: t('validation.confirmed') });
```

### Đánh giá

| | |
|---|---|
| Lợi | Một chỗ đổi giới hạn; không còn cảnh "front-end cho 50 ký tự, back-end chặn ở 30"; form mới làm nhanh hơn |
| Hại | Thêm một bước build và một thư viện sinh code (cần owner đồng ý); thông báo lỗi sinh tự động thường kém tự nhiên — vẫn phải gắn mã thông báo; luật phức tạp vẫn viết tay ở hai nơi |
| Bẫy | Đừng để front-end thành nơi *duy nhất* kiểm tra một luật. Front-end chỉ để người dùng sửa nhanh; back-end luôn kiểm tra lại toàn bộ |

---

## 5. Lỗi và giao dịch

### Nguyên tắc

1. **Giao dịch bọc cả request ghi, không bọc request đọc.** GET không mở giao dịch (Second Memory dùng `isMethodSafe()`).
2. **Ném ngoại lệ là cách duy nhất để báo thất bại** trong service. Không trả `false` / `null` để báo lỗi — ngoại lệ đi xuyên mọi tầng tới middleware, middleware rollback, bộ xử lý lỗi chuyển thành JSON.
3. **Phân loại ngoại lệ** để biết: mã HTTP nào, có rollback không, có ghi log không, có lộ thông báo cho người dùng không.

### Bảng phân loại đề xuất

| Loại lỗi | Ví dụ | HTTP | Giao dịch | Ghi log | Thông báo cho người dùng |
|---|---|---|---|---|---|
| Dữ liệu nhập sai | `ValidationException` | 422 | Rollback (thực ra chưa ghi gì) | Không | Lỗi từng trường, theo khung validate |
| Vi phạm quy tắc nghiệp vụ | "Không xoá được danh mục còn bài viết" | 409 hoặc 422 | Rollback | Không / mức info | Câu thông báo theo mã |
| Không tìm thấy | `ModelNotFoundException` | 404 | Rollback | Không | Câu chung, **không lộ tên bảng/model** |
| Chưa đăng nhập / không có quyền | `AuthenticationException`, `AuthorizationException` | 401 / 403 | Rollback | Có (403 đáng theo dõi) | Câu chung |
| Quá giới hạn | `ThrottleRequestsException` | 429 | — | Không | Kèm thời gian chờ |
| Dịch vụ ngoài lỗi | MinIO, mail, DNS | 502 / 503 | Rollback, **và dọn phần đã ghi ra ngoài** | Có | "Thử lại sau" |
| Lỗi hệ thống | Mọi thứ còn lại | 500 | Rollback | Có, đầy đủ | Câu chung, giấu chi tiết |

Gợi ý cách làm: một lớp gốc `DomainException` có `httpStatus()`, `messageCode()`, `shouldReport()`. Mỗi lỗi nghiệp vụ là một lớp con nhỏ. Bộ xử lý lỗi chỉ cần một nhánh cho cả họ này thay vì một chuỗi `match` dài.

> Nhận xét về code hiện tại: lỗi nghiệp vụ đang ném `LogicException` và mặc định ra **500**. Mã 500 nghĩa là "server hỏng", nên giám sát sẽ báo động nhầm và front-end khó phân biệt với lỗi thật. Đây là chỗ đáng cân nhắc nâng cấp (một việc riêng, cần đề xuất trước).

### Những trường hợp ngoại lệ phải nhớ

| Tình huống | Vấn đề | Cách xử lý |
|---|---|---|
| Ghi nhật ký kiểm tra (audit) khi đăng nhập sai | Lỗi → rollback → mất luôn dòng nhật ký | Không gắn giao dịch cho route đó (Second Memory đã làm, có ghi chú trong `routes/api.php`) hoặc ghi nhật ký qua kết nối DB riêng |
| Gửi mail / đẩy job trong giao dịch | Job chạy trước khi commit, hoặc mail đã gửi mà DB rollback | Dùng `afterCommit` (Laravel có sẵn `DB::afterCommit`, job `afterCommit()`) |
| Tải file lên MinIO rồi DB lỗi | File mồ côi trên MinIO | Ghi DB trước, file sau; hoặc dọn file trong `catch`; hoặc có job dọn định kỳ |
| Request dài (nhập hàng loạt) | Giữ khoá DB lâu | Chia lô, mỗi lô một giao dịch, chạy nền |
| Gọi lồng nhau | Service A gọi service B, cả hai `DB::transaction` | Laravel tự dùng savepoint; vẫn nên để middleware giữ giao dịch ngoài cùng, service không tự mở nếu không cần |

---

## 6. Khung phản hồi API và kiểu TypeScript dùng chung

### Khung hiện tại

```json
{ "data": <kết quả hoặc null>, "error": { "status": false, "code": 200, "messages": null } }
```

Lỗi validate: `"messages": { "email": ["..."] }`; lỗi khác: `"messages": "..."`.

### Đánh giá

| Lợi | Hại / điểm có thể cải thiện |
|---|---|
| Mọi API cùng hình dạng → một hàm `apiClient` xử lý chung lỗi, loading, thông báo | `code` lặp lại mã HTTP — thừa |
| Đã mô tả trong OpenAPI (`ResponseEnvelope.php`) → kiểu TS sinh tự động | `messages` lúc là chuỗi, lúc là object → front-end phải kiểm tra kiểu mỗi lần |
| Danh sách phân trang có `links`, `meta` chuẩn | Không có **mã lỗi ổn định** dạng chữ (`ADMIN_EMAIL_TAKEN`) → front-end khó rẽ nhánh theo loại lỗi |

### Khung gợi ý cho dự án mới

```json
{
  "data": null,
  "error": {
    "code": "validation_failed",
    "message": "Dữ liệu không hợp lệ",
    "fields": { "email": [{ "code": "validation.max", "params": { "max": 30 } }] },
    "trace_id": "01JC..."
  },
  "meta": { "page": 1, "per_page": 20, "total": 134 }
}
```

- `error` là `null` khi thành công → kiểm tra `if (res.error)` là đủ.
- `code` dạng chữ, có trong danh sách sinh chung → front-end dùng `switch`.
- `trace_id` khớp với log server → báo lỗi nhanh.
- Chuẩn tham khảo có sẵn: RFC 9457 "Problem Details" — nếu muốn theo chuẩn công nghiệp thay vì tự đặt.

Phía TypeScript chỉ dùng **kiểu sinh ra từ OpenAPI**, không khai báo tay (`ApiErrorResponse` viết tay trong `shared/types/api.ts` là ví dụ kiểu nên bỏ).

---

## 7. Danh mục các thành phần dùng chung khác

Ký hiệu phạm vi: **Toàn hệ thống** = mọi API / mọi màn hình; **Cụm** = một nhóm tính năng; **Hai bên** = cần đồng bộ back-end và front-end.

### 7.1 Back-end

| # | Thành phần | Phạm vi | Cách hoạt động | Lợi | Hại / bẫy | Second Memory |
|---|---|---|---|---|---|---|
| 1 | Middleware giao dịch | Toàn hệ thống | Xem mục 5 | Không bao giờ quên rollback | Bẫy với audit, mail, file ngoài | Có |
| 2 | Middleware bọc phản hồi | Toàn hệ thống | Xem mục 6 | Khung thống nhất | Xử lý cookie/header phải cẩn thận | Có |
| 3 | Bộ xử lý ngoại lệ tập trung | Toàn hệ thống | Bảng ngoại lệ → HTTP | Không lộ SQL, đường dẫn file | Chuỗi `match` dài dần nếu không có lớp gốc | Có |
| 4 | Mã định danh request (request id / trace id) | Toàn hệ thống | Middleware sinh id, gắn vào log và header phản hồi | Tìm lỗi người dùng báo trong vài giây | Gần như không | Chưa |
| 5 | Log có cấu trúc (JSON) kèm người dùng, id request | Toàn hệ thống | Context log thêm tự động | Lọc log dễ | Không ghi mật khẩu, token | Một phần |
| 6 | Request danh sách chung (phân trang, sắp xếp, lọc ngày) | Toàn hệ thống | `ListRequest` cha, các request con kế thừa | Mọi bảng cùng tham số | Lọc đặc thù vẫn viết riêng | Có (`ListRequest.php`) |
| 7 | Repository / service cơ sở: CRUD, xoá mềm, trạng thái | Cụm | Trait `HasSoftDelete`, `HasStatus` | Ít code lặp | Lớp cha quá to thì khó đọc | Có |
| 8 | Phân quyền theo tên quyền | Toàn hệ thống | spatie permission + middleware | Đổi quyền không cần sửa code | Danh sách tên quyền phải đồng bộ front-end | Có |
| 9 | Giới hạn tần suất theo nhóm route | Cụm | `throttle:user`, `throttle:login` | Chống dò mật khẩu, spam | Đặt sai làm khó người dùng thật | Có |
| 10 | Nhật ký kiểm tra (audit) | Cụm | Sự kiện model → bảng audit | Biết ai đổi gì | Bảng to nhanh, cần dọn | Có |
| 11 | Khoá chống ghi đè (optimistic lock: cột `version`/`updated_at`) | Cụm | Gửi kèm phiên bản khi sửa, khác thì 409 | Hai người sửa cùng lúc không mất dữ liệu | Thêm trường ở form | Chưa |
| 12 | Chống gửi trùng (idempotency key) | Cụm (thanh toán, tải lên) | Header khoá; server nhớ kết quả | Bấm hai lần không tạo hai bản ghi | Cần chỗ lưu khoá (Redis) | Một phần ở tải file |
| 13 | Job nền + theo dõi tiến độ | Cụm | `BackgroundJob`, `BackgroundTaskTracker`, sự kiện realtime | Việc lâu không treo request | Thêm hàng đợi phải giám sát | Có |
| 14 | Cấu hình bật/tắt tính năng (feature flag) | Toàn hệ thống | Bảng hoặc config; middleware chặn | Ra mắt dần, tắt nhanh khi lỗi | Cờ cũ không dọn → rối | Một phần (`app.email_dns_check`) |
| 15 | Định dạng ngày giờ, tiền, múi giờ | Hai bên | Server lưu UTC, trả ISO 8601; front-end hiển thị theo vùng | Hết lỗi lệch giờ | Phải thống nhất từ đầu | Đang dùng `d/m/Y` ở tham số — nên ghi rõ quy ước |
| 16 | Kiểm tra sức khoẻ (`/up`) | Toàn hệ thống | Route trả 200 nếu DB/Redis sống | Docker, giám sát dùng được | — | Có |

### 7.2 Front-end

| # | Thành phần | Phạm vi | Cách hoạt động | Lợi | Hại / bẫy | Second Memory |
|---|---|---|---|---|---|---|
| 1 | API client chung | Toàn hệ thống | Một hàm gọi: gắn cookie/CSRF, đọc khung `{data,error}`, ném lỗi có kiểu | Mọi màn hình xử lý lỗi giống nhau | — | Có (`shared/api/client`) |
| 2 | Kiểu sinh từ OpenAPI | Hai bên | `pnpm gen:api` | Hết khai báo tay | Phải chạy lại khi API đổi | Có |
| 3 | Khoá truy vấn (query keys) tập trung | Toàn hệ thống | `query-keys.ts` | Làm mới dữ liệu đúng chỗ sau khi sửa | — | Có |
| 4 | Hook CRUD / danh sách | Cụm | `use-crud`, `use-api-data` | Màn hình bảng mới làm rất nhanh | Hook quá nhiều tuỳ chọn thì khó dùng | Có |
| 5 | Gắn lỗi server vào ô nhập | Toàn hệ thống | Đọc `error.messages` dạng object → `setError` của react-hook-form | Lỗi `unique` hiện đúng ô | Phụ thuộc khung lỗi ổn định | Nên kiểm tra đã có chưa |
| 6 | Schema zod sinh + mở rộng | Hai bên | Mục 4 | Hết chép giới hạn | Thêm công cụ sinh | Chưa (đang chép tay) |
| 7 | Enum + nhãn hiển thị | Hai bên | Sinh enum, nhãn lấy theo mã ngôn ngữ | Thêm giá trị mới không quên | — | Chép tay |
| 8 | Kiểm tra quyền ở giao diện | Hai bên | `use-permissions` đọc danh sách quyền | Ẩn nút không được dùng | **Chỉ là giao diện**, back-end vẫn phải chặn | Có |
| 9 | Chống bấm hai lần | Toàn hệ thống | `use-action-lock` | Tránh gửi trùng | — | Có |
| 10 | Loading / thông báo toàn cục | Toàn hệ thống | Provider + toast | Trải nghiệm thống nhất | — | Có |
| 11 | Biến môi trường có kiểm tra | Toàn hệ thống | Schema zod cho `process.env` lúc khởi động | Thiếu biến là biết ngay | — | Một phần (`env.d.ts` chỉ là kiểu) |

### 7.3 Ngoài code (quy trình và hạ tầng)

| Thành phần | Tác dụng | Second Memory |
|---|---|---|
| Lệnh chung (`Makefile`) | Một cách chạy cho mọi người, mọi máy | Có |
| Cổng kiểm tra (`make verify`, `make ci`) có bước "code sinh ra không cũ" | Bắt lệch hợp đồng API | Có (`openapi-check`) |
| Mẫu tạo tính năng mới (scaffold) | Sinh sẵn controller, request, service, test, trang front-end | Chưa — xem mục 9 trước khi làm |
| ADR (ghi lại quyết định kiến trúc) | Người sau biết vì sao | Có (`docs/adr/`) |
| Dữ liệu mẫu + quy ước tiền tố `TMP ` / `E2E ` | Dọn dễ | Có |

---

## 8. Đánh dấu phạm vi

Đặt phạm vi bằng **vị trí thư mục** là cách rẻ và rõ nhất, không cần công cụ:

| Phạm vi | Back-end (Laravel) | Front-end (Next.js) | Quy tắc |
|---|---|---|---|
| Toàn hệ thống | `app/Http/Middleware`, `app/Support`, `app/Traits`, `bootstrap/app.php` | `src/shared/*` | Không được import code của tính năng cụ thể |
| Cụm tính năng | `app/Services/Media/*`, `app/Http/Requests/Media/*` | `src/features/media/*` | Chỉ import từ `shared` hoặc chính cụm |
| Một tính năng | file riêng trong cụm | component riêng | — |
| Sinh tự động | `.../Generated/*` | `src/shared/generated/*`, `types/openapi.d.ts` | Không sửa tay |

Có thể ép quy tắc import bằng ESLint (`no-restricted-imports` / `eslint-plugin-boundaries`) và PHP (`deptrac`). Đáng làm khi nhóm > 2 người; một người thì quy ước + review là đủ.

Middleware gắn theo phạm vi: toàn hệ thống trong `bootstrap/app.php`, cụm trong nhóm route (`Route::middleware([...])->group(...)`), một tính năng gắn trên route đó. Second Memory đang làm đúng kiểu này.

---

## 9. Chi phí và lợi ích: tự dựng bộ chung hay prompt thông thường

Câu hỏi đúng không phải "có hay không" mà "**thứ này lặp lại bao nhiêu lần và sai thì ai phát hiện**".

| Thứ | Công dựng (ước lượng) | Lặp lại | Sai thì phát hiện khi nào | Nên |
|---|---|---|---|---|
| Middleware giao dịch, bộ xử lý lỗi, khung phản hồi | 1–2 ngày | Mọi API | Rất muộn (dữ liệu hỏng âm thầm) | **Dựng chung**, ngay từ đầu |
| Kiểu TS từ OpenAPI | Nửa ngày (công cụ có sẵn) | Mọi màn hình | Lúc chạy, ở người dùng | **Dựng chung** |
| Enum + giới hạn sinh sang front-end | 1 ngày | Mọi form có enum | Khi người dùng thấy nhãn trống | Dựng chung khi enum > ~10 |
| Schema zod sinh từ OpenAPI | 1–2 ngày + chỉnh | Mọi form | Khi người dùng gặp lỗi lạ | Đáng làm khi > ~10 form |
| Mã thông báo dùng chung hai bên | 1–3 ngày (đổi khung lỗi) | Mọi lỗi | Câu chữ lệch — ít hại | Làm khi cần đa ngôn ngữ thật |
| Ngôn ngữ JSON tự chế cho mọi luật validate | 1–2 tuần, bảo trì mãi | — | — | **Không nên** |
| Bộ sinh khung tính năng (scaffold) | 3–5 ngày | Mỗi tính năng mới | — | Với AI: một **prompt mẫu + một tính năng mẫu để chép** thường rẻ hơn |
| Luật nghiệp vụ phức tạp, nhiều bảng | — | Ít | — | **Prompt + kiểm tra tay + test** |

### Khi làm cùng AI

- AI viết code lặp lại rất nhanh, nên **lợi ích "đỡ gõ" của bộ chung giảm đi**. Nhưng lợi ích "**đúng ở mọi nơi**" thì tăng: AI viết 20 form theo prompt sẽ có 20 cách hơi khác nhau, mỗi lần một giới hạn hơi khác. Bộ chung + kiểm tra tự động là thứ giữ AI không lệch.
- Cách rẻ nhất để AI tuân theo: (1) code chung tồn tại và có tên rõ, (2) một dòng trong `CLAUDE.md` / `.claude/rules` nói "form mới dùng schema sinh ra, không tự viết giới hạn", (3) bước CI bắt lỗi nếu quên. Ba thứ này rẻ hơn rất nhiều so với một framework.
- Phần phức tạp liên quan nhiều thứ: để AI viết theo prompt, rồi **bạn kiểm tra tay + viết test**, như bạn đề xuất — đúng. Đừng trừu tượng hoá thứ chỉ xuất hiện một lần (đúng quy tắc "dùng ít nhất hai lần mới tách" của dự án).

### Kết luận

Thứ tự đáng đầu tư: **giao dịch + lỗi + khung phản hồi → kiểu TS sinh tự động → enum/giới hạn sinh tự động → schema zod sinh tự động → mã thông báo chung**. Dừng ở chỗ công dựng bắt đầu lớn hơn số lần dùng lại. Ba bước đầu Second Memory đã có; bước 3–4 là việc có thể đề xuất nếu muốn (thêm công cụ sinh code là thêm thư viện → cần đồng ý).

---

## 10. Danh sách kiểm tra khi bắt đầu dự án mới

- [ ] Chọn nguồn hợp đồng API: back-end sinh OpenAPI (một back-end) hoặc file đặc tả viết trước (nhiều nhóm).
- [ ] Khung phản hồi: thành công / lỗi / phân trang; mã lỗi dạng chữ; `trace_id`.
- [ ] Lớp ngoại lệ gốc + bảng phân loại (HTTP, rollback, log, thông báo).
- [ ] Middleware giao dịch cho request ghi; quy ước `afterCommit` cho mail, job, file ngoài; ngoại lệ cho audit.
- [ ] Middleware id request + log có cấu trúc.
- [ ] Script sinh code (kiểu TS, enum, zod) chạy được lặp lại; bước CI "code sinh ra không cũ".
- [ ] Quy ước phạm vi thư mục `shared` / `features`; file sinh ra nằm riêng, có dòng "đừng sửa tay".
- [ ] Quy ước ngày giờ (UTC, ISO 8601), tiền, múi giờ.
- [ ] Mã thông báo + file ngôn ngữ; quyết định ai dịch (server hay client).
- [ ] Request danh sách chung (phân trang, sắp xếp, lọc).
- [ ] API client chung front-end; gắn lỗi server vào ô nhập.
- [ ] Biến môi trường có kiểm tra lúc khởi động.
- [ ] Ghi các quy tắc trên vào `CLAUDE.md` / `.claude/rules` để AI làm theo.
