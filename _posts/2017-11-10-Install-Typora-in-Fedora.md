---
layout: post
title: "Markdown编辑器Typora在Fedora 26中的安装"
img: canyon.jpg
date: 2017-11-10 18:54:00 +0800
description: 在Fedora26中通过工具安装Typora
tag: [Linux, Markdown]
---
# Markdown编辑器Typora在Fedora 26中的安装

[toc]

## 背景
Markdown是一种简单的标记型语言,实现了用纯文本的方式编辑格式化文档的功能.而在增加了GFM(*[GitHub Flavored Markdown](https://help.github.com/categories/writing-on-github/)*)特性之后进一步实现了富文本等编辑功能.因此在目前成为一种非常流行的编辑语言,也是我最近的首选编辑语言.

之前使用的Haroopad来编辑Markdown文本,因为不支持导出pdf, 所以这次更换电脑打算顺便换换掉它.在搜索的过程中发现了Typora,看大家评价很高, 因此决定选用.

**选择Markdown编辑器标准:**
* 支持离线编辑
* Linux/Mac/Windows全平台支持
* 支持实时预览

## 问题
在决定使用Typora之后,准备安装,首先进入[Typora官网](https://www.typora.io "Typora官网")查找下载安装方法, 发现支持Mac/Windows版本下载, 而Linux版本只给出了debian在线安装命令而没有其他系统的安装命令, 也没有提供二进制文件供下载.

也就是说, 被我弃用的Ubuntu系统是可以直接按官方给出的命令来安装的, 但是我选择的Fedora 26没办法安装. 但是Ubuntu 18.04的话还需要半年才能发布,而17.10的短期支持版本我是不打算再考虑了.所以现在问题来了:

**如何在Fedora 26上安装Typora?**

## 处理
在没有头绪的情况下, 考虑尝试去[GitHub](https://github.com "GitHub官网")上看能不能找到它的源码,最好是直接找到二进制文件来安装.因此我登陆了GitHub并在首页中搜索Typora.

很遗憾, 我没能在其中找到Typora的源码,只能找到[官网Support链接](https://support.typora.io)下的[源码](https://github.com/typora/wiki-website),可以看到它也是使用Jekyll来编译的.

不过,幸运的是,我找到了另一个神奇的库[RPM-Outpost/typora](https://github.com/RPM-Outpost/typora):
> RPM package forthe markdown editor Typora

从它的readme文件中了解到只要执行他的`create-package.sh`脚本并根据自己所用系统架构传递相应架构参数`x64`或者`ia32`,该项目就会自动从Typora的官网下载Typora的压缩包并创建成可以在Fedora 26中使用的RPM包并安装它.因此使用这个项目来实现安装的目的.

## 安装
使用[RPM-Outpost/typora](https://github.com/RPM-Outpost/typora)在Fedora 26中安装Typora.

### 下载RPM-Outpost/typora
使用git从GitHub上克隆这个库备用,命令如下:
```
$ git clone git@github.com:RPM-Outpost/typora.git
```
因为是开源代码,因此不需要鉴权等过程,可以直接下载代码.在这条命令执行成功后,当前目录下会出现一个新文件夹typora,便是下载到本地的代码库了.

### 安装Typora
由于我使用的系统都是64位系统,因此使用如下命令来安装Typora:
```
$ cd typora
$ ./create-package.sh x64
```
在这之后,如果一切正常的话, 就会开始检测环境,下载Typora的压缩包,然后解压并重新生成RPM包,最后会安装生成的RPM包.
安装完成之后, 就可以使用Typora了.

## Trouble Shooting

### 提示无法找到`rpmbuild`命令
**原因:**

在我在家里尝试安装时遇到了这个问题,这是因为Fedora 26中并没有预装这个命令,而脚本中也没有对这个依赖命令进行检测和安装.

**解决:**

既然是没有这个命令,所以我们安装这个命令即可,使用如下命令:
```
$ sudo dnf install rpm-build
```

### 提示version_number为空的错误
**原因:**

在公司尝试安装装时遇到了这个问题,通过跟踪源码发现这是因为**在下载Typora压缩包失败**造成的.而从下载时的界面也可以看到在下载时没有下载进度显示出来.

由于下载压缩包失败,进而造成解压失败,最终导致从解压到的文件中查找版本号失败.因此出现错误提示:
>错误: 宏 %version_number 中存在空内容

**解决:**

既然是下载压缩包失败,那么换一个方法自己下载对应的压缩包就好, 从代码中可以知道该脚本是通过如下命令来下载的:
```
 wget -q --show-progress https://typora.io/linux/Typora-linux-x64.tar.gz -O /path/to/your/typora/work/Typora-linux.tar.gz
```
因此,改为使用浏览器从`https://typora.io/linux/Typora-linux-x64.tar.gz`处下载这个压缩包成功后,将其放到typora库的work目录下并重命名为`Typora-linux.tar.gz`后,再次执行安装命令,并在提示是否删除已有`work`目录时选择N,在提示是否使用已下载的文件是选择y来使用单独下载好的压缩包安装即可.
```
./create-package.sh x64
... ...
> Remove the directory "/home/wangbin/private/repositories/typora/work"? [y/N]: n
Directory not removed.
... ...
> Use this archive instead of downloading a new one? [y/N]: y
Existing archive selected.
... ...
```
如此即可完成安装.

## 总结
debian和yum是Linux中的两大包管理系统,一般软件都会同时给出这两个系统上的安装包和二进制文件的.

Typora毕竟还处于开发早期,可能开发者由于时间和精力等原因暂时还没有提供yum安装包, 相信以后会有所支持才对.

这个打包库的作者的研究精神很不错,能够找到Typora的压缩包,并熟悉相关的打包过程.以后也可以学习一下相关的知识来解决类似的问题.

最后祝大家Markdown和Typora使用愉快.