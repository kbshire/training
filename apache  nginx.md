# apache / nginx 

1.编译安装apache

[(58条消息) CentOS-8.4 上安装 Apache2 Web 服务器A_sngee的博客-CSDN博客](https://blog.csdn.net/sngee/article/details/119491845)

2.实现https访问apache

[(58条消息) httpd实现https_httpd https_桜千梦的博客-CSDN博客](https://blog.csdn.net/yingqianmeng/article/details/101769625)

[(58条消息) Apache httpd 配置HTTPS SSL访问 443_httpd 443_普通网友的博客-CSDN博客](https://blog.csdn.net/sinat_28371057/article/details/112449727)

```
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
LoadModule log_config_module modules/mod_log_config.so

```

把上面两行注释去掉

```
./configure  --prefix=/usr/local/httpd  --with-pcre=/usr/local/pcre  --with-apr=/usr/local/apr   --with-apr-util=/usr/local/apr-util  --enable-so --enable-rewriter --enable-charset-lite --enable-cgi  --enable-ssl
编译安装ssl命令
```

![image-20230329143600431](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230329143600431.png) 





2.安装nginx

1.[(58条消息) Nginx：Nginx添加SSL实现HTTPS访问_ITKaven的博客-CSDN博客](https://blog.csdn.net/qq_37960603/article/details/112371339)

[Nginx配置SSL证书实现https访问及端口映射 - shileishmily - 博客园 (cnblogs.com)](https://www.cnblogs.com/shileibrave/p/14582263.html#:~:text=Nginx配置SSL证书实现https访问及端口映射 1 1、依赖,2 2、openssl生成证书 3 3、修改Nginx配置)

[Nginx配置Https（详细、完整） - huiblog - 博客园 (cnblogs.com)](https://www.cnblogs.com/ambition26/p/14077773.html)

2.实现https访问nginx

```nginx 
http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  121.37.191.89;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        #rewrite ^(.*)$ https://$host$1 permanent;
        location / {
            root   html;

            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
  server {
        listen       443 ssl;
        server_name  121.37.191.89:443;

        ssl_certificate      /usr/local/nginx/ssl/nginx.crt;
        ssl_certificate_key  /usr/local/nginx/ssl/nginx.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            root html;
            index index.html;
            proxy_pass http://121.37.191.89;
        }
    }

```

![image-20230329143429274](https://kbshire-1308981697.cos.ap-shanghai.myqcloud.com/img/image-20230329143429274.png) 