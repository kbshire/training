# DNS

DNS（Domain Name System）是互联网上的一项服务，它作为将域名和IP地址相互映射的一个分布式数据库，能够使人更方便的访问互联网。

DNS系统使用的是网络的查询，那么自然需要有监听的port。DNS使用的是53端口，在/etc/services（搜索domain）这个文件中能看到。通常DNS是以UDP这个较快速的数据传输协议来查询的，但是没有查询到完整的信息时，就会再次以TCP这个协议来重新查询。所以启动DNS时，会同时启动TCP以及UDP的port53。

1、因特网的域名结构

由于因特网的用户数量较多，所以因特网在命名时采用的是层次树状结构的命名方法。任何一个连接在因特网上的主机或路由器，都有一个唯一的层次结构的名字，即域名(domain name)。“域”(domain)是名字空间中一个可被管理的划分。

域名只是逻辑概念，并不代表计算机所在的物理地点。域名可分为三大类：

(1)国家顶级域名：采用ISO3166的规定。如：cn代表中国，us代表美国，uk代表英国，等等。国家域名又常记为ccTLD(country code top-level domains，cc表示国家代码contry-code)。

(2)通用顶级域名：最常见的通用顶级域名有7个，即：com(公司企业)，net(网络服务机构)，org(非营利组织)，int(国际组织)，gov(美国的政府部门)，mil(美国的军事部门)。

(3)基础结构域名(infrastructure domain)：这种顶级域名只有一个，即arpa，用于反向域名解析，因此称为反向域名。

![image-20230327120317580](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327120317580.png) 

**根据域名服务器起的作用，可以把域名服务器划分为以下类型：**

（1）根域名服务器：最高层次的域名服务器，也是最重要的域名服务器。所有的根域名服务器都知道所有的顶级域名服务器的域名和IP地址。不管是哪一个本地域名服务器，若要对因特网上任何一个域名进行解析，只要自己无法解析，就首先求助根域名服务器。所以根域名服务器是最重要的域名服务器。假定所有的根域名服务器都瘫痪了，那么整个DNS系统就无法工作。需要注意的是，在很多情况下，根域名服务器并不直接把待查询的域名直接解析出IP地址，而是告诉本地域名服务器下一步应当找哪一个顶级域名服务器进行查询。

现如今全球一共投放13个根服务器  

根服务器主要用来管理互联网的主目录，全世界只有13台。1个为主根服务器，放置在美国。其余12个均为辅根服务器，其中9个放置在美国，欧洲2个，位于英国和瑞典，亚洲1个，位于日本。所有根服务器均由美国政府授权的互联网域名与号码分配机构ICANN统一管理，负责全球互联网域名根服务器、域名体系和IP地址等的管理。 这13台根服务器可以指挥Firefox或互联网 Explorer这样的Web浏览器和电子邮件程序控制互联网通信。换句话说——攻击整个因特网最有力、最直接，也是最致命的方法恐怕就是攻击根域名服务器了。

（2）顶级域名服务器：负责管理在该顶级域名服务器注册的二级域名。

（3）权限域名服务器：负责一个“区”的域名服务器。(二级域名)

（4）本地域名服务器：本地域名服务器不属于域名服务器的层次结构，但是它对域名系统非常重要。当一个主机发出DNS查询请求时，这个查询请求报文就发送给本地域名服务器。

为了提高域名服务器的可靠性，DNS域名服务器都把数据复制到几个域名服务器来保存，其中的一个就是主DNS服务器（Master name server），负责解析至少一个域。其他的是辅助（从）DNS服务器（Slave name server）：负责解析至少一个域，是主DNS服务器的辅助。当主域名服务器出故障时，辅助域名服务器可以保证DNS的查询工作不会中断。但也不会变成主。主域名服务器定期把数据复制到辅助域名服务器中，而更改数据只能在主域名服务器中进行。这样就保证了数据的一致性。

缓存DNS服务器：不负责解析域，只是缓存域名解析的结果。

![image-20230327130314019](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327130314019.png) 

1、在浏览器中输入www . qq .com 域名，先查找网页缓存。操作系统会先检查自己本地的hosts文件是否有这个网址映射关系，如果有，就先调用这个IP地址映射，完成域名解析。

