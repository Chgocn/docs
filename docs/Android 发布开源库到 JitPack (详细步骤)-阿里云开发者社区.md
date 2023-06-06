> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [developer.aliyun.com](https://developer.aliyun.com/article/1049030) [![](https://img.alicdn.com/imgextra/i1/O1CN01FDd3VW1Xzl2mZdCdg_!!6000000002995-2-tps-80-82.png) ![](https://img.alicdn.com/imgextra/i3/O1CN01wbC3WV1fxwOpwzcbp_!!6000000004074-2-tps-80-82.png) ](#free-product) 1 0 [](#comment)0 15 打赏 0 分享

Android 发布开源库到 JitPack (详细步骤)
=============================

2022-10-14 243 举报

**简介：** Android 发布开源库到 JitPack (详细步骤) +关注继续查看

### 发布开源库到 JitPack

*   [前言](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_1)
*   [正文](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_6)

*   [一、创建项目](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_9)
*   [二、模块创建与依赖](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_16)
*   [三、测试依赖库](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_36)
*   [四、项目配置](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_72)
*   [五、提交代码](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_99)
*   [六、创建Release&Tag版本](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#ReleaseTag_119)
*   [七、提交到JitPack](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#JitPack_134)
*   [八、测试依赖库](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_151)
*   [九、依赖库升级](https://llw-study.blog.csdn.net/article/details/118306518?spm=1001.2014.3001.5502#_176)

  

前言
==

  

最近这段时间，Google和jCenter的事情把开源库作者给搞懵了，jCenter还能使用一段时间，最近都是说迁移到mavenCentral上去，但是mavenCentral确实不太好迁移，所以退而求其次，选择JitPack。

  

 Jitpack的简单之处就在于，你只要把项目的代码提交到Github上之后，在打一个Release或者Tag，Jitpack就自动的帮你发布了一个新的版本，然后只要更新依赖就可以了，很方便，我个人是这么觉得的，虽说我没有写过什么开源依赖库，但是开源的小Demo我还是写了很多的，以帮助学习为主，这篇文章也是出于这个帮助的目的，下面进入正题。

  

正文
==

  

  希望这是你看过的最详细的关于发布项目到JitPack上的文章，我们从创建项目开始。

  

一、创建项目
------

  

打开你的AndroidStudio（我的版本 4.2.1），新建一个名为EasyLibrary的项目。

  

![20210628163000197.png](https://ucc.alicdn.com/pic/developer-ecology/f124850f78fd4312818d0c2b5bfeab26.png "20210628163000197.png")

  

点击Finish，完成项目的创建。

  

![20210628163533322.png](https://ucc.alicdn.com/pic/developer-ecology/47dbe4d617434a299ba1a2a4a17c4fee.png "20210628163533322.png")

  

下面进行依赖模块的创建。

  

二、模块创建与依赖
---------

  

File → New → New Module…

  

![2021062816380615.png](https://ucc.alicdn.com/pic/developer-ecology/4fa41a4960d04b52af40c48435788f40.png "2021062816380615.png")

  

下面这个图要注意的是选择Android Library，然后输入模块名。

  

![20210628164003442.png](https://ucc.alicdn.com/pic/developer-ecology/438be5084cee40b8a2149337def2f57b.png "20210628164003442.png")

  

点击Finish，模块创建完成之后，你就能看到这样的页面。

  

![20210628164214893.png](https://ucc.alicdn.com/pic/developer-ecology/598e590ef521405fb26b5cb7fd047739.png "20210628164214893.png")

  

可以看到easyutil与app是不同的图标，注意这一点，app是可运行模块，easyutil是可依赖模块。下一步就是app模块依赖easyutil模块，在依赖之后先看看app模块下的build.gradle

  

![2021062816450864.png](https://ucc.alicdn.com/pic/developer-ecology/0c6564a7a773473b93ae68a4cb0b4aad.png "2021062816450864.png")

  

此时dependencies闭包中是默认的依赖，下面来添加easyutil的依赖。点击下图的这个图标或者使用快捷键 Ctrl + Alt + Shift + S

  

![20210628164556344.png](https://ucc.alicdn.com/pic/developer-ecology/4328725cba4d41ff9efeab4a658bea7c.png "20210628164556344.png")

  

会弹出这样的弹窗。然后选择Dependencies ，按照下图的箭头进行操作。

  

![20210628164856990.png](https://ucc.alicdn.com/pic/developer-ecology/f2e79ef2caca4508a6a8d6b55a3ec4fe.png "20210628164856990.png")

  

这就是给app模块添加一个依赖模块。

  

![20210628165032144.png](https://ucc.alicdn.com/pic/developer-ecology/d996053fb9384bb7b6114ca64c9ee90d.png "20210628165032144.png")

  

勾选上easyutil，点击OK。

  

![20210628165120819.png](https://ucc.alicdn.com/pic/developer-ecology/02ca59ea3def4f33b2c8d6b6d6da11f4.png "20210628165120819.png")

  

此时你的app依赖内容里面就多了一个easyutil，点击OK，等待编译完成，再看看你的app下的build.gradle这里，多了一个依赖，此时依赖成功。

  

![20210628165226411.png](https://ucc.alicdn.com/pic/developer-ecology/45b112fc51844f28b0d8d503a3345b4a.png "20210628165226411.png")

  

三、测试依赖库
-------

  

 目前依赖库里面什么都没有，因此是不是真的依赖成功了，都不知道，因此要测试一下，怎么测试呢，写几个方法，要是app模块能够调用不报错就说明依赖没有问题。在com.llw.easyutil下新建一个EasyToast类，里面的代码如下：

  

  

```
package com.llw.easyutil;

import android.content.Context;
import android.widget.Toast;

/**
 * EasyToast
 * @author llw
 */
public class EasyToast {

    /**
     * Toast
     */
    public static void show(final Context context, final CharSequence text) {
        Toast.makeText(context, text, Toast.LENGTH_SHORT).show();
    }

    /**
     * LongToast
     */
    public static void showLong(final Context context, final CharSequence text) {
        Toast.makeText(context, text, Toast.LENGTH_LONG).show();
    }
}


```

然后在app模块的MainActivity中调用一下。

  

![20210628172550864.png](https://ucc.alicdn.com/pic/developer-ecology/bcf6bbeedd6c44449de1a5654f585a1c.png "20210628172550864.png")

  

在手机或者模拟器上运行一下：

  

![20210628172731805.png](https://ucc.alicdn.com/pic/developer-ecology/5d42390b171d4e9b95226eb917c6bf6e.png "20210628172731805.png")

  

依赖没有问题，下面对依赖库进行配置。

  

四、项目配置
------

  

打开工程的build.gradle，添加如下代码：

  

```
classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1'

```

```
maven { url "https://jitpack.io" }

```

  

添加位置如下图所示：

  

![20210628190702217.png](https://ucc.alicdn.com/pic/developer-ecology/a3f8e297a41c4f9fa1cb7d9fcfa674a4.png "20210628190702217.png")

  

再打开easyutil模块下的build.gradle，添加如下代码：

  

```
apply plugin: 'com.github.dcendents.android-maven'
group='com.github.lilongweidev'
```

  

添加位置如下图：

  

![20210628190649363.png](https://ucc.alicdn.com/pic/developer-ecology/801877d6b4894de28798b8f354a32710.png "20210628190649363.png")

  

这里的group表示你自己的仓库名称，比如我的github地址是这样的，

  

![20210628191834483.png](https://ucc.alicdn.com/pic/developer-ecology/0516936f5d3641899f5005893eb003db.png "20210628191834483.png")

  

那么就是group=‘com.github.lilongweidev’，按照自己的实际情况来写。

  

最后记得要点击Sync Now。

  

五、提交代码
------

  

 下面进行代码提交，首先保证自己的电脑上安装了git，git官网地址，找到安装版本下载，直接安装，注意安装的路径。安装好之后File → Settings

  

![2021062818010476.png](https://ucc.alicdn.com/pic/developer-ecology/454473f16c734dd791051271416c8e50.png "2021062818010476.png")

  

在图中标注的地方配置刚才git的安装路径，然后点击右边的Test按钮测试一下，出现弹窗，里面显示Success则说明git没有问题，点击GitHub → Add Account… ，登录你的Github账号。

  

![20210628181239209.png](https://ucc.alicdn.com/pic/developer-ecology/e24c1069ef9c4c449be46ba79d39758b.png "20210628181239209.png")

  

登录好了，

  

![20210628191047399.png](https://ucc.alicdn.com/pic/developer-ecology/809452025d6e4be2b9e5ae93c22a7d98.png "20210628191047399.png")

  

下面直接通过AndroidStudio来提交项目代码。

  

![2021062819105184.png](https://ucc.alicdn.com/pic/developer-ecology/9023aec6228a44499d238dbc449212fd.png "2021062819105184.png")

  

第一次提交就相当于在GitHub上创建仓库

  

![20210628191502788.png](https://ucc.alicdn.com/pic/developer-ecology/a4b204ca10ea417fb1edc3c0e2c58361.png "20210628191502788.png")

  

点击Share

  

![20210628191707769.png](https://ucc.alicdn.com/pic/developer-ecology/8eff4a3226eb4e69a6d66c5a671e05cc.png "20210628191707769.png")

  

点击Add.

  

![20210628192138852.png](https://ucc.alicdn.com/pic/developer-ecology/464b74727ece47efbc06017127acef46.png "20210628192138852.png")

  

第一来说第一次提交是比较顺利，但是也不排除网络环境比较差的提交失败，那就很麻烦了，如果请评论区留言，提交成功之后，你的AS右下角会出现一个弹窗提示，如上图所示，点击找个蓝色字体，就会进入到你提交到GitHub上的仓库地址。

  

![2021062819283073.png](https://ucc.alicdn.com/pic/developer-ecology/f78ef2c6488945bcb72cf9d8b3f3ba4e.png "2021062819283073.png")

  

六、创建Release&Tag版本
-----------------

  

点击这个tags

  

![20210628192949700.png](https://ucc.alicdn.com/pic/developer-ecology/bbce186e1d994ada986783351b3dd41a.png "20210628192949700.png")

  

点击Create a new release

  

![20210628193129673.png](https://ucc.alicdn.com/pic/developer-ecology/8784307f39614ef1a2b4ae4ba81a8b72.png "20210628193129673.png")

  

填写资料，release版本号和标题，以及该版本的描述。

  

![2021062819332934.png](https://ucc.alicdn.com/pic/developer-ecology/42db190e0bde4ac99b9b04b42575bb2f.png "2021062819332934.png")

  

向下滑动。

  

![2021062819344188.png](https://ucc.alicdn.com/pic/developer-ecology/45b5eab547214164bf59bf96c84059a2.png "2021062819344188.png")

  

上面有一个This is a pre-release的选中按钮，勾选上则表示这是一个预发布版本，不勾选就是正式发布版本，这里不勾选，点击Publish release。

  

![20210628193713339.png](https://ucc.alicdn.com/pic/developer-ecology/3147ea8e32ff43e0b1696e095186b355.png "20210628193713339.png")

  

发布成功。

  

七、提交到JitPack
------------

  

下面进入Jitpack

  

![20210628194110112.png](https://ucc.alicdn.com/pic/developer-ecology/8245be25b27241ed929f6ad423983496.png "20210628194110112.png")

  

我的地址仓库如下：[https://github.com/lilongweidev/EasyLibrary](https://github.com/lilongweidev/EasyLibrary)

后续会持续更新这个库，作为我自己的开源库，增加一个APP开发过程中的常用工具类，敬请期待。

  

将仓库地址复制到这个输入框中，然后点击Look Up，

  

![20210628194353351.png](https://ucc.alicdn.com/pic/developer-ecology/dd07b05aa5144f1caa18593d3e6d0ace.png "20210628194353351.png")

  

然后会出现你的发布版本，再点击Get it。

  

![20210628194653688.png](https://ucc.alicdn.com/pic/developer-ecology/8698aa693d914687b6fbe5f440219dea.png "20210628194653688.png")

  

现在提交成功了，再点击一下这个Get it。会自动向下滑，然后会告诉你怎么样在项目中使用这个依赖库。

  

![20210628194819463.png](https://ucc.alicdn.com/pic/developer-ecology/5c4333f151a044b9ba7730f9d1601289.png "20210628194819463.png")

  

八、测试依赖库
-------

  

为了证明这个依赖库确实有效，你可以新建一个EaseTest项目，然后这样配置进去。

打开新建项目的build.gradle，配置

  

![20210628195101476.png](https://ucc.alicdn.com/pic/developer-ecology/f87894c68f8446dca4cdedf4896dd85d.png "20210628195101476.png")

  

```
maven { url 'https://jitpack.io' }

```

  

如下图所示：

  

![20210628195517388.png](https://ucc.alicdn.com/pic/developer-ecology/b04818e881914d56a2b92416066c2cbf.png "20210628195517388.png")

  

再打开app模块下的build.gradle，配置

  

```
implementation 'com.github.lilongweidev:EasyLibrary:1.0.0'


```

![20210628195523839.png](https://ucc.alicdn.com/pic/developer-ecology/8811af50b286456bab7d7d9270932a50.png "20210628195523839.png")

  

然后Sync Now。

  

![20210628195704657.png](https://ucc.alicdn.com/pic/developer-ecology/5f9767b454174a29aa12053826642c8f.png "20210628195704657.png")

  

在MainActivity中调用依赖库中的方法即可。运行一下

  

![20210628200041288.png](https://ucc.alicdn.com/pic/developer-ecology/c8be5bd205e04c0cae5b2f1736908a51.png "20210628200041288.png")

  

九、依赖库升级
-------

  

 网络依赖库会根据开发者和使用者的需要而改变或者增加一个功能API，因此对应的依赖库版本也要进行升级，否则就无法使用新增的功能API，还是拿我的哪个EasyLibrary来举例子，比如我现在更新了一些工具类。

  

![20210707200338380.png](https://ucc.alicdn.com/pic/developer-ecology/68f42428db944b079a78a9264930532d.png "20210707200338380.png")

  

那么首先你要把这些更新的代码提交到GitHub上去，然后在GitHub上再发布一个release版本，比如我的库，

  

![20210707200459149.png](https://ucc.alicdn.com/pic/developer-ecology/840bdddc72a34e919f68303ab95f3158.png "20210707200459149.png")

  

现在最新是1.0.1，当我的代码提交之后我就要升级一个版本，也就是1.0.2。

  

![20210707200613181.png](https://ucc.alicdn.com/pic/developer-ecology/d8baa2f6f71b4d419075695300c53d78.png "20210707200613181.png")

  

点击Draft a new release

  

![20210707200832156.png](https://ucc.alicdn.com/pic/developer-ecology/1febe65ee67143e38027381759ed9638.png "20210707200832156.png")

  

设置release版本以及更新版本的内容，然后点击Publish release进行版本发布。

  

![20210707201004609.png](https://ucc.alicdn.com/pic/developer-ecology/4cc5cf7f27a0499daa9ea736a6399c6a.png "20210707201004609.png")

  

发布成功，现在进入jitpack，在输入框输入仓库地址，然后Look up你会看到刚才发布的1.0.2版本，

  

![20210707201052603.png](https://ucc.alicdn.com/pic/developer-ecology/9af47bb3e1ef476bb16d47702916fb60.png "20210707201052603.png")

  

点击Get it，等待一会儿，当这里的Log处有这个绿色文件时，说明已经在jitpack上生效了。

  

![20210707201159761.png](https://ucc.alicdn.com/pic/developer-ecology/de70ab3622af43c495dcdef3d9a3984d.png "20210707201159761.png")

  

这里就可以使用1.0.2版本了。

  

![20210707201311347.png](https://ucc.alicdn.com/pic/developer-ecology/5a27f73dfcca463e9d1ab8739c94d42c.png "20210707201311347.png")

  

然后再去你的使用这里依赖的地方改变一下版本号，然后Sync Now一下就可以使用新版本依赖库中的功能了。

  

SUCCESS，山高水长，后会有期~

  

  

**版权声明：**本文内容由阿里云实名注册用户自发贡献，版权归原作者所有，阿里云开发者社区不拥有其著作权，亦不承担相应法律责任。具体规则请查看《[阿里云开发者社区用户服务协议](https://developer.aliyun.com/article/768092)》和《[阿里云开发者社区知识产权保护指引](https://developer.aliyun.com/article/768093)》。如果您发现本社区中有涉嫌抄袭的内容，填写[侵权投诉表单](https://yida.alibaba-inc.com/o/right)进行举报，一经查实，本社区将立刻删除涉嫌侵权内容。

[API](/label/sc/article_de-3-100252) [开发工具](/label/sc/article_de-3-100016) [Android开发](/label/sc/article_de-3-100015) [git](/label/sc/article_de-3-100234) [开发者](/label/sc/article_de-3-100267) [Android jitpack](https://www.aliyun.com/sswb/625833.html) [Android发布](https://www.aliyun.com/sswb/332352.html) [开发者社区](/) > [开发与运维](/group/othertech/) > [文章](/group/othertech/article/) ![](//oneconsole.alicdn.com/sitemap/img/dev_chanpin.png) [云迁移中心](https://www.aliyun.com/product/apds) 已有1人感兴趣 [ ![](https://ucc.alicdn.com/avatar/avatar3.jpg) 游客mudplpiebkpio ](/profile/mudplpiebkpio) 作者高分内容 [更多](/profile/y6dbi3g7kyd6w/highScore_1) [ Android 组件化（二）注解与注解处理器、组件通讯 105 ](https://developer.aliyun.com/article/1080836) [ Android 组件化（一）项目模式管理、模式切换 118 ](https://developer.aliyun.com/article/1080834) [ Android 天气APP（三十六）运行到本地AS、更新项目版本依赖、去掉ButterKnife 136 ](https://developer.aliyun.com/article/1080830) [ Android Studio Dolphin | 2021.3.1不显示布局XML预览 1649 ](https://developer.aliyun.com/article/1050478) [Android 低功耗蓝牙开发简述 193](https://developer.aliyun.com/article/1050473) 

评论

[登录](https://account.aliyun.com/login/login.htm?oauth_callback=https%3A%2F%2Fdeveloper.aliyun.com%2Farticle%2F1049030)后可评论 相关文章 [![](https://ucc.alicdn.com/pic/developer-ecology/6w5iiviqxobva_a6f9a872a59447bbb458db3af59ef80b.png?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/1127008) ![](https://ucc.alicdn.com/avatar/avatar3.jpg) [游客6w5iiviqxobva](/profile/6w5iiviqxobva) [ 【 uniapp 】打包Android的apk(原生APP-云打包)，及发布测试 ](https://developer.aliyun.com/article/1127008) 【 uniapp 】打包Android的apk(原生APP-云打包)，及发布测试 460 0 [ ![](https://ucc.alicdn.com/pic/developer-ecology/2ff485fcb2a8481282662bf1448087ba.png?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/1062229) ![](https://ucc.alicdn.com/avatar/0140d7e28e88430a92df17f54c19e052.png) [程序猿在广东](/profile/bzq7yb74j52jc) [ Android Studio获取开发版SHA1值和发布版SHA1值的史上最详细方法 ](https://developer.aliyun.com/article/1062229) 今天我想把百度地图的定位集成到项目中来，想写个小小的案例，实现一下，但在集成百度地图时首先要申请秘钥，申请秘钥要用到SHA1值，所以今天就来总结一下怎样去获取这个值吧，希望对大家有帮助。 739 0 [ ![](https://ucc.alicdn.com/oskhugbn6jtys_20230516_18e025359b9846b6b610c225954612be.webp?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/998005) ![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg) [聚优云惠](/profile/oskhugbn6jtys) [ Android Bintray、JCenter 替代方案MavenCentral(发布jar,aar到Maven中央仓库) ](https://developer.aliyun.com/article/998005) Android Bintray、JCenter 替代方案MavenCentral(发布jar,aar到Maven中央仓库) 358 0 [ ](https://developer.aliyun.com/article/997905) ![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg) [聚优云惠](/profile/oskhugbn6jtys) [ Android如何通过Gradle发布Android依赖库（aar）到 jitpack 公共仓库 ](https://developer.aliyun.com/article/997905) Android如何通过Gradle发布Android依赖库（aar）到 jitpack 公共仓库 263 0 [ ](https://developer.aliyun.com/article/997902) ![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg) [聚优云惠](/profile/oskhugbn6jtys) [ Android如何通过Gradle发布java依赖库（jar）到 jitpack 公共仓库（—） ](https://developer.aliyun.com/article/997902) Android如何通过Gradle发布java依赖库（jar）到 jitpack 公共仓库（—） 157 0 [ ![](https://ucc.alicdn.com/pic/developer-ecology/ef913c56351849d49a80f4aa624db854.png?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/950813) ![](https://ucc.alicdn.com/avatar/2886848136f54069855dc62b7487858b.png) [yechaoa](/profile/yrvvnalewdp3q) [ 如何把自己的Android Library发布到GitHubd依赖使用 ](https://developer.aliyun.com/article/950813) 如何把自己的Android Library发布到GitHubd依赖使用 58 0 [ ![](https://ucc.alicdn.com/pic/developer-ecology/8f531bd8bd224b9a99291fd414dee2bf.png?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/942105) ![](https://ucc.alicdn.com/avatar/b5faf17b60ad44e694eb407be47bc46f.jpg) [运维开发故事](/profile/vrfnikl2juwkk) [ 使用Jenkins发布Android项目 ](https://developer.aliyun.com/article/942105) 使用Jenkins发布Android项目 330 0 [ ![](https://ucc.alicdn.com/pic/developer-ecology/922317d6341c4e61ab2fe08684dc17ac.jpeg?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/939217) ![](https://ucc.alicdn.com/avatar/avatar3.jpg) [xaubllxwtvaqiu](/profile/lzjpxl6ms2dp6) [ 微软正式发布 Windows 11，支持 Android 应用，面向开发者友好 ](https://developer.aliyun.com/article/939217) 微软正式发布 Windows 11，支持 Android 应用，面向开发者友好 87 0 [ ![](https://ucc.alicdn.com/pic/developer-ecology/84c579030e764aa791828c11e98459cd.png?x-oss-process=image/resize,h_118) ](https://developer.aliyun.com/article/939172) ![](https://ucc.alicdn.com/avatar/avatar3.jpg) [xaubllxwtvaqiu](/profile/lzjpxl6ms2dp6) [ Android 12 将允许运行 5 个 Chrome 实例；Furion v3.0.0.rc 发布 | 思否周刊 ](https://developer.aliyun.com/article/939172) Android 12 将允许运行 5 个 Chrome 实例；Furion v3.0.0.rc 发布 | 思否周刊 84 0 [ ](https://developer.aliyun.com/article/924357) ![](https://ucc.alicdn.com/avatar/avatar3.jpg) [mogqp3unktbnq](/profile/mogqp3unktbnq) [ Android 发布应用市场资料 ](https://developer.aliyun.com/article/924357) 应用内：建议512*512 70px 应用截图：480*800 1080*1920 应用市场基本都能覆盖到 不超过1MB（以小米手机为原因，状态栏不要带任何app的logo） 102 0 [ ![](https://ucc.alicdn.com/avatar/eea0824b1c064c0096156c9c2cdf2cd9.jpg?x-oss-process=image/resize,h_150,m_lfit) ](/profile/y6dbi3g7kyd6w) ![](https://ucc.alicdn.com/pic/ucc-admin/4c31495eb5904b729ac6f0d6b52f8eb3.png) +关注 [初学者-Study](/profile/y6dbi3g7kyd6w) Android开源爱好者 [190](/profile/y6dbi3g7kyd6w/article_1) 文章 [2](/profile/y6dbi3g7kyd6w/ask_1) 问答 [0](/profile/y6dbi3g7kyd6w/live_1) 视频 作者高分内容 [更多](/profile/y6dbi3g7kyd6w/highScore_1) [ Android 组件化（二）注解与注解处理器、组件通讯 105 ](https://developer.aliyun.com/article/1080836) [ Android 组件化（一）项目模式管理、模式切换 118 ](https://developer.aliyun.com/article/1080834) [ Android 天气APP（三十六）运行到本地AS、更新项目版本依赖、去掉ButterKnife 136 ](https://developer.aliyun.com/article/1080830) [ Android Studio Dolphin | 2021.3.1不显示布局XML预览 1649 ](https://developer.aliyun.com/article/1050478) [ Android 低功耗蓝牙开发简述 193 ](https://developer.aliyun.com/article/1050473) 文章排行榜 最热 最新 [ 1 代码的简单设计五原则 31630 ](https://developer.aliyun.com/article/1205073) [ 2 java 泛型 万字详解（通俗易懂) 28663 ](https://developer.aliyun.com/article/1205089) [ 3 阿里云检索分析服务Elasticsearch版内核持续优化 25728 ](https://developer.aliyun.com/article/1204940) [ 4 【分布式技术专题】「分布式技术架构」手把手教你如何开发一个属于自己的限流器RateLimiter功能服务 22166 ](https://developer.aliyun.com/article/1205440) [ 5 Hive SQL on Flink 构建流批一体引擎 16536 ](https://developer.aliyun.com/article/1205921) [ 6 PolarDB-X 致数据库行内人 (一) ~ 如何有效评测国产数据库的分布式事务 15778 ](https://developer.aliyun.com/article/1194957) [ 7 实践教程之体验PolarDB-X分布式事务和数据分区 10833 ](https://developer.aliyun.com/article/1191306) [ 8 Python3，自从掌握了这个方法，再也不用print进行调试了。 15238 ](https://developer.aliyun.com/article/1190832) [ 9 现场直击：2023阿里云峰会精彩发布抢先看（内含福利） 336067 ](https://developer.aliyun.com/article/1190853) [ 10 实践教程之如何在ARM平台部署PolarDB-X 9858 ](https://developer.aliyun.com/article/1206144) [ 11 如何解决IoT数据入仓的客户实践 9087 ](https://developer.aliyun.com/article/1201889) [ 12 如何在5分钟之内完成一个物联网统计指标？ 7686 ](https://developer.aliyun.com/article/1205907) [ 13 大数据&AI产品月刊【2023年4月】 5895 ](https://developer.aliyun.com/article/1202497) [ 14 ClickHouse创始人：融合数据库该“卷”的还是性能和速度 55875 ](https://developer.aliyun.com/article/1191604) [ 15 阿里云联合埃森哲正式发布《跨国企业上云登陆区（Landing Zone）白皮书》 51251 ](https://developer.aliyun.com/article/1192014) [ 16 除了价格降70%，关于对象存储预留空间你还需要了解这些 32823 ](https://developer.aliyun.com/article/1191290) [ 17 编程中最难的就是命名？这几招教你快速上手 32756 ](https://developer.aliyun.com/article/1191967) [ 18 RocketMQ 多级存储设计与实现 21333 ](https://developer.aliyun.com/article/1191787) [ 19 Spring Boot 单体应用一键升级成 Spring Cloud Alibaba 2993 ](https://developer.aliyun.com/article/1205581) [ 20 实践教程之基于Prometheus+Grafana的PolarDB-X监控体系 34293 ](https://developer.aliyun.com/article/1191294) [ 1 不一样的ECS U实例 325 ](https://developer.aliyun.com/article/1241503) [ 2 NineData，稳定、高效的Redis数据同步解决方案 210 ](https://developer.aliyun.com/article/1241011) [ 3 30分钟，一键拉起基于LLM + AnalyticDB PostgreSQL构建的企业专属Chatbot 358 ](https://developer.aliyun.com/article/1240791) [ 4 python小知识点总结 59 ](https://developer.aliyun.com/article/1240403) [ 5 AIGC创作家 628 ](https://developer.aliyun.com/article/1240296) [ 6 当MySQL想恋爱，java和navicate抢着做媒婆 ------ java连接MySQL数据库 & navicat for MySQL 连接 660 ](https://developer.aliyun.com/article/1240291) [ 7 亿视电子基于PolarDB-X打造能源数字基座实践 529 ](https://developer.aliyun.com/article/1240206) [ 8 数据库内核那些事｜深度解析PolarDB DDL锁的优化和演进 5745 ](https://developer.aliyun.com/article/1240133) [ 9 MindOpt Tuner调参器，提升求解速度、性能（一） 16434 ](https://developer.aliyun.com/article/1240073) [ 10 全面升级，重磅上线！容器服务ASK评测携大奖前来！ 593 ](https://developer.aliyun.com/article/1240069) [ 11 解锁技术人的“成长公式” 5837 ](https://developer.aliyun.com/article/1240027) [ 12 WebAssembly入门：构建高性能的浏览器应用 10305 ](https://developer.aliyun.com/article/1239998) [ 13 使用React Native构建跨平台桌面应用程序 10298 ](https://developer.aliyun.com/article/1239996) [ 14 前端学习笔记202304学习笔记第八天-web前端架构学习笔记-1 115 ](https://developer.aliyun.com/article/1239970) [ 15 尾号限行 API 实现微信小程序车辆尾号限行查询功能 10520 ](https://developer.aliyun.com/article/1239830) [ 16 挑战“三大框架”的解决方案 10246 ](https://developer.aliyun.com/article/1239826) [ 17 阿里云丁宇：云上开发成为主流，Serverless 定义新范式 6420 ](https://developer.aliyun.com/article/1239808) [ 18 如何在 Python 中将数字转换为字母？ 53 ](https://developer.aliyun.com/article/1239800) [ 19 阿里云AI新品“通义听悟”，开放公测！ 2108 ](https://developer.aliyun.com/article/1239787) [ 20 web前端课程设计：个人简历 10815 ](https://developer.aliyun.com/article/1239783) 相关课程 [更多](https://developer.aliyun.com/course/explore) [ 开源Android容器化框架Atlas开发者指南 ](https://developer.aliyun.com/learning/course/392) ![](https://img.alicdn.com/imgextra/i4/O1CN01usl7VE1voLwF9ZPPt_!!6000000006219-2-tps-28-32.png) 1512 ![](https://img.alicdn.com/imgextra/i2/O1CN01QxBFSF247bVrecdi7_!!6000000007344-2-tps-34-30.png) 7 [去学习](https://developer.aliyun.com/learning/course/392) 推荐文章 [现场直击：2023阿里云峰会精彩发布抢先看](https://developer.aliyun.com/article/1190853) [AIGC主题征文开启，参与赢取富士拍立得](https://developer.aliyun.com/article/1209568) [乘风伯乐招募令，邀请伯乐获取多重好礼！](https://developer.aliyun.com/article/1209561) [乘风者计划邀您入驻社区，精彩权益即刻享](https://developer.aliyun.com/topic/bloggers?utm_content=g_1000288847) 相关电子书 [更多](https://developer.aliyun.com/ebook) [ ![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png) ](https://developer.aliyun.com/ebook/5189) [ ![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png) ](https://developer.aliyun.com/ebook/7576) [ ![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png) ](https://developer.aliyun.com/ebook/347) [ ![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png) ](https://developer.aliyun.com/ebook/5189) [ ![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png) ](https://developer.aliyun.com/ebook/7576) [ ![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png) ](https://developer.aliyun.com/ebook/347) [ ![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png) ](https://developer.aliyun.com/ebook/5189) [ ![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png) ](https://developer.aliyun.com/ebook/7576) [ ![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png) ](https://developer.aliyun.com/ebook/347) 蚂蚁聚宝Android秒级编译——Freeline [立即下载](https://developer.aliyun.com/ebook/download/5189) 低代码开发师（初级）实战教程 [立即下载](https://developer.aliyun.com/ebook/download/7576) 阿里巴巴DevOps 最佳实践手册 [立即下载](https://developer.aliyun.com/ebook/download/347) 相关镜像 [ packman ](https://developer.aliyun.com/mirror/packman) 目录 [发布开源库到 JitPack](#slide-0) [前言](#slide-1) [正文](#slide-2) [一、创建项目](#slide-3) [二、模块创建与依赖](#slide-4) [三、测试依赖库](#slide-5) [四、项目配置](#slide-6) [五、提交代码](#slide-7) [六、创建Release&Tag版本](#slide-8) [七、提交到JitPack](#slide-9) [八、测试依赖库](#slide-10) [九、依赖库升级](#slide-11)