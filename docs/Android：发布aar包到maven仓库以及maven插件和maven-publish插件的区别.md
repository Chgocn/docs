> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7017608469901475847)

在日常开发中，不可避免的需要把自己的 library 发布到 maven 仓库中，这样使用起来也更加方便。

发布 aar 包到 maven 仓库，主要是使用 Gradle 提供的插件：

1.  [maven 插件](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2F4.8%2Fuserguide%2Fmaven_plugin.html "https://docs.gradle.org/4.8/userguide/maven_plugin.html")（旧版），在 Gradle 6.2 之后，就完全被弃用了（增加了 **@Deprecated** 注解）
    
2.  [maven-publish 插件](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html "https://docs.gradle.org/current/userguide/publishing_maven.html")
    

maven 插件，是 Gradle 1.0 的时候提供的用于发布 aar/jar 包到 Maven 仓库的插件。在 Gradle 1.3 中，引入了一种新的发布插件，即：maven-publish ，这个新的插件引入了一些新概念和新功能，使 Gradle 发布功能更加强大，现在是发布工件的首选选项。

一、基本概念
======

1、什么是 POM？
----------

POM（Project Object Model）指项目对象模型，用于描述项目构件的基本信息。一个有效的 POM 节点中主要包含一下信息：

<table><thead><tr><th><strong>配置</strong></th><th><strong>描述</strong></th><th><strong>举例（'com.github.bumptech.glide:glide:4.11.0'）</strong></th></tr></thead><tbody><tr><td>groupId</td><td>组织 / 公司的名称</td><td>com.github.bumptech.glide</td></tr><tr><td>artifactId</td><td>组件的名称</td><td>glide</td></tr><tr><td>version</td><td>组件的版本</td><td>4.11.0</td></tr><tr><td>packaging</td><td>打包的格式</td><td>aar/jar</td></tr></tbody></table>

2、 什么是仓库（repository）？
---------------------

在项目中，我们会需要依赖各种各样的二方库或三方库，这些依赖一定会存放在某个位置（Place），这个 “位置” 就叫做仓库。使用仓库可以帮助我们管理项目构件，例如 jar、aar 等等。

**主流的构建工具都有三个层次的仓库概念：**

