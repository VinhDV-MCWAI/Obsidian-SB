# TÀI LIỆU NGHIÊN CỨU: TOÀN BỘ VÒNG ĐỜI MỘT REQUEST TRÊN INTERNET

## 0. Trước hết: nhìn Internet như một hệ thống gồm các cụm chức năng

Đừng bắt đầu bằng "cục Wi-Fi".

Hãy chia hệ thống thành 9 cụm:

```text
┌─────────────────────────────────────────────────────────┐
│ 1. USER / APPLICATION                                  │
│ Browser / Mobile App / JavaScript                      │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 2. NAME RESOLUTION                                    │
│ DNS / Cache / Resolver                                │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 3. SECURITY / CONNECTION                               │
│ TLS / TCP / QUIC                                      │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 4. LOCAL NETWORK                                      │
│ Wi-Fi / Ethernet / Router / NAT / DHCP / ARP/NDP      │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 5. ISP / INTERNET TRANSPORT                            │
│ Access / Aggregation / Core / BGP / Peering / Transit │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 6. DESTINATION NETWORK                                 │
│ CDN / Firewall / Load Balancer / Reverse Proxy        │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 7. APPLICATION BACKEND                                │
│ Nginx / PHP-FPM / Laravel / Node / Java / etc.        │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 8. DATA / INTERNAL SERVICES                            │
│ PostgreSQL / Redis / APIs / queues / microservices    │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 9. RESPONSE / CLIENT PROCESSING                        │
│ Network → Browser → HTML/CSS/JS → Render → UI        │
└─────────────────────────────────────────────────────────┘
```

Đây là **khung nghiên cứu chính**.

Sau đó mỗi cụm lại được chia thành các thành phần nhỏ.

---

# PHẦN I — BROWSER / APPLICATION

## 1. User không trực tiếp tạo packet

Bạn click:

```text
<button>Load products</button>
```

JavaScript:

```javascript
fetch("/api/products");
```

Bạn có thể tưởng tượng:

```text
User
 ↓
Browser
 ↓
JavaScript
 ↓
fetch()
 ↓
HTTP request
```

Browser chịu trách nhiệm rất nhiều thứ:

- URL parsing
    
- HTTP
    
- cache
    
- cookies
    
- connection pooling
    
- DNS integration
    
- TLS
    
- security policy
    
- CORS
    
- HTTP/2, HTTP/3
    
- parsing response
    
- rendering
    

Nhưng browser **không trực tiếp điều khiển từng router Internet**.

---

# PHẦN II — DNS

## 2. Tại sao DNS phải xảy ra sớm?

Vì HTTP cần biết:

```text
Tôi gửi request đến server nào?
```

Bạn có:

```text
https://api.example.com/users
```

Nhưng tầng IP cần:

```text
IP = ?
```

Ví dụ:

```text
api.example.com
        ↓
203.0.113.10
```

Sau đó connection mới có thể được thiết lập đến:

```text
203.0.113.10:443
```

Cho nên:

```text
Domain
  ↓
IP
  ↓
Connection
  ↓
HTTP
```

là mental model hợp lý.

Nhưng **DNS lookup không phải lúc nào cũng xảy ra tại thời điểm request**.

---

# 3. Browser có lưu toàn bộ IP Internet không?

**Không.**

Đây là điểm bạn suy luận sai.

Browser không có database kiểu:

```text
google.com → ...
facebook.com → ...
youtube.com → ...
amazon.com → ...
...
```

cho toàn bộ Internet.

Thay vào đó có nhiều lớp cache.

Ví dụ:

```text
Browser
  ↓
OS
  ↓
DNS Resolver
  ↓
Authoritative DNS
```

Giả sử bạn truy cập:

```text
example.com
```

lần đầu:

```text
Browser
 ↓
OS / resolver
 ↓
DNS lookup
 ↓
IP
```

Kết quả có thể được cache.

Lần sau:

```text
Browser
 ↓
DNS cache HIT
 ↓
IP
```

không cần hỏi lại authoritative DNS.

---

# 4. TTL là cơ chế rất quan trọng

DNS response thường có:

```text
IP = 203.0.113.10
TTL = 300
```

Có nghĩa đại khái:

> Có thể cache câu trả lời này trong khoảng thời gian được chỉ định.

Ví dụ:

```text
10:00
DNS → 203.0.113.10
TTL 300s

10:02
cache HIT

10:04
cache HIT

10:06
TTL hết hạn
 ↓
DNS lookup mới
```

Đây là lý do DNS vừa nhanh vừa có khả năng cập nhật.

