> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [developer.aliyun.com](https://developer.aliyun.com/article/1049030)

> Android 发布开源库到 JitPack (详细步骤)

2022-10-14 242 举报

**简介：** Android 发布开源库到 JitPack (详细步骤)

+ 关注继续查看

**版权声明：**本文内容由阿里云实名注册用户自发贡献，版权归原作者所有，阿里云开发者社区不拥有其著作权，亦不承担相应法律责任。具体规则请查看《[阿里云开发者社区用户服务协议](https://developer.aliyun.com/article/768092)》和《[阿里云开发者社区知识产权保护指引](https://developer.aliyun.com/article/768093)》。如果您发现本社区中有涉嫌抄袭的内容，填写[侵权投诉表单](https://yida.alibaba-inc.com/o/right)进行举报，一经查实，本社区将立刻删除涉嫌侵权内容。

[API](https://developer.aliyun.com/label/sc/article_de-3-100252) [开发工具](https://developer.aliyun.com/label/sc/article_de-3-100016) [Android 开发](https://developer.aliyun.com/label/sc/article_de-3-100015) [git](https://developer.aliyun.com/label/sc/article_de-3-100234) [开发者](https://developer.aliyun.com/label/sc/article_de-3-100267)

[Android jitpack](https://www.aliyun.com/sswb/625833.html) [Android 发布](https://www.aliyun.com/sswb/332352.html)

[开发者社区](https://developer.aliyun.com/) > [开发与运维](https://developer.aliyun.com/group/othertech/) > [文章](https://developer.aliyun.com/group/othertech/article/)

![](https://oneconsole.alicdn.com/sitemap/img/dev_chanpin.png) [云迁移中心](https://www.aliyun.com/product/apds)

作者高分内容

[更多](https://developer.aliyun.com/profile/y6dbi3g7kyd6w/highScore_1)

[Android 组件化（二）注解与注解处理器、组件通讯 105](https://developer.aliyun.com/article/1080836) [Android 组件化（一）项目模式管理、模式切换 118](https://developer.aliyun.com/article/1080834) [Android 天气 APP（三十六）运行到本地 AS、更新项目版本依赖、去掉 ButterKnife 136](https://developer.aliyun.com/article/1080830) [Android Studio Dolphin | 2021.3.1 不显示布局 XML 预览 1649](https://developer.aliyun.com/article/1050478) [Android 低功耗蓝牙开发简述 193](https://developer.aliyun.com/article/1050473)

评论

相关文章

[![](https://ucc.alicdn.com/pic/developer-ecology/6w5iiviqxobva_a6f9a872a59447bbb458db3af59ef80b.png?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/1127008)

![](https://ucc.alicdn.com/avatar/avatar3.jpg)

[游客 6w5iiviqxobva](https://developer.aliyun.com/profile/6w5iiviqxobva)

[【 uniapp 】打包 Android 的 apk(原生 APP - 云打包)，及发布测试](https://developer.aliyun.com/article/1127008)

【 uniapp 】打包 Android 的 apk(原生 APP - 云打包)，及发布测试

460 0

[![](https://ucc.alicdn.com/pic/developer-ecology/2ff485fcb2a8481282662bf1448087ba.png?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/1062229)

![](https://ucc.alicdn.com/avatar/0140d7e28e88430a92df17f54c19e052.png)

[程序猿在广东](https://developer.aliyun.com/profile/bzq7yb74j52jc)

[Android Studio 获取开发版 SHA1 值和发布版 SHA1 值的史上最详细方法](https://developer.aliyun.com/article/1062229)

今天我想把百度地图的定位集成到项目中来，想写个小小的案例，实现一下，但在集成百度地图时首先要申请秘钥，申请秘钥要用到 SHA1 值，所以今天就来总结一下怎样去获取这个值吧，希望对大家有帮助。

739 0

[![](https://ucc.alicdn.com/oskhugbn6jtys_20230516_18e025359b9846b6b610c225954612be.webp?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/998005)

![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg)

[聚优云惠](https://developer.aliyun.com/profile/oskhugbn6jtys)

[Android Bintray、JCenter 替代方案 MavenCentral(发布 jar,aar 到 Maven 中央仓库)](https://developer.aliyun.com/article/998005)

Android Bintray、JCenter 替代方案 MavenCentral(发布 jar,aar 到 Maven 中央仓库)

358 0

![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg)

[聚优云惠](https://developer.aliyun.com/profile/oskhugbn6jtys)

[Android 如何通过 Gradle 发布 Android 依赖库（aar）到 jitpack 公共仓库](https://developer.aliyun.com/article/997905)

Android 如何通过 Gradle 发布 Android 依赖库（aar）到 jitpack 公共仓库

263 0

![](https://ucc.alicdn.com/avatar/img_fd32b6915af67b97f63ffd8c06f85590.jpg)

[聚优云惠](https://developer.aliyun.com/profile/oskhugbn6jtys)

[Android 如何通过 Gradle 发布 java 依赖库（jar）到 jitpack 公共仓库（—）](https://developer.aliyun.com/article/997902)

Android 如何通过 Gradle 发布 java 依赖库（jar）到 jitpack 公共仓库（—）

157 0

[![](https://ucc.alicdn.com/pic/developer-ecology/ef913c56351849d49a80f4aa624db854.png?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/950813)

![](https://ucc.alicdn.com/avatar/2886848136f54069855dc62b7487858b.png)

[yechaoa](https://developer.aliyun.com/profile/yrvvnalewdp3q)

[如何把自己的 Android Library 发布到 GitHubd 依赖使用](https://developer.aliyun.com/article/950813)

如何把自己的 Android Library 发布到 GitHubd 依赖使用

58 0

[![](https://ucc.alicdn.com/pic/developer-ecology/8f531bd8bd224b9a99291fd414dee2bf.png?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/942105)

![](https://ucc.alicdn.com/avatar/b5faf17b60ad44e694eb407be47bc46f.jpg)

[运维开发故事](https://developer.aliyun.com/profile/vrfnikl2juwkk)

[使用 Jenkins 发布 Android 项目](https://developer.aliyun.com/article/942105)

使用 Jenkins 发布 Android 项目

330 0

[![](https://ucc.alicdn.com/pic/developer-ecology/922317d6341c4e61ab2fe08684dc17ac.jpeg?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/939217)

![](https://ucc.alicdn.com/avatar/avatar3.jpg)

[xaubllxwtvaqiu](https://developer.aliyun.com/profile/lzjpxl6ms2dp6)

[微软正式发布 Windows 11，支持 Android 应用，面向开发者友好](https://developer.aliyun.com/article/939217)

微软正式发布 Windows 11，支持 Android 应用，面向开发者友好

87 0

[![](https://ucc.alicdn.com/pic/developer-ecology/84c579030e764aa791828c11e98459cd.png?x-oss-process=image/resize,h_118)](https://developer.aliyun.com/article/939172)

![](https://ucc.alicdn.com/avatar/avatar3.jpg)

[xaubllxwtvaqiu](https://developer.aliyun.com/profile/lzjpxl6ms2dp6)

[Android 12 将允许运行 5 个 Chrome 实例；Furion v3.0.0.rc 发布 | 思否周刊](https://developer.aliyun.com/article/939172)

Android 12 将允许运行 5 个 Chrome 实例；Furion v3.0.0.rc 发布 | 思否周刊

84 0

![](https://ucc.alicdn.com/avatar/avatar3.jpg)

[mogqp3unktbnq](https://developer.aliyun.com/profile/mogqp3unktbnq)

[Android 发布应用市场资料](https://developer.aliyun.com/article/924357)

应用内：建议 512*512 70px 应用截图：480*800 1080*1920 应用市场基本都能覆盖到 不超过 1MB（以小米手机为原因，状态栏不要带任何 app 的 logo）

102 0

[![](https://ucc.alicdn.com/avatar/eea0824b1c064c0096156c9c2cdf2cd9.jpg?x-oss-process=image/resize,h_150,m_lfit)](https://developer.aliyun.com/profile/y6dbi3g7kyd6w)

![](https://ucc.alicdn.com/pic/ucc-admin/4c31495eb5904b729ac6f0d6b52f8eb3.png)

+ 关注

[初学者 - Study](https://developer.aliyun.com/profile/y6dbi3g7kyd6w)

Android 开源爱好者

[190](https://developer.aliyun.com/profile/y6dbi3g7kyd6w/article_1)

文章

[2](https://developer.aliyun.com/profile/y6dbi3g7kyd6w/ask_1)

问答

[0](https://developer.aliyun.com/profile/y6dbi3g7kyd6w/live_1)

视频

作者高分内容

[更多](https://developer.aliyun.com/profile/y6dbi3g7kyd6w/highScore_1)

[Android 组件化（二）注解与注解处理器、组件通讯 105](https://developer.aliyun.com/article/1080836) [Android 组件化（一）项目模式管理、模式切换 118](https://developer.aliyun.com/article/1080834) [Android 天气 APP（三十六）运行到本地 AS、更新项目版本依赖、去掉 ButterKnife 136](https://developer.aliyun.com/article/1080830) [Android Studio Dolphin | 2021.3.1 不显示布局 XML 预览 1649](https://developer.aliyun.com/article/1050478) [Android 低功耗蓝牙开发简述 193](https://developer.aliyun.com/article/1050473)

文章排行榜

最热

最新

[1 代码的简单设计五原则 31630](https://developer.aliyun.com/article/1205073)

[2 java 泛型 万字详解（通俗易懂) 28663](https://developer.aliyun.com/article/1205089)

[3 阿里云检索分析服务 Elasticsearch 版内核持续优化 25728](https://developer.aliyun.com/article/1204940)

[4 【分布式技术专题】「分布式技术架构」手把手教你如何开发一个属于自己的限流器 RateLimiter 功能服务 22166](https://developer.aliyun.com/article/1205440)

[5 Hive SQL on Flink 构建流批一体引擎 16536](https://developer.aliyun.com/article/1205921)

[6 PolarDB-X 致数据库行内人 (一) ~ 如何有效评测国产数据库的分布式事务 15778](https://developer.aliyun.com/article/1194957)

[7 实践教程之体验 PolarDB-X 分布式事务和数据分区 10833](https://developer.aliyun.com/article/1191306)

[8 Python3，自从掌握了这个方法，再也不用 print 进行调试了。 15238](https://developer.aliyun.com/article/1190832)

[9 现场直击：2023 阿里云峰会精彩发布抢先看（内含福利） 336067](https://developer.aliyun.com/article/1190853)

[10 实践教程之如何在 ARM 平台部署 PolarDB-X 9858](https://developer.aliyun.com/article/1206144)

[11 如何解决 IoT 数据入仓的客户实践 9087](https://developer.aliyun.com/article/1201889)

[12 如何在 5 分钟之内完成一个物联网统计指标？ 7686](https://developer.aliyun.com/article/1205907)

[13 大数据 & AI 产品月刊【2023 年 4 月】 5895](https://developer.aliyun.com/article/1202497)

[14 ClickHouse 创始人：融合数据库该 “卷” 的还是性能和速度 55875](https://developer.aliyun.com/article/1191604)

[15 阿里云联合埃森哲正式发布《跨国企业上云登陆区（Landing Zone）白皮书》 51251](https://developer.aliyun.com/article/1192014)

[16 除了价格降 70%，关于对象存储预留空间你还需要了解这些 32823](https://developer.aliyun.com/article/1191290)

[17 编程中最难的就是命名？这几招教你快速上手 32756](https://developer.aliyun.com/article/1191967)

[18 RocketMQ 多级存储设计与实现 21333](https://developer.aliyun.com/article/1191787)

[19 Spring Boot 单体应用一键升级成 Spring Cloud Alibaba 2993](https://developer.aliyun.com/article/1205581)

[20 实践教程之基于 Prometheus+Grafana 的 PolarDB-X 监控体系 34293](https://developer.aliyun.com/article/1191294)

[1 不一样的 ECS U 实例 325](https://developer.aliyun.com/article/1241503)

[2 NineData，稳定、高效的 Redis 数据同步解决方案 210](https://developer.aliyun.com/article/1241011)

[3 30 分钟，一键拉起基于 LLM + AnalyticDB PostgreSQL 构建的企业专属 Chatbot 358](https://developer.aliyun.com/article/1240791)

[4 python 小知识点总结 59](https://developer.aliyun.com/article/1240403)

[5 AIGC 创作家 628](https://developer.aliyun.com/article/1240296)

[6 当 MySQL 想恋爱，java 和 navicate 抢着做媒婆 ------ java 连接 MySQL 数据库 & navicat for MySQL 连接 660](https://developer.aliyun.com/article/1240291)

[7 亿视电子基于 PolarDB-X 打造能源数字基座实践 529](https://developer.aliyun.com/article/1240206)

[8 数据库内核那些事｜深度解析 PolarDB DDL 锁的优化和演进 5745](https://developer.aliyun.com/article/1240133)

[9 MindOpt Tuner 调参器，提升求解速度、性能（一） 16434](https://developer.aliyun.com/article/1240073)

[10 全面升级，重磅上线！容器服务 ASK 评测携大奖前来！ 593](https://developer.aliyun.com/article/1240069)

[11 解锁技术人的 “成长公式” 5837](https://developer.aliyun.com/article/1240027)

[12 WebAssembly 入门：构建高性能的浏览器应用 10305](https://developer.aliyun.com/article/1239998)

[13 使用 React Native 构建跨平台桌面应用程序 10298](https://developer.aliyun.com/article/1239996)

[14 前端学习笔记 202304 学习笔记第八天 - web 前端架构学习笔记 - 1 115](https://developer.aliyun.com/article/1239970)

[15 尾号限行 API 实现微信小程序车辆尾号限行查询功能 10520](https://developer.aliyun.com/article/1239830)

[16 挑战 “三大框架” 的解决方案 10246](https://developer.aliyun.com/article/1239826)

[17 阿里云丁宇：云上开发成为主流，Serverless 定义新范式 6420](https://developer.aliyun.com/article/1239808)

[18 如何在 Python 中将数字转换为字母？ 53](https://developer.aliyun.com/article/1239800)

[19 阿里云 AI 新品 “通义听悟”，开放公测！ 2108](https://developer.aliyun.com/article/1239787)

[20 web 前端课程设计：个人简历 10815](https://developer.aliyun.com/article/1239783)

相关课程

[更多](https://developer.aliyun.com/course/explore)

[

开源 Android 容器化框架 Atlas 开发者指南

](https://developer.aliyun.com/learning/course/392)

![](https://img.alicdn.com/imgextra/i4/O1CN01usl7VE1voLwF9ZPPt_!!6000000006219-2-tps-28-32.png) 1512

![](https://img.alicdn.com/imgextra/i2/O1CN01QxBFSF247bVrecdi7_!!6000000007344-2-tps-34-30.png) 7

[去学习](https://developer.aliyun.com/learning/course/392)

推荐文章

[现场直击：2023 阿里云峰会精彩发布抢先看](https://developer.aliyun.com/article/1190853) [AIGC 主题征文开启，参与赢取富士拍立得](https://developer.aliyun.com/article/1209568) [乘风伯乐招募令，邀请伯乐获取多重好礼！](https://developer.aliyun.com/article/1209561) [乘风者计划邀您入驻社区，精彩权益即刻享](https://developer.aliyun.com/topic/bloggers?utm_content=g_1000288847)

相关电子书

[更多](https://developer.aliyun.com/ebook)

 [![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png)](https://developer.aliyun.com/ebook/5189) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png)](https://developer.aliyun.com/ebook/7576) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png)](https://developer.aliyun.com/ebook/347) 

 [![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png)](https://developer.aliyun.com/ebook/5189) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png)](https://developer.aliyun.com/ebook/7576) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png)](https://developer.aliyun.com/ebook/347) 

 [![](https://img.alicdn.com/imgextra/i4/O1CN01SDC8jQ26jjhZ1DDX2_!!6000000007698-2-tps-2480-3366.png)](https://developer.aliyun.com/ebook/5189) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/6ee3d8f4ed4f4b648799089be22d05d9.png)](https://developer.aliyun.com/ebook/7576) 

 [![](https://ucc.alicdn.com/pic/developer-ecology/298a1b8c5616409592b6896746bba758.png)](https://developer.aliyun.com/ebook/347) 

蚂蚁聚宝 Android 秒级编译——Freeline

[立即下载](https://developer.aliyun.com/ebook/download/5189)

低代码开发师（初级）实战教程

[立即下载](https://developer.aliyun.com/ebook/download/7576)

阿里巴巴 DevOps 最佳实践手册

[立即下载](https://developer.aliyun.com/ebook/download/347)

相关镜像

[packman](https://developer.aliyun.com/mirror/packman)