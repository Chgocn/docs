> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/lixuejian/p/16665410.html)

Android BugReport 中包含文本 (.txt) 格式的 dumpsys（转储系统）、dumpstate（转储状态）、Logcat 数据，便于轻松搜索特定内容。以下各部分详细说明了 BugReport 的组成部分及常见问题，并提供了关于查找与这些错误相关的日志的实用提示和 grep 命令。

一、获取 BugReport
--------------

连接测试手机，进行相应测试后，打开终端，执行：$ adb shell bugreport > bugreport.txt

二、如何阅读 BugReport
----------------

**1、Logcat**  
Logcat 日志是所有 Logcat 信息采用字符串形式的转储。其中，包括：System Log、Event Log 等。

（1）System Log 部分专门用于记录框架方面的信息，与包含所有其他内容的 main 部分相比，该部分包含更长时间内的记录。  
每行都以 `timestamp PID TID log-level` 开头。  
（2）Event Log 中包含将二进制格式转换成了字符串形式的日志消息。它比 Logcat 日志要清晰明了，但也有些难以阅读。在查看 Event Log 时，可以在这一部分中搜索特定进程 ID (PID)，以查看相应进程一直在做什么。基本格式是：`timestamp PID TID log-level log-tag tag-values`。

注意：日志级别包括以下几种：  
<1> V：详细  
<2> D：调试  
<3> I：信息  
<4> W：警告  
<5> E：错误

**2、ANR（应用无响应）**  
BugReport 有助于找出导致 ANR 错误和死锁事件的原因。当某个应用在一定时间内没有响应（通常是由于主线程被阻塞或繁忙）时，系统会终止该进程，并将堆栈转储到 /data/anr。

（1）为了找出 ANR 背后的罪魁祸首，可以对二进制事件日志中的 am_anr 执行 grep 命令。

```
$ grep "am_anr" bugreport.txt
10-01 18:12:49.599  4600  4614 I am_anr : [0,29761,com.google.android.***,953695941,executing service com.google.android.***/com.google.android.apps.***.app.offline.transfer.OfflineTransferService] 10-01 18:14:10.211  4600  4614 I am_anr : [0,30363,com.google.android.apps.plus,953728580,executing service com.google.android.apps.plus/com.google.android.apps.photos.service.PhotosService] 


``` 

（2）也可以对 Logcat 日志（包含关于发生 ANR 时，是什么在占用 CPU 的更多信息）中的 ANR in 执行 grep 命令。

```
$ grep "ANR in" bugreport.txt
10-01 18:13:11.984  4600  4614 E ActivityManager: ANR in com.google.android.***
10-01 18:14:31.720  4600  4614 E ActivityManager: ANR in com.google.android.apps.plus
10-01 18:14:31.720  4600  4614 E ActivityManager: PID: 30363
10-01 18:14:31.720  4600  4614 E ActivityManager: Reason: executing service com.google.android.apps.plus/com.google.android.apps.photos.service.PhotosService
10-01 18:14:31.720  4600  4614 E ActivityManager: Load: 35.27 / 23.9 / 16.18
10-01 18:14:31.720  4600  4614 E ActivityManager: CPU usage from 16ms to 21868ms later:
10-01 18:14:31.720  4600  4614 E ActivityManager:  74% 3361/mm-qcamera-daemon: 62% user + 12% kernel / faults: 15276 minor 10 major
10-01 18:14:31.720  4600  4614 E ActivityManager:  41% 4600/system_server: 18% user + 23% kernel / faults: 18597 minor 309 major
10-01 18:14:31.720  4600  4614 E ActivityManager:  32% 27420/com.google.android.GoogleCamera: 24% user + 7.8% kernel / faults: 48374 minor 338 major
10-01 18:14:31.720  4600  4614 E ActivityManager:  16% 130/kswapd0: 0% user + 16% kernel
10-01 18:14:31.720  4600  4614 E ActivityManager:  15% 283/mmcqd/0: 0% user + 15% kernel ... 10-01 18:14:31.720  4600  4614 E ActivityManager:  0.1% 27248/irq/503-synapti: 0% 10-01 18:14:31.721  4600  4614 I ActivityManager: Killing 30363:com.google.android.apps.plus/u0a206 (adj 0): bg anr 


``` 

