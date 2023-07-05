> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7204389419700518967)

1 Gradle 的一些概念
--------------

Android Studio 默认的构建工具为 Gradle。在使用 Gradle 的过程中会碰到一些概念，理清他们的含义关系对我们使用和深入理解 Gradle 至关重要。

### 1.1 Gradle 、AGP(Android Gradle Plugin)、 buildTools 分别是什么，他们之间什么关系？

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42a8234c96514c6693ca865173f78386~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 1.1.1 Gradle

Gradle 是基于 JVM 的构建工具。他本身使用 jave 写的，gradle 的脚本也就是 build.gradle 通常是用 groovy 语言。

#### 1.1.2 Android BuildTools

Android SDK Build-Tools 是构建 Android apk、AAR 文件等 Android 平台产物所需的一个 Android SDK 组件，安装在 `<sdk>/build-tools/` 目录下。

```
//build-tools/
├── ⋮
├── aapt2
├── d8
├── apksigner
├── zipalign
├── ⋮
​


```

*   AAPT2:Android 资源打包工具）是一种构建工具，Android Studio 和 Android Gradle 插件使用它来编译和打包应用的。AAPT2 会解析资源、为资源编制索引，并将资源编译为针对 Android 平台进行过优化的二进制格式
*   apksigner: 工具为 APK 签名，并确保 APK 的签名将在该 APK 支持的所有版本 Android 平台上成功通过验证
*   d8:Android Gradle 插件使用该工具来将项目的 Java 字节码编译为在 Android 设备上运行的 DEX 字节码
*   Zipalign: 在将 APK 文件分发给最终用户之前，先使用 `zipalign` 进行优化

#### 1.1.3 Android Gradle Plugin

是基于 Gradle 的插件，Android Gradle 主要就是调用 [Android BuildTools] 这个包里的工具实现编译打包 Apk 等功能。

注: AGP3.0.0 或更高版本，插件会使用默认版本的 build Tools 无需指定。

```
android { buildToolsVersion "33.0.1"}


```

### 1.2 gradleWrapper、gradle-user-home、GradleHome

#### 1.2.1 GradleWrapper

项目的根目录的 gradle/wrapper 下

```
    /gradle/wrapper/
    ├── gradle-wrapper.jar 
    └── gradle-wrapper.properties 


```

负责下载管理特定版本的 gradle，确保不同环境中项目运行在相同个 gradle 版本里，还能达到复用效果。

*   gradlewraper 目录下的 gradle.jar 负载下载 gradle
*   gradle-wrapper.properties 内指定 gradle 的版本，下载地址，本地位置等配置信息。

#### 1.2.1 GRADLE-USER-HOME

```
    gradle-user-home
    ├── caches  // modules-2和jars-3缓存依赖和产物，且各个版本各个项目中共用
    │   ├── jars-3 
    |   ├── transforms-3
    │   └── modules-2 
    ├── wrapper //存放不同版本的gradle
    │   └── dists 
    │       ├── ⋮
    │       └── gradle-7.4-bin
    |           └── xxxxxxxx
    |                └──gradle-7.3.3 //其实这是gradle home路径
    └── gradle.properties 


```

默认在位置位于 /.gradle。这个目录下包含用户自定义的设置、maven 本地仓库、gradle wrapper 版本、gradle 插件和一些缓存。其中该路径下 caches 文件夹有 repo 缓存也就是项目的依赖缓存，这个缓存各个项目是公用的，提升编译速度。

#### 1.2.3 Gradle Home

```
gradle-home
├── bin
│   ├── gradle.bat
│   └── gradle
├── init.d
│    ├──..
│    └── xx.gradle
└── lib
    ├──...
    ├── gradle-wrapper-7.3.3.jar
    ├── gradle-tooling-api-7.3.3.jar
    ├──...
    └── plugins
        ├── ...
        ├── gradle-publish-7.3.3.jar
        ├── commons-codec-1.15.jar  
        └──...


```

其实就是 gradle 的安装目录，里面主要包括 Gradle 的可执行文件、库文件、插件等，gradle 执行时会自动加载使用安装目录内的文件插件。

2 Gradle 生命周期
-------------

Gradle 执行构建是有生命周期的，分三个阶段初始化阶段（Initialization Phases），配置阶段 (Configuration Phases) 和执行阶段(Execution Phases)。

#### 2.1 初始化阶段

*   执行 init.gradle 脚本: init.gradel 脚本可以放置在 gradle-user-home 目录下、gradle-user-home/init.d / 和 gradle-home/init.d / 目录下，其中后两者下的脚本名称只要一. gralde 结尾即可。他们的执行顺序为依次执行。在脚本里我们可以设置一些个性化的配置，如配置 repo 源、鉴权等。
*   执行 setting 脚本: gradle 会嗅探项目里的 settings 文件，settings 文件中定义哪些项目参与到构建中。在 settings.gradle 文件中可以设置一些对所有参与的构建的项目做配置，也可以有针对性的做配置。
*   创建 project 实例：为参与到构建中的项目创建 project 实例。我们在项目的 build.gradle 脚本里使用的 project 对象就是在这个时机创建的。

