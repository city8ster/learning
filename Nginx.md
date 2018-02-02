# Nginx

【1-1，1-2】
高效、可靠

基础篇
    快速安装
    配置语法
    默认模块
    Nginx 的 log
    访问限制
        HTTP 的请求和连接
        请求限制与连接限制
        access 模块配置语法
        请求限制局限性
        基本安全认证
        auth 模块配置语法
        安全认证局限性
场景实践篇
    静态资源 WEB 服务
        什么是静态资源
        静态资源服务场景
        静态资源服务配置
        客户端缓存
        静态资源压缩
        防盗链
        跨域访问
    代理服务
    负载均衡
    缓存服务
深度学习
    动静分离
    rewrite 规则
    进阶模块配置
    HTTPS 服务
        HTTPS 协议
        配置语法
        Nginx 的 HTTPS 服务
        苹果要求的 HTTPS 服务
    Nginx 与 LUA 开发
架构篇
    常见问题
    Nginx 中间件性能优化
        如何调适性能优化
        性能优化影响因素
        操作系统性能优化
        Nginx 性能优化
    Nginx 与安全
    新版本特性
    中间件架构设计
    
    
    
 
yum list|grep gcc
iptables -L
iptables -F
iptables -t nat -F
getenforce
setenforce 0
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake
yum -y install wget httpd-tools vim
cd /opt/
ls
    app/
    backup/
    download/
    logs/
    work/


【2-1~11】
为什么选择 Nginx
原因1：IO 多路复用 epoll
原因2：轻量级、模块化
原因3：CPU 亲和

nginx.org

添加yum源安装nginx
vim /etc/yum.repos.d/nginx.repo
    [nginx]
    name=nginx repo
    baseurl=http://nginx.org/packages/centos/7/$basearch/
    gpgcheck=0
    enabled=1
yum list |grep nginx
yum install nginx
ls
nginx -v
nginx -V

基本参数使用
    安装目录
        命令：rpm -ql nginx
    编译参数
        命令：nginx -V
    Nginx 基本配置语法
    


cd /etc/nginx/
ls
vim nginx.conf
cd /etc/nginx/conf.d/
ls
vim default.conf
ls /usr/share/nginx/html/

systemctl restart nginx.service 修复配置后重启服务
或 systemctl reload nginx.service


curl http://baidu.com
curl -v http://www.imooc.com/ > /dev/null

tail -f 文件名
tail -n 200 文件名

log_format main '$http_user_agent' '$remote_addr - $remote_user [$time_local] "$request"'
                '$status $body_bytes_sent "$http_referer"'
                '"$http_user_agent" "$http_x_forwarded_for"';
access_log      /var/log/nginx/access.log main

nginx -t -c /etc/nginx/nginx.conf 检查配置文件的正确与否
nginx -s reload -c /etc/nginx/nginx.conf
ps -aux|grep nginx

curl http://127.0.0.1


