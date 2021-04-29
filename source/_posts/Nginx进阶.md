---
title: Nginx-进阶梳理
date: 2021-04-13 10:09:06
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3379754713,1922618047&fm=26&gp=0.jpg
---

## Nginx 程序架构
### master/worker
一个master进程：负载加载和分析配置文件、管理worker进程、平滑升级
一个或多个worker进程：处理并响应用户请求
缓存相关的进程：

cache loader    # 载入缓存对象
cache manager   # 管理缓存对象
## Nginx特性
异步、事件驱动和非阻塞。

并发请求处理     # 通过epoll/select
文件IO          # 高级IO sendfile，异步，mmap

## Nginx 模块
高度模块化，但其模块早期不支持DSO机制；近期版本支持动态装载和卸载。

模块分类：

1、核心模块：core module
2、标准模块：
	HTTP modules：Http模块
		- Standard HTTP modules
		- Optional HTTP modules
	Mail modules：邮件相关
	Stream modules：传输层代理


## Nginx 作用
静态的web资源服务器；(图片服务器，或js/css/html/txt等静态资源服务器)
结合FastCGI/uwSGI/SCGI等协议反代动态资源请求
http/https协议的反向代理
imap4/pop3协议的反向代理
tcp/udp协议的请求转发

三台Tomcat搭建一个集群 Nginx代理

Nginx要是坏了呢？===KeepAlive
HA 高可用High Availability
两台Nginx不能同时去服务，主备节点关系。和tomcat集群不同；

使用KeepAlive实现高可用机制
基于VRRP协议，检测nginx状态，如果有故障还可以发送通知；主机修复好，自动让位；
这一些列都是由KeepAlive来做；
VRRP协议，虚拟路由冗余协议，解决内网单点故障；
keepalive==   
1. ./configure --prefix=/usr/local/keepalived --sysconf=/etc	(缺库就补一下)
2. make && make install
3. whereis keepalived
```
global_defs{  
	notification_email{   
		#通知email，根据实际情况配置    
		admin@example.com
	}
		notification_email_from admin@example.com
		smtp_server 127.0.0.1
		stmp_connect_timeout 30
		router_id node1         
		#节点名标识，主要用于通知中
	}
	vrrp_instance VI_NODE {
		state MASTER          
		#配置为主服务器

		interface eth0        
		#通讯网卡
		virtual_router_id 100 
		#路由标识
		priority 200          
		#优先级，0-254
		advert_int 5          
		#通知间隔，实际部署时可以设置小一点，减少延时
		authentication {
		auth_type PASS
		auth_pass 123456    
		#验证密码，用于通讯主机间验证
	}
	

	 virtual_ipaddress {
		192.168.1.206      
		#虚拟ip，可以定义多个
	}
        
}
```

写脚本实现全天候 检测nginx是否活着
```
#!/bin/bash
A= `ps -C nginx --no-header |wc -l`
#判断 nginx是否宕机,如果宕机了,尝试重启
if [$A -eq 0 ]; then
	/usr/locaL/nginx/sbin/nginx
	#等待一小会再次检查 nginx,如果没有启动成功,则停止 keepalived,使其启动备用机
	sleep 3
	if [`ps -C nginx --no-header |wc -l` -eq 0 ]; then
		killall keepalived
	fi
fi
```
但是备用机一般没啥事 也不能天天出事，所以为了资源合理利用
### 配置Keepalived双主热备
规则：以一个虚拟ip分组归为同一个路由
主节点配置：
```
global_defs {
	router_id keep_171
	}
	vrrp_instance VI_1 {
	state MASTER
	interface ens33
	virtual_router_id 51
	priority 100
	advert_int 1
	authentication {
	auth_type PASS
	auth_pass 1111
	}
	virtual_ipaddress {
	192.168.1.161
	}
	}
	vrrp_instance VI_2 {
	state BACKUP
	interface ens33
	virtual_router_id 52
	priority 80
	advert_int 1
	authentication {
	auth_type PASS
	auth_pass 1111
	}
	virtual_ipaddress {
	192.168.1.162
	}
}
```
备用节点配置
```
global_defs {
	router_id keep_172
	}
	vrrp_instance VI_1 {
	state BACKUP
	interface ens33
	virtual_router_id 51
	priority 80
	advert_int 1
	authentication {
	auth_type PASS
	auth_pass 1111
	}
	virtual_ipaddress {
	192.168.1.161
	}
}
```
相对而言，互为主备；

LVS-章文嵩博士

LVS负载能力超级高，LVS可以只接受不响应，