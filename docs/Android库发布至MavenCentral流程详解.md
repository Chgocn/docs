> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6953598441817636900#heading-13)

> 欢迎关注微信公众号：[FSA 全栈行动 👋](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F31960a996f1f4b0da35d69ab7480f7d6~tplv-k3u1fbpfcp-zoom-1.image "https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/31960a996f1f4b0da35d69ab7480f7d6~tplv-k3u1fbpfcp-zoom-1.image")

"jCenter 不久后将停止服务" 这个消息对所有 Android 开发者的影响是很大的，很多好用的第三方库都会上传到 jCenter，而且几乎所有的 Android 项目里都会依赖到 jCenter 仓库，这意味着 Android 开发者对其有很强的依赖性。作为第三方库使用者来说，一旦到了 jCenter 停服那时，只有 2 种选择：

1.  使用阿里等第三方代理仓库（上面可能会缓存着这些曾经托管在 jCenter 上的第三方库）
2.  祈祷第三方库作者能早日同步库到 MavenCentral

> 以下是其他人对 jCenter 停服事件的看法文章推荐，有兴趣可以去看看：  
> [《浅谈 JCenter 即将被停止服务的事件》](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F115873573 "https://blog.csdn.net/guolin_blog/article/details/115873573")

回归主题，如果你是一个第三方库开发者，且之前没有上传库到 MavenCentral 经验的话，本文可以助你早日上传库到 MavenCentral~

一、Sonatype 账号
-------------

### 1、注册 Sonatype

MavenCentral 和 Sonatype 的关系相当于 jCenter 和 jForg：

<table><thead><tr><th align="left">库平台</th><th align="left">运营商</th><th align="left">管理后台</th></tr></thead><tbody><tr><td align="left">jCenter</td><td align="left">jForg</td><td align="left">bintray.com</td></tr><tr><td align="left">MavenCentral</td><td align="left">Sonatype</td><td align="left">oss.sonatype.org</td></tr></tbody></table>

