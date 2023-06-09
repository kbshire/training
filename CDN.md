# CDN

## 0x00 CDN概述

**CDN**英文全称`Content Delivery Network`，中文翻译即为**内容分发网络**。它是建立并覆盖在承载网之上，由分布在不同区域的边缘节点服务器群组成的分布式网络。

CDN这个技术其实说起来并不复杂，最初的核心理念，就是**将内容缓存在终端用户附近**。

具体来说，CDN就是采用更多的缓存服务器（CDN边缘节点），布放在用户访问相对集中的地区或网络中。当用户访问网站时，利用全局负载技术，将用户的访问指向距离最近的缓存服务器上，由缓存服务器响应用户请求。

镜像服务器是源内容服务器的完整复制。而CDN，是部分内容的缓存，智能程度更高。

**CDN=更智能的镜像+缓存+流量导流**。

## **0x01 CDN应用场景**

###### **1、网站站点/应用加速**

站点或者应用中大量静态资源的加速分发，建议将站点内容进行动静分离，动态文件可以结合云服务器ECS，静态资源如各类型图片、html、css、js文件等，建议结合 对象存储OSS 存储海量静态资源，可以有效加速内容加载速度，轻松搞定网站图片、短视频等内容分发。

###### **2、视音频点播/大文件下载分发加速**

支持各类文件的下载、分发，支持在线点播加速业务，如mp4、flv视频文件或者平均单个文件大小在20M以上，主要的业务场景是视音频点播、大文件下载（如安装包下载）等，建议搭配对象存储OSS使用，可提升回源速度，节约近2/3回源带宽成本。

###### **3、视频直播加速**

视频流媒体直播服务，支持媒资存储、切片转码、访问鉴权、内容分发加速一体化解决方案。结合弹性伸缩服务，及时调整服务器带宽，应对突发访问流量；结合媒体转码服务，享受高速稳定的并行转码，且任务规模无缝扩展。

###### **4、移动应用加速**

移动APP更新文件（apk文件）分发，移动APP内图片、页面、短视频、UGC等内容的优化加速分发。提供httpDNS服务，避免DNS劫持并获得实时精确的DNS解析结果，有效缩短用户访问时间，提升用户体验。

## 0x01 CDN网络的组成

一个CDN网络主要由以下几部分组成：内容缓存设备、内容分发管理设备、本地负载均衡交换机、GSLB设备和CDN管理系统，其网络结构如下图所示：

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20130609113946968)

**内容缓存设备Cache**：

用于缓存内容实体和对缓存内容进行组织和管理。当有用户访问该客户内容时，直接由各缓存服务器响应用户的请求。

**内容分发管理设备**：

主要负责核心Web服务器内容到CDN网络内缓存设备的内容推送、删除、校验以及内容的管理、同步。

**GSLB设备**：

则实现CDN全网各缓存节点之间的资源负载均衡，它与各节点的SLB设备保持通信，搜集各节点缓存设备的健康状态、性能、负载等，自动将用户指引到位于**其地理区域中的最近服务器**或者引导用户**离开拥挤的网络和服务器**。还可以通过使用多站点的内容和服务来提高容错性和可用性，防止因本地网或区域网络中断、断电或自然灾害而导致的故障。

> 不用CDN技术时，使用GSLB设备可以为用户选择最合适的服务器，但受Web服务器的负荷和传输距离等因素的影响，响应速度依然经常不能满足用户的需求。这一问题的解决方案就是在传输网络上利用缓存技术使得Web服务数据流能够就近访问。内容分发网络（CDN）正是这种思想的一个实现，CDN使用GSLB设备将用户引导到最合适的缓存节点（距离近，负载低），使得用户在访问静态内容时获得更好的体验。

**CDN管理系统**：

实现对全网设备的管理，对系统的配置。它不仅能对系统中的各个设备进行实时监控，对各种故障产生相应的告警，还能实时观测到系统中总的流量以及各节点的流量，并保存在系统的数据库中，作为统计分析的基础数据，并对日志文件进行管理、报告，作为计费的基础数据。



## 0x02 CDN 原理

### CDN 解析流程

如果某个用户想要访问优酷的视频点播内容，那么：

<img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221125204154043.png" alt="image-20221125204154043" style="zoom:67%;" />

