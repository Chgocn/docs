> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6997217571208445965#heading-23)

> `Android 12`上新引入的`Splash Screen`功能，可以高效打造自由、丰富的应用启动效果。但仍占据市场主流的低版本设备，如何才能尝上鲜呢？答案就是 Jetpack 的新成员`SplashScreen`库，让我们一探其用法以及背后的原理！

前言
--

早在`Android 12 Preview`版公开的时候，我注意到了酷炫的`Splash Screen`功能，便快速定制和分享了用它打造的各式启动效果，收到了很多积极的反馈。万分荣幸的是：负责 Splash Screen 功能的 Google 工程师也给我的 Demo 点了赞，还进行了转发！

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/669c243de7964efb80db7e66789dbbd1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

但不少开发者表示 Android 12 上的效果固然不错，可 12 之前的版本才是主流，如果不兼容的话，实属有些鸡肋。包括我在内，都很关心低版本如何才能应用上这个新功能。

翻阅这位 Google 工程师的历史推文的时候，意外发现不久前`AndroidX`包也刚推出了一个叫`SplashScreen`的同名 API。我们都知道 AndroidX 归属`Jetpack`，是独立于 SDK 版本以外的开发框架集合，很显然它就是用来兼容低版本的 Splash Screen 功能库。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e77493524bfc42b4811fb70e08e21d9e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

这可谓是及时雨啊，必须研究一下，让 Splash Screen 功能充分发挥它的作用！加上之前分享的文章没有提及实现原理，本文一并探讨一下。

1. 新成员 SplashScreen
-------------------

**Jetpack SplashScreen 是向后兼容 Android 12 Splash Screen 功能的开发库。** 其最早支持到`Android 6`（API 23），全球的 Android 设备中 6 及以上的版本占用率达到了 **9 成**以上，可以说完全够用了。

### 1.1 作用以及局限

Splash Screen 功能打造的启动画面分为**进场**和**退场**两部分：前者负责展示 Icon、Icon 动画以及品牌 Logo 等基本信息，后者则用于展示整体或 Icon 视图过渡到目标画面的动画效果。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6f64f95e0ec64cf69b1001140ea8ace1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

**对于退场部分而言，无论是否运行在 12 上，Jetpack SplashScreen 库都能达到 Android 12 同等效果；但进场部分如果是运行在低版本上，暂时存在些局限。**

*   暂不支持展示 Icon 动画：`AnimatedVectorDrawable`
*   暂不支持配置 Icon 背景：`IconBackgroundColor`
*   暂不支持设置品牌 Logo：`BrandingImage`

<table><thead><tr><th>进场部分的功能对比</th><th>Jetpack 版</th><th>Android 12 版</th></tr></thead><tbody><tr><td>ScreenBackground</td><td>YES</td><td>YES</td></tr><tr><td>ScreenIcon</td><td>YES</td><td>YES</td></tr><tr><td>AnimatedVectorDrawable</td><td><strong>NA</strong></td><td>YES</td></tr><tr><td>IconBackgroundColor</td><td><strong>NA</strong></td><td>YES</td></tr><tr><td>BrandingImage</td><td><strong>NA</strong></td><td>YES</td></tr></tbody></table>

备注：后面会讲到 SplashScreen 库的实现原理，面向低版本的进场效果本质上是一个`LayerDrawable`。说实话，对于支持`Vector Drawable`动画、`Adaptive Icon`背景是无能为力的。但笔者认为在代码层面加入些额外处理，是可以做到完美支持的，期待后期升级能完善一下！

### 2.2 导入依赖

SplashScreen 库的最新版本为`1.0.0-alpha01`，Gradle 里简单依赖即可。

```
dependencies {
    def core_version = "1.6.0"
    ...
    // Optional - APIs for SplashScreen, including compatiblity helpers on devices prior Android 12
    implementation "androidx.core:core-splashscreen:1.0.0-alpha01"
}
复制代码

```

### 2.3 SplashScreen 库预设主题

