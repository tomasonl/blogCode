---
title: 阿里云CDN架构梳理
date: 2021-02-20 16:29:32
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2430186389,1658586813&fm=26&gp=0.jpg
---

**阿里云CDN-演变之路**

​                                                                  ------阿里云高级开发工程师 邓茜(沐沂)

- 静态资源分发
- 动态资源加速
- 源站保护机制
- SSL、HTTP

5000+边缘节点，中美节点之间解决网络延迟大的问题；

**Service-Mesh Webinar**

“Webinar”——网络视频研讨会,是基于互联网实现点对点连接,可以集成幻灯片演示文稿、音频或视频文件于一体,能将企业所要求推广的信息立体呈现.

**节点架构:**

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/1.png)

**从以上架构看到一些问题：**

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/2.png)

**所以调整了微服务架构如下：**

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/3.png)

**技术方案---第一阶段：基于CoreDNS的服务发现**

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/4.png)

修改DNS IP和策略

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/5.png)

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/6.png)

UDP转发、伪连接；

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/7.png)

广播，向所有host 转发请求，在streamFilter里面实现。

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/8.png)

**技术方案---第二阶段：ServiceMesh架构**

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/9.png)

pilot 

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/10.png)

小特点：跨容器的热升级；

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/11.png)

Istio适配

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/12.png)

协议

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/13.png)

新增服务，导入顺序

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/14.png)

**总结**

目前落地几十个IDC、完善节点动态监控等

后面需要加入虚机服务发现：

![img](https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.2/Pic/article/15.png)

 [您也可以点击此处下载该文档](/download/阿里云CDN-演变之路.pdf)