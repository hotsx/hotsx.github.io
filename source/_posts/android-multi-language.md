---
title: Android Multi Language
date: 2019-06-19 19:13:10
tags: android
categories: Android
---

Android 开发者如果需要在海外发布 App 必不可少的要用到多语言的切换，这里记录 Android 多语言切换的基础实现。

### 添加多语言文件

在不同的 value 文件夹下添加不同语言的 string.xml 文件。

### 更新 Configuration 中的 Locale 属性

```java
Resources resources = getContext().getResources();
DisplayMetrics dm = resources.getDisplayMetrics();
Configuration config = resources.getConfiguration(); // 应用用户选择语言
config.locale = Locale.ENGLISH;
resources.updateConfiguration(config, dm);
```

如需要跟随系统设置：

```java
Locale.getDefault()
```

### 重启HomeActivity

关闭 Activity 栈中的所有 Activity 并重启 HomeActivity

```java
Intent intent = new Intent(this, HomeActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK);
startActivity(intent);
// 杀掉进程
android.os.Process.killProcess(android.os.Process.myPid());
System.exit(0);
```

[参考链接：Android App 多语言切换](https://jaeger.itscoder.com/android/2016/05/14/switch-language-on-android-app.html)
