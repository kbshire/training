# http

***超文本传输协议\***（HTTP）是一个用于传输超媒体文档（例如 HTML）的[应用层]协议。它是为 Web 浏览器与 Web 服务器之间的通信而设计的，但也可以用于其他目的。HTTP 遵循经典的[客户端—服务端模型]，客户端打开一个连接以发出请求，然后等待直到收到服务器端响应。HTTP 是[无状态协议]，这意味着服务器不会在两个请求之间保留任何数据（状态）。

HTTP 是一种能够获取如 HTML 这样的网络资源的 [protocol]。它是在 Web 上进行数据交换的基础，是一种 **client-server** 协议，也就是说，请求通常是由像浏览器这样的接受方发起的。一个完整的 Web 文档通常是由不同的子文档拼接而成的，像是文本、布局描述、图片、视频、脚本等等。

客户端和服务端通过交换各自的消息（与数据流正好相反）进行交互。由像浏览器这样的客户端发出的消息叫做*请求*（request），被服务端响应的消息叫做*响应*（response）。

<img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/http-layers.png" alt="HTTP as an application layer protocol, on top of TCP (transport layer) and IP (network layer) and below the presentation layer." style="zoom: 33%;" /> 

client

user-agent 就是任何能够为用户发起行为的工具。这个角色通常都是由浏览器来扮演。

浏览器首先发送一个请求来获取页面的 HTML 文档，再解析文档中的资源信息发送其他请求，获取可执行脚本或 CSS 样式来进行页面布局渲染，以及一些其他页面资源（如图片和视频等）。然后，浏览器将这些资源整合到一起，展现出一个完整的文档，也就是网页。浏览器执行的脚本可以在之后的阶段获取更多资源，并相应地更新网页。

server

由 Web Server 来*服务*并提供客户端所请求的文档。Server 只是虚拟意义上代表一个机器：它可以是共享负载（负载均衡）的一组服务器组成的计算机集群，也可以是一种复杂的软件



## http特性

> 1.http是简单的 ： HTTP 大体上还是被设计得简单易读。
>
> 2.http是可扩展的： header中可以很容易的加入新字段实现新的功能
>
> 3.HTTP 是无状态，有会话的：HTTP 是无状态的：在同一个连接中，两个执行成功的请求之间是没有关系的。这就带来了一个问题，用户没有办法在同一个网站中进行连续的交互，比如在一个电商网站里，用户把某个商品加入到购物车，切换一个页面后再次添加了商品，这两次添加商品的请求之间没有关联，浏览器无法知道用户最终选择了哪些商品。而使用 HTTP 的标头扩展，HTTP Cookie 就可以解决这个问题。把 Cookie 添加到标头中，创建一个会话让每次请求都能共享相同的上下文信息，达成相同的状态。**HTTP 本质是无状态的，使用 Cookie 可以创建有状态的会话。**
>
> 4.http是基于TCP的：HTTP 依赖于面向连接的 TCP 进行消息传递。在客户端与服务器能够交互之前，必须在这两者间建立一个 TCP 链接，打开一个 TCP 连接需要多次往返交换消息。
>
> 5.缓存：服务端能告诉代理和客户端哪些文档需要被缓存，缓存多久，而客户端也能够命令中间的缓存代理来加速访问。
>
> 6.同源限制：只有来自于**相同来源**的网页才能够获取网站的全部信息。一般情况下通过host和origin来判断是否同源。而这些字段抓包时又是可以修改的。通过cors，我们可以实现跨域。
>
> 7.认证：使用 Authenticate相似的标头即可。一些页面能够被保护起来，仅让特定的用户进行访问。



## http的交换流程

当客户端想要和服务端进行信息交互时，过程表现为下面几步：

> 1. 打开一个 TCP 连接：TCP 连接被用来发送一条或多条请求，以及接受响应消息。客户端可能打开一条新的连接，或重用一个已经存在的连接，或者也可能开几个新的 TCP 连接连向服务端。
>
> 2. 发送一个 HTTP 报文：HTTP 报文（在 HTTP/2 之前）是语义可读的。在 HTTP/2 中，这些简单的消息被封装在了帧中，这使得报文不能被直接读取。
>
>    ```
>    GET / HTTP/1.1
>    Host: baidu.com
>    ```
>
> 3. 读取服务端返回的报文信息：
>
>    ```
>    HTTP/1.1 200 OK
>    Date: Sat, 09 Oct 2023 14:28:02 GMT
>    Server: Apache
>    Accept-Ranges: bytes
>    Content-Length: 29769
>    Content-Type: text/html
>    ```
>
> 4. 关闭连接或者为后续请求重用连接。

## http报文构成

> 请求由以下元素组成：
>
> - 一个 HTTP 的请求方法，经常是由一个动词像 `GET`、`POST` 或者一个名词像 `OPTIONS`、`HEAD`来定义客户端的动作行为。通常客户端的操作都是获取资源（GET 方法）或者发送 HTML 表单（POST 方法）。
> - 要获取的资源的路径，通常是上下文中就很明显的元素资源的 URL
> - HTTP 协议版本号。
> - 为服务端表达其他信息的可选标头。
> - 对于一些像 POST 这样的方法，报文的主体（body）就包含了发送的资源，这与响应报文的主体类似。

> 响应报文包含了下面的元素：
>
> - HTTP 协议版本号。
> - 一个状态码，来告知对应请求执行成功或失败，以及失败的原因。
> - 一个状态信息，这个信息是非权威的状态码描述信息，可以由服务端自行设定。
> - HTTP 标头，与请求标头类似。
> - 可选项，比起请求报文，响应报文中更常见地包含获取资源的主体。

