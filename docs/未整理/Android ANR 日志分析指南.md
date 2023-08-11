> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6844903715313303565)

> 当你的项目越做越复杂，或者你的用户达到某个数量级的时候，你的代码不小心出现细小的问题，你会收到各种各样的 bug, 其中 ANR 的问题你一定不会陌生。本文将详细讲解 ANR 的类型、出现的原因、ANR 案例详细分析、经典的案例。

定义
--

`ANR(Application Not Responding)` 应用程序无响应。如果你应用程序在 UI 线程被阻塞太长时间，就会出现 ANR，通常出现 ANR，系统会弹出一个提示提示框，让用户知道，该程序正在被阻塞，是否继续等待还是关闭。

ANR 类型
------

出现 ANR 的一般有以下几种类型：  
1:**KeyDispatchTimeout**（常见）  
input 事件在`5S`内没有处理完成发生了 ANR。  
logcat 日志关键字：`Input event dispatching timed out`  

2:**BroadcastTimeout**  
前台 Broadcast：onReceiver 在`10S`内没有处理完成发生 ANR。  
后台 Broadcast：onReceiver 在`60s`内没有处理完成发生 ANR。  
logcat 日志关键字：`Timeout of broadcast BroadcastRecord`  

3:**ServiceTimeout**  
前台 Service：`onCreate`，`onStart`，`onBind`等生命周期在`20s`内没有处理完成发生 ANR。  
后台 Service：`onCreate`，`onStart`，`onBind`等生命周期在`200s`内没有处理完成发生 ANR  
logcat 日志关键字：`Timeout executing service`  

4：**ContentProviderTimeout**  
ContentProvider 在`10S`内没有处理完成发生 ANR。 logcat 日志关键字：timeout publishing content providers  

ANR 出现的原因
---------

1: 主线程频繁进行耗时的 IO 操作：如数据库读写  
2: 多线程操作的死锁，主线程被 block；  
3: 主线程被 Binder 对端 block；  
4:`System Server`中 WatchDog 出现 ANR；  
5:`service binder`的连接达到上线无法和和 System Server 通信  
6: 系统资源已耗尽（管道、CPU、IO）  

ANR 案例分析过程
----------

我们将一步一步分析 ANR, 这个过程更加理解如何找到问题、分析问题以及解决问题。

#### 一、 查看 events_log

查看 mobilelog 文件夹下的 events_log, 从日志中搜索关键字：`am_anr`，找到出现 ANR 的时间点、进程 PID、ANR 类型。

如日志：

```
07-20 15:36:36.472  1000  1520  1597 I am_anr  : [0,1480,com.xxxx.moblie,952680005,Input dispatching timed out (AppWindowToken{da8f666 token=Token{5501f51 ActivityRecord{15c5c78 u0 com.xxxx.moblie/.ui.MainActivity t3862}}}, Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.)]


```

从上面的 log 我们可以看出： 应用`com.xxxx.moblie` 在`07-20 15:36:36.472`时间，发生了一次`KeyDispatchTimeout`类型的 ANR，它的进程号是`1480`. 把关键的信息整理一下：  
**ANR 时间**：07-20 15:36:36.472  
**进程 pid**：1480  
**进程名**：com.xxxx.moblie  
**ANR 类型**：KeyDispatchTimeout  

我们已经知道了发生`KeyDispatchTimeout`的 ANR 是因为 `input事件在5秒内没有处理完成`。那么在这个时间`07-20 15:36:36.472` 的前 5 秒，也就是（`15:36:30 ~15:36:31`）时间段左右程序到底做了什么事情？这个简单，因为我们已经知道 pid 了，再搜索一下`pid = 1480`的日志. 这些日志表示该进程所运行的轨迹，关键的日志如下：

