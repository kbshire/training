# Wireshark 解码https流量

## 服务器私钥

点击编辑——>首选项——>协议——>SSL（有的版本只有TLS），导入RSA key：

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/1497295-20190913153506525-1209501478.png) 

由于通过DH方法交换的密钥不会在中间传递，所以这种方法只能解密通过RSA交换的密钥。

![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/1497295-20190913155902948-1727782905.png) 



## pre_master_secret

通过设置环境变量截取浏览器的pre_master_secret,进而实现解密HTTPS的目的。

环境变量中新建用户变量SSLKEYLOGFILE=路径\sslkey.log文件，之后再wireshark中ssl配置中制定该文件位置即可。

<img src="https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327112217790.png" alt="image-20230327112217790" style="zoom: 67%;" /> 

![image-20230327112342038](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230327112342038.png) 

[Wireshark解密HTTPS流量的两种方法 - 豫让 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yurang/p/11505741.html#:~:text=因此，通过Wir,取到的加密报文。)