---
title: JAVA多线程
date: 2020-04-06 06:12:39
tags:
- 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: http://pic1.win4000.com/wallpaper/2020-11-18/5fb4d3232459a.png
---

## 1，线程和进程
   一个进程执行过程中可以有多个线程，同类多个线程共享进程的堆和方法区，独享程序计数器、虚拟机栈和本地方法栈。

<!--more-->

堆：存放对象实例，几乎所有的对象实例都在这里分配内存。
方法区：已被虚拟机加载的(元空间) 类信息、常量、静态变量、编译代码

虚拟机栈：每个方法被执行同时创建一个栈帧，存储局部变量表、操作栈、动态链接、方法出口等信息 。
本地方法栈：本地方法栈则是为虚拟机使用到的Native方法服务。
(二者保护线程中的局部变量不被别的线程所访问，so私有的啦）
程序计数器：字节码解释器通过改变程序计数器实现流程控制、多线程情况下给线程记录位置（so肯定是私有的啦）。也是唯一不会 OutOfMemoryError 的内存区，生命周期随着线程的而创建和销毁。

## 2,上下文切换
     一个CPU在任意时刻只能被一个线程所使用，为了让每一个线程有效执行就给他们分配了时间片进行轮转。当任务执行完当前时间片在进入下一个时间片之前进行状态保存，以便下次使用加载。这个过程就是上下文切换。（纳秒级，Linux>win）

## 3,死锁？如何避免？
多个线程同时被阻塞，都在等待某个资源被释放。

public class DeadLockDemo {
    private static Object resource1 = new Object();//资源 1
    private static Object resource2 = new Object();//资源 2
 
    public static void main(String[] args) {
        new Thread(() -> {
        synchronized (resource1) {
            System.out.println(Thread.currentThread() + "getresource1");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread() + "waitingget resource2");
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "getresource2");
                }
            }
        }, "线程 1").start();
        
        new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "getresource2");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread() + "waitingget resource1");
            
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "getresource1");
              }
            }
        }, "线程 2").start();
    }
}
输出结果：

Thread[线程 1,5,main]get resource1
Thread[线程 2,5,main]get resource2
Thread[线程 1,5,main]waiting get resource2
Thread[线程 2,5,main]waiting get resource1
两个线程休眠结束都会开始请求对方资源，线程进入互相等待状态。产生锁的四个条件和避免方式如下：

互斥：资源在任意时刻只能被一个线程调用。（无法破坏，临界资源有互斥访问的必要性）
请求与保持：进程因请求资源阻塞时，不释放现有资源。（一次性申请所有资源）
不剥夺：线程获得的资源没用完释放之前不能被剥夺。（申请不到，主动释放自占资源）
循环等待：几个进程形成头尾相接的循环等待。（按序申请）
## 4，sleep（）和wait（）的区别和共同点？
都可以暂停线程，Thread类的方法：sleep(),yield()等Object的方法：wait()和notify()等
sleep没有释放锁，暂停执行。wait释放了锁，用于线程间交互。wait不会自动苏醒，需要别的线程调用同一个对象的notify或者notifyall方法。sleep传入参数自动唤醒。
[      为什么wait、notify和notifyAll方法要和synchronized关键字一起使用?因为wait方法使一个线程进入等待状态，并释放其所持有的锁对象，notify方法是通知等待该锁对象的线程重新获得锁对象。所以前提是获得锁对象。锁对象就像一个传话的人，他对某个线程说停下来等待，然后对另一个线程说你可以执行了（实质上是被捕获了），这一过程是线程通信。sleep方法是让某个线程暂停运行一段时间，其控制范围是由当前线程决定，运行的主动权是由当前线程来控制（拥有CPU的执行权）]。
sleep是你困了，要睡觉，等你睡醒了再干活。
wait是你现在没事做，先眯会儿吧，什么时候领导提醒你该干活了再干。

## 5，start（）和run（）方法？
       新建(new)一个Thread线程，调用start()启动线程进入就绪状态，分配到时间片就运行。start()执行线程相应准备工作然后自动执行run()的内容。 单独直接run()的话就是Thread里面的一个普通方法而已。

## 6，synchronized关键字
    解决的是多个线程间访问资源的同步性。被其修饰的方法或者代码块在任意时刻只能有一个线程执行。

    修饰实例方法:作用于当前对象实例加锁，进入同步代码前要获得当前对象实例的锁。
    修饰静态方法:也就是给当前类加锁，会作用于类的所有对象实例，因为静态成员不属于任何一个实例对象，是类成员（ static表明这是该类的一个静态资源，不管new了多少个对象，只有一份）。所以如果一个线程A调用一个实例对象的非静态synchronized方法，而线程B需要调用这个实例对象所属类的静态 synchronized方法，是允许的，不会发生互斥现象，因为访问静态 synchronized方法占用的锁是当前类的锁，而访问非静态 synchronized方法占用的锁是当前实例对象锁。
    修饰代码块:指定加锁对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

    总结: synchronized关键字加到 static静态方法和 synchronized（ class）代码块上都是是给Cass类上锁。 synchronized关键字加到实例方法上是给对象实例上锁。尽量不要使用 synchronized（ string a）因为VM中，字符串常量池具有缓存功能。

## 7，说说JDK16之后的 synchronized关键字底层做了哪些优化？
         jDK1.6锁的实现引入了，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化等技术来减少锁操作的开销。

锁主要存在四种状态，依次是:无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态，随着竞争激烈而逐渐升级。但是不可降级，这种策略是为了提髙获得锁和释放锁的效率。

## 8，synchronized关键字和 volatile关键字的区别
      volatile关键字是线程同步的轻量级实现，性能稍微好些。但 volatile只修饰变量。
      synchronized关键字后来引入的偏向锁和轻量级锁等优化方式提升了效率，实际开发场景还是较多。
      volatile多线程访问 不会发生阻塞，而 synchronized关键字可能会发生阻塞。
      volatile保证数据可见性，但不保证原子性。synchronized关键字两者都能保证。
      volatile主要用于解决变量在多个线程之间的可见性，而 synchronized解决的是多个线程之间访问资源的同步性。
————————————————
版权声明：本文为CSDN博主「Leo秋实先生」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_37676429/article/details/105356634