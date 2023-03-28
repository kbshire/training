# Linux日志

日志文件用于记录linux系统的各种运行信息的文件，相当于linux主机的日记，不同的日志文件记载了不同类型的信息,如Linux内核消息、用户登录事件、程序错误等。日志文件对于诊断和解决问题很有帮助，因为linux运行的程序通常把系统的消息和错误写入对应的日志文件，这样系统可以有据可查， 此外,当主机遭受攻击时,日志文件还可以帮助寻找攻击者留下的痕迹。



## 日志文件的分类

**1.内核及系统日志:** 这种日志数据由系统服务rsyslog同-管理,根据其主配置文件/etc/rsyslog.conf中的设置决定将内核消息及各种系统程序消息记录到什么位置。

**2.用户日志:** 用于记录Linux系统用户登录及退出系统的相关信息,包括用户名、登录的终端、登录时间、来源主机、正在使用的进程操作等。

**3.程序日志**：有些应用程序会选择独立管理一份日志文件 ,而不是交给rsyslog服务管理,用于记录本程序运行过程中的各种事件信息。于这些程序只负责管理自己的日志文件,因此不同程序所使用的日志记录格式也会存在较大的差异。



## 常用日志类型

![image-20230328104224178](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230328104224178.png) 



## 常见的日志文件及查看方式

![image-20230328103751396](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230328103751396.png) 



## 日志消息的级别

根据日志信息的重要程度不同，分为不同的级别 **数字级别越小，优先级越高，消息越重要** 级别 英文表示 意义

0 EMERG（紧急） 导致主机系统不可用的情况

1 ALERT（警告） 必须马上采取解决措施

2 CRIT 严重 比较严重的情况

3 ERR 错误 运行出现错误

4 WARNING 提醒 提醒用户的重要事件

5 NOTICE 注意 不会儿影响系统，提醒用户

6 INFO 信息 一般信息

7 DEBUG 调式 程序调式

8 None 没有 不做记录



rsyslog

```
#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log
```

```
[root@localhost ~]# ps aux | grep "rsyslog" | grep -v "grep"
root 1139 0.0 0.2 35948 1500 ? Sl 09：40 0：00 /sbin/rsyslogd -i/var/run/syslogd.pid -c 5
#有rsyslogd服务的进程，所以这个服务已经启动了
[root@localhost ~]# chkconfig --list | grep rsyslog
rsyslog 0：关闭 1：关闭 2：启用 3：启用 4：启用 5：启用 6：关闭
#rsyslog服务在2、3、4、5运行级别上是开机自启动的
```



大部分存放到公共日志文件/var/log/message中，其他的日志文件直接发送给用户或者放到自己独立的日志文件中

```
root@localhost log]# tail -f /var/log/messages
Mar 28 09:56:22 localhost journal[1621]: Skipping refresh of media: Cannot update read-only repo
Mar 28 09:56:24 localhost journal[1621]: Skipping refresh of media: Cannot update read-only repo
Mar 28 09:59:01 localhost systemd[1953]: Starting Cleanup of User's Temporary Files and Directories...
Mar 28 09:59:01 localhost systemd[1953]: Finished Cleanup of User's Temporary Files and Directories.
Mar 28 09:59:06 localhost chronyd[904]: Selected source 44.4.53.4 (2.rhel.pool.ntp.org)
Mar 28 10:08:28 localhost systemd[1]: Starting Cleanup of Temporary Directories...
Mar 28 10:08:28 localhost systemd[1]: systemd-tmpfiles-clean.service: Deactivated successfully.
Mar 28 10:08:28 localhost systemd[1]: Finished Cleanup of Temporary Directories.
Mar 28 10:11:36 localhost cupsd[956]: REQUEST localhost - - "POST / HTTP/1.1" 200 183 Renew-Subscription client-error-not-found
Mar 28 10:24:12 localhost journal[1621]: Skipping refresh of media: Cannot update read-only repo
```

## 用户日志：

存放位置: /var/og/wtmp. /var/log/btmp、 /var/log/lastlog **查询命令: users、who、w、last、 lastlog、 lastb等**

who命令:报告当前登录到系统的每个用户的信息，包括用户名，终端，登录日期，远程主机

w 显示当前操作系统的每个用户及其远程所运行的进程信息

last命令:用于查询成功登录到系统的用户记录,最近的登录情况在最前面。 通过last命令可以及时掌握Linux主机的登录情况,若发现未经授权的用户登陆过,则表示当前主机可能已被入侵。 -a :把从何处登录系统的主机名称或IP地址,显示在最后一 行 -d :将IP地址转换成主机名称 -f 记录文件] :指定记录文件 -R :不显示登入系统的主机名称或IP地址 -x :显示系统关闭,重新开机,以及执行等级的改变等 -n : n代表数字,表示最近n次登录的记录

lastlog命令:用于显示系统中所有用户最近一次登录信息

