### Android开发环境搭建

#### 1. Android概述

>  Android是一种基于Linux的自由及开放源代码的操作系统，主要使用于移动设备，如智能手机和平板电脑，由Google公司和开放手机联盟领导及开发。 

#### 2. 搭建Android开发环境

Android开发所需要的工具：

* java SDK：
* Android Studio：
* Android SDK：

##### 1. 下载Android Studio

Android Studio官网：https://developer.android.google.cn/studio?hl=zh-cn

建议安装Android Studio最新版4.0版本![1631450363400](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631450363400.png)

##### 1.1 安装过程

一路默认，除了安装路径需要自己调整

##### 1.2 启动Android Studio

![1631450552202](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631450552202.png)

选择 Cancel

##### 1.3 进入欢迎页面，点击next

![1631450619829](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631450619829.png)

##### 1.4 自定义软件设置

![1631451033724](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631451033724.png)

##### 1.5 可以选择亮或者暗配色

![1631451069707](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631451069707.png)

##### 1.6 安装Android SDK，全部打钩

![1631452017292](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631452017292.png)

##### 1.7 分配内存，2G即可

![1631452076598](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631452076598.png)

##### 1.8 完成配置

![1631452098624](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631452098624.png)

#### 2. 建立Android应用

![1631452159534](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631452159534.png)

##### 2.1 选择空白项目

![1631452198088](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631452198088.png)

##### 2.2 设置项目属性

![1631454060162](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631454060162.png)

项目开发语言设置为Java，Android系统版本设置为7.0。

##### 2.3 安装AndroidSDK

![1631460711221](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631460711221.png)

选择Android7.0 版本

![1631460774832](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631460774832.png)

##### 2.4 安装Android虚拟机

![1631460884729](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631460884729.png)

创建新的虚拟机

![1631460960481](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631460960481.png)

选择系统版本Android7的镜像

![1631461091031](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631461091031.png)

安装好后，在AVDmanager里可以看见创建的虚拟机

![1631461164842](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631461164842.png)

##### 2.5 选择目的虚拟机

![1631461453375](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631461453375.png)

##### 2.6 编译运行

![1631461408238](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631461408238.png)

#### 3. 设置应用程序图标

1. 首先准备一张启动图标

![1631761181538](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631761181538.png)

2. 复制后加入mipmap中。

![1631761280197](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631761280197.png)

3. 根据合适的大小选择加入那个文件夹，由下往上分别是从小到大。

![1631761356623](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631761356623.png)

4. 在AndroidManifest.xml中更改应用的全局配置，包括应用的启动图标。

![1631761484871](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631761484871.png)

5. 运行程序，观察到应用图标被更改。

![1631761548914](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631761548914.png)

#### 4. 总结

本篇描写了搭建Android开发环境以及使用Android Studio开发Android应用的过程。