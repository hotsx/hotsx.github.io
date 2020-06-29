---
title: Pixel 4 使用 Magisk Root
date: 2020-06-29 17:27:44
tags:
categories:
---

最近新购了Pixel 4，可惜的是 主打功能 Motion Sense 被锁区了，并且 Google Feed 也无法使用，所以必须通过 Root 来解锁这些功能。

### 准备官方镜像包

根据系统当前版本号找到对应 Image 下载。

https://developers.google.com/android/images

解压缩后，文件夹中还有一个 zip 文件，继续解压，找到 boot.img ，将其放在手机的 Downloads 目录下。

### 下载 Magisk Manager 安装

https://github.com/topjohnwu/Magisk

在开源仓库中下载 apk 文件安装到手机。

### 修补 boot.img 文件

打开 Magisk Manager 点击第一个安装按钮， 点击 “选择并修补一个文件”，然后选择 之前传入手机的 boot.img 文件，开始进行修补，成功后会在 Downloads 文件夹生成一个 magisk_patched.img 文件，将此文件移动到电脑中。

### 刷入 magisk_patched.img 文件

USB 连接上手机，通过 adb 命令进入 fastboot 模式：adb reboot bootloader

进入 fastboot 模式后执行：fastboot flash boot magisk_patched.img

然后：fastboot reboot

重启成功后就已经刷入了 Magisk 了。

