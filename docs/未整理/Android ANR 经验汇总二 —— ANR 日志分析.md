> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7062981972615757854)

[Android ANR 经验汇总一 —— ANR 介绍](https://juejin.cn/post/7054093146631700488 "https://juejin.cn/post/7054093146631700488")  
[Android ANR 经验汇总二 —— ANR 日志分析](https://juejin.cn/post/7062981972615757854 "https://juejin.cn/post/7062981972615757854")

一、简介
====

发生 ANR 的时候，系统会产生一份 anr 日志文件（手机的 / data/anr 目录下，文件名称可能各厂商不一样，业内大多称呼为 trace 文件），我们需要从此文件中获取相关信息，进而定位 ANR 问题。

二、日志信息
======

 2.1 CPU 负载
-----------

```
CPU usage from 0ms to 1987ms later (2020-03-10 08:31:55.169 to 2020-03-10 08:32:17.156):
  41% 2080/system_server: 28% user + 12% kernel / faults: 76445 minor 180 major
  26% 9378/com.xiaomi.store: 20% user + 6.8% kernel / faults: 68408 minor 68 major
........省略N行.....
66% TOTAL: 20% user + 15% kernel + 28% iowait + 0.7% irq + 0.7% softirq


```

如上所示：

*   第一行：表明负载信息抓取在 ANR 发生之后的 0~1987ms。同时也指明了 ANR 的时间点：2020-03-10 08:31:55.169
*   中间部分：各个进程占用的 CPU 的详细情况
*   最后一行：各个进程合计占用的 CPU 信息。

> *   a. user: 用户态, kernel: 内核态
> *   b. faults: 内存缺页，minor——轻微的，major——重度，需要从磁盘拿数据
> *   c. iowait:IO 使用（等待）占比
> *   d. irq: 硬中断，softirq: 软中断

*   iowait 占比很高，意味着有很大可能，是 io 耗时导致 ANR，具体进一步查看有没有进程 faults major 比较多。
*   单进程 CPU 的负载并不是以 100% 为上限，而是有几个核，就有百分之几百，如 4 核上限为 400%。

2.2 内存
------

```
Total number of allocations 476778　　//进程创建到现在一共创建了多少对象

Total bytes allocated 52MB　//进程创建到现在一共申请了多少内存

Total bytes freed 52MB　　　//进程创建到现在一共释放了多少内存

Free memory 777KB　　　 //不扩展堆的情况下可用的内存

Free memory until GC 777KB　　//GC前的可用内存

Free memory until OOME 383MB　　//OOM之前的可用内存

Total memory 28MB//当前总内存（已用+可用）

Max memory 384MB  //进程最多能申请的内存



```

从含义可以得出结论：**Free memory until OOME** 的值很小的时候，已经处于内存紧张状态。应用可能是占用了过多内存。

**ps: 如果 ANR 时间点前后，日志里有打印 onTrimMemory，也可以作为内存紧张的一个参考判断**

2.3 堆栈信息
--------

堆栈信息是最重要的一个信息，展示了 ANR 发生的进程当前所有线程的状态。

```
suspend all histogram:  Sum: 2.834s 99% C.I. 5.738us-7145.919us Avg: 607.155us Max: 41543us
DALVIK THREADS (248):
"main" prio=5 tid=1 Native
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x74b17080 self=0x7bb7a14c00
  | sysTid=2080 nice=-2 cgrp=default sched=0/0 handle=0x7c3e82b548
  | state=S schedstat=( 757205342094 583547320723 2145008 ) utm=52002 stm=23718 core=5 HZ=100
  | stack=0x7fdc995000-0x7fdc997000 stackSize=8MB
  | held mutexes=
  kernel: __switch_to+0xb0/0xbc
  kernel: SyS_epoll_wait+0x288/0x364
  kernel: SyS_epoll_pwait+0xb0/0x124
  kernel: cpu_switch_to+0x38c/0x2258
  native: #00 pc 000000000007cd8c  /system/lib64/libc.so (__epoll_pwait+8)
  native: #01 pc 0000000000014d48  /system/lib64/libutils.so (android::Looper::pollInner(int)+148)
  native: #02 pc 0000000000014c18  /system/lib64/libutils.so (android::Looper::pollOnce(int, int*, int*, void**)+60)
  native: #03 pc 0000000000127474  /system/lib64/libandroid_runtime.so (android::android_os_MessageQueue_nativePollOnce(_JNIEnv*, _jobject*, long, int)+44)
  at android.os.MessageQueue.nativePollOnce(Native method)
  at android.os.MessageQueue.next(MessageQueue.java:330)
  at android.os.Looper.loop(Looper.java:169)
  at com.android.server.SystemServer.run(SystemServer.java:508)
  at com.android.server.SystemServer.main(SystemServer.java:340)
  at java.lang.reflect.Method.invoke(Native method)
  at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:536)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:856)
   
  ........省略N行.....
   
  "OkHttp ConnectionPool" daemon prio=5 tid=251 TimedWaiting
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x13daea90 self=0x7bad32b400
  | sysTid=29998 nice=0 cgrp=default sched=0/0 handle=0x7b7d2614f0
  | state=S schedstat=( 951407 137448 11 ) utm=0 stm=0 core=3 HZ=100
  | stack=0x7b7d15e000-0x7b7d160000 stackSize=1041KB
  | held mutexes=
  at java.lang.Object.wait(Native method)
  - waiting on <0x05e5732e> (a com.android.okhttp.ConnectionPool)
  at com.android.okhttp.ConnectionPool$1.run(ConnectionPool.java:103)
  - locked <0x05e5732e> (a com.android.okhttp.ConnectionPool)
  at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1167)
  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:641)
  at java.lang.Thread.run(Thread.java:764)



```

如上日志所示，本文截图了两个线程信息，一个是主线程 main，它的状态是 native。

另一个是 OkHttp ConnectionPool，它的状态是 TimeWaiting。众所周知，教科书上说线程状态有 5 种：**新建、就绪、执行、阻塞、死亡**。而 Java 中的线程状态有 6 种，6 种状态都定义在：java.lang.Thread.State 中

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f5b7d351f5fe43659502244dc291a48b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

**问题来了，上述 main 线程的 native 是什么状态，哪来的？**  
其实 trace 文件中的状态是是 CPP 代码中定义的状态，下面是一张对应关系表。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0afe4d600fd6477d8c3b07abc98542ce~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

由此可知，main 函数的 native 状态是正在执行 JNI 函数。堆栈信息是我们分析 ANR 的第一个重要的信息，一般来说：

**main 线程处于 BLOCK、WAITING、TIMEWAITING 状态，那基本上是函数阻塞导致 ANR； 如果 main 线程无异常，则应该排查 CPU 负载和内存环境。**

三、案例分析
======

3.1 主线程无卡顿，处于正常状态堆栈
-------------------

```
"main" prio=5 tid=1 Native
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x74b38080 self=0x7ad9014c00
  | sysTid=23081 nice=0 cgrp=default sched=0/0 handle=0x7b5fdc5548
  | state=S schedstat=( 284838633 166738594 505 ) utm=21 stm=7 core=1 HZ=100
  | stack=0x7fc95da000-0x7fc95dc000 stackSize=8MB
  | held mutexes=
  kernel: __switch_to+0xb0/0xbc
  kernel: SyS_epoll_wait+0x288/0x364
  kernel: SyS_epoll_pwait+0xb0/0x124
  kernel: cpu_switch_to+0x38c/0x2258
  native: #00 pc 000000000007cd8c  /system/lib64/libc.so (__epoll_pwait+8)
  native: #01 pc 0000000000014d48  /system/lib64/libutils.so (android::Looper::pollInner(int)+148)
  native: #02 pc 0000000000014c18  /system/lib64/libutils.so (android::Looper::pollOnce(int, int*, int*, void**)+60)
  native: #03 pc 00000000001275f4  /system/lib64/libandroid_runtime.so (android::android_os_MessageQueue_nativePollOnce(_JNIEnv*, _jobject*, long, int)+44)
  at android.os.MessageQueue.nativePollOnce(Native method)
  at android.os.MessageQueue.next(MessageQueue.java:330)
  at android.os.Looper.loop(Looper.java:169)
  at android.app.ActivityThread.main(ActivityThread.java:7073)
  at java.lang.reflect.Method.invoke(Native method)
  at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:536)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:876)


```

上述主线程堆栈就是一个很正常的空闲堆栈，表明主线程正在等待新的消息。

如果 ANR 日志里主线程是这样一个状态，那可能有两个原因：

> 1.  该 ANR 是 CPU 抢占或内存紧张等其他因素引起
> 2.  这份 ANR 日志抓取的时候，主线程已经恢复正常

遇到这种空闲堆栈，可以按照第 2 章的方法去分析 CPU、内存的情况。其次可以关注抓取日志的时间和 ANR 发生的时间是否相隔过久，时间过久这个堆栈就没有分析意义了。

3.2 主线程执行耗时操作
-------------

```
"main" prio=5 tid=1 Runnable
  | group="main" sCount=0 dsCount=0 flags=0 obj=0x72deb848 self=0x7748c10800
  | sysTid=8968 nice=-10 cgrp=default sched=0/0 handle=0x77cfa75ed0
  | state=R schedstat=( 24783612979 48520902 756 ) utm=2473 stm=5 core=5 HZ=100
  | stack=0x7fce68b000-0x7fce68d000 stackSize=8192KB
  | held mutexes= "mutator lock"(shared held)
  at com.example.test.MainActivity$onCreate$2.onClick(MainActivity.kt:20)——关键行！！！
  at android.view.View.performClick(View.java:7187)
  at android.view.View.performClickInternal(View.java:7164)
  at android.view.View.access$3500(View.java:813)
  at android.view.View$PerformClick.run(View.java:27640)
  at android.os.Handler.handleCallback(Handler.java:883)
  at android.os.Handler.dispatchMessage(Handler.java:100)
  at android.os.Looper.loop(Looper.java:230)
  at android.app.ActivityThread.main(ActivityThread.java:7725)
  at java.lang.reflect.Method.invoke(Native method)
  at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:526)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:1034)


```

上述日志表明，主线程正处于执行状态，看堆栈信息可知不是处于空闲状态，发生 ANR 是因为一处 click 监听函数里执行了耗时操作。

3.3 主线程被锁阻塞
-----------

```
"main" prio=5 tid=1 Blocked
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x72deb848 self=0x7748c10800
  | sysTid=22838 nice=-10 cgrp=default sched=0/0 handle=0x77cfa75ed0
  | state=S schedstat=( 390366023 28399376 279 ) utm=34 stm=5 core=1 HZ=100
  | stack=0x7fce68b000-0x7fce68d000 stackSize=8192KB
  | held mutexes=
  at com.example.test.MainActivity$onCreate$1.onClick(MainActivity.kt:15)
  - waiting to lock <0x01aed1da> (a java.lang.Object) held by thread 3 ——————关键行！！！
  at android.view.View.performClick(View.java:7187)
  at android.view.View.performClickInternal(View.java:7164)
  at android.view.View.access$3500(View.java:813)
  at android.view.View$PerformClick.run(View.java:27640)
  at android.os.Handler.handleCallback(Handler.java:883)
  at android.os.Handler.dispatchMessage(Handler.java:100)
  at android.os.Looper.loop(Looper.java:230)
  at android.app.ActivityThread.main(ActivityThread.java:7725)
  at java.lang.reflect.Method.invoke(Native method)
  at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:526)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:1034)
   
  ........省略N行.....
   
  "WQW TEST" prio=5 tid=3 TimeWating
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x12c44230 self=0x772f0ec000
  | sysTid=22938 nice=0 cgrp=default sched=0/0 handle=0x77391fbd50
  | state=S schedstat=( 274896 0 1 ) utm=0 stm=0 core=1 HZ=100
  | stack=0x77390f9000-0x77390fb000 stackSize=1039KB
  | held mutexes=
  at java.lang.Thread.sleep(Native method)
  - sleeping on <0x043831a6> (a java.lang.Object)
  at java.lang.Thread.sleep(Thread.java:440)
  - locked <0x043831a6> (a java.lang.Object)
  at java.lang.Thread.sleep(Thread.java:356)
  at com.example.test.MainActivity$onCreate$2$thread$1.run(MainActivity.kt:22)
  - locked <0x01aed1da> (a java.lang.Object)————————————————————关键行！！！
  at java.lang.Thread.run(Thread.java:919)


```

这是一个典型的主线程被锁阻塞的例子； 其中等待的锁是 <0x01aed1da>，这个锁的持有者是线程 3。进一步搜索 “tid=3” 找到线程 3， 发现它正在 TimeWating。

那么 ANR 的原因找到了：线程 3 持有了一把锁，并且自身长时间不释放，主线程等待这把锁发生超时。在线上环境中，常见因锁而 ANR 的场景是 SharePreference 写入。

3.4 CPU 被抢占
-----------

```
CPU usage from 0ms to 10625ms later (2020-03-09 14:38:31.633 to 2020-03-09 14:38:42.257):
  543% 2045/com.alibaba.android.rimet: 54% user + 89% kernel / faults: 4608 minor 1 major ————关键行！！！
  99% 674/android.hardware.camera.provider@2.4-service: 81% user + 18% kernel / faults: 403 minor
  24% 32589/com.wang.test: 22% user + 1.4% kernel / faults: 7432 minor 1 major
  ........省略N行.....


```

如上日志，第二行是钉钉的进程，占据 CPU 高达 543%，抢占了大部分 CPU 资源，因而导致发生 ANR。

3.5 内存紧张导致 ANR
--------------

如果有一份日志，CPU 和堆栈都很正常（不贴出来了），仍旧发生 ANR，考虑是内存紧张。

从 CPU 第一行信息可以发现，ANR 的时间点是 2020-10-31 22:38:58.468—CPU usage from 0ms to 21752ms later (2020-10-31 22:38:58.468 to 2020-10-31 22:39:20.220)

接着去 logcat 里搜索 am_meminfo， 这个没有搜索到。再次搜索 onTrimMemory，果然发现了很多条记录；

```
10-31 22:37:19.749 20733 20733 E Runtime : onTrimMemory level:80,pid:com.xxx.xxx:Launcher0
10-31 22:37:33.458 20733 20733 E Runtime : onTrimMemory level:80,pid:com.xxx.xxx:Launcher0
10-31 22:38:00.153 20733 20733 E Runtime : onTrimMemory level:80,pid:com.xxx.xxx:Launcher0
10-31 22:38:58.731 20733 20733 E Runtime : onTrimMemory level:80,pid:com.xxx.xxx:Launcher0
10-31 22:39:02.816 20733 20733 E Runtime : onTrimMemory level:80,pid:com.xxx.xxx:Launcher0


```

可以看出，在发生 ANR 的时间点前后，内存都处于紧张状态，level 等级是 80，查看 Android API 文档； 可知 80 这个等级是很严重的，应用马上就要被杀死，被杀死的这个应用从名字可以看出来是桌面，连桌面都快要被杀死，那普通应用能好到哪里去呢？

一般来说，发生内存紧张，会导致多个应用发生 ANR，所以在日志中如果发现有多个应用一起 ANR 了，可以初步判定，此 ANR 与你的应用无关。

3.6 系统服务超时导致 ANR
----------------

系统服务超时一般会包含 BinderProxy.transactNative 关键字，请看如下日志：

```
"main" prio=5 tid=1 Native
  | group="main" sCount=1 dsCount=0 flags=1 obj=0x727851e8 self=0x78d7060e00
  | sysTid=4894 nice=0 cgrp=default sched=0/0 handle=0x795cc1e9a8
  | state=S schedstat=( 8292806752 1621087524 7167 ) utm=707 stm=122 core=5 HZ=100
  | stack=0x7febb64000-0x7febb66000 stackSize=8MB
  | held mutexes=
  kernel: __switch_to+0x90/0xc4
  kernel: binder_thread_read+0xbd8/0x144c
  kernel: binder_ioctl_write_read.constprop.58+0x20c/0x348
  kernel: binder_ioctl+0x5d4/0x88c
  kernel: do_vfs_ioctl+0xb8/0xb1c
  kernel: SyS_ioctl+0x84/0x98
  kernel: cpu_switch_to+0x34c/0x22c0
  native: #00 pc 000000000007a2ac  /system/lib64/libc.so (__ioctl+4)
  native: #01 pc 00000000000276ec  /system/lib64/libc.so (ioctl+132)
  native: #02 pc 00000000000557d4  /system/lib64/libbinder.so (android::IPCThreadState::talkWithDriver(bool)+252)
  native: #03 pc 0000000000056494  /system/lib64/libbinder.so (android::IPCThreadState::waitForResponse(android::Parcel*, int*)+60)
  native: #04 pc 00000000000562d0  /system/lib64/libbinder.so (android::IPCThreadState::transact(int, unsigned int, android::Parcel const&, android::Parcel*, unsigned int)+216)
  native: #05 pc 000000000004ce1c  /system/lib64/libbinder.so (android::BpBinder::transact(unsigned int, android::Parcel const&, android::Parcel*, unsigned int)+72)
  native: #06 pc 00000000001281c8  /system/lib64/libandroid_runtime.so (???)
  native: #07 pc 0000000000947ed4  /system/framework/arm64/boot-framework.oat (Java_android_os_BinderProxy_transactNative__ILandroid_os_Parcel_2Landroid_os_Parcel_2I+196)
  at android.os.BinderProxy.transactNative(Native method) ————————————————关键行！！！
  at android.os.BinderProxy.transact(Binder.java:804)
  at android.net.IConnectivityManager$Stub$Proxy.getActiveNetworkInfo(IConnectivityManager.java:1204)—关键行！
  at android.net.ConnectivityManager.getActiveNetworkInfo(ConnectivityManager.java:800)
  at com.xiaomi.NetworkUtils.getNetworkInfo(NetworkUtils.java:2)
  at com.xiaomi.frameworkbase.utils.NetworkUtils.getNetWorkType(NetworkUtils.java:1)
  at com.xiaomi.frameworkbase.utils.NetworkUtils.isWifiConnected(NetworkUtils.java:1)


```

从堆栈可以看出获取网络信息发生了 ANR：getActiveNetworkInfo。

前文有讲过：系统的服务都是 Binder 机制（16 个线程），服务能力也是有限的，有可能系统服务长时间不响应导致 ANR。如果其他应用占用了所有 Binder 线程，那么当前应用只能等待。

可进一步搜索：blockUntilThreadAvailable 关键字：

```
at android.os.Binder.blockUntilThreadAvailable(Native method)


```

如果有发现某个线程的堆栈，包含此字样，可进一步看其堆栈，确定是调用了什么系统服务。此类 ANR 也是属于系统环境的问题，如果某类型机器上频繁发生此问题，应用层可以考虑规避策略。

其他信息
====

当发生 ANR 时，在 log 日志中也能获取一些信息

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f26db3ec0d1d49ae8fd17bb8276711fe~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?) 这部分的信息如果不注意是很难发现的，截图的内容只是其中输出的一部分，简要解释一下这些内容的含义

1) ANR in xxx，PID: xxx，Reason：xxx
---------------------------------

