> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.lisok.cn](https://www.lisok.cn/reverse-programming/306.html)

![](https://thirdqq.qlogo.cn/g?b=qq&nk=1727182921&s=100)

04/22

> 本文根据吾爱破解论坛, 作者:[lvbuqing](https://www.lisok.cn/go/aHR0cHM6Ly93d3cuNTJwb2ppZS5jbi9ob21lLnBocD9tb2Q9c3BhY2UmdWlkPTg0MTU5Ng) 的帖子[浅尝 Restful Fast Request 插件，一句话完成 逆向过程](https://www.lisok.cn/go/aHR0cHM6Ly93d3cuNTJwb2ppZS5jbi9mb3J1bS5waHA_bW9kPXZpZXd0aHJlYWQmdGlkPTE3NzY5MTAmcGlkPTQ2NDc0MTg2JnBhZ2U9MSZleHRyYT0jcGlkNDY0NzQxODY)进行操作复现

1. 插件简介
-------

![](https://www.lisok.cn/usr/uploads/2023/04/1172031307.png)

2. 破解流程
-------

### 2.1 检测 1-ide 层

根据 [Jetbrains 官网插件开发文档](https://www.lisok.cn/go/aHR0cHM6Ly9wbHVnaW5zLmpldGJyYWlucy5jb20vZG9jcy9pbnRlbGxpai9wbHVnaW4tY29uZmlndXJhdGlvbi1maWxlLmh0bWwjaWRlYS1wbHVnaW5fX3Byb2R1Y3QtZGVzY3JpcHRvcg)了解到, 付费插件的配置文件中都会有`product-descriptor`标签

![](https://www.lisok.cn/usr/uploads/2023/04/2208956379.png)

属性介绍

<table><thead><tr><th>属性名</th><th>描述</th></tr></thead><tbody><tr><td>code</td><td>销售系统中使用的插件产品代码, 代码必须提前和 Jetbrains 约定好, 按照要求来。</td></tr><tr><td>release-date</td><td>格式中主要版本发布的日期 YYYYMMDD</td></tr><tr><td>release-version</td><td>特殊数字格式的主要版本</td></tr></tbody></table>

安装完重新 idea 后会卡在  
![](https://www.lisok.cn/usr/uploads/2023/04/1767840135.png)

, 所以先解决这个第一层校验。

1. 找到插件的`lib`目录

我使用的`Toolbox`, 目录在:`C:\Users\Administrator\AppData\Local\JetBrains\Toolbox\apps\IDEA-U\ch-0\231.8109.175.plugins\Restful Fast Request\lib`

该插件的关键`jar`包为:`instrumented-restful-fast-request-pro-2023.1.3.2.jar`

2. 直接使用压缩包打开`jar`文件, 使用`vscode`编辑 `META-INF\plugin.xml`

![](https://www.lisok.cn/usr/uploads/2023/04/1497727812.png)

直接移除这一行。

3. 我们重新打开 idea ，发现已经去掉了收费标识，，我们需要启用后重启

![](https://www.lisok.cn/usr/uploads/2023/04/2414544680.png)

### 2.2 检测 2 - 插件层

虽然功能键已经展示出来, 但是点击后还是无法使用，会提示先激活插件。

![](https://www.lisok.cn/usr/uploads/2023/04/169852367.png)

`jar`包解压, 发现程序做了国际化, 然后全局检索关键字

![](https://www.lisok.cn/usr/uploads/2023/04/355780572.png)

得到关键`key`值为:`ActivatePluginNeed`

使用`Jadx`对程序进行反编译后全局搜索关键 key

发现如下的调用

![](https://www.lisok.cn/usr/uploads/2023/04/1078857160.png)

继续查`l11Il1III1111.l111I1llllI()`这个方法

![](https://www.lisok.cn/usr/uploads/2023/04/4223268844.png)

也就是让这个方法返回`false`

接下来的问题就是如何修改原来的`jar`文件中的字节码文件

![](https://www.lisok.cn/usr/uploads/2023/04/4139856218.png)

可以根据`Javassist`来实现

3.Javassist
-----------

> Javassist (Java Programming Assistant) makes Java bytecode manipulation simple. It is a class library for editing bytecodes in Java; it enables Java programs to define a new class at runtime and to modify a class file when the JVM loads it. Unlike other similar bytecode editors, Javassist provides two levels of API: source level and bytecode level. If the users use the source-level API, they can edit a class file without knowledge of the specifications of the Java bytecode. The whole API is designed with only the vocabulary of the Java language. You can even specify inserted bytecode in the form of source text; Javassist compiles it on the fly. On the other hand, the bytecode-level API allows the users to directly edit a class file as other editors.

![](https://www.lisok.cn/usr/uploads/2023/04/3753978811.png)

### 3.1 什么是 javassist

`javassist`是一个处理 Java 字节码的 jar 包，里面有很多类。

### 3.2 什么是 ClassPool

可以想象成一个容器，里面放着指定路径下的 class 文件，使用 javassist 对类进行操作的时候，必须先创建一个 ClassPool。

它也可以暂时存放我们编辑的 class 文件，等写完后再拿出来放到指定的位置。我们对 class 文件的操作是在 ClassPool 中的进行的。

假如我们想获取一个 Class 文件进行修改，如果 ClassPool 的路径中没有它，那么我们是找不到的，必须使用`insertClassPath();`函数将 class 文件路径导入 ClassPool 中才可以。

如果我们不自定义路径，那么它的类的搜索路径包括平台库、扩展库以及由 - classpath 选项或 CLASSPATH 环境变量指定的搜索路径。

### 3.3 什么是 CtClass

CtClass 是 javassist 中的一个类文件，它的对象可以理解成一个 class 文件的抽象表示。

一个 CtClass 对象可以用来修改一个 class 文件。

4.Javassist 实操修改字节码文件
---------------------

### 4.1 项目导包

```
<dependency>
    <groupId>org.javassist</groupId>
    <artifactId>javassist</artifactId>
    <version>3.27.0-GA</version>
</dependency>

```

### 4.2 编写代码

```
package com.lisok.test;

import javassist.*;

import java.io.IOException;

public class HookMain {
    public static void hookMethod() throws NotFoundException, CannotCompileException, IOException {
        ClassPool pool =  ClassPool.getDefault();
        
        pool.insertClassPath("D:\\Tools\\AndroidReverse\\instrumented-restful-fast-request-pro-2023.1.3.2.jar");
        CtClass driverClass = pool.get("io.github.kings1990.plugin.fastrequest.cofig.l11Il1III1111");
        CtMethod[] declaredMethods = driverClass.getDeclaredMethods();
        CtMethod hookMethod = null;
        for (CtMethod declaredMethod : declaredMethods) {
            String name = declaredMethod.getName();
            if (name.equals("l111I1llllI")) {
                hookMethod = declaredMethod;
                break;
            }
        }
        if (hookMethod != null) {
            System.out.println(hookMethod.getDeclaringClass());
            hookMethod.setBody("return false;");
        }

        driverClass.writeFile("writefile");
    }

    public static void main(String[] args) {
        try {
            hookMethod();
        } catch (NotFoundException | CannotCompileException | IOException e) {
            e.printStackTrace();
        }
    }
}


```

运行后项目会出现`writefile`目录, 找到目标文件后, 使用压缩包打开原`jar`文件进行替换即可。  
![](https://www.lisok.cn/usr/uploads/2023/04/2537165721.png)

5. 效果图
------

![](https://www.lisok.cn/usr/uploads/2023/04/3207243037.png)

![](https://www.lisok.cn/usr/uploads/2023/04/2405960643.png)

引用
--

1. 浅尝 Restful Fast Request 插件，一句话完成 逆向过程：[https://www.52pojie.cn/forum.php?mod=viewthread&tid=1776910&pid=46474186&page=1&extra=#pid46474186](https://www.lisok.cn/go/aHR0cHM6Ly93d3cuNTJwb2ppZS5jbi9mb3J1bS5waHA_bW9kPXZpZXd0aHJlYWQmdGlkPTE3NzY5MTAmcGlkPTQ2NDc0MTg2JnBhZ2U9MSZleHRyYT0jcGlkNDY0NzQxODY)

2.Javassist by jboss-javassist:[https://www.javassist.org/](https://www.lisok.cn/go/aHR0cHM6Ly93d3cuamF2YXNzaXN0Lm9yZy8)

3.java 中 javassist、ClassPool、CtClass、Apache CC2 链学习:[https://blog.csdn.net/qq_41874930/article/details/121273650](https://www.lisok.cn/go/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODc0OTMwL2FydGljbGUvZGV0YWlscy8xMjEyNzM2NTA)

4.Javassist 用法详解：[https://www.jb51.net/article/205638.htm](https://www.lisok.cn/go/aHR0cHM6Ly93d3cuamI1MS5uZXQvYXJ0aWNsZS8yMDU2MzguaHRt)