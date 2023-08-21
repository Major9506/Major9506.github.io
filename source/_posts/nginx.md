---
title: nginx 常用使用方法总结
categories: 前端
tags: node
date: 2023-8-21
---

## 1 前言   

Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好。

Nginx的工作在网络的7层之上，可以针对http应用做一些分流的策略，比如针对域名、目录结构；Nginx对网络的依赖比较小；Nginx安装和配置比较简单，测试起来比较方便；也可以承担高的负载压力且稳定，一般能支撑超过1万次。

### 1 基础配置   

Nginx是一款高性能的Web服务器/反向代理服务器，同时也提供了IMAP/POP3/SMTP服务。   

下面是一个简单的Nginx配置文件示例：   

```bash
# 全局配置
user nginx;
worker_processes auto; # 根据CPU核心数自动设置工作进程数
pid /run/nginx.pid; # PID文件路径
include /etc/nginx/modules-enabled/*.conf; # 加载模块配置

events {
    worker_connections 1024; # 每个工作进程最大连接数
}

http {
    include /etc/nginx/mime.types; # 包含MIME类型定义
    default_type application/octet-stream; # 默认MIME类型
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"'; # 日志格式
    access_log /var/log/nginx/access.log main; # 访问日志路径和格式
    sendfile on; # 开启高效传输模式
    keepalive_timeout 65; # 保持连接超时时间

    server {
        listen 80; # 监听端口
        server_name example.com; # 域名
        root /var/www/example.com; # 网站根目录
        index index.html; # 默认首页文件名

        location / {
            try_files $uri $uri/ =404; # 尝试访问的文件或目录
        }
    }
}
```

### 2.nginx 负载均衡如何配置   

Nginx是一款高性能的HTTP和反向代理服务器，它可以用来配置负载均衡。在配置Nginx负载均衡时，需要在Nginx配置文件中指定负载均衡策略和后端服务器地址。

Nginx支持多种负载均衡算法，包括轮询、IP Hash、加权轮询、最小连接数等。下面是一个简单的Nginx负载均衡配置示例：   

```bash
http {
    upstream backend {
        server backend1.example.com;
        server backend2.example.com;
        server backend3.example.com;
    }

    server {
        location / {
            proxy_pass http://backend;
        }
    }
}
```

### 3.nginx 配置代理，解决前后端联调跨域问题

利用nginx来解决前后端端联通跨域问题，原理主要是将前端静态资源与后端接口代理到统一服务下。主要是利用 nginx 的做接口转发。   

Nginx可以通过反向代理实现接口转发。下面是一个简单的Nginx反向代理配置示例：   

```bash
http {
    upstream backend {
        server 192.168.1.100:8080; # 后端服务器地址和端口号
    }

    server {
        listen 80; # 监听端口
        server_name example.com; # 域名

        location /api/ {
            proxy_pass http://backend; # 将请求转发到后端服务器
        }
    }
}
```

以上就是 nginx 的基础使用总结。