lastb命令:用于显显用户错误的登录列表,此指令可以发现系统的登录异常。如登录的用户名错误,密码不正确等情况都会记录在案,登录失败的情况属于安全事件,因此消失可能有人在尝试破解密码。除了使用lastb命令以外,可以直接从安全日志文件/var/log/secure中获得相关信息。

```
[root@localhost log]# users 
root

[root@localhost log]# who
root     tty2         2023-03-23 17:52 (tty2)

[root@localhost log]# w
 10:25:20 up 1 day, 32 min,  1 user,  load average: 0.03, 0.01, 0.03
USER     TTY        LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty2      Thu17    4days  0.03s  0.03s /usr/libexec/gnome-session-binary

[root@localhost log]# last
root     tty2         tty2             Thu Mar 23 17:52    gone - no logout
reboot   system boot  5.14.0-70.13.1.e Thu Mar 23 17:52   still running
root     tty3         tty3             Tue Jun 21 10:37 - down  (273+09:52)
root     pts/2        192.168.13.1     Tue Jun 21 10:34 - 12:04 (3+01:30)
kbshire  pts/1        192.168.13.1     Tue Jun 21 10:23 - 10:35  (00:11)
kbshire  tty2         tty2             Tue Jun 21 10:17 - down  (273+10:11)
reboot   system boot  5.14.0-70.13.1.e Tue Jun 21 10:17 - 20:29 (273+10:12)
wtmp begins Tue Jun 21 10:17:12 2022

[root@localhost log]# lastlog
Username         Port     From                                       Latest
root             tty2                                               Thu Mar 23 17:52:50 +0800 2023
bin                                                                 **Never logged in**
daemon                                                              **Never logged in**
adm                                                                 **Never logged in**
lp                                                                  **Never logged in**
sync                                                                **Never logged in**
shutdown                                                            **Never logged in**
halt                                                                **Never logged in**
mail                                                                **Never logged in**
operator                                                            **Never logged in**
games                                                               **Never logged in**
ftp                                                                 **Never logged in**
nobody                                                              **Never logged in**
systemd-coredump                                                    **Never logged in**
dbus                                                                **Never logged in**
polkitd                                                             **Never logged in**
tss                                                                 **Never logged in**
colord                                                              **Never logged in**
clevis                                                              **Never logged in**
rtkit                                                               **Never logged in**
sssd                                                                **Never logged in**
libstoragemgmt                                                      **Never logged in**
setroubleshoot                                                      **Never logged in**
pipewire                                                            **Never logged in**
avahi                                                               **Never logged in**
geoclue                                                             **Never logged in**
flatpak                                                             **Never logged in**
gdm              tty1                                               Thu Mar 23 17:52:20 +0800 2023
cockpit-ws                                                          **Never logged in**
cockpit-wsinstance                                                    **Never logged in**
gnome-initial-setup tty1                                               Tue Jun 21 10:17:17 +0800 2022
sshd                                                                **Never logged in**
chrony                                                              **Never logged in**
dnsmasq                                                             **Never logged in**
tcpdump                                                             **Never logged in**
systemd-oom                                                         **Never logged in**
kbshire          pts/1    192.168.13.1                              Tue Jun 21 10:23:44 +0800 2022
www                                                                 **Never logged in**
pesign                                                              **Never logged in**

[root@localhost log]# lastb 
btmp begins Mon Mar 27 17:38:38 2023

```

## 日志轮替

  如果从一开始的所有记录都记录在一个文件中，势必会造成读写变慢、占用增加，甚至增加由于单个文件损坏导致所有日志丢失的风险。为了解决这个问题，Linux系统采用日志轮替方式，将一段时间以前的记录切割打包到另外的存档文件中，而主日志文件从全新重新开始记录。

​	如果配置文件中有"dateext"参数，那么日志会用日期来作为文件后缀，日志文件名不会重叠，就不会发生旧文件改名

​	当没有这个参数，日志文件会发生改名。当启用新的文件时例如messages，旧的文件会改为messages.1，原来的messages.1文件会变为messages.2，以此类推，过早的文件可能会被压缩，超过最大序号的旧文件会被删除。

​	日志轮替的配置文件为`/etc/logrotate.conf`，同时具体信息也可以保存在`/etc/logrotate.d`目录中，在其中可以设置日志轮替信息

```
# no packages own wtmp -- we'll rotate it here
/var/log/wtmp {
    missingok
    monthly
    create 0664 root utmp
    minsize 1M
    rotate 1
}
```

![image-20230328104122176](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230328104122176.png) 











## 查看日志常用命令

**tail:**

>  -n 是显示行号；相当于nl命令；例子如下：
> ​ **tail -100f test.log 实时监控100行日志**
> ​ tail -n 10 test.log 查询日志尾部最后10行的日志;
> ​ tail -n +10 test.log 查询10行之后的所有日志;