（3）查找堆栈轨迹  
通常可以找到与 ANR 对应的堆栈轨迹。首先确保虚拟机轨迹上的 timeStamp 和 PID 与您正在调查的 ANR 相符，然后再检查进程的主线程。注意：  
<1> 主线程只能了解发生 ANR 时，它在做什么，这可能是导致 ANR 的真正原因，也可能不是。（BugReport 中的堆栈可能是无辜的，可能有其它线程在恢复正常之前，粘滞了很长时间，但不足以导致 ANR。）  
<2> 可能存在多组堆栈轨迹（`VM TRACES JUST NOW` 和 `VM TRACES AT LAST ANR`），要确保查看的是正确的部分。

（4）查找死锁  
由于线程出现粘滞，死锁往往首先表现为 ANR。如果系统服务器发生死锁，监控定时器最终会将其终止，从而导致日志中出现类似以下的条目：`WATCHDOG KILLING SYSTEM PROCESS`。对于用户来说，他们看到的是设备重新启动，但从技术上来讲，这是运行时重启，而不是真正的设备重新启动。说明：  
<1> 在运行时重启时，系统服务器已死机并会重启，并且用户会看到设备返回到显示启动动画。  
<2> 在设备重新启动时，内核已崩溃，并且用户会看到设备返回到显示 Google 启动徽标。  
要查找死锁，请检查 VM 跟踪部分中是否存在以下模式：线程 A 在等待线程 B 占用的某些资源，而线程 B 也在等待线程 A 占用的某些资源。

**3、Activity**  
Activity 是一种应用组件，为用户提供一个可互动的屏幕，用来执行某些操作（如：拨号、拍照、发送电子邮件等）。从 BugReport 的角度来看，Activity 是用户可以执行的一项明确具体的操作，这使得查找在崩溃期间处于聚焦状态的 Activity 变得非常重要。Activity 通过 ActivityManager 运行进程，因此找出指定 Activity 的所有进程启动和停止事件也有助于进行问题排查。

（1）查看处于聚焦状态的 Activity 的历史记录：

```
$ grep "am_focused_activity" bugreport.txt
10-01 18:10:41.409  4600 14112 I am_focused_activity: [0,com.google.android.GoogleCamera/com.android.camera.CameraActivity] 10-01 18:11:17.313  4600  5687 I am_focused_activity: [0,com.google.android.googlequicksearchbox/com.google.android.launcher.GEL] 


``` 

（2）查看进程启动事件的历史记录

```
$ grep "Start proc" bugreport.txt
10-01 18:09:15.309  4600  4612 I ActivityManager: Start proc 24533:com.metago.astro/u0a240 for broadcast com.metago.astro/com.inmobi.commons.analytics.androidsdk.IMAdTrackerReceiver
10-01 18:09:15.687  4600 14112 I ActivityManager: Start proc 24548:com.google.android.apps.fitness/u0a173 for service com.google.android.apps.fitness/.api.services.ActivityUpsamplingService
10-01 18:09:15.777  4600  6604 I ActivityManager: Start proc 24563:cloudtv.hdwidgets/u0a145 for broadcast cloudtv.hdwidgets/cloudtv.switches.SwitchSystemUpdateReceiver
10-01 18:09:20.574  4600  6604 I ActivityManager: Start proc 24617:com.wageworks.ezreceipts/u0a111 for broadcast com.wageworks.ezreceipts/.ui.managers.IntentReceiver ... 


``` 

（3）确定设备是否发生抖动，需检查在 `am_proc_died` 和 `am_proc_start` 前后的短时间内，是否出现 Activity 异常增多的情况。

```
$ grep -e "am_proc_died" -e "am_proc_start" bugreport.txt
10-01 18:07:06.494  4600  9696 I am_proc_died: [0,20074,com.android.musicfx] 10-01 18:07:06.555  4600  6606 I am_proc_died: [0,31166,com.concur.breeze] 10-01 18:07:06.566  4600 14112 I am_proc_died: [0,18812,com.google.android.apps.fitness] 10-01 18:07:07.018  4600  7513 I am_proc_start: [0,20361,10113,com.sony.playmemories.mobile,broadcast,com.sony.playmemories.mobile/.service.StartupReceiver] 10-01 18:07:07.357  4600  4614 I am_proc_start: [0,20381,10056,com.google.android.talk,service,com.google.android.talk/com.google.android.libraries.hangouts.video.CallService] 10-01 18:07:07.784  4600  4612 I am_proc_start: [0,20402,10190,com.andcreate.app.trafficmonitor:loopback_measure_serivce,service,com.andcreate.app.trafficmonitor/.loopback.LoopbackMeasureService] 10-01 18:07:10.753  4600  5997 I am_proc_start: [0,20450,10097,com.amazon.mShop.android.shopping,broadcast,com.amazon.mShop.android.shopping/com.amazon.identity.auth.device.storage.LambortishClock$ChangeTimestampsBroadcastReceiver] 10-01 18:07:15.267  4600  6605 I am_proc_start: [0,20539,10173,com.google.android.apps.fitness,service,com.google.android.apps.fitness/.api.services.ActivityUpsamplingService] 10-01 18:07:15.985  4600  4612 I am_proc_start: [0,20568,10022,com.android.musicfx,broadcast,com.android.musicfx/.ControlPanelReceiver] 10-01 18:07:16.315  4600  7512 I am_proc_died: [0,20096,com.google.android.GoogleCamera] 


``` 

