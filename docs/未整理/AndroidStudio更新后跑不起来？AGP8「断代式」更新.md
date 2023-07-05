> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mp.weixin.qq.com](https://mp.weixin.qq.com/s/DC50Zz46LcnImt6hAJ38Xw)

随着 Android Studio Flamingo 正式版的发布，AGP 8（Android Gradle Plugin 8）也正式进入大家的视野，「**这次 AGP 8 相关更新属于「断代式」更新，同时如果想体验 AGP 8，就需要升级到 Android Studio Flamingo 版本，而升级到 Flamingo 的话，默认自带的 Java 版本就会变成 JDK 17**」 ····· 所以，这就是你需要适配 AGP8 的主要原因之一。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9wH3AYOXxRxuS97icckGSOiaCbAFBG7l5XGqN1Lkbb2xgXOlA5k2nHldQ/640?wx_fmt=png)

_1_

Flamingo 兼容

首先，如下图所示，「**使用 Flamingo 不一定就要用 AGP 8，它的支持范围是 3.2- 8.0 ，但是，因为 Flamingo 默认自带的 Java 版本是 JDK 17 ，所以默认情况下你最低需要 AGP 7**」 。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9iaetsrnQUQJYGIficTx80f1rKiceWhoEU16GOVjsF6BRfN58NvIHMPzFA/640?wx_fmt=png)

为什么 Flamingo 默认情况下只能用 AGP 7 ？

如下图 1 所示，是 Gradle & Java 的版本对照表，可以看到 Gradle 7.3 是第一个支持 Java 17 的 Gradle 版本，而根据图 2 Gradle 和 AGP 的版本对应关系，AGP7.2 开始所需最低 Gradle 版本就是 7.3.3，「**所以一般情况下建议 Flamingo 使用 AGP 7.2 和 gradle-7.3.3**」 。

<table><thead><tr><th><img class="" src="https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9abuyeZRWpzSHgnkWLdOvbGcf3dP2rzLgLceoUM25t2iaQWP5JE70iaAg/640?wx_fmt=png" style="max-height: 400px;"></th><th><img class="" src="https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9KYAwMic4hv6UqPyWRYicbXSfHDNLf88rZhDRgXZP80c1Ik11MKTkRdxQ/640?wx_fmt=png" style="width: auto; max-height: 400px;"></th></tr></thead></table>

> 当然，「**这里写了所需最低版本，所以你也可以用 AGP 7.0 搭配 gradle-7.3 运行**」，我有的项目就是使用了 build:gradle:7.0.3 / distributions/gradle-7.3-bin.zip 来适配 Flamingo。

另外，你也可以「**通过修改环境变量和 Android Studio 里的配置来使用低版本的 JDK**」 ，例如通过 Project Structure - Gradle Settings - Gradle JDK 来选择外部 JDK 版本，通过降低 JDK 版本来支持更低的 AGP 版本。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9uZUkwcuQanP6r5rQ3ibzqN0BEXHPDEobJiaAOuFHW6Fs3JMLT5ykUvQw/640?wx_fmt=png)

最后，Gradle 版本还和 Kotlin 版本有关系，根据你使用的 Gradle 版本也需要配置对应的  kotlin-gradle-plugin 版本。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9zb93KkRUM9Aia8oKM43kwpkQFzuytUWuT3Q2fgsaMU7r9CXskoQGThw/640?wx_fmt=png)![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K97eYNeKjzWXHVKczvBCnSyxNbg14ibhgjX7TLdONzs1wS1ibMlu7ibfszw/640?wx_fmt=png)

所以这里可以简单先总结一下：

*   要使用 AGP 8 需要 Android Studio Flamingo。
    
*   Android Studio Flamingo 自带 JDK17 ，默认情况下最低需要 build:gradle:7.2 / distributions/gradle-7.3.3-bin.zip ，兼容下可以 build:gradle:7.0.3 / distributions/gradle-7.3-bin.zip。
    
*   通过配置使用外部 JDK 可以降低 AGP 的版本要求。
    

> 是不是觉得现在 Android Studio 和 Gradle/Kotlin 关系捆绑得越来越紧密？

_2_

AGP 8

对于一些人而已，可能 7 还没用过，8 就又来了，但是 AGP 8 又属于「半断代式」更新，所以还是有需要适配一下。

**AGP Upgrade Assistant**
-------------------------

「**一般情况下我建议使用 AGP Upgrade Assistant 来先自动处理升级**」 ，可能还有一些人不知道什么是 AGP Upgrade Assistant ，其实就是你启动 Android Studio 的时候，右下角经常会弹出的提示框，也可以通过 Tools - AGP Upgrade Assistant 去打开。

<table><thead><tr><th><img class="" src="https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9xNc5aTy9GnZa96RtRvmrw1NOe1R5uOguVNVHMCzTmDOxNL4fJCKZcg/640?wx_fmt=png" style="max-height: 400px;"></th><th><img class="" src="https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9vIbYibUvX54hqcLTuicM33CsAm0z1OWgZcXqrDreianLJ1OlibElEzlXcw/640?wx_fmt=png" style="max-height: 400px;"></th></tr></thead></table>

