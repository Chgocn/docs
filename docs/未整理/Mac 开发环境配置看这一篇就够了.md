> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7198081187955802171)

#### 前言

从 **macOS Catalina** 开始，`Mac` 使用 **zsh** 作为默认登录 `Shell` 和交互式 `Shell`。当然你也可以[修改默认 Shell](https://link.juejin.cn?target=https%3A%2F%2Fsupport.apple.com%2Fzh-cn%2FHT208050 "https://support.apple.com/zh-cn/HT208050")，但一般没这个必要。而实际开发中经常会遇到一些环境问题导致的报错，下面我们就讲一下一些常用库的环境配置以及原理。

#### 一、Homebrew

作为 Mac 上最常用的包管理器，[Homebrew](https://link.juejin.cn?target=https%3A%2F%2Fbrew.sh%2Findex_zh-cn "https://brew.sh/index_zh-cn") 可以称为神器，用它来管理 Mac 上的依赖环境便捷又省心。

##### 1. 安装

这里我们直接在终端执行国人写的[一键安装脚本](https://link.juejin.cn?target=https%3A%2F%2Fgitee.com%2Fcunkai%2FHomebrewCN "https://gitee.com/cunkai/HomebrewCN")，换源（官方源的速度你懂的）啥的都直接安排上了。

```
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"


```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0f5beadcf804f6f9a79ed84363dd2df~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

这里我们选择`1、中科大下载源`就好了，按照提示输入并耐心等待安装完成。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3476ae71f331480eaa3b18f2db9aa8bf~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f70e662452ec4d8fad0fc5779027b010~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp)

最后一步重载配置文件我们执行`source ~/.zshrc`，重载用户目录下的`.zshrc`。

到这里我们可以执行`brew -v`测试一下 **Homebrew** 的安装结果：

```
~:~$brew -v
Homebrew 3.6.21-26-gb0a74e5
Homebrew/homebrew-core (git revision 4fbf6930104; last commit 2023-02-08)
Homebrew/homebrew-cask (git revision cbce859534; last commit 2023-02-09)


```

有版本号输出说明已经安装完成了。

##### 2. 卸载

直接在终端执行一键脚本即可

```
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"


```

##### 3. 常用命令

```
/// 安装
brew install FORMULA|CASK...

/// 搜索
brew search TEXT|/REGEX/

/// 卸载包
brew uninstall FORMULA|CASK...

/// 查看安装列表
brew list [FORMULA|CASK...]

/// 查看包信息
brew info [FORMULA|CASK...]

/// 查看哪些包可以更新
brew outdated 

/// 更新指定包（安装新包，但旧包依旧保留）
brew upgrade [FORMULA|CASK...]

/// 更新Homebrew
brew update                  

/// 清理旧版本和缓存
brew cleanup       					# 清理所有包的旧版本
brew cleanup [FORMULA ...]  # 清理指定包的旧版本
brew cleanup -n             # 查看可清理的旧版本包，不执行实际操作

/// 锁定不想更新的包（因为update会一次更新所有的包的，当我们想忽略的时候可以使用这个命令）
brew pin [FORMULA ...]      # 锁定某个包
brew unpin [FORMULA ...]    # 取消锁定

/// 软件服务管理
brew services list  								# 查看使用brew安装的服务列表
brew services run formula|--all  		# 启动服务（仅启动不注册）
brew services start formula|--all  	# 启动服务，并注册
brew services stop formula|--all   	# 停止服务，并取消注册
brew services restart formula|--all # 重启服务，并注册


```

#### 二、Ruby

##### 1. 安装

其实 **Mac** 系统默认已经有`Ruby`的环境了，在终端中执行`ruby -v`查看版本号。

```
~:~$ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580) [universal.x86_64-darwin20]


```

本地`ruby`版本有点低了，这里我们使用`Homebrew`来更新，

```
brew install ruby


```

执行结束后默认会将最新版本的`ruby`安装到`/usr/local/Cellar/`目录下。

我们查看一下当前的`ruby`版本：

```
~:~$ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580) [universal.x86_64-darwin20]


```

好像版本并未发生变化，**why？** 这里主要是因为 **Shell** 环境中并没有读到最新的`ruby`路径，我们可以再编辑一下用户目录下的环境配置文件`~/.zshrc`，新增`ruby`的路径并写入环境变量：

```
# 环境变量配置
export RUBY=/usr/local/Cellar/ruby/3.2.0/bin
export GEMS=/usr/local/lib/ruby/gems/3.2.0/bin

# 写入环境变量
export PATH=$RUBY:$GEMS:$PATH


```

这里先添加上面的内容然后执行`source ~/.zshrc`，后面会讲到`Shell`环境配置相关的内容。

再次查看`ruby`版本：

```
~:~$ruby -v
ruby 3.2.0 (2022-12-25 revision a528908271) [x86_64-darwin20]


```

此时可以看到`ruby`已经升级到最新的 **3.2.0** 版本。

当然我们还可以执行`which ruby`查看当前的`ruby`的具体路径：

```
~:~$which ruby
/usr/local/Cellar/ruby/3.2.0/bin/ruby


```

从结果可以看出当前使用的`ruby`正是我们在`.zshrc`中配置的路径。

##### 2. Gem 换源

`Gem`是`ruby`的包管理器，一些`ruby`库我们需要使用`Gem`来安装，但`Gem`官方源速度拉胯，这里我们需要替换为国内源。

```
/// 添加国内源并删除官方源
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

/// 查看当前源地址
gem sources -l


```

查看当前源，确认已替换为国内源即可。

```
~:~$gem sources -l
*** CURRENT SOURCES ***

https://gems.ruby-china.com/


```

##### 3. 常用包安装

```
/// cocoapods安装
gem install cocoapods

/// fastlane安装
gem install fastlane


```

耐心等待安装完成后我们可以测试一下：

```
~:~$pod --version
1.11.3

~:~$fastlane --version
fastlane installation at path:
/usr/local/lib/ruby/gems/3.2.0/gems/fastlane-2.211.0/bin/fastlane
-----------------------------
[✔] 🚀
fastlane 2.211.0


```

从结果可以看出`cocoapods`和`fastlane`都安装完成了。

#### 三、Python

##### 1. 使用 Xcode 自带 Python 库（推荐）

其实 **Xcode** 命令行工具自带了`python`库，项目中需要执行`python`脚本的优先使用这个会更合适，因为 **Xcode** 编译项目时会优先使用这个`python`库，**Mac** 中仅使用这一个版本可以避免一些多`python`版本环境问题导致的报错。

根据当前 **Xcode** 命令行工具中的`python`版本，这里我们需要在`~/.zshrc`中添加相关配置并执行`source ~/.zshrc`重载配置：

```
# 环境变量配置
export PYTHON=/Library/Developer/CommandLineTools/Library/Frameworks/Python3.framework/Versions/3.8/Python3/bin

# 写入环境变量
export PATH=$PYTHON:$PATH

# 别名
alias python=python3
alias pip=pip3


```

这里使用别名以便于执行`python`命令时使用的是`python3`， 查看一下版本，结果也符合预期。

```
~:~$python --version
Python 3.8.9


```

##### 2. 使用 Homebrew 安装

这里我们直接执行：

```
brew install python


```

耐心等待安装完成，其实 **Homebrew** 会将`Python`安装到`/usr/local/Cellar/`目录下，并在`/usr/local/bin`目录创建了链接文件。这里我们需要在`~/.zshrc`中添加相关配置并执行`source ~/.zshrc`重载配置：

```
# 环境变量配置
export SBIN=/usr/local/bin:/usr/local/sbin

# 写入环境变量
export PATH=$SBIN:$PATH

# 别名
alias python=python3
alias pip=pip3


```

查看一下版本，已经升级到最新版：

```
~:~$python --version
Python 3.10.10


```

##### 3. pip 换源

`pip`是`python`的包管理器，我们可以使用它来安装一些`python`库。我们可以更换一个国内源来提升下载速度：

```
/// 查看当前源
pip config list

/// 替换为清华大学源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

/// 还原为默认源
pip config unset global.index-url


```

*   常用的国内镜像源
    
    ```
    阿里云 http://mirrors.aliyun.com/pypi/simple
    豆瓣http://pypi.douban.com/simple
    清华大学 https://pypi.tuna.tsinghua.edu.cn/simple
    中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple
    华中科技大学 http://pypi.hustunique.com
    中科大 https://pypi.mirrors.ustc.edu.cn/simple
    
    
    ```
    

##### 4. 常用包安装

```
/// openpyxl安装
pip install openpyxl


```

安装速度非常快：

```
~:~$pip install openpyxl
Collecting openpyxl
  Using cached openpyxl-3.1.0-py2.py3-none-any.whl (250 kB)
Requirement already satisfied: et-xmlfile in /usr/local/lib/python3.10/site-packages (from openpyxl) (1.1.0)
Installing collected packages: openpyxl
Successfully installed openpyxl-3.1.0


```

#### 四、Shell 环境配置

##### 1. zsh 的配置文件.zshrc

从 **macOS Catalina** 开始，`Mac` 使用 **zsh** 作为默认`shell`，而它的配置文件是用户目录下的`.zshrc`文件，所以我们之前在定义环境变量时都会编辑这个文件。每次打开终端时都会读取这个配置文件，如果需要在当前的`shell`窗口读取最新的环境配置则需要执行`source ~/.zshrc`，这也是之前我们编辑该文件后重载配置的原因（为了让最新的配置生效😁）。

##### 2. 定义环境变量（全局变量）

```
export RUBY=/usr/local/Cellar/ruby/3.2.0/bin


```

其实我们之前在讲`Ruby`的安装时已经在`~/.zshrc`文件中定义过全局变量，语法就是在一个变量名前面加上`export`关键字。这里我们可以在终端输出一下这个变量：

```
~:~$echo $RUBY
/usr/local/Cellar/ruby/3.2.0/bin


```

变量的值可以正常输出，这也意味着这样的变量在当前`shell`程序中全局可读。

##### 3. 写入环境变量

常见的环境变量：

*   `CDPATH`：冒号分隔的目录列表，作为`cd`命令的搜索路径
*   `HOME`：当前用户的主目录
*   `PATH`：`shell`查找命令的目录列表，由冒号分隔
*   `BASH`：当前`shell`实例的全路径名
*   `PWD`：当前工作目录

这里重点关注一下`PATH`变量，当我们在`shell`命令行界面中输入一个外部命令时，`shell`必须搜索系统来找到对应的程序。`PATH`环境变量定义了用于进行命令和程序查找的目录：

```
echo $PATH


```

某些时候我们执行命令会遇到`command not found`这样的报错，比如：

```
~:~$hi
zsh: command not found: hi


```

这是因为`PATH`中的目录并没有包含`hi`命令，所以我们执行`hi`就报错。同理，当我们在配置环境时，某些库的目录需要被写入到`PATH`中，比如：

```
# 环境变量配置
export SBIN=/usr/local/bin:/usr/local/sbin
export HOMEBREW=/usr/local/Homebrew/bin
export RUBY=/usr/local/Cellar/ruby/3.2.0/bin
export GEMS=/usr/local/lib/ruby/gems/3.2.0/bin

# 写入环境变量
export PATH=$SBIN:$HOMEBREW:$RUBY:$GEMS:$PATH


```

这样当我们执行具体的命令时，`shell`才能够正确的访问。

*   **附.zshrc 常见配置**
    
    ```
    # 环境变量配置
    export SBIN=/usr/local/bin:/usr/local/sbin
    export HOMEBREW=/usr/local/Homebrew/bin
    export RUBY=/usr/local/Cellar/ruby/3.2.0/bin
    export GEMS=/usr/local/lib/ruby/gems/3.2.0/bin
    
    # 写入环境变量
    export PATH=$SBIN:$HOMEBREW:$RUBY:$GEMS:$PATH
    
    # 别名
    alias python=python3
    alias pip=pip3
    
    # 编码
    export LC_ALL=en_US.UTF-8
    export LANG=en_US.UTF-8
    
    # 控制PS1信息
    PROMPT='%U%F{51}%1~%f%u:~$'
    
    # 镜像源
    export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
    
    
    ```
    

#### 五、参考文档

*   [Homebrew 常用命令](https://link.juejin.cn?target=https%3A%2F%2Fblog.csdn.net%2Fruziwang%2Farticle%2Fdetails%2F124034504 "https://blog.csdn.net/ruziwang/article/details/124034504")
*   [Python pip 国内换源的几个办法](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fd02d0b16163b "https://www.jianshu.com/p/d02d0b16163b")