这里简要叙述了出现 ANR 的应用以及进程号，比如上面的截图，ANR 发生在 aartest 应用中，对应的进程号是 2509，后续还给出了出现 ANR 的原因，当然这种原因并不能帮我们直接发现问题根源  
一般来说原因有两类，分别对应于 InputDispatcher.cpp 文件的两段代码，InputDispatcher 是派发事件过程中的一个中转类，每次派发事件时他会进行如下判断

### a) 是否有 focused 组件以及 focusedApplication

这种一般是在应用启动时触发，比如启动时间过长在这过程中触发了 keyevent 或者 trackball motionevent 就会出现 ![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8ee851b41a9a4529abd953ab4894f63b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?) 这种情况下，对应的 Reason 类似于这样

```
Reason: Input dispatching timed out (Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.)


```

我们 Monkey 脚本中出现的 ANR 问题绝大部分都是属于这种问题，实际上出现这种 ANR 之前我们应用一般发生了崩溃需要重启，但是重启进行的操作比较耗时，但是具体并不清楚耗时的地方。

### b) 判断前面的事件是否及时完成

这里的事件包含 keyevent 和 touchevent，虽然它们对允许的延时要求不一样，但最终都会执行到如下代码 ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ae4c68ad12c4953a69398e9a18c8df5~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?) 这种情况下，对应的 Reason 类似于这样

```
Reason: Input dispatching timed out (Waiting to send non-key event because the touched window has not finished processing certain input events that were delivered to it over 500.0ms ago. Wait queue length: 10. Wait queue head age: 5591.3ms.)


```

出现这种问题意味着主线程正在执行其他的事件但是比较耗时导致输入事件无法及时处理。

Load: 0.24 / 0.06 / 0.06
------------------------

这里输出了 CPU 的负载情况  
CPU 负载是指某一时刻系统中运行队列长度之和加上当前正在 CPU 上运行的进程数，而 CPU 平均负载可以理解为一段时间内正在使用和等待使用 CPU 的活动进程的平均数量。这里的三个数值分别对应 1、5、15 分钟内正在使用和等待使用 CPU 的活动进程的平均数

感谢
==

[干货：ANR 日志分析全面解析](https://juejin.cn/post/6971327652468621326#heading-12 "https://juejin.cn/post/6971327652468621326#heading-12")  
[Android 应用 ANR 分析](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F30c1a5ad63a3 "https://www.jianshu.com/p/30c1a5ad63a3")