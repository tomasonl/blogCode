---
title: Java日常开发-规范起来1
date: 2021-03-04 15:25:59
tags:
- 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1832317564,3433131942&fm=26&gp=0.jpg
---

# 一、编程规约
##  命名风格
1. 【强制】命名不能以下划线或美元符号开始或者结束。
反例：_name / __name / $name / name_ / name$ / name__ 

2. 【强制】严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。
正例：renminbi / alibaba / taobao / youku / hangzhou 等国际通用的名称，可视同英文。
反例：DaZhePromotion [打折] / getPingfenByName() [评分] / int 某变量= 3
说明：正确的英文拼写和语法可以让阅读者易于理解，避免歧义。纯拼音命名避免采用。中文同音字，汉字编码，Pingfen：评分，平分。

3. 【强制】类名使用 首字母大写驼峰UpperCamelCase 风格，例外：DO / BO / DTO / VO / AO / PO / UID 等。
正例：JavaServerlessPlatform / UserDAO / XmlService / TcpUdpDeal /TaPromotion
反例：javaserverlessplatform / UserDo / XMLService / TCPUDPDeal /TAPromotion
TIPS：
DO（ Data Object）：与数据库表结构一一对应，通过 DAO 层向上传输数据源对象。
DTO（ Data Transfer Object）：数据传输对象，Service 或 Manager 向外传输的对象。
BO（ Business Object）：业务对象。 由 Service 层输出的封装业务逻辑的对象。
VO（ View Object）：显示层对象，通常是 Web 向模板渲染引擎层传输的对象。
AO (Application Object) :应用对象,在 Web 层与 Service 层之间抽象的复用对象模型,类似展示层,复用度不高。
POJO 是 DO / DTO / BO / VO 的统称

4. 【强制】方法名、参数名、成员变量、局部变量都统一使用 首字母小写驼峰lowerCamelCase风格。
正例： localValue / getHttpMessage() / inputUserId

*开发小坑1*. 【强制】POJO 类中布尔类型变量都不要加 is 前缀，否则部分框架解析会引起序列化错误。

反例：定义为基本数据类型 Boolean isDeleted 的属性，它的方法也是isDeleted()，RPC 框架在反向解析的时候，“误以为”对应的属性名称是deleted，导致属性获取不到，进而抛出异常。
解释：这是开发中的一个坑，很难发现问题。
debug，com.p3c.controller.ApiTestController，在 postman 中传入
{ "num":1, "isSuccess":true}但是 controller 收到的却是 false;

5. 【参考】各层命名规约：
A) Service/DAO 层方法命名规约
1） 获取单个对象的方法用 get 做前缀。
2） 获取多个对象的方法用 list 做前缀，复数形式结尾如：listObjects。
3） 获取统计值的方法用 count 做前缀。
4） 插入的方法用 save/insert 做前缀。
5） 删除的方法用 remove/delete 做前缀。
6） 修改的方法用 update 做前缀。
B) 领域模型命名规约
1） 数据对象：xxxDO，xxx 即为数据表名。
2） 数据传输对象：xxxDTO，xxx 为业务领域相关的名称。
3） 展示对象：xxxVO，xxx 一般为网页名称。
4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO。

## 常量定义
1. 【强制】不允许任何魔法值（即未经预先定义的常量）直接出现在代码中。
反例：String key = "Id#taobao_" + tradeId;
cache.put(key, value);
// 缓存 get 时，由于在代码复制时，漏掉下划线，导致缓存击穿而出现
问题
2. 【强制】在 long 或者 Long 赋值时，数值后使用大写的 L，不能是小写的 l，
小写容易跟数
字 1 混淆，造成误解。
说明：Long a = 2l; 写的是数字的 21，还是 Long 型的 2。
3. 【推荐】不要使用一个常量类维护所有常量，要按常量功能进行归类，分开维
护。
说明：大而全的常量类，杂乱无章，使用查找功能才能定位到修改的常量，不
利于理解和维护。
正例：缓存相关常量放在类 CacheConsts 下；系统配置相关常量放在类
ConfigConsts 下。
解释：大而全其实相当于没有，比如读书，重点划线，如果一本书都划线，和
没划线有什么区别呢？
4. 【推荐】常量的复用层次有五层：跨应用共享常量、应用内共享常量、子工程内共享常量、包内共享常量、类内共享常量。
1） 跨应用共享常量：放置在二方库中，通常是 client.jar 中的 constant 目录下。
2） 应用内共享常量：放置在一方库中，通常是子模块中的 constant 目录下。
反例：易懂变量也要统一定义成应用内共享常量，两位工程师在两个类中分别定义了“YES”的变量：
类 A 中：public static final String YES = "yes";
类 B 中：public static final String YES = "y";
A.YES.equals(B.YES)，预期是 true，但实际返回为 false，导致线上问题。

3） 子工程内部共享常量：即在当前子工程的 constant 目录下。
4） 包内共享常量：即在当前包下单独的 constant 目录下。
5） 类内共享常量：直接在类内部 private static final 定义。