**4、内存**  
由于 Android 设备的物理内存通常都存在限制，因此管理 RAM (随机存取存储器) 至关重要。BugReport 中包含一些用于指示内存不足的指标以及一个提供内存快照的 dumpstate。

（1）发现内存不足的情况  
内存不足可能会导致系统发生抖动，这是因为虽然内存不足时，系统会终止某些进程来释放内存，但又会继续启动其它进程。如需查看内存不足的确凿证据，请检查二进制事件日志中的 `am_proc_died` 和 `am_proc_start` 条目的密集程度。  
内存不足还可能会减慢任务切换速度，并且可能会阻止进行返回的尝试（因为用户尝试返回的任务已被终止）。如果启动器被终止，它会在用户触摸主屏幕按钮时重启，并且日志中会显示启动器重新加载其内容。  
<1> 查看历史指标  
二进制事件日志中的 `am_low_memory` 条目，表示最后一个缓存的进程已终止。在此之后，系统开始终止各项服务。

```
$ grep "am_low_memory" bugreport.txt
10-01 18:11:02.219  4600  7513 I am_low_memory: 41
10-01 18:12:18.526  4600 14112 I am_low_memory: 39
10-01 18:12:18.874  4600  7514 I am_low_memory: 38
10-01 18:12:22.570  4600 14112 I am_low_memory: 40
10-01 18:12:34.811  4600 20319 I am_low_memory: 43
10-01 18:12:37.945  4600  6521 I am_low_memory: 43
10-01 18:12:47.804  4600 14110 I am_low_memory: 43 


``` 

<2> 查看抖动指标  
关于系统抖动（分页、直接回收等）的其它指标，包括：kswapd、kworker、mmcqd 消耗周期。（ 注意：收集 BugReport 可能会影响抖动指标 ）  
ANR 日志可以提供类似的内存快照。

（2）获取内存快照  
内存快照是一种 dumpstate，其中会列出正在运行的 Java 进程和本机进程。（ 注意：快照仅提供特定时刻的状态，在此快照之前，系统的状况可能更好，也可能更糟。）  
**5、广播**  
应用会生成广播，以便在当前应用内或向其它应用发送事件。广播接收器可以（通过过滤器）订阅特定消息，以便收听和响应广播。BugReport 中包含已发送广播和未发送广播的相关信息，以及关于收听特定广播的所有接收器的 dumpsys。

（1）查看历史广播  
历史广播（即：已发送的广播），按时间逆序排列。  
<1> summary（摘要）部分，用于提供最近 300 个前台广播和最近 300 个后台广播的概况。

```
Historical broadcasts summary [foreground]: #0: act=android.intent.action.SCREEN_ON flg=0x50000010 +1ms dispatch +90ms finish
    enq=2015-10-29 17:10:51 disp=2015-10-29 17:10:51 fin=2015-10-29 17:10:51 #1: act=android.intent.action.SCREEN_OFF flg=0x50000010 0 dispatch +60ms finish
    enq=2015-10-29 17:10:05 disp=2015-10-29 17:10:05 fin=2015-10-29 17:10:05 ... Historical broadcasts summary [background]: ... 


``` 

<2> detail（详情）部分，包含最近 50 条前台广播和最近 50 条后台广播的完整信息，以及每个广播的接收方。其中：  
具有 BroadcastFilter 条目的接收器是在运行时注册的，并且只会被发送到已在运行的进程。  
具有 ResolveInfo 条目的接收器是通过清单条目注册的。ActivityManager 会为每个 ResolveInfo 启动相应进程（如果相应进程尚未在运行）。

（2）查看待发送的广播  
待发送的广播（即：尚未发送的广播），如果队列中存在大量广播，则意味着系统无法足够快地发送广播来跟上进度。

```
Active ordered broadcasts [background]: Active Ordered Broadcast background #133: // size of queue ... 


``` 

