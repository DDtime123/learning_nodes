### Android Studio界面分析

#### 1. Android Studio主界面基本组成

![1631763199251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631763199251.png)

Android Studio主界面基本布局如下：

1. 编辑区：用于编辑文件。

2. Project面板：Project面板用于浏览文件，显示当前Project的所有Module。

3. Android DDMS面板：Android DDMS面板类似于Eclipse中的日志/输出窗口，但是比它多出了截图，查看系统信息等功能。

4. Build Variants面板：用于设置当前项目的Build Variants，所有Module默认都会有Debug和Release两种选项。
5. Gradle tasks面板：用于显示Gradle任务面板，例如 assemble，assembleRelease，assembleDebug，build，check，clean，lint等常用任务，双击可执行Gradle任务。
6. Android Studio主菜单栏，工具栏和快捷导航。

#### 2. Project面板的各级目录结构及主要文件

![1631763560684](C:\Users\Administrator\Desktop\PPT\1631763560684.png)

​								 图1：Android类型项目视图

Android Studio主界面默认的Project为Android类型，可通过单击左上角标签切换为Project类型。

![1631763644150](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631763644150.png)

![1631763701024](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631763701024.png)

​								图2：Project类型项目视图

Project视图下的文件结构：

* src文件夹和 MainActivity.java 文件

  src是用于存放源码的文件夹

* res文件夹

  res文件夹为存放资源的文件夹，它包含项目中的资源文件并将其编译进应用程序。向该文件夹添加资源时，会被 R.java 文件自动记录。新建一个项目，res文件夹下会自动添加多个子文件夹，包括drawable，layout，mipmap和values等。

  drawable子文件夹存放代用的图片文件（*.png，*.jpg），layout子文件夹存放界面布局文件，mip文件夹存放应用程序的待用图标文件。

  values文件夹可存放多个 .xml 文件，包括 colors.xml，styles.xml，strings.xml等配置文件，还可存放不同类型数据。

* 

#### 3. Android Studio实时布局（live layout），富布局编辑器

打开布局文件，点击preview

![1631764348582](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631764348582.png)

实时布局功能让开发者在无需运行在虚拟机或真机的情况下预览应用的布局，能帮助开发者节约大量的时间。

Android Studio还提供一套富布局编辑器，可以在其中随意拖拽各类用户界面控件，以及在多屏幕配置中同时查看多种布局的显示效果。

点击Design打开富布局编辑器

![1631764620438](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631764620438.png)

#### 4. Android Studio Lint工具

Android Studio提供的Android Lint是一套静态分析工具，负责对源代码进行解析，能够检测出程序中的潜在漏洞及其他可能被编译器忽略的代码问题，

点击 Analyze -> inspect code

![1631764775661](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631764775661.png)

点击OK开始分析代码

![1631764829080](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631764829080.png)

#### 5. 总结

本篇介绍了Android  Studio的界面主要功能。