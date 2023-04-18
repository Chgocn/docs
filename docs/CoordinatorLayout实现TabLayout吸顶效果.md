> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7015432958123180062)

吸顶是目前市场上很多应用都会采用的方式，例如京东、闲鱼、美团、美团外卖、饿了么、链家、贝壳找房等首页都有，如果细心的话，其他主流 App 也都能找到这种效果的实现。先奉上一个效果图

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6fd76da7abc74c059930aaf34709c9e7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

接下来是饿了么的示例：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/df97078fd4864f06b03110b59e5704e6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

再看下闲鱼的：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ce3a2d57238e405694c72ae201bedfb9~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

最后是贝壳找房的（跟链家相似）:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17602d15f6004963b8f31ab6b866994c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

没有对比就没有伤害，是不是比起主流应用也很丝滑？

之前公司项目也有这方面的需求，网上找了很久这种效果，基本都是自定义`RecyclerView`，然后去处理滑动事件，但是实现出来都不是很理想。后来做其他项目，用了一下`CoordinatorLayout`，无意间发现也可以实现吸顶，不得不高呼一声，谷歌爸爸真好！

下面开始正文，我一直认为`CoordinatorLayout`的布局嵌套特别复杂，所以不是很想用，不过做出来的效果... 真香！

先看一下页面布局结构：

```
    <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:id="@+id/coordinator"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <com.google.android.material.appbar.AppBarLayout
            android:id="@+id/appbar"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <com.google.android.material.appbar.CollapsingToolbarLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:titleEnabled="false"
                app:layout_scrollFlags="scroll|exitUntilCollapsed">
                ``````````
            </com.google.android.material.appbar.CollapsingToolbarLayout>

        </com.google.android.material.appbar.AppBarLayout>

        <androidx.appcompat.widget.LinearLayoutCompat
            app:layout_behavior="@string/appbar_scrolling_view_behavior"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
            ````````
        </androidx.appcompat.widget.LinearLayoutCompat>
        
    </androidx.coordinatorlayout.widget.CoordinatorLayout>
复制代码

```

整个父布局是`CoordinatorLayout`，然后下面分了两个子布局，分别是`AppBarLayout`和`LinearLayoutCompat`，`AppBarLayout`下面嵌套的是`CollapsingToolbarLayout`，再下面我简单放了个`AppCompatImageView`，`LinearLayoutCompat`下面是`TabLayout`和`ViewPage2`

下面是完整的布局内容:

```
  <androidx.coordinatorlayout.widget.CoordinatorLayout
        android:id="@+id/coordinator"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <com.google.android.material.appbar.AppBarLayout
            android:id="@+id/appbar"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <com.google.android.material.appbar.CollapsingToolbarLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:titleEnabled="false"
                app:layout_scrollFlags="scroll|exitUntilCollapsed">

                <androidx.appcompat.widget.AppCompatImageView
                    android:id="@+id/iv_image"
                    app:layout_collapseMode="pin"
                    android:scaleType="centerCrop"
                    android:layout_width="match_parent"
                    android:layout_height="@dimen/dp_200"/>

            </com.google.android.material.appbar.CollapsingToolbarLayout>

        </com.google.android.material.appbar.AppBarLayout>

        <androidx.appcompat.widget.LinearLayoutCompat
            app:layout_behavior="@string/appbar_scrolling_view_behavior"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <com.google.android.material.tabs.TabLayout
                android:id="@+id/tab_layout"
                app:tabMode="scrollable"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"/>

            <androidx.viewpager2.widget.ViewPager2
                android:id="@+id/view_pager2"
                android:layout_width="match_parent"
                android:layout_height="match_parent"/>

        </androidx.appcompat.widget.LinearLayoutCompat>

    </androidx.coordinatorlayout.widget.CoordinatorLayout>
复制代码

```

下面贴上业务代码：

```
class MultiplexFragment : DataBindingFragment<FragmentMultiplexBinding, MultiplexViewModel>() {
    companion object {
        fun newInstance(): MultiplexFragment {
            return MultiplexFragment()
        }
    }
    private val strArray = arrayOf("关注", "推荐", "视频", "直播", "图片", "段子", "精华", "热门")

    override fun initView(view: View, savedInstanceState: Bundle?) {
        mViewData?.apply {
            Glide.with(getThis())
                .load("https://wx2.sinaimg.cn/mw690/002Po4pSly1grt79wku06j61jk0rskjl02.jpg").into(ivImage)
//            这种用法当fragment不会有内存泄露 虽然fragment很多 但是每次都是用的RecyclerFragment.newInstance()
            viewPager2.adapter = object :FragmentStateAdapter(getThis()){
                override fun getItemCount(): Int = strArray.size

                override fun createFragment(position: Int): Fragment {
                    return RecyclerFragment.newInstance()
                }
            }
            TabLayoutMediator(tabLayout,viewPager2){tab,position->
                tab.text = strArray[position]
            }.attach()
        }
    }

    //配置当前页面的内容 各项参数都可为空
    //BR.xxxxViewModel是kotlin-kapt插件默认生成的 对应xml文件里的xxxxViewModel
    override fun getDataBindingConfig(): DataBindingConfig? {
        return DataBindingConfig(layoutId = R.layout.fragment_multiplex,variableId = BR.MultiplexViewModel,
            vmClass = MultiplexViewModel::class.java)
    }
}

复制代码

```

以上就是实现吸顶效果的所有代码了，急着用的童鞋可以直接拿过去交差了，不急的可以接着往下看，我们不仅要知其然，还要知其所以然。

刚开始了解`CoordinatorLayout`，是因为它的折叠效果，用它来做吸顶不过是意外发现。这其中转变的一个重要属性就是`app:layout_collapseMode="pin"`，它还有另一个属性`app:layout_collapseMode="parallax"`我们将 demo 中的属性改为`parallax`就可以看到折叠效果：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/603d6f750583465aa2c074ca823cb84b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

可以看到`parallax`属性会将图片以折叠的效果隐藏，`pin`属性会将图片以向上滚动的效果隐藏。

关于`CoordinatorLayout`的更多属性可参考： [CoordinatorLayout 学习](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Feec5a397ce79 "https://www.jianshu.com/p/eec5a397ce79")

demo 的 github 地址：

[Android Jetpack 最佳实践！结合 MVVM 快速开发，助力开发者深入理解 Jetpack！](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2F932707629%2FTinMvvm "https://github.com/932707629/TinMvvm")