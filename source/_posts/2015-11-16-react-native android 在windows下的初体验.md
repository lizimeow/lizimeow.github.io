---
title:  "react-native android 在windows下的初体验"
date:   2015-11-16 11:30:00
author: Lizimeow
catalog: true
---
#react-native android 在windows下的初体验#

## 需要的环境：
* node 4.0以上 （我用的是node5.0）
* JDK
* android SDK


### 安装node
https://nodejs.org/en/


### 安装JDK 
从[Java官网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)选下载最新的JDK并安装

配好环境变量（这里就不赘述了）

配置完毕后，通过cmd运行以下命令：`java -version`，`javac` 如果出现返回信息，则设置成功。


### 下载android SDK 
下载地址：[http://developer.android.com/sdk/index.html](http://developer.android.com/sdk/index.html)

Andorid SDK为Android管理开发包工具，提供了Android各级平台的开发包和工具。注意，因为我们是独立安装，不是一体化(集成系列工具)所以需要单独下载SDK。

运行安装文件，并把安装目录下tools文件夹路径设置进PATH环境变量。

到安装目录运行SDK Manager.exe 。
把android 4.0以上的包和extra都装了。
（可能需要翻一下墙）

### 创建项目 
`npm install -g react-native-cli`

`react-native init AwesomeProject`

`cd AwesomeProject/`

### 运行项目 

`react-native start` 

运行成功后，打开另一个命令行窗口，

`cd AwesomeProject`

连接好安卓模拟器或者真机（要android5.0以上，不然运行之后会出现Error Type 3）

因为我的安卓模拟器（AVD）超级卡，原先是勾选了Use Host GPU，模拟器启动会加快，但是后来不知道为什么模拟器启动后会自动黑屏。取消勾选Use Host GPU之后模拟器又启动不了，于是我就把自己的小米2s刷成了android 5.0 _(:зゝ∠)_。

然后

`adb devices`

查看自己是否连接上安卓设备，确定连接上后

`react-native run-android`

这个时候记得打开你的VPN。。

如果出现了unable to download js bundle，也就是不能reload js，可以尝试
`adb reverse tcp:8081 tcp:8081`

如果还是不行的话，你可以尝试这篇文章的方法
[http://www.cnblogs.com/unofficial/p/4843734.html](http://www.cnblogs.com/unofficial/p/4843734.html)

最后的最后，终与可以在手机上看到这个应用啦。

然后你就可以在选定的文本编辑器中打开 index.android.js 并且编辑代码。

摇晃手机或者按菜单键，可以看到reload JS。点击，即可看到修改之后的样子。


### 小结 
QAQ自带bug体质，几乎每一个坑都踩到了。GFW真是太烦了，下载东西超慢。QAQ。然后在sdk manager下载东西的时候出现了下载了好久最后没有下载下来的情况，
好在舍友在玩安卓，从他那儿复制了extra的整个包才得以继续。还有一个坑就是在手机上安装完之后出现了红色的页面，就是不能reload js，谷歌百度之后尝试了各种方法，最后才跑了起来。
QAQ痛心疾首，感觉下一台电脑必须是mac了。
_(:зゝ∠)


