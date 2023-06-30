> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7088990772808122405)

新电脑上重新装了一套环境，整理下 IDEA 的好用插件

### Lombok

> 代码生成插件。配合 Lombok 依赖及注解使用，能够大大减少 POJO（简单老式 Java 对象）的代码量。

### CodeGlance

> 代码迷你缩放图插件

### Maven Helper

> 可以查看 maven 树，同时也可以解决 maven 冲突

### Restful Fast Request（付费）

> 接口调试，无需借助其他软件 

替代品：[RestfulT](https://link.juejin.cn?target=https%3A%2F%2Fplugins.sheng90.wang%2Ffast-request%2F "https://plugins.sheng90.wang/fast-request/")ool 或者 Restful Fast Request

### Translation

> 翻译

### Alibaba Java Coding Guidelines

> 阿里巴巴代码检测工具

### SonarLint

> 代码检查

### JRebel

> 热部署

### Grep Console

> 日志检索，超赞

### MybatisX

> mapper 插件便利，需破解

其他：MybatisCodeHelperNew（收费），Free Mybatis Plugin（不收费）

### SequenceDiagram

> 代码时序图

### Leetcode Editor

> leetcode 刷题

### MyBatis Log Plugin（收费）

> 看 sql 日志（收费），替代品 [plugins.jetbrains.com/plugin/1706…](https://link.juejin.cn?target=https%3A%2F%2Fplugins.jetbrains.com%2Fplugin%2F17067-mybatislog "https://plugins.jetbrains.com/plugin/17067-mybatislog")

### EasyCode

> 生成 mybatis 类、dao、service 等

以下本人暂时没有安装的
-----------

### Tabnine(原 Codota)  

> 代码智能提示工具

竞品 copilot

### Key Promoter X

> 快捷键提示插件

### CamelCase

> 驼峰命名和下划线转换

### Save Actions 格式化插件

> 可以帮忙自动优化导入，自动给没有修改的变量添加 final 等，性能是否会有影响？

### Properties to YAML Converter

propertie 和 yaml 文件互转

### Awesome Console

> ​ 对控制台的增强，可以通过点击的方式，提供代码或连接的跳转

其他
--

### 1、如何使 local changes 显示出来（2020.1 版本后默认只有 commit 才会现实）

可以进入 Settings/Preferences/Version Control/Commit 将 Use non-modal commit interface 取消勾选

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b9f557c31b034afa9b69e18d42f82f60~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

结果如下，Local Changes 标签页又回来了～

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b569fba28d324a2da9930cdca5ae4b58~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 2、只用 IDEA 开发的话可以不配置环境变量

如果要在 idea 控制台使用 maven 或者 gradle 命令的话需要配置环境变量

### 3、自动添加和删除依赖

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b267a6de835a416a81a61afa94d70573~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 4、取消 tab 页单行显示

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/249b0d4c36444b7d99e3aec848de8c44~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a26005dd39a9443bbf867397516f3876~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

多行显示更多的文件，方便查看。

### 5、双斜杠注释改成紧跟代码头

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b667cd6e0a4a4f3097051520761b2793~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/150cde2d49554ce693d7394d169460a0~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 6、 选中复制整行

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/79e439840a58484394e33d0c36bc6703~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)  
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e9682d4e37c4d68ac9ffacb92e37f63~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

原本只会复制你选中的代码，改完配置后，就能复制整行，无论你是否完全选中。

### 7、取消匹配大小写

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7eaf85529ac3451f9846ddcfbf79b678~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

取消勾选后，输入小写 **s** ，也能提示出 **String**

### 8、优化版本控制的目录颜色展示

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28b8b25c5ec3419c9ab45132cd3fd5f7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 9、创建文件时，自动生成作者和时间信息

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e13b609933ed428390ec8f781c8bc0ee~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 10、显示行号和方法分割线

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/913a4ad1669440f69a8b3bac1a9af624~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 11、保存后自动格式化修改的代码

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3132a55f04c9490984e4c0dc8a40a5de~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 12、显示提交人和引用次数

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/14ecc1fb45414adcae42d65a8480e2b7~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp?)

### 13、自动清理 maven lastUpdate 文件

查看：find ~/.m2/repository -name "*.lastUpdated" | xargs ls

清理：find ~/.m2/repository -name "*.lastUpdated" | xargs rm

参考
--

[mp.weixin.qq.com/s/euXnd7hFA…](https://link.juejin.cn?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FeuXnd7hFArhfJ4eN4nqUUw "https://mp.weixin.qq.com/s/euXnd7hFArhfJ4eN4nqUUw")

很全的一篇：[www.bilibili.com/read/cv1368…](https://link.juejin.cn?target=https%3A%2F%2Fwww.bilibili.com%2Fread%2Fcv13682624 "https://www.bilibili.com/read/cv13682624")