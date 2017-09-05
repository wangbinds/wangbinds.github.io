---
layout: post
title: "从Github上下载Android源码"
img: himalayan.jpg 
date: 2017-09-05 21:06:00 +0800
description: 通过修改manifest文件中的路径，从github上下载Android源码
tag: Android
---
# 从Github上下载Android源码

[toc]

## 背景
由于国内没有办法访问Android源码的官方网站及代码库，但是作为一个Android开发人员不能看到Android源码，这是没办法接受的。

经过查找发现Android已经把代码发布在了[Github][github-site]上，那么我们为什么不能从[Github][github-site]上下载repo源码呢？查看了Android在[Github][github-site]上托管的项目展示网站 [Android Github IO][android-io]。上面提到：
> The Android GitHub account provides a read-only mirror of some of the most common repositories from the Android Open Source Project. You can view the source code and review the history of the project, just like any other repository on GitHub.

这就是说[Github][github-site]上的Android代码是一份官网上的**只读镜像**，可以在这里像其他Github上的项目一样查看代码和提交历史等。
> When working with the Android source code, developers use the repo tool to checkout the code from the official git repositories. Because of differences in how repositories are named, repo will not work with the Android mirror on GitHub. Several people have published guides on using repo with GitHub, but you're on your own there.

而这里就明确说道由于项目库命名的差异，repo并不能工作在Android的[Github][github-site]镜像上。但是有很多人发布了使用repo从[Github][github-site]下载Android代码的方法，可以通过google搜索到。如下面两篇文章：
* [Checking out Android source from github][stackoverflow-art]
* [How to get android source from GitHub using repo][google+-art]

但是由于时间问题，目前这两篇文章所列出的方法都已经不可用了。因此在这里我参考这些文章整理了如下方法并测试成功了，在此记录以供大家参考。

## 初始化repo库
大家都知道Android代码使用repo工具来管理，因此下载Android代码需要使用repo工具。而从github上下载Android代码仍然需要使用repo工具。首先执行repo init命令，而与在官方网站下载代码的区别在于指定URL不同，并且为了绕开从google代码库下载repo，需要指定github上repo项目的地址来从github上下载repo。命令如下
```
$ repo init -u git://github.com/android/platform_manifest.git --repo-url=git://github.com/android/tools_repo.git
```
**注１：git://github.com/android/platform_manifest.git是Android　manifest库在github上的地址。**
**注２：git://github.com/android/tools_repo.git是repo库在github上的地址。**
**注３：--repo-url参数用来覆盖下载repo的默认URL。**

## 修改manifest.xml
repo通过manifest文件来管理所有相关的git库。所有的manifest文件被保存在manifest.git库中。由于github上的Android镜像与googlesource中的代码库相对位置和名称不同，因此在初始化repo库之后执行下载代码会提示失败。需要将repo sync时要使用的manifest.xml文件进行修改，该文件本来只是一个指向manifest.git库中某一特定manifest的软连接，用来表明repo init时所指定的manifest。

为了防止对manifest库的修改，我的做法是复制该软连接的源文件到此处，并重命名为manifest.xml。

1. 在manifest.xml中修改**remote**标签中**fetch**属性的值。因为在github中Android的所有库都是在同一级目录之下，因此需要把该属性的值从“..”*(上级目录)*修改为“.”*(当前目录)*。
2. 在manifest.xml中修改每个**project**标签的**name**属性的值。因为在github中的Android项目库与googlesource中的名称不同，因此需要把每个**project**标签的**name**属性的值从googlesource格式改为github格式。
3. 在manifest.xml中删除在github中没有的project。因为目前在github中只能看到99个项目，而manifest中却指定了更多project，因此需要把github中没有的project从manifest.xml中注释掉。
4. 有部分github上的库在manifest.xml中却无法找到，暂时并不知道原因，不过为了保险起见，我也将它们增加到了manifest中。

