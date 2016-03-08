---
layout:     post
title:      Android debug神器Stetho使用
date:       2016-01-11 17:59:18
summary:    插件
author:     Tneciv
categories: android
thumbnail: android
tags:
 - stetho
 - http debug
---



#### [https://github.com/facebook/stetho](https://github.com/facebook/stetho "https://github.com/facebook/stetho")

## 在Android Studio下使用：

### 加入依赖

Download [the latest JARs](https://github.com/facebook/stetho/releases/latest) or grab via Gradle:
```groovy
compile 'com.facebook.stetho:stetho:1.2.0'
```
or Maven:
```xml
<dependency>
  <groupId>com.facebook.stetho</groupId>
  <artifactId>stetho</artifactId>
  <version>1.2.0</version>
</dependency>
```

选择对应网络请求框架:

```groovy
compile 'com.facebook.stetho:stetho-okhttp:1.2.0'
```
or:
```groovy
compile 'com.facebook.stetho:stetho-urlconnection:1.2.0'
```

开启js控制台:

```groovy
compile 'com.facebook.stetho:stetho-js-rhino:1.2.0'
```
For more details on how to customize the JavaScript runtime see [stetho-js-rhino](stetho-js-rhino/).


### 在Application类中重写`oncreat`：


```java
public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        Stetho.initialize(
                Stetho.newInitializerBuilder(this)
                        .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
                        .enableWebKitInspector(Stetho.defaultInspectorModulesProvider(this))
                        .build());
    }
}
```

### 在`manifest`文件中添加`Application`类名称，务必添加网络权限：

```xml

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="io.tneciv.goodnight">
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        android:name=".App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launch"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity
            android:name=".activity.MainActivity"
            android:label="@string/app_name"
            android:theme="@style/AppTheme.NoActionBar">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>

```

### 在使用okhttp的位置开启：

```java

 OkHttpClient okHttpClient = new OkHttpClient();
 okHttpClient.networkInterceptors().add(new StethoInterceptor());

```

debug方式：
在chrome地址栏输入chrome://inspect/，会出现如图：
![](http://i.imgur.com/LyNcnFS.png)
点击inspect会出现chrome控制台（如果出现空白页面表示需要科学上网）：
![](http://i.imgur.com/mkhD7D7.jpg)

