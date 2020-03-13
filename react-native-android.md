---
title: React-native 环境搭建
date: 2016-05-04 10:12
Author: Neo
categories: 技术流
tags:
 - react-native
permalink: react-native-android
---

----

#### 环境
1. `JAVA `
2. `Gradle` <http://gradle.org/gradle-download/>
3. `Node`  <https://nodejs.org/en/>
4. `Android-SDK` <http://www.androiddevtools.cn/>
5. `Genymotion` <https://www.genymotion.com/>
 
#### Bugfix
```
java.io.IOException: Cannot run program "/usr/local/android-sdk/build-tools/23.0.1/aapt": error=2, 没有那个文件或目录
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1047)
	at com.android.builder.png.AaptProcess$Builder.start(AaptProcess.java:154)
	at com.android.builder.png.QueuedCruncher$1.creation(QueuedCruncher.java:105)
	at com.android.builder.tasks.WorkQueue.run(WorkQueue.java:206)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.io.IOException: error=2, 没有那个文件或目录
	at java.lang.UNIXProcess.forkAndExec(Native Method)
	at java.lang.UNIXProcess.<init>(UNIXProcess.java:187)
	at java.lang.ProcessImpl.start(ProcessImpl.java:130)
	at java.lang.ProcessBuilder.start(ProcessBuilder.java:1028)
	... 4 more
```

Linux 下需要安装依赖`sudo apt-get install lib32stdc++6 lib32z1`

执行`react-native run-android`