---

# 5. DNS resolver nằm ở đâu?

Không nhất thiết trong browser.

Có thể có:

```text
Browser
 ↓
OS
 ↓
Router
 ↓
ISP DNS resolver
```

hoặc:

```text
Browser
 ↓
OS
 ↓
Configured DNS resolver
```

Resolver có thể là:

- ISP
    
- enterprise
    
- public resolver
    
- local network
    
- router
    
- DNS software trên máy
    

---

# 6. Vì vậy câu hỏi "Browser gửi request đến DNS ở đâu?" cần trả lời chính xác

Không nhất thiết:

```text
Browser → DNS server
```

mà thường:

```text
Browser
   ↓
DNS API của OS / resolver
   ↓
DNS infrastructure
```

Browser sử dụng cơ chế DNS của hệ điều hành hoặc cơ chế resolver riêng tùy kiến trúc.

---

# PHẦN III — TLS

Đây là phần bạn đang hiểu gần đúng nhưng cần sửa bản chất.

---

# 7. HTTPS certificate được đăng ký trước?

Đúng về ý tưởng.

Server có certificate được CA cấp, ví dụ:

```text
example.com
     ↓
Certificate
     ↓
CA signs certificate
```

Certificate chứa thông tin để client có thể xác minh danh tính server, cùng public key và các thông tin liên quan.

---

# 8. Nhưng certificate KHÔNG phải khóa mã hóa toàn bộ request

Đây là điểm rất quan trọng.

Không phải:

```text
Request
 ↓
certificate encryption
 ↓
Internet
```

Mà gần đúng hơn:

```text
Server certificate
        ↓
Client xác thực server
        ↓
TLS handshake
        ↓
Hai bên thiết lập shared session keys
        ↓
HTTP data
        ↓
Symmetric encryption
```

---

# 9. Vì sao dùng session key?

Nếu mỗi byte HTTP đều được mã hóa bằng cryptography bất đối xứng thì rất tốn tài nguyên.

Do đó TLS dùng:

```text
Asymmetric cryptography
        ↓
authentication + key establishment
```

sau đó:

```text
Symmetric cryptography
        ↓
encrypt actual traffic
```

Ví dụ conceptually:

```text
TLS handshake
       ↓
shared secret
       ↓
session keys
       ↓
GET /users
       ↓
encrypted
```

---

# 10. TLS có chống Man-in-the-Middle không?

**Có, nếu TLS được triển khai và xác thực đúng.**

Ví dụ attacker ở Wi-Fi:

```text
Laptop
   ↓
Attacker
   ↓
Internet
```

Attacker có thể nhìn thấy network traffic ở một số mức độ.

Nhưng nếu HTTPS hoạt động đúng:

```text
HTTP data
   ↓
TLS encryption
   ↓
encrypted traffic
```

attacker không thể đơn giản đọc:

```text
Authorization: Bearer ...
```

hoặc nội dung HTTP.

---

# 11. Nhưng tại sao Wi-Fi công cộng vẫn nguy hiểm?

Vì HTTPS **không biến Wi-Fi thành mạng đáng tin cậy**.

Có nhiều loại rủi ro khác.

Ví dụ attacker có thể cố:

```text
Fake Wi-Fi
     ↓
Bạn kết nối
     ↓
Attacker controls network
```

Họ có thể thực hiện:

- DNS manipulation
    
- redirect
    
- traffic interception
    
- phishing
    
- malicious captive portal
    
- downgrade/legacy protocol attacks
    
- metadata observation
    
- local network attacks
    
- exploit thiết bị nếu thiết bị/dịch vụ có lỗ hổng
    

Nhưng HTTPS giúp chặn một phần rất lớn của kiểu tấn công:

```text
"tôi đứng giữa và đọc/sửa HTTPS traffic"
```

nếu certificate validation không bị phá.

---

# 12. Ví dụ MITM

Không HTTPS:

```text
Browser
   ↓
HTTP
   ↓
Wi-Fi attacker
   ↓
ISP
   ↓
Server
```

Attacker có thể đọc HTTP content.

HTTPS:

```text
Browser
   ↓
TLS encrypted
   ↓
Wi-Fi attacker
   ↓
ISP
   ↓
Server
```

Attacker vẫn có thể nhìn thấy metadata nhất định nhưng không có khóa phiên để giải mã nội dung TLS bình thường.

---

# 13. Nếu buộc phải dùng Wi-Fi không tin cậy?

Ở mức thực hành:

### Ưu tiên 1

Chỉ sử dụng website/app có:

