> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7347207466818289703#heading-7)

在官方的[文档](https://link.juejin.cn?target=https%3A%2F%2Fcentral.sonatype.org%2Fregister%2Fcentral-portal%2F "https://central.sonatype.org/register/central-portal/")中提到：

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/faab5ab52b0c45babfef0d1be3c7fac5~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=1372&s=361378&e=png&b=fefefe) 从 2024 年 3 月 12 日起，所有注册都将通过中央门户网站进行。有关传统注册的信息，请参阅相关文档。有关在门户网站早期访问期间通过传统的 OSSRH 发布方式进行发布的支持，请参阅以下部分。

所以 `2024 年 3 月 12 日`之后，[【拥抱开源】发布自己的项目到 maven 中央仓库](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2FcsdnerM%2Farticle%2Fdetails%2F128610930 "https://blog.csdn.net/csdnerM/article/details/128610930")博主原来写的教程就不适用了，所以这次博主根据他的官方文档出了一篇新教程，踩了很多的坑，最后三天终于把他弄出来了，这里我会把我遇到的坑给大家整理一下，避免大家踩坑。

如果你没看过原来的那篇文章没问题的[【拥抱开源】发布自己的项目到 maven 中央仓库](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2FcsdnerM%2Farticle%2Fdetails%2F128610930 "https://blog.csdn.net/csdnerM/article/details/128610930")，现在我们开始从 0 开始，到最后 jar 包成功发布在 maven 中央仓库上。

账号准备
====

第一步，注册账号
--------

[central.sonatype.com/](https://link.juejin.cn?target=https%3A%2F%2Fcentral.sonatype.com%2F "https://central.sonatype.com/")

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/858026661ef9443f9a68b3ce0c1a982f~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=1163006&e=png&b=161c38)

依次输入 username，邮箱地址，和密码。

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a23542f708274f8196d3c161c3240e14~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=238287&e=png&b=010101)

注册完了是这样的：

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e3ce9ab4408e4accbad621a06cdbaab5~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=1166795&e=png&b=161c38)

第二步，新建命名空间
----------

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c36e882f51d54108bf0bcce39e742fc2~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=1970&s=1095898&e=gif&f=121&b=171c38)

根据上面的教程点击创建命名空间，这里面可以看博主已经在之前创建了两个了。如果你的代码存在`Gitee`上，你就需要把命名空间设置为`io.gitee.myusername`。这里的`myusername`是你的 gitee 地址，例如博主的是`https://gitee.com/wangfugui-ma`所以我的命名空间是：`io.gitee.wangfugui-ma`

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e9e5fd88e53644569abffbc7fd21716c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=1770&s=483943&e=png&b=fefefe)

其他的仓库可以按照下面的要求来创建： **GitHub io.github.myusername GitLab io.gitlab.myusername Gitee io.gitee.myusername Bitbucket io.bitbucket.myusername**

第三步，验证命名空间
----------

创建之后需要验证命名空间来依次证明这个命名空间是你独有的，需要在对应的地址创建一个开源的仓库，名称就是你点击`verify namespace`的名称，例如下面这样：

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43d6104b0a464c2496a3b0143fc923db~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=309312&e=png&b=f2f3fa)

点击 verify namespace

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b1daf429b23d47c6bbb3c747c32d1e82~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=376728&e=png&b=767678)

创建好了之后点击`confirm`按钮即可进行下一步。

第四步，创建 push 的账号和密码
------------------

这一步抛弃了原来固定的 username 和 password，选择了一个随机的 username 和 password，这个 username 和 password 用来 push 你的 jar 包到中央仓库里面去，所以一定要保存好，以后都不会显示了，只有在创建成功的时候才会显示一次。

### 点击右上角的 view account

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/edd8dd8fdea94f68a94b797c3c1867e4~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=334430&e=png&b=f3f4fa)

### 点击 Generate User Token

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4320121c1b66431cbf3d360c42143501~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=289525&e=png&b=f3f4fa)

### 点击 ok

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/771e8e3530774c15a54a7462d3236404~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=311844&e=png&b=767678)

最终会生成一个这样的数据，大家保存好

### 设置 setting