2、如果hosts里没有这个域名的映射，则查找本地DNS解析器缓存，是否有这个网址映射关系，如果有，直接返回，完成域名解析。

3、如果hosts与本地DNS解析器缓存都没有相应的网址映射关系，首先会找TCP/IP参数中设置的首选DNS服务器，在此我们叫它本地DNS服务器，此服务器收到查询时，如果要查询的域名，包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析，此解析具有权威性。

4、如果要查询的域名，不由本地DNS服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个IP地址映射，完成域名解析，此解析不具有权威性。

5、如果本地DNS服务器本地区域文件与缓存解析都失效，则根据本地DNS服务器的设置（是否设置转发器）进行查询，如果未用转发模式，本地DNS就把请求发至13台根DNS，根DNS服务器收到请求后会判断这个域名(.com)是谁来授权管理，并会返回一个负责该顶级域名服务器的一个IP。本地DNS服务器收到IP信息后，将会联系负责.com域的这台服务器。这台负责.com域的服务器收到请求后，如果自己无法解析，它就会找一个管理qq.com的DNS服务器地址给本地DNS服务器。当本地DNS服务器收到这个地址后，就会找qq.com域服务器，重复上面的动作，进行查询，直至找到www . qq .com主机。

//5中所有的步骤都是本地DNS服务器通信，服务器返回也是返回给本地DNS服务器

6、如果用的是转发模式，本地DNS服务器就会把请求转发至上一级DNS服务器，由上一级服务器进行解析，上一级服务器如果不能解析，或找根DNS或把请求转至上上级，以此循环。找到最后把结果返回给本地DNS服务器，由此DNS服务器再返回给客户机。 

注：从客户端到本地DNS服务器是属于递归查询，而DNS服务器之间使用的交互查询就是迭代查询。



**DNS****解析方式**

正向解析：将FQDN----->IP

反向解析：将IP----->FQDN



![image-20230327131302180](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327131302180.png) 

PTR  反向解析

NS  解析域的域名服务器名称

SOA主要是与区域有关，所以domain要写域名。而SOA后面会接七个参数，这七个参数的意义如下：

1)Master DNS服务器主机名：这个区域主要是哪台DNS作为Master的意思。

2）管理员的Email，发生问题可以联系这个管理员。由于@在数据库文件中有特殊含义，所以将用“.”代替@

3）序号（Serial），这个序号代表的是这个数数据库文件的新旧，序号越大代表越新。所以当你更改了数据库内容时，需要将这个数值放大。最长10位

4）更新频率（Refresh）定义slave多久向Master要求数据更新。

5）失败重新尝试时间（Retry），如果Slave无法对Master实现连接，那么在多长时间内，Slave会尝试重新连接到Master。

6）失效时间（Expire），如果一直尝试失败，持续连接到达这个设置值时限，那么Slave将不再继续尝试连接，并且尝试删除这份下载的zone file信息。

7）缓存时间（Minumum TTL），如果这个数据库zone file中，每笔RR记录都没有写到TTL缓存时间的话，那么就以这个SOA的设置值为主。ttl的意思是当这笔记录被其他DNS服务器查询到后，这个记录会在对方DNS服务器的缓存中，保持多久时间。如果写了$TTL，则以该值为准。

TTL的优先级分别为：RR记录的时候优于$TTL优于SOA里的TTL

时间单位：M（分钟），H（小时），D（天），W（周），默认是秒



简单的说，A 记录直接指向 IP 地址，CNAME 记录指向域名。IP 地址是互联网世界中唯一的，而 CNAME 指向的域名也要指向 A 记录，也就说 IP 地址可以按需更换，而无需变更 CNAME 的记录值。

长期建站、项目运营的话，一般都建议使用 CNAME 记录。CNAME 记录可用于 CDN 加速，通过 CDN 加速别名解析网站域名，这样既可以起到加速网站的作用，又能隐藏网站的真实 IP，减少被攻击的几率。现在的云服务器一般都接入了 BGP 多线路，至少是电信、联通、移动三线路，在更换 IP 的时候 CNAME 记录变，特别方便。

## dig

yum install bind-utils 

apt-get install dnsutils 