SplashScreen 库针对启动画面的打造定义了专用的 Attr，比如设置 Icon 和画面背景的`windowSplashScreenAnimatedIcon`和`windowSplashScreenBackground`，以及设置启动画面退出后 Activity 主题的`postSplashScreenTheme`等。

低版本并不支持 Icon 动画，也就谈不上配置时长，但库还是提供了相关的属性`windowSplashScreenAnimationDuration`，原因不太清楚。

```
<attr format="reference" />
<attr format="reference" />
<attr format="integer" />
<attr format="color" />
复制代码

```

此外为了简化 App 端的配置，还预设了主题，同时并针对设备版本进行了区分。

如下是面向低版本的主题：

```
<style >
    <item >?android:attr/theme</item>
    <item >@integer/default_icon_animation_duration</item>
    <item >@android:color/background_light</item>
    <item >@android:drawable/sym_def_app_icon</item>
</style>

<style >
    <item >@drawable/compat_splash_screen</item>
    <item >opaque</item>
    <item >true</item>
    <item >false</item>
    <item >@android:color/transparent</item>
    <item >@android:color/transparent</item>
</style>
复制代码

```

比较关键的是父主题`SplashScreenBase`设置了`windowBackground`属性，其指向的 Drawable 负责展示低版本的启动画面。

原理很简单：读取 windowSplashScreenBackground 设置的背景`ColorDrawable`和 windowSplashScreenAnimatedIcon 设置的图标 Drawable，图标 Drawable 放置到背景 Drawable 的中央，然后组合成`Layer Drawable`。老实说，跟我们以前自定义启动画面的思路是类似的。

```
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:gravity="fill">
        <color android:color="?attr/windowSplashScreenBackground" />
    </item>
    <item
        android:drawable="?attr/windowSplashScreenAnimatedIcon"
        android:gravity="center"
        android:width="@dimen/splashscreen_icon_size"
        android:height="@dimen/splashscreen_icon_size" />
</layer-list>
复制代码

```

因 12 版本提供了 Splash Screen 功能的系统属性，所以针对 12 及以上的主题，只需要将库定义的属性转化为系统属性。

```
<style >
    <item >?windowSplashScreenAnimatedIcon</item>
    <item >?windowSplashScreenBackground</item>
    <item > ?windowSplashScreenAnimationDuration</item>
</style>
复制代码

```

2. 打造进场效果
---------

在原有的 Demo 上适配新的 API，感觉缺乏新意。之前用`Jetpack Compose`复刻的`Flappy Bird`游戏没来得及设计启动画面，那这次就利用 Jetpack 的 SplashScreen 库完善一下。

### 2.1 准备动画图标

Flappy Bird 游戏的 Logo 是一个小鸟，目前只有一张 PNG，要定制 Icon 动画效果的话要转为 SVG。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8595b78fbd5243a89d4713a7b061daef~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

找了很多工具，终于发现一个可以将 PNG 完美转换为 SVG 的网站：支持添加和删除各种颜色区域，进而可以最大程度地还原每个色块，每个 Path。

