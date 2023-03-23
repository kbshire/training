# 当你输入baidu.com发生了什么

```
1.首先在浏览器地址栏中输入URL或者域名。

2.电脑开始域名解析：
看查找IP是否是本机。不是再去查询浏览器缓存，本机hosts文件，如果有对应的IP则进行反馈，然后进行下一步。如果本机Hosts文件不含有，则本机向发出一个DNS请求到本地DNS服务器。
DNS请求到达本地DNS服务器之后，本地DNS服务器会首先查询它的缓存记录，如果缓存中有此条记录，就可以直接返回结果。如果没有，本地DNS服务器还要向DNS根服务器进行查询。
根DNS服务器没有记录具体的域名和IP地址的对应关系，而是告诉本地DNS服务器，你可以到域服务器上去继续查询，并给出域服务器的地址。
最后，本地DNS服务器向域名的解析服务器发出请求，这时就能收到一个域名和IP地址对应关系，本地DNS服务器不仅要把IP地址返回给用户电脑，还要把这个对应关系保存在缓存中，以备下次别的用户查询时，可以直接返回结果，加快网络访问。
网址真正的解析过程为:  . -> .com -> Microsoft.com. -> www.Microsoft.com. 

3.进行tcp三次握手

4.http/https请求和返回
HTTP报文是包裹在TCP报文中发送的，服务器端收到TCP报文时会解包提取出HTTP报文。
https = http + tls
浏览器对服务器发出HTTP请求报文
发送HTTP请求的过程就是构建HTTP请求报文并通过TCP协议中发送到服务器指定端口(HTTP协议80/8080, HTTPS协议443)。HTTP请求报文是由三部分组成: 请求行, 请求报头和请求正文。
服务器处理请求并返回HTTP响应报文
后端从在固定的端口接收到TCP报文开始，这一部分对应于编程语言中的socket。它会对TCP连接进行处理，对HTTP协议进行解析，并按照报文格式进一步封装成HTTP Request对象，供上层使用。HTTP响应报文也是由三部分组成: 状态码, 响应报头和响应报文。

5.浏览器渲染页面
浏览器在收到HTML,CSS,JS文件后，浏览器是一个边解析边渲染的过程。首先浏览器解析HTML文件构建DOM树，然后解析CSS文件构建渲染树，等到渲染树构建完成后，浏览器开始布局渲染树并将其绘制到屏幕上。
```

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20161222114949313) 

```
1、在浏览器中输入 www . qq .com 域名，操作系统会先检查自己本地的 hosts 文件是否有这个网址映射关系，如果有，就先调用这个 IP 地址映射，完成域名解析。
2、如果 hosts 里没有这个域名的映射，则查找本地 DNS 解析器缓存，是否有这个网址映射关系，如果有，直接返回，完成域名解析。
3、如果 hosts 与本地 DNS 解析器缓存都没有相应的网址映射关系，首先会找 TCP/ip 参数中设置的首选 DNS 服务器，在此我们叫它本地 DNS 服务器，此服务器收到查询时，如果要查询的域名，包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析，此解析具有权威性。
4、如果要查询的域名，不由本地 DNS 服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个 IP 地址映射，完成域名解析，此解析不具有权威性。
5、如果本地 DNS 服务器本地区域文件与缓存解析都失效，则根据本地 DNS 服务器的设置（是否设置转发器）进行查询，如果未用转发模式，本地 DNS 就把请求发至 13 台根 DNS，根 DNS 服务器收到请求后会判断这个域名 (.com) 是谁来授权管理，并会返回一个负责该顶级域名服务器的一个 IP。本地 DNS 服务器收到 IP 信息后，将会联系负责.com 域的这台服务器。这台负责.com 域的服务器收到请求后，如果自己无法解析，它就会找一个管理.com 域的下一级 DNS 服务器地址 (http://qq.com) 给本地 DNS 服务器。当本地 DNS 服务器收到这个地址后，就会找 http://qq.com 域服务器，重复上面的动作，进行查询，直至找到 www . qq .com 主机。
6、如果用的是转发模式，此 DNS 服务器就会把请求转发至上一级 DNS 服务器，由上一级服务器进行解析，上一级服务器如果不能解析，或找根 DNS 或把转请求转至上上级，以此循环。不管是本地 DNS 服务器用是是转发，还是根提示，最后都是把结果返回给本地 DNS 服务器，由此 DNS 服务器再返回给客户机。
从客户端到本地 DNS 服务器是属于递归查询，而 DNS 服务器之间就是的交互查询就是迭代查询。
```

