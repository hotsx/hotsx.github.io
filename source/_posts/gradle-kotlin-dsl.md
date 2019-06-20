---
title: Gradle Kotlin DSL
date: 2019-06-20 16:36:14
tags: [gradle,kotlin,dsl]
---

Android Studio 使用的是 Gradle 构建 Android 项目，而 Gradle 使用的是基于 Groovy 语言的 DSL 来配置项目的。因为 Groovy 是动态语言，并且为了项目语言的统一和减少语言学习成本，所以我选择从 Groovy 迁移到 Kotlin DSL。这里记录迁移步骤😄。

### 迁移条件

Gradle Wrapper 需要5.0以上版本

### 修改 setting.gradle

重命名文件，加上 `.kts` 后缀 `setting.gradle.kts`，所有 `.gradle` 文件都需要加上 `.kts` 后缀。
修改文件内容

```groovy
//Groovy
include ':app'
```

```kotlin
//Kotlin
include(":app")
```

### 修改 project build.gradle.kts 文件

Groovy 变量声明改为 Kotlin 变量声明，Groovy 中的字符串可以使用单引号包裹而 Kotlin 必须使用双引号。

```groovy
//Groovy
ext.kotlin_version = '1.3.40'
```

```Kotlin
//Kotlin
val kotlinVersion = "1.3.40"
```

buildscript

```groovy
//Groovy
buildscript {
    ext.kotlin_version = '1.3.40'
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.4.1'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
```

```kotlin
//Kotlin
buildscript {
    val kotlinVersion = "1.3.40"
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath("com.android.tools.build:gradle:3.4.1")
        classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlinVersion")
    }
}
```

allprojects

```grovvy
//Grovvy
allprojects {
    repositories {
        google()
        jcenter()
        maven{ url https://jitpack.io }
    }
}
```

```kotlin
//Kotlin
allprojects {
    repositories {
        google()
        jcenter()
        maven(url = "https://jitpack.io")
    }
}
```

task

```groovy
//Groovy
task clean(type: Delete) {
    delete rootProject.buildDir
}
```

```kotlin
//Kotlin
task<Delete>("clean") {
    delete(rootProject.buildDir)
}
```

### 修改 module build.gradle.kts 文件

plugins

```groovy
//Groovy
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
apply plugin: 'kotlin-kapt'
```

```kotlin
//Kotlin
plugins {
    id("com.android.application")
    kotlin("android")
    id("kotlin-android-extensions")
    kotlin("kapt")
}
```

android

```groovy
//Groovy
android {
    compileSdkVersion 29
    buildToolsVersion = '28.0.3'
    defaultConfig {
        applicationId "com.hotsx.bandwagon"
        minSdkVersion 24
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile ('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }

    dataBinding {
        enabled true
    }

    androidExtensions {
        experimental = true
    }
}
```

```kotlin
//Kotlin
configure<AppExtension> {
    compileSdkVersion(29)
    buildToolsVersion("28.0.3")
    defaultConfig {
        applicationId = "com.hotsx.bandwagon"
        minSdkVersion(24)
        targetSdkVersion(29)
        versionCode = 1
        versionName = "1.0"
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        getByName("release") {
            isMinifyEnabled = false
            proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
        }
    }

    dataBinding.isEnabled = true
    androidExtensions.isExperimental = true
}
```

dependencies

```groovy
//Groovy
dependencies {
    ext.room_version = "2.1.0"
    ext.lifecycle_version = "2.0.0"

    implementation fileTree (dir: 'libs', include: ['*.jar'])
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'com.google.android.material:material:1.0.0'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'

    implementation 'androidx.navigation:navigation-fragment-ktx:2.1.0-alpha05' // Navigation
    implementation 'androidx.navigation:navigation-ui-ktx:2.1.0-alpha05'//Navigation
    implementation("com.squareup.retrofit2:retrofit:2.5.0")//retrofit
    implementation("com.squareup.retrofit2:converter-gson:2.5.0")//Gson
    implementation 'com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:0.9.2'//Coroutines
    implementation("org.koin:koin-android-viewmodel:2.0.0-GA")//Koin
    implementation 'com.tencent:mmkv-static:1.0.19'//MMKV
    implementation 'com.orhanobut:logger:2.2.0'//logger

    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    kapt "androidx.lifecycle:lifecycle-compiler:$lifecycle_version"
    kapt "androidx.room:room-compiler:$room_version"
    implementation "androidx.lifecycle:lifecycle-extensions:$lifecycle_version"
    implementation "androidx.room:room-runtime:$room_version"
    implementation "androidx.room:room-ktx:$room_version"
    implementation "me.toptas.fancyshowcase:fancyshowcaseview:1.1.4"//Fancyshowcaseview
    /**Dialog*/
    implementation 'com.afollestad.material-dialogs:core:3.0.0-alpha2'
    implementation 'com.afollestad.material-dialogs:input:3.0.0-alpha2'
    /**chart*/
    implementation 'com.diogobernardino:williamchart:2.5.0'
    /**debugDatabase*/
    debugImplementation 'com.amitshekhar.android:debug-db:1.0.6'
    /*test*/
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
```

```kotlin
//Kotlin
dependencies {
    val roomVersion = "2.1.0"
    val lifecycleVersion = "2.0.0"

    implementation(fileTree(mapOf("include" to "*.jar", "dir" to "libs")))
    implementation(kotlin("stdlib-jdk8", KotlinCompilerVersion.VERSION))
    implementation("androidx.appcompat:appcompat:1.0.2")
    implementation("com.google.android.material:material:1.0.0")
    implementation("androidx.constraintlayout:constraintlayout:1.1.3")

    implementation("androidx.navigation:navigation-fragment-ktx:2.1.0-alpha05")// Navigation
    implementation("androidx.navigation:navigation-ui-ktx:2.1.0-alpha05")//Navigation
    implementation("com.squareup.retrofit2:retrofit:2.5.0")//retrofit
    implementation("com.squareup.retrofit2:converter-gson:2.5.0")//Gson
    implementation("com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:0.9.2")//Coroutines
    implementation("org.koin:koin-android-viewmodel:2.0.0-GA")//Koin
    implementation("com.tencent:mmkv-static:1.0.19")//MMKV
    implementation("com.orhanobut:logger:2.2.0")//logger

    implementation("androidx.legacy:legacy-support-v4:1.0.0")
    kapt("androidx.lifecycle:lifecycle-compiler:$lifecycleVersion")
    kapt("androidx.room:room-compiler:$roomVersion")
    implementation("androidx.lifecycle:lifecycle-extensions:$lifecycleVersion")
    implementation("androidx.room:room-runtime:$roomVersion")
    implementation("androidx.room:room-ktx:$roomVersion")
    implementation("me.toptas.fancyshowcase:fancyshowcaseview:1.1.4")//Fancyshowcaseview
    /**Dialog*/
    implementation("com.afollestad.material-dialogs:core:3.0.0-alpha2")
    implementation("com.afollestad.material-dialogs:input:3.0.0-alpha2")
    /**chart*/
    implementation("com.diogobernardino:williamchart:2.5.0")
    /**debugDatabase*/
    debugImplementation("com.amitshekhar.android:debug-db:1.0.6")
    /*test*/
    testImplementation("junit:junit:4.12")
    androidTestImplementation("androidx.test:runner:1.2.0")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.2.0")
}
```

### 结束

所有 `.gradle` 文件修改完成后重新 build 项目即可。

### 参考

[Migrating build logic from Groovy to Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/)