> ①、当用户点击APP上的内容，APP会根据URL地址去**本地DNS**（域名解析系统）寻求IP地址解析。
>
> ②、当用户点击网站页面上的内容URL，经过本地DNS系统解析，DNS 系统会最终将域名的解析权交给 CNAME指向的 CDN 专用 DNS 服务器。
>
> ③、CDN专用DNS服务器，将CDN的全局负载均衡设备IP地址返回用户。也就是上面所说的GSLB设备
>
> ④、用户向**CDN的负载均衡设备**发起内容URL访问请求。
>
> ⑤、CDN负载均衡设备根据用户IP地址，以及用户请求的内容URL，选择一台用户所属区域的**缓存服务器**。
>
> ⑥、负载均衡设备告诉用户这台缓存服务器的IP地址，让用户向所选择的缓存服务器发起请求。
>
> ⑦、用户向缓存服务器发起请求，缓存服务器响应用户请求，将用户所需内容传送到用户终端。
>
> ⑧、如果这台缓存服务器上并没有用户想要的内容，那么这台缓存服务器就要网站的**源服务器**请求内容。
>
> ⑨、源服务器返回内容给缓存服务器，缓存服务器发给用户，并根据用户自定义的缓存策略，判断要不要把内容缓存到缓存服务器上。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/u6pjybbkxn.png)

> 1. 当终端用户（北京）向`www.a.com`下的指定资源发起请求时，首先向LDNS（本地DNS）发起域名解析请求。
> 2. LDNS检查缓存中是否有`www.a.com`的IP地址记录。如果有，则直接返回给终端用户；如果没有，则向授权DNS查询。
> 3. 当授权DNS解析`www.a.com`时，返回域名CNAME www.a.tbcdn.com对应IP地址。
> 4. 域名解析请求发送至阿里云DNS调度系统，并为请求分配最佳节点IP地址。
> 5. LDNS获取DNS返回的解析IP地址。
> 6. 用户获取解析IP地址。
> 7. 用户向获取的IP地址发起对该资源的访问请求。
>
> - 如果该IP地址对应的节点已缓存该资源，则会将数据直接返回给用户，例如，图中步骤7和8，请求结束。
> - 如果该IP地址对应的节点未缓存该资源，则节点向源站发起对该资源的请求。获取资源后，结合用户自定义配置的缓存策略，将资源缓存至节点，例如，图中的北京节点，并返回给用户，请求结束。

> 1. CDN的加速资源是跟域名绑定的。
> 2. 通过域名访问资源，首先是通过DNS分查找离用户最近的CDN节点（边缘服务器）的IP
> 3. 通过IP访问实际资源时，如果CDN上并没有缓存资源，则会到源站请求资源，并缓存到CDN节点上，这样，用户下一次访问时，该CDN节点就会有对应资源的缓存了。

应用CDN后，DNS 返回的不再是 IP 地址，而是一个CNAME(Canonical Name ) 别名记录，指向 CDN的全局负载均衡 GSLB, GSLB实际上在域名解析的过程中承担了中间人（或者说代理）的角色，这是CDN实现的关键

CDN主要功能是在不同的地点缓存内容，通过负载均衡技术，将用户的请求定向到最合适的缓存服务器上去获取内容，比如说，是北京的用户，我们让他访问北京的节点，深圳的用户，我们让他访问深圳的节点。通过就近访问，加速用户对网站的访问。解决Internet网络拥堵状况，提高用户访问网络的响应速度。



由于没有返回IP地址，于是本地DNS会向负载均衡系统再发送请求 ，则进入到CDN的全局负载均衡系统GSLB 进行智能调度：

- 看用户的 IP 地址，查表得知地理位置，找相对最近的边缘节点
- 看用户所在的运营商网络，找相同网络的边缘节点
- 检查边缘节点的负载情况，找负载较轻的节点
- 其他，比如节点的“健康状况”、服务能力、带宽、响应时间等

结合上面的因素，得到最合适的边缘节点，然后把这个节点返回给用户，用户就能够就近访问CDN的缓存代理

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/fdde5787e61870ee5d997f1ca89d0870.png)



我们可以用dig查看dns解析结果