（3）查看广播监听器  
如需查看监听广播的接收器列表，请查看 dumpsys activity broadcasts 中的 Receiver Resolver Table。以下示例显示了监听 USER_PRESENT 的所有接收器。

```
ACTIVITY MANAGER BROADCAST STATE (dumpsys activity broadcasts) .. Receiver Resolver Table: Full MIME Types: .. Wild MIME Types: .. Schemes: .. Non-Data Actions: .. android.intent.action.USER_PRESENT: BroadcastFilter{8181cc1 u-1 ReceiverList{5d929a8 902 system/1000/u-1 local:eca4dcb}} BroadcastFilter{6371c97 u-1 ReceiverList{2938b16 902 system/1000/u-1 local:840b831}} BroadcastFilter{320c00 u0 ReceiverList{d3a6283 902 system/1000/u0 local:799c532}} BroadcastFilter{e486048 u0 ReceiverList{36fbaeb 902 system/1000/u0 local:5f51e3a}} BroadcastFilter{22b02 u-1 ReceiverList{b3f744d 902 system/1000/u-1 local:de837e4}} BroadcastFilter{3e989ab u0 ReceiverList{f8deffa 2981 com.google.process.gapps/10012/u0 remote:26bd225}} BroadcastFilter{fb56150 u0 ReceiverList{22b7b13 2925 com.android.systemui/10027/u0 remote:c54a602}} BroadcastFilter{63bbb6 u-1 ReceiverList{ba6c751 3484 com.android.nfc/1027/u-1 remote:5c4a478}} BroadcastFilter{95ad20d u0 ReceiverList{d8374a4 3586 com.google.android.googlequicksearchbox/10029/u0 remote:feb3737}} BroadcastFilter{fdef551 u0 ReceiverList{28ca78 3745 com.google.android.gms.persistent/10012/u0 remote:f23afdb}} BroadcastFilter{9830707 u0 ReceiverList{aabd946 3745 com.google.android.gms.persistent/10012/u0 remote:a4da121}} BroadcastFilter{83c43d2 u0 ReceiverList{d422e5d 3745 com.google.android.gms.persistent/10012/u0 remote:f585034}} BroadcastFilter{8890378 u0 ReceiverList{26d2cdb 3745 com.google.android.gms.persistent/10012/u0 remote:dfa61ea}} BroadcastFilter{7bbb7 u0 ReceiverList{214b2b6 3745 com.google.android.gms.persistent/10012/u0 remote:8353a51}} BroadcastFilter{38d3566 u0 ReceiverList{de859c1 3745 com.google.android.gms.persistent/10012/u0 remote:e003aa8}} BroadcastFilter{3435d9f u0 ReceiverList{6e38b3e 3745 com.google.android.gms.persistent/10012/u0 remote:8dd7ff9}} BroadcastFilter{d0a34bb u0 ReceiverList{5091d4a 3745 com.google.android.gms.persistent/10012/u0 remote:d6d22b5}} BroadcastFilter{d43c416 u0 ReceiverList{51a3531 3745 com.google.android.gms.persistent/10012/u0 remote:d0b9dd8}} BroadcastFilter{aabf36d u0 ReceiverList{a88bf84 3745 com.google.android.gms.persistent/10012/u0 remote:a9d6197}} BroadcastFilter{93f16b u0 ReceiverList{5c61eba 17016 com.google.android.gm/10079/u0 remote:24083e5}} BroadcastFilter{68f794e u0 ReceiverList{4cb1c49 947 com.google.android.googlequicksearchbox:search/10029/u0 remote:251d250}} .. MIME Typed Actions: 


``` 

**6、监视器争用**  
监视器争用日志记录，有时可以表明实际的监视器争用情况，但通常情况下表明系统负载过重，从而导致所有进程都变慢了。可能会在 System Log 或 Event Log 中看到 ART（Android Runtime）记录的长时间占用监视器的事件。

（1）在 System Log 中：

```
10-01 18:12:44.343 29761 29914 W art : Long monitor contention event with owner method=void android.database.sqlite.SQLiteClosable.acquireReference() from SQLiteClosable.java:52 waiters=0 for 3.914s 


``` 

（2）在 Event Log 中：

```
10-01 18:12:44.364 29761 29914 I dvm_lock_sample : [com.google.android.***,0,pool-3-thread-9,3914,ScheduledTaskMaster.java,138,SQLiteClosable.java,52,100] 


``` 

**7、后台编译**  
编译可能会占用大量资源，而且会加重设备负载。

