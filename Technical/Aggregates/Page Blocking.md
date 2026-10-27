Có thể phân tích vấn đề này ở góc độ **mạng máy tính và cơ chế censorship**, nhưng tôi sẽ không đưa quy trình thực hành để vượt chặn một website cụ thể. Phần kỹ thuật bên dưới đủ để bạn hiểu **ISP chặn ở đâu, vì sao một số cách bypass có tác dụng, và vì sao cần nhiều tầng fallback**.

### 1. Trước hết: "website bị chặn" không nhất thiết có nghĩa website tự chặn Việt Nam

Có ít nhất hai trường hợp hoàn toàn khác nhau:

```text
                    ┌── Website tự chặn IP VN
Browser ─ Internet ─┤
                    └── ISP tại VN chặn
```

Với ISP blocking, website có thể hoàn toàn hoạt động bình thường đối với người dùng ở quốc gia khác.

Các nghiên cứu đo lường Internet tại Việt Nam cho thấy việc chặn đã được quan sát ở các ISP lớn và không chỉ dùng một kỹ thuật: DNS, HTTP/HTTPS, TCP và TLS đều xuất hiện trong các phép đo. ([Sinar Project](https://imap.sinarproject.org/reports/2024/imap-vietnam-2024-internet-censorship-report/imap-vietnam-2024-internet-censorship-report.pdf?utm_source=chatgpt.com "iMAP Vietnam 2024 Internet Censorship Report"))

Vì vậy, khi nói:

> "Medium bị chặn ở Việt Nam"

cần phân biệt:

> **Ai đang chặn? Chặn ở tầng nào?**

Đây mới là câu hỏi kỹ thuật quan trọng.

---

# 2. Có thể hình dung toàn bộ đường đi như thế này

Ví dụ bạn nhập:

```text
https://example.com
```

Browser phải đi qua một chuỗi:

```text
Browser
   │
   │ DNS
   ▼
DNS Resolver
   │
   │ IP address
   ▼
Internet / ISP
   │
   │ TCP
   ▼
Server IP
   │
   │ TLS handshake
   ▼
HTTPS
   │
   │ HTTP request
   ▼
Web server
```

ISP có nhiều vị trí khác nhau để can thiệp.

Do đó:

```text
DNS blocking
TCP blocking
TLS blocking
HTTP blocking
IP blocking
```

không phải cùng một thứ.

---

# 3. Tầng 1 — DNS blocking

Đây là cách dễ hiểu nhất.

Browser cần:

```text
medium.com
       ↓
DNS
       ↓
IP
```

Ví dụ bình thường:

```text
medium.com
      ↓
104.x.x.x
```

Nhưng ISP có thể làm:

```text
medium.com
      ↓
DNS của ISP
      ↓
127.0.0.1
```

hoặc:

```text
NXDOMAIN
```

hoặc timeout / một địa chỉ dùng cho block page.

Đây là kỹ thuật đã được ghi nhận tại Việt Nam. Một nghiên cứu về `luatkhoa.org` năm 2018 kết luận ISP can thiệp DNS bằng cách trả về các phản hồi DNS giả cho domain bị chặn. ([Qurium Media Foundation](https://www.qurium.org/alerts/dns-tampering-in-vietnam/?utm_source=chatgpt.com "DNS tampering in Vietnam – Qurium Media Foundation"))

Về mặt kiến trúc:

```text
                  ISP DNS
                    │
Browser ────────► DNS query
                    │
              ┌─────┴─────┐
              │ blacklist │
              └─────┬─────┘
                    │
              fake response
                    │
                    ▼
Browser nhận IP sai
```

### Vì vậy DNS blocking có đặc điểm:

Nếu:

```text
DNS A → bị chặn
DNS B → trả IP thật
```

thì vấn đề có thể chỉ nằm ở DNS.

Nhưng điều này **không đảm bảo bypass được**, vì ISP có thể chặn ở tầng khác nữa.

---

# 4. Tầng 2 — IP blocking

Giả sử DNS không còn bị can thiệp:

```text
example.com
     ↓
1.2.3.4
```

Browser bắt đầu kết nối:

```text
Browser ───── TCP ─────► 1.2.3.4
```

ISP có thể biết:

```text
client → 1.2.3.4
```

và firewall/router của ISP có thể drop packet.

Khi đó:

```text
DNS       ✓
TCP       ✗
```

Browser thường biểu hiện:

```text
timeout
connection refused
connection reset
```

Đây là lý do:

> "Đổi DNS nhưng vẫn không vào được"

là hoàn toàn bình thường.

DNS chỉ giải quyết **domain → IP**.

Nó không làm thay đổi việc ISP có cho phép TCP connection tới IP đó hay không.

---

# 5. Tầng 3 — HTTP blocking

HTTP plaintext trước đây rất dễ kiểm tra.

Ví dụ:

```text
GET /article HTTP/1.1
Host: example.com
```

ISP có thể đọc:

```text
Host: example.com
```

rồi quyết định:

```text
if domain in blacklist:
    block
```

Có thể:

```text
RST connection
```

hoặc:

```text
redirect → block page
```

hoặc trả về một HTTP response đặc biệt.

Các phép đo OONI tại Việt Nam cũng ghi nhận HTTP block pages. ([Sinar Project](https://imap.sinarproject.org/reports/2022/imap-state-of-internet-censorship-country-report-2022-vietnam/2022-vietnam?utm_source=chatgpt.com "iMAP State of Internet Censorship Country Report 2022 - Vietnam"))

---

# 6. Nhưng HTTPS làm vấn đề khó hơn

Ngày nay:

```text
https://example.com
```

thì nội dung HTTP nằm trong TLS encryption.

ISP không đơn giản nhìn thấy:

```text
GET /secret/article
```

như HTTP plaintext.

Luồng sẽ gần giống:

```text
Browser
   │
   │ TCP
   ▼
Server
   │
   │ TLS handshake
   ▼
encrypted connection
   │
   ▼
HTTP encrypted
```

Nhưng điều đó **không có nghĩa ISP hoàn toàn không biết domain**.

Đây là chỗ TLS/SNI trở nên quan trọng.

---

# 7. TLS/SNI blocking

Trong quá trình TLS handshake, client cần cung cấp thông tin để server biết client muốn kết nối tới hostname nào.

Một trường quan trọng historically là:

```text
SNI
Server Name Indication
```

Ví dụ:

```text
TLS ClientHello

SNI = example.com
```

ISP có thể quan sát metadata này và thực hiện:

```text
if SNI in blacklist:
    reset connection
```

Khi đó:

```text
DNS       ✓
TCP       ✓
TLS       ✗
```

Browser có thể báo:

```text
ERR_CONNECTION_RESET
```

hoặc TLS timeout / handshake failure.

Đây chính là lý do một số phương pháp chỉ giải quyết DNS nhưng **không giải quyết được vấn đề thực sự**.

Báo cáo iMAP 2024 về Việt Nam ghi nhận cả `tls.connection_reset`, `tls.timeout`, cùng các dạng TCP/HTTPS blocking. ([Sinar Project](https://imap.sinarproject.org/reports/2024/imap-vietnam-2024-internet-censorship-report/imap-vietnam-2024-internet-censorship-report.pdf?utm_source=chatgpt.com "iMAP Vietnam 2024 Internet Censorship Report"))

---

# 8. Đây là điểm rất quan trọng để hiểu "bypass"

Không nên suy nghĩ:

```text
Website bị chặn
      ↓
Có một cách bypass
      ↓
Xong
```

Mà nên suy nghĩ:

```text
                ┌── DNS
                │
                ├── IP
                │
Client ─ ISP ───┼── TCP
                │
                ├── TLS
                │
                └── HTTP/HTTPS
```

Mỗi lớp có thể có một cơ chế kiểm soát khác nhau.

Ví dụ:

|DNS|TCP|TLS|Kết quả|
|---|---|---|---|
|✓|✓|✓|truy cập bình thường|
|✗|✓|✓|DNS problem|
|✓|✗|✓|connection timeout/reset|
|✓|✓|✗|TLS handshake failure|
|✓|✓|✓|nhưng HTTP bị block|

Vì vậy một "bypass" hoạt động hôm nay có thể không hoạt động ngày mai.

---

# 9. Vì sao các kỹ thuật bypass thường phải thay đổi?

Đây là một **cuộc chơi thích nghi giữa hai phía**.

Ví dụ:

```text
ISP:
DNS blocking
       ↓
User:
đổi DNS
       ↓
ISP:
chặn DNS khác / kiểm soát traffic
       ↓
User:
dùng cơ chế khác
       ↓
ISP:
chặn ở TLS
       ↓
User:
...
```

Đó là lý do các phương pháp vượt chặn trên Internet thường có tính thời điểm.

Một ví dụ thực tế: có những trường hợp Medium gặp vấn đề do DNS ở một ISP nước ngoài và chính Medium từng khuyến nghị người dùng thử public DNS trong sự cố đó. ([Medium Status](https://status.medium.com/incidents/01KFASSCVKK6PPM646RZZXHGRG?utm_source=chatgpt.com "Issues accessing Medium from India - Medium Status"))

Nhưng điều này không có nghĩa:

> "Public DNS luôn bypass được Medium."

DNS chỉ là **một lớp**.

---

# 10. Có một khái niệm rất quan trọng: proxy/VPN

Thay vì:

```text
Browser
   ↓
ISP
   ↓
Website
```

ta đưa thêm một intermediary:

```text
Browser
   ↓
Encrypted tunnel
   ↓
Proxy/VPN
   ↓
Website
```

ISP lúc này không nhất thiết nhìn thấy trực tiếp:

```text
Browser → blocked-domain.com
```

mà có thể chỉ thấy:

```text
Browser → VPN/proxy endpoint
```

và tunnel chứa traffic bên trong.

Đây là thay đổi kiến trúc, chứ không phải "sửa DNS".

---

# 11. Vì vậy có thể phân loại các phương án theo tầng

Từ góc nhìn network engineering:

```text
Layer                         Ý tưởng
────────────────────────────────────────────────
DNS                          thay đổi cách resolve
Routing / IP                 thay đổi đường đi
Proxy                        thêm intermediary
VPN                          tạo encrypted tunnel
TLS-related                  thay đổi metadata/handshake visibility
Application proxy            request qua server trung gian
```

Mỗi phương án giải quyết **một loại failure khác nhau**.

Đây là cách tư duy tốt hơn việc sưu tầm hàng chục "tool bypass".

---

# 12. Có cần tạo một danh sách fallback không?

**Có, nếu mục đích của bạn là thiết kế hệ thống có khả năng chịu failure.**

Nhưng tôi sẽ không tạo kiểu:

```text
Bypass 1
Bypass 2
Bypass 3
Bypass 4
...
```

Mà tạo theo **failure classification**:

```text
                    Website unavailable
                           │
             ┌─────────────┴─────────────┐
             │                           │
        DNS failure                Connection failure
             │                           │
       DNS diagnosis             ┌───────┴───────┐
                                 │               │
                              TCP failure     TLS failure
```

Tức là:

> **Detect → classify → choose mitigation**

chứ không phải:

> **Try random bypass #1 → #2 → #3**

Đây chính xác là tư duy troubleshooting của một system/network engineer.

---

# 13. Và đây là điều tôi nghĩ bạn đang thiếu trong mô hình hiện tại

Nếu bạn đang học HTTP request flow như cuộc trao đổi trước, đừng coi:

```text
DNS → TCP → TLS → HTTP
```

chỉ là một chuỗi thủ tục.

Hãy coi nó là **các control points**.

Mỗi bước có:

```text
Protocol
↓
Data visible
↓
Who can observe it
↓
Who can modify it
↓
Possible failure
↓
Diagnostic signal
```

Ví dụ:

```text
DNS
├── visible: hostname
├── observer: DNS resolver / network
├── failure: NXDOMAIN / wrong IP / timeout
└── diagnosis: DNS resolution

TCP
├── visible: source/destination IP, ports
├── observer: routers/firewalls
├── failure: timeout/reset
└── diagnosis: connectivity

TLS
├── visible: handshake metadata varies by protocol/version
├── observer: network middleboxes
├── failure: handshake reset/timeout
└── diagnosis: TLS negotiation

HTTP
├── encrypted under HTTPS
├── server receives request
├── failure: HTTP status/error
└── diagnosis: application layer
```

Khi hiểu được bảng này, bạn sẽ tự suy ra tại sao một phương pháp xử lý có thể thành công ở mạng A nhưng thất bại ở mạng B.

---

## 14. Một điểm pháp lý cũng cần tách riêng

Việc một ISP thực hiện blocking ở Việt Nam có cơ sở từ các yêu cầu quản lý Internet và các quy định hiện hành; chẳng hạn Nghị định 147/2024/NĐ-CP quy định trách nhiệm của doanh nghiệp Internet trong việc triển khai biện pháp kỹ thuật ngăn chặn truy cập tới một số nội dung/dịch vụ khi có yêu cầu của cơ quan có thẩm quyền. ([Thư Viện Pháp Luật](https://thuvienphapluat.vn/van-ban/EN/Cong-nghe-thong-tin/Decree-147-2024-ND-CP-management-provision-and-use-of-Internet-services-and-cyber-information/636187/tieng-anh.aspx?utm_source=chatgpt.com "147/2024/ND-CP in Vietnam, Decree 147/2024/ND-CP management provision and use of Internet services and cyber information in Vietnam"))

Do đó, về mặt kỹ thuật có thể nghiên cứu:

> **"ISP censorship hoạt động thế nào?"**

nhưng không nên mặc định:

> **"mọi website bị chặn đều có cùng nguyên nhân hoặc mọi cách vượt chặn đều phù hợp/được phép."**

---

### Mô hình tôi khuyên bạn dùng để tiếp tục nghiên cứu

```text
                    ┌─────────────┐
                    │   Browser   │
                    └──────┬──────┘
                           │
                         DNS
                           │
                    ┌──────▼──────┐
                    │ DNS Resolver│
                    └──────┬──────┘
                           │
                          IP
                           │
                    ┌──────▼──────┐
                    │ ISP / Router│
                    └──────┬──────┘
                           │
                          TCP
                           │
                         TLS
                           │
                         HTTP
                           │
                    ┌──────▼──────┐
                    │ Web Server  │
                    └─────────────┘

              ISP có thể can thiệp ở:
                 DNS / IP / TCP /
                    TLS / HTTP
```

Nếu mục tiêu của bạn là **học networking thật sự**, bước tiếp theo đáng làm hơn là lấy một website _do chính bạn sở hữu hoặc một test server_ và mô phỏng từng kiểu blocking, sau đó dùng `dig`, `nslookup`, `curl`, Wireshark và browser DevTools để quan sát từng tầng. Cách này sẽ giúp bạn hiểu cơ chế bypass ở mức kiến trúc mà không cần phụ thuộc vào danh sách tool.