```text
HTTPS
```

và không bỏ qua cảnh báo certificate.

### Ưu tiên 2

Không thực hiện các thao tác nhạy cảm trên mạng không đáng tin nếu không cần thiết.

### Ưu tiên 3

Tắt:

```text
file sharing
network discovery
```

trên mạng công cộng.

### Ưu tiên 4

Giữ OS/browser cập nhật.

### Ưu tiên 5

VPN có thể tăng bảo vệ trên mạng không tin cậy, nhưng **VPN không phải thuốc chữa mọi vấn đề**.

---

# PHẦN IV — LOCAL NETWORK

Bây giờ quay lại "cục Wi-Fi".

Đây là nơi nên bắt đầu tư duy theo **vai trò**, không theo tên thiết bị.

---

# 14. Một thiết bị mạng gia đình thường tích hợp nhiều chức năng

Bạn nhìn thấy:

```text
┌──────────────┐
│ CỤC WIFI     │
└──────────────┘
```

nhưng bên trong có thể là:

```text
┌─────────────────────────────────────┐
│ Wi-Fi Access Point                  │
│ Ethernet Switch                    │
│ Router                             │
│ NAT                                │
│ DHCP Server                        │
│ Firewall                           │
│ DNS Forwarder / Cache              │
│ Management UI                      │
│ sometimes modem/ONT                │
└─────────────────────────────────────┘
```

Đây là một insight rất quan trọng.

> **Thiết bị vật lý ≠ một chức năng.**

Một thiết bị có thể chứa rất nhiều chức năng.

---

# 15. Chia "cục Wi-Fi" thành các module

|Thành phần|Nhiệm vụ|Thường có sẵn?|Có thể custom?|
|---|---|--:|--:|
|Wi-Fi AP|Radio ↔ network|Có|Có|
|Switch|Ethernet forwarding|Có|Có|
|Router|IP forwarding|Có|Có|
|NAT|Private ↔ public|Có|Có|
|DHCP|cấp IP nội bộ|Có|Có|
|Firewall|filtering|Có|Có|
|DNS forwarder|chuyển DNS query|Thường có|Có|
|VPN client/server|tunnel|Tùy|Có|
|QoS|traffic control|Tùy|Có|
|VLAN|network segmentation|Tùy|Có|
|Routing|route selection|Có|Có|
|Management|cấu hình|Có|Có|

---

# 16. Wi-Fi AP làm gì?

Nó chủ yếu chuyển:

```text
Radio
 ↕
802.11 frames
```

Ví dụ:

```text
Laptop
   │
   │ radio
   ▼
Access Point
   │
   │ Ethernet
   ▼
Network
```

Nó không phải "Internet".

Nó chỉ là **đoạn truy cập mạng không dây**.

---

# 17. Switch làm gì?

Ví dụ nhà có:

```text
PC
Laptop
TV
Server
```

Switch chuyển Ethernet frame giữa các port.

```text
PC ─┐
TV ─┼── Switch ─── Router
NAS ─┘
```

Switch chủ yếu hoạt động ở Layer 2.

---

# 18. Router làm gì?

Router nối các network khác nhau.

Ví dụ:

```text
192.168.1.0/24
       │
    Router
       │
ISP network
```

Router quyết định:

```text
Destination IP
     ↓
routing table
     ↓
next hop/interface
```

---

# 19. DHCP làm gì?

Khi laptop vào Wi-Fi, nó cần:

```text
IP address
subnet mask
default gateway
DNS server
```

DHCP có thể cung cấp:

```text
IP = 192.168.1.20
Gateway = 192.168.1.1
DNS = 192.168.1.1
```

---

# 20. NAT làm gì?

Mạng nhà:

```text
Laptop
192.168.1.20
```

Router:

```text
Public IP
14.x.x.x
```

NAT tạo mapping:

```text
192.168.1.20:50000
        ↕
14.x.x.x:42000
```

---

# 21. Firewall làm gì?

Ví dụ:

```text
Internet
   ↓
Firewall
   ↓
Home network
```

Firewall quyết định traffic nào được phép.

Đây là một chức năng khác với router mặc dù cùng thiết bị có thể thực hiện cả hai.

---

# PHẦN V — ISP

Đây là nơi mental model "cục Wi-Fi → trung tâm ISP → server" cần thay đổi.

---

# 22. ISP có nhiều tầng

Một mô hình đơn giản:

```text
Home
 ↓
Access Network
 ↓
Aggregation
 ↓
ISP Edge
 ↓
ISP Core
 ↓
Peering / Transit
 ↓
Other Networks
```

