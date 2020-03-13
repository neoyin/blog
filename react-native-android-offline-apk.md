---
title: React-native Android打离线APK
date: 2016-05-06 10:12
Author: Neo
categories: 技术流
tags:
 - react-native
permalink: react-native-android-offline-apk
---

----

#### 项目配置

- 项目`package.json` 如下

```
{
  "name": "news",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  },
  "dependencies": {
    "react": "^0.14.8",
    "react-native": "^0.25.1",
    "react-native-umeng-push": "^1.0.3",
    "react-native-xtabbar": "^0.1.8"
  }
}
```
- 项目引入了友盟消息Push 使用了相关react-native项目<https://github.com/liuchungui/react-native-umeng-push> 
 
```
 # 需先安装rnpm 
npm install rnpm -g
再安装依赖 
rnpm install react-native-umeng-push
```
- 项目 `settings.gradle`如下

```
rootProject.name = 'XXX'
include ':app'
 # 这里引入了两个工程
include ':react-native-umeng-push'
project(':react-native-umeng-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-umeng-push/android')
include ':PushSDK'
project(':PushSDK').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-umeng-sdk/android/PushSDK')
```
- 在项目`build.gradle`中添加

```
dependencies {
    compile project(':react-native-umeng-push')
    compile fileTree(dir: "libs", include: ["*.jar"])
    compile "com.android.support:appcompat-v7:23.0.1"
    compile "com.facebook.react:react-native:+"  // From node_modules
    compile 'com.umeng.analytics:analytics:latest.integration'
    compile project(':PushSDK')
}
```
- 生成keystore
 `keytool -genkey -v -keystore xxx.keystore -alias xxx -keyalg RSA -keysize 2048 -validity 10000`
- 将生成的keystore放入项目目录中 
 - 在`build.gradle`中添加签名配置

```
//签名配置
    signingConfigs {
        release {
            storeFile file("xxx.keystore")
            keyAlias "xxx"
            storePassword "xxx"
            keyPassword "xxx"
        }
    }
	buildTypes {
        release {
            minifyEnabled enableProguardInReleaseBuilds
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
            signingConfig signingConfigs.release
        }
    }
```
- 利用`react-native` 打包`react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/`
 > 这里将`index.android.bundle` 放入`src/main/assets/`中 并将所用到的资源放入`src/main/res/`目录中

- Gradle 生成 Relase API `cd android && ./gradlew installRelease` 
 


#### 参考引用
- <https://segmentfault.com/a/1190000004192816>
- <http://blog.zhaiyifan.cn/2015/09/16/Try-React-Native-for-Android/>
- <http://reactnative.cn/docs/0.24/signed-apk-android.html#content>
- <http://hanhailong.com/2016/04/02/apk%E6%89%93%E5%8C%85%E6%B5%81%E7%A8%8B%E6%A2%B3%E7%90%86/>