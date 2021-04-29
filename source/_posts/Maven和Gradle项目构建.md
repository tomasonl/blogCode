---
title: Maven和Grade构建项目
date: 2020-02-01 08:26:59
tags: 工作记录

copyright_author: 秋实先生
copyright_author_href: https://github.com/tomasonl?tab=repositories
copyright_url: https://tomasonl.github.io
copyright_info: 此文章版权归属Tomasonl秋实先生所有，如有转载请注明出处

cover: https://cdn.jsdelivr.net/gh/tomasonl/CDNRepo@1.1/Pic/Blogcover/%E6%97%A5%E5%B8%B8%E9%9A%8F%E7%AC%94.jpg
---

# 一、Maven介绍
## 一、maven介绍
maven 是一个项目管理工具，主要作用是在项目开发阶段对Java项目进行依赖管理和项目构建。
依赖管理：就是对jar包的管理。通过导入maven坐标，就相当于将仓库中的jar包导入了当前项目中。
项目构建：通过maven的一个命令就可以完成项目从清理、编译、测试、报告、打包，部署整个过程。

①maven中央仓库（地址：http://repo2.maven.org/maven2/）
②maven私服（公司局域网内的仓库，需要自己搭建）
③其他公共远程仓库（例如apache提供的远程仓库，地址：http://repo.maven.apache.org/maven2/）

clean： 清理
compile：编译
test： 测试
package：打包
install： 安装

## 二、分模块开发
继承！！！parent！！！


聚合！！！ module！！！


Dependencies和dependencyManagement
dependencyManagement-----统一管理项目的版本号，确保应用的各个项目的依赖和版本一致，升级版本方便；
子项目需要特定版本号时，只需在其dependencies中声明，就可使用自己的不继承父类的。
dependencies-----所有声明在dependencies里的依赖都会自动引入，并默认被所有的子项目继承。

Build标签
一种<build>被称为Project Build，即是<project>子元素。另一种<build>被称为Profile Build，即是<profile>的直接子元素。

Profile Build包含了基本的build元素，而Project Build还包含两个特殊的元素，即各种<...Directory>和<extensions>。

plugins---用于指定使用的插件；pluginManagement只是用于继承，使得可以在子pom中使用。

# 二、Gradle介绍


## 一、Gradle介绍+Groove语言
       Gradle，使用Groovy语言基于Apache Ant和Apache Maven概念的项目自动化构建开源工具。
Groovy是一种基于JVM的语言，它结合了Python、Ruby强大的特性，与 Java 代码易结合、扩展。

2000Ant---2004Maven---2012Gradle

轻量化趋势，Gradle优点：简化Maven繁琐xml配置、强大支持多工程构建、Groove语言性能。

语法：

输出： println “hello，world！”---------比较随意，单引号双引号随意，分号可以不加，括号也可以没有。给力！
定义变量：def 赋值决定变量----类似js 也可以强类型定义 int a=666;不过没有基本类型，都是对象类型。
lList：def numbers = [1, 2, 3]；Groovy 不支持 Java 数组初始化符号 {}，因为 {} 可能会被曲解成 Groovy 闭包的符号；
Map：def colors = [red: '#FF0000', green: '#00FF00', blue: '#0000FF']   
闭包：代码块，可以作为参数在方法之间传递。就是定义一个 def m{sout }
build.gradle----局部设置

settings.gradle---全局设置

## 二、Gradle组成
1. Project与Task
在Gradle中，每一个待构建的工程是一个Project，构建一个Project需要执行一系列Task，比如编译、打包这些构建过程的子过程都对应着一个Task。

2. 插件
插件用来定义和执行Task。

在新建工程的app模块的build.gradle文件的第一行，往往都是如下这句：

apply plugin: 'com.android.application'
这句话的意思就是应用“com.android.application“这个插件来构建app模块，app模块就是Gradle中的一个Project。也就是说，这个插件负责定义并执行Java源码编译、资源文件编译、打包等一系列Task。实际上"com.android.application"整个插件中定义了如下4个顶级任务：

assemble: 构建项目的输出（apk）

check: 进行校验工作

build: 执行assemble任务与check任务

clean: 清除项目的输出

当我们执行一个任务时，会自动执行它所依赖的任务。比如，执行assemble任务会执行assembleDebug任务和assembleRelease任务，这是因为一个Android项目至少要有debug和release这两个版本的输出。

3. Gradle配置文件
我们在Android Studio中新建一个工程，可以得到如下的工程结构图：



上面我们说过，Android Studio中的一个Module即为Gradle中的一个Project。上图的app目录下，存在一个build.gradle文件，代表了app Module的构建脚本，它定义了应用于本模块的构建规则。我们可以看到，工程根目录下也存在一个build.gradle文件，它代表了整个工程的构建，其中定义了适用于这个工程中所有模块的构建规则。