---

# 23. Access network

Đây là phần gần khách hàng nhất.

Ví dụ fiber:

```text
Home
 ↓
ONT
 ↓
Fiber access network
 ↓
OLT
```

Không phải tất cả mạng đều giống nhau, nhưng đây là mô hình FTTH phổ biến.

---

# 24. Aggregation

Nhiều khách hàng được gom:

```text
Home A ─┐
Home B ─┤
Home C ─┼── Aggregation
Home D ─┘
```

Sau đó đi vào mạng ISP lớn hơn.

---

# 25. ISP Core

Core network có các router tốc độ rất cao.

```text
Edge
 ↓
Core Router
 ↓
Core Router
 ↓
Core Router
```

Các router này forward IP traffic.

---

# 26. ISP không quyết định toàn bộ Internet bằng một blacklist duy nhất

Đây là phần liên quan trực tiếp đến câu hỏi cuối của bạn.

Bạn đang giả định:

```text
Browser
 ↓
ISP DNS
 ↓
Blacklist
 ↓
DNS không trả IP
```

Điều này **có thể xảy ra**.

Nhưng chỉ là **một kỹ thuật**.

---

# PHẦN VI — CÁC KIỂU CHẶN WEBSITE

## 27. DNS blocking

Ví dụ:

```text
example-blocked.com
       ↓
DNS resolver
       ↓
policy
       ↓
NXDOMAIN / wrong IP / block response
```

Browser không có IP đúng.

Kết quả:

```text
DNS failure
 ↓
connection không được thực hiện
```

Đây đúng với giả thuyết của bạn.

Nhưng chưa đủ.

---

# 28. IP blocking

ISP/firewall có thể biết:

```text
Destination IP = X
```

và drop packet.

Khi đó:

```text
DNS
 ↓
IP đúng
 ↓
ISP
 ↓
BLOCK
```

DNS hoàn toàn có thể hoạt động bình thường.

---

# 29. Domain/SNI filtering

Ngay cả khi DNS không bị chặn, hệ thống mạng có thể phát hiện domain ở một số phần của connection metadata, tùy giao thức và cấu hình.

Ví dụ:

```text
DNS → IP đúng
       ↓
TCP connection
       ↓
TLS handshake
       ↓
network filtering
       ↓
BLOCK
```

---

# 30. HTTP filtering

Nếu là HTTP plaintext:

```text
GET /something
Host: example.com
```

thiết bị trung gian có thể nhìn thấy Host và request.

Có thể chặn trực tiếp.

HTTPS làm điều này khó hơn vì nội dung HTTP được mã hóa.

---

# 31. DPI — Deep Packet Inspection

Một hệ thống DPI có thể phân tích traffic ở mức sâu hơn router forwarding thông thường.

Có thể dùng:

```text
packet metadata
protocol patterns
connection behavior
TLS metadata
traffic signatures
```

để đưa ra policy.

Do đó:

> "DNS không trả IP" chỉ là một trong nhiều lớp có thể block.

---

# PHẦN VII — PROXY VÀ VPN

Đây là phần bạn đang có một mental model khá gần nhưng cần sửa.

---

# 32. Proxy không nhất thiết thay đổi "địa điểm DNS"

Proxy:

```text
Browser
   ↓
Proxy
   ↓
Destination
```

Ví dụ:

```text
Browser
 ↓
Proxy server
 ↓
example.com
```

Browser không nhất thiết kết nối trực tiếp đến destination.

---

# 33. VPN khác proxy ở kiến trúc

VPN thường tạo:

```text
Laptop
   ↓
Encrypted tunnel
   ↓
VPN server
   ↓
Internet
   ↓
Destination
```

Ví dụ:

```text
                 encrypted tunnel
Laptop ===========================> VPN server
                                       │
                                       ↓
                                   Internet
                                       │
                                       ↓
                                   Website
```

Do đó ISP có thể chủ yếu nhìn thấy:

```text
Laptop → VPN server
```

thay vì trực tiếp:

```text
Laptop → website
```

tùy cấu hình VPN.

---

# 34. VPN có "đổi địa điểm" không?

Có thể làm thay đổi **public source IP mà destination nhìn thấy**.

Ví dụ:

Không VPN:

```text
Laptop
 ↓
ISP
 ↓
Website

Website sees:
Vietnam ISP public IP
```

VPN:

```text
Laptop
 ↓
ISP
 ↓
VPN server in another region
 ↓
Website
```

Website có thể thấy:

```text
VPN server public IP
```