把他粘贴到 maven 的 setting.xml 文件里面

```
<server>
	<id>${server}</id>
	<username>rRdszgHD</username>
	<password>wqKmkox2x2y2+yA/3ER/TYwX0eQoBOJYZSVLcnegAVgi</password>
</server>



```

这个`${server}`可以写成你自定义的 id，待会会用到，记住。

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d9a77aac42341f38996d46c8eb1cdeb~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1536&h=1204&s=247164&e=png&b=212121)

GPG 准备
======

第一步，下载 GPG
----------

GPG 用于创建 asc 文件用于验证你的文件的正确性和安全性，我们直接去官网下载：

[gnupg.org/download/in…](https://link.juejin.cn?target=https%3A%2F%2Fgnupg.org%2Fdownload%2Findex.html "https://gnupg.org/download/index.html")

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/46dcc3aed8a042658ef7b209f5eb28c1~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=558722&e=png&b=f0f0fc)

第二步，生成秘钥
--------

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e49ab74050be44c8a3ed8e7a4d2eb018~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1721&h=1168&s=187997&e=png&b=ffffff)

去图中的路径的 bin 路径下，进入 cmd 命令行

### 生成秘钥

输入`gpg --gen-key`

依次输入名称，邮箱地址，**名称输入你命名空间的名称**

```
E:\dev\gpg2\GnuPG\bin>gpg --gen-key
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: wangfugui-ma
Email address: masiyi163163@163.com
You selected this USER-ID:
    "wangfugui-ma <masiyi163163@163.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: revocation certificate stored as 'C:\\Users\\admin\\AppData\\Roaming\\gnupg\\openpgp-revocs.d\\4CB3D9314CD5F1277582A11F4ADBA3851D627E38.rev'
public and secret key created and signed.

pub   ed25519 2024-03-17 [SC] [expires: 2027-03-17]
      4CB3D9314CD5F1277582A11F4ADBA3851D627E38
uid                      wangfugui-ma <masiyi163163@163.com>
sub   cv25519 2024-03-17 [E] [expires: 2027-03-17]



```

这里会弹出一个让你输入密钥并二次确认的窗口, 记住你的密码，后面发布的时候会用。

### 发布秘钥

上面 4CB3D9314CD5F1277582A11F4ADBA3851D627E38 就是你的密钥 id

```
gpg --keyserver keyserver.ubuntu.com --send-keys 4CB3D9314CD5F1277582A11F4ADBA3851D627E38


```

这里发布到`keyserver.ubuntu.com`服务器上，这样中央仓库也有你的密钥，所以它才能验证你的身份

> As SKS Keyserver Network is being deprecated we recommend the use an specific GPG keyserver. Current GPG Keyservers supported by Central Servers are: **keyserver.ubuntu.com keys.openpgp.org pgp.mit.edu**

如果发布失败开源用上面的其他两个地址

### 验证秘钥

验证秘钥是否发布成功：

```
gpg --keyserver keyserver.ubuntu.com --recv-keys 4CB3D9314CD5F1277582A11F4ADBA3851D627E38


```

出现下面内容则说明发布成功

```
E:\dev\gpg2\GnuPG\bin>gpg --keyserver keyserver.ubuntu.com --recv-keys 4CB3D9314CD5F1277582A11F4ADBA3851D627E38
gpg: key 4ADBA3851D627E38: "wangfugui-ma <masiyi163163@163.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1


```

发布 jar 包
========

第一步，编辑 pom 文件
-------------