```
07-20 15:36:29.749 10102  1480  1737 D moblie-Application: [Thread:17329] receive an intent from server, action=com.ttt.push.RECEIVE_MESSAGE
07-20 15:36:30.136 10102  1480  1737 D moblie-Application: receiving an empty message, drop
07-20 15:36:35.791 10102  1480  1766 I Adreno  : QUALCOMM build                   : 9c9b012, I92eb381bc9
07-20 15:36:35.791 10102  1480  1766 I Adreno  : Build Date                       : 12/31/17
07-20 15:36:35.791 10102  1480  1766 I Adreno  : OpenGL ES Shader Compiler Version: EV031.22.00.01
07-20 15:36:35.791 10102  1480  1766 I Adreno  : Local Branch                     : 
07-20 15:36:35.791 10102  1480  1766 I Adreno  : Remote Branch                    : refs/tags/AU_LINUX_ANDROID_LA.UM.6.4.R1.08.00.00.309.049
07-20 15:36:35.791 10102  1480  1766 I Adreno  : Remote Branch                    : NONE
07-20 15:36:35.791 10102  1480  1766 I Adreno  : Reconstruct Branch               : NOTHING
07-20 15:36:35.826 10102  1480  1766 I vndksupport: sphal namespace is not configured for this process. Loading /vendor/lib64/hw/gralloc.msm8998.so from the current namespace instead.
07-20 15:36:36.682 10102  1480  1480 W ViewRootImpl[MainActivity]: Cancelling event due to no window focus: KeyEvent { action=ACTION_UP, keyCode=KEYCODE_PERIOD, scanCode=0, metaState=0, flags=0x28, repeatCount=0, eventTime=16099429, downTime=16099429, deviceId=-1, source=0x101 }


```

从上面我们可以知道，在时间 07-20 15:36:29.749 程序收到了一个 action 消息。

```
07-20 15:36:29.749 10102  1480  1737 D moblie-Application: [Thread:17329] receive an intent from server, action=com.ttt.push.RECEIVE_MESSAGE。



```

原来是应用`com.xxxx.moblie` 收到了一个推送消息（`com.ttt.push.RECEIVE_MESSAGE`）导致了阻塞，我们再串联一下目前所获取到的信息：在时间`07-20 15:36:29.749` 应用`com.xxxx.moblie` 收到了一下推送信息`action=com.ttt.push.RECEIVE_MESSAGE`发生阻塞，5 秒后发生了`KeyDispatchTimeout的ANR`。

虽然知道了是怎么开始的，但是具体原因还没有找到，是不是当时 CPU 很紧张、各路 APP 再抢占资源？ 我们再看看 CPU 的信息,。搜索关键字关键字： `ANR IN`  

```
07-20 15:36:58.711  1000  1520  1597 E ActivityManager: ANR in com.xxxx.moblie (com.xxxx.moblie/.ui.MainActivity) (进程名)
07-20 15:36:58.711  1000  1520  1597 E ActivityManager: PID: 1480 (进程pid)
07-20 15:36:58.711  1000  1520  1597 E ActivityManager: Reason: Input dispatching timed out (AppWindowToken{da8f666 token=Token{5501f51 ActivityRecord{15c5c78 u0 com.xxxx.moblie/.ui.MainActivity t3862}}}, Waiting because no window has focus but there is a focused application that may eventually add a window when it finishes starting up.)
07-20 15:36:58.711  1000  1520  1597 E ActivityManager: Load: 0.0 / 0.0 / 0.0 (Load表明是1分钟,5分钟,15分钟CPU的负载)
07-20 15:36:58.711  1000  1520  1597 E ActivityManager: CPU usage from 20ms to 20286ms later (2018-07-20 15:36:36.170 to 2018-07-20 15:36:56.436):
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   42% 6774/pressure: 41% user + 1.4% kernel / faults: 168 minor
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   34% 142/kswapd0: 0% user + 34% kernel
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   31% 1520/system_server: 13% user + 18% kernel / faults: 58724 minor 1585 major
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   13% 29901/com.ss.android.article.news: 7.7% user + 6% kernel / faults: 56007 minor 2446 major
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   13% 32638/com.android.quicksearchbox: 9.4% user + 3.8% kernel / faults: 48999 minor 1540 major
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   11% (CPU的使用率)1480/com.xxxx.moblie: 5.2%(用户态的使用率) user + (内核态的使用率) 6.3% kernel / faults: 76401 minor 2422 major
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   8.2% 21000/kworker/u16:12: 0% user + 8.2% kernel
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   0.8% 724/mtd: 0% user + 0.8% kernel / faults: 1561 minor 9 major
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   8% 29704/kworker/u16:8: 0% user + 8% kernel
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   7.9% 24391/kworker/u16:18: 0% user + 7.9% kernel
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   7.1% 30656/kworker/u16:14: 0% user + 7.1% kernel
07-20 15:36:58.711  1000  1520  1597 E ActivityManager:   7.1% 9998/kworker/u16:4: 0% user + 7.1% kernel


```