Do đó website có thể suy ra vị trí khác dựa trên IP.

Nhưng:

> VPN không làm browser "lầm tưởng DNS nằm ở một địa điểm khác" theo cách bạn mô tả.

Đây là hai vấn đề khác nhau.

---

# 35. DNS khi dùng VPN

Tùy VPN configuration.

Có thể:

```text
Laptop
 ↓
VPN tunnel
 ↓
VPN DNS resolver
```

hoặc DNS vẫn được xử lý theo một cấu hình khác.

Một VPN tốt thường thiết kế DNS traffic để tránh DNS leak, nhưng chi tiết phụ thuộc implementation.

Do đó:

```text
VPN = thay đổi DNS
```

là sai.

Đúng hơn:

```text
VPN = network tunnel
```

và tunnel đó **có thể bao gồm DNS traffic**.

---

# 36. Tại sao VPN có thể bypass DNS blocking?

Ví dụ ISP:

```text
DNS query
example-blocked.com
        ↓
ISP DNS
        ↓
BLOCK
```

VPN:

```text
Laptop
   ↓
encrypted VPN tunnel
   ↓
VPN server
   ↓
DNS resolver
   ↓
example-blocked.com
   ↓
IP
```

ISP không còn trực tiếp xử lý DNS query theo cách cũ nếu DNS query được gửi qua tunnel.

Nhưng đây chỉ là một dạng bypass.

---

# 37. Nếu ISP block IP thì sao?

Ví dụ:

```text
DNS → correct IP
```

nhưng:

```text
ISP
 ↓
destination IP blocked
```

VPN có thể thay đổi đường đi:

```text
Laptop
 ↓
VPN tunnel
 ↓
VPN server
 ↓
Destination
```

ISP chỉ thấy connection tới VPN server.

Do đó IP blocking trực tiếp của destination có thể không còn áp dụng theo cùng cách.

---

# 38. Nhưng VPN không phải lúc nào cũng bypass được mọi blocking

Nếu network chặn:

```text
VPN protocol
VPN server IP
VPN traffic pattern
```

thì VPN có thể không kết nối được.

Ngoài ra destination website có thể tự chặn:

```text
VPN IP ranges
datacenter IPs
proxy IPs
```

Vì vậy:

```text
VPN ≠ guaranteed bypass
```

---

# PHẦN VIII — TẠI SAO VPN CHẬM HƠN?

Suy luận này của bạn **cơ bản đúng**, nhưng nguyên nhân chính xác hơn.

Không VPN:

```text
Laptop
 ↓
ISP
 ↓
Internet
 ↓
Server
```

VPN:

```text
Laptop
 ↓
ISP
 ↓
VPN server
 ↓
Internet
 ↓
Server
```

Thêm một node:

```text
VPN server
```

và thêm processing:

```text
encrypt
 ↓
encapsulate
 ↓
transmit
 ↓
decrypt
 ↓
forward
```

---

# 39. Latency có thể tăng

Ví dụ:

```text
Direct:

Laptop ──────── Server
       30 ms
```

VPN:

```text
Laptop ─ VPN ─ Server
       15 + 40 ms
```

thành:

```text
55 ms
```

Đây chỉ là ví dụ.

VPN có thể nhanh hoặc chậm tùy:

- vị trí VPN server
    
- network path
    
- congestion
    
- protocol
    
- server load
    
- encryption overhead
    
- ISP
    
- peering
    
- routing
    

---

# 40. Proxy cũng có overhead

Không proxy:

```text
Client → Server
```

Proxy:

```text
Client → Proxy → Server
```

Proxy phải:

```text
receive
process
forward
```

Có thể tăng:

- latency
    
- CPU
    
- bandwidth bottleneck
    
- congestion
    

Nhưng proxy không nhất thiết luôn chậm đáng kể.

CDN/reverse proxy là ví dụ cực kỳ quan trọng:

```text
Client
 ↓
CDN
 ↓
Origin
```

Proxy ở đây đôi khi **làm hệ thống nhanh hơn rất nhiều**, vì cache và đặt server gần user.

---

# PHẦN IX — CDN LÀM THAY ĐỔI MENTAL MODEL "IP = SERVER"

Đây là phần rất đáng học.

Bạn đang nghĩ:

```text
domain
 ↓
IP
 ↓
server
```

Trong production:

```text
domain
 ↓
DNS
 ↓
CDN / Load Balancer
 ↓
edge
 ↓
origin
 ↓
application
```

Ví dụ:

```text
example.com
      ↓
     DNS
      ↓
CDN edge
      ↓
   cache HIT
      ↓
   response
```