## http 发展

### 1.http 0.9

HTTP/0.9 极其简单：请求由单行指令构成，以唯一可用方法get。其后跟目标资源的路径（一旦连接到服务器，协议、服务器、端口号这些都不是必须的）。

`GET /1.html`

响应也极其简单的：只包含响应文档本身。

```
<HTML> response! </HTML>
```

HTTP/0.9 的响应内容并不包含 HTTP 头。这意味着只有 HTML 文件可以传送，无法传输其他类型的文件。也没有状态码或错误代码。

### 2.http 1.0

协议版本信息现在会随着每个请求发送（`HTTP/1.0` 被追加到了 `GET` 行）。

状态码会在响应开始时发送，使浏览器能了解请求执行成功或失败，并相应调整行为（如更新或使用本地缓存）。

引入了 HTTP 标头的概念，无论是对于请求还是响应，允许传输元数据，使协议变得非常灵活，更具扩展性。

在新 HTTP 标头的帮助下，具备了传输除纯文本 HTML 文件以外其他类型文档的能力(content-type)

```html
GET /mypage.html HTTP/1.0
User-Agent: NCSA_Mosaic/2.0 (Windows 3.1)

200 OK
Date: Tue, 15 Nov 1994 08:12:31 GMT
Server: CERN/3.0 libwww/2.17
Content-Type: text/html
<HTML>
一个包含图片的页面
  <IMG SRC="/myimage.gif">
</HTML>
```

### http 1.1

连接可以复用，节省了多次打开 TCP 连接加载网页文档资源的时间。

增加管线化技术，允许在第一个应答被完全发送之前就发送第二个请求，以降低通信延迟。

支持响应分块。

引入额外的缓存控制机制。

引入内容协商机制，包括语言、编码、类型等。并允许客户端和服务器之间约定以最合适的内容进行交换。

凭借 `Host`标头，能够使不同域名配置在同一个 IP 地址的服务器上。

```bash
GET /en-US/docs/Glossary/Simple_header HTTP/1.1
Host: developer.mozilla.org
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://developer.mozilla.org/en-US/docs/Glossary/Simple_header

200 OK
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Date: Wed, 20 Jul 2016 10:55:30 GMT
Etag: "547fa7e369ef56031dd3bff2ace9fc0832eb251a"
Keep-Alive: timeout=5, max=1000
Last-Modified: Tue, 19 Jul 2016 00:59:33 GMT
Server: Apache
Transfer-Encoding: chunked
Vary: Cookie, Accept-Encoding

(content)
```

### https

在此基础上创建了一个额外的加密传输层：SSL。SSL 2.0 及其后继者 SSL 3.0 允许通过加密来保证服务器和客户端之间交换消息的真实性。

### http/2

HTTP/2 是二进制协议而不是文本协议。不再可读，也不可无障碍的手动创建，改善的优化技术现在可被实施。

这是一个多路复用协议。并行的请求能在同一个链接中处理，移除了 HTTP/1.x 中顺序和阻塞的约束。

压缩了标头。因为标头在一系列请求中常常是相似的，其移除了重复和传输重复数据的成本。

其允许服务器在客户端缓存中填充数据，通过一个叫服务器推送的机制来提前请求。

### http/3

HTTP/3 有这与 HTTP 早期版本的相同语义，但在传输层部分使用QUIC,而不是TCP

QUIC 旨在为 HTTP 连接设计更低的延迟。类似于 HTTP/2，它是一个多路复用协议，但是 HTTP/2 通过单个 TCP 连接运行，所以在 TCP 层处理的数据包丢失检测和重传可以阻止所有流。QUIC 通过 UDP运行多个流，并为每个流独立实现数据包丢失检测和重传，因此如果发生错误，只有该数据包中包含数据的流才会被阻止。



## http消息

HTTP 请求和响应具有相似的结构，由以下部分组成：

1. 一行起始行用于描述要执行的请求，或者是对应的状态，成功或失败。这个起始行总是单行的。
2. 一个可选的 HTTP 标头集合指明请求或描述消息主体（body）。
3. 一个空行指示所有关于请求的元数据已经发送完毕。
4. 一个可选的包含请求相关数据的*主体*（比如 HTML 表单内容），或者响应相关的文档。主体的大小有起始行的 HTTP 头来指定。

起始行和 HTTP 消息中的 HTTP 头统称为请求头，而其有效负载被称为消息主体。

![Requests and responses share a common structure in HTTP](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/httpmsgstructure2.png) 

### 请求：

#### 起始行

HTTP 请求是由客户端发出的消息，用来使服务器执行动作。*起始行*（start-line）包含三个元素：

> 一个 HTTP 方法，一个动词（像 GET、PUT 或者 POST）或者一个名词（像 HEAD 或者 OPTIONS），描述要执行的动作。例如，GET 表示要获取资源，POST 表示向服务器推送数据（创建或修改资源，或者产生要返回的临时文件）。
>
> *请求目标*（request target），通常是一个 URL，或者是协议、端口和域名的绝对路径，通常以请求的环境为特征。请求的格式因不同的 HTTP 方法而异。它可以是：
>
> - `POST / HTTP/1.1`
> - `GET /background.png HTTP/1.0`
> - `HEAD /test.html?query=alibaba HTTP/1.1`
> - `OPTIONS /anypage.html HTTP/1.0`
> - `GET http://uuu.org/en-US/docs/Web/HTTP/Messages HTTP/1.1`
> - `OPTIONS * HTTP/1.1`