vim default.conf
server {
    location /mystatus {
        stub_status;
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

curl -v http://127.0.0.1/mystatus




vim /etc/nginx/conf.d/default.conf
server {
    location / {
        root /opt/app/code/;
        random_index on;
    }
}
nginx -tc /etc/nginx/nginx.conf
systemctl status rsyslog.service
systemctl reload nginx

mkdir /opt/app/code/
ls
    1.html, 2.html, 3.html .3.html  不会选择以 . 开头的隐藏文件作为随机选择的文件
ps -aux|grep nginx
ip a
curl http://127.0.0.1


vim /etc/nginx/conf.d/default.conf
server {
    location / {
        root /opt/app/code;
        index index.html index.htm;
        sub_filter '<a>imooc</a>' '<a>IMOOC</a>';  默认只替换第一个，要把 sub_filter_once 关闭才能完成所有的替换
        sub_filter_once off;
    }
}

systemctl reload nginx

vim /opt/app/code/submodule.html






limit_conn_zone $binary_remote_addr zone=conn_zone:1m;
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s;
server {
    location / {
        root /opt/app/code;
        #limit_conn conn_zone 1;
        #limit_req zone=req_zone burst=3 nodelay;
        #limit_req zone=req_zone burst=3;
        #limit_req zone=req_zone;
        index index.html index.htm
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

// ab 工具单位时间请求 
ab -n -50 -c 20 http://127.0.0.1/1.html
tail -f /var/log/nginx/error.log








limit_conn_zone $binary_remote_addr zone=conn_zone:1m;
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s;
server {
    location / {
        root /opt/app/code;
        #limit_conn conn_zone 1;
        #limit_req zone=req_zone burst=3 nodelay;
        #limit_req zone=req_zone burst=3;
        limit_req zone=req_zone;
        index index.html index.htm
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

ab -n -20 -c 20 http://127.0.0.1/1.html
tail -f /var/log/nginx/error.log








limit_conn_zone $binary_remote_addr zone=conn_zone:1m;
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s;
server {
    location / {
        root /opt/app/code;
        #limit_conn conn_zone 1;
        limit_req zone=req_zone burst=3 nodelay;
        #limit_req zone=req_zone burst=3;
        #limit_req zone=req_zone;
        index index.html index.htm
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

ab -n -20 -c 20 http://127.0.0.1/1.html
tail -f /var/log/nginx/error.log






limit_conn_zone $binary_remote_addr zone=conn_zone:1m;
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s;
server {
    location / {
        root /opt/app/code;
        limit_conn conn_zone 1;
        #limit_req zone=req_zone burst=3 nodelay;
        #limit_req zone=req_zone burst=3;
        #limit_req zone=req_zone;
        index index.html index.htm
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

ab -n -20 -c 20 http://127.0.0.1/1.html
tail -f /var/log/nginx/error.log




cd /etc/nginx/conf.d/
mv default.conf access_mod.conf  在 /etc/nginx/conf.d/nginx.conf 中有 include /etc/nginx/conf.d/*.conf 会将 conf.d 目录下的所有配置文件 .conf 引进来
server {
    location / {
        root /opt/app/code;
        index index.html index.htm;
    }
    loation ~ ^/admin.html {  模式匹配
        root /opt/app/code;
        deny 222.128.189.17; 限制自己的 IP 不能访问
        allow all;
        index index.html index.htm;
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf




vim /etc/nginx/conf.d/access_mod.conf
server {
    location / {
        root /opt/app/code;
        index index.html index.htm;
    }
    loation ~ ^/admin.html {  
        root /opt/app/code;
        allow 222.128.189.0/24;  允许 IP 段访问
        deny all;
        index index.html index.htm;
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf





rpm -qf /usr/bin/htpasswd
yum install httpd-tools -y
htpasswd -c /etc/nginx/auth_conf jeson   用户名 jeson
cd /etc/nginx/conf.d/
mv default.conf auth_mod.conf
vim auth.mod.conf
server {
    location ~ ^/admin.html {
        root /opt/app/code;
        auth_basic "Auth access test! Input your password!";
        auth_basic_user_file /etc/nginx/auth_conf;
        index index.html index.htm
    }
}

nginx -t -c /etc/nginx/ngin.conf
nginx -s reload -c /etc/nginx/nginx.conf
浏览器访问服务器地址 http://服务器地址/admin.html 输入用户名及密码




 



进阶学习 - 常见 Nginx 中间架构
cd /etc/nginx/conf.d/
ls
    static_server.conf
vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        #gzip on;
        #gzip_http_version 1.1;
        #gzip_comp_level 2;
        #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        #gzip on;
        #gzip_http_version 1.1;
        #gzip_comp_level 1;
        #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        gzip_static on;
        tcp_nopush on;
        root /opt/app/code;
    }
}

    
cd /opt/app/code/images/
ls
    wei.png
vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        #gzip on;
        #gzip_http_version 1.1;
        #gzip_comp_level 2;
        #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        #gzip on;
        #gzip_http_version 1.1;
        #gzip_comp_level 1;
        #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        #gzip_static on; 关闭
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
http://116.62.103.228/wei.png 打开 chrome 开发者工具 Network 查看到图片大小为 239 KB


vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        gzip on; 打开
        gzip_http_version 1.1; 打开
        gzip_comp_level 2; 打开
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png; 打开
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        #gzip on;
        #gzip_http_version 1.1;
        #gzip_comp_level 1;
        #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        #gzip_static on; 关闭
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
http://116.62.103.228/wei.png 打开 chrome 开发者工具 Network 查看到图片大小为 233 KB




vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        gzip on;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        gzip off; 关闭
        gzip_http_version 1.1;
        gzip_comp_level 1;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        #gzip_static on; 关闭
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
http://116.62.103.228/access.log 打开 chrome 开发者工具 Network 查看到文件为 174 KB



vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        gzip on;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        gzip on; 打开
        gzip_http_version 1.1;
        gzip_comp_level 1;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        #gzip_static on; 关闭
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
http://116.62.103.228/access.log 打开 chrome 开发者工具 Network 查看到文件为 23.5 KB




vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        gzip on;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        gzip on; 打开
        gzip_http_version 1.1;
        gzip_comp_level 1;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        gzip_static off; 关闭
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
gzip /opt/app/code/test.img
ls /opt/app/code/
    test.img.gz
http://116.62.103.228/download/test.img 报错找不到



vim static_server.conf
server {
    listen 80;
    server_name 116.62.103.228 jeson.imooc.com;
    
    sendfile on;
    #charset koi8-r
    access_log /var/log/nginx/log/static_access.log main
    
    
    location ~ .*\. (jpg|gif|png)$ {
        gzip on;
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/images;
    }
    
    location ~ .*\. (txt|xml)$ {
        gzip on; 打开
        gzip_http_version 1.1;
        gzip_comp_level 1;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/-httpd-php image/jpeg image/gif image/png;
        root /opt/app/code/doc;
    }
    
    location ~ ^/download {
        gzip_static on;
        tcp_nopush on;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
ls /opt/app/code/
    test.img.gz
    http://116.62.103.228/download/test.img 找到





vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (htm|html)$ {
        #expires 24h;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
ls /opt/app/code/
    test_expires.html
http://116.62.103.228/test_expires.html    200 OK
http://116.62.103.228/test_expires.html    304 OK  request:Cache-Control:max-age=0 是由浏览器加上的而不是服务端返回的
 

vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (htm|html)$ {
        expires 24h;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
http://116.62.103.228/test_expires.html    304  OK response:Cache-Control:max-age=86400;Expires:...









// jeson.imooc.com 
vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (htm|html)$ {
        #add_header Access-Control-Allow-Origin http://www.jesonc.com;
        #add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf

// www.jesonc.com 
cat /opt/app/code/test_origin.html
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <title>测试 ajax 和跨域访问</title>
        <script src="http://libs.baidu.com/jquery/2.1.4/jquery.min.js"></script>
    </head>
    <script type="text/javascript">
        $(document).ready(function () {
            $.ajax({
                type: "GET",
                url: "http://jeson.imooc.com/1.html",
                success: function (data) {
                    alert("success!");
                },
                error: function () {
                    alert("fail! 请刷新再试！");
                }
            });
        });
    </script>
    <body>
        <h1>测试跨域访问</h1>
    </body>
</html>
可以打开 chrome 的开发者工具 将 Disable Cache 关闭掉避免受缓存影响
访问 http://www.jesonc.com/test_origin.html 弹出“fail!请刷新再试！"，控制台报错



// jeson.imooc.com 
vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (htm|html)$ {
        add_header Access-Control-Allow-Origin http://www.jesonc.com; 打开（写 * 的话则允许所有的站点，则容易受 CSRF 攻击了）
        add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;
        root /opt/app/code;
    }
}
nginx -s reload -c /etc/nginx/nginx.conf

访问 http://www.jesonc.com/test_origin.html 弹出“success!"，控制台报错





防盗链
vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (jpg|gif|png)$ {
        ... ...
        #valid_referers none blocked 116.62.103.228;
        #if ($invalid_referer) {
        #    return 403;
        #}
        ... ...
    }
}
nginx -s reload -c /etc/nginx/nginx.conf

ls /opt/app/code
    test_refer.html
    <html>
        <head>
            <meta charset="utf-8">
            <title>imooc</title>
        </head>
        <body>
            <img src="http://116.62.103.228/wei.png" />
        </body>
    </html>
浏览器访问 http://116.62.103.228/test_refer.html
查看访问日志 http_refer





vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (jpg|gif|png)$ {
        ... ...
        valid_referers none blocked 116.62.103.228;  
        if ($invalid_referer) {
            return 403;
        }
        ... ...
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
curl -I http://116.62.103.228/wei.png 只发起头信息
查看访问日志 http_refer 上一次页面打开的 URL 地址 允许因为没有 http_refer 信息

curl -e "http://baidu.com" -I http://116.62.103.228/wei.png
出现 403 Forbidden

curl -e "http://116.62.103.228" -I http://116.62.103.228/wei.png
成功 200 OK
查看日志




vim /etc/nginx/conf.d/static_server.conf
server {
    location ~ .*\. (jpg|gif|png)$ {
        ... ...
        valid_referers none blocked 116.62.103.228 ~/google\./; 也可以支持匹配的写法 
        if ($invalid_referer) {
            return 403;
        }
        ... ...
    }
}








cd /etc/nginx/conf.d
ls
    fx_proxy.conf  realserver.conf
vim realserver.conf
server {
    listen 8080;
    server_name localhost jeson.t.imooc.io;
    
    location / {
        root /opt/app/code2;
        index index.html index.htm
    }
    
    #location ~ \.php$ {
    #    proxy_pass http://127.0.0.1;
    #}
}

vim fx_proxy.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;

    location ~ /test_proxy.html$ {
        proxy_pass http://127.0.0.1:8080;
    }
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

ls /opt/app/code2/
    test_proxy.html
    <html>
        <head>
            <meta charset="utf-8">
            <title>imooc1</title>
        </head>
        <body>
            <h1>Welcome to jeson.t.imooc.io</h1>
        </body>
    </html>
netstat -luntp|grep nginx

jeson.t.imooc.io:8080/test_proxy.html 外网无法访问 
jeson.t.imooc.io/test_proxy.html 外网可以访问 并通过反向代理到 8080 端口的资源




vim fx_proxy.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;

    #location ~ /test_proxy.html$ {
    #    proxy_pass http://127.0.0.1:8080;
    #}
}

nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

jeson.t.imooc.io/test_proxy.html 访问出现 404 Not Found






cd /opt/app/code/
ls
    jesonc.html
<html>
    <head>
        <meta charset="utf-8">
        <title>jeson1</title>
    </head>
    <body>
        <h1>Jeson</h1>
    </body>
</html>
cd /etc/nginx/conf.d/
ls
    admin.conf
vim admin.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    
    location / {
        if ( $http_x_forwarded_for !~* "^116\.62\.103\.228") {
            return 403;
        }
        root /opt/app/code;
        index index.html index.htm
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

客户端直接访问 jeson.t.imooc.io/jesonc.html 出现 403 Forbidden



到 116.62.103.228 服务器上
ip a
cd /etc/nginx/conf.d/
ls
    zx_proxy.conf
vim zx_proxy.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    
    resolver 8.8.8.8 需要用到 DNS 解析
    location / {
        proxy_pass http://$http_host$request_uri;    其中 host 为主机名 request_uri 为后面的参数
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
在客户端代理 chrome 插件 SwitchySharp （将其中的代理地址填成上述的 116.62.103.228 地址）    若是 IE 可在其中设置
访问 jeson.t.imooc.io/jesonc.html 访问成功













cd /etc/nginx/conf.d
vim proxy.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    
    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_redirect default;
        
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote-addr;
        
        proxy_connect_timeout 30;
        proxy_send_timeout 60;
        proxy_read_timeout 60;
        
        proxy_buffer_size 32k;
        proxy_buffering on;
        proxy_buffers 4 128k;
        proxy_busy_buffers_size 256k;
        proxy_max_temp_file_size 256k;
    }
}

nginx -V 查看临时文件存在哪 *-temp-path








vim /etc/nginx/conf.d/proxy.conf
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;  从另一个配置中读取
    }
}
vim /etc/nginx/proxy_params.conf
    proxy_redirect default;
    
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    
    proxy_connect_timeout 30;
    proxy_send_timeout 60;
    proxy_read_timeout 60;
    
    proxy_buffer_size 32k;
    proxy_buffering on;
    proxy_buffers 4 128k;
    proxy_busy_buffers_size 256k;
    proxy_max_temp_file_size 256k;
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf





负载均衡
服务器 1
cat /opt/app/code1/index.html
<h1>Server 1<h1>
cat /opt/app/code2/index.html
<h1>Server 2<h1>
cat /opt/app/code3/index.html
<h1>Server 3<h1>

cd /etc/nginx/conf.d/
ls
    server1.conf
    server2.conf
    server3.conf
vim server1.conf
server {
    listen 8001;
    server_name localhost;
    
    location / {
        root /opt/app/code1;
        index index.html index.htm
    }
}
vim server2.conf
server {
    listen 8002;
    server_name localhost;
    
    location / {
        root /opt/app/code2;
        index index.html index.htm
    }
}
vim server3.conf
server {
    listen 8003;
    server_name localhost;
    
    location / {
        root /opt/app/code3;
        index index.html index.htm
    }
}nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问测试
116.62.103.228:8001;
116.62.103.228:8002;
116.62.103.228:8003;



服务器 2
cd /etc/nginx/conf.d/
vim upstream_test.conf
upstream imooc {
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_pass http://imooc;
        include proxy_params;
    }
}

vim /etc/nginx/proxy_params.conf
    proxy_redirect default;
    
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    
    proxy_connect_timeout 30;
    proxy_send_timeout 60;
    proxy_read_timeout 60;
    
    proxy_buffer_size 32k;
    proxy_buffering on;
    proxy_buffers 4 128k;
    proxy_busy_buffers_size 256k;
    proxy_max_temp_file_size 256k;
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

访问 jeson.t.imooc.io 会轮询 server1 server2 server3


测试 server2 崩掉
服务器 1
iptables -I INPUT -p tcp --dport 8002 -j DROP

访问 jeson.t.imooc.io 会轮询 server1 server3 （ server2 挂掉不再使用 ）








服务器 1
vim /etc/nginx/conf.d/upstream_test.conf
upstream imooc {
    server 116.62.103.228:8001 down; 下线
    server 116.62.103.228:8002 backup; 备份    
    server 116.62.103.228:8003 max_fails=1 fail_timeout=10s;
}
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_pass http://imooc;
        include proxy_params;
    }
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
iptables -F
访问 jeson.t.imooc.io; 只有 server3 提供服务
iptables -i INPUT -p tcp --dport 8003 -j DROP
访问 jeson.t.imooc.io; server2 备份提供服务
iptables -F
访问 jeson.t.imooc.io; server3 提供服务









vim /etc/nginx/conf.d/upstream_test.conf
upstream imooc {
    ip_hash;
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_pass http://imooc;
        include proxy_params;
    }
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io; 只停留在一台服务器（ip_hash）






服务器 1
cd /etc/nginx/conf.d/
ls
    server1.conf（server 116.62.103.228:8001）
    server2.conf（server 116.62.103.228:8002）
    server3.conf（server 116.62.103.228:8003）
cd /opt/app/
ls
    code1/
        index.html
        url1.html(server1 url1)
        url2.html(server2 url2)
        url3.html(server3 url3)
    code2/
        index.html
        url1.html(server2 url1) 
        url2.html(server2 url2)
        url3.html(server2 url3)
    code3/
        index.html
        url1.html(server3 url1)
        url2.html(server3 url2)
        url3.html(server3 url3)
服务器 2
vim /etc/nginx/conf.d/upstream_test.conf
upstream imooc {
    #hash $request_uri;
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_pass http://imooc;
        include proxy_params;
    } 
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/url1.html 会轮询 server1 server2 server3 的 url1.html




vim /etc/nginx/conf.d/upstream_test.conf
upstream imooc {
    hash $request_uri; 同一个参数会定位到同一台服务器上
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_pass http://imooc;
        include proxy_params;
    } 
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/url1.html  总会定位到一台服务器
访问 jeson.t.imooc.io/url2.html  总会定位到另一台服务器











缓存服务
服务器 1
netstat -luntp |grep 800
    server1.conf（server 116.62.103.228:8001）
    server2.conf（server 116.62.103.228:8002）
    server3.conf（server 116.62.103.228:8003）

服务器 2
cd /etc/nginx/conf.d/
ls
    cache_test.conf
vim cache_test.conf
upstream imooc {
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}

proxy_cache_path /opt/app/cache levels=1:2 keys_zone=imooc_cache:10m max_size=10g inactive=60m use_temp_path=off;
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_cache imooc_cache;
        proxy_pass http://imooc;
        proxy_cache_valid 200 304 12h;
        proxy_cache_valid any 10m;
        proxy_cache_key $host$uri$is_args$args;
        add_header Nginx-Cache "$upstream_cache_status";
        
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        include proxy_params;
    } 
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/url3.html 缓存生效总是定位在一台服务器
ls /opt/app/cache/
rm -rf /opt/app/cache/*
访问 jeson.t.imooc.io/url3.html 缓存生效总是定位在一台服务器
ls /opt/app/cache 又有新的缓存文件生成



vim cache_test.conf
upstream imooc {
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}

proxy_cache_path /opt/app/cache levels=1:2 keys_zone=imooc_cache:10m max_size=10g inactive=60m use_temp_path=off;
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    location / {
        proxy_cache off; 关闭缓存
        proxy_pass http://imooc;
        proxy_cache_valid 200 304 12h;
        proxy_cache_valid any 10m;
        proxy_cache_key $host$uri$is_args$args;
        add_header Nginx-Cache "$upstream_cache_status";
        
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        include proxy_params;
    } 
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/url3.html 开始轮询 server1 server2 server3











vim cache_test.conf
upstream imooc {
    server 116.62.103.228:8001;
    server 116.62.103.228:8002;
    server 116.62.103.228:8003;
}

proxy_cache_path /opt/app/cache levels=1:2 keys_zone=imooc_cache:10m max_size=10g inactive=60m use_temp_path=off;
server {
    listen 80;
    server_name localhost jeson.t.imooc.io;
    
    if ($request_uri ~ ^/(url3|login|register|password\/reset)) {
        set $cookie_nocache 1;
    }
    
    location / {
        proxy_cache imooc_cache;
        proxy_pass http://imooc;
        proxy_cache_valid 200 304 12h;
        proxy_cache_valid any 10m;
        proxy_cache_key $host$uri$is_args$args;
        proxy_no_cache $cookie_nocache $arg_nocache $arg_comment; 不进行缓存的部分配置：以变量来控制（若 $cookie_nocache 为 1 则不进行缓存）
        proxy_no_cache $http_pragma $http_authorization; 不进行缓存的部分配置
        add_header Nginx-Cache "$upstream_cache_status";
        
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        include proxy_params;
    } 
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
rm -rf /opt/app/cache/*

访问 jeson.t.imooc.io/url1.html 只停留在一个页面 （缓存了）
访问 jeson.t.imooc.io/url3.html 轮询 server1 server2 server3 （不被缓存）




深度学习篇
tomcat
动静分离：
    减少服务端不必要的请求运算：静态资源、动态资源
cd /opt/app/code/
ls
    test_mysite.html
    img/
cat test_mysite.html
<html lang="en">
    <head>
        <meta charset="utf-8">
        <title>测试 Ajax 和跨域访问</title>
        <script src="http://libs.baidu.com/jquery/2.1.4/jquery.min.js"></script>
    </head>
    <script>
        $(document).ready(function (){
            $.ajax({
                type: "GET",
                url: "http://jeson.t.imooc.io/java_test.jsp",
                success: function (data) {
                    $("#get_data").html(data);
                },
                error: function () {
                    alert("fail! 请刷新后再试");
                }
            });
        });
    </script>
    <body>
        <h1>测试动静分离</h1>
        <img src="http://jeson.t.imooc.io/img/nginx.png" />
        <div id="get_data"></div>
    </body>
</html>

ls /opt/app/code/img/
    nginx.jpg

cd /etc/nginx/conf.d/
vim test_mysite.conf
upstream java_api {
    server 127.0.0.1:8080; 默认的 tomcat 端口
}
server {
    listen 80;
    server_name localhost;
    root /opt/app/code;  将 root 目录写在前面这里后面的 location 默认是这个不目录则不用重复写了
    
    location ~ \.jsp$ {   动态资源（如请求 jsp php 等文件）去请求 8080 端口
        proxy_pass http://java_api;
        index index.html index.htm
    }
    location ~ \.(jpg|png|gif)$ { 静态资源请求 80 端口
        expires 1h;
        gzip on;
    }
    location / {
        index index.html index.htm
    }
}

cd /opt/app/apache-tomcat-9.0.0.M22/
cd bin/
sh catalina.sh stop
ps -aux |grep java
kill 21570
ps -aux|grep java
sh catalina.sh start;tail -f ../logs/catalina.out
netstat -luntp|grep 8080
cd /etc/nginx/
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
游览器访问 jeson.t.imooc.io/test_mysite.html
  关闭动态请求（关闭 Tomcat）
cd /ipt/app/apache-tomcat-9.0.0.M22/
cd bin/
sh catalina.sh stop
ps -aux|grep java
kill 22173
netstat -luntp|grep 8080
再访问 jeson.t.imooc.io/test_mysite.html  出现 "fail! 请刷新再试"



$ pcretest
> /(\d+)\.(\d+)\.(\d+)\.(\d+)/  正则规则
> 192.162.2.1 要匹配的数据
0:192.162.2.1
1:192
2:168
3:2
4:1
> fadsdf
No match

cd /etc/nginx/conf.d/
vim test_rewrite.conf
server {
    listen 80 default_server;
    server_name jeson.t.imooc.io;
    
    root /opt/app/code;
    
    location ~ ^/break {
        rewrite ^/break /test break; 跳转到 主机/test/ 不存在
    }
    
    location ~ ^/last {
        rewrite ^/last /test/ last; 跳转到 主机/test/ 并重新建立一次请求（此时相当于访问 主机/test/ ）
    }
    
    location /test/ {
        default_type application/json;
        return 200 '{"status": "success"}';
    }

}
nginx -tc /etc/nginx/nginx.conf
nginx -s -reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/test/ 返回 { status: success }
访问 jeson.t.imooc.io/break/ 返回 404 Not Found
访问 jeson.t.imooc.io/last/ 返回 { status: success }




vim test_rewrite.conf
server {
    listen 80 default_server;
    server_name jeson.t.imooc.io;
    
    root /opt/app/code;
    
    location ~ ^/break {
        rewrite ^/break /test break;
    }
    
    location ~ ^/last {
        rewrite ^/last /test/ last; 日志会收到两请求 302 200
        #rewrite ^/last /test/ redirect; 日志只收到一个请求 200
    }
    
    location ~ ^/imooc {
        #rewrite ^/imooc http://www.imooc.com/ permanent;
        #rewrite ^/imooc http://www.imooc.com/ redirect;
    }
    
    location /test/ {
        default_type application/json;
        return 200 '{"status": "success"}';
    }

}
nginx -tc /etc/nginx/nginx.conf
nginx -s -reload -c /etc/nginx/nginx.conf
curl -vL jeson.t.imooc.io/last 查看整个请求的过程
  相当于客户端发起了两次请求
  先收到 302 Moved Temporarily 状态 及 Location: http://jeson.t.imooc.io/test
  (再请求 http://jeson.t.imooc.io/test)然后收到 200 OK 状态
查看日志 tail -f /var/log/nginx/log/host.access.log




vim test_rewrite.conf
server {
    listen 80 default_server;
    server_name jeson.t.imooc.io;
    
    root /opt/app/code;
    
    location ~ ^/break {
        rewrite ^/break /test break;
    }
    
    location ~ ^/imooc {
        #rewrite ^/imooc http://www.imooc.com/ permanent;
        rewrite ^/imooc http://www.imooc.com/ redirect;
    }
    
    location /test/ {
        default_type application/json;
        return 200 '{"status": "success"}';
    }

}
nginx -tc /etc/nginx/nginx.conf
nginx -s -reload -c /etc/nginx/nginx.conf
jeson.t.imooc.io/imooc 访问会被重定向到 www.imooc.com

nginx -s stop -c /etc/nginx/nginx.conf 关闭服务端的 nginx
jeson.t.imooc.io/imooc 访问会出现错误说明服务端 nginx 已关闭


vim test_rewrite.conf
server {
    listen 80 default_server;
    server_name jeson.t.imooc.io;
    
    root /opt/app/code;
    
    location ~ ^/break {
        rewrite ^/break /test break;
    }
    
    location ~ ^/imooc {
        rewrite ^/imooc http://www.imooc.com/ permanent;
        #rewrite ^/imooc http://www.imooc.com/ redirect;
    }
    
    location /test/ {
        default_type application/json;
        return 200 '{"status": "success"}';
    }

}
nginx -c /etc/nginx/nginx.conf 启动 nginx
jeson.t.imooc.io/imooc 访问会重定向
nginx -s stop -c /etc/nginx/nginx.conf 关闭服务端的 nginx
jeson.t.imooc.io/imooc 访问仍会重定向（客户端会永久保留需要重定向的结果，不会再向服务端发起请求） 






vim test_rewrite.conf
server {
    listen 80;
    server_name localhost;
    
    root /opt/app/code;
    
    location / {
        rewrite ^/course-(\d+)-(\d+)-(\d+)\.html$ /course/$1/$2/course_$3.html break;
        if ($http_user_agent ~* Chrome) { 如果客户端是 Chrome 且访问以 /nginx 开头则进行跳转
           rewrite ^/nginx http://coding.imooc.com/class/121.html redirect;    
        }
        
        if (!-f $request_filename) { 如果所请求的文件名不存在则进行重定向转发
            rewrite ^/(.*)$ http://www.imooc.com/$1 redirect;
        }
        index index.html index.htm
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf

ls /opt/app/code/
    course/
            11/
                22/
                    oourse_33.html
访问时 http://servername/course/11/22/course_33.html 但不利于搜索引擎的收录 利用 rewrite 改写 URL
访问 http://jeson.t.imooc.io/course-11-22-33.html 可以访问到实际的页面 /course/11/22/course_33.html
Chrome 访问 http://jeson.t.imooc.io/nginx 跳转
jeson.t.imooc.io/memcached 会被重定向到 www.baidu.com



Nginx 高级模块
cd /etc/nginx/conf.d/
nginx -V 确认编译的时候已将安全模块 http_secure_link_module 编译进去了
vim test_safe_down.conf
server {
    listen 80;
    server_name localhost;
    
    root /opt/app/code;
    
    location / {
        secure_link $arg_md5,$arg_expires;  取参数值：加密值、过期值
        secure_link_md5 "$secure_link_expires$uri imooc";   其中 imooc 是加密字符串
        
        if ($secure_link = "") {
            return 403;
        }
        
        if ($secure_link = "0") {
            return 410;
        }
    }
}
ls /opt/app/code/download/
    file.img
cd /opt/work/
vim md5url.sh 用于生成加密内容 需要用到 openssl 命令 没有的令需要装一下
#!/bin/sh
#
#Auth: Jeson@imooc.com
server_name="jeson.t.imooc.io"
download_file="/download/file.img"
time_num=$(date -d "2018-10-18 00:00:00" +%s)
secret_num="imooc"
res=$(echo -n "${time_num}${download_file} ${secret_num}"|openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =)
echo "http://${servername}${download_file}?md5=${res}&expires=${time_num}"

sh md5url.sh 会生成加密的下载地址
http://jeson.t.imooc.io/download/file.img?md5=dafdljkjkl&expires=2435454 访问即可下载 改一下 expires 值再访问则会失败 403 错误



yum install nginx-module-geoip
ls /etc/nginx/modules/
    ngx_http_geoip_module-debug.so
    ngx_stream_geoip_module-debug.so
    ngx_http_geoip_module.so
    ngx_stream_geoip_module.so
vim nginx.conf 需要将模块 load 进去
vim /opt/work/download.geoip.sh 下载 IP 的地域文件及城市数据文件
    #!/bin/sh
    wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz
    wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz
sh /opt/work/download.geoip.sh
gunzip GeoIP.dat.gz
gunzip GeoLiteCity.data.gz
cd /etc/nginx/conf.d/
vim test_geo.conf
geoip_country /etc/nginx/geoip/GeoIP.dat;
geoip_city /etc/nginx/geoip/GeoLiteCity.dat;
server {
    listen 80;
    server_name localhost;
    
    location / {
        if ($geoip_country_code != CN) {
            return 403;
        }
        
        root /usr/share/nginx/html;
        index index.html index.htm
    }
    
    location /myip {
        default_type text/plain;
        return 200 "$remote_addr $geoip_country_name $geoip_country_code $geoip_city";
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问浏览器 116.62.103.228/myip 输出 IP 地区 地区码 城市
开启代理（国外） 返回 403 错误


HTTPS
自签证书
确认安装了 openssl
openssl -> 生成密钥 -> CSR 签名文件 -> 向第三方签名证书



cd /etc/nginx/
ls
openssl
rpm -qa|grep open
mkdir ssl_key
cd ssl_key/
openssl genrsa -idea -out jesonc.key 1024回车之后需输自定义密码
openssl req -new -key jesonc.key -out jeson.csr 之后输入身份相关信息
ls
    jesonc.csr
    jesonc.key
openssl x509 -req -days 3650 -in jesonc.csr -signkey jesonc.key -out jesonc.crt   其中 3650 为十年过期


HTTPS 配置
    SSL
openssl req -days 36500 -x509 -sha256 -nodes -newkey rsa:2048 -keyout jesonc.key -out jesonc_apple.crt

vim /etc/nginx/conf.d/test_https.conf jesonc
server {
    listen 443;
    server_name 116.62.103.228 jeson.t.imooc.io;
    ssl on;
    #ssl_certificate /etc/nginx/ssl_key/jesonc.crt;
    ssl_certificate /etc/nginx/ssl_key/jesonc_apple.crt;
    ssl_certificate_key /etc/nginx/ssl_key/jesonc_key;
    #ssl_certificate_key /etc/nginx/ssl_key/jesonc_nopass.key;
    
    index index.html index.htm;
    location / {
        root /opt/app/code;
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
netstat -luntp|grep 443
访问 116.62.103.228/jesonc.html



openssl rsa -in ./jesoncold.key -out ./jesoncou_nopass.key 这样再用到的时候就不用输入保护码了








vim /etc/nginx/conf.d/test_https.conf
server {
    listen 443;
    server_name 116.62.103.228 jeson.t.imooc.io;
 
    keepalive_timeout 100;
    
    ssl on;
    ssl_ssession_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    #ssl_certificate /etc/nginx/ssl_key/jesonc.crt;
    ssl_certificate /etc/nginx/ssl_key/jesonc_apple.crt;
    ssl_certificate_key /etc/nginx/ssl_key/jesonc.key;
    #ssl_certificate_key /etc/nginx/ssl_key/jesonc_nopass.key;
    
    index index.html index.htm
    location / {
        root /opt/app/code;
    }   
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 116.62.103.228/jesonc.html







Nginx 与 Lua 开发
yum install lua
$ lua
> print("Hello world!");


vim test.lua
#!/usr/bin/lua
print("I'm Jeson!");

chmod a+rx ./test.lua
./test.lua 执行
    I'm Jeson!





www.imooc.com/article/19597  Nginx 编译安装 Lua 模块
灰度发布
yum install memcached
cd /opt/app/
ls 
    tomcat8080/
    tomcat9090/
cd tomcat9090/conf/
vim server.xml  默认 TOMCAT 端口为 8080 需要更改(这里相关的端口 8 开头的都被改成了 9)
    <Connector port="9009" protocol="AJP/1.3" redirectPort="9443" />
    <Connector port="9090" protocol="HTTP/1.1" connectionTimeout="2000" redirectPort="8443" />
    <Server port="9005" shutdown="SHUTDOWN">
ls tomcat8080/webapps/ROOT/
    java_test.jsp
ps -aux|grep java
cd /opt/app/tomcat8080/bin/
sh catalina.sh start; tail -f ../logs/catalina.out 启动 Tomcat 8080
cd /opt/app/tomcat9090/bin
sh catalina.sh start; tail -f ../logs/catalina.out 启动 Tomcat 9090
netstat -luntp
ps -aux|grep mem
memcached -p11211 -u nobody -d 启动 memcahe
netstat -luntp|grep 11211 

cd /etc/nginx/conf.d/
vim dep.conf
server {
    listen 80;
    server_name localhost;
    
    location /hello {
        default_type 'text/plain';
        content_by_lua 'ngx.say("hello", lua)';
    }
    
    location /myip {
        default_type 'text/plain';
        content_by_lua '
            clientIP = ngx.req.get_headers()["x_forwarded_for"]
            ngx.say("IP:", clientIP)
            ';
    }
    
    location / {
        default_type "text/html";
        content_by_lua_file /opt/app/lua/dep.lua;   都是 Lua 的程序 （file）
        #add_after_body "$http_x_forwarded_for";
    }
    
    location @server{
        proxy_pass http://127.0.0.1:9090
    }
    
    location @server_test {
        proxy_pass http://127.0.0.1:8080 
    }
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/hello
访问 jeson.t.imooc.io/myip


vim /opt/app/lua/dep.lua
clientIP = ngx.req.get_headers()["X-Real-IP"]   约定的变量在 headers 中（优先取）
if clientIP = nil then
    clientIP = ngx.req.get_headers()["x_forwarded_for"]
end
if clientIP = nil then
    clientIP = ngx.var.remote.addr
end
    local memcached = require "resty.memcached"   调用 memcache 的模块
    local memc, err = memcached:new() 实例化对象
    if not memc then
        ngx.say("failed to instantiate memc: ", err)
        return
    end
    local ok, err = memc: connect("127.0.0.1", 11211) 连接
    if not ok then
        ngx.say("failed to connect: ", err)
        return
    end
    local res, flags, err = memc:get(clientIP)   获取 KEY 为 IP 的值
    ngx.say("value key: ", res, clientIP)
    if err then
        ngx.say("failed to get clientIP ", err)
        return
    end
    if res == "1" then
        ngx.exec("@server_test" )
        return
    end
    ngx.exe("@server")

安装 Lua 调用 memcache 的方式
cd /opt/work/
cat install_memcache_lua.sh
wget https://github.com/agentzh/lua-resty-memcached/archive/v0.11.tar.gz
tar -zxvf v0.11.tar.gz
cp -r lua-resty-memcached-0.11/ib/resty /usr/local/share/lua/5.1/

telnet 127.0.0.1 11211
get 103.215.191.72
    VALUE 103.215.191.72 0 1
    1
    END
set 103.215.191.99 0 0 1
1
    STORED
get 103.215.191.99
    VALUE 103.215.191.99 0 1
    1
    END


nginx -s reload -c /etc/nginx/nginx.conf
cd /opt/app/tomcat8080/webapps/ROOT/
cat java_test.jsp
<%@ page language="java" import="java.util.*" pageEncodeing="utf-8"%>
<HTML>
    <HEAD>
        <TITLE>JSP Test Page</TITLE>
    </HEAD>
    <BODY>
        <%
            Random rand = new Random();
            out.println("<h1>Test server</h1>");
            out.println("<h1>Random number :</h1>");
            out.println(rand.nextInt(99)+100);
        %>
    </BODY>
</HTML>
cd /opt/app/tomcat8080/bin/
sh catalina.sh stop;
ps -aux|grep java
sh catalina.sh start; tail -f ../logs/catalina.out  重启 Tomcat 服务
cd /opt/app/tomcat9090/bin/
sh startup.sh start; tail -f ../logs/catalina.out
访问 jeson.t.imooc.io/java_test.jsp 再换一个 IP 代理来访问










架构篇
cd /etc/nginx/conf.d/
ls
    test_server1.conf
    test_server2.conf
    test_server3.conf
cat test_server1.conf
server {
    listen 80;
    server_name testserver1 jeson.t.imooc.io;
    
    location / {
        root /opt/app/code1;
        index index.html index.htm;
    }
}
cat test_server2.conf
server {
    listen 80;
    server_name testserver2 jeson.t.imooc.io;
    
    location / {
        root /opt/app/code2;
        index index.html index.htm;
    }
}
diff test_server1.conf test_server2.conf
cat test_server3.conf
server {
    listen 80;
    server_name testserver3 jeson.t.imooc.io;
    
    location / {
        root /opt/app/code3;
        index index.html index.htm;
    }
}

cat /opt/app/code1/index.html
<html>
    <head>
        <meta charset="utf-8">
        <title>Server 1</title>
    </head>
    <body>
        <h1>Server 1</h1>
    </body>
</html>
cat /opt/app/code2/index.html
<html>
    <head>
        <meta charset="utf-8">
        <title>Server 2</title>
    </head>
    <body>
        <h1>Server 2</h1>
    </body>
</html>
cat /opt/app/code1/index.html
<html>
    <head>
        <meta charset="utf-8">
        <title>Server 3</title>
    </head>
    <body>
        <h1>Server 3</h1>
    </body>
</html>
nginx -t -c /etc/nginx/nginx.conf 出现 warn 但依然成功 （warn 被忽略）
nginx -s reload -c /etc/nginx/nginx.conf 同样出现 warn 但依然成功
ps -aux|grep nginx
不断刷新 jeson.t.imooc.io 访问的都是 server1 的内容
mv /etc/nginx/conf.d/test_server1.conf /etc/nginx/conf.d/test_server5.conf
ls /etc/nginx/conf.d/
    test_server2.conf
    test_server3.conf
    test_server5.conf
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
再访问 jeson.t.imooc.io 访问都是 server2 的内容
    按文件名优先读取的配置来使用







vim /etc/nginx/nginx.conf
listen 80;
server_name testserver1 jeson.t.imooc.io;

root /opt/app;
location = /code1/ {   优先级最高
    rewrite ^(.*)$ /code1/index.html break;
}
location ~ /code.* {   优先级最低
    rewrite ^(.*)$ /code3/index.html break;
}
location ^~ /code {    优先级居中
    rewrite ^(.*)$ /code2/index.html break;
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
访问 jeson.t.imooc.io/code1/ 





vim /etc/nginx/nginx.conf
listen 80;
server_name testserver1 jeson.t.imooc.io;

root /opt/app;
location / {
    root /opt/app/code/cache;
    try_files $uri $java_page;
}
location @java_page {
    proxy_pass http://127.0.0.1:9090;
}
nginx -t -c /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
cat /opt/app/code/cache/jesonc.html
<html>
    <head>
        <meta charset="utf-8">
        <title>Server 1</title>
    </head>
    <body>
        <h1>page 1</h1>
    </body>
</html>
cat /opt/app/tomcat9090/webapps/ROOT/jesonc.html
<html>
    <head>
        <meta charset="utf-8">
        <title>Server 1</title>
    </head>
    <body>
        <h1>Page 3</h1>
    </body>
</html>
ps -aux|grep java  需要启动 Tomcat
访问 jeson.t.imooc.io/jesonc.html 出现 page 1 内容
mv /opt/app/code/cache/jesonc.html /opt/app/code/cache/test.html
再访问 jeson.t.imooc.io/jesonc.html 出现 page 3 内容





yum install httpd-tools
vim /etc/nginx/conf.d/test_server1.conf
server {
    listen 80;
    server_name testserver1 jeson.t.imooc.io;
    
    location / {
        root /opt/app/code;
        try_files /cache $uri $java_page;
    }
    
    location @java_page {
        proxy_pass http://127.0.0.1:9090
    }
}
nginx -s reload -c /etc/nginx/nginx.conf
ab -n 2000 -c 2 http://127.0.0.1/jesonc.html

cat /opt/app/tomcat9090/webapps/ROOT/sleepjava.jsp
<%@ page language="java" import="java.util.*" pageEncoding="utf-8" %>
<HTML>
    <HEAD>
        <TITLE>JSP Test Page</TITLE>
    </HEAD>
    <BODY>
        <%
            Thread.sleep(5000);
            Random rand = new Random();
            out.println("<h1>Random number:</h1>");
            out.println(rand.newInt(99)+100);
        %>
    </BODY>
</HTML>
ab -n 2000 -c 10 http://127.0.0.1/sleepjava.jsp
ps -aux|grep java
curl -I http://127.0.0.1/sleepjava.jsp 由于 Tomcat 关闭了 返回 502 错误
cd /opt/app/tomcat9090/bin
sh catalina.sh start; tail -f ../log/catalina.out
netstat -luntp|grep java
freem -m
curl -I http://127.0.0.1/sleepjava.jsp 返回 200 成功
ab -n 20 -c 2 http://127.0.0.1/sleepjava.jsp




mv /opt/app/tomcat9090/webapps/ROOT/jesonc.html /opt/app/tomcat9090/webapps/ROOT/jesonc.html.bak
ab -n 2000 -c 2 http://127.0.0.1/jesonc.html
动静分离 Nginx 处理静态资源非常快






修改文件句柄
vim /etc/security/limits.conf
root soft nofile 65535
root hard nofile 65535
*    soft nofile 25535
*    hard nofile 25535
修改完退出终端并重新登录就生效了




cat /proc/cpuinfo|grep 'physical id'|soft|uniq|wc -l
cat /proc/cpuinfo|grep "cpu cores"|uniq 查看几核
cat /proc/cpuinfo|grep "processor"|wc -
cat /proc/cpuinfo|grep "processor"|wc -l
cat /proc/cpuinfo|grep "physical id"|sort|uniq|wc -l 查看 CPU 个数
top
再按 1 键（？E 键） 就能查看系统的 CPU 核心运行状态



ps -eo pid,args,psr |grep [n]ginx 查看 Nginx 在使用 CPU 的哪个 核心






systemctl start mariadb
systemctl status mariadb

php-fpm -D 以守护进程启动


Nginx + Lua 防火墙
yum install git
cd /opt/download/
git clone https://github.com/loveshell/ngx_lua_waf.git
cd ngx_lua_waf
mv /opt/download/ngx_lua_waf/ /etc/nginx/waf/
cd waf
ls
    config.lua
    init.lua
    install.sh
    README.md
    wafconf
    waf.lua
vim config.lua
    RulePath = "/etc/nginx/waf/wafconf"  关键字文件目录
    attacklog = "on" 记录日志
    CCDeny = "on"  对访问频率进行限制 CC 攻击
    CCrate = "600/60" 每 60 秒访问 600 次

ls /etc/nginx/waf/wafconf/ 匹配关键字规则
    args
    cookie
    post
    url
    user-agent
    whiteurl

vim /etc/nginx/nginx.conf
http {
    lua_package_path "/etc/nginx/waf/?.lua";
    lua_shared_dict limit 10m;
    init_by_lua_file /etc/nginx/waf/init.lua;
    access_by_lua_file /etc/ngix/waf/waf.lua;
}
nginx -tc /etc/nginx/nginx.conf
nginx -s reload -c /etc/nginx/nginx.conf
netstat -lunp|grep
nginx -c /etc/nginx/nginx.conf

curl -I http://116.62.103.228/2.html  成功 200
ab -n 2000 -c 200 http://116.62.103.228/2.html   海量攻击
curl -I http://116.62.103.228/2.html     503



静态资源服务
    类型分类：视频、图片、HTML
    缓存：时长（短、中、长）
    防盗链
    流量限制
    防资源盗用
    静态资源压缩：模式、比率
代理服务
    协议
    正向
    反向
        哈希
    缓存
    头信息处理
    分片
动静分离



了解原理！！！
分析日志

