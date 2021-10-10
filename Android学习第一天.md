### Android学习第一天

#### 1. Android体系结构

> Android 体系结构由5部分组成，分别是 Linux Kernel，Android Runtime，Libraries，Application Framework，Application。

* Linux Kernel（Linux内核）。Android基于Linux2.6内核提供安全管理，内存管理，进程管理，网络堆栈，驱动模型等系统服务。Linux Kernel作为软件和硬件之间的抽象层，封装了底层的硬件而向上层提供了统一的接口。

* Android Runtime（Android运行时）。Android提供一个核心库的集合，该库提供大部分Java核心库中的功能。Android应用程序运行在Dalvik虚拟机中，这是一种可以在一个设备上多虚拟机同时高效运行的虚拟机。

* Libraries（核心库）。Android应用程序包含一个C/C++代码组成的核心库，供Android系统的空间使用，并通过Application FrameWork（应用程序框架）提供给开发者。以下是列出部分核心库：

  1. 系统C库：基于BSD衍生的标准C系统库，基于嵌入式Linux设备。
  2. 媒体库：基于PacketVideo的openCORE。这些库支持录制，播放许多流行的音频，视频格式以及静态图像文件，包括 MPEG4，H.264，MP3，AAC，AMR，JPG，PNG等。
  3. 界面管理：访问管理显示子系统以及无缝组合多个应用程序的二维和三维图形层。
  4. LibWebCore：新式的浏览器引擎，驱动Android浏览器以及内嵌的Web视图。
  5. SGL：基本的2D图形引擎。
  6. 3D库：基于OpenGL ES 1.0 APIs的实现，库使用3D硬件加速或者3D应用软件光栅。
  7. FreeType：位图和矢量字体渲染。
  8. SQLite：所有应用程序都可以使用的强大的轻量级关系型数据库引擎。

* Application FrameWork（应用程序框架）。开发者可以使用核心系统程序所使用的APIs。应用程序可以发布功能同时其他应用程序可以使用这些功能，但需服从框架执行的安全限制。

  所有的应用程序其实是一组服务和系统，主要包括：

  1. 视图（View）：丰富可扩展的视图集合，可构建应用程序的界面，包括列表，网格，文本框，按钮甚至是内嵌的Web浏览器。
  2. 内容提供者（ContentProviders）：使应用程序能访问其他应用程序的数据，或者共享自己的数据。
  3. 资源管理器（Resource Manager）：使应用程序能够访问非代码文件，包括字符串，图形和布局文件等。
  4. 通知管理器（Notification Manager）：使应用程序可以在状态栏自定义通知。
  5. 活动管理器（Activity Manager）：管理应用的生命周期，提供通用的导航回退功能。

* Application（应用）。Android装配一个核心应用程序集合，包括电子邮件客户端，SMS程序，日历，地图，浏览器，联系人和其他设置，所有应用程序均由Java编写。

#### 2. Android布局文件

* Android通过XML格式的布局文件管理应用程序的界面。在Android Studio里，项目被创建时就拥有一个界面以及对应的布局文件。
* 布局文件的存放位置为res/layout

设计Android界面布局的方式有两种，Design（图形化界面设计形式）以及Text（代码形式）：

![1631668844821](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1631668844821.png)

* Android程序默认的布局方式是ConstraintLayout（约束布局）。
* 控制布局文件以及控件使其能够在界面上正常显示，需要设置ConstraintLayout和控件的宽度，高度。高度和宽度属性有以下几种设置方式：

1. match_parent：强制性将组件宽度或高度扩展为parent的宽度和高度。
2. wrap_content：组件的宽度或高度由内容决定，内容多则控件宽，内容少则控件窄。

#### 3. Android应用程序的Activity

Activity文件用于实现界面的交互功能。

* Activity继承AppCompatActivity，并重写onCreate()方法。
* 调用setContentView()方法设置当前页面的布局文件，将布局文件转化为View，并显示在界面上。
* onCreate (Bundle)：通常在这个方法中通过setContentView方法设置UI，并且通过FindViewById方法找到需要交互的小部件。
* onPause ()：处理离开活动时应该做的事情，用户做的所有改变应该在这里提交。

#### 4. Android 全局配置文件基本结构

AndroidManifest.xml是Android Studio项目的全局配置文件，记录Android应用程序的全部组件，是应用程序的任何代码运行前必须要有的文件。当添加或修改了Activity后，要修改相应的配置，只有配置好后，才能使用该Activity。

AndroidManifest的结构，元素，属性的规则如下：

* 元素：以尖括号<>包裹的标签为元素，在所有元素中，只有<manifest>和<application>是必需的。处于同一层次的元素之间没有先后顺序。
* 属性：除了 <manifest> 的属性外，其他属性均以以 android: 作为前缀。
* 定义类名：所有元素名都对应SDK中的类名，如果自定义类，则需引入包名，如果类在application在同一数据包中，则可以直接用 "."。
* 多数值项：如果某个元素具有多个数值，那么必需以重复的方式说明该属性具有多个数值，而不能在一个属性之中声明多个数值。
* 资源项说明：当要引用某个资源时，其采用如下格式：@[package:]type:name。例如，<activity android: icon=[@drawable/icon]>。
* 字符串值：当字符串中含有 "\\"时，必需要用转义字符"\\\\"替代。