#### 标头

来自请求的 HTTP 标头遵循和 HTTP 标头相同的基本结构：不区分大小写的字符串，紧跟着的冒号（':'）和一个结构取决于标头的值。整个标头（包括值）由一行组成，这一行可以相当长。

> 通用标头（General header），例如 Via，适用于整个消息。
>
> 请求标头（Request header），例如 User-Agent、Accept-Type，通过进一步的定义（例如 Accept-Language）、给定上下文（例如 Referer）或者进行有条件的限制（例如 If-None）来修改请求。
>
> 表示标头（Representation header），例如 Content-Type 描述了消息数据的原始格式和应用的任意编码（仅在消息有主体时才存在）。
>
> ![Example of headers in an HTTP request](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/http_request_headers3.png) 

#### 主体

请求的最后一部分是它的主体。不是所有的请求都有一个主体：例如获取资源的请求，像 `GET`、`HEAD`、`DELETE` 和 `OPTIONS`，通常它们不需要主体。有些请求将数据发送到服务器以便更新数据：常见的的情况是 POST 请求（包含 HTML 表单数据）。

### 响应：

HTTP 响应的起始行被称作状态行（status line），包含以下信息：

#### 状态行

> 协议版本，通常为 HTTP/1.1。
>
> 状态码（status code），表明请求是成功或失败。常见的状态码是 200、404 或 302。
>
> 状态文本（status text）。一个简短的，纯粹的信息，通过状态码的文本描述，帮助人们理解该 HTTP 消息。

#### 标头

> 通用标头（General header），例如 Via，适用于整个消息。
>
> 响应标头（Response header），例如 Vary 和 Accept-Ranges，提供有关服务器的其他信息，这些信息不适合状态行。
>
> 表示标头（Representation header），例如 Content-Type 描述了消息数据的原始格式和应用的任意编码（仅在消息有主体时才存在）。
>
> ![Example of headers in an HTTP response](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/http_response_headers3.png) 

#### 主体

响应的最后一部分是主体。不是所有的响应都有主体：具有状态码（如201或204）的响应，通常不会有主体。



## URL

一般情况下，资源的名称和位置由同一个 URL（统一资源定位符，它是 URI 的一种）来标识。它也被称为 *Web 地址*。

```
https://a.baidu.org
https://a.baidu.org/en-US/docs/Learn/
https://a.baidu.org/en-US/search?q=URL
http://www.example.com:80/path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument
```

### URL的语法

`http://www.example.com:80/path/to/my.html?key1=value1&key2=value2#SomeWhere`

