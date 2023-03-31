# iptables

iptables 是 Linux 防火墙工作在用户空间的管理工具，是 `netfilter/iptables`IP 信息包过滤系统是一部分，用来设置、维护和检查 Linux 内核的 IP 数据包过滤规则。

iptables 是基于内核的防火墙，功能非常强大；iptables 内置了`filter`，`nat`和`mangle`，`raw`四张表。所有规则配置后，立即生效，不需要重启服务。

**iptables的结构是由表（tables）组成，而tables是由链组成，链又是由具体的规则组成**。因此我们在编写iptables规则时，要先指定表，再指定链。tables的作用是区分不同功能的规则，并且存储这些规则。

 ![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/9ddgnzua1k.png)

## 四表 

- **`filter`**

负责过滤数据包，包括的规则链有：`input`，`output`和`forward`

- **`nat`**

用于网络地址转换（IP、端口），包括的规则链有：`prerouting`，`postrouting` 和 `output`

- **`mangle`**

主要应用在修改数据包、流量整形、给数据包打标识，默认的规则链有：`INPUT`，`OUTPUT`、 `forward`，`POSTROUTING`，`PREROUTING`。它能改变TCP头中的QoS位。

- **`raw`**

Raw表用于处理异常，它具有2个内建链：`PREROUTING`，`OUTPUT`

优先级：mangle > nat > filter



## 五链

- **`input`**

匹配目标IP是本机的数据包

- **`output`**

出口数据包 ， 一般不在此链上做配置

- **`forward`**

匹配流经本机的数据包

- **`prerouting`**

修改目的地址，用来做 DNAT 。如：把内网中的 80 端口映射到互联网端口

- `postrouting`

修改源地址，用来做 SNAT。如：局域网共享一个公网IP接入Internet。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/u0joaicej5.png) 

1. 当一个数据包进入网卡时，它首先进入 `PREROUTING` 链，内核根据数据包目的 IP 判断是否需要转送出去。
2. 如果数据包就是进入本机的，它就会沿着图向下移动，到达 `INPUT` 链。数据包到了 INPUT 链后，任何进程都会收到它。
3. 本机上运行的程序可以发送数据包，这些数据包会经过 `OUTPUT` 链，然后到达`POSTROUTING` 链输出。
4. 如果数据包是要转发出去的，且内核允许转发，数据包就会如图所示向右移动，经过 `FORWARD` 链，然后到达 `POSTROUTING` 链输出。

整体数据包分两类：1、发给防火墙本身的数据包 ；2、需要经过防火墙的数据包



## iptables语法

### 安装

```
systemctl stop firewalld //关闭firewalld服务
yum -y install iptables*
systemctl start iptables //启动iptables
vim /etc/sysconfig/iptables  //iptables 配置实例
```



### 语法

`“iptables [-t table] command [链名] [条件匹配] [-j 目标动作]`

如果满足条件，就执行目标(target)中的规则或者特定值。

如果不满足条件，就判断下一条Rules。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/jd6utdfije.png) 

**-t table**

用来指明使用的表，有三种选项: `filter`，`nat`，`mangle`。若未指定，则**默认使用filter表**。

**command参数**

指定iptables 对我们提交的规则要做什么样的操作，以下是command常用参数：

> ```bash
> -A  添加防火墙规则
> -D  删除防火墙规则
> -I  插入防火墙规则
> -F  清空防火墙规则
> -L  列出添加防火墙规则
> -R  替换防火墙规则
> -Z  清空防火墙数据表统计信息
> -P  设置链默认规则
> ```

```bash
iptables -A INPUT -j DROP #拒绝所有人访问服务器（作为最后一条规则），-A Append，追加一条规则
iptables -I INPUT 2 -s 10.10.10.1 -j ACCEPT #允许10.10.10.1主机访问本机，-I  Insert，在指定的位置插入规则
iptables -nvL --line-number  #查看iptables中所有规则
iptables -D INPUT 2  # -D  删除 filter 表 INPUT 链中的第 2 条规则
iptables -P INPUT DROP  #-p  设置 filter 表 INPUT 链的默认规则是 DROP
iptables -F  #清空filter表上所有规则
iptables -F INPUT  iptables -F INPUT
iptables -t nat -F PREROUTING  #清空NAT表中PREROUTING链上的规则 
iptables -Z INPUT  #清除filter表INPUT链上的计数器，-Z清空防火墙数据表统计信息
```

**parameter参数**

```
-p  匹配协议，! 表示取反
-s  匹配源地址
-d  匹配目标地址
-i  匹配入站网卡接口
-o  匹配出站网卡接口
--sport  匹配源端口
--dport  匹配目标端口
--src-range  匹配源地址范围
--dst-range  匹配目标地址范围
--limit  四配数据表速率
--mac-source  匹配源MAC地址
--sports  匹配源端口
--dports  匹配目标端口
-state  匹配状态（INVALID、ESTABLISHED、NEW、RELATED)
--string  匹配应用层字串
--tcp-flags   有效值可以是：SYN, ACK, FIN, RST, URG, PSH,可以使用ALL或者NONE
-–icmp-type ICMP类型 针对-p icmp
  –icmp-type 0 表示Echo Reply
  –icmp-type 8 表示Echo
```