**head:**

>  跟tail是相反的，tail是看后多少行日志，而head是查看日志文件的头多少行，例子如下：
> ​ head -n 10 test.log 查询日志文件中的头10行日志;
> ​ head -n -10 test.log 查询日志文件除了最后10行的其他所有日志;

**cat：**

>  tac是倒序查看，是cat单词反写；例子如下：
> ​ **cat -n test.log |grep "debug" 查询关键字的日志(常用！~)**

**vim：**

>  1、进入vim编辑模式：vim filename 
> 2、输入“/关键字”,按enter键查找 ​
> 3、查找下一个，按“n”即可 ​ 退出：按ESC键后，接着再输入:号时，vi会在屏幕的最下方等待我们输入命令 ​ wq! 保存退出 ​ q! 不保存退出

**ac:**

> 根据/var/log/wtmp文件中的登陆和退出时间报告用户连接的时间（小时），默认输出报告总时间 
> （1）-p：显示每个用户的连接时间 
> （2）-d：显示每天的连接时间 
> （3）-y：显示年份，和-d配合使用





## 使用工具分析

在 CentOS 中自带了一个日志分析工具，就是 logwatch。不过这个工具默认没有安装，所以需要手工安装。安装命令如下：

```
[root@localhost Packages]# yum -y install logwatch
```

安装完成之后，需要手工生成 logwatch 的配置文件。默认配置文件是 /etc/logwatch/conf/logwatch.conf，不过这个配置文件是空的，需要把模板配置文件复制过来。命令如下：

```
[root@localhost ~]# cp /usr/share/logwatch/default.conf/logwatch.conf /etc/logwatch/conf/logwatch.conf
\#复制配置文件
```

这个配置文件的内容中绝大多数是注释，我们把注释去掉，那么这个配置文件的内容如下所示：

```
[root@localhost ~]# vi /etc/logwatch/conf/logwatch.conf
\#查看配置文件
LogDir = /var/log
\#logwatch会分析和统计/var/log/中的日志
TmpDir = /var/cache/logwatch
\#指定logwatch的临时目录
MailTo = root
\#日志的分析结果，给root用户发送邮件
MailFrom = Logwatch
\#邮件的发送者是Logwatch，在接收邮件时显示
Print =
\#是否打印。如果选择“yes”，那么日志分析会被打印到标准输出，而且不会发送邮件。我们在这里不打印，#而是给root用户发送邮件
\#Save = /tmp/logwatch
\#如果开启这一项，日志分析就不会发送邮件，而是保存在/tmp/logwatch文件中
\#如果开启这一项，日志分析就不会发送邮件，而是保存在/tmp/logwatch文件中
Range = yesterday
\#分析哪天的日志。可以识别“All”“Today”“Yesterday”，用来分析“所有日志”“今天日志”“昨天日志”
Detail = Low
\#日志的详细程度。可以识别“Low”“Med”“High”。也可以用数字表示，范围为0～10，“0”代表最不详细，“10”代表最详细
Service = All
\#分析和监控所有日志
Service = "-zz-network"
\#但是不监控“-zz-network”服务的日志。“-服务名”表示不分析和监控此服务的日志
Service = "-zz-sys"
Service = "-eximstats"
```



## 内核日志

Linux内核启动时会加载硬件驱动，在有新硬件时也会加载驱动，如果想要查看内核的活动，可以使用dmesg命令。

Linux内核日志存储在一个ring-buffer中，它的大小是固定的，当队列满时，新的消息会覆盖掉最旧的消息。

实际上，在boot阶段，所有的应用还没有启动，syslogd也未启动，这时内核日志是非常重要的信息。

除了设备初始化日志、内核模块日志，它还会包含一些应用崩溃的相关信息记录，了解dmesg的使用对于调试程序相当重要。


- 直接输入dmesg即可输出所有的mesg信息到终端，有的发行版本可能需要root权限
- 设置不需要root也可以查看：`sudo sysctl -w kernel.dmesg_restrict=0`

```
dmesg -L # color
dmesg -H # human timestamp
dmesg -T # readable timestamp
dmesg --follow # 持续观察输出
dmesg | tail -10 # 最后10行，当然也可以使用其它管线命令，如more，less，grep
```

使用`dmesg -l info`仅输出info级别的日志，这不包括更高级别的日志。

`dmesg -l debug,notice`同时输出多种级别的日志。

```
dmesg -c
```

显示并清除当前的日志内容。

























































































[Linux日志分析工具（logwatch）安装及使用 (biancheng.net)](http://c.biancheng.net/view/1108.html)

[(57条消息) Linux日志管理（日志配置、日志轮替）_linux日志配置文件详解_Zheng__Huang的博客-CSDN博客](https://blog.csdn.net/Zheng__Huang/article/details/108286741)

[linux系统下各种日志文件的介绍，查看，及日志服务配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/298335887)