> **协议：**
>
> `http://`告诉浏览器使用何种协议。对于大部分 Web 资源，通常使用 HTTP 协议或其安全版本，HTTPS 协议。另外，浏览器也知道如何处理其他协议。例如， `mailto:` 协议指示浏览器打开邮件客户端；`ftp:`协议指示浏览器处理文件传输。常见的方案有：
>
> ![image-20230324100318367](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230324100318367.png) 
>
>  **主机：**
>
>  `www.example.com` 既是一个域名，也代表管理该域名的机构。它指示了需要向网络上的哪一台主机发起请求。当然，也可以直接向主机的 [IP address](https://developer.mozilla.org/zh-CN/docs/Glossary/IP_Address) 地址发起请求。但直接使用 IP 地址的场景并不常见。
>
> **端口：**
>
> `:80` 是端口。它表示用于访问 Web 服务器上资源的种类。如果访问的该 Web 服务器使用 HTTP 协议的标准端口（HTTP 为 80，HTTPS 为 443）授予对其资源的访问权限，则通常省略此部分。否则端口就是 URI 必须的部分。
>
> **路径：**
>
> `/path/to/myfile.html` 是 Web 服务器上资源的路径。在 Web 的早期，类似这样的路径表示 Web 服务器上的物理文件位置。现在，它主要是由没有任何物理实体的 Web 服务器抽象处理而成的。
>
> **参数：**
>
> `?key1=value1&key2=value2` 是提供给 Web 服务器的额外参数。这些参数是用 & 符号分隔的键/值对列表。Web 服务器可以在将资源返回给用户之前使用这些参数来执行额外的操作。每个 Web 服务器都有自己的参数规则。
>
> **锚点：**
>
> `#SomewhereInTheDocument` 是资源本身的某一部分的一个锚点。锚点代表资源内的一种“书签”，它给予浏览器显示位于该“加书签”点的内容的指示。例如，在 HTML 文档上，浏览器将滚动到定义锚点的那个点上；在视频或音频文档上，浏览器将转到锚点代表的那个时间。 # 号后面的部分，也称为片段标识符，永远不会与请求一起发送到服务器。

## mime类型

mime即响应头中的content-type字段的取值

![image-20230324101213596](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230324101213596.png) 

主要的mime类型

>- `text/plain` 表示文本文件的默认值。一个文本文件应当是人类可读的，并且不包含二进制数据。
>- `application/octet-stream` 表示所有其他情况的默认值。一种未知的文件类型应当使用此类型。浏览器在处理这些文件时会特别小心，试图防止、避免用户的危险行为。
>- `image/gif` 表示图片格式为`gif`的content-type
>- `image/jpeg`
>- `text/html`
>- `image/svg+xml`



## http状态码

> HTTP 响应状态码用来表明特定 HTTP 请求是否成功完成。 响应被归为以下五大类：
>
> 信息响应 (100–199)
>
> ```
> 100 Continue
> 这个临时响应表明，迄今为止的所有内容都是可行的，客户端应该继续请求，如果已经完成，则忽略它。
> 
> 101 Switching Protocols
> 该代码是响应客户端的 Upgrade (en-US) 请求头发送的，指明服务器即将切换的协议。允许将一个已建立的连接升级成新的、不相容的协议。
> 
> 102 Processing (en-US) (WebDAV)
> 此代码表示服务器已收到并正在处理该请求，但当前没有响应可用。
> ```
>
> 成功响应 (200–299)
>
> ```
> 200 OK
> 
> 201 Created
> 该请求已成功，并因此创建了一个新的资源。这通常是在 POST 请求，或是某些 PUT 请求之后返回的响应。
> 
> 202 Accepted
> 请求已经接收到，但还未响应，没有结果。意味着不会有一个异步的响应去表明当前请求的结果，预期另外的进程和服务去处理请求或者批处理。
> ```
>
> 重定向消息 (300–399)
>
> ```
> 300 Multiple Choice
> 请求拥有多个可能的响应。用户代理或者用户应当从中选择一个。（没有标准化的方法来选择其中一个响应，但是建议使用指向可能性的 HTML 链接，以便用户可以选择。）
> 
> 301 Moved Permanently
> 请求资源的 URL 已永久更改。在响应中给出了新的 URL。
> 
> 302 Found
> 此响应代码表示所请求资源的 URI 已 暂时 更改。未来可能会对 URI 进行进一步的改变。因此，客户机应该在将来的请求中使用这个相同的 URI。
> ```
>
> 客户端错误响应 (400–499)
>
> ```
> 400 Bad Request
> 由于被认为是客户端错误（例如，错误的请求语法、无效的请求消息帧或欺骗性的请求路由），服务器无法或不会处理请求。
> 
> 401 Unauthorized
> 虽然 HTTP 标准指定了"unauthorized"，但从语义上来说，这个响应意味着"unauthenticated"。也就是说，客户端必须对自身进行身份验证才能获得请求的响应。
> 
> 402 Payment Required 实验性
> 此响应代码保留供将来使用。创建此代码的最初目的是将其用于数字支付系统，但是此状态代码很少使用，并且不存在标准约定。
> 
> 403 Forbidden
> 客户端没有访问内容的权限；也就是说，它是未经授权的，因此服务器拒绝提供请求的资源。与 401 Unauthorized 不同，服务器知道客户端的身份。
> 
> 404 Not Found
> 服务器找不到请求的资源。在浏览器中，这意味着无法识别 URL。在 API 中，这也可能意味着端点有效，但资源本身不存在。服务器也可以发送此响应，而不是 403 Forbidden，以向未经授权的客户端隐藏资源的存在。这个响应代码可能是最广为人知的，因为它经常出现在网络上。
> ```
>
> 服务端错误响应 (500–599)
>
> ```
> 500 Internal Server Error
> 服务器遇到了不知道如何处理的情况。
> 
> 501 Not Implemented
> 服务器不支持请求方法，因此无法处理。服务器需要支持的唯二方法（因此不能返回此代码）是 GET and HEAD.
> 
> 502 Bad Gateway
> 此错误响应表明服务器作为网关需要得到一个处理这个请求的响应，但是得到一个错误的响应。
> 
> 503 Service Unavailable
> 服务器没有准备好处理请求。常见原因是服务器因维护或重载而停机。请注意，与此响应一起，应发送解释问题的用户友好页面。这个响应应该用于临时条件和如果可能的话，HTTP 标头 Retry-After 字段应该包含恢复服务之前的估计时间。网站管理员还必须注意与此响应一起发送的与缓存相关的标头，因为这些临时条件响应通常不应被缓存。
> 
> 504 Gateway Timeout
> 当服务器充当网关且无法及时获得响应时，会给出此错误响应。
> ```

## http header

**HTTP 标头**（header）允许客户端和服务器通过 HTTP 请求（request）或者响应（response）传递附加信息。一个 HTTP 标头由它的名称（不区分大小写）后跟随一个冒号（`:`），冒号后跟随它具体的值。

根据不同的消息上下文，标头可以分为：

- **请求标头**包含有关要获取的资源或客户端或请求资源的客户端的更多信息。
- **响应标头**包含有关响应的额外信息，例如响应的位置或者提供响应的服务器。
- **表示标头**包含资源主体的信息，例如主体的 MIME 类型或者应用的编码/压缩方案。
- **有效负荷标头**包含有关有效载荷数据表示的单独信息，包括内容长度和用于传输的编码。



端到端（End-to-end）标头

这类标头*必须*被传输到最终的消息接收者：请求的服务器或者响应的客户端。中间的代理必须重新转发这些未经修改的标头，并且必须缓存它们。

逐跳（Hop-by-hop）标头

这类标头仅对单次传输连接有意义，并且*不得由*代理重传或者缓存。注意，只能使用 `Connection` 标头来设置逐跳标头。



### 缓存header

> [`Age`]
>
> 对象在代理缓存中的时间（以秒为单位）。
>
> [`Cache-Control`]
>
> 请求和响应中缓存机制的指令。
>
> [`Clear-Site-Data`]
>
> 清除与请求网站相关联的浏览器数据（例如 cookie、storage、cache）。
>
> [`Expires`]
>
> 响应被视为过时的日期/时间。
>
> [`Pragma`]
>
> 特定于实现的标头可能会在请求-响应链（request-response chain）的任何地方产生各种影响。用于向后兼容 `Cache-Control` 标头尚不存在的 HTTP/1.0 缓存。

### 用户代理客户端提示

[用户代理客户端提示]是请求标头，其提供有关用户代理、它运行的平台/架构以及在用户代理或平台上设置的用户首选项信息：

> Sec-CH-Prefers-Reduced-Motion (en-US) 
> 用户代理的减少动画运动首选项设置。
>
> Sec-CH-UA 
> 用户代理的品牌（brand）和版本。
>
> Sec-CH-UA-Arch 
> 用户代理的底层平台架构。
>
> Sec-CH-UA-Bitness 
> 用户代理的底层 CPU 架构位数（例如“64”位）。
>
> Sec-CH-UA-Full-Version 
> 用户代理的完整语义版本字符串。
>
> Sec-CH-UA-Full-Version-List 
> 用户代理品牌（brand）列表中每个品牌的完整版本。
>
> Sec-CH-UA-Mobile (en-US) 
> 用户代理是否在手机设备上运行，或者更一般地说，更偏好“手机”用户体验。
>
> Sec-CH-UA-Model (en-US) 
> 用户代理的设备模型。
>
> Sec-CH-UA-Platform (en-US) 
> 用户代理的底层操作系统/平台。
>
> Sec-CH-UA-Platform-Version (en-US) 
> 用户代理的底层操作系统版本。
>
> ```
> sec-ch-ua: "Microsoft Edge";v="111", "Not(A:Brand";v="8", "Chromium";v="111"
> sec-ch-ua-mobile: ?0
> sec-ch-ua-platform: "Windows"
> ```

### 条件

> Last-Modified
> 资源的最后修改日期，用于比较同一个资源的多个版本。它不如 ETag 准确，但在某些环境中更容易计算。使用 If-Modified-Since 和 If-Unmodified-Since 的条件请求可以使用此值来更改请求的行为。
>
> ETag
> **标识资源版本的唯一字符串**。使用 If-Match 和 If-None-Match 的条件请求使用此值来更改请求的行为。
>
> If-Match
> 使请求有条件，并且仅当存储的资源与给定的 ETag 之一匹配时才应用该方法。
>
> If-None-Match
> 使请求有条件，并且仅当存储的资源与给定的 ETag 都不匹配时才应用该方法。这用于更新缓存（用于安全请求），或防止在资源已存在时上传新资源。
>
> If-Modified-Since
> 使请求有条件，并期望只有在给定日期后修改资源时才请求传输资源。仅当缓存过期时才用于传输数据。
>
> If-Unmodified-Since
> 使请求有条件，并期望只有在给定日期后资源未被修改时才请求传输资源。这确保了特定范围的新片段与先前片段的一致性，或者在修改现有文档时实现乐观的（optimistic）并发控制系统。
>
> Vary
> 确定如何匹配请求标头以决定**是否可以使用缓存的响应而不是从源服务器请求新的响应。**
>
> ```
> etag: W/"fbedb4e6bbd9a63237f481b1d785dd6b"
> vary: Accept-Encoding
> if-modified-since: Fri, 24 Mar 2023 00:59:26 GMT
> if-none-match: W/"fbedb4e6bbd9a63237f481b1d785dd6b"
> ```

### 连接管理

> Connection
> 控制当前事务完成后网络连接是否保持打开状态。
>
> Keep-Alive
> 控制持久连接应保持打开状态的时间。

### 内容协商

> Accept
> 通知服务器可以发回的数据类型。
>
> Accept-Encoding
> 编码算法，通常是压缩算法，用于返回的资源。
>
> Accept-Language
> 通知服务器有关服务器预期返回的人类语言。这是一个提示，不一定在用户的完全控制之下

### cookie 

> Cookie
> 包含先前由服务器使用 Set-Cookie 标头发送存储的 HTTP cookie。
>
> Set-Cookie
> 将 cookie 从服务器发送到用户代理。
>
> ```
> cookie: _ga=GA1.2.2039740383.1663417057; _gid=GA1.2.901079682.1679623065; preferredlocale=zh-CN
> ```

### 消息主体类型

> Content-Length
> 资源的大小，以十进制字节数表示。
>
> Content-Type
> 指示资源的媒体类型。
>
> Content-Encoding
> 用于指定压缩算法。
>
> Content-Language
> 描述面向受众的人类语言，以便用户可以根据自己的首选语言进行区分。
>
> Content-Location
> 指示返回数据的备用位置。
>
> ```
> content-encoding: gzip
> content-language: zh-CN
> content-type: text/html;charset=utf-8
> ```

### 代理

> Forwarded
> 包含来自代理服务器面向客户端的信息，当请求路径中涉及代理时，这些信息会被更改或丢失。
>
> X-Forwarded-For 非标准
> 标识通过 HTTP 代理或负载均衡器（load balancer）连接到 Web 服务器的客户端的原始 IP 地址。
>
> X-Forwarded-Host 非标准
> 标识请求客户端用于连接到你的代理或负载均衡器（load balancer）的原始主机。
>
> X-Forwarded-Proto 非标准
> 标识客户端用于连接到你的代理或负载均衡器（load balancer）的协议（HTTP 或 HTTPS）。
>
> Via
> 由代理添加，包括正向和反向代理，并且可以出现在请求标头和响应标头中。
>
> ```
> via: 1.1 af9d66efe7802df1efbc8106c86a13e6.cloudfront.net (CloudFront)
> x-forwarded-for: 127.0.0.1
> ```

### reloacation

> Location
> 指示要将页面重定向到的 URL。
>
> Refresh
> 指示浏览器重新加载页面或重定向到另一个页面。采用与带有 http-equiv="refresh" 的 meta 元素相同的值。

### 请求上下文

> Host
> 指定服务器的域名（用于虚拟主机）和（可选）服务器侦听的 TCP 端口号。
>
> Referer
> 前一个网页的地址，表示从该网页链接（进入）到当前请求的页面。
>
> Referrer-Policy
> 管理 Referer 标头中发送的哪些引用信息应包含在发出的请求中。
>
> User-Agent
> 包含一个特征字符串，允许网络协议对端识别发起请求的用户代理软件的应用程序类型、操作系统、软件供应商或软件版本。

### 安全

> Cross-Origin-Embedder-Policy（COEP）
> 允许服务器为给定文档声明嵌入器（embedder）策略。
>
> Cross-Origin-Embedder-Policy（COEP）
> 允许服务器为给定文档声明嵌入器（embedder）策略。
>
> Cross-Origin-Embedder-Policy（COEP）
> 允许服务器为给定文档声明嵌入器（embedder）策略。
>
> X-Frame-Options (XFO)
> 指示是否应允许浏览器在 `<frame>`、`<iframe>`、`<embed> `或 `<object> `中呈现页面。
>
> X-XSS-Protection
> 启用跨站点脚本过滤。
>
> ```
> x-cache: Hit from cloudfront
> x-content-type-options: nosniff
> x-frame-options: DENY
> x-xss-protection: 1; mode=block
> ```

### fetch元数据请求标头

> 这允许服务器根据请求的来源和资源将如何使用来决定是否允许请求。
>
> Sec-Fetch-Site
> 它是一个请求标头，指示请求发起者的源与其目标源之间的关系。它是一个结构化标头（Structured Header），其值是一个标记，可能的值有 cross-site、same-origin、same-site 和 none。
>
> Sec-Fetch-Mode
> 它是一个请求标头，向服务器指示请求的模式。它是一个结构化标头（Structured Header），其值是一个标记，可能的值有 cors、navigate、no-cors、same-origin 和 websocket。
>
> Sec-Fetch-User
> 它是一个请求标头，指示导航请求是否由用户激活触发。它是一个结构化标头（Structured Header），其值为布尔值，因此可能的值为 ?0 表示 false，?1 表示true。
>
> Sec-Fetch-Dest
> 它是一个请求标头，指示请求到服务器的目的地。它是一个结构化标头（Structured Header），其值为具有可能值的标记 audio、audioworklet、document、embed、empty、font、image、manifest、object、paintworklet、report、script、serviceworker、sharedworker、style、track、video、worker 和 xslt。
>
> Service-Worker-Navigation-Preload (en-US)
> 在 service worker 启动期间以抢占式请求发送到 fetch() 资源的请求标头。该值由 NavigationPreloadManager.setHeaderValue() (en-US) 设置，可用于通知服务器应返回与正常 fetch() 操作不同的资源。
>
> ```
> sec-fetch-dest: document
> sec-fetch-mode: navigate
> sec-fetch-site: same-origin
> sec-fetch-user: ?1
> ```



## http请求方法

> HTTP 定义了一组**请求方法**，以表明要对给定资源执行的操作。
>
> ```
> GET
> GET 方法请求一个指定资源的表示形式，使用 GET 的请求应该只被用于获取数据。
> 
> HEAD
> HEAD 方法请求一个与 GET 请求的响应相同的响应，但没有响应体。
> 
> POST
> POST 方法用于将实体提交到指定的资源，通常导致在服务器上的状态变化或副作用。
> 
> PUT
> PUT 方法用请求有效载荷替换目标资源的所有当前表示。
> 
> DELETE
> DELETE 方法删除指定的资源。
> 
> CONNECT
> CONNECT 方法建立一个到由目标资源标识的服务器的隧道。
> 
> OPTIONS
> OPTIONS 方法用于描述目标资源的通信选项。
> 
> TRACE
> TRACE 方法沿着到目标资源的路径执行一个消息环回测试。
> 
> PATCH
> PATCH 方法用于对资源应用部分修改。
> ```

## http连接管理

### 短连接

HTTP 最早期的模型和 HTTP/1.0 的默认模型，是短连接。每一个 HTTP 请求都由它自己独立的连接完成；这意味着发起每一个 HTTP 请求之前都会有一次 TCP 握手，而且是连续不断的。

这是 HTTP/1.0 的默认模型（如果没有指定 `Connection`协议头，或者是值被设置为 `close`）。而在 HTTP/1.1 中，只有当 `Connection`被设置为 `close` 时才会用到这个模型。

### 长连接

一个长连接会保持一段时间，重复用于发送一系列请求，节省了新建 TCP 连接握手的时间，还可以利用 TCP 的性能增强能力。当然这个连接也不会一直保留着：连接在空闲一段时间后会被关闭（服务器可以使用 `Keep-Alive`协议头来指定一个最小的连接保持时间）。

长连接也还是有缺点的；就算是在空闲状态，它还是会消耗服务器资源，而且在重负载时，还有可能遭受 DoS 攻击。这种场景下，可以使用非长连接，即尽快关闭那些空闲的连接，也能对性能有所提升。

HTTP/1.0 里默认并不使用长连接。把 `Connection`设置成 `close` 以外的其他参数都可以让其保持长连接，通常会设置为 `retry-after`。

### 流水线

默认情况下，HTTP 请求是按顺序发出的。下一个请求只有在当前请求收到响应过后才会被发出。由于会受到网络延迟和带宽的限制，在下一个请求被发送到服务器之前，可能需要等待很长时间。

流水线是在同一条长连接上发出连续的请求，而不用等待应答返回。这样可以避免连接延迟。理论上讲，性能还会因为两个 HTTP 请求有可能被打包到一个 TCP 消息包中而得到提升。

并不是所有类型的 HTTP 请求都能用到流水线：只有比如 `GET`、`PUT`和 `DELETE`能够被安全地重试。

<img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/http1_x_connections.png" alt="Compares the performance of the three HTTP/1.x connection models: short-lived connections, persistent connections, and HTTP pipelining." style="zoom: 67%;" /> 

> 要注意的一个重点是 HTTP 的连接管理适用于两个连续节点之间的连接，它是逐跳（Hop-by-hop）标头，而不是端到端（End-to-end）标头。当模型用于从客户端到第一个代理服务器的连接和从代理服务器到目标服务器之间的连接时（或者任意中间代理）效果可能是不一样的。HTTP 协议头受不同连接模型的影响，比如 Connection 和 Keep-Alive，就是逐跳标头标头，它们的值是可以被中间节点修改的。



## 协议升级

如果服务器决定升级这次连接，就会返回一个 `101 Switching Protocols`响应状态码，和一个要切换到的协议的标头字段 Upgrade。如果服务器没有（或者不能）升级这次连接，它会忽略客户端发送的 `Upgrade` 标头字段，返回一个常规的响应：例如一个 `200 OK`).