```
[root@VM-16-11-centos ~]# dig www.bilibili.com

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.7 <<>> www.bilibili.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60626
;; flags: qr rd ra; QUERY: 1, ANSWER: 22, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.bilibili.com.		IN	A

;; ANSWER SECTION:
www.bilibili.com.	144	IN	CNAME	a.w.bilicdn1.com.
a.w.bilicdn1.com.	169	IN	CNAME	ct.w.bilicdn1.com.
ct.w.bilicdn1.com.	60	IN	A	183.131.147.27
ct.w.bilicdn1.com.	60	IN	A	183.131.147.28
ct.w.bilicdn1.com.	60	IN	A	183.131.147.29
ct.w.bilicdn1.com.	60	IN	A	183.131.147.30
ct.w.bilicdn1.com.	60	IN	A	183.131.147.48
ct.w.bilicdn1.com.	60	IN	A	61.147.236.42
ct.w.bilicdn1.com.	60	IN	A	117.21.179.18
ct.w.bilicdn1.com.	60	IN	A	61.147.236.44
ct.w.bilicdn1.com.	60	IN	A	61.147.236.45
ct.w.bilicdn1.com.	60	IN	A	61.147.236.46
ct.w.bilicdn1.com.	60	IN	A	116.207.137.66
ct.w.bilicdn1.com.	60	IN	A	116.207.137.67
ct.w.bilicdn1.com.	60	IN	A	116.207.137.68
ct.w.bilicdn1.com.	60	IN	A	61.147.236.43
ct.w.bilicdn1.com.	60	IN	A	117.21.179.19
ct.w.bilicdn1.com.	60	IN	A	117.21.179.20
ct.w.bilicdn1.com.	60	IN	A	117.23.60.12
ct.w.bilicdn1.com.	60	IN	A	117.23.60.13
ct.w.bilicdn1.com.	60	IN	A	117.23.60.14
ct.w.bilicdn1.com.	60	IN	A	117.23.60.15

;; Query time: 7 msec
;; SERVER: 183.60.83.19#53(183.60.83.19)
;; WHEN: Fri Nov 25 21:22:04 CST 2022
;; MSG SIZE  rcvd: 398

```

```
在ANSWER SECTION列表可以看出

www.bilibili.com为cname记录指向a.w.bilicdn1.com.
a.w.bilicdn1.com为cname记录指向ct.w.bilicdn1.com.
ct.w.bilicdn1.com.返回了20条A记录，这20个ip 信息是
中国浙江金华 电信
中国江苏南通 电信
中国湖北宜昌 电信 
中国 陕西省 西安市 电信  -- 117.23.60.14
比如我在陕西省西安市鄠邑区，可以看出返回的都是就近节点。实际上CDN是有非常多的边缘节点。
```

用tcpdump来监控下DNS的UDP数据包

```
[root@VM-16-11-centos ~]# tcpdump -n -s 1500 udp and port 53
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 1500 bytes
21:28:21.393765 IP 10.0.16.11.45832 > 183.60.83.19.domain: 32323+ A? www.bilibili.com. (34)

21:28:21.394724 IP 183.60.83.19.domain > 10.0.16.11.45832: 32323 22/0/0 CNAME a.w.bilicdn1.com., CNAME ct.w.bilicdn1.com., A 61.147.236.42, A 61.147.236.43, A 61.147.236.44, A 61.147.236.45, A 61.147.236.46, A 116.207.137.66, A 116.207.137.67, A 116.207.137.68, A 117.21.179.18, A 117.21.179.19, A 117.21.179.20, A 117.23.60.12, A 171.214.10.140, A 171.214.10.141, A 171.214.10.142, A 183.131.147.27, A 183.131.147.28, A 183.131.147.29, A 183.131.147.30, A 183.131.147.48 (398)

21:28:21.407290 IP 10.0.16.11.32999 > 183.60.83.19.domain: 53397+ PTR? 42.236.147.61.in-addr.arpa. (44)

21:28:21.431995 IP 183.60.83.19.domain > 10.0.16.11.32999: 53397 NXDomain 0/1/0 (93)

```

其中，10.0.16.11为腾讯云服务器内网地址。也就是本机向路由器询问DNS解析，如果路由器已经缓存了，就会直接返回。



### 传统的网站访问与CDN访问

**传统访问访问：**

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/n630r4cz49.png)

**使用了CDN的网站访问：**

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/7qb88zlcll.png)



在没有CDN的情况下，用户向浏览器输入`www.web.com`这个域名，客户端访问本地DNS服务器的时候，如果本地DNS服务器有缓存，则返回网站的地址；如果没有，递归查询到网站的权威DNS服务器，这个权威DNS服务器是负责web.com的，它会返回网站的IP地址。

本地DNS服务器缓存下IP 地址，将IP地址返回，然后客户端直接访问这个IP地址，就访问到了这个网站。

**然而有了 CDN之后，情况发生了变化**。

在web.com这个权威DNS服务器上，会设置一个CNAME别名，指向另外一个域名`www.web.cdn.com`，返回给本地DNS服务器。

当本地DNS服务器拿到这个新的域名时，需要继续解析这个新的域名。这个时候，再访问的就不是 web.com 的权威DNS服务器了，而是web.cdn.com的权威DNS服务器，这是CDN自己的权威 DNS 服务器。在这个服务器上，还是会设置一个CNAME，指向另外一个域名，也即CDN网络的全局负载均衡器。

接下来，**本地DNS服务器去请求CDN的全局负载均衡器解析域名**，全局负载均衡器会为用户选择一台合适的缓存服务器提供服务

基于以上这些条件，进行综合分析之后，全局负载均衡器会返回一台缓存服务器的IP地址。

