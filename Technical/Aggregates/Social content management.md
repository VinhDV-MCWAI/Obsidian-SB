Được. Với yêu cầu mới của bạn, tôi sẽ **không bắt đầu từ API Facebook/TikTok/YouTube ngay**. Ta sẽ xây một hệ thống theo kiểu:

> **Google Drive = kho media**  
> **Google Sheets = database + control panel**  
> **n8n = workflow engine**  
> **Gemini API = AI content generator**  
> **Bạn = bước duyệt nội dung**  
> **Publishing API/platform = lớp cuối, bật từng nền tảng một**

Đây là hướng an toàn và miễn phí nhất để bắt đầu. n8n self-host cũng được thiết kế theo hướng privacy-focused. ([n8n Documentation](https://docs.n8n.io/?utm_source=chatgpt.com "Explore n8n Docs: Your Resource for Workflow Automation and Integrations | n8n Docs"))

Gemini API hiện có Free Tier với một số model, không tính phí input/output trong hạn mức miễn phí; tuy nhiên quota được tính theo RPM/TPM/RPD và thay đổi theo model/project, nên ta phải thiết kế workflow có giới hạn và retry. ([Google AI for Developers](https://ai.google.dev/gemini-api/docs/rate-limits?utm_source=chatgpt.com "Rate limits  |  Gemini API  |  Google AI for Developers"))

---

# 1. Kiến trúc tôi đề xuất

Đừng làm một workflow khổng lồ.

Ta chia thành **5 workflow nhỏ**:

```text
                    GOOGLE DRIVE
                         │
                         │ media
                         ▼
                 ┌───────────────┐
                 │   n8n CORE    │
                 └───────┬───────┘
                         │
              đọc metadata từ Sheet
                         │
                         ▼
                    GEMINI AI
                         │
             tạo content từng platform
                         │
                         ▼
                  GOOGLE SHEETS
                         │
                   HUMAN REVIEW
                         │
              ┌──────────┴──────────┐
              │                     │
           APPROVED              REJECTED
              │                     │
              ▼                     ▼
       PUBLISH WORKFLOW          AI REVISE
              │
       ┌──────┼───────┐
       ▼      ▼       ▼
     FB     TikTok   YouTube
       │      │       │
       └──────┼───────┘
              ▼
          LOG RESULTS
              │
              ▼
        GOOGLE SHEETS
```

**Điểm quan trọng:** nếu Facebook/TikTok/YouTube thay đổi API, ta chỉ sửa workflow publishing tương ứng. Database và AI workflow không phải viết lại.

---

# 2. Google Sheet sẽ là "database"

Tôi đề xuất **một Google Spreadsheet duy nhất**, nhưng có nhiều sheet/tab.

Tạo file:

```text
SOCIAL_CONTENT_MANAGER
```

với **8 tabs**:

```text
1. CONTENT
2. PLATFORM_CONTENT
3. MEDIA
4. CATEGORIES
5. PLATFORMS
6. PROMPTS
7. PUBLISH_LOG
8. SETTINGS
```

Không nên nhét tất cả vào một bảng.

---

# 3. Tab `CONTENT`

Đây là bảng chính.

Tạo header chính xác như sau:

|Column|Ý nghĩa|
|---|---|
|content_id|ID duy nhất|
|created_at|thời gian tạo|
|updated_at|cập nhật cuối|
|category|danh mục|
|topic|chủ đề|
|source_text|nội dung nguồn|
|source_url|URL nguồn nếu có|
|media_folder_id|Google Drive folder|
|primary_media_url|media chính|
|thumbnail_url|thumbnail|
|language|ngôn ngữ|
|target_audience|đối tượng|
|campaign|campaign|
|ai_status|trạng thái AI|
|review_status|trạng thái duyệt|
|publish_status|trạng thái publish|
|priority|ưu tiên|
|notes|ghi chú|
|error_message|lỗi|
|created_by|nguồn tạo|

Ví dụ:

```text
CNT-000001
2026-09-15
2026-09-15
travel
5 places in Vietnam
...
Vietnam
tourists
VN-TRAVEL-01
READY
PENDING
NOT_PUBLISHED
NORMAL
```

---

# 4. Tab `PLATFORM_CONTENT`

Đây mới là phần cực kỳ quan trọng.

**Một content có nhiều phiên bản theo platform.**

Ví dụ:

```text
CNT-000001
Facebook
...
```

và:

```text
CNT-000001
TikTok
...
```

và:

```text
CNT-000001
YouTube
...
```

Header:

|Column|Ý nghĩa|
|---|---|
|content_id|liên kết CONTENT|
|platform|facebook/tiktok/youtube/pinterest/...|
|account_name|account/page/channel|
|enabled|YES/NO|
|title|title riêng|
|description|description riêng|
|caption|caption|
|hashtags|hashtags|
|tags|tags|
|category|category riêng nếu cần|
|board|Pinterest board|
|playlist|YouTube playlist|
|scheduled_at|thời gian đăng|
|status|DRAFT/APPROVED/READY/PUBLISHED/FAILED|
|platform_post_id|ID bài đăng|
|published_url|URL bài|
|published_at|thời gian đăng|
|error|lỗi|
|retry_count|số lần retry|

Ví dụ:

```text
CNT-000001 | facebook  | YES | ...
CNT-000001 | tiktok    | YES | ...
CNT-000001 | youtube   | YES | ...
CNT-000001 | pinterest | NO  | ...
```

Điều này cho phép bạn:

> **một nội dung → 4 phiên bản → duyệt từng phiên bản → đăng từng platform**

---

# 5. Tab `MEDIA`

Google Drive chứa file thật.

Sheet chỉ giữ metadata.

|Column|Nội dung|
|---|---|
|media_id|MEDIA-000001|
|content_id|CNT-000001|
|type|VIDEO/IMAGE/AUDIO/THUMBNAIL|
|filename|video.mp4|
|drive_file_id|Google Drive ID|
|drive_url|URL|
|mime_type|video/mp4|
|size|kích thước|
|width|width|
|height|height|
|duration|duration|
|status|READY/ERROR|
|checksum|tùy chọn|
|notes|ghi chú|

---

# 6. Cấu trúc Google Drive

Tạo:

```text
SOCIAL_CONTENT/
│
├── 00_INBOX/
│
├── 01_PROCESSING/
│
├── 02_READY/
│
├── 03_PUBLISHED/
│
├── 04_REJECTED/
│
├── 05_THUMBNAILS/
│
└── 99_ARCHIVE/
```

Sau này n8n có thể tự động:

```text
INBOX
 ↓
PROCESSING
 ↓
READY
 ↓
PUBLISHED
```

Như vậy bạn nhìn Drive cũng biết content đang ở đâu.

---

# 7. Tab `CATEGORIES`

Bạn tự định nghĩa.

Ví dụ:

|category_id|category_name|description|active|
|---|---|---|---|
|CAT001|Travel|Du lịch|YES|
|CAT002|Food|Ẩm thực|YES|
|CAT003|Technology|Công nghệ|YES|
|CAT004|Education|Giáo dục|YES|
|CAT005|Entertainment|Giải trí|YES|

Sau này AI chỉ được chọn category từ danh sách này.

**Không cho AI tự bịa category.**

---

# 8. Tab `PLATFORMS`

|platform|enabled|account_name|publish_method|max_length|notes|
|---|---|---|---|--:|---|
|facebook|YES|My Page|API|63206||
|instagram|NO|My IG|API|2200||
|tiktok|YES|My TikTok|API|...||
|youtube|YES|My Channel|API|...||
|pinterest|NO|My Pinterest|API|...||

Sau này:

```text
enabled = NO
```

thì n8n **không tạo/publish platform đó**.

Đây là cách rất hay để kiểm soát hệ thống.

---

# 9. Tab `SETTINGS`

Đây là nơi chứa cấu hình không nhạy cảm.

Ví dụ:

|key|value|
|---|---|
|default_language|vi|
|timezone|Asia/Ho_Chi_Minh|
|default_status|DRAFT|
|auto_publish|NO|
|max_retry|3|
|ai_model|YOUR_GEMINI_MODEL|
|approval_required|YES|
|default_hashtag_count|8|
|default_title_length|100|

**Không lưu API key trong Google Sheet.**

API key để trong **n8n Credentials**.

---

# 10. Tab `PROMPTS`

Đây là nơi tôi đặc biệt khuyên bạn làm.

Thay vì hard-code prompt trong workflow, hãy để prompt trong Sheet.

|prompt_id|platform|purpose|prompt|active|
|---|---|---|---|---|
|P001|global|content_generation|...|YES|
|P002|facebook|caption|...|YES|
|P003|tiktok|caption|...|YES|
|P004|youtube|metadata|...|YES|
|P005|pinterest|pin|...|YES|

Sau này bạn chỉ sửa prompt trong Sheet.

**Không cần sửa workflow n8n.**

---

# 11. Prompt Gemini tôi đề xuất

Đây là prompt core.

```text
You are a professional social media content strategist.

Your task is to transform the provided source content into platform-specific
social media content.

IMPORTANT RULES:

1. Do not invent facts that are not supported by the source.
2. Do not fabricate statistics, quotes, URLs, names, dates, or claims.
3. Keep the original meaning.
4. Adapt the style to each platform.
5. Do not use clickbait that makes unsupported claims.
6. Avoid excessive emojis.
7. Hashtags must be relevant to the actual topic.
8. Do not repeat the same text across all platforms.
9. Respect each platform's content structure.
10. Return valid JSON only.

SOURCE:

{{SOURCE_TEXT}}

CATEGORY:

{{CATEGORY}}

LANGUAGE:

{{LANGUAGE}}

TARGET AUDIENCE:

{{TARGET_AUDIENCE}}

PLATFORMS:

{{PLATFORMS}}

Generate content for each enabled platform.

Return this exact JSON structure:

{
  "global": {
    "content_summary": "",
    "keywords": [],
    "suggested_hashtags": []
  },
  "platforms": {
    "facebook": {
      "enabled": true,
      "title": "",
      "caption": "",
      "description": "",
      "hashtags": []
    },
    "tiktok": {
      "enabled": true,
      "title": "",
      "caption": "",
      "description": "",
      "hashtags": []
    },
    "youtube": {
      "enabled": true,
      "title": "",
      "description": "",
      "tags": [],
      "hashtags": []
    },
    "pinterest": {
      "enabled": true,
      "title": "",
      "description": "",
      "hashtags": []
    }
  }
}
```

Điểm quan trọng ở đây là:

**AI không quyết định đăng.**

AI chỉ:

```text
GENERATE
```

sau đó:

```text
REVIEW
```

rồi bạn:

```text
APPROVE
```

rồi mới:

```text
PUBLISH
```

---

# 12. Workflow #1 — `01_CONTENT_INTAKE`

Workflow đầu tiên cực kỳ đơn giản.

```text
Google Drive Trigger
        ↓
Get File Metadata
        ↓
Create Content ID
        ↓
Google Sheets → Append Row
        ↓
Update Drive Folder
```

Ví dụ bạn bỏ:

```text
5-places-vietnam.mp4
```

vào:

```text
00_INBOX
```

n8n phát hiện.

Tạo:

```text
CNT-000001
```

và ghi:

```text
CONTENT

content_id = CNT-000001
topic = 5 places vietnam
media_folder_id = ...
ai_status = READY
review_status = PENDING
publish_status = NOT_PUBLISHED
```

---

# 13. Workflow #2 — `02_AI_GENERATE`

Đây là workflow quan trọng nhất.

```text
Schedule Trigger
      ↓
Google Sheets
Get Rows
      ↓
Filter:
ai_status = READY
      ↓
Get Content
      ↓
Get Prompt
      ↓
Gemini
      ↓
Parse JSON
      ↓
Validate JSON
      ↓
Google Sheets
Append Platform Content
      ↓
Update CONTENT
```

Sau khi chạy:

### CONTENT

```text
CNT-000001
ai_status = GENERATED
review_status = PENDING
```

### PLATFORM_CONTENT

sẽ có:

```text
CNT-000001 | facebook
CNT-000001 | tiktok
CNT-000001 | youtube
CNT-000001 | pinterest
```

---

# 14. Workflow #3 — `03_REVIEW`

Đây là chỗ tôi muốn giữ **human-in-the-loop**.

Không tự động publish.

Bạn mở Google Sheet:

```text
PLATFORM_CONTENT
```

sửa:

```text
status = APPROVED
```

khi thấy nội dung ổn.

Ví dụ:

```text
CNT-000001 | TikTok | APPROVED
CNT-000001 | YouTube | APPROVED
CNT-000001 | Facebook | REJECTED
CNT-000001 | Pinterest | APPROVED
```

Điều này cực kỳ an toàn.

---

# 15. Workflow #4 — `04_PUBLISH`

n8n chạy định kỳ:

```text
Every 5 minutes
       ↓
Google Sheets
       ↓
Get rows
       ↓
status = APPROVED
       ↓
Switch(platform)
```

Sau đó:

```text
                  APPROVED
                      │
               ┌──────┼───────┐
               ▼      ▼       ▼
            Facebook TikTok YouTube
               │      │       │
               ▼      ▼       ▼
             API     API      API
```

Tôi **không muốn chúng ta viết các node API này ngay lập tức**.

Ta làm từng platform một.

---

# 16. Workflow #5 — `05_STATUS_SYNC`

Sau publish:

```text
API response
      ↓
platform_post_id
      ↓
published_url
      ↓
published_at
      ↓
status = PUBLISHED
```

Nếu lỗi:

```text
status = FAILED
error = API error
retry_count += 1
```

Ví dụ:

```text
FAILED
TikTok API:
"invalid video format"
```

Bạn sẽ thấy trực tiếp trong Sheet.

---

# 17. Tôi muốn thêm một bảng `ERROR_LOG`

Tạo tab:

```text
ERROR_LOG
```

Header:

|timestamp|workflow|content_id|platform|node|error_code|error_message|retry_count|resolved|
|---|---|---|---|---|---|---|--:|---|

Ví dụ:

```text
2026-09-15
04_PUBLISH
CNT-000001
tiktok
Upload Video
400
Invalid video duration
0
NO
```

Đây sẽ cực kỳ hữu ích khi bạn debug.

---

# 18. n8n Credentials

Bạn sẽ có khoảng:

```text
Google Drive OAuth2
Google Sheets OAuth2
Gemini API
Facebook OAuth2
TikTok OAuth2
YouTube OAuth2
Pinterest OAuth2
```

Nhưng **chưa cần tạo hết**.

Giai đoạn đầu chỉ:

```text
Google Drive
Google Sheets
Gemini
```

n8n có credential hỗ trợ Google/Gemini và các node Google Workspace; nếu một API không có node phù hợp thì n8n cũng cho phép dùng HTTP Request với credential/API tương ứng. ([n8n Documentation](https://docs.n8n.io/workflows/sharing/?utm_source=chatgpt.com "Sharing | n8n Docs"))

---

# 19. Gemini: lưu ý về "miễn phí"

Bạn có thể dùng Gemini Free Tier cho bước AI này.

Nhưng tôi sẽ thiết kế workflow để:

```text
1 content
     ↓
1 Gemini request
     ↓
4 platform outputs
```

**không phải:**

```text
1 content
 ↓
Gemini Facebook
 ↓
Gemini TikTok
 ↓
Gemini YouTube
 ↓
Gemini Pinterest
```

Như vậy tiết kiệm quota hơn.

Free tier hiện có quota theo RPM/TPM/RPD, và quota được áp dụng theo project chứ không đơn giản là "mỗi API key". ([Google AI for Developers](https://ai.google.dev/gemini-api/docs/rate-limits?utm_source=chatgpt.com "Rate limits  |  Gemini API  |  Google AI for Developers"))

Ngoài ra, Google hiện ghi rõ rằng dữ liệu ở Free Tier **có thể được sử dụng để cải thiện sản phẩm**, trong khi paid tier không có đặc điểm đó. ([Google AI for Developers](https://ai.google.dev/gemini-api/docs/pricing?hl=vi&utm_source=chatgpt.com "Giá của Gemini Developer API  |  Gemini API  |  Google AI for Developers"))

Vì bạn nhấn mạnh **an toàn**, tôi sẽ tránh đưa:

- password
    
- token
    
- private customer data
    
- confidential business documents
    

vào Gemini Free.

---

# 20. Một điểm bảo mật cực kỳ quan trọng

Không làm:

```text
Google Sheet
    ↓
API KEY
```

Không làm:

```text
n8n Set Node
    ↓
facebook_token = "EAAB..."
```

Không làm:

```text
Code Node
    ↓
const GEMINI_KEY = "AIza..."
```

Thay vào đó:

```text
n8n Credentials
       │
       ├── Google OAuth
       ├── Gemini API
       ├── Facebook
       └── ...
```

n8n có security audit để kiểm tra credentials, filesystem, risky nodes, webhooks và tình trạng instance; khi hệ thống bắt đầu có nhiều credential, nên chạy audit định kỳ. ([n8n Documentation](https://docs.n8n.io/hosting/securing/security-audit/?utm_source=chatgpt.com "Security audit | n8n Docs"))

---

# 21. Tôi cũng muốn thêm một "kill switch"

Trong `SETTINGS`:

```text
auto_publish = NO
```

Workflow publish phải kiểm tra:

```text
IF auto_publish != YES
        ↓
STOP
```

Như vậy nếu có sự cố:

```text
SETTINGS

auto_publish = NO
```

là **toàn bộ hệ thống ngừng publish**.

Không cần deactivate 5 workflow.

Đây là một chi tiết nhỏ nhưng rất đáng có.

---

# 22. Trạng thái tôi đề xuất

Đừng dùng quá nhiều status.

### Content

```text
INBOX
PROCESSING
READY
GENERATING
GENERATED
REVIEW
APPROVED
REJECTED
SCHEDULED
PUBLISHED
FAILED
ARCHIVED
```

### Platform

```text
DISABLED
DRAFT
REVIEW
APPROVED
QUEUED
PUBLISHING
PUBLISHED
FAILED
```

---

# 23. Quy trình sử dụng hàng ngày của bạn sẽ rất đơn giản

Cuối cùng bạn chỉ cần:

### Bước 1

Kéo video/ảnh vào:

```text
Google Drive
/
SOCIAL_CONTENT
/
00_INBOX
```

### Bước 2

n8n tự nhận.

### Bước 3

Gemini tạo:

```text
Facebook
TikTok
YouTube
Pinterest
```

### Bước 4

Bạn mở Google Sheet.

### Bước 5

Sửa nội dung nếu muốn.

### Bước 6

Đổi:

```text
REVIEW → APPROVED
```

### Bước 7

Đến giờ:

```text
n8n → platform API
```

### Bước 8

Sheet tự ghi:

```text
PUBLISHED
URL
POST ID
TIME
```

Đó là toàn bộ vòng đời.

---

# 24. Và tôi muốn thay đổi một điểm trong kế hoạch ban đầu

**Đừng tích hợp 5 platform cùng lúc.**

Ta làm:

### Phase 1

```text
Google Drive
+
Google Sheets
+
Gemini
+
n8n
```

→ hoàn thiện Content Management System.

### Phase 2

```text
YouTube
```

→ publish thử nghiệm.

### Phase 3

```text
Facebook
```

### Phase 4

```text
Pinterest
```

### Phase 5

```text
TikTok
```

Lý do: nếu ngay từ đầu làm 5 API, khi có lỗi bạn sẽ không biết lỗi nằm ở:

```text
AI
↓
Sheet
↓
Drive
↓
OAuth
↓
Media
↓
API
↓
Platform policy
```

Trong khi từng lớp được test độc lập thì rất dễ debug.

---

# 25. Về "flow có thể import vào n8n"

**Có — tôi có thể tạo cho bạn workflow JSON để import trực tiếp vào n8n**, chứ không chỉ mô tả sơ đồ.

Nhưng tôi muốn chia thành các file/workflow nhỏ:

```text
01_CONTENT_INTAKE.json
02_AI_GENERATE.json
03_REVIEW_SYNC.json
04_PUBLISH.json
05_STATUS_SYNC.json
```

và một bộ:

```text
SOCIAL_CONTENT_MANAGER.xlsx
```

chứa sẵn:

```text
CONTENT
PLATFORM_CONTENT
MEDIA
CATEGORIES
PLATFORMS
PROMPTS
PUBLISH_LOG
ERROR_LOG
SETTINGS
```

Bạn chỉ việc:

```text
Import workflow
      ↓
Create Google Sheet
      ↓
Connect Google OAuth
      ↓
Connect Gemini
      ↓
Điền Spreadsheet ID
      ↓
Điền Drive Folder ID
      ↓
Test
```

**Tôi đề xuất bước kế tiếp là tôi tạo luôn bộ `Google Sheet template + workflow 01/02` trước**, tức là phần **Drive → n8n → Gemini → Google Sheets → Review**, chưa đụng API publish. Khi hai workflow này chạy ổn, chúng ta mới cắm YouTube/Facebook/TikTok/Pinterest từng cái một. Cách này vừa miễn phí, vừa hạn chế rủi ro token/API, và bạn có thể kiểm tra toàn bộ "bộ não" của hệ thống trước khi cho nó quyền đăng bài.