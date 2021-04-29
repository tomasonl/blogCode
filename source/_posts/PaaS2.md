---
title: 华为云PaaS平台-服务引擎CSE开发-（2）
date: 2021-01-02 16:29:32
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=172869225,1865299545&fm=15&gp=0.jpg
---
# 华为云实战
## ServiceComb 项目接入CSE
父工程依赖管理配置
```
<dependencyManagement>      
<dependencies>  
<dependency>              
      <groupId>com.huawei.paas.cse</groupId>              
      <artifactId>cse‐dependency</artifactId>              
      <version>2.3.20</version>              
      <type>pom</type>              
      <scope>import</scope>          
</dependency>  
</dependencies>  
</dependencyManagement>
```
消费和服务方引入
```
<dependency>
    <groupId>com.huawei.paas.cse</groupId>
    <artifactId>cse‐solution‐service‐engine</artifactId>
</dependency>
```
注册中心配置
```
PPLICATION_ID: helloworld
service_description:
  name: helloworld‐provider
  version: 1.0.0
cse:
  service:
    registry:
      address: https://cse.cn‐north‐1.myhuaweicloud.com
      instance:
        watch: false
  credentials:
    accessKey: CMPEUCGEDINQKGWBGHVB
    secretKey: HEYSZL5zTw6HLfPEOxzGnWpbzLwHWAylUc99vMtd
    akskCustomCipher: default
  rest:
    address: 0.0.0.0:8080
```
然后就可以访问和消费服务了。
### 

