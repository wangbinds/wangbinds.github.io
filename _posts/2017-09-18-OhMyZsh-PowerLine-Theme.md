---
layout: post
title: "向大家推荐oh-my-zsh和可用的powerline主题"
img: malaysia.jpg
date: 2017-09-18 19:30 +0800
description: 向大家推荐一个非常好用的Zsh配置，我现在就在用着，非常满意。
tag: Linux
---
# 向大家推荐oh-my-zsh和可用的powerline主题

[toc]

## 效果展示
![效果图](https://coding.net/u/wangbinds/p/MyZsh/git/raw/master/imgs/preview.png)

## 背景
从上面的效果图大家都能看到了，这篇文章是要给大家介绍一套命令行相关配置的。不使用命令行的同学可以绕行了。

作为一个程序员，尤其是Android程序员，一定是难免会接触到命令行的。不知道大家如何，反正我在使用了几年Linux和命令行接口之后，已经深深地喜欢上了这个强大的东西。

*虽然还有很多不懂的地方和用不习惯的地方，但是这并不影响我喜爱它。*

不过Linux虽好，却需要每个人去把它调教成自己喜欢的样子，才能完美体现出它的魅力。Linux默认的bash已经不能满足我们，现在有了更强大的**Zsh**.难以配置的Zsh让人头疼，但是我们有了强大的**oh-my-zsh**.Oh-my-zsh自带的主题不能满足期望，还有powerline主题可以使用。神奇吧，大家鼓掌～～～

## 依赖
这套配置，我们已经说的很明白了，我们依靠下面三部分内容：
* **Zsh** : 号称终极强大的shell,这从它的名字就可以知道*(z是字母表中最后一个字母)*。
* **oh-my-zsh** : 一个用于Zsh配置的开源项目，打包了Zsh的基本配置，插件和一些主题，完美解决了Zsh难以配置的问题。
* **powerline-theme** : 这是一个用于oh-my-zsh的主题项目，它完美的在终端中实现了PowerLine效果。
* **powerline-fonts** : PowerLine需要使用的字体。

## 安装
接下来我们需要逐步安装我们所需要的内容

### 安装Zsh
这一步很简单，大部分Linux发行版的软件中心都包含了Zsh,我们只需要用相应的系统命令安装即可。
我测试过的系统有Ubuntu和Fedora,对应命令列在下面。其他系统还请自行安装。

#### Ubuntu
```
$ sudo apt-get install zsh
```

#### Fedora
```
$ sudo dnf install zsh
```

### 安装oh-my-zsh
从oh-my-zsh的官网可以找到它的安装方法：

#### via curl
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### view wget
```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

### 安装PowerLine Theme
根据powerlien theme的官网给出的安装方法：

1. 通过git clone下载powerline theme
```
$ git clone https://github.com/jeremyFreeAgent/oh-my-zsh-powerline-theme.git
```

2. 创建一个符号链接将下载到的**powerline.zsh-theme**链接到**oh-my-zsh**的主题目录中。也可以通过直接运行下载到的安装脚本**install_in_omz.sh**来完成安装。
```
$ ./install_in_omz.sh
```

3. 在.zshrc文件中启用powerline主题
```
ZSH_THEME="powerline"
```

**注：这里第三步也可以不执行，后面直接使用我配置好的.zshrc文件。**

### 安装病启用字体
到这里其实我们已经可以看到点效果了，但是powerline的箭头显示有问题，着其实就是缺少字体造成的。
我们从powerline fonts官网下载所需字体，并安装然后启用字体就可以了。这里在Ubuntu和Fedora上有点区别，我分别列出。

#### Ubuntu
在Ubuntu中按照官网给的命令执行之后在终端的配置选项中仍然找不到相关的字体。因此需要手动安装字体。

1. 用git clone下载字体库
```
$ git clone https://github.com/powerline/fonts.git
```

2. 手动安装所需字体
> 这里我选择了hack字体，在下载的字体库中找到它，双击打开后，点击安装即可。

3. 在终端配置文件中启用字体
> 在终端中右击，选择“配置文件” --> “配置文件首选项”，在其中选中刚刚安装的字体即可。

#### Fedora
在Fedora中我们只要按照官网给的命令执行后，即可在终端的配置文件中找到所有安装的字体，选择你喜欢的自己即可。

1. 用git clone下载字体库
```
$ git clone https://github.com/powerline/fonts.git
```

2. 安装所有字体
```
$ ./install.sh
```

3. 在终端配置文件中启用字体
> 在终端中右击，选择“配置文件” --> “配置文件首选项”，在其中选中刚刚安装的字体即可。

## 我的配置
到这一步我们已经完成了，你应该已经看到与我类似的界面了。为什么说类似呢？因为这里还有一些可以自定义的配置。大家也可以直接使用我的配置文件。方法是：

1. 下载[我的Zsh配置](https://coding.net/u/wangbinds/p/MyZsh/)
```
$ git clone git://git.coding.net/wangbinds/MyZsh.git
```

2. 将项目中的zshrc文件链接到主目录下的.zshrc
```
$ ln -s -f /path/to/your/clone/zshrc ~/.zshrc
```

**注：将/path/to/your/clone/替换为你下载的项目地址**

## 最后欢迎大家关注我的项目
[我的Zsh配置项目](https://coding.net/u/wangbinds/p/MyZsh/)
