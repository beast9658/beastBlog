---
title: 彻底搞懂nginx
date: 2022-10-14 19:56:14
---
# 什么是nginx？

nginx是一个开源可靠的HTTP中间件代理服务。

# 配置目录

## 主配置文件

/etc/nginx/nginx.conf  
配置文件基本结构：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ed9a1394519f48c2a309824318238891~tplv-k3u1fbpfcp-zoom-1.image)

# nginx 开启，关闭，重启

开启 systemctl start nginx 关闭 nginx -s stop 重启，nginx -s reload （这个一般是在修改配置文件后，先用nginx -t 检查配置语法是否正确后，再用nginx -s reload 平滑重启）

## 日志文件

/var/log/nginx

# 发生错误的时候查找方法

最重要的就是错误日志，错误日志，错误日志，当发生错误的时候，首先第一要想到去查看错误日志，查看发生错误的原因。

-   1，首先查看错误日志 tail -n 10 /var/log/nginx/error.log
-   2，找到错误原因，修改配置文件 /etc/nginx/conf.d/default.conf
-   3，查看修改的错误命令是否争取 nginx -t
-   4，平滑重启 nginx -s reload

# server_name

## 优先级

1，当在多个serve中匹配了相同的server_name 最先出现的优先级是最高的。

# location

## location 匹配规则

-   1 = 表示精确匹配。只有请求的url路径与后面的字符串完全相等时，才会命中。
-   2 ~ 表示该规则是使用正则定义的，区分大小写。
-   3 ~* 表示该规则是使用正则定义的，不区分大小写。
-   4 ^~ 表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找。 匹配顺序
-   1 如果匹配到 = ， 也就是精确匹配到url，则返回
-   2 如果匹配到^~,则返回，并且不会想下匹配。这里我曾经遇到过在同一服务器上同时部署两个react单页项目的时候，一个react A 项目放在/home目录下，一个react B项目放在/codsse目录下，因为写了如下规则

```
^~ /codsse {
    ...
}
复制代码
```

导致当url是/codesse/的时候，可以匹配到B项目的index.html,但是同时在B中index.html中的js也匹配到了B项目的index.html,所以导致形成了一个死循环。

-   3 接下来匹配正则表达式的，~ 或者~*的，如果匹配到则返回
-   4 最后都没匹配到，则返回最大匹配的或者404
-   5 = > ^~ > ~/~* 优先级

# try_files

try_files 试图寻找的文件

# alias与root的区别

root会把匹配到location连在一起去访问 比如有以下配置

```
location /cat {
    root /cat/imgs/;
}
复制代码
```

当访问 xx/cat/a.png的时候，实际上是到 /cat/imgs/cat/a.png中去寻找 当alias是/cat/imgs 的时候，同样的网址，实际上到/cat/imgs/a.png中去寻找

# nginx 的访问控制

## http_access_module

deny 拒绝访问  
allow 可以访问

# 作为静态资源服务器

## 压缩静态资源

1， gzip：on  
        gzip_comp_level: 2 (压缩级别)  


## 设置过期时间

一般浏览器首先会检查max-age，如果过期，则会发送etag和Last-Modified到服务器验证 1， 设置过期时间  
       expires

# 正向代理和反向代理

## 正向代理？

正向代理就是代理客户端发起请求，比如我们经常使用的vpn

## 反向代理？

就是在服务器端进行代理，比如你访问一个域名，并不知道真正的实际资源是否通过nginx给代理到哪里去了。

# 跨域访问

设置可以nginx允许可以跨域访问

```
location /sddss/ {
       proxy_redirect off; # 关闭重定向
       add_header Access-Control-Allow-Origin *;
   }
复制代码
```

# 负载均衡

所谓的负载均衡其实就是通过nginx把请求转发到不同的服务器上面去。 每个服务可以配置一些参数，比如weight权重，比如max_fails等 负载均衡也可以选择不同的算法，比如轮询，比如ip—hash算法和url—hash算法等

```
upsteam ii {
    serve 1;
    serve 2;
}
server {
    location / {
        proxy_pass ii;
        include proxy_params;
    }
}
复制代码
```

# nginx 作为缓存服务器

nginx 作为中间缓存，使用proxy_cache_path

# nginx 重定向url

可以通过rewrite 根据正则匹配，把匹配到的url重定向到别的url中去

## rewrite 语法

rewrite 正则 flag  
flag 包括break，last,redirect，permanent break会直接去查找  
而last相当于会继续向下匹配  
rediect 临时重定向  
permanent 永久重定向

## rewrite 优先级

sever 中的rewrite规则 > location 中的rewrite规则

# nginx 链接周期限制

## 使用模块

secure_link_module 原理就是首先nginx根据一些信息返回下载链接包括md5和expries，然后下载的时候，nginx在进行验证md5和过期时间。 返回链接如下： /download?mad5=dsdsd&expries=112233222

```
location ~ {
   secure_link
   secure_link_md5
}
复制代码
```

# nginx 区分国内外ip

## 使用模块

http_geoip_module 可以根据访问的ip是否是国内ip，来进行限制访问

```
location / {
    if ($geoip_country_code != CN) {
        return 403
    }
}
复制代码
```

# nginx https

## https 配置

*1 安装openssl http_ssl_module *2 生成CA证书 *3 配置

```
server {
    listen      443;
    server_name www.onedns.net;
    ssl         on;
    ssl_certificate     /etc/nginx/ssl/111net.pem;
    ssl_certificate_key /etc/nginx/ssl/2222.key;
    ssl_protocols       Tddsdss;
    ssl_ciphers         dddssdss;
复制代码
```

## https 服务优化

*1 激活keepalive 长链接 *2 激活ssl cache 缓存

## nginx+ Lua

## Lua 基础语法

-   1 ~= 表示不等于
-   2 字符串拼接 ..

## 根据ip进行不同内容的访问

基本原理： 当用户访问的时候，nginx 获得用户的ip，然后通过lua去访问ip数据库，然后让不同的ip访问不同的内容。

```
location / {
    default_type "text/html";
    content_by_lua_file /opt/as.lua;
}
复制代码
```

# nginx 性能优化

## ab接口压力测试工具

ab -n 2000 -c 2 [http://127.0.0.1](https://link.juejin.cn?target=http%3A%2F%2F127.0.0.1 "http://127.0.0.1")

## cpu亲和

设置worker process 为cpu核数

# nginx 安全

## 常见的攻击手段

-   1 密码撞库
-   2 文件上传漏洞
-   3 sql 注入

  