# tcpdump

```text
tcpdump [ -AdDefIJKlLnNOpqRStuUvxX ] [ -B buffer_size ] [ -c count ]
               [ -C file_size ] [ -G rotate_seconds ] [ -F file ]
               [ -i interface ] [ -j tstamp_type ] [ -m module ] [ -M secret ]
               [ -Q|-P in|out|inout ]
               [ -r file ] [ -s snaplen ] [ -T type ] [ -w file ]
               [ -W filecount ]
               [ -E spi@ipaddr algo:secret,...  ]
               [ -y datalinktype ] [ -z postrotate-command ] [ -Z user ]
               [ expression ]
```

linux系统下执行tcpdump命令需要`root`账号或者具备`sudo`权限的账号，否则执行tcpdump命令说，系统会提示`tcpdump: no suitable device found`。

在下面的例子中，`-i eth0` 参数表示只抓取 eth0 接口数据包，不加`-i eth0` 是表示抓取所有的接口包括 `lo`。

01、抓取所有网络包，并在`terminal`中显示抓取的结果，将包以十六进制的形式显示。

```text
tcpdump
```

02、抓取所有的网络包，并存到 `result.cap` 文件中。

```text
tcpdump -w result.cap
```

03、抓取所有的经过`eth0`网卡的网络包，并存到`result.cap` 文件中。

```text
tcpdump -i eth0 -w result.cap
```

04、抓取源地址是`192.168.1.100`的包，并将结果保存到 `result.cap` 文件中。

```text
tcpdump src host 192.168.1.100 -w result.cap
```

05、抓取地址包含是`192.168.1.100`的包，并将结果保存到 `result.cap` 文件中。

```text
tcpdump host 192.168.1.100 -w result.cap
```

06、抓取目的地址包含是`192.168.1.100`的包，并将结果保存到 `result.cap` 文件中。

```text
tcpdump dest host 192.168.1.100 -w result.cap
```

07、抓取主机地址为 `192.168.1.100` 的数据包

```text
tcpdump -i eth0 -vnn host 192.168.1.100
```

08、抓取包含`192.168.1.0/24`网段的数据包

```text
tcpdump -i eth0 -vnn net 192.168.1.0/24
```

09、抓取网卡`eth0`上所有包含端口`22`的数据包

```text
tcpdump -i eth0 -vnn port 22
```

10、抓取指定协议格式的数据包，协议格式可以是「udp,icmp,arp,ip」中的任何一种,例如以下命令：

```text
tcpdump udp  -i eth0 -vnn
```

11、抓取经过 eth0 网卡的源 ip 是 192.168.1.100 数据包，`src`参数表示源。

```text
tcpdump -i eth0 -vnn src host 192.168.1.100
```

12、抓取经过 eth0 网卡目的 ip 是 192.168.1.100 数据包，`dst`参数表示目的。

```text
tcpdump -i eth0 -vnn dst host 192.168.1.100
```

13、抓取源端口是`22`的数据包

```text
tcpdump -i eth0 -vnn src port 22
```

14、抓取源`ip`是 `192.168.1.100` 且目的`ip`端口是`22`的数据包

```text
tcpdump -i eth0 -vnn src host 192.168.1.100 and dst port 22
```

15、抓取源`ip 192.168.1.100``22`

```text
tcpdump -i eth0 -vnn src host 192.168.1.100 or port 22
```

16、抓取源`ip 192.168.1.100``22`

```text
tcpdump -i eth0 -vnn src host 192.168.1.100 and not port 22
```

17、抓取源`ip`是`192.168.1.100`且目的端口是`22`，或源`ip`是`192.168.1.102`且目的端口是`80`的数据包。

```text
tcpdump -i eth0 -vnn ( src host 192.168.1.100 and dst port 22 ) or ( src host 192.168.1.102 and dst port 80 )
```

18、把抓取的数据包记录存到`/tmp/result`文件中，当抓取`100`个数据包后就退出程序。

```text
tcpdump –i eth0 -vnn -w /tmp/result -c 100
```

19、从`/tmp/result`记录中读取`tcp`协议的数据包

```text
tcpdump -i eth0  tcp  -vnn -r /tmp/result
```

20、想要截获所有`192.168.1.100`的主机收到的和发出的所有的数据包：

```text
tcpdump host 192.168.1.100
```

21、如果想要获取主机`192.168.1.100`除了和主机`192.168.1.101`之外所有主机通信的ip包，使用命令：

```text
tcpdump ip host 192.168.1.100 and ! 192.168.1.101
```

22、如果想要获取主机 `192.168.1.100` 接收或发出的 `telnet` 包，使用如下命令：

```text
tcpdump tcp port 23 host 192.168.1.100
```

[超详细的网络抓包神器 Tcpdump 使用指南 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1589827)