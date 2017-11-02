---
layout: post
title: "在命令行编译AndroidStudio 3.0的项目"
img: nevada.jpg 
date: 2017-10-30 12:50:00 +0800
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
tag: [Android, Linux]
---

# 在命令行编译AndroidStudio 3.0的项目

[toc]

# 背景
在实际使用中，我们难免会遇到使用命令行来编译Android工程的情况，比如CI服务器等。而我目前所面临的尴尬情况是，家里笔记本配置太低，无法运行AndroidStudio，而不得不通过命令行的方式来临时编译代码。

本来在升级AndroidStudio 3.0之前一直用的很好，毕竟AndroidStudio本来就是通过调用gradle wrapper来进行编译的。但是在升级3.0之后，再次使用命令行编译代码时出现了问题。

