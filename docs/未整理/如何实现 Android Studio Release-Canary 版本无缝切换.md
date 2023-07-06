> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7024527570867585038)

本文已参与「[掘力星计划](https://juejin.cn/post/7012210233804079141/ "https://juejin.cn/post/7012210233804079141/")」，赢取创作大礼包，挑战创作激励金。

背景
--

首先众所周知的，`Android Studio` 有四个渠道包

*   Canary
*   Dev
*   Beta
*   Stable

这四个版本的根本区别在于他们的稳定性

Canary（中文名金丝雀）是 `Android Studio` 最早发布的预览版，用于获取开发过程中的真实反馈，通常一周更新一次，比如 Android  Studio 3.2 Canary 2、3、4.。。。, 但是对应的也会有 stable，dev，beta 版本，例如现在最新的 `Android Studio Chipmunk Canary 3`

Dev（开发版） 在 Canary 版本之后，经过完整测试之后即发布一个 Dev 版本，而在 Dev 版本中选择相对稳定的发布为 Beta 版本。

Beta（Beta 版本） 通常选择稳定的 Canary 版本也就是 Dev 版本中的一个作为 Beta 版本发行。而每发布一个 Beta 版本时，通常会同时发行一个 stable 版本。

Stable 即稳定版本

目的
--

首先我们知道，`Android Studio` 不同版本是可以共存的，比如我同时安装一个`Stable`版本用作平时开发，但是我又想提前体验一些新鲜的功能，这时候我就需要一个`Canary`版本

但是这样问题就来了，`Android Studio` 默认会为我们每个版本生成对应的缓存配置文件夹，比如我在 Windows 上同时安装了 `Android Studio 3.4.2 稳定版`和 `Android Studio Chipmunk Canary`，那么每个版本的设置会分别保存在类似如下的目录中

```
C:\Users\xxx\.AndroidStudio3.4.2\
C:\Users\xxx\.AndroidStudioChipmunkPreview\


```

> 在 Mac 上，这些目录位于 `~/Library/Preferences/` 和 `~/Library/Application Support/` 中。在 Linux 上，这些目录位于 /home 主目录中。

这样有好处，所有的版本的数据都是完全隔离的，但是隔离意味着独立，然而我们有许多的东西并不想独立，比如我的 IDE 的设置，比如我已经习惯了的一套快捷键，假如是完全分开的，那么我就要在新的`Android Studio`中去重新设置一下这些，很是繁琐，且没必要

就像 `Android SDK` 一样，我们一般只会下一份，所有的 `Android Studio` 都共用一份

那么现在我们的目的就很明确了，我希望 `Android Studio` 的配置可以做到像`SDK`一样的，全局只有一份，这样我不管用哪个版本的 `Android Studio` ，打开就是熟悉的配方，一样的项目分布，一样的 IDE 设置，一样的插件，不一样的仅仅是 `Android Studio` 本身的功能差异

比如我突然想体验一个 `Canary` 才上的功能，我希望我切过去的时候，我的项目还是那样，而不是要重新导入一份

又或者比如我最近在用 `Canary` 版本开发，但是由于 `Canary` 本身的不稳定，我突然遇到了一个棘手的`Canary`版本的 bug，这时候我要切回 `Release` 版本开发，同样的，我希望我的项目用 `Release` 打开还是一样的。

解决方案
----

首先我们在 `Android Studio` 官方的文档中就有专门的一项 [配置 IDE](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fintro%2Fstudio-config%3Fhl%3Dzh-cn "https://developer.android.com/studio/intro/studio-config?hl=zh-cn")，里面就提到了

> 您可以通过 Android Studio 的 **Help** 菜单访问下面这两个配置文件：
> 
> *   [`studio.vmoptions`](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fintro%2Fstudio-config%3Fhl%3Dzh-cn%23customize_vm "https://developer.android.com/studio/intro/studio-config?hl=zh-cn#customize_vm")：自定义 Studio 的 Java 虚拟机 (JVM) 选项，例如堆大小和缓存大小。请注意，在 Linux 计算机上，此文件可能会命名为 `studio64.vmoptions`，具体取决于您的 Android Studio 版本。
> *   [`idea.properties`](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fintro%2Fstudio-config%3Fhl%3Dzh-cn%23customize_ide "https://developer.android.com/studio/intro/studio-config?hl=zh-cn#customize_ide")：自定义 Android Studio 的属性，例如插件文件夹路径或支持的文件大小上限。

我们可以看到通过 `idea.properties` 文件，我们可以自定义 `Android Studio` 的 IDE 属性，例如用户所安装插件的路径以及 `Android Studio` 支持的文件大小上限。`idea.properties` 文件会与 `Android Studio` 的默认属性合并，以便我们可以仅指定需替换的属性。

可啊

这不正是我们需要的吗

首先我们先抽出一份自定义的 `idea.properties` 文件 ，接下来我们只要想办法让所有的 `Android Studio` 都用这份自定义的文件，而不用自带的不就完事了吗

是不是很熟悉，想想我们的 SDK，是怎么做到的，通过环境变量啊，大胆猜一猜，`Android Studio` 肯定会支持设置环境变量啊，我们去扒文档

果然有，我们很容易就在 [官方文档](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fintro%2Fstudio-config%3Fhl%3Dzh-cn%23file_location "https://developer.android.com/studio/intro/studio-config?hl=zh-cn#file_location") 找到了

> 您也可以使用以下环境变量指向其他位置的特定替换文件：
> 
> *   `STUDIO_VM_OPTIONS`：设置 `.vmoptions` 文件的名称和位置
> *   `STUDIO_PROPERTIES`：设置 `.properties` 文件的名称和位置
> *   `STUDIO_JDK`：设置运行 Studio 所使用的 JDK

所以到此为止，路就已经完全打通了，剩下的我们就是去熟悉下 `idea.properties` 都有哪些内容，只有了解这些，我们才能去做自己的定制

我们先看一份常见的 `idea.properties` 吧，这文件里面包括常见的自定义的 IDE 属性。如需获取完整的属性列表，请参阅 [IntelliJ IDEA 的 `idea.properties` 文件](https://link.juejin.cn?target=https%3A%2F%2Fwww.jetbrains.com%2Fhelp%2Fidea%2F2020.2%2Ffile-idea-properties.html "https://www.jetbrains.com/help/idea/2020.2/file-idea-properties.html")。

```
#---------------------------------------------------------------------
# Uncomment this option if you want to customize path to user installed plugins folder. Make sure
# you're using forward slashes.
#---------------------------------------------------------------------
# idea.plugins.path=${idea.config.path}/plugins
#---------------------------------------------------------------------
# Maximum file size (kilobytes) IDE should provide code assistance for.
# The larger file is the slower its editor works and higher overall system memory requirements are
# if code assistance is enabled. Remove this property or set to very large number if you need
# code assistance for any files available regardless their size.
#---------------------------------------------------------------------
idea.max.intellisense.filesize=2500
#---------------------------------------------------------------------
# This option controls console cyclic buffer: keeps the console output size not higher than the
# specified buffer size (Kb). Older lines are deleted. In order to disable cycle buffer use
# idea.cycle.buffer.size=disabled
#---------------------------------------------------------------------
idea.cycle.buffer.size=1024
#---------------------------------------------------------------------
# Configure if a special launcher should be used when running processes from within IDE.
# Using Launcher enables "soft exit" and "thread dump" features
#---------------------------------------------------------------------
idea.no.launcher=false
#---------------------------------------------------------------------
# To avoid too long classpath
#---------------------------------------------------------------------
idea.dynamic.classpath=false
#---------------------------------------------------------------------
# There are two possible values of idea.popup.weight property: "heavy" and "medium".
# If you have WM configured as "Focus follows mouse with Auto Raise" then you have to
# set this property to "medium". It prevents problems with popup menus on some
# configurations.
#---------------------------------------------------------------------
idea.popup.weight=heavy
#---------------------------------------------------------------------
# Use default anti-aliasing in system, i.e. override value of
# "Settings|Editor|Appearance|Use anti-aliased font" option. May be useful when using Windows
# Remote Desktop Connection for instance.
#---------------------------------------------------------------------
idea.use.default.antialiasing.in.editor=false
#---------------------------------------------------------------------
# Disabling this property may lead to visual glitches like blinking and fail to repaint
# on certain display adapter cards.
#---------------------------------------------------------------------
sun.java2d.noddraw=true
#---------------------------------------------------------------------
# Removing this property may lead to editor performance degradation under Windows.
#---------------------------------------------------------------------
sun.java2d.d3d=false
#---------------------------------------------------------------------
# Workaround for slow scrolling in JDK6
#---------------------------------------------------------------------
swing.bufferPerWindow=false
#---------------------------------------------------------------------
# Removing this property may lead to editor performance degradation under X Window.
#---------------------------------------------------------------------
sun.java2d.pmoffscreen=false
#---------------------------------------------------------------------
# Workaround to avoid long hangs while accessing clipboard under Mac OS X.
#---------------------------------------------------------------------
# ide.mac.useNativeClipboard=True
#---------------------------------------------------------------------
# Maximum size (kilobytes) IDEA will load for showing past file contents -
# in Show Diff or when calculating Digest Diff
#---------------------------------------------------------------------
# idea.max.vcs.loaded.size.kb=20480


```

这里面的属性我就不带大家一个一个去解释了，一般都是知名见意的，遇到不了解的，google 一下就有

最佳实践
----

下面贴下我的方案，已经用了五六年了，这么多年，我电脑上一般同时安装 3 个版本的`Android Studio` ，`Canary`， `Beta`， `Release`各一份，由于我本人毕竟喜欢追新，所以这么多年我 99% 的时间都是用`Canary`版本开发的，也有很紧急遇到 bug 的需要临时切换到`Beta`或`Release`的，我所做的就是关掉预览版，打开稳定版就完事了，项目都保持原样，包括你在上个 IDE 打开的页面，完全一毛一样。

下面贴下我的设置，我目前切回 Windows 了，以前也用过 Arch，Mac 开发，都是这样搞的，都可以实现。下面就只贴 Windows 的了

首先环境变量

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d880c05b0d241b79a0d258e869e3c55~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

看到我已经设置了环境变量，使得我所有的`Android Studio` 都使用这份配置，我们再去打开这个自定义的文件看一下

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ca76867df3944a5ba601816604a4421~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

这样就行了，以后我们所有的`Android Studio` 的配置都在下面的四个文件夹里

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c71f2fb6f39e455f9abbe7b51bd678ba~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

**「欢迎在评论区讨论，掘金官方将在[掘力星计划](https://juejin.cn/post/7012210233804079141 "https://juejin.cn/post/7012210233804079141")活动结束后，在评论区抽送 100 份掘金周边，抽奖详情见活动文章」。**