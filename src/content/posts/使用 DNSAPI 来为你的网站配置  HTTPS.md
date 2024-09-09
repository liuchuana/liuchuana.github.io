---
title: "使用 DNSAPI 来为你的网站配置  HTTPS"
pubDate: 2021-08-18
description: "使用acme.sh脚本，申请ssl证书并且自动续期证书时间"
categories: ["Linux"]
---

使用 acme.sh 脚本，申请 ssl 证书并且自动续期证书时间

---

### 1、安装**acme.sh**脚本

安装很简单, 一个命令:

```
curl  https://get.acme.sh | sh -s email=my@example.com
```

**PS：这里的 email 填写自己常用的邮箱，直接复制安装的话后续会报错。**

普通用户和 root 用户都可以安装使用.
安装过程进行了以下几步:

1. 把 acme.sh 安装到你的 **home** 目录下:

```
    ~/.acme.sh/
```

并创建 一个 bash 的 alias, 方便你的使用: `alias acme.sh=~/.acme.sh/acme.sh`

2. 自动为你创建 cronjob, 每天 0:00 点自动检测所有的证书, 如果快过期了, 需要更新, 则会自动更新证书.

**安装过程不会污染已有的系统任何功能和文件**, 所有的修改都限制在安装目录中: `~/.acme.sh/`

### 2、使用[阿里云 DNSAPI](https://ak-console.aliyun.com/#/accesskey)自动签发证书

首先，您需要登录您的阿里云帐户以获取您的 API 密钥。 https://ak-console.aliyun.com/#/accesskey
然后设置全局变量

```
    export Ali_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
    export Ali_Secret="jlsdflanljkljlfdsaklkjflsa"
```

现在让我们颁发证书：

```
    acme.sh --issue --dns dns_ali -d example.com -d www.example.com
```

Ali_Key 和 Ali_Secret 将被保存`~/.acme.sh/account.conf`，需要时会被重用。

### copy/安装证书

前面证书生成以后, 接下来需要把证书 copy 到真正需要用它的地方.

注意, 默认生成的证书都放在安装目录下: ~/.acme.sh/, 请不要直接使用此目录下的文件, 例如: 不要直接让 nginx/apache 的配置文件使用这下面的文件. 这里面的文件都是内部使用, 而且目录结构可能会变化.

正确的使用方法是使用 –installcert 命令,并指定目标位置, 然后证书文件会被 copy 到相应的位置。

```
    acme.sh --installcert -d domain.com \
    --key-file /etc/nginx/ssl/example/key.pem \
    --fullchain-file /etc/nginx/ssl/example/cert.pem \
    --reloadcmd "service nginx reload"
```

这里用的是 service nginx force-reload, 不是 service nginx reload, 据测试, reload 并不会重新加载证书, 所以用的 force-reload

### nginx 配置

附带我自己的 nginx 配置

```
server {
    listen       80;
    server_name  example.com www.example.com;
    #http访问重定向至https
    rewrite ^ https://$http_host$request_uri? permanent;
}

server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/example/cert.pem;
        ssl_certificate_key /etc/nginx/ssl/example/key.pem;
         keepalive_timeout   70;
        server_name www.example.com example.com;
        #禁止在header中出现服务器版本，防止黑客利用版本漏洞攻击
        server_tokens off;
        #如果是全站 HTTPS 并且不考虑 HTTP 的话，
        #可以加入 HSTS 告诉你的浏览器本网站全站加密，并且强制用 HTTPS 访问
        #add_header Strict-Transport-Security "max-age=31536000; includeSubdomains";
        fastcgi_param   HTTPS               on;
        fastcgi_param   HTTP_SCHEME         https;
        access_log      /var/log/nginx/wiki.xby1993.net.access.log;
        error_log       /var/log/nginx/wiki.xby1993.net.error.log;
        location / {
                proxy_set_header Host $http_host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_pass         http://127.0.0.1:2368;
        }
}

```