> 如今的 AGP Upgrade Assistant 已然不是曾经的傻瓜式工具，它已经长大了。

如下图所示，如今的 AGP Upgrade Assistant 已经相当成熟， AS 会根据你当前的 AGP 版本，为你罗列出可以升级的 AGP 版本进行选择，同时你可以根据需要勾选迁移的选项，然后 Assistant 会根据你的选择自动调整你当前配置，这对于一些模块较多的项目在迁移时可以节省很多时间。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9ZzaopkQ70t1GKmv6nLmuzFuiapKd2AutZxxOIMdzVJvE9icn1j4MMjzQ/640?wx_fmt=png)

**DSL 支持 namespace**
--------------------

说到 AGP8 适配，首先必提的就是 namespace 的适配需求，升级到 AGP 8 之后，「**在 Gradle Sync 的时候你可能会到类似的错误提示 ：Namespace not specified ，这是因为 AGP 开始强制要求 namespace 配置**」。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9qV5RLoEfiaVN1ajbjGlAq0uia4WFGfIBml9HJMNcRL1IqLfgNlK2UBhg/640?wx_fmt=png)

> 其实 AGP 7 开始就是有 namespace 配置，而 AGP 8 开始强制要求。

谷歌这次是希望通过  namespace 来让  package 属性得到释放，特别对于 Module 结构来说， namespace 更贴合认知逻辑，而不会像  package 一样还 “夹带” 了 applicationId 的属性。

> namespace 也和后续的 R id 有关联。

如下图所示是需要调整的逻辑，主要就是移除了 Manifest 文件下的  package 属性 ，然后增加了 build.gradle 文件下的  namespace 配置，「**这一步推荐使用 AGP Upgrade Assistant 自动迁移，特别是对于 Module 比较多的项目，可以解放大量重复劳动**」。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9sick8oGcCGOEUHd3lbDh7V2nibOl5icknQ5rJ4DN7G3KzhicRz8XXQsQcA/640?wx_fmt=png)

**默认参数调整**
----------

如下图所示，AGP 8 里一些默认配置参数进行了调整，基本上这部分也是导致项目升级 AGP 8 跑不起来的「元凶」之一。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9zg3ayQib8405ic21ibNjuQGww4KYBs6q3eJ0LseOPXKV4E9oZvdjEwTwQ/640?wx_fmt=png)

当然，如果你是使用 AGP Upgrade Assistant 进行升级迁移的话，一般情况下 Assistant 会根据你的项目情况自动进行适配，如下图就是 Assistant 在升级迁移时在 gradle,properties 下自动新增的部分。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9Feqw031zicKCxdgZm9rNgiaRy8RHYzavSxD8RwSYycV6IoYliaic6SWDxg/640?wx_fmt=png)

### **buildfeatures.buildconfig**

如果你需要在 Module 代码里调用 BuildConfig，那么现在你需要如下代码所示一样配置 buildConfig：

```
android {
  buildFeatures {
    buildConfig = true
  }
}


```

或者直接在  gradle,properties 里全局配置。

```
android.defaults.buildfeatures.buildconfig=true


```

另外，如果是需要在 Kotlin 里使用  BuildConfig，还可以配置 BuildConfigAsBytecode 来提高编译速度：

```
android.enableBuildConfigAsBytecode=true


```

### **nonTransitiveRClass**

nonTransitiveRClass 也是存在已久的属性，简单来说就是配置非传递性 R 类 ，以前也有人用它来解决资源冲突，因为 「**nonTransitiveRClass 会强制要求 Module 的资源按 namespace 来区分使用**」。

由于这次默认值变成 true，所以如果不想弃用，可以在  gradle,properties 下配置为 false 。

```
android.nonTransitiveRClass=false

```

当然，你也可以通过 Android Studio 的自动化迁移工具来完成，毕竟这部分主要是涉及 namespace 声明而已，官方的自动化脚步处理还是挺方便的。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K97sa1ic44nZj9YNIGMaPS2g20ibDTZbia0ibp736SQ7yJTEEJOZXu6DzWoA/640?wx_fmt=png)

处理后也就是从 R 变成了 xxxxxx.xxxx.xxx.R 的效果：

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9iaTkZ2zoJUZWBINMpyu89BnTrkXiaAMVXialKG1fHgmK2BZI3wpC0SibFA/640?wx_fmt=png)

### **nonFinalResIds**

升级到 AGP8 ，你可能会看到一个错误：Resource IDs will be non-final ，这个问题主要出现在使用  switch 下的 R.id 场面，这个问题如果是没了解过，可能第一眼看到会觉得蒙圈，为什么不能用 R.id 了？

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K9RISib7jdpJiaDyNRHwogXfTM4KorwTSdrrsuEiblz3m9sjAlDLPq7HrdA/640?wx_fmt=png)

