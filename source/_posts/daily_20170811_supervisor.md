---
title: supervisor 快速进阶
date:  2017-08-10 11:10:14
tags:
       - 工具学习
---

> supervisor是python实现的进程管理工具，能将命令行进程变为后台daemon。管理进程的启动，重启，关闭。
> 
> 有兴趣的话可以看下[官方详细介绍](http://supervisord.org/index.html)。


## 1. 安装说明

    1. Debian/Ubuntu可以直接通过apt-get安装:
        
       apt-get install supervisor

    2. supervisor配置文件

       echo_supervisord_conf > /etc/supervisord.conf

<!-- more -->

## 2. 配置说明

   下面是配置内容说明

        [unix_http_server]
        file=/tmp/supervisor.sock   ; UNIX socket 文件，supervisorctl 会使用
        ;chmod=0700                 ; socket 文件的 mode，默认是 0700
        ;chown=nobody:nogroup       ; socket 文件的 owner，格式： uid:gid
        
        ;[inet_http_server]         ; HTTP 服务器，提供 web 管理界面
        ;port=127.0.0.1:9001        ; Web 管理后台运行的 IP 和端口，如果开放到公网，需要注意安全性
        ;username=user              ; 登录管理后台的用户名
        ;password=123               ; 登录管理后台的密码
        
        [supervisord]
        logfile=/tmp/supervisord.log    ; 日志文件，默认是 $CWD/supervisord.log
        logfile_maxbytes=50MB           ; 日志文件大小，超出会 rotate，默认 50MB
        logfile_backups=10              ; 日志文件保留备份数量默认 10
        loglevel=info                   ; 日志级别，默认 info，其它: debug,warn,trace
        pidfile=/tmp/supervisord.pid    ; pid 文件
        nodaemon=false                  ; 是否在前台启动，默认是 false，即以 daemon 的方式启动
        minfds=1024                     ; 可以打开的文件描述符的最小值，默认 1024
        minprocs=200                    ; 可以打开的进程数的最小值，默认 200
        
        ; the below section must remain in the config file for RPC
        ; (supervisorctl/web interface) to work, additional interfaces may be
        ; added by defining them in separate rpcinterface: sections
        [rpcinterface:supervisor]
        supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
        
        [supervisorctl]
        serverurl=unix:///tmp/supervisor.sock   ; 通过UNIX socket连接supervisord
        serverurl=http://127.0.0.1:9001         ; 通过 HTTP 的方式连接 supervisord
        
        [include]                               ; 包含其他的配置文件
        files = relative/directory/*.ini        ; 可以是 *.conf 或 *.ini


## 3. 进程使用例子

    使用例子说明

        [unix_http_server]
        file=/tmp/supervisor.sock   ; UNIX socket 文件，supervisorctl 会使用
        ;chmod=0700                 ; socket 文件的 mode，默认是 0700
        ;chown=nobody:nogroup       ; socket 文件的 owner，格式： uid:gid

        ;[inet_http_server]         ; HTTP 服务器，提供 web 管理界面
        ;port=127.0.0.1:9001        ; Web 管理后台运行的IP 和端口，如果开放到公网需要注意安全性
        ;username=user              ; 登录管理后台的用户名
        ;password=123               ; 登录管理后台的密码
        
        [supervisord]
        logfile=/tmp/supervisord.log    ; 日志文件，默认是 $CWD/supervisord.log
        logfile_maxbytes=50MB           ; 日志文件大小，超出会 rotate，默认 50MB
        logfile_backups=10              ; 日志文件保留备份数量默认 10
        loglevel=info                   ; 日志级别，默认 info，其它: debug, warn, trace
        pidfile=/tmp/supervisord.pid    ; pid 文件
        nodaemon=false                  ; 是否在前台启动，默认是 false，即以 daemon 的方式启动
        minfds=1024                     ; 可以打开的文件描述符的最小值，默认 1024
        minprocs=200                    ; 可以打开的进程数的最小值，默认 200
        
        ; the below section must remain in the config file for RPC
        ; (supervisorctl/web interface) to work, additional interfaces may be
        ; added by defining them in separate rpcinterface: sections
        [rpcinterface:supervisor]
        supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
        
        [supervisorctl]
        serverurl=unix:///tmp/supervisor.sock   ; 通过UNIX socket连接supervisord
        serverurl=http://127.0.0.1:9001         ; 通过HTTP的方式连接 supervisord

        [include]                               ; 包含其他的配置文件
        files = relative/directory/*.ini        ; 可以是 *.conf 或 *.ini


## 4. 配置引用

将进程配置添加到supervisord.conf文件中, 推荐将各个进程配置放到单独的文件中，然后通过[include]配置项引用

    
    [include]
    files = /etc/supervisor/*.conf

## 5. 服务启动

    
    supervisord -c /etc/supervisord.conf 

## 6. 服务客户端管理

可以使用supervisord的命令行客户端管理工具supervisorctl，来管理进程。


    $ supervisorctl status
    $ supervisorctl stop usercenter
    $ supervisorctl start usercenter
    $ supervisorctl restart usercenter
    $ supervisorctl reread
    $ supervisorctl update

## 7. supervisor web管理界面

在浏览器中输入`http://127.0.0.1:9001`，可进入web管理界面


    [inet_http_server]      ; HTTP 服务器，提供 web 管理界面
    ; port=127.0.0.1:9001   ; Web 管理后台运行的IP和端口，如果开放到公网，需要注意安全性
    ; username=user         ; 登录管理后台的用户名
    ; password=123          ; 登录管理后台的密码


## 参考引用

更多使用和参考资料可查看以下资源

1. [官方主页](http://supervisord.org/index.html)
2. [源码GitHub](https://github.com/Supervisor/supervisor)