Origin server có thể **không nhận request**.

---

# 41. DNS cũng không chỉ trả "một IP server"

Có thể trả:

```text
IP A
IP B
IP C
```

hoặc dùng DNS infrastructure để hướng traffic theo nhiều chính sách.

Mục đích có thể là:

- redundancy
    
- load distribution
    
- geographic steering
    
- latency
    
- failover
    
- CDN
    
- availability
    

Vì vậy ý tưởng của bạn:

> DNS có nhiều nơi để tăng performance

**có phần đúng nhưng cần sửa**.

DNS resolver có thể phân bố ở nhiều địa điểm để giảm latency.

Nhưng authoritative DNS và resolver là hai khái niệm khác nhau, và việc "chọn server gần nhất" không đơn giản chỉ dựa vào vị trí vật lý của user.

---

# 42. Resolver gần user có ý nghĩa gì?

Ví dụ:

```text
User Vietnam
      ↓
Vietnam DNS resolver
```

thì query:

```text
example.com?
```

có thể được trả nhanh hơn nếu resolver gần user và có cache.

Nhưng:

> Resolver gần user **không có nghĩa destination server cũng gần user**.

Đây là hai chuyện khác nhau.

---

# 43. Có thể DNS trả IP khác nhau tùy vị trí

Ví dụ conceptually:

```text
User Vietnam
     ↓
DNS
     ↓
CDN edge Vietnam

User Japan
     ↓
DNS
     ↓
CDN edge Japan
```

Mục đích:

```text
user
 ↓
nearest / suitable edge
```

giảm latency.

Đây là một trong những nền tảng của CDN/global traffic management.

---

# PHẦN X — "REQUEST ĐI ĐÂU?" NHÌN BẰNG 4 MẶT

Đây là cách tôi khuyên bạn nghiên cứu.

Một request phải theo dõi **4 dòng song song**.

## Dòng 1 — DATA

```text
HTTP data
 ↓
TLS
 ↓
TCP/QUIC
 ↓
IP
 ↓
Frame
```

## Dòng 2 — LOCATION

```text
Domain
 ↓
IP
 ↓
routing
 ↓
destination network
 ↓
server
```

## Dòng 3 — SECURITY

```text
Certificate
 ↓
TLS handshake
 ↓
session keys
 ↓
encrypted traffic
```

## Dòng 4 — HARDWARE

```text
Browser
 ↓
Wi-Fi NIC
 ↓
Access Point
 ↓
Router
 ↓
ONT
 ↓
Fiber
 ↓
ISP routers
 ↓
Data-center network
 ↓
Server NIC
```

Khi học networking, **đừng trộn 4 dòng này thành một thứ**.

---

# PHẦN XI — BẢNG "THÀNH PHẦN NÀO TỰ ĐỘNG, THÀNH PHẦN NÀO CUSTOM?"

Đây là bảng rất phù hợp với mục tiêu của bạn.

|Thành phần|Ai vận hành?|Thường tự động?|Developer thường custom?|
|---|---|--:|--:|
|Browser|Browser vendor|Có|Ít|
|DNS resolver|OS/network/ISP/provider|Có|Có thể|
|DNS authoritative|Domain owner/provider|Có|Có|
|TLS|Browser/server|Có|Cấu hình|
|TCP/QUIC|OS/network stack|Có|Ít|
|Wi-Fi|AP/router|Có|Cấu hình|
|DHCP|Router/ISP|Có|Cấu hình|
|NAT|Router|Có|Cấu hình|
|Routing|Router/ISP|Có|Network engineer|
|BGP|ISP/network|Có|Network engineer|
|Firewall|Network/security|Có|Cấu hình|
|CDN|Provider|Có|Cấu hình|
|Load Balancer|Infra|Có|Cấu hình|
|Reverse Proxy|Infra|Có|Cấu hình|
|Web server|Infra|Có|Cấu hình|
|PHP-FPM|Backend infrastructure|Có|Cấu hình|
|Laravel|Developer|Không hoàn toàn|**Có**|
|Redis|Developer/infra|Có|**Có**|
|PostgreSQL|DBA/backend|Có|**Có**|
|Business logic|Developer|Không|**Có**|

---

# PHẦN XII — CÁI GÌ BẠN NÊN HỌC SÂU?

Với một backend developer như bạn, **không cần trở thành network engineer để hiểu request**.

Tôi sẽ chia thành 4 mức.

## Level 1 — Bắt buộc