#### 2.2 配置阶段

*   应用插件：下载应用声明的插件，脚本里有两种写法一种是 apply plugin 和 plugins{}。他们的区别暂不作展开。
*   配置属性：给插件做参数配置，我们最熟悉的 android{} 闭包其实就是对 AGP 里的 com.android.application 插件做参数配置。
*   应用依赖：gradle 脚本中声明使用的依赖也会在配置阶段下载应用。
*   构建有向无环 task 任务图。

* _注意_：* 执行任何 task 都会走初始化阶段和配置阶段

#### 2.3 执行阶段

执行上一个阶段也就是配置阶段构造好的有向无环图任务。

```
tasks.register('test') {
  println '该语句会在配置阶段执行'
    doLast {
        println '该语句会在执行阶段运行'
    }
}


```

3 监听 Gradle 生命周期回调
------------------

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e58d6fa966094c5f99b9e9a87cc674b0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

#### 3.1 监听初始化阶段回调

初始化阶段主要有两个能接收回调的地方 settingEvaluated{} 和 gradle.projectsLoaded {}

```
 //settings.gradle配置执行完成
    gradle.settingsEvaluated {
        ...
    }


```

settingEvaluated{}setting 创建加载成功。在此之前有几项 gradle 有几项重要的工作已经完成。

*   gradle.properties 加载完成
*   init 脚本执行完成
*   Settings 对象创建成功，并且从 gradle.properties 加载的值也注入到了 settings 中

```
   // 所有 Project 对象创建（注意：此时 build.gradle 中的配置代码还未执行）
   gradle.projectsLoaded {
       ...
   }


```

初始化阶段还有扫描项目下的所有包含位于根 project 和子 project 中的 build.gradle 并对应生成 Project 对象，对象创建完成之后就会调用 gradle.projectsLoaded{}，**但注意此时 build.gradle 中的配置代码还未执行。**

#### 3.2 配置阶段的回调

配置阶段会对项目下的每个 build.gradle 进行配置，如上图，beforeProject{}/project.beforeEvaluated{} 和 afterProject{}/after.beforeEvaluate 会执行多次，这取决于构建项目下有几个 project。

*   beforeProject{}/project.beforeEvaluated{}：会在每个 project 配置之前调用.
*   afterProject{}/after.beforeEvaluate：会在 build.gradle 脚本 "从头跑到尾" 之后调用。
*   当所有的 gradle.build 都执行完毕后，会调用 projectEvaluted{}
*   最后，task 图构建完成后，回调 gradle.taskGraph.whenReady {} 和 gradle.taskGraph.taskExecutionGraphListener.graphPopulated()

#### 3.3 hook 执行阶段

每个 task 执行前后，我们也有方式去监听：主要是使用 gradle.taskGraph.beforeTask{} 和 gradle.taskGraph.afterTask {}，如下：

```
gradle.taskGraph.beforeTask { Task task ->
    println "executing $task ..."
}
​
gradle.taskGraph.afterTask { Task task, TaskState state ->
    if (state.failure) {
        println "FAILED"
    }
    else {
        println "done"
    }
}


```

*   **!!! 注意:** 这些方法在 gradle8.0 已经直接被移除掉了统统不能用，原因简单的说就是影响 cofigurationCache 具体看这里: [task_execution_events](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fupgrading_version_7.html%23task_execution_events "https://docs.gradle.org/current/userguide/upgrading_version_7.html#task_execution_events")
*   替代的方式：使用 [build_service](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fbuild_services.html%23concurrent_access_to_the_service "https://docs.gradle.org/current/userguide/build_services.html#concurrent_access_to_the_service")

4 Gradle 项目结构
-------------

```
root-project
├── buildSrc
|   ├── ...
│   └── build.gradle
├── sub-project
│   └── build.gradle
├── build.gradle
├── settings.gradle
├── gradle.properties
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew(Unix Shell script)
└── gradlew.bat(Windows batch file)


```

### 4.1 buildSrc

*   buildSrc 是位于 gradle 项目根目录的特殊目录，我们可以在这编写 Gradle 的插件和任务。
*   这里面的代码会在 gardle 的配置阶段执行。

### 4.2 gradle.Properties

*   在 gradle-user-home 目录下，项目根目录下和子项目目录下（和 build.gradle 同级）下都可以存在。
*   gradle.properties 定义的值有些时配置 gradle 环境属性，也可以添加自己自定义的属性，在我们的构建脚本中可以直接访问这些值。