本地DNS服务器缓存这个IP地址，然后将IP返回给客户端，客户端去访问这个边缘节点，下载资源。 缓存服务器响应用户请求，将用户所需内容传送到用户终端。如果这台缓存服务器上并没有用户想要的内容，那么这台服务器就要向它的上一级缓存服务器请求内容，直至追溯到网站的源服务器将内容拉到本地

![在这里插入图片描述](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20200418104105733.png)



 与传统访问方式不同，**CDN网络则是在用户和服务器之间增加缓存层，将用户的访问请求引导到最优的缓存节点**而不是服务器源站点，从而加速访问速度。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/ciawk92i4h.png)

### 缓存代理

缓存系统是 CDN的另一个关键组成部分，缓存系统会有选择地缓存那些最常用的那些资源

其中有两个衡量CDN服务质量的指标：

- 回源率：缓存里没有，必须用代理的方式回源站取，回源次数与所有访问次数之比

> 缓存系统也可以划分出层次，分成一级缓存节点和二级缓存节点。一级缓存配置高一些，直连源站，二级缓存配置低一些，直连用户
> 回源的时候二级缓存只找一级缓存，一级缓存没有才回源站，可以有效地减少真正的回源

- 命中率：用户访问的资源恰好在缓存系统里，可以直接返回给用户，命中次数与所有访问次数之比

> 现在的商业 CDN命中率都在 90% 以上，相当于把源站的服务能力放大了 10 倍以上



## 0x03 CDN好处