```text
DNS
IP
TCP
UDP
HTTP
HTTPS
TLS
Port
Socket
NAT
Router
Firewall
```

## Level 2 — Backend rất nên hiểu

```text
TCP handshake
TLS handshake
Connection reuse
Keep-Alive
HTTP/2
HTTP/3
QUIC
DNS caching
CDN
Load Balancer
Reverse Proxy
Timeout
Retry
Connection pool
```

## Level 3 — System Engineer

```text
Routing
BGP
Peering
Transit
Anycast
DDoS
NAT
VLAN
Subnet
MTU
Packet fragmentation
Congestion control
Network observability
```

## Level 4 — Network specialist

```text
OSPF
IS-IS
MPLS
BGP policy
Optical transport
Carrier architecture
ISP edge/core design
```

Không nhất thiết phải học Level 4 cho backend middle.

---

# PHẦN XIII — TOÀN BỘ FLOW VỚI MỘT REQUEST THỰC

Giả sử:

```text
https://api.example.com/users/123
```

Bạn click button.

## Bước 1

Browser parse:

```text
scheme=https
host=api.example.com
port=443
path=/users/123
```

## Bước 2

Browser/OS kiểm tra DNS cache.

```text
HIT?
```

Nếu hit:

```text
IP = X
```

Nếu miss:

```text
DNS resolver
 ↓
DNS hierarchy
 ↓
IP
```

## Bước 3

Browser cần connection.

Có thể:

```text
existing connection
```

hoặc:

```text
TCP handshake
```

## Bước 4

HTTPS:

```text
TLS handshake
 ↓
certificate validation
 ↓
session keys
```

## Bước 5

Browser tạo:

```http
GET /users/123
Host: api.example.com
...
```

## Bước 6

TLS encrypt.

## Bước 7

Transport layer xử lý:

```text
TCP segments
```

hoặc:

```text
QUIC
```

## Bước 8

IP:

```text
source IP
destination IP
```

## Bước 9

Local network:

```text
IP packet
 ↓
Wi-Fi frame
 ↓
radio
```

## Bước 10

AP/router nhận.

## Bước 11

Router/NAT xử lý.

## Bước 12

ISP nhận.

## Bước 13

ISP routing:

```text
routing table
 ↓
next hop
```

## Bước 14

Traffic đi qua:

```text
ISP
 ↓
peering/transit
 ↓
destination network
```

## Bước 15

Đến:

```text
CDN / firewall / load balancer
```

## Bước 16

Đến:

```text
Nginx
```

## Bước 17

Đến:

```text
PHP-FPM
```

## Bước 18

Laravel:

```text
Route
 ↓
Middleware
 ↓
Controller
 ↓
Service
```

## Bước 19

Laravel:

```text
Redis?
```

Nếu cache HIT:

```text
Redis
 ↓
response
```

Nếu MISS:

```text
PostgreSQL
 ↓
data
 ↓
Redis
 ↓
response
```

## Bước 20

Response:

```json
{
    "id": 123
}
```

được truyền ngược qua network.

## Bước 21

Browser nhận.

## Bước 22

TLS decrypt.

## Bước 23

HTTP parser xử lý.

## Bước 24

JavaScript nhận JSON.

## Bước 25

UI update.

```text
Network
 ↓
JS
 ↓
DOM / framework
 ↓
screen
```

---

# PHẦN XIV — MỘT REQUEST KHÔNG PHẢI LÀ MỘT PACKET

Đây là kiến thức bạn nên khóa lại trong đầu.

Giả sử:

```text
HTTP request
```

không đồng nghĩa:

```text
1 request = 1 packet
```

Một HTTP message có thể được truyền qua nhiều transport segments/packets.

Ngược lại, một connection có thể mang nhiều HTTP requests.

Ví dụ HTTP/2:

```text
TCP connection
 ├── HTTP stream 1
 ├── HTTP stream 2
 ├── HTTP stream 3
 └── HTTP stream 4
```

Do đó:

```text
HTTP request
≠
TCP connection
≠
TCP segment
≠
IP packet
≠
Wi-Fi frame
```

Đây là một trong những distinction quan trọng nhất trong toàn bộ tài liệu này.

---

# PHẦN XV — CÁCH NHÌN "CỤC WIFI" ĐÚNG

Thay vì:

```text
                 CỤC WIFI
                    ↓
               xử lý request
```

hãy nhìn:

```text
                    HOME NETWORK
                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
       ▼                 ▼                  ▼
    Wi-Fi AP          Switch              Router
       │                                    │
       │                                    ├── NAT
       │                                    ├── Firewall
       │                                    ├── DHCP
       │                                    └── Routing
       │
       └────────────── devices
                                            │
                                            ▼
                                           ISP
```