> 注意：最新管理后台链接是：`s01.oss.sonatype.org`，详情见 [central.sonatype.org/publish/rel…](https://link.juejin.cn?target=https%3A%2F%2Fcentral.sonatype.org%2Fpublish%2Frelease%2F "https://central.sonatype.org/publish/release/")

所以，在上传库到 MavenCentral 之前，需要先注册登录 Sonatype，访问 [issues.sonatype.org](https://link.juejin.cn?target=https%3A%2F%2Fissues.sonatype.org "https://issues.sonatype.org") :

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03effe283dd445418e8ee706ec005a01~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

如果你已经有账号，则直接登录，否则点击 `Sign up` 进入注册页面：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1080f2a718a46fdb283e9e515d7a98e~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

填写好账号、密码、邮箱等信息即可注册成功，注册成功后再登录 Sonatype。

> 注意：邮箱很重要，建议是你常用的邮箱，才能及时收到 Sonatype 在 issue 中给你的答复信息提醒。

### 2、申请上传权限

现在你已经有 Sonatype 账号了，接下来理应就是借助 grdle 脚本经 管理后台 (`s01.oss.sonatype.org`) 把库上传到 MavenCentral ，但是，Sonatype 新用户默认是没有这个权限的，不信你可以访问 [s01.oss.sonatype.org](https://link.juejin.cn?target=https%3A%2F%2Fs01.oss.sonatype.org%2F "https://s01.oss.sonatype.org/") 后，点击右上角 "Log In" 登录试试看，会提示没有权限：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ad1b48628b34e06a8ae179454f5a810~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

> 注意：出现这个弹窗有 2 种可能，一种是账号密码错误，另一种没有权限。

所以，现在我们需要让 Sonatype 给我们开通这个权限，回到登录成功后跳转的那个页面 [issues.sonatype.org](https://link.juejin.cn?target=https%3A%2F%2Fissues.sonatype.org%2F "https://issues.sonatype.org/")，点击顶部的 `新建` 按钮，填写项目信息：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f638634a7c80410a89eaa786fa399f50~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

> 注意：图中是我个人项目信息，只是举例，不要照抄！！

这个步骤中，唯一需要注意的地方就是 `Group Id`，有两种情况：

*   无网站域名：可以直接使用 github 的子域名 `io.github.username`，比如我的 github 账号名是 GitLqr，那么可以填写 `io.github.gitlqr`。
*   有网站域名：可以填写个人或公司域名，比如：`com.gitlqr`，另外，还需要在 DNS 配置中配置一个 TXT 记录来验证域名所有权，具体请看：[central.sonatype.org/pages/produ…](https://link.juejin.cn?target=https%3A%2F%2Fcentral.sonatype.org%2Fpages%2Fproducers.html "https://central.sonatype.org/pages/producers.html")

> 注意：目前已经不能再使用 `com.github` 域名了，具体原因请看：[central.sonatype.org/changelog/#…](https://link.juejin.cn?target=https%3A%2F%2Fcentral.sonatype.org%2Fchangelog%2F%232021-04-01-comgithub-is-not-supported-anymore-as-a-valid-coordinate "https://central.sonatype.org/changelog/#2021-04-01-comgithub-is-not-supported-anymore-as-a-valid-coordinate")；使用自己的网站域名时，建议写顶级域名，不需要具体到某个项目的子域名，如此一来，`Group Id` 只需要申请一次，以后你的其他库都使用同一个 `Group Id` 即可。

填写信息后，点击 "新建" 按钮，开启一个 issue，会显示 `wait for response`，等待 Sonatype 工作人员审核回复：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38b5be24d7714b14890f27e4ee2f05eb~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

因为 Sonatype 是国外运营，所以工作时间上会有时差，我们需要耐心等待 Sonatype 工作人员处理这个 issue，比如我这样：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b4669d1ba4e4d89975f2a47aae63601~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

> 注意：如果你想尽快上传库，那么前面已经提到了，请使用你常用的邮箱，当 Sonatype 工作人员评论时，会第一时间通过邮件通知到你。

二、Gradle 配置
-----------

首次向 Sonatype 申请上传权限可能会有点久，我们可以同步处理 gradle 配置。

### 1、Maven 提交脚本

在项目根目录下新建一个 `maven-publish.gradle` 文件，内容如下：

> 注意：以下内容是通用配置，直接拷贝即可。

```
if (project.hasProperty("android")) { // Android libraries
    task sourcesJar(type: Jar) {
        classifier = 'sources'
        from android.sourceSets.main.java.srcDirs
    }

    task javadoc(type: Javadoc) {
        // https://github.com/novoda/bintray-release/issues/71
        excludes = ['**/*.kt'] // < ---- Exclude all kotlin files from javadoc file.
        source = android.sourceSets.main.java.srcDirs
        classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
        options.encoding = "utf-8"
        options.charSet = "utf-8"
    }
} else { // Java libraries
    task sourcesJar(type: Jar, dependsOn: classes) {
        classifier = 'sources'
        from sourceSets.main.allSource
    }
}

// 强制 Java/JavaDoc 等的编码为 UTF-8
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}

tasks.withType(Javadoc) {
    options.encoding = "UTF-8"
}

task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

// add javadoc/source jar tasks as artifacts
artifacts {
    archives javadocJar
    archives sourcesJar
}

apply plugin: 'maven'
apply plugin: 'signing'


//Properties properties = new Properties()
//properties.load(project.rootProject.file('local.properties').newDataInputStream())
//
//def ossrhUsername = properties.getProperty("ossrhUsername")
//def ossrhPassword = properties.getProperty("ossrhPassword")

def PUBLISH_GROUP_ID = publishedGroupId //这里可以不是直接申请时候的groupId只要开头是就可以

def PUBLISH_ARTIFACT_ID = artifact

def PUBLISH_VERSION = libraryVersion // android.defaultConfig.versionName //这个是直接获取的库gradle里配置好的版本号，不用到处修改版本号，只需要维护一份就可以。

//签名
signing {
    required { gradle.taskGraph.hasTask("uploadArchives") }
    sign configurations.archives
}

uploadArchives {
    repositories {
        mavenDeployer {

            beforeDeployment { MavenDeployment deployment -> signing.signPom(deployment) }

            repository(url: "https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/") {
                authentication(userName: ossrhUsername, password: ossrhPassword)
            }

            snapshotRepository(url: "https://s01.oss.sonatype.org/content/repositories/snapshots/") {
                authentication(userName: ossrhUsername, password: ossrhPassword)
            }

            pom.groupId = PUBLISH_GROUP_ID
            pom.artifactId = PUBLISH_ARTIFACT_ID
            pom.version = PUBLISH_VERSION

            pom.project {
                packaging 'aar' //我这里发布的是安卓的包，所有写的aar

                name libraryName // '发布库的简单名称'
                // optionally artifactId can be defined here
                description libraryDescription // '发布包的描述'
                url siteUrl // '可以写公司官网地址或github个人页面地址'

                scm {
                    connection gitUrl // 'scm:替换成项目git地址'
                    developerConnection gitUrl // 'scm:替换为git开头的项目地址'
                    url siteUrl // '项目首页，可以是github项目的主页'
                }

                licenses {
                    license {
                        name licenseName // 'The Apache License, Version 2.0'
                        url licenseUrl // 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }

                developers {
                    developer {
                        id developerId // '这里填写申请账号时候的全名就可以'
                        name developerName // '这里随意填写就可以'
                        email developerEmail// '最好是申请账号时用的邮箱'
                    }
                }
            }
        }
    }
}
复制代码

```

### 2、Maven 库信息配置

库信息共用两处配置：

*   通用信息：填写在项目根目录下的 `gradle.prperties` 文件中。
*   Module 信息：填写在具体 Module 目录下的 `build.gradle` 文件中。

#### 通用信息 `gradle.prperties` 配置

```
publishedGroupId=你申请权限时填写的GroupId
siteUrl=项目url
gitUrl=git链接
developerId=账号id
developerName=账号id
developerEmail=邮箱

licenseName=The Apache Software License, Version 2.0
licenseUrl=http://www.apache.org/licenses/LICENSE-2.0.txt
allLicenses=["Apache-2.0"]
复制代码

```

具体可参考 [github.com/GitLqr/Lite…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FGitLqr%2FLiteARouter%2Fblob%2Fmain%2Fgradle.properties "https://github.com/GitLqr/LiteARouter/blob/main/gradle.properties")

#### Module 信息 `build.gradle` 配置

```
ext {
    artifact = '库的唯一标识'
    libraryName = '发布库的简单名称'
    libraryDescription = '发布包的描述'
    libraryVersion = '发布库的版本号'
}
复制代码

```

具体可参考 [github.com/GitLqr/Lite…](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FGitLqr%2FLiteARouter%2Fblob%2Fmain%2Flitearouter-api%2Fbuild.gradle "https://github.com/GitLqr/LiteARouter/blob/main/litearouter-api/build.gradle")

### 3、导入 Maven 脚本

在库对应的 Module 目录下的`build.gradle`文件底部添加如下代码：

```
apply from: '../maven-publish.gradle'
复制代码

```

在 AS 中 `Sync Now` 一下 Gradle，就可以在 Gradle 面板中看到多了一个 `upload/uploadArchives` 任务了。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3d0ea63f38544e228434d215698730d1~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

三、GPG 签名
--------

通过以上两步之后，还差最后一步，此时你运行 `uploadArchives` 任务是无法正常提交的，MavenCentral 提交还需要配置 GPG 签名文件。

### 1、使用 Gpg4win 生成 GPG 密钥

> 注意：以下生成 GPG 密钥的操作流程使用的是 Window 环境，如果你使用的是 Mac，请参考：[www.jianshu.com/p/1c715203c…](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F1c715203c7c6 "https://www.jianshu.com/p/1c715203c7c6")

先到 [www.gpg4win.org/get-gpg4win…](https://link.juejin.cn?target=https%3A%2F%2Fwww.gpg4win.org%2Fget-gpg4win.html "https://www.gpg4win.org/get-gpg4win.html") 下载安装好 Gpg4win：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52d8b6a2b23c489d91848019f6764173~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

启动 Gpg4win，点击 "文件" - "新建密钥对..."：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f11f6384a07456293b201aa767c4efa~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

创建个人 OpenPGP 密钥对：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3eef6a8af21c447487d4d6f5c7222718~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

填写账号、邮箱，并勾选 `Protect the generated key with a passphrase`：

> 注意：在高级设置里可以设置更详细的，例如过期时间，但过期时间不可以太长，或报错。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a6cb48084bd4eba9e340196b09bf463~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

点击 "新建"， 设置密码（确认密码）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7028a728852475b8b128cc836d44a32~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

等待生成密钥后，出现如下弹窗，点击 "完成"：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c595a55a37cf4be68e153138d08d863a~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

在主界面双击刚刚创建好的密钥，会出现如下弹窗，需要把底部的那 8 个字符复制下来，在后面的【配置密钥】环节会用到：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/01b7dae684e94a50b054bd47b234d224~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

在主界面右击刚刚创建好的密钥，点击 "在服务器上发布..."，然后一路确定：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9959ab6ef49a45458caceb51e2d4ebb4~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

在主界面右击刚刚创建好的密钥，点击 "导出..."，这时注意了，一定要在这里先把文件名改成 "gpg" 或者 "pgp"，再点保存：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/89a2644aba934a8fbf2f7b3a4bc4dcef~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

注意了，Gpg4win 的导出分为两种：

*   `"导出..."`: 导出公钥
*   `"Backup Secret Keys"`: 导出 `私钥` !!

> 提示：后续步骤需要用到的是 `私钥`，所以需要点 `"Backup Secret Keys"` 导出私钥的 gpg 文件；导出时默认文件后缀是 "asc"，一定要在这时修改后缀为 "gpg"，这样才能生成正确的 `二进制 gpg 密钥文件`。如果先保存成 `asc 文本文件`，再修改成 gpg 是没有用的！！！

### 2、配置密钥

这一步很关键，需要在电脑 `用户目录` 下的 `.gradle/gradle.properties` 中配置如下内容：

> 比如：`C:\Users\CharyLin\.gradle\gradle.properties`

```
# MavenCentral
signing.keyId=刚才获取的密钥后8位
signing.password=之前我们执行命令时设置的密码
signing.secretKeyRingFile=刚才生成的gpg文件路径
ossrhUsername=sonatype用户名
ossrhPassword=sonatype密码
复制代码

```

四、发布
----

### 1、上传档案 (uploadArchives)

到了这一步，就可以去 Gradle 面板中双击执行 `uploadArchives` 任务了，成功的话，会出现如下日志：

```
11:12:00: Executing task 'uploadArchives'...

...

> Task :litearouter-api:sourcesJar UP-TO-DATE
> Task :litearouter-api:signArchives UP-TO-DATE
> Task :litearouter-api:uploadArchives

...

BUILD SUCCESSFUL in 16s
23 actionable tasks: 1 executed, 22 up-to-date
11:12:16: Task execution finished 'uploadArchives'.
复制代码

```

如果出现 `Unable to read secret key from file: it may not be a PGP secret key ring` 的错误，有以下三种可能：

*   GPG 账号、密码有误
*   使用的是 GPG 公钥文件
*   使用的是 GPG 密钥文件，但是是先保存成 asc 文件后修改成 gpg 文件

```
FAILURE: Build failed with an exception.

* What went wrong:
Could not evaluate onlyIf predicate for task ':litearouter-annotation:signArchives'.
> Unable to read secret key from file: D:\CharyLinDatas\GitLqr\secret.gpg (it may not be a PGP secret key ring)

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 11s
复制代码

```

### 2、发布档案

现在可以去登录下的管理后台 [s01.oss.sonatype.org/](https://link.juejin.cn?target=https%3A%2F%2Fs01.oss.sonatype.org%2F "https://s01.oss.sonatype.org/"), 然后点击左侧的 "Staging Repositories":

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/364a90f8782e495aaf48116a1127b678~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

选中刚上传的包, 底部的 `Content` 选项卡下, 可以看到包内的文件有哪些:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f4f765c085f24a2da3a9ec1a64dd3e3b~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

确定无误后, 点击 "Close", 填写描述信息, 然后点击 "Confirm":

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/23547b3b02644087baa73c450183764c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

正常情况下, 等待几分钟后, `Activity` 选项卡中出现对号的 "Repository closed" 就是 close 成功了:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99d2658a174f4e8ea4d7822341030be6~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

然后再点击 "Release" 按钮即可发布到 MavenCentral, 等待几个小时后可以在 [search.maven.org/](https://link.juejin.cn?target=https%3A%2F%2Fsearch.maven.org%2F "https://search.maven.org/") 查询发布结果。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0878e06c62cd423d916914de99c53724~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

> 因为 Release 时勾选了 "Automatically Drop" 选项, 所以当包发布成功之后会自动从 Staging Repository 中删除。

需要注意的是, 如果你的库有好几个 component, 像我的项目就包含了 `litearouter-annotation` 、`litearouter-api` 、`litearouter-compiler` 三个, 切记一个一个按流程来, 比如:

*   `litearouter-annotation`: upload --> close --> release
*   `litearouter-api`: upload --> close --> release
*   `litearouter-compiler`: upload --> close --> release

不要一次性全部 upload, 然后又一次性 close, 最后再一次性 release, 这种做法不确定会带来什么后果, 建议不要这样搞。

五、补充
----

### 1、`Group Id` 是什么

gradle 中会使用 `implementation` 来依赖某个第三方库，结构为：`implementation GroupId:ArtifactId:Version`：

> 比如：`implementation 'com.android.support:appcompat-v7:27.1.1'`

<table><thead><tr><th align="left">组成部分</th><th align="left">解释</th></tr></thead><tbody><tr><td align="left">GroupId</td><td align="left">是项目组织的唯一标识符，在实际开发中一般对应 JAVA 的包的结构，就是 main 目录里 java 的目录结构，如 ‘com.android.support’。</td></tr><tr><td align="left">ArtifactId</td><td align="left">是项目的唯一标识符，在实际开发中一般对应项目的名称，就是项目根目录的名称, 例：appcompat-v7。</td></tr><tr><td align="left">Version</td><td align="left">是项目的版本号，例：1.0-SNAPSHOT 。其中 1.0 是版本号，SNAPSHOT 版本代表不稳定、尚处于开发中的版本。而衍生的有 Release 版本则代表稳定的版本</td></tr></tbody></table>

> 注意：这里说的是 GroupId `一般` 对应包结构，也就是说，`GroupId` 与 `包名` 是可以不一样的。就像 Android 项目里的 `applicationId` 跟 `包名` 是两个不同的概念一样。

参考资料
----

*   [Android 库发布到 Maven Central 全攻略](https://link.juejin.cn?target=https%3A%2F%2Fxiaozhuanlan.com%2Ftopic%2F6174835029 "https://xiaozhuanlan.com/topic/6174835029")
*   [Android 发布库到 MavenCentral 说明以及踩坑解决](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F1c715203c7c6 "https://www.jianshu.com/p/1c715203c7c6")

> 如果文章对您有所帮助, 请不吝点击关注一下我的微信公众号：[FSA 全栈行动](https://link.juejin.cn?target=https%3A%2F%2Fp3-juejin.byteimg.com%2Ftos-cn-i-k3u1fbpfcp%2F31960a996f1f4b0da35d69ab7480f7d6~tplv-k3u1fbpfcp-zoom-1.image "https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/31960a996f1f4b0da35d69ab7480f7d6~tplv-k3u1fbpfcp-zoom-1.image"), 这将是对我最大的激励. 公众号不仅有 Android 技术, 还有 iOS, Python 等文章, 可能有你想要了解的技能知识点哦~