---
title: 日常工作记录总结帖
date: 2020-01-01 08:26:59
tags: 工作记录
sticky: 2

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.1/Pic/Blogcover/%E6%97%A5%E5%B8%B8%E5%B7%A5%E4%BD%9C%E7%BA%AA%E5%BE%8B.jpg
---

# 1. 生产力工具篇
## 1.1. IDEA优秀插件必备
## 1.2. IDEA快捷键使用
## 1.3. HEXO博客相关技术栈
## 1.4.  
# 2. Java后端技术篇
## 2.1. ，想要在启动SpringBoot时自动运行某接口？
package com.example.controller;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
@Component
public class Hahah implements CommandLineRunner{
@Override
public void run(String... args) throws Exception {
    System.out.println("What the fuck!");
}
}
记得在类上加@Component注解；
其实类似“开机启动”，两种：ApplicationRunner和CommandLineRunner。
如果想要设定顺序，可以实现org.springframework.core.Ordered接口 或者 @Order(value = X)注解来实现。
动态修改定时器任务：
1.首先这里我们需要重新认识一个类ThreadPoolTaskScheduler：线程池任务调度类，能够开启线程池进行任务调度。
2.ThreadPoolTaskScheduler.schedule()方法会创建一个定时计划ScheduledFuture，在这个方法需要添加两个参数， Runnable（线程接口类） 和CronTrigger（定时任务触发器）
3.在ScheduledFuture中有一个cancel可以停止定时任务。

## 2.2. , 定时器任务？嫌麻烦？@Schedule 香不香？哈哈。
package com.example.controller;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

@Component
public class Hahaha {
@Scheduled(cron=”0 0/30 * * * ? “)
public void Hello(){
SimpleDateFormat format = new SimpleDateFormat(“yyyy年MM月dd日HH时mm分ss秒”);
Date date = new Date();
System.out.println(“当前时间表示方法A”+format.format(date));
Calendar calendar = Calendar.getInstance();
System.out.println(“当前时间表示方法B:”+format.format(calendar.getTime()));
System.out.println(“又过去了半个小时，你都干了些啥？？？老弟！”);
}
}
## 2.3. ，SpringDataJpa插入数据表名有下划线的时候？
据库中的字段名是loginName，实体中应当这样写
@Column(name = “loginname”)
private String loginName;
执行sql语句时，大写的驼峰字符会变成下划线“_”，如果写loginName查询时会变成login_name，所以会报异常:

Caused by: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Unknown column ‘login_name’ in ‘field list’

而javabean中要保持大小写格式，否则不能映射成功！！！

## 2.4. ，mysql在Windows大小写敏感问题
https://www.cnblogs.com/swje/p/6628315.html?utm_source=debugrun&utm_medium=referral

How table and database names are stored on disk and used in MySQL is affected by the lower_case_table_names system variable, which you can set when starting mysqld. lower_case_table_names can take the values shown in the following table. This variable does not affect case sensitivity of trigger identifiers. On Unix, the default value of lower_case_table_names is 0. On Windows, the default value is 1. On OS X, the default value is 2.

PS：mac OS（2012年前称Mac OS X，2012年-2016年称OS X）

## 2.5. ，Springboot 同时插入json和file文件时出现如下错误？
{“status”: 415,
“error”: “Unsupported Media Type”,
“Content type ‘multipart/form-data;boundary=————————429210727736774900703947;charset=UTF-8’ not supported”,
}
只需要去掉@requestBody注解，为啥再看看。而且平时传参数一般都是写个javabean，没有这个表的模型数据就自己写一个以便于别人知道你传过来的是啥。

/**
 * 该方法为XXX
 * @param
 * @return
 */
@RequestMapping(value = "/addMaintainTask", method = RequestMethod.POST)
@ResponseBody
public String addMaintainTask(@RequestBody MaintainTaskOptions options, @RequestParam("file") MultipartFile file){}
|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||
@RequestMapping(value = “/addMaintainTask”, method = RequestMethod.POST)
@ResponseBody
public String addMaintainTask(MaintainTaskOptions options, @RequestParam(“file”) MultipartFile file){}

## 2.6. ，Json传输时间传输格式？
long类型的时间戳Unix timestamp 使用FASTJSON的JsonObject去接很完美，如果自己写model类的话 时间类型定义匹配然后转date。如果前台传过来String 那就 SimpleDateFormat类的parse(String source)方法和format(Date date)方法。

Failed to convert property value of type ‘java.lang.String’ to required type ‘java.util.Date’ for property ‘maintainStartTime’; nested exception is org.springframework.core.convert.ConversionFailedException: Failed to convert from type [java.lang.String] to type [java.util.Date] for value ‘1577068194898’; nested exception is java.lang.
## 2.7. , common-lang3插件？
import org.apache.commons.lang3.StringUtils;
/**

StringUtils常用方法
/
public class TestStringUtils {
public static void main(String[] args) {
testReplace();
}
/**
测试IsBlank
/
public static void testBlank() {
String str = “you are luck!”;
// 结果为false
System.out.println(StringUtils.isBlank(str));
// 结果为true
System.out.println(StringUtils.isBlank(“”));
// 结果为true
System.out.println(StringUtils.isBlank(null));
//// 对于制表符、换行符、换页符和回车符StringUtils.isBlank()均识为空白符
System.out.println(StringUtils.isBlank(“\t \n \f \r”));
// true,只要为空白字符都为true
System.out.println(StringUtils.isBlank(“ “));
// isNotBlank,只要为null或者””都等于false
System.out.println(StringUtils.isNotBlank(“”));
}
/**
测试isEmpty 只要不是null和””都为true
/
public static void testEmpty() {
String str = “thanks you”;
System.out.println(StringUtils.isEmpty(str));
// 空白字符为true
System.out.println(StringUtils.isEmpty(“”));
// 空格字符为false
System.out.println(StringUtils.isEmpty(“ “));
// 对于制表符、换行符、换页符和回车符StringUtils.isEmpty()均识不为空
System.out.println(StringUtils.isEmpty(“\t \n \f \r”));
System.out.println(StringUtils.isEmpty(null));
}
/**
测试equals 只要内容相同，就返回true
/
public static void testEquals() {
String s1 = “hello word”;
String s2 = “hello word”;
String s3 = new String(“hello word”);
System.out.println(StringUtils.equals(s1, s2));
System.out.println(StringUtils.equals(s1, s3));
}
/**
测试join 将数组以符号或其他字符串为间隔组成新的字符串
/
public static void testJoin() {
    String[] strs = { "1", "2", "3", "4", "5", "6" };
    String join = StringUtils.join(strs, "|");
    // 1|2|3|4|5|6
    System.out.println(join);
}
/**
* 测试split 把字符串以某个字符分开，返回一个字符串数组
*/
public static void testSplit() {
    String str = "1|2|3|4|5|6";
    String[] split = StringUtils.split(str, '|');
    // 结果为1,2,3,4,5,6
    for (String string : split) {
        System.out.println(string);
    }
}
/**
* 测试TrimToNull 把字符串的开头空格和尾部的空格去掉
*/
public static void testTrimToNull() {
    String str = "  you are luck  ";
    String trimToNull = StringUtils.trimToNull(str);
    // "you are luck"
    System.out.println(trimToNull);
}
/**
* 测试replace 对字符串，在其中截取某个字符串替换成某个字符串
*/
public static void testReplace() {
    String str = "you is luck";
    String replace = StringUtils.replace(str, "is", "are");
    // you is luck
    System.out.println(str);
    // you are luck
    System.out.println(replace);
}
}
# 3. 前端技术篇