在发送 `101` 状态码之后，服务器可以使用新协议，并根据需要执行任何额外的特定于协议的握手。实际上，一旦这次升级完成了，连接就变成了双向管道。并且可以通过新协议完成启动升级的请求。

客户端使用 `Upgrade` (en-US)标头字段请求服务器，以降序优先的顺序切换到其中列出的一个协议。

因为 `Upgrade` 是一个逐跳（Hop-by-hop）标头，它还需要在 `Connection`标头字段中列出。这意味着包含 Upgrade 的典型请求类似于：

```
GET /index.html HTTP/1.1
Host: www.example.com
Connection: upgrade
Upgrade: example/1, foo/2
```



## http身份验证

RFC 7235定义了一个 HTTP 身份验证框架，服务器可以用来质询（challenge）客户端的请求，客户端则可以提供身份验证凭据。

质询与响应的工作流程如下：

> 服务器端向客户端返回 401（Unauthorized，未被授权的）响应状态码，并在 WWW-Authenticate 响应标头提供如何进行验证的信息，其中至少包含有一种质询方式。
>
> 之后，想要使用服务器对自己身份进行验证的客户端，可以通过包含凭据的 Authorization 请求标头进行验证。
>
> 通常，客户端会向用户显示密码提示，然后发送包含正确的 Authorization 标头的请求。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/http-auth-sequence-diagram.png) 