在你的项目 pom 文件中加入以下的内容：

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>


    <groupId>io.gitee.wangfugui-ma</groupId>
    <artifactId>aliyun-oss-spring-boot-starter</artifactId>
    <version>0.0.1</version>
    <name>aliyun-oss-spring-boot-starter</name>
    <description>aliyun-oss-spring-boot-starter</description>
    <url>https://gitee.com/wangfugui-ma/aliyun-oss-spring-boot-starter</url>

    <licenses>
        <license>
            <name>The Apache Software License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>

    <developers>
        <developer>
            <id>masiyi</id>
            <name>masiyi</name>
            <email>masiyi163163@163.com</email>
            <roles>
                <role>Project Manager</role>
                <role>Architect</role>
            </roles>
        </developer>
    </developers>


    <scm>
        <connection>https://gitee.com/wangfugui-ma/aliyun-oss-spring-boot-starter.git</connection>
        <developerConnection>scm:git:ssh://git@gitee.com:wangfugui-ma/aliyun-oss-spring-boot-starter.git
        </developerConnection>
        <url>https://gitee.com/wangfugui-ma/aliyun-oss-spring-boot-starter</url>
    </scm>


    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
            <version>2.2.9.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>2.0.4</version>
        </dependency>

        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>3.17.2</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>

    </dependencies>

    <build>
        <plugins>

            <!--   central发布插件    -->
            <plugin>
                <groupId>org.sonatype.central</groupId>
                <artifactId>central-publishing-maven-plugin</artifactId>
                <version>0.4.0</version>
                <extensions>true</extensions>
                <configuration>
                    <publishingServerId>masiyi</publishingServerId>
                    <tokenAuth>true</tokenAuth>
                </configuration>
            </plugin>
            <!--   source源码插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.2.1</version>
                <executions>
                    <execution>
                        <id>attach-sources</id>
                        <goals>
                            <goal>jar-no-fork</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!--   javadoc插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-javadoc-plugin</artifactId>
                <version>2.9.1</version>
                <executions>
                    <execution>
                        <id>attach-javadocs</id>
                        <goals>
                            <goal>jar</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-gpg-plugin</artifactId>
                <version>1.5</version>
                <configuration>
                    <executable>E:\dev\gpg2\GnuPG\bin\gpg.exe</executable>
                    <keyname>wangfugui-ma</keyname>
                </configuration>
                <executions>
                    <execution>
                        <id>sign-artifacts</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>sign</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

        </plugins>
    </build>

</project>



```

除了`dependencies`里面的内容是根据你的项目里面的实际情况写，其他的都必须写，否则会上传失败。

第二步，打包上传
--------

**mvn** 先 clean 后 deploy

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0e09cff409834c058749ef6ded2fc077~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2100&s=756092&e=gif&f=148&b=fdfcfc)

这个时候会让你输入你在开头设置的密码，输入成功开始打包上传。

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/616f865aa47f4d10acd64c42e7227478~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2152&s=755724&e=png&b=fcfbfb)

打包成功

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf7a51338a854f099a38d2f76b7f5622~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2152&s=747515&e=png&b=fdfcfc)

第三步，发布 jar 包
------------

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6407c19d7de84c1b99273ad069eb9087~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2080&s=399019&e=png&b=f3f4fb)

打包成功之后`Deployments`模块里面也有对应的步骤，刚刚我们在 idea 控制台看到的 id 是：`50706b21-6fd9-4017-9b43-31e8d05b8566`也就对应这个页面里面的`Deployment ID`

这个时候我们点击`Publish`意思就是将我们的 jar 包发布了

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5679366cd6534691a72ce5e4fc325d6a~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=343081&e=png&b=f3f4fb)

第四步，搜索我们的 jar 包
---------------

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/924002af71d841c8a329a8d32d882ba5~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=792086&e=png&b=f3f4fa)

可以看到成功搜索到了我们发布的 jar 包

注意事项
====

*   publishingServerId 里面的值要对应 setting 文件里面的 id
*   之前有 gpg 秘钥的时候请先导出，如何把原来的删除，否则会一直验证失败
*   使用 mvn clean deploy 命令的时候请指定 setting 文件地址，如果在 idea 打包则不需要
*   Generate User Token 如果重新生成，之前的就会用不了，会报 401 错误
*   使用 gpg 生成秘钥的时候名称一定要填命名空间里面的名称，否则打包会一直验证失败
*   一定要用我发出来的 pom 文件里面的结构，否则会打包失败

三天验证，打包快 100 次失败写出来的教程，希望大家不吝啬手中的三连！！！

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1528931a4981497d928e6e094ab47f57~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=3840&h=2022&s=376066&e=png&b=f2f3fa)

> ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bf76da105fc64df996ca9e1fc37181a3~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=859&h=161&s=110014&e=png&b=fbfbfb)