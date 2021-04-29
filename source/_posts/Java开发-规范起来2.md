---
title: Java开发-规范起来2
date: 2021-03-09 15:27:40
tags:
- 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1832317564,3433131942&fm=26&gp=0.jpg
---
## (三) 代码格式
1. 【强制】如果是大括号内为空，则简洁地写成{}即可，大括号中间无需换行和
空格；如果是非空代码块则：
1） 左大括号前不换行。
2） 左大括号后换行。
3） 右大括号前换行。
4） 右大括号后还有 else 等代码则不换行；表示终止的右大括号后必须换行。
2. 【强制】左小括号和字符之间不出现空格；同样，右小括号和字符之间也不出现空格；而左大括号前需要空格。详见第 5 条下方正提示。
反例：if (空格 a == b 空格)
3. 【强制】if/for/while/switch/do 等保留字与括号之间都必须加空格。
4. 【强制】任何二目、三目运算符的左右两边都需要加一个空格。
说明：运算符包括赋值运算符=、逻辑运算符&&、加减乘除符号等。
解释：小时候第一次看马老师视频，才知道要加运算符两边的空格这个东西。
所以花钱学东西很值得，如果花 1 万，马上知道这个知识点，不花这个钱，有可能 2、3 年后知道，损失的就不止 1 万了。
5. 【强制】采用 4 个空格缩进，禁止使用 tab 字符。
说明：如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。IDEA 设置 tab为 4 个空格时，请勿勾选 Use tab character；而在 eclipse 中，必须勾选insert spaces for tabs。
    public static void main(String[] args) {
        // 缩进 4 个空格
        String say = "hello";
        // 运算符的左右必须有一个空格
        int flag = 0;
        // 关键词 if 与括号之间必须有一个空格，括号内的
        f 与左括号，0 与右括号不需要空格 if (flag == 0) {
        System.out.println(say);
        }
        // 左大括号前加空格且不换行；左大括号后换行
        if (flag == 1) {
        System.out.println("world");
        // 右大括号前换行，右大括号后有 else，不用换行
        } else {
        System.out.println("ok");
        // 在右大括号后直接结束，则必须换行
    }
}

6. 【强制】注释的双斜线与注释内容之间有且仅有一个空格。
正例：
// 这是示例注释，请注意在双斜线之后有一个空格
String param = new String();
7. 【强制】在进行类型强制转换时，右括号与强制转换值之间不需要任何空格隔开。
正例：
long first = 1000000000000L;
int second = (int)first + 2;
8. 【强制】单行字符数限制不超过 120 个，超出需要换行，换行时遵循如下原则：
1）第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示
例。
2）运算符与下文一起换行。
3）方法调用的点符号与下文一起换行。
4）方法调用中的多个参数需要换行时，在逗号后进行。
5）在括号前不要换行，见反例。
正例：
StringBuilder sb = new StringBuilder();
// 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行
sb.append("Jack").append("Ma")...
.append("alibaba")...
.append("alibaba")...
.append("alibaba");
反例：
StringBuilder sb = new StringBuilder();
// 超过 120 个字符的情况下，不要在括号前换行
sb.append("Jack").append("Ma")...append
("alibaba");
// 参数很多的方法调用可能超过 120
个字符，不要在逗号前换行
method(args1, args2, args3, ...
, argsX);
9. 【强制】方法参数在定义和传入时，多个参数逗号后边必须加空格。
正例：下例中实参的 args1，后边必须要有一个空格。
method(args1, args2, args3);
10. 【强制】IDE 的 text file encoding 设置为 UTF-8; IDE 中文件的换行符使用Unix 格式，不要使用 Windows 格式。
解释：很多 windows 格式会有问题，比如记事本开头添加 0xefbbbf（十六进制）的字符。会导致文本问题。
11. 【推荐】单个方法的总行数不超过 80 行。
说明：除注释之外的方法签名、左右大括号、方法内代码、空行、回车及任何不可见字符的总行数不超过80行。
正例：代码逻辑分清红花和绿叶，个性和共性，绿叶逻辑单独出来成为额外方法，使主干代码更加清晰；共性逻辑抽取成为共性方法，便于复用和维护。
解释：不仅仅写代码，生活中方方面面也一样，绿叶就当好绿叶，否则惹人烦。比如重要会议高层讲话，一个低层员工总插嘴，那就很可能会被单独抽取出来。放到公司外面。
12. 【推荐】没有必要增加若干空格来使变量的赋值等号与上一行对应位置的等号对齐。
正例：
int one = 1;
long two = 2L;
float three = 3F;
StringBuilder sb = new StringBuilder();
说明：增加 sb 这个变量，如果需要对齐，则给 one、two、three 都要增加几个空格，在变量比较多的情况下，是非常累赘的事情。
13. 【推荐】不同逻辑、不同语义、不同业务的代码之间插入一个空行分隔开来以提升可读性。
说明：任何情形，没有必要插入多个空行进行隔开。
解释：点到为止，不要过分。否则会激发逆反情绪，小时候玩 CS，人已经被打死了，就不要一直突突了，小时候在网吧有一次因为游戏中的鞭尸发生肢体冲突。