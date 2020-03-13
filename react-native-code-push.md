---
title: React-native Codepush 热更新
date: 2016-05-20 10:12
Author: Neo
categories: 技术流
tags:
 - react-native
permalink: react-native-code-push
---


#### CodePush
> CodePush是一个微软开发的云服务器。通过它，开发者可以直接在用户的设备上部署手机应用更新。CodePush相当于一个中心仓库，开发者可以推送当前的更新（包括JS/HTML/CSS/IMAGE等）到CoduPush，然后应用将会查询是否有更新。

#### 安装CodePush CLI
控制台输入`npm install -g code-push-cli`，就可以安装了。
安装完毕后，输入 `code-push -v`查看版本代表成功。
#### 创建CodePush账号
控制台输入`code-push register` 后，将会打开一个网页进行注册
账号支持 github和 Microsofe，选其中一个就可以了
成功登陆后，session会写在 `~/.code-push.config`文件中。

```
 #用户相关命令
code-push login 登陆
code-push logout 注销
code-push access-key ls 列出登陆的token
code-push access-key rm {access-key} 删除某个 access-key
```

#### CodePush注册app
控制台输入`code-push app add {appName}`注册一个app

```
 #App添加相关命令
add 在账号里面添加一个新的app
remove 或者 rm 在账号里移除一个app
rename 重命名一个存在app
list 或则 ls 列出账号下面的所有app
transfer 把app的所有权转移到另外一个账号
```

#### 项目配置
1. 安装`rnpm install react-native-code-push`

```
 #这条命令将会自动帮我们在项目文件中添加好设置
rnpm-link info Linking react-native-code-push android dependency
rnpm-link info Android module react-native-code-push has been successfully linked
rnpm-link info Linking react-native-code-push ios dependency
rnpm-link info iOS module react-native-code-push has been successfully linked
```
 2. 在 `android/app/build.gradle`文件里面添加额为的创建任务：

```    
apply from: "react.gradle"
apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
```
3. 运行 `code-push deployment ls {appName}`获取 部署秘钥。默认的部署名是 staging
4. 更新 `MainActivity.java`文件

```
import com.microsoft.codepush.react.CodePush;
 public class MainActivity extends ReactActivity {
 //覆盖 getJSBundleFile 方法，让CodePush决定当app启动时，去哪里加载 JS bundle
    @Override
    protected String getJSBundleFile(){
        return CodePush.getBundleUrl():
    }
    @Override 
    protected List<ReactPackage> getPackages(){
        //实例化 CodePush运行时，把它添加到 packages,填写正确的 部署秘钥
        return Arrays.<ReactPackage> as List(
            new MainReactPackage(),
            new CodePush("deployment-key" , this , BuildCofig.DEBUG)
        )
    }   
 }
```
5. 修改 `android/app/build.gradle`

```
 android{
        defaultConfig{
            versionName "1.0.0"
        }
    }
```
6.  项目应用中部署

```
import codePush from 'react-native-code-push'
componentDidMount() {
---
codePush.sync( { updateDialog: {title:"中国足球",appendReleaseDescription:true,optionalIgnoreButtonLabel:"忽略",optionalInstallButtonLabel:"安装",optionalUpdateMessage:"有可用的更新，快快更新吧!" }, installMode: codePush.InstallMode.IMMEDIATE }
);
}
```
7. code-push 部署APP相关命令

```
code-push deployment add {appName} 部署
code-push deployment rename {appName} 重命名
code-push deployment rm {appName} 删除部署
code-push deployment ls {appName} 列出应用的部署情况
code-push deployment ls {appName} -k 查看部署的key
code-push deployment history {appName} {deploymentNmae} 查看历史版本
```
8. 发布bundles 
	 1. 在项目中添加 bundles文件夹放置bundle：
	 2. 打包`react-native bundle --platform 平台 --entry-file 启动文件 --bundle-output 打包js输出文件 --assets-dest 资源输出目录 --dev 是否调试`
	 3. CodePush发布更新了`code-push release {appName} {Bundles所在目录} {对应的应用版本} -deploymentName 更新环境 --description 更新描述 --mandatory 是否强制更新`    
	 
#### 参考引用
- <http://microsoft.github.io/code-push/index.html>
- <https://github.com/Microsoft/react-native-code-push>
- <http://blog.differential.com/react-native-codepush/>
- <http://hammercui.github.io/post/react-native-android%E5%AE%9E%E6%88%98%EF%BC%9A4-CodePush/>
- <http://bbs.reactnative.cn/topic/725/code-push-%E7%83%AD%E6%9B%B4%E6%96%B0%E4%BD%BF%E7%94%A8%E8%AF%A6%E7%BB%86%E8%AF%B4%E6%98%8E%E5%92%8C%E6%95%99%E7%A8%8B>