```
09-14 06:27:05.670 2508 2587 E ActivityManager : CPU usage from 0ms to 5857ms later: 09-14 06:27:05.670 2508 2587 E ActivityManager : 84% 5708/dex2oat: 81% user + 2.3% kernel / faults: 3731 minor 1 major 09-14 06:27:05.670 2508 2587 E ActivityManager : 73% 2508/system_server: 21% user + 51% kernel / faults: 10019 minor 28 major 09-14 06:27:05.670 2508 2587 E ActivityManager : 1% 3935/com.android.phone: 0.3% user + 0.6% kernel / faults: 2684 minor 2 major 


``` 

下载 Google Play 商店更新时，编译可能会在后台进行。在这种情况下，来自 Google Play 商店应用的 finsky 和 installd 的消息会显示在 dex2oat 消息之前。

```
10-07 08:42:33.725 11051 11051 D Finsky : [1] InstallerTask.advanceState: Prepare to patch com.garmin.android.apps.virb (com.garmin.android.apps.virb) from content://downloads/my_downloads/3602 format 2 10-07 08:42:33.752 495 495 I installd : free_cache(48637657) avail 15111192576 … 10-07 08:42:39.998 2497 2567 I PackageManager.DexOptimizer : Running dexopt (dex2oat) on: /data/app/vmdl436577137.tmp/base.apk pkg=com.garmin.android.apps.virb isa=arm vmSafeMode=false debuggable=false oatDir = /data/app/vmdl436577137.tmp/oat bootComplete=true … 


``` 

当某个应用加载尚未编译的 dex 文件时，编译也可能会在后台进行。在这种情况下，将看不到 finsky 或 installd 日志记录。

```
09-14 07:29:20.433 15736 15736 I dex2oat : /system/bin/dex2oat -j4 --dex-file=/data/user/0/com.facebook.katana/app_secondary_program_dex/program-72cef82b591768306676e10161c886b58b34315a308602be.dex.jar --oat-file=/data/user/0/com.facebook.katana/app_secondary_program_dex_opt/program-72cef82b591768306676e10161c886b58b34315a308602be.dex.dex ... 09-14 07:29:25.102 15736 15736 I dex2oat : dex2oat took 4.669s (threads: 4) arena alloc=7MB java alloc=3MB native alloc=29MB free=4MB 


``` 

**8、问题概况**  
要呈现问题概况（例如：问题是如何开始的、发生了什么、系统是如何应对的），需要一个固定的事件时间轴。可以利用 BugReport 中的信息，来同步多个日志中的时间轴，并确定 BugReport 的确切时间戳。

（1）同步时间轴  
BugReport 反映的是多个并行时间轴（系统日志、事件日志、内核日志，以及针对广播、电池统计信息等的多个专用时间轴）。系统通常会使用不同的时间基准来报告不同的时间轴。  
System Log 时间戳和 Event Log 时间戳，采用用户所用的时区（与大多数其它时间戳一样），  
例如：当用户点按主屏幕按钮时，System Log 会报告以下内容：

```
10-03 17:19:52.939 1963 2071 I ActivityManager : START u0 {act=android.intent.action.MAIN cat=[android.intent.category.HOME] flg=0x10200000 cmp=com.google.android.googlequicksearchbox/com.google.android.launcher.GEL (has extras)} from uid 1000 on display 0 


``` 

Event Log 会报告以下内容：

```
10-03 17:19:54.279 1963 2071 I am_focused_activity : [0,com.google.android.googlequicksearchbox/com.google.android.launcher.GEL] 


``` 

内核 (dmesg) 日志采用不同的时间基准，按距离引导加载程序完成的时间（以秒为单位）来标记日志内容。如需按照其它时间表的时间基准记录此时间表，可以搜索 “suspend exit” 和 “suspend entry” 消息：

```
<6>[201640.779997] PM: suspend exit 2015-10-03 19:11:06.646094058 UTC
… <6>[201644.854315] PM: suspend entry 2015-10-03 19:11:10.720416452 UTC 


``` 

由于内核日志可能不包含处于暂停状态的时间，因此，应该分段记录暂停进入和暂停退出消息之间的日志。此外，内核日志使用 UTC 时区，必须将其调整为用户时区。

（2）确定 BugReport 的生成时间  
<1> 可以先查看系统日志 (Logcat) 中的 “dumpstate: begin”：

```
10-03 17:19:54.322 19398 19398 I dumpstate: begin 


``` 

<2> 然后查看内核日志 (dmesg) 时间戳中的 Starting service ‘bugreport’ 消息：

```
<5>[207064.285315] init: Starting service 'bugreport'... 


``` 

