> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7056576958875992077)

「这是我参与 2022 首次更文挑战的第 7 天，活动详情查看：[2022 首次更文挑战](https://juejin.cn/post/7052884569032392740 "https://juejin.cn/post/7052884569032392740")」

前言
==

由于很多应用在启动时需要进行一些初始化事务，导致在启动应用时有一定的空白延迟，在之前我们一般的做法是通过替换 `android:windowBackground` 的自定义主题，使应用启动时及时显示一张默认图片来改善启动体验。

在 Android 12 中，官方添加了 [SplashScreen API](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fabout%2Fversions%2F12%2Ffeatures%2Fsplash-screen%23suspend-drawing "https://developer.android.google.cn/about/versions/12/features/splash-screen#suspend-drawing")，它可为所有应用启用新的应用启动界面。新的启动界面是瞬时显示的，所以就不必再自定义`android:windowBackground` 了。新启动页面的样式默认是正中显示应用图标，但是允许我们自定义，以便应用能够保持其独特的品牌。下面我们来看看如何使用它。

启动画面实现
======

其实在 Android 12 上已经默认使用了 SplashScreen，如果没有任何配置，会自动使用 App 图标。

当然也允许自定义启动画面，在 value-v31 中的 style.xml 中，可以在 App 的主 Theme 中通过如下属性来进行配置：

```
<style >
    <item >@android:color/white</item>
    <item >@drawable/anim_ai_loading</item>
    <item >1000</item>
    <item >@mipmap/brand</item>
</style>
复制代码

```

*   `windowSplashScreenBackground`设置启动画面的背景色
    
*   `windowSplashScreenAnimatedIcon`启动图标。就是显示在启动界面中间的图片，也可以是动画
    
*   `windowSplashScreenAnimationDuration`设置动画的长度。注意这里最大只能 1000ms，如果需要动画时间更长，则需要通过代码的手段让启动画面在屏幕上显示更长时间（下面会讲到）
    
*   `windowSplashScreenIconBackground`设置启动图标的背景色
    
*   `windowSplashScreenBrandingImage`设置要显示在启动画面底部的图片。官方设计准则建议不要使用品牌图片。
    

运行启动应用就可以看到新的启动画面了，如下： ![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c40ed009992d4446be300062262c5464~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

动画的元素
=====

在 Android 12 上，显示在启动界面中间的图片会有一个圆形遮罩，所以在设计图片或动画的时候一定要注意，比如上面我的例子，动画其实就没有显示完整。对此官方给了详细的设计指导，如下：

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8969a1bb3f294a7eb7a12fda4414481b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

*   应用图标 (1) 应该是矢量可绘制对象，它可以是静态或动画形式。虽然动画的时长可以不受限制，但我们建议让其不超过 1000 毫秒。默认情况下，使用启动器图标。
*   图标背景 (2) 是可选的，在图标与窗口背景之间需要更高的对比度时很有用。如果您使用一个[自适应图标](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fguide%2Fpractices%2Fui_guidelines%2Ficon_design_adaptive "https://developer.android.google.cn/guide/practices/ui_guidelines/icon_design_adaptive")，当该图标与窗口背景之间的对比度足够高时，就会显示其背景。
*   与自适应图标一样，前景的 ⅓ 被遮盖 (3)。
*   窗口背景 (4) 由不透明的单色组成。如果窗口背景已设置且为纯色，则未设置相应的属性时默认使用该背景。

启动时长
====

默认当应用绘制第一帧后，启动画面会立即关闭。但是在我们实际使用中，一般在启动时进行一些初始化操作，另外大部分应用会请求启动广告，这样其实需要一些耗时的。通常情况下，这些耗时操作我们会进行异步处理，那么是否可以让启动画面等待这些初始化完成后才关闭？

我们可以使用 `ViewTreeObserver.OnPreDrawListener`让应用暂停绘制第一帧，直到一切准备就绪才开始，这样就会让启动画面停留更长的时间，如下：

```
...
var isReady = false
...

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.main_activity)
    ...
    
    val content: View = findViewById(android.R.id.content)
    content.viewTreeObserver.addOnPreDrawListener(
        object : ViewTreeObserver.OnPreDrawListener {
            override fun onPreDraw(): Boolean {
                return if (isReady) {
                    content.viewTreeObserver.removeOnPreDrawListener(this)
                    true
                } else {
                    false
                }
            }
        }
    )
}
复制代码

```

这样当初始化等耗时操作完成后，将 isReady 置为 true 即可关闭启动画面进入应用。

上面我们提到配置启动动画的时长最多只能是 1000ms，但是通过上面的代码可以让启动画面停留更长时间，所以动画的展示时间也就更长了。

关闭动画
====

启动画面关闭时默认直接消失，当然我们也可以对其进行自定义。

在 Activity 中可以通过`getSplashScreen`来获取（注意判断版本，低版本中没有这个函数，会 crash），然后通过它的`setOnExitAnimationListener`来定义关闭动画，如下：

```
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    splashScreen.setOnExitAnimationListener { splashScreenView ->
        val slideUp = ObjectAnimator.ofFloat(
            splashScreenView,
            View.TRANSLATION_Y,
            0f,
            -splashScreenView.height.toFloat()
        )
        slideUp.interpolator = AnticipateInterpolator()
        slideUp.duration = 200L
        //这里doOnEnd需要Android KTX库，即androidx.core:core-ktx:1.7.0
        slideUp.doOnEnd { splashScreenView.remove() }
        slideUp.start()
    }
}
复制代码

```

加上如上代码后，本来直接消失的启动画面就变成了向上退出了。

这里可以通过`splashScreenView`可以获取到启动动画的时长和开始时间，如下：

```
val animationDuration = splashScreenView.iconAnimationDurationMillis
val animationStart = splashScreenView.getIconAnimationStartMillis
复制代码

```

这样就可以计算出启动动画的剩余时长。

顺便吐槽一下官网这里代码错了，开始时间也用了`iconAnimationDurationMillis`来获取，实际上应该是`getIconAnimationStartMillis`

低版本使用 SplashScreen
==================

只能在 Android 12 上体验官方的启动动画，显然不能够啊！官方提供了 Androidx SplashScreen compat 库，能够向后兼容，并可在所有 Android 版本上显示外观和风格一致的启动画面（这点我保留意见）。

首先要升级 compileSdkVersion，并依赖 SplashScreen 库，如下：

```
android {
   compileSdkVersion 31
   ...
}
dependencies {
   ...
   implementation 'androidx.core:core-splashscreen:1.0.0-alpha01'
}
复制代码

```

然后在 style.xml 添加代码如下：

```
<style >
    // Set the splash screen background, animated icon, and animation duration.
    <item >@android:color/white</item>

    // Use windowSplashScreenAnimatedIcon to add either a drawable or an
    // animated drawable. One of these is required.
    <item >@drawable/anim_ai_loading</item>
    <item >1000</item>  # Required for
    # animated icons

    // Set the theme of the Activity that directly follows your splash screen.
    <item >@style/AppTheme</item>  # Required.
</style>
复制代码

```

前三个我们上面都介绍过了，这里新增了一个`postSplashScreenTheme`，它应该设置为应用的原主题，这样会将这个主题设置给启动画面之后的 Activity，这样就可以保持样式的不变。

注意上面提到的`windowSplashScreenIconBackground`和`windowSplashScreenBrandingImage`没有，这是与 Android12 的不同之一。

然后我们将这个 style 设置给 Application 或 Activity 即可：

```
<manifest>
   <application android:theme="@style/Theme.App.Starting">
    <!-- or -->
        <activity android:theme="@style/Theme.App.Starting">
...
复制代码

```

最后需要在启动 activity 中，先调用`installSplashScreen`，然后才能调用`setContentView`，如下

```
class MainActivity : ComponentActivity() {

   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       val splashScreen = installSplashScreen()
       setContentView(R.layout.activity_main)
...
复制代码

```

然后在低版本系统上启动应用就可以看到启动画面了。

> `installSplashScreen`这一步很重要，如果没有这一行代码，postSplashScreenTheme 就无法生效，这样启动画面后 Activity 就无法使用之前的样式，严重的会造成崩溃。比如在 Activity 中存在 AppCompat 组件，这就需要使用 AppCompat 样式，否则就会 Crash。

最后注意在 Android 12 上依然有圆形遮罩，所以需要遵循官方的设计准则；但是在低版本系统上则没发现有这个遮罩，而且在低版本上动画无效，只会显示第一帧的画面，所以我对官方说的风格一致保留意见。

现有启动画面迁移
========

目前市场上的 App 基本都自己实现了启动页面，如果直接添加 SplashScreen，就会造成重复，所以我们需要对原有启动页面进行处理。具体处理还要根据每个 App 自己的启动页面的实现逻辑来定，这里官方给出了一些意见，大家可以参考一下：[将现有的启动画面实现迁移到 Android 12 及更高版本](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fguide%2Ftopics%2Fui%2Fsplash-screen%2Fmigrate "https://developer.android.google.cn/guide/topics/ui/splash-screen/migrate")

总结
==

官方的 SplashScreen 有点姗姗来迟，不过效果还是不错的，使用起来也非常简单，但是一定要注意版本。虽然 Androidx SplashScreen compat 库可以向后兼容，但是与 Android 12 上还是有一些不同。