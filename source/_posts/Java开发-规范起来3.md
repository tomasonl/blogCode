---
title: Java开发-规范起来3
date: 2021-03-14 15:29:35
tags:
- 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=1832317564,3433131942&fm=26&gp=0.jpg
---
## (四) OOP 规约
1. 【强制】避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增
加编译器解析成本，直接用类名来访问即可。
解释：不是说语法错，而是增加 jvm 成本，每增加一个对象变量，new 一个
对象需要分配内存空间，增加无谓的内存消耗。调用时会去堆中检索对象，用类
名访问只需要去方法区中找，减少了堆中检索的成本。
2. 【强制】所有的覆写方法，必须加@Override 注解。
说明：getObject()与 get0bject()的问题。一个是字母的 O，一个是数字的
0，加@Override 可以准确判断是否覆盖成功。另外，如果在抽象类中对方法
签名进行修改，其实现类会马上编译报错。
解释：jdk5.0 才有这个注解，可以帮助我们做数据检测，上例中的 O 和 0 眼
睛看不出来。
3. 【强制】相同参数类型，相同业务含义，才可以使用 Java 的可变参数，避免使
用 Object。
说明：可变参数必须放置在参数列表的最后。（提倡同学们尽量不用可变参数
编程）
正例：public List<User> listUsers(String type, Long... ids) {...}
解释：可变参数本质上就是一个数组（经编译器编译后转化为对应类型的数
组）。因为如果遇到此种业务，绝对会定下类型，而不是 Object，可变参数会
增加 jdk 编译成本，会将它作为一个数组转换，增加编译成本，运行时也会预
留内存空间，额外增加运行空间。
4. 【强制】外部正在调用或者二方库依赖的接口，不允许修改方法签名，避免对
接口调用方产生影响。接口过时必须加@Deprecated 注解，并清晰地说明采
用的新接口或者新服务是什么。
解释：因为现在微服务比较多，有 dubbo，rpc 调用，spring cloud rest
（特例，bean 参数不同）调用，如果方法签名改了，会对调用者造成麻烦。我们可
以用重写，重载的方式进行扩展。扩展接口升级 url 中加上版本号 v1,v2 等。但是原
来使用的接口不要停。
5. 【强制】不能使用过时的类或方法。
说明：java.net.URLDecoder 中的方法 decode(String encodeStr) 这个方法
已经过时，应该使用双参数 decode(String source, String encode)。接口提
供方既然明确是过时接口，那么有义务同时提供新的接口；作为调用方来说，
有义务去考证过时方法的新实现是什么。
解释：如果有可替代的，不要用过时的。肯定是有问题，才会声明过时。没人
喜欢过期的食物，为什么编程时就喜欢用过期的方法，而就不想着用新方法
呢？
6. 【强制】Object 的 equals 方法容易抛空指针异常，应使用常量或确定有值的
对象来调用 equals。
正例："test".equals(object);
反例：object.equals("test");
说明：推荐使用 java.util.Objects#equals（JDK7 引入的工具类）。
解释：字面值做调用方。避免空指针。看代码：oop 包下 EqualsTest
7. 【强制】所有整型包装类对象之间值的比较，全部使用 equals 方法比较。
说明：对于 Integer var = ? 在-128 至 127 范围内的赋值，Integer 对象是
在 IntegerCache.cache 产生，会复用已有对象，这个区间内的 Integer 值
可以直接使用==进行判断，但是这个区间之外的所有数据，都会在堆上产
生，并不会复用已有对象，这是一个大坑，推荐使用 equals 方法进行判
断。
解释：面试大坑，Integer intA = 3,intB =3 ,用==判断是不是 true？不是基
本类型，都用 equals。equal 比较内容。看代码 oop.EqualsTest
8. 【强制】浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类
型不能用 equals 来判断。
说明：浮点数采用“尾数+阶码”的编码方式，类似于科学计数法的“有效数
字+指数”的表示方式。二进制无法精确表示大部分的十进制小数，具体原理
参考《马士兵 Java 基础课程》。
反例：
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
if (a == b) {
// 预期进入此代码快，执行其它业务逻辑
// 但事实上 a==b 的结果为 false
}
Float x = Float.valueOf(a);
Float y = Float.valueOf(b);
if (x.equals(y)) {
// 预期进入此代码快，执行其它业务逻辑
// 但事实上 equals 的结果为 false
}
正例：
(1)指定一个误差范围，两个浮点数的差值在此范围之内，则认为是相等的。
float a = 1.0f - 0.9f;
float b = 0.9f - 0.8f;
float diff = 1e-6f;
if (Math.abs(a - b) < diff) {
System.out.println("true");
}
(2)使用 BigDecimal 来定义值，再进行浮点数的运算操作。
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");
BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);
if (x.equals(y)) {
System.out.println("true");
}
解释：在 10 进制下，我们将 1 分成 10 份，还能表示 0.1，0.2 等，但表示
1/3，只能无限接近，永远达不到。所以精度不够。完全相等是不可能的。
9. 【强制】定义数据对象 DO 类时，属性类型要与数据库字段类型相匹配。
正例：数据库字段的 bigint 必须与类属性的 Long 类型相对应。
反例：某个案例的数据库表 id 字段定义类型 bigint unsigned，实际类对象属性
为 Integer，随着 id 越来越大，超过 Integer 的表示范围而溢出成为负数。
解释：门当户对，否则匹配不上出问题。天天吵架调 bug，太费精力。
10. 【强制】为了防止精度损失，禁止使用构造方法 BigDecimal(double)的方式把
double 值转化为 BigDecimal 对象。
说明：BigDecimal(double)存在精度损失风险，在精确计算或值比较的场景中可
能会导致业务逻辑异常。
如：BigDecimal g = new BigDecimal(0.1f); 实际的存储值为：0.10000000149
正例：优先推荐入参为 String 的构造方法，或使用 BigDecimal 的 valueOf 方
法，此方法内部其实执行了 Double 的 toString，而 Double 的 toString 按
double 的实际能表达的精度对尾数进行了截断。
BigDecimal recommend1 = new BigDecimal("0.1");
BigDecimal recommend2 = BigDecimal.valueOf(0.1);
解释：官方注释说了 unpredictable，你以为的你以为的不是你以为的。
BigDecimal(0.1)这货实际上等于
0.1000000000000000055511151231257827021181583404541015625，因为准
确的来说 0.1 本身不能算是一个 double（其实 0.1 不能代表任何一个定长二进制分
数）。去 EqualsTests 看源码 注释
11. 关于基本数据类型与包装数据类型的使用标准如下：
1） 【强制】所有的 POJO 类属性必须使用包装数据类型。
2） 【强制】RPC 方法的返回值和参数必须使用包装数据类型。
3） 【推荐】所有的局部变量使用基本数据类型。
说明：POJO 类属性没有初值是提醒使用者在需要使用时，必须自己显式地进行赋
值，任何 NPE 问题，或者入库检查，都由使用者来保证。
正例：数据库的查询结果可能是 null，因为自动拆箱，用基本数据类型接收有
NPE 风险。
反例：比如显示成交总额涨跌情况，即正负 x%，x 为基本数据类型，调用的 RPC
服务，调用不成功时，返回的是默认值，页面显示为 0%，这是不合理的，应该
显示成中划线。所以包装数据类型的 null 值，能够表示额外的信息，如：远程调
用失败，异常退出。
解释：局部变量大多数用于计算，基本类型有优势，不需要拆箱装箱，效率高。
属性用包装类型：因为有可能序列化。看代码，当用 Integer 做参数时，num 为
空时，接受到的是 null，由使用者注意，但 为 int 时收到的是 0。如果使用者由
于没有 npe 忘了，上线会出问题。
12. 【强制】定义 DO/DTO/VO 等 POJO 类时，不要设定任何属性默认值。
反例：POJO 类的 createTime 默认值为 new Date()，但是这个属性在数据提取
时并没有置入具体值，在更新其它字段时又附带更新了此字段，导致创建时间被
修改成当前时间。
解释：
DO:data object 和数据库表对应，可以 1 个 do 对应多个表，。
Dto：trans 转换 dao 输出的对象，do 和 dto 不绝对一样。Dto 可以对数据做额
外的处理，do 整理后需要向上传递时用。
Vo：渲染视图。Vo 和 do 有时候也相同。
当 do 用于页面渲染用分页时，需要 vo，do 做一些特殊处理时，用 dto。
pojo：属性，set，get，object 方法，没有业务含义。
Pojo 是纯净的。
设置默认值，会让错误不好排查。
13. 【强制】序列化类新增属性时，请不要修改 serialVersionUID 字段，避免反序列
失败；如果完全不兼容升级，避免反序列化混乱，那么请修改 serialVersionUID
值。
说明：注意 serialVersionUID 不一致会抛出序列化运行时异常。
解释：用了版本号，如果改了版本号，有地方有序列化的对象，在反序列化时就会
报错。如果抛弃原来所有的，全部进行升级，无所谓了。Io 文件，内存，网络。
反序列化 会读取流中的 版本号，如果版本号和类中版本号不一致，会报错。也可
以让所以类版本号有的都是 1L，就像 2 个人同一个身份证号，正常活没问题。
Hashcode 方法，返回 0 也行，但是不建议。看代码：oop.SerializeTest
14. 【强制】构造方法里面禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init
方法中。
解释：为了逻辑清晰，不要让孩子快出生的时候，干些他成为独立的人之后才能
干的活。先让他生出来再说，别着急。可以在 init 上加上，@PostConstruct 注解，
在构造方法执行结束后调用。在服务发现 DiscoveryClient 中构造方法执行了业务逻
辑，我们不学。国产标准。
15. 【强制】POJO 类必须写 toString 方法。使用 IDE 中的工具：source>
generate toString 时，如果继承了另一个 POJO 类，注意在前面加一下
super.toString。
说明：在方法执行抛出异常时，可以直接调用 POJO 的 toString()方法打印其属性
值，便于排查问题。
解释：异常处理会用到。
16. 【强制】禁止在 POJO 类中，同时存在对应属性 xxx 的 isXxx()和 getXxx()方
法。
说明：框架在调用属性 xxx 的提取方法时，并不能确定哪个方法一定是被优先调
用到。
17. 【推荐】使用索引访问用 String 的 split 方法得到的数组时，需做最后一个分隔
符后有无内容的检查，否则会有抛 IndexOutOfBoundsException 的风险。
说明：
String str = "a,b,c,,";
String[] ary = str.split(",");
// 预期大于 3，结果是 3
System.out.println(ary.length);
解释：看代码：oop.SpiltTest
18. 【推荐】当一个类有多个构造方法，或者多个同名方法，这些方法应该按顺序放
置在一起，便于阅读，此条规则优先于下一条。
解释：构造方法，overload，属性，getter/setter 分别放在一起。
19. 【推荐】 类内方法定义的顺序依次是：公有方法或保护方法 > 私有方法 >
getter / setter 方法。
说明：公有方法是类的调用者和维护者最关心的方法，首屏展示最好；保护方法虽
然只是子类关心，也可能是“模板设计模式”下的核心方法；而私有方法外部一般
不需要特别关心，是一个黑盒实现；因为承载的信息价值较低，所有 Service 和
DAO 的 getter/setter 方法放在类体最后。
解释：方便看代码的人。
20. 【推荐】setter 方法中，参数名称与类成员变量名称一致，this.成员名 = 参数名。在 getter/setter 方法中，不要增加业务逻辑，增加排查问题的难
度。
反例：
public Integer getData() {
if (condition) {
return this.data + 100;
} else {
return this.data - 100;
}
}
解释：加了业务逻辑，别人会误解。
21. 【推荐】循环体内，字符串的连接方式，使用 StringBuilder 的 append 方法进
行扩展。
说明：下例中，反编译出的字节码文件显示每次循环都会 new 出一个
StringBuilder 对象，然后进行 append 操作，最后通过 toString 方法返回
String 对象，造成内存资源浪费。
反例：
String str = "start";
for (int i = 0; i < 100; i++) {
str = str + "hello";
}
解释：减少对象的构建过程，builder 用的时 char[]实现。减少堆中不必要对
象的创建过程。
22. 【推荐】final 可以声明类、成员变量、方法、以及本地变量，下列情况使用 final
关键字：
1） 不允许被继承的类，如：String 类。
2） 不允许修改引用的域对象。
3） 不允许被覆写的方法，如：POJO 类的 setter 方法。
4） 不允许运行过程中重新赋值的局部变量。
5） 避免上下文重复使用一个变量，使用 final 可以强制重新定义一个变量，方便
更好地进行重构。
23. 【推荐】慎用 Object 的 clone 方法来拷贝对象。
说明：对象 clone 方法默认是浅拷贝，若想实现深拷贝需覆写 clone 方法实现域
对象的深度遍历式拷贝。
解释：clone 是浅拷贝。就当没有 clone 这个方法。Oop.CloneTest
https://blog.csdn.net/meism5/article/details/90414050
24. 【推荐】类成员与方法访问控制从严：
1） 如果不允许外部直接通过 new 来创建对象，那么构造方法必须是 private。
2） 工具类不允许有 public 或 default 构造方法。
3） 类非 static 成员变量并且与子类共享，必须是 protected。
4） 类非 static 成员变量并且仅在本类使用，必须是 private。
5） 类 static 成员变量如果仅在本类使用，必须是 private。
6） 若是 static 成员变量，考虑是否为 final。
7） 类成员方法只供类内部调用，必须是 private。
8） 类成员方法只对继承类公开，那么限制为 protected。
说明：任何类、方法、参数、变量，严控访问范围。过于宽泛的访问范围，不利于
模块解耦。思考：如果是一个 private 的方法，想删除就删除，可是一个 public
的 service 成员方法或成员变量，删除一下，不得手心冒点汗吗？变量像自己的小
孩，尽量在自己的视线内，变量作用域太大，无限制的到处跑，那么你会担心的。
解释：最小知道原则。阿里安全条例，关于信息传递也