```bash
-i eth0  # 匹配从网络接口eth0进来的数据包
-o eth1  # 匹配从eth1流出的数据包
iptables -A INPUT -s 10.10.10.10 -j DROP  # 拒绝源为10.10.10.10主机访问本机
iptables -A OUTPUT -d www.baidu.com -j DROP  # 禁止本机访问百度
iptables -A INPUT -s 10.10.10.10 -p icmp -j DROP  # 禁止10.10.10.10主机ping通本机
-- sport 23 # 匹配源端口是23的数据包
-- sport 2000:3000 # 匹配源端口是 2000-3000 的数据包
iptables -A INPUT -p tcp –dport 80 -j ACCEPT # --sport 和 --dport 必须配合 -p 参数使用，例如：
```

```bash
**附加模块**
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT # 将目前已运行的服务端口全部放行！无风险，良心推荐使用
iptables -A INPUT -m mac --mac-source xx:xx:xx:xx:xx:xx -j DROP #  拒绝来自某 MAC 地址的数据包进入本机
iptables -A FORWARD -d 192.168.1.1 -m limit --limit 50/s -j ACCEPT # 50/s 表示 1 秒中转发 50 个数据包,按一定速率去匹配
iptables -A INPUT -p tcp -m multiport --dports 22,53,80,443 -j ACCEPT # 多端口匹配 
```



**target参数**

```
ACCEPT  允许数据包通过
DROP  丢弃数据包
REJECT  拒绝数据包通过
LOG  将数据包信息记录 syslog 曰志
DNAT  目标地址转换
SNAT  源地址转换
MASQUERADE  地址欺骗
REDIRECT  重定向
```

```bash
iptables -A INPUT -j ACCEPT # 允许所有访问本机的数据包通过
iptables -A FORWARD -s 10.10.10.10 -j DROP # 阻止来自10.10.10.10 的数据包通过本机
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -j SNAT --to 18.18.18.18 #  将内网 192.168.1.0/24 转换为公网18.18.18.18地址
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -j SNAT --to 18.18.18.18-18.18.18.28
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to 192.168.1.1 # 把从eth0口进来访问TCP/80端口的数据包目的地址改成192.168.1.1
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to 192.168.1.1-192.168.1.10
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE # 将源地址是 192.168.1.0/24 的数据包进行地址伪装，转换成 eth0 上的 IP 地址
```

## 规则备份和恢复

执行 iptables 命令时，规则只是保存在内存中，并没有保存到某一个文件中。因此系统重启之后，之前设定的规则就没有了，所以规则设定完毕，检查无误后要及时保存规则

```bash
service iptables save   # 保存规则
cp /etc/sysconfig/iptables /opt/myipt.rule # 备份规则

iptables-restore < /opt/myipt.rule # 恢复规则
service iptables save
```



## 一些常用配置

```bash
# 1.允许接收远程主机的SSH请求
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

# 2.允许发送本地主机的SSH响应
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# 1.送出的数据包目的端口为22
iptables -A OUTPUT -o eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT

# 2.接收的数据包源端口为22
iptables -A INPUT -i eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT

# 3.允许接收远程主机的HTTP请求
iptables -A INPUT -i eth0 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT

# 4.允许发送本地主机的HTTP响应
iptables -A OUTPUT -o eth0 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT

# 转发端口 DNAT
iptables -t nat -I PREROUTING -p tcp --dport 3389 -j DNAT --to x.x.x.x

# 转发端口 SNAT
iptables -t nat -I POSTROUTING -p tcp --dport 3389 -j MASQUERADE

# 把本地的mysql 3306端口映射出去变成63306
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 63306 -j DNAT --to-destination 127.0.0.1:3306
iptables -t nat -A POSTROUTING -p tcp -m tcp --dport 63306 -j SNAT --to-source 127.0.0.1
# 限制单个来源IP
iptables -t nat -R PREROUTING 4 -s 192.168.40.154 -p tcp -m tcp --dport 63306 -j DNAT --to-destination 127.0.0.1:3306
iptables -t nat -R POSTROUTING 4 -s 192.168.40.154 -p tcp -m tcp --dport 63306 -j SNAT --to-source 127.0.0.1
```































[iptables系列教程（二）| iptables语法规则 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1632774?from=article.detail.1632776&areaSource=106000.2&traceId=5Mp_DWz74hxnYB_OHZmJ5)

[iptables系列教程（一）| iptables入门篇 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1632776)

[Linux下 iptables 超详细教程和使用示例 - 苍青浪 - 博客园 (cnblogs.com)](https://www.cnblogs.com/cangqinglang/p/15438461.html)