### nginx 访问限制和 basic 认证
在 nginx 配置中，你需要指定一个要保护的 location 并且 auth_basic 指令提供密码保护区域的名称。

auth_basic_user_file 指令指定包含加密的用户凭据 .htpasswd 文件

```
location /status {
    auth_basic           "Access to the staging site";
    auth_basic_user_file /etc/apache2/.htpasswd;
}
```



## http数据压缩

**数据压缩**是提高 Web 站点性能的一种重要手段。

在实际应用时，web 开发者不需要亲手实现压缩机制，浏览器及服务器都已经将其实现了，不过他们需要确保在服务器端进行了合理的配置。

数据压缩会在三个不同的层面发挥作用：

- 首先某些格式的**文件**会采用特定的优化算法进行压缩，
- 其次在 **HTTP 协议层面**会进行通用数据加密，即数据资源会以压缩的形式进行端到端传输，
- 最后数据压缩还会发生在**网络连接层面**，即发生在 HTTP 连接的两个节点之间。

### 文件格式压缩

每一种文件类型都会存有冗余，也就是*浪费的空间*。不同于文本文件，这些其他类型的媒体文件占据的空间也更大，所以很早就出现了回收这些浪费的空间的需求。

用于文件的压缩算法可以大致分为两类：无损压缩、有损压缩