我已经在 log 中标志了相关的含义。`com.xxxx.moblie` 占用了 11% 的 CPU，其实这并不算多。现在的手机基本都是多核 CPU。假如你的 CPU 是 4 核，那么上限是 400%，以此类推。

既然不是 CPU 负载的原因，那么到底是什么原因呢？ 这时就要看我们的终极大杀器——`traces.txt`。

#### 二、 traces.txt 日志分析

当 APP 不响应、响应慢了、或者 WatchDog 的监视没有得到回应时，系统就会 dump 出一个`traces.txt`文件，存放在文件目录:`/data/anr/traces.txt`，通过 traces 文件, 我们可以拿到线程名、堆栈信息、线程当前状态、binder call 等信息。  
通过 adb 命令拿到该文件：`adb pull /data/anr/traces.txt`  
trace: Cmd line:com.xxxx.moblie

```
"main" prio=5 tid=1 Runnable
  | group="main" sCount=0 dsCount=0 obj=0x73bcc7d0 self=0x7f20814c00
  | sysTid=20176 nice=-10 cgrp=default sched=0/0 handle=0x7f251349b0
  | state=R schedstat=( 0 0 0 ) utm=12 stm=3 core=5 HZ=100
  | stack=0x7fdb75e000-0x7fdb760000 stackSize=8MB
  | held mutexes= "mutator lock"(shared held)
  // java 堆栈调用信息,可以查看调用的关系，定位到具体位置
  at ttt.push.InterceptorProxy.addMiuiApplication(InterceptorProxy.java:77)
  at ttt.push.InterceptorProxy.create(InterceptorProxy.java:59)
  at android.app.Activity.onCreate(Activity.java:1041)
  at miui.app.Activity.onCreate(SourceFile:47)
  at com.xxxx.moblie.ui.b.onCreate(SourceFile:172)
  at com.xxxx.moblie.ui.MainActivity.onCreate(SourceFile:68)
  at android.app.Activity.performCreate(Activity.java:7050)
  at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1214)
  at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2807)
  at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2929)
  at android.app.ActivityThread.-wrap11(ActivityThread.java:-1)
  at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1618)
  at android.os.Handler.dispatchMessage(Handler.java:105)
  at android.os.Looper.loop(Looper.java:171)
  at android.app.ActivityThread.main(ActivityThread.java:6699)
  at java.lang.reflect.Method.invoke(Native method)
  at com.android.internal.os.Zygote$MethodAndArgsCaller.run(Zygote.java:246)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:783)


```