最终修改后的manifest.xml文件为：
```
<?xml version="1.0" encoding="UTF-8"?>
<manifest>

  <remote  name="aosp"
           fetch="."
           review="https://android-review.googlesource.com/" />
  <default revision="master"
           remote="aosp"
           sync-j="4" />

  <project path="frameworks/base" name="platform_frameworks_base" groups="pdk-cw-fs,pdk-fs" />
  <project path="kernel/common" name="kernel_common" groups="pdk-cw-fs,pdk-fs" />
  <project path="build/make" name="platform_build" groups="pdk" >
    <copyfile src="core/root.mk" dest="Makefile" />
    <linkfile src="CleanSpec.mk" dest="build/CleanSpec.mk" />
    <linkfile src="buildspec.mk.default" dest="build/buildspec.mk.default" />
    <linkfile src="core" dest="build/core" />
    <linkfile src="envsetup.sh" dest="build/envsetup.sh" />
    <linkfile src="target" dest="build/target" />
    <linkfile src="tools" dest="build/tools" />
  </project>
  <project path="packages/apps/Settings" name="platform_packages_apps_Settings" groups="pdk-fs" />
  <project path="packages/apps/Contacts" name="platform_packages_apps_Contacts" groups="pdk-fs" />
  <project path="external/strace" name="platform_external_strace" groups="pdk-cw-fs,pdk-fs" />
  <project path="dalvik" name="platform_dalvik" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/qemu" name="platform_external_qemu" />
  <project path="external/libxml2" name="platform_external_libxml2" groups="pdk,pdk-cw-fs,pdk-fs,libxml2" />
  <project path="kernel/msm" name="kernel_msm" />
  <project path="development" name="platform_development" groups="pdk-cw-fs,pdk-fs" />
  <project path="system/core" name="platform_system_core" groups="pdk" />
  <project path="bionic" name="platform_bionic" groups="pdk" />
  <project path="frameworks/support" name="platform_frameworks_support" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/fdlibm" name="platform_external_fdlibm" groups="pdk" />
  <project path="external/skia" name="platform_external_skia" groups="pdk,qcom_msm8x26" />
  <project path="packages/apps/Stk" name="platform_packages_apps_Stk" groups="apps_stk,pdk-fs" />
  <project path="hardware/ril" name="platform_hardware_ril" groups="pdk" />
  <project path="external/protobuf" name="platform_external_protobuf" groups="pdk" />
  <project path="packages/providers/TelephonyProvider" name="platform_packages_providers_telephonyProvider" groups="pdk-cw-fs,pdk-fs" />
  <project path="hardware/libhardware" name="platform_hardware_libhardware" groups="pdk" />
  <project path="external/ppp" name="platform_external_ppp" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/libpng" name="platform_external_libpng" groups="pdk" />
  <project path="packages/providers/DownloadProvider" name="platform_packages_providers_downloadprovider" groups="pdk-cw-fs,pdk-fs" />
  <project path="packages/apps/Music" name="platform_packages_apps_music" groups="pdk-fs" />
  <project path="packages/providers/MediaProvider" name="platform_packages_providers_mediaprovider" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/jdiff" name="platform_external_jdiff" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/neven" name="platform_external_neven" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/iptables" name="platform_external_iptables" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/giflib" name="platform_external_giflib" groups="pdk,qcom_msm8x26" />
  <project path="packages/providers/CalendarProvider" name="platform_packages_providers_calendarprovider" groups="pdk-cw-fs,pdk-fs" />
  <project path="frameworks/opt/calendar" name="platform_frameworks_opt_calendar" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/expat" name="platform_external_expat" groups="pdk" />
  <project path="packages/apps/SoundRecorder" name="platform_packages_apps_soundrecorder" groups="pdk-fs" />
  <project path="external/sonivox" name="platform_external_sonivox" groups="pdk" />
  <project path="external/apache-http" name="platform_external_apache-http" groups="pdk" />
  <project path="external/freetype" name="platform_external_freetype" groups="pdk" />
  <project path="external/tcpdump" name="platform_external_tcpdump" groups="pdk,pdk-cw-fs,pdk-fs" />
  <project path="packages/apps/PackageInstaller" name="platform_packages_apps_packageinstaller" groups="pdk-cw-fs,pdk-fs" />
  <project path="packages/providers/ContactsProvider" name="platform_packages_providers_contactsprovider" groups="pdk-cw-fs,pdk-fs" />
  <project path="packages/apps/HTMLViewer" name="platform_packages_apps_htmlviewer" groups="pdk-fs" />
  <project path="packages/apps/Email" name="platform_packages_apps_email" groups="pdk-fs" />
  <project path="external/libpcap" name="platform_external_libpcap" groups="pdk,pdk-cw-fs,pdk-fs" />
  <project path="external/tinyxml" name="platform_external_tinyxml" groups="pdk" />
  <project path="external/elfutils" name="platform_external_elfutils" groups="pdk" />
  <project path="external/elfcopy" name="platform_external_elfcopy" groups="pdk" />
  <project path="external/zlib" name="platform_external_zlib" groups="pdk" />
  <project path="external/sqlite" name="platform_external_sqlite" groups="pdk" />
  <project path="external/netperf" name="platform_external_netperf" groups="pdk-cw-fs,pdk-fs" />
  <project path="packages/apps/Phone" name="platform_packages_apps_Phone" groups="pdk-fs" />
  <project path="external/emma" name="platform_external_emma" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/tagsoup" name="platform_external_tagsoup" groups="pdk,pdk-cw-fs,pdk-fs" />
  <project path="external/safe-iop" name="platform_external_safe-iop" groups="pdk" />
  <project path="external/netcat" name="platform_external_netcat" groups="pdk-cw-fs,pdk-fs" />
  <project path="external/esd" name="platform_external_esd" groups="pdk-cw-fs,pdk-fs" />
  <project path="packages/apps/Calculator" name="platform_packages_apps_calculator" groups="pdk-fs" />
  <project path="packages/apps/Mms" name="platform_packages_apps_mms" groups="pdk-fs" />
  <project path="packages/apps/VoiceDialer" name="platform_packages_apps_voicedialer" groups="pdk-fs" />
  <project path="packages/apps/Browser" name="platform_packages_apps_browser" groups="pdk-fs" />
  <project path="packages/apps/AlarmClock" name="platform_packages_apps_alarmclock" groups="pdk-fs" />
  <project path="packages/apps/GoogleSearch" name="platform_packages_apps_googlesearch" groups="pdk-fs" />
  <project path="packages/apps/Updater" name="platform_packages_apps_updater" groups="pdk-fs" />
  <project path="packages/apps/IM" name="platform_packages_apps_im" groups="pdk-fs" />
  <project path="packages/apps/Launcher" name="platform_packages_apps_launcher" groups="pdk-fs" />
  <project path="packages/apps/Camera" name="platform_packages_apps_camera" groups="pdk-fs" />
  <project path="packages/apps/Sync" name="platform_packages_apps_sync" groups="pdk-fs" />
  <project path="packages/providers/GoogleContactsProvider" name="platform_packages_providers_googlecontactsprovider" groups="pdk-fs" />
  <project path="packages/providers/drmprovider" name="platform_packages_providers_drmprovider" groups="pdk-fs" />
  <project path="packages/providers/ImProvider" name="platform_packages_providers_improvider" groups="pdk-fs" />
  <project path="external/srec" name="platform_external_srec" />
  <project path="external/yaffs2" name="platform_external_yaffs2" />
  <project path="external/jpeg" name="platform_external_jpeg" />
  <project path="external/openssl" name="platform_external_openssl" />
  <project path="external/jhead" name="platform_external_jhead" />
  <project path="external/dbus" name="platform_external_dbus" />
  <project path="external/dhcpcd" name="platform_external_dhcpcd" />
  <project path="external/tremor" name="platform_external_tremor" />
  <project path="external/icu4c" name="platform_external_icu4c" />
  <project path="external/bluez" name="platform_external_bluez" />
  <project path="external/opencore" name="platform_external_opencore" />
  <project path="external/ClearSilver" name="platform_external_clearsilver" />
  <project path="external/aes" name="platform_external_aes" />
  <project path="external/webkit" name="platform_external_webkit" />
  <project path="external/libffi" name="platform_external_libffi" />
  <project path="external/dropbear" name="platform_external_dropbear" />
  <project path="external/wpa/supplicant" name="platform_external_wpa_supplicant" groups="pdk" />
  <project path="external/GoogleClient" name="platform_external_googleclient" />
  <project path="external/gdata" name="platform_external_gdata" />
  <project path="external/ping" name="platform_external_ping" />
  <project path="prebuilt" name="platform_prebuilt" groups="pdk-fs" />
  <project path="frameworks/opt/com.google.android" name="platform_frameworks_opt_com.google.android" groups="pdk-fs" />
  <project path="frameworks/policies/base" name="platform_frameworks_policies_base" groups="pdk-fs" />
  <project path="system/wlan/ti" name="platform_system_wlan_ti" groups="pdk-fs" />
  <project path="system/bluetooth" name="platform_system_bluetooth" groups="pdk-fs" />

</manifest>
```

## 下载代码
在修改完manifest.xml之后，只需要执行repo sync命令即可成功下载manifest.xml中所列的所有项目。






[github-site]: https://github.com "Github官网"
[android-io]: https://android.github.io "Android Github IO"
[google+-art]: https://plus.google.com/+MarkRenouf/posts/3NngLGAjVgG "How to get android source from GitHub using repo"
[stackoverflow-art]: https://stackoverflow.com/questions/7761518/checking-out-android-source-from-github "Checking out Android source from github"