```
//gradle.properties中
mVersionName=1.12.1
​
//build.gradle
println("mVersion name in gradle.properties ${mVersionName}")


```

### 4.3 build.gradle 和 settings.gradle 文件

Gradle 世界里经过初始化阶段后，build.gradle 文件会对应生成 Poject 对象，settings.gradle 文件会生成 Settings 对象，其实还有 Gradle 对象，每次构建启动时就会创建。

5 plugin 与 task
---------------

### 5.1 Gradle Plugin

Gradle 是一个构建框架，本身不具备特别强的构建能力，具体构建能力依托于 Gradle Plugin ，比如要打包 APK 就要依托 AGP。AGP 内部通过调用 Android Build Tool 去具体执行诸如编译 class，打包资源等任务。

#### 5.1.1 Apply plugin 方式引入

Gradle Plugin 的是使用首先是引入，就跟我们在项目里需要某个第三方库需要引入一样。在跟目录的 build.gradle 中就可声明这些依赖，然后在构建脚本里声明应用，然后做配置。

```
//in rootproject/build.gradle
buildscript {
  ...
   dependencies {
     classpath:'com.android.tools.build:gradle:x.x.x'
   }
}
​
//in subProject/build.gradle
apply plugin: 'com.android.application'//用于打包生成Apk
android {//构建Apk的配置
}
​
//in subProject2/build.gradle
apply plugin: 'com.android.library'//用于打包产出AAR
android{ //构建产生aar配置
}


```

如果在脚本 gradle 脚本中想使用一个第三方库，也需要通过 classpath 引入，然后就可以在脚本中使用了，示例如下。

```
//in root project build.gradle
buildscript {
   dependencies {
     classpath: 'com.squareup.okhttp3:okhttp:3.14.9'
   }
}
import okhttp3.OkHttpClient
task("checkClasspath").doFirst {
   //在脚本中使用引入了的okhttp
    def client = new OkHttpClient()
  ..,
}


```

#### 5.1.2 plugin{} 脚本块引入

以上这种使用 plugin 的方式是分两步【引入 + 应用】，gradle 还有引入和应用一步搞定的方式。

```
plugin{ id 'com.android.library' version 'x.x.x' }


```

默认的这种不需要引入就能应用插件的方式，插件来源只能是 gradle 插件官网 [plugins.gradle.org/](https://link.juejin.cn?target=https%3A%2F%2Fplugins.gradle.org%2F "https://plugins.gradle.org/")，如果想使用自己的插件需要在 pluginManagement 中配置源如下

```
//settings.gradle
pluginManagement {
    repositories {
        maven {
            url './maven-repo'
        }
    } 
 }


```

#### 5.1.3 其他

Gradel 插件分两种：二进制插件和脚本插件; 二进制插件就是被打包成了 jar 包的插件。脚本插件. gradle 文件可以被作为插件使用。这种插件的使用只能通过 apply plugin 方式不能使用 plugin{} 的方式。

//config.gradle 中

```
ext {
    mVersion = '1.1.0'
}


```

//build.gradle

```
apply from: "../config.gradle"
println("mVersion${ext.mVersion}")


```

### 5.2 Task

#### 5.2.1 执行动作

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9bfb872a302c4939b24d9a27013478db~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

*   上文也提到过，gradle 的一次构建，就是一系列 task 的执行，task 就是 gradle 的一个执行单元。
*   一个 gradle 项目中包含若干个 project，每个 project 中包含若干个 task。
*   一个 gradle task 里面会包含若干个 action。

#### 5.2.2 创建 task

task 一般都定义在 plugin 中，我们也可以自己定义 task，给其配置名字，描述，分组，action，依赖关系等。

```
//build.gradle
project.tasks.register("aTask") {
    println 'in configuration phase'
    it.description("this is a sample task")
    it.dependsOn("hello")
    group("build")//IDE右边的Gradle快捷工具栏中，会看到该task被归类到build分组内，方便查找
    description "this is a gradle hello sample" //描述
    it.doLast {
        println("do action >>1") //向当前task的action链路后追加action
    }
    it.doFirst {
        println("do aciton >>2")//向当前task的action链路前查action
    }
}
​
project.tasks.register("hello") {
    it.doLast {
        println("hello task is excuted-")
    }
}
​


```

以上只是一个简单的例子，task 的创建使用可以单开一个章节。

6 结
---

本文主要是在一个可能比较高的视角，谈了谈 gradle 相关的概念，生命周期特性，插件 task 等。掌握了这些之后，还有一些更深入更细致的话题值得探索。比如

*   自定义插件，细致的 Task 详解
*   gradle tansformer
*   构建性能如何提升
*   AGP 常见的配置
*   Gradle 项目实战

今后会写系列文章去一一探索，**关注作者获取更新**。

**写文不易，错误不足之处望不吝赐教，点赞鼓励**