#### 5. AndroidManifest.xml文件各结点解析

* 第一层（manifest）：

~~~ xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.taco">
</manifest>
~~~

​		xmls:android 定义Android命名空间，一般为"http://schemas.android.com/apk/res/android"，这样使得Android的各种标准属性能够在文件中使用，提供了元素中大部分数据。

​		package指定了本程序中Java主程序包的包名。	

* 第二层（application）：

~~~xml
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
</application>
~~~

<application>元素内声明了应用程序的所有组件及其属性，如icon，label，permission等。

* 第三层（activity）：

~~~xml
<activity android:name=".MainActivity">
</activity>
~~~

一个应用程序可包含多个activity

* 第四层（intent-filter）：

~~~xml
 <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
~~~

intent-filter 常见的android:name值为android.intent.action.MAIN，表明该activity是作为应用程序的入口。

#### 6. Android样式设计

Android通过 res/values/style.xml 文件定义单个组件的样式，全局样式以及样式继承关系。

1. 样式定义

   Android通过 style.xml 文件定义组件样式，类似于Web网页通过CSS文件定义样式。不过 style.xml 文件不用通过import或者link加载，而是自动导入的。

   ~~~xml
   <resources>
   
       <!-- Base application theme. -->
       <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
           <!-- Customize your theme here. -->
           <item name="colorPrimary">@color/colorPrimary</item>
           <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
           <item name="colorAccent">@color/colorAccent</item>
       </style>
   
   </resources>
   ~~~

   Android样式通过style标签完成，通过添加 item 元素设置不同的属性值。

2. 设置单个控件样式

   ~~~xml
   <TextView android:text="OK"
               android:layout_width="match_parent"
               android:layout_height="wrap_content"
               android:textSize="18px"
               android:textColor="#0000CC"/>
   ~~~

3. 全局样式设置

   使用 theme 设置全局样式

   ```xml
   android:theme="@style/AppTheme"
   ```

4. 样式继承关系

   Android的样式采取和CSS相同的覆盖，继承原则。如果一个TextView组件自己设置了样式，他的ViewGroup设置了样式，activity设置了主题，application设置了主题，那么它首先会读取自己的样式的值，对于自己没有的样式的值，会一层一层网上找，读取的顺序为 自己的样式View -> 上一层ViewGroup的属性值 -> 上上一层ViewGroup的属性值 -> ...

#### 7. Android系统的包

Android中各个包被写成 android.* 的方式，重要的包如下：

* android.app：提供高层的程序模型，提供基本的运行环境。
* android.os：提供系统服务，消息传输和IPC机制。
* android.view：提供基本的用户界面接口框架。
* android.content：包含各种对设备上的数据进行访问和发布的类。
* android.database：通过内容提供者浏览和操作数据库。
* android.graphics：提供底层的图形库，包括画布，颜色过滤，点，可以将它们直接绘制在屏幕上。
* android.location：提供定位和相关服务的类。
* android.media：提供一些关于音频视频接口的类。
* android.net：提供帮助网络服务的类。
* android.opengl：提供OpenGL的工具。
* android.provider：提供类访问Android的内容提供者。
* android.telephony：提供与拨打电话相关的API交互。
* android.util：提供设计工具类的方法，比如时间日期的操作。
* android.webkit：提供默认浏览器操作接口。
* android.widget：包含各种UI元素在应用程序中使用的类。

#### 8. Android中的Project和Module

在Android Studio中，Project是工作空间，Module是一个具体的项目，能够独立运行，测试并且独立测试。

#### 9. Android程序如何获取界面中的控件并且在窗口中显示

1. 在 activity_main.xml 配置文件中，为界面上的控件增加android:id 属性，代码如下：

   ~~~xml
   <TextView
    android:id="@+id/device_id">
       ...
   </TextView>
   ~~~

2. 在Activity类中通过 findViewById() 方法获取获取对应的android:id。获取activity_main.xml中的配置的代码如下：

   ~~~java
   TextView tv = (TextView) findViewById(R.id.device_id)
   ~~~

3. 在Android布局文件中华 [@+id/username] 和 [@id/username] 两者有何区别？

   分析如下布局文件中的代码：

   ~~~xml
   <TextView
             android:id="@+id/xyz"
             android:text="@string/hello_world"/>
   ~~~

   其中包含了android:id="@+id/xyz" 以及 android:text="@string/hello_world"

   Android中的控件需要用一个 int 值来表示，这个值是标签中的id 值，如 @+id/abc，@id/xyz 等。如果在@后加上 + ，则表示修改完某个布局文件并保存后，系统自动在 R.java 文件中生成对应的 int 整形标量。例如 @+id/xyz 会在 R.java 文件中生成  int xyz = value，value是一个16进制的数。如果 R.java 中已存在 同名变量，则不会重新生成新的变量，而是会调用该变量的值。 android:id="@id/xyz" 表示 TextView 的 id 是 xyz，然后可通过 findViewById(R.id.xyz) 获得一个 TextView 对象。



































