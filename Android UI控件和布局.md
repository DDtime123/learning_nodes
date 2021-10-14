# Android UI控件和布局
**大纲：**

* 介绍
* 被组织和被看到



#### 1. 介绍

本篇的内容包括 UI 布局和 UI 控件：

UI布局包括：

* 线性布局：LinearLayout
* 相对布局：RelativeLayout
* 表格布局：TableLayout
* 框架布局：FrameLayout
* 无线电集团：RadioGroup
* 列表视图：ListView
* 网格视图：GripView

UI控件包括：

* 按钮：Button
* 文本视图：TestView
* 编辑文本：EditText
* 复选框：CheckBox
* 单选按钮：RadioButton
* 切换按钮：ToggleButton
* 纺纱机：Spinner
* 自动完成文本视图：AutoCompleteTextView
* 进度条：Progress Bar
* 拣货员：Picker
* 图像视图：ImageView



#### 2. 被组织和被看到

每个 Android 的用户界面都由 View 和 ViewGroup 的分层集合组成。

View 类是所有的 UI 控件的基类，包括 TextView，Button等。每个可见的 UI 控件都占据屏幕上的一个区域，通过单击，触摸，键控等事件与用户进行交互。

一个 ViewGroup 则是一个 View 组件的组织者，是 UI 布局的基类。可以用 LinearLayout 布局以线性形式排列 UI 控件，或者用 RelativeLayout 布局将它们彼此相对排列。而 ViewGroup 类也是从 View 类派生出来的，所以一个 ViewGroup 类可以嵌套在另一个 ViewGroup 中呈现不同的布局。

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013204152.png)



##### 2.1 线性布局

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#ffb9d7ff">

    <Button android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button"/>

    <Button
        android:id="@+id/button2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" />

</LinearLayout>
~~~

**背景：**

我们添加了两个按钮，在 LinearLayout 的 orientation 朝向属性设置为 **horizontal** 水平排列，由此得到了水平排列的两个 Button：

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013210653.png)

这里我们改变了布局的 **background** 属性使得它的背景颜色变为蓝色，LinearLayout 作为布局本身是不可见的，而设置它的背景颜色却会让整个屏幕充满颜色，说明最外层的 LinearLayout 的宽度高度与父屏幕一样大。

**重力：**

如果在 LinearLayout 的重力 **Gravity** 属性上设置 right 和 center ，这两个按钮将会定位在屏幕的右中心：

~~~xml
android:gravity="center|right"
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013211602.png)



**填充（padding）：**

撤销 Gravity 属性的设置，设置 padding 属性，padding 属性有 paddingTop ，paddingLeft，paddingBottom 等，这里设置 paddingTop 和 paddingLeft 两个属性分别为 70dp 和 50dp，意为距离上边界和左边界的距离分别为 70dp 和 50dp：

~~~xml
android:paddingLeft="50dp"
android:paddingTop="70dp"
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013212629.png)



**布局宽度，高度：**

现在，我们改变布局的宽度和高度，让它们从 match_parent 变为 wrap_content，将填充属性去掉，可以看到，布局的宽度和高度被设置为刚好配合这两个按钮：

~~~xml
android:layout_width="wrap_content"
android:layout_height="wrap_content"
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013213128.png)

> match_parent 表示拉伸布局，使其适应其父组件的长宽，wrap_content 表示调整布局大小以适应它的子视图



**方向（orientation）：**

我们将 LinearLayout 的 orientation 属性从 horizontal（水平方向） 改为 vertical（垂直方向） 布局：

~~~xml
android:orientation="vertical"
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013213640.png)

两个 Button 组件将垂直排列。



**外边界（margin）：**

我们往 LinearLayout 中添加 layout_margin 属性值：

~~~xml
android:layout_margin="@android:dimen/app_icon_size"
~~~

![](https://gitee.com/zhang-jianhua1/blogimage/raw/master/img/20211013214057.png)



##### 2.2 相对布局

