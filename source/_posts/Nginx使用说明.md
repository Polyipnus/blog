---
title: Nginx使用说明
date: 2017-08-10 11:18:54
tags:
---

## 反向代理（reverse proxy）

> 终结客户端连接，并且生成另一个新的连接，新的连接代表客户端向上游服务器（upstream server）生成连接。

- proxy_pass指令

参数说明

    # 没有必要重写Location头, 将proxy_redirect指令设置为off;
    proxy_redirect off;
    
    # 设置Host头, 因此上游服务器能够将请求映射到一个虚拟服务器
    # 否则使用用户输入的URL中的主机部分
    # X-Real-IP 和X-Forwarded-For头有相似的目的都用于转发客户端IP
    # 地址到上游服务器得到信息
    # $X-Real_IP变量在remote_addr头内使用就是Nginx接受客户端请求的IP地址
    # $proxy_add_x_forwarded_for变量包含在X-Forwarded-For头中，它源于客户端请求,跟随有$remote_addr变量
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    
    # client_max_body_size指令不是严格的代理模块指令，但与代理相关
    # 如果这个值设置太低，将不能上传文件到上流服务器上，在设置需要注意
    # 通过web窗口上传的文件大小通常要大于它在文件系统中的大小
    client_max_body_size 10m;
    
    client_connetct_timeout 30;
    client_body_buffer_size 128k;
    
    # 在建立与上游服务器初始连接的过程中，proxy_connect_timeout指令表明
    # Nginx将会等待的时间长度
    proxy_connect_timeout 30;
    
    # 设置Nginx同上游服务器连接成功再次操作的等待时间
    proxy_send_timeout 15;
    proxu_read_timeout 15;
    
    # FreeBSD系统下有效，并且在该协议下传输数据之前
    # 指定套接字发送缓冲应该容纳的字节数
    proxy_send_lowat 12000;
    
    proxy_buffer_size 4k;
    proxy_buffers 4 32k;
    proxy_busy_buffers_size 64k;
    
    # 控制worker进程阻塞后台数据的显示器。值越大，处理阻塞的时间越长
    proxy_temp_file_write_size 64k;

例子

    server {
    
        listen 80;
    
        location ~^/local {
            proxy_pass http://localhost:8080;
        }
    
        location / {
            include proxy.conf;
    
            # 覆盖原有值
            client_max_body_size 50m;
    
            rewrite /(.*)$ /index.html?page=$1 break;
            proxy_pass http://localhost:8080;
        }
    }
    
    
- upstream 模块

