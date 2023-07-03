> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [mengdd.github.io](http://mengdd.github.io/Android/2020/01/15/migrate-gradle-build-scripts-to-kotlin/)

> Android Gradle 从 Groovy 迁移到 KotlinAndroid 项目用 Gradle 构建, 其脚本语言之前是 Groovy, 目前也提供了 Kotlin 的支持, 所以可以迁移到 Kotlin. 官方的......

Android 项目用 Gradle 构建, 其脚本语言之前是 Groovy, 目前也提供了 Kotlin 的支持, 所以可以迁移到 Kotlin.

官方的迁移文档: [Migrating build logic from Groovy to Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/)  
说明的是更通用的步骤.

本文通过一个具体的 Android 项目来举例如何迁移, 文后附有 sample.  

[](#名词概念解释 "名词概念解释")名词概念解释
--------------------------

*   Gradle: 自动化构建工具. 平行产品: Maven.
*   Groovy: 语言, 编译后变为 JVM byte code, 兼容 Java 平台.
*   DSL: Domain Specific Language, 领域特定语言.
*   Groovy DSL: Gradle 的 API 是 Java 的, Groovy DSL 是在其之上的脚本语言. Groovy DSL 脚本文件后缀: `.gradle`.
*   Kotlin DSL: 和前者类似, 同样根据 Gradle 的 Java API 构建, 只是替换了语言: Groovy -> Kotlin. Kotlin DSL 脚本文件后缀: `.gradle.kts`.

[](#为什么要迁移 "为什么要迁移")为什么要迁移
--------------------------

优点:

*   可以使用 Kotlin, 开发者可能对这个语言更熟悉更喜欢.
*   IDE 支持更好, 自动补全提示, 重构, imports 等.
*   类型安全: Kotlin 是静态类型.
*   不用一次性迁移完: 两种语言的脚本可以共存, 也可以互相调用.

缺点:

*   据说 Kotlin DSL 会比 Groovy DSL 稍微慢一点: [https://github.com/gradle/kotlin-dsl-samples/issues/902](https://github.com/gradle/kotlin-dsl-samples/issues/902).

[](#迁移步骤 "迁移步骤")迁移步骤
--------------------

### [](#Step-0-环境支持 "Step 0: 环境支持")Step 0: 环境支持

Kotlin DSL 在 Android Studio 上是全面支持的. 确保使用的 IDE 版本较新.

使用最新版的 Gradle, 这样会包含最新版的 Kotlin DSL.

### [](#Step-1-把单引号替换为双引号 "Step 1: 把单引号替换为双引号")Step 1: 把单引号替换为双引号

这一步利用 IDE 的文件内搜索替换功能, 在想要改的`.gradle`文件中, 全局替换`'`到`"`就行.

比如:  

```
dependencies {
    classpath 'com.android.tools.build:gradle:3.5.3'
}
```

变成了:  

```
dependencies {
    classpath "com.android.tools.build:gradle:3.5.3"
}
```

这一步的改动可见:  
[https://github.com/mengdd/KotlinDSLSample/commit/d3fc644e88fb461920a8b60a0430bb42f6a6053e](https://github.com/mengdd/KotlinDSLSample/commit/d3fc644e88fb461920a8b60a0430bb42f6a6053e)

### [](#Step-2-区分属性赋值和方法调用 "Step 2: 区分属性赋值和方法调用")Step 2: 区分属性赋值和方法调用

属性赋值用`=`, 方法调用用`()`.  
有时候分不清是属性赋值还是方法调用, 可以先用`=`试试, 如果报错再改为方法调用.

比如`settings.gradle`在这一步, 由:  

```
include ":app"
rootProject.
```

变成了:  

```
include(":app")
rootProject.name = "KotlinDSLSample"
```

第一行是一个方法调用, 第二行是一个属性赋值.

项目根目录的`build.gradle`中发生了两处变化, 变成了:  

```
dependencies {
    classpath("com.android.tools.build:gradle:3.5.3")
```

和:  

```
task clean(type: Delete) {
    delete(rootProject.buildDir)
}
```

这一步的改动见:  
[https://github.com/mengdd/KotlinDSLSample/commit/b36a508e7d4f1d5d25c23a9772e1cfd9df363fad](https://github.com/mengdd/KotlinDSLSample/commit/b36a508e7d4f1d5d25c23a9772e1cfd9df363fad)

### [](#Step-3-文件重命名 "Step 3: 文件重命名")Step 3: 文件重命名

上面两步只是准备工作, 经过上面两步, 你的脚本仍然是 Groovy 的, 只不过现在更接近 Kotlin 了.

真正的改变发生在这一步: 把后缀为`.gradle`的文件重命名, 后缀改为`.gradle.kts`.  
没有必要全部改完, 这两种脚本是可以共存的.

改完之后各种报错了, 不要慌, 手动解决一下.

项目根目录的`build.gradle.kts`比较好修, 只有全局变量和 task 的问题.  
`app/build.gradle.kts`中要改 plugins, build type 和依赖部分.

#### [](#不再支持ext的全局变量定义 "不再支持ext的全局变量定义.")不再支持 ext 的全局变量定义.

这里图简单, 删掉`ext.kotlin_version = "1.3.61"`这句, 直接写:  

```
dependencies {
    classpath("com.android.tools.build:gradle:3.5.3")
    classpath(kotlin("gradle-plugin", version = "1.3.61"))
}
```

#### [](#task "task")task

task 本来是这:  

```
task clean(type: Delete) {
    delete(rootProject.buildDir)
}
```

现在要改成这样:  

```
tasks.register("clean", Delete::class) {
    delete(rootProject.buildDir)
}
```

#### [](#插件 "插件")插件

应用 plugins: 应用插件有 apply 和 plugin 两种方式.  
强烈建议使用`plugins {}` block.

所以`app/build.gradle.kts`里面改成这样:  

```
plugins {
    id("com.android.application")
    kotlin("android")
    kotlin("android.extensions")
}
```

#### [](#build-type "build type")build type

build type 原先是这样写的:  

```
buildTypes {
    release {
        minifyEnabled = false
        proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro")
    }
}
```

现在改成这样:  

```
buildTypes {
    getByName("release") {
        isMinifyEnabled = false
        proguardFiles(getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro")
    }
}
```

#### [](#依赖 "依赖")依赖

libs 文件依赖原先是:  

```
implementation(fileTree(dir: "libs", include: ["*.jar"]))
```

需要改为:  

```
implementation(fileTree(mapOf("dir" to "libs", "include" to listOf("*.jar"))
```

另外 kotlin 的部分:  

```
implementation(kotlin("stdlib-jdk7", org.jetbrains.kotlin.config.KotlinCompilerVersion.VERSION))
```

这一步的改动见:  
[https://github.com/mengdd/KotlinDSLSample/commit/e392028b128bc83f1d183b880c479687d28cdcc3](https://github.com/mengdd/KotlinDSLSample/commit/e392028b128bc83f1d183b880c479687d28cdcc3)

[](#自动转换的方法 "自动转换的方法")自动转换的方法
-----------------------------

有一个自动转换的工具:  
[https://github.com/bernaferrari/GradleKotlinConverter](https://github.com/bernaferrari/GradleKotlinConverter)

mac 上首先安装 kscript: [https://github.com/holgerbrandl/kscript](https://github.com/holgerbrandl/kscript)  

```
brew install holgerbrandl/tap/kscript
```

然后把`gradlekotlinconverter.kts`文件保存到根目录.

再运行:  

```
kscript gradlekotlinconverter.kts build.gradle
```

和  

```
kscript gradlekotlinconverter.kts app/build.gradle
```

进行文件的转换.

我测试了一下转换的结果并不是很完美, 还需要手动修改一下.

[](#Toubleshooting "Toubleshooting")Toubleshooting
--------------------------------------------------

*   文件后缀改为`kts`之后, `app/build.gradle`中`android`关键字被标红.  
    我就遇到了这个问题, 开始以为没改好, 找不到`android`关键字了, 各种困惑找原因.  
    但是我发现 build 是可以过的.  
    后来突然 IDE 弹出一个条: `There is a new script context available`.  
    点击了`Apply context`就可以了.
*   要是还有问题, 参考一下官方的 android sample 吧: [kotlin-dsl-samples/samples/hello-android](http://mengdd.github.io/Android/2020/01/15/migrate-gradle-build-scripts-to-kotlin/kotlin-dsl-samples/samples/hello-android/)

[](#参考 "参考")参考
--------------

*   我的 Android 项目 sample: [KotlinDSLSample](https://github.com/mengdd/KotlinDSLSample)
*   迁移文档: [Migrating build logic from Groovy to Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/)
*   官方 samples: [kotlin-dsl-samples](https://github.com/gradle/kotlin-dsl-samples)
*   官方 samples 里面的 android sample: [kotlin-dsl-samples/samples/hello-android](https://github.com/gradle/kotlin-dsl-samples/tree/master/samples/hello-android)