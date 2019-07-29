---
title: Windows 配置Flutter 环境
date: 2019-06-29 01:45:06
tags: [android,flutter]
categories: Android
---

#### 使用镜像

由于在国内访问Flutter有时可能会受到限制，Flutter官方为中国开发者搭建了临时镜像，大家可以将如下环境变量加入到用户环境变量中：

```bash
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

#### 系统要求

要安装并运行Flutter，您的开发环境必须满足以下最低要求:

- 操作系统: Windows 7 或更高版本 (64-bit)
- 磁盘空间: 400 MB (不包括Android Studio的磁盘空间)
- 工具: Flutter 依赖 Git for Windows (Git命令行工具)

#### 获取Flutter SDK

1. 去flutter官网下载其最新可用的安装包，[点击下载](https://flutter.dev/docs/development/tools/sdk/releases#windows)，或者到 [Github](https://github.com/flutter/flutter/releases) 仓库下载。

2. 将安装包zip解压到你想安装Flutter SDK的路径(如：C:\src\flutter；注意，不要将flutter安装到需要一些高权限的路径如C:\Program Files\)。

3. 在Flutter安装目录的flutter文件下找到flutter_console.bat，双击运行并启动flutter命令行，接下来，你就可以在Flutter命令行运行flutter命令了。

#### 更新环境变量

要在终端运行 flutter 命令， 你需要添加以下环境变量到系统PATH：

- 转到 “控制面板>用户帐户>用户帐户>更改我的环境变量”.
- 在“用户变量”下检查是否有名为“Path”的条目:
  - 如果该条目存在, 追加 flutter\bin的全路径，使用 ; 作为分隔符.
  - 如果条目不存在, 创建一个新用户变量 Path ，然后将 flutter\bin的全路径作为它的值.
- 在“用户变量”下检查是否有名为”PUB_HOSTED_URL”和”FLUTTER_STORAGE_BASE_URL”的条目，如果没有，也添加它们.

#### 运行 flutter doctor

打开一个新的命令提示符或PowerShell窗口并运行以下命令以查看是否需要安装任何依赖项来完成安装：

```bash
 flutter doctor
```

该命令检查您的环境并在终端窗口中显示报告。Dart SDK已经在捆绑在Flutter里了，没有必要单独安装Dart。 仔细检查命令行输出以获取可能需要安装的其他软件或进一步需要执行的任务（以粗体显示）

例如:

```bash
[-] Android toolchain - develop for Android devices
    • Android SDK at D:\Android\sdk
    ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
    • Try re-installing or updating your Android SDK,
      visit https://flutter.io/setup/#android-setup for detailed instructions.
```

第一次运行一个flutter命令（如flutter doctor）时，它会下载它自己的依赖项并自行编译。以后再运行就会快得多。
