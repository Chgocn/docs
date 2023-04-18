> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/105885c44e49)

随着 iPhone X 的发布，一众 Android 厂商也开始跟风推出自己的刘海屏，全面屏手机，整个手机行业已经从传统的 16:9 逐渐往 18:9，18.5:9 等屏幕比例过渡，其中也给我们开发着带来了一个比较棘手的问题，那就是 Splash Screen 对全面屏手机的适配问题。市面上通用的方案就是创建适配多种分辨率的 drawable 文件夹，这种方案有缺点明显，不能完美的适配各种宽高比的屏幕。于是乎就有了下面这种方案就是使用 layer-list 来进行适配，先看效果：

<table><thead><tr><th>16:9 屏幕</th><th>全面屏不带刘海</th><th>全面屏带刘海</th></tr></thead><tbody><tr><td><img class="" src="http://upload-images.jianshu.io/upload_images/1960657-3d70f2b40b693a59.png" style="width: auto;"></td><td><img class="" src="http://upload-images.jianshu.io/upload_images/1960657-f9f279e002fef329.png" style="width: auto;"></td><td><img class="" src="http://upload-images.jianshu.io/upload_images/1960657-1bbb12d2ee69de00.jpg" style="width: auto;"></td></tr></tbody></table>

可以看出，无论是普通的 16:9 屏幕还是全面屏，刘海屏都可以完美适配。

上代码：

### 在 drawable 目录创建一个 layer-list，如 splash.xml

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 背景颜色 -->
    <item android:drawable="@android:color/white" />

    <!--注意此处的bottom要和activity_splash.xml中的logo图marginBottom的值保持一致-->
    <item android:bottom="40dp">
        <!-- 图片 -->
        <bitmap
            android:gravity="center|bottom"
            android:src="@drawable/splash_logo" />
    </item>
</layer-list


```

### 在 style.xml 中为 SplashActivity 创建一个 theme

```
<style >
    <item >@drawable/splash</item>
    <item >true</item>
</style>


```

### 在 values-v21 中为 SplashActivity 创建一个和 SplashTheme 同名的一个 theme

创建这个 theme 的主要目的是为了适配有 navigation bar 的手机，不让 windowBackground 延申到 navigationBar 的区域内

```
<style >
    <item >@drawable/splash</item>
    <item >true</item>
    <!--不让windowBackground延申到navigation bar区域-->
    <item >false</item>
</style>


```

### 在 AndroidManifest.xml 中定义 SplashActivity 的 theme 为 SplashTheme

```
<activity
    android:
    android:theme="@style/SplashTheme">
    <intent-filter>
        <action android: />
        <category android: />
    </intent-filter>
</activity>


```

### SplashActivity 布局（参考）

需注意的是 logo 占位图的 marginBottom 要与上面 layer-list 中的 bottom 属性的值保持一致，这就可视觉上和 windowBackground 中的 logo 的位置保持完全一致。使得下面的 logo 在任意一种屏幕下都能保持居中的位置。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ImageView
        android:id="@+id/iv_ad"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:contentDescription="广告图"
        android:scaleType="centerCrop"
        android:src="@drawable/ad_img" />


    <ImageView
        android:id="@+id/logo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="40dp"
        android:src="@drawable/splash_logo"
        android:contentDescription="logo图，占位"/>

</LinearLayout>


```

### 刘海屏适配

需解决的问题是让布局延申到刘海（状态栏）区域，方法如下：  
针对 Android p，在 values-v28 中为 SplashActivity 创建一个和 SplashTheme 同名的一个 theme，

```
<style >
    <item >@drawable/splash</item>
    <item >true</item>
    <!--不让windowBackground延申到navigation bar区域-->
    <item >false</item>
    <!--适配Android P刘海屏-->
    <item >shortEdges</item>
</style>


```

SplashActivity 的 onCreate 中

```
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    Window window = getWindow();
    View decorView = window.getDecorView();
    decorView.setOnApplyWindowInsetsListener(new View.OnApplyWindowInsetsListener() {
        @TargetApi(Build.VERSION_CODES.KITKAT_WATCH)
        @Override
        public WindowInsets onApplyWindowInsets(View v, WindowInsets insets) {
            WindowInsets defaultInsets = v.onApplyWindowInsets(insets);
            return defaultInsets.replaceSystemWindowInsets(
                    defaultInsets.getSystemWindowInsetLeft(),
                    0,
                    defaultInsets.getSystemWindowInsetRight(),
                    defaultInsets.getSystemWindowInsetBottom());
        }
    });
    ViewCompat.requestApplyInsets(decorView);
    //将状态栏设成透明，如不想透明可设置其他颜色
    window.setStatusBarColor(ContextCompat.getColor(this, android.R.color.transparent));
}


```

华为手机  
在 AndroidManifest.xml 中的 SplashActivity 的节点添加如下 meta-data

```
<meta-data android:/>


```

### [参考 demo](https://github.com/dueeeke/EasySplash)

参考资料：  
[Android 开发文档](https://developer.android.google.cn/guide/practices/screens-distribution?hl=zh-cn)  
[华为刘海屏手机安卓 O 版本适配指导](https://developer.huawei.com/consumer/cn/devservice/doc/50114?from=timeline)