> ###### **1. 为了实现跨运营商、跨地域的全网覆盖**
>
> 互联不互通、区域ISP地域局限、出口带宽受限制等种种因素都造成了网站的区域性无法访问。CDN加速可以覆盖全球的线路，通过和运营商合作，部署IDC资源，在全国骨干节点商，合理部署CDN边缘分发存储节点，充分利用带宽资源，平衡源站流量。
>
> 例如中国移动手机用户访问中国电信网络的内容源，可以通过在中国移动假设CDN服务器，进行加速。效果是非常明显的。
>
> ###### **2. 为了保障你的网站安全**
>
> CDN的负载均衡和分布式存储技术，可以加强网站的可靠性，相当无无形中给你的网站添加了一把保护伞，应对绝大部分的互联网攻击事件。防攻击系统也能避免网站遭到恶意攻击。
>
> 内容进行分发后，源服务器的IP被隐藏，受到攻击的概率会大幅下降。CDN 可以通过分流 ，洗掉来自Ddos 大部分的攻击 。
>
> ###### **3. 为了异地备援**
>
> 当某个服务器发生意外故障时，系统将会调用其他临近的健康服务器节点进行服务，进而提供接近100%的可靠性，这就让你的网站可以做到永不宕机。
>
> ###### **4. 为了节约成本投入**
>
> 使用CDN加速可以实现网站的全国铺设，你根据不用考虑购买服务器与后续的托管[运维](https://cloud.tencent.com/solution/operation?from=10680)，服务器之间镜像同步，也不用为了管理维护技术人员而烦恼，节省了人力、精力和财力。
>
> ###### **5. 为了让你更专注业务本身**
>
> CDN加速厂商一般都会提供一站式服务，业务不仅限于CDN，还有配套的云存储、大数据服务、视频云服务等，而且一般会提供7x24运维监控支持，保证网络随时畅通，你可以放心使用。并且将更多的精力投入到发展自身的核心业务之上。
>
> 6.互联网服务提供商采用CDN，是**以存储换时延**。通信运营商也追捧CDN，但它们的目的，是**以存储换带宽**——通过服务“下沉”，减轻上层骨干网络的流量压力，避免硬件扩容，降低网络建设成本。

![image-20221125204403468](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221125204403468.png)

## 0x04 常用的CDN缓存软件

> - Varnish ：可以认为是内存缓存，速度一流，但是内存缓存也限制了其容量，缓存页面和图片一般是挺好的；
> - squid是功能最全面的比较传统的web cache server，有自己的存储引擎。，但是架构太老，性能不怎样。
> - nginx本来是反向代理/web服务器，用了插件可以做做这个副业，但是本身不支持的功能比较多





## 0x05 CDN相关术语

**CDN相关的术语解释**

###### **1、Origin Server源站**

做 CDN 之前的客户真正的服务器。

###### **2、User**

访问者，也就是要访问网站的网民。

###### **3、Last Mile**

最后一公里，也就是网民到他所访问到的 CDN 服务器之间的路径。

###### **4、域名**

域名是Internet网络上的一个服务器或一个网络系统的名字，全世界，没有重复的域名。

###### **5、CNAME记录**

它是一个别名记录( Canonical Name )；当 DNS 系统在查询 CNAME 左面的名称的时候，都会转向 CNAME 右面的名称再进行查询，一直追踪到最后的 PTR 或 A 名称，成功查询后才会做出回应，否则失败。

###### **6、CNAME域名**

CDN的域名加速需要用到CNAME记录，在服务器控制台配置完成CDN加速后，您会得到一个加速后的域名，称之为CNAME域名（该域名一定是`*.*http://wljslmz.com`）， 用户需要将自己的域名作CNAME指向这个`*.*http://wljslmz.com`的域名后，域名解析的工作就正式转向云服务器，该域名所有的请求都将转向云CDN的节点。

###### **7、DNS**

DNS即Domain Name System，是域名解析服务的意思。它在互联网的作用是：把域名转换成为网络可以识别的ip地址。人们习惯记忆域名，但机器间互相只认IP地址，域名与IP地址之间是一一对应的，它们之间的转换工作称为域名解析，域名解析需要由专门的域名解析服务器来完成，整个过程是自动进行的。比如：上网时输入的百度一下，你就知道会自动转换成为`220.181.112.143`

###### **8、边缘节点**

也称CDN节点、Cache节点等；是相对于网络的复杂结构而提出的一个概念，指距离最终用户接入具有较少的中间环节的网络节点，对最终接入用户有较好的响应能力和连接速度。其作用是将访问量较大的网页内容和对象保存在服务器前端的专用cache设备上，以此来提高网站访问的速度和质量。

###### **9、cache**

cache高速缓冲存储器一种特殊的存储器子系统，其中复制了频繁使用的数据以利于快速访问。存储器的高速缓冲存储器存储了频繁访问的RAM位置的内容及这些数据项的存储地址。当处理器引用存储器中的某地址时，高速缓冲存储器便检查是否存有该地址。如果存有该地址，则将数据返回处理器;如果没有保存该地址，则进行常规的存储器访问。因为高速缓冲存储器总是比主RAM存储器速度快，所以当RAM的访问速度低于微处理器的速度时，常使用高速缓冲存储器。







[什么是CDN？它解决了什么难题？5分钟让你明明白白！ - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1779335)

[也许是史上最全的一次CDN详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/28940451)

[到底什么是CDN？ - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/52362950)

[一图秒懂CDN原理 - 掘金 (juejin.cn)](https://juejin.cn/post/6962904216503320589)

[CDN图解（秒懂 + 史上最全） - 疯狂创客圈 - 博客园 (cnblogs.com)](https://www.cnblogs.com/crazymakercircle/p/14978513.html#autoid-h2-0-0-0)



来都来了，看个DNS再走吧~

# DNS 

## DNS概述

DNS是 Domain Name System 的缩写，也就是 域名解析系统，它的作用非常简单，就是根据域名查出对应的 IP地址。

你可以把它想象成一本巨大的电话本，比如当你要访问域名`www.163.com`，首先要通过DNS查出它的IP地址是`112.48.162.8`。

为啥需要解析捏？ 因为域名是给人看的，你只能通过IP的方式访问资源，但是让你背IP你又不干，而且最坏的情况是他用了CDN或者负载均衡，导致他的IP更多了，你更不想背了，所以我们有了DNS，将域名解析成IP，我们只需要输入域名，域名通过DNS解析成IP，最终访问到资源。

注意：上面的DNS解析对于用户来说是透明的，你并不知道你输入www.bilibili.com后到底他给你解析了那个IP。而如果你真想知道为啥会给你解析成某个IP，要取决于对方有没有使用CDN和一些其他因素





## DNS资源记录

在 DNS 服务器上，一个域名及其下级域名组成一个区域 （Zone）。一个 Zone的 相关的 DNS 信息构成一个数据库文件。

下面是一条A类型的资源记录（简称为A记录）：域名 www.zdns.cn 的数据为 202.173.11.10

![在这里插入图片描述](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20210704153331929.png)

记录一条域名信息映射关系，称之为资源记录（RR）。当我们查询域名www.zdns.cn的时候，查询结果得到的资源记录结构体中有如下数据：

- TTL，就是生存周期，是递归服务器会在缓存中保存该资源记录的时长。
- 网络/协议类型，它的代表的标识是IN，IN就是internet，目前DNS系统主要支持的协议是IN。
- type，就是资源记录类型，一般的网站都是都是A记录（IPv4的主机地址）。
- rdata是资源记录数据，就是域名关联的信息数据。

常见的资源记录类型如表所示。

![image-20221126101836133](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221126101836133.png)

**A记录: 又称IP指向**

用户可以在此设置子域名并指向到自己的目标主机地址上，从而实现通过域名找到服务器。

> 说明：指向的目标主机地址类型只能使用IP地址；
> 附加说明：
>
> - 泛域名解析即将该域名所有未指定的子域名都指向一个空间。在“主机名”中填入*，“类型”为A，“IP地址/主机名”中填入web服务器的IP地址，点击“新增”按钮即可。
>
> - 负载均衡的实现：负载均衡(Server Load Balancing，SLB)是指在一系列资源上面动态地分布网络负载。负载均衡可以减少网络拥塞，提高整体网络性能，提高自愈性， 并确保企业关键性应用的可用性。
>
>   当相同子域名有多个目标地址时，表示轮循，可以达到负载均衡的目的，但需要虚拟主机服务商支持。

**CNAME : 通常称别名指向。**

您可以为一个主机设置别名。
比如设置 test.mydomain.com，用来指向一个主机www.rddns.com ,那么以后就可以用test.mydomain.com来代替访问www.rddns.com了。

说明：·

- CNAME的目标主机地址只能使用主机名，不能使用IP地址；
- 主机名前不能有任何其他前缀，如：[http://等是不被允许的](http://xn--ihqx1fh8z53oltfnpwi2e/)；
- A记录优先于CNAME记录。即如果一个主机地址同时存在A记录和CNAME记录，则CNAME记录不生效。

**NS记录:指向DNS子域名**

服务器解析记录, 用来表明由哪台服务器对该域名进行解析。这里的NS记录只对子域名生效。

例如用户希望由12.34.56.78 这台服务器解析 news.mydomain.com，则需要设置 news.mydomain.com 的NS记录。

说明：

- “优先级”中的数字越小表示级别越高；
- “IP地址/主机名”中既可以填写IP地址，也可以填写像 ns.mydomain.com 这样的主机地址，但必须保证该主机地址有效。

> 如，将news.mydomain.com的NS记录指向到 ns.mydomain.com，在设置NS记录的同时还需要设置ns.mydomain.com的指向，否则NS记录将无法正常解析；

- NS记录优先于A记录。

> 即，如果一个主机地址同时存在NS记录和A记录，则A记录不生效。这里的NS记录只对子域名生效。



## 解析过程

　1、缓存查找IP

　2、本机的hosts文件查找IP

　3、DNS服务器查找IP

![在这里插入图片描述](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20210705144630547.png)

**从递归和迭代查询可以看出：**

客户端-本地dns服务端：

> 这部分属于递归查询。递归查询时，返回的结果只有两种:查询成功或查询失败.

本地dns服务端---外网：

> 这部分属于迭代查询。迭代查询，又称作重指引,返回的是最佳的查询点或者主机地址.



> 1. 浏览器先检查自身缓存中有没有被解析过的这个域名对应的ip地址，如果有，解析结束。同时域名被缓存的时间也可通过TTL属性来设置。
> 2. 如果浏览器缓存中没有（专业点叫还没命中），浏览器会检查操作系统缓存中有没有对应的已解析过的结果。而操作系统也有一个域名解析的过程。在windows中可通过c盘里一个叫hosts的文件来设置，如果你在这里指定了一个域名对应的ip地址，那浏览器会首先使用这个ip地址。
>
> > 但是这种操作系统级别的域名解析规程也被很多黑客利用，通过修改你的hosts文件里的内容把特定的域名解析到他指定的ip地址上，造成所谓的域名劫持。所以在windows7中将hosts文件设置成了readonly，防止被恶意篡改。
>
> 1. 如果至此还没有命中域名，才会真正的请求本地域名服务器（LDNS）来解析这个域名，这台服务器一般在你的城市的某个角落，距离你不会很远，并且这台服务器的性能都很好，一般都会缓存域名解析结果，大约80%的域名解析到这里就完成了。
> 2. 如果LDNS仍然没有命中，就直接跳到Root Server 域名服务器请求解析
> 3. 根域名服务器返回给LDNS一个所查询域的主域名服务器（gTLD Server，国际顶尖域名服务器，如.com .cn .org等）地址
> 4. 此时LDNS再发送请求给上一步返回的gTLD
> 5. 接受请求的gTLD查找并返回这个域名对应的Name Server的地址，这个Name Server就是网站注册的域名服务器
> 6. Name Server根据映射关系表找到目标ip，返回给LDNS
> 7. LDNS缓存这个域名和对应的ip
> 8. LDNS把解析的结果返回给用户，用户根据TTL值缓存到本地系统缓存中，域名解析过程至此结束

**记住这个解析过程，后面要考**



# 举两个例子

腾讯连接了两家运营商电信和联通，电信访问内部服务的流量大，联通的少，怎么把电信流入的流量扔一些给联通

> 这道题的侧重点在于控制用户侧和运营商侧，因为如果要控制腾讯侧，那么流量已经到了腾讯的防火墙上，不能实现流量控制。
>
> 有两种可能的解法，dns和cdn，个人理解，cdn其实是一种分布式dns的解法，两种解法的本质都是控制通过控制dns的解析来控制流量选路
>
> 1.dns
>
> 上面也提过DNS的解析过程了，那么我们需要控制的就是用户来解析域名时候返回的IP。
>
> 一般来说，联通用户解析出联通的IP访问速度肯定是最快的，但是当电信的访问拥塞时，我们可以给电信的用户在解析时返回联通的IP，让电信用户通过联通访问，而众所周知，电信和联通可定是可以互通的，只不过是次优路径的问题，因此我们无需关注电信是怎么找到联通的。就像你以前在下游戏的时候，你也不会先看看自己是电信的IP还是联通的IP再下载，通常看心情随便一点，甚至还可以点到铁通去。
>
> 那么还有个问题是运营商/腾讯怎么知道你的IP是电信还是联通的。其实每个IP都是可以查到归属的，我们只需要增加一个查表或者查缓存的过程罢了
>
> ![image-20221126105505371](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221126105505371.png)
>
> 2.cdn
>
> cdn本质就是也是通过dns解析返回给你一个离你最近的一个边缘节点的IP，只不过这个IP解析比较曲折，上面在将cdn的时候也讲过了，应用CDN后，DNS 返回的不再是 IP 地址，而是一个CNAME(Canonical Name ) 别名记录，指向 CDN的全局负载均衡 GSLB, GSLB实际上在域名解析的过程中承担了中间人（或者说代理）的角色，这是CDN实现的关键。CDN负载均衡设备根据用户IP地址，以及用户请求的内容URL，选择一台用户所属区域的**缓存服务器**。负载均衡设备告诉用户这台缓存服务器的IP地址，让用户向所选择的缓存服务器发起请求。并且CDN可以监控链路质量，实现动态切换和找到负载较轻的节点
>
> 3.负载均衡，nginx，redis(这个没啥用，但可以在这里写一笔)
>
> 因为这明显是已经进入腾讯内部之后了，nginx 的负载均衡和redis的负载均衡在集群的场景下确实是有很大作用滴
>
> ![image-20221126110209123](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221126110209123.png)
>
> <img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221126110225565.png" alt="image-20221126110225565" style="zoom:50%;" />·



或者你有两个出口电信和联通，怎么控制选路

> 这道题侧重点在于流量出去，也是在本AS内控制选路
>
> 1.PBR 策略路由 
>
> 略路由PBR（Policy-Based Routing）是一种依据用户制定的策略进行路由选择的机制，分为本地策略路由、接口策略路由和智能策略路由SPR
>
> 路由策略（route-policy）与策略路由（policy based route）区别
>
> - 策略路由的操作对象是数据包，在路由表已经产生的情况下，不按照路由表进行转发，而是根据需要，依照某种策略改变数据包转发路径。
>
> - 路由策略的操作对象是路由信息。路由策略主要实现了路由过滤和路由属性设置等功能，它通过改变路由属性（包括可达性）来改变网络流量所经过的路径。
>
> 传统的路由转发原理是首先根据报文的目的地址查找路由表，然后进行报文转发。但是目前越来越多的用户希望能够在传统路由转发的基础上根据自己定义的策略进行报文转发和选路。策略路由使网络管理者不仅能够根据报文的目的地址，而且能够根据报文的源地址、报文大小和链路质量等属性来制定策略路由，以改变数据包转发路径，满足用户需求。
>
> 如果联通的流量很少，但电信流量很大，可以通过PBR强制改变路由选路实现流量迁移
>
> 多说一句，其实我们的组网可以使用负载分担双联路方案
>
> <img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20221126112007676.png" alt="image-20221126112007676" style="zoom:67%;" />
>
> \1. 2条链路能同时使用，一半PC优先选择电信，另外一半PC优先选择联通；
>
> \2. 2条链路为主备方式，电信线路为主，联通线路为备；
>
> \3. 但可以实现电信线路断开，所有PC选择联通线路，联通线路故障，所有PC选择电信。
>
> 其实基本原理还是策略路由，我们可以通过源IP奇偶性控制选路
>
> \1. 由于策略路由只控制奇数IP，所以偶数IP默认情况下就是优选电信线路，电信线路Down情况下选择联通线路，这和传统解决方案一致；
>
> \2. 关键的是奇数IP，奇数IP在策略路由的控制下，优选了联通线路，PBR有一个特性，如果出接口联通线路Down，则PBR失效，奇数IP恢复正常的路由转发，选择电信线路。
>
> ```
> acl number 2000
> rule permit source 0.0.0.1 255.255.255.254
> #
> policy-based-route mypbr permit node 5
> if-match acl 2000
> apply ip-address next-hop 联通网关
> #
> interface vlan-interface1
> ip address 192.168.1.1 255.255.255.0
> ip policy-based-route mypbr
> #
> ip route-static 0.0.0.0 0.0.0.0 电信网关
> ip route-static 0.0.0.0 0.0.0.0 联通网关 preference 80
> ```
>
> 
>
> 2.DNS 选路
>
> 和上面的案例一样，只不过由于我们这次可以在内网控制，我们不一定要依赖运营商的DNS解析，比如学校里我们可以使用自己的DNS解析一部分地址，将其解析到空闲的链路上
>
> 3.控制preference
>
> ip route-static 0.0.0.0 0 192.168.1.254 preference 10
>
> 将希望优先选择的链路优先级改小，实现优先选路



[技术点详解---互联网双出口的选择 - IP技术专栏 - 技术甜甜圈 - 新华三集团-H3C](https://www.h3c.com/cn/d_201010/697664_97665_0.htm)

[DNS图解（秒懂 - 史上最全） - 疯狂创客圈 - 博客园 (cnblogs.com)](https://www.cnblogs.com/crazymakercircle/p/14976612.html#autoid-h3-11-0-6)





# 接下来是实现CDN环节

## proxy_cache 搭建缓存服务器

> 1、Nginx的Proxy_cache是根据Key值md5哈希存储缓存，支持任意的Key，例如你可以根据”域名、URI、参数”组合成key，也支持非200状态码，如404/302等。
>
> 2、要利用Nginx的Proxy_cache，你需要在Nginx编译进ngx_cache_purge 模块，执行：nginx -V，查看有没有ngx_cache_purge 字样，没有的话需要自己手动编译。

```
# 下载并解压cache_purge,我的在/usr/local/ngx_cache_purge-2.3
wget http://labs.frickle.com/files/ngx_cache_purge-2.3.tar.gz
tar zxvf ngx_cache_purge-2.3.tar.gz

[root@localhost sbin]# ./nginx -V
./configure --prefix=/usr/local/nginx --user=www --group=www --with-http_stub_status_module --with-http_v2_module --with-http_ssl_module --with-http_gzip_static_module --with-http_realip_module --with-http_flv_module --with-http_mp4_module --with-stream --add-module=/usr/local/ngx_cache_purge-2.3

```



修改配置文件

```bash
http {
proxy_connect_timeout 5;
proxy_read_timeout 60;
proxy_send_timeout 5;
proxy_buffer_size 16k;
proxy_buffers 4 64k;
proxy_busy_buffers_size 128k;
proxy_temp_file_write_size 128k;
proxy_cache_path /data/wwwroot/www.shinya.com levels=1:2 keys_zone=cache_one:200m inactive=30d max_size=5g;
proxy_temp_path /data/wwwroot/www.shinya.com/temp;
}
```

```
location /{
proxy_pass https://www.shinya.com;
proxy_redirect off;
proxy_set_header Host www.shinya.com;
proxy_cache cache_one;
proxy_cache_valid 200 302 304 365d;
proxy_cache_valid 301 1d;
proxy_cache_valid any 1m;
add_header Images-Cache “$upstream_cache_status from $host”;
add_header Pragma public;
add_header Cache-Control “public, must-revalidate, proxy-revalidate”;
add_header  Nginx-Cache "$upstream_cache_status";   # 显示是否命令缓存
access_log off; log_not_found off; expires max;
}
```

回生成以下格式的缓存

```
.
├── cache
│   ├── 0
│   │   └── 73
│   │       └── 0b3cd73b8afe98ff92e238ebbcaef730
│   ├── 1
│   │   └── 6c
│   │       └── f86f8edc323791f718b1cccaa068f6c1
│   ├── 6
│   │   └── f1
│   │       └── 02d9ac690b0ad1d32f123b7c4c6def16
│   ├── 8
│   │   ├── fb
│   │   │   └── 80ccd3fd415f4c9fd380ffe294e25fb8
│   │   └── fc
│   │       └── 8249da23676a1b8b7fae236980842fc8
│   ├── 9
│   │   └── d4
│   │       └── 01a49b4327ec08c30aa4cdeecc16dd49
│   ├── a
│   │   └── 98
│   │       └── c9aa10b77d4100e5db76604b684b198a
│   └── c
│       └── 22
│           └── 8ebbd1ad5c54886ec0727b25cfbd722c
└── temp
```

通过status判断是否命中缓存

![image-20230330151139269](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230330151139269.png) 