*   **1、本地仓库：** 无论使用 Linux 还是 Window，计算机中会有一个目录用来存放从中央仓库或远程仓库下载的依赖文件；
*   **2、中央仓库：** 开源社区提供的仓库，是绝大多数开源库的存放位置。比如 Maven 社区的中央仓库 [Maven Central](https://link.juejin.cn?target=https%3A%2F%2Fsearch.maven.org%2F "https://link.juejin.cn?target=https%3A%2F%2Fsearch.maven.org%2F")；
*   **3、私有仓库：** 公司或组织的自定义仓库，可以理解为二方库的存放位置。

**构建时搜索依赖的顺序如下：**

*   1、在本地仓库搜索，如果搜索不到，执行步骤 2；
*   2、在中央仓库和私有仓库中搜索，搜索顺序按照`repositories`中声明的顺序依次查找。如果找到，则下载依赖文件到本地仓库，否则执行步骤 3；
*   3、如果最终找不到依赖项，则抛出错误 “无法找到依赖项”。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b00819417274c31a918e6820a54fa2e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

了解这些基本概念之后，下面就介绍一下，通过 Gradle 提供的 maven 插件 和 maven-publish 插件，如何发布 aar/jar 包。

二、[maven 插件](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2F4.8%2Fuserguide%2Fmaven_plugin.html "https://docs.gradle.org/4.8/userguide/maven_plugin.html")
=========================================================================================================================================================================

maven 插件是 Gradle 1.0 的时候提供的，使用文档：[docs.gradle.org/4.8/usergui…](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2F4.8%2Fuserguide%2Fmaven_plugin.html "https://docs.gradle.org/4.8/userguide/maven_plugin.html")

使用 maven 插件，遵循如下步骤：

1、使用 maven 插件
-------------

在 需要发布 aar 包的 library 模块的 build.gradle 文件中，应用 maven 插件：

```
apply plugin: "maven"
复制代码

```

2、配置 POM
--------

在 build.gradle 文件中，增加如下的 配置信息：

```
def localDefaultRepo = "file://" + new File(System.getProperty("user.home"), '.m2/repository').absolutePath
uploadArchives {
    repositories.mavenDeployer {
        // repository(url: uri('~/.m2/repository')) // 注释1
        // 配置仓库地址
        repository(url: uri(localDefaultRepo))
        // repository(url: uri('../repo'))
      
        // 配置 pom 信息
        pom.groupId "com.mei.http"
        pom.artifactId "myhttp"
        pom.version "1.0.0-SNAPSHOT"
        pom.packaging "aar"
      
      	// pom.project {
        //    groupId "com.mei.http"
        //    artifactId "myhttp"
        //    version "1.0.1-SNAPSHOT"
        //    packaging "aar"
        // }
    }
}
复制代码

```

如上所示，指定 仓库的地址，配置 pom 信息。这样配置完成之后，在 task 任务列表中，就可以看到 **upload/uploadArchives** 任务。

这里配置的仓库地址是一个本地路径，即把 aar 发布到本地的一个文件夹中。这里的 **USER_HOME/.m2/repository/** 目录，是 Gradle 默认的本地仓库地址，其中 **USER_HOME** 是用户目录。

### 2-1、默认本地仓库

在指定 本地仓库地址的时候，踩了一个坑，如果想使用 本地默认仓库地址，如：

```
repository(url: uri('/.m2/repository'))
println "path=${uri('~/.m2/repository')}"
复制代码

```

打印出的路径：

> path=file:/Users/mei/WorkSpace/AndroidDemo/MAarPublish/myhttpjava/~/.m2/repository/

执行 uploadArchives 任务之后，在 **USER_HOME/.m2/repository/** 目录 中，是没有 aar 文件的，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4fcbf3521b34bd18a908d1757207738~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

所以这个时候，去使用的话，是加载不到 aar 文件的。

```
implementation 'com.mei.http:myhttp:1.0.0-SNAPSHOT'
复制代码

```

这是什么原因导致的呢，其实就是路径问题。

当用绝对路径的时候，即：

```
def localDefaultRepo = "file://" + new File(System.getProperty("user.home"), '.m2/repository').absolutePath
复制代码

```

当仓库地址 用 localDefaultRepo 的时候，在 **USER_HOME/.m2/repository/** 目录就可以看到发布之后的 aar 包了，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/403d195d66fd4caf91b871140c2f7977~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

这样 aar 包就发布成功了。

**使用**

在 工程的 build.gradle 文件中，引入默认的本地仓库，如：

```
allprojects {
    repositories {
        .....
        mavenLocal() // 使用默认的本地仓库
    }
}
复制代码

```

在 app 的 build.gradle 文件中，引用 myhttp 库：

```
dependencies {
		.....
    implementation 'com.mei.http:myhttp:1.0.1-SNAPSHOT'
}
复制代码

```

这样，在 app 中就可以使用 myhttp 中的代码了。

### 2-2、自定义本地仓库

除了使用默认的本地仓库之外，还可以指定自定义的本地仓库，即：自己指定一个目录，作为本地仓库，如：

```
uploadArchives {
    repositories.mavenDeployer {
        // 配置仓库地址
        repository(url: uri('../repo'))
      
        // 配置 pom 信息
        pom.groupId "com.mei.http"
        pom.artifactId "myhttp"
        pom.version "1.0.0-SNAPSHOT"
        pom.packaging "aar"
    }
}
复制代码

```

如：上面的路径就是在本工程的根目录下，创建一个 repo 文件夹，用于充当 本地仓库，执行 uploadArchives 任务之后，在工程的目录下就可以看到 repo 目录，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/76b96b353f0c4c4383b6d5c1498ab9bd~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

可以看到，这也是发布成功了的，在使用的时候，也是需要明确指定 本地仓库的路径，即存放该 aar 包的文件路径，与上面类似，在工程的 build.gradle 文件中，增加如下代码，指明本地仓库路径：

```
allprojects {
    repositories {
				.....
       maven {
           url '../repo' // 如果是本工程的路径，可以直接这样显示
       }
    }
}
复制代码

```

如果是其他的工程想要用这个库的话，则需要使用绝对路径，即：

```
allprojects {
    repositories {
				.....
       maven {
           url '/Users/mei/WorkSpace/AndroidDemo/MAarPublish/repo' 
       }
    }
}
复制代码

```

3、上传源码和文档
---------

通过上面的步骤，aar 基本上就发布成功了，但 aar 包中的代码，都是没有注释的，也没有源码，只是反编译看到一些代码信息，这种体验就不是很好，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f12a761464b4157b0629f7af13be879~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

造成这个问题的原因就是，在 上传 aar 文件的时候，没有上传源码和文档。

### 3-1、上传源码

参考文档：[docs.gradle.org/current/dsl…](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fdsl%2Forg.gradle.api.tasks.bundling.Jar.html "https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html")

创建一个 Task 任务，用于上传源代码，具体如下：

```
task uploadSourceJar(type: Jar) {
  // 定义一个标志 (生成的jar包后面加上sources, 如: myhttp-1.0.2-20210927.115550-2-sources.jar)
    classifier = 'sources'
    println "srcDirs=${project.android.sourceSets.main.java.sourceFiles}"
  // 指定源码文件路径
    from project.android.sourceSets.main.java.sourceFiles
}

// 指定发包的时候，需要执行的task任务
artifacts {
    archives uploadSourceJar
}
复制代码

```

增加上面的代码之后，在执行 uploadArchives 任务，就会上传 源码，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c986c3e80a904034825963884a1125ed~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

在 Android Studio 中，查看 myhttp 的源码，如下：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d8f05a6eae79492cab1a3a609223e635~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

确实是可以看到源码和注释了。

扩展：**artifact**

`artifact`方法是提供`Object`对象，具体是什么呢？主要是三种。

1.  `artifact 'my-file-name.jar'`具体的文件。
2.  `artifact sourceJar`任务`sourceJar`输出物，例如这里是对源码进行了打包的`Jar`包。
3.  `artifact source: sourceJar, classifier: 'src', extension: 'zip'`通过`source`、`classifier`以及`extension`构造的`MavenArtifact`实例，参数分别对应源文件，名称类别 (`artifactId-classifier`) 以及扩展名称 (`.jar/.zip`等)。

### 3-2、生成文档并上传

参考文档：

*   [docs.gradle.org/current/dsl…](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fdsl%2Forg.gradle.api.tasks.javadoc.Javadoc.html%23org.gradle.api.tasks.javadoc.Javadoc%3AdestinationDir "https://docs.gradle.org/current/dsl/org.gradle.api.tasks.javadoc.Javadoc.html#org.gradle.api.tasks.javadoc.Javadoc:destinationDir")
*   [blog.didispace.com/books/Gradl…](https://link.juejin.cn?target=http%3A%2F%2Fblog.didispace.com%2Fbooks%2FGradleUserGuide%2Fthe_java_plugin%2Fjava_plugin_javadoc.html%3Fh%3Djavadoc "http://blog.didispace.com/books/GradleUserGuide/the_java_plugin/java_plugin_javadoc.html?h=javadoc")

通过上面的操作，源码就上传到 maven 仓库了，在引用 aar 文件到时候，就可以看到 源码和注释信息。

如果是一个 SDK 的话，那么为了方便别人接入，还需要上传文档。

```
task androidJavadocs(type: Javadoc) {
    doLast {
        source = project.android.sourceSets.main.java.srcDirs
        // 需要生成 doc 的 代码路径
        classpath += project.files(project.android.getBootClasspath().join(File.pathSeparator))
        failOnError = false // javadoc 解析错误时task 不会异常停止
    }
}

// 解决 JavaDoc 中文注释生成失败的问题
tasks.withType(Javadoc) {
    options.addStringOption('Xdoclint:none', '-quiet')
    options.addStringOption('encoding', 'UTF-8')
    options.addStringOption('charSet', 'UTF-8')
}

task androidJavadocsJar(type: Jar, dependsOn: androidJavadocs) {
    doLast {
        //archiveFileName  The archive name. If the name has not been explicitly set
        // , the pattern for the name is:
        //[archiveBaseName]-[archiveAppendix]-[archiveVersion]-[archiveClassifier].[archiveExtension]
        // 存档名称的分类器部分，名称后面加的类别区分的名字.e.g. xxxx-javadoc.jar。
        classifier = 'javadoc'
        from androidJavadocs.destinationDir
    }
}

artifacts {
    // aar包增加注释
    archives androidJavadocsJar
}
复制代码

```

增加如上代码，就可以生成 Java 文档。

4、上传远程 Maven 仓库
---------------

要想把 aar 包上传到远程的 maven 仓库，只需要把上面的 maven 仓库地址替换成 远程 maven 仓库地址就可以了，除此之外还需要增提供 maven 仓库的用户名和密码，因为构建的 私有 maven 仓库，一般都是需要用户名和密码的。

具体如下：

```
uploadArchives {
    repositories.mavenDeployer {
      // 假设远程maven仓库地址为：http://10.0.192.56:8081/repository/core/
      // 账号：meiTest，密码：123456
        repository(url: "http://10.0.192.56:8081/repository/core/") {
            authentication(userName: "meiTest", password: "123456")
        }
        // 配置 pom 信息
        pom.groupId "com.mei.http"
        pom.artifactId "myhttp"
        pom.version "1.0.0-SNAPSHOT"
        pom.packaging "aar"
    }
}
复制代码

```

如上，替换 maven 仓库地址，增加仓库的账号和密码，就可以上传到远程仓库中。

**使用**

把 aar 包上传到 maven 私有仓库时，需要校验账号和密码，在使用的的时候，同样也要校验账号和密码，如：

```
allprojects {
    repositories {
        ....
        // 指定私服路径和账号密码
        maven{
            url 'http://10.0.192.56:8081/repository/core/'
            credentials {
                username = "meiTest"
                password = "123456"
            }
        }
    }
}
复制代码

```

三、[maven-publish 插件](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html "https://docs.gradle.org/current/userguide/publishing_maven.html")
=================================================================================================================================================================================================

插件类：MavenPublishPlugin

文档：

*   [maven-publish 插件文档](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html "https://docs.gradle.org/current/userguide/publishing_maven.html")
    
*   [MavenPublication 参数说明](https://link.juejin.cn?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fdsl%2Forg.gradle.api.publish.maven.MavenPublication.html "https://docs.gradle.org/current/dsl/org.gradle.api.publish.maven.MavenPublication.html")
    

在 Gradle 6.2 之后， maven 插件就彻底被废弃，无法使用了，只能使用 maven-publish 插件，因此 maven-publish 插件的使用一样也要掌握。下面就来看看具体的使用方式。

1、maven-publish 插件的基本使用
-----------------------

**应用插件：**

```
apply plugin: "maven-publish"
复制代码

```

** 配置发布产物：** 使用 maven-publish 插件发布 aar 包的时候，基础 **配置信息** 如下：

```
publishing {
    // 配置maven 仓库
    repositories { RepositoryHandler handler->
        handler.mavenLocal()  // 发布到默认的 本地maven 仓库 ，路径： USER_HOME/.m2/repository/
    }
  // 配置发布产物
    publications {PublicationContainer publication->
      // 名称可以随便定义，这里定义成 maven，是因为我的 aar 包是发布到 maven 仓库的，所以这里为了见名知义，定义成了 maven
      // 任务名称：maven 
        maven(MavenPublication) {// 容器可配置的信息 MavenPublication
            // 依赖 bundleReleaseAar 任务，并上传其产出的aar
            afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) } // 方式一：生成aar包
        	  // artifact "$buildDir/outputs/aar/${project.name}-release.aar" // 方式二：指定生成的aar路径
            groupId = "com.mei.http"
            artifactId = "myhttp"
            version = "1.0.4-SNAPSHOT"
        }
    }
}
复制代码

```

maven-publish 插件的扩展配置类是：PublishingExtension，查看 PublishingExtension 类的源码可以看到，publishing 配置，可以配置的信息有两个：

*   **repositories**：用于配置 maven 仓库地址
    
    地址可以配置多个，在执行 publish 任务的时候，就会把 aar 包发布到所有指定的 maven 仓库地址中去。
    
    ```
    repositories { RepositoryHandler handler ->
        handler.mavenLocal()
        handler.maven {
            url "${rootDir}/repo"
        }
        // 仓库用户名密码
        // handler.maven { MavenArtifactRepository mavenArtifactRepository ->
        //     // maven 仓库地址
        //     url 'http://10.0.192.56:8081/repository/core/'
        //     // 访问仓库的 账号和密码
        //     credentials {
        //         username = "meiTest"
        //         password = "123456"
        //     }
        // }
    }
    复制代码
    
    ```
    
*   **publications**：配置需要发布的 jar 的信息，即产物 aar 包的信息。publications 是一个容器，类型是 PublicationContainer ，其可以配置的信息类型是 MavenPublication。即 可以理解成 **publications** 是一个列表集合，而集合中存储的对象是 MavenPublication，而对象的名称可以由自己随便定义。 所以 publications 也是可以配置多个的，如：
    
    ```
    publications { PublicationContainer publicationContainer ->
      // 发布 snapshot 包
        debug(MavenPublication) {
            afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) }
            groupId = "com.mei.http"
            artifactId = "myhttp"
            version = "1.0.4-SNAPSHOT"
        }
      // 发布正式包
        release(MavenPublication) {
            afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) }
            groupId = "com.mei.http"
            artifactId = "myhttp"
            version = "1.0.4"
        }
    复制代码
    
    ```
    
    **指定上传的 aar 包的方式：**
    
    *   通过依赖生成 aar 包任务，如：**afterEvaluate {artifact(tasks.getByName("bundleReleaseAar")) }**
    *   通过指定生成的 aar 路径，如：**artifact "buildDir/outputs/aar/{project.name}-release.aar"**

**发布 aar：**

增加上述配置之后，执行 AS 右侧的 Tasks 列表中的 publishing/publish 任务，就可以发布 aar 包。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cee288802c6045c9a9c5b22acf72fdd3~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

配置了两个发版产品，debug 和 release，执行发布任务后，可以看到，在默认的 本地仓库中，确实是有正式包和测试包，如下图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d661ad44e6e54f06a92fa4d22b7ccdc0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

2、上传源码
------

在 maven-publish 插件的基本使用中，是没有上传 aar 包的源码的，在 Android Studio 中，打开类查看源码可以看到：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/663b7c21dba241cbad2e15624cc9c209~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

提示用户选择源码，这里能看到代码，是 Android Studio 根据字节码反编译的。所以这里还需要上传源码。

增加上传源码的 task，如：

```
// 1. 增加上传源码的task
task sourceJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    archiveClassifier = "sources"
}

publishing {
    repositories { RepositoryHandler handler ->
        handler.mavenLocal()
    }
    publications { PublicationContainer publicationContainer ->
        maven(MavenPublication) {
            artifact sourceJar // 增加上传源码的 task 
          
            afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) }
            groupId = "com.mei.http"
            artifactId = "myhttp"
            version = "1.0.5-SNAPSHOT"
        }
    }
}
复制代码

```

增加上面的注释处的代码之后，在发布 aar 包的时候，就会连同源码一起上传了，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcaae673ca0e47389d731a04d9a97f62~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

从上面的代码可以看出，源码确实是上传了，注释都能看到了。

3、依赖传递
------

通过上面的步骤，发布的 aar 包，是不会进行依赖传递的，如：我在 demo：myHttpjava 中，依赖了 OkHttp，对 myHttpjava 发布 aar 包并引用之后，在 app 工程中，无法使用 OkHttp 相关的 Api，这就是因为 依赖没有传递过来。在 app 模块中：

```
dependencies {
		。。。。

    implementation 'com.mei.http:myhttp:1.0.4-SNAPSHOT'
}
复制代码

```

无法使用 OkHttp 相关的 Api，但 myHttpjava 自己写的类可以调用，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ceef07da6d5e41b9b78886db3ec4d819~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

从上图也可以看出，OkHttp 的依赖，确实没有传递过来。

在 maven 仓库中，可以打开 **.pom** 文件，如图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0867c2e73ed415a95f008db8796551f~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

发现里面只有 myHttpjava 库的声明，没有其依赖库的声明，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cd5a0bc3b1704c34a6ede775f611a298~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

这就只有 myHttpjava 的声明信息，没有依赖库的如：OkHttp 的 声明信息。而 **使用 maven 插件发布的 aar 包，默认是依赖传递的**，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de1f305aaa0c474a9ae54d486a5cf4dc~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

当然，maven-publish 插件，对依赖传递也提供了支持。把 library 中的依赖信息，手动的添加到 pom 文件中（配置信息参考：**MavenPom** 类），就可以完成依赖传递了，具体如下：

```
maven(MavenPublication) {
    // 依赖 bundleReleaseAar 任务，并上传其产出的aar
    afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) }
    artifact sourceJar
    groupId = "com.mei.http"
    artifactId = "myhttp"
    version = "1.0.6-SNAPSHOT"
    // pom文件中声明依赖，从而传递到使用方
    pom.withXml {
        def dependenciesNode = asNode().appendNode('dependencies')
        configurations.implementation.allDependencies.each {
            // 避免出现空节点或 artifactId=unspecified 的节点
            if (it.group != null && (it.name != null && "unspecified" != it.name) && it.version != null) {
                println "dependency=${it.toString()}"
                def dependencyNode = dependenciesNode.appendNode('dependency')
                dependencyNode.appendNode('groupId', it.group)
                dependencyNode.appendNode('artifactId', it.name)
                dependencyNode.appendNode('version', it.version)
                dependencyNode.appendNode('scope', 'implementation')
            }
        }
    }
}
复制代码

```

增加上面的 pom 模块，在 发布 aar 包的时候，打开 pom 文件，就可以看到依赖的库的声明信息，如图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/51a80afc25d243cc8ab76f039e7570c1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

在 app 模块中，使用 myhttp 库时，虽然还是没有手动引用 OkHttp 库，但发现已经可以使用 OkHttp 相关的 Api 了。说明依赖确实得到了传递。

依赖是否传递，我们通过打印 依赖库的信息也可以看出来，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/700b432ee27648108f937c2a5ab7c695~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

pom 闭包中配置的信息，最终都会保存到 **.pom** 文件中，如描述信息，名称，licenses，developers，scm 等，如：

```
pom {
    name = "Demo"
    description = "A demonstration of Maven POM customization"
    url = "http://www.example.com/project"
}
复制代码

```

结果：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/37e3938d88f84fe68a1c692090e14cd0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

4、结合 Android Gradle 插件，完成 aar 包的发布
----------------------------------

在上面的步骤中，**publications** 闭包中的有些配置还是不够优雅的，比较繁琐，如：

1.  配置发布的内容（即配置上传的 aar 文件），是通过如下两种方式：
    
    *   依赖生成 aar 包的 Task 任务，如：**afterEvaluate {artifact(tasks.getByName("bundleReleaseAar")) }**
    *   通过指定生成的 aar 文件路径，如：**artifact "buildDir/outputs/aar/{project.name}-release.aar"**
2.  **依赖传递**：通过手动配置的方式，即 使用 **withXml** 闭包往 .pom 文件中，追加 dependency 依赖信息。
    

具体如下：

```
publishing {
    // 配置maven 仓库
    repositories { RepositoryHandler handler ->
        handler.mavenLocal()
    }
    publications { PublicationContainer publicationContainer ->
        maven(MavenPublication) {
            // 依赖 bundleReleaseAar 任务，并上传其产出的aar
            afterEvaluate { artifact(tasks.getByName("bundleReleaseAar")) }
            // // 也可以指定上传的AAR包，但是需要先手动生成aar
            // artifact "$buildDir/outputs/aar/${project.name}-release.aar"
            artifact sourceJar
            groupId = "com.mei.http"
            artifactId = "myhttp"
            version = "1.0.8-SNAPSHOT"
            // pom文件中声明依赖，从而传递到使用方
            pom.withXml {
                def dependenciesNode = asNode().appendNode('dependencies')
                configurations.implementation.allDependencies.each {
                    // 避免出现空节点或 artifactId=unspecified 的节点
                    if (it.group != null && (it.name != null && "unspecified" != it.name) && it.version != null) {
                        println "dependency=${it.toString()}"
                        def dependencyNode = dependenciesNode.appendNode('dependency')
                        dependencyNode.appendNode('groupId', it.group)
                        dependencyNode.appendNode('artifactId', it.name)
                        dependencyNode.appendNode('version', it.version)
                        dependencyNode.appendNode('scope', 'implementation')
                    }
                }
            }
        }
    }
}
复制代码

```

而在 Android 开发中，上述配置是可以简化的。具体来说就是 Android Gradle 插件对 maven-publish 插件有了支持。

但从 Android Gradle 插件 3.6.0 及更高版本（说的是这里 `classpath 'com.android.tools.build:gradle:3.6.0'`）之后，也支持 [maven-publish 插件](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html "https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html")了，使配置可以更加简洁。

Android Gradle 插件会为应用或库模块中的每个**构建变体工件**创建一个[组件](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fdependency_management_terminology.html%23sub%3Aterminology_component "https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fdependency_management_terminology.html%23sub%3Aterminology_component")，您可以使用它来自定义要发布到 Maven 代码库的[发布内容](https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html%23publishing_maven%3Apublications "https://link.juejin.cn/?target=https%3A%2F%2Fdocs.gradle.org%2Fcurrent%2Fuserguide%2Fpublishing_maven.html%23publishing_maven%3Apublications")。

Android 插件所创建的组件取决于模块是否使用应用或库插件，如下表所述。

<table><thead><tr><th>module 应该的插件</th><th>发布内容工件</th><th>组件名称</th></tr></thead><tbody><tr><td><strong>com.android.library</strong></td><td>AAR</td><td>components.variant</td></tr><tr><td><strong>com.android.application</strong></td><td>APK 和可用的 ProGuard 或 R8 映射文件的 ZIP</td><td>components.variant_apk</td></tr><tr><td><strong>com.android.application</strong></td><td>Android App Bundle (AAB)</td><td>components.variant_aab</td></tr></tbody></table>

知道这些之后，publications 的配置就变得很简单了，具体如下：

```
afterEvaluate {// components.release 只有在配置完成之后，才能拿到值
    publishing {
        // 配置maven 仓库
        repositories { RepositoryHandler handler ->
            handler.mavenLocal()
        }
        publications { PublicationContainer publicationContainer ->
            maven(MavenPublication) {
                from components.release // 注释1:使用 Android Gradle 插件生成的组件，作为发布的内容
                artifact sourceJar // 上传源码
                groupId = "com.mei.http"
                artifactId = "myhttp"
                version = "1.0.8-SNAPSHOT"
            }
        }
    }
}
复制代码

```

如上所示，在**注释 1** 处：通过 from 方法，设置发布的内容，而内容是 Android Gradle 插件生成的组件。

这样指定之后，就可以正常的上传 aar 包了。并且不需要手动的添加依赖传递信息，Android Gradle 插件已经帮我们添加好了。

发布 **aar 包** 之后，查看 .pom 文件，依赖库的配置信息，也都是有的，如：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/026028748d5e4ebeaced861c48c88387~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

所以，使用 Android Gradle 插件 创建的 **组件**，当作发布内容的时候，aar 文件 和 依赖传递，都得到了解决，非常完美。

**这里需要特别注意一个问题**

​ 就是 components 组件信息，只有在 Gradle 配置完成之后，才能够拿到，所以在使用的时候，需要放在 **afterEvaluate** 闭包内。这点一定要注意，我就是在这个地方，被坑了，开始没有放在 **afterEvaluate** 闭包内，所以一直找不到 release 组件。[Android 官网](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.android.com%2Fstudio%2Fbuild%2Fmaven-publish-plugin%3Fhl%3Dzh-cn "https://developer.android.com/studio/build/maven-publish-plugin?hl=zh-cn")也提示我们了：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fb5b32684fa4205860189a0d45503d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

Maven-publish 插件 结合 Android Gradle 插件，使得 上传 aar 包 的配置也变得简单了。

四、发布 Kotlin 项目的 aar 包，源码上传问题
============================

在配置上传源码的 任务时，基本配置如下：

```
task sourceJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    archiveClassifier = "sources"
}
复制代码

```

但这种配置，如果库工程是用 Java 写的话，源码可以正常上传，但如果是 Kotlin 编写的库，发布 aar 包时，无法查看源码。

通过 android.sourceSets.main.java.srcDirs 指定的源码，只能识别到 Java 文件，而 kt 文件被忽略了，但 通过查看官方文档可以知道，from 函数是可以指定源码路径的，所以这里直接把 from 函数的参数替换为 源码路径，即：

```
task sourceJar(type: Jar) {
    from android.sourceSets.main.java.getSrcDirs() // 源码路径
    archiveClassifier = "sources"
}
复制代码

```

这样在 发布 aar 包的时候，发现可以正常的访问源码了。

总结：
===

1、**maven 与 maven-publish 插件的区别：**
----------------------------------

*   maven 插件比较老，配置简单，在 Gradle 6.2 之后，就完全废弃了。
*   maven-publish 插件，从 gradle 1.3 之后开始支持，且是现在的通用方案，且功能更加强大，配合 Android Gradle 插件提供的组件内容，配置也很简单，推荐使用。

2、参考：
-----

*   [Android aar 包发布到 maven 仓库总结](https://link.juejin.cn?target=https%3A%2F%2Fwww.appblog.cn%2F2020%2F06%2F06%2FAndroid%2520aar%25E5%258C%2585%25E5%258F%2591%25E5%25B8%2583%25E5%2588%25B0maven%25E4%25BB%2593%25E5%25BA%2593%25E6%2580%25BB%25E7%25BB%2593%2F "https://www.appblog.cn/2020/06/06/Android%20aar%E5%8C%85%E5%8F%91%E5%B8%83%E5%88%B0maven%E4%BB%93%E5%BA%93%E6%80%BB%E7%BB%93/")
*   [Android 工程化实践：组件化发布](https://juejin.cn/post/6963633839860088846 "https://juejin.cn/post/6963633839860088846")
*   [创建和生成本地或远程 Maven 仓库](https://link.juejin.cn?target=https%3A%2F%2Fonlybenyang.github.io%2F2019%2F02%2F12%2Fandroid%2F%25E5%2588%259B%25E5%25BB%25BA%25E5%2592%258C%25E7%2594%259F%25E6%2588%2590%25E6%259C%25AC%25E5%259C%25B0%25E6%2588%2596%25E8%25BF%259C%25E7%25A8%258BMaven%25E4%25BB%2593%25E5%25BA%2593%2F "https://onlybenyang.github.io/2019/02/12/android/%E5%88%9B%E5%BB%BA%E5%92%8C%E7%94%9F%E6%88%90%E6%9C%AC%E5%9C%B0%E6%88%96%E8%BF%9C%E7%A8%8BMaven%E4%BB%93%E5%BA%93/")
*   [Gradle 插件之 maven-publish：发布 android library 到 maven 仓库](https://juejin.cn/post/6844904185754812423 "https://juejin.cn/post/6844904185754812423")
*   [发布包含源码的 Kotlin 项目 aar 包到 Maven](https://juejin.cn/post/6844904198350323720 "https://juejin.cn/post/6844904198350323720")

3、感谢：
-----

特别感谢以下朋友在评论中提出的问题，让我得以完善 指定 aar 文件 和 依赖的问题：

*   [萨林雷电](https://juejin.cn/user/1609340750927469 "https://juejin.cn/user/1609340750927469")
*   [Joehaivo 飞羽](https://juejin.cn/user/1380642334187303 "https://juejin.cn/user/1380642334187303")