进行逆向推算以关联这两个事件，同时牢记同步时间轴中提到的注意事项。尽管在初始化 BugReport 后发生了很多活动，但大多数活动并不是非常有用，因此生成 BugReport 这一活动会大大加重系统负载。

**9、电源**  
Event Log 中包含屏幕电源状态信息，其中： 0 表示屏幕关闭，1 表示屏幕打开，2 表示已锁屏。

```
$ grep "screen_toggled" bugreport.txt 10-18 15:05:04.383 992 992 I screen_toggled: 1 10-18 15:05:07.010 992 992 I screen_toggled: 0 10-18 15:23:15.063 992 992 I screen_toggled: 1 10-18 15:23:25.684 992 992 I screen_toggled: 0 10-18 15:36:31.623 992 992 I screen_toggled: 1 10-18 15:36:37.660 3283 3283 I screen_toggled: 2 


``` 

BugReport 中还包含关于唤醒锁定的统计信息，唤醒锁定是应用开发者采用的一种机制，用于表明其应用需要设备保持开启状态。  
唤醒锁定总时长统计信息，仅跟踪唤醒锁定实际负责使设备保持唤醒状态的时间，不包括屏幕处于开启状态的时间。此外，如果同时持有多个唤醒锁定，系统会在它们之间分配唤醒锁定时长。

注：若需直观呈现电源状态方面的更多信息，可以使用 Battery Historian（ 一种 Google 开源工具，能够利用 Android BugReport 文件分析电池消耗进程 ）。

**10、软件包**  
“DUMP OF SERVICE” 软件包中，包含应用版本信息，以及其它实用信息。

```
... Packages: ... Package [com.google.android.gms] (3cf534b): userId=10013 sharedUser=SharedUserSetting{98f3d28 com.google.uid.shared/10013} pkg=Package{b8f6a41 com.google.android.gms} codePath=/system/priv-app/PrebuiltGmsCore
   resourcePath=/system/priv-app/PrebuiltGmsCore
   legacyNativeLibraryDir=/system/priv-app/PrebuiltGmsCore/lib
   primaryCpuAbi=arm64-v8a
   secondaryCpuAbi=armeabi-v7a
   versionCode=8186448 targetSdk=23 versionName=8.1.86 (2287566-448) splits=[base] applicationInfo=ApplicationInfo{5158507 com.google.android.gms} flags=[ SYSTEM HAS_CODE ALLOW_CLEAR_USER_DATA ] privateFlags=[ PRIVILEGED ] dataDir=/data/user/0/com.google.android.gms
   supportsScreens=[small, medium, large, xlarge, resizeable, anyDensity] libraries: com.google.android.gms
   usesOptionalLibraries: com.android.location.provider
     com.google.android.ble
     com.android.media.remotedisplay
   usesLibraryFiles: /system/framework/com.android.media.remotedisplay.jar /system/framework/com.android.location.provider.jar
   timeStamp=2015-10-14 15:17:56 firstInstallTime=2015-09-22 14:08:35 lastUpdateTime=2015-10-14 15:17:56 signatures=PackageSignatures{db63be6 [1af63d8]} installPermissionsFixed=true installStatus=1 pkgFlags=[ SYSTEM HAS_CODE ALLOW_CLEAR_USER_DATA ] declared permissions: com.google.android.gms.permission.INTERNAL_BROADCAST: prot=signature, INSTALLED ... com.google.android.gms.permission.CAR_VENDOR_EXTENSION: prot=dangerous, INSTALLED
   User 0: installed=true hidden=false stopped=false notLaunched=false enabled=0 disabledComponents: com.google.android.gms.icing.service.PowerConnectedReceiver ... com.google.android.gms.icing.proxy.AppsMonitor
     enabledComponents: com.google.android.gms.mdm.receivers.GmsRegisteredReceiver ... com.google.android.gms.subscribedfeeds.SyncService 


``` 

**11、进程**  
BugReport 中含有大量进程数据，包括：启动和停止时间、运行时时长、相关服务、oom_adj 得分等。

（1）确定进程运行时  
procstats 部分包含有关进程及相关服务，已运行时长的完整统计信息。  
若想快速获得便于用户阅读的摘要，可搜索：“AGGREGATED OVER”，以查看最近 3 个小时或 24 个小时的数据，然后搜索：“Summary:”，以查看进程列表、这些进程已以各种优先级运行的时长，以及它们使用 RAM 的情况（格式为 “最小 - 平均 - 最大 PSS”/“最小 - 平均 - 最大 USS”）。