[www.pngtosvg.com/](https://link.juejin.cn?target=https%3A%2F%2Fwww.pngtosvg.com%2F "https://www.pngtosvg.com/")

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52dbe022f3b2418a98e103dff329f852~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

通过 AS 自带的`Vector Asset Tool`和`Animated Vector Drawable`标签，可以将 SVG 扩展成格式效果的矢量图动画文件。

```
<animated-vector ...>
    <aapt:attr >
        <vector
            android:width="400dp"
            android:height="404.73373dp"
            ...>
            <group
                android:
                android:pivotX="200"
                android:pivotY="202"
                android:rotation="0">
                <path
                    android:fillColor="#503745"
                    android:fillType="evenOdd"
                    android:pathData="M173.7,133.065C173.419,133.178 ..."
                    android:strokeColor="#00000000" />
                ...
            </group>
        </vector>
    </aapt:attr>

  <target android:>
    <aapt:attr >
      <set>
        <objectAnimator
            android:duration="@integer/icon_animator_duration"
            android:interpolator="@android:anim/decelerate_interpolator"
            android:property
            android:valueFrom="@integer/icon_animator_translate_from"
            android:valueTo="@integer/icon_animator_translate_to" />
        ...
      </set>
    </aapt:attr>
  </target>

</animated-vector>
复制代码

```

### 2.2 适配主题

Android 12 上给入口 Activity 指定 Splash Screen 功能的相关属性就可以实现进场效果。SplashScreen 库也是一样的思路，不过为了简化代码，我们可以给 Activity 配置库预设好的主题。

因 App 端要给这些属性指定独有的资源，所以主题还需要简单扩展下，同时也要针对版本进行区分。如果碰巧最早也支持到 Android 6 的话，配置默认主题和面向 12 的 values-v31 主题即可，不然还需要配置面向 6~11 的 values-23 主题。

默认主题必须继扩展自预设主题`Theme.SplashScreen`，同时覆写一下 Icon、Duration 和 ScreenBackground 三个属性。因面向 12 的主题的部分属性和默认主题是一致的，所以将共通的部分抽出到 Base 中复用。

```
<style >
</style>

<style >
    ...
    <item >@drawable/ic_icon_bird_small_animated_translate</item>
    <item >@integer/icon_animator_duration</item>
    <item >@color/appBackground</item>

    <item >@style/SplashActivityTheme</item>
</style>

<style >
    <item >@color/purple_500</item>
    <item >@color/purple_700</item>
    <item >@color/teal_200</item>
</style>
复制代码

```

需要提醒的是，最好指定下`postSplashScreenTheme`属性。因为后续的定制效果需要用到 SplashScreen 类，而它将严格检查要求这个属性的配置，否则会发生 Crash。

> Cannot set AppTheme. No theme value defined for attribute postSplashScreenTheme.

另外，大部分 Activity 一般继承自 AppCompat 框架提供的 Activity，该框架要求画面必须配置 AppCompat 系主题。也就是说，postSplashScreenTheme 属性不仅要指定，还得是 AppCompat 系主题！

备注：AppCompat 框架作此限制的原因，可以在之前写的文章里查看[「深度解读 Jetpack 框架的基石 - AppCompat」](https://juejin.cn/post/6965860527897575437 "https://juejin.cn/post/6965860527897575437")。

> You need to use a Theme.AppCompat theme!

通过复用共通主题，面向 12 的主题只需要指定 Icon 背景属性`IconBackgroundColor`和品牌 Logo 属性`BrandingImage`。

```
<style >
    <item >@color/skyBlue</item>
    <item >@drawable/ic_tm_brand_newer</item>
</style>
复制代码

```

下面是分别运行在 Android 8.1 和 12 上的启动画面进场效果，小鸟从左边入场并逐渐放大的动画。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c0708276ad9446d84a4b5903498d166~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

再比如小鸟旋转着放大进场动画。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec70526368b149878cecfaac9b6f6507~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

事实上 Animated Vector Drawable 所支持的 Path 动画更加炫酷和灵活，可以打造更丰富的动画体验，大家可以试试！

### 2.3 优化低版本的进场 Icon

通过对比，可以看到 8.1 的进场效果里确实没有展示 Icon 动画，也没有 Icon 背景和品牌 Logo。为了尽可能和 12 的进场效果接近，可以将低版本主题的图标属性改为 Adaptive Icon，面向 12 的主题才使用动画 Icon。

```
<!-- 默认主题：面向12之前的版本 -->
<style >
    <!-- Adaptive icon drawable -->
    <item >@mipmap/ic_launcher_bird_final</item>
</style>

<!-- 面向12的版本 -->
<style >
    <!-- Animated vector drawable -->
    <item >@drawable/ic_icon_bird_small_animated_translate</item>
    ...
</style>
复制代码

```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ca6a7ddf2fb4d1190d0e926949df53f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

3. 延长启动画面
---------

### 3.1 获取 SplashScreen 定制入口

上述主题配置完就可以实现进场效果了，但为了进一步控制启动画面或定制退场效果，还得取得控制的入口即 SplashScreen 类。SplashScreen 库提供的是`installSplashScreen()`。

```
class MainActivity : ComponentActivity() {
    private val viewModel: GameViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        // Need to be called before setContentView or other view operation on the root view.
        val splashScreen = installSplashScreen()

        setContent { ... }
        
        SplashScreenController(splashScreen, viewModel).apply {
            customizeSplashScreen()
        }
    }
}
复制代码

```

有一点需要留意一下：installSplashScreen() 必须先于`setContentView()`调用，原因在于 install 函数会获取 postSplashScreenTheme 属性指定的主题，并在检查通过后`setTheme`给 Activity。

**需要明确一下：install 函数只是执行一下主题和资源方面的初始化，此刻尚未加载退场使用的视图。**

### 3.2 控制启动画面展示时长

当 App 的第一帧开始描画，启动画面 Window 即`Splash Screen Window`将退出。若描画已经开始，但部分关键逻辑还没执行完，这将影响完整的展示。这时候我们可以适当地延长启动画面，让用户再等一会儿。

Android 12 并没有提供控制启动时长的 API，之前的 Demo 是通过向`ContentView`注册`OnPreDrawListener`回调挂起描画来实现的。（挂起描画间接导致 Splash Screen Window 的延迟退出，进而达到延长启动画面的目的。）

SplashScreen 库提供了专用 API 来处理这种需求，即`KeepOnScreenCondition`。我们需要告知 SplashScreen 需要持续展示的条件，在条件被破坏之前 WMS 将维持它的展示。事实上这个 API 的实现原理跟之前的思路是一致的。

```
class SplashScreenController( ... ) {
    fun customizeSplashScreen() {
        keepSplashScreenLonger()
        ...
    }

    // Keep splash screen showing till data initialized.
    private fun keepSplashScreenLonger() {
        splashScreen.setKeepVisibleCondition { !viewModel.isDataReady() }
    }
}
复制代码

```

可以看到启动画面的展示时间明显变长了。

![](https://z3.ax1x.com/2021/08/13/frpzmn.gif)

4. 定制退场效果
---------

为了使得启动画面能完美过渡到目标画面，退场效果的定制格外重要。而且相较于进场只能定制 Icon 动画，退场则能定制整体和 Icon 的各式动画，空间更大，也更灵活。

### 4.1 执行退场动画

Splash Screen Window 在退出的时候，是执行退场动画的时机。通过 SplashScreen 库提供的`OnExitAnimationListener` API 可以拿到这个时机，它同时会返回启动画面视图，便于后续的定制。

不同于 Android 12，SplashScreen 库启动画面视图统一封装到了`SplashScreenViewProvider`中。这个 API 将依据版本返回对应视图：低版本是自定义的`FrameLayout`，12 则是版本专属的`SplashScreenView`。

```
fun customizeSplashScreen() {
    ...
    customizeSplashScreenExit()
}

// Customize splash screen exit animator.
private fun customizeSplashScreenExit() {
    splashScreen.setOnExitAnimationListener { splashScreenViewProvider ->
        val onExit = {
            splashScreenViewProvider.remove()
        }
        showSplashExitAnimator(splashScreenViewProvider.view, onExit)
        showSplashIconExitAnimator(splashScreenViewProvider.iconView, onExit)
    }
}
复制代码

```

无论运行在哪个版本，通过统一提供的 SplashScreenViewProvider 实例，可以执行一致的动画效果。

比如针对整体视图做缩放和淡出动画。

```
private fun showSplashExitAnimator(splashScreenView: View, onExit: () -> Unit = {}) {
    val alphaOut = ObjectAnimator.ofFloat(
        splashScreenView,
        ...
    )

    val scaleOut = ObjectAnimator.ofFloat( ... )

    AnimatorSet().run {
        duration = defaultExitDuration
        interpolator = AnticipateInterpolator()
        playTogether(scaleOut, alphaOut)
        start()
    }
}
复制代码

```

针对 Icon 视图做缩放、淡出和位移的动画。目的是让小鸟渐渐缩小并上移至游戏画面中，即无缝过渡到游戏区域。

```
private fun showSplashIconExitAnimator(iconView: View, onExit: () -> Unit = {}) {
    val alphaOut = ObjectAnimator.ofFloat(
        iconView,
        ...
    )
    val scaleOut = ObjectAnimator.ofFloat( ... )
    val slideUp = ObjectAnimator.ofFloat( ... )

    AnimatorSet().run {
        ...
        playTogether(alphaOut, scaleOut, slideUp)
        doOnEnd {
            onExit()
        }
        start()
    }
}
复制代码

```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54f81cbb2b7c4f259867965c313906fb~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

**注意**：退场动画结束后一定要调用 remove() 手动移除视图，否则可能会看到启动画面一直盖在 App 上，无法消失。对于 12 来说，这实际上是系统返回的 SplashScreenView Surface 一直残留在画面上，而低版本则是 FrameLayout 仍残存在 ContentView 树里。

### 4.2 优化退场动画时长

App 开始描画的时候，无论进场动画是否执行完，Splash Screen Window 都将退出，同时执行预设的退场动画。 **而设备性能的优劣或负荷状态的不同，又会影响 App 描画的开始时机，所以退场动画的执行时机并不固定，随着设备的状况略微变化。**

*   如果 App 描画得早，进场动画可能还没执行完。为了让用户更快看到目标内容，退场动画可以缩短执行时长，比如直接沿用进场动画的剩余时长
    
*   相反，如果描画得晚，进场动画早结束了。如果退场动画还再占用时间，那将严重耽误用户看到目标内容，造成**启动卡顿**的坏印象。所以这时候，退场动画可以执行很短的固定时长，甚至可以不执行
    

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc779e92cbb7425e860da5f097646e57~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

换句话说，退场动画本意是后台加载的缓冲和过渡，不能为了单纯展示动画牺牲启动体验，可以灵活控制退场的占用时长。

为了便于计算进场 Icon 动画的剩余时长，SplashScreen 库提供了获取其开始时刻和总时长的 API：

```
/**
 * Start time of the icon animation.
 *
 * On API 31+, returns the number of millisecond since the Epoch time (1970-1-1T00:00:00Z)
 *
 * Below API 31, returns 0 because the icon cannot be animated.
 */
public val iconAnimationStartMillis: Long get() = impl.iconAnimationStartMillis

/**
 * Duration of the icon animation as provided in attr.
 */
public val iconAnimationDurationMillis: Long get() = impl.iconAnimationDurationMillis
复制代码

```

下面的代码演示了在退场动画执行前判断下进场动画是否完毕，完毕的话沿用动画剩余的时间，否则放弃执行。

```
private fun getRemainingDuration(splashScreenView: SplashScreenViewProvider): Long {
    // Get the duration of the animated vector drawable.
    val animationDuration = splashScreenView.iconAnimationDurationMillis

    // Get the start time of the animation.
    val animationStart = splashScreenView.iconAnimationStartMillis

    // Calculate the remaining duration of the animation.
    return if (animationDuration == 0L || animationStart == 0L)
        defaultExitDuration
    else (animationDuration - SystemClock.uptimeMillis() + animationStart)
        .coerceAtLeast(0L)
}
复制代码

```

前面说过低版本在进场的时候不支持 Icon 动画，那自然没有必要计算剩余时长。所以运行在低版本上的话，这两个 API 总是返回默认值 0，值得留意！

```
private open class ViewImpl(val activity: Activity) {
    open val iconAnimationStartMillis: Long get() = 0
    open val iconAnimationDurationMillis: Long get() = 0
    ...
}
复制代码

```

5. SplashScreen 实现原理
--------------------

Android 12 的源码尚未公开，下面针对 Jetpack SplashScreen 库的源码进行解读。※之前觉得 SplashScreen 库提供的 API 简单明了，原理应该也不复杂。但深究源码后发现，不少细节和猜测存在出入，还是值得反复思考的。

### 5.1 总体架构

Activity 通过 SplashScreen 库提供的 SplashScreen 实例可以获取到控制启动画面的入口，其内部将依据 OS 版本决定采用 12 的专属 API 还是自定义视图，来展示、延时或移除启动画面。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/73ec38628bc742438e48c63bbcc7284b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 5.2 installSplashScreen

获取 SplashScreen 实例的 installSplashScreen() 将读取并设置目标 Activity 的 Theme。如果运行在低版本上的话，还需要获取 Icon 和 Background 的配置。此刻只是获取，并未将退场用的自定义视图添加上来。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8045f8cde9e24be898a38e1da9d26e9e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 5.3 setKeepVisibleCondition

通过 setKeepVisibleCondition() 可以延长启动画面的展示，无关运行的版本，原理都是向 ContentView 的 ViewTreeObserver 注册`OnPreDrawListener`回调来实现。描画放行的时候，低版本额外需要手动执行退出的回调，12 则由系统自行执行。

这个时候退场用的自定义视图仍然还没添加上来，只是延迟了 Splash Screen Window 的退出而已。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3e1052e2e1a4615a1d2c03d6d182523~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 5.4 setOnExitAnimationListener

setOnExitAnimationListener() 可以监听退场时机，SplashScreen 会将启动画面视图准备好封装到 SplashScreenViewProvider 中，然后在启动画面需要退出的时候，通过 OnExitAnimationListener 接口回调。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4be793e4d8d1487ba7448fb1c4afd12c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

低版本上视图相关的处理比较多，需要 inflate 一个自定义布局并添加到 ContentView 中，然后将 install 准备好的 background 和 icon 反映进来。

面向低版本添加的自定义布局 ：

```
<FrameLayout ...>

  <ImageView
      android:id="@+id/splashscreen_icon_view"
      android:layout_width="@dimen/splashscreen_icon_size"
      android:layout_height="@dimen/splashscreen_icon_size"
      android:layout_gravity="center" />

</FrameLayout>
复制代码

```

### 5.5 adjustInsets 特殊处理

SplashScreenViewProvider 初始化后会额外调用 adjustInsets()，而且只有面向低版本才实现了具体逻辑。一起来研究下这个特殊处理的用意。

先来看下函数的注释：

> Adjust the insets to avoid any jump between the actual splash screen and the SplashScreen View.

字面意思是为了避免启动画面和 SplashView 视图之间发生跳跃，需要调整下视图的参数。好像还不是很理解，再结合下函数的具体实现：

```
private class Impl23(activity: Activity) : Impl(activity) {
    override fun adjustInsets(
        view: View,
        splashScreenViewProvider: SplashScreenViewProvider
    ) {
        // Offset the icon if the insets have changed
        val rootWindowInsets = view.rootWindowInsets
        val ty =
            rootWindowInsets.systemWindowInsetTop - rootWindowInsets.systemWindowInsetBottom
        splashScreenViewProvider.iconView.translationY = -ty.toFloat() / 2f
    }
}
复制代码

```

默认的 adjustInsets() 没有具体实现，只有低版本才有具体实现，这又是为什么？

处理的内容很直白：监听 SplashScreenViewProvider 里存放的 View 的布局变化，从中获取 rootWindowInsets，将覆盖在 Window 的状态栏和导航栏的高度差值取中值，然后将 Icon 视图向上或向下进行移动。

*   前面的原理提到过低版本的进场效果实际上是将一个 Layer Drawable 设置到了`Window Background`上，Icon 在该 Drawable 里的位置是居中的，所以 Icon 在 Splash Screen Window 里也完全居中
*   但退场效果的画面视图并非 Window Background Drawable，而是向 ContentView 中手动添加的`Framelayout`布局。对于布局来说，Icon 视图是居中的，但对于它所属的 Window 来说，因顶部的 Statusbar 和底部的 NavigationBar 的高度不同，最终 Icon 视图在 Window 整体的位置并不完全居中。尤其当设备采用`Gesture Navigation`导航模式的话，Icon 视图偏下的情况更明显
*   12 的退场视图可以说是 Splash Screen Window 的完整拷贝，不是自定义的 ContentView 子布局，不存在这个问题

**总结来说，如果不加干预的话，低版本上从进场的 Window Background 到退场的 FrameLayout 时，App 的 Icon 会发生错位或跳跃的违和感！**

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2075d329d794639a0a6314497e7e524~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 5.6 remove

setOnExitAnimationListener 调用后，SplashScreen 库会将启动画面的视图回调回来，执行完退场动画后需要调用 remove() 手动移除视图，它的原理很简单：

*   12 之前就是将 FrameLayout 从 ContentView 树上移除
*   12 则是调用专用的 API 即 SplashScreenView#remove()

6. API 总结
---------

对 SplashScreen 库几个关键 API 做个整理：

<table><thead><tr><th>API</th><th>说明</th></tr></thead><tbody><tr><td>SplashScreen</td><td>Jetpack 版获取定制启动画面入口的类</td></tr><tr><td>Activity#installSplashScreen()</td><td>覆写 Activity，用以获取定制入口的静态成员函数</td></tr><tr><td>setKeepVisibleCondition</td><td>指定保持启动画面展示的条件</td></tr><tr><td>KeepOnScreenCondition</td><td>实现展示条件的接口</td></tr><tr><td>setOnExitAnimationListener</td><td>监听启动画面的退出时机</td></tr><tr><td>OnExitAnimationListener</td><td>启动画面退出的回调接口</td></tr><tr><td>SplashScreenViewProvider</td><td>定制退场效果的启动画面视图</td></tr></tbody></table>

并和 Android 12 提供的版本做个简单对比：

<table><thead><tr><th>Attr 对比</th><th>Jetpack 版</th><th>Android 12 版</th></tr></thead><tbody><tr><td>指定目标 Activity 主题</td><td>postSplashScreenTheme</td><td>-</td></tr><tr><td>指定动画 Icon</td><td>windowSplashScreenAnimatedIcon</td><td>android:windowSplashScreenAnimatedIcon</td></tr><tr><td>指定启动画面背景</td><td>windowSplashScreenBackground</td><td>android:windowSplashScreenBackground</td></tr><tr><td>指定 Icon 动画时长</td><td>windowSplashScreenAnimationDuration</td><td>android:windowSplashScreenAnimationDuration</td></tr><tr><td>指定 Icon 背景</td><td>-</td><td>android:windowSplashScreenIconBackgroundColor</td></tr><tr><td>指定品牌 Logo</td><td>-</td><td>android:windowSplashScreenBrandingImage</td></tr></tbody></table>

<table><thead><tr><th>API 对比</th><th>Jetpack 版</th><th>Android 12 版</th></tr></thead><tbody><tr><td>启动画面定制入口</td><td>class androidx.core.splashscreen.SplashScreen</td><td>interface android.window.SplashScreen</td></tr><tr><td>入口实例获取</td><td>Activity#installSplashScreen()</td><td>Activity#getSplashScreen()</td></tr><tr><td>启动画面视图</td><td>SplashScreenViewProvider</td><td>SplashScreenView</td></tr></tbody></table>

7. 本文 Demo
----------

开源地址：[github.com/ellisonchan…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fellisonchan%2FComposeBird "https://github.com/ellisonchan/ComposeBird")

适配 Splash Screen 功能后的 Compose 版 Flappy Bird 游戏的整体效果：

![](https://z3.ax1x.com/2021/08/13/frB0eO.gif)

8. 未决事项
-------

通过上面的阐述已经知道：低版本上展示退场效果的启动画面，实质上是临时添加到 App 内部的 FrameLayout。而 12 上显然不是这种方式，通过 Dump 命令发现退场的时候也没有创建额外的 Window。

*   那么 12 上启动画面 Window 退出后提供给 App 定制退场效果的 SplashScreenView 到底是什么？
*   假使这个 “View” 并不属于 App 本身，那么 App 能够访问它并对属性做出改动，是如何实现的？

在决定搁置这个疑惑的时候，偶然发现下 12 上启动画面执行过程中会打印 Splash 相关关键字的系统日志：

> StartingSurfaceDrawer: addSplashScreen com.ellison.flappybird: nonLocalizedLabel=null theme=7f1000e7 task= 334 StartingSurfaceDrawer: window attributes color: ffecfcdd icon android.graphics.drawable.AnimatedVectorDrawable... StartingSurfaceDrawer: fillViewWithIcon surfaceWindowView android.window.SplashScreenView... StartingSurfaceDrawer: Copying splash screen window view for task: 334 parcelable? android.window.SplashScreenView$SplashScreenViewParcelable StartingSurfaceDrawer: Task start finish, remove starting surface for task 334 StartingSurfaceDrawer: Removing splash screen window for task: 334

于是我大胆猜测，`StartingSurfaceDrawer`负责通过`WMS`读取、展示和移除 App 设置的 Splash Screen Window 视图。并在 Window 退出前通过 WMS 通知目标 Activity 的`PhoneWindow`，从 Splash Screen Window 里复原视图，然后将复原得到的 SplashScreenView 添加到 App 画面上。因为 PhoneWindow 直接持有`DecorView`，所以可能直接附着到 DecorView 上去。

StartingSurfaceDrawer 到底是谁、猜测是否正确以及具体的细节，还有待 12 的源码公开后再做深入地研究~

结语
--

起初推测因为 Android 6 以上的版本已是市场主流，才决定让 SplashScreen 库最早兼容到这个版本。后面探究原理的时候，发现其内部调用的 adjustInsets() 处理依赖一个版本 6 才加入的 API，所以又感觉这才是兼容到 6 的真正原因。

兼容到 Android 6 版本是出于占用率的考虑还是源于 API 的限制已不再重要，关键是 Splash Screen 功能已支持足够多的 Android 设备。

**之前你们说 Android 12 全新的 Splash Screen 功能虽然很酷，但不兼容低版本，略显鸡肋。如今有了`Jetpack SplashScreen`库的加持，总该适配起来了吧？**

参考资料
----

[SplashScreen 文档](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fabout%2Fversions%2F12%2Ffeatures%2Fsplash-screen%3Fhl%3Dzh-cn "https://developer.android.google.cn/about/versions/12/features/splash-screen?hl=zh-cn")

[A peek inside Jetpack Core Splashscreen](https://link.juejin.cn?target=https%3A%2F%2Fdev.to%2Ftkuenneth%2Fa-peek-inside-jetpack-core-splashscreen-odo "https://dev.to/tkuenneth/a-peek-inside-jetpack-core-splashscreen-odo")

[Meet the Jetpack Splashscreen API: a definitive guide for splash screens in Android](https://link.juejin.cn?target=https%3A%2F%2Fwww.tiagoloureiro.tech%2Fposts%2Fdefinitive-guide-for-splash-screen-android%2F "https://www.tiagoloureiro.tech/posts/definitive-guide-for-splash-screen-android/")

推荐阅读
----

[Android 12 上全新的应用启动画面，还不适配一下？](https://juejin.cn/post/6962706834889113614 "https://juejin.cn/post/6962706834889113614")

[一气呵成：用 Compose 完美复刻 Flappy Bird！](https://juejin.cn/post/6989432341592539166 "https://juejin.cn/post/6989432341592539166")

[从 Preference 组件的更迭看 Jetpack 的前世今生](https://juejin.cn/post/6973611219395543070 "https://juejin.cn/post/6973611219395543070")

[深度解读 Jetpack 框架的基石 - AppCompat](https://juejin.cn/post/6965860527897575437 "https://juejin.cn/post/6965860527897575437")