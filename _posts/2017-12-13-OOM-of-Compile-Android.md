---
layout: post
title: 编译Android源码出现OOM错误的解决方法
img: new-zealand.jpg 
date: 2017-12-13 17:40:00 +0800
description: 
tag: Android
---

# 编译Android源码出现OOM错误的解决方法

[TOC]

## 背景

这两天因为工作的原因再次尝试编译Android代码,在经历一步步困难之后,却再次遇到一个新的问题.

> Out of memory error 
>
> ...
>
> Try increasing heap size with java option '-Xmx<size>'.
>
> ...
>
> build stopped: subcommand failed.make: *** [ninja_wrapper] Error 1

从上面的错误输出可以看出, 是java编译器jack堆内存不足造成的编译失败.通过百度搜索关键词**Try increasing heap size with java option '-Xmx<size>'**之后找到了解决办法.

## 分析

从错误输出可以看到现在的Android编译java文件使用了新的jack编译器,而不在使用javac编译了.并且在jack-server运行过程中,因为堆内存不足造成的OOM错误.根据提示可以在启动jack-server时向虚拟机传递-Xmx<size>参数增加jack-server的内存即可.

## 解决方法

1. 通过修改jack-server的配置文件`prebuilts/sdk/tools/jack-admin`在它当中启动jack-server的参数中通过`-Xmx<size>`指定堆内存的大小位4096M, 从而增加堆内存来解决OOM问题.

   具体修改为, 在该文件中的`JACK_SERVER_COMMAND="java -XX:MaxJavaStackTraceDepth=-1 -Djava.io.tmpdir=$TMPDIR $JACK_SERVER_VM_ARGUMENTS -cp $LAUNCHER_JAR $LAUNCHER_NAME"`行中增加内存配置`-Xmx4096m`.修改后的代码为:

   `JACK_SERVER_COMMAND="java -XX:MaxJavaStackTraceDepth=-1 -Djava.io.tmpdir=$TMPDIR $JACK_SERVER_VM_ARGUMENTS -Xmx4096m -cp $LAUNCHER_JAR $LAUNCHER_NAME"`

2. 重启jack-admin服务:

   ```bash
   $ ./prebuilts/sdk/tools/jack-admin stop-server
   $ ./prebuilts/sdk/tools/jack-admin start-server
   ```

3. 再次编译代码即可