```
（1）递归查询
递归查询是一种DNS 服务器的查询模式，在该模式下DNS 服务器接收到客户机请求，必须使用一个准确的查询结果回复客户机。如果DNS 服务器本地没有存储查询DNS 信息，那么该服务器会询问其他服务器，并将返回的查询结果提交给客户机。

（2）迭代查询
DNS 服务器另外一种查询方式为迭代查询，DNS 服务器会向客户机提供其他能够解析查询请求的DNS 服务器地址，当客户机发送查询请求时，DNS 服务器并不直接回复查询结果，而是告诉客户机另一台DNS 服务器地址，客户机再向这台DNS 服务器提交请求，依次循环直到返回查询的结果为止。
```

```
1xx：信息性状态码，表示服务器已接收了客户端请求，客户端可继续发送请求。

    100 Continue

    101 Switching Protocols

 2xx：成功状态码，表示服务器已成功接收到请求并进行处理。

    200 OK 表示客户端请求成功

    204 No Content 成功，但不返回任何实体的主体部分

    206 Partial Content 成功执行了一个范围（Range）请求

3xx：重定向状态码，表示服务器要求客户端重定向。

    301 Moved Permanently 永久性重定向，响应报文的Location首部应该有该资源的新URL

    302 Found 临时性重定向，响应报文的Location首部给出的URL用来临时定位资源

    303 See Other 请求的资源存在着另一个URI，客户端应使用GET方法定向获取请求的资源

    304 Not Modified 服务器内容没有更新，可以直接读取浏览器缓存

     307 Temporary Redirect 临时重定向。与302 Found含义一样。302禁止POST变换为GET，但实际使用时并不一定，307则更多浏览器可能会遵循这一标准，但也依赖于浏览器具体实现

 4xx：客户端错误状态码，表示客户端的请求有非法内容。

       400 Bad Request 表示客户端请求有语法错误，不能被服务器所理解

       401 Unauthonzed 表示请求未经授权，该状态代码必须与 WWW-Authenticate 报头域一起使用

       403 Forbidden 表示服务器收到请求，但是拒绝提供服务，通常会在响应正文中给出不提供服务的原因

       404 Not Found 请求的资源不存在，例如，输入了错误的URL

5xx：服务器错误状态码，表示服务器未能正常处理客户端的请求而出现意外错误。

        500 Internel Server Error 表示服务器发生不可预期的错误，导致无法完成客户端的请求

        503 Service Unavailable 表示服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常
```

![image-20230221195148254](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230221195148254.png) 

```
<?php
	header("Location: c.php",true,301);
```

```c
请求 URL: https://mp.weixin.qq.com
请求方法: GET
状态代码: 200 
远程地址: 180.163.210.81:443
引用者策略: strict-origin-when-cross-origin
cache-control: public, max-age=500
content-encoding: gzip
content-security-policy: script-src 'self' 'unsafe-inline' 'unsafe-eval' 
content-security-policy-report-only: script-src 'self' 'unsafe-inline' 'unsafe-eval' 
content-type: text/html; charset=UTF-8
content-type: text/html; charset=UTF-8
expires: Wed, 22 Mar 2023 11:47:01 +0800
last-modified: Wed, 22 Mar 2023 11:38:41 +0800
logicret: 0
mmlas-verifyresult: CAEoADCEggg=
retkey: 14
set-cookie: rewardsn=; Path=/
set-cookie: payforreadsn=EXPIRED; Path=/; Expires=Tue, 21-Mar-2023 03:38:41 GMT; HttpOnly
set-cookie: wxtokenkey=777; Path=/; HttpOnly
strict-transport-security: max-age=0
:authority: mp.weixin.qq.com
:method: GET
:path: /s?__biz=MzAxNDMwMTMwMw==&amp;mid=2247488981&amp;idx=1&amp;sn=cddfdfe93d7d90d463b3f81cae56ecd2&amp;chksm=9b9434cdace3bddbcc19fe6740f0e6a1ac33b5f29f7bdb9f1f7861b761065fff85c4778af562&amp;scene=0&amp;key=daf5926c99d6695fd0757e2ef09b95c76f593895f3fa1827bcf1b6894f31e6d71e50ed8c537cffd881ff0081b069c62df345a6d61b99802e6fcd83e98dc6a57c65af10d81dbb0c1be13406d18cd8664a&amp;ascene=14&amp;uin=MjEzOTc2MjYzMQ%3D%3D&amp;devicetype=Windows+10&amp;version=62060728&amp;lang=zh_CN&amp;pass_ticket=YVYQuTg%2FBMTYHxQounqWoDVtpcueDJYHWXh4IkXedW%2BAo2tjpP7U4AaduEI%2BEga%2F
:scheme: https
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7*/
accept-encoding: gzip, deflate, br
accept-language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
cache-control: max-age=0
cookie: RK=ELeg13s1ck; ptcz=50951ecaab060724178b85a5355d058b5b1f14afcbea40700ab3bbd687b411f6;
if-modified-since: Wed, 22 Mar 2023 11:29:23 +0800
referer: https://hd.nowcoder.com/
sec-ch-ua: "Microsoft Edge";v="111", "Not(A:Brand";v="8", "Chromium";v="111"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
sec-fetch-dest: document
sec-fetch-mode: navigate
sec-fetch-site: cross-site
sec-fetch-user: ?1
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/111.0.0.0 Safari/537.36 Edg/111.0.1661.44
x-forwarded-for: 127.0.0.1
```

