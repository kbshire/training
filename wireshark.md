# wireshark 

wireshark安装完后如果权限不够需要用管理员权限启动

下面是一个wireshark主页面

![image-20230322121441606](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322121441606.png) 

上面是曾经打开过的wireshark文件，下面是网卡

![image-20230322122146849](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322122146849.png) 

不同颜色代表了不同的协议类型



![img](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/20200410151631573.png) 





最上面是过滤器，可以从抓到的所有包中筛选



捕获过滤器

捕获过滤器表达式作用在wireshark开始捕获数据包之前，只捕获符合条件的数据包，不记录不符合条件的数据包。

**捕获过滤器的语法格式为：**
`<Protocol> <Direction> <Host> <Value> <Logical Operation> <other expression>`

![image-20230322130031614](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322130031614.png) 





显示过滤器

显示过滤器表达式作用在在wireshark捕获数据包之后，从已捕获的所有数据包中显示出符合条件的数据包，隐藏不符合条件的数据包

![image-20230322122129477](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322122129477.png) 

```
使用多个过滤器 not and or 
host 192.168.1.100 and tcp port 80
基于长度过滤使用 less ，greater关键字   
len <= 12
```



对包中内容作为过滤条件

![image-20230322132151822](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322132151822.png) 



data contains "union select"



追踪流

![image-20230322131817562](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322131817562.png)

追踪流一般在流量分析或者ctf中使用较多

比如http contains“flag”

![image-20230322133043527](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230322133043527.png) 

[wireshark流量分析实战_wireshark实战案例_humble嘻的博客-CSDN博客](https://blog.csdn.net/qq_43571759/article/details/105434350)