> b——当主机具有多个IP地址，指定使用本机的哪个IP地址向域名服务器发送域名查询请求；
>
> f——指定dig以批处理的方式运行，指定的文件中保存着需要批处理查询的DNS任务信息；
>
> P——指定域名服务器所使用端口号；
>
> t——指定要查询的DNS数据类型；
>
> x——执行逆向域名查询；
>
> 4——使用IPv4；
>
> 6——使用IPv6；
>
> h——显示指令帮助信息。
>
> @ ：指定进行域名解析的域名服务器  
>
> +noall：忽略全部，不进行输出
>
> +answer：只输出结果，省略过程
>
> +trace： 进行迭代查

```
[root@hecs-346515 ~]# dig 

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.13 <<>>
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 39233
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;.				IN	NS

;; ANSWER SECTION:
.			90813	IN	NS	b.root-servers.net.
.			90813	IN	NS	e.root-servers.net.
.			90813	IN	NS	k.root-servers.net.
.			90813	IN	NS	l.root-servers.net.
.			90813	IN	NS	j.root-servers.net.
.			90813	IN	NS	m.root-servers.net.
.			90813	IN	NS	f.root-servers.net.
.			90813	IN	NS	i.root-servers.net.
.			90813	IN	NS	a.root-servers.net.
.			90813	IN	NS	g.root-servers.net.
.			90813	IN	NS	c.root-servers.net.
.			90813	IN	NS	d.root-servers.net.
.			90813	IN	NS	h.root-servers.net.

;; Query time: 1 msec
;; SERVER: 100.125.1.250#53(100.125.1.250)
;; WHEN: Mon Mar 27 13:25:38 CST 2023
;; MSG SIZE  rcvd: 239

```

```
[root@hecs-346515 ~]# dig www.bilibili.com 

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.13 <<>> www.bilibili.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41996
;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.bilibili.com.		IN	A

;; ANSWER SECTION:
www.Bilibili.com.	187	IN	CNAME	a.w.bilicdn1.com.
a.w.bilicdn1.com.	24	IN	A	61.147.236.44
a.w.bilicdn1.com.	24	IN	A	183.131.147.48
a.w.bilicdn1.com.	24	IN	A	61.147.236.45
a.w.bilicdn1.com.	24	IN	A	183.131.147.27
a.w.bilicdn1.com.	24	IN	A	61.147.236.43
a.w.bilicdn1.com.	24	IN	A	116.207.137.66
a.w.bilicdn1.com.	24	IN	A	61.147.236.42
a.w.bilicdn1.com.	24	IN	A	116.207.137.67
a.w.bilicdn1.com.	24	IN	A	117.21.179.20
a.w.bilicdn1.com.	24	IN	A	117.21.179.19
a.w.bilicdn1.com.	24	IN	A	183.131.147.30
a.w.bilicdn1.com.	24	IN	A	61.147.236.46
a.w.bilicdn1.com.	24	IN	A	183.131.147.29
a.w.bilicdn1.com.	24	IN	A	117.21.179.18
a.w.bilicdn1.com.	24	IN	A	116.207.137.68
a.w.bilicdn1.com.	24	IN	A	183.131.147.28

;; Query time: 1 msec
;; SERVER: 100.125.1.250#53(100.125.1.250)
;; WHEN: Mon Mar 27 13:26:10 CST 2023
;; MSG SIZE  rcvd: 341

```

**注意CDN，这边解析的是cdn，**在通过cname映射到服务器上

