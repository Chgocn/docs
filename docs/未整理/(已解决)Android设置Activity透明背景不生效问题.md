> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7159013509496307725)

##### 问题描述

在网上查资料，使用下面设置主题的方法发现怎么都没有效果，一直是黑色的背景，我记得很久以前我也设置过，当时好像是可以的 ######AndroidManifest.xml

```
<activity android:
      android:theme="@style/Theme.AppCompat.Translucent">


```

###### styles.xml

```
<style >
    <item >@android:color/transparent</item>
    <item >true</item>
    <item >true</item>
</style>


```

据说是因为在 AndroidX 中设置非 Theme.AppCompat.Dialog 的主题会默认将背景设置成黑色，这个问题目前还没有去验证，不过尝试了网上各种方案，自己研究出了以下方法，测了几台设备都 OK

##### 解决方案

###### （1）对于直接继承 Activity 的子类

######1.AndroidManifest.xml

```
# 请直接将theme设置成@android:style/Theme.Translucent.NoTitleBar.Fullscreen
<activity android:
      android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">


```

###### （2）对于继承 AppCompatActivity 的子类

###### 1.AndroidManifest.xml

```
# 请直接将theme设置成@android:style/Theme.Translucent.NoTitleBar.Fullscreen
<activity android:
      android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen">


```

> 请注意：上一步的 android:theme 一定要直接设置成这个属性，如果使用继承的方式设置主题很可能会无效，不用担心自己原来的主题用不了，我们将会在 java 代码里重新设置回我们想要的主题

###### 2.styles.xml

```
# 声明透明背景主题, 这里你可以任意继承自己想要的主题
<style >
    <item >@android:color/transparent</item>
    <item >true</item>
    <item >true</item>
</style>


```

###### 3.DialogInputMessageActivity.java

```
public class DialogInputMessageActivity extends AppCompatActivity{
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        /*********************************修改部分Start*************************************/
        //在super.onCreate之前将主题设置回我们定义的透明的主题即可
        setTheme(R.style.Theme_AppCompat_Translucent);
        /*********************************修改部分End*************************************/
        super.onCreate(savedInstanceState);
        setContentView(R.layout.dialog_input_message);
   }
}


```

> 尊重原创，转载请注明出处  
> 本文所参考的文章：  
> [1.Updated to 4.2RC1 and transparent activities do not work ? Suddenly black](https://link.juejin.cn?target=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F67041191%2Fupdated-to-4-2rc1-and-transparent-activities-do-not-work-suddenly-black "https://stackoverflow.com/questions/67041191/updated-to-4-2rc1-and-transparent-activities-do-not-work-suddenly-black")  
> [2.Android: Issue with transparent activity](https://link.juejin.cn?target=https%3A%2F%2Fwww.anycodings.com%2F1questions%2F1669614%2Fandroid-issue-with-transparent-activity "https://www.anycodings.com/1questions/1669614/android-issue-with-transparent-activity")