```
------------------------------------------------------------------------------- DUMP OF SERVICE processinfo: ------------------------------------------------------------------------------- DUMP OF SERVICE procstats: COMMITTED STATS FROM 2015-10-19-23-54-56 (checked in): ... COMMITTED STATS FROM 2015-10-20-03-00-00 (checked in): ... CURRENT STATS: ... AGGREGATED OVER LAST 24 HOURS: System memory usage: ... Per-Package Stats: ... Summary: ... * com.google.android.gms.persistent / u0a13 / v8186448: TOTAL: 100% (21MB-27MB-40MB/20MB-24MB-38MB over 597) Top: 51% (22MB-26MB-38MB/21MB-24MB-36MB over 383) Imp Fg: 49% (21MB-27MB-40MB/20MB-25MB-38MB over 214) …
          Start time: 2015-10-19 09:14:37 Total elapsed time: +1d0h22m7s390ms (partial) libart.so

AGGREGATED OVER LAST 3 HOURS: System memory usage: ... Per-Package Stats: ... Summary: * com.google.android.gms.persistent / u0a13 / v8186448: TOTAL: 100% (23MB-27MB-32MB/21MB-25MB-29MB over 111) Top: 61% (23MB-26MB-31MB/21MB-24MB-28MB over 67) Imp Fg: 39% (23MB-28MB-32MB/21MB-26MB-29MB over 44) ... Start time: 2015-10-20 06:49:24 Total elapsed time: +2h46m59s736ms (partial) libart.so 


``` 

（2）某个进程为什么正在运行  
dumpsys activity processes 部分会列出当前正在运行的所有进程，并按 oom_adj 得分排序（Android 通过为进程分配 oom_adj 值，来表明进程的重要性，该值可由 ActivityManager 动态更新）。这种输出类似于内存快照的输出，但包含有关是什么导致进程运行的更多信息。在以下示例中，以粗体显示的条目表明 gms.persistent 进程正在以 vis（可见）优先级运行，因为该系统进程已经与其 NetworkLocationService 绑定。