Và nếu thiết bị ISP tích hợp:

```text
             HOME GATEWAY
                  │
      ┌───────────┼────────────┐
      ▼           ▼            ▼
    Wi-Fi       Switch       Router
                              │
                   ┌──────────┼──────────┐
                   ▼          ▼          ▼
                  NAT      Firewall     DHCP
                              │
                              ▼
                             ISP
```

Đây mới là cách nhìn có khả năng mở rộng.

---

# PHẦN XVI — MENTAL MODEL CUỐI CÙNG

Bạn có thể dùng mô hình này làm "bản đồ Internet" cho tất cả những nghiên cứu sau:

```text
                         USER
                           │
                           ▼
                  ┌─────────────────┐
                  │ BROWSER / APP   │
                  └────────┬────────┘
                           │
                    "Server ở đâu?"
                           │
                           ▼
                     ┌───────────┐
                     │    DNS    │
                     └─────┬─────┘
                           │
                         IP
                           │
                    "Kết nối thế nào?"
                           │
                           ▼
                 ┌──────────────────┐
                 │ TCP / QUIC / TLS │
                 └────────┬─────────┘
                          │
                    encrypted data
                          │
                    "Ra khỏi nhà"
                          │
                          ▼
              ┌───────────────────────┐
              │ HOME NETWORK          │
              │ Wi-Fi / Switch / NAT  │
              │ Router / Firewall     │
              └───────────┬───────────┘
                          │
                         ISP
                          │
                          ▼
              ┌───────────────────────┐
              │ INTERNET              │
              │ Routing / BGP /       │
              │ Peering / Transit     │
              └───────────┬───────────┘
                          │
                   destination
                          │
                          ▼
              ┌───────────────────────┐
              │ EDGE / CDN / LB /     │
              │ REVERSE PROXY         │
              └───────────┬───────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │ APPLICATION SERVER    │
              │ Nginx / PHP-FPM /     │
              │ Laravel               │
              └───────────┬───────────┘
                          │
                    "Cần dữ liệu?"
                          │
                ┌─────────┴──────────┐
                ▼                    ▼
             Redis              PostgreSQL
                │                    │
                └─────────┬──────────┘
                          ▼
                       RESPONSE
                          │
                          ▼
                    NETWORK BACK
                          │
                          ▼
                       BROWSER
                          │
                 ┌────────┴────────┐
                 ▼                 ▼
               HTML              JSON
                 │                 │
                 ▼                 ▼
               Render          JavaScript
                 │                 │
                 └────────┬────────┘
                          ▼
                          UI
```

## Một nguyên tắc rất quan trọng để bạn tiếp tục nghiên cứu

Mỗi khi gặp một công nghệ mới, đừng hỏi đơn giản:

> "Nó làm gì?"

Hãy đặt 6 câu hỏi:

```text
1. Nó nằm ở layer nào?

2. Nó giải quyết vấn đề gì?

3. Nó nhận input gì và tạo output gì?

4. Nó chạy ở đâu?
   Browser / OS / Router / ISP / Server?

5. Nó tự động làm gì?
   Tôi phải cấu hình/custom cái gì?

6. Nếu bỏ nó đi thì chuyện gì xảy ra?
```

Ví dụ với **Redis**:

```text
Application layer
       ↓
giải quyết latency/database load
       ↓
Application → Redis
       ↓
cache hit/miss
       ↓
developer quyết định:
key / TTL / invalidation / consistency
```

Với **DNS**:

```text
Naming layer
       ↓
domain → IP
       ↓
resolver/cache/authoritative
       ↓
đa số tự động
       ↓
developer/operator cấu hình:
records / TTL / routing policy
```

Với **VPN**:

```text
Network/security layer
       ↓
encrypted tunnel
       ↓
client ↔ VPN server
       ↓
network path thay đổi
       ↓
client có thể cấu hình,
nhưng routing/tunnel được software tự thực hiện
```

Với **Load Balancer**:

```text
Infrastructure
       ↓
một entry point → nhiều backend
       ↓
health check / load distribution
       ↓
software tự quyết định theo policy
       ↓
operator custom algorithm/configuration
```

Đây là cách tiếp cận sẽ giúp bạn nối được **CS Fundamentals → Networking → Linux → Web Server → PHP-FPM → Laravel → Redis → PostgreSQL → System Design**, thay vì học từng công nghệ như các mảnh kiến thức rời rạc.