```
[root@hecs-346515 ~]# dig www.bilibili.com +trace 

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.el7_9.13 <<>> www.bilibili.com +trace
;; global options: +cmd
.			46581	IN	NS	j.root-servers.net.
.			46581	IN	NS	m.root-servers.net.
.			46581	IN	NS	b.root-servers.net.
.			46581	IN	NS	d.root-servers.net.
.			46581	IN	NS	f.root-servers.net.
.			46581	IN	NS	c.root-servers.net.
.			46581	IN	NS	k.root-servers.net.
.			46581	IN	NS	a.root-servers.net.
.			46581	IN	NS	i.root-servers.net.
.			46581	IN	NS	g.root-servers.net.
.			46581	IN	NS	e.root-servers.net.
.			46581	IN	NS	h.root-servers.net.
.			46581	IN	NS	l.root-servers.net.
;; Received 239 bytes from 100.125.1.250#53(100.125.1.250) in 1 ms

com.			172800	IN	NS	m.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20230408170000 20230326160000 951 . H7BRkckchMHEEOFcAtCXz2NB4/s6cJmVnZ+eYbGfd6KC3uheApbJqNKe oL3hvZ8fbBRFWLmJu84MCvghWPlHDnuB1XxcZEXlg6Q2paLvESRe6XNu OH1l324FfFxxeH4RRhiEahEKQ/4RMhz56QCAGU3OentERgDZdr5Wjbf6 icsP52w4tArTK2R7dGyZ/rDtu8PD9K+EkNxWR+iBsQ6fIqoqmoUGtiUY 8VQO4SjWP7yYe9L38YkTcGuQYyGdoTaEAbfNY3mdd48wKqqL0KN/KrDo GzSbmfMlvZnGOh2lX9shEh1k/vsY2gtvKr0fDEiNNaH7+0odXLqr8lDe 1ytbXg==
;; Received 1176 bytes from 192.5.5.241#53(f.root-servers.net) in 29 ms

bilibili.com.		172800	IN	NS	ns3.dnsv5.com.
bilibili.com.		172800	IN	NS	ns4.dnsv5.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q2D6NI4I7EQH8NA30NS61O48UL8G5 NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20230331042255 20230324031255 36739 com. VuCMIh+c5DfRjij/K4fkRPEFyAPx5mB/+57+Gt+nGx+eOiyIZYUJoJxY FzEI/r+M91bJo2q/t1iB+DdX22WpxWAJsp5zDQjinXQMih/u/ps+oaDc mjWakvEwWrreOnoaRFWV3tYCE52i77p29YBE02gsdDY2gwNLQLoxEaW6 PwIQD+7i28EkXcBn7mxbCmeR8oI5iNFDUsmRdQqIydZiow==
34N8GNNORFKQBRKAKQ6EAHOKV8LMRBQM.com. 86400 IN NSEC3 1 1 0 - 34N8VR2QIA8E6FQT7AUGCM6FARONEELO NS DS RRSIG
34N8GNNORFKQBRKAKQ6EAHOKV8LMRBQM.com. 86400 IN RRSIG NSEC3 8 2 86400 20230331050712 20230324035712 36739 com. F6TSlkePHmRY2znFYPhzx2ScGn7xQTa5l4h1n8OvdMazXtYd2zjfWCSq mw7wpopI8E8MraiAeQMB2OI898FqLquucboa3CwW9vwEkRm4n/I6RP0k VeEGDbVa40A1kdPg8kl4mgdKPjf5OriXe4jehnlcDGD4uYLRLv7HwTKB 3pK/S9kRurokzOQasDL3DAw/wi3NrDLytn+xxKneSYjV3A==
;; Received 980 bytes from 192.55.83.30#53(m.gtld-servers.net) in 175 ms

www.bilibili.com.	300	IN	CNAME	a.w.bilicdn1.com.
bilibili.com.		86400	IN	NS	ns3.dnsv5.com.
bilibili.com.		86400	IN	NS	ns4.dnsv5.com.
;; Received 129 bytes from 1.12.0.20#53(ns3.dnsv5.com) in 7 ms

```

+trace 可以显示递归过程，先解析 `. -> com. -> bilibili.com. -> www.bilibili.com.`

> 简明使用，只会输出A记录(写脚本的时候容易获取ip地址)
>
> ```
> dig cmsky.com +short
> ```
>
> 只输出mx记录，简明使用
>
> ```
> dig mx jpuyy.com +short
> ```
>
> 只输出NS记录
>
> ```
> dig ns cmsky.com
> ```
>
> 查询SOA( Start of Autority ) 返回主DNS服务器
>
> ```
> dig soa cmsky.com
> ```
>
> 指定dns，例如查询8.8.8.8中的jpuyy.com记录
>
> ```
> dig +short @8.8.8.8 cmsky.com
> ```
>
> dig @ns4.sina.com（该域名下的ns服务器）sina.com axfr



[DNS 原理入门 - 阮一峰的网络日志 (ruanyifeng.com)](https://ruanyifeng.com/blog/2016/06/dns.html)