```
------------------------------------------------------------------------------- ACTIVITY MANAGER RUNNING PROCESSES (dumpsys activity processes) ... Process LRU list (sorted by oom_adj, 34 total, non-act at 14, non-svc at 14): PERS #33: sys   F/ /P  trm: 0 902:system/1000 (fixed) PERS #32: pers  F/ /P  trm: 0 2925:com.android.systemui/u0a27 (fixed) PERS #31: pers  F/ /P  trm: 0 3477:com.quicinc.cne.CNEService/1000 (fixed) PERS #30: pers  F/ /P  trm: 0 3484:com.android.nfc/1027 (fixed) PERS #29: pers  F/ /P  trm: 0 3502:com.qualcomm.qti.rcsbootstraputil/1001 (fixed) PERS #28: pers  F/ /P  trm: 0 3534:com.qualcomm.qti.rcsimsbootstraputil/1001 (fixed) PERS #27: pers  F/ /P  trm: 0 3553:com.android.phone/1001 (fixed) Proc #25: psvc  F/ /IF trm: 0 4951:com.android.bluetooth/1002 (service) com.android.bluetooth/.hfp.HeadsetService<=Proc{902:system/1000} Proc # 0: fore  F/A/T  trm: 0 3586:com.google.android.googlequicksearchbox/u0a29 (top-activity) Proc #26: vis   F/ /SB trm: 0 3374:com.google.android.googlequicksearchbox:interactor/u0a29 (service) com.google.android.googlequicksearchbox/com.google.android.voiceinteraction.GsaVoiceInteractionService<=Proc{902:system/1000} Proc # 5: vis   F/ /T  trm: 0 3745:com.google.android.gms.persistent/u0a12 (service) com.google.android.gms/com.google.android.location.network.NetworkLocationService<=Proc{902:system/1000} Proc # 3: vis   F/ /SB trm: 0 3279:com.google.android.gms/u0a12 (service) com.google.android.gms/.icing.service.IndexService<=Proc{947:com.google.android.googlequicksearchbox:search/u0a29} Proc # 2: vis   F/ /T  trm: 0 947:com.google.android.googlequicksearchbox:search/u0a29 (service) com.google.android.googlequicksearchbox/com.google.android.sidekick.main.remoteservice.GoogleNowRemoteService<=Proc{3586:com.google.android.googlequicksearchbox/u0a29} Proc # 1: vis   F/ /T  trm: 0 2981:com.google.process.gapps/u0a12 (service) com.google.android.gms/.tapandpay.hce.service.TpHceService<=Proc{3484:com.android.nfc/1027} Proc #11: prcp  B/ /IB trm: 0 3392:com.google.android.inputmethod.latin/u0a64 (service) com.google.android.inputmethod.latin/com.android.inputmethod.latin.LatinIME<=Proc{902:system/1000} Proc #24: svc   B/ /S  trm: 0 27071:com.google.android.music:main/u0a67 (started-services) Proc #22: svc   B/ /S  trm: 0 853:com.qualcomm.qcrilmsgtunnel/1001 (started-services) Proc # 4: prev  B/ /LA trm: 0 32734:com.google.android.GoogleCamera/u0a53 (previous) Proc #23: svcb  B/ /S  trm: 0 671:com.qualcomm.telephony/1000 (started-services) Proc #20: cch   B/ /CE trm: 0 27659:com.android.providers.calendar/u0a2 (provider) com.android.providers.calendar/.CalendarProvider2<=Proc{27697:com.google.android.calendar/u0a40} Proc #13: cch   B/ /CE trm: 0 653:com.google.android.gms.wearable/u0a12 (cch-empty) Proc #10: cch   B/ /S  trm: 0 4067:com.google.android.talk/u0a62 (cch-started-ui-services) Proc # 7: cch   B/ /S  trm: 0 18868:com.google.corp.huddle.android/u0a95 (cch-started-ui-services) Proc # 6: cch   B/ /CA trm: 0 27697:com.google.android.calendar/u0a40 (cch-act) Proc # 8: cch+1 B/ /CA trm: 0 25675:com.google.android.apps.genie.geniewidget/u0a81 (cch-act) Proc #16: cch+2 B/ /CE trm: 0 1272:com.google.android.keep/u0a106 (cch-empty) Proc #15: cch+2 B/ /CE trm: 0 885:android.process.media/u0a9 (cch-empty) Proc #14: cch+2 B/ /CE trm: 0 15146:android.process.acore/u0a3 (cch-empty) Proc # 9: cch+3 B/ /CA trm: 0 17016:com.google.android.gm/u0a79 (cch-act) Proc #19: cch+4 B/ /CE trm: 0 973:com.google.android.apps.maps/u0a66 (cch-empty) Proc #18: cch+4 B/ /CE trm: 0 1091:com.google.android.apps.photos/u0a71 (cch-empty) Proc #17: cch+4 B/ /CE trm: 0 1141:com.google.android.apps.plus/u0a74 (cch-empty) Proc #12: cch+5 B/ /CA trm: 0 22299:com.google.android.apps.dogfood/u0a105 (cch-act) Proc #21: cch+6 B/ /CE trm: 0 995:com.google.android.partnersetup/u0a18 (cch-empty)> 


``` 

**12、扫描**  
可以按照以下步骤来确定过度执行 BLE（蓝牙低功耗）扫描的应用：

（1）查找 BluetoothLeScanner 的日志消息：

```
$ grep 'BluetoothLeScanner' bugreport.txt 07-28 15:55:19.090 24840 24851 D BluetoothLeScanner: onClientRegistered() - status=0 clientIf=5 


``` 

（2）在日志消息中找到 PID。在此示例中，“24840” 和 “24851” 分别为 PID 和 TID 。  
（3）找到与该 PID 关联的应用：

```
PID #24840: ProcessRecord{4fe996a 24840:com.badapp/u0a105} 


``` 

在此示例中，可以看出软件包名称为：com.badapp。  
（4）在 Google Play 上查找该程序包名称，以找出相应的应用：[https://play.google.com/store/apps/details?id=com.badapp。](https://play.google.com/store/apps/details?id=com.badapp%E3%80%82)

[原文](https://www.superweb999.com/article/569631.html)

*   [一、获取 BugReport](#一获取-bugreport)
*   [二、如何阅读 BugReport](#二如何阅读-bugreport)

  

__EOF__

![](https://images.cnblogs.com/cnblogs_com/lixuejian/1446003/o_200117082910288619%20(2).jpg) *   **本文作者：** [鲸小鱼的博客](https://www.cnblogs.com/lixuejian)
*   **本文链接：** [https://www.cnblogs.com/lixuejian/p/16665410.html](https://www.cnblogs.com/lixuejian/p/16665410.html)
*   **关于博主：** 富强 民主 文明 和谐 自由 平等 公正 法治 爱国 敬业 诚信 友善
*   **版权声明：** 本博客所有文章除特别声明外，均采用 [BY-NC-SA](https://creativecommons.org/licenses/by-nc-nd/4.0/ "BY-NC-SA") 许可协议。转载请注明出处！
*   **声援博主：** 如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。