有损压缩通常会比无损压缩效率更高一些。

### 端到端压缩

对于各种压缩手段来说，端到端压缩技术是 Web 站点性能提升最大的地方。端到端压缩技术指的是消息主体的压缩是在服务器端完成的，并且在传输过程中保持不变，直到抵达客户端。不管途中遇到什么样的中间节点，它们都会使消息主体保持原样。

![服务器通过网络节点向客户端发送一个压缩的 HTTP 主体。该主体直到到达客户端之前，不会在网络中的任何一跳之间进行解压缩。](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/httpenco1.png) 

今只有两种算法有着举足轻重的地位：`gzip` 应用最广泛，`br` 则是新的挑战者。

![客户端使用“Accept-Encoding:br, gzip”标头请求内容。服务器使用 Brotli 算法压缩的主体以及所需的“Content-Encoding”和“Vary”标头进行响应。](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/httpcompression1.png) 

### 逐跳压缩

即这里的压缩指的不是对源头服务器上的资源的压缩，而是对客户端与服务器端之间的任意两个节点之间传递的消息的主体的压缩。在两个相邻的中间节点之间的连接上，可能会应用*不同*的压缩方式。

![客户端从没有压缩相关标头的服务器请求内容。服务器会使用未经压缩的主体进行响应。该主体在到达客户端之前，由网络上的节点进行压缩和解压缩。](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/httpcomp2.png) 

在实际应用中，逐跳压缩对于服务器和客户端来说是不可见的，并且很少使用。



## cookie

