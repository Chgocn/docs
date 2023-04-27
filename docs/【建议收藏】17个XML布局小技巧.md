> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7145861715798802462)

我报名参加金石计划 1 期挑战——瓜分 10 万奖池，这是我的第 1 篇文章，[点击查看活动详情](https://s.juejin.cn/ds/jooSN7t "https://s.juejin.cn/ds/jooSN7t")

前言
==

我们开发时接触最多的就是`xml`布局了，还记得我们写 Android 的第一个`Hello World`吗，就是通过 activity_main.xml 显示出来的。

虽然 xml 写的很多，而且也没有什么技术难度，但是，这也往往是我们最容易忽略的地方，写 xml 不难，写出好的 xml 还是得下点功夫了。

什么算是好的 xml 布局呢，我认为核心有两点，一个是`提升开发效率`，另一个是`提升app性能`。围绕着这两点，我也精心整理出了`17`个 xml 布局小技巧，下面一起来看看都有哪些，你又掌握了几个呢？

Space
=====

官网是这么介绍的：

Space 是一个轻量级的 View 子类，可用于在通用布局中创建组件之间的间距。

为什么说是轻量级呢，是因为 Space 的`draw`方法是空的，也就是什么都不绘制，只有 onMeasure 方法测量宽高。

来看下源码：

```
public final class Space extends View {

    /**
     * Draw nothing.
     *
     * @param canvas an unused parameter.
     */
    @Override
    public void draw(Canvas canvas) {
    }

    //...

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        setMeasuredDimension(
                getDefaultSize2(getSuggestedMinimumWidth(), widthMeasureSpec),
                getDefaultSize2(getSuggestedMinimumHeight(), heightMeasureSpec));
    }
}
复制代码

```

所以 Space 作用于组件之间的间距时，绘制效率更高，特别是在需要动态修改间距时，这点尤为体现。

比如你要动态修改组件的 margin，如果用 Space 来当间距，只需要修改 Space 的宽度或高度即可，因为减少了绘制流程，所以比重绘其他组件更高效。

使用起来也很简单：

```
<Space
    android:id="@+id/space"
    android:layout_width="20dp"
    android:layout_height="20dp"/>
复制代码

```

如果你想，Space 完全可以替代 margin，但是不一定能替代 padding，因为 padding 是内边距，假如 padding 有背景色的话，就不能用 Space 代替了，因为 Space 的 draw 方法什么都不绘制的原因，所以也不会有背景色，除非背景色是在父 view 里设置的。

GuideLine
=========

ConstraintLayout 自 2018 年发布第一个正式版本以来，已经 4 年多了，它通过扁平化的布局方式，有效的解决了层级嵌套的问题，不仅比 RelativeLayout 更灵活，而且性能上更佳，再配合上可视化工具拖拽编辑，效率上也有大大的提升，如果你还没有用上，建议你一定要尝试一下。

而在使用 ConstraintLayout 的过程中，我发现有些同学总是会忽略`GuideLine`，尽管 ConstraintLayout 已经非常好用了，但是有些布局仍然显得有些「笨拙」。而如果你能妙用 GuideLine，你会发现，布局越来越简单，适配也越来越方便。

GuideLine 是 ConstraintLayout 布局的辅助对象，仅用于布局定位使用，它被标记了`View.GONE`，并不会显示在设备上。

来看下源码：

```
public class Guideline extends View {
    public Guideline(Context context) {
        super(context);
        super.setVisibility(View.GONE);
    }

    public Guideline(Context context, AttributeSet attrs) {
        super(context, attrs);
        super.setVisibility(View.GONE);
    }

    public Guideline(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        super.setVisibility(View.GONE);
    }

    public Guideline(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr);
        super.setVisibility(View.GONE);
    }

    //...
    @SuppressLint("MissingSuperCall")
    @Override
    public void draw(Canvas canvas) {

    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        setMeasuredDimension(0, 0);
    }
    //...
}
复制代码

```

标记为 View.GONE 是这句`super.setVisibility(View.GONE)`设置的默认值，不显示还是因为 draw 方法为空，跟上面的 Space 同出一辙。

GuideLine 可以通过 3 种不同的方式来辅助定位：

*   layout_constraintGuide_begin 指定距布局左侧或顶部的固定距离
*   layout_constraintGuide_end 指定距布局右侧或底部的固定距离
*   layout_constraintGuide_percent 指定布局宽度或高度的百分比

同时也可以指定不同的方向：

*   horizontal 垂直参考线
*   vertical 水平参考线

下面简单演示一下效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2433e760cb58461d834c24b98f6429e6~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

1.  箭头所指处即创建 GuideLine 的地方，当然也不止 GuideLine，比如还有 Barrier
2.  第一个红框里是水平参考线，70% 定位，用百分比能很好的解决适配问题，而我们常规的做法是使用 LinearLayout 嵌套然后设置子 view 的 weight，虽然嵌套一层不多，但那也是嵌套，就像怀孕一样，你不能说只怀了一点点...
3.  第二个红框里是垂直参考线，距离左边 30dp，这种情况适合多个子 view 向一个目标距离对齐，同样减少了层级嵌套问题，省得再嵌套一层设置 padding，或者多个子 view 分别设置 margin。而右边如果想要指定一个位置换行，可以了解一下 Barrier~

xml 代码就不贴了，已上传到 Github，[点击查看](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fyechaoa%2FMaterialDesign "https://github.com/yechaoa/MaterialDesign")

include
=======

当我们在写一个复杂的页面时，xml 代码可能有几百行甚至几千行，阅读起来总是很麻烦，如果又有很多的 RelativeLayout 嵌套的话，各个组件之间依赖关系错综复杂，看起来更是头大，这时候就可以考虑抽取一波，用总分总的模式分为 header、content、footer，进一步把内容区抽成一个一个的独立的子 layout，然后使用 include 标签把它们分别引进根布局，这就跟我们项目架构设计一个意思，一个壳工程加 n 个子模块。子 layout 只需要负责处理好自己内部的布局，统筹交给父 layout，这样总体就比较清晰，想了解细节再去看子 layout 即可。

比如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1879f2e063744b498fbaa02d39c887ee~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

```
<include layout="@layout/content_scrolling"/>
复制代码

```

content_scrolling 即是我们抽出去的子 layout。

tools:showIn
============

这个属性一般是配合 include 标签使用的。当我们把子 layout 抽出去之后，它的布局是相对独立的效果，但是总归要 include 到根布局的，如果能在子 layout 布局的时候看到它在父 layout 里面的效果，那就事半功倍了。

上面的 content_scrolling.xml：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/635b65e854b64a2db997d20b07dab85b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

实际上布局只有一个 TextView，但是在预览视图中还可以看到 FloatingActionButton，这就是使用了 tools:showIn 属性，当子 layout 嵌入在父 layout 中时，只需要使用`tools:showIn`在子 layout 的根布局指定父 layout，就可以实时预览在父 layout 中的效果了。

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.core.widget.NestedScrollView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:context="com.yechaoa.materialdesign.activity.CollapsingToolbarActivity"
    tools:showIn="@layout/activity_collapsing_toolbar">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="@dimen/text_margin"
        android:text="@string/large_text"/>

</androidx.core.widget.NestedScrollView>
复制代码

```

即：tools:showIn="@layout/activity_collapsing_toolbar"。

ViewStub
========

ViewStub 是一个轻量级的条件视图组件。在做类似`页面秒开`这类性能优化时，是比较常见的`延迟加载`手段。

轻量级是因为 ViewStub 跟 Space 一样 draw 方法为空。

条件视图的场景比如，当我们需要根据条件判断来显示哪个 view 的时候，一般都会把每个场景的 view 都写在页面中，然后根据条件分别设置 view 的 visibility，这样做的缺点是，即使 view 是 View.GONE，但是在页面渲染加载的时候仍会实例化创建对象，并初始化它的属性，很明显这是浪费资源的，所以这个时候用 ViewStub 是一个很好的优化手段。

当我们明确知道需要显示哪个 view 的时候，通过 ViewStub 把实际视图`inflate`进来，这样就避免了资源浪费。

只有调用了 ViewStub.inflate() 的时候布局才会加载，才会创建对象实例化。

示例：

```
    <ViewStub
        android:id="@+id/stub_import"
        android:inflatedId="@+id/panel_import"
        android:layout="@layout/progress_overlay"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom" />
复制代码

```

inflate：

```
    findViewById<View>(R.id.stub_import).visibility = View.VISIBLE
    // or
    val importPanel: View = findViewById<ViewStub>(R.id.stub_import).inflate()
复制代码

```

tools:text
==========

TextView 是我们使用的最多的一个组件了，经常有这样的需求，“标题显示不下用... 代替”，是不是很熟悉。

如果标题是一个动态数据，默认显示 app name，拿到数据后再更新。这种情况，一般都是在 android:text 里面加字符来调试，调试完了再改成默认的 app name，其实也不用这么麻烦，直接默认 app name，然后使用 tools:text 属性就可以预览字符超限的效果。

```
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/item_textView"
    android:layout_width="100dp"
    android:layout_height="wrap_content"
    android:maxLines="1"
    android:ellipsize="end"
    android:text="TextView"
    tools:text="TextViewTextView" />
复制代码

```

默认文案还是用 android:text 显示，超限的效果用 tools:text 预览即可，实际效果还是 android:text，tools:text 只是方便我们调试预览，提高效率，减少编译等待时间。

tools:visibility
================

这个属性是用来预览不显示的 View。

比如在 “个人中心” 页面需要在昵称后面给个文案提示“开通会员”，默认不显示，即 android:visibility="gone"，判断不是会员后才显示文案，但是在开发的过程中需要调试会员和非会员的两种显示效果，即可以通过 tools:visibility="visible" 来预览显示的效果，省得再编译运行造数据了，方便又提效。

代码示例：

```
        <TextView
            tools:visibility="visible"
            android:visibility="gone"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@color/greenPrimary"
            android:gravity="center"
            android:padding="10dp"
            android:text="开通会员"
            android:textColor="@color/white" />
复制代码

```

RecyclerView
============

RecyclerView 也是我们使用非常高频的一个组件了，一般会在 xml 中这么定义 RecyclerView：

```
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
复制代码

```

效果是这样的：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ca801b2c47f4fd99f588f61e7eb862d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

这样其实完全看不出 RecyclerView 在页面中显示的效果，只能每次编译运行看效果，而每次编译运行无疑会花费我们很多宝贵的时间，下面就介绍几个可以帮助大家`提效`的属性。

tools:listitem
--------------

我们可以通过设置 tools:listitem 属性来预览 item 的显示效果，tools:listitem 属性指定的是一个 layout

```
tools:listitem="@layout/item_main"
复制代码

```

效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/940e9abbb75247eeb5e47b797c637185~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

tools:itemCount
---------------

预览 item 在 RecyclerView 中显示设置数量的效果，比如：

```
tools:itemCount="3"
复制代码

```

即会显示 3 个 item 的效果。

tools:listheader
----------------

```
tools:listheader="@layout/item_header"
复制代码

```

效果同 tools:listitem

tools:listfooter
----------------

效果同 tools:listitem

```
tools:listfooter="@layout/item_footer"
复制代码

```

app:layoutManager
-----------------

上面 RecyclerView 的效果是默认垂直方向的，我们都知道 RecyclerView 必须要设置一个 layoutManager 才可以显示出来，我们通常会用代码来设置，比如：

```
mBinding.recycleView.layoutManager = GridLayoutManager(this, 2)
复制代码

```

实际上 layoutManager 也是可以在 xml 中通过`app:layoutManager`属性来设置的，比如：

```
app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
复制代码

```

默认的 LinearLayoutManager 是垂直方向的，如果我们想要改方向可以通过`android:orientation`属性，比如：

```
android:orientation="horizontal"
复制代码

```

这样就可以在编写 xml 的时候顺手就加上了，既可以查看预览效果，也避免了代码忘记设置的尴尬情况。

app:spanCount
-------------

上面的示例中 RecyclerView 的 layoutManager 指定了 LinearLayoutManager，我们还可以指定为 GridLayoutManager，但是 GridLayoutManager 默认的 spanCount 是 1，如果我们需要设置 spanCount 为 2，那该怎么预览呢，这时候就用到了`app:spanCount`属性，可以指定需要显示的列数。

```
app:spanCount="2"
复制代码

```

效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9650a059510a4e7fb9e3035ae0a21ccb~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

android:tint
============

着色器，这个属性在之前的[包体积优化](https://juejin.cn/post/7016225898768629773#heading-31 "https://juejin.cn/post/7016225898768629773#heading-31")中有提到，可以减少图片数量，从而减小`包大小`。

我们通常会用 ImageView 显示一张图片，比如原本是一个白色的返回 icon，现在另一个地方要用黑色的了，就不需要使用黑白两张图了，而是使用 tint 来修改为黑色即可，当然，也有局限，适合纯色图片。

效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d93fcd2d462d4074b780c7306408b15a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

示例：

```
        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="30dp"
            android:orientation="horizontal">

            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:background="@color/black"
                android:src="@mipmap/ic_back" />

            <ImageView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:background="@color/red"
                android:src="@mipmap/ic_back"
                app:tint="@color/black" />

        </LinearLayout>
复制代码

```

在 appcompat 的高版本中已经改用`app:tint`代替。

代码方式修改 tint：

```
mBinding.imageView.imageTintList = ContextCompat.getColorStateList(this, R.color.greenPrimary)
复制代码

```

除了 tint 还有 backgroundTint，效果同理。

使用场景除了上面的示例外，还可以在点赞、收藏这类场景的显示上使用。

android:divider
===============

LinearLayout 也是我们使用非常高频的一个 Layout，下面介绍两个个少为人知的属性。

相信很多人都用 View 写过分割线的效果，类似这样：

```
        <TextView />

        <View
            android:layout_width="match_parent"
            android:layout_height="1dp"
            android:background="#EEEEEE" />
        
        <TextView />
复制代码

```

如上，当有多个 TextView 之间需要添加分割线的时候，就只能一个一个复制，复制其实也没什么，就是代码看起来不优雅。

其实有个比较优雅的办法，LinearLayout 可以通过`android:divider`属性添加分割线，结合`android:showDividers`属性即可达到效果。

xml：

```
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="20dp"
        android:background="@drawable/shape_radius5_white"
        android:divider="@drawable/shape_divider_linear"
        android:orientation="vertical"
        android:showDividers="middle" >

        <TextView
            style="@style/MyTextView"
            android:text="删除个人信息"
            app:drawableStartCompat="@mipmap/ic_helper" />

        <TextView
            style="@style/MyTextView"
            android:text="注销账户"
            app:drawableStartCompat="@mipmap/ic_helper" />

        <TextView
            android:id="@+id/tv_about"
            style="@style/MyTextView"
            android:text="关于我们"
            app:drawableStartCompat="@mipmap/ic_helper" />

    </LinearLayout>
复制代码

```

shape_divider_linear 是分割线的样式：

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:left="50dp" >
        <shape android:shape="rectangle">
            <solid android:color="#F6F6F6" />
            <size android:height="1dp" />
        </shape>
    </item>
</layer-list>
复制代码

```

效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab32ad0da097444a8cd61e2a5ac4068a~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

showDividers 有 4 个选项：

*   middle 每两个组件间显示分隔线
*   beginning 开始处显示分隔线
*   end 结尾处显示分隔线
*   none 不显示

其实举一反三，除了分割线，View 之间的间隔也可以这么实现，省得每个子 view 都要写 margin。

android:animateLayoutChanges
============================

`animateLayoutChanges`属性是 ViewGroup 里面的，主要是在子 view 的添加和移除时，添加一个默认 300ms 的渐变动画。

代码：

```
android:animateLayoutChanges="true"
复制代码

```

效果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ccc040aafbd4722998764ff5fa6aba7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

默认添加移除操作是比较生硬的，加上动画之后体验上会好很多。

当然，如果你想修改默认动画也是可以的。怎么修改？没有比学习源码更直接的了。

源码：

```
case R.styleable.ViewGroup_animateLayoutChanges:
    boolean animateLayoutChanges = a.getBoolean(attr, false);
    if (animateLayoutChanges) {
        setLayoutTransition(new LayoutTransition());
    }
    break;
复制代码

```

当 animateLayoutChanges 属性值为 true 时，调用`setLayoutTransition`方法，并传入一个默认的`LayoutTransition`对象。

LayoutTransition 对象用于构造动画，跟一般的动画使用差不多，感兴趣的可以看下[官方文档](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fanimation%2FLayoutTransition%3Fhl%3Den "https://developer.android.google.cn/reference/android/animation/LayoutTransition?hl=en")或者跟下源码。

自定义 LayoutTransition 对象之后，调用`ViewGroup.setLayoutTransition(LayoutTransition)`即可。

android:foreground
==================

```
android:foreground="?android:attr/selectableItemBackground"
复制代码

```

在 Android5.0 以后，给 View 加上这个属性之后，点击时默认会有一个水波纹的效果，一般可点击的 View 默认都有这个效果，比如 Button，一般通常会在自定义的 item view 上加上这个属性用来提升用户体验。

最后
==

如上，本文一共介绍了 17 个在日常编写 xml 的过程中对`提升效率`和`提升性能`的属性，如果你也有心得，欢迎评论补充。

如果本文对你有一丢丢帮助，也感谢点赞支持~

Github
======

[github.com/yechaoa/Mat…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fyechaoa%2FMaterialDesign "https://github.com/yechaoa/MaterialDesign")

相关文档
====

*   [Space](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fkotlin%2Fandroid%2Fwidget%2FSpace%3Fhl%3Den "https://developer.android.google.cn/reference/kotlin/android/widget/Space?hl=en")
*   [Guideline](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroidx%2Fconstraintlayout%2Fwidget%2FGuideline "https://developer.android.google.cn/reference/androidx/constraintlayout/widget/Guideline")
*   [ConstraintLayout](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Ftraining%2Fconstraint-layout "https://developer.android.google.cn/training/constraint-layout")
*   [ViewSub](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Ftraining%2Fimproving-layouts%2Floading-ondemand "https://developer.android.com/training/improving-layouts/loading-ondemand")
*   [tools](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Fstudio%2Fwrite%2Ftool-attributes%3Fhl%3Den "https://developer.android.google.cn/studio/write/tool-attributes?hl=en")
*   [LayoutTransition](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fanimation%2FLayoutTransition%3Fhl%3Den "https://developer.android.google.cn/reference/android/animation/LayoutTransition?hl=en")