接下来我们介绍一下上图中其他几个Gradle配置文件：

gradle.properties: 从它的名字可以看出，这个文件中定义了一系列“属性”。实际上，这个文件中定义了一系列供build.gradle使用的常量，比如keystore的存储路径、keyalias等等。

gradlew与gradlew.bat: gradlew为Linux下的shell脚本，gradlew.bat是Windows下的批处理文件。gradlew是gradle wrapper的缩写，也就是说它对gradle的命令进行了包装，比如我们进入到指定Module目录并执行“gradlew.bat assemble”即可完成对当前Module的构建（Windows系统下）。

local.properties: 从名字就可以看出来，这个文件中定义了一些本地属性，比如SDK的路径。

settings.gradle: 假如我们的项目包含了不只一个Module时，我们想要一次性构建所有Module以完成整个项目的构建，这时我们需要用到这个文件。比如我们的项目包含了ModuleA和ModuleB这两个模块，则这个文件中会包含这样的语句：include ':ModuleA', ':ModuleB'。

4. 构建脚本
首先我们来看一下工程目录下的build.gradle，它指定了真个整个项目的构建规则，它的内容如下：

buildscript {
    repositories {
        jcenter() //构建脚本中所依赖的库都在jcenter仓库下载
    }
    dependencies {
        //指定了gradle插件的版本
        classpath 'com.android.tools.build:gradle:1.5.0'
    }
}

allprojects {
    repositories {
        //当前项目所有模块所依赖的库都在jcenter仓库下载
        jcenter()
    }
}

我们再来简单介绍下app模块的build.gradle的内容：

//加载用于构建Android项目的插件
apply plugin: 'com.android.application'

android { //构建Android项目使用的配置
    compileSdkVersion 23 //指定编译项目时使用的SDK版本
    buildToolsVersion "23.0.1" //指定构建工具的版本

    defaultConfig {
        applicationId "com.absfree.debugframwork" //包名
        minSdkVersion 15  //指定支持的最小SDK版本
        targetSdkVersion 23 //针对的目标SDK版本
        versionCode 1 
        versionName "1.0"
    }
    buildTypes { //针对不同的构建版本进行一些设置
        release { //对release版本进行的设置
            minifyEnabled false //是否开启混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'  //指定混淆文件的位置
        }
    }
}

dependencies { //指定当前模块的依赖
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.1.1'
    compile 'com.android.support:design:23.1.1'
}

## 三、常见配置
整个工程的build.gradle通常不需我们改动，这里我们介绍下一些对模块目录下build.gradle文件的常见配置。

1. 依赖第三方库
当我们的项目中用到了了一些第三方库时，我们就需要进行一些配置，以保证能正确导入相关依赖。设置方法很简单，比如我们在app模块中中用到了Fresco，只需要在build.gradle文件中的dependencies块添加如下语句：

dependencies {
    ...
    compile 'com.facebook.fresco:fresco:0.11.0'
}
这样一来，Gradle会自动从jcenter仓库下载我们所需的第三方库并导入到项目中。

2. 导入本地jar包
在使用第三方库时，除了像上面那样从jcenter仓库下载，我们还可以导入本地的jar包。配置方法也很简单，只需要先把jar文件添加到app\libs目录下，然后在相应jar文件上单击右键，选择“Ad As Library”。然后在build.gradle的dependencies块下添加如下语句：

compile files('libs/xxx.jar')
实际上我们可以看到，系统为我们创建的build.gradle中就已经包含了如下语句：

compile fileTree(dir: 'libs', include: ['*.jar'])
这句话的意思是，将libs目录下的所有jar包都导入。所以实际上我们只需要把jar包添加到libs目录下并“Ad As Library"即可。

3. 依赖其它模块
假设我们的项目包含了多个模块，并且app模块依赖other模块，那么我们只需app\build.gradle的denpendencies块下添加如下语句：

compile project(':other')
4. 构建输出为aar文件
通常我们构建的输出目标都是apk文件，但如果我们的当前项目时Android Library，我们的目标输出就是aar文件。要想达到这个目的也很容易，只需要把build.gradle的第一句改为如下：

apply plugin:'com.android.library'
这话表示我们使用的插件不再是构建Android应用的插件，而是构建Android Library的插件，这个插件定义并执行用于构建Android Library的一系列Task。

5. 自动移除不再使用的资源
只需进行如下配置：

android {
    ...
    }
    buildTypes {
        release {
            ...
            shrinkResources true
            ...
        }
    }
}
**TIPS：**
1. 深入理解Android之Gradle:http://blog.csdn.net/Innost/article/details/48228651

2. Gradle构建最佳实践: http://www.figotan.org/2016/04/01/gradle-on-android-best-practise/



部分结合作者-Bonker 地址-https://www.cnblogs.com/Bonker/p/5619458.html