我详细解析一下`traces.txt`里面的一些字段，看看它到底能给我们提供什么信息.  
**main**：main 标识是主线程，如果是线程，那么命名成 “Thread-X” 的格式, x 表示线程 id, 逐步递增。  
**prio**：线程优先级, 默认是 5  
**tid**：tid 不是线程的 id，是线程唯一标识 ID  
**group**：是线程组名称  
**sCount**：该线程被挂起的次数  
**dsCount**：是线程被调试器挂起的次数  
**obj**：对象地址  
**self**：该线程 Native 的地址  
**sysTid**：是线程号 (主线程的线程号和进程号相同)  
**nice**：是线程的调度优先级  
**sched**：分别标志了线程的调度策略和优先级  
**cgrp**：调度归属组  
**handle**：线程处理函数的地址。  
**state**：是调度状态  
**schedstat**：从 `/proc/[pid]/task/[tid]/schedstat`读出，三个值分别表示线程在 cpu 上执行的时间、线程的等待时间和线程执行的时间片长度，不支持这项信息的三个值都是 0；  
**utm**：是线程用户态下使用的时间值 (单位是 jiffies）  
**stm**：是内核态下的调度时间值  
**core**：是最后执行这个线程的 cpu 核的序号。  

Java 的堆栈信息是我们最关心的，它能够定位到具体位置。从上面的 traces, 我们可以判断`ttt.push.InterceptorProxy.addMiuiApplicationInterceptorProxy.java:77` 导致了`com.xxxx.moblie`发生了 ANR。这时候可以对着源码查看，找到出问题，并且解决它。  

总结一下这分析流程：首先我们搜索`am_anr`，找到出现 ANR 的时间点、进程 PID、ANR 类型、然后再找搜索`PID`，找前 5 秒左右的日志。过滤 ANR IN 查看 CPU 信息，接着查看`traces.txt`，找到 java 的堆栈信息定位代码位置，最后查看源码，分析与解决问题。这个过程基本能找到发生 ANR 的来龙去脉。

ANR 案例整理
--------

**一、主线程被其他线程 lock，导致死锁**

`waiting on <0x1cd570> (a android.os.MessageQueue)`

```
DALVIK THREADS:
"main" prio=5 tid=3 TIMED_WAIT
  | group="main" sCount=1 dsCount=0 s=0 obj=0x400143a8
  | sysTid=691 nice=0 sched=0/0 handle=-1091117924
  at java.lang.Object.wait(Native Method)
  - waiting on <0x1cd570> (a android.os.MessageQueue)
  at java.lang.Object.wait(Object.java:195)
  at android.os.MessageQueue.next(MessageQueue.java:144)
  at android.os.Looper.loop(Looper.java:110)
  at android.app.ActivityThread.main(ActivityThread.java:3742)
  at java.lang.reflect.Method.invokeNative(Native Method)
  at java.lang.reflect.Method.invoke(Method.java:515)
  at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:739)
  at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:497)
  at dalvik.system.NativeStart.main(Native Method)

"Binder Thread #3" prio=5 tid=15 NATIVE
  | group="main" sCount=1 dsCount=0 s=0 obj=0x434e7758
  | sysTid=734 nice=0 sched=0/0 handle=1733632
  at dalvik.system.NativeStart.run(Native Method)

"Binder Thread #2" prio=5 tid=13 NATIVE
  | group="main" sCount=1 dsCount=0 s=0 obj=0x1cd570
  | sysTid=696 nice=0 sched=0/0 handle=1369840
  at dalvik.system.NativeStart.run(Native Method)

"Binder Thread #1" prio=5 tid=11 NATIVE
  | group="main" sCount=1 dsCount=0 s=0 obj=0x433aca10
  | sysTid=695 nice=0 sched=0/0 handle=1367448
  at dalvik.system.NativeStart.run(Native Method)

----- end 691 -----


```

**二、主线程做耗时的操作：比如数据库读写。**

```
"main" prio=5 tid=1 Native
held mutexes=
kernel: (couldn't read /proc/self/task/11003/stack)
native: #00 pc 000492a4 /system/lib/libc.so (nanosleep+12)
native: #01 pc 0002dc21 /system/lib/libc.so (usleep+52)
native: #02 pc 00009cab /system/lib/libsqlite.so (???)
native: #03 pc 00011119 /system/lib/libsqlite.so (???)
native: #04 pc 00016455 /system/lib/libsqlite.so (???)
native: #16 pc 0000fa29 /system/lib/libsqlite.so (???)
native: #17 pc 0000fad7 /system/lib/libsqlite.so (sqlite3_prepare16_v2+14)
native: #18 pc 0007f671 /system/lib/libandroid_runtime.so (???)
native: #19 pc 002b4721 /system/framework/arm/boot-framework.oat (Java_android_database_sqlite_SQLiteConnection_nativePrepareStatement__JLjava_lang_String_2+116)
at android.database.sqlite.SQLiteConnection.setWalModeFromConfiguration(SQLiteConnection.java:294)
at android.database.sqlite.SQLiteConnection.open(SQLiteConnection.java:215)
at android.database.sqlite.SQLiteConnection.open(SQLiteConnection.java:193)
at android.database.sqlite.SQLiteConnectionPool.openConnectionLocked(SQLiteConnectionPool.java:463)
at android.database.sqlite.SQLiteConnectionPool.open(SQLiteConnectionPool.java:185)
at android.database.sqlite.SQLiteConnectionPool.open(SQLiteConnectionPool.java:177)
at android.database.sqlite.SQLiteDatabase.openInner(SQLiteDatabase.java:808)
locked <0x0db193bf> (a java.lang.Object)
at android.database.sqlite.SQLiteDatabase.open(SQLiteDatabase.java:793)
at android.database.sqlite.SQLiteDatabase.openDatabase(SQLiteDatabase.java:696)
at android.app.ContextImpl.openOrCreateDatabase(ContextImpl.java:690)
at android.content.ContextWrapper.openOrCreateDatabase(ContextWrapper.java:299)
at android.database.sqlite.SQLiteOpenHelper.getDatabaseLocked(SQLiteOpenHelper.java:223)
at android.database.sqlite.SQLiteOpenHelper.getWritableDatabase(SQLiteOpenHelper.java:163)
locked <0x045a4a8c> (a com.xxxx.video.common.data.DataBaseHelper)
at com.xxxx.video.common.data.DataBaseORM.<init>(DataBaseORM.java:46)
at com.xxxx.video.common.data.DataBaseORM.getInstance(DataBaseORM.java:53)
locked <0x017095d5> (a java.lang.Class<com.xxxx.video.common.data.DataBaseORM>)


```

**三、binder 数据量过大**

```
07-21 04:43:21.573  1000  1488 12756 E Binder  : Unreasonably large binder reply buffer: on android.content.pm.BaseParceledListSlice$1@770c74f calling 1 size 388568 (data: 1, 32, 7274595)
07-21 04:43:21.573  1000  1488 12756 E Binder  : android.util.Log$TerribleFailure: Unreasonably large binder reply buffer: on android.content.pm.BaseParceledListSlice$1@770c74f calling 1 size 388568 (data: 1, 32, 7274595)
07-21 04:43:21.607  1000  1488  2951 E Binder  : Unreasonably large binder reply buffer: on android.content.pm.BaseParceledListSlice$1@770c74f calling 1 size 211848 (data: 1, 23, 7274595)
07-21 04:43:21.607  1000  1488  2951 E Binder  : android.util.Log$TerribleFailure: Unreasonably large binder reply buffer: on android.content.pm.BaseParceledListSlice$1@770c74f calling 1 size 211848 (data: 1, 23, 7274595)
07-21 04:43:21.662  1000  1488  6258 E Binder  : Unreasonably large binder reply buffer: on android.content.pm.BaseParceledListSlice$1@770c74f calling 1 size 259300 (data: 1, 33, 7274595)


```

**四、binder 通信失败**

```
07-21 06:04:35.580 <6>[32837.690321] binder: 1698:2362 transaction failed 29189/-3, size 100-0 line 3042
07-21 06:04:35.594 <6>[32837.704042] binder: 1765:4071 transaction failed 29189/-3, size 76-0 line 3042
07-21 06:04:35.899 <6>[32838.009132] binder: 1765:4067 transaction failed 29189/-3, size 224-8 line 3042
07-21 06:04:36.018 <6>[32838.128903] binder: 1765:2397 transaction failed 29189/-22, size 348-0 line 2916


```

推荐
--

[最新源码汇总](https://link.juejin.cn?target=http%3A%2F%2Fwww.androidblog.cn%2Findex.php%2FSource%2Findex%2Fp%2F1 "http://www.androidblog.cn/index.php/Source/index/p/1"): 每周分享新的开源代码，有效果图，更直观。