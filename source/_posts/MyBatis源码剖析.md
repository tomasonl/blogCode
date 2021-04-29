---
title: MyBatis源码剖析
date: 2020-12-23 08:53:31
tags: 工作记录
copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处
cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1090660196,3930716840&fm=26&gp=0.jpg
---
# MyBatis是什么？
orm框架---把字段映射为对象的属性；

比如一个User
id int primary_key auto_increament;
name varchar(10) not null;
**映射为User类，建立关联**
class User{
    pravite Integer id;
    private String name;
}
***
之所以建立关联，可以想下之前我们传统JDBC步骤：
1. 导入JDBC驱动包。
2. Drivermanager注册驱动。
3. 创建连接，Connection接口类连接数据库。
4. 创建Statement，接口创建运行对象来执行SQL。
5. CRUD操作。
6. 操作结果集，ResultSet保存查询结果。
7. 关闭连接。

手动操作Statement啦，Java里面写一大堆sql啦等等问题，很难！
ps：关于Iterator三个方法：hasNext()、next()、remove()
  hasNext:没有指针下移操作，只是判断是否存在下一个元素
  next：指针下移，返回该指针所指向的元素
  remove：删除当前指针所指向的元素，一般和next方法一起用，这时候的作用就是删除next方法返回的元素；

代理模式：接口不能实例化，Proxy提供一个静态方法，动态创建代理类，实现这个接口。

Mybatis架构设计：
<img src="https://img-blog.csdnimg.cn/20201223103228930.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3Njc2NDI5,size_11,color_FFFFFF,t_70">
然后与Spring整合！

源码：打开入口点，想想如何实现。
问题：怎么映射的？xml怎么解析的？还有那写@Select注解，整个注解就能给我映射？带着问题，冲！

先找到配置文件，把他的流拿出来，调用javax.xml封装到xpath里面解析configuration里面的xml属性，塞进configuration对象，返回给一个sqlSessionFactory。    
解析sql 考虑-r -n这种然后取"<"之间的内容 遍历建树">"；