HTTP Cookie是服务器发送到用户浏览器并保存在本地的一小块数据。浏览器会存储 cookie 并在下次向同一服务器再发起请求时携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自同一浏览器——如保持用户的登录状态。Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能。

Cookie 主要用于以下三个方面：

> - 会话状态管理
>
>   如用户登录状态、购物车、游戏分数或其他需要记录的信息
>
> - 个性化设置
>
>   如用户自定义设置、主题和其他设置
>
> - 浏览器行为跟踪
>
>   如跟踪分析用户行为等

### 创建cookie

> 服务器收到 HTTP 请求后，服务器可以在响应标头里面添加一个或多个 Set-Cookie 选项。浏览器收到响应后通常会保存下 Cookie，并将其放在 HTTP Cookie 标头内，向同一服务器发出请求时一起发送。你可以指定一个过期日期或者时间段之后，不能发送 cookie。

服务器使用 `Set-Cookie` 响应头部向用户代理（一般是浏览器）发送 Cookie 信息。

```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```

现在，对该服务器发起的每一次新请求，浏览器都会将之前保存的 Cookie 信息通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 请求头部再发送给服务器。

```
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

### cookie生命周期

> Cookie 的生命周期可以通过两种方式定义：
>
> - *会话期* Cookie 会在当前的会话结束之后删除。浏览器定义了“当前会话”结束的时间，一些浏览器重启时会使用*会话恢复*。这可能导致会话 cookie 无限延长。
>
> - *持久性* Cookie 在过期时间（`Expires`）指定的日期或有效期（`Max-Age`）指定的一段时间后被删除。
>
>   ```
>   Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2023 07:28:00 GMT
>   ```

### 限制cookie访问

有两种方法可以确保 `Cookie` 被安全发送，并且不会被意外的参与者或脚本访问：`Secure` 属性和 `HttpOnly` 属性。

> 标记为 Secure 的 Cookie 只应通过被 HTTPS 协议加密过的请求发送给服务端。它永远不会使用不安全的 HTTP 发送（本地主机除外），这意味着中间人攻击者无法轻松访问它。不安全的站点（在 URL 中带有 http:）无法使用 Secure 属性设置 cookie。但是，Secure 不会阻止对 cookie 中敏感信息的访问。
>
> JavaScript Document.cookie API 无法访问带有 HttpOnly 属性的 cookie；此类 Cookie 仅作用于服务器。例如，持久化服务器端会话的 Cookie 不需要对 JavaScript 可用，而应具有 HttpOnly 属性。此预防措施有助于缓解跨站点脚本（XSS）攻击。
>
> ```
> Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
> ```

### samesite

`SameSite`属性允许服务器指定是否/何时通过跨站点请求发送

这提供了一些针对跨站点请求伪造攻击的保护。它采用三个可能的值：`Strict`、`Lax` 和 `None`。

使用 `Strict`，cookie 仅发送到它来源的站点。`Lax` 与 Strict 相似，只是在用户*导航*到 cookie 的源站点时发送 cookie。例如，通过跟踪来自外部站点的链接。`None` 指定浏览器会在同站请求和跨站请求下继续发送 cookie，但*仅在安全的上下文中*（即，如果 `SameSite=None`，且还必须设置 `Secure` 属性）。如果没有设置 `SameSite` 属性，则将 cookie 视为 `Lax`。

> ```
> Set-Cookie: mykey=myvalue; SameSite=Strict
> ```

### cookie 和 session区别

```
1.Cookie 的工作原理
（1）浏览器端第一次发送请求到服务器端

（2）服务器端创建 Cookie，该 Cookie 中包含用户的信息，然后将该 Cookie 发送到浏览器端

（3）浏览器端再次访问服务器端时会携带服务器端创建的 Cookie

（4）服务器端通过 Cookie 中携带的数据区分不同的用户

————————————————

2.Session 的工作原理

（1）浏览器端第一次发送请求到服务器端，服务器端创建一个 Session，同时会创建一个特殊的 Cookie（name 为 JSESSIONID 的固定值，value 为 session 对象的 ID），然后将该 Cookie 发送至浏览器端

（2）浏览器端发送第 N（N>1）次请求到服务器端，浏览器端访问服务器端时就会携带该 name 为 JSESSIONID 的 Cookie 对象

（3）服务器端根据 name 为 JSESSIONID 的 Cookie 的 value (sessionId), 去查询 Session 对象，从而区分不同用户。

name 为 JSESSIONID 的 Cookie 不存在（关闭或更换浏览器），返回 1 中重新去创建 Session 与特殊的 Cookie

name 为 JSESSIONID 的 Cookie 存在，根据 value 中的 SessionId 去寻找 session 对象

value 为 SessionId 不存在 **（Session 对象默认存活 30 分钟）**，返回 1 中重新去创建 Session 与特殊的 Cookie

value 为 SessionId 存在，返回 session 对象

————————————————

(1) cookie 数据存放在客户的浏览器上，session 数据放在服务器上，但是服务端的 session 的实现对客户端的 cookie 有依赖关系的；

(2) cookie 不是很安全，别人可以分析存放在本地的 COOKIE 并进行 COOKIE 欺骗，如果主要考虑到安全应当使用 session

(3) session 会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果主要考虑到减轻服务器性能方面，应当使用 COOKIE

(4) 单个 cookie 在客户端的限制是 3K，就是说一个站点在客户端存放的 COOKIE 不能 3K。

(5) 所以：将登陆信息等重要信息存放为 SESSION; 其他信息如果需要保留，可以放在 COOKIE 中
```

