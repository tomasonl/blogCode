---
title: Nginx实战部署
date: 2021-03-01 09:38:09
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3379754713,1922618047&fm=26&gp=0.jpg

---

# WHAT---Nginx？
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，2004年发布。2011年，nginx 1.0.4发布。

其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。在全球活跃的网站中有12.18%的使用比率，大约为2220万个网站。

Nginx 是一个安装非常的简单、配置文件非常简洁（还能够支持perl语法）、Bug非常少的服务。Nginx 启动特别容易，并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够不间断服务的情况下进行软件版本的升级。

Nginx代码完全用C语言从头写成。官方数据测试表明能够支持高达 50,000 个并发连接数的响应。

# WHY---Nginx？
负载均衡，反向代理，静态缓存。

1 负载均衡
Nginx提供的负载均衡策略有2种：内置策略和扩展策略。
内置策略：轮询，加权轮询，IP-hash。
扩展策略，形式多样。

轮询----依次循环
加权轮询---按权重比例分发请求
iphash---对ip进行hash操作，固定IP永远打一台服务器，解决Session共享。但是现在都是用Redis做共享；

2 代理
正向代理---代理客户端
<img src="https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/01/25/kuangstudy46bdad36-d3e0-43b0-a223-43360b7e8fc7.png">

反向代理---代理服务端
<img src="https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/01/25/kuangstudy62a15097-6e2a-4dbe-bcf5-f0d7cab81089.png">

3 缓存
动静分离可以根据静态资源的特点将其做缓存操作。提高资源响应的速度。

# HOW---Nginx?

Nginx的安装
1、下载nginx
http://nginx.org/en/download.html 下载稳定版本。

2、启动nginx
有很多种方法启动nginx
(1)直接双击nginx.exe，双击后一个黑色的弹窗一闪而过
(2)打开cmd命令窗口，切换到nginx解压目录下，输入命令 nginx.exe ，回车即可

3、检查nginx是否启动成功
直接在浏览器地址栏输入网址 http://localhost:80 回车，出现以下页面说明启动成功！
<img src="https://kuangstudy.oss-cn-beijing.aliyuncs.com/bbs/2021/01/25/kuangstudya21688c8-159e-4caa-8e65-3dc056b6b78e.png">



4、配置监听
nginx的配置文件是conf目录下的nginx.conf，默认配置的nginx监听的端口。
重启：nginx -s reload

5、关闭nginx
如果使用cmd命令窗口启动nginx， 关闭cmd窗口是不能结束nginx进程的，可使用两种方法关闭nginx
(1)输入nginx命令 nginx -s stop(快速停止nginx) 或 nginx -s quit(完整有序的停止nginx)
(2)使用taskkill taskkill /f /t /im nginx.exe


```
Nginx常用命令
cd /usr/local/nginx/sbin/
./nginx  启动
./nginx -s stop  停止
./nginx -s quit  安全退出
./nginx -s reload  重新加载配置文件
ps aux|grep nginx  查看nginx进程
启动成功访问 服务器ip:80
```

```
TIPS：连接不上阿里云或者LINUX服务器，检查安全组是否开放端口，或者服务器防火墙是否开放端口！
相关命令：
# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop
# 查看防火墙规则
firewall-cmd --list-all
# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 开放80端口
firewall-cmd --permanent --add-port=80/tcp
# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp
#重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload
# 参数解释
1、firwall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；
演示
upstream lb{
    server 127.0.0.1:8080 weight=1;
    server 127.0.0.1:8081 weight=1;
}
location / {
    proxy_pass http://lb;
}
```