```
POST /sqli/Less-11/ HTTP/1.1
Host: 127.0.0.1:18888
Content-Length: 37
Cache-Control: max-age=0
sec-ch-ua: "Chromium";v="97", " Not;A Brand";v="99"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: http://127.0.0.1:18888
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://127.0.0.1:18888/sqli/Less-11/
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: PHPSESSID=15pq9733sklf0ut1eimv39q1a7; ZDEDebuggerPresent=php,phtml,php3
Connection: close

uname=hhhh&passwd=ggggg&submit=Submit

包含着我们需要的一些具体信息，比如cookie，html,image，后端返回的请求数据等等。这里需要注意，响应正文和响应头之间有一行空格，表示响应头的信息到空格为止
```

```
服务器处理请求
后端从在固定的端口接收到TCP报文开始，它会对TCP连接进行处理，对HTTP协议进行解析，并按照报文格式进一步封装成HTTP Request对象，供上层使用。

一些大一点的网站会将你的请求到反向代理服务器中，因为当网站访问量非常大，网站越来越慢，一台服务器已经不够用了。于是将同一个应用部署在多台服务器上，将大量用户的请求分配给多台机器处理。此时，客户端不是直接通过HTTP协议访问某网站应用服务器，而是先请求到Nginx，Nginx再请求应用服务器，然后将结果返回给客户端，这里Nginx的作用是反向代理服务器。同时也带来了一个好处，其中一台服务器万一挂了，只要还有其他服务器正常运行，就不会影响用户使用。
```



![图片](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/640) 

```
解析html以构建dom树 -> 构建render树 -> 布局render树 -> 绘制render树
浏览器在解析html文件时，会”自上而下“加载，并在加载过程中进行解析渲染。在解析过程中，如果遇到请求外部资源时，如图片、外链的CSS、iconfont等，请求过程是异步的，并不会影响html文档进行加载。
解析过程中，浏览器首先会解析HTML文件构建DOM树，然后解析CSS文件构建渲染树，等到渲染树构建完成后，浏览器开始布局渲染树并将其绘制到屏幕上。这个过程比较复杂，涉及到两个概念: reflow(回流)和repain(重绘)。
DOM节点中的各个元素都是以盒模型的形式存在，这些都需要浏览器去计算其位置和大小等，这个过程称为relow;当盒模型的位置,大小以及其他属性，如颜色,字体,等确定下来之后，浏览器便开始绘制内容，这个过程称为repain。
页面在首次加载时必然会经历reflow和repain。reflow和repain过程是非常消耗性能的，尤其是在移动设备上，它会破坏用户体验，有时会造成页面卡顿。所以我们应该尽可能少的减少reflow和repain。
当文档加载过程中遇到js文件，html文档会挂起渲染（加载解析渲染同步）的线程，不仅要等待文档中js文件加载完毕，还要等待解析执行完毕，才可以恢复html文档的渲染线程。因为JS有可能会修改DOM，最为经典的document.write，这意味着，在JS执行完成前，后续所有资源的下载可能是没有必要的，这是js阻塞后续资源下载的根本原因。所以我明平时的代码中，js是放在html文档末尾的。
JS的解析是由浏览器中的JS解析引擎完成的，比如谷歌的是V8。JS是单线程运行，也就是说，在同一个时间内只能做一件事，所有的任务都需要排队，前一个任务结束，后一个任务才能开始。但是又存在某些任务比较耗时，如IO读写等，所以需要一种机制可以先执行排在后面的任务，这就是：同步任务(synchronous)和异步任务(asynchronous)。
JS的执行机制就可以看做是一个主线程加上一个任务队列(task queue)。同步任务就是放在主线程上执行的任务，异步任务是放在任务队列中的任务。所有的同步任务在主线程上执行，形成一个执行栈;异步任务有了运行结果就会在任务队列中放置一个事件；脚本运行时先依次运行执行栈，然后会从任务队列里提取事件，运行任务队列中的任务，这个过程是不断重复的，所以又叫做事件循环(Event loop)。
```

```
其实这个步骤可以并列在步骤8中，在浏览器显示HTML时，它会注意到需要获取其他地址内容的标签。这时，浏览器会发送一个获取请求来重新获得这些文件。比如我要获取外图片，CSS，JS文件等，类似于下面的链接：
这些地址都要经历一个和HTML读取类似的过程。所以浏览器会在DNS中查找这些域名，发送请求，重定向等等...

不像动态页面，静态文件会允许浏览器对其进行缓存。有的文件可能会不需要与服务器通讯，而从缓存中直接读取，或者可以放到CDN中
```

