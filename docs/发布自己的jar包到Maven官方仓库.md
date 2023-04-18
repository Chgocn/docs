> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/Createsequence/p/15907992.html)

一、申请 GroupId
------------

我们知道，一个 maven 坐标由`groupId`，`artifactId`和`version`组成，后两者可以在 pom 中调整，而可以用在中央仓库的 `groupId`需要申请。由于 Maven 中央仓库由 sonatype 公司进行运营，所以我们首先需要[注册一个 sonatype 账号](https://issues.sonatype.org/secure/Signup!default.jspa)，通过该账号去申请一个 `groupId`。

### 1、注册

注册没什么好说的，按顺序填就行，需要注意用户名最好不要用中文。

[![](https://img.xiajibagao.top/image-20220215144939190.png)](https://img.xiajibagao.top/image-20220215144939190.png)image-20220215144939190

### 2、申请

这里的操作跟 GitHub 很像，新增相当于提出一个 issues：

[![](https://img.xiajibagao.top/image-20220215150018531.png)](https://img.xiajibagao.top/image-20220215150018531.png)

image-20220215150018531

然后填写信息：

[![](https://img.xiajibagao.top/image-20220215150401093.png)](https://img.xiajibagao.top/image-20220215150401093.png)image-20220215150401093

我们从上往下看：

1.  摘要：简单写点；
2.  Group Id：你想要申请的 Group Id，可以是：
    *   你的 GitHub 或者 Gitee 的 `GitPage`地址倒序：我的个人主页是`createsequence.github.io`，这里就可以填`io.github.createsequence`；
    *   你的域名倒序：比如我的域名是`xiajibagao.top`，这里就是 `top.xiajibagao`。
3.  Project URL：项目的仓库地址；
4.  SCM url：项目 clone 的地址；
5.  Username：即你的 sonatype 账户名称；

然后点击新建就会创建一个编号为`OSSRH-xxx`的申请 / issues。

### 3、验证

当你的申请被受理后，工作人员会给你发邮件，让你证明你申请的 Group Id 对应的域名是你自己的：

[![](https://img.xiajibagao.top/image-20220215154451409.png)](https://img.xiajibagao.top/image-20220215154451409.png)

image-20220215154451409

这里根据 URL 是个人域名还是托管平台的会有两种情况：

*   托管平台：会让你根据在仓库中创建一个指定名字的仓库；
*   个人域名：会让你在域名的 DNS 解析记录下添加一个指定内容的 text 记录；

此时，申请的状态会变为是 `Waiting for Response`，当你完成操作后，需要手动将申请的状态改为 `Open`，然后工作人员就会再一次审批。

到此为止，等待审批完成 Group Id 下发即可。

二、申请 gpg 签名
-----------

为了防止任何人都可以拿着你的账号密码去部署 jar 包，打包和发布代码前需要先根据 gpg 密匙进行加密。因此我们需要先获取一个 gpg 签名。

首先，window 电脑可以[直接下载](https://link.juejin.cn/?target=https%3A%2F%2Fgpg4win.org%2Fdownload.html)，Linux 系统用命令行 install gpg 即可。后续操作都可在命令行进行。

### 1. 生成签名

安装完毕后执行`gpg --version`查看是否成功安装，然后使用 `gpg --gen-key`命令生成签名，然后会让你输入邮箱和密码，按流程走下来即可。

生成密匙后，可以使用 `gpg --list-keys`查看已生成的密匙对应的签名：

```
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   2  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 2u
C:/Users/huang/AppData/Roaming/gnupg/pubring.kbx
---------------------------------------------
pub   rsa3072 2021-07-06 [SC]
      9E02D0E104E3D3517B5B0E54DF7B4B4D1A32FB02 # 这个就是签名
uid           [ultimate] username <email@gmail.com>

```

### 2. 上传公钥到服务器

使用 `gpg --keyserver hkp://URL --send-keys 签名`上传公钥，然后使用 `gpg --keyserver hkp://URL --recv-keys 签名`查看是否上传成功。

URL 可以用：

*   `pool.sks-keyservers.net`：这个不太稳定，容易出错；
*   `keyserver.ubuntu.com:11371`：这个相对稳定，**推荐**。

### 3. 在其他设备导入秘钥

如果更换的设备，需要在其他设备继续用这个秘钥，可以参考：[发布项目到 Maven 中央仓库 (换机器后)](https://www.ttzero.org/blog/2019/10/21/gpg-import.html)

三、Maven 配置
----------

原则上，拿着 gpg 签名，并且申请完以后就可以拿着 Group Id 去上传项目了，但是还需要先把 Maven 配置好。

### 1、配置 Maven 服务

首先，将 sonatype 账号密码，将其添加到`setting.xml`的`<servers></servers>`配置中：

```
<!-- Maven公共仓库 -->
<server>
    <id>maven-public</id>
    <username>username</username>
    <password>password</password>
</server>

```

### 2、项目信息

然后，需要在项目的 `pom.xml`文件中配置基本信息，这些信息会在上传到[中央仓库](https://mvnrepository.com/)后在 jar 包页面展示：

```
<!-- 许可证类型，一般与项目仓库保持一致 -->
<licenses>
    <license>
        <name>The Apache Software License, Version 2.0</name>
        <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
        <distribution>repo</distribution>
    </license>
</licenses>

<!-- 开发者信息 -->
<developers>
    <developer>
        <name>Createsequence</name>
        <email>841396397@qq.com</email>
        <organization>xiajibagao</organization>
    </developer>
</developers>

<!-- groupId, artifactid, version -->
<groupId>top.xiajibagao</groupId>
<artifactId>mybatis-plus-join</artifactId>
<version>1.0.0</version>
<name>mybatis-plus-join</name>
<description>mybatis plus join extend</description>
<packaging>jar</packaging>

```

### 3、打包插件

接着，为了可以正常部署，需要在 pom.xml 中添加以下四个插件：

```
<!-- 生成javadoc -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.2.0</version>
    <executions>
        <execution>
            <id>attach-javadocs</id>
            <goals>
                <goal>jar</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <source>${java.version}</source>
        <additionalJOption>-Xdoclint:none</additionalJOption>
        <failOnError>false</failOnError>
    </configuration>
</plugin>

<!-- 打包resources -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-source-plugin</artifactId>
    <version>3.2.1</version>
    <executions>
        <execution>
            <id>attach-sources</id>
            <goals>
                <goal>jar-no-fork</goal>
            </goals>
        </execution>
    </executions>
</plugin>

<!-- 打包源码 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.0</version>
    <configuration>
        <source>${java.version}</source>
        <target>${java.version}</target>
        <showWarnings>true</showWarnings>
    </configuration>
</plugin>

<!-- gpg认证 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-gpg-plugin</artifactId>
    <version>1.6</version>
    <executions>
        <execution>
            <phase>verify</phase>
            <goals>
                <goal>sign</goal>
            </goals>
        </execution>
    </executions>
</plugin>

```

注意：

`maven-javadoc-plugin`插件会检查 javadoc 格式是否严格符合标准，比如 `<p>`标签是否闭合之类的，如果注释很多不标准的话可以在插件上添加配置跳过检查：

```
<configuration>
    <!-- 禁用doclint，不严格检查 -->
    <additionalJOption>-Xdoclint:none</additionalJOption>
    <!-- 文档解析出错时仍然继续 -->
    <failOnError>false</failOnError>
</configuration>

```

### 4、发布到 OSS

最后，需要在 pom.xml 中添加部署配置

```
<distributionManagement>
    <snapshotRepository>
        <id>maven-public</id> <!-- 此处id与setting.xml中的服务id保持一致 -->
        <name>oss Snapshots Repository</name>
        <url>https://s01.oss.sonatype.org/content/repositories/snapshots/</url>
    </snapshotRepository>
    <repository>
        <id>maven-public</id> <!-- 此处id与setting.xml中的服务id保持一致 -->
        <name>oss Staging Repository</name>
        <url>https://s01.oss.sonatype.org/service/local/staging/deploy/maven2/</url>
    </repository>
</distributionManagement>

```

然后执行 `mvn clean deploy`命令，会出现弹框让你输入之前在 gpg 创建签名时输入的密码，完成后等待上传完毕。

注意：

gpg 在命令行中确认没问题，但是实际上使用 mvn 命令部署时仍然会出现找不到命令的问题，因此若出现找不到 gpg 命令之类的问题需要直接在`<profiles></profiles>`中配置脚本路径：

```
<!-- gpg -->
<profile>
    <id>maven-public</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <gpg.executable>C:\programs\GnuPG\bin\gpg</gpg.executable>
        <gpg.passphrase>you pass</gpg.passphrase>
    </properties>
</profile>

```

四、同步到 Maven 中央仓库
----------------

当执行 `mvn clean deploy`后，项目会成功上传的远程仓库，此时访问 https://s01.oss.sonatype.org/#stagingRepositories

注意，有些教程说的是 https://oss.sonatype.org/#stagingRepositories，实际上 2021-02-25 以后的新用户使用的仓库都换为了 s01 这个地址。

然后：

1.  点击 `Staging Repositories`查看待处理的 jar 包；
    
2.  选择需要发布的 jar 包；
    
3.  点击 `Close`进行检查；[![](https://img.xiajibagao.top/image-20220216091800813.png)](https://img.xiajibagao.top/image-20220216091800813.png)
    
    image-20220216091800813
    
4.  系统自动检查是否可发布，若有问题可以在 Activity 中查看；
    
5.  校验完成后，点击 `Release`即可发布到中央仓库，一般首次发布需要等一天左右，后续再发布就只要半小时即可。
    
    [![](https://img.xiajibagao.top/image-20220216092214049.png)](https://img.xiajibagao.top/image-20220216092214049.png)
    
    image-20220216092214049
    
6.  成功发布后，jar 包坐标可在[中央仓库](https://mvnrepository.com/artifact/top.xiajibagao/mybatis-plus-join/1.0.0)中查到，可以在 pom 中引用了。
    
    比如我这个示例项目：[mybaits-plus-join](https://github.com/Createsequence/mybaits-plus-join)，对应 Maven 坐标即为：
    
    ```
    <dependency>
        <groupId>top.xiajibagao</groupId>
        <artifactId>mybatis-plus-join</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    ```
    

注意：

有时候校验的时候会卡在 `Evaluating：Signature Validation`这一步，这个时候需要注意一下上面获取到的 gpg 签名的公钥是否成功上传，或者是否在 pom.xml 中把 gpg 插件给关了，这两个问题都会导致这一步校验不通过。

参考
--

*   [快速发布组件到 mavenCentral](https://juejin.cn/post/6984018498385084447)
*   [Java 上传本地 jar 包到 maven 中央仓库的坑逼经历](https://blog.csdn.net/pdsu161530247/article/details/105429597)
*   [Maven(6) Java 上传本地 jar 包到 maven 中央仓库](https://zhengqing.blog.csdn.net/article/details/94381467?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2.pc_relevant_aa&utm_relevant_index=5)
*   [Java 项目发布到 Maven 中央仓库](http://blog.gopersist.com/2020/05/28/maven-center-repository/index.html)