解决问题的最简单方式就是使用配置  nonFinalResIds 为 false ，或者你将 switch 修改为 if，其实我个人建议还是直接关闭  nonFinalResIds 来的实际，毕竟一对 if 还是很难受的。

```
android.nonFinalResIds=false


```

### **enableR8.fullMode**

这是一个很有意思的配置，R8 我记得应该是从 Android Studio 3.3 就存在，简单来说，「**R8 是一站式处理代码压缩（或 tree-shaking），资源缩减、混淆和优化的过程**」，一个官方定义比 Proguard 更快且压缩更好的配置。

「**默认情况下 AGP 3.4 开始 R8 就是默认编译器，但是它还是会使用 ProGuard 文件来修改其默认行为**」，此时的 R8 是普通模式，也就是之前的  android.enableR8=true 配置，「**普通模式是兼容 Proguard 的，所以用户基本无感**」。

但是 AGP8 开始 R8 默认是 fullMode ，R8 的 fullMode 会自动处理一些常见的混淆规则，但它比普通模式优化更激进，例如：

> 你只通过 Java 反射 API 引用了一个类， fullMode 下 R8 会觉得你的代码在运行时从不使用这个类，它会直接从 DEX 中删除该类。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K97BTDccGIIfTo4Rp7mQtByR5yE9bLfSpBMUN069fsC6fR6zSz41SqTw/640?wx_fmt=png)

当然，如果你的项目里 keep 规则是完整的，例如反射使用的所有内容都包含在 keep 规则中，那么 fullMode 应该不会引起什么问题，但是，如果你不希望它工作，那么配置  fullMode 为 false 也是可以的。

```
android.enableR8.fullMode=false


```

所以「适配  fullMode 的主要精力在于针对反射部分的混淆适配」，因为如果一不注意，反射部分的 class 在 dex 里就会被 R8 删除。

另外，要输出 R8 在构建项目时应用的所有规则完整报告，可以在  proguard-rules.pro 添加：

```
// You can specify any path and filename.
-printconfiguration ~/tmp/full-r8-config.txt


```

### **Plugin 适配**

AGP 8 里正式移除了  Transform API 并通过 Artifacts API 和 Instrumentation API 来缩短构建时间 ，在此之前，我们一般通过 Transform API 在编译后的 class 文件转换为 dex 文件之前一些自定义打桩处理，而 AGP 8 开始，为了提高构建性能，使用 Transform API 很难与 Gradle 的其它特性结合，所以本次正式移除 Transform API ，这是一些第三方插件需要适配的地方。

另外，在做 Maven 发布的时候，需要添加对应的 singleVariant 来支持 publications 的兼容，虽然这不是 AGP 8 才开始的，但是也算是需要适配的点之一。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/j9CXw3c38v49Nb2WmmP286UyBoI268K98oDibScZp4RncID6cw12dv9TOQkVibyqYicapSHWdyk6Np4dFS2c6gXPg/640?wx_fmt=png)

_3_

最后

AGP 8 和 Flamingo 需要兼容的问题大致就这样，可以看到 Android Studio 和 Gradle/Kotlin 关系捆绑得越来越紧密，如果不了解它们的依赖关系，处理器兼容就会迷失方向。

另外 AGP 现在的每个大版本变动也很大，比如前面没有特别介绍的 aidl 和 renderscript 配置位，下个大版本应该就会被移除了，只能说 Gradle 真的就是为了「折腾」而生。

如果你还有什么问题，欢迎点击阅读原文评价交流。

最后推荐一下我做的网站，玩 Android: _wanandroid.com_ ，包含详尽的知识体系、好用的工具，还有本公众号文章合集，欢迎体验和收藏！

推荐阅读：  

[视角拉高，系统性地梳理下 Gradle](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849933&idx=1&sn=523134fd3d73937643d2391f03d92d66&chksm=80b71613b7c09f0557d09e18ec1bee6e015725b0d246be10d4b51b12a1cedc56c62e1864095a&scene=21#wechat_redirect)  

[这一次，让 Kotlin Flow 操作符真正好用起来](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849930&idx=1&sn=9bebf20620959122663acbbd73e5faa3&chksm=80b71614b7c09f02a13e47682cb41ae3044e4a72dba76eee13b1a2e657087f7866d72e3fd4f9&scene=21#wechat_redirect)  

[Android 14 新功能之 TextView 搜索结果高亮和焦点移动～](http://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650849927&idx=1&sn=9ced2500bd417e84a5c311c674d0c58d&chksm=80b71619b7c09f0fb94539e10dbb93eb7f5725cf42fe8d9b846312d0ff61f55dbeed568a77b1&scene=21#wechat_redirect)  

**点击** 关注我的公众号

如果你想要跟大家分享你的文章，欢迎投稿~

┏(＾0＾)┛明天见！