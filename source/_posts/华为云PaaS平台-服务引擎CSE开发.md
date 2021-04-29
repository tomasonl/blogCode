---
title: 华为云PaaS平台-服务引擎CSE开发-（1）
date: 2021-01-01 12:29:32
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=1661695526,663034429&fm=26&gp=0.jpg
---
# 初识华为云
&nbsp;&nbsp;&nbsp;&nbsp;华为云PaaS平台以华为FusionStage2.0项目为基础，基于Docker、Kubernetes等主流开源容器项目，提供了开发、测试、部署、运维的全流程自动化流水线能力，提供先进的微服务框架和丰富的服务组件，致力于让开发者专注于代码开发本身，提升开发效率，支持丰富的应用生态，推动企业更好的云化转型。
&nbsp;&nbsp;&nbsp;&nbsp;华为云PaaS平台不光提供云容器部署、微服务治理的功能，而且提供微服务开发、测试、部署、运维一站式解决方 案，支持Dubbo、SpringCloud等技术开发的微服务接入PaaS平台，也支持老系统的接入，并且对于Apache ServiceComb开的微服务应用更是零修改代码接入。华为云PaaS平台提供微服务引擎CSE框架，此框架在ServiceComb框架的基础上另外提供更多的商业功能支持。
## 华为WeatherMap案例体验
<img src="https://img-blog.csdnimg.cn/20210107155857525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3Njc2NDI5,size_16,color_FFFFFF,t_70">

- Weathermapweb ：前端界面服务，提供天气信息展示及查询界面。
- Fusionweather ：汇聚服务，前端请求查询天气统一Fusionweather服务，它相当于一个网关。
- Weather ：当前天气查询，前端请求汇聚服务查询天气，汇聚服务请求Weather查询天气。
- Forecast ：预报天气，查询未来天气信息，同Weather服务，汇聚服务请求Forecast查询未来天气信息。

1. 配置环境
华为云环境部署-微服务会连接华为云上的CSE服务中心、配置中心、仪表盘，本节配置华为云账号。
**AK/SK访问密钥**验证调用API发起请求的访问者身份，与密码的功能相似，需要使用成对的AK/SK进行加密签名确保请求的机密性、完整性和请求双方身份的正确性。用户可以在“我的凭证”中生成并管理访问密钥。
```
获取步骤：
1. 单击用户名，在下拉列表中单击“基本信息”。
2. 在基本信息页面单击“管理我的凭证”。
3. 单击“管理访问密钥”。
4. 单击“新增访问密钥”，进入“新增访问密钥”页面。
5. 输入当前用户的登录密码。
6. 通过邮箱或者手机进行验证，输入对应的验证码。
7. 下载credentials.csv文件，文件内容包括了 AK和SK。
```
也就是一个服务注册的东西。本地没有注册中心，注册中心在云上，本地启动服务注册到云平台。微服务引擎CSE，里面的服务目录就知道你跑了多少实例微服务。
所以需要解决问题1：怎样把我们的微服务注册到云平台注册中心。
2. 服务治理
<img src="https://img-blog.csdnimg.cn/20210107161954345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3Njc2NDI5,size_16,color_FFFFFF,t_70">
治理过程主要包括{服务的监控、服务降级（合理分配资源，砍掉不重要服务）、负载均衡等}

## 微服务框架ServiceComb 
2017年华为开源的微服务框架，主要也是微服务开发治理。相比于SpringCloud，华为积淀了很多企业级开发经验。service comb（service koumu）服务们的大梳子，梳理服务
ServiceComb相比SpringCloud的优势有什么？
1. 通信协议方面
SpringCloud仅支持Http的Rest。ServiceComb支持多种通信协议, Rest、RPC等;Highway（RPC）协议性能更高，Highway是基于二进制的序列化方式传输数据，性能远高于采用文本的HTTP协议。
2. 集成与服务提供方面
ServiceComb的商业版本CSE相比SpringCloud不仅提供了微服务开发框架，还提供了微服务云部署，管理、治理等一站式解决方案。而且华为自己当然和自家产品无缝集成。

**最小服务搭建**
通过@RestSchema替换@RestController
需要显示声明@RequestMapping
- provider服务提供方：
microservice.yaml：
```APPLICATION_ID: helloworldproject
service_description:
  name: helloworld‐provider
  version: 1.0.0
servicecomb:
  rest:
    address: 0.0.0.0:8080
  service:
    registry:
      address: http://127.0.0.1:30100
```
- 启动类：
@EnableServiceComb：启用java.chassis核心功能。
@SpringBootApplication：springBoot提供的注解，实现包扫描、自动配置。
- provider服务消费方：
```
APPLICATION_ID: helloworldproject
service_description:
  name: helloworld‐consumer
  version: 1.0.0
servicecomb:
  rest:
    address: 0.0.0.0:8081
  service:
    registry:
      address: http://127.0.0.1:30100
```

至于调用方式：
SServiceComb提供RestTemplate、AsynRestTemplate、透明RPC方式调用服务接口。
- RestTemplate：Spring提供的RESTful访问接口，ServiceComb提供该接口的实现类用于服务的调用。
- AsynRestTemplate：允许用户异步的进行服务调用。
- 透明RPC方式：允许用户通过简单的java interface像本地调用一样进行服务调用。透明在与与服务接口的协议、开发方式（使用SpringMVC方式、RPC/Jax-RS）无关，所以推荐。
采用透明PRC方式向服务调用controller中注入接口代理对象：

```
@RpcReference(microserviceName="helloworld‐provider",schemaId="helloworld")
HelloWorldInterface helloWorldInterface;
```

## 华为云Paas平台微服务引擎CSE
接触设施-平台-软件   Infrastructure 、Paltform、Software as a Service；
华为云：FusionCloud分为三部分
- Iaas-FusionSphere
- Paas-FusionStage
- 大数据-FusionInsight

**CSE**cloudServiceEngine是华为云Paas提供的企业微服务应用管理平台。
提供：服务SDK（开发）、服务注册、配置和治理中心;
快速构建、实时监控和高可用。兼容主流开源生态，不绑定特定开发框架和平台。

**ServiceComb与CSE有什么不同？**
1、 CSE是ServiceComb的商业版本，CSE的大部分组件来自于开源的ServiceComb，CSE没有开源但可以免费使用。
2、 使用ServiceComb开发的微服务不依赖华为云，使用CSE开发的微服务则依赖华为云。

**所以为什么要用微服务引擎CSE？**
在云上使用CSE，用户无需关心自己业务之外的微服务基础设施，保证系统整体稳定性。
微服务部署，管理、治理全部开箱即用，还可以获得开发时期的指导和帮助，以及后续技术发展的最新信息。


英语角哈哈ヾ(≧▽≦*)o
1. Fusion[ˈfjuːʒn] 
n. 融合; 熔接; 结合; 核聚变; ---fusionstage聚合平台；
2. Comb[kəʊm]
n. 梳子; 篦子; 压发梳; (作为装饰物的)发插; 梳理(头发); (公鸡的)鸡冠;
v. 梳，梳理(头发); 仔细搜索; 搜寻; 梳理(羊毛、棉花等) ---serviceComb服务梳理；
3. KuberNets 库伯耐踢死 K8s 类似I18n（internationalization）
管理云平台中多个主机上的容器化，提供了应用部署，规划，更新，维护的一种机制。
